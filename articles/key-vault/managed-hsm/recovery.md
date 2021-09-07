---
title: Azure Key Vault 관리형 HSM 복구 개요 | Microsoft Docs
description: 관리형 HSM 복구 기능은 HSM 리소스 및 키를 실수로 또는 악의적으로 삭제할 수 없게 하도록 설계되었습니다.
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: how-to
ms.author: mbaldwin
author: mbaldwin
ms.date: 06/01/2021
ms.openlocfilehash: c0688d40e0ccb71b98e598da7d5ff8100b735229
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122567754"
---
# <a name="managed-hsm-soft-delete-and-purge-protection"></a>관리형 HSM 일시 삭제. 및 제거 보호

이 문서에서는 관리형 HSM의 두 가지 복구 기능인 일시 삭제 및 제거 보호에 대해 설명합니다. 여기서는 이러한 기능의 개요를 제공하고 Azure CLI 및 Azure PowerShell을 통해 해당 기능을 관리하는 방법을 설명합니다.

자세한 내용은 [관리형 HSM 개요](overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 [체험 계정 만들기](https://azure.microsoft.com/free/dotnet)
* [PowerShell 모듈](/powershell/azure/install-az-ps).
* Azure CLI 2.25.0 이상. `az --version`을 실행하여 보유한 버전을 확인합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.
* 관리형 HSM. [Azure CLI](./quick-create-cli.md) 또는 [Azure PowerShell](./quick-create-powershell.md)을 사용하여 하나 만들 수 있습니다.
* 일시 삭제된 HSM 또는 키에 대한 작업을 수행하려면 사용자에게 다음 권한이 필요합니다.

  | 역할 할당 | 설명 |
  |---|---|
  |[관리형 HSM 기여자](../../role-based-access-control/built-in-roles.md#managed-hsm-contributor)|일시 삭제된 HSM 나열, 복구 및 제거|
  |[관리형 HSM Crypto 사용자](./built-in-roles.md)|일시 삭제된 키 나열|
  |[관리형 HSM Crypto 책임자](./built-in-roles.md)|일시 삭제된 키 제거 및 복구|



## <a name="what-are-soft-delete-and-purge-protection"></a>일시 삭제 및 제거 보호란?

[일시 삭제](soft-delete-overview.md) 및 제거 보호는 복구 기능입니다.


*일시 삭제* 는 HSM 및 키를 실수로 삭제하지 않도록 설계되었습니다. 일시 삭제는 휴지통처럼 작동합니다. HSM 또는 키를 삭제하면 구성 가능한 보존 기간 또는 기본 기간인 90일 동안 복구 가능한 상태로 유지됩니다. 일시 삭제된 상태의 HSM 및 키를 제거하여 영구적으로 삭제할 수 있습니다. 제거를 통해 제거된 항목과 이름이 같은 HSM 및 키를 다시 만들 수 있습니다. HSM 및 키를 복구하고 삭제하려면 특정 역할 할당이 필요합니다. 일시 삭제는 사용하지 않도록 설정할 수 없습니다.

> [!NOTE]
> 기본 리소스는 삭제된 상태인 경우에도 HSM에 할당된 상태로 유지되기 때문에 HSM 리소스는 해당 상태에 있는 동안 계속해서 시간당 요금이 부과됩니다.

관리형 HSM 이름은 모든 클라우드 환경에서 전역적으로 고유합니다. 따라서 일시 삭제된 상태에 있는 것과 동일한 이름을 사용하여 관리형 HSM을 만들 수 없습니다. 마찬가지로 키의 이름은 HSM 내에서 고유합니다. 일시 삭제된 상태에 있는 것과 동일한 이름의 키를 만들 수 없습니다.

자세한 내용은 [관리형 HSM 일시 삭제 개요](soft-delete-overview.md)를 참조하세요.

*제거 보호* 는 악의적인 내부자가 HSM 및 키를 삭제하지 못하도록 하기 위해 설계되었습니다. 이 기능은 시간 기반 잠금을 사용하는 휴지통과 같습니다. 구성 가능한 보존 기간 동안 언제든지 항목을 복구할 수 있습니다. 보존 기간이 끝날 때까지 HSM 또는 키를 영구적으로 삭제하거나 제거할 수는 없습니다. 보존 기간이 종료되면 HSM 또는 키가 자동으로 제거됩니다.

> [!NOTE]
> 관리자 역할이나 사용 권한으로 제거 보호를 재정의하거나 사용하지 않도록 설정하거나 우회할 수 없습니다. *제거 보호를 사용하도록 설정한 후에는 Microsoft를 포함한 누구도 사용하지 않도록 설정하거나 재정의할 수 없습니다.* 따라서 HSM 이름을 다시 사용하려면 삭제된 HSM을 복구하거나 보존 기간이 종료될 때까지 기다려야 합니다.

## <a name="manage-keys-and-managed-hsms"></a>키 및 관리형 HSM 관리

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="managed-hsms-cli"></a>관리형 HSM(CLI) 

* 관리형 HSM에 대한 일시 삭제 및 제거 보호의 상태를 확인하려면 다음을 수행합니다.

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME}
    ```

* HSM을 삭제하려면 다음을 수행합니다.

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME}
    ```
    
  일시 삭제가 기본적으로 설정되어 있으므로 이 작업은 복구할 수 있습니다.

* 일시 삭제된 모든 HSM을 나열하려면 다음을 수행합니다.

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type hsm
    ```

* 일시 삭제된 HSM을 복구하려면 다음을 수행합니다.

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --location {LOCATION}
    ```


* 일시 삭제된 HSM을 제거하려면 다음을 수행합니다.

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --location {LOCATION}
    ```
    > [!WARNING] 
    > 이 작업을 수행하면 HSM이 영구적으로 삭제됩니다.

* HSM에 대해 제거 보호를 사용하도록 설정하려면 다음을 수행합니다.

    ```azurecli
    az keyvault update-hsm --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME} --enable-purge-protection true
    ```

### <a name="keys-cli"></a>키(CLI)

* 키를 삭제하려면 다음을 수행합니다.

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```

* 삭제된 키를 나열하려면 다음을 수행합니다.

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME}
    ```

* 삭제된 키를 복구하려면 다음을 수행합니다.

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```

* 일시 삭제된 키를 제거하려면 다음을 수행합니다. 

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```
    > [!WARNING] 
    > 이 작업을 수행하면 키가 영구적으로 삭제됩니다.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="managed-hsms-powershell"></a>관리형 HSM(PowerShell)

* 관리형 HSM에 대한 일시 삭제 및 제거 보호의 상태를 확인하려면 다음을 수행합니다.

    ```powershell
    Get-AzKeyVaultManagedHsm -Name "ContosoHSM"
    ```

* HSM을 삭제하려면 다음을 수행합니다.

    ```powershell
    Remove-AzKeyVaultManagedHsm -Name 'ContosoHSM'
    ```
  일시 삭제가 기본적으로 설정되어 있으므로 이 작업은 복구할 수 있습니다.

### <a name="keys-powershell"></a>키(PowerShell)

* 키를 삭제하려면 다음을 수행합니다.

  ```powershell
  Remove-AzKeyVaultKey -HsmName ContosoHSM -Name 'MyKey'
  ```

* 삭제된 모든 키를 나열하려면 다음을 수행합니다. 

  ```powershell
  Get-AzKeyVaultKey -HsmName ContosoHSM -InRemovedState
  ```

* 일시 삭제된 키를 복구하려면 다음을 수행합니다.

    ```powershell
    Undo-AzKeyVaultKeyRemoval -HsmName ContosoHSM -Name ContosoFirstKey
    ```

* 일시 삭제된 키를 제거하려면 다음을 수행합니다.

    ```powershell
    Remove-AzKeyVaultKey -HsmName ContosoHSM -Name ContosoFirstKey -InRemovedState
    ```
    > [!WARNING] 
    > 이 작업을 수행하면 키가 영구적으로 삭제됩니다.
    
---

## <a name="next-steps"></a>다음 단계

- [관리형 HSM PowerShell cmdlet](/powershell/module/az.keyvault)
- [Key Vault Azure CLI commands](/cli/azure/keyvault)(Key Vault Azure CLI 명령)
- [관리형 HSM 전체 백업 및 복원](backup-restore.md)
- [관리형 HSM 로깅을 사용하도록 설정하는 방법](logging.md)
