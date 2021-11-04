---
title: Azure CDN 규칙 엔진의 Verizon 특정 HTTP 헤더 | Microsoft Docs
description: 이 문서에서는 Azure CDN 규칙 엔진에서 Verizon 특정 HTTP 헤더를 사용하는 방법을 설명합니다.
services: cdn
documentationcenter: ''
author: duongau
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: duau
ms.openlocfilehash: 25e51bbc96f07f29fe62e85366d1d6359dffaaea
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439313"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Azure CDN 규칙 엔진의 Verizon 특정 HTTP 헤더

**Verizon의 Azure CDN Premium** 제품의 경우, HTTP 요청을 원본 서버로 보내면 POP(상호 접속 위치) 서버는 클라이언트 요청에서 예약된 하나 이상의 헤더(또는 프록시 특별 헤더)를 POP에 추가할 수 있습니다. 이러한 헤더는 수신되는 표준 전달 헤더에 추가됩니다. 표준 요청 헤더에 대한 자세한 내용은 [요청 필드](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields)를 참조하세요.

이러한 예약된 헤더 중 하나가 Azure CDN(콘텐츠 전송 네트워크) POP 요청에서 원본 서버에 추가되지 않도록 하려면 규칙 엔진에서 [프록시 특별 헤더 기능](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm)을 사용하여 규칙을 만들어야 합니다. 이 규칙에서는 헤더 필드의 기본 헤더 목록에서 제거하려는 헤더를 제외합니다. [디버그 캐시 응답 헤더 기능](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm)을 사용하도록 설정한 경우 필요한 `X-EC-Debug` 헤더를 추가해야 합니다. 

예를 들어 `Via` 헤더를 제거하려면 규칙의 헤더 필드에 *X-Forwarded-For, X-Forwarded-Proto, X-Host, X-Midgress, X-Gateway-List, X-EC-Name, Host* 헤더의 목록이 포함되어야 합니다. 

![프록시 특별 헤더 규칙](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

다음 표에서는 Verizon CDN POP에서 요청에 추가할 수 있는 헤더를 설명합니다.

요청 헤더 | Description | 예제
---------------|-------------|--------
[Via](#via-request-header) | 요청을 원본 서버에 프록시한 POP 서버를 식별합니다. | HTTP/1.1 ECS (dca/1A2B)
X-Forwarded-For | 요청자의 IP 주소를 나타냅니다.| 10.10.10.10
X-Forwarded-Proto | 요청의 프로토콜을 나타냅니다. | http
X-Host | 요청의 호스트 이름을 나타냅니다. | cdn.mydomain.com
X-Midgress | 요청이 추가 CDN 서버를 통해 프록시되었는지 여부를 나타냅니다. 예를 들어 POP 서버-원본 실드 서버 또는 POP 서버-ADN 게이트웨이 서버 프록시 연결이 있습니다. <br />중간 트래픽이 발생할 때만 요청에 이 헤더가 추가됩니다. 이 경우 헤더가 1로 설정되어 요청이 추가 CDN 서버를 통해 프록시되었음을 나타냅니다.| 1
[Host](#host-request-header) | 요청된 콘텐츠를 찾을 수 있는 호스트와 포트를 식별합니다. | marketing.mydomain.com:80
[X-Gateway-List](#x-gateway-list-request-header) | ADN: 고객 원본에 할당된 ADN 게이트웨이 서버의 장애 조치 목록을 식별합니다. <br />원본 실드: 고객 원본에 할당된 원본 실드 서버 집합을 나타냅니다. | `icn1,hhp1,hnd1`
X-EC- _&lt;name&gt;_ | *X-EC* 로 시작하는 요청 헤더(예: X-EC-Tag, [X-EC-Debug](cdn-http-debug-headers.md))는 CDN에서 사용하도록 예약됩니다.| waf-production

## <a name="via-request-header"></a>Via 요청 헤더
`Via` 요청 헤더에서 POP 서버를 식별하는 형식은 다음 구문으로 지정됩니다.

`Via: Protocol from Platform (POP/ID)` 

구문에 사용된 용어는 다음과 같이 정의됩니다.
- Protocol: 요청을 프록시하는 데 사용된 프로토콜 버전(예: HTTP/1.1)을 나타냅니다. 

- Platform: 콘텐츠가 요청된 플랫폼을 나타냅니다. 이 필드에 유효한 코드는 다음과 같습니다. 

    코드 | 플랫폼
    -----|---------
    ECAcc | HTTP Large
    ECS   | HTTP Small
    ECD   | ADN(애플리케이션 전달 네트워크)

- POP: 요청을 처리한 [POP](cdn-pop-abbreviations.md)를 나타냅니다. 

- ID: 내부 전용입니다.

### <a name="example-via-request-header"></a>Via 요청 헤더 예제

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>호스트 요청 헤더
다음 두 조건에 모두 해당하면 POP 서버에서 `Host` 헤더를 덮어씁니다.
- 요청된 콘텐츠에 대한 원본은 고객 원본 서버입니다.
- 해당 고객 원본의 HTTP 호스트 헤더 옵션이 비어 있지 않습니다.

HTTP 호스트 헤더 옵션에 정의된 값을 반영하기 위해 `Host` 요청 헤더를 덮어씁니다.
고객 원본의 HTTP 호스트 헤더 옵션을 비워 두면 요청자가 제출한 `Host` 요청 헤더가 고객의 원본 서버로 전달됩니다.

## <a name="x-gateway-list-request-header"></a>X-Gateway-List 요청 헤더
다음 조건 중 하나가 충족되면 POP 서버에서 X-Gateway-List 요청 헤더를 추가하거나 덮어씁니다.
- 요청이 ADN 플랫폼을 가리킵니다.
- 요청이 원본 실드 기능으로 보호되는 고객 원본 서버로 전달됩니다.

