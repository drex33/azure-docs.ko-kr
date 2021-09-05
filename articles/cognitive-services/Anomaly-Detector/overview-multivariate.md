---
title: Anomaly Detector 다변량 API란?
titleSuffix: Azure Cognitive Services
description: 새 Anomaly Detector 미리 보기 다변량 API의 개요입니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: 변칙 검색, 기계 학습, 알고리즘
ms.openlocfilehash: 2e74edb0f716862925bb19140a4425366ebab894
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113587664"
---
# <a name="multivariate-time-series-anomaly-detection-preview"></a>다변량 시계열 변칙 검색(미리 보기)

새로운 **다변량 변칙 검색** API를 사용하는 개발자는 기계 학습 지식 또는 레이블이 지정된 데이터 없이도 메트릭 그룹에서 변칙을 검색하는 고급 AI를 쉽게 통합할 수 있습니다. 이제 최대 300가지 신호 간 종속성 및 상관 관계가 자동으로 키 요소로 계산됩니다. 이 새로운 기능을 통해 소프트웨어 애플리케이션, 서버, 공장 기계, 우주선 또는 비즈니스 도구와 같은 복잡한 시스템의 장애를 사전에 방지할 수 있습니다.

![변칙이 강조 표시된 진동, 온도, 압력, 속도, 회전 속도의 변수에 관한 여러 시계열 선 그래프](./media/multivariate-graph.png)

예를 들어, 진동, 온도, 연료 압력 등의 20가지 신호를 생성하는 자동 엔진에 20개 센서가 있다고 가정해 보세요. 개별적인 신호 판독값으로는 시스템 수준 문제를 정확히 알 수 없지만 전체 신호 판독값은 엔진 상태를 나타낼 수 있습니다. 신호의 상호 작용이 일반적인 범위를 벗어나는 경우 다변량 변칙 검색 기능은 전문적으로 변칙을 감지할 수 있습니다. 기본 AI 모델은 비즈니스의 고유한 요구 사항을 이해하도록 데이터를 사용하여 학습되고 사용자 지정됩니다. 이제 개발자는 Anomaly Detector의 새로운 API를 통해 다변량 시계열 변칙 검색 기능을 예측 유지 관리 솔루션, 복잡한 엔터프라이즈 소프트웨어용 AIOps 모니터링 솔루션 또는 비즈니스 인텔리전스 도구에 쉽게 통합할 수 있습니다.

## <a name="when-to-use-multivariate-versus-univariate"></a>**다변량** 및 **단변량** 을 사용하는 경우 비교

자체 기록 데이터에만 기반하여 개별 시계열에서 정상 패턴을 벗어난 변칙을 검색하려는 경우에는 단변량 변칙 검색 API를 사용합니다. 예를 들어 매출 데이터 자체를 기준으로 일일 매출 변칙을 검색하거나 CPU 데이터만 기준으로 사용하여 CPU 급증을 검색하려고 합니다.

시계열 데이터 그룹에서 시스템 수준 변칙을 검색하는 것이 목표인 경우 아래 다변량 변칙 검색 API를 사용합니다. 특히, 개별 시계열이 많은 것을 알려 주지 않는 경우에는 시스템 수준 문제를 확인하기 위해 전체적으로 모든 신호(시계열 그룹)를 확인해야 합니다. 예를 들어 항공기, 석유 굴착 장비 또는 위성과 같은 고가의 물리적 자산이 있습니다. 각 자산에는 수십 또는 수천 가지 유형의 센서가 있습니다. 시스템 수준 문제가 있는지 여부를 결정하려면 해당 센서의 모든 시계열 신호를 확인해야 합니다.

## <a name="notebook"></a>Notebook

Anomaly Detector API(다변량)를 호출하는 방법을 알아보려면 이 [Notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb)을 사용해 보세요. 이 Jupyter Notebook은 API 요청을 보내고 결과를 시각화하는 방법을 보여 줍니다.

Notebook을 실행하려면 유효한 Anomaly Detector API **구독 키** 와 **API 엔드포인트** 를 가져와야 합니다. Notebook에서 유효한 Anomaly Detector API 구독 키를 `subscription_key` 변수에 추가하고 `endpoint` 변수를 사용자의 엔드포인트로 변경합니다.

## <a name="region-support"></a>지역 지원

Anomaly Detector 다변량의 미리 보기는 현재 10개 Azure 지역(동남 아시아, 오스트레일리아 동부, 캐나다 중부, 북유럽, 서유럽, 미국 동부, 미국 동부 2, 미국 중남부, 미국 서부 2 및 영국 남부)에서 사용할 수 있습니다.

## <a name="algorithms"></a>알고리즘

사용되는 알고리즘에 대한 정보는 다음 기술 문서를 참조하세요.

* 블로그: [다변량 변칙 검색 소개](https://techcommunity.microsoft.com/t5/azure-ai/introducing-multivariate-anomaly-detection/ba-p/2260679)
* 논문: [Graph Attention Network를 통한 다변량 시계열 변칙 검색](https://arxiv.org/abs/2009.02040)


> [!VIDEO https://www.youtube.com/embed/FwuI02edclQ]


## <a name="join-the-anomaly-detector-community"></a>Anomaly Detector 커뮤니티 참여

- [Microsoft Teams의 Anomaly Detector Advisors 그룹](https://aka.ms/AdAdvisorsJoin)에 조인

## <a name="next-steps"></a>다음 단계

- [자습서](./tutorials/learn-multivariate-anomaly-detection.md): 이 문서는 다변량 API를 사용하는 방법에 대한 엔드투엔드 자습서입니다.
- [빠른 시작](./quickstarts/client-libraries-multivariate.md).
- [모범 사례](./concepts/best-practices-multivariate.md): 이 문서는 다변량 API에서 사용할 권장 패턴과 관련이 있습니다.
