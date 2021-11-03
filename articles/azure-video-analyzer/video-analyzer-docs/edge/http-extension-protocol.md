---
title: HTTP 확장 프로토콜 - Azure
description: Azure Video Analyzer를 사용하면 파이프라인 확장 노드를 통해 처리 능력을 향상시킬 수 있습니다. HTTP 확장 프로세서는 HTTP 프로토콜을 사용하는 확장성 시나리오를 가능하게 합니다. 여기서 성능 및/또는 최적 리소스 사용률은 주요 문제가 아닙니다.
ms.topic: reference
ms.date: 06/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 50da053a6454a2c6973f52ed55891ce2f40d6e06
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053024"
---
# <a name="use-the-http-extension-protocol"></a>HTTP 확장 프로토콜 사용 

[!INCLUDE [header](includes/edge-env.md)]

Azure Video Analyzer를 사용하면 [파이프라인 확장](../pipeline-extension.md) 노드를 통해 처리 능력을 향상시킬 수 있습니다. HTTP 확장 프로세서 노드는 HTTP 확장 프로토콜을 사용하는 확장성 시나리오를 가능하게 합니다. 여기서 성능 및/또는 최적 리소스 사용률은 주요 문제가 아닙니다. 이 문서에서는 이 프로토콜을 사용하여 Video Analyzer와 일반적으로 AI 유추 서버에 연결되는 HTTP REST 엔드포인트 간에 메시지를 보내는 방법에 대해 알아봅니다.

HTTP 계약은 다음 두 구성 요소 간에 정의됩니다.

* HTTP 서버
* Video Analyzer 모듈이 HTTP 클라이언트 역할을 함

## <a name="http-contract"></a>HTTP 계약

### <a name="request"></a>요청

Video Analyzer 모듈에서 HTTP 서버로의 요청은 다음과 같습니다.

|키|값|
|---|---|
|POST|https://hostname/optional-path?optional-query|
|동의함|application/json|
|권한 부여| 기본, 다이제스트, 전달자(사용자 지정 헤더 지원을 통해)|
|콘텐츠 형식|  image/jpeg<br/>image/png<br/>image/bmp<br/>image/x-raw|
|Content-Lengt 본문 길이(바이트)   ||
|User-Agent |Azure Media Services|
|본문|  지원되는 콘텐츠 형식 중 하나로 인코딩된 이진 파일인 이미지 바이트입니다.|
```

### Example

```html
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>응답

유추 서버에서 Video Analyzer 모듈로의 응답은 다음과 같아야 합니다.

|키|   값|
|---|----|
|상태 코드|  200 OK - 유추 결과 찾음<br/>204 콘텐츠가 없음 - AI에서 결과를 찾을 수 없음<br/>400 잘못된 요청 - 예상되지 않음<br/>500 내부 서버 오류 - 예상되지 않음<br/>503 서버 작업 중 - Video Analyzer에서 ‘Retry-After’ 헤더를 기반으로, 또는 헤더가 없는 경우 기본 시간을 기반으로 백오프됩니다.|
|콘텐츠 형식   |application/json|
|Content-Length |본문 길이(바이트)|
|본문|  단일 "추론" 속성이 있는 JSON 개체입니다.|

### <a name="example"></a>예

```html
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2021 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

[유추 메타데이터 스키마 개체 모델](inference-metadata-schema.md)에 따라 정의된 미리 설정된 스키마에 따라 유효한 JSON 문서를 사용하여 응답을 반환하는 것이 좋습니다. 이 스키마를 준수하면 라이브 비디오에서 개체를 추적하는 기능과 같은 Video Analyzer의 다른 구성 요소와의 상호 운용성을 보장하고, [여기](record-stream-inference-data-with-video.md)에서 설명하는 것과 같이 재생 중 비디오에 대한 유추 메타데이터를 오버레이합니다.

모듈에서 콘텐츠 형식이 ‘application/json’이 아닌 응답을 반환하는 경우 Live Video Analyzer는 메시지를 기본 64 콘텐츠로 인코딩하고 불투명한 JSON 페이로드로 직렬화합니다.

모듈에서 콘텐츠 형식이 ‘application/json’인 응답을 반환하지만 JSON 스키마가 위에서 설명한 유추 메타데이터 스키마를 따르지 않는 경우 메시지 페이로드는 파이프라인을 통해 전달되지만 상호 운용성은 감소됩니다.

> [!NOTE]
> 유추 서버가 지정된 이미지에 대한 결과를 생성하지 않는 경우 빈 응답 본문이 포함된 HTTP 204 상태 코드(콘텐츠 없음)를 반환해야 합니다. Video Analyzer는 이를 빈 결과로 인식하고 파이프라인 전체에 이벤트를 전달하지 않습니다.

## <a name="next-steps"></a>다음 단계

[gRPC 확장 프로토콜 세부 정보](grpc-extension-protocol.md)
