---
title: Exchange B2B 워크플로의 EDIFACT 메시지
description: Exchange Azure Logic Apps 및 Enterprise 통합 팩을 통해 워크플로를 만들어 파트너 간의 EDIFACT 메시지
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/29/2021
ms.openlocfilehash: 19fea6a0405d1fcc4cfbc9b1aa7647c52b4459b6
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401561"
---
# <a name="exchange-edifact-messages-using-workflows-in-azure-logic-apps"></a>Exchange Azure Logic Apps 워크플로를 사용하는 EDIFACT 메시지

Azure Logic Apps 사용하여 만든 워크플로에서 EDIFACT 메시지를 보내고 받으려면 EDIFACT 통신을 지원하고 관리하는 트리거 및 작업을 제공하는 **EDIFACT** 커넥터를 사용합니다.

이 문서에서는 EDIFACT 인코딩 및 디코딩 작업을 기존 논리 앱 워크플로에 추가하는 방법을 보여줍니다. 트리거를 사용하여 워크플로를 시작할 수 있지만 예제에서는 [요청](../connectors/connectors-native-reqres.md) 트리거를 사용합니다. **EDIFACT** 커넥터의 트리거, 작업 및 제한 버전에 대한 자세한 내용은 커넥터의 Swagger 파일에 설명된 대로 [커넥터의 참조 페이지를](/connectors/edifact/) 검토하세요.

![메시지 디코딩 속성이 있는 "EDIFACT 메시지 디코딩" 작업을 보여주는 개요 스크린샷](./media/logic-apps-enterprise-integration-edifact/overview-edifact-message-consumption.png)

## <a name="edifact-encoding-and-decoding"></a>EDIFACT 인코딩 및 디코딩

다음 섹션에서는 EDIFACT 인코딩 및 디코딩 작업을 사용하여 완료할 수 있는 작업에 대해 설명합니다.

### <a name="encode-to-edifact-message-action"></a>EDIFACT 메시지 작업으로 인코딩

* 보낸 사람 한정자 & 식별자와 받는 사람 한정자 및 식별자를 일치시켜 규약을 해결합니다.

* XML로 인코딩된 메시지를 교환의 EDI 트랜잭션 집합으로 변환하는 EDI(Electronic Data Interchange)를 직렬화합니다.

* 트랜잭션 집합 헤더 및 트레일러 세그먼트를 적용합니다.

* 보내는 각 교환에 대해 교환 컨트롤 번호, 그룹 컨트롤 번호 및 트랜잭션 집합 컨트롤 번호를 생성합니다.

* 페이로드 데이터의 구분 기호를 대체합니다.

* 메시지 스키마에 대한 트랜잭션 집합 데이터 요소의 스키마, 트랜잭션 집합 데이터 요소 및 트랜잭션 집합 데이터 요소에 대한 확장 유효성 검사와 같은 EDI 및 파트너별 속성의 유효성을 검사합니다.

* 각 트랜잭션 집합에 대한 XML 문서를 생성합니다.

* 기술 승인, 기능 승인 또는 둘 다 요청합니다(구성된 경우).

  * 기술 승인으로 CONTRL 메시지는 교환 수신을 나타냅니다.

  * 기능 승인으로 CONTRL 메시지는 오류 또는 지원되지 않는 기능 목록을 포함하여 수신된 교환, 그룹 또는 메시지에 대한 동의 또는 거부를 나타냅니다.

### <a name="decode-edifact-message-action"></a>EDIFACT 메시지 디코딩 작업

* 거래 업체 계약에 대해 봉투의 유효성을 검사합니다.

* 보낸 사람 한정자 및 식별자와 받는 사람 한정자 및 식별자를 일치시켜 규약을 해결합니다.

* 계약의 **Receive 설정** 에 따라 교환에 트랜잭션이 두 개 이상 있는 경우 교환을 여러 트랜잭션 집합으로 분할합니다.

* 교환을 디스어셈블합니다.

* 교환 봉투 구조, 컨트롤 스키마에 대한 봉투 스키마, 메시지 스키마에 대한 트랜잭션 집합 데이터 요소의 스키마 및 트랜잭션 집합 데이터 요소에 대한 확장 유효성 검사와 같은 EDI(Electronic Data Interchange) 및 파트너별 속성의 유효성을 검사합니다.

* 교환, 그룹 및 트랜잭션 집합 컨트롤 번호가 중복되지 않는지 확인합니다(구성된 경우). 예를 들면 다음과 같습니다.

  * 이전에 받은 교환을 기준으로 교환 컨트롤 번호를 확인합니다.

  * 교환의 다른 그룹 컨트롤 번호에 대해 그룹 컨트롤 번호를 확인합니다.

  * 해당 그룹의 다른 트랜잭션 집합 컨트롤 번호에 대해 트랜잭션 집합 컨트롤 번호를 확인합니다.

* 교환을 트랜잭션 집합으로 분할하거나 전체 교환을 유지합니다. 예를 들면 다음과 같습니다.

  * 교환을 트랜잭션 집합으로 분할 - 오류 발생 시에 트랜잭션 집합을 일시 중단합니다.

    디코딩 작업은 교환을 트랜잭션 집합으로 분할하고 각 트랜잭션 집합을 구문 분석합니다. 작업은 유효성 검사에 실패한 트랜잭션 집합만 에 `badMessages` 출력하고 나머지 트랜잭션 집합을 로 출력합니다. `goodMessages`

  * 교환을 트랜잭션 집합으로 분할 - 오류 발생 후 교환을 일시 중단합니다.

    디코딩 작업은 교환을 트랜잭션 집합으로 분할하고 각 트랜잭션 집합을 구문 분석합니다. 교환에 있는 하나 이상의 트랜잭션 집합이 유효성 검사에 실패하면 작업은 해당 교환의 모든 트랜잭션 집합을 `badMessages` 로 출력합니다.

  * 교환 유지 - 오류 발생 시에 트랜잭션 집합을 일시 중단합니다.

    디코딩 작업은 교환을 유지하고 일괄 처리된 전체 교환을 처리합니다. 작업은 유효성 검사에 실패한 트랜잭션 집합만 에 `badMessages` 출력하고 나머지 트랜잭션 집합을 로 출력합니다. `goodMessages`

  * 교환 유지 - 오류 발생 시에 교환을 일시 중단합니다.

    디코딩 작업은 교환을 유지하고 일괄 처리된 전체 교환을 처리합니다. 교환에 있는 하나 이상의 트랜잭션 집합이 유효성 검사에 실패하면 작업은 해당 교환의 모든 트랜잭션 집합을 `badMessages` 로 출력합니다.

* 기술 승인, 기능 승인 또는 둘 다(구성된 경우)를 생성합니다.

  * 기술 승인 또는 CONTRL ACK는 받은 교환의 전체 구문 검사 결과를 보고합니다.

  * 받은 교환 또는 그룹에 대한 동의 또는 거부를 승인하는 기능 승인입니다.

## <a name="connector-reference"></a>커넥터 참조

**EDIFACT** 커넥터에 대한 기술 정보는 [커넥터의 Swagger](/connectors/edifact/)파일에 설명된 대로 트리거, 작업 및 제한을 설명하는 커넥터의 참조 페이지 를 검토하세요. 또한 [다중 테넌트 Azure Logic Apps, 단일 테넌트 Azure Logic Apps 또는 ISE(통합 서비스 환경)에서](logic-apps-overview.md#resource-environment-differences)실행되는 워크플로의 메시지 크기에 대한 [B2B 프로토콜 제한을](logic-apps-limits-and-config.md#b2b-protocol-limits) 검토합니다. 예를 들어 [ISE(통합 서비스 환경)에서](connect-virtual-network-vnet-isolated-environment-overview.md)이 커넥터의 ISE 버전은 [ISE에 대한 B2B 메시지 제한을](logic-apps-limits-and-config.md#b2b-protocol-limits)사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 엔터프라이즈 통합 및 B2B 워크플로에서 사용하기 위해 거래 업체, 계약, 인증서 등과 같은 아티팩트 정의 및 저장을 위한 [통합 계정 리소스입니다.](logic-apps-enterprise-integration-create-integration-account.md) 이 리소스는 다음 요구 사항을 충족해야 합니다.

  * 논리 앱 리소스와 동일한 Azure 구독과 연결되어야 합니다.

  * 논리 앱 리소스와 동일한 위치 또는 Azure 지역에 존재합니다.

  * [ **논리 앱(소비)** 리소스 종류](logic-apps-overview.md#resource-environment-differences) 및 **EDIFACT** 작업을 사용하는 경우 논리 앱 리소스에 통합 계정에 대한 링크가 필요하지 않습니다. 그러나 EDIFACT, [X12](logic-apps-enterprise-integration-x12.md)또는 [AS2](logic-apps-enterprise-integration-as2.md) 작업과 함께 파트너, 계약 및 인증서와 같은 아티팩트를 저장하려면 이 계정이 여전히 필요합니다. 통합 계정은 여전히 논리 앱 리소스와 동일한 위치에 있는 동일한 Azure 구독 및 기존 구독을 사용하는 것과 같은 다른 요구 사항을 충족해야 합니다.

  * [ **논리 앱(표준)** 리소스 종류](logic-apps-overview.md#resource-environment-differences) 및 **EDIFACT** 작업을 사용하는 경우 AS2 작업을 추가할 때 워크플로에서 직접 만든 통합 계정에 대한 연결이 워크플로에 필요합니다.

* 통합 계정에 두 개 이상의 [거래 업체가](logic-apps-enterprise-integration-partners.md) 있습니다. 두 파트너에 대한 정의는 이 시나리오에 대해 **ZZZ - 상호 정의된** 동일한 *비즈니스 ID* 한정자를 사용해야 합니다.

* 워크플로에 참여하는 거래 업체 간 통합 계정의 [EDIFACT 계약입니다.](logic-apps-enterprise-integration-agreements.md) 각 계약에는 호스트 파트너 및 게스트 파트너가 필요합니다. 사용자와 다른 파트너 간의 메시지 콘텐츠는 계약 유형과 일치해야 합니다.

  > [!IMPORTANT]
  > EDIFACT 커넥터는 UTF-8 문자만 지원합니다. 출력에 예기치 않은 문자가 포함되어 있는 경우 EDIFACT 메시지에서 UTF-8 문자 집합을 사용하는지 확인합니다.

* EDIFACT 작업을 사용하려는 논리 앱 리소스 및 워크플로입니다.

  논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)를 검토하세요.

<a name="encode"></a>

## <a name="encode-edifact-messages"></a>EDIFACT 메시지 인코딩

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 리소스 및 워크플로를 엽니다.

1. 디자이너의 EDIFACT 작업을 추가하려는 트리거 또는 작업 아래에서 **새 단계** 를 선택합니다.

1. **작업 선택** 검색 상자에서 **모두** 를 선택합니다. 검색 상자에 `edifact encode`를 입력합니다. 이 예제에서는 **규약 이름으로 EDIFACT 메시지로 인코딩이라는** 작업을 선택합니다.

   ![선택한 Azure Portal, 워크플로 디자이너 및 "규약 이름으로 EDIFACT 메시지에 인코딩" 작업을 보여주는 스크린샷](./media/logic-apps-enterprise-integration-edifact/select-encode-edifact-message-consumption.png)

   > [!NOTE]
   > **대신 ID로 EDIFACT 메시지 인코딩** 작업을 선택하도록 선택할 수 있지만 나중에 EDIFACT 규약에 지정된 **보낸 사람 식별자** 및 받는 사람 **식별자와** 같은 다른 값을 제공해야 합니다. 트리거 또는 이전 동작의 출력일 수 있는 **인코딩할 XML 메시지도** 지정해야 합니다.

1. 통합 계정에 대한 연결을 만들라는 메시지가 표시되면 다음 정보를 제공합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **연결 이름** | 예 | 연결 이름 |
   | **통합 계정** | 예 | 사용 가능한 통합 계정 목록에서 사용할 계정을 선택합니다. |
   ||||

   예:

   !["규약 이름으로 EDIFACT 메시지 인코딩" 연결 창을 보여주는 스크린샷](./media/logic-apps-enterprise-integration-edifact/create-edifact-encode-connection-consumption.png)

1. 완료되면 **만들기** 를 선택합니다.

1. 디자이너에 EDIFACT 작업이 나타나면 이 작업과 관련한 다음 속성에 대한 정보를 제공합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **EDIFACT 규약의 이름** | 예 | 사용할 EDIFACT 계약입니다. |
   | **인코딩할 XML 메시지** | 예 | EDIFACT 규약에 지정된 메시지 발신자의 비즈니스 식별자입니다. |
   | 다른 매개 변수 | 아니요 | 이 작업에는 다음과 같은 다른 매개 변수가 포함됩니다. <p>- **데이터 요소 구분 기호** <br>- **릴리스 표시기** <br>- **구성 요소 구분 기호** <br>- **반복 구분 기호** <br>- **세그먼트 종결자** <br>- **세그먼트 종결자 접미사** <br>- **10진수 표시기** <p>자세한 내용은 [EDIFACT 메시지 설정 를](logic-apps-enterprise-integration-edifact-message-settings.md)검토하세요. |
   ||||

   예를 들어 XML 메시지 페이로드는 요청 트리거의 **본문** 콘텐츠 출력일 수 있습니다.

   ![메시지 인코딩 속성이 있는 "규약 이름으로 EDIFACT 메시지 인코딩" 작업을 보여주는 스크린샷](./media/logic-apps-enterprise-integration-edifact/encode-edifact-message-agreement-consumption.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 리소스 및 워크플로를 엽니다.

1. 디자이너의 EDIFACT 작업을 추가하려는 트리거 또는 작업 아래에서 **새 단계 삽입(더하기** 기호)을 선택한 다음, 작업 **추가를** 선택합니다.

1. 작업 선택 검색 **상자에서** **Azure를** 선택합니다. 검색 상자에 `edifact encode`를 입력합니다. 규약 **이름으로 EDIFACT 메시지 인코딩이라는** 작업을 선택합니다.

   ![선택한 Azure Portal, 워크플로 디자이너 및 "규약 이름으로 EDIFACT 메시지에 인코딩" 작업을 보여주는 스크린샷](./media/logic-apps-enterprise-integration-edifact/select-encode-edifact-message-standard.png)

   > [!NOTE]
   > **대신 ID로 EDIFACT 메시지 인코딩** 작업을 선택하도록 선택할 수 있지만 나중에 EDIFACT 규약에 지정된 **보낸 사람 식별자** 및 받는 사람 **식별자와** 같은 다른 값을 제공해야 합니다. **인코딩할 XML 메시지도** 지정해야 합니다. 이 메시지는 트리거 또는 이전 동작의 출력일 수 있습니다.

1. 통합 계정에 대한 연결을 만들라는 메시지가 표시되면 다음 정보를 제공합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **연결 이름** | 예 | 연결 이름 |
   | **통합 계정** | 예 | 사용 가능한 통합 계정 목록에서 사용할 계정을 선택합니다. |
   ||||

   예:

   !["매개 변수 이름으로 EDIFACT 메시지 인코딩" 연결 창을 보여주는 스크린샷](./media/logic-apps-enterprise-integration-edifact/create-edifact-encode-connection-standard.png)

1. 완료되면 **만들기** 를 선택합니다.

1. 디자이너에 EDIFACT 세부 정보 창이 나타나면 다음 속성에 대한 정보를 제공합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **EDIFACT 규약의 이름** | 예 | 사용할 EDIFACT 계약입니다. |
   | **인코딩할 XML 메시지** | 예 | EDIFACT 규약에 지정된 메시지 발신자의 비즈니스 식별자입니다. |
   | 다른 매개 변수 | 아니요 | 이 작업에는 다음과 같은 다른 매개 변수가 포함됩니다. <p>- **데이터 요소 구분 기호** <br>- **릴리스 표시기** <br>- **구성 요소 구분 기호** <br>- **반복 구분 기호** <br>- **세그먼트 종결자** <br>- **세그먼트 종결자 접미사** <br>- **10진수 표시기** <p>자세한 내용은 [EDIFACT 메시지 설정 를](logic-apps-enterprise-integration-edifact-message-settings.md)검토하세요. |
   ||||

   예를 들어 메시지 페이로드는 요청 트리거의 **본문** 콘텐츠 출력입니다.

   ![메시지 인코딩 속성을 사용하여 "매개 변수 이름으로 EDIFACT 메시지 인코딩" 작업을 보여주는 스크린샷](./media/logic-apps-enterprise-integration-edifact/encode-edifact-message-agreement-standard.png)

---

<a name="decode"></a>

## <a name="decode-edifact-messages"></a>EDIFACT 메시지 디코딩

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 리소스 및 워크플로를 엽니다.

1. 디자이너의 EDIFACT 작업을 추가하려는 트리거 또는 작업 아래에서 **새 단계** 를 선택합니다.

1. **작업 선택** 검색 상자에서 **모두** 를 선택합니다. 검색 상자에 `edifact encode`를 입력합니다. **EDIFACT 메시지 디코딩이라는** 작업을 선택합니다.

1. 통합 계정에 대한 연결을 만들라는 메시지가 표시되면 다음 정보를 제공합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **연결 이름** | 예 | 연결 이름 |
   | **통합 계정** | 예 | 사용 가능한 통합 계정 목록에서 사용할 계정을 선택합니다. |
   ||||

   예:

   !["EDIFACT 메시지 디코딩" 연결 창을 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-edifact/create-edifact-decode-connection-consumption.png)

1. 완료되면 **만들기** 를 선택합니다.

1. 디자이너에 EDIFACT 작업이 나타나면 이 작업과 관련한 다음 속성에 대한 정보를 제공합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **디코딩할 EDIFACT 플랫 파일 메시지** | 예 | 디코딩할 XML 플랫 파일 메시지입니다. |
   | 다른 매개 변수 | 아니요 | 이 작업에는 다음과 같은 다른 매개 변수가 포함됩니다. <p>- **구성 요소 구분 기호** <br>- **데이터 요소 구분 기호** <br>- **릴리스 표시기** <br>- **반복 구분 기호** <br>- **세그먼트 종결자** <br>- **세그먼트 종결자 접미사** <br>- **10진수 표시기** <br>- **페이로드 문자 집합** <br>- **세그먼트 마침 표시 접미사** <br>- **교환 유지** <br>- **오류 발생 시 교환 일시 중단** <p>자세한 내용은 [EDIFACT 메시지 설정](logic-apps-enterprise-integration-edifact-message-settings.md)을 검토 합니다. |
   ||||

   예를 들어 디코딩할 XML 메시지 페이로드는 요청 트리거의 **본문** 내용 출력 일 수 있습니다.

   ![메시지 디코딩 속성이 있는 "EDIFACT 메시지 디코딩" 작업을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-edifact/decode-edifact-message-consumption.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure Portal](https://portal.azure.com)에서 디자이너의 논리 앱 리소스 및 워크플로를 엽니다.

1. 디자이너의 EDIFACT 작업을 추가 하려는 트리거 또는 작업에서 **새 단계 삽입** (더하기 기호)을 선택 하 고 **작업 추가** 를 선택 합니다.

1. **작업 선택** 검색 상자에서 **Azure** 를 선택 합니다. 검색 상자에 `edifact encode`를 입력합니다. **EDIFACT 메시지 디코딩** 이라는 동작을 선택 합니다.

   ![Azure Portal, 워크플로 디자이너 및 "EDIFACT 메시지 디코딩" 작업을 선택 하 여 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-edifact/select-decode-edifact-message-standard.png)

1. 통합 계정에 대 한 연결을 만들지 묻는 메시지가 표시 되 면 다음 정보를 제공 합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **연결 이름** | 예 | 연결의 이름입니다. |
   | **통합 계정** | 예 | 사용할 수 있는 통합 계정 목록에서 사용할 계정을 선택 합니다. |
   ||||

   예:

   !["EDIFACT 메시지 디코딩" 연결 창을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-edifact/create-edifact-decode-connection-standard.png)

1. 완료되면 **만들기** 를 선택합니다.

1. 디자이너에 EDIFACT 세부 정보 창이 표시 되 면 다음 속성에 대 한 정보를 제공 합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **EDIFACT 규약의 이름** | 예 | 사용할 EDIFACT 규약입니다. |
   | **인코딩할 XML 메시지입니다.** | 예 | EDIFACT 규약에서 지정한 메시지 보낸 사람에 대 한 비즈니스 식별자입니다. |
   | 다른 매개 변수 | 아니요 | 이 작업에는 다음과 같은 기타 매개 변수가 포함 됩니다. <p>- **데이터 요소 구분 기호** <br>- **릴리스 표시기** <br>- **구성 요소 구분 기호** <br>- **반복 구분 기호** <br>- **세그먼트 마침 표시** <br>- **세그먼트 마침 표시 접미사** <br>- **소수 표시기** <p>자세한 내용은 [EDIFACT 메시지 설정](logic-apps-enterprise-integration-edifact-message-settings.md)을 검토 합니다. |
   ||||

   예를 들어 메시지 페이로드는 요청 트리거의 **본문** 내용 출력입니다.

   ![메시지 디코딩 속성이 있는 "EDIFACT 메시지 디코딩" 작업을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-edifact/decode-edifact-message-standard.png)

---

## <a name="handle-unh25-segments-in-edifact-documents"></a>EDIFACT 문서에서 UNH 2.5 세그먼트를 처리 합니다.

EDIFACT 문서에서 [Unh 2.5 세그먼트](logic-apps-enterprise-integration-edifact-message-settings.md#receive-settings-schemas) 는 스키마 조회에 사용 됩니다. 예를 들어 이 샘플 EDIFACT 메시지에서 UNH 필드는 `EAN008`입니다.

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

EDIFACT 문서를 처리 하거나 2.5 개의 세그먼트가 있는 EDIFACT 메시지를 처리 하려면 다음 단계를 수행 합니다.

1. UNH 2.5 루트 노드 이름이 있는 스키마를 업데이트 하거나 배포 합니다.

   예를 들어 샘플 UNH 필드의 스키마 루트 이름은 `EFACT_D03B_ORDERS_EAN008` 입니다. UNH2.5 세그먼트가 다른 각각의 `D03B_ORDERS`에 대해 개별 스키마를 배포해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **논리 앱 (소비)** 또는 **논리 앱 (표준)** 리소스 유형으로 작업 하 고 있는지 여부에 따라 통합 계정 리소스 또는 논리 앱 리소스에 스키마를 추가 합니다.

1. edifact 디코딩 또는 인코딩 작업을 사용 하 고 있는지 여부에 관계 없이 스키마를 업로드 하 고 EDIFACT 규약의 **수신 설정** 에서 스키마 설정을 설정 하거나 설정 섹션을 **보냅니다** .

1. EDIFACT 규약을 편집 하려면 규약 창에서 규약을 **선택 합니다.** **규약** 창의 도구 모음에서 **JSON으로 편집** 을 선택 합니다.

   * 규약 `receiveAgreement` 섹션에서 `schemaReferences` 섹션을 찾고 unh 2.5 값을 추가 합니다.

     ![JSON 편집기에서 EDIFACT 규약의 "receiveAgreement" 섹션이 있는 Azure Portal를 보여 주는 스크린샷 및 "schemaReferences" 섹션이 강조 표시 됩니다.](./media/logic-apps-enterprise-integration-edifact/agreement-receive-schema-references.png)

   * 규약 `sendAgreement` 섹션에서 `schemaReferences` 섹션을 찾고 unh 2.5 값을 추가 합니다.

     ![JSON 편집기에서 EDIFACT 규약의 "sendAgreement" 섹션이 있는 Azure Portal을 보여 주는 스크린샷 및 "schemaReferences" 섹션이 강조 표시 됩니다.](./media/logic-apps-enterprise-integration-edifact/agreement-send-schema-references.png)

## <a name="next-steps"></a>다음 단계

* [EDIFACT 메시지 설정](logic-apps-enterprise-integration-edifact-message-settings.md)
