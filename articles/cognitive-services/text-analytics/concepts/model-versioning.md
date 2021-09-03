---
title: Text Analytics v3에서 모델 버전 지정
titleSuffix: Azure Cognitive Services
description: 데이터에 사용되는 Text Analytics API 모델을 지정하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 06/17/2021
ms.author: aahi
ms.openlocfilehash: d1804505a73be2db8d7088caf74063381fa0ba33
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528678"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Text Analytics API의 모델 버전 관리

Text Analytics API 버전 3을 사용하면 데이터에 사용되는 모델 버전을 선택할 수 있습니다. 선택적 `model-version` 매개 변수를 사용하여 API 요청에서 모델의 버전을 선택합니다. 예: `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01` 이 매개 변수를 지정하지 않으면 API가 안정적인 최신 버전으로 기본 설정됩니다. 

## <a name="available-versions"></a>사용 가능한 버전

아래 표를 사용하여 호스팅된 각 엔드포인트에서 지원하는 모델 버전을 찾습니다.


| 엔드포인트                        | 지원되는 버전                                     | 최신 버전 |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`, `2020-09-01`, `2021-01-05` | `2021-01-05`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2021-01-15`,`2021-06-01`  | `2021-06-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`, `2021-01-15`  | `2021-01-15`   |
| `/entities/health`              | `2021-05-15`                           | `2021-05-15`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`, `2021-06-01`  | `2021-06-01`   |


이러한 모델의 업데이트에 대한 자세한 내용은 [새로운 기능](../whats-new.md)을 참조하세요.

## <a name="extractive-summarization"></a>추출 요약

추출 요약은 `version 3.1-preview.1`부터 비동기 `analyze` 엔드포인트를 사용하여 제공됩니다. 

현재 모델 버전은 `2021-08-01`입니다.

## <a name="text-analytics-for-health"></a>의료 분야 Text Analytics

[의료 분야 Text Analytics](../how-tos/text-analytics-for-health.md) 컨테이너는 위의 API 엔드포인트와는 별도의 모델 버전 관리를 사용합니다.  컨테이너 이미지당 하나의 모델 버전만 사용할 수 있습니다.

| 엔드포인트                        | 컨테이너 이미지 태그                     | 모델 버전 |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `3.0.016230002-onprem-amd64` 또는 최신            | `2021-05-15`  |
| `/entities/health`              | `3.0.015370001-onprem-amd64`            | `2021-03-01`  |
| `/entities/health`              | `1.1.013530001-amd64-preview`           | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |


## <a name="next-steps"></a>다음 단계

* [Text Analytics 개요](../overview.md)
* [감정 분석](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [엔터티 인식](../how-tos/text-analytics-how-to-entity-linking.md)
