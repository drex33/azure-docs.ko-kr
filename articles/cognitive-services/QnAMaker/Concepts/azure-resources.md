---
title: Azure 리소스 - QnA Maker
description: QnA Maker는 각각 다른 용도로 사용되는 여러 Azure 원본을 사용합니다. 이를 개별적으로 사용하는 방법을 이해하면 올바른 가격 책정 계층을 계획하고 선택하거나 가격 책정 계층을 변경하는 시기를 알 수 있습니다. 이를 결합하여 사용하는 방법을 이해하면 문제 발생 시 해당 문제를 찾아 해결할 수 있습니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/11/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: e421c2e799e7eeb002cffab2748f099f21f81199
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012001"
---
# <a name="azure-resources-for-qna-maker"></a>QnA Maker용 Azure 리소스

QnA Maker는 각각 다른 용도로 사용되는 여러 Azure 원본을 사용합니다. 이를 개별적으로 사용하는 방법을 이해하면 올바른 가격 책정 계층을 계획하고 선택하거나 가격 책정 계층을 변경하는 시기를 알 수 있습니다. 이를 _결합하여_ 사용하는 방법을 이해하면 문제 발생 시 해당 문제를 찾아 해결할 수 있습니다.

[!INCLUDE [Info on question answering GA](../includes/new-version.md)]

## <a name="resource-planning"></a>리소스 계획

QnA Maker 기술 자료를 처음 개발하는 경우 프로토타입 단계에서는 일반적으로 테스트와 프로덕션 모두에 적합한 단일 QnA Maker 리소스를 사용합니다.

프로젝트의 개발 단계로 이동하는 경우 다음 사항을 고려해야 합니다.

* 기술 자료 시스템에 포함될 언어의 수
* 사용 가능한 기술 자료가 필요한 지역의 수
* 시스템에 포함될 각 도메인의 문서 수

동일한 언어, 동일한 지역 및 동일한 주체 도메인이 결합된 모든 기술 자료를 단일 QnA Maker 리소스에 포함하려고 합니다.

## <a name="pricing-tier-considerations"></a>가격 책정 계층 고려 사항

일반적으로 고려해야 하는 다음 세 가지 매개 변수가 있습니다.

* **서비스에서 필요한 처리량**:
    * 필요에 따라 App Service에 적합한 [앱 플랜](https://azure.microsoft.com/pricing/details/app-service/plans/)을 선택합니다. 앱을 [확장](../../../app-service/manage-scale-up.md)하거나 축소할 수 있습니다.
    * 이 경우 Azure **Cognitive Search** SKU를 선택하는 데에도 영향을 줍니다. 자세한 내용은 [여기](../../../search/search-sku-tier.md)를 참조하세요. 또한 복제본을 사용하여 Cognitive Search [용량](../../../search/search-capacity-planning.md)을 조정해야 할 수도 있습니다.

* **기술 자료의 크기 및 개수**: 시나리오에 적합한 [Azure Search SKU](https://azure.microsoft.com/pricing/details/search/)를 선택합니다. 일반적으로 다양한 주체 도메인의 수에 따라 필요한 기술 자료의 수를 결정합니다. 단일 언어에 대한 주체 도메인은 하나의 기술 자료에 있어야 합니다.

Azure Search Service 리소스는 2019년 1월 이후에 만들어졌어야 하며 체험(공유) 계층에 있을 수 없습니다. 고객 관리형 키는 Azure Portal에서 구성할 수 없습니다.

> [!IMPORTANT]
> 특정 계층에 N-1개의 기술 자료를 게시할 수 있습니다. 여기서 N은 계층에서 허용되는 최대 인덱스 개수입니다. 또한 계층당 허용되는 문서의 최대 크기 및 개수를 확인합니다.

예를 들어 계층에 15개의 허용된 인덱스가 있는 경우 14개의 기술 자료(게시된 기술 자료당 하나의 인덱스)를 게시할 수 있습니다. 15번째 인덱스는 모든 기술 자료에서 작성 및 테스트용으로 사용됩니다.

* **원본 문서 수**: QnA Maker 관리 서비스의 체험 SKU는 포털 및 API를 통해 관리할 수 있는 문서 수를 3개(각각 1MB 크기)로 제한합니다. 표준 SKU는 관리할 수 있는 문서 수에 대한 제한이 없습니다. 자세한 내용은 [여기](https://aka.ms/qnamaker-pricing)를 참조하세요.

다음 표에서는 몇 가지 전반적인 지침을 제공합니다.

|                            | QnA Maker 관리 | App Service | Azure Cognitive Search | 제한 사항                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **실험**        | 체험 SKU             | 체험 계층   | 체험 계층    | KB 최대 2개, 50MB 크기까지 게시  |
| **개발/테스트 환경**   | 표준 SKU         | 공유됨      | Basic        | 최대 14KB, 2GB 크기까지 게시    |
| **프로덕션 환경** | 표준 SKU         | Basic       | Standard     | KB 최대 49개, 25GB 크기까지 게시 |

## <a name="recommended-settings"></a>권장 설정

|대상 QPS | App Service | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 하나의 복제본   | S1, 하나의 복제본    |
| 50         | S3, 10개 복제본       | S1, 12개 복제본         |
| 80         | S3, 10개 복제본      |  S3, 12개 복제본  |
| 100         | P3V2, 10개 복제본  | S3, 12개 복제본, 3개 파티션   |
| 200~250         | P3V2, 20개 복제본 | S3, 12개 복제본, 3개 파티션    |

## <a name="when-to-change-a-pricing-tier"></a>가격 책정 계층을 변경하는 경우

|업그레이드|이유|
|--|--|
|QnA Maker 관리 SKU [업그레이드](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)|기술 자료에 더 많은 QnA 쌍 또는 문서 원본을 포함하려고 합니다.|
|App Service SKU [업그레이드](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service), Cognitive Search 계층 확인 및 [Cognitive Search 복제본 만들기](../../../search/search-capacity-planning.md)|기술 자료는 챗봇과 같은 클라이언트 앱의 더 많은 요청을 처리해야 합니다.|
|Azure Cognitive Search 서비스 [업그레이드](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)|많은 기술 자료를 포함하려고 합니다.|

[Azure Portal에서 App Service를 업데이트](../how-to/configure-QnA-Maker-resources.md#get-the-latest-runtime-updates)하여 최신 런타임 업데이트를 가져옵니다.

## <a name="keys-in-qna-maker"></a>QnA Maker의 키

QnA Maker 서비스는 App Service에서 호스팅되는 런타임에 사용되는 **작성 키** 및 **쿼리 엔드포인트 키** 라는 두 가지 종류의 키를 처리합니다.

이러한 키는 API를 통해 서비스에 요청할 때 사용합니다.

![키 관리](../media/authoring-key.png)

|이름|Location|목적|
|--|--|--|
|작성/구독 키|[Azure Portal](https://azure.microsoft.com/free/cognitive-services/)|이러한 키는 [QnA Maker 관리 서비스 API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)에 액세스하는 데 사용됩니다. 이러한 API를 사용하면 기술 자료의 질문과 대답을 편집하고 기술 자료를 게시할 수 있습니다. 이러한 키는 새 QnA Maker 서비스를 만들 때 만들어집니다.<br><br>이러한 키는 **키 및 엔드포인트** 페이지의 **Cognitive Services** 리소스에서 찾습니다.|
|쿼리 엔드포인트 키|[QnA Maker 포털](https://www.qnamaker.ai)|이러한 키는 사용자 질문에 대한 응답을 가져오기 위해 게시된 기술 자료 엔드포인트를 쿼리하는 데 사용됩니다. 일반적으로 이 쿼리 엔드포인트는 챗봇 또는 QnA Maker 서비스에 연결되는 클라이언트 애플리케이션 코드에서 사용됩니다. 이러한 키는 QnA Maker 기술 자료를 게시할 때 만들어집니다.<br><br>이러한 키는 **서비스 설정** 페이지에서 찾습니다. 이 페이지는 페이지 오른쪽 위의 드롭다운 메뉴에 있는 사용자 메뉴에서 찾습니다.|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Azure Portal에서 작성 키 찾기

QnA Maker 리소스를 만든 Azure Portal에서 작성 키를 보고 다시 설정할 수 있습니다.

1. Azure Portal에서 QnA Maker 리소스로 이동하여 _Cognitive Services_ 종류의 리소스를 선택합니다.

    ![QnA Maker 리소스 목록](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. **키 및 엔드포인트** 로 이동합니다.

    ![QnA Maker 관리형(미리 보기) 구독 키](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="find-query-endpoint-keys-in-the-qna-maker-portal"></a>QnA Maker 포털에서 쿼리 엔드포인트 키 찾기

엔드포인트 키는 기술 자료로 호출하는 데 사용되므로 엔드포인트는 리소스와 동일한 지역에 있습니다.

엔드포인트 키는 [QnA Maker 포털](https://qnamaker.ai)에서 관리할 수 있습니다.

1. [QnA Maker 포털](https://qnamaker.ai)에 로그인하고, 프로필로 이동한 다음, **서비스 설정** 을 선택합니다.

    ![엔드포인트 키](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 키를 보거나 다시 설정합니다.

    > [!div class="mx-imgBorder"]
    > ![엔드포인트 키 관리자](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >키가 손상된 것처럼 보이면 새로 고치세요. 클라이언트 애플리케이션 또는 봇 코드에 해당 변경 내용을 적용해야 할 수도 있습니다.

## <a name="management-service-region"></a>관리 서비스 지역

QnA Maker의 관리 서비스는 QnA Maker 포털 및 초기 데이터 처리에만 사용됩니다. 이 서비스는 **미국 서부** 지역에서만 사용할 수 있습니다. 이 미국 서부 서비스에는 고객 데이터가 저장되지 않습니다.

## <a name="resource-naming-considerations"></a>리소스 명명 고려 사항

QnA Maker 리소스의 리소스 이름(예: `qna-westus-f0-b`)도 다른 리소스의 이름을 지정하는 데 사용됩니다.

Azure Portal 만들기 창에서 QnA Maker 리소스를 만들고, 다른 리소스에 대한 가격 책정 계층을 선택할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![QnA Maker 리소스를 만들기 위한 Azure Portal의 스크린샷](../media/concept-azure-resource/create-blade.png)

리소스가 만들어지면 문자를 이름에 추가하는 선택적 Application Insights 리소스를 제외하고는 동일한 이름을 갖습니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal 리소스 목록의 스크린샷](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> QnA Maker 리소스를 만들 때 새 리소스 그룹을 만듭니다. 이렇게 하면 리소스 그룹별로 검색할 때 QnA Maker 리소스와 연결된 모든 리소스를 볼 수 있습니다.

> [!TIP]
> 명명 규칙을 사용하여 리소스 또는 리소스 그룹의 이름 내에서 가격 책정 계층을 나타냅니다. 새 기술 자료를 만들거나 새 문서를 추가할 때 오류가 발생하는 경우 일반적으로 Cognitive Search 가격 책정 계층 제한 문제입니다.

## <a name="resource-purposes"></a>리소스 용도

QnA Maker를 사용하여 만든 각 Azure 리소스에는 특정 용도가 있습니다.

* QnA Maker 리소스
* Cognitive Search 리소스
* App Service
* App Service 요금제
* Application Insights 서비스

### <a name="qna-maker-resource"></a>QnA Maker 리소스

QnA Maker 리소스는 런타임에 QnA 쌍의 NLP(자연어 처리) 기반 2번째 순위 계층(#2 순위 매기기)뿐만 아니라 작성 및 게시 API에 대한 액세스도 제공합니다.

2번째 순위는 메타데이터 및 추가 작업 프롬프트를 포함할 수 있는 인텔리전트 필터를 적용합니다.

#### <a name="qna-maker-resource-configuration-settings"></a>QnA Maker 리소스 구성 설정

[QnA Maker 포털](https://qnamaker.ai)에서 새 기술 자료를 만드는 경우 **언어** 설정은 리소스 수준에서 적용되는 유일한 설정입니다. 리소스에 대한 첫 번째 기술 자료를 만들 때 언어를 선택합니다.

### <a name="cognitive-search-resource"></a>Cognitive Search 리소스

[Cognitive Search](../../../search/index.yml) 리소스는 다음 용도로 사용됩니다.

* QnA 쌍 저장
* 런타임에 QnA 쌍의 초기 순위(#1 순위 매기기) 제공

#### <a name="index-usage"></a>인덱스 사용

리소스는 테스트 인덱스 역할을 하는 하나의 인덱스를 유지하고, 나머지 인덱스는 각각 게시되는 하나의 기술 자료와 상관 관계가 있습니다.

15개의 인덱스를 포함하도록 가격이 책정된 리소스는 14개의 게시되는 기술 자료를 포함하며, 하나의 인덱스는 모든 기술 자료를 테스트하는 데 사용됩니다. 대화형 테스트 창을 사용하는 쿼리에서 테스트 인덱스를 사용하지만 특정 기술 자료와 연결된 특정 파티션의 결과만 반환하도록 이 테스트 인덱스는 기술 자료별로 분할되어 있습니다.

#### <a name="language-usage"></a>언어 사용

QnA Maker 리소스에서 만든 첫 번째 기술 자료는 Cognitive Search 리소스 및 모든 인덱스에 대한 _단일_ 언어 세트를 결정하는 데 사용됩니다. QnA Maker 서비스에 대해 _하나의 언어 세트_ 만 설정할 수 있습니다.

#### <a name="using-a-single-cognitive-search-service"></a>단일 Cognitive Search 서비스 사용

포털을 통해 QnA 서비스 및 해당 종속성(예: Search)을 만드는 경우 Search Service가 만들어지고 QnA Maker 서비스에 연결됩니다. 이러한 리소스가 만들어지면 이전에 기존 Search Service를 사용하고 방금 만든 서비스를 제거하도록 App Service 설정을 업데이트할 수 있습니다.

QnA Maker 리소스 만들기 프로세스의 일부로 만든 것과 다른 Cognitive Service 리소스를 사용하도록 QnA Maker를 [구성하는 방법](../How-To/configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource)에 대해 알아 보세요.

### <a name="app-service-and-app-service-plan"></a>App Service 및 App Service 요금제

[App Service](../../../app-service/index.yml)는 클라이언트 애플리케이션에서 런타임 엔드포인트를 통해 게시된 기술 자료에 액세스하는 데 사용됩니다.

게시된 기술 자료를 쿼리하려면 게시된 모든 기술 자료에서 동일한 URL 엔드포인트를 사용하고 **기술 자료 ID** 를 경로 내에 지정합니다.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md)는 채팅 로그 및 원격 분석을 수집하는 데 사용됩니다. 서비스에 대한 자세한 내용은 일반적인 [Kusto 쿼리](../how-to/get-analytics-knowledge-base.md)를 검토하세요.

### <a name="share-services-with-qna-maker"></a>QnA Maker와 서비스 공유

QnA Maker는 여러 Azure 리소스를 만듭니다. 관리를 줄이고 비용 공유를 활용하려면 다음 표를 사용하여 공유할 수 있거나 공유할 수 없는 리소스를 파악합니다.

|서비스|공유|이유|
|--|--|--|
|Cognitive Services|X|디자인상 불가능|
|App Service 계획|✔|App Service 요금제에 할당된 고정 디스크 공간입니다. 동일한 App Service 요금제를 공유하는 다른 앱에서 상당한 디스크 공간을 사용하는 경우 QnAMaker App Service 인스턴스에 문제가 발생합니다.|
|App Service|X|디자인상 불가능|
|Application Insights|✔|공유할 수 있습니다.|
|Search 서비스|✔|1. `testkb`는 QnAMaker 서비스의 예약된 이름입니다. 다른 사용자가 사용할 수 없습니다.<br>2. 이름이 `synonym-map`인 동의어 맵은 QnAMaker 서비스에 예약되어 있습니다.<br>3. 게시된 기술 자료 수는 Search Service 계층으로 제한됩니다. 사용 가능한 무료 인덱스가 있는 경우 다른 서비스에서 이 인덱스를 사용할 수 있습니다.|

## <a name="next-steps"></a>다음 단계

* QnA Maker [기술 자료](../How-To/manage-knowledge-bases.md)에 대해 알아보기
* [기술 자료 수명 주기](development-lifecycle-knowledge-base.md) 이해
* 서비스 및 기술 자료 [제한](../limits.md) 검토
