---
title: Azure ExpressRoute용 연결 모니터 구성
description: Azure ExpressRoute 회로에 대한 클라우드 기반 네트워크 연결 모니터링을 구성합니다. 여기서는 ExpressRoute 프라이빗 피어링 및 Microsoft 피어링에 대한 모니터링을 다룹니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/28/2021
ms.author: duau
ms.openlocfilehash: 794e841d8d50683374c86301ec55bc4f6ab53ec6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536539"
---
# <a name="configure-connection-monitor-for-expressroute"></a>ExpressRoute용 연결 모니터 구성

이 문서에서는 ExpressRoute를 모니터링하도록 연결 모니터 확장을 구성하는 방법을 설명합니다. 연결 모니터는 Azure 클라우드 배포와 온-프레미스 위치(지점 등) 간 연결을 모니터링하는 클라우드 기반 네트워크 모니터링 솔루션입니다. 연결 모니터는 Azure Monitor 로그의 일부입니다.  확장을 사용하여 프라이빗 및 Microsoft 피어링 연결의 네트워크 연결을 모니터링할 수도 있습니다. ExpressRoute용 연결 모니터를 구성하면 네트워크 문제를 검색하여 식별하고 제거할 수 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

ExpressRoute용 연결 모니터를 사용하여 다음을 수행할 수 있습니다.

* 다양한 VNet에서 손실과 대기 시간을 모니터링하고 경고를 설정합니다.

* 네트워크의 모든 경로(중복 경로 포함)를 모니터링합니다.

* 복제하기 어려운 일시적 및 특정 시점의 네트워크 문제를 해결합니다.

* 성능 저하를 일으키는 네트워크의 특정 세그먼트를 확인합니다.

## <a name="workflow"></a><a name="workflow"></a> 워크플로

모니터링 에이전트는 온-프레미스 및 Azure의 여러 서버에 설치됩니다. 에이전트는 TCP 핸드셰이크 패킷을 전송하여 서로 통신합니다. 에이전트 간 통신을 위해 Azure는 트래픽에 적용될 수 있는 네트워크 토폴로지 및 경로를 매핑할 수 있습니다.

1. Log Analytics 작업 영역을 만듭니다.

1. 소프트웨어 에이전트를 설치 및 구성합니다. Microsoft 피어링을 통해서만 모니터링하려는 경우에는 소프트웨어 에이전트를 설치하고 구성할 필요가 없습니다.

    * 온-프레미스 서버와 Azure VM에서 모니터링 에이전트를 설치합니다(프라이빗 피어링의 경우).
    * 모니터링 에이전트가 통신할 수 있도록 모니터링 에이전트 서버의 설정을 구성합니다. (방화벽 포트 등을 엽니다.)

1. Azure VM에 설치된 모니터링 에이전트가 온-프레미스 모니터링 에이전트와 통신하도록 NSG(네트워크 보안 그룹) 규칙을 구성합니다.

1. 구독에서 Network Watcher를 사용하도록 설정합니다.

1. 모니터링 설정: 테스트 그룹으로 연결 모니터를 만들어 네트워크 전체의 원본 및 대상 엔드포인트를 모니터링합니다.

이미 네트워크 성능 모니터(사용되지 않음) 또는 연결 모니터를 사용하여 다른 개체나 서비스를 모니터링하며, 지원되는 지역 중 하나에 기존 Log Analytics 작업 영역이 있는 경우 1단계와 2단계를 건너뛰고 3단계에서 구성을 시작할 수 있습니다.

## <a name="create-a-workspace"></a><a name="configure"></a> 작업 영역 만들기

ExpressRoute 회로에 대한 VNets 링크가 있는 구독에 작업 영역을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. ExpressRoute 회로에 연결된 가상 네트워크가 있는 구독에서 **+ 리소스 만들기** 를 선택합니다. ‘Log Analytics 작업 영역’을 검색하고 **만들기** 를 선택합니다.

   >[!NOTE]
   >새 작업 영역을 만들거나 기존 작업 영역을 사용할 수 있습니다. 기존 작업 영역을 사용하려면 작업 영역이 새 쿼리 언어로 마이그레이션되었는지 확인해야 합니다. [자세한 정보...](../azure-monitor/logs/log-query-overview.md)
   >

    :::image type="content" source="./media/how-to-configure-connection-monitor/search-log-analytics.png" alt-text="리소스 만들기의 Log Analytics 검색 스크린샷":::

1. 다음 정보를 입력하거나 선택하여 작업 영역을 만듭니다.  

    | 설정 | 값 |
    | -------- | ----- |
    | 구독 | ExpressRoute 회로가 있는 구독을 선택합니다. |
    | 리소스 그룹 | 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다. |
    | Name | 이 작업 영역을 식별하는 이름을 입력합니다. |
    | 지역 | 이 작업 영역을 만들 지역을 선택합니다. |

    :::image type="content" source="./media/how-to-configure-connection-monitor/create-workspace-basic.png" alt-text="Log Analytics 작업 영역 만들기의 기본 탭 스크린샷":::

     >[!NOTE]
     >ExpressRoute 회로는 전 세계 어디에나 있을 수 있습니다. 작업 영역과 동일한 지역에 있지 않아도 됩니다.
     >

1. **검토 + 만들기** 를 선택하여 유효성을 검사한 후 **만들기** 를 선택하여 작업 영역을 배포합니다. 작업 영역이 배포되면 다음 섹션으로 이동하여 모니터링 솔루션을 구성합니다.

## <a name="configure-monitoring-solution"></a><a name="npm"></a>모니터링 솔루션 구성

*$SubscriptionId*, *$location*, *$resourceGroup*, *$workspaceName* 값을 바꿔서 아래 Azure PowerShell 스크립트를 완성합니다. 그런 다음, 스크립트를 실행하여 모니터링 솔루션을 구성합니다.

```azurepowershell-interactive
$subscriptionId = "Subscription ID should come here"
Select-AzSubscription -SubscriptionId $subscriptionId

$location = "Workspace location should come here"
$resourceGroup = "Resource group name should come here"
$workspaceName = "Workspace name should come here"

$solution = @{
    Location          = $location
    Properties        = @{
        workspaceResourceId = "/subscriptions/$($subscriptionId)/resourcegroups/$($resourceGroup)/providers/Microsoft.OperationalInsights/workspaces/$($workspaceName)"
    }
    Plan              = @{
        Name          = "NetworkMonitoring($($workspaceName))" 
        Publisher     = "Microsoft"
        Product       = "OMSGallery/NetworkMonitoring"
        PromotionCode = ""
    }
    ResourceName      = "NetworkMonitoring($($workspaceName))" 
    ResourceType      = "Microsoft.OperationsManagement/solutions" 
    ResourceGroupName = $resourceGroup
}

New-AzureRmResource @solution -Force
```

모니터링 솔루션을 구성한 후에는 다음을 수행합니다. 서버에 모니터링 에이전트를 설치하고 구성하는 다음 단계를 계속 진행합니다.

## <a name="install-and-configure-agents-on-premises"></a><a name="agents"></a>온-프레미스에 에이전트 설치 및 구성

### <a name="download-the-agent-setup-file"></a><a name="download"></a>에이전트 설치 파일을 다운로드합니다

1. **Log Analytics 작업 영역** 으로 이동한 다음, ‘설정’에서 **에이전트 관리** 를 선택합니다. 머신의 운영 체제에 해당하는 에이전트를 다운로드합니다.

    :::image type="content" source="./media/how-to-configure-connection-monitor/download-agent.png" alt-text="작업 영역의 에이전트 관리 페이지 스크린샷":::

1. 이제 **작업 영역 ID** 및 **기본 키** 를 메모장에 복사합니다.

    :::image type="content" source="./media/how-to-configure-connection-monitor/copy-id-key.png" alt-text="작업 영역 ID 및 기본 키 스크린샷":::

1. Windows 머신의 경우 PowerShell 창에서 [*EnableRules.ps1*](https://aka.ms/npmpowershellscript) PowerShell 스크립트를 관리자 권한으로 다운로드하여 실행합니다. 이 PowerShell 스크립트는 TCP 트랜잭션과 관련된 방화벽 포트를 엽니다. 
 
    Linux 머신의 경우 다음 단계를 사용하여 포트 번호를 수동으로 변경해야 합니다.

    * 다음 경로로 이동: /var/opt/microsoft/omsagent/npm_state
    * 파일 열기: npmdregistry
    * 포트 번호 값 변경: `PortNumber:<port of your choice>`

### <a name="install-log-analytics-agent-on-each-monitoring-server"></a><a name="installagentonprem"></a>각 모니터링 서버에 Log Analytics 에이전트 설치

중복성을 위해 ExpressRoute 연결의 양쪽에 있는 둘 이상의 서버에 Log Analytics 에이전트를 설치하는 것이 좋습니다. 예를 들어 온-프레미스와 Azure 가상 네트워크에 설치합니다. 다음 단계를 사용하여 에이전트를 설치합니다.

1. 서버에 Log Analytics 에이전트를 설치하는 단계를 보려면 아래에서 해당 운영 체제를 선택합니다.

    * [Windows](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard)
    * [Linux](../azure-monitor/agents/agent-linux.md)

1. 완료되면 제어판에 Microsoft Monitoring Agent가 나타납니다. 구성을 검토하고 Azure Monitor 로그에 대한 [에이전트 연결을 확인](../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor)할 수 있습니다.

1. 모니터링에 사용하려는 다른 온-프레미스 머신에 대해 1단계와 2단계를 반복합니다.

### <a name="install-network-watcher-agent-on-each-monitoring-server"></a><a name="installagentazure"></a>각 모니터링 서버에 Network Watcher 에이전트 설치

#### <a name="new-azure-virtual-machine"></a>새 Azure 가상 머신

VNet 연결을 모니터링하기 위해 새 Azure VM을 만드는 경우 [VM을 만들](../network-watcher/connection-monitor.md#create-the-first-vm)때 Network Watcher 에이전트를 설치할 수 있습니다.

#### <a name="existing-azure-virtual-machine"></a>기존 Azure 가상 머신

기존 VM을 사용하여 연결을 모니터링하는 경우 [Linux](../virtual-machines/extensions/network-watcher-linux.md) 및 [Windows](../virtual-machines/extensions/network-watcher-windows.md)용 네트워크 에이전트를 개별적으로 설치할 수 있습니다.

### <a name="open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>모니터링 에이전트 서버에서 방화벽 포트 열기

방화벽 규칙에서 원본 및 대상 서버 간 통신을 차단할 수 있습니다. 연결 모니터는 해당 문제를 검색하고 토폴로지에 진단 메시지로 표시합니다. 연결 모니터링을 사용하도록 설정하려면 방화벽 규칙에서 원본과 대상 간에 TCP 또는 ICMP를 통한 패킷을 허용하는지 확인합니다.

#### <a name="windows"></a>Windows

Windows 머신의 경우 PowerShell 스크립트를 실행하여 연결 모니터에 필요한 레지스트리 키를 만들 수 있습니다. 또한 이 스크립트는 모니터링 에이전트가 서로 TCP 연결을 만들 수 있도록 Windows 방화벽 규칙을 만듭니다. 스크립트로 만들어진 레지스트리 키는 디버그 로그와 로그 파일의 경로를 기록할지 여부를 지정합니다. 통신에 사용되는 에이전트 TCP 포트도 정의합니다. 이러한 키 값은 스크립트에 의해 자동으로 설정됩니다. 키를 수동으로 변경하면 안 됩니다.

기본적으로 포트 8084가 열립니다. 스크립트에 매개 변수 'portNumber'를 지정하여 사용자 지정 포트를 사용할 수 있습니다. 그러나 이렇게 하면 스크립트를 실행하는 모든 서버에 대해 동일한 포트를 지정해야 합니다.

>[!NOTE]
>'EnableRules' PowerShell 스크립트는 스크립트가 실행되는 서버에서만 Windows 방화벽 규칙을 구성합니다. 네트워크 방화벽이 있는 경우 연결 모니터에서 사용 중인 TCP 포트로 전송된 트래픽을 허용하는지 확인해야 합니다.
>

에이전트 서버에서 관리자 권한으로 PowerShell 창을 엽니다. [EnableRules](https://aka.ms/npmpowershellscript) PowerShell 스크립트를 실행합니다(이전에 다운로드한). 매개 변수는 사용하지 마세요.

:::image type="content" source="./media/how-to-configure-connection-monitor/enable-rules-script.png" alt-text="PowerShell 창에서 실행 중인 규칙 사용 스크립트 스크린샷":::

#### <a name="linux"></a>Linux

Linux 머신의 경우 사용되는 포트 번호를 수동으로 변경해야 합니다.

1. 다음 경로로 이동: /var/opt/microsoft/omsagent/npm_state
1. 파일 열기: npmdregistry
1. 포트 번호 값 변경: `PortNumber:\<port of your choice\>` 사용되는 포트 번호는 작업 영역에서 사용되는 모든 에이전트에서 동일해야 합니다.

## <a name="configure-network-security-group-rules"></a><a name="opennsg"></a>네트워크 보안 그룹 규칙 구성

Azure에 있는 서버를 모니터링하려면 연결 모니터의 TCP 또는 ICMP 트래픽을 허용하도록 NSG(네트워크 보안 그룹) 규칙을 구성해야 합니다. 기본 포트는 **8084로, Azure VM에 설치된 모니터링 에이전트가 온-프레미스 모니터링 에이전트와 통신할 수 있도록 합니다.

NSG에 대한 자세한 내용은 [네트워크 트래픽 필터링](../virtual-network/tutorial-filter-network-traffic.md)에 대한 자습서를 참조하세요.

> [!NOTE]
> 이 단계를 진행하기 전에 에이전트(온-프레미스 서버 에이전트 및 Azure 서버 에이전트)를 설치했는지와 PowerShell 스크립트를 실행했는지 확인합니다.
>

## <a name="enable-network-watcher"></a><a name="enablenetworkwatcher"></a>Network Watcher 사용

가상 네트워크가 있는 모든 구독은 Network Watcher에서 사용하도록 설정됩니다. 구독에 대해 Network Watcher가 명시적으로 사용하지 않도록 설정되었는지 확인합니다. 자세한 내용은 [Network Watcher를 사용하도록 설정](../network-watcher/network-watcher-create.md)을 참조하세요.

## <a name="create-a-connection-monitor"></a><a name="createcm"></a> 연결 모니터 만들기

네트워크의 원본 및 대상 엔드포인트에서 연결 모니터, 테스트, 테스트 그룹을 만드는 방법에 대한 간략한 설명은 [연결 모니터 만들기](../network-watcher/connection-monitor-create-using-portal.md)를 참조하세요. 개인 피어링과 Microsoft 피어링에 대한 연결 모니터링을 구성하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Network Watcher** 리소스로 이동한 다음, ‘모니터링’에서 **연결 모니터** 를 선택합니다. 그런 다음, **만들기** 를 선택하여 새 연결 모니터를 만듭니다.

    :::image type="content" source="./media/how-to-configure-connection-monitor/create-connection-monitor.png" alt-text="Network Watcher의 연결 모니터 스크린샷":::

1. 만들기 워크플로 **기본 사항** 탭의 ‘지역’ 필드에서 Log Analytics 작업 영역을 배포한 지역과 동일한 지역을 선택합니다. ‘작업 영역 구성’에서 이전에 만든 기존 Log Analytics 작업 영역을 선택합니다. **다음: 테스트 그룹 >>** 을 선택합니다.

    :::image type="content" source="./media/how-to-configure-connection-monitor/connection-monitor-basic.png" alt-text="연결 모니터 만들기의 기본 사항 탭 스크린샷":::

1. 테스트 그룹 추가 세부 정보 페이지에서 테스트 그룹의 원본 및 대상 엔드포인트를 추가합니다. 엔드포인트 간 테스트 구성도 설정합니다. 이 테스트 그룹의 **이름** 을 입력합니다.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-group-details.png" alt-text="테스트 그룹 추가 세부 정보 페이지 스크린샷":::

1. **원본 추가** 를 선택하고 **비 Azure 엔드포인트** 탭으로 이동합니다. 연결을 모니터링하려는, Log Analytics 에이전트가 설치된 온-프레미스 리소스를 선택한 후 **엔드포인트 추가** 를 선택합니다.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-source-endpoints.png" alt-text="원본 엔드포인트 추가 스크린샷":::

1. 다음으로, **대상 추가** 를 선택합니다. 

    ExpressRoute **개인 피어링** 을 통한 연결을 모니터링하려면 **Azure 엔드포인트** 탭으로 이동합니다. Azure의 가상 네트워크에 대한 연결을 모니터링하려는, Network Watcher 에이전트가 설치된 Azure 리소스를 선택합니다. *IP* 열에서 각 리소스의 개인 IP 주소를 선택해야 합니다. **엔드포인트 추가** 를 선택하여 테스트 그룹의 대상 목록에 해당 엔드포인트를 추가합니다.
    
    :::image type="content" source="./media/how-to-configure-connection-monitor/add-destination-endpoints.png" alt-text="Azure 대상 엔드포인트 추가 스크린샷":::

    ExpressRoute **Microsoft 피어링** 을 통한 연결을 모니터링하려면 **외부 주소** 탭으로 이동합니다. Microsoft 피어링을 통한 연결을 모니터링하려는 Microsoft 서비스 엔드포인트를 선택합니다. **엔드포인트 추가** 를 선택하여 테스트 그룹의 대상 목록에 해당 엔드포인트를 추가합니다.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-external-destination-endpoints.png" alt-text="외부 대상 엔드포인트 추가 스크린샷":::

1. 이제 **테스트 구성 추가** 를 선택합니다. 프로토콜로 **TCP** 를 선택하고 서버에서 열린 **대상 포트** 를 입력합니다. 그런 다음, **테스트 빈도** 와 **실패한 검사 및 왕복 시간의 임계값** 을 구성합니다. **테스트 구성 추가** 를 선택합니다.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-configuration.png" alt-text="테스트 구성 추가 페이지 스크린샷":::

1. 원본, 대상, 테스트 구성을 추가한 후 **테스트 그룹 추가** 를 선택합니다.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-group-details-configured.png" alt-text="테스트 그룹 추가 세부 정보가 구성된 스크린샷" lightbox="./media/how-to-configure-connection-monitor/add-test-group-details-configured-expanded.png":::

1. 경고를 만들려는 경우 **다음: 경고 만들기 >>** 를 선택합니다. 완료되면 **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택합니다.

## <a name="view-results"></a>결과 보기

1. **Network Watcher** 리소스로 이동한 다음, ‘모니터링’에서 **연결 모니터** 를 선택합니다. 5분 후에 새 연결 모니터가 표시됩니다. 연결 모니터의 네트워크 토폴로지 및 성능 차트를 보려면 테스트 그룹 드롭다운에서 테스트를 선택합니다.

    :::image type="content" source="./media/how-to-configure-connection-monitor/overview.png" alt-text="연결 모니터 개요 페이지 스크린샷" lightbox="./media/how-to-configure-connection-monitor/overview-expanded.png":::

1. **성능 분석** 패널에서 실패한 검사의 백분율과 각 테스트의 왕복 시간 결과를 볼 수 있습니다. 패널 맨 위에 있는 드롭다운을 선택하여 표시되는 데이터의 시간 프레임을 조정할 수 있습니다.

    :::image type="content" source="./media/how-to-configure-connection-monitor/performance-analysis.png" alt-text="성능 분석 패널 스크린샷" lightbox="./media/how-to-configure-connection-monitor/performance-analysis-expanded.png":::

1. **성능 분석** 패널을 닫으면 연결 모니터에서 검색한, 선택한 원본 엔드포인트와 대상 엔드포인트 간 네트워크 토폴로지가 표시됩니다. 이 보기는 원본 엔드포인트와 대상 엔드포인트 간 양방향 트래픽 경로를 보여 줍니다. 패킷이 Microsoft 경계 네트워크에 도달하기 전 패킷의 홉별 대기 시간도 확인할 수 있습니다. 

    :::image type="content" source="./media/how-to-configure-connection-monitor/topology.png" alt-text="연결 모니터의 네트워크 토폴로지 스크린샷" lightbox="./media/how-to-configure-connection-monitor/topology-expanded.png":::

    토폴로지 보기에서 홉을 선택하면 홉에 대한 추가 정보가 표시됩니다. 연결 모니터에서 검색한 홉 관련 문제도 여기에 표시됩니다.

    :::image type="content" source="./media/how-to-configure-connection-monitor/hop-details.png" alt-text="네트워크 홉에 대한 자세한 정보 스크린샷":::

## <a name="next-steps"></a>다음 단계

[Azure ExpressRoute 모니터링](monitor-expressroute.md)에 대해 자세히 알아봅니다.