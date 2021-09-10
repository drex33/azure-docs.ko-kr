---
title: Azure Policy 리소스 내보내기
description: 정책 정의 및 정책 할당과 같은 Azure Policy 리소스를 GitHub으로 내보내는 방법을 알아봅니다.
ms.date: 08/17/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 170c619d41a416ac337edabcf3d4c0e0b5f24d4e
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965149"
---
# <a name="export-azure-policy-resources"></a>Azure Policy 리소스 내보내기

이 문서에서는 기존 Azure Policy 리소스를 내보내는 방법에 관한 정보를 제공합니다. 리소스 내보내기는 유용하고 백업에 권장되지만, 사용자 경험에서 클라우드 거버넌스를 사용하고 [policy-as-code](../concepts/policy-as-code.md)를 처리하는 중요한 단계이기도 합니다. Azure Policy 리소스는 [Azure Portal](#export-with-azure-portal), [Azure CLI](#export-with-azure-cli), [Azure PowerShell](#export-with-azure-powershell) 그리고 지원되는 각각의 SDK를 통해 내보낼 수 있습니다.

## <a name="export-with-azure-portal"></a>Azure Portal로 내보내기

> [!NOTE]
> Azure Portal에서 Azure Policy 리소스 내보내기는 Azure 소 버린 클라우드에서 사용할 수 없습니다.

Azure Portal에서 정책 정의를 내보내려면 다음 단계를 수행합니다.

1. **모든 서비스** 를 클릭한 후 **정책** 을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

1. Azure Policy 페이지의 왼쪽에서 **정의** 를 선택합니다.

1. **정의 내보내기** 단추를 사용하거나 정책 정의 행에서 줄임표를 선택한 다음, **정의 내보내기** 를 선택합니다.

1. **GitHub로 로그인** 단추를 선택합니다. 리소스를 내보내도록 Azure Policy에 권한을 부여하기 위해 아직 GitHub에 인증하지 않은 경우 새로 열리는 창에서 [GitHub 작업](https://github.com/features/actions)에 액세스를 검토하고 **AzureGitHubActions 권한 부여** 를 선택하여 내보내기 프로세스를 계속합니다. 완료되면 새 창이 자동으로 닫힙니다.

1. **기본 사항** 탭에서 다음 옵션을 설정하고, 페이지 아래쪽에서 **정책** 탭 또는 **다음: 정책** 단추를 선택합니다.

   - **리포지토리 필터**: 내 소유의 리포지토리만 보려면 _내 리포지토리_ 로 설정하고, GitHub 작업 액세스 권한을 부여한 모든 리포지토리를 보려면 _모든 리포지토리_ 로 설정합니다.
   - **리포지토리**: Azure Policy 리소스를 내보낼 리포지토리로 설정합니다.
   - **분기**: 리포지토리의 분기를 설정합니다. 기본 분기가 아닌 다른 분기를 사용하면 소스 코드에 추가로 병합하기 전에 업데이트의 유효성을 검사할 수 있습니다.
   - **디렉터리**: Azure Policy 리소스를 내보낼 _루트 수준 폴더_ 입니다. 이 디렉터리의 하위 폴더는 내보낸 리소스에 따라 만들어집니다.

1. **정책** 탭에서 줄임표를 선택하고 관리 그룹, 구독 또는 리소스 그룹의 조합을 선택하여 검색 범위를 설정합니다.

1. **정책 정의 추가** 단추를 사용하여 내보낼 개체 범위를 검색합니다. 열리는 측면 창에서 내보낼 각 개체를 선택합니다. 검색 상자 또는 유형을 사용하여 선택 항목을 필터링합니다. 모든 개체를 내보내기로 선택한 경우 페이지 맨 아래에 있는 **추가** 단추를 사용합니다.

1. 선택한 각 개체의 정책 정의에 대해 원하는 내보내기 옵션을 선택합니다(예: _정의만_ 또는 _정의 및 할당_). 그런 다음, **검토 + 내보내기** 탭을 선택하거나 페이지 맨 아래에서 **다음: 검토 + 내보내기** 단추를 선택합니다.

   > [!NOTE]
   > _정의 및 할당_ 옵션을 선택하면 정책 정의를 추가할 때 필터에 설정된 범위 내의 정책 할당만 내보내집니다.

1. **검토 + 내보내기** 탭에서 세부 정보가 일치하는지 확인한 다음, 페이지 맨 아래에 있는 **내보내기** 단추를 사용합니다.

1. GitHub 리포지토리, 분기 및 _루트 수준 폴더_ 를 확인하여 선택한 리소스가 소스 제어로 내보내지고 있는지 살펴봅니다.

Azure Policy 리소스는 선택한 GitHub 리포지토리 및 _루트 수준 폴더_ 내부의 다음 구조로 내보내집니다.

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Azure CLI로 내보내기

Azure Policy 정의, 이니셔티브, 할당을 [Azure CLI](/cli/azure/install-azure-cli)를 사용하여 JSON으로 내보낼 수 있습니다. 각 명령은 **name** 매개 변수를 사용하여 JSON을 가져올 개체를 지정합니다. **Name** 속성은 대개 _GUID_ 이며 개체의 **displayName** 이 아닙니다.

- 정의 - [az policy definition show](/cli/azure/policy/definition#az_policy_definition_show)
- 이니셔티브 - [az policy set-definition show](/cli/azure/policy/set-definition#az_policy_set_definition_show)
- 할당 - [az policy assignment show](/cli/azure/policy/assignment#az_policy_assignment_show)

다음은 _VirtualMachineStorage_ 라는 **이름** 이 있는 정책 정의를 위한 JSON을 가져오는 예제입니다.

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Azure PowerShell로 내보내기

Azure Policy 정의, 이니셔티브, 할당을 [Azure PowerShell](/powershell/azure/)를 사용하여 JSON으로 내보낼 수 있습니다. 각 cmdlet은 **Name** 매개 변수를 사용하여 JSON을 가져올 개체를 지정합니다. **Name** 속성은 대개 _GUID_ 이며 개체의 **displayName** 이 아닙니다.

- 정의 - [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- 이니셔티브 - [Get-AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- 할당 - [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

다음은 _VirtualMachineStorage_ 라는 **이름** 이 있는 정책 정의를 위한 JSON을 가져오는 예제입니다.

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage' | ConvertTo-Json -Depth 10
```

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 생성](programmatically-create.md)하는 방법을 이해합니다.
- [규정 비준수 리소스를 수정](remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
