---
title: 빠른 시작 - PowerShell을 사용하여 Key Vault에서 비밀 설정 및 검색
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 Azure Key Vault에서 비밀을 생성, 검색 및 삭제하는 방법을 알아봅니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 2e51e781ca988a496e3f1ba61d8b6a4fcc3d7095
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443507"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색

Azure Key Vault는 보안 비밀 저장소로 작동하는 클라우드 서비스입니다. 키, 암호, 인증서 및 기타 비밀을 안전하게 저장할 수 있습니다. Key Vault에 대한 자세한 내용을 보려면 [개요](../general/overview.md)를 살펴보세요. 이 빠른 시작에서는 PowerShell를 사용하여 Key Vault를 만듭니다. 그런 다음, 새로 만든 자격 증명 모음에 비밀을 저장합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하려는 경우 이 자습서를 진행하려면 Azure PowerShell 모듈 버전 5.0.0 이상이 필요합니다. `$PSVersionTable.PSVersion` 명령을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="give-your-user-account-permissions-to-manage-secrets-in-key-vault"></a>Key Vault에서 비밀을 관리할 수 있는 사용자 계정 권한을 부여합니다.

Azure PowerShell Set-AzKeyVaultAccessPolicy cmdlet을 사용하여 Key Vault 액세스 정책을 업데이트하고 사용자 계정에 비밀 사용 권한을 부여합니다.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -UserPrincipalName "user@domain.com" -PermissionsToSecrets get,set,delete
```

## <a name="adding-a-secret-to-key-vault"></a>Key Vault에 비밀 추가

자격 증명 모음에 비밀을 추가하려면 몇 가지 단계만 수행하면 됩니다. 이 경우 애플리케이션에서 사용할 수 있는 암호를 추가합니다. 암호는 **ExamplePassword** 로 지정되며 **hVFkk965BuUv** 값을 저장합니다.

먼저 다음 명령을 입력하여 **hVFkk965BuUv** 값을 보안 문자열로 변환합니다.

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString "hVFkk965BuUv" -AsPlainText -Force
```

그런 다음, Azure PowerShell [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet을 사용하여 값 **hVFkk965BuUv** 를 통해 **ExamplePassword** 라는 Key Vault에 비밀을 만듭니다.


```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword" -SecretValue $secretvalue
```

## <a name="retrieve-a-secret-from-key-vault"></a>Key Vault에서 비밀 검색

비밀에 들어 있는 값을 일반 텍스트로 보려면:

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword" -AsPlainText
```

지금까지 Key Vault를 만들고 비밀을 저장하고, 검색했습니다.

## <a name="clean-up-resources"></a>리소스 정리

 이 컬렉션의 다른 빠른 시작과 자습서는 이 빠른 시작을 기반으로 하여 작성됩니다. 다른 빠른 시작 및 자습서를 계속 진행하려는 경우 이러한 리소스를 유지하는 것이 좋습니다.

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용하여 리소스 그룹, Key Vault 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Key Vault를 만들어 비밀을 저장했습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Key Vault 개요](../general/overview.md) 참조
- [Key Vault에서 여러 줄 비밀을 저장](multiline-secrets.md)하는 방법을 알아봅니다.
- [Azure PowerShell Key Vault cmdlets](/powershell/module/az.keyvault/#key_vault)에 대한 참조를 참조하세요.
- [Azure Key Vault 보안 개요](../general/security-features.md)를 검토하세요.
