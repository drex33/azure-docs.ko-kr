---
title: '빠른 시작: Azure PowerShell을 사용하여 관리 그룹 만들기'
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 리소스를 리소스 계층 구조로 구성하는 관리 그룹을 만듭니다.
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, mode-api
ms.openlocfilehash: 0c84d1e2fc3438daad041a3627b546893a7073b2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131081147"
---
# <a name="quickstart-create-a-management-group-with-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 관리 그룹 만들기

관리 그룹은 여러 구독에서 액세스, 정책 및 규정 준수를 관리하는 데 도움이 되는 컨테이너입니다. 이러한 컨테이너를 만들어 [Azure 정책](../policy/overview.md) 및 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)와 함께 사용할 수 있는 효과적이고 효율적인 계층을 구축합니다. 관리 그룹에 대한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](overview.md)을 참조하세요.

디렉터리에서 만드는 첫 번째 관리 그룹을 완료하려면 최대 15분이 소요될 수 있습니다. 디렉터리에 대해 Azure 내의 관리 그룹 서비스를 설정하기 위해 처음으로 실행되는 프로세스가 있습니다. 프로세스가 완료되면 알림이 수신됩니다. 자세한 내용은 [관리 그룹의 초기 설정](./overview.md#initial-setup-of-management-groups)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

- 시작하기 전에, 최신 버전의 Azure PowerShell을 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

- 테넌트의 모든 Azure AD 사용자는 [계층 구조 보호](./how-to/protect-resource-hierarchy.md#setting---require-authorization)가 활성화되지 않은 경우 해당 사용자에게 할당된 관리 그룹 쓰기 권한 없이 관리 그룹을 만들 수 있습니다. 이 새 관리 그룹은 루트 관리 그룹 또는 [기본 관리 그룹](./how-to/protect-resource-hierarchy.md#setting---default-management-group)의 자식이 되며 작성자에게 "소유자" 역할 할당이 부여됩니다. 관리 그룹 서비스는 이 기능을 허용하므로 루트 수준에서 역할 할당이 필요하지 않습니다. 루트 관리 그룹이 생성될 때 사용자는 액세스할 수 있는 권한이 없습니다. 관리 그룹 사용을 시작하기 위해 Azure AD 전역 관리자를 찾는 장애물을 방지하기 위해 루트 수준에서 초기 관리 그룹을 만들 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-azure-powershell"></a>Azure PowerShell에서 만들기

PowerShell의 경우 [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) cmdlet을 사용하여 새 관리 그룹을 만듭니다. 이 예제에서 관리 그룹 **GroupName** 은 _Contoso_ 입니다.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** 은 생성되는 고유 식별자입니다. 이 ID는 다른 명령에서 이 그룹을 참조하는 데 사용되며 나중에 변경할 수 없습니다.

관리 그룹이 Azure Portal 내에서 다른 이름을 표시하도록 하려면 **DisplayName** 매개 변수를 추가합니다. 예를 들어, GroupName이 Contoso이고 표시 이름이 "Contoso Group"인 관리 그룹을 만들려면 다음 cmdlet을 사용합니다.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

앞의 예제에서 새 관리 그룹은 루트 관리 그룹 아래에 생성됩니다. 다른 관리 그룹을 부모로 지정하려면 **ParentId** 매개 변수를 사용합니다.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

## <a name="clean-up-resources"></a>리소스 정리

위에서 만든 관리 그룹을 제거하려면 [Remove-AzManagementGroup](/powershell/module/az.resources/remove-azmanagementgroup) cmdlet을 사용합니다.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 리소스 계층 구조를 구성하기 위한 관리 그룹을 만들었습니다. 관리 그룹은 구독 또는 기타 관리 그룹을 포함할 수 있습니다.

관리 그룹 및 리소스 계층 구조를 관리하는 방법에 대해 자세히 알아보려면 다음을 계속 진행하세요.

> [!div class="nextstepaction"]
> 관리 그룹으로 리소스 관리
