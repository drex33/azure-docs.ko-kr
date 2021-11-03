---
title: QnA Maker 서비스 설정 - QnA Maker
description: QnA Maker 기술 자료를 만들려면 먼저 Azure에서 QnA Maker 서비스를 설정해야 합니다. 구독에 새 리소스를 만들 수 있는 권한이 있으면 누구든지 QnA Maker 서비스를 설정할 수 있습니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/14/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ce152375289cd48681a87775ef534f5b9e6932de
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071366"
---
# <a name="manage-qna-maker-resources"></a>QnA Maker 리소스 관리

QnA Maker 기술 자료를 만들려면 먼저 Azure에서 QnA Maker 서비스를 설정해야 합니다. 구독에 새 리소스를 만들 수 있는 권한이 있으면 누구든지 QnA Maker 서비스를 설정할 수 있습니다. 사용자 지정 질문 답변 기능을 사용하려는 경우 Text Analytics 리소스를 만들고 사용자 지정 질문 답변 기능을 추가해야 합니다.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

리소스를 만들기 전에 다음 개념을 확실히 이해하는 것이 유용합니다.

* [QnA Maker 리소스](../Concepts/azure-resources.md)
* [키 작성 및 게시](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>새 QnA Maker 서비스 만들기

이 절차는 기술 자료 콘텐츠를 관리하는 데 필요한 Azure 리소스를 만듭니다. 이 단계를 완료한 후에는 Azure Portal의 리소스에 대한 **키** 페이지에서 _구독_ 키를 찾을 수 있습니다.

1. Azure Portal에 로그인하고 [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) 리소스를 만듭니다.

1. 사용 약관을 읽은 후 **만들기** 를 선택합니다.

    ![새 QnA Maker 서비스 만들기](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. **QnA Maker** 에서 적절한 계층과 지역을 선택합니다.

    ![새 QnA Maker 서비스 만들기 - 가격 책정 계층 및 지역](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * **이름** 필드에 이 QnA Maker 서비스를 고유하게 식별할 수 있는 이름을 입력합니다. 이 이름은 기술 자료가 연결되는 QnA Maker 엔드포인트도 식별합니다.
    * QnA Maker 리소스가 배포되는 **구독** 을 선택합니다.
    * QnA Maker 관리 서비스(포털 및 관리 API)에 대한 **가격 책정 계층** 을 선택합니다. [SKU 가격 책정에 대한 자세한 내용](https://aka.ms/qnamaker-pricing)을 참조하세요.
    * 새 **리소스 그룹** 을 만들거나(권장), QnA Maker 리소스를 배포할 기존 리소스 그룹을 사용합니다. QnA Maker는 여러 Azure 리소스를 만듭니다. 이러한 리소스를 보유하는 리소스 그룹을 만들 때 리소스 그룹 이름으로 이러한 리소스를 쉽게 찾고, 관리하고, 삭제할 수 있습니다.
    * **리소스 그룹 위치** 를 선택합니다.
    * Azure Cognitive Search 서비스의 **검색 가격 책정 계층** 을 선택합니다. 무료 계층 옵션을 사용할 수 없는 경우(흐리게 표시됨) 이미 무료 서비스가 구독을 통해 배포되었음을 의미합니다. 이 경우에는 기본 계층으로 시작해야 합니다. [Azure Cognitive Search 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/search/)를 참조하세요.
    * Azure Cognitive Search 인덱스를 배포할 **검색 위치** 를 선택합니다. 고객 데이터를 저장해야 하는 위치의 제한은 Azure Cognitive Search에 대한 위치 선택 결정에 도움이 됩니다.
    * **앱 이름** 필드에 Azure App Service 인스턴스의 이름을 입력합니다.
    * 기본적으로 App Service의 기본값은 표준(S1) 계층입니다. 계획을 만든 후에 변경할 수 있습니다. [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/)에 대해 자세히 알아보세요.
    * App Service를 배포할 **웹 사이트 위치** 를 선택합니다.

        > [!NOTE]
        > **검색 위치** 가 **웹 사이트 위치** 와 다를 수 있습니다.

    * **Application Insights** 를 사용할지 여부를 선택합니다. **Application Insights** 를 사용하도록 설정하면 QnA Maker는 트래픽, 채팅 로그 및 오류에 대한 원격 분석을 수집합니다.
    * Application Insights 리소스를 배포할 **App Insights 위치** 를 선택합니다.
    * 비용 절감 측정값의 경우, QnA Maker용으로 생성된 일부(전체는 아님) Azure 리소스를 [공유](configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource)할 수 있습니다.

1. 모든 필드 유효성 검사가 완료된 후 **만들기** 를 선택합니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다.

1. 배포가 완료되면 구독에 다음 리소스가 생성됩니다.

   ![QnA Maker 서비스를 새로 만든 리소스](../media/qnamaker-how-to-setup-service/resources-created.png)

    _Cognitive Services_ 형식의 리소스에는 _구독_ 키가 있습니다.

## <a name="upgrade-azure-resources"></a>Azure 리소스 업그레이드

### <a name="upgrade-qna-maker-sku"></a>QnA Maker SKU 업그레이드

기술 자료에서 현재 계층을 넘어서는 더 많은 질문과 답변이 필요한 경우 QnA Maker 서비스 가격 책정 계층을 업그레이드하세요.

QnA Maker 관리 SKU를 업그레이드하려면

1. Azure Portal의 QnA Maker 리소스로 이동한 다음, **가격 책정 계층** 을 선택합니다.

    ![QnA Maker 리소스](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. 적절한 SKU를 선택하고 **선택** 을 누릅니다.

    ![QnA Maker 가격 책정](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)
    
### <a name="upgrade-app-service"></a>App Service 업그레이드

기술 자료에서 클라이언트 앱에서 더 많은 요청을 처리해야 하는 경우 앱 서비스 가격 책정 계층을 업그레이드하세요.

App Service를 [스케일 업](../../../app-service/manage-scale-up.md) 또는 스케일 아웃할 수 있습니다.

Azure Portal의 App Service 리소스로 이동한 다음, 필요에 따라 **스케일 업** 또는 **스케일 아웃** 옵션을 선택합니다.

![QnA Maker 앱 서비스 크기 조정](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Azure Cognitive Search 서비스 업그레이드

많은 기술 자료를 포함할 계획인 경우 Azure Cognitive Search 서비스 가격 책정 계층을 업그레이드하세요.

현재 Azure Search SKU의 현재 위치 업그레이드를 수행할 수 없습니다. 그러나 원하는 SKU로 새 Azure Search 리소스를 만들고, 데이터를 새 리소스로 복원한 다음, QnA Maker 스택에 연결할 수 있습니다. 이렇게 하려면 다음 단계를 수행하세요.

1. Azure Portal에서 새 Azure Search 리소스를 만들고 원하는 SKU를 선택합니다.

    ![QnA Maker Azure Search 리소스](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 원래 Azure Search 리소스의 인덱스를 새 리소스로 복원합니다. [백업 복원 샘플 코드](https://github.com/pchoudhari/QnAMakerBackupRestore)를 참조하세요.

1. 데이터가 복원된 후 새 Azure Search 리소스로 이동한 다음, **키** 를 선택하고 **이름** 및 **관리자 키** 를 적어 둡니다.

    ![QnA Maker Azure Search 키](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. 새 Azure Search 리소스를 QnA Maker 스택에 연결하려면 QnA Maker App Service 인스턴스로 이동합니다.

    ![QnA Maker App Service 인스턴스](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. **애플리케이션 설정** 을 선택하고 3단계의 **AzureSearchName** 및 **AzureSearchAdminKey** 필드의 설정을 수정합니다.

    ![QnA Maker App Service 설정](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. App Service 인스턴스를 다시 시작합니다.

    ![QnA Maker App Service 인스턴스 다시 시작](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)
    
### <a name="inactivity-policy-for-free-search-resources"></a>무료 Search 리소스에 대한 비활성 정책

QnA Maker 리소스를 사용하지 않는 경우 모든 리소스를 제거해야 합니다. 사용하지 않는 리소스를 제거하지 않으면 무료 Search 리소스를 만든 경우 기술 자료가 작동하지 않습니다.

무료 Search 리소스는 API 호출을 받지 않고 90일이 지나면 삭제됩니다.

## <a name="delete-azure-resources"></a>Azure 리소스 삭제

기술 자료에 사용되는 Azure 리소스를 삭제하는 경우 기술 자료는 더 이상 작동하지 않습니다. 모든 리소스를 삭제하기 전에 **설정** 페이지에서 기술 자료를 내보냈는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[App Service](../../../app-service/index.yml) 및 [Search Service](../../../search/index.yml)에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [다른 작성 방법 알아보기](../index.yml)
