---
title: Network Watcher - Azure Resource Manager 템플릿을 사용하여 NSG 흐름 로그 만들기
description: Azure Resource Manager 템플릿 및 PowerShell을 사용하여 NSG 흐름 로그를 쉽게 설정할 수 있습니다.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.custom: fasttrack-edit, devx-track-azurepowershell
ms.openlocfilehash: 833a8ca21644368cf0350d92b787676baf0c4320
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133571051"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿에서 NSG 흐름 로그 구성

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure 리소스 관리자](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)는 [인프라를 코드로 관리](/devops/deliver/what-is-infrastructure-as-code)하는 Azure의 기본적이고 강력한 방법입니다.

이 문서에서는 Azure Resource Manager 템플릿 및 Azure PowerShell 사용하여 프로그래밍 방식으로 [NSG](./network-watcher-nsg-flow-logging-overview.md) Flow 로그를 사용하도록 설정하는 방법을 보여 줍니다. 먼저 NSG 흐름 로그 개체의 속성에 대한 개요를 제공하고 몇 가지 샘플 템플릿을 제공합니다. 그런 다음 로컬 PowerShell 인스턴스를 사용하여 템플릿을 배포합니다.


## <a name="nsg-flow-logs-object"></a>NSG 흐름 로그 개체

모든 매개 변수를 포함하는 NSG 흐름 로그 개체는 다음과 같습니다.
속성에 대한 전체 개요를 보려면 [NSG 흐름 로그 템플릿 참조](/azure/templates/microsoft.network/networkwatchers/flowlogs#retentionpolicyparameters)를 참조하세요.

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Microsoft. Network/networkWatchers/flowLogs 리소스를 만들려면 위의 JSON을 템플릿의 리소스 섹션에 추가합니다.


## <a name="creating-your-template"></a>템플릿 만들기

Azure Resource Manager 템플릿을 처음 사용하는 경우 아래 링크를 사용하여 자세히 알아볼 수 있습니다.

* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
* [자습서: 첫 번째 Azure Resource Manager 템플릿 만들기 및 배포](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)


다음은 NSG 흐름 로그를 설정하는 전체 템플릿의 두 가지 예제입니다.

**예제 1**:  최소 매개 변수가 전달된 위의 가장 간단한 버전입니다. 아래 템플릿에서는 대상 NSG에 대한 NSG 흐름 로그를 사용하도록 설정하고 지정된 스토리지 계정에 저장합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * 리소스 이름은 "부모 리소스_자식 리소스" 형식을 사용합니다. 여기서 부모 리소스는 지역 Network Watcher 인스턴스입니다(형식: NetworkWatcher_RegionName. 예제: NetworkWatcher_centraluseuap)
> * targetResourceID는 대상 NSG의 리소스 ID입니다.
> * storageId는 대상 스토리지 계정의 리소스 ID입니다.

**예제 2**: 다음 템플릿은 NSG 흐름 로그(버전 2)를 5일 동안 보존하도록 설정합니다. 처리 간격이 10분인 트래픽 분석을 사용하도록 설정합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {
          "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
          }
        },
        "retentionPolicy": {
          "days": 5,
          "enabled": true
        },
        "format": {
          "type": "JSON",
          "version": 2
        }
      }
    }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 배포

이 자습서에서는 흐름 로깅을 사용할 수 있는 기존 리소스 그룹 및 NSG가 있는 것으로 가정합니다.
위의 예제 템플릿 중 하나를 `azuredeploy.json`으로 로컬로 저장할 수 있습니다. 구독에서 유효한 리소스를 가리키도록 속성 값을 업데이트합니다.

템플릿을 배포하려면 PowerShell에서 다음 명령을 실행합니다.
```azurepowershell
$context = Get-AzSubscription -SubscriptionId <SubscriptionId>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> 위의 명령은 NSG를 포함하는 리소스 그룹이 아닌 NetworkWatcherRG 리소스 그룹에 리소스를 배포합니다.


## <a name="verifying-your-deployment"></a>배포 확인

배포가 성공했는지 확인하는 몇 가지 방법이 있습니다. PowerShell 콘솔에 "ProvisioningState"가 "Succeeded"로 표시되어야 합니다. 또한 [NSG 흐름 로그 포털 페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)를 방문하여 변경 내용을 확인할 수 있습니다. 배포에 문제가 있는 경우 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../azure-resource-manager/templates/common-deployment-errors.md)을 살펴봅니다.

## <a name="deleting-your-resource"></a>리소스 삭제
Azure에서는 "전체" 배포 모드를 통해 리소스를 삭제할 수 있습니다. 흐름 로그 리소스를 삭제하려면 삭제하려는 리소스를 포함하지 않고 배포를 전체 모드로 지정합니다. [전체 배포 모드](../azure-resource-manager/templates/deployment-modes.md#complete-mode)에 대해 자세히 알아봅니다.

## <a name="next-steps"></a>다음 단계

다음을 사용하여 NSG 흐름 데이터를 시각화하는 방법을 알아봅니다.
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [오픈 소스 도구](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure 트래픽 분석](./traffic-analytics.md)
