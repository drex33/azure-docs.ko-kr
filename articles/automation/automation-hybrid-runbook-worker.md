---
title: Azure Automation Hybrid Runbook Worker 개요
description: 이 문서에서는 로컬 데이터 센터 또는 클라우드 공급자의 머신에서 Runbook을 실행하는 데 사용할 수 있는 Hybrid Runbook Worker에 대한 개요를 제공합니다.
services: automation
ms.subservice: process-automation
ms.date: 10/06/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e5b6a6036514d1d6391c242563afbf9c185ba71e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130253118"
---
# <a name="automation-hybrid-runbook-worker-overview"></a>Automation Hybrid Runbook Worker 개요

Azure Automation의 Runbook은 Azure 클라우드 플랫폼에서 실행되므로 다른 클라우드 또는 온-프레미스 환경의 리소스에 액세스하지 못할 수도 없습니다. Azure Automation Hybrid Runbook Worker 기능을 사용하여 역할을 호스트하는 머신에서 직접 Runbook을 실행하고 환경의 리소스에 대해 Runbook을 실행하여 로컬 리소스를 관리할 수 있습니다. Runbook은 Azure Automation에 저장 및 관리된 후 하나 이상의 할당된 컴퓨터에 전달됩니다.

Azure Automation Azure VM(가상 머신) 확장 프레임워크를 통해 Hybrid Runbook Worker 역할의 네이티브 통합을 제공합니다. Azure VM 에이전트는 Arc 지원 서버 Connected Machine 에이전트를 통해 Windows 및 Linux VM의 Azure VM 및 비 Azure 머신에서 확장을 관리합니다. 이제 Azure Automation 지원하는 두 개의 Hybrid Runbook Workers 설치 플랫폼이 있습니다.

| 플랫폼 | Description |
|---|---|
|에이전트 기반(V1)  |[log Analytics 에이전트가](../azure-monitor/agents/log-analytics-agent.md) Azure Monitor [Log Analytics 작업 영역에](../azure-monitor/logs/design-logs-deployment.md) 보고한 후 설치됩니다.|
|확장 기반(V2)  |Azure Monitor Log Analytics 작업 영역에 보고하는 Log Analytics 에이전트에 대한 종속성 없이 Hybrid Runbook Worker [VM 확장을](./extension-based-hybrid-runbook-worker-install.md)사용하여 설치됩니다. 권장되는 플랫폼입니다.|

:::image type="content" source="./media/automation-hybrid-runbook-worker/hybrid-worker-group-platform.png" alt-text="플랫폼 필드를 표시하는 Hybrid Worker 그룹":::

확장 기반 Hybrid Runbook Worker 역할에서 사용할 수 있는 혜택 목록은 다음과 같습니다.

| 이점 | 설명 |
|---|---|
|원활한 온보딩| 다단계 프로세스인 Hybrid Runbook Workers를 온보딩하기 위한 Log Analytics 솔루션에 대한 종속성을 제거합니다. 이 프로세스는 시간이 오래 걸리고 오류가 발생하기 쉽습니다. |
|통합 온보딩 환경| 설치는 Azure 및 비 Azure 머신에 대해 지원되는 동일한 방법을 사용하여 관리됩니다. |
|관리 용이성| Hybrid Runbook Worker ARM ID와 네이티브 통합을 통해 정책 및 템플릿을 통해 대규모로 유연하게 거버넌스할 수 있습니다. |
|Azure AD 기반 인증| Azure AD에서 제공하는 VM 시스템 할당 ID를 사용합니다. 이렇게 하면 ID 및 리소스 자격 증명의 제어 및 관리가 중앙 집중화됩니다.|

설치 후 Hybrid Runbook Worker 작업의 경우 Hybrid Runbook Workers에서 Runbook을 실행하는 프로세스는 동일합니다. 확장 기반 접근 방식의 목적은 Hybrid Runbook Worker 역할의 설치 및 관리를 간소화하고 에이전트 기반 버전으로 작업하는 복잡성을 제거하는 것입니다. 새 확장 기반 설치는 에이전트 기반 Hybrid Runbook Worker 역할의 설치 또는 관리에 영향을 미치지 않습니다. 두 유형이 모두 동일한 컴퓨터에 공존할 수 있습니다.

확장 기반 Hybrid Runbook Worker 사용자 Hybrid Runbook Worker 유형만 지원하며 업데이트 관리 기능에 필요한 시스템 Hybrid Runbook Worker 포함하지 않습니다. 확장 기반 Hybrid Runbook Worker 설치에 대한 PowerShell 지원은 현재 지원되지 않습니다.

## <a name="runbook-worker-types"></a>Runbook Worker 유형

Runbook Worker에는 시스템과 사용자라는 두 가지 유형이 있습니다. 다음 표에서는 두 유형의 차이점을 설명합니다.

|형식 | 설명 |
|-----|-------------|
|**System** |Windows 및 Linux 컴퓨터에 사용자 지정 업데이트를 설치하도록 디자인된 업데이트 관리 기능에서 사용하는 숨겨진 Runbook 집합을 지원합니다.<br> 이 유형의 Hybrid Runbook Worker Hybrid Runbook Worker 그룹의 구성원이 아니므로 Runbook Worker 그룹을 대상으로 하는 Runbook을 실행하지 않습니다. |
|**사용자** |하나 이상의 Runbook Worker 그룹의 멤버인 Windows 및 Linux 컴퓨터에서 직접 실행하기 위한 사용자 정의 Runbook을 지원합니다. |

에이전트 기반(V1) Hybrid Runbook Workers는 [Azure Monitor Log Analytics](../azure-monitor/agents/log-analytics-agent.md) 작업 영역에 보고하는 Log [Analytics](../azure-monitor/logs/design-logs-deployment.md)에이전트를 사용합니다. 작업 영역은 컴퓨터에서 모니터링 데이터를 수집하는 것뿐만 아니라 Hybrid Runbook Worker 설치하는 데 필요한 구성 요소를 다운로드하는 데도 필요합니다.

Azure Automation [업데이트 관리](./update-management/overview.md)를 사용하도록 설정하면 Log Analytics 작업 영역에 연결된 모든 컴퓨터가 시스템 Hybrid Runbook Worker로 자동으로 구성됩니다. 사용자 Windows Hybrid Runbook Worker 구성하려면 [Automation에서 에이전트 기반 Windows Hybrid Runbook Worker 배포](automation-windows-hrw-install.md) 및 Linux의 경우 Automation에서 [에이전트 기반 Linux Hybrid Runbook Worker 배포를 참조하세요.](./automation-linux-hrw-install.md)

## <a name="runbook-worker-limits"></a>Runbook Worker 한도

다음 표에서는 Automation 계정의 최대 시스템 및 사용자 하이브리드 runbook worker 수를 보여 줍니다. 관리할 컴퓨터가 4,000대 이상인 경우 또 다른 Automation 계정을 만드는 것이 좋습니다.

|작업자 유형| Automation 계정당 지원되는 최대 수입니다.|
|---|---|
|시스템|4000|
|사용자 |4000|

## <a name="how-does-it-work"></a>작동 원리

![Hybrid Runbook Worker 개요](media/automation-hybrid-runbook-worker/automation.png)

각 사용자 Hybrid Runbook Worker는 작업자를 설치할 때 지정한 Hybrid Runbook Worker 그룹의 멤버입니다. 그룹에 단일 작업자가 포함될 수도 있지만 고가용성을 위해 그룹에 여러 작업자를 설치할 수 있습니다. 각 컴퓨터는 하나의 Automation 계정에 하나의 Hybrid Runbook Worker 보고를 호스트할 수 있습니다. 여러 Automation 계정에서 Hybrid Worker를 등록할 수 없습니다. 하이브리드 작업자는 단일 Automation 계정의 작업만 수신 대기할 수 있습니다. 업데이트 관리에 의해 관리되는 시스템 Hybrid Runbook Worker를 호스트하는 컴퓨터는 Hybrid Runbook Worker 그룹에 추가할 수 있습니다. 단, 업데이트 관리 및 Hybrid Runbook Worker 그룹 멤버 자격 모두에 대해 동일한 Automation 계정을 사용해야 합니다.

사용자 Hybrid Runbook Worker에서 Runbook을 시작할 경우 이를 실행할 그룹을 지정합니다. 그룹의 각 작업자는 Azure Automation을 폴링하여 사용할 수 있는 작업이 있는지 확인합니다. 작업을 사용할 수 있는 경우 작업을 가져올 수 있는 첫 번째 작업자가 해당 작업을 수행합니다. 작업 큐의 처리 시간은 하이브리드 작업자 하드웨어 프로필 및 로드에 따라 달라집니다. 특정 작업자를 지정할 수 없습니다. 하이브리드 작업자는 폴링 메커니즘(30초마다)에 따라 작동하며 선착순 순서를 따릅니다. 작업을 밀어넣은 시기에 따라 어느 하이브리드 작업자가 ping하는지에 상관없이 Automation 서비스가 작업을 선택합니다. 단일 하이브리드 작업자는 일반적으로 ping당(즉, 30초마다) 4개의 작업을 선택할 수 있습니다. 푸시 작업 비율이 30초당 4개보다 높은 경우 Hybrid Runbook Worker 그룹의 다른 Hybrid Worker가 작업을 선택할 가능성이 높습니다.

Hybrid Runbook Worker에는 디스크 공간, 메모리 또는 네트워크 소켓에 대한 [Azure 샌드박스](automation-runbook-execution.md#runbook-execution-environment) 리소스 [한도](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)가 별로 없습니다. 하이브리드 작업자에 대한 한도는 작업자의 자체 리소스와만 관련되며, Azure 샌드박스에 있는 [공평 분배](automation-runbook-execution.md#fair-share) 시간 한도로 제한되지 않습니다.

Hybrid Runbook Worker에서의 Runbook 배포 및 작업이 트리거되는 시기 또는 방법을 제어하기 위해 Automation 계정 내의 다른 Hybrid Runbook Worker 그룹에 대해 하이브리드 작업자를 등록할 수 있습니다. 실행 정렬을 지원하기 위해 특정 그룹에 대해 작업을 대상으로 지정합니다.

## <a name="hybrid-runbook-worker-installation"></a>Hybrid Runbook Worker 설치

Hybrid Runbook Worker를 설치하는 프로세스는 운영 체제에 따라 다릅니다. 다음 표에는 배포 유형이 정의되어 있습니다.

|운영 체제  |배포 유형  |
|---------|---------|
|Windows | [자동](automation-windows-hrw-install.md#automated-deployment)<br>[수동](automation-windows-hrw-install.md#manual-deployment). |
|Linux   | [수동](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker) |
|여기서는  | 사용자 Hybrid Runbook Workers의 경우 [Automation에서 확장 기반 Windows 또는 Linux 사용자 Hybrid Runbook Worker 배포를 참조하세요.](./extension-based-hybrid-runbook-worker-install.md) 이것이 권장된 방법입니다. |

## <a name="network-planning"></a><a name="network-planning"></a>네트워크 계획

포트, URL 및 Hybrid Runbook Worker에 필요한 기타 네트워킹 세부 정보에 대한 자세한 내용은 [Azure Automation 네트워크 구성](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker)을 확인하세요.

### <a name="proxy-server-use"></a>프록시 서버 사용

Azure Automation과 Log Analytics 에이전트를 실행하는 컴퓨터 간의 통신에 프록시 서버를 사용하는 경우 적절한 리소스에 액세스할 수 있는지 확인합니다. Hybrid Runbook Worker 및 Automation 서비스의 요청 제한 시간은 30초입니다. 3회 시도 후에는 요청이 실패합니다.

### <a name="firewall-use"></a>방화벽 사용

방화벽을 사용하여 인터넷 액세스를 제한하는 경우 액세스를 허용하도록 방화벽을 구성해야 합니다. Log Analytics 게이트웨이를 프록시로 사용하는 경우 Hybrid Runbook Workers에 대해 구성되었는지 확인합니다. [Automation Hybrid Runbook Worker에 대한 Log Analytics 게이트웨이 구성](../azure-monitor/agents/gateway.md)을 참조하세요.

### <a name="service-tags"></a>서비스 태그

Azure Automation은 [GuestAndHybridManagement](../virtual-network/service-tags-overview.md) 서비스 태그부터 Azure 가상 네트워크 서비스 태그를 지원합니다. 서비스 태그를 사용하여 [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md#security-rules) 또는 [Azure Firewall](../firewall/service-tags.md)에서 네트워크 액세스 제어를 정의할 수 있습니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용합니다. 서비스 태그 이름 **GuestAndHybridManagement** 를 규칙의 적절한 원본 또는 대상 필드에 지정하면 Automation 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. 이 서비스 태그는 IP 범위를 특정 지역으로 제한하여 보다 세부적인 제어를 허용하는 기능을 지원하지 않습니다.

Azure Automation 서비스의 서비스 태그는 다음 시나리오에 사용되는 IP만 제공합니다.

* 가상 네트워크 내에서 웹후크 트리거
* VNet의 Hybrid Runbook Worker 또는 State Configuration 에이전트가 Automation 서비스와 통신할 수 있도록 허용

>[!NOTE]
>현재 서비스 태그 **GuestAndHybridManagement** 는 Azure 샌드박스에서의 Runbook 작업 실행을 지원하지 않습니다.이 작업은 Hybrid Runbook Worker에서 직접 실행해야 합니다.

## <a name="support-for-impact-level-5-il5"></a>영향 수준 5(IL5) 지원

Azure Automation Hybrid Runbook Worker를 Azure Government에서 사용하여 다음 두 가지 구성의 영향 수준 5 워크로드를 지원할 수 있습니다.

* [격리된 가상 머신](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines) 배포되면 해당 컴퓨터의 전체 실제 호스트를 사용하여 IL5 워크로드를 지원하는 데 필요한 수준의 격리를 제공합니다.

* [Azure Dedicated Host](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host)는 하나의 Azure 구독 전용인 하나 이상의 가상 머신을 호스트할 수 있는 물리적 서버를 제공합니다.

>[!NOTE]
>Hybrid Runbook Worker 역할을 통한 컴퓨팅 격리는 Azure 상용 및 미국 정부 클라우드에서 사용할 수 있습니다.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Hybrid Runbook Worker의 업데이트 관리 주소

업데이트 관리에는 Hybrid Runbook Worker에 필요한 표준 주소 및 포트 외에도 [네트워크 계획](./update-management/plan-deployment.md#ports) 섹션에 설명된 다른 네트워크 구성 요구 사항이 있습니다.

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker의 Azure Automation State Configuration

[Hybrid Runbook Worker의 Azure Automation State Configuration](automation-dsc-overview.md)을 실행할 수 있습니다. Hybrid Runbook Worker를 지원하는 서버 구성을 관리하려면 서버를 DSC 노드로 추가해야 합니다. [머신에 Azure Automation State Configuration을 통한 관리 사용](automation-dsc-onboarding.md)을 참조하세요.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker의 Runbook

로컬 컴퓨터의 리소스를 관리하거나 사용자 Hybrid Runbook Worker가 배포된 로컬 환경의 리소스에 대해 실행되는 Runbook이 있을 수 있습니다. 이 경우 Automation 계정 대신 하이브리드 작업자에서 Runbook을 실행하도록 선택할 수 있습니다. Hybrid Runbook Worker에서 실행되는 Runbook은 Automation 계정에서 실행되는 Runbook 구조와 동일합니다. [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.

### <a name="hybrid-runbook-worker-jobs"></a>Hybrid Runbook Worker 작업

Hybrid Runbook Worker 작업은 Windows의 경우 로컬 **시스템** 계정으로 실행되고, Linux의 경우 [nxautomation 계정](automation-runbook-execution.md#log-analytics-agent-for-linux)으로 실행됩니다. Azure Automation은 Azure 샌드박스에서 실행되는 작업과 다른 방식으로 Hybrid Runbook Worker에 대한 작업을 처리합니다. [Runbook 실행 환경](automation-runbook-execution.md#runbook-execution-environment)을 참조하세요.

Hybrid Runbook Worker 호스트 머신이 다시 부팅되면, 실행 중인 모든 Runbook 작업이 처음부터 다시 시작되거나 PowerShell 워크플로 Runbook의 마지막 검사점에서 다시 시작됩니다. Runbook 작업이 세 번 이상 다시 시작되면 일시 중단됩니다.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker에 대한 Runbook 권한

사용자 Hybrid Runbook Worker에서 실행하는 Runbook은 Azure에 없는 리소스에 액세스하기 때문에 일반적으로 Azure 리소스에 인증하는 Runbook에 사용되는 인증 메커니즘을 사용할 수 없습니다. Runbook은 로컬 리소스에 고유한 인증을 제공하거나 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)를 사용하여 인증을 구성합니다. 실행 계정을 지정하여 모든 Runbook에 대한 사용자 컨텍스트를 제공할 수도 있습니다.

## <a name="view-system-hybrid-runbook-workers"></a>시스템 Hybrid Runbook Worker 보기

Windows 또는 Linux 컴퓨터에서 업데이트 관리 기능을 사용하도록 설정한 후 Azure Portal에서 시스템 Hybrid Runbook Worker 그룹 목록을 인벤토리에 추가할 수 있습니다. 선택한 Automation 계정 왼쪽 창의 **하이브리드 작업자 그룹** 옵션에서 **시스템 하이브리드 작업자 그룹** 탭을 선택하면 포털에서 최대 2,000개의 작업자를 볼 수 있습니다.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Automation 계정 시스템 하이브리드 작업자 그룹 페이지" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

2,000개를 초과하는 하이브리드 작업자 전체의 목록을 가져오려면 다음 PowerShell 스크립트를 실행하면 됩니다.

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>다음 단계

* 온-프레미스 데이터 센터 또는 다른 클라우드 환경의 프로세스를 자동화하도록 Runbook을 구성하는 방법을 알아보려면 [Hybrid Runbook Worker에서 Runbook 실행](automation-hrw-run-runbooks.md)을 참조하세요.

* Hybrid Runbook Worker 문제를 해결하는 방법을 알아보려면 [Hybrid Runbook Worker 문제 해결](troubleshoot/hybrid-runbook-worker.md#general)을 참조하세요.
