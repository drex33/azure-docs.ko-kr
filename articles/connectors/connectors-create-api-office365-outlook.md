---
title: Office 365 Outlook 연결
description: Azure Logic Apps를 사용하여 Office 365 Outlook에서 이메일, 연락처 및 일정을 관리하는 작업 및 워크플로를 자동화합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: article
ms.date: 08/11/2021
tags: connectors
ms.openlocfilehash: b60565cec180242535402daee27ed5018505727a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567078"
---
# <a name="connect-to-office-365-outlook-using-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Office 365 Outlook에 연결

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [Office 365 Outlook 커넥터](/connectors/office365connector/)를 사용하여 논리 앱을 빌드하여 회사 또는 학교 계정을 관리하는 자동화된 작업 및 워크플로를 만들 수 있습니다. 예를 들어 다음과 같은 작업을 자동화할 수 있습니다.

* 이메일을 가져오고 보내고 회신합니다.
* 일정에서 모임을 예약합니다.
* 연락처를 추가하고 편집합니다.

트리거를 사용하여 예를 들어 새 이메일이 도착하는 경우, 일정 항목이 업데이트되는 경우 또는 Salesforce와 같은 다른 서비스에서 이벤트가 발생하는 경우 워크플로를 시작할 수 있습니다. 트리거 이벤트에 응답하는 동작(예: 이메일 보내기 또는 새 일정 이벤트 만들기)을 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* [직장 또는 학교 계정](https://support.microsoft.com/office/what-account-to-use-with-office-and-you-need-one-914e6610-2763-47ac-ab36-602a81068235#bkmk_msavsworkschool)으로 로그인하는 Outlook용 Microsoft Office 365 계정.

  Outlook 계정에 액세스할 수 있는 권한을 워크플로에 부여할 수 있도록 이러한 자격 증명이 필요합니다.

  > [!NOTE]
  > @outlook.com 또는 @hotmail.com 계정이 있는 경우 [Outlook.com 커넥터](../connectors/connectors-create-api-outlook.md)를 사용합니다. 다른 사용자 계정(예: 서비스 계정)을 사용하여 Outlook에 연결하려면 [다른 계정을 사용하여 연결](#connect-using-other-accounts)을 참조하세요.
  >
  > [21Vianet에서 운영하는 Microsoft Azure](https://portal.azure.cn)를 사용하는 경우 Azure AD(Azure Active Directory) 인증은 .com 계정이 아닌 21Vianet(.cn)에서 운영하는 Microsoft Office 365 계정에서만 작동합니다.

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* Outlook.com 계정에 액세스하려는 논리 앱. Office 365 Outlook 트리거를 사용하여 워크플로를 시작하려면 [빈 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)이 있어야 합니다. 워크플로에 Office 365 Outlook 작업을 추가하려면 논리 앱 워크플로에 이미 트리거가 있어야 합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에 설명된 트리거, 작업 및 제한을 비롯하여 이 커넥터에 대한 자세한 기술 정보는 [커넥터의 참조 페이지](/connectors/office365/)에서 확인할 수 있습니다.

## <a name="add-a-trigger"></a>트리거 추가

[트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)는 논리 앱에서 워크플로를 시작하는 이벤트입니다. 이 예제 논리 앱은 지정된 간격 및 빈도에 따라 이메일 계정에서 업데이트된 일정 이벤트를 확인하는 '폴링' 트리거를 사용합니다.

1. [Azure Portal](https://portal.azure.com)의 비주얼 디자이너에서 빈 논리 앱을 엽니다.

1. 검색 상자에서 필터로 `office 365 outlook`을 입력합니다. 이 예제에서는 **예정된 이벤트가 곧 시작되는 경우** 를 선택합니다.

   ![트리거를 선택하여 논리 앱 시작](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Outlook 계정에 대한 활성 연결이 없는 경우 계정에 로그인하여 해당 연결을 만들라는 메시지가 표시됩니다. 다른 사용자 계정(예: 서비스 계정)을 사용하여 Outlook에 연결하려면 [다른 계정을 사용하여 연결](#connect-using-other-accounts)을 참조하세요. 그렇지 않으면 트리거의 속성에 대한 정보를 제공합니다.

   > [!NOTE]
   > 로그인 자격 증명을 변경하더라도 해지될 때까지는 연결이 만료되지 않습니다. 자세한 내용은 [Azure Active Directory에서 구성 가능한 토큰 수명](../active-directory/develop/active-directory-configurable-token-lifetimes.md)을 참조하세요.

   이 예제에서는 트리거가 확인하는 일정을 선택합니다. 아래 예를 참고하세요.

   ![트리거의 속성을 구성합니다.](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. 트리거에서 **빈도** 및 **간격** 값을 설정합니다. **표준 시간대** 와 같은 사용 가능한 다른 트리거 속성을 추가하려면 **새 매개 변수 추가** 목록에서 해당 속성을 선택합니다.

   예를 들어 15분마다 폴링을 트리거하려면 **빈도** 를 **분** 으로, **간격** 을 `15`로 설정합니다.

   ![트리거에 대한 빈도 및 간격 설정](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. 디자이너 도구 모음에서 **저장** 을 선택합니다.

이제 트리거가 발생한 후 실행되는 작업을 추가합니다. 예를 들어 일정 이벤트가 15분 내에 시작될 때 문자 메시지를 전송하는 Twilio **메시지 보내기** 작업을 추가할 수 있습니다.

## <a name="add-an-action"></a>작업 추가

[작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 논리 앱의 워크플로에 의해 수행되는 작업입니다. 이 예제 논리 앱은 Office 365 Outlook에서 새 연락처를 만듭니다. 다른 트리거 또는 작업의 출력을 사용하여 연락처를 만들 수 있습니다. 예를 들어, 논리 앱에서 Salesforce 트리거 **레코드가 만들어지는 경우** 를 사용한다고 가정합니다. Office 365 Outlook **연락처 만들기** 작업을 추가하고 트리거의 출력을 사용하여 새 연락처를 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 비주얼 디자이너에서 논리 앱을 엽니다.

1. 워크플로의 마지막 단계로 작업을 추가하려면 **새 단계** 를 선택합니다.

   단계 사이에 작업을 추가하려면 해당 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시되는 더하기 기호( **+** )를 선택한 다음, **작업 추가** 를 선택합니다.

1. 검색 상자에서 필터로 `office 365 outlook`을 입력합니다. 이 예제에서는 **연락처 만들기** 를 선택합니다.

   ![논리 앱에서 실행할 작업을 선택합니다.](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Outlook 계정에 대한 활성 연결이 없는 경우 계정에 로그인하여 해당 연결을 만들라는 메시지가 표시됩니다. 다른 사용자 계정(예: 서비스 계정)을 사용하여 Outlook에 연결하려면 [다른 계정을 사용하여 연결](#connect-using-other-accounts)을 참조하세요. 그렇지 않으면 작업의 속성에 대한 정보를 제공합니다.

   > [!NOTE]
   > 로그인 자격 증명을 변경하더라도 해지될 때까지는 연결이 만료되지 않습니다. 자세한 내용은 [Azure Active Directory에서 구성 가능한 토큰 수명](../active-directory/develop/active-directory-configurable-token-lifetimes.md)을 참조하세요.

   이 예제에서는 작업이 새 연락처를 만드는 연락처 폴더를 선택합니다. 예:

   ![작업 속성 구성](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   사용 가능한 다른 작업 속성을 추가하려면 **새 매개 변수 추가** 목록에서 속성을 선택합니다.

1. 디자이너 도구 모음에서 **저장** 을 선택합니다.

<a name="connect-using-other-accounts"></a>

## <a name="connect-using-other-accounts"></a>다른 계정을 사용하여 연결

현재 Azure에 로그인한 계정이 아닌 다른 계정을 사용하여 Outlook에 연결을 시도하는 경우 [SSO(Single Sign-On)](../active-directory/manage-apps/what-is-single-sign-on.md) 오류가 발생할 수 있습니다. 이 문제는 한 계정으로 Azure Portal에 로그인했지만 다른 계정을 사용하여 연결을 만드는 경우에 발생합니다. 디자이너에서는 Azure Portal에 로그인된 계정을 사용할 것으로 예상합니다. 이 문제를 해결하려면 다음 옵션을 선택합니다.

* 논리 앱의 리소스 그룹에서 **참가자** 역할을 사용하여 다른 계정을 설정합니다.

  1. 논리 앱의 리소스 그룹 메뉴에서 **액세스 제어(IAM)** 를 선택합니다. **참가자** 역할을 사용하여 다른 계정을 설정합니다. 
  
     자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

  1. 이 역할을 설정한 후에는 현재 참가자 권한이 있는 계정으로 Azure Portal에 로그인합니다. 이제 이 계정을 사용하여 Outlook에 대한 연결을 만들 수 있습니다.

* 회사 또는 학교 계정이 '다른 이름으로 보내기' 권한을 갖도록 다른 계정을 설정합니다.

   관리자 권한이 있는 경우 서비스 계정의 사서함에서 회사 또는 학교 계정에 **다른 이름으로 보내기** 또는 **대신 보내기** 권한을 설정합니다. 자세한 내용은 [다른 사용자에게 사서함 사용 권한 부여 - 관리자 도움말](/microsoft-365/admin/add-users/give-mailbox-permissions-to-another-user)을 참조하세요. 그런 다음 회사 또는 학교 계정을 사용하여 연결을 만들 수 있습니다. 이제 보낸 사람을 지정할 수 있는 트리거 또는 작업에서 서비스 계정의 이메일 주소를 사용할 수 있습니다.

   예를 들어 **이메일 보내기** 작업에는 선택적 매개 변수인 **보낸 사람(다른 이름으로 보내기)** 이 있습니다. 이 매개 변수를 작업에 추가하여 서비스 계정의 이메일 주소를 보낸 사람으로 사용할 수 있습니다. 이 매개 변수를 추가하려면 다음 단계를 수행합니다.

   1. **이메일 보내기** 작업에서 **매개 변수 추가** 목록을 열고 **보낸 사람(다른 사람으로 보내기)** 매개 변수를 선택합니다.

   1. 매개 변수가 작업에 표시되면 서비스 계정의 이메일 주소를 입력합니다.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
