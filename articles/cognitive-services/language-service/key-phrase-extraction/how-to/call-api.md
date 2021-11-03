---
title: 핵심 구 추출 API를 호출 하는 방법
titleSuffix: Azure Cognitive Services
description: 핵심 구 추출 API를 사용 하 여 핵심 문구를 추출 하는 방법입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-key-phrase, ignite-fall-2021
ms.openlocfilehash: 72a7d077b5ee7752f7b91a2f5db5e1b5614c4ddd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053737"
---
# <a name="how-to-use-key-phrase-extraction"></a>키 구 추출을 사용 하는 방법 

키 구 추출 기능은 구조화 되지 않은 텍스트를 평가할 수 있으며 각 문서에 대해 키 구의 목록을 반환 합니다.

이 기능은 문서 컬렉션에서 주요 요소를 신속 하 게 식별 해야 하는 경우에 유용 합니다. 예를 들어 입력 텍스트 "*음식은 맛 있는이 고 직원은 멋져*"는 경우 서비스는 "*음식*" 및 "*훌륭한 직원*"의 주요 항목을 반환 합니다.

> [!TIP]
> 이 기능을 사용하려면 [빠른 시작 문서](../quickstart.md)에 따라 시작합니다. 코드를 작성할 필요 없이 [Language Studio](../../language-studio.md)를 사용하여 예제 요청을 만들 수도 있습니다.


## <a name="determine-how-to-process-the-data-optional"></a>데이터 처리 방법 결정(선택 사항)

### <a name="specify-the-key-phrase-extraction-model"></a>키 구 추출 모델 지정

기본적으로 키 구 추출에서는 텍스트에 사용 가능한 최신 AI 모델을 사용 합니다. 특정 모델 버전을 사용하도록 API 요청을 구성할 수도 있습니다. 지정 하는 모델은 키 구 추출 작업을 수행 하는 데 사용 됩니다.

| 지원되는 버전 | 최신 버전 |
|--|--|
| `2019-10-01`, `2020-07-01`, `2021-06-01`  | `2021-06-01`   |

### <a name="input-languages"></a>입력 언어

키 구 추출에서 처리할 문서를 제출 하는 경우 [지원 되는 언어](../language-support.md) 중에서 작성 된 언어를 지정할 수 있습니다. 언어를 지정 하지 않으면 핵심 문구 추출은 기본적으로 영어로 표시 됩니다. API는 다양한 [다국어 및 그림 이모티콘 인코딩](../../concepts/multilingual-emoji-support.md)을 지원하기 위해 응답에 오프셋을 반환할 수 있습니다. 

## <a name="submitting-data"></a>데이터 제출

핵심 구 추출은 사용할 텍스트를 더 많이 제공할 때 가장 적합합니다. 이는 작은 양의 텍스트에서 더 효율적으로 수행되는 감정 분석과는 반대입니다. 두 작업에서 최상의 결과를 얻으려면 이에 따라 입력을 다시 구성하는 것이 좋습니다.

API 요청을 보내려면 언어 리소스 끝점 및 키가 필요 합니다.

> [!NOTE]
> Azure Portal에서 언어 리소스에 대한 키와 엔드포인트를 찾을 수 있습니다. 리소스의 **키 및 엔드포인트** 페이지의 **리소스 관리** 아래에 있습니다. 

요청을 받으면 분석이 수행됩니다. 분당 및 초당 보낼 수 있는 요청의 크기와 수에 대한 정보는 아래 데이터 제한을 참조하세요.

키 구 추출 기능을 동기적으로 사용 하는 것은 상태 비저장입니다. 계정에 데이터가 저장되지 않으며, 결과가 응답에서 즉시 반환됩니다.

이 기능을 비동기적으로 사용하는 경우 요청이 수집된 시간부터 24시간 동안 API 결과를 사용할 수 있으며 응답에 표시됩니다. 이 기간이 지나면 결과가 제거되고 더 이상 검색할 수 없습니다.


## <a name="getting-key-phrase-extraction-results"></a>키 구 추출 결과를 가져오는 중

API에서 결과를 수신하면 반환되는 핵심 구의 순서는 모델에 의해 내부적으로 결정됩니다. 결과를 애플리케이션으로 스트리밍하거나 출력을 로컬 시스템의 파일에 저장할 수 있습니다.

## <a name="data-limits"></a>데이터 제한

> [!NOTE]
> * 허용 한도보다 큰 문서를 분석해야 하는 경우 텍스트를 API로 보내기 전에 텍스트를 더 작은 텍스트 청크로 분할할 수 있습니다. 
> * 문서는 텍스트 문자의 단일 문자열입니다.  

| 제한 | 값 | 
|------------------------|---------------|
| 단일 문서의 최대 크기(동기) | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)에 의해 측정된 5,120자. |
| 요청당 최대 문자 수(비동기) | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)로 측정되는 모든 제출된 문서에서 125K 문자를 계산합니다. |
| 전체 요청의 최대 크기 | 1MB  |
| 요청당 최대 문서 수 |  10 |

문서가 문자 제한을 초과하면 API는 사용 중인 엔드포인트에 따라 다르게 작동합니다.

* 비동기: API는 전체 요청을 거부하고 그 안의 문서가 최대 크기를 초과하는 경우 `400 bad request` 오류를 반환합니다.
* 동기: API는 최대 크기를 초과하는 문서를 처리하지 않으며 잘못된 문서 오류를 반환합니다. API 요청에 여러 문서가 있는 경우 문자 제한 내에 있으면 API가 문서를 계속 처리합니다.

### <a name="rate-limits"></a>속도 제한

속도 제한은 [가격 책정 계층](https://aka.ms/unifiedLanguagePricing)에 따라 달라집니다.

| 계층          | 초당 요청 | 분당 요청 |
|---------------|---------------------|---------------------|
| S / 다중 서비스 | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |

## <a name="next-steps"></a>다음 단계

[핵심 구 추출 개요](../overview.md)
