---
title: AWS S3 Introduction
author: daehong
date: 2024-06-23 23:23:00 +0800
categories: [Group Study, AWS-Associate-Developer]
tags: [AWS, Study, AWS S3, Amazon Web Services, Simple Storage Service, AWS Certified Developer, Associate, DVA-C02]
render_with_liquid: false
pin: true
image:
  path: /assets/img/thumbnail/java-final.png
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: What is AWS S3?
---

### ✅ Amazon S3란 ?
---
- Amazon 에서 제공하는 저장 서비스이며, S3 는 `Simple Storage Service` 의 줄임 말이다.

- 업계 최고의 확장성과 데이터 가용성 및 보안과 성능을 제공하는 `온라인 오브젝트(객쳬) 스토리지 서비스`이다.

    - `온라인 오브젝트(객쳬) 스토리지 서비스` 는 데이터를 온라인에서 객체 형태로 저장할 수 있게 해주는 서비스라고 볼 수 있다.
	
	- 또한, 온라인 이라는 단어가 쓰이는 이유는, `Data 조작에 HTTP / HTTPS 를 통한 API 가 사용되기 때문`이다.

- Data 양에 대한 `비용이 저렴`하고, `데이터 양도 무한`에 가깝다.

- `탄성적인( Elastic ) 성질`이 있기 때문에, 별도의 Storage 확장, 축소에 대해 서비스 사용자가 신경을 쓰지 않아도 되는 특징이 있다.

![image](https://github.com/JeonDaehong/JeonDaehong/assets/90895144/da4c573c-be96-4755-8b22-baf602626d3c)

<br>

### ✅ Amazon S3 Use Case
---
- FTP 서버처럼 단순한 파일 저장 영역으로 사용하기 위함.

- 다른 AWS 서비스의 사용 로그 저장 ( 기록 보관 )

- 정적 웹 서비스 호스팅

    - 동적 웹페이지는 EC2에, 정적 웹페이지는 S3에 서비스하면, 성능을 높이고 비용을 절감 할 수 있다.
	
- EBS 스냅샷의 저장 영역 용도 ( + 데이터 복구 )

    - `EBS ( Elastic Block Stor )` 는 EC2 인스턴스에 사용되는 영구적인 블록 스토리지 서비스이다. 일종의 SSD나 하드라고 생각하면 된다.
    
	- 이러한 EBS 의 스냅샷을 S3에 저장하여 유사시에 백업 및 복구를 할 수 있다.

- 비디오 파일, 이미지, 미디어 호스팅

- `Glacier` 와의 연동으로 비용 절감 및 규정 준수 가능
    
	- Glacier 는 백업만을 위한 데이터 저장 서비스이다.
    
	- 백업 등 무슨 일이 있기 전까지는 절대 열어 볼 일 없는 데이터를 저장하며, 안정성이 높고 가격이 저렴하다.
	
<br>
	
### ✅ Amazon S3 특징
---
- S3는 `객체로 된 파일을 다루는 저장소`이며, EBS 의 반대라고 할 수 있다.

- 이미지, 동영상, 파일 등을 저장 할 수만 있지 `구동은 할 수 없다.`

- 파일 저장에 최적화 되어있으므로, `용량 추가나 성능을 높이는 작업이 필요 없다.`

- EC2 와 EBS로 구축하는 것보다 `훨씬 저렴`하다.

- 이미 `수천 대 이상의 고성능 웹 서버로 구성`되어 있기 때문에 Auto Scaling 이나, Load Balancing 에 신경 쓰지 않아도 된다.

- 웹 하드 서비스와 비슷하지만, 별도의 클라이언트 설치 없이 HTTP 프로토콜로 파일을 업로드하고 다운로드 할 수 있다.

<br>

### ✅ S3 Bucket 과 Object
---
- `Bucket` 은 Object 를 저장하고 관리하는 역할을 하며, `Object` 는 데이터와 메타 데이터를 구성하고 있는 저장 단위를 의미한다.

- 즉, S3 에 저장되는 모든 파일이나 데이터를 Object 라고 하면 되고, Bucket 은 그걸 저장하는 최상단 Directory 라고 생각하면 된다. ( 실제로 Directory 개념은 없음. )

- daehong-bucket 이라는 Bucket 에, testFile.png 라는 Object 를 저장한다고 가정하면, [`http://daehong-bucket.s3.amazonaws.com/testFile.ong`](http://daehong-bucket.s3.amazonaws.com/testFile.ong) 라는 URL 이 생성되게 된다. 이 URL 을 통해 이미지를 확인 할 수 있고, Application Service 와 연동한다면, 해당 파일을 다운로드 하는데에 사용 할 수 있다.

- **Bucket**

    - Amazon S3에서 생성되는 최상위의 디렉토리이며, Amazon S3에 저장된 객체의 컨테이너 이다.
    
	- S3상의 모든 객체는 버킷에 포함된다.
    
	- 버킷의 이름은 S3에서 유일해야 한다.  즉, 전세계에 어디에도 중복된 이름이 존재 할 수 가 없다.
    
	- S3는 전역 서비스처럼 보이지만 버킷은 사실상 리전에서 생성된다.
    
	- 한 계정 당 최대 100개의 버킷 사용 가능.
    
	- 버킷 주소는 [https://bucketname.s3.Region.amazonaws.com](https://bucketname.s3.region.amazonaws.com/) 형태로 이루어진다.
    
	- 버킷을 생성하면 default로 private상태이다.
    
	- 버킷 소유권은 이전할 수 없다.
    
	- 버킷 안에 다른 버킷을 둘 수 없다.
    
	- **명명 규칙**
    
		- 대문자 및 _ 사용 불가능
        
		- 길이는 3 ~ 63자
        
		- IP 주소 사용 불가능
        
		- 소문자 또는 숫자로 시작해야 함.
        
		- 이름을 xn-- 로 시작 불가능
        
		- 이름의 끝을 -s3alias 로 끝낼 수 없음.
        
		- 소문자, 숫자, - 만 사용 가능
		
- **Object**
    
	- S3 Object에는 `Key, Value, Version ID, Metadata, CORS(Cross Origin Resource Sharing)`와 같은 다양한 구성요소가 존재한다.
    
		- **1. Key : 전체 경로**
			
			- 버킷 내 객체의 고유한 식별자. 버킷 내 모든 객체는 정확히 하나의 키를 갖는다.
			
			- 버킷, 키 및 버전 ID의 조합은 각 객체를 고유하게 식별한다.
			
			- ex ) s3://my-bucket/my_folder1/another_folder/my_file.txt
			
			- Key 는 접두사 객체 이름으로 구성되어 있음.
			   
			   - s3://my-bucket/my_folder1/another_folder**/**my_file.txt
					
					- `접두사 ( prefix )` : my_folder1/another_folder/
					
					- `객체 이름` : my_file.txt
					
		- **2. Value : 파일의 데이터**
		  
		   - S3은 Key - Value 형태로 저장되지만, Key의 접두어 및 슬래시를 이용하여 폴더 개념 같이 사용 가능하다
		   
		   - 폴더 경로를 조회해보면 \Documents\img\test.png 이렇게 되어있는데, 이와 같이 url로 구성 되어진다.
	   
		- **3. Version Id : 파일의 버전 아이디**
			
			- Version ID는 S3의 고유 특징 중 하나 이다.
			
			- 같은 파일이지만 다른 버전으로 올릴 수 있게 돕는 인식표 라고 보면 된다. 만약 이전 버전으로 돌아가고 싶다면, Version ID를 통해 쉽게 복원시킬 수 있다.
		
		- **4. MetaData : 파일의 정보를 담은 데이터**
		
			- 최종 수정일
			
			- 파일 타입
			
			- 파일 소유자
			
			- 사이즈 ..등
			
		- **5. ACL : 파일의 권한을 담은 데이터 (접근이나 수정)**
		
		- **6. CORS(Cross Origin Resource Sharing) : 한 버켓의 파일을 지역을 무시하고 다른 버켓에서 접근 가능하게 해주는 기능**
	
		- `**Object 특징**`
			
			- 객체마다 각각의 접근 권한 설정 가능
			
			- 객체 metadata는 객체가 업로드 된 후에는 수정될 수 없고, 복사해서 수정해야 한다.
			
			- 객체의 metadata는 response header에 반환된다.
			
			- **객체의 용량 및 크기**
			   
				- Amazon S3에 저장할 수 있는 데이터의 전체 볼륨과 객체 수에는 제한이 없으나, 각 객체별로 0 byte 에서 최대 5TB 까지로 크기는 제한 됨.
				
				- 단일 Put 요청으로 업로드 가능한 객체의 최대 크기는 5GB 임.
				
				- 그러나 객체의 크기가 100MB 를 넘는 경우 `multi-part upload` 를 사용하는 것을 권장하며, 5GB 가 넘으면 어쩔 수 없이 multi-part upload 를 해야 함.
					
					- 즉, 5TB 를 업로드 하려면, 5GB 1,000개의 부품을 multi-part upload 해야 함.
					
					- **multi-part upload** 는 대용량 파일을 여러 부분으로 나누어 병렬로 업로드 하는 방식이며, 아래 3가지 단계를 거친다.
						
						- **1) 업로드 초기화**
							
							- 이 단계에서 S3 는 고유한 업로드 ID를 반환.
							
							- 이 ID 는 이후 모든 파트 업로드 요청에서 사용 됨.
						
						- **2) 파트 업로드**
							
							- 파일을 여러 부분으로 나누어 각 부분을 개별적으로 업로드.
							
							- 각 파트는 5MB 이상이어야 하며, 마지막 파트는 예외.
							
							- 각 파트 업로드가 완료되면, S3는 ETag 값을 반환한다. 이 ETag 값은 업로드 완료 단계에서 필요하다.
						
						- **3) 업로드 완료**
							
							- 모든 파트가 업로드 된 후, 업로드 완료 요청을 보냄.
						
<br>

### ✅ S3 Bucket 보안 / 권한 ( 접근 제어 )
---
- S3 는 전세계에서 접속 할 수 있는 Storage Service 이기 때문에, 기본적으로는 `private`으로 설정이 되어있다.

- 그렇기 때문에, 외부에서 접속하여 S3 Bucket을 제어할 필요가 있다면, Bucket을 통째로 public  으로 바꾸는 것이 아니라, `권한 제어를 통해 접근 제어를 설정`해주어야 한다.

- **S3 권한 설정 4가지**
    
	- **IAM**
     
		- 사용자를 생성하고, 사용자의 Bucket 권한 Access 를 관리
    
	- **액세스 제어 목록(ACL)**
    
		- 권한 있는 사용자에 대해 간단한 개별 객체(오브젝트)를 액세스 가능하게 만드는 설정
   
    - **버킷 정책(Bocket policy)**
      
		- 단일 S3 버킷 내 모든 객체에 대한 권한을 세부적으로 구성하는 정책
		
			![image](https://github.com/JeonDaehong/scenario-gpt-project/assets/90895144/b13f51b9-db0f-48fe-81e1-6acf1d2a35d0)
		
			- **JSON based policies**
			
				- `Resources`: 버킷과 객체
				
				- `Effect`: Allow / Deny
				
				- `Actions`: 허용하거나 거부할 수 있는 API 집합
				
				- `Principal`: 정책을 적용할 계정 또는 사용자
				
			- **S3 버킷 정책을 사용하여 다음을 수행할 수 있다.**
			
				- 버킷에 대한 공개 액세스 부여
				
				- 객체를 업로드할 때 암호화 강제화
				
				- 다른 계정에 액세스 권한 부여 (Cross Account)
				
			- **Bucket settings for Block Public Access**
			
				- 기업 데이터 유출을 방지하기 위한 `추가 보안 계층`
				
				- 버킷이 절대로 공개하면 안 되는 경우, 이 설정을 그대로 두면 됨
				
				- S3 버킷 정책을 설정하여 공개로 만들더라도 이 설정이 활성화되어 있다면 버킷은 절대 공개되지 않음
				
				- `계정 수준에서 설정 가능`
				
				- **주요 설정에는 4가지가 있음.**
				
					- 새로운 ACL(Access Control List)에 대한 공공 접근 차단
					
					- 기존 ACL에 대한 공공 접근 차단
					
					- 새로운 공공 버킷 정책 차단
					
					- 기존 공공 버킷 정책 차단
					
			- **쿼리 문자열 인증(Pre-signed URL)**
			
				- 임시 URL을 사용하여 다른 사용자에게 기간 제한(임시 권한) 액세스를 부여
				
				![image](https://github.com/JeonDaehong/scenario-gpt-project/assets/90895144/acae098a-ba55-4840-a2d8-6379368a8576)
				
		- **그 외**
		
			- **Encryption**
			
				- 암호화 키를 이용하여 객체를 암호화.
				
		- **또한, IAM Principal 이 S3 객체에 Access 하려면,**
			
			- IAM User 권한이 `Allow`이거나, Resource 정책이 `Allow`상태여야 하고,
			
			- 명시적인 `Deny`가 없어야 함.