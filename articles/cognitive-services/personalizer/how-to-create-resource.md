---
title: Personalizer 리소스 만들기
description: 이 문서에서는 Azure Portal에서 각 피드백 루프에 대해 Personalizer 리소스를 만드는 방법에 대해 알아봅니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: b901153653b292a77ff19a25f065cf225e54f76e
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831560"
---
# <a name="create-a-personalizer-resource"></a>Personalizer 리소스 만들기

Personalizer 리소스는 Personalizer 학습 루프와 동일합니다. 사용하고 있는 각 주제 도메인 또는 콘텐츠 영역에 대해 단일 리소스 또는 학습 루프가 만들어집니다. 동일한 루프에서 여러 콘텐츠 영역을 사용하지 마세요. 학습 루프를 혼동하여 잘못된 예측이 제공될 수 있습니다.

Personalizer가 웹 페이지에서 둘 이상의 콘텐츠 영역에 대해 최상의 콘텐츠를 선택하도록 하려면 각각에 대해 다른 학습 루프를 사용합니다.


## <a name="create-a-resource-in-the-azure-portal"></a>Azure Portal에서 리소스 만들기

각 피드백 루프에 대한 Personalizer 리소스를 만듭니다.

1. [Azure 포털](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)에 로그인합니다. 이전 링크를 클릭하면 Personalizer 서비스의 **만들기** 페이지로 이동합니다.
1. 서비스 이름을 입력하고 구독, 위치, 가격 책정 계층 및 리소스 그룹을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![Azure Portal을 사용하여 Personalizer 리소스 만듭니다(학습 루프라고도 함).](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. **만들기** 를 선택하여 리소스를 만듭니다.

1. 리소스를 배포한 후 **리소스로 이동** 단추를 선택하여 Personalizer 리소스로 이동합니다.

1. 리소스에 대한 **빠른 시작**  페이지를 선택한 다음 엔드포인트 및 키 값을 복사합니다. 순위 및 보상 API를 사용하려면 리소스 엔드포인트와 키가 모두 필요합니다.

1. 새 리소스에 대한 **구성** 페이지를 선택하여 [학습 루프를 구성](how-to-settings.md)합니다.

## <a name="create-a-resource-with-the-azure-cli"></a>Azure CLI를 사용하여 리소스 만들기

1. 다음 명령을 사용하여 Azure CLI에 로그인합니다.

    ```azurecli-interactive
    az login
    ```

1. Personalizer 리소스와 함께 사용하려는 모든 Azure 리소스를 관리하기 위한 논리적 그룹인 리소스 그룹을 만듭니다.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. 기존 리소스 그룹에 대한 다음 명령을 사용하여 새 Personalizer 리소스인 _학습 루프_ 를 만듭니다.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    그러면 **리소스 엔드포인트** 를 포함하는 JSON 개체가 반환됩니다.

1. 다음 Azure CLI 명령을 사용하여 **리소스 키** 를 가져옵니다.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    순위 및 보상 API를 사용하려면 리소스 엔드포인트와 키가 모두 필요합니다.

## <a name="next-steps"></a>다음 단계

* Personalizer 학습 루프 [구성](how-to-settings.md)
