---
title: 가상 머신 변경 모니터링 - Azure Event Grid 및 Logic Apps
description: Azure Event Grid와 Logic Apps를 사용하여 VM(가상 머신)의 변경을 확인합니다.
services: logic-apps, event-grid
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: estfan, azla
ms.topic: tutorial
ms.date: 07/01/2021
ms.openlocfilehash: b1ad7f8af9233aeb49d38d4af93f43dcd7b04566
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520240"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>자습서: Azure Event Grid 및 Logic Apps를 사용하여 가상 머신 변경 모니터링

Azure 리소스 또는 타사 리소스에서 발생하는 특정 이벤트를 모니터링하고 응답하려면 Azure Logic Apps를 사용하여 최소한의 코드로 자동화된 [논리 앱 워크플로](../logic-apps/logic-apps-overview.md)를 만들면 됩니다. 이러한 리소스에서 [Azure Event Grid](../event-grid/overview.md)에 이벤트를 게시하게 할 수 있습니다. 이에 따라 Event Grid에서는 큐, 웹후크 또는 [이벤트 허브](../event-hubs/event-hubs-about.md)를 엔드포인트로 가지고 있는 구독자에게 해당 이벤트를 보냅니다. 구독자는 이러한 이벤트가 Event Grid에 도착할 때까지 기다렸다가 이벤트를 처리하는 단계를 실행합니다.

예를 들어 게시자가 Azure Event Grid 서비스를 통해 구독자에게 보낼 수 있는 몇 가지 이벤트는 다음과 같습니다.

* 리소스를 만들고, 읽고, 업데이트하거나 삭제합니다. 예를 들어 Azure 구독에 요금이 부과되어 청구서에 영향을 줄 수 있는 변경 사항을 모니터링할 수 있습니다.

* Azure 구독에서 사람을 추가하거나 제거합니다.

* 앱에서 특정 작업을 수행합니다.

* 새 메시지가 큐에 표시됩니다.

이 자습서에서는 [*다중 테넌트* Azure Logic Apps](../logic-apps/logic-apps-overview.md)에서 실행되는 논리 앱 리소스를 만들며 [소비 가격 책정 모델](../logic-apps/logic-apps-pricing.md#consumption-pricing)을 기반으로 합니다. 이 논리 앱 리소스를 사용하여 가상 머신의 변경 내용을 모니터링하고 해당 변경 내용에 대한 이메일을 보내는 워크플로를 만듭니다. Azure 리소스에 대한 이벤트 구독이 있는 논리 앱을 만들면 이벤트가 해당 리소스에서 Event Grid를 거쳐 워크플로로 이동합니다. 다중 테넌트 및 단일 테넌트 Azure Logic Apps에 대한 자세한 내용은 [단일 테넌트 및 다중 테넌트 비교와 통합 서비스 환경](../logic-apps/single-tenant-overview-compare.md)을 검토하세요.

![Azure Event Grid를 사용하여 가상 머신을 모니터링하는 워크플로가 있는 워크플로 디자이너를 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

이 자습서에서는 다음 방법에 관해 알아봅니다.

> [!div class="checklist"]
> * Event Grid에서 이벤트를 모니터링하는 논리 앱 리소스와 워크플로를 만듭니다.
> * 특별히 가상 머신 변경 내용을 확인하는 조건을 추가합니다.
> * 가상 컴퓨터가 변경될 때 전자 메일을 보냅니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 알림을 보내기 위해 Azure Logic Apps에서 작동하는 이메일 공급자(예: Office 365 Outlook, Outlook.com 또는 Gmail)의 이메일 계정. 다른 공급자에 대한 내용은 [여기서 커넥터 목록을 검토하세요](/connectors/).

  이 자습서에서는 Office 365 Outlook 계정을 사용합니다. 다른 이메일 계정을 사용하는 경우 일반적인 단계는 동일하지만 UI가 약간 다르게 표시될 수 있습니다.

  > [!IMPORTANT]
  > Gmail 커넥터를 사용하려는 경우 G Suite 비즈니스 계정만 논리 앱에서 제한 없이 이 커넥터를 사용할 수 있습니다. Gmail 소비자 계정이 있는 경우 특정 Google 승인 서비스에서만 이 커넥터를 사용하거나 [Gmail 커넥터 인증에 사용할 Google 클라이언트 앱을 만들](/connectors/gmail/#authentication-and-bring-your-own-application) 수 있습니다. 자세한 내용은 [Azure Logic Apps의 Google 커넥터에 대한 데이터 보안 및 개인정보처리방침](../connectors/connectors-google-data-security-privacy-policy.md)을 참조하세요.

* 자체 Azure 리소스 그룹에만 있는 [가상 머신](https://azure.microsoft.com/services/virtual-machines). 가상 머신이 아직 없는 경우 [VM 만들기 자습서](../virtual-machines/windows/quick-create-portal.md)를 통해 새로 만듭니다. 가상 머신에서 이벤트를 게시하기 위해 [필요한 별도의 작업은 없습니다](../event-grid/overview.md).

* 특정 IP 주소로 트래픽을 제한하는 방화벽이 있는 경우에는 논리 앱 워크플로를 만드는 Azure 지역의 Azure Logic Apps에서 사용하는 [인바운드](../logic-apps/logic-apps-limits-and-config.md#inbound) 및 [아웃바운드](../logic-apps/logic-apps-limits-and-config.md#outbound) IP 주소 모두에 대한 액세스를 허용하도록 방화벽을 설정합니다.

  이 예제에서는 논리 앱 리소스에 대한 Azure 지역에서 *모든* [관리형 커넥터 아웃바운드 IP 주소](/connectors/common/outbound-ip-addresses)에 대한 액세스를 허용하도록 방화벽을 설정해야 하는 관리형 커넥터를 사용합니다.

## <a name="create-logic-app-resource"></a>논리 앱 리소스 만들기

1. Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

1. Azure 주 메뉴에서 **리소스 만들기** > **통합** > **논리 앱** 을 차례로 선택합니다.

   ![논리 앱 리소스를 만드는 단추를 표시하는 Azure Portal의 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. **논리 앱** 아래에서 논리 앱 리소스에 대한 정보를 제공합니다. 완료되면 **만들기** 를 선택합니다.

   ![이름, 구독, 리소스 그룹 및 위치와 같은 세부 정보를 표시하는 논리 앱 만들기 메뉴의 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **이름** | 예 | <*logic-app-name*> | 논리 앱의 고유한 이름을 제공합니다. |
   | **구독** | 예 | <*Azure-subscription-name*> | 이 자습서의 모든 서비스에 대해 동일한 Azure 구독을 선택합니다. |
   | **리소스 그룹** | 예 | <*Azure-resource-group*> | 이 자습서의 모든 서비스에 대해 선택할 수 있는 논리 앱의 Azure 리소스 그룹 이름입니다. |
   | **위치** | 예 | <*Azure-region*> | 이 자습서의 모든 서비스에 대해 동일한 하위 지역을 선택합니다. |
   |||

1. Azure가 논리 앱을 배포하면 워크플로 디자이너는 소개 비디오 및 많이 사용되는 트리거가 포함된 페이지를 보여줍니다. 비디오 및 트리거를 거꾸로 스크롤합니다.

1. **템플릿** 아래에서 **빈 논리 앱** 을 선택합니다.

   ![빈 논리 앱을 만들기 위한 선택 항목을 표시하는 Logic Apps 템플릿의 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   이제 워크플로 디자이너에는 논리 앱을 시작하는 데 사용할 수 있는 [*트리거*](../logic-apps/logic-apps-overview.md#logic-app-concepts)가 표시됩니다. 모든 논리 앱은 특정 이벤트가 발생하거나 특정 조건이 충족할 때 실행되는 트리거를 통해 시작되어야 합니다. 트리거가 발생할 때마다 Azure Logic Apps는 논리 앱을 실행하는 워크플로 인스턴스를 만듭니다.

## <a name="add-an-event-grid-trigger"></a>Event Grid 트리거 추가

이제 가상 머신의 리소스 그룹을 모니터링하는 데 사용하는 Event Grid 트리거를 추가합니다.

1. 디자이너의 검색 상자에서 `event grid`를 입력합니다. 트리거 목록에서 **리소스 이벤트가 발생하는 경우** 트리거를 선택합니다.

   ![선택한 Event Grid 트리거를 사용하는 워크플로 디자이너를 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. 메시지가 표시되면 Azure 계정 자격 증명을 사용하여 Azure Event Grid에 로그인합니다. Azure 구독과 연결된 Azure Active Directory 테넌트를 보여 주는 **테넌트** 목록에서 올바른 테넌트가 나타나는지 확인합니다. 예를 들면 다음과 같습니다.

   ![Event Grid에 연결하는 Azure 로그인 프롬프트가 있는 워크플로 디자이너를 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > @outlook.com 또는 @hotmail.com과 같은 개인 Microsoft 계정으로 로그인하는 경우 Event Grid 트리거가 제대로 나타나지 않을 수 있습니다. 이 문제를 해결하려면 [서비스 주체와 연결](../active-directory/develop/howto-create-service-principal-portal.md)을 선택하거나 Azure 구독과 연결된 Azure Active Directory의 멤버(예: *user-name*@emailoutlook.onmicrosoft.com)로 인증합니다.

1. 이제 논리 앱을 게시자의 이벤트에 구독합니다. 예를 들어 다음 표에서 설명한 대로 이벤트 구독에 대한 세부 정보를 제공합니다.

   ![트리거를 세부 정보 편집기 창이 열려 있는 워크플로 디자이너를 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | 속성 | 필수 | 값 | Description |
   | -------- | -------- | ----- | ----------- |
   | **구독** | 예 | <*event-publisher-Azure-subscription-name*> | *이벤트 게시자* 와 연결된 Azure 구독의 이름을 선택합니다. 이 자습서에서는 가상 머신에 대한 Azure 구독 이름을 선택합니다. |
   | **리소스 종류** | 예 | <*event-publisher-Azure-resource-type*> | 이벤트 게시자의 Azure 리소스 종류를 선택합니다. Azure 리소스 종류에 대한 자세한 내용은 [Azure 리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요. 이 자습서에서는 Azure 리소스 그룹을 모니터링하는 `Microsoft.Resources.ResourceGroups` 값을 선택합니다. |
   | **리소스 이름** |  예 | <*event-publisher-Azure-resource-name*> | 이벤트 게시자의 Azure 리소스 이름을 선택합니다. 이 목록은 선택한 리소스 종류에 따라 달라집니다. 이 자습서에서는 가상 머신이 포함된 Azure 리소스 그룹의 이름을 선택합니다. |
   | **이벤트 유형 항목** |  예 | <*event-types*> | 필터링하고 Event Grid로 보낼 하나 이상의 특정 이벤트 유형을 선택합니다. 예를 들어, 필요에 따라 다음 이벤트 유형을 추가하여 리소스가 변경 또는 삭제되는 경우를 감지할 수 있습니다. <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>자세한 내용은 다음 항목을 참조하세요. <p><p>- [Azure Event Grid 리소스 그룹에 대한 이벤트 스키마](../event-grid/event-schema-resource-groups.md) <br>- [이벤트 필터링 이해](../event-grid/event-filtering.md) <br>- [Event Grid에 대한 이벤트 필터링](../event-grid/how-to-filter-events.md) |
   | 선택적 속성을 추가하려면 **새 매개 변수 추가** 를 선택한 다음, 원하는 속성을 선택합니다. | 예 | {see descriptions} | * **접두사 필터**: 이 자습서에서는 이 속성을 비워 둡니다. 기본 동작은 모든 값과 일치합니다. 그러나 접두사 문자열(예: 특정 리소스에 대한 경로 및 매개 변수)을 필터로 지정할 수 있습니다. <p>* **접미사 필터**: 이 자습서에서는 이 속성을 비워 둡니다. 기본 동작은 모든 값과 일치합니다. 그러나 특정 파일 형식만 원하는 경우 접미사 문자열(예: 파일 이름 확장명)을 필터로 지정할 수 있습니다. <p>* **구독 이름**: 이 자습서에서는 이벤트 구독에 대한 고유한 이름을 제공할 수 있습니다. |
   |||

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장** 을 선택합니다. 논리 앱에서 작업의 세부 정보를 접어 숨기려면 작업의 제목 표시줄을 선택합니다.

   ![워크플로 디자이너에서 "저장" 단추를 선택한 것을 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Event Grid 트리거가 있는 논리 앱을 저장하면 Azure에서 사용자가 선택한 리소스에 대한 논리 앱의 이벤트 구독을 자동으로 만듭니다. 따라서 리소스가 이벤트를 Event Grid에 게시하면 이 Event Grid는 해당 이벤트를 논리 앱에 자동으로 푸시합니다. 이 이벤트는 다음 단계에서 정의하는 워크플로의 인스턴스를 만들고 실행하는 논리 앱을 트리거합니다.

논리 앱이 현재 라이브 상태이며 Event Grid의 이벤트를 수신 대기하지만, 워크플로에 작업을 추가하기 전까지는 아무 작업도 수행하지 않습니다.

## <a name="add-a-condition"></a>조건 추가

특정 이벤트 또는 작업이 발생할 때만 논리 앱이 실행되도록 하려면 `Microsoft.Compute/virtualMachines/write` 작업을 확인하는 조건을 추가합니다. 이 조건이 true인 경우 논리 앱에서 업데이트된 가상 머신에 대한 세부 정보가 포함된 전자 메일을 보냅니다.

1. 논리 앱 디자이너의 Event Grid 트리거 아래에서 **새 단계** 를 선택합니다.

   ![워크플로 디자이너에서 "새 단계"를 선택한 것을 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. **작업 선택** 아래의 검색 상자에 `condition`을 필터로 입력합니다. 작업 목록에서 **조건** 작업을 선택합니다.

   ![워크플로 디자이너에서 "조건"을 선택한 것을 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   Logic Apps 디자이너에서는 조건이 참인지 거짓인지를 기준으로 따라야 할 작업 경로를 포함하여 워크플로에 조건을 추가합니다.

   ![워크플로에 빈 조건이 추가된 워크플로 디자이너를 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. 조건 제목을 `If a virtual machine in your resource group has changed`로 바꿉니다. 조건의 제목 표시줄에서 줄임표( **...** ) 단추를 선택하고 **이름 바꾸기** 를 선택합니다.

   ![조건 편집기의 바로 가기 메뉴와 "이름 바꾸기"가 선택된 워크플로 디자이너를 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. `operationName` 속성이 `Microsoft.Compute/virtualMachines/write` 작업인 `data` 개체에 대해 `body` 이벤트를 확인하는 조건을 만듭니다. [Event Grid 이벤트 스키마](../event-grid/event-schema.md)에 대해 자세히 알아보세요.

   1. **And** 아래의 첫 번째 행에서 왼쪽 상자 내부를 클릭합니다. 표시되는 동적 콘텐츠 목록에서 **식** 을 선택합니다.

      ![조건 작업 및 동적 콘텐츠 목록이 열려 있고 "식"을 선택한 워크플로 디자이너를 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. 식 편집기에서 트리거의 작업 이름을 반환하는 다음 식을 입력하고 **확인** 을 선택합니다.

      `triggerBody()?['data']['operationName']`

      다음은 그 예입니다.

      ![작업 이름을 추출하는 식을 사용하여 조건 편집기를 보여 주는 Logic Apps 디자이너의 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. 중간 상자에서 **이(가) 다음과 같은 경우** 연산자를 유지합니다.

   1. 오른쪽 상자에 모니터링할 작업을 입력합니다. 이 예의 경우 다음 값을 입력합니다.

      `Microsoft.Compute/virtualMachines/write`

   완성한 조건은 다음 예와 같습니다.

   ![작업을 비교하는 조건이 있는 워크플로 디자이너를 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   다음과 같이 디자인 보기에서 코드 보기로 전환했다가 다시 디자인 보기로 전환할 경우 조건에 지정한 식이 **data.operationName** 토큰으로 확인됩니다.

   ![토큰을 확인한 조건이 있는 워크플로 디자이너를 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. 논리 앱을 저장합니다.

## <a name="send-email-notifications"></a>이메일 알림 보내기

이제 지정한 조건이 true일 때 이메일을 받을 수 있도록 [*작업*](../logic-apps/logic-apps-overview.md#logic-app-concepts)을 추가합니다.

1. 조건의 **true인 경우** 상자에서 **작업 추가** 를 선택합니다.

   ![조건의 "True인 경우" 창이 열려 있고 "작업 추가"가 선택된 워크플로 디자이너를 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. **작업 선택** 아래의 검색 상자에 `send an email`을 필터로 입력합니다. 전자 메일 공급자에 따라 일치하는 커넥터를 찾아 선택합니다. 그런 다음 커넥터에 대한 "전자 메일 보내기" 작업을 선택합니다. 다음은 그 예입니다.

   * 예를 들어 Azure 회사 또는 학교 계정의 경우 Office 365 Outlook 커넥터를 선택합니다.

   * Microsoft 개인 계정의 경우 Outlook.com 커넥터를 선택합니다.

   * Gmail 계정의 경우 Gmail 커넥터를 선택합니다.

   이 자습서에서는 Office 365 Outlook 커넥터를 계속 사용합니다. 다른 공급자를 사용하는 경우 단계는 동일하지만 UI의 표시가 약간 다를 수 있습니다.

   !["이메일 보내기" 작업을 찾기 위해 검색 상자가 열려 있는 워크플로 디자이너를 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. 전자 메일 공급자에 대한 연결이 아직 없는 경우 인증을 요청받을 때 전자 메일 계정에 로그인합니다.

1. 이메일 보내기 작업의 이름을 `Send email when virtual machine updated` 제목으로 바꿉니다.

1. 다음 표에서 지정한 대로 이메일에 대한 정보를 제공합니다.

   ![실제 조건에 대한 이메일 제목 줄에 동적 콘텐츠가 추가되는 워크플로 디자이너를 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > 워크플로의 이전 단계에서 출력을 선택하려면 동적 콘텐츠 목록이 표시되도록 편집 상자 내부를 클릭하거나 **동적 콘텐츠 추가** 를 선택합니다. 더 많은 결과를 보려면 목록의 각 섹션에 대해 **자세히 보기** 를 선택합니다. 동적 콘텐츠 목록을 닫으려면 **동적 콘텐츠 추가** 를 다시 선택합니다.

   | 속성 | 필수 | 값 | Description |
   | -------- | -------- | ----- | ----------- |
   | **수행할 작업** | 예 | <*recipient\@domain*> | 수신자의 이메일 주소를 입력합니다. 자신의 이메일 주소를 사용하여 테스트할 수 있습니다. |
   | **Subject** | 예 | `Resource updated:` **제목** | 전자 메일의 제목에 콘텐츠를 입력합니다. 이 자습서의 경우 지정된 텍스트를 입력하고 이벤트의 **제목** 필드를 선택합니다. 여기에서 전자 메일 제목에는 업데이트된 리소스(가상 머신)에 대한 이름이 포함됩니다. |
   | **본문** | 예 | `Resource:` **항목** <p>`Event type:` **이벤트 유형**<p>`Event ID:` **ID**<p>`Time:` **이벤트 시간** | 전자 메일의 본문에 콘텐츠를 입력합니다. 이 자습서의 경우 지정된 텍스트를 입력하고 이벤트의 **토픽**, **이벤트 유형**, **ID** 및 **이벤트 시간** 필드를 선택하여 이벤트를 발생한 리소스, 업데이트의 이벤트 유형, 이벤트 타임스탬프 및 이벤트 ID가 메일에 포함되도록 합니다. 이 자습서에서는 리소스가 트리거에서 선택한 Azure 리소스 그룹입니다. <p>콘텐츠에 빈 줄을 추가하려면 Shift + Enter 키를 누릅니다. |
   ||||

   > [!NOTE]
   > 배열을 나타내는 필드를 선택하면 디자이너에서 배열을 참조하는 작업 주위에 **For each** 루프를 자동으로 추가합니다. 그렇게 하면 논리 앱에서 각 배열 항목에 대한 해당 작업을 수행합니다.

   이제 전자 메일 작업이 다음 예와 같이 표시됩니다.

   ![VM이 업데이트될 때 이메일로 보낼 출력이 선택된 워크플로 디자이너를 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   완료된 논리 앱이 다음 예와 같이 표시됩니다.

   ![완료된 논리 앱과 트리거 및 작업의 세부 정보가 표시된 워크플로 디자이너를 보여주는 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. 논리 앱을 저장합니다. 논리 앱에서 각 작업의 세부 정보를 접어 숨기려면 작업의 제목 표시줄을 선택합니다.

   논리 앱이 현재 라이브 상태이지만 어떤 작업을 수행하기 전에 가상 머신에 대한 변경을 기다리고 있습니다. 이제 논리 앱을 테스트하려면 다음 섹션으로 이동하세요.

## <a name="test-your-logic-app-workflow"></a>논리 앱 워크플로 테스트

1. 지정한 이벤트를 논리 앱에서 받고 있는지 확인하려면 가상 머신을 업데이트합니다.

   예를 들어 [가상 머신 크기를 조정](../virtual-machines/resize-vm.md)할 수 있습니다.

   몇 분 후에 전자 메일을 받아야 합니다. 다음은 그 예입니다.

   ![VM 업데이트에 대한 세부 정보를 표시하는 Outlook 이메일 예제의 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. 논리 앱에 대한 실행 및 트리거 기록을 검토하려면 논리 앱 메뉴에서 **개요** 를 선택합니다. 실행에 대한 자세한 세부 정보를 보려면 해당 실행에 대한 행을 선택합니다.

   ![선택된 성공적 실행을 보여 주는 논리 앱의 개요 페이지 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. 각 단계에 대한 입력 및 출력을 보려면 검토하려는 단계를 확장합니다. 이 정보는 논리 앱의 문제를 진단하고 디버깅하는 데 유용합니다.

   ![각 실행에 대한 세부 정보를 표시하는 논리 앱 실행 기록 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

축하합니다! Event Grid를 통해 리소스 이벤트를 모니터링하고 이러한 이벤트가 발생하면 전자 메일을 보내는 논리 앱을 만들고 실행했습니다. 또한 프로세스를 자동화하고 시스템과 클라우드 서비스를 통합하는 워크플로를 쉽게 만들 수 있는 방법에 대해 알아보았습니다.

예를 들어 Event Grid 및 논리 앱을 사용하여 다른 구성 변경 내용을 모니터링할 수 있습니다.

* 가상 머신에서 Azure RBAC(Azure 역할 기반 액세스 제어) 권한을 가져옵니다.
* NIC(네트워크 인터페이스)의 NSG(네트워크 보안 그룹)가 변경되었습니다.
* 가상 컴퓨터용 디스크가 추가되거나 제거되었습니다.
* 공용 IP 주소가 가상 머신 NIC에 할당되었습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서는 리소스를 사용하고 Azure 구독에 요금을 부과하는 작업을 수행합니다. 따라서 이 자습서를 완료하고 테스트할 때 요금이 부과되지 않도록 리소스를 사용하지 않도록 설정하거나 삭제해야 합니다.

* 작업을 삭제하지 않고 논리 앱의 실행을 중지하려면 앱을 사용하지 않도록 설정합니다. 논리 앱 메뉴에서 **개요** 를 선택합니다. 도구 모음에서 **사용 안 함** 을 선택합니다.

  ![논리 앱을 사용하지 않도록 설정하기 위해 선택된 비활성화 단추를 표시하는 논리 앱 개요의 스크린샷](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > 논리 앱 메뉴가 표시되지 않으면 Azure 대시보드로 돌아가서 논리 앱을 다시 엽니다.

* 논리 앱을 영구적으로 삭제하려면 논리 앱 메뉴에서 **개요** 를 선택합니다. 도구 모음에서 **삭제** 를 선택합니다. 논리 앱을 삭제하려고 한다고 확인하고, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](../event-grid/custom-event-quickstart.md)

서로 다른 프로그래밍 언어를 사용하여 Event Grid에서 이벤트를 게시하고 사용하는 방법을 알아보려면 다음 샘플을 참조하세요. 

- [.NET에 대한 Azure Event Grid 샘플](/samples/azure/azure-sdk-for-net/azure-event-grid-sdk-samples/)
- [Java에 대한 Azure Event Grid 샘플](/samples/azure/azure-sdk-for-java/eventgrid-samples/)
- [Python에 대한 Azure Event Grid 샘플](/samples/azure/azure-sdk-for-python/eventgrid-samples/)
- [JavaScript에 대한 Azure Event Grid 샘플](/samples/azure/azure-sdk-for-js/eventgrid-javascript/)
- [TypeScript에 대한 Azure Event Grid 샘플](/samples/azure/azure-sdk-for-js/eventgrid-typescript/)
