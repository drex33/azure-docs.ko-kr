---
title: 컨테이너 및 Blob에 대한 익명 퍼블릭 읽기 권한 구성
titleSuffix: Azure Storage
description: 스토리지 계정의 Blob 데이터에 대한 익명 액세스를 허용하거나 허용하지 않는 방법에 대해 알아봅니다. 컨테이너 및 Blob을 익명 액세스로 사용할 수 있도록 컨테이너 퍼블릭 액세스 설정을 설정합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/29/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7a9204bdc5eae43a60dc36c41e84d83f44e4269c
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132941988"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>컨테이너 및 Blob에 대한 익명 퍼블릭 읽기 권한 구성

Azure Storage는 컨테이너 및 Blob에 대한 선택적 익명 퍼블릭 읽기 권한을 지원합니다. 기본값으로 데이터에 대한 익명 액세스는 허용되지 않습니다. 익명 액세스를 명시적으로 사용하도록 설정하지 않는 한 컨테이너 및 해당 Blob에 대한 모든 요청에는 권한이 있어야 합니다. 익명 액세스를 허용하도록 컨테이너의 퍼블릭 액세스 수준 설정을 구성하는 경우 클라이언트는 요청에 대한 권한을 부여하지 않고 해당 컨테이너의 데이터를 읽을 수 있습니다.

> [!WARNING]
> 컨테이너가 퍼블릭 액세스용으로 구성된 경우 모든 클라이언트가 해당 컨테이너의 데이터를 읽을 수 있습니다. 퍼블릭 액세스를 사용하면 보안 위험이 발생할 수 있으므로 시나리오에 필요하지 않다면 스토리지 계정에 대해 퍼블릭 액세스를 허용하지 않는 것이 좋습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 퍼블릭 읽기 권한 방지](anonymous-read-access-prevent.md)를 참조하세요.

이 문서는 컨테이너 및 해당 Blob에 대한 익명 퍼블릭 읽기 권한을 구성하는 방법을 설명합니다. 클라이언트 응용 프로그램에서 익명으로 Blob 데이터에 액세스하는 방법에 대한 자세한 내용은 [.NET을 사용하여 퍼블릭 컨테이너 및 Blob에 익명으로 액세스](anonymous-read-access-client.md)를 참조하세요.

## <a name="about-anonymous-public-read-access"></a>익명 퍼블릭 읽기 권한 정보

기본값으로 데이터에 대한 퍼블릭 액세스는 항상 금지됩니다. 퍼블릭 액세스에 영향을 주는 두 가지 개별 설정이 있습니다.

1. **스토리지 계정에 대한 퍼블릭 액세스 허용** 기본값으로 스토리지 계정은 적절한 권한을 가진 사용자가 컨테이너에 대한 퍼블릭 액세스를 사용할 수 있도록 허용합니다. 사용자가 컨테이너의 퍼블릭 액세스 설정을 명시적으로 구성하는 추가 단계를 수행하지 않는 한 퍼블릭 액세스에는 Blob 데이터를 사용할 수 없습니다.
1. **컨테이너의 퍼블릭 액세스 설정을 구성합니다.** 기본값으로 컨테이너의 퍼블릭 액세스 설정은 사용하지 않도록 설정됩니다. 즉, 컨테이너 또는 해당 데이터에 대한 모든 요청에 대한 권한 부여가 필요합니다. 적절한 권한이 있는 사용자는 컨테이너의 퍼블릭 액세스 설정을 수정하여 스토리지 계정에 대한 익명 액세스가 허용되는 경우에만 익명 액세스를 사용하도록 설정할 수 있습니다.

다음 표에는 두 설정이 컨테이너에 대한 퍼블릭 액세스에 어떻게 영향을 주는지 요약되어 있습니다.

|   | 컨테이너에 대한 공용 액세스 수준이 프라이빗(기본 설정)으로 설정됩니다. | 컨테이너에 대한 공용 액세스 수준이 컨테이너로 설정되었습니다. | 컨테이너에 대한 공용 액세스 수준이 Blob으로 설정됩니다. |
|--|--|--|--|
| **스토리지 계정에 대한 퍼블릭 액세스는 허용되지 않습니다.** | 스토리지 계정의 모든 컨테이너에 대한 퍼블릭 액세스 권한 없음 | 스토리지 계정의 모든 컨테이너에 대한 퍼블릭 액세스 권한 없음 스토리지 계정 설정은 컨테이너 설정을 재정의합니다. | 스토리지 계정의 모든 컨테이너에 대한 퍼블릭 액세스 권한 없음 스토리지 계정 설정은 컨테이너 설정을 재정의합니다. |
| **스토리지 계정은 기본값 설정으로 퍼블릭 액세스를 허용합니다.** | 이 컨테이너에 대한 퍼블릭 액세스 권한 없음(기본값 구성) | 이 컨테이너 및 해당 Blob에 대한 퍼블릭 액세스가 허용됩니다. | 이 컨테이너의 Blob에는 퍼블릭 액세스가 허용되지만 컨테이너 자체에는 허용되지 않습니다. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>스토리지 계정에 대한 퍼블릭 읽기 권한을 허용하거나 허용하지 않습니다.

기본값으로 스토리지 계정은 적절한 권한이 있는 사용자가 컨테이너에 대한 퍼블릭 액세스를 사용할 수 있도록 구성됩니다. 퍼블릭 액세스가 허용되는 경우 적절한 권한이 있는 사용자는 컨테이너의 퍼블릭 액세스 설정을 수정하여 해당 컨테이너의 데이터에 대한 익명 퍼블릭 액세스를 사용할 수 있습니다. 사용자가 컨테이너의 퍼블릭 액세스 설정을 명시적으로 구성하는 추가 단계를 수행하지 않는 한 퍼블릭 액세스에는 Blob 데이터를 사용할 수 없습니다.

컨테이너에 대한 퍼블릭 액세스는 기본값으로 항상 꺼져 있으며 익명 요청을 허용하도록 명시적으로 구성해야 합니다. 적절한 권한이 있는 사용자가 컨테이너에 대한 퍼블릭 액세스를 사용하도록 설정하는 경우를 제외하고, 스토리지 계정 설정에 관계 없이 데이터를 퍼블릭 액세스를 위해 사용할 수 없습니다.

스토리지 계정에 대한 퍼블릭 액세스를 허용하지 않으면 해당 계정의 모든 컨테이너와 Blob에 대한 익명 액세스가 차단됩니다. 계정에 대한 퍼블릭 액세스를 허용하지 않으면 컨테이너에 대한 퍼블릭 액세스 설정을 구성하여 익명 액세스를 허용할 수 없습니다. 보안 향상을 위해 사용자가 Blob 리소스에 익명으로 액세스해야 하는 시나리오를 제외하고는 스토리지 계정에 대한 퍼블릭 액세스를 허용하지 않는 것이 좋습니다.

> [!IMPORTANT]
> 스토리지 계정에 대한 퍼블릭 액세스를 허용하지 않으면 해당 스토리지 계정의 모든 컨테이너에 대한 퍼블릭 액세스 설정이 재정의됩니다. 스토리지 계정에 대한 퍼블릭 액세스가 허용되지 않는 경우 해당 계정에 대한 모든 익명 요청은 실패합니다. 이 설정을 변경하기 전에 스토리지 계정의 데이터에 익명으로 액세스할 수 있는 클라이언트 응용 프로그램에 미치는 영향을 이해해야 합니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 퍼블릭 읽기 권한 방지](anonymous-read-access-prevent.md)를 참조하세요.

스토리지 계정에 대한 퍼블릭 액세스를 허용하거나 허용하지 않으려면 계정의 **AllowBlobPublicAccess** 속성을 구성합니다. 이 속성은 Azure Resource Manager 배포 모델을 사용하여 만든 모든 스토리지 계정에서 사용할 수 있습니다. 자세한 내용은 [Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.

**AllowBlobPublicAccess** 속성은 기본값으로 스토리지 계정에 대해 설정되지 않으며, 명시적으로 설정할 때까지는 값을 반환하지 않습니다. 스토리지 계정은 속성 값이 **null** 또는 **true** 일 때 퍼블릭 액세스를 허용합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 스토리지 계정에 대한 퍼블릭 액세스를 허용하거나 허용하지 않으려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **설정** 에서 **구성** 설정을 찾습니다.
1. **Blob 퍼블릭 액세스** 를 **사용** 또는 **사용하지 않음** 으로 설정합니다.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="계정에 대한 Blob 퍼블릭 액세스를 허용하거나 허용하지 않는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 스토리지 계정에 대한 퍼블릭 액세스를 허용하거나 허용하지 않으려면 [Azure PowerShell 버전 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) 이상을 설치합니다. 다음으로, 새 또는 기존 스토리지 계정에 대한 **AllowBlobPublicAccess** 속성을 구성합니다.

다음 예제에서는 스토리지 계정을 만들고 **AllowBlobPublicAccess** 속성을 명시적으로 **true** 로 설정합니다. 그런 다음, 스토리지 계정을 업데이트하여 **AllowBlobPublicAccess** 속성을 **false** 로 설정합니다. 또한 이 예제는 각 사례에서 속성 값을 검색합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with AllowBlobPublicAccess set to true (or null).
New-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -Location $location `
    -SkuName Standard_GRS `
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property for the newly created storage account.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess

# Set AllowBlobPublicAccess set to false
Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 스토리지 계정에 대한 퍼블릭 액세스를 허용하거나 허용하지 않으려면 Azure CLI 버전 2.9.0 이상을 설치합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. 다음으로, 새 또는 기존 스토리지 계정에 대한 **AllowBlobPublicAccess** 속성을 구성합니다.

다음 예제에서는 스토리지 계정을 만들고 **allowBlobPublicAccess** 속성을 명시적으로 **true** 로 설정합니다. 그런 다음, 스토리지 계정을 업데이트하여 **allowBlobPublicAccess** 속성을 **false** 로 설정합니다. 또한 이 예제는 각 사례에서 속성 값을 검색합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --allow-blob-public-access true

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-blob-public-access false

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv
```

# <a name="template"></a>[템플릿](#tab/template)

템플릿을 사용하여 스토리지 계정에 대한 퍼블릭 액세스를 허용하거나 허용하지 않으려면 **AllowBlobPublicAccess** 속성 집합을 **true** 또는 **false** 로 설정하여 템플릿을 만듭니다. 다음 단계는 Azure Portal에서 템플릿을 만드는 방법을 보여 줍니다.

1. Azure Portal에서 **리소스 만들기** 를 선택합니다.
1. **Marketplace 검색** 에서 **템플릿 배포** 를 입력하고 **ENTER** 를 누릅니다.
1. **템플릿 배포(사용자 지정 템플릿을 사용하여 배포)(미리 보기)** 를 선택하고 **만들기** 를 선택한 다음 **편집기에서 사용자 고유의 템플릿 빌드** 를 선택합니다.
1. 템플릿 편집기에서 다음 JSON을 붙여넣어 새 계정을 만들고 **AllowBlobPublicAccess** 속성을 **true** 또는 **false** 로 설정합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'template')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "allowBlobPublicAccess": false
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. 템플릿을 저장하는 경우
1. 리소스 그룹 매개 변수를 지정하고 **검토 + 만들기** 단추를 선택하여 템플릿을 배포하고 **allowBlobPublicAccess** 속성이 구성된 스토리지 계정을 만듭니다.

---

> [!NOTE]
> 스토리지 계정에 대한 퍼블릭 액세스를 허용하지 않으면 해당 스토리지 계정에서 호스트되는 고정적인 웹 사이트에 영향을 주지 않습니다. **$web**  컨테이너는 항상 퍼블릭으로 액세스할 수 있습니다.
>
> 스토리지 계정에 대한 퍼블릭 액세스 설정을 업데이트한 후 변경이 완전히 전파되기까지 최대 30초가 걸릴 수 있습니다.

Blob 퍼블릭 액세스를 허용하거나 허용하지 않으려면 Azure Storage 리소스 공급자 2019-04-01 이상 버전이 필요합니다. 자세한 내용은 [Azure Storage 리소스 공급자 REST API](/rest/api/storagerp/)를 참조하세요.

이 섹션의 예제는 스토리지 계정에 대한 **AllowBlobPublicAccess** 속성을 읽어 퍼블릭 액세스가 현재 허용되는지 아니면 허용되지 않는지 확인하는 방법을 보여 주었습니다. 익명 액세스를 방지하기 위해 계정의 퍼블릭 액세스 설정이 구성되어 있는지 확인하는 방법에 대한 자세한 내용은 [익명 퍼블릭 액세스 재구성](anonymous-read-access-prevent.md#remediate-anonymous-public-access)을 참조하세요.

## <a name="set-the-public-access-level-for-a-container"></a>컨테이너에 대한 퍼블릭 액세스 수준 설정

익명 사용자에게 컨테이너 및 해당 Blob에 대한 읽기 권한을 부여하려면 먼저 스토리지 계정에 대한 퍼블릭 액세스를 허용하고 컨테이너의 퍼블릭 액세스 수준을 설정합니다. 스토리지 계정에 대한 퍼블릭 액세스가 거부된 경우에는 컨테이너에 대한 퍼블릭 액세스를 구성할 수 없습니다.

스토리지 계정에 대한 퍼블릭 액세스를 허용하는 경우 다음 사용 권한을 사용하여 컨테이너를 구성할 수 있습니다.

- **퍼블릭 읽기 권한 없음:** 인증된 요청에 의해서만 컨테이너 및 해당 Blob에 액세스할 수 있습니다. 이 옵션은 새로운 모든 컨테이너에 대한 기본 설정입니다.
- **Blob 전용 퍼블릭 읽기 권한:** 이 컨테이너 내의 Blob은 익명 요청을 통해 읽을 수 있지만 컨테이너 데이터는 익명으로 읽을 수 없습니다. 익명 클라이언트는 컨테이너 내의 Blob을 열거할 수 없습니다.
- **컨테이너 및 해당 Blob에 대한 퍼블릭 읽기 권한:** 컨테이너 권한 설정 및 컨테이너 메타데이터를 제외하고, 컨테이너 및 Blob 데이터를 익명 요청으로 읽을 수 있습니다. 클라이언트는 익명 요청을 통해 컨테이너 내에서 Blob을 열거할 수 있지만 스토리지 계정 내에서 컨테이너를 열거할 수는 없습니다.

개별 Blob에 대한 퍼블릭 액세스 수준을 변경할 수는 없습니다. 퍼블릭 액세스 수준은 컨테이너 수준에서만 설정됩니다. 컨테이너를 만들 때 컨테이너의 퍼블릭 액세스 수준을 설정하거나 기존 컨테이너에서 설정을 업데이트할 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 하나 이상의 기존 컨테이너에 대한 퍼블릭 액세스 수준을 업데이트하려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정 개요로 이동합니다.
1. 메뉴 블레이드의 **데이터 저장소** 에서 **Blob 컨테이너** 를 선택합니다.
1. 퍼블릭 액세스 수준을 설정하려는 컨테이너를 선택합니다.
1. **액세스 수준 변경** 단추를 사용하여 퍼블릭 액세스 설정을 표시합니다.
1. **퍼블릭 액세스 수준** 드롭다운에서 원하는 퍼블릭 액세스 수준을 선택하고 확인 단추를 클릭하여 선택한 컨테이너에 변경 내용을 적용합니다.

    :::image type="content" source="media/anonymous-read-access-configure/configure-public-access-container.png" alt-text="포털에서 공용 액세스 수준을 설정하는 방법을 보여주는 스크린샷" lightbox="media/anonymous-read-access-configure/configure-public-access-container.png":::

스토리지 계정에 대한 퍼블릭 액세스가 허용되지 않는 경우 컨테이너의 퍼블릭 액세스 수준을 설정할 수 없습니다. 컨테이너의 퍼블릭 액세스 수준을 설정하려고 하면 계정에 대한 퍼블릭 액세스가 허용되지 않기 때문에 설정이 사용하지 않는 것으로 설정된 것을 볼 수 있습니다.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="퍼블릭 액세스를 허용하지 않을 때 컨테이너 퍼블릭 액세스 수준 설정이 차단됨을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 하나 이상의 컨테이너에 대한 퍼블릭 액세스 수준을 업데이트하려면 [Set-AzStorageContainerAcl](/powershell/module/az.storage/set-azstoragecontaineracl) 명령을 호출합니다. 계정 키, 연결 문자열 또는 SAS(공유 액세스 서명)를 전달하여 이 작업에 권한을 부여합니다. 컨테이너의 퍼블릭 액세스 수준을 설정하는 [컨테이너 ACL 설정](/rest/api/storageservices/set-container-acl) 작업은 Azure AD에 대한 권한 부여를 지원하지 않습니다. 자세한 내용은 [Blob 및 큐 데이터 작업 호출에 대한 권한](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations)을 참조하세요.

다음 예제에서는 퍼블릭 액세스를 사용할 수 없는 컨테이너를 만든 다음 컨테이너의 퍼블릭 액세스 설정을 업데이트하여 컨테이너 및 해당 Blob에 대한 익명 액세스를 허용합니다. 대괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
# Set variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Get context object.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

# Create a new container with public access setting set to Off.
$containerName = "<container>"
New-AzStorageContainer -Name $containerName -Permission Off -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx

# Update the container's public access setting to Container.
Set-AzStorageContainerAcl -Container $containerName -Permission Container -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx
```

스토리지 계정에 대한 퍼블릭 액세스가 허용되지 않는 경우 컨테이너의 퍼블릭 액세스 수준을 설정할 수 없습니다. 컨테이너의 퍼블릭 액세스 수준을 설정하려고 하면 Azure Storage에서 스토리지 계정에 대한 퍼블릭 액세스가 허용되지 않음을 나타내는 오류를 반환합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 하나 이상의 컨테이너에 대한 퍼블릭 액세스 수준을 업데이트하려면 [az storage container set permission](/cli/azure/storage/container#az_storage_container_set_permission) 명령을 호출합니다. 계정 키, 연결 문자열 또는 SAS(공유 액세스 서명)를 전달하여 이 작업에 권한을 부여합니다. 컨테이너의 퍼블릭 액세스 수준을 설정하는 [컨테이너 ACL 설정](/rest/api/storageservices/set-container-acl) 작업은 Azure AD에 대한 권한 부여를 지원하지 않습니다. 자세한 내용은 [Blob 및 큐 데이터 작업 호출에 대한 권한](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations)을 참조하세요.

다음 예제에서는 퍼블릭 액세스를 사용할 수 없는 컨테이너를 만든 다음 컨테이너의 퍼블릭 액세스 설정을 업데이트하여 컨테이너 및 해당 Blob에 대한 익명 액세스를 허용합니다. 대괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage container create \
    --name <container-name> \
    --account-name <account-name> \
    --resource-group <resource-group>
    --public-access off \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key

az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key
```

스토리지 계정에 대한 퍼블릭 액세스가 허용되지 않는 경우 컨테이너의 퍼블릭 액세스 수준을 설정할 수 없습니다. 컨테이너의 퍼블릭 액세스 수준을 설정하려고 하면 Azure Storage에서 스토리지 계정에 대한 퍼블릭 액세스가 허용되지 않음을 나타내는 오류를 반환합니다.

# <a name="template"></a>[템플릿](#tab/template)

해당 없음.

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>컨테이너 집합에 대한 퍼블릭 액세스 설정 확인

컨테이너를 나열하고 퍼블릭 액세스 설정을 확인하여 하나 이상의 스토리지 계정에서 어떤 컨테이너가 퍼블릭 액세스를 위해 구성되었는지 확인할 수 있습니다. 이 방법은 스토리지 계정에 많은 수의 컨테이너가 포함되어 있지 않거나 소수의 스토리지 계정에서 설정을 확인하는 경우에 유용한 옵션입니다. 그러나 많은 수의 컨테이너를 열거하려고 하면 성능이 저하될 수 있습니다.

다음 예제에서는 PowerShell을 사용하여 스토리지 계정의 모든 컨테이너에 대한 퍼블릭 액세스 설정을 가져옵니다. 대괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

| Storage 계정 유형 | Blob Storage(기본 지원) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|--|--|--|--|--|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) |![예](../media/icons/yes-icon.png)              | ![예](../media/icons/yes-icon.png) |![예](../media/icons/yes-icon.png) |
| Premium 블록 Blob          | ![예](../media/icons/yes-icon.png)| ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |![예](../media/icons/yes-icon.png) |

<sup>1</sup> Data Lake Storage Gen2, NFS(네트워크 파일 시스템) 3.0 프로토콜 및 SFTP(SSH 파일 전송 프로토콜) 지원은 모두 계층 구조 네임스페이스를 사용하도록 설정된 스토리지 계정이 필요합니다.

## <a name="next-steps"></a>다음 단계

- [컨테이너 및 Blob에 대한 익명 퍼블릭 읽기 권한 방지](anonymous-read-access-prevent.md).
- [퍼블릭 컨테이너 및 Blob에 .NET을 사용하여 익명으로 액세스](anonymous-read-access-client.md)
- [Azure Storage에 대한 액세스 권한 부여](../common/authorize-data-access.md)
