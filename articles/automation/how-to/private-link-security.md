---
title: Azure Private Link를 사용하여 네트워크를 Azure Automation에 안전하게 연결
description: Azure Private Link를 사용하여 네트워크를 Azure Automation에 안전하게 연결
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/11/2020
ms.subservice: ''
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cc30139e0fade80da1185c009d162c7e5fc9f9ff
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771070"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>Azure Private Link를 사용하여 네트워크를 Azure Automation에 안전하게 연결

Azure 프라이빗 엔드포인트는 Azure Private Link가 제공하는, 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 Automation 서비스를 VNet으로 효과적으로 가져옵니다. VNet에 있는 머신과 Automation 계정 간의 네트워크 트래픽은 Vnet과 Microsoft 백본 네트워크의 프라이빗 링크를 통해 트래버스하여 퍼블릭 인터넷 노출을 방지합니다.

예를 들어 아웃바운드 인터넷 액세스를 사용하지 않도록 설정한 VNet이 있습니다. 그러나 Automation 계정에 프라이빗 경로로 액세스하고 Hybrid Runbook Worker의 웹후크, 상태 구성 및 Runbook 작업 같은 자동화 기능을 사용하려고 합니다. 또한 사용자가 VNET을 통해서만 Automation 계정에 액세스할 수 있도록 하려고 합니다.  프라이빗 엔드포인트 배포는 해당 목표를 달성합니다.

이 문서에서는 프라이빗 엔드포인트를 사용하는 경우 및 Automation 계정으로 설정하는 방법을 설명합니다.

![Azure Automation에 대한 Private Link의 개념적 개요](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> Azure Automation에 대한 Private Link 지원은 Azure Commercial 및 Azure US Government 클라우드에서만 사용할 수 있습니다.

## <a name="advantages"></a>장점

Private Link를 사용하면 다음을 수행할 수 있습니다.

- 공용 네트워크 액세스를 개방하지 않고 Azure Automation에 비공개로 연결
- 공용 네트워크 액세스를 개방하지 않고 Azure Monitor Log Analytics 작업 영역에 비공개로 연결

    >[!NOTE]
    >Log Analytics 작업 영역에 대한 별도의 프라이빗 엔드포인트는 작업 데이터를 전달하기 위해 Automation 계정이 Log Analytics 작업 영역에 연결되어 있고 업데이트 관리, 변경 내용 추적 및 인벤토리, State Configuration 또는 작업 시간 외 VM 시작/중지 같은 기능을 사용하도록 설정한 경우에 필요합니다. Azure Monitor에 대한 Private Link에 대한 자세한 내용은 [Azure Private Link를 사용하여 네트워크를 Azure Monitor에 안전하게 연결](../../azure-monitor/logs/private-link-security.md)을 참조하세요.

- 권한 있는 개인 네트워크를 통해서만 Automation 데이터에 액세스할 수 있는지 확인합니다.
- 프라이빗 엔드포인트를 통해 연결되는 Azure Automation 리소스를 정의하여 개인 네트워크에서 데이터 반출을 방지합니다.
- ExpressRoute 및 Private Link를 사용하여 온-프레미스 개인 네트워크를 Azure Automation에 안전하게 연결합니다.
- Microsoft Azure 백본 네트워크 내에서 모든 트래픽을 유지합니다.

자세한 내용은 [Private Link의 주요 이점](../../private-link/private-link-overview.md#key-benefits)을 참조하세요.

## <a name="limitations"></a>제한 사항

- 현재 Private Link 구현에서 Automation 계정 클라우드 작업은 프라이빗 엔드포인트를 사용하여 보호되는 Azure 리소스에 액세스할 수 없습니다. 예를 들어 Azure Key Vault, Azure SQL, Azure Storage 계정 등이 있습니다. 이 문제를 해결하려면 [Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md)를 대신 사용합니다.
- Windows 또는 Linux용 [Log Analytics 에이전트](../../azure-monitor/agents/log-analytics-agent.md)의 최신 버전을 사용해야 합니다.
- [Log Analytics 게이트웨이](../../azure-monitor/agents/gateway.md)에서 Private Link를 지원하지 않습니다.

## <a name="how-it-works"></a>작동 방식

Azure Automation Private Link는 하나 이상의 프라이빗 엔드포인트(및 따라서 이들이 포함된 가상 네트워크)를 Automation 계정 리소스에 연결합니다. 해당 엔드포인트는 웹후크를 사용하여 Runbook을 시작하는 머신, Hybrid Runbook Worker 역할을 호스트하는 머신 및 DSC(Desired State Configuration) 노드를 사용합니다.

Automation를 위한 프라이빗 엔드포인트를 만든 후에는 각 퍼블릭 연결 Automation URL이 VNet의 하나의 프라이빗 엔드포인트에 매핑됩니다. 사용자 또는 머신에서 Automation URL에 직접 연결할 수 있습니다.

### <a name="webhook-scenario"></a>웹후크 시나리오

웹후크 URL에 POST를 수행하여 Runbook을 시작할 수 있습니다. 예를 들어 URL은 `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`입니다.

### <a name="hybrid-runbook-worker-scenario"></a>Hybrid Runbook Worker 시나리오

Azure Automation의 사용자 Hybrid Runbook Worker 기능을 사용하면 Azure Arc 지원 서버에 등록된 서버를 포함하여 Azure 또는 비 Azure 머신에서 직접 Runbook을 실행할 수 있습니다. 역할을 호스트하는 머신 또는 서버에서 직접 Runbook을 실행하고 환경의 리소스에 대해 해당 로컬 리소스를 관리할 수 있습니다.

JRDS 엔드포인트는 Hybrid Worker가 Runbook을 시작/중지하고, Runbook을 작업자에 다운로드하고, 작업 로그 스트림을 Automation 서비스로 다시 보내는 데 사용됩니다.JRDS 엔드포인트를 사용하도록 설정한 후 URL은 `https://<automationaccountID>.jrds.<region>.privatelink.azure-automation.net`과 같습니다. 이렇게 하면 Azure Virtual Network에 연결된 Hybrid Worker에서 Runbook을 실행하여 인터넷에 대한 아웃바운드 연결을 열지 않고도 작업을 실행할 수 있습니다.  

> [!NOTE]
>Azure Automation에 대한 Private Link의 현재 구현을 사용하여 Azure 가상 네트워크에 연결된 Hybrid Runbook Worker에 대한 실행 작업만 지원하고 클라우드 작업은 지원하지 않습니다.

## <a name="hybrid-worker-scenario-for-update-management"></a>업데이트 관리에 대한 Hybrid Worker 시나리오  

시스템 Hybrid Runbook Worker는 Windows 및 Linux 머신에 사용자 지정 업데이트를 설치하도록 디자인된 업데이트 관리 기능에서 사용하는 숨겨진 Runbook 집합을 지원합니다. Azure Automation 업데이트 관리를 사용하도록 설정하면 Log Analytics 작업 영역에 연결된 모든 머신이 시스템 Hybrid Runbook Worker로 자동으로 구성됩니다.

업데이트 관리를 이해 및 구성하려면 [업데이트 관리에 대한 정보](../update-management/overview.md)를 검토합니다. 업데이트 관리 기능은 Log Analytics 작업 영역에 종속되므로 작업 영역을 Automation 계정과 연결해야 합니다. Log Analytics 작업 영역은 솔루션이 수집한 데이터를 저장하고 로그 검색 및 보기를 호스트합니다.

업데이트 관리를 위해 구성된 머신이 Private Link 채널을 통해 안전한 방식으로 Automation & Log Analytics 작업 영역에 연결되도록 하려면, Private Link로 구성된 Automation 계정에 연결된 Log Analytics 작업 영역에 대한 Private Link를 사용하도록 설정해야 합니다.

[Log Analytics 구성](../../azure-monitor/logs/private-link-configure.md#configure-access-to-your-resources)에 설명된 단계를 수행하여 Private Link 범위 외부에서 Log Analytics 작업 영역에 연결할 수 있는 방법을 제어할 수 있습니다. **수집을 위해 공용 네트워크 액세스 허용** 을 **아니요** 로 설정하면 연결된 범위 외부의 머신에서 데이터를 이 작업 영역에 업로드할 수 없습니다. **쿼리를 위해 공용 네트워크 액세스 허용** 을 **아니요** 로 설정하면 범위 외부의 머신에서 이 작업 영역의 데이터에 액세스할 수 없습니다.

**DSCAndHybridWorker** 대상 하위 리소스를 사용하여 사용자 & 시스템 Hybrid Worker에 대한 Private Link를 사용하도록 설정합니다.

> [!NOTE]
> 업데이트 관리에서 관리하며 Azure 외부에서 호스트되는 머신은 ExpressRoute 개인 피어링, VPN 터널 및 프라이빗 엔드포인트를 사용하는 피어링 가상 네트워크를 통해 Azure VNet에 연결됩니다.

### <a name="state-configuration-agentsvc-scenario"></a>State Configuration(agentsvc) 시나리오

State Configuration은 Azure 구성 관리 서비스를 제공하는데, 이는 모든 클라우드 또는 온-프레미스 데이터 센터의 노드에 대한 PowerShell DSC(Desired State Configuratioin) 구성을 작성, 관리 및 컴파일합니다.

머신의 에이전트는 DSC 서비스를 사용하여 등록한 다음 서비스 엔드포인트를 사용하여 DSC 구성을 가져옵니다. 에이전트 서비스 엔드포인트는 `https://<automationAccountId>.agentsvc.<region>.azure-automation.net`과 같습니다.

퍼블릭 & 프라이빗 엔드포인트의 URL은 동일하지만 Private Link를 사용하는 경우 개인 IP 주소에 매핑됩니다.

## <a name="planning-based-on-your-network"></a>네트워크 기반 계획 수립

Automation 계정 리소스를 설정하기 전에 네트워크 격리 요구 사항을 고려합니다. 퍼블릭 인터넷에 대한 가상 네트워크 액세스 및 Automation 계정에 대한 액세스 제한(Automation 계정과 통합된 경우 Private Link 그룹 범위를 Azure Monitor 로그로 설정 포함)을 평가합니다. 또한 지원되는 기능이 문제없이 작동하는지 확인하기 위한 계획의 일부로 Automation 서비스 [DNS 레코드](./automation-region-dns-records.md) 검토를 포함합니다.

### <a name="connect-to-a-private-endpoint"></a>프라이빗 엔드포인트에 연결

네트워크를 연결하는 프라이빗 엔드포인트를 만듭니다. [Azure Portal Private Link 센터](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)에서 만들 수 있습니다. PublicNetworkAccess 및 Private Link에 대한 변경 내용이 적용되면 적용되는 데 최대 35분이 걸릴 수 있습니다.

이 섹션에서는 Automation 계정에 대한 프라이빗 엔드포인트를 만듭니다.

1. 화면의 왼쪽 위에서 **리소스 만들기 > 네트워킹 > Private Link 센터** 를 차례로 선택합니다.

2. **Private Link 센터 - 개요** 의 **서비스에 대한 프라이빗 연결 설정** 옵션에서 **시작** 을 선택합니다.

3. **가상 머신 만들기 - 기본** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **myResourceGroup** 을 선택합니다. 이전 섹션에서 만든 것입니다.  |
    | **인스턴스 정보** |  |
    | Name | *PrivateEndpoint* 를 입력합니다. |
    | 지역 | **YourRegion** 을 선택합니다. |
    |||

4. 완료되면 **다음: 리소스** 를 선택합니다.

5. **프라이빗 엔드포인트 만들기 - 리소스** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |연결 방법  | 내 디렉터리의 Azure 리소스에 연결하도록 선택합니다.|
    | Subscription| 구독을 선택합니다. |
    | 리소스 유형 | **Microsoft Automation/automationAccounts** 를 선택합니다. |
    | 리소스 |*myAutomationAccount* 를 선택합니다.|
    |대상 하위 리소스 |시나리오에 따라 *Webhook* 또는 *DSCAndHybridWorker* 를 선택합니다.|
    |||

6. 완료되면 **다음: 구성** 을 선택합니다.

7. **프라이빗 엔드포인트 만들기 - 구성** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |**네트워킹**| |
    | 가상 네트워크| *MyVirtualNetwork* 를 선택합니다. |
    | 서브넷 | *mySubnet* 을 선택합니다. |
    |**프라이빗 DNS 통합**||
    |프라이빗 DNS 영역과 통합 |**예** 를 선택합니다. |
    |프라이빗 DNS 영역 |*(새로운)privatelink.azure-automation.net* 을 선택합니다. |
    |||

8. **검토 + 만들기** 를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

9. **유효성 검사 통과** 메시지가 표시되면 **만들기** 를 선택합니다.

**Private Link 센터** 에서 **프라이빗 엔드포인트** 를 선택하여 프라이빗 링크 리소스를 확인합니다.

![Automation 리소스 프라이빗 링크](./media/private-link-security/private-link-automation-resource.png)

모든 세부 정보를 보려면 리소스를 선택합니다. 이렇게 하면 Automation 계정에 대한 새 프라이빗 엔드포인트를 만들고 가상 네트워크에서 개인 IP를 할당합니다. **연결 상태** 가 **승인됨** 으로 표시됩니다.

마찬가지로, State Configuration(agentsvc)과 Hybrid Runbook Worker 작업 런타임(jrds)에 대한 고유 FQDN(정규화된 도메인 이름)이 만들어집니다. 각 항목에는 VNet과는 별도인 IP가 할당되며 **연결 상태** 는 **승인됨** 으로 표시됩니다.

서비스 소비자에게 Automation 리소스에 대한 Azure RBAC 권한이 있는 경우 자동 승인 방법을 선택할 수 있습니다. 이 경우 요청이 Automation 공급자 리소스에 도달하면 서비스 공급자의 작업이 필요하지 않으며 연결이 자동으로 승인됩니다.

## <a name="set-public-network-access-flags"></a>공용 네트워크 액세스 플래그 설정

모든 퍼블릭 구성을 거부하도록 Automation 계정을 구성하고, 프라이빗 엔드포인트를 통해서만 연결을 허용하여 네트워크 보안을 더욱 향상시킬 수 있습니다. VNet 내에서만 Automation 계정에 대한 액세스를 제한하고 퍼블릭 인터넷에서의 액세스를 허용하지 않으려면 `publicNetworkAccess` 속성을 `$false`로 설정할 수 있습니다.

**공용 네트워크 액세스** 설정을 `$false`으로 설정하면 프라이빗 엔드포인트를 통한 연결만 허용되며, 퍼블릭 엔드포인트를 통한 모든 연결은 권한이 없는 오류 메시지 및 401의 HTTP 상태와 함께 거부됩니다.

다음 PowerShell 스크립트는 `Get` 및 `Set` Automation 계정 수준에서 **공용 네트워크 액세스** 속성을 보여 줍니다.

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

Azure Portal에서 공용 네트워크 액세스 속성을 제어할 수도 있습니다. Automation 계정의 **계정 설정** 섹션의 왼쪽 창에서 **네트워크 격리** 를 선택합니다. 공용 네트워크 액세스 설정이 **아니요** 로 설정된 경우 프라이빗 엔드포인트를 통한 연결만 허용되며 퍼블릭 엔드포인트를 통한 모든 연결은 거부됩니다.

![공용 네트워크 액세스 설정](./media/private-link-security/allow-public-network-access.png)

## <a name="dns-configuration"></a>DNS 구성

연결 문자열의 일부로 FQDN(정규화된 도메인 이름)을 사용하여 프라이빗 링크 리소스에 연결하는 경우 할당된 프라이빗 IP 주소를 확인하도록 DNS 설정을 올바르게 구성하는 것이 중요합니다. 기존 Azure 서비스에는 공용 엔드포인트를 통해 연결할 때 사용할 DNS 구성이 이미 있을 수 있습니다. DNS 구성을 검토하고 프라이빗 엔드포인트를 사용하여 연결하도록 업데이트해야 합니다.

프라이빗 엔드포인트와 연결된 네트워크 인터페이스에는 지정된 프라이빗 링크 리소스에 대해 할당된 프라이빗 IP 주소 및 FQDN을 포함하여 DNS를 구성하는 데 필요한 전체 정보 집합이 포함되어 있습니다.

다음 옵션을 사용하여 프라이빗 엔드포인트에 대한 DNS 설정을 구성할 수 있습니다.

* 호스트 파일 사용(테스트에만 권장). 가상 머신의 호스트 파일을 사용하여 DNS를 먼저 이름 확인에 사용하는 것을 재정의할 수 있습니다. DNS 항목은 다음 예제의 `privatelinkFQDN.jrds.sea.azure-automation.net`과 비슷합니다.

* [프라이빗 DNS 영역](../../dns/private-dns-privatednszone.md) 사용. 프라이빗 DNS 영역을 사용하여 특정 프라이빗 엔드포인트에 대한 DNS 확인을 재정의할 수 있습니다. 프라이빗 DNS 영역을 가상 네트워크에 연결하여 특정 도메인을 확인할 수 있습니다. 가상 머신에서 에이전트가 프라이빗 엔드포인트를 통해 통신할 수 있도록 하려면 `privatelink.azure-automation.net`로 프라이빗 DNS 레코드를 만듭니다. 새 DNS *A* 레코드 매핑을 프라이빗 엔드포인트의 IP에 추가합니다.

* DNS 전달자 사용(선택 사항). DNS 전달자를 사용하여 특정 프라이빗 링크 리소스에 대한 DNS 확인을 재정의할 수 있습니다. [DNS 서버](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)가 가상 네트워크에서 호스트되는 경우 프라이빗 DNS 영역을 사용하여 모든 프라이빗 링크 리소스에 대한 구성을 간소화하는 DNS 전달 규칙을 만들 수 있습니다.

자세한 내용은 [Azure 프라이빗 엔드포인트 DNS 구성](../../private-link/private-endpoint-dns.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

프라이빗 엔드포인트에 대한 자세한 정보는 [Azure 개인 엔드포인트란 무엇인가요?](../../private-link/private-endpoint-overview.md)를 참조하세요.
