---
title: Anomaly Detector API란?
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API의 알고리즘을 사용하여 시계열 데이터에 변칙 검색을 적용합니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 02/16/2021
ms.author: mbullwin
keywords: 변칙 검색, 기계 학습, 알고리즘
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 24d30a475071667fa9f701776302aa81c652fbe2
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045050"
---
# <a name="what-is-the-anomaly-detector-univariate-api"></a>Anomaly Detector 일변량 API란?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Anomaly Detector API를 사용하면 기계 학습을 몰라도 시계열 데이터에서 변칙을 검색하고 모니터링할 수 있습니다. Anomaly Detector API의 알고리즘은 업계, 시나리오 또는 데이터 볼륨에 관계없이 가장 적합한 모델을 자동으로 식별하여 데이터에 적용하여 조정됩니다. 이 API는 시계열 데이터를 사용하여 변칙 검색, 예상 값 및 어떤 데이터 요소가 변칙인지에 대한 경계를 결정합니다.

![서비스 요청의 패턴 변경 내용 검색](./media/anomaly_detection2.png)

Anomaly Detector 사용에는 기계 학습에 대한 사전 지식이 필요하지 않으며 REST API를 통해 애플리케이션과 프로세스에 서비스를 쉽게 통합할 수 있습니다.

이 설명서에는 다음과 같은 유형의 문서가 포함되어 있습니다.
* [빠른 시작](./Quickstarts/client-libraries.md)은 서비스를 호출하고 짧은 시간 내에 결과를 얻을 수 있는 단계별 지침입니다. 
* [방법 가이드](./how-to/identify-anomalies.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.
* [개념 문서](./concepts/anomaly-detection-best-practices.md)에서는 서비스의 기능 및 기능에 대한 자세한 설명을 제공합니다.
* [자습서](./tutorials/batch-anomaly-detection-powerbi.md)는 보다 광범위한 비즈니스 솔루션에서 이 서비스를 구성 요소로 사용하는 방법을 보여주는 긴 가이드입니다.

## <a name="features"></a>기능

Anomaly Detector를 사용하면 시계열 데이터 전체에서 또는 실시간으로 발생하는 변칙을 자동으로 검색할 수 있습니다.

|기능  |Description  |
|---------|---------|
|실시간으로 변칙 검색. | 이전에 본 데이터 요소를 사용하여 최신 데이터가 변칙인지 확인하여 스트리밍 데이터에서 변칙을 검색합니다. 이 작업은 보내는 데이터 요소를 사용하여 모델을 생성하고 대상 포인트가 변칙인지 여부를 결정합니다. 생성하는 각각의 새 데이터 요소를 사용하여 API를 호출하면 데이터가 생성될 때 모니터링할 수 있습니다. |
|일괄 처리로 데이터 세트 전체의 변칙을 검색합니다. | 시계열을 사용하여 데이터 전체에 존재할 수 있는 변칙을 검색합니다. 이 작업은 전체 시계열 데이터를 사용하여 모델을 생성하고, 각 포인트는 동일한 모델로 분석됩니다.         |
|일괄 처리로 데이터 세트 전체의 변화 포인트를 검색합니다. | 시계열을 사용하여 데이터에 존재하는 추세 변화 포인트를 검색합니다. 이 작업은 전체 시계열 데이터를 사용하여 모델을 생성하고, 각 포인트는 동일한 모델로 분석됩니다.    |
| 데이터에 대한 추가 정보를 가져옵니다. | 데이터 및 관찰된 변칙에 대한 유용한 정보를 가져오며, 여기에는 예상되는 값, 변칙 경계 및 위치가 포함됩니다. |
| 변칙 검색 경계를 조정합니다. | Anomaly Detector API는 변칙 검색에 대한 경계를 자동으로 만듭니다. 이러한 경계를 조정하여 데이터에 잘 맞도록 데이터 변칙에 대한 API의 민감도를 높이거나 낮춥니다. |

## <a name="demo"></a>데모

이 [대화형 데모](https://aka.ms/adDemo)를 확인하여 Anomaly Detector가 작동하는 방식을 이해합니다.
데모를 실행하려면 Anomaly Detector 리소스를 만들고 API 키와 엔드포인트를 가져와야 합니다.

## <a name="notebook"></a>Notebook

Anomaly Detector API를 호출하는 방법을 알아보려면 이 [Notebook](https://aka.ms/adNotebook)을 사용해 보세요. 이 Jupyter Notebook은 API 요청을 보내고 결과를 시각화하는 방법을 보여 줍니다.

Notebook을 실행하려면 유효한 Anomaly Detector API **구독 키** 와 **API 엔드포인트** 를 가져와야 합니다. Notebook에서 유효한 Anomaly Detector API 구독 키를 `subscription_key` 변수에 추가하고 `endpoint` 변수를 사용자의 엔드포인트로 변경합니다.

## <a name="workflow"></a>워크플로

Anomaly Detector API는 RESTful 웹 서비스이며, HTTP 요청을 수행하고 JSON을 구문 분석할 수 있는 프로그래밍 언어에서 쉽게 호출할 수 있습니다.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

가입 후:

1. 시계열 데이터를 가져와서 유효한 JSON 형식으로 변환합니다. 데이터 준비 시 [모범 사례](concepts/anomaly-detection-best-practices.md)를 사용하면 최상의 결과를 얻을 수 있습니다.
1. 자신의 데이터로 Anomaly Detector API에 요청을 보냅니다.
1. 반환된 JSON 메시지를 구문 분석하여 API 응답을 처리합니다.

## <a name="algorithms"></a>알고리즘

* 사용되는 알고리즘에 대한 자세한 내용은 다음 기술 블로그를 참조하세요.
    * [Azure Anomaly Detector API 소개](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Azure Anomaly Detector의 SR-CNN 알고리즘 개요](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Microsoft에서 개발한 SR-CNN 알고리즘에 대해 자세히 알아보려면 [Microsoft의 시계열 Anomaly Detection Service](https://arxiv.org/abs/1906.03821)(KDD 2019에서 수락)를 참조하세요.

> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="service-availability-and-redundancy"></a>서비스 가용성 및 중복성

### <a name="is-the-anomaly-detector-service-zone-resilient"></a>Anomaly Detector 서비스는 영역 복원력이 있나요?

예. Anomaly Detector 서비스는 기본적으로 영역 복원력이 있습니다.

### <a name="how-do-i-configure-the-anomaly-detector-service-to-be-zone-resilient"></a>영역 복원력을 갖도록 Anomaly Detector 서비스를 구성하려면 어떻게 해야 하나요?

영역 복원력을 사용하도록 설정하기 위한 고객 구성은 필요 없습니다. Anomaly Detector 리소스의 영역 복원력은 기본적으로 제공되며 서비스 자체에서 관리합니다.

## <a name="deploy-on-premises-using-docker-containers"></a>Docker 컨테이너를 사용하여 온-프레미스 배포

[Anomaly Detector 컨테이너를 사용](anomaly-detector-container-howto.md)하여 온-프레미스에 API 기능을 배포합니다. Docker 컨테이너는 규정 준수, 보안 또는 기타 운영상의 이유로 서비스를 데이터에 더 가깝게 가져올 수 있습니다.

## <a name="join-the-anomaly-detector-community"></a>Anomaly Detector 커뮤니티 참여

* [Microsoft Teams의 Anomaly Detector Advisors 그룹](https://aka.ms/AdAdvisorsJoin)에 조인

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Anomaly Detector를 사용하여 시계열 데이터에서 변칙 검색](quickstarts/client-libraries.md)
* Anomaly Detector API [온라인 데모](https://github.com/Azure-Samples/AnomalyDetector/tree/master/ipython-notebook)
* Anomaly Detector [REST API 참조](https://aka.ms/anomaly-detector-rest-api-ref)