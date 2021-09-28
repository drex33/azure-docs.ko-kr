---
title: 템플릿을 사용하여 Azure Service Bus 네임스페이스 만들기
description: Azure Resource Manager 템플릿을 사용하여 Service Bus 메시징 네임스페이스 만들기
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 09/27/2021
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0a60829ead884c9b76025580714f03d993101b79
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155290"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 Service Bus 네임스페이스 만들기

Azure Resource Manager 템플릿을 배포하여 Service Bus 네임스페이스를 만드는 방법을 알아봅니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다. 템플릿을 만드는 방법에 대한 자세한 내용은 [Azure Resource Manager 설명서](../azure-resource-manager/index.yml)를 참조하세요.

Service Bus 네임스페이스를 만드는 데 다음 템플릿을 사용할 수도 있습니다.

* [큐가 있는 Service Bus 네임스페이스 만들기](./service-bus-resource-manager-namespace-queue.md)
* [토픽 및 구독이 있는 Service Bus 네임스페이스 만들기](./service-bus-resource-manager-namespace-topic.md)
* [큐 및 권한 부여 규칙이 있는 Service Bus 네임스페이스 만들기](./service-bus-resource-manager-namespace-auth-rule.md)
* [토픽, 구독 및 규칙이 있는 Service Bus 네임스페이스 만들기](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="create-a-service-bus-namespace"></a>Service Bus 네임스페이스 만들기

이 빠른 시작에서는 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)의 [기존 Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.servicebus/servicebus-create-namespace/azuredeploy.json)을 사용합니다.

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/quickstarts/microsoft.servicebus/servicebus-create-namespace/azuredeploy.json)]

더 많은 샘플 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)에서 찾을 수 있습니다.

템플릿을 배포하여 서비스 버스 네임스페이스를 만들려면:

1. 다음 코드 블록에서 **사용해보기** 를 선택한 다음, 지침에 따라 Azure Cloud 셸에 로그인합니다.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.servicebus/servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    리소스 그룹 이름은 서비스 버스 네임스페이스 이름 끝에 **rg** 가 추가된 것입니다.

2. **복사** 를 선택하여 PowerShell 스크립트를 복사합니다.
3. 셸 콘솔 창을 마우스 오른쪽 단추로 클릭하고 **붙여넣기** 를 선택합니다.

이벤트 허브를 만드는 데 몇 분 정도 걸립니다.

## <a name="verify-the-deployment"></a>배포 확인

배포된 서비스 버스 네임스페이스를 확인하려면 Azure Portal에서 리소스 그룹을 열거나 다음 Azure PowerShell 스크립트를 사용하면 됩니다. Cloud 셸이 아직 열려 있으면 다음 스크립트의 첫 번째 및 두 번째 줄을 복사/실행할 필요가 없습니다.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

이 자습서에서는 Azure PowerShell을 사용하여 템플릿을 배포합니다. 다른 템플릿 배포 방법은 다음을 참조하세요.

* [Azure Portal 사용](../azure-resource-manager/templates/deploy-portal.md)
* [Azure CLI 사용](../azure-resource-manager/templates/deploy-cli.md)
* [REST API 사용](../azure-resource-manager/templates/deploy-rest.md)

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다. Cloud 셸이 아직 열려 있으면 다음 스크립트의 첫 번째 및 두 번째 줄을 복사/실행할 필요가 없습니다.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Service Bus 네임스페이스를 만들었습니다. 다른 빠른 시작을 참조하여 큐, 토픽/구독을 만들고 사용하는 방법에 대해 알아봅니다.

* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 큐 항목 시작](service-bus-dotnet-how-to-use-topics-subscriptions.md)
