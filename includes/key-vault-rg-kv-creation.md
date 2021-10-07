---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c328541ef98391daec87c1988951e8f4bcda4ccc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597318"
---
이 빠른 시작에서는 미리 만든 Azure Key Vault를 사용합니다. [Azure CLI 빠른 시작](../articles/key-vault/general/quick-create-cli.md), [Azure PowerShell 빠른 시작](../articles/key-vault/general/quick-create-powershell.md) 또는 [Azure Portal 빠른 시작](../articles/key-vault/general/quick-create-portal.md)의 단계에 따라 키 자격 증명 모음을 만들 수 있습니다. 

또는 아래의 Azure CLI 또는 Azure PowerShell 명령을 실행하기만 하면 됩니다.

> [!Important]
> 각 Key Vault마다 고유한 이름이 있어야 합니다. 다음 예제에서 \<your-unique-keyvault-name\>을 키 자격 증명 모음의 이름으로 바꿉니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location eastus

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName myResourceGroup -Location eastus
```
---
