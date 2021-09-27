---
title: Azure Private Link를 사용하여 네트워크를 Azure Arc에 안전하게 연결
description: Azure Private Link를 사용하여 네트워크를 Azure Automation에 안전하게 연결하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 53bd9310c193d4fad1d550fbf33446754c30ecd6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631517"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-arc"></a>Azure Private Link를 사용하여 네트워크를 Azure Arc에 안전하게 연결

[Azure Private Link](../../private-link/private-link-overview.md)를 사용하면 프라이빗 엔드포인트를 사용하여 Azure PaaS 서비스를 가상 네트워크에 안전하게 연결할 수 있습니다. 많은 서비스의 경우 리소스당 엔드포인트를 설정하기만 하면 됩니다. 즉, 온-프레미스 또는 다중 클라우드 서버를 Azure Arc에 연결하고, 공용 네트워크를 사용하는 대신 Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) 또는 사이트 간 [VPN 연결](../../vpn-gateway/vpn-gateway-about-vpngateways.md)을 통해 모든 트래픽을 보낼 수 있습니다.

Azure Arc 지원 서버부터는 프라이빗 링크 범위 모델을 사용하여 여러 서버 또는 컴퓨터가 단일 프라이빗 엔드포인트로 Azure Arc 리소스와 통신하도록 할 수 있습니다.

이 문서에서는 Azure Arc Private Link 범위를 사용하는 경우와 설정 방법에 대해 설명합니다.

> [!NOTE]
> Azure Arc Private Link 범위(미리 보기)는 모든 상업용 클라우드 지역에서 제공되지만 현재 미국 정부용 클라우드에서는 사용할 수 없습니다.

## <a name="advantages"></a>장점

Private Link를 사용하면 다음을 수행할 수 있습니다.

- 공용 네트워크 액세스를 개방하지 않고 Azure Arc에 비공개로 연결
- Azure Arc 지원 컴퓨터 또는 서버의 데이터는 권한 있는 프라이빗 네트워크를 통해서만 액세스할 수 있는지 확인합니다. 여기에는 배포 후 관리 및 모니터링 지원을 제공하는 컴퓨터 또는 서버에 설치된 [VM 확장](manage-vm-extensions.md)의 데이터도 포함됩니다.
- 프라이빗 엔드포인트를 통해 연결되는 특정 Azure Arc 지원 서버 및 기타 Azure 서비스 리소스(예: Azure Monitor)를 정의하여 프라이빗 네트워크에서 데이터 반출을 방지합니다.
- ExpressRoute 및 Private Link를 사용하여 온-프레미스 개인 네트워크를 Azure Arc에 안전하게 연결
- Microsoft Azure 백본 네트워크 내에서 모든 트래픽을 유지합니다.

자세한 내용은 [Private Link의 주요 이점](../../private-link/private-link-overview.md#key-benefits)을 참조하세요.

## <a name="how-it-works"></a>작동 방법

Azure Arc Private Link 범위(미리 보기)는 프라이빗 엔드포인트(및 해당 엔드포인트가 포함된 가상 네트워크)를 Azure 리소스(이 경우 Azure Arc 지원 서버)에 연결합니다. Azure Automation 업데이트 관리 또는 Azure Monitor 같이 지원되는 Azure Arc 지원되는 서버 중 하나를 사용하도록 설정하면 해당 리소스가 다른 Azure 리소스에 연결됩니다. 예:

- Log Analytics 작업 영역 - Azure Automation 업데이트 관리, Azure Automation 변경 내용 추적 및 인벤토리, Azure Monitor VM 인사이트, Log Analytics 에이전트를 사용한 Azure Monitor 로그 수집에 필요합니다.
- Azure Automation 계정은 업데이트 관리와 변경 내용 추적 및 인벤토리에 필요합니다.
- Azure Key Vault
- Azure Blob Storage - 사용자 지정 스크립트 확장에 필요합니다.

:::image type="content" source="./media/private-link-security/private-link-topology.png" alt-text="기본 리소스 토폴로지 다이어그램" border="true":::

이전에 나열된 Azure Arc 지원 서버에서 다른 Azure 리소스에 연결하려면 각 서비스에 대한 Private Link 구성해야 합니다. [Azure Automation](../../automation/how-to/private-link-security.md), [Azure Monitor](../../azure-monitor/logs/private-link-security.md), [Azure Key Vault](../../key-vault/general/private-link-service.md) 또는 [Azure Blob Storage](../../private-link/tutorial-private-endpoint-storage-portal.md)에 대한 프라이빗 링크 구성에 대한 자세한 내용은 다음 항목을 참조하세요.

> [!IMPORTANT]
> Azure Private Link가 이제 일반 공급됩니다. 프라이빗 엔드포인트 및 Private Link 서비스(표준 부하 분산 장치 뒤의 서비스)가 모두 일반 공급됩니다. 다른 Azure PaaS는 다른 일정에 따라 Azure Private Link에 온보딩됩니다. Private Link에서 Azure PaaS의 정확한 상태는 [Private Link 가용성](../../private-link/availability.md)을 참조하세요. 알려진 제한은 [프라이빗 엔드포인트](../../private-link/private-endpoint-overview.md#limitations) 및 [Private Link Service](../../private-link/private-link-service-overview.md#limitations)를 참조하세요.

* VNet의 프라이빗 엔드포인트는 이러한 엔드포인트의 공용 IP를 사용하는 대신 네트워크 풀에서 프라이빗 IP를 통해 Azure 지원 서버 엔드포인트에 도착할 수 있습니다. 이렇게 하면 요청되지 않은 아웃바운드 트래픽까지 VNet을 열지 않고도 Azure Arc 지원 서버 리소스를 계속 사용할 수 있습니다.

* 프라이빗 엔드포인트에서 리소스에 대한 트래픽은 Microsoft Azure 백본을 지나며, 공용 네트워크로 라우팅되지 않습니다.

* 공용 네트워크에서 수집 및 쿼리를 허용하거나 거부하도록 각 컴포넌트를 구성할 수 있습니다. 리소스 수준의 보호를 제공하므로 특정 리소스에 대한 트래픽을 제어할 수 있습니다.

## <a name="restrictions-and-limitations"></a>제한 사항

Azure Arc 지원 서버 Private Link Scope 개체에는 Private Link 설정을 계획할 때 고려해야 하는 여러 제한이 있습니다.

- 최대 하나의 Azure Arc Private Link 범위를 가상 네트워크에 연결할 수 있습니다.

- Azure Arc 지원 머신 또는 서버 리소스는 하나의 Azure Arc 지원 서버 프라이빗 링크 범위에만 연결할 수 있습니다.

- 모든 온-프레미스 머신은 동일한 DNS 전달자를 사용하는 올바른 프라이빗 엔드포인트 정보(FQDN 레코드 이름 및 프라이빗 IP 주소)를 확인하여 동일한 프라이빗 엔드포인트를 사용해야 합니다. 자세한 내용은 [Azure 프라이빗 엔드포인트 DNS 구성](../../private-link/private-endpoint-dns.md)을 참조하세요.

- Azure Arc 사용 가능한 서버와 Azure Arc Private Link 범위는 동일한 Azure 지역에 있어야 합니다. 프라이빗 엔드포인트와 가상 네트워크도 동일한 Azure 지역에 있어야 하지만 이 지역은 Azure Arc Private Link 범위 및 Arc 지원 서버와 다를 수 있습니다.

- Azure Active Directory 및 Azure Resource Manager 서비스 태그에 대한 트래픽은 미리 보기 중에 온-프레미스 네트워크 방화벽을 통해 허용되어야 합니다.

- 사용할 다른 Azure 서비스(예: Azure Monitor)에는 가상 네트워크에서 고유한 프라이빗 엔드포인트가 필요합니다.

- Azure Arc 지원 서버 프라이빗 링크 범위는 현재 Azure 미국 정부 지역에서 제공되지 않습니다.

## <a name="planning-your-private-link-setup"></a>프라이빗 링크 설정 계획

프라이빗 링크를 통해 서버를 Azure Arc에 연결하려면 다음을 수행하도록 네트워크를 구성해야 합니다.

1. [사이트 간 VPN](../../vpn-gateway/tutorial-site-to-site-portal.md) 또는 [ExpressRoute 회로](../../expressroute/expressroute-howto-linkvnet-arm.md)를 사용하여 온-프레미스 네트워크와 Azure 가상 네트워크 간에 연결을 설정합니다.

1. 프라이빗 엔드포인트를 통해 Azure Arc와 통신하고 프라이빗 엔드포인트를 사용하여 Azure 가상 네트워크와 연결할 수 있는 컴퓨터 또는 서버를 제어하는 Azure Arc Private Link 범위(미리 보기)를 배포합니다.

1. 로컬 네트워크에서 DNS 구성을 업데이트하여 프라이빗 엔드포인트 주소를 확인합니다.

1. Azure Active Directory 및 Azure Resource Manager에 대한 액세스를 허용하도록 로컬 방화벽을 구성합니다. 이는 임시 단계이며 이러한 서비스의 프라이빗 엔드포인트가 미리 보기로 제공되는 경우에는 필요하지 않습니다.

1. Azure Arc 지원 서버에 등록된 컴퓨터 또는 서버를 프라이빗 링크 범위와 연결합니다.

1. 필요에 따라 다음과 같이 컴퓨터 또는 서버가 관리되는 다른 Azure 서비스에 대한 프라이빗 엔드포인트를 배포합니다.

    - Azure Monitor
    - Azure Automation
    - Azure Blob Storage
    - Azure Key Vault

이 문서에서는 ExpressRoute 회로 또는 사이트 간 VPN 연결을 이미 설정했다고 가정합니다.

## <a name="network-configuration"></a>네트워크 구성

Azure Arc 지원 서버는 여러 Azure 서비스와 통합되어 하이브리드 컴퓨터 또는 서버에 클라우드 관리 및 거버넌스를 제공합니다. 이러한 서비스의 대부분은 이미 프라이빗 엔드포인트를 제공하고 있지만 해당 서비스가 프라이빗 엔드포인트를 제공할 때까지 인터넷을 통해 Azure Active Directory 및 Azure Resource Manager에 대한 액세스를 허용하도록 방화벽 및 라우팅 규칙을 구성해야 합니다.

이 작업은 다음 두 가지 방법으로 수행할 수 있습니다.

- 네트워크가 Azure VPN 또는 ExpressRoute 회로를 통해 모든 인터넷 바인딩 트래픽을 라우팅하도록 구성된 경우, [서비스 태그](../../virtual-network/service-tags-overview.md)를 사용하여 Azure AD 및 Azure에 대한 아웃바운드 TCP 443(HTTPS) 액세스를 허용하도록 Azure의 서브넷과 연결된 NSG(네트워크 보안 그룹)를 구성할 수 있습니다. NSG 규칙은 다음과 같습니다.

    |설정 |Azure AD 규칙 | Azure 규칙 |
    |--------|--------------|-----------------------------|
    |원본 |가상 네트워크 |가상 네트워크 |
    |원본 포트 범위 |* |* |
    |대상 |서비스 태그 |서비스 태그 |
    |대상 서비스 태그 |AzureActiveDirectory |AzureResourceManager |
    |대상 포트 범위 |443 |443 |
    |프로토콜 |TCP |TCP |
    |작업 |허용 |허용 |
    |우선 순위 |150(인터넷 액세스를 차단하는 규칙보다 작아야 함) |151(인터넷 액세스를 차단하는 규칙보다 작아야 함) |
    |Name |AllowAADOutboundAccess |AllowAzOutboundAccess |

- 다운로드 가능한 서비스 태그 파일을 사용하여 Azure AD 및 Azure에 대한 아웃바운드 TCP 443(HTTPS) 액세스를 허용하도록 로컬 네트워크의 방화벽을 구성합니다. JSON 파일에는 Azure AD 및 Azure에서 사용하는 모든 공용 IP 주소 범위가 포함되어 있으며 변경 내용을 반영하도록 매월 업데이트됩니다. Azure AD 서비스 태그는 `AzureActiveDirectory`이며 Azure의 서비스 태그는 `AzureResourceManager`입니다. 방화벽 규칙을 구성하는 방법을 알아보려면 네트워크 관리자 및 네트워크 방화벽 공급 업체에 문의하세요.

네트워크 트래픽 흐름의 경우 [작동 방식](#how-it-works) 섹션 아래의 시각적 다이어그램을 참조하세요.

## <a name="create-a-private-link-scope"></a>프라이빗 링크 범위 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 **리소스 만들기** 로 이동하여  **Private Link 범위** 를 검색합니다. 또는 다음 링크를 사용하여 포털에서 [Azure Arc Private Link 범위](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2FprivateLinkScopes) 페이지를 열 수 있습니다.

    :::image type="content" source="./media/private-link-security/find-scope.png" alt-text="프라이빗 링크 범위 찾기" border="true":::

1. **만들기** 를 선택합니다.

1. 구독 및 리소스 그룹을 선택합니다. 미리 보기 중에는 가상 네트워크 및 Azure Arc 지원 서버가 Azure Arc Private Link 범위와 동일한 구독에 있어야 합니다.

1. Azure Arc Private Link 범위의 이름을 지정합니다. 의미 있고 명확한 이름을 사용하는 것이 가장 좋습니다.

   필요에 따라이 Azure Arc 개인 링크 범위 (미리 보기)와 연결 된 모든 Azure Arc 사용 컴퓨터 또는 서버에서 개인 끝점을 통해 데이터를 서비스로 보낼 수 있습니다. **공용 네트워크 액세스 사용** 을 선택하는 경우 이 Azure Arc Private Link 범위(미리 보기)와 연결 된 컴퓨터 또는 서버가 개인 네트워크나 공용 네트워크를 통해 서비스와 통신할 수 있습니다. 사용자의 생각이 바뀌면 범위를 만든 후 이 설정을 변경하면 됩니다.

1. **검토 + 만들기** 를 선택합니다.

    :::image type="content" source="./media/private-link-security/create-private-link-scope.png" alt-text="프라이빗 링크 범위 만들기" border="true":::

1. 유효성 검사를 통과하도록 한 다음, **생성** 을 선택합니다.

## <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기

Azure Arc Private Link 범위(미리 보기)를 만들고 나면 프라이빗 엔드포인트를 사용하여 하나 이상의 가상 네트워크에 연결해야 합니다. 프라이빗 엔드포인트는 가상 네트워크 주소 공간에서 개인 IP의 Azure Arc 서비스에 대한 액세스를 표시합니다.

1. 범위 리소스에서 왼쪽 리소스 메뉴에서 **프라이빗 엔드포인트 연결** 을 클릭합니다. **추가** 를 선택하여 엔드포인트 만들기 프로세스를 시작합니다. 그것들을 선택하고 **승인** 을 클릭하여 여기 프라이빗 링크 센터에서 시작된 연결을 승인할 수도 있습니다.

    :::image type="content" source="./media/private-link-security/create-private-endpoint.png" alt-text="프라이빗 엔드포인트 만들기" border="true":::

1. 구독, 리소스 그룹, 엔드포인트 이름 및 거주하는 지역을 선택합니다. 지역은 연결할 가상 네트워크와 동일한 영역이어야 합니다

1. 완료되면 **다음: 리소스** 를 선택합니다.

1. **리소스** 페이지에서 다음을 수행합니다.

   a. Azure Arc Private Link 리소스가 포함된 **구독** 을 선택합니다.

   b. **리소스 종류** 에서 **Microsoft.HybridCompute/privateLinkScopes** 를 선택합니다.

   다. **리소스** 드롭다운에서 이전에 만든 프라이빗 링크 범위를 선택합니다.

   d. **다음: 구성 >** 을 선택합니다.

    :::image type="content" source="./media/private-link-security/create-private-endpoint-configuration.png" alt-text="프라이빗 엔드포인트 만들기 완료" border="true":::

1. **구성** 페이지에서 다음을 수행합니다.

   a. Azure Monitor 리소스에 연결하려는 **가상 네트워크** 및 **서브넷** 을 선택합니다. 

   b. **프라이빗 DNS 영역과 통합** 에 대해 **예** 를 선택하고, 새 프라이빗 DNS 영역을 자동으로 만들도록 합니다. 실제 DNS 영역은 아래 스크린샷에 표시된 것과 다를 수도 있습니다.

     > [!NOTE]
     > **아니요** 를 선택하고 DNS 레코드를 수동으로 관리하려면, 먼저 이 프라이빗 엔드포인트 및 AMPLS 구성을 포함한 프라이빗 범위 구성을 포함한 프라이빗 링크 설정을 완료합니다. 그런 다음 [Azure 프라이빗 엔드포인트 DNS 구성](../../private-link/private-endpoint-dns.md)의 지침에 따라 DNS를 구성합니다. Private Link 설정을 위한 준비로 빈 레코드를 만들지 않도록 합니다. 만든 DNS 레코드는 기존 설정을 재정의 하 고 Azure Arc 사용 서버와의 연결에 영향을 줄 수 있습니다.

   다.    **검토 + 만들기** 를 선택합니다.

   d.    유효성 검사를 통과하도록 합니다.

   e.    **만들기** 를 선택합니다.

## <a name="configure-on-premises-dns-forwarding"></a>온-프레미스 DNS 전달 구성

온-프레미스 머신 또는 서버는 프라이빗 링크 DNS 레코드를 프라이빗 엔드포인트 IP 주소로 확인할 수 있어야 합니다. 이를 구성하는 방법은 Azure 개인 DNS 영역을 사용하여 DNS 레코드를 유지 관리하는지 또는 사용자 고유의 DNS 서버를 온-프레미스로 사용하는지 여부 및 구성 중인 서버 수에 따라 다릅니다.

### <a name="dns-configuration-using-azure-integrated-private-dns-zones"></a>Azure 통합 개인 DNS 영역을 사용하는 DNS 구성

프라이빗 엔드포인트를 만들 때 Azure Arc 지원 서버 및 게스트 구성에 대한 개인 DNS 영역을 설정하는 경우 온-프레미스 컴퓨터 또는 서버는 프라이빗 엔드포인트 주소를 올바르게 확인하기 위해 DNS 쿼리를 기본 제공 Azure DNS 서버로 전달할 수 있어야 합니다. Azure에는 DNS 전달자가 필요합니다(특수 목적으로 구축된 VM 또는 DNS 프록시가 사용하도록 설정된 Azure Firewall 인스턴스). 그런 다음, 쿼리를 Azure로 전달하도록 온-프레미스 DNS 서버를 구성하여 프라이빗 엔드포인트 IP 주소를 확인할 수 있습니다.

프라이빗 엔드포인트 설명서에서는 [DNS 전달자를 사용하여 온-프레미스 워크로드](../../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)를 구성하기 위한 지침을 제공합니다.

### <a name="manual-dns-server-configuration"></a>수동 DNS 서버 구성

프라이빗 엔드포인트를 만드는 동안 Azure 개인 DNS 영역을 사용하지 않도록 선택한 경우 온-프레미스 DNS 서버에서 필요한 DNS 레코드를 만들어야 합니다.

1. Azure Portal로 이동합니다.

1. 가상 네트워크 및 프라이빗 링크 범위와 연결된 프라이빗 엔드포인트 리소스로 이동합니다.

1. 왼쪽 창에서 **DNS 구성** 을 선택하여 DNS 서버에 설정해야 하는 DNS 레코드 및 해당 IP 주소 목록을 표시합니다. FQDN 및 IP 주소는 프라이빗 엔드포인트에 대해 선택한 지역과 서브넷에서 사용 가능한 IP 주소에 따라 변경됩니다.

    :::image type="content" source="./media/private-link-security/dns-configuration.png" alt-text="DNS 구성 세부 정보" border="true":::

1. DNS 서버 공급업체의 지침에 따라 포털의 테이블과 일치하도록 필요한 DNS 영역 및 A 레코드를 추가합니다. 네트워크에 대해 적절하게 범위가 지정된 DNS 서버를 선택해야 합니다. 이제 이 DNS 서버를 사용하는 모든 컴퓨터 또는 서버가 프라이빗 엔드포인트 IP 주소를 확인하고 Azure Arc Private Link 범위(미리 보기)와 연결되어 있어야 합니다. 확인되지 않으면 연결이 거부됩니다.

### <a name="single-server-scenarios"></a>단일 서버 시나리오

프라이빗 링크를 사용하여 몇 대의 컴퓨터나 서버만 지원하려는 경우 전체 네트워크의 DNS 구성은 업데이트하고 싶지 않을 수 있습니다. 이 경우 프라이빗 엔드포인트 호스트 이름 및 IP 주소를 운영 체제 **호스트** 파일에 추가할 수 있습니다. OS 구성에 따라 호스트 파일은 IP 주소에 대한 호스트 이름을 확인하는 주요 방법이나 대체 방법이 될 수 있습니다.

#### <a name="windows"></a>Windows

1. 관리자 권한이 있는 계정을 사용하여 **C:\Windows\System32\drivers\etc\hosts** 를 엽니다.

1. [수동 DNS 서버 구성](#manual-dns-server-configuration)에서 3단계의 표에 표시된 것처럼 프라이빗 엔드포인트 IP 및 호스트 이름을 추가합니다. 호스트 파일에는 먼저 IP 주소와 공백이 오고 그 뒤에 호스트 이름이 필요합니다.

1. 변경 내용이 있는 파일을 저장합니다. 먼저 다른 디렉터리에 저장한 다음, 원래 경로에 파일을 복사해야 할 수 있습니다.

#### <a name="linux"></a>Linux

1. **sudoers** 권한이 있는 계정을 사용해`sudo nano /etc/hosts`를 실행하여 호스트 파일을 엽니다.

1. [수동 DNS 서버 구성](#manual-dns-server-configuration)에서 3단계의 표에 표시된 것처럼 프라이빗 엔드포인트 IP 및 호스트 이름을 추가합니다. 호스트 파일은 먼저 IP 주소와 공백을 입력한 후 호스트 이름을 입력하도록 요청합니다.

1. 변경 내용이 있는 파일을 저장합니다.

## <a name="connect-to-an-azure-arc-enabled-servers"></a>Azure Arc 지원 서버에 연결

> [!NOTE]
> 개인 끝점을 사용 하는 Azure Arc 연결 컴퓨터 에이전트의 지원 되는 최소 버전은 버전 1.4입니다. 포털에서 생성 된 Azure Arc 사용 서버 배포 스크립트는 최신 버전을 다운로드 합니다.

### <a name="configure-a-new-azure-arc-enabled-server-to-use-private-link"></a>개인 링크를 사용 하도록 새 Azure Arc 사용 서버 구성

컴퓨터 또는 서버를 Azure Arc 지원 서버와 처음 연결하는 경우 필요에 따라 프라이빗 링크 범위에 연결할 수 있습니다. 다음 단계는 아래와 같습니다. 

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

1. **서버 - Azure Arc** 로 이동합니다.

1. **서버 - Azure Arc** 페이지에서 왼쪽 위에 있는 **추가** 를 선택합니다.

1. **Azure Arc를 사용하여 서버 추가** 페이지에서 배포 시나리오에 따라 **단일 서버 추가** 또는 **여러 서버 추가** 를 선택한 후 **스트립트 생성** 을 선택합니다.

1. **스크립트 생성** 페이지에서 머신을 Azure 내에서 관리하려는 구독 및 리소스 그룹을 선택합니다. 머신 메타데이터를 저장할 Azure 위치를 선택합니다. 이 위치는 리소스 그룹의 위치와 같을 수도 있고 다를 수도 있습니다.

1. **필수 구성 요소** 페이지에서 정보를 검토한 후, **다음: 리소스 세부 정보** 를 선택합니다.

1. **리소스 세부 정보** 페이지에서 다음을 제공합니다.

    1. **리소스 그룹** 드롭다운 목록에서 머신을 관리할 리소스 그룹을 선택합니다.
    1. **지역** 드롭다운 목록에서 컴퓨터 또는 서버 메타데이터를 저장할 Azure 지역을 선택합니다.
    1. **운영 체제** 드롭다운 목록에서 스크립트가 실행되도록 구성된 운영 체제를 선택합니다.
    1. **네트워크 연결** 에서 **프라이빗 엔드포인트(미리 보기)** 를 선택하고 1부에서 만든 Azure Arc Private Link 범위를 드롭다운 목록에서 선택합니다.

       :::image type="content" source="./media/private-link-security/arc-enabled-servers-create-script.png" alt-text="프라이빗 엔드포인트 연결 옵션 선택" border="true":::

    1. 완료되면 **다음: 태그** 를 선택합니다.

1. **인증** 페이지에서 **여러 서버 추가를** 선택한 경우 드롭다운 목록에서 Azure Arc 지원 서버에 대해 만든 서비스 주체를 선택합니다. Azure Arc 지원 서버에 대한 서비스 주체를 만들지 않은 경우 먼저 필요한 사용 권한 및 서비스 주체를 만드는 단계를 숙지하기 위해 [서비스 주체를 만드는 방법을](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) 검토합니다. 계속하려면 **다음: 태그** 를 선택합니다.

1. **태그** 페이지에서 제안된 기본 **실제 위치 태그** 를 검토하고 값을 입력하거나 표준을 지원하는 **사용자 지정 태그** 를 하나 이상 지정합니다.

1. 완료되면 **다음: 스크립트 다운로드 및 실행** 을 선택합니다.

1. **스크립트 다운로드 및 실행** 페이지에서 요약 정보를 검토한 다음, **다운로드** 를 선택합니다. 그래도 변경해야 하는 경우 **이전** 을 선택합니다.

스크립트를 다운로드하면 권한 있는(관리자 또는 루트) 계정을 사용하여 컴퓨터 또는 서버에서 스크립트를 실행해야 합니다. 네트워크 구성에 따라 인터넷에 액세스할 수 있는 컴퓨터에서 에이전트를 다운로드하여 컴퓨터나 서버로 전송한 다음, 에이전트 경로로 스크립트를 수정해야 할 수 있습니다. 

Windows 에이전트는 [https://aka.ms/AzureConnectedMachineAgent](https://aka.ms/AzureConnectedMachineAgent)에서 다운로드할 수 있으며 Linux 에이전트는 [https://packages.microsoft.com](https://packages.microsoft.com)에서 다운로드할 수 있습니다. OS 배포 디렉터리에서 최신 버전의 **azcmagent** 를 찾고 로컬 패키지 관리자와 함께 설치합니다. 

스크립트는 완료 후 온보딩이 성공했는지 알려주는 상태 메시지를 반환합니다.

> [!NOTE]
> Linux 서버에 Connected Machine 에이전트를 배포하는 경우 네트워크 연결을 확인하는 동안 5분 정도 지연될 수 있습니다. 그 다음에는 방화벽이 올바르게 구성된 경우에도 `you do not have access to login.windows.net` 오류가 발생합니다. 이는 알려진 문제이며 향후 에이전트 릴리스에서 수정될 예정입니다. 방화벽이 올바르게 구성된 경우 온보딩이 계속해서 성공합니다.

### <a name="configure-an-existing-azure-arc-enabled-server"></a>기존 Azure Arc 지원 서버 구성

프라이빗 링크 범위 이전에 설정된 Azure Arc 지원 서버의 경우 다음 단계를 완료하여 범위 Private Link Azure Arc 지원 서버를 사용하도록 허용할 수 있습니다.

1. Azure Portal에서 Azure Arc Private Link 범위 리소스로 이동합니다.

1. 왼쪽 창에서 **Azure Arc 리소스** 를 선택한 후 **+ 추가** 를 선택합니다.

1. 목록에서 프라이빗 링크 범위와 연결할 서버를 선택한 후 **선택** 을 선택하여 변경 내용을 저장합니다.

    > [!NOTE]
    > 프라이빗 링크 범위와 동일한 구독 및 지역에 있는 Azure Arc 지원 서버만 표시됩니다.

    :::image type="content" source="./media/private-link-security/select-servers-private-link-scope.png" alt-text="Azure Arc 리소스 선택" border="true":::

프라이빗 링크 범위가 최근에 연결된 서버의 연결을 허용하는 데 최대 15분이 소요될 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

1. 온-프레미스 DNS 서버를 확인하여 Azure DNS로 전달 중이거나 프라이빗 링크 영역에서 적절한 A 레코드로 구성되어 있는지 확인합니다. 이러한 조회 명령은 Azure Virtual Network에서 개인 IP 주소를 반환해야 합니다. 공용 IP 주소를 확인하는 경우 컴퓨터 또는 서버와 네트워크의 DNS 구성을 다시 확인합니다.

    nslookup gbl.his.arc.azure.com  nslookup agentserviceapi.guestconfiguration.azure.com

1. 컴퓨터 또는 서버를 온보딩하는 데 문제가 있는 경우 로컬 네트워크 방화벽에 Azure Active Directory 및 Azure Resource Manager 서비스 태그를 추가했는지 확인하세요. 에이전트는 이러한 서비스에 대해 프라이빗 엔드포인트를 사용할 수 있을 때까지 인터넷을 통해 해당 서비스와 통신해야 합니다.

## <a name="next-steps"></a>다음 단계

* 프라이빗 엔드포인트에 대한 자세한 정보는 [Azure 개인 엔드포인트란 무엇인가요?](../../private-link/private-endpoint-overview.md)를 참조하세요.

* Azure 프라이빗 엔드포인트 연결 설정에 문제가 발생하는 경우 [Azure 프라이빗 엔드포인트 연결 문제 해결](../../private-link/troubleshoot-private-endpoint-connectivity.md)을 참조하세요.

* [Azure Automation](../../automation/how-to/private-link-security.md), [Azure Monitor](../../azure-monitor/logs/private-link-security.md), [Azure Key Vault](../../key-vault/general/private-link-service.md) 또는 [Azure Blob storage](../../private-link/tutorial-private-endpoint-storage-portal.md)에 대한 프라이빗 링크 구성에 대한 내용은 다음 항목을 참조하세요.
