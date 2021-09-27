---
title: Azure Key Vault 액세스 정책(CLI) 할당
description: Azure CLI를 사용하여 보안 주체 또는 애플리케이션 ID에 Key Vault 액세스 정책을 할당하는 방법입니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: b194bec5f03d5985e282b7c8d220e268de1a763c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838674"
---
# <a name="assign-a-key-vault-access-policy"></a>Key Vault 액세스 정책 할당

Key Vault 액세스 정책은 사용자, 애플리케이션, 사용자 그룹 등의 특정 보안 주체가 Key Vault [비밀](../secrets/index.yml), [키](../keys/index.yml), [인증서](../certificates/index.yml)에 대해 서로 다른 작업을 수행할 수 있는지 여부를 결정합니다. Azure Portal , Azure CLI [또는](assign-access-policy-powershell.md) [Azure PowerShell](assign-access-policy-portal.md)사용하여 액세스 정책을 할당할 수 있습니다.

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

## <a name="assign-an-access-policy"></a>액세스 정책 할당

1.  [Azure Portal](https://portal.azure.com)에서 Key Vault 리소스로 이동합니다. 

1.  **설정** 아래에서 **액세스 정책** 을 선택한 다음, **액세스 정책 추가** 를 선택합니다.

    ![액세스 정책을 선택하고 역할 할당 추가를 선택합니다.](../media/authentication/assign-policy-portal-01.png)

1.  **인증서 권한**, **키 권한** 및 **비밀 권한** 에서 원하는 권한을 선택합니다. 일반적인 권한 조합을 포함하는 템플릿을 선택할 수도 있습니다.

    ![액세스 정책 권한 지정](../media/authentication/assign-policy-portal-02.png)

1. **보안 주체 선택** 에서 **선택한 항목 없음** 링크를 선택하여 **주** 선택 창을 엽니다. 검색 필드에 사용자, 앱 또는 서비스 사용자의 이름을 입력하고 적절한 결과와 **선택** 을 차례로 선택합니다.

    ![액세스 정책에 대한 보안 주체 선택](../media/authentication/assign-policy-portal-03.png)

    앱에 관리되는 ID를 사용하는 경우 앱 자체의 이름을 검색하여 선택합니다. (보안 주체에 대한 자세한 내용은 [Key Vault 인증](authentication.md)을 참조하세요.
 
1.  **액세스 정책 추가** 창으로 돌아가서 **추가** 를 선택하여 액세스 정책을 저장합니다.

    ![할당된 보안 주체를 사용하여 액세스 정책 추가](../media/authentication/assign-policy-portal-04.png)

1. **액세스 정책** 페이지로 돌아가서 **현재 액세스 정책** 아래에 액세스 정책이 나열되는지 확인하고 **저장** 을 선택합니다. 액세스 정책은 저장할 때까지 적용되지 않습니다.

    ![액세스 정책 변경 내용 저장](../media/authentication/assign-policy-portal-05.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 Azure Active Directory에 그룹을 만드는 방법에 대한 자세한 내용은 [az ad group create](/cli/azure/ad/group#az_ad_group_create) 및 [az ad group member add](/cli/azure/ad/group/member#az_ad_group_member_add)를 참조하세요.

## <a name="configure-the-azure-cli-and-sign-in"></a>Azure CLI 구성 및 로그인

1. Azure CLI 명령을 로컬로 실행하려면 [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.
 
    클라우드에서 직접 명령을 실행하려면 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다.

1. 로컬 CLI만 해당: `az login`을 사용하여 Azure에 로그인합니다.

    ```bash
    az login
    ```

    필요한 경우 `az login` 명령은 브라우저 창을 열어 자격 증명을 수집합니다.

## <a name="acquire-the-object-id"></a>개체 ID 가져오기

액세스 정책을 할당할 애플리케이션, 그룹, 사용자의 개체 ID를 결정합니다.

- 애플리케이션 및 기타 서비스 사용자: [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) 명령을 사용하여 서비스 사용자를 검색합니다. 명령의 출력을 검사하여 액세스 정책을 할당할 보안 주체의 개체 ID를 확인합니다.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- 그룹: [az ad group list](/cli/azure/ad/group#az_ad_group_list) 명령을 사용하여 `--display-name` 매개 변수로 결과를 필터링합니다.

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- 사용자: [az ad user show](/cli/azure/ad/user#az_ad_user_show) 명령을 사용하여 `--id` 매개 변수에 사용자의 이메일 주소를 전달합니다.

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>액세스 정책 할당
    
[Az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) 명령을 사용하여 원하는 사용 권한을 할당합니다.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

`<object-id>`를 보안 주체의 개체 ID로 바꿉니다.

이러한 특정 유형에 권한을 할당할 때는 `--secret-permissions`, `--key-permissions`, `--certificate-permissions`만 포함해야 합니다. `<secret-permissions>`, `<key-permissions>` 및 `<certificate-permissions>`에 허용되는 값은 [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) 설명서에 제공됩니다.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Azure PowerShell을 사용하여 Azure Active Directory에서 그룹을 만드는 방법에 대한 자세한 내용은 [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) 및 [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember)를 참조하세요.

## <a name="configure-powershell-and-sign-in"></a>PowerShell 및 로그인 구성

1. 명령을 로컬에서 실행하려면 [Azure PowerShell](/powershell/azure/)을 설치합니다(아직 설치하지 않은 경우).

    클라우드에서 직접 명령을 실행하려면 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다.

1. 로컬 PowerShell만 해당:

    1. [Azure Active Directory PowerShell 모듈](https://www.powershellgallery.com/packages/AzureAD)을 설치합니다.

    1. Azure에 로그인:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>개체 ID 가져오기

액세스 정책을 할당하려는 애플리케이션, 그룹, 사용자의 개체 ID를 결정합니다.

- 애플리케이션 및 기타 서비스 주체: `-SearchString` 매개 변수가 있는 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) cmdlet을 사용하여 원하는 서비스 주체 이름으로 결과를 필터링합니다.

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- 그룹: `-SearchString` 매개 변수가 있는 [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) cmdlet을 사용하여 원하는 그룹 이름으로 결과를 필터링합니다.

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    출력에서 개체 ID는 `Id`로 나열됩니다.

- 사용자: [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet을 사용하여 사용자의 메일 주소를 `-UserPrincipalName` 매개 변수에 전달합니다.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    출력에서 개체 ID는 `Id`로 나열됩니다.

## <a name="assign-the-access-policy"></a>액세스 정책 할당

[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet을 사용하여 액세스 정책을 할당합니다.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

이러한 특정 유형에 권한을 할당할 때는 `-PermissionsToSecrets`, `-PermissionsToKeys`, `-PermissionsToCertificates`만 포함해야 합니다. `<secret-permissions>`, `<key-permissions>`, `<certificate-permissions>`에 허용되는 값은 [Set-AzKeyVaultAccessPolicy - 매개 변수](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters) 설명서에 제공됩니다.

---

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 보안](security-features.md)
- [Azure Key Vault 개발자 가이드](developers-guide.md)
