---
title: VM 인사이트에 대한 Log Analytics 작업 영역 구성
description: VM 인사이트에서 사용하는 Log Analytics 작업 영역을 만들고 구성하는 방법을 설명합니다.
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurepowershell
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.openlocfilehash: a5a65f99fec0bb0db245450ead9747776e194b46
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614432"
---
# <a name="configure-log-analytics-workspace-for-vm-insights"></a>VM 인사이트에 대한 Log Analytics 작업 영역 구성
VM 인사이트는 Azure Monitor의 Log Analytics 작업 영역 하나 이상에서 데이터를 수집합니다. 에이전트를 온보딩하기 전에 작업 영역을 만들고 구성해야 합니다. 이 문서에서는 작업 영역 요구 사항을 설명하고 VM 인사이트에 작업 영역을 구성합니다.

## <a name="overview"></a>개요
단일 구독은 요구 사항에 따라 작업 영역을 여러 개 사용할 수 있습니다. 작업 영역의 유일한 요구 사항은 작업 영역이 지원되는 위치에 있고 *VMInsights* 솔루션으로 구성되어야 한다는 점입니다.

작업 영역이 구성되면 사용 가능한 옵션을 사용하여 가상 머신과 가상 머신 확장 집합에 필요한 에이전트를 설치하고 데이터를 보낼 작업 영역을 지정할 수 있습니다. VM 인사이트는 구독에 구성된 작업 영역에서 데이터를 수집합니다.

> [!NOTE]
> Azure Portal을 사용하여 단일 가상 머신이나 가상 머신 확장 집합에서 VM 인사이트를 사용하도록 설정하면 기존 작업 영역을 선택하거나 새 작업 영역을 만들 수 있는 옵션이 제공됩니다. *VMInsights* 솔루션이 아직 설치되지 않았으면 이 작업 영역에 설치됩니다. 그런 다음, 다른 에이전트에 이 작업 영역을 사용할 수 있습니다.


## <a name="create-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

>[!NOTE]
>이 섹션에 설명된 정보는 [서비스 맵 솔루션](service-map.md)에도 적용됩니다. 

**Log Analytics 작업 영역** 메뉴에서 Azure Portal의 Log Analytics 작업 영역에 액세스합니다.

[![Log Anlytics 작업 영역](media/vminsights-configure-workspace/log-analytics-workspaces.png)](media/vminsights-configure-workspace/log-analytics-workspaces.png#lightbox)

다음 메서드 중 하나를 사용하여 새 Log Analytics 작업 영역을 만들 수 있습니다. 사용자 환경에서 사용해야 하는 작업 영역의 수를 확인하고 액세스 전략을 설계하는 방법에 대한 지침은 [Azure Monitor 로그 배포 설계](../logs/design-logs-deployment.md)를 참조하세요.


* [Azure Portal](../logs/quick-create-workspace.md)
* [Azure CLI](../logs/resource-manager-workspace.md)
* [PowerShell](../logs/powershell-workspace-configuration.md)
* [Azure Resource Manager](../logs/resource-manager-workspace.md)

## <a name="supported-regions"></a>지원되는 지역
VM insights는 [Log Analytics에서 지](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all)원하는 모든 지역에서 Log Analytics 작업 영역을 지원 합니다.


>[!NOTE]
>모든 지역에서 Azure VM을 모니터링할 수 있습니다. VM 자체는 Log Analytics 작업 영역에서 지원하는 지역으로 제한되지 않습니다.

## <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어
VM 인사이트의 기능을 사용하고 액세스하려면 작업 영역에 [Log Analytics 기여자 역할](../logs/manage-access.md#manage-access-using-azure-permissions)이 있어야 합니다. 성능, 상태 및 맵 데이터를 보려면 Azure VM에 [모니터링 reader 역할](../roles-permissions-security.md#built-in-monitoring-roles)이 있어야 합니다. Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 관리](../logs/manage-access.md)를 참조하세요.

## <a name="add-vminsights-solution-to-workspace"></a>작업 영역에 VMInsights 솔루션 추가
VM 인사이트에서 Log Analytics 작업 영역을 사용하려면 먼저 *VMInsights* 솔루션이 설치 되어 있어야 합니다. 작업 영역을 구성하는 방법은 다음 섹션에 설명되어 있습니다.

> [!NOTE]
> *VMInsights* 솔루션을 작업 영역에 추가하면 작업 영역에 연결된 모든 기존 가상 머신에서 데이터를 InsightsMetrics로 보내기 시작합니다. 다른 데이터 형식의 데이터는 작업 영역에 연결된 기존 가상 머신에 Dependency Agent를 추가할 때까지 수집되지 않습니다.

### <a name="azure-portal"></a>Azure Portal
Azure Portal을 사용하여 기존 작업 영역을 구성하는 3가지 옵션이 있습니다. 각 항목에 대한 설명은 아래와 같습니다.

단일 작업 영역을 구성하려면 **Azure Monitor** 메뉴에서 **Virtual Machines** 옵션으로 이동하여 **다른 온보딩 옵션** 을 선택한 다음, **작업 영역을 구성** 합니다. 구독 및 작업 영역을 선택한 다음, **구성** 을 클릭합니다.

[![작업 영역 구성](../vm/media/vminsights-enable-policy/configure-workspace.png)](../vm/media/vminsights-enable-policy/configure-workspace.png#lightbox)

여러 작업 영역을 구성하려면 Azure Portal **모니터** 메뉴의 **Virtual Machines** 메뉴에서 **작업 영역 구성** 탭을 선택합니다. 필터 값을 설정하여 기존 작업 영역 목록을 표시합니다. 사용할 각 작업 영역 옆에 있는 상자를 선택한 다음, **선택 구성** 을 클릭합니다.

[![작업 영역 구성](../vm/media/vminsights-enable-policy/workspace-configuration.png)](../vm/media/vminsights-enable-policy/workspace-configuration.png#lightbox)


Azure Portal을 사용하여 단일 가상 머신이나 가상 머신 확장 집합에서 VM 인사이트를 사용하도록 설정하면 기존 작업 영역을 선택하거나 새 작업 영역을 만들 수 있는 옵션이 제공됩니다. *VMInsights* 솔루션이 아직 설치되지 않았으면 이 작업 영역에 설치됩니다. 그런 다음, 다른 에이전트에 이 작업 영역을 사용할 수 있습니다.

[![포털에서 단일 VM 사용](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)](../vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png#lightbox)


### <a name="resource-manager-template"></a>Resource Manager 템플릿
VM 인사이트의 Azure Resource Manager 템플릿은 [GitHub 리포지토리에서 다운로드](https://aka.ms/VmInsightsARMTemplates)할 수 있는 보관 파일(.zip)로 제공됩니다. 여기에는 VM 인사이트의 Log Analytics 작업 영역을 구성하는 **ConfigureWorkspace** 라는 템플릿이 포함됩니다. 다음 샘플 PowerShell 및 CLI 명령을 비롯한 표준 메서드 중 하나를 사용하여 이 템플릿을 배포합니다. 

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az deployment group create --name ConfigureWorkspace --resource-group my-resource-group --template-file CreateWorkspace.json  --parameters workspaceResourceId='/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace' workspaceLocation='eastus'

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
New-AzResourceGroupDeployment -Name ConfigureWorkspace -ResourceGroupName my-resource-group -TemplateFile ConfigureWorkspace.json -workspaceResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace -location eastus
```

---



## <a name="next-steps"></a>다음 단계
- 에이전트를 VM 인사이트에 연결하려면 [에이전트를 VM 인사이트에 온보딩](vminsights-enable-overview.md)을 참조하세요.
- 솔루션에서 작업 영역으로 전송되는 데이터의 양을 제한하려면 [Azure Monitor 모니터링 솔루션 대상 지정(미리 보기)](../insights/solution-targeting.md)을 참조하세요.