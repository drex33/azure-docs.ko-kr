---
title: 사용자가 할당한 관리 ID 관리 - Azure AD
description: 사용자가 할당한 관리 ID 만들기
services: active-directory
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2021
ms.author: barclayn
zone_pivot_groups: identity-mi-methods
ms.openlocfilehash: 83f47dbbdd0968a845871b106be9142fd67f9967
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "122642225"
---
# <a name="manage-user-assigned-managed-identities"></a>사용자 할당 관리 ID 관리



Azure 리소스에 대해 관리 ID를 사용하면 코드로 자격 증명을 관리할 필요가 없습니다. 이를 사용하여 Azure AD 인증을 지원하는 리소스에 액세스할 때 애플리케이션에서 사용할 수 있는 Azure AD(Azure Active Directory) 토큰을 얻을 수 있습니다. Azure에서 이 ID를 관리하므로 사용자가 직접 관리할 필요는 없습니다.

관리 ID는 시스템 할당 및 사용자 할당의 두 가지 유형이 있습니다. 두 형식 간의 주요 차이점은 시스템이 할당한 관리 ID의 수명 주기가 사용되는 리소스에 연결된다는 것입니다. 사용자가 할당한 관리 ID는 여러 리소스에서 사용할 수 있습니다. 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](overview.md)을 참조하세요.

::: zone pivot="identity-mi-methods-azp"
이 문서에서는 Azure Portal을 사용하여 사용자가 할당한 관리 ID를 만들고 나열하고 삭제하거나 이 ID에 역할을 할당하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. [시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#managed-identity-types)을 반드시 검토하세요.
- 아직 Azure 계정이 없으면 계속하기 전에 [체험 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.


## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

사용자 할당 관리 ID를 만들려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

1. 사용자가 할당한 관리 ID를 만들려면 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 상자에 **관리 ID** 를 입력합니다. **서비스** 에서 **관리 ID** 를 선택합니다.
1. **추가** 를 선택하고 **사용자가 할당한 관리 ID 만들기** 창에서 다음 상자에 값을 입력합니다.
    - **구독**: 사용자가 할당한 관리 ID를 만들 구독을 선택합니다.
    - **리소스 그룹**: 사용자가 할당한 관리 ID를 만들 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하여 새 리소스 그룹을 만듭니다.
    - **지역**: 사용자가 할당한 관리 ID를 배포할 지역(예: **미국 서부**)을 선택합니다.
    - **이름**: 사용자가 할당한 관리 ID의 이름(예: UAI1)입니다.
  
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]
  
   ![사용자가 할당한 관리 ID 만들기 창을 보여 주는 스크린샷](media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)
1. **검토 + 만들기** 를 선택하여 변경 내용을 검토합니다.
1. **만들기** 를 선택합니다.

## <a name="list-user-assigned-managed-identities"></a>사용자 할당 관리 ID 나열

사용자가 할당한 관리 ID를 나열하거나 읽으려면 계정에 [관리 ID 운영자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 또는 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

1. 사용자가 할당한 관리 ID를 나열하려면 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 상자에 **관리 ID** 를 입력합니다. **서비스** 에서 **관리 ID** 를 선택합니다.
1. 구독의 사용자 할당 관리 ID 목록이 반환됩니다. 사용자가 할당한 관리 ID 세부 정보를 보려면 해당 이름을 선택합니다.

   ![사용자가 할당한 관리 ID 목록을 보여 주는 스크린샷](media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 삭제

사용자 할당 관리 ID를 삭제하려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자가 할당한 ID를 삭제해도 ID가 할당된 VM이나 리소스에서는 ID가 제거되지 않습니다. 사용자가 할당한 ID를 VM에서 제거하려는 경우 [Remove a user-assigned managed identity from a VM](qs-configure-portal-windows-vm.md#remove-a-user-assigned-managed-identity-from-a-vm)(VM에서 사용자가 할당한 관리 ID 제거)을 참조하세요.

1. 사용자가 할당한 관리 ID를 삭제하려면 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 사용자가 할당한 관리 ID를 선택하고 **삭제** 를 선택합니다.
1. 확인 상자에서 **예** 를 선택합니다.

   ![사용자가 할당한 관리 ID 삭제를 보여 주는 스크린샷](media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID에 역할 할당 

사용자 할당 관리 ID에 역할을 할당하려면 계정에 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) 역할 할당이 필요합니다.

1. 사용자가 할당한 관리 ID를 나열하려면 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 상자에 **관리 ID** 를 입력합니다. **서비스** 에서 **관리 ID** 를 선택합니다.
1. 구독의 사용자 할당 관리 ID 목록이 반환됩니다. 역할을 할당하려는 사용자 할당 관리 ID를 선택합니다.
1. **액세스 제어(IAM)** , **역할 할당 추가** 를 차례로 선택합니다.

   ![사용자가 할당한 관리 ID 시작을 보여 주는 스크린샷](media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

1. **역할 할당 추가** 창에서 다음 값을 구성하고 **저장** 을 선택합니다.
   - **역할**: 할당할 역할입니다.
   - **다음에 대한 액세스 할당**: 사용자가 할당한 관리 ID를 할당할 리소스입니다.
   - **선택**: 액세스를 할당할 구성원입니다.
   
   ![사용자가 할당한 관리 ID IAM을 보여 주는 스크린샷](media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)



::: zone-end



::: zone pivot="identity-mi-methods-azcli"

이 문서에서는 Azure CLI를 사용하여 사용자가 할당한 관리 ID를 만들고 나열하고 삭제하거나 이 ID에 역할을 할당하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. *[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#managed-identity-types)을 반드시 검토하세요*.
- 아직 Azure 계정이 없으면 계속하기 전에 [체험 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.


[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!IMPORTANT]  
> CLI를 사용하는 앱 서비스 주체를 사용할 때 사용자 권한을 수정하려면 CLI의 일부에서 Graph API에 대해 GET 요청을 수행하는 서비스 주체에 Azure Active Directory Graph API의 추가 권한을 제공해야 합니다. 그러지 않으면 “작업을 완료할 수 있는 권한이 없습니다.” 메시지가 표시될 수 있습니다. 이 단계를 수행하려면 Azure AD의 **앱 등록** 으로 이동하여 앱을 선택하고 **API 권한** 을 선택한 다음, 아래로 스크롤하여 **Azure Active Directory Graph** 를 선택합니다. 여기에서 **애플리케이션 권한** 을 선택하고 적절한 권한을 추가합니다. 

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기 

사용자 할당 관리 ID를 만들려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 만들려면 [az identity create](/cli/azure/identity#az_identity_create) 명령을 사용합니다. `-g` 매개 변수는 사용자가 할당한 관리 ID를 만들 리소스 그룹을 지정합니다. `-n` 매개 변수는 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꿉니다.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>사용자 할당 관리 ID 나열

사용자가 할당한 관리 ID를 나열하거나 읽으려면 계정에 [관리 ID 운영자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 또는 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 나열하려면 [az identity list](/cli/azure/identity#az_identity_list) 명령을 사용합니다. `<RESOURCE GROUP>` 값을 원하는 값으로 바꿉니다.

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

JSON 응답에서 사용자가 할당한 관리 ID에는 키 `type`에 대해 반환된 `"Microsoft.ManagedIdentity/userAssignedIdentities"` 값이 있습니다.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 삭제

사용자 할당 관리 ID를 삭제하려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 삭제하려면 [az identity delete](/cli/azure/identity#az_identity_delete) 명령을 사용합니다. -n 매개 변수는 이름을 지정합니다. -g 매개 변수는 사용자가 할당한 관리 ID가 만들어진 리소스 그룹을 지정합니다. `<USER ASSIGNED IDENTITY NAME>` 및 `<RESOURCE GROUP>` 매개 변수 값을 원하는 값으로 바꿉니다.

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> 사용자가 할당한 관리 ID를 삭제해도 할당된 리소스에서 참조는 제거되지 않습니다. `az vm/vmss identity remove` 명령을 사용하여 VM 또는 가상 머신 확장 집합에서 제거합니다.

## <a name="next-steps"></a>다음 단계

Azure CLI identity 명령의 전체 목록을 보려면 [az identity](/cli/azure/identity)를 참조하세요.

사용자가 할당한 관리 ID를 Azure VM에 할당하는 방법에 대한 내용은 [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)을 참조하세요.


::: zone-end

::: zone pivot="identity-mi-methods-powershell"

이 문서에서는 PowerShell을 사용하여 사용자가 할당한 관리 ID를 만들고 나열하고 삭제하거나 이 ID에 역할을 할당하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. *[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#managed-identity-types)을 반드시 검토하세요*.
- 아직 Azure 계정이 없으면 계속하기 전에 [체험 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 예제 스크립트를 실행하려면 다음 두 가지 옵션을 사용합니다.
    - 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보기** 단추를 사용하여 열 수 있는 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다.
    - 다음 섹션에 설명된 대로 Azure PowerShell을 사용하여 로컬로 스크립트를 실행합니다.

이 문서에서는 PowerShell을 사용하여 사용자가 할당한 관리 ID를 만들고 나열하고 삭제하는 방법을 알아봅니다.

### <a name="configure-azure-powershell-locally"></a>로컬로 Azure PowerShell 구성

Cloud Shell을 사용하는 대신 이 문서에서 Azure PowerShell을 로컬로 사용하려면 다음을 수행합니다.

1. 아직 설치하지 않은 경우 [Azure PowerShell 최신 버전](/powershell/azure/install-az-ps)을 설치합니다.

1. Azure에 로그인합니다.

    ```azurepowershell
    Connect-AzAccount
    ```

1. [PowerShellGet 최신 버전](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget)을 설치합니다.

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    다음 단계에서 이 명령을 실행한 후 현재 PowerShell 세션에서 `Exit`를 제거해야 합니다.

1. `Az.ManagedServiceIdentity` 모듈의 시험판 버전을 설치하여 이 문서에 있는 사용자가 할당한 관리 ID 작업을 수행합니다.

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

사용자 할당 관리 ID를 만들려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 만들려면 `New-AzUserAssignedIdentity` 명령을 사용합니다. `ResourceGroupName` 매개 변수는 사용자가 할당한 관리 ID를 만들 리소스 그룹을 지정합니다. `-Name` 매개 변수는 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꿉니다.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```

## <a name="list-user-assigned-managed-identities"></a>사용자 할당 관리 ID 나열

사용자가 할당한 관리 ID를 나열하거나 읽으려면 계정에 [관리 ID 운영자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 또는 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 나열하려면 [Get-AzUserAssigned] 명령을 사용합니다. `-ResourceGroupName` 매개 변수는 사용자 할당 관리 ID가 만들어지는 리소스 그룹을 지정합니다. `<RESOURCE GROUP>` 값을 원하는 값으로 바꿉니다.

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```

응답에서 사용자가 할당한 관리 ID에는 키 `Type`에 대해 반환된 `"Microsoft.ManagedIdentity/userAssignedIdentities"` 값이 있습니다.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 삭제

사용자 할당 관리 ID를 삭제하려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 삭제하려면 `Remove-AzUserAssignedIdentity` 명령을 사용합니다. `-ResourceGroupName` 매개 변수는 사용자가 할당한 ID가 만들어진 리소스 그룹을 지정합니다. `-Name` 매개 변수는 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꿉니다.

```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```

> [!NOTE]
> 사용자가 할당한 관리 ID를 삭제해도 할당된 리소스에서 참조는 제거되지 않습니다. ID 할당은 별도로 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure 리소스용 Azure PowerShell 관리 ID 명령의 전체 목록 및 자세한 내용은 [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity)를 참조하세요.


::: zone-end


::: zone pivot="identity-mi-methods-arm"

이 문서에서는 Azure Resource Manager를 사용하여 사용자가 할당한 관리 ID를 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. *[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#managed-identity-types)을 반드시 검토하세요*.
- 아직 Azure 계정이 없으면 계속하기 전에 [체험 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.

Resource Manager 템플릿을 사용하여 사용자가 할당한 관리 ID를 나열하고 삭제할 수 없습니다. 사용자 할당 관리 ID를 만들고 나열하려면 아래 문서를 참조하세요.

- [사용자 할당 관리 ID 나열](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [사용자 할당 관리 ID 삭제](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)

## <a name="template-creation-and-editing"></a>템플릿 만들기 및 편집

Azure Portal 및 스크립팅을 사용할 때와 마찬가지로, Resource Manager 템플릿에서도 Azure 리소스 그룹으로 정의된 새 리소스 또는 수정된 리소스를 배포하는 기능을 제공합니다. 로컬 및 포털 기반 템플릿 편집 및 배포에 여러 가지 옵션이 제공됩니다. 다음과 같습니다.

- [Azure Marketplace의 사용자 지정 템플릿](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)을 사용하여 템플릿을 새로 만들거나 기존의 일반 템플릿 또는 [빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)을 기반으로 템플릿을 만듭니다.
- [원본 배포](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates) 또는 [배포의 현재 상태](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)에서 템플릿을 내보내 기존 리소스 그룹에서 템플릿을 파생합니다.
- 로컬 [JSON 편집기(예: VS Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)를 사용한 다음, PowerShell 또는 Azure CLI를 사용하여 템플릿을 업로드하고 배포합니다.
- Visual Studio [Azure 리소스 그룹 프로젝트](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)를 사용하여 템플릿을 만들고 배포합니다. 

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기 

사용자 할당 관리 ID를 만들려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 만들려면 다음 템플릿을 사용하세요. `<USER ASSIGNED IDENTITY NAME>` 값을 원하는 값으로 바꿉니다.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>다음 단계

Resource Manager 템플릿을 사용하여 사용자가 할당한 관리 ID를 Azure VM에 할당하는 방법에 대한 내용은 [템플릿을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-template-windows-vm.md)을 참조하세요.




::: zone-end


::: zone pivot="identity-mi-methods-rest"

이 문서에서는 REST를 사용하여 사용자가 할당한 관리 ID를 만들고 나열하고 삭제하는 방법을 알아봅니다.


## <a name="prerequisites"></a>필수 구성 요소

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. *[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#managed-identity-types)을 반드시 검토하세요*.
- 아직 Azure 계정이 없으면 계속하기 전에 [체험 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 이 문서의 모든 명령은 클라우드에서 또는 로컬로 실행할 수 있습니다.
    - 클라우드에서 실행하려면 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다.
    - 로컬로 실행하려면 [넘기기](https://curl.haxx.se/download.html)와 [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.


이 문서에서는 CURL을 통해 REST API를 호출하여 사용자가 할당한 관리 ID 만들고 나열하고 삭제하는 방법을 알아봅니다.

## <a name="obtain-a-bearer-access-token"></a>전달자 액세스 토큰 가져오기

1. 로컬로 실행하는 경우 Azure CLI를 통해 Azure에 로그인합니다.

    ```
    az login
    ```

1. [Az account get-token](/cli/azure/account#az_account_get_access_token)을 사용하여 액세스 토큰을 가져옵니다.

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기 

사용자 할당 관리 ID를 만들려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**요청 헤더**

|요청 헤더  |Description  |
|---------|---------|
|*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
|*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        |

**요청 본문**

|이름  |설명  |
|---------|---------|
|위치     | 필수 요소. 리소스 위치.        |

## <a name="list-user-assigned-managed-identities"></a>사용자 할당 관리 ID 나열

사용자가 할당한 관리 ID를 나열하거나 읽으려면 계정에 [관리 ID 운영자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 또는 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|요청 헤더  |Description  |
|---------|---------|
|*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
|*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        |

## <a name="delete-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 삭제

사용자 할당 관리 ID를 삭제하려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

> [!NOTE]
> 사용자가 할당한 관리 ID를 삭제해도 할당된 리소스에서 참조는 제거되지 않습니다. CURL을 사용하여 VM에서 사용자가 할당한 관리 ID를 제거하려면 [Remove a user-assigned identity from an Azure VM](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm)(Azure VM에서 사용자가 할당한 ID 제거)을 참조하세요.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|요청 헤더  |Description  |
|---------|---------|
|*Content-Type*     | 필수 사항입니다. `application/json`로 설정합니다.        |
|*권한 부여*     | 필수 사항입니다. 유효한 `Bearer` 액세스 토큰으로 설정합니다.        |

## <a name="next-steps"></a>다음 단계

CURL을 사용하여 사용자가 할당한 관리 ID를 Azure VM 또는 가상 머신 확장 집합에 지정하는 방법에 대한 내용은 다음을 참조하세요.
- [REST API 호출을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-rest-vm.md#user-assigned-managed-identity) 
- [REST API 호출을 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-rest-vmss.md#user-assigned-managed-identity)

::: zone-end


