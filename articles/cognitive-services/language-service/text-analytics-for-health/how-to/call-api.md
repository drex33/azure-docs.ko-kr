---
title: 상태를 위해 Text Analytics 호출하는 방법
titleSuffix: Azure Cognitive Services
description: 의료 분야 Text Analytics를 사용하여 비구조적 임상 텍스트에서 의료 정보를 추출하고 레이블을 지정하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/19/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 0fe056a6d325a164a674cf084844bcc2fdf2a8a9
ms.sourcegitcommit: 6f30424a4ab8dffc4e690086e898ab52bc4da777
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2021
ms.locfileid: "132902719"
---
# <a name="how-to-use-text-analytics-for-health"></a>의료 분야 Text Analytics 사용 방법

[!INCLUDE [service notice](../includes/service-notice.md)]

의료 Text Analytics 사용하여 의사 메모, 건강 요약, 임상 문서 및 전자 건강 기록과 같은 비정형 텍스트에서 관련 의료 정보를 추출하고 레이블을 지정할 수 있습니다.  이 서비스를 활용하는 방법에는 두 가지가 있습니다. 

* 웹 기반 API 및 클라이언트 라이브러리(비동기)
* [Docker](use-containers.md) 컨테이너(동기)

## <a name="features"></a>기능

의료 분야 Text Analytics는 NER(명명된 엔터티 인식), 관계 추출, 엔터티 부정 및 영어 텍스트에 대한 엔터티 연결을 수행하여 비구조적 임상 및 생명 공학 텍스트에서 정보를 파악합니다. 지원되는 엔터티의 전체 목록은 의료 분야 Text Analytics에서 반환하는 [엔터티 범주](../concepts/health-entity-categories.md)를 참조하세요. 신뢰도 점수에 대한 자세한 내용은 [투명도 참고를 참조하세요.](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context) 

> [!TIP]
> 이 기능을 사용하려면 [빠른 시작 도움말](../quickstart.md)에 따라 시작합니다. 코드를 작성할 필요 없이 [Language Studio](../../language-studio.md)를 사용하여 예제 요청을 만들 수도 있습니다.

## <a name="determine-how-to-process-the-data-optional"></a>데이터 처리 방법 결정(선택 사항)

### <a name="specify-the-text-analytics-for-health-model"></a>상태 모델에 대한 Text Analytics 지정

기본적으로 Text Analytics 텍스트에 사용 가능한 최신 AI 모델을 사용합니다. 특정 모델 버전을 사용하도록 API 요청을 구성할 수도 있습니다. 지정한 모델은 Text Analytics 제공된 상태 작업을 수행하는 데 사용됩니다.

| 지원되는 버전 | 최신 버전 |
|--|--|
| `2021-05-15` | `2021-05-15`   |

### <a name="text-analytics-for-health-container"></a>상태 컨테이너용 Text Analytics

[상태 컨테이너에 대한 Text Analytics](use-containers.md) REST API 및 클라이언트 라이브러리와는 별도의 모델 버전 관리 기능을 사용합니다. 컨테이너 이미지당 하나의 모델 버전만 사용할 수 있습니다.

| 엔드포인트                        | 컨테이너 이미지 태그                     | 모델 버전 |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `3.0.016230002-onprem-amd64` (최신)            | `2021-05-15`  |
| `/entities/health`              | `3.0.015370001-onprem-amd64`            | `2021-03-01`  |
| `/entities/health`              | `1.1.013530001-amd64-preview`           | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |

### <a name="input-languages"></a>입력 언어

현재 Text Analytics 영어만 [지원합니다.](../language-support.md) 

## <a name="submitting-data"></a>데이터 제출

API 요청을 보내려면 언어 리소스 엔드포인트 및 키가 필요합니다.

> [!NOTE]
> Azure Portal에서 언어 리소스에 대한 키와 엔드포인트를 찾을 수 있습니다. 리소스의 **키 및 엔드포인트** 페이지의 **리소스 관리** 아래에 있습니다. 

요청을 받으면 분석이 수행됩니다. 분당 및 초당 보낼 수 있는 요청의 크기와 수에 대한 정보는 아래 데이터 제한을 참조하세요.

REST API 또는 클라이언트 라이브러리를 사용하여 요청을 보내면 결과가 비동기적으로 반환됩니다. Docker 컨테이너를 사용하는 경우 동기적으로 반환됩니다.  


## <a name="getting-results-from-the-feature"></a>기능에서 결과 얻기

API 요청 및 상태를 위해 Text Analytics 제출하는 데이터에 따라 다음을 얻게 됩니다.

[!INCLUDE [Text Analytics for health features](../includes/features.md)]


이 기능을 비동기적으로 사용하는 경우 요청이 수집된 시간부터 24시간 동안 API 결과를 사용할 수 있으며 응답에 표시됩니다. 이 기간이 지나면 결과가 제거되고 더 이상 검색할 수 없습니다.

## <a name="data-limits"></a>데이터 제한

> [!NOTE]
> * 허용 한도보다 큰 문서를 분석해야 하는 경우 텍스트를 API로 보내기 전에 텍스트를 더 작은 텍스트 청크로 분할할 수 있습니다. 최상의 결과를 위해서는 텍스트는 문장 사이에서 분할합니다.
> * 문서는 텍스트 문자의 단일 문자열입니다.  

| 제한 | 값 |
|------------------------|---------------|
| 단일 문서의 최대 크기 | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)로 측정된 30,720자 |
| 전체 요청의 최대 크기 | 1MB |
| 요청당 최대 문서 수 | 웹 기반 API의 경우 10, 컨테이너의 경우 1000입니다. |

문서가 문자 제한을 초과하면 API는 최대 크기를 초과하는 문서를 처리하지 않으며 잘못된 문서 오류를 반환합니다. API 요청에 여러 문서가 있는 경우 문자 제한 내에 있으면 API가 문서를 계속 처리합니다.

5,120자보다 큰 문서를 보내면 상태를 위해 Text Analytics 5,120자의 청크로 분할됩니다. 두 엔터티가 관련된 분할의 양쪽에 있는 경우 모델은 관계를 검색할 수 없습니다. 잠재적 관계가 감지되지 않도록 하려면 텍스트를 전체 문장으로만 구성된 5,120자 이하의 문서로 분할하는 것이 좋습니다.

### <a name="rate-limits"></a>속도 제한

속도 제한은 [가격 책정 계층](https://aka.ms/unifiedLanguagePricing)에 따라 달라집니다. 이러한 제한은 API의 두 버전 모두에 동일합니다. 이러한 속도 제한은 설정된 속도 제한이 없는 Text Analytics for health 컨테이너에는 적용되지 않습니다.

| 계층          | 초당 요청 | 분당 요청 |
|---------------|---------------------|---------------------|
| S / 다중 서비스 | 1000                | 1000                |
| F0         | 100                 | 300                 |

## <a name="see-also"></a>참고 항목

* [의료 분야 Text Analytics 개요](../overview.md)
* [상태 엔터티 범주에 대한 Text Analytics](../concepts/health-entity-categories.md)
