---
title: Azure Load Balancer 모니터링
description: 여기에서 먼저 부하 분산 장치를 모니터링하는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: 62885e889c64c40c051f838178f9a96d4ac4a8ee
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291176"
---
# <a name="monitoring-load-balancer"></a>부하 분산 장치 모니터링

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 

이 문서에서는 Load Balancer에서 생성된 모니터링 데이터를 설명합니다. Load Balancer는 [Azure Monitor](../azure-monitor/overview.md)를 사용합니다. 이 기능을 사용하는 모든 Azure 서비스에 공통되는 Azure Monitor 기능에 익숙하지 않은 경우, [Azure Monitor로 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 읽어 보세요.

## <a name="load-balancer-insights"></a>Load Balancer 인사이트

Azure의 일부 서비스에는 서비스 모니터링을 위한 시작점을 제공하는 Azure Portal에 특별히 집중적으로 미리 작성된 모니터링 대시보드가 있습니다. 이 특수 대시보드를 “인사이트”라고 합니다.

Load Balancer 인사이트는 다음을 제공합니다.

* 기능적 종속성 보기
* 메트릭 대시보드
* 개요 탭
* 프런트 엔드 및 백 엔드 가용성 탭
* 데이터 처리량 탭
* 흐름 분포
* 연결 모니터
* 메트릭 정의

Load Balancer 인사이트에 관한 자세한 내용은 [인사이트를 사용하여 Azure Load Balancer 모니터링 및 구성](./load-balancer-insights.md)을 참조하세요.

## <a name="monitoring-data"></a>데이터 모니터링 

Load Balancer는 [Azure 리소스의 모니터링 데이터](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)에 설명된 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다. 

Load Balancer에서 생성된 메트릭과 로그 메트릭에 관한 자세한 내용은 [Load Balancer 모니터링 데이터 참조](monitor-load-balancer.md)를 참조하세요.

Load Balancer는 다음을 통해 추가 모니터링 데이터를 제공합니다.

* [상태 프로브](./load-balancer-custom-probe-overview.md)
* [Resource Health 상태](./load-balancer-standard-diagnostics.md#resource-health-status)
* [REST API](./load-balancer-query-metrics-rest-api.md)

## <a name="collection-and-routing"></a>수집 및 라우팅

플랫폼 메트릭 및 활동 로그는 자동으로 수집되고 저장되지만 진단 설정을 사용하여 다른 위치로 라우팅할 수 있습니다.  

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장되지 않습니다.

## <a name="creating-a-diagnostic-setting"></a>진단 설정 만들기

Azure Portal, PowerShell 또는 Azure CLI를 사용하여 진단 설정을 만들 수 있습니다.


일반 지침은 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요.

진단 설정을 만들 때 수집할 로그 범주를 지정합니다. Load Balancer 범주는 **AllMetrics** 입니다.

### <a name="portal"></a>포털

1. [Azure 포털](https://portal.azure.com)

2. 포털 맨 위에 있는 검색 상자에 **부하 분산 장치** 를 입력합니다.

3. 검색 결과에서 **부하 분산 장치** 를 선택합니다.

4. 부하 분산 장치를 선택합니다. 이 예제의 경우 **myLoadBalancer** 가 사용됩니다.

5. **myLoadBalancer** 의 **모니터링** 섹션에서 **진단 설정** 을 선택합니다.

6. **진단 설정** 에서 **+ 진단 설정 추가** 를 선택합니다.

7. **진단 설정** 에 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 진단 설정 이름 | 진단 설정의 이름을 입력합니다. |
    | **범주 세부 정보** |   |
    | 메트릭 | **AllMetrics** 를 선택합니다. |

8. **대상 세부 정보** 를 선택합니다. 일부 대상 옵션은 다음과 같습니다.
    * **Log Analytics에 보내기**
        * **구독** 및 **Log Analytics 작업 영역** 을 선택합니다.
    * **스토리지 계정에 보관**
        * **구독** 및 **스토리지 계정** 을 선택합니다.
    * **이벤트 허브로 스트림**
        * **구독**, **이벤트 허브 네임스페이스**, **이벤트 허브 이름(선택 사항)** , **이벤트 허브 정책 이름** 을 선택합니다.

9. **저장** 을 선택합니다.

### <a name="powershell"></a>PowerShell

Azure PowerShell에 로그인합니다.

```azurepowershell
Connect-AzAccount 
```

#### <a name="log-analytics-workspace"></a>Log Analytics 작업 영역

Log Analytics 작업 영역에서 진단 로그를 사용하려면 다음 명령을 입력합니다. 대괄호로 묶인 값을 원하는 값으로 바꿉니다.

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the workspace in a variable. ##
$wspara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-log-analytics-workspace-name>
}
$ws = Get-AzOperationalInsightsWorkspace @wspara
    
## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.id `
    -Name <your-diagnostic-setting-name> `
    -Enabled $true `
    -MetricCategory 'AllMetrics' `
    -WorkspaceId $ws.ResourceId
```

#### <a name="storage-account"></a>스토리지 계정

스토리지 계정에서 진단 로그를 사용하려면 다음 명령을 입력합니다. 대괄호로 묶인 값을 원하는 값으로 바꿉니다.

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the storage account in a variable. ##
$storpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-storage-account-name>
}
$storage = Get-AzStorageAccount @storpara
    
## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.id `
    -Name <your-diagnostic-setting-name> `
    -StorageAccountId $storage.id `
    -Enabled $true `
    -MetricCategory 'AllMetrics'
```

#### <a name="event-hub"></a>이벤트 허브

이벤트 허브 네임스페이스에서 진단 로그를 사용하려면 다음 명령을 입력합니다. 대괄호로 묶인 값을 원하는 값으로 바꿉니다.

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the event hub in a variable. ##
$hubpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-event-hub-name>
}
$eventhub = Get-AzEventHubNamespace @hubpara

## Place the event hub authorization rule in a variable. ##    
$hubrule = @{
    ResourceGroupName = 'myResourceGroup'
    Namespace = 'myeventhub8675'
}
$eventhubrule = Get-AzEventHubAuthorizationRule @hubrule

## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.Id `
    -Name 'myDiagSetting-event'`
    -EventHubName $eventhub.Name `
    -EventHubAuthorizationRuleId $eventhubrule.Id `
    -Enabled $true `
    -MetricCategory 'AllMetrics'
```
### <a name="azure-cli"></a>Azure CLI

Azure CLI에 로그인합니다.

```azurecli
az login
```

#### <a name="log-analytics-workspace"></a>Log Analytics 작업 영역

Log Analytics 작업 영역에서 진단 로그를 사용하려면 다음 명령을 입력합니다. 대괄호로 묶인 값을 원하는 값으로 바꿉니다.

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

wsid=$(az monitor log-analytics workspace show \
    --resource-group <your-resource-group> \
    --workspace-name <your-log-analytics-workspace-name> \
    --query id \
    --output tsv)
    
az monitor diagnostic-settings create \
    --name <your-diagnostic-setting-name> \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --workspace $wsid
```

#### <a name="storage-account"></a>스토리지 계정

스토리지 계정에서 진단 로그를 사용하려면 다음 명령을 입력합니다. 대괄호로 묶인 값을 원하는 값으로 바꿉니다.

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

storid=$(az storage account show \
        --name <your-storage-account-name> \
        --resource-group <your-resource-group> \
        --query id \
        --output tsv)
    
az monitor diagnostic-settings create \
    --name <your-diagnostic-setting-name> \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --storage-account $storid
```

#### <a name="event-hub"></a>이벤트 허브

이벤트 허브 네임스페이스에서 진단 로그를 사용하려면 다음 명령을 입력합니다. 대괄호로 묶인 값을 원하는 값으로 바꿉니다.

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

az monitor diagnostic-settings create \
    --name myDiagSetting-event \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --event-hub-rule /subscriptions/<your-subscription-id>/resourceGroups/<your-resource-group>/providers/Microsoft.EventHub/namespaces/<your-event-hub-namespace>/authorizationrules/RootManageSharedAccessKey
```

수집할 수 있는 메트릭 및 로그에 대해서는 다음 섹션에서 설명합니다.

## <a name="analyzing-metrics"></a>메트릭 분석

메트릭 탐색기를 사용하여 다른 Azure 서비스의 메트릭으로 Load Balancer에 대한 메트릭을 분석하려면 **Azure Monitor** 메뉴에서 **메트릭** 을 엽니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../azure-monitor/essentials/metrics-getting-started.md)을 참조하세요. 

Load Balancer에 대해 수집된 플랫폼 메트릭 목록은 [Load Balancer 모니터링 데이터 참조 메트릭](monitor-load-balancer-reference.md#metrics)을 참조하세요.  

[Azure Monitor에서 지원되는 모든 리소스 메트릭](../azure-monitor/essentials/metrics-supported.md) 목록을 참조하세요.

## <a name="analyzing-logs"></a>로그 분석

Azure Monitor Logs의 데이터는 테이블마다 고유한 자체 속성 집합이 있는 테이블에 저장됩니다.  

[활동 로그](../azure-monitor/essentials/activity-log.md)는 구독 수준 이벤트에 인사이트를 제공하는 플랫폼 로그의 유형입니다. 활동 로그는 독립적으로 보거나 Azure Monitor Logs로 라우팅할 수 있습니다. 여기서 Log Analytics를 사용하여 훨씬 더 복잡한 쿼리를 수행할 수 있습니다.  

Azure Monitor 로그에서 사용하고 Log Analytics에서 쿼리할 수 있는 테이블 목록은 [Load Balancer 모니터링 데이터 참조](monitor-load-balancer-reference.md#azure-monitor-logs-tables)를 참조하세요.  

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

> [!NOTE]
> 현재 부하 분산 장치 로그에서 데이터를 검색하지 못하게 하는 Kusto 쿼리 관련 문제가 있습니다.


## <a name="alerts"></a>경고

Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. 이를 통해 고객이 알기 전에 시스템 문제를 식별하고 해결할 수 있습니다. [메트릭](../azure-monitor/alerts/alerts-metric-overview.md), [로그](../azure-monitor/alerts/alerts-unified-log.md) 및 [활동 로그](../azure-monitor/alerts/activity-log-alerts.md)에서 경고를 설정할 수 있습니다. 서로 다른 형식의 경고에는 장점과 단점이 있습니다

Load Balancer에서 실행되는 애플리케이션을 만들거나 실행하는 경우 [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights)는 추가 유형의 경고를 제공할 수 있습니다.


다음 표에는 Load Balancer에 대한 일반적인 경고 규칙과 권장되는 경고 규칙이 나와 있습니다.

| 경고 유형 | 조건 | 설명  |
|:---|:---|:---|
| 사용할 수 없는 VM으로 인해 부하 분산 규칙을 사용할 수 없음 | 프런트 엔드 IP 주소와 프런트 엔드 포트(모든 알려진 값과 미래 값)별로 분할된 데이터 경로 가용성이 0과 같고 상태 프로브 상태가 0인 경우 경고를 발생시킴 | 이 경고는 구성된 부하 분산 규칙에 대한 데이터 경로 가용성이 구성된 상태 프로브에 의해 프로브 다운되는 연결된 백 엔드 풀의 모든 VM으로 인해 트래픽을 제공하지 않는지 여부를 확인합니다. 부하 분산 장치 [문제 해결 가이드](load-balancer-troubleshoot.md)를 검토하여 잠재적인 근본 원인을 조사합니다. |
| VM 가용성이 상당히 낮음 | 백 엔드 IP와 백 엔드 포트별로 분할된 상태 프로브 상태가 총 풀 크기의 사용자 정의 프로브 업 비율(즉, 25%가 프로브 업됨)과 같으면 경고를 발생시킴 | 이 경고는 트래픽을 제공하는 데 사용할 수 있는 VM이 필요한 것보다 적은지 확인합니다. |
| 인터넷 엔드포인트에 대한 아웃바운드 연결 실패 | 연결 상태 = 실패로 필터링된 SNAT 연결 수가 0보다 큰 경우 경고를 발생시킵니다. | 이 경고는 SNAT 포트가 고갈되고 VM이 아웃바운드 연결을 시작하지 못하는 경우에 발생합니다. |
| SNAT 고갈에 접근 | 사용된 SNAT 포트가 사용자 정의 수보다 큰 경우 경고를 발생시킵니다. | 이 경고에는 항상 동일한 수의 포트가 할당되는 정적 아웃바운드 구성이 필요합니다. 그런 다음, 이 경고는 일정 비율의 할당된 포트가 사용될 때 발생합니다. |

## <a name="next-steps"></a>다음 단계

- 부하 분산 장치에서 생성된 메트릭, 로그, 기타 중요한 값의 참조에 관해서는 [Load Balancer 모니터링 데이터 참조](monitor-load-balancer-reference.md)를 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.