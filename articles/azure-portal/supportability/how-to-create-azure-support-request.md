---
title: Azure 지원 요청을 만드는 방법
description: 지원이 필요한 고객은 Azure Portal을 사용하여 셀프 서비스 솔루션을 찾고, 지원 요청을 만들고 관리할 수 있습니다.
ms.topic: how-to
ms.custom: support-help-page
ms.date: 09/01/2021
ms.openlocfilehash: 768c778926024288b0d331b5cb6b60c8e9c16a57
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429464"
---
# <a name="create-an-azure-support-request"></a>Azure 지원 요청 만들기

Azure를 통해 지원 티켓이라고도 하는 지원 요청을 만들고 관리할 수 있습니다. 이 문서에서 설명하는 것처럼 [Azure Portal](https://portal.azure.com)에서 요청을 만들고 관리할 수 있습니다. [Azure 지원 티켓 REST API](/rest/api/support) 또는 [Azure CLI](/cli/azure/azure-cli-support-request)를 사용하여 프로그래밍 방식으로 요청을 만들고 관리할 수도 있습니다.

> [!NOTE]
> Azure Portal URL은 조직이 배포되는 Azure 클라우드에 특정합니다.
>
>* 상업적 사용을 위한 Azure Portal: [https://portal.azure.com](https://portal.azure.com)
>* 독일 Azure Portal: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* 미국 정부에 대한 Azure Portal: [https://portal.azure.us](https://portal.azure.us)

Azure에서는 청구, 할당량 조정, 계정 이전 등을 비롯한 구독 관리에 대해서는 무제한 지원을 제공합니다. 기술 지원의 경우 지원 계획이 필요합니다. 자세한 내용은 [지원 플랜 비교](https://azure.microsoft.com/support/plans)를 참조하세요.

## <a name="getting-started"></a>시작

Azure Portal에서 **도움말 + 지원** 으로 이동할 수 있습니다. Azure Portal 메뉴, 글로벌 헤더 또는 서비스의 리소스 메뉴에서 사용할 수 있습니다. 지원 요청을 제기하려면 먼저 적절한 권한이 있어야 합니다.

### <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어

지원 요청을 만들려면 구독 수준에서 [소유자](../../role-based-access-control/built-in-roles.md#owner), [기여자](../../role-based-access-control/built-in-roles.md#contributor) 또는 [지원 요청 기여자](../../role-based-access-control/built-in-roles.md#support-request-contributor) 역할에 할당되어야 합니다. Azure Active Directory 시나리오와 같이 구독 없이 지원 요청을 만들려면 [관리자](../../active-directory/roles/permissions-reference.md)여야 합니다.

### <a name="go-to-help--support-from-the-global-header"></a>글로벌 헤더에서 도움말 + 지원으로 이동합니다.

Azure Portal의 어디에서나 지원 요청을 시작하려면 다음을 수행합니다.

1. 글로벌 헤더에서 **?** 를 선택한 다음 **도움말 + 지원** 을 선택합니다.

   :::image type="content" source="media/how-to-create-azure-support-request/helpandsupportnewlower.png" alt-text="Azure Portal 도움말 메뉴의 스크린샷":::

1. **지원 요청 만들기** 를 선택합니다. 프롬프트에 따라 문제에 대한 정보를 제공합니다. 몇 가지 가능한 솔루션을 제안하고, 문제에 대한 세부 정보를 수집하며, 지원 요청을 제출 및 추적할 수 있게 지원합니다.

   :::image type="content" source="media/how-to-create-azure-support-request/newsupportrequest2lower.png" alt-text="지원 요청 만들기 링크를 사용 하 여 도움말 + 지원 페이지의 스크린샷":::

### <a name="go-to-help--support-from-a-resource-menu"></a>리소스 메뉴에서 도움말 + 지원으로 이동

리소스의 컨텍스트에서 지원 요청을 시작하기 위해 현재 다음 작업을 수행하고 있습니다.

1. 리소스 메뉴의 **지원 + 문제 해결** 섹션에서 **새 지원 요청** 을 선택 합니다.

   :::image type="content" source="media/how-to-create-azure-support-request/incontext2lower.png" alt-text="리소스 창에 있는 새 지원 요청 옵션의 스크린샷":::

1. 메시지에 따라 발생한 문제에 대한 정보를 제공합니다. 리소스에서 지원 요청 프로세스를 시작 하면 일부 옵션이 미리 선택 됩니다.

## <a name="create-a-support-request"></a>지원 요청 만들기

문제에 대한 정보를 수집하고 해결하는 데 도움이 되는 몇 가지 단계를 안내합니다. 각각의 단계는 다음 섹션에 설명되어 있습니다.

### <a name="problem-description"></a>문제 설명

지원 요청 프로세스의 첫 번째 단계는 문제 유형을 선택 하는 것입니다. 그러면 선택한 문제 유형에 따라 달라질 수 있는 자세한 정보를 묻는 메시지가 표시 됩니다. 대부분의 경우 구독을 지정 하 고, 문제를 간략하게 설명 하 고, 문제 유형을 선택 해야 합니다. **기술** 을 선택 하는 경우 문제가 관련 된 서비스를 지정 해야 합니다. 서비스에 따라 **문제 유형** 및 **문제 하위 유형에** 대 한 추가 옵션이 표시 됩니다.

:::image type="content" source="media/how-to-create-azure-support-request/basics2lower.png" alt-text="지원 요청 프로세스의 문제 설명 단계 스크린샷":::

이러한 세부 정보를 모두 제공 했으면 **다음** 을 선택 합니다.

### <a name="recommended-solution"></a>권장된 솔루션

제공 된 정보에 따라 문제를 해결 하는 데 사용할 수 있는 권장 해결 방법을 보여 드리겠습니다. 경우에 따라 빠른 진단도 실행할 수 있습니다. 이러한 솔루션은 Azure 엔지니어가 작성하며 가장 일반적인 문제를 해결합니다.

그래도 문제를 해결할 수 없는 경우 **다음** 을 선택 하 여 지원 요청을 계속 만듭니다.

### <a name="additional-details"></a>추가 정보

이제 문제에 대한 추가 세부 정보를 수집합니다. 이 단계에서 철저하고 자세한 정보를 제공하면 지원 요청을 올바른 엔지니어에게 전달하는 데 도움이 됩니다.

1. 문제에 대 한 자세한 정보를 제공 하기 위해 **문제 세부** 정보를 완료 합니다. 가능하면 문제가 시작된 시기와 문제를 재현 하는 단계를 알려 주세요. 로그 파일 또는 진단의 출력과 같은 파일을 업로드할 수 있습니다. 파일 업로드에 대한 자세한 내용은 [파일 업로드 지침](how-to-manage-azure-support-request.md#file-upload-guidelines)을 참조하세요.

1. **진단 정보 공유** 섹션에서 **예** 또는 **아니요** 를 선택합니다. **예** 를 사용하면 Azure 지원이 사용자의 Azure 리소스에서 [진단 정보](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/)를 수집할 수 있습니다. 이 정보를 공유하지 않으려는 경우 **아니요** 를 선택합니다. 경우에 따라 가상 머신의 메모리에 대한 액세스를 허용할지 등을 선택할 수 있는 추가 옵션이 있습니다.

1. **지원 방법** 섹션에서 영향의 심각도를 선택 합니다. 최대 심각도 수준은 [지원 플랜](https://azure.microsoft.com/support/plans)에 따라 다릅니다.

1. 기본 연락 방법, 가용성, 기본 지원 언어를 제공합니다.

1. 다음으로 사용자에게 연락하는 방법을 알 수 있게 **연락처 정보** 섹션을 입력합니다.

필요한 모든 정보를 완료 한 경우 **다음** 을 선택 합니다.

### <a name="review--create"></a>검토 + 만들기

요청을 만들기 전에 지원 하기 위해 보낼 모든 세부 정보를 검토 합니다. 변경 해야 하는 경우 **이전** 을 선택 하 여 탭으로 돌아갈 수 있습니다. 지원 요청이 완료되면 **만들기** 를 선택합니다.

지원 엔지니어가 귀하가 지정한 방법으로 연락할 것입니다. 초기 응답 시간에 대한 정보는 [지원 범위 및 응답성](https://azure.microsoft.com/support/plans/response/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure의 자가 지원 옵션에 대한 자세한 정보를 보려면 다음 비디오를 확인하세요.

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

자세한 내용을 보려면 다음 링크를 따라가세요.

* [Azure 지원 요청을 관리하는 방법](how-to-manage-azure-support-request.md)
* [Azure 지원 티켓 REST API](/rest/api/support)
* [Twitter](https://twitter.com/azuresupport)에서 연계
* [Microsoft Q&A 질문 페이지](/answers/products/azure)에서 동료들의 도움 받기
* [Azure 지원 FAQ](https://azure.microsoft.com/support/faq)에서 자세히 알아보기