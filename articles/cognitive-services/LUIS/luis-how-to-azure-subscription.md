---
title: LUIS 리소스를 만들고 관리하는 방법
titleSuffix: Azure Cognitive Services
description: LUIS용 Azure 리소스를 사용 및 관리하는 방법을 알아봅니다. 앱.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 07/12/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 4fe1e8cd4c78d4411a7cb9058648fc0e782cb0ee
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113666306"
---
# <a name="how-to-create-and-manage-luis-resources"></a>LUIS 리소스를 만들고 관리하는 방법

이 문서를 사용하여 LUIS와 함께 사용할 수 있는 Azure 리소스의 유형과 관리 방법을 알아봅니다.

## <a name="authoring-resource"></a>작성 리소스

작성 리소스를 사용하여 애플리케이션을 만들고, 관리하고, 학습하고, 테스트하고, 게시할 수 있습니다. 한 가지 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)은 LUIS 작성 리소스 - 체험(F0) 계층으로 제공되며 이를 통해 다음을 제공합니다.

* 100만 작성 트랜잭션 
* 매월 1,000개의 테스트 예측 엔드포인트 요청

[v3.0-preview LUIS 프로그래매틱 API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)를 사용하여 작성 리소스를 관리할 수 있습니다. 

## <a name="prediction-resource"></a>예측 리소스

예측 리소스를 사용하면 작성 리소스에서 제공하는 1,000개의 요청을 초과하여 예측 엔드포인트를 쿼리할 수 있습니다. 다음 두 개의 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)을 예측 리소스에 사용할 수 있습니다.

* 체험(F0) 예측 리소스는 매월 10,000개의 예측 엔드포인트 요청을 제공합니다.
* 표준(S0) 예측 리소스는 유료 계층입니다. 

[v3.0-preview LUIS 엔드포인트 API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5f68f4d40a511ce5a7440859)를 사용하여 예측 리소스를 관리할 수 있습니다.

> [!Note]
> * [다중 서비스 리소스](../cognitive-services-apis-create-account-cli.md?tabs=multiservice)를 사용하여 여러 Cognitive Services에 사용할 수 있는 단일 엔드포인트를 가져올 수도 있습니다.
> * LUIS는 두 가지 종류의 F0(체험 계층) 리소스를 제공합니다. 하나는 작성 트랜잭션용이고, 다른 하나는 예측 트랜잭션용입니다. 예측 트랜잭션에 대한 체험 할당량이 부족한 경우 매월 1,000개의 예측 트랜잭션을 제공하는 작성 리소스가 아니라 매월 10,000개의 체험 트랜잭션을 제공하는 F0 예측 리소스를 사용하고 있는지 확인하세요.
> * LUIS 앱은 게시 및 쿼리하려는 [지역](luis-reference-regions.md#publishing-regions)에 작성해야 합니다.

## <a name="create-luis-resources"></a>LUIS 리소스 만들기

LUIS 리소스를 만들려면 LUIS 포털, [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) 또는 Azure CLI를 사용할 수 있습니다. 리소스를 만든 후에는 해당 리소스를 사용하도록 앱에 할당해야 합니다.

# <a name="luis-portal"></a>[LUIS 포털](#tab/portal)

### <a name="create-a-luis-authoring-resource-using-the-luis-portal"></a>LUIS 포털을 사용하여 LUIS 작성 리소스 만들기

1. [LUIS 포털](https://www.luis.ai)에 로그인한 후 국가/지역을 선택하고 사용 약관에 동의합니다. 포털에 **My Apps** 섹션이 표시되면 LUIS 리소스가 이미 존재하며 다음 단계를 건너뛸 수 있습니다.

2. 표시되는 **작성 선택** 창에서 Azure 구독 및 LUIS 작성 리소스를 찾습니다. 리소스가 없는 경우 새 리소스 그룹을 만들 수 있습니다.

    :::image type="content" source="./media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Language Understanding 작성 리소스의 유형을 선택합니다.":::
    
    새 작성 리소스를 만들 때 다음 정보를 제공합니다.
    * **테넌트 이름**: Azure 구독이 연결된 테넌트입니다.
    * **Azure 구독 이름**: 리소스에 대해 청구되는 구독입니다.
    * **Azure 리소스 그룹 이름**: 사용자가 선택하거나 만드는 사용자 지정 리소스 그룹 이름입니다. 리소스 그룹을 사용하면 액세스 및 관리를 위해 Azure 리소스를 그룹화할 수 있습니다.
    * **Azure 리소스 이름**: 사용자가 선택한 사용자 지정 이름으로, 작성 및 예측 엔드포인트 쿼리의 URL 중 일부로 사용됩니다.
    * **가격 책정 계층**: 가격 책정 계층은 초 및 월별 최대 트랜잭션 수를 결정합니다.

### <a name="create-a-luis-prediction-resource-using-the-luis-portal"></a>LUIS 포털을 사용하여 LUIS 예측 리소스 만들기

[!INCLUDE [Create LUIS Prediction resource in LUIS portal](./includes/add-prediction-resource-portal.md)]

# <a name="without-luis-portal"></a>[LUIS 포털 사용 안 함](#tab/without-portal)

### <a name="create-luis-resources-without-using-the-luis-portal"></a>LUIS 포털을 사용하지 않고 LUIS 리소스 만들기

[Azure CLI](/cli/azure/install-azure-cli)를 사용하여 각 리소스를 개별적으로 만듭니다.

> [!TIP]
> * 작성 리소스 `kind`는 `LUIS.Authoring`입니다.  
> * 예측 리소스 `kind`는 `LUIS`입니다. 

1. Azure CLI에 로그인합니다.

    ```azurecli
    az login
    ```

    이 명령은 올바른 계정을 선택하고 인증을 제공할 수 있도록 브라우저를 엽니다.

2. `LUIS.Authoring` 종류의 `my-luis-authoring-resource`라는 LUIS 작성 리소스를 만듭니다. 이 리소스는 `westus` 지역에 대한 `my-resource-group`이라는 _기존_ 리소스 그룹에 만들어집니다.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

3. `LUIS` 종류의 `my-luis-prediction-resource`라는 LUIS 예측 엔드포인트 리소스를 만듭니다. 이 리소스는 `westus` 지역에 대한 `my-resource-group`이라는 _기존_ 리소스 그룹에 만들어집니다. 체험 계층에서 제공하는 것보다 더 높은 처리량을 원하는 경우 `F0`을 `S0`으로 변경합니다. [가격 책정 계층 및 처리량에 대해 자세히 알아보세요.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

---


## <a name="assign-luis-resources"></a>LUIS 리소스 할당

리소스 만들기는 반드시 사용해야 하는 것을 의미하는 것은 아닙니다. 앱에 할당해야 합니다. 단일 앱 또는 LUIS의 모든 앱에 대한 작성 리소스를 할당할 수 있습니다.

# <a name="luis-portal"></a>[LUIS 포털](#tab/portal)

### <a name="assign-resources-using-the-luis-portal"></a>LUIS 포털을 사용하여 리소스 할당

**모든 앱에 작성 리소스 할당** 

 다음 절차에서는 모든 앱에 작성 리소스를 할당합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인합니다.
1. 오른쪽 위 모서리에서 사용자 계정을 선택한 다음, **설정** 을 선택합니다.
1. **사용자 설정** 페이지에서 **작성 리소스 추가** 를 선택한 다음, 기존 작성 리소스를 선택합니다. **저장** 을 선택합니다.

**특정 앱에 리소스 할당**

다음 절차에서는 특정 앱에 리소스를 할당합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인합니다. **내 앱** 목록에서 앱을 선택합니다.
1. **관리** > **Azure 리소스** 로 차례로 이동합니다.

    :::image type="content" source="./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png" alt-text="Language Understanding 예측 리소스의 유형을 선택합니다." lightbox="./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png":::

1. **예측 리소스** 또는 **작성 리소스** 탭에서 **예측 리소스 추가** 또는 **작성 리소스 추가** 단추를 선택합니다.
1. 양식의 필드를 사용하여 올바른 리소스를 찾은 다음, **저장** 을 선택합니다.

# <a name="without-luis-portal"></a>[LUIS 포털 사용 안 함](#tab/without-portal)

## <a name="assign-prediction-resource-without-using-the-luis-portal"></a>LUIS 포털을 사용하지 않고 예측 리소스 할당

CI/CD 파이프라인과 같은 자동화된 프로세스의 경우 다음 단계를 사용하여 LUIS 앱에 대한 LUIS 리소스 할당을 자동화할 수 있습니다.

1. 문자의 영숫자 문자열인 [Azure Resource Manager 토큰을](https://resources.azure.com/api/token?plaintext=true) 가져옵니다. 이 토큰은 곧 만료되므로 즉시 사용합니다. 다음 Azure CLI 명령을 사용할 수도 있습니다.

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
    
1. 토큰을 사용하여 구독 간에 LUIS 런타임 리소스를 요청합니다. API를 사용하여 사용자 계정에 액세스할 수 있는 [LUIS Azure 계정을 얻습니다](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c).

    이 POST API에 필요한 값은 다음과 같습니다.

    |헤더|값|
    |--|--|
    |`Authorization`|`Authorization` 값이 `Bearer {token}`인 경우 토큰 값 앞에는 `Bearer` 단어 및 공백이 나와야 합니다.|
    |`Ocp-Apim-Subscription-Key`|작성 키|

    API는 LUIS 구독을 나타내는 JSON 개체 배열을 반환합니다. 반환되는 값에는 `AccountName`으로 반환되는 구독 ID, 리소스 그룹 및 리소스 이름이 포함됩니다. LUIS 앱에 할당하려는 LUIS 리소스인 배열에서 해당 항목을 찾습니다.

1. [애플리케이션에 LUIS Azure 계정 할당](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API를 사용하여 토큰을 LUIS 리소스에 할당합니다.

    이 POST API에 필요한 값은 다음과 같습니다.

    |유형|설정|값|
    |--|--|--|
    |헤더|`Authorization`|`Authorization` 값이 `Bearer {token}`인 경우 토큰 값 앞에는 `Bearer` 단어 및 공백이 나와야 합니다.|
    |헤더|`Ocp-Apim-Subscription-Key`|작성 키|
    |헤더|`Content-type`|`application/json`|
    |쿼리 문자열|`appid`|LUIS 앱 ID
    |본문||{`AzureSubscriptionId`: 구독 ID,<br>`ResourceGroup`: 예측 리소스가 있는 리소스 그룹 이름,<br>`AccountName`: 예측 리소스의 이름}|

    이 API가 성공하면 `201 - created status`를 반환합니다.

---

## <a name="unassign-a-resource"></a>리소스 할당 해제

리소스 할당을 해제하는 경우 해당 리소스는 Azure에서 삭제되지 않습니다. 다만 LUIS에서 연결이 해제될 뿐입니다.

# <a name="luis-portal"></a>[LUIS 포털](#tab/portal)

## <a name="unassign-resources-using-luis-portal"></a>LUIS 포털을 사용하여 리소스 할당 취소

1. [LUIS 포털](https://www.luis.ai)에 로그인한 다음, **내 앱** 목록에서 앱을 선택합니다.
1. **관리** > **Azure 리소스** 로 차례로 이동합니다.
1. 리소스에 대한 **리소스 할당 취소** 단추를 선택합니다.

# <a name="without-luis-portal"></a>[LUIS 포털 사용 안 함](#tab/without-portal)

## <a name="unassign-prediction-resource-without-using-the-luis-portal"></a>LUIS 포털을 사용하지 않고 예측 리소스 할당 취소

1. 문자의 영숫자 문자열인 [Azure Resource Manager 토큰을](https://resources.azure.com/api/token?plaintext=true) 가져옵니다. 이 토큰은 곧 만료되므로 즉시 사용합니다. 다음 Azure CLI 명령을 사용할 수도 있습니다.

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
 
1. 토큰을 사용하여 구독 간에 LUIS 런타임 리소스를 요청합니다. 사용자 계정에서 액세스할 수 있는 [LUIS Azure 계정 가져오기 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)를 사용합니다.

    이 POST API에 필요한 값은 다음과 같습니다.

    |헤더|값|
    |--|--|
    |`Authorization`|`Authorization` 값이 `Bearer {token}`인 경우 토큰 값 앞에는 `Bearer` 단어 및 공백이 나와야 합니다.|
    |`Ocp-Apim-Subscription-Key`|작성 키|

    API는 LUIS 구독을 나타내는 JSON 개체 배열을 반환합니다. 반환되는 값에는 `AccountName`으로 반환되는 구독 ID, 리소스 그룹 및 리소스 이름이 포함됩니다. LUIS 앱에 할당하려는 LUIS 리소스인 배열에서 해당 항목을 찾습니다.

1. [애플리케이션에서 LUIS Azure 계정 할당 취소](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32554f8591db3a86232e1/console) API를 사용하여 토큰을 LUIS 리소스에 할당합니다.

    이 DELETE API에 필요한 값은 다음과 같습니다.

    |유형|설정|값|
    |--|--|--|
    |헤더|`Authorization`|`Authorization` 값이 `Bearer {token}`인 경우 토큰 값 앞에는 `Bearer` 단어 및 공백이 나와야 합니다.|
    |헤더|`Ocp-Apim-Subscription-Key`|작성 키|
    |헤더|`Content-type`|`application/json`|
    |쿼리 문자열|`appid`|LUIS 앱 ID
    |본문||{`AzureSubscriptionId`: 구독 ID,<br>`ResourceGroup`: 예측 리소스가 있는 리소스 그룹 이름,<br>`AccountName`: 예측 리소스의 이름}|

    이 API가 성공하면 `200 - OK status`를 반환합니다.

---

## <a name="resource-ownership"></a>리소스 소유권

LUIS 리소스와 같은 Azure 리소스는 리소스를 포함하는 구독에서 소유합니다.

리소스 소유권을 변경하려면 다음 작업 중 하나를 수행할 수 있습니다.
* 구독 [소유권](../../cost-management-billing/manage/billing-subscription-transfer.md)을 양도합니다.
* LUIS 앱을 파일로 내보낸 다음, 다른 구독에서 앱을 가져옵니다. 내보내기는 LUIS 포털의 **내 앱** 페이지에서 사용할 수 있습니다.

## <a name="resource-limits"></a>리소스 제한

### <a name="authoring-key-creation-limits"></a>작성 키 만들기 제한

구독당 지역별 최대 10개의 작성 키를 만들 수 있습니다. 게시 지역은 작성 지역과 다릅니다. 클라이언트 애플리케이션을 배치할 게시 지역에 해당하는 작성 지역에 앱을 만들어야 합니다. 작성 지역이 게시 지역에 매핑되는 방법에 대한 내용은 [작성 및 게시 지역](luis-reference-regions.md)을 참조하세요. 

키 제한에 대한 자세한 내용은 [키 제한](luis-limits.md#key-limits)을 참조하세요.

### <a name="errors-for-key-usage-limits"></a>키 사용량 제한 오류

사용량 제한은 가격 책정 계층을 기반으로 합니다.

TPS(초당 트랜잭션 수) 할당량을 초과하는 경우 HTTP 429 오류가 표시됩니다. TPM(월별 트랜잭션 수) 할당량을 초과하는 경우 HTTP 403 오류가 표시됩니다.

## <a name="change-the-pricing-tier"></a>가격 책정 계층 변경

1.  [Azure portal](https://portal.azure.com)에서 **모든 리소스** 로 이동하여 리소스를 선택합니다.

    :::image type="content" source="./media/luis-usage-tiers/find.png" alt-text="Azure Portal의 LUIS 구독을 보여 주는 스크린샷" lightbox="./media/luis-usage-tiers/find.png":::

1.  왼쪽 메뉴에서 **가격 책정 계층** 을 선택하여 사용 가능한 가격 책정 계층을 확인합니다.
1.  원하는 가격 책정 계층을 선택하고, **선택** 을 클릭하여 변경 내용을 저장합니다. 가격 책정 변경이 완료되면 가격 책정 계층 업데이트가 있는 알림이 오른쪽 위에 표시됩니다.

## <a name="view-azure-resource-metrics"></a>Azure 리소스 메트릭 보기

## <a name="view-a-summary-of-azure-resource-usage"></a>Azure 리소스 사용량 요약 보기
LUIS 사용량 정보는 Azure Portal에서 볼 수 있습니다. **개요** 페이지에는 최근 호출 및 오류를 포함한 요약이 표시됩니다. LUIS 엔드포인트를 요청하는 경우 변경 내용이 표시될 때까지 최대 5분 정도 기다립니다.

:::image type="content" source="./media/luis-usage-tiers/overview.png" alt-text="개요 페이지를 보여 주는 스크린샷" lightbox="./media/luis-usage-tiers/overview.png":::

## <a name="customizing-azure-resource-usage-charts"></a>Azure 리소스 사용량 차트 사용자 지정
**메트릭** 페이지는 데이터에 대한 자세한 보기를 제공합니다. 특정 **기간** 및 **메트릭** 에 대한 메트릭 차트를 구성할 수 있습니다.

:::image type="content" source="./media/luis-usage-tiers/metrics.png" alt-text="메트릭 페이지를 보여 주는 스크린샷" lightbox="./media/luis-usage-tiers/metrics.png":::

## <a name="total-transactions-threshold-alert"></a>총 트랜잭션 임계값 경고
특정 트랜잭션 임계값(예: 10,000개 트랜잭션)에 도달한 시기를 확인하려는 경우 경고를 만들 수 있습니다.

1. 왼쪽 메뉴에서 **경고** 를 선택합니다.
2. 맨 위의 메뉴에서 **새 경고 규칙** 을 선택합니다.

    :::image type="content" source="./media/luis-usage-tiers/alerts.png" alt-text="경고 규칙 페이지를 보여 주는 스크린샷" lightbox="./media/luis-usage-tiers/alerts.png":::

3. **조건 추가** 를 클릭합니다.

    :::image type="content" source="./media/luis-usage-tiers/alerts-2.png" alt-text="경고 규칙에 대한 조건 추가 페이지를 보여 주는 스크린샷" lightbox="./media/luis-usage-tiers/alerts-2.png":::

4. **총 호출** 을 선택합니다.

    :::image type="content" source="./media/luis-usage-tiers/alerts-3.png" alt-text="경고에 대한 총 호출 페이지를 보여 주는 스크린샷" lightbox="./media/luis-usage-tiers/alerts-3.png":::

5. **경고 논리** 섹션으로 스크롤하고 원하는 대로 특성을 설정하고 **완료** 를 클릭합니다.

    :::image type="content" source="./media/luis-usage-tiers/alerts-4.png" alt-text="경고 논리 페이지를 보여 주는 스크린샷" lightbox="./media/luis-usage-tiers/alerts-4.png":::

6. 경고 규칙 트리거가 **작업** 섹션으로 이동하고 작업 그룹을 추가할 때 알림을 보내거나 동작을 호출하려면 다음을 수행합니다.

    :::image type="content" source="./media/luis-usage-tiers/alerts-5.png" alt-text="경고에 대한 작업 페이지를 보여 주는 스크린샷" lightbox="./media/luis-usage-tiers/alerts-5.png":::

### <a name="reset-an-authoring-key"></a>작성 키 다시 설정

[마이그레이션된 작성 리소스](luis-migration-authoring.md) 앱의 경우: 작성 키가 손상되면 Azure Portal의 작성 리소스에 대한 **키** 페이지에서 키를 다시 설정합니다.

마이그레이션되지 않은 앱의 경우: LUIS 포털의 모든 앱에서 키를 다시 설정합니다. 작성 API를 통해 앱을 작성하는 경우 `Ocp-Apim-Subscription-Key` 값을 새 키로 변경해야 합니다.

### <a name="regenerate-an-azure-key"></a>Azure 키 다시 생성

Azure 키는 Azure Portal의 **키** 페이지에서 다시 생성할 수 있습니다.

<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>앱 소유권, 액세스 및 보안

앱은 소유자의 구독에 따라 결정되는 Azure 리소스에서 정의됩니다.

LUIS 앱은 이동할 수 있습니다. 다음 리소스를 사용하면 Azure Portal 또는 Azure CLI를 통해 이 작업을 수행할 수 있습니다.

* [LUIS 작성 리소스 간에 앱 이동](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [동일한 구독 내에서 또는 구독 간에 리소스 이동](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


## <a name="next-steps"></a>다음 단계

* [버전을 사용](luis-how-to-manage-versions.md)하여 앱 수명 주기를 제어하는 방법을 알아봅니다.


