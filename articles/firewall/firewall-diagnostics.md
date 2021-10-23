---
title: Azure Firewall 로그 및 메트릭 모니터링
description: 이 문서에서는 Azure Firewall 로그 및 메트릭을 사용하도록 설정하고 관리하는 방법을 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 10/22/2021
ms.author: victorh
ms.openlocfilehash: d421986ebeb308efb0e076b1a80f537d49082ec5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242352"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Azure Firewall 로그 및 메트릭 모니터링

방화벽 로그를 사용하여 Azure Firewall을 모니터링할 수 있습니다. 또한 Azure Firewall 리소스에서 작업을 감사하려면 활동 로그를 사용할 수 있습니다. 메트릭을 사용하여 포털에서 성능 카운터를 볼 수 있습니다.

이러한 로그 중 일부는 포털을 통해 액세스할 수 있습니다. 로그를 [Azure Monitor 로그](../azure-monitor/insights/azure-networking-analytics.md), 스토리지 및 Event Hubs로 보내고 Azure Monitor 로그 또는 Excel 및 Power BI와 같은 다른 도구에서 분석합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 [Azure Firewall 로그 및 메트릭](logs-and-metrics.md)에서 Azure Firewall에 사용할 수 있는 진단 로그 및 메트릭에 대한 개요를 읽어보아야 합니다.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Azure Portal을 통한 진단 로깅 사용

진단 로깅을 켜는 이 절차를 완료한 후 데이터가 로그에 표시되는 데 몇 분 정도 걸릴 수 있습니다. 처음에 아무 것도 표시되지 않으면 몇 분 후에 다시 확인합니다.

1. Azure Portal에서 방화벽 리소스 그룹을 열고 방화벽을 선택합니다.
2. **모니터링** 아래에서 **진단 설정** 을 선택합니다.

   Azure Firewall의 경우 네 개의 서비스 관련 로그를 사용할 수 있습니다.

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallDnsProxy


3. **진단 설정 추가** 를 선택합니다. **진단 설정** 페이지에서는 진단 로그에 대한 설정을 제공합니다.
5. 이 예제에서 Azure Monitor 로그가 로그를 저장하므로 이름에 **방화벽 로그 분석** 을 입력합니다.
6. **로그** 에서 **AzureFirewallApplicationRule**, **AzureFirewallNetworkRule** 및 **AzureFirewallDnsProxy** 를 선택하여 로그를 수집합니다.
7. **로그 분석에 보내기** 를 선택하여 작업 영역을 구성합니다.
8. 구독을 선택합니다.
9. **저장** 을 선택합니다.

## <a name="enable-diagnostic-logging-by-using-powershell"></a>PowerShell을 사용하여 진단 로깅을 사용하도록 설정

활동 로깅은 모든 Resource Manager 리소스에 대해 사용하도록 설정됩니다. 이러한 로그를 통해 사용 가능한 데이터 수집을 시작하려면 이벤트 로깅을 사용하도록 설정해야 합니다.

PowerShell을 사용하여 진단 로깅을 사용하도록 설정하려면 다음 단계를 따릅니다.

1. 로그 데이터를 저장할 로그 분석 작업 영역의 리소스 ID를 적어 둡니다. 이 값은 다음과 같은 형식입니다.

   `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>`

   구독의 모든 작업 영역을 사용할 수 있습니다. Azure Portal을 사용하여 이 정보를 찾을 수 있습니다. 정보는 리소스 **속성** 페이지에 있습니다.

2. 방화벽의 리소스 ID를 적어둡니다. 이 값은 다음과 같은 형식입니다.

   `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>`

   포털을 사용하여 이 정보를 찾을 수 있습니다.

3. 다음 PowerShell cmdlet을 사용하여 모든 로그 및 메트릭에 대해 진단 로깅을 사용하도록 설정합니다.

   ```azurepowershell
      $diagSettings = @{
      Name = 'toLogAnalytics'
      ResourceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      WorkspaceId = '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      Enabled = $true
      }
   Set-AzDiagnosticSetting  @diagSettings 
   ```

## <a name="enable-diagnostic-logging-by-using-the-azure-cli"></a>Azure CLI를 사용하여 진단 로깅을 사용하도록 설정

활동 로깅은 모든 Resource Manager 리소스에 대해 사용하도록 설정됩니다. 이러한 로그를 통해 사용 가능한 데이터 수집을 시작하려면 이벤트 로깅을 사용하도록 설정해야 합니다.

Azure CLI를 사용하여 진단 로깅을 사용하도록 설정하려면 다음 단계를 따릅니다.

1. 로그 데이터를 저장할 로그 분석 작업 영역의 리소스 ID를 적어 둡니다. 이 값은 다음과 같은 형식입니다.

   `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>`

   구독의 모든 작업 영역을 사용할 수 있습니다. Azure Portal을 사용하여 이 정보를 찾을 수 있습니다. 정보는 리소스 **속성** 페이지에 있습니다.

2. 방화벽의 리소스 ID를 적어둡니다. 이 값은 다음과 같은 형식입니다.

   `/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>`

   포털을 사용하여 이 정보를 찾을 수 있습니다.

3. 다음 Azure CLI 명령을 사용하여 모든 로그 및 메트릭에 대해 진단 로깅을 사용하도록 설정합니다.

   ```azurecli
      az monitor diagnostic-settings create -n 'toLogAnalytics'
      --resource '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name>'
      --workspace '/subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
      --logs '[{\"category\":\"AzureFirewallApplicationRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallNetworkRule\",\"Enabled\":true}, {\"category\":\"AzureFirewallDnsProxy\",\"Enabled\":true}]' 
      --metrics '[{\"category\": \"AllMetrics\",\"enabled\": true}]'
   ```

## <a name="view-and-analyze-the-activity-log"></a>활동 로그 보기 및 분석

다음 방법 중 하나를 사용하여 활동 로그 데이터를 확인하고 분석할 수 있습니다.

* **Azure 도구** - Azure PowerShell, Azure CLI, Azure REST API 또는 Azure Portal을 통해 활동 로그에서 정보를 검색합니다. 각 방법에 대한 단계별 지침은 [Resource Manager의 활동 작업](../azure-monitor/essentials/activity-log.md) 문서에 자세히 나와 있습니다.
* **Power BI** - [Power BI](https://powerbi.microsoft.com/pricing) 계정이 아직 없는 경우 무료로 사용해볼 수 있습니다. [Power BI용 Azure Activity Logs 콘텐츠 팩](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)을 사용하여 미리 구성된 대시보드를 그대로 사용하거나 사용자 지정하여 데이터를 분석할 수 있습니다.
* **Azure Sentinel**: Azure Firewall 로그를 Azure Sentinel에 연결하여 통합 문서에서 로그 데이터를 볼 수 있도록 설정하고, 이 데이터를 사용하여 사용자 지정 경고를 만들고, 이 경고를 통합하여 조사를 개선할 수 있습니다. Azure Sentinel의 Azure Firewall 데이터 커넥터는 현재 공용 미리 보기로 제공됩니다. 자세한 내용은 [Azure Firewall에서 데이터 연결](../sentinel/data-connectors-reference.md#azure-firewall)을 참조하세요.

   개요는 Mohit Kumar의 다음 비디오를 참조하십시오.
   > [!VIDEO https://www.microsoft.com/videoplayer/embed/RWI4nn]


## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>네트워크 및 애플리케이션 규칙 로그 보기 및 분석

[Azure Firewall 통합 문서](firewall-workbook.md)는 Azure Firewall 데이터를 분석할 수 있도록 유연한 캔버스를 제공합니다. 이 캔버스를 사용하여 Azure Portal 내에서 풍부한 시각적 보고서를 만들 수 있습니다. Azure에 걸쳐 배포된 여러 방화벽을 탭하여 통합된 대화형 환경으로 결합할 수 있습니다.

스토리지 계정에 연결하고 액세스 및 성능 로그에 대한 JSON 로그 항목을 검색할 수도 있습니다. JSON 파일을 다운로드한 후 CSV로 변환하여 Excel, Power BI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다.

> [!TIP]
> Visual Studio를 익숙하게 사용할 수 있고 C#에서 상수 및 변수에 대한 값 변경에 대한 기본 개념이 있는 경우 GitHub에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter)를 사용할 수 있습니다.

## <a name="view-metrics"></a>메트릭 보기
Azure Firewall로 이동합니다. **모니터링** 아래에서 **메트릭** 을 선택합니다. 사용 가능한 값을 보려면 **메트릭** 드롭다운 목록을 선택합니다.

## <a name="next-steps"></a>다음 단계

로그를 수집하도록 방화벽을 구성했으므로 Azure Monitor 로그를 살펴보고 데이터를 볼 수 있습니다.

- [Azure Firewall 통합 문서를 사용하여 로그 모니터링](firewall-workbook.md)

- [Azure Monitor 로그의 네트워킹 모니터링 솔루션](../azure-monitor/insights/azure-networking-analytics.md)