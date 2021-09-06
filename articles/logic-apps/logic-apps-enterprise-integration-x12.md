---
title: B2B 통합을 위한 X12 메시지 교환
description: Azure Logic Apps 및 엔터프라이즈 통합 팩으로 B2B 엔터프라이즈 통합 솔루션을 빌드할 때 X12 메시지를 주고받으며 처리합니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, divswa, azla
ms.topic: how-to
ms.date: 07/16/2021
ms.openlocfilehash: 5328fad1530ee8dd7b4a2c79581d443488c44b28
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453844"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-using-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps과 엔터프라이즈 통합 팩을 사용하여 B2B 엔터프라이즈 통합용 X12 메시지 교환

Azure Logic Apps에서는 **X12** 작업을 통해 x12 메시지를 사용하는 워크플로를 만들 수 있습니다. 이러한 작업에는 워크플로에서 X12 통신을 처리하는 데 사용할 수 있는 트리거와 작업이 포함됩니다. 워크플로의 다른 트리거 및 작업과 동일한 방식으로 X12 트리거와 작업을 추가할 수 있지만, X12 작업을 사용하려면 추가 필수 구성 요소를 충족해야 합니다.

이 문서에서는 워크플로에서 X12 트리거와 작업을 사용하기 위한 요구 사항 및 설정을 설명합니다. 대신 EDIFACT 메시지를 찾고 있다면 [Exchange EDIFACT 메시지](logic-apps-enterprise-integration-edifact.md)를 검토합니다. 논리 앱을 처음 사용하는 경우 [Azure Logic Apps란](logic-apps-overview.md)과 [빠른 시작: 다중 테넌트 Azure Logic Apps와 Azure Portal을 사용하여 통합 워크플로 만들기](quickstart-create-first-logic-app-workflow.md)를 검토하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* X12 트리거 또는 작업을 사용하려는 논리 앱 리소스와 워크플로. X12 트리거를 사용하려면 빈 워크플로가 필요합니다. X12 작업을 사용하려면 기존 트리거가 있는 워크플로가 필요합니다.

* 논리 앱 리소스에 연결된 [통합 계정](logic-apps-enterprise-integration-create-integration-account.md). 논리 앱과 통합 계정 모두 동일한 Azure 구독을 사용하고 동일한 Azure 지역 또는 위치에 존재해야 합니다.

  통합 계정에는 다음과 같은 B2B 아티팩트도 포함되어야 합니다.

  * X12 ID 한정자를 사용하는 두 개 이상의 [거래 업체](logic-apps-enterprise-integration-partners.md).

  * 거래 업체 간에 정의된 X12 [규약](logic-apps-enterprise-integration-agreements.md). 메시지를 주고받을 때 사용할 설정에 대한 자세한 내용은 [설정 받기](#receive-settings)와 [설정 보내기](#send-settings)를 검토하세요.

    > [!IMPORTANT]
    > HIPAA(Health Insurance Portability and Accountability Act) 스키마를 사용하는 경우 `schemaReferences` 섹션을 규약에 추가해야 합니다. 자세한 내용은 [HIPAA 스키마 및 메시지 유형](#hipaa-schemas)을 검토하세요.

  * XML 유효성 검사에 사용할 [스키마](logic-apps-enterprise-integration-schemas.md).

    > [!IMPORTANT]
    > HIPAA(Health Insurance Portability and Accountability Act) 스키마를 사용하는 경우 [HIPAA 스키마 및 메시지 유형](#hipaa-schemas)을 검토해야 합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에 설명된 트리거, 작업, 제한 등 이 커넥터에 대한 자세한 기술 정보는 [커넥터의 참조 페이지](/connectors/x12/)를 참조하세요.

> [!NOTE]
> 이 커넥터의 ISE 레이블이 지정된 버전은 [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)의 논리 앱에는 [ISE에 대한 B2B 메시지 제한](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)을 사용합니다.

<a name="receive-settings"></a>

## <a name="receive-settings"></a>수신 설정

거래 업체 규약에 속성을 설정하면 이 규약을 통해 파트너로부터 받는 인바운드 메시지를 이 규약에서 식별하고 처리하는 방법을 구성할 수 있습니다.

1. **추가** 아래에서 **수신 설정** 을 선택합니다.

1. 메시지를 교환하는 파트너와의 규약에 따라 **수신 설정** 창에서 속성을 설정합니다. 이 창은 다음 섹션으로 구성되어 있습니다.

   * [식별자](#inbound-identifiers)
   * [승인](#inbound-acknowledgement)
   * [스키마](#inbound-schemas)
   * [봉투](#inbound-envelopes)
   * [컨트롤 번호](#inbound-control-numbers)
   * [Validations](#inbound-validations)
   * [내부 설정](#inbound-internal-settings)

1. 모두 마쳤으면 **확인** 을 선택하여 설정을 저장합니다.

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>수신 설정 - 식별자

![인바운드 메시지의 식별자 속성](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| 속성 | Description |
|----------|-------------|
| **ISA1(권한 부여 한정자)** | 사용할 권한 부여 한정자 값입니다. 기본값은 **00 - 권한 부여 정보 없음** 입니다. <p>**참고**: 다른 값을 선택하는 경우 **ISA2** 속성의 값을 지정해야 합니다. |
| **ISA2** | **ISA1** 속성이 **00 - 권한 부여 정보 없음** 이 아닌 경우에 사용할 권한 부여 정보 값입니다. 이 속성 값은 1개 이상 10개 이하의 영숫자 문자를 포함해야 합니다. |
| **ISA3(보안 한정자)** | 사용할 보안 한정자 값입니다. 기본값은 **00 - 보안 정보 없음** 입니다. <p>**참고**: 다른 값을 선택하는 경우 **ISA4** 속성의 값을 지정해야 합니다. |
| **ISA4** | **ISA3** 속성이 **00 - 보안 정보 없음** 이 아닌 경우에 사용할 보안 정보 값입니다. 이 속성 값은 1개 이상 10개 이하의 영숫자 문자를 포함해야 합니다. |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>수신 설정 - 승인

![인바운드 메시지 승인](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| 속성 | Description |
|----------|-------------|
| **TA1이 예상됨** | 교환 보낸 사람에게 기술 승인(TA1)을 반환합니다. |
| **FA가 예상됨** | 교환 보낸 사람에게 기능 승인(FA)을 반환합니다. <p>**FA Version** 속성의 경우 스키마 버전에 따라 997 또는 999 승인을 선택합니다. <p>수락된 트랜잭션 세트의 기능 승인에서 AK2 루프를 생성할 수 있도록 설정하려면 **AK2/IK2 루프 포함** 을 선택합니다. |

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>수신 설정 - 스키마

![인바운드 메시지의 스키마](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

이 섹션에서는 각 트랜잭션 유형(ST01) 및 발신자 애플리케이션(GS02)의 [통합 계정](./logic-apps-enterprise-integration-create-integration-account.md)에서 [스키마](../logic-apps/logic-apps-enterprise-integration-schemas.md)를 선택합니다. EDI 수신 파이프라인은 이 섹션에서 설정한 값과 스키마를 들어오는 메시지의 ST01 및 GS02 값 그리고 들어오는 메시지의 스키마와 매칭하여 들어오는 메시지를 디스어셈블합니다. 각 행을 완료하면 비어 있는 새 행이 자동으로 표시됩니다.

| 속성 | Description |
|----------|-------------|
| **버전** | 스키마의 X12 버전 |
| **트랜잭션 유형(ST01)** | 트랜잭션 유형 |
| **발신자 애플리케이션(GS02)** | 발신자 애플리케이션 |
| **스키마** | 사용하려는 스키마 파일 |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>수신 설정 - 봉투

![인바운드 메시지의 트랜잭션 세트에 사용할 구분 기호](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| 속성 | Description |
|----------|-------------|
| **ISA11 사용** | 트랜잭션 세트에 사용할 구분 기호: <p>- **표준 식별자**: EDI 수신 파이프라인에서 들어오는 문서의 소수 표기법 대신 소수 표기법에 마침표(.)를 사용합니다. <p>- **반복 구분 기호**: 단순 데이터 요소 또는 반복되는 데이터 구조의 반복 발생에 대한 구분 기호를 지정합니다. 예를 들어 일반적으로 캐럿(^)을 반복 구분 기호로 사용합니다. HIPAA 스키마의 경우 캐럿만 사용할 수 있습니다. |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>수신 설정 - 컨트롤 번호

![인바운드 메시지에 대한 컨트롤 번호 중복 처리](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| 속성 | Description |
|----------|-------------|
| **교환 컨트롤 번호 중복 허용 안 함** | 중복 교환을 차단합니다. 받은 교환 컨트롤 번호의 교환 컨트롤 번호(ISA13)를 확인합니다. 일치하는 항목이 발견되면 EDI 수신 파이프라인은 교환을 처리하지 않습니다. <p><p>확인 작업을 수행할 일 수를 지정하려면 **매일 중복 ISA13 확인** 의 값을 입력합니다. |
| **그룹 컨트롤 번호 중복 허용 안 함** | 그룹 컨트롤 번호가 중복된 교환을 차단합니다. |
| **트랜잭션 세트 컨트롤 번호 중복 허용 안 함** | 트랜잭션 세트 컨트롤 번호가 중복된 교환을 차단합니다. |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>수신 설정 - 유효성 검사

![인바운드 메시지의 유효성 검사](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

**기본값** 행은 EDI 메시지 유형에 사용되는 유효성 검사 규칙을 보여줍니다. 다른 규칙을 정의하려면 규칙을 **true** 로 설정하려는 각 상자를 선택합니다. 각 행을 완료하면 비어 있는 새 행이 자동으로 표시됩니다.

| 속성 | Description |
|----------|-------------|
| **메시지 유형** | EDI 메시지 유형 |
| **EDI 유효성 검사** | 스키마의 EDI 속성, 길이 제한, 빈 데이터 요소 및 후행 구분 기호로 정의되는 데이터 형식에 대해 EDI 유효성 검사를 수행합니다. |
| **확장 유효성 검사** | 데이터 형식이 EDI가 아니면 데이터 요소 요구 사항 및 허용되는 반복, 열거, 데이터 요소 길이 유효성 검사(최솟값 또는 최댓값)에 대한 유효성 검사가 수행됩니다. |
| **선행/후행 0 허용** | 추가되는 선행 또는 후행 0과 공백 문자를 보존합니다. 이러한 문자를 제거하지 마십시오. |
| **선행/후행 0 자르기** | 선행 또는 후행 0과 공백 문자를 제거합니다. |
| **후행 구분 기호 정책** | 후행 구분 기호를 생성합니다. <p>- **허용되지 않음**: 인바운드 교환에서 후행 구분 기호를 금지합니다. 이 경우 교환에 후행 구분 기호가 있으면 잘못된 것으로 선언됩니다. <p>- **선택 사항**: 후행 구분 기호의 존재 여부에 관계없이 교환을 수락합니다. <p>- **필수**: 인바운드 교환에 후행 구분 기호가 있어야 합니다. |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>수신 설정 - 내부 설정

![인바운드 메시지에 대한 내부 설정](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| 속성 | Description |
|----------|-------------|
| **10진수 형식 Nn을 밑수 10 숫자 값으로 변환** | "Nn" 형식으로 지정된 EDI 번호를 밑수가 10인 숫자 값으로 변환합니다. |
| **후행 구분 기호가 허용되는 경우 빈 XML 태그 만들기** | 교환 발신자가 후행 구분 기호에 대한 빈 XML 태그를 포함하게 합니다. |
| **교환을 트랜잭션 세트로 분할 - 오류 발생 시 트랜잭션 세트 일시 중단** | 적절한 봉투를 트랜잭션 세트에 적용하여 교환의 각 트랜잭션 세트를 별도의 XML 문서로 구문 분석합니다. 유효성 검사에 실패한 트랜잭션만 일시 중단합니다. |
| **교환을 트랜잭션 세트로 분할 - 오류 발생 시 교환 일시 중단** | 적절한 봉투를 적용하여 교환의 각 트랜잭션 세트를 별도의 XML 문서로 구문 분석합니다. 교환에 포함된 하나 이상의 트랜잭션 집합에 대한 유효성 검사가 실패하는 경우 전체 교환을 일시 중단합니다. |
| **교환 유지 - 오류 발생 시 트랜잭션 세트 일시 중단** | 교환을 그대로 유지하고 일괄 처리된 교환 전체에 대한 XML 문서를 만듭니다. 유효성 검사를 실패하는 트랜잭션 세트만 일시 중단하고, 그 외의 트랜잭션 세트를 계속 처리합니다. |
| **교환 유지 - 오류 발생 시 교환 일시 중단** |교환을 그대로 유지하고 배치로 처리된 교환 전체에 대한 XML 문서를 만듭니다. 교환에 포함된 하나 이상의 트랜잭션 집합에 대한 유효성 검사가 실패하는 경우 전체 교환을 일시 중단합니다. |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>송신 설정

규약 속성을 설정한 후에는 이 규약을 통해 파트너에게 보내는 아웃바운드 메시지를 이 규약에서 어떻게 식별하고 처리할 것인지 구성할 수 있습니다.

1. **추가** 아래에서 **송신 설정** 을 선택합니다.

1. 사용자와 메시지를 교환하는 파트너와의 규약에 따라 이러한 속성을 구성합니다. 속성 설명은 이 섹션에 있는 테이블을 참조하세요.

   **송신 설정** 은 다음 섹션으로 구성됩니다.

   * [식별자](#outbound-identifiers)
   * [승인](#outbound-acknowledgement)
   * [스키마](#outbound-schemas)
   * [봉투](#outbound-envelopes)
   * [컨트롤 버전 번호](#outbound-control-version-number)
   * [컨트롤 번호](#outbound-control-numbers)
   * [문자 세트 및 구분 기호](#outbound-character-sets-separators)
   * [유효성 검사](#outbound-validation)

1. 모두 마쳤으면 **확인** 을 선택하여 설정을 저장합니다.

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>송신 설정 - 식별자

![아웃바운드 메시지의 식별자 속성](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| 속성 | Description |
|----------|-------------|
| **ISA1(권한 부여 한정자)** | 사용할 권한 부여 한정자 값입니다. 기본값은 **00 - 권한 부여 정보 없음** 입니다. <p>**참고**: 다른 값을 선택하는 경우 **ISA2** 속성의 값을 지정해야 합니다. |
| **ISA2** | **ISA1** 속성이 **00 - 권한 부여 정보 없음** 이 아닌 경우에 사용할 권한 부여 정보 값입니다. 이 속성 값은 1개 이상 10개 이하의 영숫자 문자를 포함해야 합니다. |
| **ISA3(보안 한정자)** | 사용할 보안 한정자 값입니다. 기본값은 **00 - 보안 정보 없음** 입니다. <p>**참고**: 다른 값을 선택하는 경우 **ISA4** 속성의 값을 지정해야 합니다. |
| **ISA4** | **ISA3** 속성이 **00 - 보안 정보 없음** 이 아닌 경우에 사용할 보안 정보 값입니다. 이 속성 값은 1개 이상 10개 이하의 영숫자 문자를 포함해야 합니다. |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>송신 설정 - 승인

![아웃바운드 메시지의 승인 속성](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| 속성 | Description |
|----------|-------------|
| **TA1이 예상됨** | 교환 보낸 사람에게 기술 승인(TA1)을 반환합니다. <p>이 설정은 메시지를 보내는 호스트 파트너가 규약의 게스트 파트너로부터 승인을 요청하도록 지정합니다. 규약의 수신 설정에 따라 호스트 파트너에는 이러한 승인이 필요합니다. |
| **FA가 예상됨** | 교환 보낸 사람에게 기능 승인(FA)을 반환합니다. **FA Version** 속성의 경우 스키마 버전에 따라 997 또는 999 승인을 선택합니다. <p>이 설정은 메시지를 보내는 호스트 파트너가 규약의 게스트 파트너에게 승인을 요청하도록 지정합니다. 규약의 수신 설정에 따라 호스트 파트너에는 이러한 승인이 필요합니다. |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>송신 설정 - 스키마

![아웃바운드 메시지의 스키마](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

이 섹션에서는 각 트랜잭션 유형(ST01)의 [통합 계정](./logic-apps-enterprise-integration-create-integration-account.md)에서 [스키마](../logic-apps/logic-apps-enterprise-integration-schemas.md)를 선택합니다. 각 행을 완료하면 비어 있는 새 행이 자동으로 표시됩니다.

| 속성 | Description |
|----------|-------------|
| **버전** | 스키마의 X12 버전 |
| **트랜잭션 유형(ST01)** | 스키마의 트랜잭션 유형입니다. |
| **스키마** | 사용하려는 스키마 파일입니다. 스키마를 먼저 선택하면 버전과 트랜잭션 유형이 자동으로 설정됩니다. |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>송신 설정 - 봉투

![아웃바운드 메시지에 사용할 트랜잭션 세트의 구분 기호](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| 속성 | Description |
|----------|-------------|
| **ISA11 사용** | 트랜잭션 세트에 사용할 구분 기호: <p>- **표준 식별자**: EDI 송신 파이프라인에서 아웃바운드 문서의 소수 표기법 대신 소수 표기법에 마침표(.)를 사용합니다. <p>- **반복 구분 기호**: 단순 데이터 요소 또는 반복되는 데이터 구조의 반복 발생에 대한 구분 기호를 지정합니다. 예를 들어 일반적으로 캐럿(^)을 반복 구분 기호로 사용합니다. HIPAA 스키마의 경우 캐럿만 사용할 수 있습니다. |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>송신 설정 - 컨트롤 버전 번호

![아웃바운드 메시지의 컨트롤 버전 번호](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

이 섹션에서는 각 교환의 [통합 계정](./logic-apps-enterprise-integration-create-integration-account.md)에서 [스키마](../logic-apps/logic-apps-enterprise-integration-schemas.md)를 선택합니다. 각 행을 완료하면 비어 있는 새 행이 자동으로 표시됩니다.

| 속성 | Description |
|----------|-------------|
| **컨트롤 버전 번호(ISA12)** | X12 표준 버전 |
| **사용 표시기(ISA15)** | 교환의 컨텍스트이며 **테스트** 데이터, **정보** 데이터 또는 **프로덕션** 데이터 중 하나입니다. |
| **스키마** | EDI 송신 파이프라인으로 보내는 X12 인코딩 교환에 대한 GS 및 ST 세그먼트를 생성하는 데 사용되는 스키마입니다. |
| **GS1** | 선택 사항으로, 함수 코드를 선택합니다. |
| **GS2** | 선택 사항으로, 애플리케이션 발신자를 지정합니다. |
| **GS3** | 선택 사항으로, 애플리케이션 수신자를 지정합니다. |
| **GS4** | 선택 사항으로, **CCYYMMDD** 또는 **YYMMDD** 를 선택합니다. |
| **GS5** | 선택 사항으로, **HHMM**, **HHMMSS** 또는 **HHMMSSdd** 를 선택합니다. |
| **GS7** | 선택 사항으로, 담당 에이전시의 값을 선택합니다. |
| **GS8** | 선택 사항으로, 스키마 문서 버전을 지정합니다. |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>송신 설정 - 컨트롤 번호

![아웃바운드 메시지의 컨트롤 번호](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| 속성 | Description |
|----------|-------------|
| **교환 컨트롤 번호(ISA13)** | 교환 컨트롤 번호의 값 범위로, 최솟값은 1이고 최댓값은 999999999입니다. |
| **그룹 컨트롤 번호(GS06)** | 그룹 컨트롤 번호의 값 범위로, 최솟값은 1이고 최댓값은 999999999입니다. |
| **트랜잭션 세트 컨트롤 번호(ST02)** | 트랜잭션 세트 컨트롤 번호의 값 범위로, 최솟값은 1이고 최댓값은 999999999입니다. <p>- **접두사**: 선택 사항으로, 영숫자 값입니다. <br>- **접미사**: 선택 사항으로, 영숫자 값입니다. |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>송신 설정 - 문자 세트 및 구분 기호

![아웃바운드 메시지의 메시지 유형에 대한 구분 기호](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

**기본** 행은 메시지 스키마의 구분 기호로 사용되는 문자 세트를 표시합니다. **기본** 문자 세트를 사용하지 않으려면 각 메시지 유형에 다른 구분 기호 세트를 입력하면 됩니다. 각 행을 완료하면 비어 있는 새 행이 자동으로 표시됩니다.

> [!TIP]
> 특수 문자 값을 제공하려면 JSON으로 계약을 편집하고 특수 문자에 대한 ASCII 값을 제공합니다.

| 속성 | Description |
|----------|-------------|
| **사용할 문자 세트** | X12 문자 세트이며 **기본**, **확장** 또는 **UTF8** 입니다. |
| **스키마** | 사용하려는 스키마입니다. 스키마를 선택한 후에는 아래의 구분 기호 설명에 따라 사용하려는 문자 세트를 선택합니다. |
| **입력 형식** | 문자 세트의 입력 형식 |
| **구성 요소 구분 기호** | 복합 데이터 요소를 분리하는 단일 문자 |
| **데이터 요소 구분 기호** | 복합 데이터 내에서 단순 데이터 요소를 분리하는 단일 문자 |
| **대체 문자 구분 기호** | 아웃바운드 X12 메시지를 생성할 때 페이로드 데이터에 있는 모든 구분 기호 문자를 대체하는 대체 문자 |
| **세그먼트 마침 표시** | EDI 세그먼트의 끝을 나타내는 단일 문자 |
| **접미사** | 세그먼트 식별자와 함께 사용할 문자. 접미사를 지정하면 세그먼트 마침 표시 데이터 요소를 비워 둘 수 있습니다. 세그먼트 마침 표시를 비워 두는 경우에는 반드시 접미사를 지정해야 합니다. |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>송신 설정 - 유효성 검사

![아웃바운드 메시지의 유효성 검사 속성](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

**기본값** 행은 EDI 메시지 유형에 사용되는 유효성 검사 규칙을 보여줍니다. 다른 규칙을 정의하려면 규칙을 **true** 로 설정하려는 각 상자를 선택합니다. 각 행을 완료하면 비어 있는 새 행이 자동으로 표시됩니다.

| 속성 | Description |
|----------|-------------|
| **메시지 유형** | EDI 메시지 유형 |
| **EDI 유효성 검사** | 스키마의 EDI 속성, 길이 제한, 빈 데이터 요소 및 후행 구분 기호로 정의되는 데이터 형식에 대해 EDI 유효성 검사를 수행합니다. |
| **확장 유효성 검사** | 데이터 형식이 EDI가 아니면 데이터 요소 요구 사항 및 허용되는 반복, 열거, 데이터 요소 길이 유효성 검사(최솟값 또는 최댓값)에 대한 유효성 검사가 수행됩니다. |
| **선행/후행 0 허용** | 추가되는 선행 또는 후행 0과 공백 문자를 보존합니다. 이러한 문자를 제거하지 마십시오. |
| **선행/후행 0 자르기** | 선행 또는 후행 0과 공백 문자를 제거합니다. |
| **후행 구분 기호 정책** | 후행 구분 기호를 생성합니다. <p>- **허용되지 않음**: 아웃바운드 교환에서 후행 구분 기호를 금지합니다. 이 경우 교환에 후행 구분 기호가 있으면 잘못된 것으로 선언됩니다. <p>- **선택 사항**: 후행 구분 기호의 존재 여부에 관계없이 교환을 보냅니다. <p>- **필수**: 아웃바운드 교환에 후행 구분 기호가 있어야 합니다. |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>HIPAA 스키마 및 메시지 유형

HIPAA 스키마 및 277 또는 837 메시지 유형을 사용하는 경우 몇 가지 추가 단계를 수행해야 합니다. 이러한 메시지 유형의 [문서 버전 번호(GS8)](#outbound-control-version-number)에는 10자 이상의 문자가 포함됩니다(예: ‘005010X222A1’). 또한 일부 문서 버전 번호는 변형 메시지 유형에 매핑됩니다. 스키마 및 규약에서 올바른 메시지 유형을 참조하지 않으면 다음 오류 메시지가 표시됩니다.

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

다음 테이블에는 영향을 받는 메시지 유형, 모든 변형, 해당 메시지 유형에 매핑되는 문서 버전 번호가 나열됩니다.

| 메시지 유형 또는 변형 |  Description | 문서 버전 번호(GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | 의료 보험 정보 상태 통지 | 005010X212 |
| 837_I | 의료 보험 청구 기관 | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | 의료 보험 청구 치과 | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | 의료 보험 청구 전문가 | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

또한 이러한 문서 버전 번호를 사용하면 문자 길이가 잘못되었다는 오류가 발생하므로 EDI 유효성 검사를 사용하지 않도록 설정해야 합니다.

이러한 문서 버전 번호와 메시지 유형을 지정하려면 다음 단계를 수행합니다.

1. HIPAA 스키마에서 현재 메시지 유형을 사용하려는 문서 버전 번호의 변형 메시지 유형으로 바꿉니다.

   예를 들어 메시지 유형 `837`에 문서 버전 번호 `005010X222A1`을 사용하려 한다고 가정해 보겠습니다. 스키마에서 각 `"X12_00501_837"` 값을 `"X12_00501_837_P"` 값으로 바꿉니다.

   스키마를 업데이트하려면 다음 단계를 수행합니다.

   1. Azure Portal에서 통합 계정으로 이동합니다. 해당 스키마를 찾아서 다운로드합니다. 메시지 유형을 바꾸고, 스키마 파일의 이름을 바꾸고, 수정된 스키마를 통합 계정에 업로드합니다. 자세한 내용은 [스키마 편집](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas)을 참조하세요.

   1. 규약의 메시지 설정에서 수정된 스키마를 선택합니다.

1. 규약의 `schemaReferences` 개체에서 문서 버전 번호와 일치하는 변형 메시지 유형을 지정하는 또 다른 항목을 추가합니다.

   예를 들어 메시지 유형 `837`에 문서 버전 번호 `005010X222A1`을 사용하려 한다고 가정하겠습니다. 규약에는 다음과 같은 속성과 값을 가진 `schemaReferences` 섹션이 있습니다.

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   이 `schemaReferences` 섹션에서 다음 값을 가진 또 다른 항목을 추가합니다.

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   여기까지 마쳤으면 `schemaReferences` 섹션이 다음과 비슷한 모습입니다.

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. 규약의 메시지 설정에서 각 메시지 유형 또는 모든 메시지 유형(**기본값** 을 사용하는 경우)의 **EDI 유효성 검사** 확인란을 선택 취소하여 EDI 유효성 검사를 사용하지 않도록 설정합니다.

   ![모든 메시지 유형 또는 각 메시지 유형에 유효성 검사를 사용하지 않도록 설정](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="next-steps"></a>다음 단계

* [X12 TA1 기술 승인 및 오류 코드](logic-apps-enterprise-integration-x12-ta1-acknowledgment.md)
* [X12 997 기능 승인 및 오류 코드](logic-apps-enterprise-integration-x12-997-acknowledgment.md)
* [Azure Logic Apps의 커넥터 정보](../connectors/apis-list.md)
