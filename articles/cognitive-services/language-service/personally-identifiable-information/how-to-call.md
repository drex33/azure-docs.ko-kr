---
title: PII (개인 식별이 가능한 정보)를 검색 하는 방법
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 텍스트에서 PII 및 상태 정보 (화)를 추출 하 고 식별 가능한 정보를 검색 하는 방법을 보여 줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-pii, ignite-fall-2021
ms.openlocfilehash: 93f232233da0310881539441fdb296ac4ae39b62
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103057"
---
# <a name="how-to-detect-and-redact-personally-identifying-information-pii"></a>PII (개인 식별 정보)를 검색 하 고 교정 하는 방법

PII 기능은 구조화 되지 않은 텍스트를 평가 하 고, PII (중요 한 정보)를 추출 하 고, 몇 가지 미리 정의 된 범주에 걸쳐 텍스트로 상태 정보 (화)를 추출할 수 있습니다.

## <a name="determine-how-to-process-the-data-optional"></a>데이터 처리 방법 결정(선택 사항)

### <a name="specify-the-pii-detection-model"></a>PII 검색 모델 지정

기본적으로이 기능은 텍스트에서 사용 가능한 최신 AI 모델을 사용 합니다. 특정 모델 버전을 사용하도록 API 요청을 구성할 수도 있습니다. 지정 하는 모델은 NER 및 PII 작업을 수행 하는 데 사용 됩니다.

| 지원되는 버전 | 최신 버전 |
|--|--|
| `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`, `2021-01-15`  | `2021-01-15`   |

### <a name="input-languages"></a>입력 언어

처리할 문서를 제출할 때 해당 문서를 작성할 때 [지원 되는 언어](language-support.md) 를 지정할 수 있습니다. 언어를 지정 하지 않으면 핵심 문구 추출은 기본적으로 영어로 표시 됩니다. API는 다양한 [다국어 및 그림 이모티콘 인코딩](../concepts/multilingual-emoji-support.md)을 지원하기 위해 응답에 오프셋을 반환할 수 있습니다. 

## <a name="submitting-data"></a>데이터 제출

요청을 받으면 분석이 수행됩니다. 분 및 초당 보낼 수 있는 요청의 크기와 수에 대한 정보는 아래의 데이터 제한 섹션을 참조하세요.

PII 검색 기능을 동기적으로 사용 하는 것은 상태 비저장입니다. 계정에 데이터가 저장되지 않으며, 결과가 응답에서 즉시 반환됩니다.

이러한 기능을 비동기적으로 사용 하는 경우 요청이 수집 된 시간부터 48 시간 동안 API 결과를 사용할 수 있으며 응답에 표시 됩니다. 이 기간이 지나면 결과가 제거되고 더 이상 검색할 수 없습니다.

API는 지정 된 문서 언어에 대해 [정의 된 엔터티 범주](concepts/entity-categories.md) 를 검색 하려고 합니다. 검색하고 반환할 엔터티를 지정하려면 적절한 엔터티 범주가 포함된 선택적 `piiCategories` 매개 변수를 사용합니다. 이 매개 변수를 사용하여 문서 언어에 대해 기본적으로 사용되지 않는 엔터티를 검색할 수도 있습니다. 다음 URL 예제는 영어 텍스트로 표시 될 수 있는 프랑스어 드라이버의 라이선스 번호와 기본 영어 엔터티를 검색 합니다.

> [!TIP]
> 엔터티 범주를 지정할 때 `default`가 포함되지 않으면 API에서 사용자가 지정한 엔터티 범주만 반환합니다.

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.5/entities/recognition/pii?piiCategories=default,FRDriversLicenseNumber`

## <a name="getting-pii-results"></a>PII 결과 가져오기

PII 검색에서 결과를 가져올 때 응용 프로그램에 결과를 스트림이나 로컬 시스템의 파일에 출력을 저장할 수 있습니다. API 응답에는 범주, 하위 범주 및 신뢰 점수를 비롯 하 여 [인식 된 엔터티가](concepts/entity-categories.md)포함 됩니다. PII 엔터티 교정 된 포함 된 텍스트 문자열도 반환 됩니다.

## <a name="data-limits"></a>데이터 제한

> [!NOTE]
> * 허용 한도보다 큰 문서를 분석해야 하는 경우 텍스트를 API로 보내기 전에 텍스트를 더 작은 텍스트 청크로 분할할 수 있습니다. 
> * 문서는 텍스트 문자의 단일 문자열입니다.  

| 제한 | 값 |
|------------------------|---------------|
| 단일 문서의 최대 크기(동기) | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)에 의해 측정된 5,120자.  |
| 요청당 최대 문자 수(비동기)  | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)로 측정되는 모든 제출된 문서에서 125K 문자를 계산합니다.  |
| 전체 요청의 최대 크기 | 1MB |
| 요청당 최대 문서 수 | 5 |

문서가 글자수 제한을 초과하는 경우 API는 사용 중인 기능에 따라 다르게 작동합니다.

* 비동기:
  * API는 전체 요청을 거부하고 그 안의 문서가 최대 크기를 초과하는 경우 `400 bad request` 오류를 반환합니다.
* 동기:  
  * API는 최대 크기를 초과하는 문서를 처리하지 않으며 잘못된 문서 오류를 반환합니다. API 요청에 여러 문서가 있는 경우 문자 제한 내에 있으면 API가 문서를 계속 처리합니다.

단일 요청으로 보낼 수 있는 최대 문서 수를 초과하면 HTTP 400 오류 코드가 생성됩니다.

### <a name="rate-limits"></a>속도 제한

속도 제한은 [가격 책정 계층](https://aka.ms/unifiedLanguagePricing)에 따라 달라집니다.

| 계층          | 초당 요청 | 분당 요청 |
|---------------|---------------------|---------------------|
| S / 다중 서비스 | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |

## <a name="next-steps"></a>다음 단계

[명명 된 엔터티 인식 개요](overview.md)
