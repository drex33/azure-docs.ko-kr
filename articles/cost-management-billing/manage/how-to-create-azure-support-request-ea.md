---
title: 기업계약 문제에 대한 Azure 지원 요청을 만드는 방법
description: 지원이 필요한 기업계약 고객은 Azure Portal 사용하여 셀프 서비스 솔루션을 찾고 지원 요청을 만들고 관리할 수 있습니다.
ms.topic: troubleshooting
ms.date: 10/06/2021
ms.author: banders
author: bandersmsft
ms.reviewer: sapnakeshari
ms.service: cost-management-billing
ms.subservice: billing
ms.openlocfilehash: 3563851d6942ea292b82fb6c1db1c597db7fdadd
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129622409"
---
# <a name="create-an-azure-support-request-for-an-enterprise-agreement-issue"></a>기업계약 문제에 대한 Azure 지원 요청 만들기

Azure를 사용하면 Enterprise 계약에 대한 지원 요청(지원 티켓이라고도 함)을 만들고 관리할 수 있습니다. 이 문서에서 설명하는 것처럼 [Azure Portal](https://portal.azure.com)에서 요청을 만들고 관리할 수 있습니다. [Azure 지원 티켓 REST API](/rest/api/support) 또는 [Azure CLI](/cli/azure/azure-cli-support-request)를 사용하여 프로그래밍 방식으로 요청을 만들고 관리할 수도 있습니다.

> [!NOTE]
> Azure Portal URL은 조직이 배포되는 Azure 클라우드에 특정합니다.
>
>- 상업적 사용을 위한 Azure Portal: [https://portal.azure.com](https://portal.azure.com)
>- 독일 Azure Portal: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>- 미국 정부에 대한 Azure Portal: [https://portal.azure.us](https://portal.azure.us)

Azure에서는 청구, 할당량 조정, 계정 이전 등을 비롯한 구독 관리에 대해서는 무제한 지원을 제공합니다. 기술 지원을 위한 지원 계획이 필요합니다. 자세한 내용은 [지원 플랜 비교](https://azure.microsoft.com/support/plans)를 참조하세요.

## <a name="getting-started"></a>시작

Azure Portal에서 **도움말 + 지원** 으로 이동할 수 있습니다. Azure Portal 메뉴, 글로벌 헤더 또는 서비스의 리소스 메뉴에서 사용할 수 있습니다. 지원 요청을 제기하려면 먼저 적절한 권한이 있어야 합니다.

### <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어

기업계약 대한 지원 요청을 만들려면 엔터프라이즈 등록과 연결된 Enterprise 관리자 또는 파트너 관리자여야 합니다. 

### <a name="go-to-help--support-from-the-global-header"></a>글로벌 헤더에서 도움말 + 지원으로 이동합니다.

Azure Portal의 어디에서나 지원 요청을 시작하려면 다음을 수행합니다.

1. 전역 헤더에서 물음표 기호를 선택한 다음, **도움말 + 지원을** 선택합니다.

   :::image type="content" source="media/how-to-create-azure-support-request-ea/help-support-new-lower.png" alt-text="Azure Portal 도움말 메뉴의 스크린샷.":::

1. **지원 요청 만들기** 를 선택합니다. 프롬프트에 따라 문제에 대한 정보를 제공합니다. 몇 가지 가능한 솔루션을 제안하고, 문제에 대한 세부 정보를 수집하며, 지원 요청을 제출 및 추적할 수 있게 지원합니다.

   :::image type="content" source="media/how-to-create-azure-support-request-ea/new-support-request-2-lower.png" alt-text="지원 요청 만들기 링크가 있는 도움말 + 지원 페이지의 스크린샷.":::

### <a name="go-to-help--support-from-a-resource-menu"></a>리소스 메뉴에서 도움말 + 지원으로 이동

지원 요청을 시작하려면 다음을 수행합니다.

1. 리소스 메뉴의 지원 **+ 문제 해결** 섹션에서 **새 지원 요청을** 선택합니다.

   :::image type="content" source="media/how-to-create-azure-support-request-ea/in-context-2-lower.png" alt-text="리소스 창의 새 지원 요청 옵션 스크린샷.":::

1. 메시지에 따라 발생한 문제에 대한 정보를 제공합니다. 리소스에서 지원 요청 프로세스를 시작하면 일부 옵션이 미리 선택됩니다.

## <a name="create-a-support-request"></a>지원 요청 만들기

문제에 대한 정보를 수집하고 해결하는 데 도움이 되는 몇 가지 단계를 안내합니다. 각각의 단계는 다음 섹션에 설명되어 있습니다.

### <a name="problem-description"></a>문제 설명

1. 문제에 대한 요약을 입력한 **다음, 문제 유형** 을 선택합니다. 
1. 문제 **유형** 목록에서 **등록 관리** 를 선택합니다.  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-issue-type-enrollment-administration.png" alt-text="등록 관리 선택을 보여주는 스크린샷." lightbox="./media/how-to-create-azure-support-request-ea/select-issue-type-enrollment-administration.png" :::
1. **등록 번호** 에 대해 등록 번호를 선택합니다. 
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-enrollment.png" alt-text="등록 번호 선택을 보여주는 스크린샷." :::
1. **문제 유형에** 대해 현재 문제 유형을 가장 잘 설명하는 문제 범주를 선택합니다.  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-problem-type.png" alt-text="문제 유형 선택을 보여주는 스크린샷" :::
1. **문제 하위 유형** 에 대해 문제 하위 범주를 선택합니다. 

이러한 세부 정보를 모두 제공한 후 **다음: 솔루션 을 선택합니다.**

### <a name="recommended-solution"></a>권장된 솔루션

제공한 정보에 따라 문제를 해결하는 데 사용할 수 있는 권장 솔루션을 보여 드리겠습니다. 경우에 따라 빠른 진단도 실행할 수 있습니다. 이러한 솔루션은 Azure 엔지니어가 작성하며 가장 일반적인 문제를 해결합니다.

그래도 문제를 해결할 수 없는 경우 **다음: 세부 정보** 를 선택하여 지원 요청을 계속 만듭니다.

### <a name="additional-details"></a>추가 정보

다음으로, 문제에 대한 자세한 정보를 수집합니다. 이 단계에서 철저하고 자세한 정보를 제공하면 지원 요청을 올바른 엔지니어에게 전달하는 데 도움이 됩니다.

1. 세부 정보 탭에서 **문제에** 대한 자세한 정보를 확인할 수 있도록 문제 세부 정보 섹션을 완료합니다. 가능하면 문제가 시작된 시기와 문제를 재현 하는 단계를 알려 주세요. 로그 파일 또는 진단의 출력과 같은 파일을 업로드할 수 있습니다. 파일 업로드에 대한 자세한 내용은 [파일 업로드 지침](../../azure-portal/supportability/how-to-manage-azure-support-request.md#file-upload-guidelines)을 참조하세요.

1. **진단 정보 공유** 섹션에서 **예** 또는 **아니요** 를 선택합니다. **예** 를 사용하면 Azure 지원이 사용자의 Azure 리소스에서 [진단 정보](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/)를 수집할 수 있습니다. 이 정보를 공유하지 않으려는 경우 **아니요** 를 선택합니다. 경우에 따라 더 많은 옵션을 선택할 수 있습니다.

1. 지원 **방법** 섹션에서 문제의 심각도를 선택합니다. 최대 심각도 수준은 [지원 플랜](https://azure.microsoft.com/support/plans)에 따라 다릅니다.

1. 기본 연락 방법, 가용성, 기본 지원 언어를 제공합니다.

1. 다음으로 사용자에게 연락하는 방법을 알 수 있게 **연락처 정보** 섹션을 입력합니다.  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/details-tab.png" alt-text="세부 정보 탭을 보여주는 스크린샷." lightbox="./media/how-to-create-azure-support-request-ea/details-tab.png" :::

필요한 모든 정보를 완료하면 **다음: 검토 + 만들기를** 선택합니다.

### <a name="review--create"></a>검토 + 만들기

요청을 만들기 전에 지원으로 보낼 모든 세부 정보를 검토합니다. 변경해야 하는 경우 **이전을** 선택하여 탭으로 돌아갈 수 있습니다. 지원 요청이 완료되면 **만들기** 를 선택합니다.

지원 엔지니어가 귀하가 지정한 방법으로 연락할 것입니다. 초기 응답 시간에 대한 정보는 [지원 범위 및 응답성](https://azure.microsoft.com/support/plans/response/)을 참조하세요.

## <a name="cant-create-request-with-microsoft-account"></a>Microsoft 계정으로 요청을 만들 수 없습니다.

MSA(Microsoft 계정)가 있는 경우 Azure 지원 티켓을 만들 수 없습니다. Microsoft 계정은 Outlook, Windows Live 및 Hotmail을 포함한 서비스에 대해 만들어집니다.

Azure 지원 티켓을 만들려면 *조직 계정에* EA 관리자 역할이 있어야 합니다.

MSA가 있는 경우 관리자가 조직 계정을 만들게 합니다. 그런 다음 엔터프라이즈 관리자 또는 파트너 관리자는 조직 계정을 엔터프라이즈 관리자 또는 파트너 관리자로 추가해야 합니다. 그런 다음, 조직 계정을 사용하여 지원 요청을 제출할 수 있습니다.

- Enterprise 관리자를 추가하려면 다른 [엔터프라이즈 관리자 만들기를 참조하세요.](ea-portal-administration.md#create-another-enterprise-administrator)
- 파트너 관리자를 추가하려면 [파트너 관리자 관리를 참조하세요.](ea-partner-portal-administration.md#manage-partner-administrators)

## <a name="next-steps"></a>다음 단계

자세한 내용을 보려면 다음 링크를 따라가세요.

* [Azure 지원 요청을 관리하는 방법.]() /.. /azure-portal/supportability/how-to-manage-azure-support-request.md)
* [Azure 지원 티켓 REST API](/rest/api/support)
* [Twitter](https://twitter.com/azuresupport)에서 연계
* [Microsoft Q&A 질문 페이지](/answers/products/azure)에서 동료들의 도움 받기
* [Azure 지원 FAQ](https://azure.microsoft.com/support/faq)에서 자세히 알아보기