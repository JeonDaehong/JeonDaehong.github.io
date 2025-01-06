---
layout: post
title:  "[#2] Apache Iceberg Deep Dive - How Does It Address the Limitations of Hive?"
author: daehong
categories: [ Study, Iceberg ]
image: assets/images/apache-iceberg.png
featured: true
rating: 5
---

**Apache Iceberg**는 Toss, Kakao, 11번가, LINE 할 거 없이 많은 곳에서 도입하고 있는 테이블 포멧 형태의 기술이며, 심지어 네이버 웹툰에서는 아이스버그 구축 경험이 있는 사람을 대놓고 우대사항에 적어두고 있을만큼, 이름과는 상반되게 현재 뜨겁게 타오르는 태양과도 같은 기술입니다.

새로운 기술을 접목 하는데에 있어, 기업과 개발자의 관점은 같으면서도 다른 것을 중점으로 두고 있습니다. 기업은 해당 기술을 도입함으로써, 얼만큼의 금전적 이윤을 얻을 수 있는지를 추구하고, 개발자는 그 기술을 도입함으로써 자신의 기술적 스킬 향상과 가치를 상승 시킬 수 있습니다.

이렇게 이윤과 성장이라는 다른 중점은 가지고 있지만, 이익을 추구한다는 관점은 같다고 볼 수 있습니다.

<img src="https://github.com/user-attachments/assets/880e24fd-5dd7-46f7-9703-50e07c5beb46" style="width: 100%;"/>

그러한 면에서 **Apache Iceberg** 은 기업 입장에서도, 개발자 입장에서도 반드시 도입해야 할 기술이라는 결론을 먼저 말씀드리며, 지금부터 왜 **Apache Iceberg**를 도입해야 하는지, 대체 **Apache Iceberg**가 무엇인지 아이스 버그라는 거대한 빙산을 조금씩 녹여가며 중요한 부분들에 대해 이야기하도록 하겠습니다.

<br>

<div style="background-color: pink; padding: 5px; margin: 0;">
  <h2 style="margin: 0;">🧊🧊 1. Apache Iceberg 란 ?</h2>
</div>
---

<img src="https://github.com/user-attachments/assets/a68c9766-1cbd-4de1-bbe6-4aa313665d4b" style="width: 100%;"/>

**Apache Iceberg**는 **2017년 Netflix**에서 **Apache Hive**의 한계를 극복하기 위해 개발된 개방형 테이블 형식의 데이터 관리 시스템입니다. 대규모 데이터 세트를 효율적으로 관리하고 처리하기 위한 목적으로 만들어졌으며, 현재는 **Apache** 재단에 기부된 오픈소스 프로젝트입니다. 데이터 엔지니어들 사이에서는 데이터를 효과적으로 관리하고 실시간으로 **쿼리(Query)**할 수 있는 도구로 주목받으며 최근 큰 인기를 끌고 있습니다.

<img src="https://github.com/user-attachments/assets/955eb187-ce92-46c1-8097-e9ec8fedb41e" style="width: 100%;"/>

**Apache Hive**의 주요 한계로는 **ACID 지원 부족, MetaStore의 병목 현상, Schema 확장성의 한계, 디렉터리 및 파일 조회 시간 문제, 파티션 필터링의 비효율성, 그리고 높은 지연 시간(Latency)** 등이 꼽힙니다.

이렇게만 설명하면 다소 추상적으로 느껴질 수 있으니, 몇 가지 예시를 통해 더 자세히 설명드리겠습니다.

<br>

<h3><span style="background-color: lightblue; padding: 0 5px;">🧊 1.1. Hive 의 한계 : 불완전한 ACID 지원</span></h3>
---

**첫 번째 예시입니다.**

은행에서 고객 거래 데이터를 분석해 더 나은 서비스와 맞춤형 금융 상품을 제공하고자 합니다.  고객들의 입출금 내역, 카드 사용 내역, 그리고 온라인 뱅킹 활동 데이터를 수집해 **Apache Hive**에 적재한 상태입니다.

어느 날, 마케팅 팀이 특정 고객군의 월별 카드 사용 패턴을 분석하려 합니다. 

<img src="https://github.com/user-attachments/assets/d9def49a-18da-4cf9-83ba-788e60100ed0" style="width: 100%;"/>

데이터 엔지니어링 팀은 고객 데이터를 **Hive**에 적재하고, ETL 작업을 통해 분석 가능한 상태로 만들어 제공했습니다. 그런데 문제가 생겼습니다. 같은 시간대에 IT 운영팀에서도 동일한 데이터셋을 이용해 대규모 업데이트 작업을 수행하고 있던겁니다.

<img src="https://github.com/user-attachments/assets/fe937ede-e3a3-480b-97e5-17279f808bc7" style="width: 100%;"/>

**Hive**는 기본적으로 파일 기반의 저장소를 사용하므로, 두 작업이 동시에 실행되면서 다음과 같은 문제가 발생했습니다.

- IT 운영팀이 데이터를 업데이트하는 과정에서 일부 파일이 교체되었습니다. 마케팅 팀이 분석 쿼리를 실행하는 중간에 데이터가 변경되었고, 그 결과 마케팅 팀이 가져간 데이터는 절반은 업데이트 전, 나머지 절반은 업데이트 후 상태였습니다. 즉, 일관성이 깨져 분석 결과가 신뢰할 수 없게 되었습니다.

- 또 다른 문제는 IT 운영팀이 데이터를 병렬로 덮어쓰는 과정에서 일부 파일이 잘못 덮어써지거나 손실되었습니다. **Hive**는 **INSERT OVERWRITE** 방식으로 동작하므로, 중복 작업이나 오류가 발생하면 데이터를 복구하기 어렵습니다.

- 이 모든 과정에서 두 팀이 동시에 Hive 테이블을 읽고 쓰면서 경합이 발생했습니다. 특정 쿼리는 파일이 존재하지 않거나 잠겨 있어 실패했고, 결과적으로 분석팀의 업무가 지연되었습니다.

이렇게 데이터가 유실되거나, 읽기/쓰기 과정에서 충돌이 발생하여 업무가 지연되는 일이 발생 할 수 있습니다.

<img src="https://github.com/user-attachments/assets/3ce921e4-92c9-40a4-af3e-093c8ac04a0c" style="width: 100%;"/>

물론 현재는 **Hive** 에서도 현재 ACID  트랜잭션을 활성화 시키면, 일부 ACID 를 지원해주기는 합니다. 그러나 트랜잭션 작업 시 바로 원본 데이터 파일에 반영되는 것이 아니라 별도의 **Delta 파일**이 생성되고, **Delta 파일**이 **Compaction**되는 과정을 통하여 원본 파일의 ACID 를 보장하는 절차를 가지고 있습니다.

이로 인하여 별도의 Compaction 작업으로 인한 비용 문제와, Delta 파일이 증가함에 따라 빈번하게 발생하는 Compaction 작업으로 인한 병목 현상, 이러한 작업으로 인한 실시간 데이터 분석의 어려움 문제가 있으며, 무엇보다 ORC 파일 포맷 형식만 ACID 를 지원하므로 csv 나 다른 형식의 포맷은 이용 할 수 없다는 큰 단점이 있습니다.

<br>

<h3><span style="background-color: lightblue; padding: 0 5px;">🧊 1.2. Hive 의 한계 : MetaStore 의 병목 현상</span></h3>
---

**두 번째 예시입니다.**

이번에도 마찬가지로, 은행에서 데이터를 분산 환경에서 HDFS 에 저장하고, **Apache Hive** 를 통하여 관리하고 있습니다.

<img src="https://github.com/user-attachments/assets/84f39176-0b99-4c76-a9ed-b03d9ec73d99" style="width: 100%;"/>

- 은행은 각 거래 기록을 날짜별로 파티셔닝된 테이블에 저장합니다.
- 테이블은 **고객 계좌 번호(account_id)**를 기준으로 추가적인 서브 파티셔닝이 되어 있어, 하루에 생성되는 파티션 수는 수천 개에 이릅니다.
- 하루 동안 약 **100만 건의 거래 데이터**가 발생하며, 이를 조회하고 분석하기 위해 여러 사용자가 Hive를 동시에 사용합니다.

<img src="https://github.com/user-attachments/assets/e12552d2-1313-4922-999d-68803bfa9304" style="width: 100%;"/>

이 때 다음과 같은 집계와, 보고서를 생성하기 위해, 여러 부서에서 동시에 해당 데이터로 접근을 합니다.

이 작업이 실행되면서 **MetaStore**에 과부하가 걸리기 시작합니다.

<img src="https://github.com/user-attachments/assets/6321721a-f666-47b7-9e97-5160dc8697f8" style="width: 100%;"/>

그 이유는 **Hive** 는 쿼리 실행 전에 `transactions`와 `loan_payments` 테이블의 **파티션 정보**를 **MetaStore**에서 조회해야 하지만, `transactions` 테이블은 날짜별, 계좌별로 수천 개의 파티션을 가지고 있고, MetaStore는 RDBMS(MySQL)에서 이러한 파티션 정보를 순차적으로 조회하므로, 대규모 파티션에서 조회 시간이 급격히 늘어나기 때문입니다.

또, **MetaStore** 의 **Thrift 서버**는 단일 프로세스 기반으로 동작하기 때문에 동시 사용자 요청이 많게 되면, Connection Timeout 오류를 반환하기도 합니다.

무엇보다 MetaStore 는 RDBMS 와 연동하여 저장된 메타데이터를 사용하기 때문에 Connection Pool 크기에 따라 문제가 생기기도 하며, RDBMS 의 트랜잭션 처리 속도가 느려지게 됩니다.

쿼리가 MetaStore에서 필요한 정보를 가져오지 못하면, **Hive** 는 데이터 처리를 시작하지 못하기 때문에 위와 같은 문제가 발생 할 수 있습니다.

<br>

<h3><span style="background-color: lightblue; padding: 0 5px;">🧊 1.3. Hive 의 한계 : Schema 확장성의 한계</span></h3>
---

**세 번째 예시입니다.**

은행에서 데이터를 **Hive** 에 저장하는데, 고객이 거래를 한 지역별로 파티션을 나누어 저장하고, 기존 스키마는 아래와 같이 설정되어 있습니다.

```sql
CREATE TABLE card_transactions (
    transaction_id STRING,
    customer_id STRING,
    transaction_date DATE,
    amount DOUBLE
)
PARTITIONED BY (region STRING);
```

이 테이블은 마케팅 팀에서 고객의 지역별 소비 패턴을 분석하는 데 사용되고 있습니다.

그런데, 뭔가 요구 사항이 들어옵니다.

**첫 번째 요구 사항**은 **거래에 사용된 카드의 종류(예: 신용카드, 체크카드)를 추가하라는 요청**입니다. **두 번째**는 **지역 파티션 외에 연도별 파티션도 추가**하라는 요구입니다.

이를 구현하기 위해 스키마를 확장하고 파티션을 변경하려고 합니다. 하지만 Hive의 특성 때문에 여러 문제가 발생합니다.

일단 첫 번째 요구사항을 해결하기 위해, 스키마에 `card_type` 컬럼을 추가했습니다.

```sql
ALTER TABLE card_transactions
ADD COLUMNS (card_type STRING);
```

그런데 **Hive** 는 기존 데이터를 변경하지 않습니다. 새롭게 적재된 데이터만 `card_type` 정보를 포함하고, 기존 데이터는 이 컬럼에 대해 `NULL` 값을 갖습니다.

이로 인해 마케팅 팀은 동일한 테이블을 조회하면서 일부 데이터에는 카드 종류가 표시되고, 나머지는 `NULL`로 나옵니다. 분석 결과가 일관되지 않게 되어 혼란을 초래합니다.

그래서 파티션을 변경하는 작업을 실행하고자 합니다.

하지만 **Hive** 는 동적으로 파티션을 변경할 수 있는 기능이 제한적입니다.

기존 데이터에 새로운 연도별 파티션을 추가하려면, 데이터를 추출한 후 새로 생성된 테이블에 다시 적재해야 합니다. 이 과정에서 시간이 많이 소요되었고, 작업 도중 시스템이 과부하로 느려지는 문제가 발생했습니다.

추가로, 새로운 파티션 구조를 적용하는 동안 일부 쿼리가 기존 파티션 구조를 참조하면서 오류를 발생시켰습니다. 즉, 파티션 변경 과정에서 일시적으로 데이터 접근에 제한이 생겼습니다.

그리고 파티션을 변경하는 작업 중에, 무슨 일이 생길 수 있으니 백업 데이터를 만들어 저장한 후, 파티션 변경 작업을 시작함에 따라 추가적인 시간과 자원이 소모가 되었습니다.

이러한 문제 때문에, 스키마를 확장하거나, 파티션을 변경하는 요구사항에 대한 거부감이 생기게 되었습니다.

<br>

<h3><span style="background-color: lightblue; padding: 0 5px;">🧊 1.4. Hive 의 한계 : 기타 한계점</span></h3>
---

그 외에도 시간이 지남에 따라 데이터의 양이 폭발적으로 증가하면서, 디렉터리와 파일을 직접 스캔하는 형식으로 관리하는 **Hive** 에서는 조회 성능이 나빠지는 문제가 발견되기도 하였고,

잘못된 데이터가 덮어쓰여졌지만, 데이터를 **롤백** 할 방법이 없기에 데이터를 다시 적재해야 하는 문제가 발견되기도 하였습니다.

그리고 파티션 필터링의 비효율성도 발견되기도 하였습니다.

파티션 필터링의 비효율성을 간단히 설명드리면, 

```sql
CREATE TABLE card_transactions (
    transaction_id STRING,
    customer_id STRING,
    transaction_date DATE,
    amount DOUBLE
)
PARTITIONED BY (year INT, region STRING);
```

이러한 쿼리문이 있을 때, 

```sql
SELECT * 
FROM card_transactions
WHERE customer_id = 'CUST12345';
```

이렇게 **고객ID** 로 조회를 할 경우, **고객ID** 는 파티션으로 지정되어있지 않기 때문에 **FULL SCAN** 작업을 거쳐 성능이 느려지게 됩니다.

이러한 단점들을 보완하기 위해 **파일 목록을 메타데이터로 사용하는 테이블 포멧 방식들이 생겨나기 시작하였습니다.** 

<br>

<h3><span style="background-color: lightblue; padding: 0 5px;">🧊 1.5. Hive 의 한계를 극복하기 위한 새로운 테이블 포멧 방식의 등장</span></h3>
---

**그러면 파일 목록을 메타데이터로 사용하는 테이블 포멧 방식이 뭘까요?**

이 접근 방식은 테이블을 디렉터리가 아닌 **파일 집합으로 구성**하며, 쿼리 엔진이 데이터를 효율적으로 처리할 수 있도록 도와줍니다. 이 방식은 전통적으로 디렉토리 구조를 기반으로 테이블을 정의하던 방식과는 차별화됩니다.

새로운 데이터가 추가되거나 기존 데이터가 수정될 경우, 변경된 파일 목록이 메타데이터에 업데이트 되며, 쿼리 엔진은 메타데이터를 먼저 읽어, 쿼리 실행에 필요한 정확한 데이터 파일들만 접근한다는 특징이 있습니다. 대표적으로는  **Apache Iceberg, Apache Hudi, Delta Lake** 가 있습니다.

**자, 그럼 오늘 발표 주제인 Apache Iceberg는, 대체 어떤 특징이 있길래 Hive 의 단점을 모두 보완 할 수 있는 특징을 가지고 있을까요?** 그리고, 많은 기업들이 **Hudi** 나 **Delta Lake** 대신 **Apache Iceberg** 를 선택하는 이유가 무엇일까요?

저는 **Apache Iceberg** 에 대해 공부하고 조사하면서 이러한 메타 데이터 형식의 테이블 포맷에는 여러가지 개발 철학이 있고, 수 많은 장점이 있다는 것을 알았습니다. 그리고, 저는 그걸 **C.P.C.S** **네 가지**의 중심적인 개발 철학과 **일곱 가지**의 큰 장점으로 정리를 하였습니다.

<br>

<h3><span style="background-color: lightblue; padding: 0 5px;">🧊 1.6. Iceberg 의 네 가지 핵심 철학</span></h3>
---

**Apache Iceberg** 의 개발 철학 네 가지는 **“일관성”, “성능”, “편의성”, “확장성”**  입니다.

**1. 일관성 (Consistency)**

- 데이터가 항상 예상된 상태를 유지하도록 설계되었습니다. **Iceberg**는 **ACID** 트랜잭션을 기본적으로 지원하며, 이를 통해 데이터 작업의 일관성을 보장합니다. 예를 들어, 여러 작업이 동시에 이루어져도 데이터의 손상이나 충돌이 발생하지 않도록 합니다.

**2. 성능 (Performance)**

- Iceberg는 대규모 데이터 세트를 효율적으로 처리할 수 있도록 최적화되었습니다. 쿼리 성능을 높이기 위해 파일 스키핑, 파티셔닝, 정렬, Z-order와 같은 다양한 최적화 기법을 제공합니다.
- 또한, Hive에서는 **파일과 디렉터리를 모두 열거**하면서 쿼리 계획 수립에 시간이 많이 소요됩니다. 하지만 Iceberg는 **메타데이터**를 활용하여 필요한 파일만 조회하고, 쿼리 성능을 최적화 합니다.

**3. 편의성 (Convenience)**

- Iceberg는 다양한 컴퓨팅 엔진(예: Apache Spark, Apache Flink, Dremio 등)과 호환되어 사용자가 친숙한 환경에서 작업할 수 있도록 합니다. 또한, 강력한 메타데이터 관리 기능을 통해 데이터 관리와 분석을 단순화합니다.
- Hive에서는 사용자가 파티션된 컬럼을 직접 필터링해야 하는 번거로움이 있었습니다. 예를 들어, `timestamp`로 필터링 하고자 할 때 이를 `month` 파티션으로 집접 매핑해야 했습니다. 그러나 Iceberg는 사용자가 **직관적인 쿼리**만 작성해도 자동으로 파티셔닝을 활용할 수 있도록 설계되었습니다. 예를들면 자주 사용하는 컬럼에 대한 자동 파티셔닝을 지원합니다.

**4. 확장성 (Scalability)**

- Hive에서는 **스키마를 변경하거나 파티션 방식을 수정**할 때 테이블 전체를 다시 작성해야 합니다. 하지만 **Iceberg**는 **스키마 진화와 파티션 재구성**을 안전하게 지원하여 기존 데이터를 다시 작성하지 않아도 됩니다. 즉, **스키마와 파티션을 유연하게 변경**할 수 있어 데이터 모델의 확장성을 보장합니다.

이러한 네 가지 개발 철학은 **Apache Iceberg**가 단순히 데이터 저장 및 관리의 도구를 넘어, 현대 데이터 플랫폼에서 필수적인 역할을 수행할 수 있는 이유를 잘 보여줍니다. **Iceberg**는 데이터 엔지니어와 분석가가 점점 더 복잡해지는 데이터 환경에서도 신뢰성과 효율성을 유지하면서 작업을 수행할 수 있도록 설계되었습니다.

<br>

<h3><span style="background-color: lightblue; padding: 0 5px;">🧊 1.7. Iceberg 의 일곱 가지 주요 장점</span></h3>
---

이제 **Apache Iceberg**가 제공하는 **일곱 가지 주요 장점**에 대해 살펴보겠습니다. 이를 통해 Iceberg가 어떻게 **데이터 레이크하우스**의 강력한 기반으로 자리 잡고 있는지 더욱 구체적으로 이해할 수 있을 것입니다.

**아이스버그의** 첫 번째 장점은 **ACID 기능**을 완벽하게 지원해줌으로 데이터 레이크에서 신뢰성과 일관성을 보장하는 핵심 기능 역할을 합니다.

앞서 설명하였듯, Hive 는 데이터 파티션 단위로 관리되기 때문에, 파티션 내 데이터를 갱신할 때 전체 파티션을 다시 저장해야 하는 구조입니다. 그렇기 때문에 동시에 여러 사용자가 데이터를 읽거나 쓰는 경우 **동시성 문제**로 인해 데이터 일관성이 깨질 위험이 있습니다. 예를 들어, A 사용자가 파티션을 갱신하는 중에 B 사용자가 동일한 파티션의 데이터를 읽으려고 한다면, 데이터 충돌이나 손실이 발생할 가능성이 높습니다.

하지만 Iceberg 는 데이터를 파일 단위로 관리하며, **스냅샷 기반 트랜잭션**을 지원하며, **Optimistic Concurrency Control** 방식을 이용하여, 동시성 문제를 해결해줍니다. **Optimistic Concurrency Control** 는 서버 개발 시에도 종종 사용되는 Optimistic Lock 과 유사한 방법이며, 변경이 일어나기 전 데이터의 버전과, 변경이 일어난 후의 데이터 버전을 비교하는 방식으로 충돌 여부를 판단하고, 제어하는 방법입니다. 이러한 방법을 통하여 완벽한 ACID 를 지원해줍니다.

예시를 하나 들어보며, 어떻게 동작하기에 ACID 를 지원해주는지 설명해드리겠습니다. 고객 입출금 데이터셋이 있고, A 팀과, B 팀에서 해당 데이터를 동시에 업데이트 해야 할 일이 발생하였습니다. 그리고 그 사이 C 팀에서 해당 데이터를 조회했다고 가정하겠습니다. Hive 였으면, 앞서 설명드렸듯 데이터 유실, 잘못된 데이터 조회 등의 문제가 발생할 수 있지만 Iceberg 는 그렇지 않습니다.

먼저 테이블의 최신 스냅샷 메타데이터를 로드하여 현재 테이블 상태를 가져옵니다. ( part-00000.parquet)

```JSON
{
  "snapshot_id": 1001,
  "timestamp_ms": 1700000000000,
  "manifest_list": "s3a://iceberg-data/manifest-list-1001.avro",
  "summary": {
    "operation": "append",
    "added_files": 1,
    "deleted_files": 0,
    "added_rows": 1000,
    "deleted_rows": 0
  }
}
```

```JSON
[
  {
    "manifest_path": "s3a://iceberg-data/manifest-1001.avro",
    "manifest_length": 1024,
    "partition_spec_id": 0,
    "added_data_files_count": 1,
    "existing_data_files_count": 0,
    "deleted_data_files_count": 0
  }
]
```

```JSON
[
  {
    "file_path": "s3a://iceberg-data/part-00000.parquet",
    "partition": {"customer_id": "12345"},
    "record_count": 100000,
    "file_size_in_bytes": 1048576,
    "status": "ADDED"
  }
]
```

순서대로 메타데이터 파일, 매니페스트 리스트 파일, 매니페스트 파일입니다.

이후, A 팀과, B 팀에서 각각의 트랜잭션이 시작됩니다.

- **A 트랜잭션**:

	- A는 테이블의 최신 메타데이터(스냅샷 ID: 1001)를 기반으로 트랜잭션을 시작합니다.
	
	- 고객의 입금 내역을 추가하고, 새로운 데이터 파일(`part-00001.parquet`)을 생성합니다.

- **B 트랜잭션**:

	- B도 동일한 최신 메타데이터(스냅샷 ID : 1001)를 기반으로 트랜잭션을 시작하며, 출금 내역을 처리하고 새로운 데이터 파일(`part-00002.parquet`)을 생성합니다.
		
만약 이 때, C 팀에서 데이터를 조회한다면, 반영 전 현재 스냅샷 ID 인 1001 을 기준으로 데이터를 제공합니다.

이제 A팀에서 자신의 변경 사항을 커밋합니다. Iceberg는 A가 생성한 새로운 데이터 파일(`part-00001.parquet`)을 매니페스트 파일(`manifest-1002.avro`)에 기록하고, 스냅샷 메타데이터를 업데이트합니다.

```JSON

{
	"added_files": ["s3a://iceberg-data/part-00001.parquet"],
	"partition": {"customer_id": "12345"},
	"status": "ADDED"
}

```

```JSON

{
	"snapshot_id": 1002,
	"manifest_list": ["manifest-1001.avro", "manifest-1002.avro"],
	"data_files": [
		  "s3a://iceberg-data/part-00000.parquet" ,
		  "s3a://iceberg-data/part-00001.parquet"
  ]
}

```

다음은 B팀에서 변경 사항을 커밋하게 됩니다. 하지만 초기 스냅샷(ID:1001)과 현재 스냅샷(ID:1002)가 다르기 때문에 충돌 여부를 검사합니다.

충돌이 있을 경우, B를 롤백하게되고, 충돌이 없는경우는 그대로 병합하여 새로운 스냅샷 1003 을 만들어냅니다.

```JSON

{
  "snapshot_id": 1003,
  "manifest_list": ["manifest-1001.avro", "manifest-1002.avro", "manifest-1003.avro"],
  "data_files": [
    "s3a://iceberg-data/part-00000.parquet",
    "s3a://iceberg-data/part-00001.parquet",
    "s3a://iceberg-data/part-00002.parquet"
  ]
}

```

이 때 C 팀에서 다시 데이터를 조회한다면, A B 가 모두 반영된 1003 기준의 데이터를 조회하게 됩니다.

이걸 ACID 로 풀어서 보면 다음과 같습니다.

- **Atomicity (원자성):**

    - A와 B의 작업은 각각 커밋되기 전까지 테이블 상태에 영향을 미치지 않습니다.
	
    - C는 활성 스냅샷(1001, 1002, 1003)을 기준으로만 데이터를 조회하므로, 부분 변경된 상태를 볼 일이 없습니다.
	
- **Consistency (일관성):**

    - 스냅샷과 매니페스트 파일을 활용하여 항상 일관된 상태를 유지합니다.
	
    - C가 데이터를 조회할 때, 테이블은 항상 유효한 상태로 보입니다.
	
- **Isolation (독립성):**

    - A와 B의 작업은 서로 독립적으로 진행되며, 각 커밋 후에만 다른 트랜잭션 및 조회에 반영됩니다.
	
- **Durability (지속성):**

    - A와 B의 커밋은 스냅샷 및 매니페스트 파일로 안전하게 기록되어 데이터가 손실되지 않습니다.

이처럼 Iceberg는 충돌이 발생할 경우, 해당 트랜잭션을 거부하고 데이터 일관성을 유지합니다. 이후 재시도를 통해 충돌 없이 변경 사항을 반영할 수 있도록 설계되어, **효율적인 충돌 감지**와 **ACID 보장**을 동시에 제공합니다.

<br>

**아이스버그**의 두 번째 강점은 파티션의 변경 및 확장성이 용이하다는 것입니다.

먼저, 앞에 설명했던 Hive와 같은 기존 데이터 레이크에서 파티셔닝의 한계를 다시 말씀드리겠습니다.

Hive에서는 데이터를 처음 저장할 때 **정해진 파티셔닝 방식**이 고정됩니다. 예를 들어, 데이터를 **월 단위**로 파티셔닝했다고 가정해 보겠습니다. 이후 비즈니스 요구가 변경되어 **일 단위**로 데이터를 파티셔닝해야 한다면, 이를 변경하기 위해 전체 데이터를 다시 써야 합니다. 이 작업은 테라바이트, 심지어 페타바이트 단위의 데이터를 다루는 환경에서는 엄청난 시간과 비용을 소모합니다.

그렇다고 기존의 월별 파티셔닝 구조를 유지하면 어떻게 될까요? 일별 분석 쿼리를 실행할 때 월별 데이터에서 필요한 데이터를 필터링해야 하므로, **쿼리 성능이 크게 저하**됩니다. 특히, 분석팀이 특정 날짜에 대한 트랜잭션 데이터를 반복적으로 요청한다면, 쿼리 실행 시간과 리소스 소모는 더욱 증가하게 됩니다.

하지만 **Apache Iceberg** 는 이러한 문제를 근본적으로 해결해줍니다. 파티션 구조를 변경하려면, **데이터 자체를 수정하거나 이동할 필요가 없습니다.** **Iceberg**는 파티셔닝 방식을 **메타데이터** 레벨에서 관리하기 때문입니다. 데이터를 다시 쓰는 대신, 파티션 스키마를 변경한 후 **메타데이터만 업데이트**하면 됩니다. 예를 들어, 기존의 월별 파티셔닝을 일별 파티셔닝으로 변경하려고 할 때, Iceberg에서는 수 분 내로 이 작업이 완료됩니다. 이는 대규모 데이터를 다룰 때 **시간과 비용을 획기적으로 절약**하게 해줍니다. 그렇기 때문에 특정 날짜나 특정 고객에 대한 쿼리가 많아지면 **고객 ID 기반 파티셔닝**이나 **일별 파티셔닝**으로 쉽게 전환 할 수 있으며, 유연하게 변경하여 성능을 최적화 할 수 있습니다.

이 부분을 조금 더 뜯어보면 다음과 같습니다. 

Iceberg의 파티션 정의는 **Partition Spec**이라는 메타 데이터 레벨에서 관리됩니다.

```JSON
{
  "partition_specs": [
    {
      "spec_id": 0,
      "fields": [
        {"source_id": 3, "transform": "month", "field_id": 1000, "name": "transaction_date_month"}
      ]
    }
  ],
  "data_files": [
    {"path": "s3a://iceberg-data/part-00000.parquet", "partition": {"transaction_date_month": "2024-01"}}
  ] 
}
```

여기에 사용자가, 다음과 같은 쿼리를 날렸다고 가정하겠습니다.

```sql
ALTER TABLE transactions 
SET PARTITION SPEC (
  YEAR(transaction_date),
  MONTH(transaction_date),
  DAY(transaction_date)
);
```

이러면, Iceberg는 테이블의 메타데이터 파일(metadata.json)을 로드하여 현재의 Partition Spec을 확인합니다.

이 파일에는 기존 파티셔닝 방식(`month(transaction_date)`)에 대한 정의와 관련된 데이터 파일 정보가 포함되어 있습니다.

이후 사용자가 요청한 새로운 파티션 스키마(`year`, `month`, `day`)를 기반으로 새로운 Partition Spec을 생성합니다.

이 새로운 스키마를 새로운 `spec_id` 로 관리합니다.

```JSON
{
  "partition_specs": [
    {
      "spec_id": 0,
      "fields": [
        {"source_id": 3, "transform": "month", "field_id": 1000, "name": "transaction_date_month"}
      ]
    },
    {
      "spec_id": 1,
      "fields": [
        {"source_id": 3, "transform": "year", "field_id": 1001, "name": "transaction_date_year"},
        {"source_id": 3, "transform": "month", "field_id": 1002, "name": "transaction_date_month"},
        {"source_id": 3, "transform": "day", "field_id": 1003, "name": "transaction_date_day"}
      ]
    }
  ],
  "data_files": [
    {"path": "s3a://iceberg-data/part-00000.parquet", "partition": {"transaction_date_year": "2024", "transaction_date_month": "01", "transaction_date_day": "01"}}
  ]
}
```

이후 아이스버그는 새로운 파티션 스펙인 spec_id = 1 을 활성화 합니다. 이전 spec_id = 0 은 보존되며, 과거 데이터와의 호환성을 유지하게 됩니다.

사실 지금 메타데이터니, 매니패스트니 이런 단어들이 좀 생소하실 수도 있습니다. 이 부분에 대해서는 추후 아이스 버그의 아키텍처 부분에서 상세히 설명드리도록 하겠습니다.

