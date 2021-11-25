---
title: ARM 템플릿을 사용하여 Azure Cognitive Services 리소스 만들기 | Microsoft Docs
description: ARM 템플릿을 사용하여 Azure Cognitive Service 리소스를 만듭니다.
keywords: 인지 서비스, 인지 솔루션, 인지 인텔리전스, 인지 인공 지능
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 06/04/2021
ms.author: aahi
ms.custom: subject-armqs, mode-arm
ms.openlocfilehash: 15392d04508aa5d84eb505818bf4c1bbe3fbd973
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133062346"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Cognitive Services 리소스 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Cognitive Services를 만드는 방법을 설명합니다.

Azure Cognitive Services는 REST API가 있는 클라우드 기반 서비스이며, 개발자가 직접적인 AI(인공 지능) 또는 데이터 과학 기술이나 지식 없이도 인지적 인텔리전스를 애플리케이션에 빌드하도록 지원하는 데 사용할 수 있는 클라이언트 라이브러리 SDK입니다. Azure Cognitive Services를 사용하는 개발자는 보고, 듣고, 말하고, 이해하고, 추론할 수도 있는 인지적 솔루션을 통해 애플리케이션에 인지적 기능을 손쉽게 추가할 수 있습니다.

ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 리소스를 만듭니다. 이 다중 서비스 리소스를 사용하여 다음을 수행할 수 있습니다.

* 단일 키와 엔드포인트로 여러 Azure Cognitive Services에 액세스합니다.
* 사용하는 여러 서비스의 대금 청구를 통합합니다.
* [!INCLUDE [terms-azure-portal](./includes/quickstarts/terms-azure-portal.md)]

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 인지 서비스 배포](../media/template-deployments/deploy-to-azure.svg "Azure에 인지 서비스 배포")](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.cognitiveservices%2Fcognitive-services-universalkey%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 아직 없는 경우 [체험판 구독을 만듭니다](https://azure.microsoft.com/free/cognitive-services).

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/cognitive-services-universalkey/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.cognitiveservices/cognitive-services-universalkey/azuredeploy.json":::

템플릿에 다음과 같은 Azure 리소스 하나가 정의되어 있습니다.
* [Microsoft.CognitiveServices/accounts](/azure/templates/microsoft.cognitiveservices/accounts): Cognitive Services 리소스를 만듭니다.

## <a name="deploy-the-template"></a>템플릿 배포

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. **Azure에 배포** 단추를 클릭합니다.

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.cognitiveservices%2Fcognitive-services-universalkey%2Fazuredeploy.json)

2. 다음 값을 입력합니다.

    |값  |Description  |
    |---------|---------|
    | **구독** | Azure 구독을 선택합니다. |
    | **리소스 그룹** | **새로 만들기** 를 선택하고 리소스 그룹의 고유한 이름을 입력한 다음, **확인** 을 클릭합니다. |
    | **지역** | 지역을 선택합니다.  예: **미국 동부** |
    | **Cognitive Service 이름** | 리소스의 고유한 이름으로 바꿉니다. 다음 섹션에서 배포의 유효성을 검사할 때 이름이 필요합니다. |
    | **위치** | 위에서 사용한 영역으로 바꿉니다. |
    | **Sku** | 리소스의 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/)입니다. |

    :::image type="content" source="media/arm-template/universal-key-portal-template.png" alt-text="리소스 만들기 화면":::입니다.

3. **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택합니다. 리소스가 성공적으로 배포되면 **리소스로 이동** 단추가 강조 표시됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

> [!NOTE]
> `az deployment group`을 사용하려면 Azure CLI 버전 2.6 이상이 필요합니다. 버전을 표시하려면 `az --version`을 입력합니다. 자세한 내용은 [설명서](/cli/azure/deployment/group)를 참조하세요.

[로컬 머신](/cli/azure/install-azure-cli)에서 Azure CLI(명령줄 인터페이스)를 사용하거나 브라우저에서 **사용해 보세요** 단추를 사용하여 다음 스크립트를 실행합니다. 새 리소스 그룹의 이름과 위치(예: `centralus`)를 입력합니다. 그러면 ARM 템플릿을 사용하여 그 안에 Cognitive Services 리소스가 배포됩니다. 여기서 사용하는 이름을 기억해 두세요. 나중에 배포의 유효성을 검사할 때 필요합니다.


```azurecli-interactive
read -p "Enter a name for your new resource group:" resourceGroupName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.cognitiveservices/cognitive-services-universalkey/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="review-deployed-resources"></a>배포된 리소스 검토

# <a name="portal"></a>[포털](#tab/portal)

배포가 완료되면 **리소스로 이동** 단추를 클릭하여 새 리소스를 볼 수 있습니다. 다음 방법으로 리소스 그룹을 찾을 수도 있습니다.

1. 왼쪽 탐색 메뉴에서 **리소스 그룹** 을 선택합니다.
2. 리소스 그룹 이름을 선택합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

Azure CLI를 사용하여 다음 스크립트를 실행하고, 앞에서 만든 리소스 그룹의 이름을 입력합니다.

```azurecli-interactive
echo "Enter the resource group where the Cognitive Services resource exists:" &&
read resourceGroupName &&
az cognitiveservices account list -g $resourceGroupName
```

---


## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 그룹에 포함된 모든 리소스가 함께 삭제됩니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Azure Portal에서 왼쪽의 메뉴를 확장하여 서비스 메뉴를 열고 **리소스 그룹** 을 선택하여 리소스 그룹 목록을 표시합니다.
2. 삭제할 리소스가 포함된 리소스 그룹을 찾습니다.
3. 리소스 그룹 목록을 마우스 오른쪽 단추로 클릭합니다. **리소스 그룹 삭제** 를 선택하고 확인합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

Azure CLI를 사용하여 다음 스크립트를 실행하고, 앞에서 만든 리소스 그룹의 이름을 입력합니다.

```azurecli-interactive
echo "Enter the resource group name, for deletion:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

삭제된 리소스를 복구해야 하는 경우 [삭제된 Cognitive Services 리소스 복구](manage-resources.md)를 참조하세요.

## <a name="see-also"></a>참고 항목

* Cognitive Services를 안전하게 사용하는 방법을 알아보려면 **[Azure Cognitive Services 요청 인증](authentication.md)** 을 참조하세요.
* Cognitive Services에 포함된 다양한 범주 목록을 가져오려면 **[Azure Cognitive Services란?](./what-are-cognitive-services.md)** 을 참조하세요.
* Cognitive Services에서 지원하는 자연어 목록을 보려면 **[자연어 지원](language-support.md)** 을 참조하세요.
* Cognitive Services를 온-프레미스로 사용하는 방법은 **[Cognitive Services를 컨테이너로 사용](cognitive-services-container-support.md)** 을 참조하세요.
* Cognitive Services 사용 비용을 추산하려면 **[Cognitive Services 비용 계획 및 관리](plan-manage-costs.md)** 를 참조하세요.
