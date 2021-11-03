---
title: Azure-SSIS 통합 런타임의 표준 주입을 위한 가상 네트워크 구성
description: Azure-SSIS 통합 런타임의 표준 주입을 위해 가상 네트워크를 구성하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5ee88df19385041de660da311fe51c66099d15cc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053544"
---
# <a name="standard-virtual-network-injection-method"></a>표준 가상 네트워크 삽입 방법

ADF(Azure Data Factory SSIS)에서 SSIS(SQL Server Integration Services)를 사용하는 경우 Azure-SSIS IR(통합 런타임)을 가상 네트워크에 조인하는 두 가지 방법인 표준 및 express가 있습니다. 표준 방법을 사용하는 경우 다음 요구 사항을 충족하도록 가상 네트워크를 구성해야 합니다.

- *Microsoft.Batch가* Azure-SSIS IR 조인할 가상 네트워크가 있는 Azure 구독의 등록된 리소스 공급자인지 확인합니다. 자세한 지침은 [리소스 공급자로 Azure Batch 등록 섹션을](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch) 참조하세요.

- Azure-SSIS IR 만드는 사용자에게 가상 네트워크/서브넷에 조인하는 데 필요한 RBAC(역할 기반 액세스 제어) 권한이 부여되었는지 확인합니다.  자세한 내용은 아래의 [가상 네트워크 권한 선택 섹션을](#perms) 참조하세요.

- Azure-SSIS IR 조인할 가상 네트워크에서 적절한 서브넷을 선택합니다. 자세한 내용은 아래 [서브넷 선택 섹션을](#subnet) 참조하세요.

특정 시나리오에 따라 필요에 따라 다음을 구성할 수 있습니다.

- Azure-SSIS IR 아웃바운드 트래픽에 대한 BYOIP(고정 공용 IP) 주소를 가져오려면 [아래의 고정 공용 IP 주소 구성 섹션을](#ip) 참조하세요.

- 가상 네트워크에서 자체 DNS(도메인 이름 시스템) 서버를 사용하려면 [아래의 사용자 지정 DNS 서버 구성 섹션을](#dns) 참조하세요.

- NSG(네트워크 보안 그룹)를 사용하여 서브넷에서 인바운드/아웃바운드 트래픽을 제한하려면 [아래의 NSG 구성 섹션을](#nsg) 참조하세요.

- UDR(사용자 정의 경로)을 사용하여 아웃바운드 트래픽을 감사/검사하려면 [아래의 UDR 구성](#udr) 섹션을 참조하세요.

- 가상 네트워크의 리소스 그룹(또는 사용자 자체 공용 IP 주소를 가져오는 경우 공용 IP 주소의 리소스 그룹)이 특정 Azure 네트워크 리소스를 만들고 삭제할 수 있는지 확인합니다. 자세한 내용은 [관련 리소스 그룹 구성을 참조하세요.](#rg) 

- Azure-SSIS IR [대한 사용자 지정 설정](how-to-configure-azure-ssis-ir-custom-setup.md) 문서에 설명된 대로 Azure-SSIS IR 사용자 지정하는 경우 해당 노드를 관리하는 내부 프로세스는 미리 정의된 범위 *172.16.0.0에서* *172.31.255.255까지의* 개인 IP 주소를 사용합니다. 따라서 가상 및 온-프레미스 네트워크의 개인 IP 주소 범위가 이 범위와 충돌하지 않는지 확인하세요.

다음은 Azure-SSIS IR에 필요한 연결을 보여 주는 다이어그램입니다.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png" alt-text="Azure-SSIS IR에 필요한 연결을 보여 주는 다이어그램.":::

## <a name="select-virtual-network-permissions"></a><a name="perms"></a>가상 네트워크 권한 선택

표준 가상 네트워크 주입을 사용하도록 설정하려면 Azure-SSIS IR 만드는 사용자에게 가상 네트워크/서브넷에 조인하는 데 필요한 RBAC 권한이 부여되어야 합니다.

- Azure-SSIS IR Azure Resource Manager 가상 네트워크에 조인하는 경우 두 가지 옵션이 있습니다.

  - 기본 제공 *네트워크 기여자* 역할을 사용합니다. 이 역할에는 범위가 필요 이상으로 큰 _Microsoft.Network/\*_ 권한이 제공됩니다.

  - 필요한 _Microsoft.Network/virtualNetworks/\*/join/action_ 권한만 포함하는 사용자 지정 역할을 만듭니다. 또한 Azure Resource Manager 가상 네트워크에 조인하는 동안 Azure-SSIS IR 고유한 고정 공용 IP 주소를 가져오려면 역할에 _Microsoft.Network/publicIPAddresses/ \* /join/action_ 권한도 포함하세요.

  - 자세한 지침은 [가상 네트워크 권한 부여 섹션을 참조하세요.](azure-ssis-integration-runtime-virtual-network-configuration.md#grantperms)

- 클래식 가상 네트워크에 Azure-SSIS IR 조인하는 경우 기본 제공 *클래식 Virtual Machine 기여자* 역할을 사용하는 것이 좋습니다. 그렇지 않으면 가상 네트워크에 가입할 수 있는 권한이 포함된 사용자 지정 역할을 만들어야 합니다. 또한 기본 제공/사용자 지정 역할에 *MicrosoftAzureBatch를* 할당해야 합니다.

## <a name="select-a-subnet"></a><a name="subnet"></a>서브넷 선택

표준 가상 네트워크 주입을 사용하도록 설정하려면 Azure-SSIS IR 조인할 적절한 서브넷을 선택해야 합니다.

- 가상 네트워크 게이트웨이 전용이므로 GatewaySubnet을 선택하지 마세요.

- 선택한 서브넷에 Azure-SSIS IR 노드 번호의 두 배 이상 사용 가능한 IP 주소가 있는지 확인합니다. 이는 Azure-SSIS IR 대한 패치/업그레이드를 롤아웃할 때 중단을 방지하기 위해 필요합니다. 또한 Azure는 각 서브넷에서 사용할 수 없는 일부 IP 주소를 예약합니다. 첫 번째 및 마지막 IP 주소는 프로토콜 준수를 위해 예약된 반면, 세 개의 주소는 Azure 서비스에 예약되어 있습니다. 자세한 내용은 [서브넷 IP 주소 제한 섹션을 참조하세요.](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

- 다른 Azure 서비스(예: Azure SQL Managed Instance, App Service 등)에서 단독으로 사용하는 서브넷을 사용하지 마세요. 

## <a name="configure-static-public-ip-addresses"></a><a name="ip"></a>고정 공용 IP 주소 구성

가상 네트워크에 조인하는 동안 Azure-SSIS IR 아웃바운드 트래픽에 대해 고유한 고정 공용 IP 주소를 가져오려는 경우 방화벽에서 허용하려면 다음 요구 사항을 충족해야 합니다.

- 다른 Azure 리소스에 아직 연결되지 않은 사용하지 않는 주소를 정확히 두 개 제공해야 합니다. 추가 주소 하나는 Azure-SSIS IR을 정기적으로 업그레이드할 때 사용됩니다. 하나의 공용 IP 주소는 활성 Azure-SSIS RS 간에 공유할 수 없습니다.

- 둘 다 표준 형식의 고정 주소여야 합니다. 자세한 내용은 [공용 IP 주소의 S SKU](../virtual-network/ip-services/public-ip-addresses.md#sku) 섹션을 참조하세요.

- 둘 다 DNS 이름이 있어야 합니다. DNS 이름을 만들 때 DNS 이름을 제공하지 않은 경우 Azure Portal 지정할 수 있습니다.

  :::image type="content" source="media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png" alt-text="Azure-SSIS IR":::

- 공용 IP 주소와 가상 네트워크는 동일한 구독 및 동일한 지역에 속해야 합니다.

## <a name="configure-a-custom-dns-server"></a><a name="dns"></a>사용자 지정 DNS 서버 구성 

가상 네트워크에서 자체 DNS 서버를 사용하여 프라이빗 호스트 이름을 확인하려면 전역 Azure 호스트 이름(예: Azure Blob Storage)도 확인할 수 있는지 `<your storage account>.blob.core.windows` 확인합니다.

확인되지 않은 DNS 요청을 Azure 재귀 확인자의 IP *주소(168.63.129.16)로* 전달하도록 자체 DNS 서버를 구성하는 것이 좋습니다.

자세한 내용은 DNS 서버 이름 확인 섹션을 [참조하세요.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

> [!NOTE]
> 프라이빗 호스트 이름에 FQDN(정규화된 도메인 이름)을 사용하세요(예: `<your_private_server>.contoso.com` 대신 `<your_private_server>` 사용). 또는 Azure-SSIS IR 표준 사용자 지정 설정을 사용하여 정규화되지 않은 단일 레이블 도메인 이름에 자체 DNS 접미사(예: )를 자동으로 추가한 다음 `contoso.com` DNS 쿼리에서 사용하기 전에 FQDN으로 변환할 수 있습니다. [표준 사용자 지정 설정 샘플](how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples) 섹션을 참조하세요. 

## <a name="configure-an-nsg"></a><a name="nsg"></a>NSG 구성

Azure-SSIS IR 조인된 서브넷에서 NSG를 사용하려면 다음 인바운드 및 아웃바운드 트래픽을 허용합니다.

| 방향 | 전송 프로토콜 | 원본 | 원본 포트 | 대상 | 대상 포트 | 주석 | 
|-----------|--------------------|--------|--------------|-------------|-------------------|----------| 
| 인바운드 | TCP | *BatchNodeManagement* | * | *VirtualNetwork* | *29876, 29877(SSIS* IR을 Azure Resource Manager 가상 네트워크에 조인하는 경우)<br/><br/>*10100, 20100, 30100(클래식* 가상 네트워크에 SSIS IR을 조인하는 경우)| Data Factory 서비스는 이러한 포트를 사용하여 가상 네트워크의 Azure-SSIS IR 노드와 통신합니다.<br/><br/>서브넷에서 NSG를 만들지 여부에 관계없이 Data Factory 항상 Azure-SSIS IR 호스트하는 가상 머신에 연결된 NIC(네트워크 인터페이스 카드)에서 NSG를 구성합니다.<br/><br/>지정된 포트에서 Data Factory IP 주소의 인바운드 트래픽만 NIC 수준 NSG에서 허용됩니다.<br/><br/>서브넷 수준에서 인터넷 트래픽에 대해 이러한 포트를 여는 경우에도 IP 주소에 Data Factory 않는 IP 주소의 트래픽은 여전히 NIC 수준에서 차단됩니다. | 
| 인바운드 | TCP | *CorpNetSaw* | * | *VirtualNetwork* | *3389* | (선택 사항) Microsoft 지원 엔지니어가 고급 문제 해결을 위해 포트 *3389를* 열도록 요청하는 경우에만 필요하며 문제 해결 후 바로 닫을 수 있습니다.<br/><br/>*CorpNetSaw* 서비스 태그는 Microsoft 회사 네트워크의 SAW(보안 액세스 워크스테이션) 컴퓨터만 RDP(원격 데스크톱 프로토콜)를 통해 Azure-SSIS IR 액세스하도록 허용합니다.<br/><br/>이 서비스 태그는 Azure Portal 선택할 수 없으며 Azure PowerShell/CLI를 통해서만 사용할 수 있습니다.<br/><br/>NIC 수준 NSG에서 포트 *3389는* 기본적으로 열려 있지만 서브넷 수준 NSG를 사용하여 제어할 수 있지만, 해당 포트의 아웃바운드 트래픽은 기본적으로 Windows 방화벽 규칙을 사용하여 Azure-SSIS IR 노드에서 허용되지 않습니다. | 

| 방향 | 전송 프로토콜 | 원본 | 원본 포트 | 대상 | 대상 포트 | 주석 |
|-----------|--------------------|--------|--------------|-------------|-------------------|----------|
| 아웃바운드 | TCP | *VirtualNetwork* | * | *AzureCloud* | *443* | Azure-SSIS IR Azure Storage 및 Azure Event Hubs 같은 Azure 서비스에 액세스하는 데 필요합니다. | 
| 아웃바운드 | TCP | *VirtualNetwork* | * | *인터넷* | *80* | 필드 Azure-SSIS IR는이 포트를 사용 하 여 인터넷에서 CRL (인증서 해지 목록)을 다운로드 합니다.<br/><br/>이 트래픽을 차단 하는 경우 Azure-SSIS IR를 시작할 때 성능이 저하 되 고 인증서를 사용할 때 Crl을 확인 하는 기능이 손실 될 수 있습니다 .이는 보안 관점에서 권장 되지 않습니다.<br/><br/>특정 Fqdn의 대상 범위를 좁히려면 아래의 **UDRs 구성** 섹션을 참조 하세요. | 
| 아웃바운드 | TCP | *VirtualNetwork* | * | *Sql/VirtualNetwork* | *1433, 11000-11999* | 필드 Azure SQL Database server/Managed Instance를 사용 하 여 SSIS 카탈로그 (SSISDB)를 호스트 하는 경우에만 필요 합니다.<br/><br/>Azure SQL Database 서버/Managed Instance가 공용 끝점/가상 네트워크 서비스 끝점으로 구성 된 경우 *SQL* 서비스 태그를 대상으로 사용 합니다.<br/><br/>Azure SQL Database 서버/Managed Instance 개인 끝점으로 구성 된 경우 *VirtualNetwork* service 태그를 대상으로 사용 합니다.<br/><br/>서버 연결 정책이 *리디렉션* 대신 *프록시* 로 설정 된 경우에는 포트 *1433* 만 필요 합니다. | 
| 아웃바운드 | TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *443* | 필드 Azure Storage blob 컨테이너를 사용 하 여 표준 사용자 지정 설치 스크립트/파일을 저장 하는 경우에만 필요 합니다.<br/><br/>Azure Storage 공용 끝점/가상 네트워크 서비스 끝점을 사용 하 여 구성 된 경우 *Storage* 서비스 태그를 대상으로 사용 합니다.<br/><br/>Azure Storage 개인 끝점으로 구성 된 경우 *VirtualNetwork* service 태그를 대상으로 사용 합니다. | 
| 아웃바운드 | TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *445* | 필드 Azure Files에 액세스 해야 하는 경우에만 필요 합니다.<br/><br/>Azure Storage 공용 끝점/가상 네트워크 서비스 끝점을 사용 하 여 구성 된 경우 *Storage* 서비스 태그를 대상으로 사용 합니다.<br/><br/>Azure Storage 개인 끝점으로 구성 된 경우 *VirtualNetwork* service 태그를 대상으로 사용 합니다. | 

## <a name="configure-udrs"></a><a name="udr"></a>UDRs 구성

Azure-SSIS IR에서 아웃 바운드 트래픽을 감사/검사 하려는 경우 [UDRs (사용자 정의 경로](../virtual-network/virtual-networks-udr-overview.md) )를 사용 하 여 BGP (border gateway protocol) 경로 *0.0.0.0/0* 을 가상 네트워크에, 방화벽으로 구성 된 nva (네트워크 가상 어플라이언스) 또는 [Azure 방화벽](../firewall/overview.md) 서비스에 보급 하는 [azure express](https://azure.microsoft.com/services/expressroute/) 경로 강제 터널링을 통해 온-프레미스 방화벽 어플라이언스로 리디렉션할 수 있습니다.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png" alt-text="Azure-SSIS IR의 NVA 시나리오":::

이러한 작업을 수행 하려면 다음을 확인 해야 합니다.

- Azure Batch management 서비스와 Azure-SSIS IR 간의 트래픽은 방화벽 어플라이언스/서비스로 라우팅되지 않아야 합니다.

- 방화벽 어플라이언스/서비스는 Azure-SSIS IR에 필요한 아웃 바운드 트래픽을 허용 해야 합니다.

Azure Batch management 서비스와 Azure-SSIS IR 간 트래픽이 방화벽 어플라이언스/서비스로 라우팅되는 경우 비대칭 라우팅으로 인해 중단 됩니다. 이 트래픽에 대해 UDRs를 정의 해야 합니다 .이는 들어오는 경로를 통해 이동할 수 있습니다. UDRs를 구성 하 여 다음 홉 유형이 *인터넷* 인 Azure Batch 관리 서비스와 Azure-SSIS IR 간에 트래픽을 라우팅할 수 있습니다.

예를 들어 Azure-SSIS IR *영국 남부* 에 있고 Azure 방화벽을 사용 하 여 아웃 바운드 트래픽을 검사 하려는 경우 먼저 [서비스 태그 ip 범위 다운로드 링크](https://www.microsoft.com/download/details.aspx?id=56519) 또는 [서비스 태그 검색 API](../virtual-network/service-tags-overview.md#service-tags-on-premises)에서 *batchnodemanagement* 서비스 태그의 ip 범위를 가져올 수 있습니다. 그런 다음, 다음 홉 유형을 *인터넷* 으로 사용 하 고, *0.0.0.0/0* 경로를 사용 하 여 다음 홉 유형을 *가상 어플라이언스* 로 사용 하는 관련 IP 범위 경로에 대해 다음 udrs를 구성할 수 있습니다.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png" alt-text="Azure Batch UDR 설정":::

> [!NOTE]
> 이 접근 방식에는 관련 IP 범위를 정기적으로 확인 하 고 새 항목에 대해 UDRs를 추가 하 여 Azure-SSIS IR 중단을 방지 해야 하므로 추가 유지 관리 비용이 발생 합니다. 관련 서비스 태그에 대해 새 IP 범위가 표시 되 면 다른 달이 적용 되는 경우에는 매월 확인 하는 것이 좋습니다. 

다음 PowerShell 스크립트를 실행 하 여 Azure Batch management service에 대 한 UDRs를 추가할 수 있습니다.

```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your route table]"
$RouteTableResourceName = "[resource name of your route table]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start adding UDRs to your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch Azure service tag, please confirm that your location is valid."
}
```

위의 [NSG 구성](#nsg) 섹션에 나오는 지침에 따라 방화벽 어플라이언스/서비스에서 비슷한 규칙을 구현 하 여 Azure-SSIS IR의 아웃 바운드 트래픽을 허용 해야 합니다.

- Azure 방화벽을 사용 하는 경우:
  - *Azurecloud* service 태그를 대상으로 하는 아웃 바운드 TCP 트래픽에 대해 포트 *443* 을 열어야 합니다.

  - Azure SQL Database server/Managed Instance를 사용 하 여 SSISDB를 호스트 하는 경우 *SQL/VirtualNetwork* service 태그를 대상으로 하는 아웃 바운드 TCP 트래픽에 대해 *1433, 11000-11999 포트를* 열어야 합니다.

  - Azure Storage blob 컨테이너를 사용 하 여 표준 사용자 지정 설치 스크립트/파일을 저장 하는 경우 *Storage/VirtualNetwork* service 태그를 대상으로 사용 하는 아웃 바운드 TCP 트래픽에 대해 *443* 포트를 열어야 합니다.

  - Azure Files에 액세스 해야 하는 경우 *Storage/VirtualNetwork* service 태그를 대상으로 하는 아웃 바운드 TCP 트래픽에 대해 *445* 포트를 열어야 합니다.

- 다른 방화벽 어플라이언스/서비스를 사용 하는 경우:
  - *0.0.0.0/0* 또는 다음 Azure 환경 관련 fqdn을 대상으로 하는 아웃 바운드 TCP 트래픽에 대해 포트 *443* 을 열어야 합니다.

    | Azure 환경 | FQDN |
    |-------------------|-------|
    | <b>Azure 퍼블릭</b> | <ul><li><b>Azure Data Factory(관리)</b><ul><li>_\*. frontend.clouddatahub.net_</li></ul></li><li><b>Azure Storage(관리)</b><ul><li>_\*. blob.core.windows.net_</li><li>_\*. table.core.windows.net_</li></ul></li><li><b>Azure Container Registry(사용자 지정 설정)</b><ul><li>_\*. azurecr.io_</li></ul></li><li><b>Event Hubs (로깅)</b><ul><li>_\*.servicebus.windows.net_</li></ul></li><li><b>Microsoft 로깅 서비스(내부 사용)</b><ul><li>_gcs.prod.monitoring.core.windows.net_</li><li>_prod.warmpath.msftcloudes.com_</li><li>_azurewatsonanalysis-prod.core.windows.net_</li></ul></li></ul> |
    | <b>Azure Government</b> | <ul><li><b>Azure Data Factory(관리)</b><ul><li>_\*. frontend.datamovement.azure.us_</li></ul></li><li><b>Azure Storage(관리)</b><ul><li>_\*. blob.core.usgovcloudapi.net_</li><li>_\*. table.core.usgovcloudapi.net_</li></ul></li><li><b>Azure Container Registry(사용자 지정 설정)</b><ul><li>_\*. azurecr.us_</li></ul></li><li><b>Event Hubs (로깅)</b><ul><li>_\*.servicebus.usgovcloudapi.net_</li></ul></li><li><b>Microsoft 로깅 서비스(내부 사용)</b><ul><li>_fairfax.warmpath.usgovcloudapi.net_</li><li>_azurewatsonanalysis.usgovcloudapp.net_</li></ul></li></ul> |
    | <b>Azure China 21Vianet</b> | <ul><li><b>Azure Data Factory(관리)</b><ul><li>_\*. frontend.datamovement.azure.cn_</li></ul></li><li><b>Azure Storage(관리)</b><ul><li>_\*. blob.core.chinacloudapi.cn_</li><li>_\*. table.core.chinacloudapi.cn_</li></ul></li><li><b>Azure Container Registry(사용자 지정 설정)</b><ul><li>_\*. azurecr.cn_</li></ul></li><li><b>Event Hubs (로깅)</b><ul><li>_\*. servicebus.chinacloudapi.cn_</li></ul></li><li><b>Microsoft 로깅 서비스(내부 사용)</b><ul><li>_mooncake.warmpath.chinacloudapi.cn_</li><li>_azurewatsonanalysis.chinacloudapp.cn_</li></ul></li></ul> |

  - Azure SQL Database server/Managed Instance를 사용 하 여 SSISDB를 호스트 하는 경우 *0.0.0.0/0* 또는 Azure SQL Database 서버/Managed Instance FQDN이 있는 아웃 바운드 TCP 트래픽에 대해 *1433, 11000-11999 포트를* 열어야 합니다.

  - Azure Storage blob 컨테이너를 사용 하 여 표준 사용자 지정 설치 스크립트/파일을 저장 하는 경우 *0.0.0.0/0* 또는 Azure Blob Storage FQDN을 대상으로 하는 아웃 바운드 TCP 트래픽에 대해 포트 *443* 을 열어야 합니다.

  - Azure Files 액세스해야 하는 경우 *0.0.0.0/0이* 있는 아웃바운드 TCP 트래픽 또는 대상으로 Azure Files FQDN에 대한 포트 *445를* 열어야 합니다.

- *Microsoft.Storage Microsoft.ContainerRegistry Microsoft.EventHub Microsoft.Sql* 리소스를 각각 서브넷에서 사용하도록 설정하여 Azure Storage/Container Registry/Event Hubs/SQL 가상 네트워크 서비스 엔드포인트를 구성하는 경우 /  /  /  동일한/쌍을 이루는 지역의 Azure-SSIS IR 서비스 간의 모든 트래픽이 방화벽 어플라이언스/서비스 대신 Azure 백본 네트워크로 라우팅됩니다.

- 다음 CRL(인증서 해지 목록) 다운로드 사이트를 대상으로 하여 아웃바운드 TCP 트래픽에 대해 포트 *80을* 열어야 합니다.

  - *crl.microsoft.com:80*
  - *mscrl.microsoft.com:80*
  - *crl3.digicert.com:80*
  - *crl4.digicert.com:80*
  - *ocsp.digicert.com:80*
  - *cacerts.digicert.com:80*
  
  다른 CRL에서 인증서를 사용하는 경우 다운로드 사이트도 대상으로 추가해야 합니다. 자세한 내용은 인증서 [해지 목록](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx) 문서를 참조하세요.

  이 트래픽을 차단하면 Azure-SSIS IR 시작할 때 성능이 저하되고 인증서를 사용할 때 CRL을 확인하는 기능이 손실될 수 있습니다. 이는 보안 관점에서 권장되지 않습니다.

Azure-SSIS IR 아웃바운드 트래픽을 감사/검사할 필요가 없는 경우 UDR을 사용하여 다음 홉 형식의 모든 트래픽을 *인터넷으로* 강제할 수 있습니다.

- Azure ExpressRoute 사용하는 경우 다음 홉 유형을 *인터넷으로* 사용하여 서브넷에서 *0.0.0.0/0* 경로에 대한 UDR을 구성할 수 있습니다. 

- NVA를 사용하는 경우 서브넷에서 *0.0.0.0/0* 경로에 대한 기존 UDR을 수정하여 다음 홉 형식을 *가상 어플라이언스에서* *인터넷으로* 전환할 수 있습니다.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png" alt-text="경로 추가":::

> [!NOTE]
> 다음 홉 유형으로 UDR을 *인터넷으로* 구성했다고 해서 모든 트래픽이 인터넷을 통해 이동하는 것은 아닙니다. 대상 주소가 Azure 서비스 중 하나에 속하는 한 Azure는 인터넷 대신 Azure 백본 네트워크를 통해 모든 트래픽을 해당 주소로 라우팅합니다.

## <a name="configure-the-relevant-resource-group"></a><a name="rg"></a>관련 리소스 그룹 구성

표준 가상 네트워크 주입을 사용하도록 설정하려면 Azure-SSIS IR 가상 네트워크와 동일한 리소스 그룹에 특정 네트워크 리소스를 만들어야 합니다. 해당 리소스는 다음과 같습니다.

- 이름이 _\<Guid\>-azurebatch-cloudserviceloadbalancer_ 인 Azure Load Balancer.
- 이름이 _\<Guid\>-azurebatch-cloudservicepublicip_ 인 Azure 공용 IP 주소.
- _\<Guid\> 이름이 -azurebatch-cloudservicenetworksecuritygroup_ 인 NSG입니다. 

> [!NOTE]
> 이제 Azure-SSIS IR의 자체 고정 공용 IP 주소를 가져올 수 있습니다. 이 시나리오에서는 가상 네트워크 대신 고정 공용 IP 주소와 동일한 리소스 그룹에 Azure Load Balancer 및 NSG를 만듭니다.

이러한 리소스는 Azure-SSIS IR 시작할 때 만들어집니다. Azure-SSIS IR이 중지될 때 삭제됩니다. Azure-SSIS IR 대한 사용자 고유의 고정 공용 IP 주소를 가져오는 경우 Azure-SSIS IR 중지될 때 삭제되지 않습니다. Azure-SSIS IR 중지하는 것을 방지하려면 이러한 리소스를 다른 용도로 다시 사용하지 마세요.

가상 네트워크/고정 공용 IP 주소가 속한 리소스 그룹/구독에 리소스 잠금이 없는지 확인합니다. 읽기 전용/삭제 잠금을 구성하는 경우 Azure-SSIS IR 시작 및 중지에 실패하거나 응답이 중지됩니다.

가상 네트워크/고정 공용 IP 주소가 속한 리소스 그룹/구독에서 다음 리소스가 생성되지 않도록 하는 Azure Policy 할당이 없는지 확인합니다. 

- *Microsoft.Network/LoadBalancers* 
- *Microsoft.Network/NetworkSecurityGroups* 
- *Microsoft.Network/PublicIPAddresses* 

구독에 대한 리소스 할당량이 이러한 리소스에 충분한지 확인합니다. 특히 가상 네트워크에서 만든 각 Azure-SSIS IR 대해 Azure-SSIS IR 주기적으로 업그레이드할 때 추가 리소스가 사용되므로 이러한 리소스의 두 배 수를 예약해야 합니다.

## <a name="faq"></a><a name="faq"></a>FAQ

- 인바운드 연결의 경우 내 Azure-SSIS IR에서 노출되는 공용 IP 주소를 보호하려면 어떻게 해야 하나요? 공용 IP 주소를 제거할 수 있나요?
 
  지금은 Azure-SSIS IR이 가상 네트워크에 조인할 때 공용 IP 주소가 자동으로 생성됩니다. Azure Batch 관리 서비스만 Azure-SSIS IR 인바운드 연결하도록 허용하는 NIC 수준 NSG가 있습니다. 인바운드 보호를 위해 서브넷 수준 NSG를 지정할 수도 있습니다.

  공용 IP 주소를 노출하지 않으려면 Azure-SSIS IR 가상 네트워크에 조인하는 대신 [자체 호스팅 IR을 Azure-SSIS IR 프록시로 구성하는](self-hosted-integration-runtime-proxy-ssis.md) 것이 좋습니다.
 
- 내 Azure-SSIS IR 공용 IP 주소를 내 데이터 원본에 대한 방화벽의 허용 목록에 추가할 수 있나요?

  이제 Azure-SSIS IR의 자체 고정 공용 IP 주소를 가져올 수 있습니다. 이 경우 데이터 원본에 대한 방화벽의 허용 목록에 IP 주소를 추가할 수 있습니다. 또는 시나리오에 따라 Azure-SSIS IR 데이터 액세스를 보호하기 위해 아래의 다른 옵션을 고려할 수도 있습니다.

  - 데이터 원본이 온-프레미스에 있는 경우 가상 네트워크를 온-프레미스 네트워크에 연결하고 Azure-SSIS IR 가상 네트워크 서브넷에 조인한 후 해당 서브넷의 개인 IP 주소 범위를 방화벽의 데이터 원본 허용 목록에 추가할 수 있습니다.

  - 데이터 원본이 가상 네트워크 서비스 엔드포인트를 지원하는 Azure 서비스인 경우 가상 네트워크 서브넷에서 가상 네트워크 서비스 엔드포인트를 구성하고 Azure-SSIS IR 해당 서브넷에 조인할 수 있습니다. 그런 다음 해당 서브넷을 사용하여 가상 네트워크 규칙을 데이터 원본의 방화벽에 추가할 수 있습니다.

  - 데이터 원본이 비 Azure 클라우드 서비스인 경우 UDR을 사용하여 NVA/Azure Firewall 통해 Azure-SSIS IR 아웃바운드 트래픽을 고정 공용 IP 주소로 라우팅할 수 있습니다. 그런 다음 NVA/Azure Firewall 고정 공용 IP 주소를 데이터 원본에 대한 방화벽의 허용 목록에 추가할 수 있습니다.

  - 위의 옵션 중 요구 사항을 충족하는 옵션이 없는 경우 [자체 호스팅 IR을 Azure-SSIS IR 프록시로 구성](self-hosted-integration-runtime-proxy-ssis.md)하는 것을 고려해 보세요. 그런 다음 자체 호스팅 IR을 호스트하는 컴퓨터의 고정 공용 IP 주소를 데이터 원본에 대한 방화벽의 허용 목록에 추가할 수 있습니다.

- Azure-SSIS IR의 자체 고정 공용 IP 주소를 가져오려면 두 개의 고정 공용 주소를 제공해야 하는 이유는 무엇인가요?

  Azure SSIS IR은 정기적으로 자동 업데이트됩니다. 업그레이드하는 동안 새 노드가 만들어지고 이전 노드가 삭제됩니다. 그러나 가동 중지 시간을 방지하기 위해 새 노드가 준비될 때까지 이전 노드가 삭제되지 않습니다. 따라서 이전 노드에 사용되는 첫 번째 고정 공용 IP 주소를 즉시 해제할 수 없고, 새 노드를 만들려면 두 번째 고정 공용 IP 주소가 필요합니다.

- Azure-SSIS IR 대한 고정 공용 IP 주소를 가져왔지만 여전히 내 데이터 원본에 액세스할 수 없는 이유는 무엇인가요?

  두 개의 고정 공용 IP 주소가 모두 데이터 원본에 대한 방화벽의 허용 목록에 추가되어 있는지 확인하세요. Azure-SSIS IR 업그레이드될 때마다 고정 공용 IP 주소가 가져온 두 주소 간에 전환됩니다. 이 중 하나만 허용 목록에 추가하면 업그레이드 후 Azure-SSIS IR 대한 데이터 액세스가 끊어지게 됩니다.

  데이터 원본이 Azure 서비스인 경우 가상 네트워크 서비스 엔드포인트를 사용하여 구성했는지 확인하세요. 이 경우 Azure-SSIS IR 데이터 원본으로의 트래픽은 Azure 서비스에서 관리하는 개인 IP 주소를 사용하도록 전환되며, 사용자 고유의 고정 공용 IP 주소를 방화벽의 데이터 원본 허용 목록에 추가해도 적용되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [ADF UI를 통해 가상 네트워크에 Azure-SSIS IR 조인](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure PowerShell 통해 가상 네트워크에 Azure-SSIS IR 조인](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR에 대한 자세한 내용은 다음 문서를 참조하세요. 

- [Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR 등 IR에 대한 일반적인 개념적 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](tutorial-deploy-ssis-packages-azure.md). 이 자습서에서는 Azure-SSIS IR을 만드는 단계별 지침을 제공합니다. Azure SQL Database 서버를 사용하여 SSISDB를 호스트합니다. 
- [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 더 자세히 설명합니다. 가상 네트워크 서비스 엔드포인트/IP 방화벽 규칙/프라이빗 엔드포인트로 구성된 Azure SQL Database 서버 또는 가상 네트워크를 조인하여 SSISDB를 호스트하는 Azure SQL Managed Instance 사용하는 방법에 대한 지침을 제공합니다. 가상 네트워크에 Azure-SSIS IR 조인하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure SSIS IR에 대한 정보를 가져와 파악하는 방법을 설명합니다.
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법도 설명합니다.
