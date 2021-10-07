---
title: PowerShell - 랩에서 사용자 지정 역할 만들기
description: 이 문서에서는 Azure DevTest Labs의 랩에서 사용자 지정 역할을 만드는 Azure PowerShell 스크립트를 제공합니다.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: f217ac5a0e65d0952942a15b30f1f6727af24599
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654227"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>PowerShell을 사용하여 Azure DevTest Labs의 랩에 사용자 지정 역할 만들기

이 샘플 PowerShell 스크립트는 Azure DevTest Labs의 랩에서 사용할 사용자 지정 역할을 만듭니다. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>사전 요구 사항
* **랩**. 스크립트를 사용하려면 기존 랩이 있어야 합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Azure RBAC를 사용하여 보안을 설정할 수 있는 Azure 리소스 공급자에 대한 작업을 가져옵니다. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | 할당에 사용할 수 있는 모든 Azure 역할을 나열합니다. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | 사용자 지정 역할을 만듭니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.

추가 Azure Lab Services PowerShell 스크립트 샘플은 [Azure Lab Services PowerShell 샘플](../samples-powershell.md)에서 확인할 수 있습니다.
