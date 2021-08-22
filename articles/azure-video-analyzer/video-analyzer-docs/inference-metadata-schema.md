---
title: 유추 메타데이터 스키마 - Azure
description: Azure Video Analyzer에서 각 유추 개체는 HTTP 기반 계약 또는 gRPC 기반 계약 사용에 관계없이 이 항목에 설명된 개체 모델을 따릅니다.
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: bacf69638b3b9f9b8feb86030b1a8498eb69260a
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601528"
---
# <a name="inference-metadata-schema"></a>유추 메타데이터 스키마 

Azure Video Analyzer에서 각 유추 개체는 HTTP 기반 계약 또는 gRPC 기반 계약 사용에 관계없이 이 항목에 설명된 개체 모델을 따릅니다.

## <a name="object-model"></a>개체 모델

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/inference-metadata-schema/object-model.png" alt-text="Azure Video Analyzer Oot 모델" lightbox="./media/inference-metadata-schema/object-model.png":::

 
|형식 정의|설명|
|---|---|
|태그|결과와 관련된 태그 또는 레이블 태그 지정과 함께 태그와 관련된 신뢰도 값을 얻을 수도 있습니다.|
|특성|결과와 관련된 추가 특성입니다. 신뢰도 값과 함께 추론 엔진에서 수신하는 새 특성을 추가할 수 있습니다.|
|특성 목록|선택적 특성의 목록입니다.|
|직사각형|이미지 왼쪽 위 모서리를 기준으로 하는 사각형 영역입니다. 필수 속성은 "길이", "너비", 높이" 및 "원점으로부터의 위쪽 가장자리 거리"입니다.|
|분류|종종 전체 샘플에 적용되는 분류자의 레이블입니다. ‘태그’를 활용하여 결과를 분류할 수 있습니다.|
|엔터티|샘플에서 검색되거나 식별된 엔터티. 추론 엔진에서 엔터티를 검색하는 경우 ‘태그’를 가져오고, 유추된 추가 특성 및 검색된 엔터티 주위에 있는 사각형 상자의 좌표가 반환됩니다.|
|이벤트|샘플에서 이벤트가 검색되었습니다. 샘플에서 이벤트가 검색되면 이벤트 이름과 이벤트별 속성이 반환됩니다.|
|동작|샘플에서 동작이 검색되었습니다. 샘플에서 동작이 검색되면 동작이 검색되는 사각형 경계 상자의 좌표가 반환됩니다.|
|텍스트|텍스트의 시작 및 종료 타임스탬프와 함께 샘플과 연결된 텍스트가 반환됩니다.|
|기타|다른 일반 페이로드 정보를 반환합니다.|

아래 예제에는 지원되는 일부 유추 형식이 포함된 단일 이벤트가 포함되어 있습니다.

```
[ 
  // Light Detection 
  { 
    "type": "classification", 
    "subtype": "lightDetection", 
    "classification": { 
      "tag": { "value": "daylight", "confidence": 0.86 }, 
      "attributes": [ 
          { "name": "isBlackAndWhite", "value": "false", "confidence": 0.71 } 
      ] 
    } 
  }, 
 
  // Motion Detection 
  { 
    "type": "motion", 
    "subtype": "motionDetection", 
    "motion": 
    { 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Yolo V3 
  { 
    "type": "entity", 
    "subtype": "objectDetection",     
    "entity": 
    { 
      "tag": { "value": "dog", "confidence": 0.97 }, 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // Vehicle Identification 
  { 
    "type": "entity", 
    "subtype": "vehicleIdentification",     
    "entity": 
    { 
      "tag": { "value": "007-SPY", "confidence": 0.82 }, 
      "attributes":[   
        { "name": "color", "value": "black", "confidence": 0.90 }, 
        { "name": "body", "value": "coupe", "confidence": 0.87 }, 
        { "name": "make", "value": "Aston Martin", "confidence": 0.35 }, 
        { "name": "model", "value": "DBS V12", "confidence": 0.33 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    } 
  }, 
 
  // People Identification 
  { 
    "type": "entity", 
    "subtype": "peopleIdentification",     
    "entity": 
    { 
      "tag": { "value":"Erwin Schrödinger", "confidence": 0.50 }, 
      "attributes":[   
        { "name": "age", "value": "73", "confidence": 0.87 }, 
        { "name": "glasses", "value": "yes", "confidence": 0.94 } 
      ], 
      "box": { "l": 0.0, "t": 0.0, "w": 0.0, "h": 0.0 } 
    }, 
 
    // Open type coming from the gRPC Map 
    "extensions":  
    { 
      "vector": "e1xkaXNwbGF5c3R5bGUgaVxoYmFyIHtcZnJhYyB7ZH17ZHR9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSA9e1xoYXQge0h9fVx2ZXJ0IFxQc2kgKHQpXHJhbmdsZSB9KQ==", 
      "skeleton": "p1,p2,p3,p4" 
    } 
  }, 
 
  // Captions 
  {     
    "type": "text", 
    "subtype": "speechToText",   
    "text": 
    { 
      "value": "Humor 75%. Confirmed. Self-destruct sequence in T minus 10… 9…", 
      "language": "en-US", 
      "startTimestamp": 12000, 
      "endTimestamp": 13000 
    } 
]
```

## <a name="next-steps"></a>다음 단계

- [gRPC 데이터 계약](./grpc-extension-protocol.md)
- [HTTP 데이터 계약](./http-extension-protocol.md)
