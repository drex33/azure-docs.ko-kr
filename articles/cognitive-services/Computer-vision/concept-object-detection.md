---
title: 개체 검색-Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API의 개체 검색 기능과 관련된 개념에 대해 알아봅니다(사용 및 한도).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d8ef3a319b9701ece0b7a66e3d428c519642767c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454792"
---
# <a name="detect-common-objects-in-images"></a>이미지에서 공통 개체 검색

개체 검색은 [태그 지정과](concept-tagging-images.md)유사하지만 API는 이미지에 있는 각 개체에 대한 경계 상자 좌표(픽셀)를 반환합니다. 예를 들어 이미지에 개, 고양이 및 사람이 포함된 경우 검색 작업은 이미지에 해당 좌표가 있는 개체를 나열합니다. 이 기능을 사용하여 이미지의 개체 간 관계를 처리할 수 있습니다. 또한 이미지에 동일한 개체의 여러 인스턴스가 있는지 여부를 확인할 수 있습니다.

Detect API는 개체를 기반으로 한 태그 또는 이미지에서 식별되는 생물에 적용합니다. 현재 태깅 분류와 개체 검색 분류 간에는 공식적 관계가 없습니다. 개념 수준에서 Detect API는 개체 및 생물만 검색하지만 Tag API는 경계 상자로 지역화할 수 없는 ‘실내’와 같은 상황별 용어를 포함할 수도 있습니다.

## <a name="object-detection-example"></a>개체 검색 예제

다음 JSON 응답에서는 예제 이미지의 개체를 검색할 때 Computer Vision이 반환하는 내용을 보여줍니다.

![주방에서 Microsoft Surface 디바이스를 사용하는 여자](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="limitations"></a>제한 사항

거짓 부정(누락된 개체) 및 제한된 세부 정보의 영향을 방지하거나 완화할 수 있도록 개체 검색 기능의 제한 사항에 유의해야 합니다.

* 일반적으로 개체는 작으면 검색되지 않습니다(이미지의 5% 미만).
* 일반적으로 개체는 근접하게 배열되어 있으면 검색되지 않습니다(예: 접시 더미).
* 개체는 브랜드 또는 제품 이름으로 구분되지 않습니다(예: 상점 선반에 있는 다양한 유형의 탄산 음료). 그러나 [브랜드 검색](concept-brand-detection.md) 기능을 사용하여 이미지에서 브랜드 정보를 가져올 수 있습니다.

## <a name="use-the-api"></a>API 사용

개체 검색 기능은 [이미지 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) API의 일부입니다. 이 API는 네이티브 SDK 또는 REST 호출을 통해 호출할 수 있습니다. **visualFeatures** 쿼리 매개 변수에 `Objects`을 포함합니다. 그런 다음 전체 JSON 응답을 받으면 `"objects"` 섹션의 내용에 대한 문자열을 구문 분석하기만 하면 됩니다.

* [빠른 시작: Computer Vision REST API 또는 클라이언트 라이브러리](./quickstarts-sdk/image-analysis-client-library.md?pivots=programming-language-csharp)
