---
title: Anomaly Detector 다변량 API를 사용하기 위한 예측 유지 관리 아키텍처
titleSuffix: Azure Cognitive Services
description: Anomaly Detector 다변량 API를 사용하여 예측 유지 관리를 위한 시계열 데이터에 변칙 검색을 적용하는 참조 아키텍처입니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: 변칙 검색, 기계 학습, 알고리즘
ms.openlocfilehash: 9de0a774ab2464d3b59ee8875453255e92ea5c45
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2021
ms.locfileid: "113297247"
---
# <a name="predictive-maintenance-solution-with-anomaly-detector-multivariate"></a>Anomaly Detector(다변량)을 사용한 예측 유지 관리 솔루션

수많은 다양한 업계에서는 장비에서 데이터 처리를 통해 위험을 줄이고 실행 가능한 인사이트를 얻으려면 예측 유지 관리 솔루션이 필요합니다. 예측 유지 관리는 온라인 모니터링을 수행하여 장비의 상태를 평가합니다. 목표는 성능 저하 또는 고장이 발생하기 전에 장비를 유지 관리하는 것입니다.

장비의 각 구성 요소가 진동, 방향, 회전 등의 많은 신호를 생성할 수 있으므로 장비의 성능 상태를 모니터링하는 것이 어려울 수 있습니다.  이러한 신호에 암시적 관계가 있고 신호를 함께 모니터링하고 분석해야 하는 경우에는 작업이 훨씬 더 복잡할 수 있습니다. 다양한 신호에 서로 다른 규칙을 정의하고 수작업으로 신호 간 상관 관계를 지정하는 것은 비용이 많이 들 수 있습니다. Anomaly Detector의 다변량 기능을 사용하면 다음이 가능합니다.

* 여러 상관 관계가 있는 신호를 함께 모니터링할 수 있고 신호 간 상관 관계가 모델에서 고려됩니다.
* 캡처된 각 변칙에서 다양한 신호의 기여 순위는 변칙 설명 및 인시던트 근본 원인 분석에 도움이 될 수 있습니다.
* 다변량 변칙 검색 모델은 감독되지 않는 방식으로 빌드됩니다. 모델은 특별히 다양한 유형의 장비에 대해 학습될 수 있습니다.

여기서는 Anomaly Detector 다변량을 기반으로 하는 예측 유지 관리 솔루션용 참조 아키텍처를 제공합니다.

## <a name="reference-architecture"></a>참조 아키텍처

[ ![산업 장비를 사용하여 에지에서 수집되는 센서 데이터에서 시작되고 Anomaly Detector가 실행된 후 생성되는 인시던트 경고의 종료 출력까지 처리/분석 파이프라인을 추적하는 아키텍처 다이어그램](../media/multivariate-architecture/multivariate-architecture.png) ](../media/multivariate-architecture/multivariate-architecture.png#lightbox)

위 아키텍처에서 센서 데이터에서 들어오는 스트리밍 이벤트는 Azure Data Lake에 저장된 후 데이터 변환 모듈을 통해 처리되어 시계열 형식으로 변환됩니다. 한편, 스트리밍 이벤트는 학습된 모델을 사용하여 실시간 검색을 실행합니다. 일반적으로 이 아키텍처에는 ‘Bridge 서비스’와 같이 다변량 모델 수명 주기를 관리하는 모듈이 있습니다.

**모델 학습**: Anomaly Detector 다변량을 사용하여 구성 요소나 장비의 변칙을 검색하기 전에. 이 엔터티에서 생성된 특정 신호(시계열)에 대해 모델을 학습시켜야 합니다. ‘Bridge 서비스’는 기록 데이터를 페치하고, Anomaly Detector에 학습 작업을 제출한 다음, 모델 ID를 ‘모델 메타’ 스토리지에서 유지합니다. 

**모델 유효성 검사**: 특정 모델의 학습 시간은 학습 데이터 볼륨에 따라 달라질 수 있습니다. ‘Bridge 서비스’는 모델 상태와 진단 정보를 정기적으로 쿼리할 수 있습니다. 모델을 온라인으로 전환하기 전에 모델 품질의 유효성을 검사해야 할 수 있습니다. 시나리오에 레이블이 있는 경우 해당 레이블을 사용하여 모델 품질을 확인할 수 있습니다. 그렇지 않으면 진단 정보를 사용하여 모델 품질을 평가할 수 있으며, 학습된 모델을 사용하여 기록 데이터에 대해 검색을 수행하고 결과를 평가하여 모델의 유효성을 역방향 테스트할 수도 있습니다.

**모델 유추**: 온라인 검색은 유효한 모델을 사용하여 수행되며 결과 ID는 ‘유추 테이블’에 저장할 수 있습니다. 학습 프로세스와 유추 프로세스는 둘 다 비동기 방식으로 수행됩니다. 일반적으로 검색 작업은 몇 초 내에 완료할 수 있습니다. 검색에 사용되는 신호는 학습에 사용된 것과 동일한 신호여야 합니다. 예를 들어, 학습에 진동, 방향 및 회전을 사용하는 경우 검색에 세 개의 신호를 입력으로 포함해야 합니다.

**인시던트 경고** 결과 ID를 사용하여 검색 결과를 쿼리할 수 있습니다. 각 결과에는 각 변칙의 심각도 및 기여 순위가 포함됩니다. 기여 순위는 이 변칙이 발생한 이유 및 해당 인시던트를 초래한 신호를 파악하는 데 사용할 수 있습니다. 심각도에 대해 서로 다른 임계값을 설정하여 유지 관리 작업을 수행하기 위해 현장 엔지니어에게 보낼 경고 및 알림을 생성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작](../quickstarts/client-libraries-multivariate.md).
- [모범 사례](../concepts/best-practices-multivariate.md): 이 문서는 다변량 API에서 사용할 권장 패턴과 관련이 있습니다.