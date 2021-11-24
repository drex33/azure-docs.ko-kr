---
title: Azure Key Vault에서 여러 줄 비밀 저장
description: Azure CLI 및 PowerShell을 사용하여 Azure Key Vault에서 여러 줄 비밀을 설정하는 방법을 보여 주는 자습서입니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli, devx-track-azurepowershell, mode-other
ms.date: 03/17/2021
ms.author: mbaldwin
ms.openlocfilehash: 5d9582eb82edd4db8417b163f797a3cb0d414689
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133067455"
---
# <a name="store-a-multi-line-secret-in-azure-key-vault"></a>Azure Key Vault에서 여러 줄 비밀 저장

[Azure CLI 빠른 시작](quick-create-cli.md) 및 [Azure PowerShell 빠른 시작](quick-create-powershell.md)에서는 한 줄 비밀을 저장하는 방법을 보여 줍니다.   또한 Key Vault를 사용하여 JSON 파일 또는 RSA 프라이빗 키와 같은 여러 줄 비밀을 저장할 수도 있습니다.

여러 줄 비밀은 명령줄을 통해 Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) 명령 또는 Azure PowerShell [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet에 전달할 수 없습니다. 대신, 먼저 여러 줄 비밀을 텍스트 파일로 저장해야 합니다. 

예를 들어 다음 줄이 포함된 "secretfile.txt"라는 텍스트 파일을 만들 수 있습니다.

```bash
This is my
multi-line
secret
```

그런 다음, `--file` 매개 변수를 사용하여 이 파일을 Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) 명령에 전달할 수 있습니다.

```azurecli-interactive
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "MultilineSecret" --file "secretfile.txt"
```

Azure PowerShell을 사용하는 경우 먼저 [Get-Content](/powershell/module/microsoft.powershell.management/get-content) cmdlet을 사용하여 파일을 읽은 다음, [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring)을 사용하여 보안 문자열로 변환해야 합니다. 

```azurepowershell-interactive
$RawSecret =  Get-Content "secretfile.txt" -Raw
$SecureSecret = ConvertTo-SecureString -String $RawSecret -AsPlainText -Force
```

마지막으로, [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet을 사용하여 비밀을 저장합니다.

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "MultilineSecret" -SecretValue $SecureSecret
```

두 경우 모두 Azure CLI [az keyvault secret show](/cli/azure/keyvault/secret#az_keyvault_secret_show) 명령 또는 Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) cmdlet을 사용하여 저장된 비밀을 볼 수 있습니다.

```azurecli-interactive
az keyvault secret show --name "MultilineSecret" --vault-name "<your-unique-keyvault-name>" --query "value"
```

비밀은 줄 바꿈이 포함된 상태로 반환됩니다.

```bash
"This is\nmy multi-line\nsecret"
```

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 개요](../general/overview.md) 참조
- [Azure CLI 빠른 시작](quick-create-cli.md) 참조
- [Azure CLI az keyvault 명령](/cli/azure/keyvault) 참조
- [Azure PowerShell 빠른 시작](quick-create-powershell.md) 참조
- [Azure PowerShell Az.KeyVault cmdlet](/powershell/module/az.keyvault#key-vault) 참조
