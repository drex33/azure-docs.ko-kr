---
title: Azure Monitor의 모니터링 솔루션 | Microsoft Docs
description: Azure Monitor의 모니터링 솔루션은 특정 문제 영역을 중심으로 피벗된 메트릭을 제공하는 논리, 시각화 및 데이터 취득 규칙의 컬렉션입니다.  이 문서에서는 모니터링 솔루션을 설치하고 사용하는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/16/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 236fcdb27bccf56cfe9bf92784f74cfa7ac3057b
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112285448"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Azure Monitor의 모니터링 솔루션

Azure Monitor의 모니터링 솔루션은 특정 Azure 애플리케이션 또는 서비스의 작업에 대한 분석을 제공합니다. 이 문서에서는 Azure 모니터링 솔루션에 대해 간략하게 설명하고 모니터링 솔루션을 설치 및 사용하는 방법을 자세히 알아봅니다. 사용하는 애플리케이션 및 서비스의 Azure Monitor에 모니터링 솔루션을 추가할 수 있습니다. 일반적으로 무료로 제공되지만 데이터 수집 시 사용 요금이 발생할 수 있습니다.

## <a name="use-monitoring-solutions"></a>모니터링 솔루션 사용

Azure Monitor의 솔루션 **개요** 페이지에는 Log Analytics 작업 영역에 설치된 각 솔루션에 대한 타일이 표시됩니다. 이 페이지를 열려면 [Azure Portal](https://ms.portal.azure.com)에서 **Azure Monitor** 로 이동합니다. **인사이트** 메뉴에서 **자세히** 를 선택하여 **인사이트 허브** 를 열고 **Log Analytics 작업 영역** 을 클릭합니다.

[![인사이트 허브](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


타일에 사용되는 작업 영역 또는 시간 범위를 변경하려면 화면 맨 위에 있는 드롭다운 상자를 사용합니다. 솔루션의 타일을 클릭하면 수집된 데이터를 자세히 분석하는 보기가 열립니다.

[![솔루션이 선택된 Azure Portal 메뉴와 솔루션이 표시된 솔루션 창을 보여 주는 스크린샷](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

모니터링 솔루션은 여러 유형의 Azure 리소스를 포함할 수 있으며, 다른 리소스와 마찬가지로 솔루션에 포함된 모든 리소스를 볼 수 있습니다. 예를 들어 솔루션에 포함된 모든 로그 쿼리는 [쿼리 탐색기](../logs/log-analytics-tutorial.md)의 **솔루션 쿼리** 에 나열되어 있습니다. [로그 쿼리](../logs/log-query-overview.md)로 임시 분석을 수행할 때 해당 쿼리를 사용할 수 있습니다.

## <a name="list-installed-monitoring-solutions"></a>설치된 모니터링 솔루션 나열

### <a name="portal"></a>[포털](#tab/portal)

다음 절차를 사용하여 구독에 설치된 모니터링 솔루션을 나열할 수 있습니다.

1. [Azure 포털](https://ms.portal.azure.com)로 이동합니다. **솔루션** 을 검색하고 선택합니다.
1. 모든 작업 영역에 설치된 솔루션이 나열됩니다. 솔루션 이름 뒤에는 솔루션이 설치된 작업 영역의 이름이 나옵니다.
1. 화면 맨 위에 있는 드롭다운 상자를 사용하여 구독 또는 리소스 그룹으로 필터링할 수 있습니다.

![모든 솔루션 나열](media/solutions/list-solutions-all.png)

솔루션 이름을 클릭하면 요약 페이지가 열립니다. 이 페이지에는 솔루션에 포함된 보기가 표시되고 솔루션 자체 및 해당 작업 영역에 대한 여러 옵션이 제공됩니다. 위의 절차 중 하나를 사용하여 솔루션의 요약 페이지를 살펴본 후 솔루션 이름을 클릭하세요.

![솔루션 속성](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az monitor log-analytics solution list](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_list) 명령을 사용하여 구독에 설치된 모니터링 솔루션을 나열할 수 있습니다.   `list` 명령을 실행하기 전에 [모니터링 솔루션 설치](#install-a-monitoring-solution)에 있는 사전 요구 사항을 따릅니다.

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

[Get-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution) cmdlet을 사용하여 구독에 설치된 모니터링 솔루션을 나열할 수 있습니다. 해당 명령을 실행하기 전에 [모니터링 솔루션 설치](#install-a-monitoring-solution)에 있는 사전 요구 사항을 따릅니다.

```azurepowershell-interactive
# List all log-analytics solutions in the current subscription.
Get-AzMonitorLogAnalyticsSolution

# List all log-analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all log-analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>모니터링 솔루션 설치

### <a name="portal"></a>[포털](#tab/portal)

Microsoft 및 파트너의 모니터링 솔루션은 [Azure Marketplace](https://azuremarketplace.microsoft.com)에서 사용할 수 있습니다. 다음 절차를 사용하여 사용 가능한 솔루션을 검색하고 설치할 수 있습니다. 솔루션을 설치할 때 솔루션이 설치되고 데이터가 수집될 [Log Analytics 작업 영역](../logs/manage-access.md)을 선택해야 합니다.

1. [구독의 솔루션 목록](#list-installed-monitoring-solutions)에서 **추가** 를 클릭합니다.
1. 솔루션을 찾아보거나 검색합니다. [이 검색 링크](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)에서 솔루션을 찾아볼 수도 있습니다.
1. 원하는 모니터링 솔루션을 찾아서 해당 설명을 자세히 읽습니다.
1. 설치 프로세스를 시작하려면 **만들기** 를 클릭합니다.
1. 설치 프로세스가 시작되면 Log Analytics 작업 영역을 지정하고 솔루션에 필요한 구성을 제공하라는 메시지가 표시됩니다.

![솔루션 설치](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>커뮤니티에서 솔루션 설치

커뮤니티 구성원은 관리 솔루션을 Azure 빠른 시작 템플릿에 제출할 수 있습니다. 이러한 솔루션을 직접 설치하거나 나중에 설치할 수 있도록 템플릿을 다운로드할 수 있습니다.

1. [Log Analytics 작업 영역 및 Automation 계정](#log-analytics-workspace-and-automation-account)에 설명된 프로세스에 따라 작업 영역 및 계정에 연결합니다.
2. [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)으로 이동합니다.
3. 관심 있는 솔루션을 검색합니다.
4. 해당 정보를 보려면 결과에서 솔루션을 선택합니다.
5. **Azure에 배포** 단추를 클릭합니다.
6. 솔루션의 매개 변수 값 외에도 리소스 그룹 및 위치 같은 정보를 제공해야 합니다.
7. **구매** 를 클릭하여 솔루션을 설치합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prepare-your-environment"></a>환경 준비

1. Azure CLI 설치

   CLI 참조 명령을 실행하기 전에 [Azure CLI를 설치](/cli/azure/install-azure-cli)해야 합니다.  원한다면 Azure Cloud Shell을 사용하여 이 문서의 단계를 완료할 수도 있습니다.  Azure Cloud Shell은 브라우저를 통해 사용할 수 있는 대화형 셸 환경입니다.  다음 방법 중 하나를 사용하여 Cloud Shell을 시작합니다.

   - [https://shell.azure.com](https://shell.azure.com)으로 이동하여 Cloud Shell 열기

   - [Azure Portal](https://portal.azure.com)의 오른쪽 위 모서리에 있는 메뉴 모음에서 **Cloud Shell** 단추 선택

1. 로그인합니다.

   CLI를 로컬로 설치한 경우 [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 로그인합니다.  터미널에 표시된 단계에 따라 인증 프로세스를 완료합니다.

    ```azurecli
    az login
    ```

1. `log-analytics-solution` 확장 설치

   `log-analytics-solution` 명령은 핵심 Azure CLI의 실험적 확장입니다. 확장 참조에 대한 자세한 내용은 [Azure CLI 확장 사용하기](/cli/azure/azure-cli-extensions-overview?)를 참조하세요.

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   다음 경고가 표시될 수 있습니다.

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Azure CLI를 사용하여 솔루션 설치하기

솔루션을 설치할 때 솔루션이 설치되고 데이터가 수집될 [Log Analytics 작업 영역](../logs/manage-access.md)을 선택해야 합니다.  Azure CLI를 사용하는 경우, [az monitor log-analytics workspace](/cli/azure/monitor/log-analytics/workspace) 참조 명령을 사용하여 작업 영역을 관리합니다.  [Log Analytics 작업 영역 및 Automation 계정](#log-analytics-workspace-and-automation-account)에 설명된 프로세스에 따라 작업 영역 및 계정에 연결합니다.

[az monitor log-analytics solution create](/cli/azure/monitor/log-analytics/solution)를 사용하여 모니터링 솔루션을 설치합니다.  대괄호 안에 있는 매개 변수는 선택적 요소입니다.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

OMSGallery/Containers의 계획 제품에 대한 로그 분석 솔루션을 생성하는 코드 샘플은 다음과 같습니다.

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="prepare-your-environment"></a>환경 준비

1. Azure Powershell 설치

   Azure PowerShell 참조 명령을 실행하기 전에 [Azure PowerShell을 설치](/powershell/azure/install-az-ps)해야 합니다. 원한다면 Azure Cloud Shell을 사용하여 이 문서의 단계를 완료할 수도 있습니다. Azure Cloud Shell은 브라우저를 통해 사용할 수 있는 대화형 셸 환경입니다. 다음 방법 중 하나를 사용하여 Cloud Shell을 시작합니다.

   - [https://shell.azure.com](https://shell.azure.com)으로 이동하여 Cloud Shell 열기

   - [Azure Portal](https://portal.azure.com)의 오른쪽 위 모서리에 있는 메뉴 모음에서 **Cloud Shell** 단추 선택

   > [!IMPORTANT]
   > **Az.MonitoringSolutions** PowerShell 모듈이 미리 보기로 제공되는 동안 `Install-Module` cmdlet을 사용하여 모듈을 별도로 설치해야 합니다. 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 일부가 되며 기본적으로 Azure Cloud Shell 내에서 사용할 수 있습니다.

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. 로그인합니다.

   PowerShell을 로컬로 설치한 경우 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 로그인합니다. PowerShell에 표시된 단계에 따라 인증 프로세스를 완료합니다.

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Azure PowerShell을 사용하여 솔루션 설치하기

솔루션을 설치할 때 솔루션이 설치되고 데이터가 수집될 [Log Analytics 작업 영역](../logs/manage-access.md)을 선택해야 합니다. Azure PowerShell을 사용하는 경우, [Az.MonitoringSolutions](/powershell/module/az.monitoringsolutions) PowerShell 모듈에서 cmdlet을 사용하여 작업 영역을 관리합니다. [Log Analytics 작업 영역 및 Automation 계정](#log-analytics-workspace-and-automation-account)에 설명된 프로세스에 따라 작업 영역 및 계정에 연결합니다.

[New-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution) cmdlet을 사용하여 모니터링 솔루션을 설치합니다. 대괄호 안에 있는 매개 변수는 선택적 요소입니다.

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

다음 예에서는 로그 분석 작업 영역에 대한 모니터 로그 분석 솔루션을 만듭니다.

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics 작업 영역 및 Automation 계정

모든 모니터링 솔루션에는 수집된 데이터를 저장하고 로그 검색 및 보기를 호스트할 [Log Analytics 작업 영역](../logs/manage-access.md)이 필요합니다. 일부 솔루션은 Runbook 및 관련 리소스를 포함할 [Automation 계정](../../automation/automation-security-overview.md)도 필요합니다. 작업 영역 및 계정은 다음 요구 사항을 충족해야 합니다.

* 각 솔루션 설치 시 하나의 Log Analytics 작업 영역과 하나의 Automation 계정만 사용할 수 있습니다. 솔루션을 여러 작업 영역에 개별적으로 설치할 수 있습니다.
* 솔루션에서 Automation 계정을 요구하는 경우 Log Analytics 작업 영역과 Automation 계정이 서로 연결되어야 합니다. Log Analytics 작업 영역은 하나의 Automation 계정에만 연결되고, Automation 계정은 하나의 Log Analytics 작업 영역에만 연결될 수 있습니다.

Azure Marketplace를 통해 솔루션을 설치하는 경우 작업 영역 및 Automation 계정을 요구하는 메시지가 표시됩니다. 작업 영역과 계정이 아직 서로 연결되지 않았으면 둘 사이에 연결이 만들어집니다.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Log Analytics 작업 영역 및 Automation 계정 간의 링크 확인

다음 절차를 통해 Log Analytics 작업 영역 및 Automation 계정 간의 링크를 확인할 수 있습니다.

1. Azure Portal에서 Automation 계정을 선택합니다.
1. 메뉴의 **관련 리소스** 섹션으로 스크롤하여 **연결된 작업 영역** 을 선택합니다.
1. **작업 영역** 이 Automation 계정에 연결된 경우 이 페이지에는 연결된 작업 영역이 나열됩니다. 나열된 작업 영역 이름을 선택하면 해당 작업 영역의 개요 페이지로 리디렉션됩니다.

## <a name="remove-a-monitoring-solution"></a>모니터링 솔루션 제거

### <a name="portal"></a>[포털](#tab/portal)

포털을 사용하여 설치된 솔루션을 제거하려면 [설치된 솔루션 목록](#list-installed-monitoring-solutions)에서 해당 솔루션을 찾습니다. 솔루션 이름을 클릭하여 요약 페이지를 열고 **삭제** 를 클릭합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 설치된 솔루션을 제거하려면 [az monitor log-analytics solution delete](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_delete) 명령을 사용합니다.

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell을 사용하여 설치된 솔루션을 제거하려면 [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) cmdlet을 사용합니다.

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>다음 단계

* [Microsoft에서 모니터링 솔루션 목록](../monitor-reference.md)을 가져옵니다.
* 모니터링 솔루션에서 수집한 데이터를 분석하는 [쿼리 만들기](../logs/log-query-overview.md) 방법을 알아봅니다.
* [Azure Monitor에 대한 모든 Azure CLI 명령](/cli/azure/azure-cli-reference-for-monitor)을 참조하세요.