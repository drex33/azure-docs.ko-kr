---
title: Azure Migrate Server Migration을 사용하여 ExpressRoute를 통해 데이터 복제
description: Azure ExpressRoute를 사용하여 Azure Migrate Server Migration으로 데이터를 복제합니다.
author: DeSeelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: c16b4a91f297621fa96e0e18f816d77e9f3b4e2a
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112322407"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Azure Migrate을 사용하여 ExpressRoute를 통해 데이터 복제: Server Migration

이 문서에서는 서버를 Azure로 마이그레이션하는 동안 Azure ExpressRoute 회로를 통해 데이터를 복제하도록 [Azure Migrate: 서버 마이그레이션](./migrate-services-overview.md#azure-migrate-server-migration-tool) 도구를 구성하는 방법을 알아봅니다.

## <a name="understand-azure-expressroute-circuits"></a>중복 Azure ExpressRoute 회로

ExpressRoute 회로는 연결 공급자를 통해 온-프레미스 인프라를 Microsoft에 연결합니다. 개인 피어링, Microsoft 피어링 또는 둘 다 사용하도록 ExpressRoute 회로를 구성할 수 있습니다. ExpressRoute를 사용하는 피어링 옵션에 대해 자세히 알아보려면 [ExpressRoute 회로 및 피어링](../expressroute/expressroute-circuit-peerings.md#peeringcompare)을 참조하세요.

Azure Migrate: 서버 마이그레이션 도구를 사용하여 다른 클라우드의 온-프레미스 서버 및 서버를 Azure Virtual Machines로 마이그레이션할 수 있습니다. 이 도구는 Azure 구독의 관리 디스크로 마이그레이션할 서버에서 데이터를 복제하는 지속적인 복제 스트림을 설정합니다. 서버를 마이그레이션할 준비가 되면 Azure에서 복제된 데이터가 서버를 마이그레이션하는 데 사용됩니다.

온-프레미스 서버에서 복제된 데이터를 인터넷 또는 ExpressRoute 연결을 통해 Azure 구독에 보내도록 구성할 수 있습니다. 인터넷을 통해 전송되는 데이터는 암호화된 보안 연결을 사용합니다. 마이그레이션할 서버가 여러 개 있는 경우 데이터 복제에 ExpressRoute를 사용하면 ExpressRoute 회로에서 사용할 수 있는 프로비저닝된 대역폭을 사용하여 보다 효율적으로 마이그레이션할 수 있습니다.

이 문서에서는 다음을 사용하여 데이터를 복제하는 방법을 알아봅니다.
> [!div class="checklist"]
>
> * 개인 피어링을 사용하는 ExpressRoute
> * Microsoft 피어링을 사용하는 ExpressRoute

## <a name="replicate-data-by-using-an-expressroute-circuit-with-private-peering"></a>개인 피어링을 사용하는 ExpressRoute를 통해 데이터 복제

> [!Note]
> 이 문서에서는 [에이전트 없이 VMware 가상 머신을 Azure로 마이그레이션](./tutorial-migrate-vmware.md)하기 위해 개인 피어링 회로를 통해 복제하는 방법을 보여줍니다. [다른 복제 방법](./migrate-services-overview.md#azure-migrate-server-migration-tool)을 사용할 수 있도록 프라이빗 엔드포인트 지원을 사용하려면 [프라이빗 엔드포인트에서 Azure Migrate 사용](./how-to-use-azure-migrate-with-private-endpoints.md)을 참조하세요.
 
VMware 가상 머신을 Azure로 마이그레이션하는 에이전트 없는 방법에서는 Azure Migrate 어플라이언스가 먼저 구독의 스토리지 계정(캐시 스토리지 계정)에 복제 데이터를 업로드합니다. 그러면 Azure Migrate가 복제된 데이터를 캐시 스토리지 계정에서 구독의 복제본 관리 디스크로 이동합니다.

복제에 개인 피어링 회로를 사용하려면 프라이빗 엔드포인트를 만들어 캐시 스토리지 계정에 연결합니다. 프라이빗 엔드포인트는 가상 네트워크에서 하나 이상의 개인 IP 주소를 사용하여 스토리지 계정을 Azure 가상 네트워크에 효과적으로 제공합니다. 프라이빗 엔드포인트를 사용하면 Azure Migrate 어플라이언스에서 ExpressRoute 개인 피어링을 사용하여 캐시 스토리지 계정에 연결할 수 있습니다. 그런 다음, 개인 IP 주소에서 직접 데이터를 전송할 수 있습니다. <br/>

![복제 프로세스를 보여주는 스크린샷](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
> - Azure Migrate 어플라이언스는 복제 데이터 외에도 컨트롤 플레인 작업을 위해 Azure Migrate 서비스와 통신합니다. 이러한 작업에는 오케스트레이션 복제가 포함됩니다. Azure Migrate 어플라이언스와 Azure Migrate 서비스 간의 컨트롤 플레인 통신은 Azure Migrate 서비스의 퍼블릭 엔드포인트에서 인터넷을 통해 계속 발생합니다.
> - 스토리지 계정의 프라이빗 엔드포인트는 Azure Migrate 어플라이언스가 배포된 네트워크에서 액세스할 수 있어야 합니다.
> - 캐시 스토리지 계정의 BLOB 서비스 엔드포인트에 대한 Azure Migrate 어플라이언스의 DNS 쿼리를 캐시 스토리지 계정에 연결된 프라이빗 엔드포인트의 개인 IP 주소로 확인하도록 DNS를 구성해야 합니다.
> - 캐시 스토리지 계정은 공용 엔드포인트에서 액세스할 수 있어야 합니다. Azure Migrate는 캐시 스토리지 계정의 퍼블릭 엔드포인트를 사용하여 스토리지 계정에서 복제본 관리 디스크로 데이터를 이동합니다.

### <a name="prerequisites"></a>사전 요구 사항

프라이빗 엔드포인트를 만들려는 리소스 그룹 및 가상 네트워크에 대한 다음 권한이 필요합니다.

사용 사례 | 사용 권한
--- | --- 
 프라이빗 엔드포인트를 만들고 관리합니다. | Microsoft. Network/privateEndpoint/write/action<br/>Microsoft.Network/privateEndpoint/read/action 
|가상 네트워크 또는 서브넷에 프라이빗 엔드포인트를 연결합니다.<br/>이 권한은 프라이빗 엔드포인트가 생성될 가상 네트워크에서 필요합니다.| Microsoft.Network/virtualNetworks/subnet/join/action <br/> Microsoft.Network/virtualNetworks/join/action
|스토리지 계정에 프라이빗 엔드포인트를 연결합니다. <br/>| Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnectionApproval/action <br/> Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnections/read
|네트워크 인터페이스를 만들고 네트워크 보안 그룹에 참가시킵니다. | Microsoft.Network/networkInterfaces/read <br/> Microsoft.Network/networkInterfaces/subnets/write <br/> Microsoft.Network/networkInterfaces/subnets/read<br/> Microsoft.Network/networkSecurityGroups/join/action(선택 사항)
프라이빗 DNS 영역을 만들고 관리합니다.| 프라이빗 DNS 영역 contributor 역할 <br/> _Or_ <br/> Microsoft.Network/privateDnsZones/A/* <br/> Microsoft.Network/privateDnsZones/write 
Microsoft.Network/privateDnsZones/read <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/write <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/read <br/> Microsoft.Network/privateDnsZones/virtualNetworkLinks/write <br/> Microsoft.Network/privateDnsZones/virtualNetworkLinks/read <br/> Microsoft.Network/virtualNetworks/join/action 

### <a name="identify-the-cache-storage-account"></a>캐시 스토리지 계정 식별

 Azure Migrate는 Azure Migrate 프로젝트에서 가상 머신에 대한 복제(Azure Portal 환경 사용)를 처음으로 구성할 때 캐시 스토리지 계정을 자동으로 만듭니다. 스토리지 계정은 Azure Migrate 프로젝트를 만든 것과 동일한 구독 및 리소스 그룹에 만들어집니다.

스토리지 계정을 만들고 찾으려면 다음을 수행합니다.

1. Azure Portal을 사용하여 하나 이상의 가상 머신을 Azure Migrate 프로젝트에 복제합니다.
1. Azure Migrate 프로젝트의 리소스 그룹으로 이동합니다.
1. 스토리지 계정 이름에 **lsa** 라는 접두사가 붙은 캐시 스토리지 계정을 찾습니다.

   ![앱 리소스 보기를 보여주는 스크린샷](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
> 리소스 그룹에 **lsa** 라는 접두사가 붙은 스토리지 계정이 2개 이상 있는 경우 프로젝트의 복제 VM에 대한 복제 설정 및 대상 구성 메뉴로 이동하여 스토리지 계정을 확인할 수 있습니다.
>
> ![복제 설정 개요를 보여주는 스크린샷](./media/replicate-using-expressroute/storage-account.png)

### <a name="upgrade-the-cache-storage-account-to-general-purpose-v2"></a>캐시 스토리지 계정을 범용 v2로 업그레이드

범용 v2 스토리지 계정에서만 프라이빗 엔드포인트를 만들 수 있습니다. 캐시 스토리지 계정이 범용 v2 스토리지 계정이 아닌 경우 업그레이드합니다.

1. 스토리지 계정으로 이동합니다.
1. **Configuration(구성)** 을 선택합니다.
1. **계정 종류** 에서 **업그레이드** 를 클릭합니다.
1. **업그레이드 확인** 에서 계정 이름을 입력합니다.
1. 페이지의 가장 아래에서 **업그레이드** 를 선택합니다.

   ![스토리지 계정을 업그레이드하는 방법을 보여주는 스크린샷](./media/replicate-using-expressroute/upgrade-storage-account.png) 

### <a name="create-a-private-endpoint-for-the-storage-account"></a>스토리지 계정의 프라이빗 엔드포인트 만들기

1. 스토리지 계정으로 이동하여 왼쪽 메뉴에서 **네트워킹** 을 선택하고 **프라이빗 엔드포인트 연결** 탭을 선택합니다.
1. **+ 프라이빗 엔드포인트** 를 선택합니다.

    1. **프라이빗 엔드포인트 만들기** 창에서 **구독** 및 **리소스 그룹** 을 선택합니다. 프라이빗 엔드포인트의 이름을 입력하고 스토리지 계정 지역을 선택합니다.
     
       ![프라이빗 엔드포인트 구성 창을 보여주는 스크린샷](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    1. **리소스** 탭에서 스토리지 계정이 있는 **구독 이름** 을 입력합니다. **리소스 종류** 로 **Microsoft. Storage/storageAccounts** 를 선택합니다. **리소스** 에서 범용 v2 유형 복제 스토리지 계정의 이름을 입력합니다. **대상 하위 리소스** 로 **BLOB** 을 선택합니다.
     
       ![스토리지 계정 프라이빗 엔드포인트 설정을 보여주는 스크린샷](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    1. **구성** 탭에서 **가상 네트워크** 를 선택하고 스토리지 계정의 프라이빗 엔드포인트를 만들어야 하는 **서브넷** 을 선택합니다.

       > [!Note]
       > 가상 네트워크는 ExpressRoute 게이트웨이 엔드포인트를 포함하거나 ExpressRoute 게이트웨이를 사용하여 가상 네트워크에 연결되어야 합니다.

       **프라이빗 DNS 통합** 섹션에서 **예** 를 선택하고 프라이빗 DNS 영역과 통합합니다. **예** 를 선택하면 선택한 가상 네트워크에 DNS 영역이 자동으로 연결됩니다. 또한 프라이빗 엔드포인트에 대해 만든 새 IP 및 FQDN(정규화된 도메인 이름)의 DNS 확인에 필요한 DNS 레코드가 추가됩니다. [프라이빗 DNS 영역](../dns/private-dns-overview.md)에 대해 자세히 알아보세요.

       ![프라이빗 DNS 영역을 보여주는 스크린샷](./media/replicate-using-expressroute/private-dns-zone.png)

    1. 프라이빗 엔드포인트에 대한 **태그** 를 추가할 수도 있습니다.

    1. 세부 정보 입력을 완료한 후 **검토 + 만들기** 탭을 선택합니다. 유효성 검사가 완료된 후 **만들기** 를 선택하여 프라이빗 엔드포인트를 만듭니다.

> [!Note]
> 프라이빗 엔드포인트를 만든 사용자가 스토리지 계정의 소유자인 경우 프라이빗 엔드포인트가 자동 승인됩니다. 그렇지 않으면 소유자가 프라이빗 엔드포인트를 승인해야만 사용할 수 있습니다.

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>프라이빗 DNS 영역을 만들고 DNS 레코드를 수동으로 추가(선택 사항)

프라이빗 엔드포인트를 만들 때 프라이빗 DNS 영역과 통합하는 옵션을 선택하지 않은 경우 수동으로 프라이빗 DNS 영역을 만들어야 합니다.

> [!Note]
> **예** 를 선택하여 프라이빗 DNS 영역과 통합하는 경우 이 섹션을 건너뛸 수 있습니다.

수동으로 프라이빗 DNS 영역을 만들려면 다음을 수행합니다.

1. **프라이빗 DNS 영역** 을 선택합니다.

    ![프라이빗 DNS 영역을 만드는 방법을 보여주는 스크린샷](./media/replicate-using-expressroute/create-private-dns.png)

    1. **프라이빗 DNS 영역** 페이지에서 **+ 추가** 를 선택하여 새 영역을 만듭니다.
    1. **프라이빗 DNS 영역 만들기** 페이지에서 필요한 세부 정보를 입력합니다. 프라이빗 DNS 영역의 이름을 **_privatelink_.blob.core.windows.net** 으로 입력합니다.
    1. **검토 + 만들기** 탭에서 DNS 영역을 검토하고 만듭니다.

1. 가상 네트워크에 프라이빗 DNS 영역을 연결합니다.

    앞에서 만든 프라이빗 DNS 영역은 프라이빗 엔드포인트가 연결된 가상 네트워크에 연결되어야 합니다.

    1. 이전 단계에서 만든 프라이빗 DNS 영역으로 이동한 후 페이지 왼쪽에 있는 가상 네트워크 링크로 이동합니다. **+추가** 를 선택합니다.
    1. 필수 세부 정보를 입력합니다. **구독** 및 **가상 네트워크** 필드는 프라이빗 엔드포인트가 연결된 가상 네트워크의 해당 세부 정보로 채워야 합니다. 다른 필드는 모두 그대로 둘 수 있습니다.

1. 다음 단계는 DNS 레코드를 DNS 영역에 추가하는 것입니다. 프라이빗 DNS 영역에 있는 스토리지 계정의 FQDN에 대한 항목을 추가합니다.

    1. 프라이빗 DNS 영역으로 이동한 후 페이지 왼쪽에 있는 **개요** 섹션으로 이동합니다. **+ 레코드** 를 선택하여 레코드 추가를 시작합니다.
    1. **레코드 집합 추가** 페이지에서 FQDN 및 개인 IP 항목을 A 형식 레코드로 추가합니다.

> [!Important]
> 원본 환경에서 스토리지 계정의 프라이빗 엔드포인트에 대한 개인 IP 주소를 확인하려면 추가 DNS 설정이 필요할 수 있습니다. 필요한 DNS 구성을 이해하려면 [Azure 프라이빗 엔드포인트 DNS 구성](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)을 참조하세요.  

### <a name="verify-network-connectivity-to-the-storage-account"></a>스토리지 계정에 대한 네트워크 연결 확인

프라이빗 링크 연결의 유효성을 검사하려면 Azure Migrate 어플라이언스를 호스트하는 온-프레미스 VM에서 캐시 스토리지 계정 리소스 엔드포인트의 DNS 확인을 수행하고 개인 IP 주소로 확인되는지 확인합니다.

캐시 스토리지 계정의 DNS 확인을 설명하는 예제입니다. 

- nslookup _storageaccountname_.blob.core.windows.net을 입력합니다. <storage-account-name>을 Azure Migrate에서 생성되는 캐시 스토리지 계정 이름으로 바꿉니다.  

    다음과 같은 메시지가 표시됩니다.  

   ![DNS 확인 예제](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- 스토리지 계정 이름에 대해 개인 IP 주소 10.1.0.5가 반환됩니다. 이 주소는 스토리지 계정의 프라이빗 엔드포인트에 속해야 합니다. 

DNS 확인이 잘못된 경우 다음 단계를 수행합니다.  

- **팁:** 스토리지 계정 FQDN 링크인 _storageaccountname_.blob.core.windows.net과 연결된 개인 IP 주소로 온-프레미스 어플라이언스의 DNS 호스트 파일을 편집하여 원본 환경 DNS 레코드를 수동으로 업데이트할 수 있습니다. 이 옵션은 테스트용으로만 사용하는 것이 좋습니다. 
- 사용자 지정 DNS를 사용하는 경우 사용자 지정 DNS 설정을 검토하고 DNS 구성이 올바른지 유효성을 검사합니다. 지침은 [프라이빗 엔드포인트 개요: DNS 구성](../private-link/private-endpoint-overview.md#dns-configuration)을 참조하세요. 
- Azure 제공 DNS 서버를 사용하는 경우 [추가 문제 해결을 위한](./troubleshoot-network-connectivity.md#validate-the-private-dns-zone) 참조로 이 가이드를 사용합니다.   

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-expressroute-private-peering-connectivity"></a>Azure Migrate 어플라이언스(ExpressRoute 개인 피어링 연결의 경우)에서 프록시 바이패스 규칙 구성 
프록시 바이패스의 경우 다음과 같이 캐시 스토리지 계정에 대한 프록시 바이패스 규칙을 추가할 수 있습니다. 
- _storageaccountname_.blob.core.windows.net.

> [!Important]
>  Azure Migrate는 인터넷 액세스가 필요한 다른 스토리지 계정을 사용하므로 *.blob.core.windows.net을 무시하지 마세요. 이 스토리지 계정, 게이트웨이 스토리지 계정은 복제되는 VM에 관한 상태 정보를 저장하는 데만 사용됩니다. Azure Migrate 프로젝트 리소스 그룹의 스토리지 계정 이름에서 접두사를 _**gwsa**_ 로 식별하여 게이트웨이 스토리지 계정을 찾을 수 있습니다. 

## <a name="replicate-data-by-using-an-expressroute-circuit-with-microsoft-peering"></a>ExpressRoute 회로를 사용하여 Microsoft 피어링을 통해 데이터 복제

Microsoft 피어링 또는 기존 공용 피어링 도메인(새 ExpressRoute 연결에 사용되지 않음)을 사용하여 ExpressRoute 회로를 통해 복제 트래픽을 라우팅할 수 있습니다.

![Microsoft 피어링을 사용한 복제를 보여주는 다이어그램](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

복제 데이터가 Microsoft 피어링 회로를 통해 이동하는 경우에도 컨트롤 플레인을 위한 온-프레미스 사이트의 인터넷 연결과 ExpressRoute를 통해 연결할 수 없는 다른 URL이 필요합니다. 복제 어플라이언스 또는 Hyper-V 호스트는 복제 프로세스를 오케스트레이션 하기 위해 URL에 액세스할 수 있어야 합니다. [VMware 에이전트 없는 마이그레이션](./migrate-appliance.md#public-cloud-urls) 또는 [에이전트 기반 마이그레이션](./migrate-replication-appliance.md)의 두 가지 마이그레이션 시나리오에 따른 URL 요구 사항을 검토하세요. 

 Microsoft 피어링을 통한 복제 데이터 전송의 경우 Azure Storage 엔드포인트의 경로를 보급하도록 경로 필터를 구성합니다. 이는 대상 Azure 지역(마이그레이션할 지역)의 지역 BGP 커뮤니티가 됩니다. Microsoft 피어링을 통해 컨트롤 플레인 트래픽을 라우팅하려면 필요에 따라 다른 퍼블릭 엔드포인트의 경로를 보급하도록 경로 필터를 구성합니다.  

- 원본 Azure 지역에 대한 지역 BGP 커뮤니티(Azure Migrate 프로젝트 영역)
- 대상 Azure 지역에 대한 지역 BGP 커뮤니티(마이그레이션 영역)
- Azure Active Directory에 대한 BGP 커뮤니티(12076:5060)

[경로 필터](../expressroute/how-to-routefilter-portal.md) 및 [ExpressRoute에 대한 BGP 커뮤니티](../expressroute/expressroute-routing.md#bgp) 목록에 대해 자세히 알아보세요.

### <a name="proxy-configuration-for-expressroute-microsoft-peering"></a>ExpressRoute Microsoft 피어링의 프록시 구성

어플라이언스가 인터넷 연결에 프록시를 사용하는 경우 특정 URL에 대한 프록시 바이패스를 구성하여 Microsoft 피어링 회로를 통해 해당 URL을 라우팅해야 할 수 있습니다. 

#### <a name="configure-proxy-bypass-rules-for-expressroute-microsoft-peering-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Azure Migrate 어플라이언스에서 ExpressRoute Microsoft 피어링에 대한 프록시 바이패스 규칙 구성(VMware 에이전트 없는 마이그레이션의 경우)

1. 원격 데스크톱을 통해 Azure Migrate 어플라이언스에 로그인합니다.
2.  메모장을 사용하여 *C:/ProgramData/MicrosoftAzure/Config/appliance.json* 파일을 엽니다.
3. 이 파일에서 `"EnableProxyBypassList": "false"` 줄을 `"EnableProxyBypassList": "true"`로 변경합니다. 변경 내용을 저장하고 어플라이언스를 다시 시작합니다.
4. 다시 시작한 후 어플라이언스 구성 관리자를 열면 웹앱 UI에 프록시 바이패스 옵션이 보입니다. 
5. 복제 트래픽의 경우 “.*.blob.core.windows.net”에 대한 프록시 바이패스 규칙을 구성할 수 있습니다. 필요에 따라 다른 컨트롤 플레인 엔드포인트에 대한 프록시 바이패스 규칙을 구성할 수 있습니다. 이 엔드포인트에는 다음이 필요합니다. 

    - *vault.azure.net
    - .*.servicebus.windows.net
    - .*.discoverysrv.windowsazure.com
    - .*.migration.windowsazure.com
    - .\*.hypervrecoverymanager.windowsazure.com

> [!Note]
> 다음 URL은 ExpressRoute를 통해 액세스할 수 없으며 인터넷 연결이 필요합니다. *.portal.azure.com, *.windows.net, *.msftauth.net, *.msauth.net, *.microsoft.com, *.live.com, *.office.com, *.microsoftonline.com, *.microsoftonline-p.com, *.microsoftazuread-sso.com, management.azure.com, *.services.visualstudio.com*(선택 사항), aka.ms/(선택 사항), download.microsoft.com/download.


#### <a name="configure-proxy-bypass-rules-expressroute-microsoft-peering-on-the-replication-appliance-for-agent-based-migrations"></a>복제 어플라이언스에서 ExpressRoute Microsoft 피어링에 대한 프록시 바이패스 규칙 구성(에이전트 기반 마이그레이션의 경우)

구성 서버 및 프로세스 서버에서 프록시 바이패스 목록을 구성하려면 다음을 수행합니다.

1. [PsExec 도구](/sysinternals/downloads/psexec)를 다운로드하여 시스템 사용자 컨텍스트에 액세스합니다.
2. `psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"` 명령줄을 실행하여 시스템 사용자 컨텍스트에서 Internet Explorer를 엽니다.
3. Internet Explorer에서 프록시 설정을 추가합니다.
4. 복제 트래픽의 경우 “.*.blob.core.windows.net”에 대한 프록시 바이패스 규칙을 구성할 수 있습니다. 필요에 따라 다른 컨트롤 플레인 엔드포인트에 대한 프록시 바이패스 규칙을 구성할 수 있습니다. 이 엔드포인트에는 다음이 필요합니다. 

    - .*.backup.windowsazure.com
    - .\*.hypervrecoverymanager.windowsazure.com

Azure Storage 엔드포인트에 대한 바이패스 규칙은 컨트롤 플레인 통신이 인터넷에 대한 프록시를 통해 이동할 수 있는 동안 복제 트래픽이 ExpressRoute를 통해 이동할 수 있는지 확인합니다. 

> [!Note]
> 다음 URL은 ExpressRoute를 통해 액세스할 수 없으며 인터넷 연결이 필요합니다. *.portal.azure.com, *.windows.net, *.msftauth.net, *.msauth.net, *.microsoft.com, *.live.com, *.office.com, *.microsoftonline.com, *.microsoftonline-p.com, *.microsoftazuread-sso.com, management.azure.com, *.services.visualstudio.com*(선택 사항), aka.ms/(선택 사항), download.microsoft.com/download, dev.mysql.com.

## <a name="next-steps"></a>다음 단계

다음 문서에서 자세한 내용을 알아봅니다.

- [ExpressRoute 회로](../expressroute/expressroute-circuit-peerings.md)
- [ExpressRoute 라우팅 도메인](../expressroute/expressroute-circuit-peerings.md#peeringcompare)
- [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)
