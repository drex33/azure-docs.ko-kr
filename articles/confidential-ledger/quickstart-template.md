---
title: Azure Resource Manager 템플릿을 사용하여 Microsoft Azure 기밀 원장 만들기
description: Azure Resource Manager 템플릿을 사용하여 Microsoft Azure 기밀 원장을 만드는 방법을 알아봅니다.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.author: mbaldwin
ms.date: 04/15/2021
ms.openlocfilehash: 0b393c39940e0912a2497b6efba9ca920ce71fe7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438838"
---
# <a name="quickstart-create-an-microsoft-azure-confidential-ledger-with-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Microsoft Azure 기밀 원장 만들기

[Microsoft Azure 기밀 원장](overview.md)은 중요한 데이터 레코드를 관리하는 데 사용하는 매우 안전한 새로운 서비스입니다. 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 새 원장을 만드는 방법을 설명합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.confidentialledger%2Fconfidential-ledger-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>사전 요구 사항

### <a name="azure-subscription"></a>Azure 구독

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

### <a name="register-the-resource-provider"></a>리소스 공급자 등록

[!INCLUDE [Register the microsoft.ConfidentialLedger resource provider](../../includes/confidential-ledger-register-rp.md)]

### <a name="obtain-your-principal-id"></a>보안 주체 ID 가져오기

템플릿에는 보안 주체 ID가 필요합니다. `--show-mine` 플래그를 사용하여 Azure CLI [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) 명령을 실행하는 보안 주체 ID를 가져올 수 있습니다.

```azurecli-interactive
az ad sp list --show-mine -o table
```

보안 주체 ID는 "ObjectId" 열에 표시됩니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.confidentialledger/confidential-ledger-create/azuredeploy.json":::

템플릿에 정의된 Azure 리소스:

- Microsoft.ConfidentialLedger/ledgers

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하여 Azure에 로그인하고 템플릿을 엽니다.

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.confidentialledger%2Fconfidential-ledger-create%2Fazuredeploy.json)

1. 다음 값을 선택하거나 입력합니다.

    별도로 지정되지 않은 경우 기본값을 사용하여 기밀 원장을 만듭니다.

    - **원장 이름**: 원장의 이름을 선택합니다. 원장 이름은 전역적으로 고유해야 합니다.
    - **위치**: 위치를 선택합니다. 예: **미국 동부**
    - **PrincipalId**: 위의 [필수 구성 요소](#obtain-your-principal-id) 섹션에서 기록한 보안 주체 ID를 제공합니다.

1. **구매** 를 선택합니다. 기밀 원장 리소스가 성공적으로 배포되면 알림을 받게 됩니다.

Azure Portal은 템플릿을 배포하는데 사용됩니다. Azure Portal 외에도 Azure PowerShell, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal을 사용하여 원장 리소스를 확인할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

다른 Azure 기밀 원장 문서도 이 빠른 시작을 이용해 작성할 수 있습니다. 이후의 빠른 시작 및 자습서를 계속 진행하려는 경우 이러한 리소스를 유지하는 것이 좋습니다.

더 이상 필요 없는 리소스 그룹은 삭제하세요. 이 경우 원장 리소스도 삭제됩니다. Azure CLI 또는 Azure PowerShell을 사용하여 리소스 그룹을 삭제하려면 다음을 수행합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ARM 템플릿을 사용하여 기밀 원장을 만들고 배포의 유효성을 검사했습니다. 서비스에 대해 자세히 알아보려면 [Microsoft Azure 기밀 원장 개요](overview.md)를 참조하세요.