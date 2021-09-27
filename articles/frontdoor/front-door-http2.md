---
title: Azure Front Door - HTTP2 지원 | Microsoft Docs
description: 이 문서에서는 Azure Front Door의 HTTP/2 지원에 대해 알아볼 수 있습니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: dc5679036eb241abc82a57779e41e2d667238216
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601429"
---
# <a name="http2-support-in-azure-front-door"></a>Azure Front Door의 HTTP/2 지원

현재, HTTP/2 지원은 모든 Azure Front Door 구성에서 활성화됩니다. 고객의 추가적인 조치가 필요하지 않습니다.

HTTP/2는 응답 시간을 줄여 웹 성능을 향상시키는 HTTP/1.1의 주 수정 버전입니다. HTTP/2는 사용자 환경을 개선하기 위해 친숙한 HTTP 메서드, 상태 코드 및 HTTP/1.1의 의미 체계를 유지 관리합니다. HTTP/2는 HTTP 및 HTTPS에서 작동하도록 설계되었지만 많은 클라이언트 웹 브라우저는 TLS(전송 계층 보안)를 통한 HTTP/2만 지원합니다.

> [!NOTE]
> HTTP/2 프로토콜 지원은 클라이언트에서 Front Door로 전달되는 요청에만 사용할 수 있습니다. Front Door와 백 엔드 풀의 백 엔드 간 통신은 HTTP/1.1을 통해 수행됩니다. 

### <a name="http2-benefits"></a>HTTP/2 이점

HTTP/2의 이점은 다음과 같습니다.

*   **멀티플렉싱 및 동시성**

    HTTP 1.1을 사용하여 여러 리소스 요청을 수행하려면 여러 TCP 연결이 필요하며 각 연결에 고유한 성능 비용이 부과됩니다. HTTP/2를 통해 단일 TCP 연결에서 여러 리소스가 요청되도록 할 수 있습니다.

*   **헤더 압축**

    제공된 리소스에 대한 HTTP 헤더를 압축하면 유선으로 전송되는 데이터가 훨씬 줄어듭니다.

*   **스트림 종속성**

    스트림 종속성을 통해 클라이언트가 우선 순위가 높은 리소스를 서버에 나타낼 수 있습니다.


## <a name="http2-browser-support"></a>HTTP/2 브라우저 지원

모든 주요 브라우저는 최신 버전에서 HTTP/2 지원을 구현했습니다. 지원되지 않는 브라우저는 HTTP/1.1로 자동으로 대체됩니다.

|브라우저|최소 버전|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>다음 단계

HTTP/2에 대한 자세한 내용은 다음 리소스를 참조하세요.

- [HTTP/2 사양 홈 페이지](https://http2.github.io/)
- [공식 HTTP/2 FAQ](https://http2.github.io/faq/)
- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
