---
title: Azure CLI를 사용하여 Azure Image Builder 서비스 사용 권한 구성
description: Azure CLI를 사용하여 사용 권한 및 권한을 포함하는 Azure VM Image Builder 서비스에 대한 요구 사항 구성
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 04/02/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: 3f20276c8cb1f3b777f3cbb1400559cfe85294bc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428257"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>Azure CLI를 사용하여 Azure Image Builder 서비스 사용 권한 구성

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

(AIB)에 등록하면 AIB 서비스에 스테이징 리소스 그룹(IT_*)을 만들고, 관리하고, 삭제할 수 있는 권한이 부여되며, 이미지 빌드에 필요한 리소스를 추가할 수 있는 권한이 부여됩니다. 성공적으로 등록하는 동안 구독에서 사용할 수 있는 AIB SPN(서비스 주체 이름)이 이 작업을 수행합니다.

Azure VM Image Builder 관리되는 이미지 또는 Azure Compute 갤러리(이전의 Shared Image Gallery)에 이미지를 배포할 수 있도록 하려면 이미지를 읽고 쓸 수 있는 권한이 있는 Azure 사용자 할당 ID를 만들어야 합니다. Azure Storage에 액세스하는 경우 프라이빗 또는 퍼블릭 컨테이너를 읽을 수 있는 권한이 필요합니다.

이미지를 빌드하기 전에 사용 권한 및 권한을 설정해야 합니다. 다음 섹션에서는 Azure CLI를 사용하여 가능한 시나리오를 구성하는 방법에 대해 자세히 설명합니다.


[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-azure-user-assigned-managed-identity"></a>Azure 사용자 할당 관리 ID 만들기

Azure Image Builder를 사용하려면 [Azure 사용자 할당 관리 ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)를 만들어야 합니다. Azure Image Builder는 사용자 할당 관리 ID를 사용하여 이미지를 읽고, 이미지를 쓰고, Azure Storage 계정에 액세스합니다. 구독에서 특정 작업을 수행할 수 있는 권한을 해당 ID에 부여합니다.

> [!NOTE]
> 이전에는 Azure Image Builder에서 Azure Image Builder SPN(서비스 사용자 이름)을 사용하여 이미지 리소스 그룹에 대한 사용 권한을 부여했습니다. SPN을 사용하는 것은 더 이상 지원되지 않습니다. 대신, 사용자 할당 관리 ID를 사용합니다.

다음 예제에서는 Azure 사용자 할당 관리 ID를 만드는 방법을 보여 줍니다. 자리 표시자 설정을 대체하여 변수를 설정합니다.

| 설정 | Description |
|---------|-------------|
| \<Resource group\> | 사용자 할당 관리 ID를 만들 리소스 그룹입니다. |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
```

Azure 사용자 할당 ID에 대한 자세한 내용은 ID를 만드는 방법에 대한 [Azure 사용자 할당 관리 ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) 설명서를 참조하세요.

## <a name="allow-image-builder-to-distribute-images"></a>Image Builder에서 이미지를 배포하도록 허용

Azure Image Builder 이미지(관리되는 이미지/Azure Compute 갤러리)를 배포하려면 Azure Image Builder 서비스가 이러한 리소스 그룹에 이미지를 삽입하도록 허용해야 합니다. 필요한 사용 권한을 부여하려면 사용자 할당 관리 ID를 만들고 이미지가 빌드된 리소스 그룹에 대한 권한을 부여해야 합니다. Azure Image Builder에는 구독의 다른 리소스 그룹에 있는 리소스에 액세스할 수 있는 권한이 **없습니다**. 빌드 실패를 방지하려면 액세스를 허용하기 위해 명시적인 작업을 수행해야 합니다.

이미지를 배포하기 위해 리소스 그룹에 대한 사용자 할당 관리 ID 참가자 권한을 부여하지 않아도 됩니다. 그러나 사용자 할당 관리 ID의 경우 배포 리소스 그룹에 다음 Azure `Actions` 권한이 필요합니다.

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Azure Compute 갤러리에 배포하는 경우 다음도 필요합니다.

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>기존 이미지를 사용자 지정하기 위한 권한

Azure Image Builder 원본 사용자 지정 이미지(관리되는 이미지/Azure Compute 갤러리)에서 이미지를 빌드하려면 Azure Image Builder 서비스에서 이러한 리소스 그룹으로 이미지를 읽을 수 있어야 합니다. 필요한 사용 권한을 부여하려면 사용자 할당 관리 ID를 만들고 이미지가 있는 리소스 그룹에 대한 권한을 부여해야 합니다.

기존 사용자 지정 이미지에서 빌드:

```Actions
Microsoft.Compute/galleries/read
```

기존 Azure Compute 갤러리 버전에서 빌드:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>VNET에서 이미지를 사용자 지정할 수 있는 권한

Azure Image Builder에는 구독에서 기존 VNET을 배포하고 사용하여 사용자 지정이 연결된 리소스에 액세스할 수 있도록 허용하는 기능이 있습니다.

VM을 기존 VNET에 배포하기 위해 리소스 그룹에 대한 사용자 할당 관리 ID 참가자 권한을 부여하지 않아도 됩니다. 그러나 사용자 할당 관리 ID의 경우 VNET 리소스 그룹에 다음 Azure `Actions` 권한이 필요합니다.

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Azure 역할 정의 만들기

다음 예제에서는 이전 섹션에서 설명한 작업에서 Azure 역할 정의를 만듭니다. 예제는 리소스 그룹 수준에서 적용됩니다. 사용자 요구 사항에 따라 예제가 충분히 세분화되어 있는지 평가하고 테스트합니다. 시나리오의 경우 특정 Azure Compute 갤러리로 구체화해야 할 수 있습니다.

이미지 작업은 읽기 및 쓰기를 허용합니다. 사용자 환경에 적합한 항목을 결정합니다. 예를 들어, Azure Image Builder가 리소스 그룹 *‘example-rg-1’* 에서 이미지를 읽고 리소스 그룹 *‘example-rg-2’* 에 이미지를 쓰도록 허용하는 역할을 만듭니다.

### <a name="custom-image-azure-role-example"></a>사용자 지정 이미지 Azure 역할 예제

다음 예제에서는 원본 사용자 지정 이미지를 사용하고 배포하는 Azure 역할을 만듭니다. 그런 다음, Azure Image Builder의 사용자 할당 관리 ID에 사용자 지정 역할을 부여합니다.

예제의 값을 간단하게 바꾸기 위해 먼저 다음 변수를 설정합니다. 자리 표시자 설정을 대체하여 변수를 설정합니다.

| 설정 | Description |
|---------|-------------|
| \<Subscription ID\> | Azure 구독 ID |
| \<Resource group\> | 사용자 지정 이미지에 대한 리소스 그룹 |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=$(az account show --query id --output tsv)
# Resource group - image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName --query clientId -o tsv)

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>기존 VNET Azure 역할 예제

다음 예제에서는 기존 VNET 이미지를 사용하고 배포하는 Azure 역할을 만듭니다. 그런 다음, Azure Image Builder의 사용자 할당 관리 ID에 사용자 지정 역할을 부여합니다.

예제의 값을 간단하게 바꾸기 위해 먼저 다음 변수를 설정합니다. 자리 표시자 설정을 대체하여 변수를 설정합니다.

| 설정 | Description |
|---------|-------------|
| \<Subscription ID\> | Azure 구독 ID |
| \<Resource group\> | VNET 리소스 그룹 |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=$(az account show --query id --output tsv)
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName --query clientId -o tsv)

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>Azure Storage 액세스에 관리 ID 사용

Azure Storage로 원활하게 인증을 수행하고 프라이빗 컨테이너를 사용하려는 경우 Azure에 사용자 할당 관리 ID가 필요합니다. Azure Image Builder는 해당 ID를 사용하여 Azure Storage에서 인증을 받습니다.

> [!NOTE]
> Azure Image Builder는 이미지 템플릿 전송 시간에만 ID를 사용합니다. 빌드 VM은 이미지 빌드 중에 ID에 액세스할 수 없습니다.

Azure CLI를 사용하여 사용자 할당 관리 ID를 만듭니다.

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

이미지 작성기 템플릿에서 사용자 할당 관리 ID를 제공해야 합니다.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

다음 자리 표시자 설정을 바꿉니다.

| 설정 | Description |
|---------|-------------|
| \<Region\> | 템플릿 지역 |
| \<Resource group\> | Resource group |
| \<Storage account container\> | 스토리지 계정 컨테이너 이름 |
| \<Subscription ID\> | Azure 구독 |

사용자 할당 관리 ID를 사용하는 방법에 대한 자세한 내용은 [Azure 사용자 할당 관리 ID를 사용하여 파일 Azure Storage에 원활하게 액세스하는 사용자 지정 이미지 만들기](./image-builder-user-assigned-identity.md)를 참조하세요. 빠른 시작은 사용자 할당 관리 ID를 만들고 구성하여 스토리지 계정에 액세스하는 방법을 안내합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Image Builder 개요](../image-builder-overview.md)를 참조하세요.
