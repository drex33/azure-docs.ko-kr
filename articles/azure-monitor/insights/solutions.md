---
title: Azure Monitor의 모니터링 솔루션 | Microsoft Docs
description: 여러 문제 영역에 대 한 미리 패키지 된 된 논리, 시각화 및 데이터 취득 규칙 컬렉션에 대 한 정보를 가져옵니다.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 11/21/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 2c9ece2eecb8a98e2d314ce0ab5e0fb752a2f11d
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132936133"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Azure Monitor의 모니터링 솔루션

> [!CAUTION]
> 많은 모니터링 솔루션이 더 이상 활성 개발에 있지 않습니다.  각 솔루션을 확인 하 여 대체가 있는지 확인 하는 것이 좋습니다. 이러한 솔루션을 계속 사용할 수 있는 경우에도 다른 옵션을 포함 하는 솔루션의 새 인스턴스를 배포 하지 않는 것이 좋습니다. 많은 내용이 [최신 큐 레이트 시각화 또는 통찰력](/azure/azure-monitor/monitor-reference#insights-and-curated-visualizations)으로 바뀌었습니다.  

Azure Monitor의 모니터링 솔루션은 Azure 응용 프로그램 또는 서비스의 작업에 대 한 분석을 제공 합니다. 이 문서에서는 Azure의 모니터링 솔루션에 대 한 간략 한 개요와 사용 및 설치에 대 한 세부 정보를 제공 합니다. 

사용하는 애플리케이션 및 서비스의 Azure Monitor에 모니터링 솔루션을 추가할 수 있습니다. 일반적으로 무료로 사용할 수 있지만 사용 요금을 호출할 수 있는 데이터를 수집 합니다.

## <a name="use-monitoring-solutions"></a>모니터링 솔루션 사용

Azure Monitor의 **개요** 페이지에는 Log Analytics 작업 영역에 설치 된 각 솔루션에 대 한 타일이 표시 됩니다. 이 페이지를 열려면 [Azure Portal](https://ms.portal.azure.com)에서 **Azure Monitor** 로 이동합니다. **Insights** 메뉴에서 **자세히** 를 선택 하 여 **Insights Hub** 를 열고 **Log Analytics 작업 영역** 을 선택 합니다.

[![Insights 허브를 열기 위한 선택 항목을 보여 주는 스크린샷](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


화면 맨 위에 있는 드롭다운 상자를 사용 하 여 타일에 사용 되는 작업 영역 또는 시간 범위를 변경할 수 있습니다. 솔루션에 대 한 타일을 선택 하 여 수집 된 데이터에 대 한 보다 자세한 분석을 포함 하는 보기를 엽니다.

[![Azure Portal의 솔루션 모니터링에 대 한 통계를 보여 주는 스크린샷](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

모니터링 솔루션은 여러 유형의 Azure 리소스를 포함할 수 있습니다. 다른 리소스와 마찬가지로 솔루션에 포함 된 모든 리소스를 볼 수 있습니다. 예를 들어 솔루션에 포함 된 모든 로그 쿼리는 [쿼리 탐색기](../logs/log-analytics-tutorial.md)의 **솔루션 쿼리** 아래에 나열 됩니다. [로그 쿼리](../logs/log-query-overview.md)를 사용 하 여 임시 분석을 수행 하는 경우 이러한 쿼리를 사용할 수 있습니다.

## <a name="list-installed-monitoring-solutions"></a>설치된 모니터링 솔루션 나열

### <a name="portal"></a>[포털](#tab/portal)

구독에 설치 된 모니터링 솔루션을 나열 하려면 다음을 수행 합니다.

1. [Azure 포털](https://ms.portal.azure.com)로 이동합니다. **솔루션** 을 검색하고 선택합니다.

   모든 작업 영역에 설치된 솔루션이 나열됩니다. 솔루션 이름 뒤에는 설치 된 작업 영역의 이름이 나옵니다.
1. 화면 맨 위에 있는 드롭다운 상자를 사용하여 구독 또는 리소스 그룹으로 필터링할 수 있습니다.

![나열 된 솔루션을 보여 주는 스크린샷](media/solutions/list-solutions-all.png)

솔루션 이름을 선택 하 여 요약 페이지를 엽니다. 이 페이지에는 솔루션에 포함 된 모든 보기가 표시 되 고 솔루션 자체와 해당 작업 영역에 대 한 옵션이 제공 됩니다.

![솔루션에 대 한 요약 정보를 보여 주는 스크린샷](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

구독에 설치 된 모니터링 솔루션을 나열 하려면 [az monitor log-analytics solution list](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_list) 명령을 사용 합니다. 명령을 실행 하기 전에 [모니터링 솔루션 설치](#install-a-monitoring-solution)의 필수 구성 요소를 따릅니다.

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

구독에 설치 된 모니터링 솔루션을 나열 하려면 [AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution) cmdlet을 사용 합니다. Cmdlet을 실행 하기 전에 [모니터링 솔루션 설치](#install-a-monitoring-solution)의 필수 구성 요소를 따릅니다.

```azurepowershell-interactive
# List all Log Analytics solutions in the current subscription
Get-AzMonitorLogAnalyticsSolution

# List all Log Analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all Log Analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>모니터링 솔루션 설치

### <a name="portal"></a>[포털](#tab/portal)

Microsoft 및 파트너의 모니터링 솔루션은 [Azure Marketplace](https://azuremarketplace.microsoft.com)에서 사용할 수 있습니다. 다음 절차를 사용 하 여 사용 가능한 솔루션을 검색 하 고 설치할 수 있습니다. 솔루션을 설치할 때 솔루션이 설치되고 데이터가 수집될 [Log Analytics 작업 영역](../logs/manage-access.md)을 선택해야 합니다.

1. [구독에 대 한 솔루션 목록](#list-installed-monitoring-solutions)에서 **추가** 를 선택 합니다.
1. 솔루션을 찾아보거나 검색합니다. [이 검색 링크](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)를 사용할 수도 있습니다.
1. 원하는 모니터링 솔루션을 찾아 해당 설명을 자세히 읽습니다.
1. **만들기** 를 선택 하 여 설치 프로세스를 시작 합니다.
1. 메시지가 표시 되 면 Log Analytics 작업 영역을 지정 하 고 솔루션에 필요한 구성을 제공 합니다.

![Azure Marketplace에 대 한 솔루션을 보여 주는 스크린샷](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>커뮤니티에서 솔루션 설치

커뮤니티 구성원은 관리 솔루션을 Azure 빠른 시작 템플릿에 제출할 수 있습니다. 이러한 솔루션을 직접 설치 하거나 나중에 설치할 수 있도록 템플릿을 다운로드할 수 있습니다.

1. [Log Analytics 작업 영역 및 Automation 계정](#log-analytics-workspace-and-automation-account)에 설명된 프로세스에 따라 작업 영역 및 계정에 연결합니다.
2. [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)으로 이동합니다.
3. 관심 있는 솔루션을 검색합니다.
4. 해당 정보를 보려면 결과에서 솔루션을 선택합니다.
5. **Azure에 배포** 단추를 선택 합니다.
6. 솔루션의 모든 매개 변수에 대 한 값 외에도 리소스 그룹 및 위치와 같은 정보를 제공 하 라는 메시지가 표시 됩니다.
7. **구매** 를 선택 하 여 솔루션을 설치 합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prepare-your-environment"></a>환경 준비

1. Azure CLI를 설치합니다.

   CLI 참조 명령을 실행하기 전에 [Azure CLI를 설치](/cli/azure/install-azure-cli)해야 합니다. 원한다면 Azure Cloud Shell을 사용하여 이 문서의 단계를 완료할 수도 있습니다. Azure Cloud Shell은 브라우저를 통해 사용할 수 있는 대화형 셸 환경입니다. 다음 방법 중 하나를 사용 하 여 Cloud Shell를 엽니다.

   - [Cloud Shell 웹 페이지로](https://shell.azure.com)이동 합니다.

   - [Azure Portal](https://portal.azure.com)의 오른쪽 위 모퉁이에 있는 메뉴 모음에서 **Cloud Shell** 단추를 선택 합니다. 

1. 로그인합니다.

   CLI의 로컬 설치를 사용 하는 경우 [az login](/cli/azure/reference-index#az_login) 명령을 사용 하 여 로그인 합니다.  터미널에 표시된 단계에 따라 인증 프로세스를 완료합니다.

    ```azurecli
    az login
    ```

1. `log-analytics-solution` 확장을 설치합니다.

   `log-analytics-solution` 명령은 핵심 Azure CLI의 실험적 확장입니다. [Azure CLI 확장 사용](/cli/azure/azure-cli-extensions-overview?)의 확장 참조에 대해 자세히 알아보세요.

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   다음 경고가 표시될 수 있습니다.

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Azure CLI를 사용하여 솔루션 설치하기

솔루션을 설치할 때 솔루션이 설치되고 데이터가 수집될 [Log Analytics 작업 영역](../logs/manage-access.md)을 선택해야 합니다.  Azure CLI를 사용하는 경우, [az monitor log-analytics workspace](/cli/azure/monitor/log-analytics/workspace) 참조 명령을 사용하여 작업 영역을 관리합니다.  [Log Analytics 작업 영역 및 Automation 계정](#log-analytics-workspace-and-automation-account)에 설명된 프로세스에 따라 작업 영역 및 계정에 연결합니다.

[Az monitor log-analytics solution create](/cli/azure/monitor/log-analytics/solution) 명령을 사용 하 여 모니터링 솔루션을 설치 합니다.  대괄호 안에 있는 매개 변수는 선택적 요소입니다.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

OMSGallery/컨테이너의 계획 제품에 대 한 Log Analytics 솔루션을 만드는 코드 샘플은 다음과 같습니다.

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

1. Azure PowerShell을 설치합니다.

   Azure PowerShell 참조 명령을 실행하기 전에 [Azure PowerShell을 설치](/powershell/azure/install-az-ps)해야 합니다. 원한다면 Azure Cloud Shell을 사용하여 이 문서의 단계를 완료할 수도 있습니다. Azure Cloud Shell은 브라우저를 통해 사용할 수 있는 대화형 셸 환경입니다. 다음 방법 중 하나를 사용 하 여 Cloud Shell를 엽니다.

   - [Cloud Shell 웹 페이지로](https://shell.azure.com)이동 합니다.

   - [Azure Portal](https://portal.azure.com)의 오른쪽 위 모퉁이에 있는 메뉴 모음에서 **Cloud Shell** 단추를 선택 합니다.

   > [!IMPORTANT]
   > **MonitoringSolutions** PowerShell 모듈은 미리 보기 상태 이지만 cmdlet을 사용 하 여 별도로 설치 해야 합니다 `Install-Module` . 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 일부가 되며 기본적으로 Azure Cloud Shell 내에서 사용할 수 있습니다.

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. 로그인합니다.

   PowerShell의 로컬 설치를 사용 하는 경우 [커넥트 AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용 하 여 로그인 합니다. PowerShell에 표시된 단계에 따라 인증 프로세스를 완료합니다.

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Azure PowerShell을 사용하여 솔루션 설치하기

솔루션을 설치할 때 솔루션이 설치되고 데이터가 수집될 [Log Analytics 작업 영역](../logs/manage-access.md)을 선택해야 합니다. Azure PowerShell을 사용하는 경우, [Az.MonitoringSolutions](/powershell/module/az.monitoringsolutions) PowerShell 모듈에서 cmdlet을 사용하여 작업 영역을 관리합니다. 작업 [영역 및 Automation 계정 Log Analytics](#log-analytics-workspace-and-automation-account) 설명 된 프로세스에 따라 작업 영역 및 계정을 연결 합니다.

[New-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution) cmdlet을 사용하여 모니터링 솔루션을 설치합니다. 대괄호 안에 있는 매개 변수는 선택적 요소입니다.

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

다음 예에서는 Log Analytics 작업 영역에 대 한 모니터링 솔루션을 만듭니다.

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics 작업 영역 및 Automation 계정

모든 모니터링 솔루션에는 수집 된 데이터를 저장 하 고 로그 검색 및 보기를 호스트 하는 [Log Analytics 작업 영역이](../logs/manage-access.md) 필요 합니다. 일부 솔루션은 Runbook 및 관련 리소스를 포함할 [Automation 계정](../../automation/automation-security-overview.md)도 필요합니다. 작업 영역 및 계정은 다음 요구 사항을 충족 해야 합니다.

* 솔루션을 설치할 때마다 하나의 Log Analytics 작업 영역과 하나의 Automation 계정만 사용할 수 있습니다. 솔루션은 여러 작업 영역에 별도로 설치할 수 있습니다.
* 솔루션에서 Automation 계정을 요구하는 경우 Log Analytics 작업 영역과 Automation 계정이 서로 연결되어야 합니다. Log Analytics 작업 영역은 하나의 Automation 계정에만 연결할 수 있으며 Automation 계정은 하나의 Log Analytics 작업 영역에만 연결할 수 있습니다.

Azure Marketplace를 통해 솔루션을 설치 하는 경우 작업 영역 및 Automation 계정을 묻는 메시지가 표시 됩니다. 작업 영역과 계정이 아직 서로 연결되지 않았으면 둘 사이에 연결이 만들어집니다.

Log Analytics 작업 영역 및 Automation 계정 간의 링크를 확인 하려면 다음을 수행 합니다.

1. Azure Portal에서 Automation 계정을 선택합니다.
1. 메뉴의 **관련 리소스** 섹션으로 스크롤하여 **연결된 작업 영역** 을 선택합니다.
1. 작업 영역이 Automation 계정에 연결 된 경우이 페이지에는 연결 된 작업 영역이 나열 됩니다. 나열 된 작업 영역의 이름을 선택 하면 해당 작업 영역에 대 한 개요 페이지로 리디렉션됩니다.

## <a name="remove-a-monitoring-solution"></a>모니터링 솔루션 제거

### <a name="portal"></a>[포털](#tab/portal)

포털을 사용 하 여 설치 된 솔루션을 제거 하려면 [설치 된 솔루션 목록](#list-installed-monitoring-solutions)에서 해당 솔루션을 찾습니다. 솔루션의 이름을 선택 하 여 요약 페이지를 연 다음 **삭제** 를 선택 합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 설치 된 솔루션을 제거 하려면 [az monitor log-analytics solution delete](/cli/azure/monitor/log-analytics/solution#az_monitor_log_analytics_solution_delete) 명령을 사용 합니다.

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell를 사용 하 여 설치 된 솔루션을 제거 하려면 [AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) cmdlet을 사용 합니다.

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>다음 단계

* [Microsoft에서 모니터링 솔루션 목록](../monitor-reference.md)을 가져옵니다.
* 모니터링 솔루션에서 수집 된 데이터를 분석 하는 [쿼리를 만드는](../logs/log-query-overview.md) 방법에 대해 알아봅니다.
* [Azure Monitor에 대한 모든 Azure CLI 명령](/cli/azure/azure-cli-reference-for-monitor)을 참조하세요.
