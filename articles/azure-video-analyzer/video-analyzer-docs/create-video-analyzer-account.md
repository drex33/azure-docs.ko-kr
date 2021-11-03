---
title: Azure Video Analyzer 계정 만들기
description: 이 항목에서는 Azure Video Analyzer의 계정을 만드는 방법을 설명합니다.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 10/31/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3dc43f2d6882b4a1c22dee331e780a91c8425af7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031198"
---
# <a name="create-a-video-analyzer-account"></a>Video Analyzer 계정을 만듭니다.

Azure Video Analyzer를 사용하려면 먼저 Video Analyzer 계정을 만들어야 합니다. 계정은 스토리지 계정 및 하나 이상의 [UAMI(사용자 할당 관리 ID)와][docs-uami]연결되어야 합니다. UAMI에는 스토리지 계정에 대한 Storage [Blob 데이터 기여자][docs-storage-access] 역할 및 [읽기][docs-role-reader] 권한자 역할의 권한이 있어야 합니다. 필요에 따라 Video Analyzer 계정과 IoT Hub 연결할 수 있습니다. Video Analyzer 에지 모듈을 [투명 게이트웨이로](./cloud/use-remote-device-adapter.md)사용하는 경우에 필요합니다. 이렇게 하는 경우 [기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할 권한이 있는 UAMI를 추가해야 합니다. 스토리지 계정과 IoT Hub 또는 별도의 UAMI에 동일한 UAMI를 사용할 수 있습니다.

이 문서에서는 새 Video Analyzer 계정을 만드는 단계를 설명합니다. Azure Portal 또는 [ARM(Azure Resource Manager) 템플릿][docs-arm-template]을 사용할 수 있습니다. 사용하려는 방법에 대한 탭을 선택합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[포털](#tab/portal/)

[!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Azure Portal에서 Video Analyzer 계정 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 상단의 검색 창을 사용하여 **Video Analyzer** 를 입력합니다.
1. *서비스* 에서 *Video Analyzer* 를 클릭합니다.
1. **만들기** 를 클릭합니다.
1. **Video Analyzer 계정 만들기** 섹션에서 필수 값을 입력합니다.

    | 이름 | 설명 |
    | ---|---|
    |**구독**|둘 이상의 구독이 있는 경우 액세스 권한이 있는 Azure 구독 목록에서 하나를 선택합니다.|
    |**리소스 그룹**|기존 리소스를 선택하거나 새 리소스를 만듭니다. 리소스 그룹은 수명 주기, 권한 및 정책을 공유하는 리소스의 컬렉션입니다. [여기](../../azure-resource-manager/management/overview.md#resource-groups)를 참조하세요.|
    |**Video Analyzer 계정 이름**|새 Video Analyzer 계정의 이름을 입력합니다. Video Analyzer 계정 이름은 공백 없이 모두 소문자로 이루어진 3-24자의 숫자 또는 문자입니다.|
    |**위치**|Video Analyzer 계정에 대한 비디오 및 메타데이터 레코드를 저장하는 데 사용할 지리적 지역을 선택합니다. 사용 가능한 Video Analyzer 지역만 드롭다운 목록 상자에 표시됩니다. |
    |**스토리지 계정**|Video Analyzer 계정에 대한 비디오 콘텐츠의 Blob Storage를 제공하려면 스토리지 계정을 선택합니다. Video Analyzer 계정과 동일한 지역의 기존 스토리지 계정을 선택하거나 새 스토리지 계정을 만들 수 있습니다. 동일한 지역에 새 스토리지 계정이 생성됩니다. 스토리지 계정 이름에 대한 규칙은 Video Analyzer 계정의 경우와 같습니다.<br/>|
    |**관리 ID**|새 Video Analyzer 계정에서 스토리지 계정에 액세스하는 데 사용할 사용자 할당 관리 ID를 선택합니다. 기존 사용자 할당 관리 ID를 선택하거나 새로 만들 수 있습니다. 사용자 할당 관리 ID에 스토리지 계정에 대한 [Storage Blob 데이터 기여자][docs-storage-access] 및 [읽기 권한자][docs-role-reader] 역할이 할당됩니다.

1. 양식 아래쪽에서 **검토 + 만들기** 를 클릭합니다.

### <a name="post-deployment-steps"></a>배포 후 단계
Video Analyzer 계정에 기존 IoT Hub 연결하도록 선택할 수 있으며, 기존 UAMI가 필요합니다.

1. 리소스 배포가 완료되면 **리소스로 이동을** 클릭합니다.
1. 설정에서 **IoT Hub** 선택한 **다음, 연결** 을 클릭합니다.  연결 **IoT Hub** 구성 플라이아웃 블레이드에서 필요한 값을 입력합니다.
     - 구독 - IoT Hub 만든 Azure 구독 이름을 선택합니다.
     - IoT Hub - 원하는 IoT Hub 선택합니다.
     - 관리 ID - IoT Hub 액세스하는 데 사용할 기존 UAMI를 선택합니다.
1. **저장을** 클릭하여 IoT Hub Video Analyzer 계정에 연결합니다.

## <a name="template"></a>[템플릿](#tab/template/)

[!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

### <a name="create-a-video-analyzer-account-using-a-template"></a>템플릿을 사용하여 Video Analyzer 계정 만들기

다음 리소스는 템플릿에 정의되어 있습니다.

- [**Microsoft.Media/videoAnalyzers**](/azure/templates/Microsoft.Media/videoAnalyzers): Video Analyzer의 계정 리소스입니다.
- [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts): 비디오 및 메타데이터를 저장하기 위해 Video Analyzer에서 사용할 스토리지 계정입니다.
- [**Microsoft.ManagedIdentity / userAssignedIdentities**](/azure/templates/Microsoft.ManagedIdentity/userAssignedIdentities): Video Analyzer가 스토리지에 액세스하는 데 사용할 사용자 할당 관리 ID입니다.
- [**Microsoft.Storage/storageAccounts/providers/roleAssignments**](/azure/templates/microsoft.authorization/roleassignments): Video Analyzer가 스토리지 계정에 액세스할 수 있도록 하는 역할 할당입니다.

<!-- TODO replace with a reference like this:
:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.compute/vm-simple-linux/azuredeploy.json":::
-->

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "namePrefix": {
            "metadata": {
                "description": "Used to qualify the names of all of the resources created in this template."
            },
            "defaultValue": "avasample",
            "type": "string",
            "minLength": 3,
            "maxLength": 13
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('namePrefix'),uniqueString(resourceGroup().id))]",
        "accountName": "[concat(parameters('namePrefix'),uniqueString(resourceGroup().id))]",
        "managedIdentityName": "[concat(parameters('namePrefix'),'-',resourceGroup().name,'-storage-access-identity')]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "deploy-storage-and-identity",
            "properties": {
                "mode": "Incremental",
                "expressionEvaluationOptions": {
                    "scope": "Inner"
                },
                "parameters": {
                    "namePrefix": {
                        "value": "[parameters('namePrefix')]"
                    },
                    "managedIdentityName": {
                        "value": "[variables('managedIdentityName')]"
                    }
                },
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "namePrefix": {
                            "type": "string"
                        },
                        "managedIdentityName": {
                            "type": "string"
                        }
                    },
                    "variables": {
                        "storageAccountName": "[concat(parameters('namePrefix'),uniqueString(resourceGroup().id))]",
                        "managedIdentityName": "[parameters('managedIdentityName')]",
                        "storageBlobDataContributorAssignment": "[guid('Storage Blob Data Contributor',variables('managedIdentityName'))]",
                        "storageBlobDataContributorDefinitionId": "[concat(resourceGroup().id, '/providers/Microsoft.Authorization/roleDefinitions/', 'ba92f5b4-2d11-453d-a403-e96b0029c9fe')]",
                        "readerAssignment": "[guid('Reader',variables('managedIdentityName'))]",
                        "readerDefinitionId": "[concat(resourceGroup().id, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
                    },
                    "resources": [
                        {
                            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
                            "name": "[variables('managedIdentityName')]",
                            "apiVersion": "2015-08-31-preview",
                            "location": "[resourceGroup().location]"
                        },
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2019-04-01",
                            "name": "[variables('storageAccountName')]",
                            "location": "[resourceGroup().location]",
                            "sku": {
                                "name": "Standard_LRS"
                            },
                            "kind": "StorageV2",
                            "properties": {
                                "accessTier": "Hot"
                            }
                        },
                        {
                            "name": "[concat(variables('storageAccountName'), '/Microsoft.Authorization/', variables('storageBlobDataContributorAssignment'))]",
                            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
                            "apiVersion": "2021-04-01-preview",
                            "dependsOn": [
                                "[variables('managedIdentityName')]",
                                "[variables('storageAccountName')]"
                            ],
                            "properties": {
                                "roleDefinitionId": "[variables('storageBlobDataContributorDefinitionId')]",
                                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName')), '2018-11-30').principalId]",
                                "principalType": "ServicePrincipal"
                            }
                        },
                        {
                            "name": "[concat(variables('storageAccountName'), '/Microsoft.Authorization/', variables('readerAssignment'))]",
                            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
                            "apiVersion": "2021-04-01-preview",
                            "dependsOn": [
                                "[variables('managedIdentityName')]",
                                "[variables('storageAccountName')]"
                            ],
                            "properties": {
                                "roleDefinitionId": "[variables('readerDefinitionId')]",
                                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName')), '2018-11-30').principalId]",
                                "principalType": "ServicePrincipal"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        },
        {
            "type": "Microsoft.Media/videoAnalyzers",
            "comments": "The Azure Video Analyzer account",
            "apiVersion": "2021-05-01-preview",
            "name": "[variables('accountName')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "deploy-storage-and-identity"
            ],
            "properties": {
                "storageAccounts": [
                    {
                        "id": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
                        "identity": {
                            "userAssignedIdentity": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName'))]"
                        }
                    }
                ]
            },
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName'))]": {}
                }
            }
        }
    ],
    "outputs": { }
}
```

> [!NOTE]
> 템플릿은 역할 할당에 대해 중첩된 배포를 사용하여 Video Analyzer 계정 리소스를 배포하기 전에 사용할 수 있는지 확인합니다.

### <a name="deploy-the-template"></a>템플릿 배포

1. *Azure에 배포* 단추를 클릭하여 Azure에 로그인하고 템플릿을 엽니다.

    [![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

1. 다음 값을 선택하거나 입력합니다. 사용 가능한 경우 기본값을 사용합니다.

    - **구독**: Azure 구독을 선택합니다.
    - **리소스 그룹**: 드롭 다운에서 기존의 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하고 리소스 그룹의 고유한 이름을 입력한 다음, **확인** 을 클릭합니다.
    - **위치**: 위치를 선택합니다.  예를 들어 **미국 서부2** 로 선택합니다.
    - **이름 접두사**: 리소스 이름을 접두사로 지정하는 데 사용되는 문자열을 제공합니다(기본값이 권장됨).

1. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기를** 선택하여 템플릿을 만들고 배포합니다.

위의 Azure Portal 템플릿을 배포하는 데 사용됩니다. Azure Portal 외에도 Azure CLI, Azure PowerShell 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../../azure-resource-manager/templates/deploy-cli.md)를 참조하세요.

### <a name="post-deployment-steps"></a>배포 후 단계
Video Analyzer 계정에 기존 IoT Hub 연결하도록 선택할 수 있으며, 기존 UAMI가 필요합니다.

1. 리소스 배포가 완료되면 **리소스로 이동을** 클릭합니다.
1. 설정에서 **IoT Hub** 선택한 **다음, 연결** 을 클릭합니다.  연결 **IoT Hub** 구성 플라이아웃 블레이드에서 필요한 값을 입력합니다.
     - 구독 - IoT Hub 만든 Azure 구독 이름을 선택합니다.
     - IoT Hub - 원하는 IoT Hub 선택합니다.
     - 관리 ID - IoT Hub 액세스하는 데 사용할 기존 UAMI를 선택합니다.
1. **저장을** 클릭하여 IoT Hub Video Analyzer 계정에 연결합니다.

### <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal을 사용하여 만든 계정 및 기타 리소스를 확인할 수 있습니다. 배포가 완료되면 **리소스 그룹으로 이동** 을 선택하여 계정 및 기타 리소스를 확인합니다.

### <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없으면 리소스 그룹을 삭제합니다. 그러면 리소스 그룹의 계정과 모든 리소스가 삭제됩니다.

1. **리소스 그룹** 을 선택합니다.
1. 리소스 그룹 페이지에서 **삭제** 를 선택합니다.
1. 메시지가 표시되면 리소스 그룹의 이름을 입력한 다음 **삭제** 를 선택합니다.

---

### <a name="next-steps"></a>다음 단계

* [IoT Edge 디바이스에 Video Analyzer를 배포][docs-deploy-on-edge]하는 방법을 알아봅니다.
* [클라우드에 직접 비디오를 캡처하고 녹화하는](cloud/get-started-livepipelines-portal.md)방법을 알아봅니다.

<!-- links -->
[docs-uami]: ../../active-directory/managed-identities-azure-resources/overview.md
[docs-storage-access]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor
[docs-role-reader]: ../../role-based-access-control/built-in-roles.md#reader
[docs-arm-template]: ../../azure-resource-manager/templates/overview.md
[docs-deploy-on-edge]: deploy-iot-edge-device.md
