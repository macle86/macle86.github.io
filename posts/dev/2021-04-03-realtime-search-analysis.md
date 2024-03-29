---
title: 리얼타임 인메모리 검색, 분석, 통계
author: macle
date: 2021-04-03 21:30:00 +0800
categories: [개발]
tags: [java,인메모리,data,빅데이터,병렬처리]
---

- 아직 작성중인 글로 완성 된 내용이 아닙니다. 내용이 많아서 시간의 여유가 되는 대로 추가로 작성될 예정 입니다.

# 개요
테그클라우드, 의미네트워크, 로지트리, 트랜드분석 등의 분류, 통계등에 활용되는 기능은 (5,10,30)분 (1,6,12,24)시간 (1,7,30)일 (1,3,6,12)달 1년 단위의 마트데이터를 만들어서 활용할때가 많습니다.

비정형데이터(텍스트)는 한달데이터만 해도 수천만건에서 수억건이 되는경우가 많습니다.

서비스를 하려고 마트데이터를 고객사, 부서별 등으로 생성하면 아래와 같은 현상이 발생합니다.
- 분류체계가 약 100여개
- 수집채널이 약 100여개
- 기간정보가 약 10개
- 회사별로

위와같은 정보로 통계 분석이 돈다고 가정하면 배치프로그램은 하루에 (100*100*10*서비스를 신청한 고객회사 의 수) 고객사당 10만개의 배치가 돌게 되죠. 이러한 방식은 서비스를 하기가 어렵습니다. 관리비용(서버, 서버관리)이 높아지기 떄문입니다.

물론 카카오, 네이버처럼 많은 트래픽이 발생하는 대형 서비스의 경우는 저런 배치프로그램이 많은 경우가 더 괜찮을 수 있습니다.

단 하루에 조건을 걸어서 서비스를 호출하는 회수가 10회 이하인 회사가 많을때 비효율적인 서비스 모델이 됩니다.

이러한경우에 마트를 활용하지 않고 실시간 분석결과를 생성하게 하면 관리비용이 크게 줄기 때문에 서비스 비용도 저렴해질 수 있습니다.

이상황을 해결한 방법에 대한 내용입니다. 관련부분은 기술 특허도 진행중이 내용입니다.

아래 서비스들이 활용되는 서비스 입니다. 검색조건별, 분류별, 체녈별로 단어통게 분류통계 연관분석 혹은 다른형태의 데이터 분석에 활용되는 시각화 형태입니다.

![통계](https://raw.githubusercontent.com/macle86/macle86.github.io/master/img/realtime/stat.JPG)
![테그 클라우드](https://raw.githubusercontent.com/macle86/macle86.github.io/master/img/realtime/tagcloud.png)
![의미 네트워크](https://raw.githubusercontent.com/macle86/macle86.github.io/master/img/realtime/sna.png)
![기타차트](https://raw.githubusercontent.com/macle86/macle86.github.io/master/img/realtime/etc.jpg)

# 어떠한 상황 이었는지
- 한달에 약 1억건이상의 텍스트 데이터
- 채널 * 키워드(검색조건) * 분류 * 기간 등으로 마트화가 불가능 (고객사당 마트 10만개 이상)
- 고객사당 하루에 20회정도의 호출로 마트화가 비효율적
- 고객사는 30 개이상에서 사용하는 서비스
- 한번호출에 5초이하에 결과들이 보이기 시작하는 형태의 서비스

위의 조건들로 만족하려면 한번 호출될때 적게는 수천건 많개는 수십만건의 검색결과가 나옵니다. 이러한 문서들을 실시간성 분석 통계를 내려먼 데이터 처리 관점에 고민이 많이 필요한 상황 이었습니다.




