---
title: Git Flow vs Github Flow
author: daehong
date: 2024-07-23 20:55:00 +0800
categories: [Personal IT Study, Git]
tags: [Git, Github, Gitlab, Git Flow, Github Flow]
render_with_liquid: false
pin: true
image:
  path: /assets/img/thumbnail/ci-cd.png
  lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
  alt: CI/CD Introduction
---

## ✏️ CI/CD 란?
---

- Application 개발 단계부터 배포 때까지의 모든 단계를 자동화 하여 좀 더 효율적이고 빠르게 사용자에게 빈번히 배포 할 수 있는 것을 의미함.

- 개념만을 두고 보면 자동화와 직접적인 관련이 있지만 않지만, 자동화라는 키워드는 CI/CD 라는 단어에 거의 항상 따라붙는다. 또한 CI/CD 는 DevOps 엔지니어의 핵심 업무라고 불리기도 한다.

<br>

## ✏️ CI/CD 가 필요한 이유
---

- 프로덕트 품질을 낮추지 않기 위해

- 불필요한 비용을 줄이기 위해

- 주변 동료와 업무를 공유하기 위해

- 개발자가 개발에만 집중할 수 있도록

<br>

## ✏️ CI ( Continuous Integration )
---

- CI 는 **지속적인 통합**이라는 의미이다.

- Application 의 새로운 기능이나 버그 수정 등으로 코드가 바뀌게 되고, 그걸 주기적으로 빌드 및 테스트 되면서 **공유하고 있는 Repository 에 Merge** 되는 것을 의미한다.

- 그리고 CI 에는 크게 2가지 방법이 있다.
    
	1. **코드 변경 사항을 주기적으로 빈번하게 Merge 하는 방법**
        
		- 지속적으로 짧은 주기로 통합하는 방법이며, 동시에 Merge 가 잘되고 충돌은 없는지 확인하는 방법이다.
        
		- 만약 짧은 주기로 Merge 하지 않으면, 완전히 독립적인 파트가 아닌 이상 Conflict 가 일어날 확률이 매우 높아지기 때문이다.
    
	2. **통합 단계의 자동화**
        
		- 위 1번의 방법은 다 좋은데, 귀찮다는 단점이 있다.
            
			- Build 하고 Test 하는 과정은 굳이 사람이 하지 않더라도 되는 작업이며,
            
			- 1번처럼 빈번하게 통합을 하면 개발 시간보다 빌드 하고 테스트하는 시간이 더 많아질 수도 있다.
            
			- 그래서 자동화를 시켜주는 것이다.
        
		- 통합 단계를 자동화하게 되면, 거기서 발생하는 버그만 체크하여 해결해주면 된다.
        
		- Jenkins 나 Github Action 등을 활용하여 자동화 할 수 있다.


- **CI의 장점**
   
    - 코드 검증에 들어가는 시간이 줄어든다.
    
	- 개발의 편의성이 증가한다.
    
	- 항상 테스트 코드를 통과한 코드만이 Repository 에 올라가기 때문에, 좋은 코드 퀄리티를 유지 할 수 있다.

- **CI의 단점**
    
	- DevOps 와 같이 CI 를 구축 할 수 있는 사람이 필요하며, 직접 하려면 그 만큼의 공부와 이해가 필요하다.

<br>

## ✏️ CD ( Continuous Delivery  —>  Continuous Deployment )
---

- CD 는 **지속적인 제공**이라는 의미와, **지속적인 배포**라는 의미를 동시에 가지고 있다.

- CI 에서 Build 되고 Test 된 후에, 배포 단계에서 release 할 준비 단계를 거치고, 문제가 없는지, 수정해야 하는 부분은 없는지 개발자가 검증하는 검증을 하게 된다. 검증 이후 수**동적으로 배포를 하는 것을 Continuous Delivery** 라고 하며, **자동화 하여 배포하는 것을  Continuous Deployment** 라고 한다.

- **CD 의 장점**
    
	- 개발자가 배포보다는 개발 자체에 신경 쓸 수 있도록 도와준다.
    
	- CI 까지 잘 되어있다면, 원클릭으로 수작업 없이 빌드, 테스트, 배포까지 자동화 할 수 있다.

- **CD 의 단점**
    
	- DevOps 와 같이 CD 를 구축 할 수 있는 사람이 필요하며, 직접 하려면 그 만큼의 공부와 이해가 필요하다.

<br>

![image](https://github.com/user-attachments/assets/64d5ba60-c0d6-48ec-9887-46e455b3f7ea)

<br>

## ✏️ CI/CD 종류
---

- Jenkins, Travis CI, Circle CI, GitHub Action 등이 있음.

- AWS Lambda 처럼 서버리스 플랫폼을 이용하여 배포 할 수도 있음.

- Docker 또는 Kubernetes 를 이용하여 컨테이너 기반의 Application 을 배포 할 수도 있음.

![image](https://github.com/user-attachments/assets/6c54daf1-9cc7-418c-a7f0-927477f93ed7)

<br>


## ✏️ 참고사항
---

- [[CICD] GitHub Action으로 CI/CD 구축하기 (velog.io)](https://velog.io/@sangwoong/CICD-GitHub-Action%EC%9C%BC%EB%A1%9C-CICD-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0)
    - 해당 내용을 참고하여 Github Action 으로 CI/CD 환경을 구축 할 수 있다.