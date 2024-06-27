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

### Amazon S3란 ?

- Amazon 에서 제공하는 저장 서비스이며, S3 는 `Simple Storage Service` 의 줄임 말이다.

- 업계 최고의 확장성과 데이터 가용성 및 보안과 성능을 제공하는 `온라인 오브젝트(객쳬) 스토리지 서비스`이다.

    - `온라인 오브젝트(객쳬) 스토리지 서비스` 는 데이터를 온라인에서 객체 형태로 저장할 수 있게 해주는 서비스라고 볼 수 있다.
	
	- 또한, 온라인 이라는 단어가 쓰이는 이유는, `Data 조작에 HTTP / HTTPS 를 통한 API 가 사용되기 때문`이다.

- Data 양에 대한 `비용이 저렴`하고, `데이터 양도 무한`에 가깝다.

- `탄성적인( Elastic ) 성질`이 있기 때문에, 별도의 Storage 확장, 축소에 대해 서비스 사용자가 신경을 쓰지 않아도 되는 특징이 있다.

<br>

### Amazon S3 Use Case

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
	
### Amazon S3 특징

- S3는 `객체로 된 파일을 다루는 저장소`이며, EBS 의 반대라고 할 수 있다.

- 이미지, 동영상, 파일 등을 저장 할 수만 있지 `구동은 할 수 없다.`

- 파일 저장에 최적화 되어있으므로, `용량 추가나 성능을 높이는 작업이 필요 없다.`

- EC2 와 EBS로 구축하는 것보다 `훨씬 저렴`하다.

- 이미 `수천 대 이상의 고성능 웹 서버로 구성`되어 있기 때문에 Auto Scaling 이나, Load Balancing 에 신경 쓰지 않아도 된다.

- 웹 하드 서비스와 비슷하지만, 별도의 클라이언트 설치 없이 HTTP 프로토콜로 파일을 업로드하고 다운로드 할 수 있다.