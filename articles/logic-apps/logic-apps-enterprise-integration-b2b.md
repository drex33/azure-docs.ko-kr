---
title: Exchange 워크플로를 사용하는 B2B 메시지
description: Azure Logic Apps Enterprise 통합 팩을 통해 워크플로를 만들어 거래 업체 간에 메시지를 Exchange.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/17/2021
ms.openlocfilehash: 4b8907b9a50be3bd6021b72dd05c7d5445da03f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128660432"
---
# <a name="exchange-b2b-messages-between-partners-using-workflows-in-azure-logic-apps"></a>Exchange Azure Logic Apps 워크플로를 사용하는 파트너 간의 B2B 메시지

거래 업체 및 계약을 정의하는 통합 계정이 있는 경우 Azure Logic Apps 사용하여 거래 업체 간에 메시지를 교환하는 자동화된 B2B(기업 간) 워크플로를 만들 수 있습니다. 워크플로는 AS2, X12, EDIFACT 및 RosettaNet과 같은 업계 표준 프로토콜을 지원하는 커넥터를 사용할 수 있습니다. Office 365 Outlook, SQL Server 및 Salesforce와 같은 Azure Logic Apps 다른 [커넥터에서](/connectors/connector-reference/connector-reference-logicapps-connectors)제공하는 작업을 포함할 수도 있습니다.

이 문서에서는 **요청** 트리거를 사용하여 HTTP 요청을 수신하고, **AS2 디코딩** 및 X12 디코딩 작업을 사용하여 메시지 콘텐츠를 **디코딩하고,** **응답** 작업을 사용하여 응답을 반환할 수 있는 예제 논리 앱 워크플로를 만드는 방법을 보여줍니다. 이 예제에서는 Azure Portal 워크플로 디자이너를 사용하지만 Visual Studio 워크플로 디자이너에 대해 비슷한 단계를 따를 수 있습니다.

논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](logic-apps-overview.md)을 검토하세요. B2B 엔터프라이즈 통합에 대한 자세한 내용은 [Azure Logic Apps B2B 엔터프라이즈 통합 워크플로를 검토하세요.](logic-apps-enterprise-integration-overview.md)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 엔터프라이즈 통합 및 B2B 워크플로에서 사용하기 위해 거래 업체, 계약, 인증서 등과 같은 아티팩트 정의 및 저장을 위한 [통합 계정 리소스입니다.](logic-apps-enterprise-integration-create-integration-account.md) 이 리소스는 다음 요구 사항을 충족해야 합니다.

  * 논리 앱 리소스와 동일한 Azure 구독과 연결되어야 합니다.

  * 논리 앱 리소스와 동일한 위치 또는 Azure 지역에 존재합니다.

  * [ **논리 앱(소비)** 리소스 종류를](logic-apps-overview.md#resource-type-and-host-environment-differences)사용하는 경우 워크플로에서 아티팩트를 사용하려면 통합 계정에 논리 앱 리소스에 대한 [링크가](logic-apps-enterprise-integration-create-integration-account.md#link-account) 필요합니다.

  * [ **논리 앱(표준)** 리소스 종류를](logic-apps-overview.md#resource-type-and-host-environment-differences)사용하는 경우 통합 계정에 논리 앱 리소스에 대한 링크가 필요하지 않지만 [AS2,](logic-apps-enterprise-integration-as2.md) [X12](logic-apps-enterprise-integration-x12.md)또는 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 작업과 함께 파트너, 계약 및 인증서와 같은 다른 아티팩트를 저장해야 합니다. 통합 계정은 여전히 논리 앱 리소스와 동일한 위치에 있는 동일한 Azure 구독 및 기존 구독을 사용하는 것과 같은 다른 요구 사항을 충족해야 합니다.

  > [!NOTE]
  > 현재 **논리 앱(소비)** 리소스 유형만 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업을 지원합니다. **논리 앱(표준)** 리소스 종류에는 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업이 포함되지 않습니다.

* 통합 계정에 두 개 이상의 [거래 업체가](logic-apps-enterprise-integration-partners.md) 있습니다. 두 파트너에 대한 정의는 AS2, X12, EDIFACT 또는 RosettaNet과 동일한 *비즈니스 ID* 한정자를 사용해야 합니다.

* 이 워크플로에서 사용하는 파트너에 대한 [AS2 계약 및 X12 계약입니다.](logic-apps-enterprise-integration-agreements.md) 각 계약에는 호스트 파트너 및 게스트 파트너가 필요합니다.

* [요청](../connectors/connectors-native-reqres.md) 트리거 및 다음 작업을 추가할 수 있는 빈 워크플로가 있는 논리 앱 리소스:

  * [AS2 디코딩](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [조건](../logic-apps/logic-apps-control-flow-conditional-statement.md) - AS2 디코딩 작업이 성공 또는 실패 여부에 따라 [응답](../connectors/connectors-native-reqres.md)을 보냅니다.

  * [X12 메시지 디코딩](../logic-apps/logic-apps-enterprise-integration-x12.md)

<a name="add-request-trigger"></a>

## <a name="add-the-request-trigger"></a>요청 트리거 추가

이 예제에서 워크플로를 시작하려면 요청 트리거를 추가합니다.

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. [Azure Portal](https://portal.azure.com)워크플로 디자이너에서 논리 앱 리소스와 빈 워크플로를 엽니다.

1. 디자이너 검색 상자에서 **모두** 를 선택합니다(선택하지 않은 경우). 검색 상자에 `when a http request`를 입력합니다. HTTP 요청을 받을 때 라는 **요청 트리거를** 선택합니다.

   ![검색 상자에서 "http 요청 시"가 선택되고 요청 트리거가 선택된 Azure Portal 및 다중 테넌트 디자이너를 보여주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/select-request-trigger-consumption.png)

1. 트리거에서 **요청 본문 JSON 스키마** 상자를 비워 둡니다.

   그 이유는 트리거가 플랫 파일 형식의 X12 메시지를 받기 때문입니다.

   ![다중 테넌트 디자이너 및 요청 트리거 속성을 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-b2b/request-trigger-consumption.png)

1. 완료되면 디자이너 도구 모음에서 **저장** 을 선택합니다.

   이 단계에서는 나중에 논리 앱 워크플로를 트리거하는 요청을 보내는 데 사용하는 **HTTP POST URL을** 생성합니다.

   ![요청 트리거에 대한 다중 테넌트 디자이너 및 생성된 URL을 보여주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/request-trigger-generated-url-consumption.png)

1. 나중에 사용할 URL을 복사하고 저장합니다.

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure Portal](https://portal.azure.com)워크플로 디자이너에서 논리 앱 리소스와 빈 워크플로를 엽니다.

1. 디자이너에서 작업 **선택을 선택합니다.** 검색 상자에서 **기본 제공** 을 선택합니다(선택하지 않은 경우). 검색 상자에 `when a http request`를 입력합니다. HTTP 요청을 받을 때 라는 **요청 트리거를** 선택합니다.

   ![검색 상자에서 "http 요청 시"가 선택되고 요청 트리거가 선택된 Azure Portal 및 단일 테넌트 디자이너를 보여주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/select-request-trigger-standard.png)

1. 트리거에서 **요청 본문 JSON 스키마** 상자를 비워 둡니다.

   그 이유는 트리거가 플랫 파일 형식의 X12 메시지를 받기 때문입니다.

   ![단일 테넌트 디자이너 및 요청 트리거 속성을 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-b2b/request-trigger-standard.png)

1. 완료되면 디자이너 도구 모음에서 **저장** 을 선택합니다.

   이 단계에서는 나중에 논리 앱 워크플로를 트리거하는 요청을 보내는 데 사용하는 **HTTP POST URL을** 생성합니다.

   ![요청 트리거에 대한 단일 테넌트 디자이너 및 생성된 URL을 보여주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/request-trigger-generated-url-standard.png)

1. 나중에 사용할 URL을 복사하고 저장합니다.

---

<a name="add-decode-as2-trigger"></a>

## <a name="add-the-decode-as2-action"></a>AS2 디코딩 작업 추가

이제 AS2 및 X12 작업을 사용하는 이 예제에 대한 B2B 작업을 추가합니다.

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. 트리거 아래에서 **새 단계** 를 선택합니다.

   > [!TIP]
   > 요청 트리거 세부 정보를 숨기려면 트리거의 제목 표시줄을 선택합니다.

   ![다중 테넌트 디자이너 및 "새 단계"가 선택된 트리거를 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-b2b/add-action-under-trigger-consumption.png)

1. 작업 **선택** 검색 상자에서 **모두** 를 선택합니다(선택하지 않은 경우). 검색 상자에 를 `as2` 입력하고 **AS2 디코딩을** 선택합니다.

   !["AS2 디코딩" 작업이 선택된 다중 테넌트 디자이너를 보여주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action-consumption.png)

1. 동작의 **디코딩할 메시지** 속성에 AS2 작업을 디코딩할 입력(요청 `body` 트리거의 출력)을 입력합니다. 동적 콘텐츠 목록에서 선택하거나 식으로 선택하여 이 콘텐츠를 작업의 입력으로 지정하는 여러 가지 방법이 있습니다.

   * 사용 가능한 트리거 출력을 표시하는 목록에서 선택하려면 **디코딩할 메시지** 상자 내부를 클릭합니다. 동적 콘텐츠 목록이 나타나면 **HTTP 요청을 받은 경우** 아래에서 **본문** 속성 값을 선택합니다. 예를 들면 다음과 같습니다.

     ![동적 콘텐츠 목록 및 "본문" 속성이 선택된 다중 테넌트 디자이너를 보여주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/select-trigger-output-body-consumption.png)

     > [!TIP]
     > 트리거 출력이 표시되지 않으면 동적 속성 목록의 **HTTP 요청을 받은 경우** 에서 자세히 **보기를** 선택합니다.

   * 트리거의 `body` 출력을 참조하는 식을 입력하려면 **디코딩할 메시지** 상자 내부를 클릭합니다. 동적 콘텐츠 목록이 표시되면 **식** 을 선택합니다. 식 편집기에서 다음 식을 입력하고 **확인을** 선택합니다.

     `triggerOutputs()['body']`

     또는 **디코딩할 메시지** 상자에 다음 식을 직접 입력합니다.

     `@triggerBody()`

     식은 **Body** 토큰으로 확인됩니다.

     ![해결된 "본문" 속성 출력이 있는 다중 테넌트 디자이너를 보여주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/resolved-body-property-consumption.png)

1. 작업의 **메시지 헤더** 속성에서 요청 트리거의 출력에 있는 AS2 작업에 필요한 `headers` 헤더를 입력합니다.

   1. 트리거의 `headers` 출력을 참조하는 식을 입력하려면 **메시지 헤더를 텍스트 모드로 전환** 을 선택합니다.

      !["메시지 헤더를 텍스트 모드로 전환"이 선택된 다중 테넌트 디자이너를 보여주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/switch-text-mode-consumption.png)

   1. **메시지 헤더** 상자 내부를 클릭합니다. 동적 콘텐츠 목록이 표시되면 **식** 을 선택합니다. 식 편집기에서 다음 식을 입력하고 **확인을** 선택합니다.

      `triggerOutputs()['Headers']`

      **AS2 디코딩** 작업에서 식은 이제 토큰으로 표시됩니다.

      !["()['Headers']" 토큰이 있는 다중 테넌트 디자이너 및 "메시지 헤더" 상자를 보여주는 스크린샷 @triggerOutputs](./media/logic-apps-enterprise-integration-b2b/as2-header-expression-consumption.png)

   1. **헤더** 토큰으로 확인할 식 토큰을 가져오려면 디자이너와 코드 보기 간을 전환합니다. 이 단계를 수행하면 **AS2 디코딩** 작업이 다음 예제와 같이 표시됩니다.

      ![트리거에서 다중 테넌트 디자이너 및 해결된 헤더 출력을 보여주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/resolved-as2-header-expression-consumption.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. 트리거 아래에서 **새 단계 삽입(더하기** 기호)을 선택한 **다음, 작업 추가를** 선택합니다.

   ![더하기 기호가 선택된 단일 테넌트 디자이너 및 트리거를 보여주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/add-action-under-trigger-standard.png)

1. 작업 **선택** 검색 상자에서 **Azure** 를 선택합니다(아직 선택하지 않은 경우). 검색 상자에 를 `as2` 입력하고 **AS2 메시지 디코딩을** 선택합니다.

   !["AS2 메시지 디코딩" 작업이 선택된 단일 테넌트 디자이너를 보여주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action-standard.png)

1. 통합 계정에 대한 연결을 만들라는 메시지가 표시되면 연결에 사용할 이름을 입력하고 통합 계정을 선택한 다음, **만들기를** 선택합니다.

1. 작업의 **본문** 속성에 AS2 작업을 디코딩할 `body` 입력(요청 트리거의 출력)을 입력합니다. 동적 콘텐츠 목록에서 선택하거나 식으로 선택하여 이 콘텐츠를 작업의 입력으로 지정하는 여러 가지 방법이 있습니다.

   * 사용 가능한 트리거 출력을 표시 하는 목록에서 선택 하려면 **본문** 속성 상자 내부를 클릭 합니다. 동적 콘텐츠 목록이 나타나면 **HTTP 요청을 받은 경우** 아래에서 **본문** 속성 값을 선택합니다. 예를 들면 다음과 같습니다.

     ![동적 콘텐츠 목록이 있는 단일 테 넌 트 디자이너와 "Body" 속성이 선택 된 스크린샷](./media/logic-apps-enterprise-integration-b2b/select-trigger-output-body-standard.png)

     > [!TIP]
     > 트리거 출력이 나타나지 않으면 동적 속성 목록의 **HTTP 요청을 받을 때** 아래에 있는 **자세히 보기** 를 선택 합니다.

   * 트리거의 출력을 참조 하는 식을 입력 하려면 `body` **본문** 속성 상자 내부를 클릭 합니다. 동적 콘텐츠 목록이 표시되면 **식** 을 선택합니다. 식 편집기에서 다음 식을 입력 하 고 **확인** 을 선택 합니다.

     `triggerOutputs()['body']`

     또는 **본문** 속성 상자에 다음 식을 직접 입력 합니다.

     `@triggerBody()`

     식은 **Body** 토큰으로 확인됩니다.

     !["본문" 속성 출력이 확인 된 단일 테 넌 트 디자이너를 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/resolved-body-property-standard.png)

1. **헤더** 속성 상자에 요청 트리거의 출력에 있는 AS2 작업에 필요한 헤더를 입력 합니다 `headers` .

   1. 트리거의 `headers` 출력을 참조하는 식을 입력하려면 **메시지 헤더를 텍스트 모드로 전환** 을 선택합니다.

      !["메시지 헤더를 텍스트 모드로 전환"을 선택 하 여 단일 테 넌 트 디자이너를 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/switch-text-mode-standard.png)

   1. **머리글** 속성 상자 내부를 클릭 합니다. 동적 콘텐츠 목록이 표시되면 **식** 을 선택합니다. 식 편집기에서 다음 식을 입력 하 고 **확인** 을 선택 합니다.

      `triggerOutputs()['Headers']`

      **AS2 메시지 디코딩** 작업에서 식은 이제 토큰으로 표시 됩니다.

      ![단일 테 넌 트 디자이너와 " @triggerOutputs () [' headers ']" 토큰이 있는 "headers" 속성을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/as2-header-expression-standard.png)

   1. **헤더** 토큰으로 확인할 식 토큰을 가져오려면 디자이너와 코드 보기 간을 전환합니다. 이 단계를 수행한 후 **AS2 디코드** 작업은 다음 예제와 같습니다.

      ![단일 테 넌 트 디자이너 및 트리거에서 확인 된 헤더 출력을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/resolved-as2-header-expression-standard.png)

---

<a name="add-response-action"></a>

## <a name="add-the-response-action-as-a-message-receipt"></a>메시지 수신으로 응답 동작 추가

거래 파트너에 게 메시지를 수신 했음을 알리려면 조건 및 응답 작업을 사용 하 여 AS2 MDN (메시지 처리 알림)이 포함 된 응답을 반환 하면 됩니다. As2 작업 바로 뒤에 이러한 작업을 추가 하면 AS2 작업이 성공 하는 경우 논리 앱 워크플로에서 처리를 계속할 수 있습니다. 그렇지 않으면 AS2 작업이 실패 하는 경우 논리 앱 워크플로는 처리를 중지 합니다.

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. **AS2 디코딩** 작업 아래에서 **새 단계** 를 선택합니다.

1. **작업 선택** 검색 상자 아래에서 **기본 제공** 을 선택 합니다 (아직 선택 하지 않은 경우). 검색 상자에 `condition`를 입력합니다. **조건** 작업을 선택 합니다.

   ![다중 테 넌 트 디자이너와 "조건" 동작을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/add-condition-action-consumption.png)

   이제 조건을 충족 하는지 여부를 결정 하는 경로를 포함 하 여 condition 셰이프가 나타납니다.

   ![다중 테 넌 트 디자이너와 빈 경로가 있는 condition 셰이프를 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/added-condition-action-consumption.png)

1. 이제 평가할 조건을 지정합니다. **값 선택** 상자에 다음 식을 입력 합니다.

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   가운데 상자에서 비교 작업이 `is equal to`로 설정되었는지 확인합니다. 오른쪽 상자에 값 `Expected`를 입력합니다.

1. 논리 앱 워크플로 저장 이 토큰으로 확인할 식을 가져오려면 디자이너와 코드 보기 간을 전환합니다.

   ![다중 테 넌 트 디자이너와 작업을 사용 하는 condition 셰이프를 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/evaluate-condition-expression-consumption.png)

1. 이제 **AS2 디코딩** 작업 성공 여부에 따라 반환할 응답을 지정 합니다.

   1. **AS2 디코드** 작업이 성공 하는 경우 **True** 모양에서 **작업 추가** 를 선택 합니다. **작업 선택** 검색 상자에를 입력 하 `response` 고 **응답** 을 선택 합니다.

      ![다중 테 넌 트 디자이너 및 "응답" 동작을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/select-response-consumption.png)

   1. **As2 디코드** 작업의 출력에서 as2 MDN에 액세스 하려면 다음 식을 지정 합니다.

      * **응답** 동작의 **헤더** 속성에 다음 식을 입력 합니다.

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * **응답** 동작의 **Body** 속성에 다음 식을 입력 합니다.

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. 토큰으로 확인할 식을 가져오려면 디자이너와 코드 보기 간을 전환합니다.

      ![AS2 MDN에 액세스 하기 위한 다중 테 넌 트 디자이너 및 확인 된 식을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/response-success-resolved-expression-consumption.png)

   1. **AS2 디코드** 작업이 실패 하는 경우 **False** 셰이프에서 **동작 추가** 를 선택 합니다. **작업 선택** 검색 상자에를 입력 하 `response` 고 **응답** 을 선택 합니다. 원하는 상태와 오류를 반환하도록 **응답** 작업을 설정합니다.

1. 논리 앱 워크플로 저장

### <a name="standard"></a>[Standard](#tab/standard)

1. **AS2 메시지 디코딩** 작업에서 **새 단계 삽입** (더하기 기호)을 선택 하 고 **작업 추가** 를 선택 합니다.

1. **작업 선택** 검색 상자 아래에서 **기본 제공** 을 선택 합니다 (아직 선택 하지 않은 경우). 검색 상자에 `condition`를 입력합니다. **조건** 작업을 선택 합니다.

   !["조건" 작업이 선택 된 단일 테 넌 트 디자이너를 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/add-condition-action-standard.png)

   이제 조건을 충족 하는지 여부를 결정 하는 경로를 포함 하 여 condition 셰이프가 나타납니다.

   ![단일 테 넌 트 디자이너와 빈 경로가 있는 "Condition" 셰이프를 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/added-condition-action-standard.png)

1. 이제 평가할 조건을 지정합니다. 세부 정보 패널이 나타나도록 **Condition** 셰이프를 선택 합니다. **값 선택** 상자에 다음 식을 입력 합니다.

   `@body('Decode_AS2_message')?['AS2Message']?['MdnExpected']`

   가운데 상자에서 비교 작업이 `is equal to`로 설정되었는지 확인합니다. 오른쪽 상자에 값 `Expected`를 입력합니다.

1. 논리 앱 워크플로 저장 이 토큰으로 확인할 식을 가져오려면 디자이너와 코드 보기 간을 전환합니다.

   ![단일 테 넌 트 디자이너와 작업을 사용 하는 condition 셰이프를 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/evaluate-condition-expression-standard.png)

1. 이제 **AS2 메시지 디코딩** 작업이 성공 하는지 여부에 따라 반환할 응답을 지정 합니다.

   1. **AS2 메시지 디코딩** 작업이 성공 하는 경우 **True** 모양에서 더하기 기호를 선택한 다음 **작업 추가** 를 선택 합니다. 작업 **추가** 창의 **작업 선택** 검색 상자에를 입력 하 `response` 고 **응답** 을 선택 합니다.

      ![단일 테 넌 트 디자이너 및 "응답" 작업을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/select-response-standard.png)

   1. **As2 디코드** 작업의 출력에서 as2 MDN에 액세스 하려면 다음 식을 지정 합니다.

      * **응답** 동작의 **헤더** 속성에 다음 식을 입력 합니다.

        `@body('Decode_AS2_message')?['OutgoingMdn']?['OutboundHeaders']`

      * **응답** 동작의 **Body** 속성에 다음 식을 입력 합니다.

        `@body('Decode_AS2_message')?['OutgoingMdn']?['Content']`

   1. 토큰으로 확인할 식을 가져오려면 디자이너와 코드 보기 간을 전환합니다.

      ![AS2 MDN에 액세스 하기 위한 단일 테 넌 트 디자이너 및 확인 된 식을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/response-success-resolved-expression-standard.png)

   1. **AS2 메시지 디코딩** 작업이 실패 하는 경우 **False** 셰이프에서 더하기 기호를 선택한 다음 **작업 추가** 를 선택 합니다. 작업 **추가** 창의 **작업 선택** 검색 상자에를 입력 하 `response` 고 **응답** 을 선택 합니다. 원하는 상태와 오류를 반환하도록 **응답** 작업을 설정합니다.

1. 논리 앱 워크플로 저장

---

<a name="add-decode-x12-action"></a>

## <a name="add-the-decode-x12-message-action"></a>X12 메시지 디코딩 작업 추가

이제 **X12 메시지 디코딩** 작업을 추가합니다.

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. **응답** 작업에서 **작업 추가** 를 선택합니다.

1. **작업 선택** 아래의 검색 상자에를 입력 하 `x12 decode` 고 **X12 메시지 디코딩** 을 선택 합니다.

   ![다중 테 넌 트 디자이너와 "X12 메시지 디코딩" 작업을 선택한 작업을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action-consumption.png)

1. X12 작업 중에 연결 정보를 묻는 메시지가 표시되면 연결 이름을 입력하고 사용할 통합 계정을 선택한 다음, **만들기** 를 선택합니다.

   ![다중 테 넌 트 디자이너 및 통합 계정에 대 한 연결을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection-consumption.png)

1. 이제 X12 작업에 대한 입력을 지정합니다. 이 예제에서는 메시지 콘텐츠에 해당하는 AS2 작업의 출력을 사용하지만 이 콘텐츠는 JSON 개체 형식이며 base64로 인코딩됩니다. 따라서 이 콘텐츠를 문자열로 변환해야 합니다.

   **디코딩할 X12 플랫 파일 메시지** 상자에서 다음 식을 입력 하 여 AS2 출력을 변환 합니다.

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

1. 논리 앱 워크플로 저장 이 토큰으로 확인할 식을 가져오려면 디자이너와 코드 보기 간을 전환합니다.

    ![다중 테 넌 트 디자이너 및 b a s e 64로 인코딩된 콘텐츠에서 문자열로 변환 하는 과정을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content-consumption.png)

1. 논리 앱 워크플로 저장

   메시지 내용을 디코딩하고 JSON 개체 형식으로 해당 콘텐츠를 출력 하는 등이 논리 앱 워크플로에 대 한 추가 단계가 필요한 경우 논리 앱 워크플로에 필요한 작업을 계속 추가 합니다.

### <a name="standard"></a>[Standard](#tab/standard)

1. **응답** 동작 아래에서 더하기 기호를 선택 하 고 **작업 추가** 를 선택 합니다. 작업 **추가** 창의 **작업 선택** 검색 상자 아래에서 **Azure** 를 선택 합니다 (아직 선택 하지 않은 경우). 검색 상자에를 입력 하 `x12 decode` 고 **X12 메시지 디코딩** 을 선택 합니다.

   ![단일 테 넌 트 디자이너와 "X12 메시지 디코딩" 작업을 선택한 작업을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action-standard.png)

1. X12 작업 중에 연결 정보를 묻는 메시지가 표시되면 연결 이름을 입력하고 사용할 통합 계정을 선택한 다음, **만들기** 를 선택합니다.

   ![단일 테 넌 트 디자이너 및 통합 계정에 대 한 연결을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection-standard.png)

1. 이제 X12 작업에 대한 입력을 지정합니다. 이 예제에서는 메시지 콘텐츠 인 AS2 작업의 출력을 사용 하지만,이 콘텐츠는 JSON 개체 형식이 며 b a s e 64로 인코딩됩니다. 따라서 이 콘텐츠를 문자열로 변환해야 합니다.

   **디코딩할 X12 플랫 파일 메시지** 상자에서 다음 식을 입력 하 여 AS2 출력을 변환 합니다.

   `@base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])`

1. 논리 앱 워크플로 저장 이 토큰으로 확인할 식을 가져오려면 디자이너와 코드 보기 간을 전환합니다.

    ![단일 테 넌 트 디자이너 및 b a s e 64로 인코딩된 콘텐츠에서 문자열로 변환 하는 과정을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content-standard.png)

1. 논리 앱 워크플로를 다시 저장 합니다.

   메시지 내용을 디코딩하고 JSON 개체 형식으로 해당 콘텐츠를 출력 하는 등이 논리 앱 워크플로에 대 한 추가 단계가 필요한 경우 논리 앱 워크플로에 필요한 작업을 계속 추가 합니다.

---

이제 B2B 논리 앱 워크플로를 설정 하는 작업이 완료 되었습니다. 실제 앱에서는 디코딩된 X12 데이터를 LOB(기간 업무) 앱 또는 데이터 스토리지에 저장하려고 할 수도 있습니다. 예를 들어 다음 설명서를 검토 합니다.

* [Azure Logic Apps에서 SAP 시스템에 연결](/logic-apps-using-sap-connector.md)

* [SSH 및 Azure Logic Apps를 사용하여 SFTP 파일 모니터링, 만들기 및 관리](../connectors/connectors-sftp-ssh.md)

자신의 LOB 앱을 연결하고 논리 앱에서 이러한 API를 사용하려면 추가 작업을 추가하거나 [사용자 지정 API를 작성](logic-apps-create-api-app.md)하면 됩니다.

## <a name="next-steps"></a>다음 단계

* [들어오는 HTTPS 호출 수신 및 응답](../connectors/connectors-native-reqres.md)
* [B2B 엔터프라이즈 통합을 위한 AS2 메시지 교환](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [B2B 엔터프라이즈 통합을 위한 X12 메시지 교환](../logic-apps/logic-apps-enterprise-integration-x12.md)
* [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)에 대해 자세히 알아보기
