---
layout: post
title:  "Apache Iceberg Deep Dive - How Does It Address the Limitations of Hive?"
author: daehong
categories: [ Apache, Iceberg, Bigdata ]
image: assets/images/apache-iceberg.jpg
---

## 🧊 서론

**Apache Iceberg**는 Toss, Kakao, 11번가, LINE 할 거 없이 많은 곳에서 도입하고 있는 테이블 포멧 형태의 기술이며, 심지어 네이버 웹툰에서는 아이스버그 구축 경험이 있는 사람을 대놓고 우대사항에 적어두고 있을만큼, 이름과는 상반되게 현재 뜨겁게 타오르는 태양과도 같은 기술입니다.

새로운 기술을 접목 하는데에 있어, 기업과 개발자의 관점은 같으면서도 다른 것을 중점으로 두고 있습니다. 기업은 해당 기술을 도입함으로써, 얼만큼의 금전적 이윤을 얻을 수 있는지를 추구하고, 개발자는 그 기술을 도입함으로써 자신의 기술적 스킬 향상과 가치를 상승 시킬 수 있습니다.

이렇게 이윤과 성장이라는 다른 중점은 가지고 있지만, 이익을 추구한다는 관점은 같다고 볼 수 있습니다.

그러한 면에서 **Apache Iceberg** 은 기업 입장에서도, 개발자 입장에서도 반드시 도입해야 할 기술이라는 결론을 먼저 말씀드리며, 지금부터 왜 **Apache Iceberg**를 도입해야 하는지, 대체 **Apache Iceberg**가 무엇인지 이 거대한 빙산을 조금씩 녹여가며 중요한 부분들에 대해 이야기하도록 하겠습니다.