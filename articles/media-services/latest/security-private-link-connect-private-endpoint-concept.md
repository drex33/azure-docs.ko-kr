---
title: 프라이빗 엔드포인트 연결 개요
description: 이 문서는 Media Services 프라이빗 엔드포인트 연결에 대한 개요입니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: inhenkel
ms.openlocfilehash: 346630425cd6a77c7ebf18b6f16a1bbd1bcab705
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053534"
---
# <a name="private-endpoint-connections-overview"></a>프라이빗 엔드포인트 연결 개요

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서는 Media Services 프라이빗 엔드포인트 연결에 대한 개요입니다.

## <a name="clients-using-vnet"></a>VNet을 사용하는 클라이언트

프라이빗 엔드포인트를 사용하는 VNet의 클라이언트는 공용 Media Services 엔드포인트에 연결하는 클라이언트와 동일한 DNS 이름을 사용하여 Media Services 연결해야 합니다. Media Services DNS 확인에 의존하여 프라이빗 링크를 통해 VNet에서 Media Services 엔드포인트로 연결을 자동으로 라우팅합니다.

> [!IMPORTANT]
> 프라이빗 엔드포인트를 사용할 때와 동일한 DNS 이름을 Media Services 엔드포인트에 사용합니다. privatelink 하위 도메인 URL을 사용하여 Media Services 엔드포인트에 연결하지 마세요.

Media Services 기본적으로 프라이빗 엔드포인트에 필요한 업데이트를 사용하여 VNet에 연결된 프라이빗 [DNS 영역을](/dns/private-dns-overview) 만듭니다. 그러나 사용자가 자체 DNS 서버를 사용하는 경우 DNS 구성을 추가로 변경해야 할 수 있습니다. 아래 DNS 변경 섹션에서는 프라이빗 엔드포인트에 필요한 업데이트에 대해 설명합니다.

## <a name="dns-changes-for-private-endpoints"></a>프라이빗 엔드포인트에 대한 DNS 변경 내용

프라이빗 엔드포인트를 만들면 각 Media Services 엔드포인트에 대한 **DNS CNAME** 리소스 레코드가 접두사 가 있는 하위 도메인의 별칭으로 업데이트됩니다. `privatelink` 기본적으로 프라이빗 엔드포인트에 대한 DNS A 리소스 레코드를 사용하여 하위 도메인에 해당하는 프라이빗 DNS 영역을 `privatelink` 만듭니다.

프라이빗 엔드포인트를 사용하여 VNet 외부에서 Media Services DNS 이름을 확인하면 Media Services 엔드포인트의 퍼블릭 엔드포인트로 확인됩니다. 프라이빗 엔드포인트를 호스트하는 VNet에서 확인되면 Media Services URL이 프라이빗 엔드포인트의 IP 주소로 확인됩니다.

예를 들어 프라이빗 엔드포인트를 호스트하는 VNet 외부에서 확인되는 경우 Media Services 스트리밍 엔드포인트에 대한 DNS 리소스 `MediaAccountA` 레코드는 다음과 같습니다.

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| mediaaccounta-uswe1.streaming.media.azure.net | CNAME | mediaaccounta-uswe1.streaming.privatelink.media.azure.net |
|mediaaccounta-uswe1.streaming.privatelink.media.azure.net | CNAME | `<Streaming Endpoint public endpoint>` |
| `<Streaming Endpoint public endpoint>` | CNAME | `<Streaming Endpoint internal endpoint>` |
| `<Streaming Endpoint internal endpoint>` | A | `<Streaming Endpoint public IP address>` |

IP 허용 목록을 사용하거나 계정 내의 모든 리소스에 대해 공용 네트워크 액세스를 사용하지 않도록 하여 Media Services 엔드포인트에 대한 공용 인터넷 액세스를 거부하거나 제한할 수 있습니다.

프라이빗 엔드포인트를 호스팅하는 VNet의 클라이언트에서 확인할 때 'MediaAccountA'의 스트리밍 엔드포인트 예제에 대한 DNS 리소스 레코드는 다음과 같습니다.

| 이름 | Type | 값 |
| ---- | ---- | ----- |
| mediaaccounta-uswe1.streaming.media.azure.net | CNAME | mediaaccounta-uswe1.streaming.privatelink.media.azure.net |
|mediaaccounta-uswe1.streaming.privatelink.media.azure.net | A | `<Streaming Endpoint public endpoint>`, 예: " 10.0.0.9 |

이 방법을 사용하면 프라이빗 엔드포인트를 호스트하는 VNet 내에서 클라이언트에 대해 동일한 DNS 이름을 사용하여 Media Services 엔드포인트에 액세스할 수 있습니다. VNet 외부의 클라이언트에 대해 동일한 작업을 수행합니다.

네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 클라이언트는 Media Services 엔드포인트에 대한 FQDN을 프라이빗 엔드포인트 IP 주소로 확인해야 합니다. 프라이빗 링크 하위 도메인을 VNet의 프라이빗 DNS 영역에 위임하도록 DNS 서버를 구성하거나 프라이빗 엔드포인트 IP 주소를 사용하여 에 대한 A  `mediaaccounta-usw22.streaming.privatelink.media.azure.net` 레코드를 구성합니다.

> [!TIP]
> 사용자 지정 또는 온-프레미스 DNS 서버를 사용하는 경우 privatelink 하위 도메인의 Media Services 엔드포인트 이름을 프라이빗 엔드포인트 IP 주소로 확인하도록 DNS 서버를 구성해야 합니다. VNet의 프라이빗 DNS 영역에 privatelink 하위 도메인을 위임하거나 DNS 서버에서 DNS 영역을 구성하고 DNS A 레코드를 추가하여 이 작업을 수행할 수 있습니다.

스토리지 서비스 및 연결된 엔드포인트 대상 하위 리소스에 대한 프라이빗 엔드포인트에 권장되는 DNS 영역 이름은 다음과 같습니다.

| Media Services 끝점 | Private Link 그룹 ID | DNS 영역 이름 |
| ----------------------- | --------------------- | ------------- |
| 스트리밍 엔드포인트 | streamingendpoint | privatelink.media.azure.net |
| 키 배달 | streamingendpoint | privatelink.media.azure.net |
| 라이브 이벤트 | liveevent | privatelink.media.azure.net |

프라이빗 엔드포인트를 지원하도록 자체 DNS 서버를 구성하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 가상 네트워크의 리소스 이름 확인](/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [프라이빗 엔드포인트에 대한 DNS 구성](/private-link/private-endpoint-overview#dns-configuration)

## <a name="public-network-access-flag"></a>공용 네트워크 액세스 플래그

`publicNetworkAccess`Media Services 계정의 플래그를 사용하여 공용 인터넷에서 Media Services 엔드포인트에 대한 액세스를 허용하거나 차단할 수 있습니다. `publicNetworkAccess`를 사용하지 않도록 설정하면 공용 인터넷의 모든 Media Services 엔드포인트에 대한 요청이 차단되고 프라이빗 엔드포인트에 대한 요청은 여전히 허용됩니다.  

## <a name="service-level-ip-allowlists"></a>서비스 수준 IP 허용 목록

`publicNetworkAccess`를 사용하도록 설정하면 서비스 수준 IP 허용 목록에 따라 공용 인터넷의 요청이 허용됩니다. 를 사용하지 않도록 `publicNetworkAccess` 설정하면 IP 허용 목록 설정에 관계없이 공용 인터넷의 요청이 차단됩니다. IP 허용 목록은 공용 인터넷의 요청에만 적용됩니다. 프라이빗 엔드포인트에 대한 요청은 IP 허용 목록에 의해 필터링되지 않습니다.
