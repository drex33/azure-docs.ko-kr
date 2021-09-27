---
title: PowerShell을 사용하여 할당을 관리하는 방법
description: 공식 Azure Blueprints PowerShell 모듈인 Az.Blueprint를 사용하여 청사진 할당을 관리하는 방법을 알아봅니다.
ms.date: 08/17/2021
ms.topic: how-to
ms.openlocfilehash: 0ce2132fe9d43ef0d5df1a4e0eec3a80a3a5c5ca
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612817"
---
# <a name="how-to-manage-assignments-with-powershell"></a>PowerShell을 사용하여 할당을 관리하는 방법

**Az.Blueprint** Azure PowerShell 모듈을 사용하여 청사진 할당을 관리할 수 있습니다. 모듈은 할당에 대한 가져오기, 만들기, 업데이트 및 제거를 지원합니다. 또한 모듈은 기존 청사진 정의에 대한 세부 정보를 가져올 수 있습니다. 이 문서에서는 모듈을 설치하고 사용을 시작하는 방법을 설명합니다.

## <a name="add-the-azblueprint-module"></a>Az.Blueprint 모듈을 추가합니다.

Azure PowerShell을 사용하도록 설정하여 청사진 할당을 관리하려면 해당 모듈을 추가해야 합니다. [Azure Cloud Shell](https://shell.azure.com) 또는 [Azure PowerShell Docker 이미지](/powershell/azure/azureps-in-docker)와 함께 로컬로 설치된 PowerShell로 이 모듈을 사용할 수 있습니다.

### <a name="base-requirements"></a>기본 요구 사항

Azure Blueprints 모듈에는 다음과 같은 소프트웨어 요구 사항이 있습니다.

- Azure PowerShell 1.5.0 이상. 아직 설치되지 않은 경우 [다음 지침](/powershell/azure/install-az-ps)을 따릅니다.
- PowerShellGet 2.0.1 이상 설치되거나 업데이트되지 않은 경우 [다음 지침](/powershell/scripting/gallery/installing-psget)을 따릅니다.

### <a name="install-the-module"></a>모듈 설치

PowerShell에 대한 Azure Blueprints 모듈은 **Az.Blueprint** 입니다.

1. **관리자** PowerShell 프롬프트에서 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > **Az.Accounts** 가 이미 설치되어 있으면 `-AllowClobber`를 사용하여 설치를 강제로 수행해야 할 수 있습니다.

1. 모듈을 가져왔고 올바른 버전(0.2.6)인지 확인합니다.

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>청사진 정의 가져오기

할당을 사용하는 첫 번째 단계는 청사진 정의에 대한 참조를 가져오는 것입니다.
`Get-AzBlueprint` cmdlet은 청사진 정의를 하나 이상 가져옵니다. cmdlet은 `-ManagementGroupId {mgId}`를 사용하는 관리 그룹 또는 `-SubscriptionId {subId}`를 사용하는 구독에 대해서 청사진 정의를 가져올 수 있습니다. **Name** 매개 변수는 청사진 정의를 가져오며 **Managementgroupid** 또는 **SubscriptionId** 와 함께 사용해야 합니다. **Version** 은 **Name** 과 함께 사용되어 반환되는 청사진 정의를 보다 명확하게 만들 수 있습니다. **Version** 대신, 스위치 `-LatestPublished`는 가장 최근에 게시된 버전을 가져옵니다.

다음 예에서는 `Get-AzBlueprint`를 사용하여 다음과 같이 `{subId}`로 표시된 특정 구독에서 '101-blueprints-definition-subscription'이라는 청사진 정의의 모든 버전을 가져옵니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

여러 버전의 청사진 정의에 대한 출력 예는 다음과 같습니다.

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

청사진 정의의 [청사진 매개 변수](../concepts/parameters.md#blueprint-parameters)를 확장하여 자세한 정보를 확인할 수 있습니다.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>청사진 할당 가져오기

할당된 청사진이 이미 있는 경우 `Get-AzBlueprintAssignment` cmdlet을 사용하여 관련된 참조를 가져올 수 있습니다. cmdlet은 **SubscriptionId** 및 **Name** 을 선택적 매개 변수로 사용합니다. **SubscriptionId** 를 지정하지 않으면 현재의 구독 컨텍스트가 사용됩니다.

다음 예에서는 `Get-AzBlueprintAssignment`를 사용하여 다음과 같이 `{subId}`로 표시된 특정 구독에서 'Assignment-lock-resource-groups'라는 단일 청사진 할당을 가져옵니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

청사진 할당에 대한 출력 예는 다음과 같습니다.

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>청사진 할당 만들기

청사진 할당이 아직 존재하지 않는 경우 `New-AzBlueprintAssignment` cmdlet을 사용하여 만들 수 있습니다. 이 cmdlet은 다음 매개 변수를 사용합니다.

- **Name** [필수]
  - 청사진 할당의 이름을 지정합니다.
  - 이름은 고유해야 하며 **SubscriptionId** 에 아직 존재하지 않아야 합니다.
- **Blueprint** [필수]
  - 할당할 청사진 정의를 지정합니다.
  - 참조 개체를 가져오는 데 `Get-AzBlueprint`를 사용합니다.
- **Location** [필수]
  - 시스템이 할당한 관리 ID 및 구독 배포 개체가 생성될 지역을 선택합니다.
- **Subscription**(선택 사항)
  - 할당이 배포되는 구독을 지정합니다.
  - 지정하지 않으면 기본값으로 현재 구독 컨텍스트로 설정됩니다.
- **Lock**(선택 사항)
  - 배포된 리소스에 사용할 [청사진 리소스 잠금](../concepts/resource-locking.md)을 정의합니다.
  - 지원되는 옵션: _None_, _allresourcesreadonly_, _AllResourcesDoNotDelete_
  - 지정하지 않으면 기본값은 _None_ 입니다.
- **SystemAssignedIdentity**(선택 사항)
  - 할당에 대한 시스템이 할당한 관리 ID를 만들고 리소스를 배포하려면 선택합니다.
  - "Identity" 매개 변수 집합에 대한 기본값
  - **UserAssignedIdentity** 와 함께 사용될 수 없습니다.
- **UserAssignedIdentity**(선택 사항)
  - 리소스를 배포하고 할당에 사용할 사용자가 할당한 관리 ID를 지정합니다.
  - "Identity" 매개 변수 집합의 일부
  - **SystemAssignedIdentity** 와 함께 사용될 수 없습니다.
- **Parameter**(선택 사항)
  - 청사진 할당에서 [동적 매개 변수](../concepts/parameters.md#dynamic-parameters)를 설정하기 위한 키/값 쌍 [해시 테이블](/powershell/module/microsoft.powershell.core/about/about_hash_tables)입니다.
  - 동적 매개 변수의 기본값은 정의에 있는 **defaultValue** 입니다.
  - 매개 변수가 제공되지 않고 **defaultValue** 를 포함하지 않는 경우 매개 변수는 선택 사항이 아닙니다.

    > [!NOTE]
    > **매개 변수** 가 secureStrings를 지원하지 않습니다.

- **ResourceGroupParameter**(선택 사항)
  - 리소스 그룹 아티팩트의 [해시 테이블](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 각 리소스 그룹 아티팩트 자리 표시자에는 리소스 그룹 아티팩트의 **Name** 및 **Location** 을 동적으로 설정하기 위한 키/값 쌍이 있습니다.
  - 리소스 그룹 매개 변수가 제공되지 않고 **defaultValue** 가 없는 경우 리소스 그룹 매개 변수는 선택 사항이 아닙니다.
- **AssignmentFile**(선택 사항)
  - 청사진 할당의 JSON 파일 표현에 대한 경로입니다.
  - 이 매개 변수는 **Name**, **Blueprint**, **SubscriptionId** 및 공통 매개 변수를 포함하는 PowerShell 매개 변수 집합의 일부입니다.

### <a name="example-1-provide-parameters"></a>예 1: 매개 변수 제공

다음 예에서는 `Get-AzBlueprint`를 사용하여 가져온 'my-blueprint' 청사진 정의의 '1.1' 버전에 대한 새 할당을 만들고, 관리 ID 및 할당 개체 위치를 'westus2'로 설정하고, _AllResourcesReadOnly_ 를 사용하여 리소스를 잠그고, 다음과 같이 `{subId}`로 표시된 특정 구독에 대한 **Parameter** 및 **ResourceGroupParameter** 의 해시 테이블을 설정합니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

청사진 할당을 만들기 위한 출력 예는 다음과 같습니다.

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>예 2: JSON 할당 정의 파일 사용

다음 예에서는 [예 1](#example-1-provide-parameters)과 거의 동일한 할당을 만듭니다. 이 예에서는 cmdlet에 매개 변수를 전달하는 대신 JSON 할당 정의 파일 및 **AssignmentFile** 매개 변수를 사용하는 방법을 보여 줍니다. 또한 **excludedPrincipals** 속성은 **잠금** 의 일부로 구성됩니다. **ExcludedPrincipals** 에 대한 PowerShell 매개 변수는 없으며 JSON 할당 정의 파일을 통해 설정해야만 속성을 구성할 수 있습니다.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

사용자가 할당한 관리 ID에 대한 JSON 할당 정의 파일의 예는 [예: REST API에 대한 사용자가 할당한 관리 ID로 할당](/rest/api/blueprints/assignments/createorupdate#examples)의 요청 본문을 참조하세요.

## <a name="update-blueprint-assignments"></a>청사진 할당 업데이트

이미 만들어진 청사진 할당을 업데이트해야 하는 경우도 있습니다. `Set-AzBlueprintAssignment` cmdlet은 이 작업을 처리합니다. Cmdlet은 `New-AzBlueprintAssignment` cmdlet이 수행하는 것과 동일한 매개 변수를 대부분 사용하므로 할당에 설정된 모든 항목을 업데이트할 수 있습니다. 예외는 _Name_, _Blueprint_ 및 _SubscriptionId_ 입니다. 제공된 값만 업데이트됩니다.

청사진 할당을 업데이트할 때 수행되는 작업을 이해하려면 [할당 업데이트 규칙](./update-existing-assignments.md#rules-for-updating-assignments)을 참조하세요.

- **Name** [필수]
  - 업데이트할 청사진 할당의 이름을 지정합니다.
  - 할당을 변경하는 것이 아니라 업데이트할 할당을 찾는 데 사용됩니다.
- **Blueprint** [필수]
  - 청사진 할당의 청사진 정의를 지정합니다.
  - 참조 개체를 가져오는 데 `Get-AzBlueprint`를 사용합니다.
  - 할당을 변경하는 것이 아니라 업데이트할 할당을 찾는 데 사용됩니다.
- **Location**(선택 사항)
  - 시스템이 할당한 관리 ID 및 구독 배포 개체가 생성될 지역을 선택합니다.
- **Subscription**(선택 사항)
  - 할당이 배포되는 구독을 지정합니다.
  - 지정하지 않으면 기본값으로 현재 구독 컨텍스트로 설정됩니다.
  - 할당을 변경하는 것이 아니라 업데이트할 할당을 찾는 데 사용됩니다.
- **Lock**(선택 사항)
  - 배포된 리소스에 사용할 [청사진 리소스 잠금](../concepts/resource-locking.md)을 정의합니다.
  - 지원되는 옵션: _None_, _allresourcesreadonly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity**(선택 사항)
  - 할당에 대한 시스템이 할당한 관리 ID를 만들고 리소스를 배포하려면 선택합니다.
  - "Identity" 매개 변수 집합에 대한 기본값
  - **UserAssignedIdentity** 와 함께 사용될 수 없습니다.
- **UserAssignedIdentity**(선택 사항)
  - 리소스를 배포하고 할당에 사용할 사용자가 할당한 관리 ID를 지정합니다.
  - "Identity" 매개 변수 집합의 일부
  - **SystemAssignedIdentity** 와 함께 사용될 수 없습니다.
- **Parameter**(선택 사항)
  - 청사진 할당에서 [동적 매개 변수](../concepts/parameters.md#dynamic-parameters)를 설정하기 위한 키/값 쌍 [해시 테이블](/powershell/module/microsoft.powershell.core/about/about_hash_tables)입니다.
  - 동적 매개 변수의 기본값은 정의에 있는 **defaultValue** 입니다.
  - 매개 변수가 제공되지 않고 **defaultValue** 를 포함하지 않는 경우 매개 변수는 선택 사항이 아닙니다.

    > [!NOTE]
    > **매개 변수** 가 secureStrings를 지원하지 않습니다.

- **ResourceGroupParameter**(선택 사항)
  - 리소스 그룹 아티팩트의 [해시 테이블](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 각 리소스 그룹 아티팩트 자리 표시자에는 리소스 그룹 아티팩트의 **Name** 및 **Location** 을 동적으로 설정하기 위한 키/값 쌍이 있습니다.
  - 리소스 그룹 매개 변수가 제공되지 않고 **defaultValue** 가 없는 경우 리소스 그룹 매개 변수는 선택 사항이 아닙니다.

다음 예에서는 잠금 모드를 변경하여 `Get-AzBlueprint`로 가져온 'my-blueprint' 청사진 정의의 버전 '1.1'에 대한 할당을 업데이트합니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

청사진 할당을 만들기 위한 출력 예는 다음과 같습니다.

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>청사진 할당 제거

청사진 할당을 제거할 시간이 된 경우 `Remove-AzBlueprintAssignment` cmdlet은 이 작업을 처리합니다. Cmdlet은 **Name** 또는 **InputObject** 중 하나를 사용하여 제거할 청사진 할당을 지정합니다. **SubscriptionId** 는 _필수_ 이며 모든 경우에 제공되어야 합니다.

다음 예에서는 `Get-AzBlueprintAssignment`을 사용하여 기존 청사진 할당을 가져온 다음 `{subId}`로 표시된 특정 구독에서 해당 할당을 제거합니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="code-example"></a>코드 예제

다음 예에서는 모든 단계를 함께 수행하여 청사진 정의를 가져온 다음, `{subId}`로 표시되는 특정 구독에 대한 청사진 할당을 만들고, 업데이트하고, 제거합니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결
