---
title: Azure 파일 공유에 대한 액세스 제어 - 온-프레미스 AD DS 인증
description: 스토리지 계정을 나타내는 Active Directory Domain Services ID에 사용 권한을 할당하는 방법을 알아봅니다. 이렇게 하면 ID 기반 인증을 사용하여 액세스를 제어할 수 있습니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 07/13/2021
ms.author: rogarana
ms.custom: devx-track-azurepowershell, subject-rbac-steps
ms.openlocfilehash: cf2d9c2921599680781695631eae9c5276ff53c2
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400648"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>2부: ID에 공유 수준 권한 할당

이 문서를 시작하기 전에 이전 문서인 [계정에 AD DS 인증 사용](storage-files-identity-ad-ds-enable.md)을 완료했는지 확인합니다.

스토리지 계정에 AD DS(Active Directory Domain Services) 인증을 사용하도록 설정한 후에는 파일 공유에 대한 액세스 권한을 얻기 위해 공유 수준 권한을 구성해야 합니다. 공유 수준 권한을 할당하는 방법에는 두 가지가 있습니다. 특정 Azure AD 사용자/사용자 그룹에 할당할 수 있으며 모든 인증된 ID에 기본 공유 수준 권한으로 할당할 수 있습니다.

> [!IMPORTANT]
> 파일 소유권을 가져오는 기능을 포함하여 파일 공유에 대한 모든 관리 권한을 사용하려면 스토리지 계정 키가 있어야 합니다. Azure AD 자격 증명으로는 관리 권한이 지원되지 않습니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="which-configuration-should-you-use"></a>어떤 구성을 사용해야 하나요?

대부분의 사용자는 특정 Azure AD 사용자 또는 그룹에 공유 수준 권한을 할당한 다음, 디렉터리 및 파일 수준에서 세분화된 액세스 제어를 위해 Windows ACL을 사용해야 합니다. 이는 가장 엄격하고 안전한 구성입니다.

인증된 모든 ID에 할당된 기본 공유 수준 권한을 대신 사용하도록 권장하는 세 가지 시나리오가 있습니다.

- 온-프레미스 AD DS를 Azure AD와 동기화할 수 없는 경우 기본 공유 수준 권한을 사용할 수도 있습니다. 기본 공유 수준 권한을 할당하면 Azure AD에서 ID에 대한 권한을 지정할 필요가 없으므로 동기화 요구 사항을 해결할 수 있습니다. 그런 다음, 파일 및 디렉터리에 대한 세분화된 권한 적용을 위해 Windows ACL을 사용할 수 있습니다.
- 사용 중인 온-프레미스 AD DS는 파일 공유가 배포된 Azure AD와 다른 Azure AD에 동기화됩니다.
    - 이는 다중 테넌트 환경을 관리하는 경우에 일반적입니다. 기본 공유 수준 권한을 사용하면 Azure AD 하이브리드 ID에 대한 요구 사항을 무시할 수 있습니다. 세분화된 권한 적용을 위해 파일 및 디렉터리에서 Windows ACL을 계속 사용할 수 있습니다.
- 파일 및 디렉터리 수준에서 Windows ACL만 사용하여 인증을 적용하는 것이 좋습니다. 

## <a name="share-level-permissions"></a>공유 수준 권한

다음 표에는 공유 수준 권한과 이를 기본 제공 RBAC 역할에 맞게 조정하는 방법이 나와 있습니다.

|지원되는 기본 제공 역할  |설명  |
|---------|---------|
|[Storage 파일 데이터 SMB 공유 읽기 권한자](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-reader)     |Azure 파일 공유의 파일 및 디렉터리에 대한 읽기 액세스를 허용합니다. 이 역할은 Windows 파일 서버에서 읽기의 파일 공유 ACL과 유사합니다. [자세히 알아보기](storage-files-identity-auth-active-directory-enable.md).         |
|[Storage 파일 데이터 SMB 공유 기여자](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-contributor)     |Azure 파일 공유의 파일 및 디렉터리에 대한 읽기, 쓰기 및 삭제 액세스를 허용합니다. [자세히 알아보기](storage-files-identity-auth-active-directory-enable.md).         |
|[Storage 파일 데이터 SMB 공유 높은 권한 기여자](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-elevated-contributor)     |Azure 파일 공유의 파일 및 디렉터리에 대한 읽기, 쓰기, 삭제 및 수정 ACL을 허용합니다. 이 역할은 Windows 파일 서버에서 변경의 파일 공유 ACL과 유사합니다. [자세히 알아보기](storage-files-identity-auth-active-directory-enable.md).         |


## <a name="share-level-permissions-for-specific-azure-ad-users-or-groups"></a>특정 Azure AD 사용자 또는 그룹에 대한 공유 수준 권한

특정 Azure AD 사용자 또는 그룹을 사용 하 여 Azure 파일 공유 리소스에 액세스 하려는 경우 해당 id는 온 **-프레미스 AD DS와 AZURE AD에 모두 있는 하이브리드 id** 여야 합니다. 예를 들어 AD에 user1@onprem.contoso.com인 사용자가 있고 Azure AD Connect 동기화를 사용하여 user1@contoso.com으로 Azure AD에 동기화했다고 가정해 보겠습니다. 이 사용자가 Azure Files에 액세스할 수 있도록 하려면 user1@contoso.com에 공유 수준 권한을 할당해야 합니다. 그룹 또는 서비스 주체에도 동일한 개념이 적용됩니다. 따라서 Azure AD Connect 동기화를 사용하여 AD에서 Azure AD로 사용자 및 그룹을 동기화해야 합니다. 

Azure 파일 공유에 대한 AD DS 인증을 지원하려면 AD DS의 동일한 사용자 또는 그룹을 나타내는 Azure AD ID에 공유 수준 권한을 할당해야 합니다. Azure 관리 ID(MSI)와 같이 Azure AD에만 있는 ID에 대한 인증 및 권한 부여는 AD DS 인증에서 지원되지 않습니다.

Azure Portal, Azure PowerShell 모듈 또는 Azure CLI를 사용하여 공유 수준 권한을 부여하기 위해 사용자의 Azure AD ID에 기본 제공 역할을 할당할 수 있습니다.

> [!IMPORTANT]
> 완료 된 후에 적용 되는 공유 수준 권한은 3 시간 정도 소요 됩니다. 자격 증명을 사용 하 여 파일 공유에 연결 하기 전에 사용 권한이 동기화 될 때까지 기다려 주세요.   

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure Portal](https://portal.azure.com)을 사용하여 Azure AD ID에 Azure 역할을 할당하려면 다음 단계를 수행합니다.

1. Azure Portal에서 파일 공유로 이동하거나 [파일 공유를 만듭니다](storage-how-to-create-file-share.md).
1. **Access Control(IAM)** 을 선택합니다.
1. **역할 할당 추가** 를 선택합니다.
1. **역할 할당 추가** 블레이드의 **역할** 목록에서 [적절한 기본 제공 역할](#share-level-permissions)을 선택합니다.
    1. 스토리지 파일 데이터 SMB 공유 Reader
    1. 스토리지 파일 데이터 SMB 공유 Contributor
    1. 스토리지 파일 데이터 SMB 공유 관리자 권한 Contributor
1.  **다음에 대한 액세스 할당** 은 기본 설정인 **Azure AD 사용자, 그룹 또는 서비스 주체** 로 그대로 둡니다. 이름 또는 메일 주소를 기준으로 대상 Azure AD ID를 선택합니다. **선택한 Azure AD ID는 하이브리드 ID여야 하며 클라우드 전용 ID일 수 없습니다.** 즉, 동일한 ID가 AD DS에도 표시됩니다.
1. **저장** 을 선택하여 역할 할당 작업을 완료합니다.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

다음 PowerShell 샘플은 로그인 이름을 기준으로 Azure AD ID에 Azure 역할을 할당하는 방법을 보여 줍니다. PowerShell을 사용하여 Azure 역할을 할당하는 방법에 대한 자세한 내용은 [Azure PowerShell 모듈을 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-powershell.md)를 참조하세요.

다음 샘플 스크립트를 실행하기 전에 대괄호를 포함한 자리 표시자 값을 원하는 값으로 바꿉니다.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
  
다음 CLI 2.0 명령은 로그인 이름을 기준으로 Azure AD ID에 Azure 역할을 할당합니다. Azure CLI를 사용하여 Azure 역할을 할당하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-cli.md)를 참조하세요. 

다음 샘플 스크립트를 실행하기 전에 대괄호를 포함한 자리 표시자 값을 원하는 값으로 바꾸어야 합니다.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="share-level-permissions-for-all-authenticated-identities"></a>모든 인증된 ID에 대한 공유 수준 권한

Azure AD 사용자 또는 그룹에 대한 공유 수준 권한을 구성하는 대신 스토리지 계정에 대한 기본 공유 수준 권한을 추가할 수 있습니다. 스토리지 계정에 할당된 기본 공유 수준 권한은 스토리지 계정에 포함된 모든 파일 공유에 적용됩니다. 

기본 공유 수준 권한을 설정하면 인증된 모든 사용자와 그룹에 동일한 권한이 부여됩니다. 인증된 사용자 또는 그룹은 스토리지 계정이 연결된 온-프레미스 AD DS에 대해 ID를 인증할 수 있는 것으로 식별됩니다. 기본 공유 수준 권한은 초기화 시 **없음** 으로 설정되어 Azure 파일 공유의 파일 및 디렉터리에 대한 액세스가 허용되지 않습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

현재 Azure Portal을 사용하여 스토리지 계정에 권한을 할당할 수 없습니다. 대신 Azure PowerShell 모듈 또는 Azure CLI를 사용합니다.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

다음 스크립트를 사용하여 스토리지 계정에 대한 기본 공유 수준 권한을 구성할 수 있습니다. 파일 인증을 위해 디렉터리 서비스와 연결된 스토리지 계정에 대해서만 기본 공유 수준 권한을 사용하도록 설정할 수 있습니다. 

다음 스크립트를 실행하기 전에 Az.Storage 모듈이 버전 3.7.0 이상인지 확인합니다.

```azurepowershell
$defaultPermission = "None|StorageFileDataSmbShareContributor|StorageFileDataSmbShareReader|StorageFileDataSmbShareElevatedContributor" # Set the default permission of your choice

$account = Set-AzStorageAccount -ResourceGroupName "<resource-group-name-here>" -AccountName "<storage-account-name-here>" -DefaultSharePermission $defaultPermission

$account.AzureFilesIdentityBasedAuth
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 스크립트를 사용하여 스토리지 계정에 대한 기본 공유 수준 권한을 구성할 수 있습니다. 파일 인증을 위해 디렉터리 서비스와 연결된 스토리지 계정에 대해서만 기본 공유 수준 권한을 사용하도록 설정할 수 있습니다. 

다음 스크립트를 실행하기 전에 Azure CLI가 버전 2.24.1 이상인지 확인합니다.

```azurecli
# Declare variables
storageAccountName="YourStorageAccountName"
resourceGroupName="YourResourceGroupName"
defaultPermission="None|StorageFileDataSmbShareContributor|StorageFileDataSmbShareReader|StorageFileDataSmbShareElevatedContributor" # Set the default permission of your choice


az storage account update --name $storageAccountName --resource-group $resourceGroupName --default-share-permission $defaultPermission
```
---

## <a name="what-happens-if-you-use-both-configurations"></a>두 구성을 모두 사용하면 어떻게 되나요?

인증된 모든 Azure AD 사용자 및 특정 Azure AD 사용자/그룹에 권한을 할당할 수도 있습니다. 이 구성을 사용하면 특정 사용자 또는 그룹이 기본 공유 수준 권한 및 RBAC 할당에서 허용되는 상위 권한 집합을 갖게 됩니다. 이것이 어떻게 작동하는지 이해하는 데 도움이 되도록 사용자에게 대상 파일 공유에 대해 **스토리지 파일 데이터 SMB 읽기 권한자** 역할을 부여했다고 가정해 보겠습니다. 또한 인증된 모든 사용자에게 기본 공유 수준 권한인 **스토리지 파일 데이터 SMB 공유의 승격된 기여자** 을 부여했습니다. 이 구성을 사용하면 해당 사용자는 파일 공유에 대해 **스토리지 파일 데이터 SMB 공유의 승격된 기여자** 수준의 액세스 권한을 갖게 됩니다. 높은 수준의 권한이 항상 우선 적용됩니다.

## <a name="next-steps"></a>다음 단계

이제 공유 수준 권한을 할당했으므로 디렉터리 및 파일 수준 권한을 구성해야 합니다. 다음 문서를 계속 진행합니다.

[3부: SMB를 통한 디렉터리 및 파일 수준 권한 구성](storage-files-identity-ad-ds-configure-permissions.md)
