---
title: Azure CLI를 사용하여 Cognitive Services 리소스 만들기
titleSuffix: Azure Cognitive Services
description: Azure 명령줄 인터페이스를 사용하여 리소스를 만들고 구독하여 Azure Cognitive Services를 시작합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: 인지 서비스, 인지 인텔리전스, 인지 솔루션, AI 서비스
ms.topic: quickstart
ms.date: 06/04/2021
ms.author: aahi
ms.custom: mode-other
ms.openlocfilehash: 6c277b8917dca99f833833a845bfcc77dc50cadf
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133056305"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>빠른 시작: Azure CLI(명령줄 인터페이스)를 사용하여 Cognitive Services 리소스 만들기

[Azure CLI(명령줄 인터페이스)](/cli/azure/install-azure-cli)를 사용하여 Azure Cognitive Services를 시작하려면 이 빠른 시작을 참조하세요.

Azure Cognitive Services는 REST API가 있는 클라우드 기반 서비스이며, 개발자가 직접적인 AI(인공 지능) 또는 데이터 과학 기술이나 지식 없이도 인지적 인텔리전스를 애플리케이션에 빌드하도록 지원하는 데 사용할 수 있는 클라이언트 라이브러리 SDK입니다. Azure Cognitive Services를 사용하는 개발자는 보고, 듣고, 말하고, 이해하고, 추론할 수도 있는 인지적 솔루션을 통해 애플리케이션에 인지적 기능을 손쉽게 추가할 수 있습니다.

Cognitive Services는 Azure 구독에서 만든 Azure [리소스](../azure-resource-manager/management/manage-resources-portal.md)로 표시됩니다. 리소스를 만든 후에는 자동으로 생성된 키와 엔드포인트를 사용하여 애플리케이션을 인증합니다.

이 빠른 시작에서는 Azure Cognitive Services에 가입한 후 [Azure CLI(명령줄 인터페이스)](/cli/azure/install-azure-cli)를 사용하여 단일 서비스 또는 다중 서비스 구독이 있는 계정을 만드는 방법을 알아봅니다. 이러한 서비스는 하나 이상의 Azure Cognitive Services API에 연결할 수 있는 Azure [리소스](../azure-resource-manager/management/manage-resources-portal.md)로 표시됩니다.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>사전 요구 사항

* 유효한 Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [Azure CLI(명령줄 인터페이스)](/cli/azure/install-azure-cli)
* [!INCLUDE [contributor-requirement](./includes/quickstarts/contributor-requirement.md)]
* [!INCLUDE [terms-azure-portal](./includes/quickstarts/terms-azure-portal.md)]

## <a name="install-the-azure-cli-and-sign-in"></a>Azure CLI 설치 및 로그인

[Azure CLI](/cli/azure/install-azure-cli)를 설치합니다. 로컬에 설치된 CLI로 로그인하려면 [az login](/cli/azure/reference-index#az_login) 명령을 실행합니다.

```azurecli-interactive
az login
```

녹색 **사용해 보기** 단추를 사용하여 브라우저에서 이 명령을 실행할 수도 있습니다.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>새 Azure Cognitive Services 리소스 그룹 만들기

Cognitive Services 리소스를 만들려면 리소스를 포함할 Azure 리소스 그룹이 있어야 합니다. 새 리소스 그룹을 만들 때 새 리소스 그룹을 만들 수도 있고 기존 리소스 그룹을 사용할 수도 있습니다. 이 문서에서는 새 리소스 그룹을 만드는 방법을 보여줍니다.

### <a name="choose-your-resource-group-location"></a>리소스 그룹 위치 선택

리소스를 만들려면 구독에 사용할 수 있는 Azure 위치 중 하나가 필요합니다. [az account list-locations](/cli/azure/account#az_account_list_locations) 명령으로 사용 가능한 지역 목록을 검색할 수 있습니다. 대부분의 Cognitive Services는 여러 위치에서 액세스할 수 있습니다. 가장 가까운 위치를 선택하거나 서비스에 사용할 수 있는 위치를 확인합니다.

> [!IMPORTANT]
> * Azure Cognitive Services를 호출할 때 필요하므로 Azure 위치를 기억해 두어야 합니다.
> * 일부 Cognitive Services의 가용성은 지역에 따라 다를 수 있습니다. 자세한 내용은 [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)을 참조하세요.

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Azure 위치를 선택한 후에는 Azure CLI에서 [az group create](/cli/azure/group#az_group_create) 명령을 사용하여 새 리소스 그룹을 만듭니다.

아래 예제에서 Azure 위치 `westus2`를 구독에 사용할 수 있는 Azure 위치 중 하나로 바꿉니다.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Cognitive Services 및 가격 책정 계층 선택

새 리소스를 만들 때 원하는 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/)(또는 SKU)과 함께 사용할 서비스의 "종류"를 알아야 합니다. 리소스를 만들 때 이 정보와 기타 정보를 매개 변수로 사용합니다.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]


[!INCLUDE [SKUs and pricing](./includes/quickstarts/sku-pricing.md)]

[az cognitiveservices account list-kinds](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_kinds) 명령으로 사용 가능한 Cognitive Service "종류" 목록을 찾을 수 있습니다.

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>리소스 그룹에 새 리소스 추가

새 Cognitive Services 리소스를 만들고 구독하려면 [az cognitiveservices account create](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_create) 명령을 사용합니다. 이 명령은 앞에서 만든 리소스 그룹에 요금이 발생할 수 있는 새 리소스를 추가합니다. 새 리소스를 만들 때 가격 책정 계층(또는 SKU) 및 Azure 위치와 함께 사용할 서비스의 "종류"를 알아야 합니다.

아래 명령을 사용하여 `anomaly-detector-resource`라는 Anomaly Detector에 사용할 F0(체험) 리소스를 만들 수 있습니다.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>리소스의 키 가져오기

로컬에 설치된 CLI(명령줄 인터페이스)로 로그인하려면 [az login](/cli/azure/reference-index#az_login) 명령을 사용합니다.

```azurecli-interactive
az login
```

[az cognitiveservices account keys list](/cli/azure/cognitiveservices/account/keys#az_cognitiveservices_account_keys_list) 명령을 사용하여 Cognitive Service 리소스의 키를 가져옵니다.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>가격 책정 계층 및 요금 청구

가격 책정 계층(및 청구되는 금액)은 인증 정보를 사용하여 전송하는 트랜잭션 수를 기반으로 합니다. 각 가격 책정 계층은 다음을 지정합니다.
* 초당 허용되는 최대 트랜잭션 수(TPS)입니다.
* 가격 책정 계층 내에서 사용하도록 설정된 서비스 기능입니다.
* 미리 정의된 트랜잭션 수에 대한 비용입니다. 이 숫자를 초과하면 해당 서비스의 [가격 책정 정보](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)에 지정된 대로 추가 요금이 발생합니다.

## <a name="get-current-quota-usage-for-your-resource"></a>리소스의 현재 할당량 사용 정보 가져오기

Cognitive Service 리소스 사용량 정보를 확인하려면 [az cognitiveservices account list-usage](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_usage) 명령을 사용합니다.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 리소스를 정리하고 제거하려면 해당 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 그룹에 포함된 모든 리소스가 함께 삭제됩니다.

리소스 그룹과 관련 리소스를 제거하려면 az group delete 명령을 사용합니다.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

삭제된 리소스를 복구해야 하는 경우 [삭제된 Cognitive Services 리소스 복구](manage-resources.md)를 참조하세요.

## <a name="see-also"></a>참고 항목

* Cognitive Services를 안전하게 사용하는 방법을 알아보려면 **[Azure Cognitive Services 요청 인증](authentication.md)** 을 참조하세요.
* Cognitive Services에 포함된 다양한 범주 목록을 가져오려면 **[Azure Cognitive Services란?](./what-are-cognitive-services.md)** 을 참조하세요.
* Cognitive Services에서 지원하는 자연어 목록을 보려면 **[자연어 지원](language-support.md)** 을 참조하세요.
* Cognitive Services를 온-프레미스로 사용하는 방법은 **[Cognitive Services를 컨테이너로 사용](cognitive-services-container-support.md)** 을 참조하세요.
* Cognitive Services 사용 비용을 추산하려면 **[Cognitive Services 비용 계획 및 관리](plan-manage-costs.md)** 를 참조하세요.
