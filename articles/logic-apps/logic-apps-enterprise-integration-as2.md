---
title: Exchange B2B 워크플로의 AS2 메시지
description: Exchange Azure Logic Apps 및 Enterprise 통합 팩을 사용하여 워크플로를 만들어 거래 간 AS2 메시지
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: article
ms.date: 09/27/2021
ms.openlocfilehash: 8b42987055ca2e2b6533ae2f9d45b4bb62fbe016
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129082458"
---
# <a name="exchange-as2-messages-using-workflows-in-azure-logic-apps"></a>Exchange Azure Logic Apps 워크플로를 사용하는 AS2 메시지

Azure Logic Apps 사용하여 만든 워크플로에서 AS2 메시지를 보내고 받으려면 **AS2(버전** 1.2) 통신을 지원하고 관리하는 트리거 및 작업을 제공하는 AS2 커넥터를 사용합니다.

* **논리 앱(소비)** 리소스 종류로 작업 중이고 추적 기능이 필요하지 않은 경우 더 이상 사용되지 않는 원래 AS2 커넥터 대신 **AS2(v2)** 커넥터를 사용합니다. 

  추적을 제외하고 **AS2(v2)는** 더 나은 성능을 제공하고, 원래 버전과 동일한 기능을 제공하며, Azure Logic Apps 런타임의 기본이며, 메시지 크기, 처리량 및 대기 시간 성능이 크게 향상되었습니다. 또한, v2 커넥터는 통합 계정에 대한 연결을 만들 필요가 없습니다. 대신, 필수 구성요소에 설명된 대로 커넥터를 사용하려는 논리 앱 리소스에 통합 계정을 연결해야 합니다.

* **논리 앱(표준)** 리소스 종류로 작업하는 경우 현재 원래 **AS2** 커넥터만 사용할 수 있습니다. 이 버전에 대한 자세한 내용은 [커넥터의 Swagger](/connectors/as2/)파일에 설명된 대로 트리거, 작업 및 제한을 설명하는 커넥터의 참조 페이지 를 검토하세요.

### <a name="consumption"></a>[Consumption](#tab/consumption)

다음 목록에서는 메시지를 전송할 때 **AS2(v2)** 커넥터가 보안 및 안정성을 설정하기 위해 제공하는 작업을 설명합니다.

* [**AS2 인코딩** 작업](#encode) - MDN(메시지 처리 알림)을 통해 암호화, 디지털 서명 및 승인을 제공하는 데 사용됩니다. 이를 통해 부인 방지를 지원할 수 있습니다. 예를 들어, 이 작업은 AS2/HTTP 헤더를 적용하고 구성될 때 이러한 작업을 수행합니다.

  * 발신 메시지에 서명합니다.
  * 발신 메시지를 암호화합니다.
  * 메시지를 압축합니다.
  * MIME 헤더의 파일 이름을 전송합니다.

* [**AS2 디코딩** 작업](#decode) - MDN(메시지 처리 알림)을 통해 암호 해독, 디지털 서명 및 승인을 제공하는 데 사용됩니다. 예를 들어, 이 작업은 다음 작업을 수행합니다.

  * AS2/HTTP 헤더를 처리합니다.
  * 원본 아웃바운드 메시지와 함께 수신된 MDN을 조정합니다.
  * 부인 방지 데이터베이스에서 레코드를 업데이트 및 연결합니다.
  * AS2 상태 보고에 대한 레코드를 작성합니다.
  * 페이로드 콘텐츠를 base64 인코딩으로 출력합니다.
  * MDN이 필요한지 여부를 결정합니다. AS2 규약을 기반으로 MDN이 동기인지 비동기인지 결정합니다.
  * AS2 규약에 따라 동기 또는 비동기 MDN을 생성합니다.
  * MDN에 대한 상관 관계 토큰과 속성을 설정합니다.

  이 작업은 구성될 때 다음 작업도 수행합니다.

  * 서명을 확인합니다.
  * 메시지 암호를 해독합니다.
  * 메시지를 압축 해제합니다.
  * 메시지 ID 중복을 확인하고 허용하지 않습니다.

### <a name="standard"></a>[Standard](#tab/standard)

원래 **AS2** 커넥터의 트리거, 작업 및 제한 버전에 대한 자세한 내용은 커넥터의 Swagger 파일에 설명된 대로 [커넥터의 참조 페이지를](/connectors/as2/) 검토하세요.

---

이 문서에서는 AS2 인코딩 및 디코딩 작업을 기존 논리 앱 워크플로에 추가하는 방법을 보여줍니다. 트리거를 사용하여 워크플로를 시작할 수 있지만 예제에서는 [요청](../connectors/connectors-native-reqres.md) 트리거를 사용합니다.

## <a name="limits"></a>제한

[다중 테넌트 Azure Logic Apps, 단일 테넌트 Azure Logic Apps 또는 ISE(통합 서비스 환경)에서](logic-apps-overview.md#resource-environment-differences)실행되는 워크플로에 대한 AS2 커넥터 제한에 대한 자세한 내용은 [메시지 크기에 대한 B2B 프로토콜 제한을 검토하세요.](logic-apps-limits-and-config.md#b2b-protocol-limits)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 엔터프라이즈 통합 및 B2B 워크플로에서 사용하기 위해 거래 업체, 계약, 인증서 등과 같은 아티팩트 정의 및 저장을 위한 [통합 계정 리소스입니다.](logic-apps-enterprise-integration-create-integration-account.md) 이 리소스는 다음 요구 사항을 충족해야 합니다.

  * 논리 앱 리소스와 동일한 Azure 구독과 연결되어야 합니다.

  * 논리 앱 리소스와 동일한 위치 또는 Azure 지역에 존재합니다.

  * [ **논리 앱(소비)** 리소스 종류와](logic-apps-overview.md#resource-environment-differences) **AS2(v2)** 작업을 사용하는 경우 논리 앱 리소스에 통합 계정에 대한 링크가 필요하지 않습니다. 그러나 AS2, [X12](logic-apps-enterprise-integration-x12.md)또는 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 작업과 함께 파트너, 계약 및 인증서와 같은 아티팩트를 저장하려면 이 계정이 여전히 필요합니다. 통합 계정은 여전히 논리 앱 리소스와 동일한 위치에 있는 동일한 Azure 구독 및 기존 구독을 사용하는 것과 같은 다른 요구 사항을 충족해야 합니다.

  * [ **논리 앱(표준)** 리소스 종류와](logic-apps-overview.md#resource-environment-differences) 원래 **AS2** 작업을 사용하는 경우 AS2 작업을 추가할 때 워크플로에서 직접 만든 통합 계정에 대한 연결이 워크플로에 필요합니다.

* 통합 계정에 두 개 이상의 [거래 업체가](logic-apps-enterprise-integration-partners.md) 있습니다. 두 파트너에 대한 정의는 이 시나리오에서 **AS2Identity인** 동일한 *비즈니스 ID* 한정자를 사용해야 합니다.

* 워크플로에 참여하는 거래 업체 간의 통합 계정의 [AS2 계약입니다.](logic-apps-enterprise-integration-agreements.md) 각 계약에는 호스트 파트너 및 게스트 파트너가 필요합니다.

* AS2 작업을 사용하려는 논리 앱 리소스 및 워크플로입니다.

  > [!NOTE]
  > **AS2(v2)** 커넥터는 트리거가 아닌 작업만 제공합니다. 이 문서에서는 이 커넥터의 예제에서 [요청](../connectors/connectors-native-reqres.md) 트리거를 사용합니다. 원래 **AS2** 커넥터에는 트리거 및 작업이 포함됩니다. 원래 **AS2** 커넥터의 트리거, 작업 및 제한 버전에 대한 자세한 내용은 커넥터의 Swagger 파일에 설명된 대로 [커넥터의 참조 페이지를](/connectors/as2/) 검토하세요.

  논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)를 검토하세요.

* 인증서 관리에 [Azure Key Vault](../key-vault/general/overview.md)를 사용하는 경우 자격 증명 모음 키에서 **암호화** 및 **암호 해독** 작업을 허용하는지 확인합니다. 그렇지 않으면 인코딩 및 디코딩 작업이 실패합니다.

  1. Azure Portal 키 자격 증명 모음을 엽니다. 키 자격 증명 모음 메뉴의 **설정** 아래에서 **키를** 선택합니다.

  1. **키** 창에서 키를 선택합니다. **버전** 창에서 사용 중인 키 버전을 선택합니다.

  1. 키 **버전** 창의 **허용된 작업** 에서 **암호화** 및 **암호 해독** 작업이 선택되어 있는지 확인합니다. 예를 들면 다음과 같습니다.

     !["암호화" 및 "암호 해독" 작업이 선택된 키 자격 증명 모음, 키 및 키 버전 창이 열려 있는 Azure Portal 보여주는 스크린샷.](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>AS2 메시지 인코딩

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 리소스 및 워크플로를 엽니다.

1. 디자이너의 AS2 작업을 추가하려는 트리거 또는 작업 아래에서 **새 단계** 를 선택합니다.

1. **작업 선택** 검색 상자에서 **모두** 를 선택합니다. 검색 상자에 `as2 encode`를 입력합니다. **AS2 인코딩이라는** 작업을 선택합니다.

   ![선택한 Azure Portal, 워크플로 디자이너 및 "AS2 인코딩" 작업을 보여주는 스크린샷](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. AS2 작업이 디자이너에 나타나면 다음 속성에 대한 정보를 제공합니다.

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **인코딩할 메시지** | 예 | 메시지 페이로드 |
   | **AS2 보낸 사람** | 예 | AS2 규약에 지정된 메시지 발신자의 비즈니스 식별자 |
   | **AS2 받는 사람** | 예 | AS2 규약에 지정된 메시지 수신자의 비즈니스 식별자입니다. |
   ||||

   예를 들어 메시지 페이로드는 요청 트리거의 **본문** 콘텐츠 출력입니다.

   ![메시지 인코딩 속성이 있는 "AS2 인코딩" 작업을 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-as2/as2-message-encode-details.png)

   > [!TIP]
   > 서명되거나 암호화된 메시지를 보낼 때 문제가 발생하면 다른 SHA256 알고리즘 형식을 사용해 보세요. AS2 사양은 SHA256 형식에 대한 정보를 제공하지 않으므로 각 공급자는 고유한 구현 또는 형식을 사용합니다.

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 리소스 및 워크플로를 엽니다.

1. 디자이너의 AS2 작업을 추가하려는 트리거 또는 작업 아래에서 **새 단계 삽입(더하기** 기호)을 선택한 다음, 작업 **추가를** 선택합니다.

1. 작업 **선택** 검색 상자에서 **Azure를** 선택합니다. 검색 상자에 `as2 encode`를 입력합니다. **AS2 메시지로 인코딩이라는** 작업을 선택합니다.

   ![선택한 Azure Portal, 워크플로 디자이너 및 "AS2 메시지로 인코딩" 작업을 보여주는 스크린샷](./media/logic-apps-enterprise-integration-as2/select-encode-as2-message.png)

1. 통합 계정에 대한 연결을 만들라는 메시지가 표시되면 다음 정보를 제공합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **연결 이름** | 예 | 연결 이름 |
   | **통합 계정** | 예 | 사용 가능한 통합 계정 목록에서 사용할 계정을 선택합니다. |
   ||||

   예:

   !["AS2 메시지로 인코딩" 연결 창을 보여주는 스크린샷](./media/logic-apps-enterprise-integration-as2/create-as2-encode-connection-standard.png)

1. 완료되면 **만들기** 를 선택합니다.

1. 디자이너에 AS2 세부 정보 창이 나타나면 다음 속성에 대한 정보를 제공합니다.

   | 속성 | 필수 | 설명 |
   |----------|----------|-------------|
   | **인코딩할 메시지** | 예 | 메시지 페이로드 |
   | **AS2 보낸 사람** | 예 | AS2 규약에서 지정한 메시지 발신자의 비즈니스 식별자입니다. |
   | **AS2 받는 사람** | 예 | AS2 규약에 지정된 메시지 수신자의 비즈니스 식별자입니다. |
   ||||

   예를 들어 메시지 페이로드는 요청 트리거의 **본문** 콘텐츠 출력입니다.

   ![메시지 인코딩 속성이 있는 "AS2 인코딩" 작업을 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-as2/encode-as2-message-details.png)

   > [!TIP]
   > 서명되거나 암호화된 메시지를 보낼 때 문제가 발생하면 다른 SHA256 알고리즘 형식을 사용해 보세요. AS2 사양은 SHA256 형식에 대한 정보를 제공하지 않으므로 각 공급자는 고유한 구현 또는 형식을 사용합니다.

---

<a name="decode"></a>

## <a name="decode-as2-messages"></a>AS2 메시지 디코딩

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 리소스 및 워크플로를 엽니다.

1. 디자이너의 AS2 작업을 추가하려는 트리거 또는 작업 아래에서 **새 단계** 를 선택합니다. 이 예제에서는 [요청](../connectors/connectors-native-reqres.md) 트리거를 사용합니다.

1. **작업 선택** 검색 상자에서 **모두** 를 선택합니다. 검색 상자에 `as2 decode`를 입력합니다. **AS2 디코딩이라는** 작업을 선택합니다.

   ![선택한 Azure Portal, 워크플로 디자이너 및 "AS2 디코딩" 작업을 보여주는 스크린샷](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. AS2 작업 셰이프에서 **인코딩할 메시지** 및 이전 트리거 또는 작업 출력의 **메시지 헤더** 속성에 대한 값을 선택합니다.

   이 예제에서는 요청 트리거에서 출력을 선택할 수 있습니다.

   ![요청 트리거에서 "본문" 및 "헤더" 출력이 선택된 Azure Portal, 워크플로 디자이너 및 "AS2 디코딩" 작업을 보여주는 스크린샷](media/logic-apps-enterprise-integration-as2/as2-message-decode-details.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 리소스 및 워크플로를 엽니다.

1. 디자이너의 AS2 작업을 추가하려는 트리거 또는 작업 아래에서 **새 단계 삽입(더하기** 기호)을 선택한 다음, 작업 **추가를** 선택합니다.

1. 작업 **선택** 검색 상자에서 **Azure를** 선택합니다. 검색 상자에 `as2 decode`를 입력합니다. **AS2 메시지 디코딩이라는** 작업을 선택합니다.

   ![선택한 Azure Portal, 워크플로 디자이너 및 "AS2 메시지 디코딩" 작업을 보여주는 스크린샷](./media/logic-apps-enterprise-integration-as2/select-decode-as2-message.png)

1. 통합 계정에 대한 연결을 만들라는 메시지가 표시되면 다음 정보를 제공합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **연결 이름** | 예 | 연결 이름 |
   | **통합 계정** | 예 | 사용 가능한 통합 계정 목록에서 사용할 계정을 선택합니다. |
   ||||

   예:

   !["AS2 메시지 디코딩" 연결 창을 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-as2/create-as2-decode-connection-standard.png)

1. 완료되면 **만들기** 를 선택합니다.

1. AS2 세부 정보 창에서 **인코딩할 메시지** 및 이전 트리거 또는 작업 출력의 **메시지 헤더** 속성에 대한 값을 선택합니다.

   이 예제에서는 요청 트리거에서 출력을 선택할 수 있습니다.

   ![요청 트리거에서 "본문" 및 "헤더" 출력이 선택된 Azure Portal, 워크플로 디자이너 및 "AS2 메시지 디코딩" 작업을 보여주는 스크린샷](media/logic-apps-enterprise-integration-as2/decode-as2-message-details.png)

---

## <a name="sample"></a>샘플

완전히 작동 가능한 논리 앱 및 샘플 AS2(v2) 시나리오를 배포하려면 [AS2(v2) 논리 앱 템플릿 및 시나리오를](https://azure.microsoft.com/resources/templates/logic-app-as2-send-receive/)검토합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps용 다른 커넥터](../connectors/apis-list.md)에 대해 자세히 알아보기
