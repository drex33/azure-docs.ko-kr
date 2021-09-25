---
title: 메시지를 그룹으로 일괄 처리
description: Azure Logic Apps에서 일괄 처리를 사용하여 워크플로 간에 메시지를 그룹으로 보내고 받습니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: cb38f70c20ad8a45ed1cb91d93e7a6f09a7d51df
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644375"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Azure Logic Apps에서 메시지 보내기, 받기 및 일괄 처리

특정한 방식을 사용하여 메시지를 그룹으로 함께 보내고 처리하기 위해 일괄 처리 솔루션을 만들 수 있습니다. 이 솔루션에서는 일괄 처리된 메시지를 해제하고 처리하기 전에 지정된 조건이 충족될 때까지 메시지를 ‘일괄 처리’로 수집하고 대기합니다. 일괄 처리는 논리 앱에서 메시지를 처리하는 빈도를 줄일 수 있습니다.

이 문서에서는 동일한 Azure 구독, Azure 지역 내에서, 다음 순서에 따라 두 개의 논리 앱을 만들어 일괄 처리 솔루션을 빌드하는 방법을 보여 줍니다.

1. ["일괄 처리 수신자"](#batch-receiver) 논리 앱은 해당 메시지 해제 및 처리에 대해 지정된 조건을 충족할 때까지 메시지를 허용하고 일괄 처리로 수집합니다. 먼저 일괄 처리 수신자를 만들어 나중에 일괄 처리 발신자를 만들 때 일괄 처리 대상을 선택할 수 있도록 합니다.

1. 하나 이상의 ["일괄 처리 발신자"](#batch-sender) 논리 앱은 이전에 만든 일괄 처리 수신자에게 메시지를 보냅니다.

   또한 고객 번호와 같은 고유 키를 지정하여 해당 키에 따라 대상 일괄 처리를 논리적 하위 집합으로 *분할* 하거나 나눌 수도 있습니다. 이런 방식으로 수신자 앱에서 동일한 키가 있는 모든 항목을 수집하고 함께 처리할 수 있습니다.

일괄 처리 수신자와 일괄 처리 발신자는 동일한 Azure 구독 ‘및’ Azure 지역을 공유해야 합니다. 그렇지 않은 경우 서로 표시되지 않기 때문에 일괄 처리 발신자를 만들 때 일괄 처리 수신자를 선택할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 없는 경우 [Azure 계정을 사용하여 시작](https://azure.microsoft.com/free/)할 수 있습니다. 또는 [종량제 구독에 등록합니다](https://azure.microsoft.com/pricing/purchase-options/).

* [Azure Logic Apps에서 지원하는 전자 메일 공급자](../connectors/apis-list.md)를 사용하는 메일 계정

  > [!IMPORTANT]
  > Gmail 커넥터를 사용하려는 경우 G Suite 비즈니스 계정만 논리 앱에서 제한 없이 이 커넥터를 사용할 수 있습니다. Gmail 소비자 계정이 있는 경우 특정 Google 승인 서비스에서만 이 커넥터를 사용하거나 [Gmail 커넥터 인증에 사용할 Google 클라이언트 앱을 만들](/connectors/gmail/#authentication-and-bring-your-own-application) 수 있습니다. 자세한 내용은 [Azure Logic Apps의 Google 커넥터에 대한 데이터 보안 및 개인정보처리방침](../connectors/connectors-google-data-security-privacy-policy.md)을 참조하세요.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* Azure Portal 대신 Visual Studio를 사용하려면 [Logic Apps를 사용하도록 Visual Studio를 설정](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)해야 합니다.

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>일괄 처리 수신자 만들기

메시지를 일괄 처리로 보내려면 먼저 해당 일괄 처리가 해당 메시지를 보내는 대상으로 존재해야 합니다. 따라서 먼저 **일괄 처리** 트리거를 시작하는 "일괄 처리 수신자" 논리 앱을 만들어야 합니다. 이런 방식으로 "일괄 처리 발신자" 논리 앱을 만들 때 일괄 처리 수신자 논리 앱을 선택할 수 있습니다. 일괄 처리 수신자는 지정된 조건이 충족되어 해당 메시지를 해제하고 처리할 때까지 메시지 수집을 계속합니다. 일괄 처리 수신자는 일괄 처리 발신자에 대해 인식하고 있을 필요가 없지만, 일괄 처리 발신자는 메시지를 보내는 대상을 인식하고 있어야 합니다.

1. [Azure Portal](https://portal.azure.com) 또는 Visual Studio에서 이름이 `BatchReceiver`인 논리 앱을 만듭니다.

1. 논리 앱 디자이너에서 논리 앱 워크플로를 시작하는 **일괄 처리** 트리거를 추가합니다. 검색 상자에 `batch`를 입력하고 **일괄 처리 메시지** 트리거를 선택합니다.

   !["일괄 처리 메시지" 트리거 추가](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

1. 일괄 처리 수신자에 대해 다음 속성을 설정합니다.

   | 속성 | 설명 |
   |----------|-------------|
   | **일괄 처리 모드** | - **인라인**: 일괄 처리 트리거 내부에 해제 조건을 정의하는 경우 <br>- **통합 계정**: [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)을 통해 다중 해제 조건 구성을 정의하는 경우. 통합 계정을 사용하면 별도의 논리 앱이 아닌 한 곳에서 이러한 구성을 모두 유지 관리할 수 있습니다. |
   | **일괄 처리 이름** | 일괄 처리에 대한 이름이며(이 예의 경우 "TestBatch"), **인라인** 일괄 처리 모드에만 적용됩니다. |
   | **해제 조건** | **인라인** 일괄 처리 모드에만 적용되며, 각 일괄 처리를 처리하기 전에 충족할 조건을 선택합니다. <p>- **메시지 수 기반**: 일괄 처리에서 수집된 메시지 수를 기반으로 일괄 처리를 해제합니다. <br>- **크기 기반**: 해당 일괄 처리에서 수집된 모든 메시지의 총 크기(바이트)를 기반으로 일괄 처리를 해제합니다. <br>- **일정**: 간격과 빈도를 지정하는 되풀이 일정을 기반으로 일괄 처리를 해제합니다. 고급 옵션에서는 표준 시간대를 선택하고 시작 날짜와 시간을 선택할 수도 있습니다. <br>- **모두 선택**: 지정된 모든 조건을 사용합니다. |
   | **메시지 수** | 일괄 처리에서 수집할 메시지 수입니다(예: 10개 메시지). 일괄 처리당 메시지는 8,000개로 제한됩니다. |
   | **Batch 크기** | 일괄 처리에서 수집할 총 바이트 크기입니다(예: 10MB). 일괄 처리 크기는 80MB로 제한됩니다. |
   | **일정** | 일괄 처리 해제 간의 간격 및 빈도입니다(예: 10분). 되풀이는 최소 60초 또는 1분입니다. 분의 소수 자릿수 부분은 1분으로 반올림됩니다. 표준 시간대 또는 시작 날짜와 시간을 지정하려면 **새 매개 변수 추가** 목록을 열고 해당 속성을 선택합니다. |
   |||

   > [!NOTE]
   >
   > 일괄 처리에 포함되었지만 전송되지 않은 메시지가 트리거에 아직 포함되어 있는 상태에서 해제 기준을 변경하면 트리거는 업데이트된 해제 기준을 사용하여 미전송 메시지를 처리합니다.

   이 예에는 모든 기준이 나와 있지만 실제 테스트에서는 기준을 하나만 사용해 보세요.

   ![Batch 트리거 세부 정보 제공](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

1. 이제 각 일괄 처리를 처리하는 작업을 하나 이상 추가합니다.

   이 예의 경우 일괄 처리 트리거가 발생할 때 이메일을 보내는 작업을 추가합니다. 일괄 처리에서 10개의 메시지가 있거나 10MB에 도달하거나 10분이 경과하면 트리거가 실행되고 이메일을 보냅니다.

   1. 일괄 처리 트리거 아래에서 **새 단계** 를 선택합니다.

   1. 검색 상자에서 필터로 `send email`을 입력합니다. 전자 메일 공급자에 따라 전자 메일 커넥터를 선택합니다.

      예를 들어 @fabrikam.com이나 @fabrikam.onmicrosoft.com과 같은 회사 또는 학교 계정이 있는 경우 **Microsoft 365 Outlook** 커넥터를 선택합니다. @outlook.com이나 @hotmail.com과 같은 개인 계정이 있는 경우 **Outlook.com** 커넥터를 선택합니다. 이 예제에서는 Microsoft 365 Outlook 커넥터를 사용합니다.

   1. 공급자에 대해 “메일 보내기” 작업을 선택합니다. 예를 들면 다음과 같습니다.

      ![전자 메일 공급자에 대한 “전자 메일 보내기” 작업을 선택합니다.](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

1. 메시지가 표시되면 이메일 계정에 로그인합니다.

1. 추가한 작업에 대한 속성을 설정합니다.

   * **받는 사람** 상자에 받는 사람의 이메일 주소를 입력합니다. 자신의 이메일 주소를 사용하여 테스트할 수 있습니다.

   * **제목** 상자에서 동적 콘텐츠 목록이 표시되면 **파티션 이름** 필드를 선택합니다.

     ![동적 콘텐츠 목록에서 "파티션 이름"을 선택합니다.](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     나중에 일괄 처리 발신자에서 메시지를 보낼 수 있는 논리적 하위 세트로 대상 일괄 처리를 분할하는 고유 파티션 키를 지정할 수 있습니다. 각 집합에는 일괄 처리 발신자 논리 앱에서 생성된 고유 번호가 있습니다. 이 기능을 사용하면 여러 하위 집합이 있는 단일 일괄 처리를 사용하고 사용자가 제공한 이름으로 각 하위 집합을 정의할 수 있습니다.

     > [!IMPORTANT]
     > 파티션 한도는 5,000개 메시지 또는 80MB입니다. 두 조건 중 하나가 충족되면 정의된 해제 조건이 충족되지 않은 경우에도 Logic Apps에서 일괄 처리를 해제할 수 있습니다.

   * **본문** 상자에서 동적 콘텐츠 목록이 표시되면 **메시지 ID** 필드를 선택합니다.

     논리 앱 디자이너는 자동으로 메일 보내기 작업에 대한 **For each** 루프를 추가합니다. 해당 작업은 이전 작업의 출력을 일괄 처리가 아닌 컬렉션으로 처리하기 때문입니다.

     ![“본문”에서 “메시지 ID”를 선택합니다.](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

1. 논리 앱을 저장합니다. 이제 일괄 처리 수신자를 만들었습니다.

    ![논리 앱 저장](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

   > [!IMPORTANT]
   > Visual Studio를 사용하는 경우 다음 섹션을 계속 진행하기 전에 먼저 [일괄 처리 수신자 논리 앱을 Azure에 ‘배포’](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)해야 합니다. 그렇지 않으면 일괄 처리 발신자를 만들 때 일괄 처리 수신자를 선택할 수 없습니다.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>일괄 처리 발신자 만들기

이제 일괄 처리 수신자 논리 앱에 메시지를 보내는 하나 이상의 일괄 처리 발신자 논리 앱을 만듭니다. 각 일괄 처리 발신자에서 일괄 처리 수신자와 일괄 처리 이름, 메시지 콘텐츠 및 다른 설정을 지정합니다. 필요에 따라 고유 파티션 키를 제공하여 해당 키가 있는 메시지를 수집하기 위한 논리적 하위 집합으로 일괄 처리를 나눌 수 있습니다.

* 일괄 처리 발신자를 만들 때 기존 일괄 처리 수신자를 대상 일괄 처리로 선택할 수 있도록 이전에 [일괄 처리 수신자를 만들고 배포](#batch-receiver)했는지 확인합니다. 일괄 처리 수신자는 일괄 처리 발신자에 대해 인식하고 있을 필요가 없지만, 일괄 처리 발신자는 메시지를 보내는 위치를 인식하고 있어야 합니다.

* 일괄 처리 수신자와 일괄 처리 발신자가 동일한 Azure 지역 ‘및’ Azure 구독을 공유하는지 확인합니다. 그렇지 않은 경우 서로 표시되지 않기 때문에 일괄 처리 발신자를 만들 때 일괄 처리 수신자를 선택할 수 없습니다.

1. 이름이 `BatchSender`인 다른 논리 앱을 만듭니다.

   1. 검색 상자에서 필터로 `recurrence`을 입력합니다. 트리거 목록에서 다음 트리거를 선택합니다. **되풀이**

      ![되풀이 트리거 추가](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   1. 1분마다 발신자 논리 앱을 실행하도록 간격 및 빈도를 설정합니다.

      ![되풀이 트리거에 대한 빈도 및 간격 설정](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

1. 메시지를 보내는 새 작업을 일괄 처리에 추가합니다.

   1. **되풀이** 트리거 아래에서 **새 단계** 를 선택합니다.

   1. 검색 상자에 `batch`를 필터로 입력하고 **일괄 처리 트리거를 사용하여 Logic Apps 워크플로 선택** 작업을 선택합니다.

      !["일괄 처리 트리거가 있는 Logic Apps 워크플로를 선택합니다." 선택](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

      목록이 표시됩니다. 목록은 일괄 처리 트리거가 포함되어 있으며 일괄 처리 발신자 논리 앱과 동일한 Azure 지역 ‘및’ Azure 구독에 있는 논리 앱만 보여 줍니다.

   1. 논리 앱 목록에서 이전에 만든 일괄 처리 수신자 논리 앱을 선택합니다.

      ![일괄 처리 수신자 논리 앱 선택](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!IMPORTANT]
      > Visual Studio를 사용하고 있으며 선택할 일괄 처리 수신자가 표시되지 않는 경우 이전에 일괄 처리 수신자를 만들어 Azure에 배포했는지 확인합니다. 아직 배포하지 않은 경우 [일괄 처리 수신자 논리 앱을 Azure에 배포하는 방법](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)을 알아보세요.

   1. 작업 목록에서 다음 작업을 선택 합니다. **Batch_messages- \<*your-logic-app-name*\>**

      !["Batch_messages- \< 논리 앱" 작업을 선택 합니다. \>](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

1. 일괄 처리 발신자의 속성을 설정합니다.

   | 속성 | 설명 |
   |----------|-------------|
   | **일괄 처리 이름** | 수신자 논리 앱에서 정의된 일괄 처리 이름입니다. 이 예제에서는 `TestBatch`입니다. <p>**중요**: 일괄 처리 이름은 런타임에 유효성이 검사되고 수신자 논리 앱에서 지정된 이름과 일치해야 합니다. 일괄 처리 이름을 변경하면 일괄 처리 발신자가 실패하게 됩니다. |
   | **메시지 콘텐츠** | 보내려는 메시지에 대한 콘텐츠입니다. |
   |||

   > [!NOTE]
   > **트리거 이름** 및 **워크플로** 속성 값은 선택한 논리 앱에서 자동으로 채워집니다.

   이 예의 경우 현재 날짜 및 시간을 일괄 처리로 보내는 메시지 콘텐츠에 삽입하는 식을 추가합니다.

   1. **메시지 콘텐츠** 상자 내부를 클릭합니다.

   1. 동적 콘텐츠 목록이 표시되면 **식** 을 선택합니다.

   1. 식 `utcnow()`를 입력하고 **확인** 을 선택합니다.

      ![“메시지 콘텐츠”에서 “식”을 선택하고 “utcnow()”를 입력한 후 “확인”을 선택합니다.](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

1. 이제 일괄 처리에 대한 파티션을 설정합니다. `BatchReceiver` 작업에서 **새 매개 변수 추가** 목록을 열고 다음 속성을 선택합니다.

   | 속성 | 설명 |
   |----------|-------------|
   | **파티션 이름** | 대상 일괄 처리를 논리적 하위 집합으로 나누고 해당 키에 따라 메시지를 수집하는 데 사용하는 선택적 고유 파티션 키입니다. |
   | **메시지 ID** | 비어 있을 때 생성된 GUID(Globally Unique Identifier)라는 선택적 메시지 식별자입니다. |
   |||

   이 예의 경우 **파티션 이름** 상자에서 1에서 5 사이의 난수를 생성하는 식을 추가합니다. **메시지 ID** 상자는 비워 둡니다.

   1. **파티션 이름** 상자 내부를 클릭하여 동적 콘텐츠 목록을 표시합니다.

   1. 동적 콘텐츠 목록에서 **식** 을 선택합니다.

   1. `rand(1,6)` 식을 입력하고 **확인** 을 선택합니다.

      ![대상 일괄 처리에 대한 파티션 설정](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      이 **rand** 함수는 1과 5 사이의 숫자를 생성합니다. 따라서 이 식이 동적으로 설정하는 번호가 지정된 5개 파티션으로 이 일괄 처리를 나눕니다.

1. 논리 앱을 저장합니다. 발신자 논리 앱이 이제 다음 예제와 비슷하게 표시됩니다.

   ![발신자 논리 앱 저장](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>논리 앱 테스트

일괄 처리 솔루션을 테스트하려면 논리 앱을 몇 분 동안 실행 상태로 둡니다. 곧 동일한 파티션 키를 사용하는 5개 그룹에서 전자 메일을 받기 시작합니다.

일괄 처리 발신자 논리 앱은 1분마다 실행되며, 1에서 5 사이의 난수를 생성하고, 메시지를 보내는 대상 일괄 처리에 대한 파티션 키로 이 생성된 번호를 사용합니다. 일괄 처리에 동일한 파티션 키가 있는 5개 항목이 있을 때마다 일괄 처리 수신자 논리 앱이 실행되고 각 메시지에 대한 메일을 보냅니다.

> [!IMPORTANT]
> 테스트가 완료되면 `BatchSender` 논리 앱을 사용하지 않도록 설정하여 메시지 보내는 것을 중지하고 받은 편지함이 오버로드되지 않게 합니다.

## <a name="next-steps"></a>다음 단계

* [EDI 메시지 일괄 처리 및 보내기](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [JSON을 사용하여 논리 앱 정의 빌드](../logic-apps/logic-apps-author-definitions.md)
* [논리 앱에 대한 예외 처리 및 오류 로깅](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
