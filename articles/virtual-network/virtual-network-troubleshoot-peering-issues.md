---
title: 가상 네트워크 피어링 문제 해결
description: 대부분의 가상 네트워크 피어링 문제를 해결하는 데 도움이 되는 단계입니다.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: cfdb79885563cbc0791ae3f3b2effd54ea3123a7
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867492"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>가상 네트워크 피어링 문제 해결

이 문제 해결 가이드에서는 대부분의 [가상 네트워크 피어링](virtual-network-peering-overview.md) 문제를 해결하는 데 도움이 되는 단계를 제공합니다.

![가상 네트워크 피어링 다이어그램](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>두 개의 가상 네트워크 간에 가상 네트워크 피어링 구성

가상 네트워크가 같은 구독에 있나요 아니면 서로 다른 구독에 있나요?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>가상 네트워크가 같은 구독에 있는 경우

같은 구독에 있는 가상 네트워크의 가상 네트워크 피어링을 구성하려면 다음 문서의 방법을 사용합니다.

* 가상 네트워크가 *동일한 지역* 에 있는 경우 [피어링 만들기](./virtual-network-manage-peering.md#create-a-peering)를 참조하세요.
* 가상 네트워크가 *서로 다른 지역* 에 있는 경우 [가상 네트워크 피어링](./virtual-network-peering-overview.md)을 참조하세요. 

> [!Note]
> 다음 리소스는 글로벌 가상 네트워크 피어링을 통해 연결할 수 없습니다. 
>
> * 기본 ILB(내부 부하 분산 장치) SKU를 초과하는 VM(가상 머신)
> * Redis Cache(기본 ILB SKU 사용)
> * 애플리케이션 게이트웨이 v1(기본 ILB SKU 사용)
> * 가상 머신 확장 집합(기본 ILB SKU 사용)
> * Azure Service Fabric 클러스터(기본 ILB SKU 사용)
> * SQL Server Always On(기본 ILB SKU 사용)
> * Azure App Service Environment for Power Apps(기본 ILB SKU 사용)
> * Azure API Management(기본 ILB SKU 사용)
> * Azure AD DS(Azure Active Directory Domain Services)(기본 ILB SKU 사용)

자세한 내용은 글로벌 피어링의 [요구 사항 및 제약 조건](./virtual-network-peering-overview.md#requirements-and-constraints)을 참조하세요.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>가상 네트워크가 서로 다른 구독 또는 Active Directory 테넌트에 있는 경우

서로 다른 구독 또는 Active Directory 테넌트에 있는 가상 네트워크 간에 가상 네트워크 피어링을 구성하려면 [서로 다른 구독에서 Azure CLI에 대한 피어링 만들기](./create-peering-different-subscriptions.md#cli)를 참조하세요.

> [!Note]
> 네트워크 피어링을 구성하려면 두 구독 모두에서 **네트워크 기여자** 권한이 있어야 합니다. 자세한 내용은 [피어링 권한](virtual-network-manage-peering.md#permissions)을 참조하세요.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>온-프레미스 리소스를 사용하는 허브-스포크 토폴로지를 통해 가상 네트워크 피어링 구성

![온-프레미스 스포크를 사용하는 가상 네트워크 피어링의 다이어그램](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>사이트 간 연결 또는 ExpressRoute 연결

섹션 [가상 네트워크 피어링을 위한 VPN 게이트웨이 전송 구성](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)의 단계를 수행합니다.

### <a name="for-point-to-site-connections"></a>지점 및 사이트 간 연결

1. 섹션 [가상 네트워크 피어링을 위한 VPN 게이트웨이 전송 구성](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)의 단계를 수행합니다.
2. 가상 네트워크 피어링을 설정 또는 변경한 후에는 지점 및 사이트 간 패키지를 다운로드하여 다시 설치합니다. 그러면 지점 및 사이트 간 클라이언트가 스포크 가상 네트워크의 업데이트된 경로를 가져옵니다.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>허브-스포크 토폴로지 가상 네트워크를 사용하여 가상 네트워크 피어링 구성

![가상 네트워크 스포크를 사용하는 가상 네트워크 피어링의 다이어그램](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>가상 네트워크가 같은 지역에 있는 경우


1. 허브 가상 네트워크에서 NVA(네트워크 가상 어플라이언스)를 구성합니다.
1. 스포크 가상 네트워크에서 다음 홉 유형이 "네트워크 가상 어플라이언스"인 사용자 정의 경로를 적용합니다.

자세한 내용은 [Service 연결](./virtual-network-peering-overview.md#service-chaining)을 참조하세요.

> [!Note]
> NVA 설정에 도움이 필요한 경우 [NVA 공급업체에 문의](https://mskb.pkisolutions.com/kb/2984655)하세요.

NVA 디바이스 설정 및 라우팅 문제 해결에 도움이 필요한 경우 [Azure의 네트워크 가상 어플라이언스 문제](./virtual-network-troubleshoot-nva.md)를 참조하세요.

### <a name="the-virtual-networks-are-in-different-regions"></a>가상 네트워크가 서로 다른 지역에 있는 경우

이제 글로벌 가상 네트워크 피어링을 통한 전송이 지원됩니다. 다음 리소스는 글로벌 가상 네트워크 피어링을 통해 연결할 수 없습니다.

* 기본 ILB SKU 뒤의 VM
* Redis Cache(기본 ILB SKU 사용)
* 애플리케이션 게이트웨이(기본 ILB SKU 사용)
* 확장 집합(기본 ILB SKU 사용)
* Service Fabric 클러스터(기본 ILB SKU 사용)
* SQL Server Always On(기본 ILB SKU 사용)
* App Service Environment(기본 ILB SKU 사용)
* API Management(기본 ILB SKU 사용)
* Azure AD DS(기본 ILB SKU 사용)

글로벌 피어링 요구 사항 및 제약 조건에 대한 자세한 내용은 [가상 네트워크 피어링](./virtual-network-peering-overview.md#requirements-and-constraints)을 참조하세요.

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>두 개의 피어링 가상 네트워크 간의 연결 문제 해결

필요한 [역할 및 권한](virtual-network-manage-peering.md#permissions)이 있는 계정으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 가상 네트워크를 선택하고 **피어링** 을 선택한 다음, **상태** 필드를 확인합니다. 상태가 무엇인가요?

### <a name="the-peering-status-is-connected"></a>피어링 상태가 "연결됨"인 경우

이 문제를 해결하려면 다음을 수행합니다.

1. 다음과 같이 네트워크 트래픽 흐름을 확인합니다.

   [연결 문제 해결](../network-watcher/network-watcher-connectivity-overview.md) 및 원본 VM에서 대상 VM으로의 [IP 흐름 확인](../network-watcher/network-watcher-ip-flow-verify-overview.md)를 사용하여 트래픽 흐름에 간섭을 일으키는 NSG 또는 UDR이 있는지 확인합니다.

   방화벽 또는 NVA를 사용하는 경우: 
   1. 이 단계가 완료된 후 복원할 수 있도록 UDR 매개 변수를 문서화합니다.
   2. 다음 홉으로 NVA를 가리키는 원본 VM 서브넷 또는 NIC에서 UDR을 제거합니다. NVA를 우회하는 원본 VM에서 대상으로의 직접 연결을 확인합니다. 이 단계가 작동하지 않으면 [NVA 문제 해결사](./virtual-network-troubleshoot-nva.md)를 참조하세요.

2. 네트워크 추적: 
   1. 대상 VM에서 네트워크 추적을 시작합니다. Windows는 **Netsh** 를 사용합니다. Linux는 **TCPDump** 를 사용합니다.
   2. 원본에서 대상 IP로 **TcpPing** 또는 **PsPing** 을 실행합니다.

      **TcpPing** 명령의 예: `tcping64.exe -t <destination VM address> 3389`

   3. **TcpPing** 이 완료되면 대상에서 네트워크 추적을 중지합니다.
   4. 원본의 패킷이 도착하면 네트워킹 문제가 없는 것입니다. VM 방화벽과 해당 포트에서 수신 대기하는 애플리케이션을 모두 검사하여 구성 문제를 찾습니다.

   > [!Note]
   > 다음 종류의 리소스는 글로벌 가상 네트워크 피어링(서로 다른 지역의 가상 네트워크)을 통해 연결할 수 없습니다.
   >
   > * 기본 ILB SKU 뒤의 VM
   > * Redis Cache(기본 ILB SKU 사용)
   > * 애플리케이션 게이트웨이(기본 ILB SKU 사용)
   > * 확장 집합(기본 ILB SKU 사용)
   > * Service Fabric 클러스터(기본 ILB SKU 사용)
   > * SQL Server Always On(기본 ILB SKU 사용)
   > * App Service Environment(기본 ILB SKU 사용)
   > * API Management(기본 ILB SKU 사용)
   > * Azure AD DS(기본 ILB SKU 사용)

자세한 내용은 글로벌 피어링의 [요구 사항 및 제약 조건](./virtual-network-peering-overview.md#requirements-and-constraints)을 참조하세요.

### <a name="the-peering-status-is-disconnected"></a>피어링 상태가 "연결 끊김"인 경우

이 문제를 해결하려면 두 가상 네트워크에서 피어링을 삭제하고 다시 만듭니다.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>허브-스포크 가상 네트워크와 온-프레미스 리소스 간의 연결 문제 해결

네트워크에서 타사 NVA 또는 VPN 게이트웨이를 사용하나요?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>네트워크에서 타사 NVA 또는 VPN 게이트웨이를 사용하는 경우

타사 NVA 또는 VPN 게이트웨이에 영향을 주는 연결 문제를 해결하려면 다음 문서를 참조하세요.

* [NVA 문제 해결사](./virtual-network-troubleshoot-nva.md)
* [서비스 체이닝](./virtual-network-peering-overview.md#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>네트워크에서 타사 NVA 또는 VPN 게이트웨이를 사용하지 않는 경우

허브 가상 네트워크와 스포크 가상 네트워크에 VPN 게이트웨이가 있나요?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>허브 가상 네트워크와 스포크 가상 네트워크 둘 다 VPN 게이트웨이가 있는 경우

원격 게이트웨이 사용은 지원되지 않습니다.

스포크 가상 네트워크에 이미 VPN 게이트웨이가 있는 경우 **원격 게이트웨이 사용** 옵션은 스포크 가상 네트워크에서 지원되지 않습니다. 이는 가상 네트워크 피어링 제한 때문입니다.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>허브 가상 네트워크와 스포크 가상 네트워크 둘 중 하나에 VPN 게이트웨이가 없는 경우

다음과 같은 사이트 간 또는 Azure ExpressRoute 연결의 경우 온-프레미스에서 원격 가상 네트워크로 연결할 때 발생하는 연결 문제의 주요 원인을 확인합니다.

* 게이트웨이가 있는 가상 네트워크에서 **전달된 트래픽 허용** 확인란을 선택했는지 확인합니다.
* 게이트웨이가 없는 가상 네트워크에서 **전달된 트래픽 허용** 확인란을 선택했는지 확인합니다.
* 네트워크 관리자에게 온-프레미스 디바이스를 검사하여 모든 디바이스에 원격 가상 네트워크 주소 공간이 추가되었는지 확인해 달라고 요청합니다.

지점 및 사이트 간 연결:

* 게이트웨이가 있는 가상 네트워크에서 **전달된 트래픽 허용** 확인란을 선택했는지 확인합니다.
* 게이트웨이가 없는 가상 네트워크에서 **전달된 트래픽 허용** 확인란을 선택했는지 확인합니다.
* 지점 및 사이트 간 클라이언트 패키지를 다운로드하여 다시 설치합니다. 새로 피어링된 가상 네트워크 경로는 자동으로 지점 및 사이트 간 클라이언트에 경로를 추가하지 않습니다.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>같은 지역에 있는 스포크 가상 네트워크 간의 허브-스포크 네트워크 연결 문제 해결

허브 네트워크에 NVA가 있어야 합니다. 스포크에서 다음 홉으로 NVA가 있는 UDR을 구성하고, 허브 가상 네트워크에서 **전달된 트래픽 허용** 을 사용하도록 설정합니다.

자세한 내용은 [서비스 연결](./virtual-network-peering-overview.md#service-chaining)을 참조하고, 원하는 [NVA 공급업체](https://mskb.pkisolutions.com/kb/2984655)와 요구 사항을 논의하세요.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>서로 다른 지역에 있는 스포크 가상 네트워크 간의 허브-스포크 네트워크 연결 문제 해결

이제 글로벌 가상 네트워크 피어링을 통한 전송이 지원됩니다. 다음 리소스는 글로벌 가상 네트워크 피어링을 통해 연결할 수 없습니다.

* 기본 ILB SKU 뒤의 VM
* Redis Cache(기본 ILB SKU 사용)
* 애플리케이션 게이트웨이(기본 ILB SKU 사용)
* 확장 집합(기본 ILB SKU 사용)
* Service Fabric 클러스터(기본 ILB SKU 사용)
* SQL Server Always On(기본 ILB SKU 사용)
* App Service Environment(기본 ILB SKU 사용)
* API Management(기본 ILB SKU 사용)
* Azure AD DS(기본 ILB SKU 사용)

자세한 내용은 글로벌 피어링의 [요구 사항 및 제약 조건](./virtual-network-peering-overview.md#requirements-and-constraints)과 [여러 VPN 토폴로지](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2)를 참조하세요.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>웹앱과 스포크 가상 네트워크 간의 허브-스포크 네트워크 연결 문제 해결

이 문제를 해결하려면 다음을 수행합니다.

1. Azure Portal에 로그인합니다. 
1. 웹앱에서 **네트워킹** 을 선택한 다음, **VNet 통합** 을 선택합니다.
1. 원격 가상 네트워크가 보이는지 확인합니다. 원격 가상 네트워크 주소 공간(**동기화 네트워크** 및 **경로 추가**)을 수동으로 입력합니다.

자세한 내용은 다음 문서를 참조하세요.

* [Azure 가상 네트워크에 앱 통합](../app-service/web-sites-integrate-with-vnet.md)
* [지점 및 사이트 간 VPN 라우팅 정보](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>가상 네트워크 피어링 구성 오류 메시지 문제 해결 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>현재 테넌트 `<TENANT ID>`에는 연결된 구독에 액세스할 수 있는 권한이 없습니다.

이 문제를 해결하려면 [피어링 만들기 - Azure CLI](./create-peering-different-subscriptions.md#cli)를 참조하세요.

### <a name="not-connected"></a>연결되지 않음

이 문제를 해결하려면 두 가상 네트워크에서 피어링을 삭제하고 다시 만듭니다.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Databricks 가상 네트워크를 피어링할 수 없음

이 문제를 해결하려면 **Azure Databricks** 에서 가상 네트워크 피어링을 구성한 다음, **리소스 ID** 를 사용하여 대상 가상 네트워크를 지정합니다. 자세한 내용은 [원격 가상 네트워크에 Databricks 가상 네트워크 피어링](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-peering#id2)를 참조하세요.

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>원격 가상 네트워크에 게이트웨이 없음

이 문제는 다른 테넌트에서 가상 네트워크를 피어링하고 나중에 `Use Remote Gateways`를 구성하려 할 때 발생합니다. Azure Portal은 다른 테넌트의 가상 네트워크에 있는 가상 네트워크 게이트웨이를 확인할 수 없습니다.

이 문제를 해결하는 다음 두 가지 방법이 있습니다.

 * 새 피어링을 만들 때 피어링을 삭제하고 `Use Remote Gateways` 옵션을 활성화합니다.
 * Azure Portal 대신 PowerShell 또는 CLI를 사용하여 `Use Remote Gateways`를 사용하도록 설정합니다.

## <a name="next-steps"></a>다음 단계

* [Azure VM 간의 연결 문제 해결](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)
