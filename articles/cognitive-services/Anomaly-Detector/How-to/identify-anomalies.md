---
title: 시계열 데이터에서 Anomaly Detector API를 사용하는 방법
titleSuffix: Azure Cognitive Services
description: 일괄 또는 스트리밍 데이터에서 변칙을 검색하는 방법을 알아봅니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: mbullwin
ms.openlocfilehash: ae8759bd10096737b400fe672c3555ff5fd41585
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2021
ms.locfileid: "113296634"
---
# <a name="how-to-use-the-anomaly-detector-univariate-api-on-your-time-series-data"></a>방법: 시계열 데이터에서 Anomaly Detector 단변량 API를 사용하는 방법  

[Anomaly Detector API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)는 두 가지 변칙 검색 방법을 제공합니다. 시계열 전체에서 일괄 처리로 변칙을 검색하거나 최신 데이터 포인트의 변칙 상태를 검색하여 데이터가 생성될 때 변칙을 검색할 수 있습니다. 검색 모델은 각 데이터 포인트의 예상 값과 변칙 검색의 상한 및 하한을 포함하여 변칙 결과를 반환합니다. 이러한 값을 사용하여 일반 값의 범위와 데이터의 변칙을 시각화할 수 있습니다.

## <a name="anomaly-detection-modes"></a>Anomaly Detection 모드

Anomaly Detector API는 검색 모드(일괄 처리 및 스트리밍)를 제공합니다.

> [!NOTE]
> 다음 요청 URL은 구독에 적합한 엔드포인트와 결합되어야 합니다. 예: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>일괄 검색

지정된 시간 범위 동안 데이터 포인트 일괄 처리를 통해 변칙을 검색하려면 시계열 데이터에 다음 요청 URI를 사용합니다. 

`/timeseries/entire/detect`. 

시계열 데이터를 한꺼번에 보내면 API는 전체 계열을 사용하여 모델을 생성하여 이를 통해 각 데이터 포인트를 분석합니다.  

### <a name="streaming-detection"></a>스트리밍 검색

스트리밍 데이터에 대해서 지속적으로 변칙을 검색하려면 최신 데이터 포인트와 함께 다음 요청 URI를 사용합니다. 

`/timeseries/last/detect`. 

새 데이터 포인트를 생성하면서 보내면 실시간으로 데이터를 모니터링할 수 있습니다. 보내는 데이터 포인트를 사용하여 모델이 생성되고 API는 시계열의 최신 포인트가 변칙인지 여부를 확인합니다.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>변칙 검색의 하한 및 상한 조정

기본적으로 변칙 검색의 상한 및 하한은 `expectedValue`, `upperMargin` 및 `lowerMargin`을 사용하여 계산됩니다. 다른 경계가 필요한 경우 `marginScale`을 `upperMargin` 또는 `lowerMargin`에 적용하는 것이 좋습니다. 경계는 다음과 같이 계산됩니다.

|경계  |계산  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

다음 예에서는 다른 민감도에서의 Anomaly Detector API 결과를 보여줍니다.

### <a name="example-with-sensitivity-at-99"></a>민감도 99의 예

![기본 민감도](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>민감도 95의 예

![99 민감도](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>민감도 85의 예

![85 민감도](../media/sensitivity_85.png)

## <a name="next-steps"></a>다음 단계

* [Anomaly Detector API란?](../overview.md)
* [빠른 시작: Anomaly Detector를 사용하여 시계열 데이터에서 변칙 검색](../quickstarts/client-libraries.md)
