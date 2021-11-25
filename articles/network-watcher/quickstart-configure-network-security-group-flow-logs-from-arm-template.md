---
title: '빠른 시작: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 네트워크 보안 그룹 흐름 로그 구성'
description: ARM 템플릿(Azure Resource Manager 템플릿) 및 Azure PowerShell을 사용하여 NSG(네트워크 보안 그룹) 흐름 로그를 프로그래밍 방식으로 사용하도록 설정하는 방법을 알아봅니다.
services: network-watcher
author: damendo
ms.author: damendo
ms.date: 01/07/2021
ms.topic: quickstart
ms.service: network-watcher
ms.custom: devx-track-azurepowershell, subject-armqs, mode-other
ms.openlocfilehash: fa0c4f8a5d3f3eecd15e8c378576908e2bb0ddd5
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133045789"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 네트워크 보안 그룹 흐름 로그 구성

이 빠른 시작에서는 [ARM 템플릿(Azure Resource Manager 템플릿)](../azure-resource-manager/management/overview.md) 및 Azure PowerShell을 사용하여 [NSG(네트워크 보안 그룹) 흐름 로그](network-watcher-nsg-flow-logging-overview.md)를 사용하도록 설정하는 방법을 알아봅니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

NSG 흐름 로그 개체의 속성에 대한 개요부터 시작합니다. 샘플 템플릿을 제공합니다. 그런 다음, 로컬 Azure PowerShell 인스턴스를 사용하여 템플릿을 배포합니다.

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fnetworkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/networkwatcher-flowlogs-create/)에서 가져온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/networkwatcher-flowLogs-create/azuredeploy.json":::

이러한 리소스는 템플릿에 정의되어 있습니다.

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>NSG 흐름 로그 개체

다음 코드는 NSG 흐름 로그 개체와 해당 매개 변수를 보여줍니다. `Microsoft.Network/networkWatchers/flowLogs` 레코드를 만들려면 템플릿의 리소스 섹션에 다음 코드를 추가합니다.

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

NSG 흐름 로그 개체 속성에 대한 전체 개요는 [Microsoft.Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs)를 참조하세요.

## <a name="create-your-template"></a>템플릿 만들기

ARM 템플릿을 처음으로 사용하는 경우 다음 문서에서 ARM 템플릿에 대해 자세히 알아보세요.

- [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- 첫 번째 ARM 템플릿 만들기 및 배포[

다음 예제는 전체 템플릿입니다. 템플릿의 가장 간단한 버전이기도 합니다. 이 예제에는 NSG 흐름 로그를 설정하기 위해 전달되는 최소 매개 변수가 포함되어 있습니다. 더 많은 예제를 보려면 [Azure Resource Manager 템플릿에서 NSG 흐름 로그 구성](network-watcher-nsg-flow-logging-azure-resource-manager.md) 개요 문서를 참조하세요.

### <a name="example"></a>예

다음은 NSG에 흐름 로그를 사용하도록 설정한 다음, 특정 스토리지 계정에 로그를 저장하는 템플릿입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> - 리소스 이름은 _ParentResource_ChildResource_ 형식을 사용합니다. 이 예제에서 부모 리소스는 지역 Azure Network Watcher 인스턴스입니다.
>    - **Format**: NetworkWatcher_RegionName
>    - **예**: NetworkWatcher_centraluseuap
> - `targetResourceId`는 대상 NSG의 리소스 ID입니다.
> - `storageId`는 대상 스토리지 계정의 리소스 ID입니다.

## <a name="deploy-the-template"></a>템플릿 배포

이 자습서에서는 흐름 로깅을 사용할 수 있는 기존 리소스 그룹 및 NSG가 있는 것으로 가정합니다.

이 문서에 표시된 모든 예제 템플릿을 로컬에 *azuredeploy.json* 으로 저장할 수 있습니다. 구독의 유효한 리소스를 가리키도록 속성 값을 업데이트합니다.

템플릿을 배포하려면 PowerShell에서 다음 명령을 실행합니다.

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> 이 명령은 NSG를 포함하는 리소스 그룹이 아닌 NetworkWatcherRG 리소스 그룹에 리소스를 배포합니다.

## <a name="validate-the-deployment"></a>배포 유효성 검사

배포가 성공했는지 확인하는 두 가지 방법이 있습니다.

- PowerShell 콘솔에서 `ProvisioningState`가 `Succeeded`로 표시되는지 확인합니다.
- [NSG 흐름 로그 포털 페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)로 이동하여 변경 내용을 확인합니다.

배포에 문제가 있는 경우 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../azure-resource-manager/templates/common-deployment-errors.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

전체 배포 모드를 사용하여 Azure 리소스를 삭제할 수 있습니다. 흐름 로그 리소스를 삭제하려면 삭제하려는 리소스를 포함하지 않고 배포를 전체 모드로 지정합니다. [전체 배포 모드](../azure-resource-manager/templates/deployment-modes.md#complete-mode)에 대해 읽어보세요.

Azure Portal에서 다음과 같이 NSG 흐름 로그를 사용하지 않도록 설정할 수도 있습니다.

1. Azure Portal에 로그인합니다.
1. **모든 서비스** 를 선택합니다. **필터** 상자에 **network watcher** 를 입력합니다. 검색 결과에서 **Network Watcher** 를 선택합니다.
1. **로그** 에서 **NSG 흐름 로그** 를 선택합니다.
1. NSG 목록에서 흐름 로그를 사용하지 않도록 설정할 NSG를 선택합니다.
1. **흐름 로그 설정** 에서 **끄기** 를 선택합니다.
1. **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ARM 템플릿을 통해 NSG 흐름 로그를 사용하도록 설정하는 방법을 알아보았습니다. 다음으로 다음 옵션 중 하나를 사용하여 NSG 흐름 데이터를 시각화하는 방법을 알아보세요.

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [오픈 소스 도구](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure 트래픽 분석](traffic-analytics.md)
