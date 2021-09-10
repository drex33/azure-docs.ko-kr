---
title: 텍스트 조정 - Content Moderator
titleSuffix: Azure Cognitive Services
description: 가능한 원치 않는 텍스트, 개인 데이터, 사용자 지정 용어 목록에 대해 텍스트 조정을 사용합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: pafarley
ms.openlocfilehash: e001e268a5e805fbab7cc84e7248750bd58e911e
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822863"
---
# <a name="learn-text-moderation-concepts"></a>텍스트 조정 개념 알아보기

Content Moderator의 텍스트 조정 모델을 사용하여 텍스트 콘텐츠를 분석합니다.

정책 및 임계값을 기준으로 콘텐츠를 차단, 승인, 검토할 수 있습니다(사용자 검토를 설정하는 방법을 알아보려면 [검토, 워크플로, 작업](./review-api.md) 참조). 텍스트 조정 모델을 사용하여 파트너, 직원, 소비자가 텍스트 콘텐츠를 생성하는 환경에 대한 사용자의 조정을 강화합니다. 여기에는 채팅방, 토론 게시판, 챗봇, 전자 상거래 카탈로그 및 문서가 포함됩니다.

서비스 응답에는 다음 정보가 포함됩니다.

- 욕설: 다양한 언어의 기본 제공 욕설 목록을 사용하는 용어 기반 일치
- 분류: 세 가지 범주의 기계 지원 분류
- 개인 데이터
- 자동 수정된 텍스트
- 원래 텍스트
- 언어

## <a name="profanity"></a>욕설

API가 [지원되는 언어](./language-support.md)의 욕설을 감지하면 해당 용어가 응답에 포함됩니다. 응답에는 원래 텍스트에서 해당 위치(`Index`)도 포함됩니다. 다음 샘플 JSON의 `ListId`는 [사용자 지정 용어 목록](try-terms-list-api.md)(사용 가능한 경우)에 있는 용어를 가리킵니다.

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }
```

> [!NOTE]
> **language** 매개 변수에 대해 `eng`를 할당하거나, 기계 지원 **분류** 응답(미리 보기 기능)을 확인하려면 비워 둡니다. **이 기능은 영어만 지원합니다**.
>
> **욕설** 감지의 경우 이 문서에 나열된 지원되는 언어의 [ISO 639-3 코드](http://www-01.sil.org/iso639-3/codes.asp)를 사용하거나 비워 둡니다.

## <a name="classification"></a>분류

Content Moderator의 기계 지원 **텍스트 분류 기능** 은 **영어만** 지원하며, 잠재적으로 원치 않는 콘텐츠를 감지하는 데 도움이 됩니다. 플래그가 지정된 콘텐츠는 컨텍스트에 따라 부적절한 콘텐츠로 평가될 수 있습니다. 각 범주의 가능성을 전달하고 사용자 검토를 권장할 수 있습니다. 이 기능은 학습된 모델을 사용하여 상스럽거나 경멸적이거나 차별적인 언어를 식별합니다. 여기에는 은어, 약어, 불쾌한 단어, 의도적으로 철자가 틀린 단어의 검토가 포함됩니다. 

JSON 추출의 다음 추출은 예제 출력을 보여 줍니다.

```json
"Classification": {
    "ReviewRecommended": true,
    "Category1": {
        "Score": 1.5113095059859916E-06
    },
    "Category2": {
        "Score": 0.12747249007225037
    },
    "Category3": {
        "Score": 0.98799997568130493
    }
}
```

### <a name="explanation"></a>설명

- `Category1`은 잠재적으로 특정 상황에서 성적으로 노골적이거나 음란한 것으로 간주될 수 있는 언어가 있음을 나타냅니다.
- `Category2`는 잠재적으로 특정 상황에서 성적으로 외설적이거나 도발적인 것으로 간주될 수 있는 언어가 있음을 나타냅니다.
- `Category3`는 잠재적으로 특정 상황에서 모욕적인 것으로 간주될 수 있는 언어가 있음을 나타냅니다.
- `Score`는 0에서 1 사이입니다. 점수가 높을수록 모델이 예측하는 범주에 해당할 가능성이 높아집니다. 이 기능은 수동으로 코딩된 결과가 아닌 통계 모델을 사용합니다. 고유한 콘텐츠로 테스트하여 각 범주가 요구 사항과 얼마나 일치하는지 확인하는 것이 좋습니다.
- `ReviewRecommended`는 내부 점수 임계값에 따라 true 또는 false입니다. 고객은 이 값을 사용할지 또는 콘텐츠 정책에 따라 사용자 지정 임계값을 결정할지를 평가해야 합니다.

## <a name="personal-data"></a>개인 데이터

개인 데이터 기능은 잠재적으로 다음과 같은 정보가 있는지 감지합니다.

- 전자 메일 주소
- 미국 우편 주소
- IP 주소
- 미국 전화 번호

다음 예제에서는 샘플 응답을 보여 줍니다.

```json
"pii":{
  "email":[
      {
        "detected":"abcdef@abcd.com",
        "sub_type":"Regular",
        "text":"abcdef@abcd.com",
        "index":32
      }
  ],
  "ssn":[

  ],
  "ipa":[
      {
        "sub_type":"IPV4",
        "text":"255.255.255.255",
        "index":72
      }
  ],
  "phone":[
      {
        "country_code":"US",
        "text":"6657789887",
        "index":56
      }
  ],
  "address":[
      {
        "text":"1 Microsoft Way, Redmond, WA 98052",
        "index":89
      }
  ]
}
```

## <a name="auto-correction"></a>자동 고침

입력 텍스트가(의도적으로 “qu!ck”, “f0x”, “lzay”라고 입력한 경우) 다음과 같다고 가정합니다.

> The qu!ck brown f0x jumps over the lzay dog.

자동 고침을 요청하면 응답에 다음과 같은 수정된 텍스트 버전이 포함됩니다.

> The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>사용자 지정 용어 목록 만들기 및 관리

대부분의 경우 기본 전역 용어 목록을 사용해도 되지만, 비즈니스 요구와 관련된 용어를 기준으로 심사하는 것이 좋습니다. 예를 들어 사용자 게시물에서 경쟁 브랜드 이름을 필터링할 수 있습니다.

> [!NOTE]
> 최대 **5개 용어 목록** 으로 제한되고, 각 목록은 **10,000개 용어를 초과하지 않아야** 합니다.
>

다음 예제에서는 일치하는 목록 ID를 보여 줍니다.

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }
```

Content Moderator는 사용자 지정 용어 목록 관리 작업이 포함된 [용어 목록 API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)를 제공합니다. [용어 목록 API 콘솔](try-terms-list-api.md)에서 시작하고 REST API 코드 샘플을 사용합니다. Visual Studio 및 C#에 익숙한 경우 [용어 목록 .NET 빠른 시작](term-lists-quickstart-dotnet.md)도 확인합니다.

## <a name="next-steps"></a>다음 단계

[Text Moderation API 콘솔](try-text-api.md)로 API를 테스트합니다. 또한 사용자 검토를 설정하는 방법을 알아보려면 [검토, 워크플로 및 작업](./review-api.md)을 참조하세요.
