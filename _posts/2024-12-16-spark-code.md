---
layout: post
title:  "[#3] Spark Code Optimization at Work"
author: daehong
categories: [ company, spark ]
image: assets/images/company-spark.png
featured: true
rating: 5
---

### ✏️ Problem
---

회사 spark 코드 중 일자별로 모아놓은 데이터를 입수해서, 월 별 데이터로 파티셔닝 하여 다시 적재하는 로직이 있었다.

코드는 다음과 같았다.

```scala

val df = spark.read.option("header", "true").option("inferSchema", "true").csv("/~/*.csv")

val dfWithMonth = df.withColumn("month", date_format(col("use_dttm"), "yyyy-MM"))

val months = dfWithMonth.select("month").distinct().as[String].collect()

val outputPath = "/~/output"

months.foreach { month => val monthData = dfWithMonth.filter(col("month") === month); monthData.write.option("header", "true").mode("overwrite").csv(s"$outputPath/$month") } 

```

코드를 하나하나 설명해보면,

`spark.read` 를 통해 DataFrame API 를 이용하여 데이터를 읽는다. ( 참고로, spark 클러스터링 환경이므로, Data 는 NAS나, Object Storage 에서 읽어온다. )

`.option("header", "true")` 를 통해 csv 파일의 첫 번째 행을 Column Header 로 사용한다.

`.option("inferSchema", "true")` 를 이용하여 spark 가 자동으로 각 Column 별 Data Type 을 추론한다. ( String 인지, Int 인지 등등 )

`.csv(~)` 는 **NAS** 나 **Object Storage** 에서 해당 Data 가 위치한 Path 이다.

저렇게 하면, **df** 라는 Immutable Value 는 csv Data 를 포함하는 spark DataFrame 이 된다.

다음에는 `.withColumn("month", ...)` 와 `date_format(col("use_dttm"), "yyyy-MM")` 를 활용하여 yyyy-MM 형식의 `month` 라는 새로운 Column이 추가된 DataFrame 을 만든다.

그리고 **months** 라는 Immutable Value 에 `dfWithMonth.select("month").distinct().as[String].collect()` 를 활용하여, 월 값을 추출 후 집어넣는다.

`.select("month")` 는 month Column 만을 선택하게 해주고, `distinct()` 를 통해 중복을 제거한다. 그리고 `.as[String]` 을 통해 데이터를 문자열 배열로 반환한다. 마지막으로 `.collect()` 를 활용하여 해당 데이터를 Driver Node 로 가져온다.

이걸 최종적으로 for 문을 돌려 저장하는 방식인데, **df** 에서 `.filter(col("month") === month)` 를 통해 해당 month 와 같은 데이터만 담아 output 경로로 보내주는 역할을 한다.

이렇게 하면, 실질적으로 Data 도 월 별로 분류가 된다.

하지만 이 코드에는 문제점이 너무도 많았다.

Spark 는 기본적으로 병렬 처리 구조이며, 읽고 처리하는 단계에서 여러개의 파티션으로 나뉜다. 심지어 클러스터 방식이어서 worker 도 여러개였기 때문에 더더욱 그렇다.

그래서 **"2024-01"** 이라는 폴더 안에는 **part-000....csv** 이런 파일이 여러개가 생겼고, 병합을 한 번 더 해줘야 하는 귀찮은 작업이 있었다.

또한, 적은양의 데이터면 상관이 없었지만, 초 단위의 방대한 양의 데이터로 이 작업을 돌린다면, month partition 만큼 반복문을 돌기 때문에, 작업 시간이 엄청나게 많이 들었다.

<br>

### ✏️ Solution
---

처음에는 단순이 `coalesce` 를 통해 최적화를 하려헀지만, 이 역시 시간은 오래 걸리고, **OOM** 문제가 발생하여, 다른 방법을 찾게 되었다.

물론 Memory 를 더 주면 해결 될 수 있을 거 같았지만, 근본적인 문제가 해결되는 것은 아니기 때문에 고려하지 않았다.

**나는 결국 이 문제를 해결하기 위해, 코드를 아래와 같이 바꿨다.**


```scala

val df = spark.read.option("header","true").csv("/~/data").na.fill("").withColumn("partitioned",regexp_replace(substring(col("use_dttm"),1,7),"-","")).orderBy(col("partitioned"))

df.repartition(1,col("partitioned")).write.option("header","true").partitionBy("partitioned").csv("/~/output")

```

df 에 데이터를 담는 것까지는 똑같다.

하지만, 여기에서 다른 점은 `withColumn` 을 df 안에서 활용하여 날짜 파티션 `partitioned` 컬럼을 만들었고,

spark 는 데이터의 저장 순서를 보장하지 않기 때문에, `orderBy` 를 통해 `partitioned` 컬럼을 기준으로 정렬을 하였다.

또한, `.na.fill("")` 을 통해 `Null` 값을 빈 문자열로 대체하여 혹시나 하는 변수를 대처 할 수 있게 하였다.

이걸 `write` 할 때에는, `repartition` 을 통해 `partitioned` 컬럼을 기준으로 재파티셔닝을 하였으며, 옵션을 통해 파티션 값당 하나의 파일을 생성하도록 하였다. 그로 인하여, 이후에 다시 병합하는 단계를 생략 할 수 있게 되었다.

마지막으로는 `partitionBy("partitioned")` 을 통해 데이터를 `partitioned` 컬럼 값별로 디렉터리에 나누어 저장하였다.

이로써 병합의 과정을 생략하게 되었고, 성능 역시 5배 이상 좋아지는 결과를 얻게 되었다.

<br>

### ✏️ Conclusion
---

사실 그렇게 어려운 문제는 아니다.

**Spark** 를 막 튜닝해서 퍼포먼스를 올리는 문제도 아니었고, 단순이 Scala 로 작성된 Spark 의 Code 를 변경한 것 뿐이다.

심지어 코드가 어려운 코드도 아니다.

하지만, 이 코드 하나를 변경한 것만으로, 32G 짜리 Data를 24개월로 나누는 기준으로, 약 2시간 가량 걸리던 작업 시간을 20분 미만으로 단축할 수 있었다.

Code Level 에서 해결 할 수 있는 문제는 깔끔하게 다듬을 수 있도록 하고, 그 외에도 더더욱 성능 향상을 위해 연구하고 공부해야겠다는 생각이 들었다.

<br>

<img src="https://github.com/user-attachments/assets/f78ba38c-c373-44ec-9adc-af994a08f9dd" style="width: 100%;">