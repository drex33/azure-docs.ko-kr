---
title: 자습서 - Azure API Management를 사용하여 API 버전 게시
description: 이 자습서의 단계에 따라 API Management에서 여러 API 버전을 게시하는 방법에 대해 알아봅니다.
author: dlepow
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: danlep
ms.openlocfilehash: 0f9d43f0b9abd53f76efb1e29d40667f1daf19b3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644812"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>자습서: 여러 버전의 API 게시 

모든 API 호출자가 정확히 같은 버전을 사용하기가 어려운 경우가 있습니다. 최신 버전으로 업그레이드하려는 호출자는 이해하기 쉬운 방법을 원합니다. 이 자습서에서 보여드리는 것처럼 Azure API Management에 여러 *버전* 이 제공될 수 있습니다. 

배경 정보는 [버전 및 수정 버전](https://azure.microsoft.com/blog/versions-revisions/)을 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 기존 API에 새 버전 추가
> * 버전 관리 체계 선택
> * 제품에 버전 추가
> * 개발자 포털을 검색하여 버전 확인

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="Azure Portal에 표시된 버전":::

## <a name="prerequisites"></a>사전 요구 사항

+ [Azure API Management 용어](api-management-terminology.md)를 익힙니다.
+ 다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
+ 또한, 다음 자습서 [첫 번째 API 가져오기 및 게시](import-and-publish.md)를 완료합니다.

## <a name="add-a-new-version"></a>새 버전 추가

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.
1. **API** 를 선택합니다.
1. API 목록에서 **데모 회의 API** 를 선택합니다. 
1. **Demo Conference API** 옆에서 바로 가기 메뉴( **...** )를 선택합니다.
1. **버전 추가** 를 선택합니다.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="API 바로 가기 메뉴 - 버전 추가":::


> [!TIP]
> 새 API를 만들 때 버전을 사용하도록 설정할 수도 있습니다. **API 추가** 화면에서 **API 버전 관리** 를 선택합니다.

## <a name="choose-a-versioning-scheme"></a>버전 관리 체계 선택

Azure API Management에서 *버전 관리 체계*: **경로, 헤더** 또는 **쿼리 문자열** 을 선택하여 호출자가 API 버전을 지정하는 방법을 선택합니다. 다음 예제에서 *경로* 는 버전 관리 체계로 사용됩니다.

다음 표의 값을 나열합니다. 그런 다음, **만들기** 를 선택하여 자체 버전을 만듭니다.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="버전 추가 창":::



|설정   |값  |Description  |
|---------|---------|---------|
|**이름**     |  *demo-conference-api-v1*       |  API Management 인스턴스의 고유한 이름입니다.<br/><br/>버전은 API의 [수정 버전](api-management-get-started-revise-api.md)을 기반으로 하는 새 API이므로, 이 설정은 새 API의 이름입니다.   |
|**버전 관리 체계**     |  **Path**       |  호출자가 API 버전을 지정하는 방식입니다.     |
|**버전 식별자**     |  *v1*       |  버전의 체계 관련 표시기입니다. **경로** 의 경우 API URL 경로의 접미사입니다. <br/><br/> **헤더** 또는 **쿼리 문자열** 을 선택하는 경우에는 추가 값(헤더의 이름 또는 쿼리 문자열 매개 변수)을 입력합니다.<br/><br/> 사용 예제가 표시됩니다.        |
|**제품**     |  **무제한**       |  필요에 따라 API 버전이 연결된 하나 이상의 제품입니다. API를 게시하려면 API를 제품과 연결해야 합니다. 나중에 [제품에 버전을 추가](#add-the-version-to-a-product)할 수도 있습니다.      |

버전을 만든 후에는 API 목록의 **Demo Conference API** 아래에 버전이 표시됩니다. 이제 두 가지 API: **원본** 및 **v1** 이 표시됩니다.

![Azure Portal에서 API 아래에 나열된 버전](media/api-management-getstarted-publish-versions/version-list.png)

이제 **원본** 과는 별개의 API로 **v1** 을 편집하고 구성할 수 있습니다. 즉, 버전 하나를 변경해도 다른 버전에는 영향을 주지 않습니다.

> [!Note]
> 버전이 없는 API에 버전을 추가하면 **원본** 이 자동으로 만들어집니다. 이 버전은 기본 URL에 응답합니다. 원본 버전을 만들면 버전 추가 프로세스로 인해 기존 호출자의 호출이 중단되지 않습니다. 시작 시 버전을 사용하도록 설정하여 새 API를 만들면 원본이 작성되지 않습니다.

## <a name="add-the-version-to-a-product"></a>제품에 버전 추가

호출자가 새 버전을 보게 하려면 *제품* 에 추가해야 합니다. 아직 제품에 버전을 추가하지 않은 경우 언제든지 제품에 추가할 수 있습니다.

예를 들어 **무제한** 제품에 버전을 추가하려면 다음을 수행합니다.
1. Azure Portal에서 API Management 인스턴스로 이동합니다.
1. **제품** > **무제한** > **API** >  **+ 추가** 를 선택합니다.
1. **Demo Conference API**, 버전 **v1** 을 선택합니다.
1. **선택** 을 클릭합니다.

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="제품에 버전 추가":::

## <a name="use-version-sets"></a>버전 집합 사용

여러 버전을 만들 때 Azure Portal은 단일 논리적 API에 대한 버전 세트를 나타내는 *버전 집합* 을 만듭니다. 여러 버전이 있는 API 이름을 선택합니다. Azure Portal은 해당 **버전 집합** 을 표시합니다. 가상 집합의 **이름** 및 **설명** 을 사용자 지정할 수 있습니다.

Azure CLI를 사용하여 버전 집합과 직접 상호 작용할 수 있습니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

모든 버전 집합을 보려면 [az apim api versionset list](/cli/azure/apim/api/versionset#az_apim_api_versionset_list) 명령을 실행합니다.

```azurecli
az apim api versionset list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Azure Portal에서 버전 집합을 만들 때 목록의 **이름** 열에 표시되는 영숫자 이름이 할당됩니다. 다른 Azure CLI 명령에서 이 이름을 사용합니다.

버전 집합에 대한 세부 정보를 보려면 [az apim api versionset show](/cli/azure/apim/api/versionset#az_apim_api_versionset_show) 명령을 실행합니다.

```azurecli
az apim api versionset show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --version-set-id 00000000000000000000000
```

버전 집합에 대한 자세한 내용은 [Azure API Management의 버전](api-management-versions.md#how-versions-are-represented)을 참조하세요.

## <a name="browse-the-developer-portal-to-see-the-version"></a>개발자 포털을 검색하여 버전 확인

[개발자 포털](api-management-howto-developer-portal-customize.md)를 사용한 경우 개발자 포털에서 API 버전을 볼 수 있습니다.

1. 상단 메뉴에서 **개발자 포털** 을 선택합니다.
2. **API**, **데모 회의 API** 를 차례로 선택합니다.
3. API 이름 옆에 여러 버전이 있는 드롭다운이 표시됩니다.
4. **v1** 을 선택합니다.
5. 목록에서 첫 번째 작업의 **요청 URL** 을 확인합니다. API URL 경로에 **v1** 이 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 기존 API에 새 버전 추가
> * 버전 관리 체계 선택 
> * 제품에 버전 추가
> * 개발자 포털을 검색하여 버전 확인

다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [개발자 포털 페이지의 스타일 사용자 지정](api-management-howto-developer-portal-customize.md)
