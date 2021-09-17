---
title: Azure Portal에서 Cognitive Services 리소스 만들기
titleSuffix: Azure Cognitive Services
description: Azure Portal에서 리소스를 만들고 구독하여 Azure Cognitive Services를 시작합니다.
services: cognitive-services
author: aahill
manager: nitinme
keywords: 인지 서비스, 인지 인텔리전스, 인지 솔루션, AI 서비스
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: aahi
ms.openlocfilehash: dfd5edc2444b3aa56dce556766013370a08ba846
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123475965"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Cognitive Services 리소스 만들기

이 빠른 시작을 참조하여 Azure Cognitive Services를 시작하세요. Azure Portal에서 Cognitive Service 리소스를 만든 후에는 애플리케이션을 인증하는 데 사용할 엔드포인트와 키를 가져옵니다.

Azure Cognitive Services는 REST API가 있는 클라우드 기반 서비스이며, 개발자가 직접적인 AI(인공 지능) 또는 데이터 과학 기술이나 지식 없이도 인지적 인텔리전스를 애플리케이션에 빌드하도록 지원하는 데 사용할 수 있는 클라이언트 라이브러리 SDK입니다. Azure Cognitive Services를 사용하는 개발자는 보고, 듣고, 말하고, 이해하고, 추론할 수도 있는 인지적 솔루션을 통해 애플리케이션에 인지적 기능을 손쉽게 추가할 수 있습니다.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>사전 요구 사항

* 유효한 Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/).
* [!INCLUDE [contributor-requirement](./includes/quickstarts/contributor-requirement.md)]


## <a name="create-a-new-azure-cognitive-services-resource"></a>새 Azure Cognitive Services 리소스 만들기

1. 리소스를 만듭니다.

### <a name="multi-service-resource"></a>[다중 서비스 리소스](#tab/multiservice)

다중 서비스 리소스의 이름은 포털에서 **Cognitive Services** 로 지정됩니다. [Cognitive Services 리소스를 만드세요](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).

현재는 다중 서비스 리소스를 사용하여 다음 Cognitive Services에 액세스할 수 있습니다.

* **시력** Computer Vision, Custom Vision, 얼굴
* **음성** - 음성
* **언어** - Language Understanding(LUIS), Text Analytics, Translator
* **결정** - Content Moderator

### <a name="single-service-resource"></a>[단일 서비스 리소스](#tab/singleservice)

아래 링크를 사용하여 사용 가능한 Cognitive Services에 대한 리소스를 만듭니다.

| 시각                      | 음성                  | 언어                          | 의사 결정             |
|-----------------------------|-------------------------|-----------------------------------|----------------------|
| [컴퓨터 비전](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [음성 서비스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [몰입형 판독기](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Anomaly Detector](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | 
| [Custom Vision Service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) |  | [언어 이해(LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | 
| [Face](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     |
|        |                         | [텍스트 분석](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2142156)                    |
| | | [Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) | |

---

2. **만들기** 페이지에서 다음 정보를 제공합니다.
<!-- markdownlint-disable MD024 -->

### <a name="multi-service-resource"></a>[다중 서비스 리소스](#tab/multiservice)

|프로젝트 세부 정보| Description   |
|--|--|
| **구독** | 사용 가능한 Azure 구독 중 하나를 선택합니다. |
| **리소스 그룹** | Cognitive Services 리소스를 포함할 Azure 리소스 그룹입니다. 새 그룹을 만들거나 기존 그룹에 추가할 수 있습니다. |
| **지역** | Cognitive Service 인스턴스의 위치입니다. 다른 위치를 사용하면 대기 시간이 발생할 수 있지만 리소스의 런타임 가용성에는 영향을 주지 않습니다. |
| **이름** | Cognitive Services 리소스를 설명하는 이름입니다. 예: *MyCognitiveServicesResource*. |
| **가격 책정 계층** | Cognitive Services 계정의 비용은 선택한 옵션 및 사용량에 따라 다릅니다. 자세한 내용은 API [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/)를 참조하세요.

<!--![Multi-service resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)-->
:::image type="content" source="media/cognitive-services-apis-create-account/resource_create_screen-multi.png" alt-text="다중 서비스 리소스 만들기 화면":::

해당하는 조건을 읽고 동의한 다음, **검토 + 만들기** 를 선택합니다.

### <a name="single-service-resource"></a>[단일 서비스 리소스](#tab/singleservice)

|프로젝트 세부 정보| Description   |
|--|--|
| **구독** | 사용 가능한 Azure 구독 중 하나를 선택합니다. |
| **리소스 그룹** | Cognitive Services 리소스를 포함할 Azure 리소스 그룹입니다. 새 그룹을 만들거나 기존 그룹에 추가할 수 있습니다. |
| **지역** | Cognitive Service 인스턴스의 위치입니다. 다른 위치를 사용하면 대기 시간이 발생할 수 있지만 리소스의 런타임 가용성에는 영향을 주지 않습니다. |
| **이름** | Cognitive Services 리소스를 설명하는 이름입니다. 예: *MyCognitiveServicesResource*. |
| **가격 책정 계층** | Cognitive Services 계정의 비용은 선택한 옵션 및 사용량에 따라 다릅니다. 자세한 내용은 API [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/cognitive-services/)를 참조하세요.

<!--![Single-service resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen.png)-->
:::image type="content" source="media/cognitive-services-apis-create-account/resource_create_screen.png" alt-text="단일 서비스 리소스 만들기 화면":::

**다음: Virtual Network** 를 선택하고 리소스에 대해 허용할 네트워크 액세스 유형을 선택한 다음, **검토 + 만들기** 를 선택합니다.

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>리소스의 키 가져오기

1. 리소스가 성공적으로 배포되면 **다음 단계** 아래에서 **리소스로 이동** 을 클릭합니다.

    ![Cognitive Services 검색](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. 열리는 빠른 시작 창에서 키와 엔드포인트에 액세스할 수 있습니다.

    ![키 및 엔드포인트 가져오기](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 그룹에 포함된 모든 리소스가 함께 삭제됩니다.

1. Azure Portal에서 왼쪽의 메뉴를 확장하여 서비스 메뉴를 열고 **리소스 그룹** 을 선택하여 리소스 그룹 목록을 표시합니다.
2. 삭제할 리소스가 포함된 리소스 그룹을 찾습니다.
3. 리소스 그룹 목록을 마우스 오른쪽 단추로 클릭합니다. **리소스 그룹 삭제** 를 선택하고 확인합니다.

삭제된 리소스를 복구해야 하는 경우 [삭제된 Cognitive Services 리소스 복구](manage-resources.md)를 참조하세요.

## <a name="see-also"></a>참고 항목

* Cognitive Services를 안전하게 사용하는 방법을 알아보려면 **[Azure Cognitive Services 요청 인증](authentication.md)** 을 참조하세요.
* Cognitive Services에 포함된 다양한 범주 목록을 가져오려면 **[Azure Cognitive Services란?](./what-are-cognitive-services.md)** 을 참조하세요.
* Cognitive Services에서 지원하는 자연어 목록을 보려면 **[자연어 지원](language-support.md)** 을 참조하세요.
* Cognitive Services를 온-프레미스로 사용하는 방법은 **[Cognitive Services를 컨테이너로 사용](cognitive-services-container-support.md)** 을 참조하세요.
* Cognitive Services 사용 비용을 추산하려면 **[Cognitive Services 비용 계획 및 관리](plan-manage-costs.md)** 를 참조하세요.
