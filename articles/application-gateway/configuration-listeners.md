---
title: Azure Application Gateway 수신기 구성
description: 이 문서에서는 Azure Application Gateway 수신기를 구성하는 방법을 설명합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e9468de9cbd88e9ad4406ac929aebc6bda938fb3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447382"
---
# <a name="application-gateway-listener-configuration"></a>Application Gateway 수신기 구성

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

수신기는 포트, 프로토콜, 호스트, IP 주소를 사용하여 들어오는 연결 요청을 확인하는 논리적 엔터티입니다. 수신기를 구성하는 경우 게이트웨이에 들어오는 요청의 해당 값과 일치하는 설정 값을 입력해야 합니다.

Azure Portal을 사용하여 애플리케이션 게이트웨이를 만들 때 수신기의 프로토콜 및 포트를 선택하여 기본 수신기도 만듭니다. 수신기에서 HTTP2 지원을 사용할지 여부를 선택할 수 있습니다. 애플리케이션 게이트웨이를 만든 후 기본 수신기(*appGatewayHttpListener*)의 설정을 편집하거나 새 수신기를 만들 수 있습니다.

## <a name="listener-type"></a>수신기 유형

새 수신기를 만들 때 [*기본* 과 *다중 사이트*](./application-gateway-components.md#types-of-listeners) 중에서 선택할 수 있습니다.

- 모든 도메인에 대한 모든 요청을 허용하여 백 엔드 풀에 전달하려는 경우 기본을 선택합니다. [기본 수신기로 애플리케이션 게이트웨이를 만드는 방법](./quick-create-portal.md)을 알아봅니다.

- *호스트* 헤더 또는 호스트 이름을 기반으로 요청을 다른 백 엔드 풀로 전달하려면 다중 사이트 수신기를 선택합니다. 여기서 수신 요청과 일치하는 호스트 이름도 지정해야 합니다. Application Gateway가 HTTP 1.1 호스트 헤더를 사용하여 동일한 공용 IP 주소 및 포트에서 둘 이상의 웹 사이트를 호스트하기 때문입니다. 자세한 내용은 [Application Gateway를 사용하여 여러 사이트 호스팅](multiple-site-overview.md)을 참조하세요.

### <a name="order-of-processing-listeners"></a>수신기 처리 순서

v1 SKU의 경우 요청은 규칙의 순서와 수신기의 유형에 일치하게 됩니다. 기본 수신기를 사용하는 규칙이 순서 첫 번째인 경우 이는 먼저 처리되며 해당 포트 및 IP 조합에 대한 모든 요청을 허용합니다. 이를 방지하려면 다중 사이트 수신기를 사용하는 규칙을 첫 번째로 구성하고 기본 수신기를 사용하는 규칙을 목록의 마지막으로 푸시합니다.

v2 SKU의 경우 다중 사이트 수신기가 기본 수신기보다 먼저 처리됩니다.

## <a name="front-end-ip-address"></a>프런트 엔드 IP 주소

이 수신기와 연결하려는 프런트 엔드 IP 주소를 선택합니다. 수신기는 이 IP에서 들어오는 요청을 수신 대기합니다.

## <a name="front-end-port"></a>프런트 엔드 포트

프런트 엔드 포트를 선택합니다. 기존 포트를 선택하거나 새 포트를 만듭니다. [허용되는 포트 범위](./application-gateway-components.md#ports)에서 값을 선택합니다. 잘 알려진 포트(예: 80 및 443)뿐만 아니라 허용되는 모든 적합한 사용자 지정 포트를 사용할 수 있습니다. 포트는 공용 연결 수신기 또는 프라이빗 연결 수신기에 대해 사용할 수 있습니다.

## <a name="protocol"></a>프로토콜

HTTP 또는 HTTPS 선택:

- HTTP를 선택하면 클라이언트와 애플리케이션 게이트웨이 간의 트래픽이 암호화되지 않습니다.

- [TLS 종료](features.md#secure-sockets-layer-ssltls-termination) 또는 [엔드투엔드 TLS 암호화](./ssl-overview.md)를 원하는 경우 HTTPS를 선택합니다. 클라이언트와 애플리케이션 게이트웨이 간의 트래픽이 암호화됩니다. 그리고 TLS 연결은 애플리케이션 게이트웨이에서 종료됩니다. 엔드투엔드 TLS 암호화를 원하는 경우 HTTPS를 선택하고 **백 엔드 HTTP** 설정을 구성해야 합니다. 이렇게 하면 애플리케이션 게이트웨이에서 백 엔드로 이동하는 경우 트래픽이 다시 암호화됩니다.


TLS 종료를 구성 하려면 TLS/SSL 인증서를 수신기에 추가 해야 합니다. 이를 통해 Application Gateway는 들어오는 트래픽의 암호를 해독 하 고 클라이언트에 응답 트래픽을 암호화할 수 있습니다. Application Gateway에 제공 된 인증서는 개인 키와 공개 키를 모두 포함 하는 PFX (개인 정보 Exchange) 형식 이어야 합니다.

## <a name="supported-certificates"></a>지원되는 인증서

[Application Gateway를 사용한 TLS 종료 및 엔드투엔드 TLS 개요](ssl-overview.md#certificates-supported-for-tls-termination)를 참조하세요.

## <a name="additional-protocol-support"></a>추가 프로토콜 지원

### <a name="http2-support"></a>HTTP2 지원

HTTP/2 프로토콜 지원은 애플리케이션 게이트웨이 수신기에만 연결하는 클라이언트에서 사용할 수 있습니다. 백 엔드 서버 풀에 대한 통신은 HTTP/1.1을 통해 이루어집니다. 기본적으로 HTTP/2 지원은 사용할 수 없습니다. 다음 Azure PowerShell 코드 조각은 이를 사용하도록 설정하는 방법을 보여 줍니다.

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="websocket-support"></a>WebSocket 지원

WebSocket 지원은 기본적으로 사용하도록 설정되어 있습니다. 사용하도록 설정 또는 사용하지 않도록 설정하기 위해 사용자가 구성할 수 있는 설정이 없습니다. HTTP 및 HTTPS 수신기 모두에서 WebSocket을 사용할 수 있습니다.

## <a name="custom-error-pages"></a>사용자 지정 오류 페이지

전체 수준 또는 수신기 수준에서 사용자 지정 오류를 정의할 수 있습니다. 그러나 Azure Portal에서 전역 수준 사용자 지정 오류 페이지 만들기는 현재 지원되지 않습니다. 수신기 수준에서 403 웹 애플리케이션 방화벽 오류 또는 502 유지 관리 페이지에 대한 사용자 지정 오류 페이지를 구성할 수 있습니다. 또한 지정된 오류 상태 코드에 대해 공개적으로 액세스할 수 있는 BLOB URL을 지정해야 합니다. 자세한 내용은 [Application Gateway 사용자 지정 오류 페이지 만들기](./custom-error.md)를 참조하세요.

![Application Gateway 오류 코드](/azure/application-gateway/media/custom-error/ag-error-codes.png)

전역 사용자 지정 오류 페이지를 구성하려면 [Azure PowerShell 구성](./custom-error.md#azure-powershell-configuration)을 참조하세요.

## <a name="tls-policy"></a>TLS 정책

TLS/SSL 인증서 관리를 중앙 집중화하고 백 엔드 서버 팜에 대한 암호화-암호 해독 오버헤드를 줄일 수 있습니다. 중앙 집중식 TLS 처리를 통해 보안 요구 사항에 적합한 중앙 TLS 정책을 지정할 수도 있습니다. *기본*, *사전 정의된* 또는 *사용자 지정* TLS 정책을 선택할 수 있습니다.

TLS 프로토콜 버전을 제어하는 TLS 정책을 구성합니다. TLS1.0, TLS1.1 및 TLS1.2의 TLS 핸드셰이크에 최소 프로토콜 버전을 사용하도록 애플리케이션 게이트웨이를 구성할 수 있습니다. SSL 2.0 및 3.0은 기본적으로 사용하지 않도록 설정되며 구성할 수 없습니다. 자세한 내용은 [Application Gateway TLS 정책 개요](./application-gateway-ssl-policy-overview.md)를 참조하세요.

수신기를 만든 후에는 요청 라우팅 규칙과 연결합니다. 이 규칙은 수신기에서 수신된 요청이 백 엔드로 라우팅되는 방법을 결정합니다.

## <a name="next-steps"></a>다음 단계

- [요청 라우팅 규칙에 대해 알아보기](configuration-request-routing-rules.md)
