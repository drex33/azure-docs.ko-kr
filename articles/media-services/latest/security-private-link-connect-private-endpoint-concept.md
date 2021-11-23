---
title: 개인 끝점 연결 개요
description: 이 문서는 Media Services를 사용한 개인 끝점 연결에 대 한 개요입니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: 1f6559f347c58be3f4b077b9ca41dc03482af125
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936434"
---
# <a name="private-endpoint-connections-overview"></a>개인 끝점 연결 개요

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서는 Media Services를 사용한 개인 끝점 연결에 대 한 개요입니다.

## <a name="clients-using-vnet"></a>VNet을 사용 하는 클라이언트

개인 끝점을 사용 하는 VNet의 클라이언트는 동일한 DNS 이름을 사용 하 여 공용 Media Services 끝점에 연결 하는 클라이언트와 Media Services에 연결 해야 합니다. Media Services은 개인 링크를 통해 VNet에서 Media Services 끝점으로의 연결을 자동으로 라우팅하는 DNS 확인에 의존 합니다.

> [!IMPORTANT]
> 다른 방법으로는 전용 끝점을 사용 하는 경우 Media Services 끝점에 동일한 DNS 이름을 사용 합니다. privatelink 하위 도메인 URL을 사용 하 여 Media Services 끝점에 연결 하지 마세요.

Media Services는 기본적으로 개인 끝점에 대 한 필수 업데이트를 사용 하 여 VNet에 연결 된 [개인 DNS 영역](/azure/dns/private-dns-overview) 을 만듭니다. 그러나 사용자가 자체 DNS 서버를 사용하는 경우 DNS 구성을 추가로 변경해야 할 수 있습니다. 아래 DNS 변경 섹션에서는 프라이빗 엔드포인트에 필요한 업데이트에 대해 설명합니다.

## <a name="dns-changes-for-private-endpoints"></a>프라이빗 엔드포인트용 DNS 변경

개인 끝점을 만들 때 각 Media Services 끝점에 대 한 **DNS CNAME** 리소스 레코드는 접두사가 있는 하위 도메인의 별칭으로 업데이트 됩니다 `privatelink` . 또한 기본적으로 `privatelink` 개인 끝점에 대 한 Dns a 리소스 레코드를 사용 하 여 하위 도메인에 해당 하는 개인 DNS 영역을 만듭니다.

개인 끝점을 사용 하 여 VNet 외부에서 Media Services DNS 이름을 확인 하면 Media Services 끝점의 공용 끝점으로 확인 됩니다. 개인 끝점을 호스트 하는 VNet에서 확인 되는 경우 Media Services URL은 개인 끝점의 IP 주소로 확인 됩니다.

예를 들어 Media Services의 스트리밍 끝점에 대 한 DNS 리소스 레코드는 `MediaAccountA` 개인 끝점을 호스트 하는 VNet 외부에서 확인 되는 경우 다음과 같습니다.

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| mediaaccounta-uswe1.streaming.media.azure.net | CNAME | mediaaccounta-uswe1.streaming.privatelink.media.azure.net |
|mediaaccounta-uswe1.streaming.privatelink.media.azure.net | CNAME | `<Streaming Endpoint public endpoint>` |
| `<Streaming Endpoint public endpoint>` | CNAME | `<Streaming Endpoint internal endpoint>` |
| `<Streaming Endpoint internal endpoint>` | A | `<Streaming Endpoint public IP address>` |

IP allowlists를 사용 하 여 Media Services 끝점에 대 한 공용 인터넷 액세스를 거부 하거나 제한 하거나 계정 내의 모든 리소스에 대 한 공용 네트워크 액세스를 사용 하지 않도록 설정할 수 있습니다.

' MediaAccountA '의 예제 스트리밍 끝점에 대 한 DNS 리소스 레코드는 개인 끝점을 호스트 하는 VNet의 클라이언트에서 확인 되는 경우 다음과 같습니다.

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| mediaaccounta-uswe1.streaming.media.azure.net | CNAME | mediaaccounta-uswe1.streaming.privatelink.media.azure.net |
|mediaaccounta-uswe1.streaming.privatelink.media.azure.net | A | `<Streaming Endpoint public endpoint>`(예: "10.0.0.9가 |

이 접근 방식을 사용 하면 개인 끝점을 호스트 하는 VNet 내의 클라이언트에 대해 동일한 DNS 이름을 사용 하 여 Media Services 끝점에 액세스할 수 있습니다. VNet 외부의 클라이언트에 대해서도 동일한 작업을 수행 합니다.

네트워크에서 사용자 지정 DNS 서버를 사용 하는 경우 클라이언트는 개인 끝점 IP 주소에 대 한 Media Services 끝점의 FQDN을 확인 해야 합니다. 개인 링크 하위 도메인을 VNet의 개인 DNS 영역에 위임 하도록 DNS 서버를 구성 하거나  `mediaaccounta-usw22.streaming.privatelink.media.azure.net` 개인 끝점 IP 주소를 사용 하 여에 대 한 A 레코드를 구성 합니다.

> [!TIP]
> 사용자 지정 또는 온-프레미스 dns 서버를 사용 하는 경우 privatelink 하위 도메인의 Media Services 끝점 이름을 개인 끝점 IP 주소로 확인 하도록 DNS 서버를 구성 해야 합니다. Privatelink 하위 도메인을 VNet의 개인 DNS 영역에 위임 하거나 DNS 서버에서 DNS 영역을 구성 하 고 DNS A 레코드를 추가 하 여이 작업을 수행할 수 있습니다.

저장소 서비스에 대 한 개인 끝점과 연결 된 끝점 대상 하위 리소스에 대해 권장 되는 DNS 영역 이름은 다음과 같습니다.

| Media Services 끝점 | 개인 링크 그룹 ID | DNS 영역 이름 |
| ----------------------- | --------------------- | ------------- |
| 스트리밍 엔드포인트 | streamingendpoint | privatelink.media.azure.net |
| 키 배달 | streamingendpoint | privatelink.media.azure.net |
| 라이브 이벤트 | 라이브 | privatelink.media.azure.net |

전용 끝점을 지원 하기 위해 자체 DNS 서버를 구성 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure 가상 네트워크의 리소스 이름 확인](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [프라이빗 엔드포인트에 대한 DNS 구성](/azure/private-link/private-endpoint-overview#dns-configuration)

## <a name="public-network-access-flag"></a>공용 네트워크 액세스 플래그

`publicNetworkAccess`Media Services 계정의 플래그를 사용 하 여 공용 인터넷에서 Media Services 끝점에 대 한 액세스를 허용 하거나 차단할 수 있습니다. `publicNetworkAccess`을 사용 하지 않도록 설정 하면 공용 인터넷에서 Media Services 끝점에 대 한 요청이 차단 됩니다. 개인 끝점에 대 한 요청은 계속 허용 됩니다.  

## <a name="service-level-ip-allowlists"></a>서비스 수준 IP allowlists

`publicNetworkAccess`을 사용 하도록 설정 하면 서비스 수준 IP allowlists에 따라 공용 인터넷의 요청이 허용 됩니다. `publicNetworkAccess`을 사용 하지 않도록 설정 하면 IP allowlist 설정에 관계 없이 공용 인터넷의 요청이 차단 됩니다. IP allowlists는 공용 인터넷의 요청에만 적용 됩니다. 개인 끝점에 대 한 요청은 IP allowlists로 필터링 되지 않습니다.
