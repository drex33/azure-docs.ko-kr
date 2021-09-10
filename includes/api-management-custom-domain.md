---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 08/23/2021
ms.author: vlvinogr
ms.openlocfilehash: 4f75dc57d6f94983b390d45de657029dd157cf64
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123484449"
---
## <a name="how-api-management-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>API Management 프록시 서버가 TLS 핸드셰이크에서 SSL 인증서로 응답하는 방법

### <a name="clients-calling-with-server-name-indication-sni-header"></a>SNI(서버 이름 표시) 헤더를 포함하여 호출하는 클라이언트
프록시(게이트웨이)에 대해 구성된 사용자 지정 도메인이 하나 또는 여러 개 있는 경우 API Management는 다음 두 도메인 모두에서 HTTPS 요청에 응답할 수 있습니다.
* 사용자 지정 도메인(예: `contoso.com`)
* 기본 도메인(예: `apim-service-name.azure-api.net`). 

SNI 헤더의 정보에 따라 API Management는 적절한 서버 인증서로 응답합니다.

### <a name="clients-calling-without-sni-header"></a>SNI 헤더 없이 호출하는 클라이언트
[SNI](https://tools.ietf.org/html/rfc6066#section-3) 헤더를 보내지 않는 클라이언트를 사용하는 경우 API Management는 다음 논리에 따라 응답을 만듭니다.

* **서비스에 프록시에 대해 구성된 사용자 지정 도메인이 하나만 있는 경우** 기본 인증서는 프록시 사용자 지정 도메인에 발급된 인증서입니다.
* **서비스에 프록시에 대해 구성된 사용자 지정 도메인이 여러 개 있는 경우(**개발자** 및 **프리미엄** 계층에서 지원됨)** [defaultSslBinding](/rest/api/apimanagement/2020-12-01/api-management-service/create-or-update#hostnameconfiguration) 속성을 true로 설정("defaultSslBinding":"true")하여 기본 인증서를 지정할 수 있습니다. 속성을 설정하지 않으면 기본 인증서는 `*.azure-api.net`에서 호스트되는 기본 프록시 도메인에 발급된 인증서입니다.

## <a name="support-for-putpost-request-with-large-payload"></a>대용량 페이로드를 사용하여 PUT/POST 요청에 대한 지원

API Management 프록시 서버는 HTTPS에서 클라이언트 쪽 인증서를 사용하는 경우 대용량 페이로드(> 40KB)로 요청을 지원합니다. 서버의 요청이 중단되지 않도록 하려면 프록시 호스트 이름에서 속성 ["negotiateClientCertificate": "true"](/rest/api/apimanagement/2020-12-01/api-management-service/create-or-update#hostnameconfiguration)를 설정할 수 있습니다. 

속성이 true로 설정된 경우 모든 HTTP 요청 교환 전에 클라이언트 인증서가 SSL/TLS 연결 시 요청됩니다. 설정은 **프록시 호스트 이름** 수준에서 적용되므로 모든 연결 요청은 클라이언트 인증서를 요청합니다. 이 제한을 해결하고 프록시에 대해 최대 20개의 사용자 지정 도메인을 구성할 수 있습니다(**프리미엄** 계층에서만 지원됨).
