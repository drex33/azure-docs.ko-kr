---
title: Azure 템플릿을 사용하여 Service Bus 권한 부여 규칙 만들기
description: Azure Resource Manager 템플릿을 사용하여 네임스페이스 및 큐에 대한 Service Bus 권한 부여 규칙 만들기
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 09/27/2021
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6c1f1f4961a33e818d16642b6ef97fc9a709257e
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155309"
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 네임스페이스 및 큐에 대한 Service Bus 권한 부여 규칙 만들기

이 문서에서는 Service Bus 네임스페이스 및 큐에 대한 [권한 부여 규칙](service-bus-authentication-and-authorization.md#shared-access-signature)을 만드는 Azure Resource Manager 템플릿을 사용하는 방법을 보여 줍니다. 이 문서는 어떤 리소스를 배포할지 지정하는 방법 및 배포를 실행할 때 지정되는 매개 변수를 정의하는 방법을 설명합니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다.

템플릿을 만들기에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성][Authoring Azure Resource Manager templates]을 참조하세요.

전체 템플릿은 GitHub에서 [Service Bus 권한 부여 규칙 템플릿][Service Bus auth rule template]을 참조하세요.

> [!NOTE]
> 다음 Azure Resource Manager 템플릿은 다운로드하여 배포할 수 있습니다.
>
> * [Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace.md)
> * [큐가 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-queue.md)
> * [토픽 및 구독이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-topic.md)
> * [토픽, 구독 및 규칙이 있는 Service Bus 네임스페이스 만들기](service-bus-resource-manager-namespace-topic-with-rule.md)
>
> 최신 템플릿을 확인하려면 [Azure 빠른 시작 템플릿][Azure Quickstart Templates] 갤러리를 방문하여 **Service Bus** 를 검색합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>배포할 항목

이 템플릿을 사용하여 네임스페이스 및 메시징 엔터티(이 경우에는 큐)에 대한 Service Bus 권한 부여 규칙을 배포합니다.

이 템플릿은 인증에 [SAS(공유 액세스 서명)](service-bus-sas.md)를 사용합니다. SAS를 사용하면 애플리케이션을 네임스페이스 또는 특정 권한이 연관된 메시징 엔터티(큐 또는 토픽)에서 구성된 액세스 키를 사용하여 Service Bus에 인증할 수 있습니다. 그런 다음 이 키를 사용하여 클라이언트가 Service Bus를 인증하는 데 차례로 사용할 수 있는 SAS 토큰을 생성할 수 있습니다.

배포를 자동으로 실행하려면 다음 단추를 클릭합니다.

[![Azure에 배포](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.servicebus%2Fservicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>매개 변수

Azure 리소스 관리자와 함께 템플릿을 배포할 때 지정하고자 하는 값으로 매개 변수를 정의합니다. 템플릿은 모든 매개 변수 값이 포함된 `Parameters` 라는 섹션을 포함합니다. 배포하는 프로젝트에 따라 또는 환경에 따라 달라지는 이러한 값에 대한 매개 변수를 정의해야 합니다. 항상 동일하게 유지되는 값으로 매개 변수를 정의하지 마세요. 각 매개 변수 값은 배포되는 리소스를 정의하는 템플릿에 사용됩니다.

템플릿은 다음 매개 변수를 정의합니다.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

만들 Service Bus 네임스페이스 이름입니다.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName

네임스페이스에 대한 권한 부여 규칙 이름입니다.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

Service Bus 네임스페이스에 있는 큐의 이름입니다.

```json
"serviceBusQueueName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

템플릿의 Service Bus API 버전입니다.

```json
"serviceBusApiVersion": {
       "type": "string",
       "defaultValue": "2017-04-01",
       "metadata": {
           "description": "Service Bus ApiVersion used by the template"
       }
```

## <a name="resources-to-deploy"></a>배포할 리소스

**메시징** 형식의 표준 Service Bus 네임스페이스와 네임스페이스 및 엔터티에 대한 Service Bus 권한 부여 규칙을 만듭니다.

```json
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "Standard",
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

JSON 구문 및 속성의 경우 [네임스페이스](/azure/templates/microsoft.servicebus/namespaces), [큐](/azure/templates/microsoft.servicebus/namespaces/queues) 및 [AuthorizationRules](/azure/templates/microsoft.servicebus/namespaces/authorizationrules)를 참조하세요.

## <a name="commands-to-run-deployment"></a>배포 실행 명령

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/quickstarts/microsoft.servicebus/servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/quickstarts/microsoft.servicebus/servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>다음 단계

이제 Azure Resource Manager를 사용하여 리소스를 만들고 배포했으므로 다음 문서를 참조하여 이러한 리소스를 관리하는 방법에 대해 알아봅니다.

* [PowerShell을 사용하여 Service Bus 관리](./service-bus-manage-with-ps.md)
* [Service Bus 탐색기로 Service Bus 리소스 관리](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Service Bus 인증 및 권한 부여](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/resources/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.servicebus/servicebus-create-authrule-namespace-and-queue/