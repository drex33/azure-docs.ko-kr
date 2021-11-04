---
title: 텍스트 조정 API를 사용하여 텍스트 조정 - Content Moderator
titleSuffix: Azure Cognitive Services
description: 온라인 콘솔에서 텍스트 조정 API를 사용하여 텍스트 조정을 시험 사용해 보세요.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/27/2021
ms.openlocfilehash: 965d8f420f25dead5709c333ffaaa93cf64ead13
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454716"
---
# <a name="moderate-text-from-the-api-console"></a>API 콘솔에서 텍스트 조정

Azure Content Moderator에서 [Text Moderation API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)를 사용하여 텍스트 콘텐츠를 욕설에 대해 검사하고 사용자 지정 및 공유 목록과 비교합니다.

## <a name="get-your-api-key"></a>API key 가져오기

온라인 콘솔에서 API를 테스트하려면 구독 키가 필요합니다. 구독 키는 **Ocp-Apim-Subscription-Key** 상자의 **설정** 탭에 있습니다. 자세한 내용은 [개요](overview.md)를 참조하세요.

## <a name="navigate-to-the-api-reference"></a>API 참조로 이동

[텍스트 조정 API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)로 이동합니다. 

  **텍스트-화면** 페이지가 열립니다.

## <a name="open-the-api-console"></a>API 콘솔 열기

**개방형 API 테스트 콘솔** 의 경우 사용자 위치를 가장 근접하게 설명하는 지역을 선택합니다. 

  ![텍스트 - 화면 페이지 영역 선택](images/test-drive-region.png)

  **텍스트 - 화면** API 콘솔이 열립니다.

## <a name="select-the-inputs"></a>입력 선택

### <a name="parameters"></a>매개 변수

텍스트 화면에서 사용하려는 쿼리 매개 변수를 선택합니다. 이 예제의 경우 **언어** 에 대한 기본값을 사용합니다. 작업에서 실행의 일부로 가능성이 있는 언어를 자동으로 검색하므로 비워둘 수도 있습니다.

> [!NOTE]
> **language** 매개 변수에 대해 `eng`를 할당하거나, 기계 지원 **분류** 응답(미리 보기 기능)을 확인하려면 비워 둡니다. **이 기능은 영어만 지원합니다**.
>
> **욕설** 감지의 경우 이 문서에 나열된 지원되는 언어의 [ISO 639-3 코드](http://www-01.sil.org/iso639-3/codes.asp)를 사용하거나 비워 둡니다.

**자동 수정**, **PII** 및 **분류(미리 보기)** 의 경우 **true** 를 선택합니다. **ListId** 필드는 비워둡니다.

  ![텍스트 - 화면 콘솔 쿼리 매개 변수](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>내용 유형

**Content-type** 의 경우 차단하려는 콘텐츠 유형을 선택합니다. 이 예제의 경우 기본 **텍스트/일반** 콘텐츠 형식을 사용합니다. **Ocp-Apim-Subscription-Key** 상자에 구독 키를 입력합니다.

### <a name="sample-text-to-scan"></a>검색할 샘플 텍스트

**요청 본문** 상자에 일부 텍스트를 입력합니다. 다음 예제에서는 텍스트의 의도적인 오타를 보여줍니다.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>응답 분석

다음과 같은 응답이 API에서 다양한 인사이트를 보여줍니다. 잠재적인 욕설, 개인 데이터, 분류(미리 보기) 및 자동 수정 버전이 포함됩니다.

> [!NOTE]
> 컴퓨터 지원 ‘분류’ 기능은 현재 미리 보기로 제공되며 영어만 지원합니다.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
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
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

JSON 응답의 모든 섹션에 대한 자세한 내용은 [텍스트 조정](text-moderation-api.md) 개념 가이드를 참조합니다.

## <a name="next-steps"></a>다음 단계

코드에 REST API를 사용하거나 [.NET SDK 빠른 시작](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)을 따라 애플리케이션을 통합합니다.