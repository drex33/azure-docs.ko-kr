---
title: 데이터 손실 방지
description: Cognitive Services 데이터 손실 방지 기능을 통해 고객은 Cognitive Services 리소스가 액세스할 수 있는 아웃바운드 URL 목록을 구성할 수 있습니다. 이 구성은 고객이 데이터 손실을 방지할 수 있도록 또 다른 수준의 제어를 만듭니다.
author: gclarkmt
ms.author: gregc
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/02/2021
ms.custom: template-concept
ms.openlocfilehash: 1d6e7d6dd94efb11f5a2ea7cc8263f7916926fbe
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123475767"
---
# <a name="configure-data-loss-prevention-for-azure-cognitive-services"></a>Azure Cognitive Services의 데이터 손실 방지 구성

Cognitive Services 데이터 손실 방지 기능을 통해 고객은 Cognitive Services 리소스가 액세스할 수 있는 아웃바운드 URL 목록을 구성할 수 있습니다. 이 구성은 고객이 데이터 손실을 방지할 수 있도록 또 다른 수준의 제어를 만듭니다. 이 문서에서는 Cognitive Services 리소스에 대해 데이터 손실 방지 기능을 사용하는 데 필요한 단계를 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

요청을 수행하기 전에 Azure 계정과 Azure Cognitive Services 구독이 필요합니다. 계정이 이미 있는 경우 다음 섹션으로 건너뜁니다. 계정이 없는 경우 [Azure용 Cognitive Services 계정 만들기](cognitive-services-apis-create-account.md) 가이드를 참조하여 몇 분만에 설정할 수 있습니다.

구독 키는 [계정을 만든 후](https://azure.microsoft.com/free/cognitive-services/) [Azure Portal](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)에서 구할 수 있습니다.

## <a name="enabling-data-loss-prevention"></a>데이터 손실 방지 사용

데이터 손실 방지를 사용하도록 설정하는 두 가지 부분이 있습니다. 먼저 restrictOutboundNetworkAccess 속성을 true로 설정해야 합니다. true로 설정된 경우 승인된 URL 목록도 제공해야 합니다. URL 목록이 allowedFqdnList 속성에 추가됩니다. allowedFqdnList 속성은 쉼표로 구분된 URL 배열을 포함합니다.

>[!NOTE]
>
> * `allowedFqdnList` 속성 값은 최대 1000개 URL을 지원합니다.
> * 이 속성은 IP 주소와 정규화된 도메인 이름(`www.microsoft.com`, 값)을 둘 다 지원합니다.
> * 업데이트된 목록이 적용되는 데 최대 15분이 걸릴 수 있습니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure CLI](/cli/azure/install-azure-cli)를 설치하고 [로그인](/cli/azure/authenticate-azure-cli)하거나 **사용해 보세요** 를 선택합니다.

1. Cognitive Services 리소스의 세부 정보를 봅니다.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
    ```

1. Cognitive Services 리소스의 현재 속성을 봅니다.

    ```azurecli-interactive
    az rest -m get \
        -u /subscriptions/{subscription ID}}/resourceGroups/{resource group}/providers/Microsoft.CognitiveServices/accounts/{account name}?api-version=2021-04-30 \
    ```

1. restrictOutboundNetworkAccess 속성을 구성하고 승인된 URL을 사용하여 허용된 FqdnList를 업데이트합니다.

    ```azurecli-interactive
    az rest -m patch \
        -u /subscriptions/{subscription ID}}/resourceGroups/{resource group}/providers/Microsoft.CognitiveServices/accounts/{account name}?api-version=2021-04-30 \
        -b '{"properties": { "restrictOutboundNetworkAccess": true, "allowedFqdnList": [ "microsoft.com" ] }}'
    ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. [Azure PowerShell](/powershell/azure/install-az-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)하거나 **사용해 보세요** 를 선택합니다.

1. Cognitive Services 리소스의 현재 속성을 표시합니다.

    ```azurepowershell-interactive
    $getParams = @{
        ResourceGroupName = 'myresourcegroup'
        ResourceProviderName = 'Microsoft.CognitiveServices'
        ResourceType = 'accounts'
        Name = 'myaccount'
        ApiVersion = '2021-04-30'
        Method = 'GET'
    }
    Invoke-AzRestMethod @getParams
    ```

1. restrictOutboundNetworkAccess 속성을 구성하고 승인된 URL을 사용하여 허용된 FqdnList를 업데이트합니다.

    ```azurepowershell-interactive
    $patchParams = @{
        ResourceGroupName = 'myresourcegroup'
        ResourceProviderName = 'Microsoft.CognitiveServices'
        ResourceType = 'accounts'
        Name = 'myaccount'
        ApiVersion = '2021-04-30'
        Payload = '{"properties": { "restrictOutboundNetworkAccess": true, "allowedFqdnList": [ "microsoft.com" ] }}'
        Method = 'PATCH'
    }
    Invoke-AzRestMethod @patchParams
    ```

---

## <a name="supported-services"></a>지원되는 서비스

다음 서비스는 데이터 손실 방지 구성을 지원합니다.

- Computer Vision
- Content Moderator
- Custom Vision
- Face
- Speech Service
- QnA Maker

## <a name="next-steps"></a>다음 단계

- [가상 네트워크 구성](cognitive-services-virtual-networks.md)
