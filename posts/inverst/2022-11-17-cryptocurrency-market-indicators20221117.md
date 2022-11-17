---
title: 암호화폐(비트코인) 시장지표 20221117
author: macle
date: 2022-11-17 06:30:00 +0800
categories: [투자]
tags: [트레이딩,비트코인,암호화폐,투자]
---


# 시장거래량 지표
![시장거래량](https://github.com/macle86/macle86.github.io/blob/master/img/data/20221117/2022-11-17market-mvd.png?raw=true)

좌측값은 500일평균거래량의 하위90% 대비 지금 거래량의 이격도평균이다. 각 종목의 이격도 최대값은 1000이다
가격은 저점을 갱신하고 있지만 거래량은 살아나고 있다.


# 시장 거래대금 지표
![시장거래대금](https://github.com/macle86/macle86.github.io/blob/master/img/data/20221117/2022-11-17market-mvtd.png?raw=true)

좌측값은 500일평균거래대금의 하위90% 대비 지금 거래량의 이격도평균이다. 각 종목의 이격도 최대값은 1000이다


# 생존비율
![시장생존비율](https://github.com/macle86/macle86.github.io/blob/master/img/data/20221117/2022-11-17market-survival_rate.png?raw=true)

시장 생존비율이다. 유명 유투버의 영상을 보고 만든 지표인데. 주식 시장에서 vix 40이상 생존비율 10%미만 거래량 급증의 현상이 발생하면 바닥일 확률이 높다고 하였다.

생존 비율은 200일 이동평균선 위에 있는 종목 수의 비율이다

생존 비율만 보면 암호화폐 시장은 최근 FTX 뱅크런 이슈로 처참한 수준이다.

# ADR
![ADR](https://github.com/macle86/macle86.github.io/blob/master/img/data/20221117/2022-11-17market-adr.png?raw=true)

주식 시장 지표로 사용하는걸 암호화폐 시장 지표에도 추가하였다.
상승종목수/하락종목 수의 14일 지수이동평균 값이다.

단 최대값은 200으로 고정하였다.

# ADMR( ADR 변형)
![ADMR](https://github.com/macle86/macle86.github.io/blob/master/img/data/20221117/2022-11-17market-admr.png?raw=true)

이부분은 봇을 개발하다보니까 숫자의 절대치를 두고자 만든값으로 (상승종목수-하락종목수)/마켓종목수 로 -100~100 까지 움직이는 값을 추가하였다 모양은 ADR값과 비슷하다.
14일 지수이동평균 값이다.

# 신고가 신저가 지수(NHNL)
![NHNL](https://github.com/macle86/macle86.github.io/blob/master/img/data/20221117/2022-11-17market-nhnl.png?raw=true)

100일 기준의 신고가 종목- 신저가 종목의 수를 14일 지수이동평균화한 데이터이다.

100일 값을 잡은건 암호화폐시장이 주식시장보다 3~4시 빠르게 움직인다고 생각하여 임의의 값으로 하였다 주식시장에서는 52주를 사용하는 편이다.

# 거래량 급증 종목 지수
![NHNL](https://github.com/macle86/macle86.github.io/blob/master/img/data/20221117/2022-11-17market-stv.png?raw=true)

거래량 급증 종목의 수는 시장의 고점이나 저점에서 거래량이 급증하는 종목의 수가 많아지는것을 찾고자 개발하였다.

좌측값은 500일평균거래량의 하위90% 대비 이격도가 500이상인 종목의 수를 전체종목의 수로 나눈값의 백분율 값이다.



# 기술적 분석은 참고만
기술적 분석 지표는 개인마다 해석하는 방법이 다르다. 특정 지표로 시장상황을 예측하기는 어렵다. 지금의 시장분위기를 참고하는 정도만 보는게 좋을것 같다.

오늘 업비트 바이낸스 비트코인 프리미엄은 3% 정도로 과열되어 있다고 보기는 어렵다. 단 시장이 급격한 공포에 있으면 역프현상이 나타나는데 프리미엄으로 그런현상은 보이지 않는다.

지금이 저점이라고 매수하고자 하는 투자자도 일부 있는것 처럼 보인다.

평균적인 수준이다.

# communication
시스템 매매봇 관련 오픈톡
- https://open.kakao.com/o/g6vzOKqb
    - 참여코드: runon

## 오픈소스
일부는 오픈소스로 운영하고 있습니다. 단 운영되고 있는 매매 봇은 오픈소스는 아닙니다.

- [github.com/runonio/trading](https://github.com/runonio/trading)
- [github.com/runonio/cryptocurrency](https://github.com/runonio/cryptocurrency)
