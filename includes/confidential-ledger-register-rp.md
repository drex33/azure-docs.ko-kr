---
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: include
ms.date: 05/05/2021
ms.author: msmbaldwin
ms.openlocfilehash: fa07459629d832516fda5d3141f85b23afe3fbb8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860530"
---
리소스 공급자는 Azure 리소스를 제공하는 서비스입니다. Azure CLI [az provider register](/cli/azure/provider#az_provider_register) 명령 또는 Azure PowerShell [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) cmdlet을 사용하여 Azure Confidential Ledger 리소스 공급자인 'microsoft.ConfidentialLedger'를 등록합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az provider register --namespace "microsoft.ConfidentialLedger"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Register-AzureRmResourceProvider -ProviderNamespace "microsoft.ConfidentialLedger"
```
---

Azure CLI [az provider register](/cli/azure/provider#az_provider_show) 명령 또는 Azure PowerShell [Get-AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider) cmdlet을 통해 등록이 완료되었는지 확인할 수 있습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az provider show --namespace "microsoft.ConfidentialLedger"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzureRmResourceProvider -ProviderNamespace "microsoft.ConfidentialLedger"
```
---
