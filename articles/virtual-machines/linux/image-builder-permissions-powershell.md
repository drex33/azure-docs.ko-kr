---
title: PowerShell을 사용하여 Azure Image Builder 서비스 권한 구성
description: PowerShell을 사용하여 권한을 포함하는 Azure VM Image Builder 서비스에 대한 요구 사항 구성
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 03/05/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 759f48a8bcfaa30c190c701ba5dcc8b9cad8a876
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696145"
---
# <a name="configure-azure-image-builder-service-permissions-using-powershell"></a>PowerShell을 사용하여 Azure Image Builder 서비스 권한 구성

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

(AIB)에 등록하면 AIB 서비스에 스테이징 리소스 그룹(IT_*)을 만들고, 관리하고, 삭제할 수 있는 권한이 부여되며, 이미지 빌드에 필요한 리소스를 추가할 수 있는 권한이 부여됩니다. 성공적으로 등록하는 동안 구독에서 사용할 수 있는 AIB SPN(서비스 주체 이름)이 이 작업을 수행합니다.

Azure VM Image Builder에서 관리형 이미지 또는 공유 이미지 갤러리에 이미지를 배포하도록 허용하려면 이미지를 읽고 쓸 수 있는 권한이 있는 Azure 사용자 할당 ID를 만들어야 합니다. Azure Storage에 액세스하는 경우 프라이빗 또는 퍼블릭 컨테이너를 읽을 수 있는 권한이 필요합니다.

이미지를 빌드하기 전에 사용 권한 및 권한을 설정해야 합니다. 다음 섹션에서는 PowerShell을 사용하여 가능한 시나리오를 구성하는 방법에 관해 자세히 설명합니다.

## <a name="create-an-azure-user-assigned-managed-identity"></a>Azure 사용자 할당 관리 ID 만들기

Azure Image Builder를 사용하려면 [Azure 사용자 할당 관리 ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)를 만들어야 합니다. Azure Image Builder는 사용자 할당 관리 ID를 사용하여 이미지를 읽고, 이미지를 쓰고, Azure Storage 계정에 액세스합니다. 구독에서 특정 작업을 수행할 수 있는 권한을 해당 ID에 부여합니다.

> [!NOTE]
> 이전에는 Azure Image Builder에서 Azure Image Builder SPN(서비스 사용자 이름)을 사용하여 이미지 리소스 그룹에 대한 사용 권한을 부여했습니다. SPN을 사용하는 것은 더 이상 지원되지 않습니다. 대신, 사용자 할당 관리 ID를 사용합니다.

다음 예제에서는 Azure 사용자 할당 관리 ID를 만드는 방법을 보여 줍니다. 자리 표시자 설정을 대체하여 변수를 설정합니다.

| 설정 | Description |
|---------|-------------|
| \<Resource group\> | 사용자 할당 관리 ID를 만들 리소스 그룹입니다. |

```powershell-interactive
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

$parameters = @{
    Name = 'aibIdentity'
    ResourceGroupName = '<Resource group>'
}
# create identity
New-AzUserAssignedIdentity @parameters
```

Azure 사용자 할당 ID에 대한 자세한 내용은 ID를 만드는 방법에 대한 [Azure 사용자 할당 관리 ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) 설명서를 참조하세요.

## <a name="allow-image-builder-to-distribute-images"></a>Image Builder에서 이미지를 배포하도록 허용

Azure Image Builder가 이미지(관리형 이미지/공유 이미지 갤러리)를 배포하려면 Azure Image Builder 서비스에서 이러한 리소스 그룹으로 이미지를 삽입할 수 있어야 합니다. 필요한 사용 권한을 부여하려면 사용자 할당 관리 ID를 만들고 이미지가 빌드된 리소스 그룹에 대한 권한을 부여해야 합니다. Azure Image Builder에는 구독의 다른 리소스 그룹에 있는 리소스에 액세스할 수 있는 권한이 **없습니다**. 빌드 실패를 방지하려면 액세스를 허용하기 위해 명시적인 작업을 수행해야 합니다.

이미지를 배포하기 위해 리소스 그룹에 대한 사용자 할당 관리 ID 참가자 권한을 부여하지 않아도 됩니다. 그러나 사용자 할당 관리 ID의 경우 배포 리소스 그룹에 다음 Azure `Actions` 권한이 필요합니다.

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

공유 이미지 갤러리에 배포하는 경우 다음도 필요합니다.

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>기존 이미지를 사용자 지정하기 위한 권한

Azure Image Builder가 원본 사용자 지정 이미지(관리형 이미지/공유 이미지 갤러리)에서 이미지를 빌드하려면 Azure Image Builder 서비스에서 이러한 리소스 그룹으로 이미지를 읽을 수 있어야 합니다. 필요한 사용 권한을 부여하려면 사용자 할당 관리 ID를 만들고 이미지가 있는 리소스 그룹에 대한 권한을 부여해야 합니다.

기존 사용자 지정 이미지에서 빌드:

```Actions
Microsoft.Compute/galleries/read
```

기존 공유 이미지 갤러리 버전에서 빌드:

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

다음 예제에서는 이전 섹션에서 설명한 작업에서 Azure 역할 정의를 만듭니다. 예제는 리소스 그룹 수준에서 적용됩니다. 사용자 요구 사항에 따라 예제가 충분히 세분화되어 있는지 평가하고 테스트합니다. 시나리오의 경우 특정 공유 이미지 갤러리로 구체화해야 할 수 있습니다.

이미지 작업은 읽기 및 쓰기를 허용합니다. 사용자 환경에 적합한 항목을 결정합니다. 예를 들어, Azure Image Builder가 리소스 그룹 *‘example-rg-1’* 에서 이미지를 읽고 리소스 그룹 *‘example-rg-2’* 에 이미지를 쓰도록 허용하는 역할을 만듭니다.

### <a name="custom-image-azure-role-example"></a>사용자 지정 이미지 Azure 역할 예제

다음 예제에서는 원본 사용자 지정 이미지를 사용하고 배포하는 Azure 역할을 만듭니다. 그런 다음, Azure Image Builder의 사용자 할당 관리 ID에 사용자 지정 역할을 부여합니다.

예제의 값을 간단하게 바꾸기 위해 먼저 다음 변수를 설정합니다. 자리 표시자 설정을 대체하여 변수를 설정합니다.

| 설정 | Description |
|---------|-------------|
| \<Subscription ID\> | Azure 구독 ID |
| \<Resource group\> | 사용자 지정 이미지에 대한 리소스 그룹 |

```powershell-interactive
$sub_id = "<Subscription ID>"
# Resource group - image builder will only support creating custom images in the same Resource Group as the source managed image.
$imageResourceGroup = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$role_definition="aibRoleImageCreation.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleImageCreation.json description file. 
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $imageRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $imageResourceGroup
}

New-AzRoleAssignment @parameters
```

### <a name="existing-vnet-azure-role-example"></a>기존 VNET Azure 역할 예제

다음 예제에서는 기존 VNET 이미지를 사용하고 배포하는 Azure 역할을 만듭니다. 그런 다음, Azure Image Builder의 사용자 할당 관리 ID에 사용자 지정 역할을 부여합니다.

예제의 값을 간단하게 바꾸기 위해 먼저 다음 변수를 설정합니다. 자리 표시자 설정을 대체하여 변수를 설정합니다.

| 설정 | Description |
|---------|-------------|
| \<Subscription ID\> | Azure 구독 ID |
| \<Resource group\> | VNET 리소스 그룹 |

```powershell-interactive
$sub_id = "<Subscription ID>"
$res_group = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$role_definition="aibRoleNetworking.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same AAD domain
$timeInt=$(get-date -UFormat "%s")
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<vnetRgName>', $res_group) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleNetworking.json description file
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Assign the custom role to the user-assigned managed identity for Azure Image Builder
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $networkRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $res_group
}

New-AzRoleAssignment @parameters
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Image Builder 개요](../image-builder-overview.md)를 참조하세요.
