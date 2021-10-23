---
title: Azure-SSIS 통합 런타임에 대한 Azure 가상 네트워크 구성
description: Azure-SSIS 통합 런타임에 대한 Azure 가상 네트워크를 구성하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b4274144e8437bd47adad0854fec5c3ed9550ffb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229411"
---
# <a name="virtual-network-configuration-for-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임에 대한 가상 네트워크 구성

다음 요구 사항을 충족하도록 가상 네트워크를 설정합니다. 

- `Microsoft.Batch`가 Azure-SSIS IR을 호스트하는 가상 네트워크 서브넷의 구독에 속한 등록된 공급자인지 확인합니다. 클래식 가상 네트워크를 사용하는 경우 `MicrosoftAzureBatch`도 해당 가상 네트워크의 클래식 가상 머신 기여자 역할에 조인하세요. 

- 필요한 권한이 있는지 확인하세요. 자세한 내용은 [권한 설정](#perms)을 참조하세요.

- Azure-SSIS IR을 호스팅할 적절한 서브넷을 선택합니다. 자세한 내용은 [서브넷 선택](#subnet)을 참조하세요. 

- Azure-SSIS IR의 사용자 자체 공용 IP 주소를 가져오는 경우 [고정 공용 IP 주소 선택](#publicIP)을 참조하세요.

- 가상 네트워크에서 자체 DNS(Domain Name System) 서버를 사용하는 경우 [DNS 서버 설정](#dns_server)을 참조하세요. 

- 서브넷에서 NSG(네트워크 보안 그룹)를 사용하는 경우 [NSG 설정](#nsg)을 참조하세요. 

- Azure ExpressRoute 또는 UDR(사용자 정의 경로)을 사용하는 경우 [Azure ExpressRoute 또는 UDR 사용](#route)을 참조하세요. 

- 가상 네트워크의 리소스 그룹(또는 사용자 자체 공용 IP 주소를 가져오는 경우 공용 IP 주소의 리소스 그룹)이 특정 Azure 네트워크 리소스를 만들고 삭제할 수 있는지 확인합니다. 자세한 내용은 [리소스 그룹 설정](#resource-group)을 참조하세요. 

- [Azure-SSIS IR 사용자 지정 설정](./how-to-configure-azure-ssis-ir-custom-setup.md)에 설명된 대로 Azure-SSIS IR을 사용자 지정하는 경우 해당 노드를 관리하는 내부 프로세스는 미리 정의된 172.16.0.0~172.31.255.255 범위의 개인 IP 주소를 사용합니다. 따라서 가상 또는 온-프레미스 네트워크의 개인 IP 주소 범위가 이 범위와 충돌하지 않는지 확인하세요.

다음은 Azure-SSIS IR에 필요한 연결을 보여 주는 다이어그램입니다.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png" alt-text="Azure-SSIS IR에 필요한 연결을 보여 주는 다이어그램.":::

## <a name="set-up-permissions"></a><a name="perms"></a> 권한 설정

Azure-SSIS IR을 만드는 사용자에게는 다음 권한이 있어야 합니다.

- SSIS IR를 Azure Resource Manager 가상 네트워크에 연결하는 경우 다음 두 가지 옵션이 있습니다.

  - 기본 제공 네트워크 참가자 역할을 사용합니다. 이 역할에는 범위가 필요 이상으로 큰 _Microsoft.Network/\*_ 권한이 제공됩니다.

  - 필요한 _Microsoft.Network/virtualNetworks/\*/join/action_ 권한만 포함하는 사용자 지정 역할을 만듭니다. 또한 Azure-SSIS IR을 Azure Resource Manager 가상 네트워크에 조인하면서 Azure-SSIS IR의 자체 공용 IP 주소를 가져오려는 경우 역할에 _Microsoft.Network/publicIPAddresses/*/join/action_ 권한을 포함하세요.

- SSIS IR을 클래식 가상 네트워크에 연결하는 경우에는 기본 제공 ‘클래식 가상 머신 참가자’ 역할을 사용하는 것이 좋습니다. 그렇지 않은 경우 가상 네트워크 연결 권한이 포함된 사용자 지정 역할을 정의해야 합니다.

## <a name="select-the-subnet"></a><a name="subnet"></a> 서브넷 선택

서브넷을 선택할 때: 

- Azure-SSIS IR을 배포하려면 GatewaySubnet을 선택하지 마세요. GatewaySubnet은 가상 네트워크 게이트웨이 전용입니다. 

- 선택한 서브넷에 Azure-SSIS IR이 사용할 충분한 주소 공간이 있는지 확인합니다. 사용 가능한 IP 주소를 IR 노드 수의 두 배 이상 남겨 둡니다. Azure는 각 서브넷 내의 일부 IP 주소를 예약합니다. 이와 같은 주소는 사용할 수 없습니다. 서브넷의 첫 번째와 마지막 IP 주소는 프로토콜 규칙에 예약되어 있으며, 추가로 3개의 주소가 Azure 서비스에 사용됩니다. 자세한 내용은 [이러한 서브넷 내에서 IP 주소를 사용하는데 제한 사항이 있습니까?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- 다른 Azure 서비스(예: SQL Database Managed Instance, App Service 등)에서 독점 사용되는 서브넷은 사용하지 마세요. 

## <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>고정 공용 IP 주소 선택

Azure-SSIS IR을 가상 네트워크에 조인하는 동안 Azure-SSIS IR의 자체 고정 공용 IP 주소를 가져오려면 다음과 같은 요구 사항을 충족하는지 확인합니다.

- 다른 Azure 리소스에 아직 연결되지 않은 사용하지 않는 주소를 정확히 두 개 제공해야 합니다. 추가 주소 하나는 Azure-SSIS IR을 정기적으로 업그레이드할 때 사용됩니다. 활성 Azure SSIS IR 간에 하나의 공용 IP 주소를 공유할 수 없습니다.

- 둘 다 표준 형식의 고정 주소여야 합니다. 자세한 내용은 [공용 IP 주소의 SKU](../virtual-network/ip-services/public-ip-addresses.md#sku)를 참조하세요.

- 둘 다 DNS 이름이 있어야 합니다. 만들 때 DNS 이름을 제공하지 않은 경우 Azure Portal에서 DNS 이름을 제공할 수 있습니다.

:::image type="content" source="media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png" alt-text="Azure-SSIS IR":::

- 공용 IP 주소와 가상 네트워크는 동일한 구독 및 동일한 지역에 속해야 합니다.

## <a name="set-up-the-dns-server"></a><a name="dns_server"></a> DNS 서버 설정 
Azure-SSIS IR이 조인한 가상 네트워크에서 자체 DNS 서버를 사용하여 비공개 호스트 이름을 확인해야 하는 경우 전역 Azure 호스트 이름(예: 이름이 `<your storage account>.blob.core.windows.net`인 Azure Storage Blob 이름)도 확인할 수 있는지 확인합니다. 

권장되는 방법 중 하나는 다음과 같습니다. 

-   요청을 Azure DNS에 전달하도록 사용자 지정 DNS를 구성합니다. 확인되지 않은 DNS 레코드를 자체 DNS 서버에 있는 Azure 재귀 확인자의 IP 주소(168.63.129.16)에 전달할 수 있습니다. 

자세한 내용은 [자체 DNS 서버를 사용하는 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조하세요. 

> [!NOTE]
> 비공개 호스트 이름에 FQDN(정규화된 도메인 이름)을 사용하세요(예를 들어 `<your_private_server>` 대신 `<your_private_server>.contoso.com`을 사용하세요). 또는 Azure-SSIS IR에서 표준 사용자 지정 설정을 사용하여 자체 DNS 접미사(예: `contoso.com`)를 정규화되지 않은 단일 레이블 도메인 이름에 자동으로 추가하고 DNS 쿼리에 사용하기 전에 FQDN으로 전환할 수 있습니다. [표준 사용자 지정 설정 샘플](./how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples)을 참조하세요. 

## <a name="set-up-an-nsg"></a><a name="nsg"></a> NSG 설정
Azure-SSIS IR에 사용되는 서브넷에 NSG를 구현해야 하는 경우 다음 포트를 통해 인바운드 및 아웃바운드 트래픽을 허용합니다. 

-   **Azure-SSIS IR의 인바운드 요구 사항**

| Direction | 전송 프로토콜 | 원본 | 원본 포트 범위 | 대상 | 대상 포트 범위 | 주석 |
|---|---|---|---|---|---|---|
| 인바운드 | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877(IR을 Resource Manager 가상 네트워크에 조인하는 경우) <br/><br/>10100, 20100, 30100(IR을 클래식 가상 네트워크에 조인하는 경우)| Data Factory 서비스는 해당 포트를 사용하여 가상 네트워크의 Azure-SSIS IR 노드와 통신합니다. <br/><br/> 서브넷 수준 NSG를 만드는지 여부에 관계없이 Data Factory는 Azure-SSIS IR을 호스트하는 가상 머신에 연결된 NIC(네트워크 인터페이스 카드)의 수준에서 항상 NSG를 구성합니다. 지정된 포트에서 Data Factory IP 주소의 인바운드 트래픽만 해당 NIC 수준 NSG에서 허용됩니다. 서브넷 수준에서 인터넷 트래픽에 대해 해당 포트를 여는 경우라도 Data Factory IP 주소가 아닌 IP 주소에서의 트래픽은 NIC 수준에서 차단됩니다. |
| 인바운드 | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (선택 사항) 이 규칙은 Microsoft 지원에서 고객에게 고급 문제 해결을 위해 열도록 요청하고 문제 해결 후 바로 닫을 수 있는 경우에만 필요합니다. **CorpNetSaw** 서비스 태그는 Microsoft 회사 네트워크의 보안 액세스 워크스테이션만 원격 데스크톱을 사용하도록 허용합니다. 또한 이 서비스 태그는 포털에서 선택할 수 없으며 Azure PowerShell 또는 Azure CLI를 통해서만 사용할 수 있습니다. <br/><br/> NIC 수준 NSG에서 포트 3389는 기본적으로 열려 있으며, Microsoft에서는 서브넷 수준 NSG에서 포트 3389를 제어하도록 허용하지만 Azure-SSIS IR은 보호를 위해 각 IR 노드의 Windows 방화벽 규칙에서 기본적으로 포트 3389 아웃바운드를 허용하지 않았습니다. |
||||||||

-   **Azure-SSIS IR의 아웃바운드 요구 사항**

| Direction | 전송 프로토콜 | 원본 | 원본 포트 범위 | 대상 | 대상 포트 범위 | 주석 |
|---|---|---|---|---|---|---|
| 아웃바운드 | TCP | VirtualNetwork | * | AzureCloud | 443 | 가상 네트워크의 Azure-SSIS IR 노드는 이 포트를 사용하여 Azure 서비스(예: Azure Storage, Azure Event Hubs)에 액세스합니다. |
| 아웃바운드 | TCP | VirtualNetwork | * | 인터넷 | 80 | (선택 사항) 가상 네트워크의 Azure-SSIS IR 노드는 이 포트를 사용하여 인터넷에서 인증서 해지 목록을 다운로드합니다. 이 트래픽을 차단하면 IR을 시작할 때 성능이 다운그레이드되고 인증서 사용을 위해 인증서 해지 목록을 확인하는 기능이 손실될 수 있습니다. 대상 범위를 특정 FQDN으로 더 좁히려면 **Azure ExpressRoute 또는 UDR 사용** 을 참조하세요.|
| 아웃바운드 | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | (선택 사항) 이 규칙은 가상 네트워크에 있는 Azure-SSIS IR의 노드가 서버에서 호스트되는 SSISDB에 액세스하는 경우에만 필요합니다. 서버 연결 정책이 **리디렉션** 대신 **프록시** 로 설정된 경우에는 포트 1433만 필요합니다. <br/><br/> 이 아웃바운드 보안 규칙은 가상 네트워크에 있는 SQL Managed Instance에서 호스트되는 SSISDB 또는 프라이빗 엔드포인트로 구성된 SQL Database에는 적용되지 않습니다. |
| 아웃바운드 | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | (선택 사항) 이 규칙은 가상 네트워크에 있는 Azure-SSIS IR의 노드가 가상 네트워크에 있는 SQL Managed Instance가 호스트하는 SSISDB 또는 프라이빗 엔드포인트로 구성된 SQL Database에 액세스하는 경우에만 필요합니다. 서버 연결 정책이 **리디렉션** 대신 **프록시** 로 설정된 경우에는 포트 1433만 필요합니다. |
| 아웃바운드 | TCP | VirtualNetwork | * | 스토리지 | 445 | (선택 사항) 이 규칙은 Azure Files에 저장된 SSIS 패키지를 실행하려는 경우에만 필요합니다. |
||||||||

## <a name="use-azure-expressroute-or-udr"></a><a name="route"></a> Azure ExpressRoute 또는 UDR 사용
Azure-SSIS IR의 아웃바운드 트래픽을 검사하려는 경우 Azure-SSIS IR에서 시작된 트래픽을 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 강제 터널링(BGP 경로인 0.0.0.0/0을 가상 네트워크에 보급)을 통해 온-프레미스 방화벽 어플라이언스로 라우팅하거나 [UDR](../virtual-network/virtual-networks-udr-overview.md)을 통해 NVA(네트워크 가상 어플라이언스)에 방화벽 또는 [Azure Firewall](../firewall/index.yml)로 라우팅할 수 있습니다. 

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png" alt-text="Azure-SSIS IR의 NVA 시나리오":::

전체 시나리오가 작동하게 하려면 다음 작업을 수행해야 합니다.
   -   Azure Batch 관리 서비스와 Azure-SSIS IR 간의 인바운드 트래픽은 방화벽 어플라이언스를 통해 라우팅할 수 없습니다.
   -   방화벽 어플라이언스는 Azure-SSIS IR에 필요한 아웃바운드 트래픽을 허용해야 합니다.

Azure Batch 관리 서비스와 Azure-SSIS IR 간의 인바운드 트래픽은 방화벽 어플라이언스로 라우팅할 수 없습니다. 라우팅하면 비대칭 라우팅 문제로 인해 트래픽이 중단됩니다. 들어온 경로와 동일한 경로로 다시 응답할 수 있도록 인바운드 트래픽의 경로를 정의해야 합니다. 다음 홉 형식을 **인터넷** 으로 지정하여 Azure Batch 관리 서비스와 Azure-SSIS IR 간의 트래픽을 라우팅하는 특정 UDR을 정의할 수 있습니다.

예를 들어 Azure-SSIS IR이 `UK South`에 있고 Azure Firewall을 통과하는 아웃바운드 트래픽을 검사하려는 경우 먼저 [서비스 태그 IP 범위 다운로드 링크](https://www.microsoft.com/download/details.aspx?id=56519)에서 또는 [서비스 태그 검색 API](../virtual-network/service-tags-overview.md#service-tags-on-premises)를 통해 `BatchNodeManagement.UKSouth` 서비스 태그의 IP 범위 목록을 가져옵니다. 그러고 나서 다음 홉 형식이 **가상 어플라이언스** 인 0.0.0.0/0 경로와 함께 다음 홉 형식이 **인터넷** 인 관련 IP 범위 경로의 다음 UDR을 적용합니다.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png" alt-text="Azure Batch UDR 설정":::

> [!NOTE]
> 이 방법은 추가 유지 관리 비용이 발생합니다. 정기적으로 IP 범위를 확인하고 UDR에 새 IP 범위를 추가하여 Azure-SSIS IR 중단을 방지합니다. 서비스 IP에 표시되는 새 IP는 다음 달에 적용되기 때문에 매월 IP 범위를 확인하는 것이 좋습니다. 

UDR 규칙을 더 쉽게 설정하려면 다음 PowerShell 스크립트를 실행하여 Azure Batch 관리 서비스에 대한 UDR 규칙을 추가할 수 있습니다.
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

방화벽 어플라이언스에서 아웃바운드 트래픽을 허용하려면 NSG 아웃바운드 규칙의 요구 사항과 동일한 아래 포트에 대한 아웃바운드 트래픽을 허용해야 합니다.
-   Azure Cloud Services를 대상으로 하는 포트 443.

    Azure Firewall을 사용하는 경우 AzureCloud 서비스 태그를 사용하여 네트워크 규칙을 지정할 수 있습니다. 다른 유형의 방화벽의 경우 대상을 모두 포트 443에 대해 허용하거나 Azure 환경의 유형을 기준으로 아래 FQDN을 허용할 수 있습니다.

    | Azure 환경 | 엔드포인트                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure 공용      | <ul><li><b>Azure Data Factory(관리)</b><ul><li>\*.frontend.clouddatahub.net</li></ul></li><li><b>Azure Storage(관리)</b><ul><li>\*.blob.core.windows.net</li><li>\*.table.core.windows.net</li></ul></li><li><b>Azure Container Registry(사용자 지정 설정)</b><ul><li>\*.azurecr.io</li></ul></li><li><b>이벤트 허브(로깅)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Microsoft 로깅 서비스(내부 사용)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure Data Factory(관리)</b><ul><li>\*.frontend.datamovement.azure.us</li></ul></li><li><b>Azure Storage(관리)</b><ul><li>\*.blob.core.usgovcloudapi.net</li><li>\*.table.core.usgovcloudapi.net</li></ul></li><li><b>Azure Container Registry(사용자 지정 설정)</b><ul><li>\*.azurecr.us</li></ul></li><li><b>이벤트 허브(로깅)</b><ul><li>\*.servicebus.usgovcloudapi.net</li></ul></li><li><b>Microsoft 로깅 서비스(내부 사용)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure 중국 21Vianet     | <ul><li><b>Azure Data Factory(관리)</b><ul><li>\*.frontend.datamovement.azure.cn</li></ul></li><li><b>Azure Storage(관리)</b><ul><li>\*.blob.core.chinacloudapi.cn</li><li>\*.table.core.chinacloudapi.cn</li></ul></li><li><b>Azure Container Registry(사용자 지정 설정)</b><ul><li>\*.azurecr.cn</li></ul></li><li><b>이벤트 허브(로깅)</b><ul><li>\*.servicebus.chinacloudapi.cn</li></ul></li><li><b>Microsoft 로깅 서비스(내부 사용)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Azure Storage, Azure Container Registry, 이벤트 허브의 FQDN의 경우 이러한 엔드포인트에 대한 네트워크 트래픽이 방화벽 어플라이언스로 라우팅되지 않고 Azure 백본 네트워크를 통과하도록 가상 네트워크에 다음 서비스 엔드포인트를 사용하도록 선택할 수도 있습니다.
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   대상이 CRL 다운로드 사이트인 포트 80.

    Azure-SSIS IR 관리를 위해 인증서의 CRL(인증서 해지 목록) 다운로드 사이트로 사용되는 아래 FQDN을 허용해야 합니다.
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    다른 CRL이 있는 인증서를 사용하는 경우 해당 인증서도 포함하는 것이 좋습니다. [인증서 해지 목록](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx)에서 자세히 알아볼 수 있습니다.

    이 트래픽을 허용하지 않으면 Azure-SSIS IR을 시작할 때 성능 다운그레이드가 발생할 수 있으며, 인증서 사용에 대한 인증서 해지 목록을 확인하는 기능이 손실될 수 있습니다. 이는 보안 관점에서 권장되지 않습니다.

-   대상이 Azure SQL Database인 포트 1433, 11000~11999(가상 네트워크의 Azure-SSIS IR 노드가 서버에서 호스트되는 SSISDB에 액세스하는 경우에만 필요).

    azure 방화벽을 사용 하는 경우 azure SQL Service 태그를 사용 하 여 네트워크 규칙을 지정할 수 있습니다. 그렇지 않으면 방화벽 어플라이언스에서 대상을 특정 Azure SQL URL로 허용할 수 있습니다.

-   대상이 Azure Storage인 포트 445(Azure Files에 저장된 SSIS 패키지를 실행하는 경우에만 필요).

    Azure Firewall을 사용하는 경우 Storage 서비스 태그를 사용하여 네트워크 규칙을 지정할 수 있습니다. 지정하지 않으면 방화벽 어플라이언스에서 특정 Azure 파일 공유 URL로 대상을 허용할 수 있습니다.

> [!NOTE]
> Azure SQL 및 Storage의 경우 서브넷에서 가상 네트워크 서비스 엔드포인트를 구성하면 동일한 지역 또는 쌍으로 연결된 지역에 있는 동일한 지역 \ Azure Storage의 Azure-SSIS IR과 Azure SQL 간의 트래픽이 방화벽 어플라이언스 대신 직접 Microsoft Azure 백본 네트워크로 라우팅됩니다.

Azure-SSIS IR의 아웃바운드 트래픽을 검사하는 기능이 필요하지 않은 경우 단순히 경로를 적용하여 모든 트래픽을 다음 홉 형식 **인터넷** 으로 강제 라우팅할 수 있습니다.

-   Azure ExpressRoute 시나리오에서는 Azure-SSIS IR를 호스트하는 서브넷에서 다음 홉 형식이 **인터넷** 인 0.0.0.0/0 경로를 적용할 수 있습니다. 
-   NVA 시나리오에서는 Azure-SSIS IR을 호스트하는 서브넷에 적용된 기존 0.0.0.0/0 경로를 **가상 어플라이언스** 다음 홉 형식에서 **인터넷** 다음 홉 형식으로 수정할 수 있습니다.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png" alt-text="경로 추가":::

> [!NOTE]
> 다음 홉 형식 **인터넷** 으로 경로를 지정하더라도 모든 트래픽이 인터넷을 통해 이동하는 것은 아닙니다. 대상 주소가 Azure 서비스 중 하나의 주소인 한 Azure는 트래픽을 인터넷으로 라우팅하지 않고 Azure의 백본 네트워크를 통해 해당 서비스로 직접 라우팅합니다.

## <a name="set-up-the-resource-group"></a><a name="resource-group"></a> 리소스 그룹 설정

Azure-SSIS IR은 가상 네트워크와 동일한 리소스 그룹에 특정 네트워크 리소스를 만들어야 합니다. 해당 리소스는 다음과 같습니다.
- 이름이 *\<Guid>-azurebatch-cloudserviceloadbalancer* 인 Azure Load Balancer.
- 이름이 *\<Guid>-azurebatch-cloudservicepublicip* 인 Azure 공용 IP 주소.
- 이름이 *\<Guid>-azurebatch-cloudservicenetworksecuritygroup* 인 네트워크 작업 보안 그룹. 

> [!NOTE]
> 이제 Azure-SSIS IR의 자체 고정 공용 IP 주소를 가져올 수 있습니다. 이 시나리오에서는 가상 네트워크 대신 고정 공용 IP 주소와 동일한 리소스 그룹에 Azure 부하 분산 장치 및 네트워크 보안 그룹만 만듭니다.

해당 리소스는 Azure-SSIS IR이 시작될 때 생성됩니다. Azure-SSIS IR이 중지될 때 삭제됩니다. Azure-SSIS IR의 자체 고정 공용 IP 주소를 가져오는 경우 Azure-SSIS IR이 중지될 때 자체 고정 공용 IP 주소가 삭제되지 않습니다. Azure-SSIS IR 중지가 차단되지 않게 하려면 다른 리소스에서 해당 네트워크 리소스를 다시 사용하지 마세요.

가상 네트워크/고정 공용 IP 주소가 속한 리소스 그룹/구독에 리소스 잠금이 없는지 확인합니다. 읽기 전용/삭제 잠금을 구성하는 경우 Azure-SSIS IR 시작 및 중지에 실패하거나 응답이 중지됩니다.

가상 네트워크/고정 공용 IP 주소가 속한 리소스 그룹/구독에 다음 리소스를 만들 수 없도록 하는 Azure Policy 할당이 없는지 확인합니다. 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

구독의 리소스 할당량이 위의 세 네트워크 리소스에 충분한지 확인합니다. 특히 가상 네트워크에서 만든 각 Azure-SSIS IR의 경우 위의 세 네트워크 리소스 각각에 두 개의 무료 할당량을 예약해야 합니다. 추가 할당량 하나는 Azure-SSIS IR을 정기적으로 업그레이드할 때 사용됩니다.

## <a name="faq"></a><a name="faq"></a> FAQ

- 인바운드 연결의 경우 내 Azure-SSIS IR에서 노출되는 공용 IP 주소를 보호하려면 어떻게 해야 하나요? 공용 IP 주소를 제거할 수 있나요?
 
  지금은 Azure-SSIS IR이 가상 네트워크에 조인할 때 공용 IP 주소가 자동으로 생성됩니다. Azure Batch 관리 서비스만 Azure-SSIS IR에 인바운드 연결하는 것을 허용하는 NIC 수준 NSG가 있습니다. 인바운드 보호를 위해 서브넷 수준 NSG를 지정할 수도 있습니다.

  공용 IP 주소를 노출하지 않으려면 가상 네트워크에 Azure-SSIS IR를 조인하는 대신 [자체 호스팅 IR을 Azure-SSIS IR의 프록시로 구성](./self-hosted-integration-runtime-proxy-ssis.md)하는 것이 좋습니다(시나리오에 적용되는 경우).
 
- 내 Azure-SSIS IR의 공용 IP 주소를 데이터 원본에 대 한 방화벽의 allowlist 추가할 수 있나요?

  이제 Azure-SSIS IR의 자체 고정 공용 IP 주소를 가져올 수 있습니다. 이 경우 데이터 원본에 대 한 방화벽의 allowlist에 IP 주소를 추가할 수 있습니다. 시나리오에 따라 Azure-SSIS IR에서의 데이터 액세스를 보호하는 아래와 같은 다른 옵션도 고려할 수 있습니다.

  - 온-프레미스 네트워크에 가상 네트워크를 연결 하 고 Azure-SSIS IR를 가상 네트워크 서브넷에 조인한 후 데이터 원본이 온-프레미스에 있는 경우 해당 서브넷의 개인 IP 주소 범위를 데이터 원본에 대 한 방화벽의 allowlist에 추가할 수 있습니다.
  - 데이터 원본이 가상 네트워크 서비스 엔드포인트를 지원하는 Azure 서비스인 경우 가상 네트워크 서브넷에서 가상 네트워크 서비스 엔드포인트를 구성하고 해당 서브넷에 Azure-SSIS IR을 조인할 수 있습니다. 그런 다음 해당 서브넷을 사용하여 가상 네트워크 규칙을 데이터 원본의 방화벽에 추가할 수 있습니다.
  - 데이터 원본이 Azure 클라우드 서비스가 아닌 경우 UDR을 사용하여 고정 공용 IP 주소를 통해 Azure-SSIS IR에서 NVA/Azure Firewall로 아웃바운드 트래픽을 라우팅할 수 있습니다. 그런 다음 데이터 원본에 대 한 방화벽의 allowlist에 NVA/Azure 방화벽의 고정 공용 IP 주소를 추가할 수 있습니다.
  - 위의 옵션 중 요구 사항을 충족하는 옵션이 없는 경우 [자체 호스팅 IR을 Azure-SSIS IR 프록시로 구성](./self-hosted-integration-runtime-proxy-ssis.md)하는 것을 고려해 보세요. 그런 다음 자체 호스팅 IR을 호스트 하는 컴퓨터의 고정 공용 IP 주소를 데이터 원본에 대 한 방화벽의 allowlist 추가할 수 있습니다.

- Azure-SSIS IR의 자체 고정 공용 IP 주소를 가져오려면 두 개의 고정 공용 주소를 제공해야 하는 이유는 무엇인가요?

  Azure SSIS IR은 정기적으로 자동 업데이트됩니다. 업그레이드하는 동안 새 노드가 만들어지고 이전 노드가 삭제됩니다. 그러나 가동 중지 시간을 방지하기 위해 새 노드가 준비될 때까지 이전 노드가 삭제되지 않습니다. 따라서 이전 노드에 사용되는 첫 번째 고정 공용 IP 주소를 즉시 해제할 수 없고, 새 노드를 만들려면 두 번째 고정 공용 IP 주소가 필요합니다.

- Azure-SSIS IR의 고정 공용 IP 주소를 가져왔지만 여전히 내 데이터 원본에 액세스할 수 없는 이유는 무엇인가요?

  - 두 개의 고정 공용 IP 주소가 데이터 원본에 대 한 방화벽의 allowlist에 추가 되었는지 확인 합니다. Azure-SSIS IR이 업그레이드될 때마다 해당 고정 공용 IP 주소는 사용자가 가져온 두 주소 간에 전환됩니다. Allowlist에이 중 하나만 추가 하면 업그레이드 후 Azure-SSIS IR에 대 한 데이터 액세스가 중단 됩니다.
  - 데이터 원본이 Azure 서비스인 경우 가상 네트워크 서비스 엔드포인트를 사용하여 구성했는지 확인하세요. 해당 하는 경우 데이터 원본에 대 한 Azure-SSIS IR 트래픽을 Azure 서비스에서 관리 하는 개인 IP 주소를 사용 하도록 전환 하 고 사용자 고유의 고정 공용 IP 주소를 데이터 원본에 대 한 방화벽의 allowlist에 추가 하면 적용 되지 않습니다.

## <a name="next-steps"></a>다음 단계
- [가상 네트워크에 Azure SSIS 통합 런타임 조인-개요](join-azure-ssis-integration-runtime-virtual-network.md)
- [Azure Data Factory Studio UI를 사용 하 여 Azure SSIS 통합 런타임을 가상 네트워크에 조인](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure PowerShell를 사용 하 여 Azure SSIS 통합 런타임을 가상 네트워크에 조인](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR에 대한 자세한 내용은 다음 문서를 참조하세요. 
- [Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR 등 IR에 대한 일반적인 개념적 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](./tutorial-deploy-ssis-packages-azure.md). 이 자습서에서는 Azure-SSIS IR을 만드는 단계별 지침을 제공합니다. Azure SQL Database를 사용하여 SSIS 카탈로그를 호스트합니다. 
- [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 더 자세히 설명합니다. 가상 네트워크에서 가상 네트워크 서비스 엔드포인트 또는 SQL Managed Instance와 함께 Azure SQL Database를 사용하여 SSIS 카탈로그를 호스트하는 방법에 대한 지침을 제공합니다. 이 문서는 Azure-SSIS IR을 가상 네트워크에 조인하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR에 대한 정보를 가져오는 방법을 보여 줍니다. 반환된 정보의 상태 설명도 제공합니다. 
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR을 규모 확장하는 방법을 보여줍니다.