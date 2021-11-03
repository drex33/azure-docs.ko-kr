---
title: API를 연결하는 엔터티를 호출하는 방법
titleSuffix: Azure Cognitive Services
description: 텍스트에 있는 엔터티를 식별하여 API를 연결하는 엔터티를 연결하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-entity-linking, ignite-fall-2021
ms.openlocfilehash: a7edc27898c1af9fcb8f7bc72698d2d6c3a63e68
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052868"
---
# <a name="how-to-use-entity-linking"></a>엔터티 연결을 사용하는 방법

엔터티 연결 기능을 사용하여 텍스트에서 찾은 엔터티의 ID를 식별하고 구분할 수 있습니다(예:*"Mars"라는* 단어가 행성을 참조하는지 아니면 로마의전을 참조하는지 확인). 기술 자료로 [Wikipedia에](https://www.wikipedia.org/) 대한 링크가 있는 텍스트의 엔터티를 반환합니다.

> [!TIP]
> 이 기능을 사용하려면 [빠른 시작 문서](../quickstart.md)에 따라 시작합니다. 코드를 작성할 필요 없이 [Language Studio](../../language-studio.md)를 사용하여 예제 요청을 만들 수도 있습니다.

## <a name="determine-how-to-process-the-data-optional"></a>데이터 처리 방법 결정(선택 사항)

### <a name="specify-the-entity-linking-model"></a>엔터티 연결 모델 지정

기본적으로 엔터티 링크는 텍스트에 사용 가능한 최신 AI 모델을 사용합니다. 특정 모델 버전을 사용하도록 API 요청을 구성할 수도 있습니다. 지정한 모델은 엔터티 연결 작업을 수행하는 데 사용됩니다.

| 지원되는 버전 | 최신 버전 |
|--|--|
| `2019-10-01`, `2020-02-01` | `2020-02-01` |

### <a name="input-languages"></a>입력 언어

엔터티 링크로 처리할 문서를 제출할 때 작성된 [지원되는 언어를](../language-support.md) 지정할 수 있습니다. 언어를 지정하지 않으면 엔터티 연결이 기본적으로 영어로 설정됩니다. [다국어 및 이모지 지원으로](../../concepts/multilingual-emoji-support.md)인해 응답에 텍스트 오프셋이 포함될 수 있습니다. 

## <a name="submitting-data"></a>데이터 제출

엔터티 연결은 작업할 텍스트 양을 줄이면 더 높은 품질의 결과를 생성합니다. 이는 더 큰 텍스트 블록에서 더 잘 수행되는 핵심 구 추출과 같은 일부 기능과 반대입니다. 두 작업 모두에서 최상의 결과를 얻으려면 적절하게 입력을 재구성하는 것을 고려해보세요.

API 요청을 보내려면 언어 리소스 엔드포인트 및 키가 필요합니다.

> [!NOTE]
> Azure Portal에서 언어 리소스에 대한 키와 엔드포인트를 찾을 수 있습니다. 리소스의 **키 및 엔드포인트** 페이지의 **리소스 관리** 아래에 있습니다. 

요청을 받으면 분석이 수행됩니다. 분당 및 초당 보낼 수 있는 요청의 크기와 수에 대한 정보는 아래 데이터 제한을 참조하세요.

엔터티 연결을 동기적으로 사용하는 것은 상태 비지정입니다. 계정에 데이터가 저장되지 않으며, 결과가 응답에서 즉시 반환됩니다.

이 기능을 비동기적으로 사용하는 경우 요청이 수집된 시간부터 24시간 동안 API 결과를 사용할 수 있으며 응답에 표시됩니다. 이 기간이 지나면 결과가 제거되고 더 이상 검색할 수 없습니다.

### <a name="getting-entity-linking-results"></a>엔터티 연결 결과 가져오기  

결과를 애플리케이션으로 스트리밍하거나 출력을 로컬 시스템의 파일에 저장할 수 있습니다.

## <a name="data-limits"></a>데이터 제한

> [!NOTE]
> * 허용 한도보다 큰 문서를 분석해야 하는 경우 텍스트를 API로 보내기 전에 텍스트를 더 작은 텍스트 청크로 분할할 수 있습니다. 
> * 문서는 텍스트 문자의 단일 문자열입니다.  

| 제한 | 값 |
|------------------------|---------------|
| 단일 문서의 최대 크기 | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)에 의해 측정된 5,120자. |
| 요청당 최대 문자 수(비동기)  | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)로 측정되는 모든 제출된 문서에서 125K 문자를 계산합니다. |
| 전체 요청의 최대 크기 | 1MB |
| 요청당 최대 문서 수 | 5 |

문서가 문자 제한을 초과하면 API는 동기적으로 사용하는지 비동기적으로 사용하는지에 따라 다르게 동작합니다.

* 비동기: API는 전체 요청을 거부하고 그 안의 문서가 최대 크기를 초과하는 경우 `400 bad request` 오류를 반환합니다.
* 동기: API는 최대 크기를 초과하는 문서를 처리하지 않으며 잘못된 문서 오류를 반환합니다. API 요청에 여러 문서가 있는 경우 문자 제한 내에 있으면 API가 문서를 계속 처리합니다.

### <a name="rate-limits"></a>속도 제한

속도 제한은 [가격 책정 계층](https://aka.ms/unifiedLanguagePricing)에 따라 달라집니다.

| 계층          | 초당 요청 | 분당 요청 |
|---------------|---------------------|---------------------|
| S / 다중 서비스 | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |

## <a name="see-also"></a>참고 항목

* [엔터티 연결 개요](../overview.md)
