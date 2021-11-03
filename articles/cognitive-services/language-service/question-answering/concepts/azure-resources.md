---
title: Azure 리소스-질문과 대답
description: 질문에 대 한 답변은 각각 다른 목적으로 여러 Azure 소스를 사용 합니다. 이를 개별적으로 사용하는 방법을 이해하면 올바른 가격 책정 계층을 계획하고 선택하거나 가격 책정 계층을 변경하는 시기를 알 수 있습니다. 이를 결합하여 사용하는 방법을 이해하면 문제 발생 시 해당 문제를 찾아 해결할 수 있습니다.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 10/10/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 0ed037e44ff202ec1fd0cacfbbbcfb16ee9be273
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103049"
---
# <a name="azure-resources-for-question-answering"></a>질문에 대 한 답변에 대 한 Azure 리소스

질문에 대 한 답변은 각각 다른 목적으로 여러 Azure 소스를 사용 합니다. 이를 개별적으로 사용하는 방법을 이해하면 올바른 가격 책정 계층을 계획하고 선택하거나 가격 책정 계층을 변경하는 시기를 알 수 있습니다. 리소스를 _조합_ 하 여 사용 하는 방법을 이해 하면 문제 발생 시 문제를 찾아 해결할 수 있습니다.

## <a name="resource-planning"></a>리소스 계획

기술 자료를 처음 개발하는 경우 프로토타입 단계에서는 일반적으로 테스트와 프로덕션 모두에 적합한 단일 리소스를 사용합니다.

> [!TIP]
> "기술 자료" 및 "project"는 질문과 대답에 해당 하는 용어 이며 교대로 사용할 수 있습니다.

프로젝트의 개발 단계로 이동하는 경우 다음 사항을 고려해야 합니다.

* 기술 자료 시스템에 포함될 언어의 수
* 사용 가능한 기술 자료가 필요한 지역의 수
* 시스템에 포함될 각 도메인의 문서 수

## <a name="pricing-tier-considerations"></a>가격 책정 계층 고려 사항

일반적으로 고려해야 하는 다음 세 가지 매개 변수가 있습니다.

* **필요한 처리량**:
    * 질문에 대 한 답변은 무료 기능이 며 처리량은 현재 관리 Api 및 예측 Api에 대 한 초당 10 개 트랜잭션으로 더 있습니다.
    * 이 경우 Azure **Cognitive Search** SKU를 선택하는 데에도 영향을 줍니다. 자세한 내용은 [여기](../../../../search/search-sku-tier.md)를 참조하세요. 또한 복제본을 사용하여 Cognitive Search [용량](../../../../search/search-capacity-planning.md)을 조정해야 할 수도 있습니다.

* **기술 자료의 크기 및 개수**: 시나리오에 적합한 [Azure Search SKU](https://azure.microsoft.com/pricing/details/search/)를 선택합니다. 일반적으로 다른 주체 도메인의 수에 따라 필요한 기술 자료의 수를 결정 합니다. 하나의 주제 도메인 (단일 언어의 경우)은 하나의 기술 자료에 있어야 합니다.

    사용자 지정 질문에 대 한 답변을 통해 언어 리소스를 단일 언어나 여러 언어로 설정할 수 있습니다. [언어 스튜디오](https://language.azure.com/)에서 첫 번째 프로젝트를 만들 때이를 선택할 수 있습니다.

    > [!IMPORTANT]
    > 특정 계층에서 단일 언어의 N-1 기술 자료 또는 다른 언어의 N/2 기술 자료를 게시할 수 있습니다. 여기서 N은 해당 계층에서 허용되는 최대 인덱스 수입니다. 또한 계층당 허용되는 문서의 최대 크기 및 개수를 확인합니다.

    예를 들어 계층에 허용 되는 인덱스가 15 인 경우 동일한 언어의 14 개 기술 자료를 게시할 수 있습니다 (게시 된 기술 자료 당 하나의 인덱스). 15 번째 인덱스는 작성 및 테스트를 위한 모든 기술 자료에 사용 됩니다. 다른 언어로 기술 자료를 선택 하는 경우 7 개의 기술 자료만 게시할 수 있습니다.

* **원본으로 문서 수**: 질문에 대 한 답변은 무료 기능이 며, 소스로 추가할 수 있는 문서 수에는 제한이 없습니다. 

다음 표에서는 몇 가지 전반적인 지침을 제공합니다.

|                            |Azure Cognitive Search | 제한 사항                      |
| -------------------------- |------------ | -------------------------------- |
| **실험**        |체험 계층    | KB 최대 2개, 50MB 크기까지 게시  |
| **개발/테스트 환경**   |Basic        | 최대 14KB, 2GB 크기까지 게시    |
| **프로덕션 환경** |Standard     | KB 최대 49개, 25GB 크기까지 게시 |

## <a name="recommended-settings"></a>권장 설정

사용자 지정 질문에 대 한 답변은 무료 기능이 며 처리량은 현재 관리 Api 및 예측 Api에 대해 초당 10 개의 트랜잭션으로 더 이상 제공 되지 않습니다. 서비스에 대해 초당 10개 트랜잭션을 대상으로 하려면 Azure Cognitive Search의 S1(1개 인스턴스) SKU를 선택하는 것이 좋습니다.

## <a name="keys-in-question-answering"></a>질문에 대 한 답변의 키

사용자 지정 질문 응답 기능은 두 가지 종류의 키를 처리 합니다. 즉, 고객의 구독에서 서비스에 액세스 하는 데 사용 되는 키와 **Azure Cognitive Search 키** 를 **작성** 합니다.

이러한 키는 API를 통해 서비스에 요청할 때 사용합니다.

> [!div class="mx-imgBorder"]
> ![키 관리 관리 미리 보기의 스크린샷](../../../qnamaker/media/qnamaker-how-to-key-management/custom-question-answering-key-management.png)

|이름|Location|목적|
|--|--|--|
|작성/구독 키|[Azure Portal](https://azure.microsoft.com/free/cognitive-services/)|이러한 키는 언어 서비스 Api에 액세스 하는 데 사용 됩니다. 이러한 API를 사용하면 기술 자료의 질문과 대답을 편집하고 기술 자료를 게시할 수 있습니다. 이러한 키는 새 리소스를 만들 때 생성 됩니다.<br><br>이러한 키는 **키 및 엔드포인트** 페이지의 **Cognitive Services** 리소스에서 찾습니다.|
|Azure Cognitive Search 관리자 키|[Azure Portal](../../../../search/search-security-api-keys.md)|이러한 키는 사용자의 Azure 구독에 배포된 Azure Cognitive Search 서비스와 통신하는 데 사용됩니다. Azure Cognitive Search 리소스를 사용자 지정 질문 응답 기능과 연결 하면 관리자 키가 자동으로 질문 응답에 전달 됩니다. <br><br>이러한 키는 **키** 페이지의 **Azure Cognitive Search** 리소스에서 찾을 수 있습니다.|

### <a name="find-authoring-keys-in-the-azure-portal"></a>Azure Portal에서 작성 키 찾기

Text Analytics 리소스에 사용자 지정 질문 답변(미리 보기) 기능을 추가한 Azure Portal에서 작성 키를 보고 다시 설정할 수 있습니다.

1. Azure Portal에서 Text Analytics 리소스로 이동하여 *Cognitive Services* 종류의 리소스를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![질문 응답 리소스 목록의 스크린샷](../../../qnamaker/media/qnamaker-how-to-setup-service/resources-created-question-answering.png)

2. **키 및 엔드포인트** 로 이동합니다.

    > [!div class="mx-imgBorder"]
    > ![구독 키의 스크린샷](../../../qnamaker/media/qnamaker-how-to-key-management/custom-qna-keys-and-endpoint.png)

### <a name="management-service-region"></a>관리 서비스 지역

사용자 지정 질문 응답에서 관리 및 예측 서비스는 모두 동일한 지역에 공동 배치 됩니다.

## <a name="resource-purposes"></a>리소스 용도

사용자 지정 질문 응답 기능을 사용 하 여 만든 각 Azure 리소스는 특정 목적이 있습니다.

* 리소스를 평가 하는 상황에 따라 언어 리소스 (Text Analytics 리소스가 라고도 함)
* Cognitive Search 리소스

### <a name="language-resource"></a>언어 리소스

사용자 지정 질문 응답 기능이 있는 언어 리소스는 작성 및 게시 Api에 대 한 액세스를 제공 하 고, 순위 런타임을 호스팅하고, 원격 분석을 제공 합니다.

### <a name="azure-cognitive-search-resource"></a>Azure Cognitive Search 리소스

[Cognitive Search](../../../../search/index.yml) 리소스는 다음 용도로 사용됩니다.

* 질문 및 답변 쌍 저장
* 런타임에 질문 및 답변 쌍의 초기 순위 (ranker #1)를 제공 합니다.

#### <a name="index-usage"></a>인덱스 사용

단일 언어의 N-1 기술 자료를 게시 하거나 특정 계층에 다른 언어의 N/2 기술 자료를 게시할 수 있습니다. 여기서 N은 Azure Cognitive Search 계층에서 허용 되는 최대 인덱스 수입니다. 또한 계층당 허용되는 문서의 최대 크기 및 개수를 확인합니다.

예를 들어 계층에 허용 되는 인덱스가 15 인 경우 동일한 언어의 14 개 기술 자료를 게시할 수 있습니다 (게시 된 기술 자료 당 하나의 인덱스). 15 번째 인덱스는 작성 및 테스트를 위한 모든 기술 자료에 사용 됩니다. 다른 언어로 기술 자료를 선택 하는 경우 7 개의 기술 자료만 게시할 수 있습니다.

#### <a name="language-usage"></a>언어 사용

사용자 지정 질문에 대 한 답변을 통해 기술 자료에 대 한 서비스를 단일 언어나 여러 언어로 설정할 수 있습니다. 언어 리소스에 첫 번째 기술 자료를 만드는 동안이 옵션을 선택 합니다.

## <a name="next-steps"></a>다음 단계

* 질문에 대 한 답변 [기반](../How-To/manage-knowledge-base.md) 질문에 대해 알아보기
