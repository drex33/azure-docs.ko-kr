---
title: 모의 테스트 워크플로
description: 프로덕션 환경에 영향을 주지 않고 Azure Logic Apps 워크플로를 테스트하도록 모의 데이터를 설정합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/08/2021
ms.openlocfilehash: 4167dcffe0a1b4db50f6d1580ad6284f2cf9321d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712424"
---
# <a name="test-workflows-with-mock-data-in-azure-logic-apps-preview"></a>Azure Logic Apps 모의 데이터를 통해 워크플로 테스트(미리 보기)

> [!NOTE]
> 이 기능은 미리 보기로 제공되고 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)이 적용됩니다.

실제로 라이브 앱, 데이터, 서비스 또는 시스템을 호출하거나 액세스하지 않고 워크플로를 테스트하려면 작업에서 모의 값을 설정하고 반환할 수 있습니다. 예를 들어 다양한 조건에 따라 다른 작업 경로를 테스트하거나, 강제로 오류를 발생시키고, 특정 메시지 응답 본문을 제공하거나, 일부 단계를 건너뛰려고 할 수 있습니다. 작업에 대해 모의 데이터 테스트를 설정해도 작업이 실행되지 않고 모의 데이터가 대신 반환됩니다.

예를 들어 Outlook 365 메일 보내기 작업에 대한 모의 데이터를 설정하는 경우 Azure Logic Apps Outlook 호출하고 이메일을 보내는 대신 제공한 모의 데이터를 반환합니다.

이 문서에서는 [ **논리 앱(소비)** 및 논리 **앱(표준)** 리소스 종류에](logic-apps-overview.md#resource-environment-differences)대한 워크플로에서 작업에 대한 모의 데이터를 설정하는 방법을 보여 있습니다. 이러한 모의 데이터를 사용하고 기존 작업 출력을 모의 데이터로 다시 사용하는 이전 워크플로 실행을 찾을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 없는 경우 <a href="https://azure.microsoft.com/free/?WT.mc_id=A261C142F" target="_blank">Azure 체험 계정에 등록</a>합니다.

* 모의 데이터를 설정하려는 논리 앱 리소스 및 워크플로입니다. 이 문서에서는 **되풀이** 트리거 및 **HTTP** 작업을 예제 워크플로로 사용합니다.

  논리 앱을 처음 접하는 경우 [Azure Logic Apps 정의](logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 워크플로 만들기를 검토합니다.](quickstart-create-first-logic-app-workflow.md)

<a name="enable-mock-data"></a>

## <a name="enable-mock-data-output"></a>모의 데이터 출력 사용

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 워크플로를 엽니다.

1. 모의 데이터를 반환하려는 작업에서 다음 단계를 수행합니다.

   1. 작업의 오른쪽 위 모서리에서 타원(*...*) 단추를 선택한 다음, **테스트** 를 선택합니다. 예를 들면 다음과 같습니다.

      ![Azure Portal, 워크플로 디자이너, 작업 바로 가기 메뉴 및 선택된 "테스트"를 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/select-testing.png)

   1. **테스트** 창에서 **정적 결과 사용(미리 보기) 을** 선택합니다. 작업의 필수(*) 속성이 표시되면 작업의 응답으로 반환할 모의 출력 값을 지정합니다.

      속성은 선택한 작업 유형에 따라 다릅니다. 예를 들어 HTTP 작업에는 다음과 같은 필수 속성이 있습니다.

      | 속성 | 설명 |
      |----------|-------------|
      | **상태** | 반환할 작업 상태 |
      | **상태 코드** | 출력으로 반환할 특정 상태 코드 |
      | **헤더** | 반환할 헤더 콘텐츠 |
      |||

      !["정적 결과 사용"을 선택한 후 "테스트" 창을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      > [!TIP]
      > JavaScript Object Notation(JSON) 형식으로 값을 입력하려면 **JSON 모드로 전환("JSON 모드로** ![ 전환"의 ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) 아이콘)을 선택합니다.

   1. 선택적 속성의 경우, **선택적 필드 선택** 목록을 열고 모의하려는 속성을 선택합니다.

      !["선택적 필드 선택" 목록이 열린 "테스트" 창을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. 준비가 되면 **완료** 를 선택합니다.

   작업의 오른쪽 위 모서리에 이제 제목 표시줄에 테스트 비커 아이콘()이 표시됩니다.![정적 결과의 아이콘](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)) - 정적 결과를 사용하도록 설정했음을 나타냅니다.

   ![정적 결과 아이콘이 있는 작업을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/static-result-enabled.png)

   모의 데이터를 사용하는 워크플로 실행을 찾으려면 이 항목의 후반부에서 [정적 결과를 사용하는 실행 찾기를](#find-runs-mock-data) 검토하세요.

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 워크플로를 엽니다.

1. 디자이너에서 작업 세부 정보 창이 표시되도록 모의 데이터를 반환하려는 작업을 선택합니다.

1. 작업 세부 정보 창이 오른쪽에 열리면 **테스트를** 선택합니다.

   ![Azure Portal, 워크플로 디자이너, 작업 세부 정보 창 및 선택된 "테스트"를 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/select-testing-standard.png)

1. **테스트** 탭에서 **정적 결과 사용(미리 보기) 을** 선택합니다. 작업의 필수(*) 속성이 표시되면 작업의 응답으로 반환할 모의 출력 값을 지정합니다.

   속성은 선택한 작업 유형에 따라 다릅니다. 예를 들어 HTTP 작업에는 다음과 같은 필수 속성이 있습니다.

   | 속성 | 설명 |
   |----------|-------------|
   | **상태** | 반환할 작업 상태 |
   | **상태 코드** | 출력으로 반환할 특정 상태 코드 |
   | **헤더** | 반환할 헤더 콘텐츠 |
   |||

   !["정적 결과 사용"을 선택한 후 "테스트" 탭을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/enable-static-result-standard.png)

   > [!TIP]
   > JavaScript Object Notation(JSON) 형식으로 값을 입력하려면 **JSON 모드로 전환("JSON 모드로** ![ 전환"의 ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png) 아이콘)을 선택합니다.

1. 선택적 속성의 경우, **선택적 필드 선택** 목록을 열고 모의하려는 속성을 선택합니다.

   !["선택적 필드 선택" 목록이 열린 "테스트" 창을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/optional-properties-standard.png)

1. 준비가 되면 **완료** 를 선택합니다.

   이제 작업의 오른쪽 아래 모서리에 테스트 비커 아이콘()이 표시됩니다.![정적 결과의 아이콘](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)) - 정적 결과를 사용하도록 설정했음을 나타냅니다.

   ![디자이너에서 정적 결과 아이콘이 있는 동작을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/static-result-enabled-standard.png)

   모의 데이터를 사용하는 워크플로 실행을 찾으려면 이 항목의 후반부에서 [정적 결과를 사용하는 실행 찾기를](#find-runs-mock-data) 검토하세요.

---

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-mock-data"></a>모의 데이터를 사용하는 실행 찾기

### <a name="consumption"></a>[Consumption](#tab/consumption)

작업에서 모의 데이터를 사용하는 이전 워크플로 실행을 찾으려면 해당 워크플로의 실행 기록을 검토합니다.

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 워크플로를 엽니다.

1. 논리 앱 리소스 메뉴에서 **개요를** 선택합니다.

1. **Essentials** 섹션에서 실행 **기록(아직** 선택하지 않은 경우)을 선택합니다.

1. 실행 기록 테이블에서 **정적 결과** 열을 찾습니다. 

   모의 데이터 출력이 있는 작업을 포함하는 모든 실행에는 **정적 결과** 열이 **사용으로 설정됩니다.** 예를 들면 다음과 같습니다.

   !["정적 결과" 열이 있는 워크플로 실행 기록을 보여주는 스크린샷](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. 모의 데이터를 사용하는 실행에서 해당 작업을 보려면 **정적 결과** 열이 **사용으로** 설정된 실행을 선택합니다.

   정적 결과를 사용하는 작업은 테스트 비커()를 표시합니다.![정적 결과의 아이콘](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)) 아이콘( 예:

   ![정적 결과를 사용하는 작업이 있는 워크플로 실행 기록을 보여 주는 스크린샷](./media/test-logic-apps-mock-data-static-results/static-result-enabled-run-details.png)

### <a name="standard"></a>[Standard](#tab/standard)

작업에서 모의 데이터를 사용하는 다른 워크플로 실행을 찾으려면 각 실행을 확인해야 합니다.

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 워크플로를 엽니다.

1. 워크플로 메뉴에서 **개요** 를 선택합니다.

1. 필수 **섹션** 아래에서 **실행 기록** 을 선택합니다(아직 선택하지 않은 경우).

1. 실행 **기록** 테이블에서 검토하려는 실행을 선택합니다.

   ![워크플로 실행 기록을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/select-run-standard.png)

1. 실행 세부 정보 창에서 작업에서 테스트 비커()를 표시하는지 확인합니다.![정적 결과의 아이콘](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)) 아이콘( 예:

   ![정적 결과를 사용하는 작업이 있는 워크플로 실행 기록을 보여 주는 스크린샷](./media/test-logic-apps-mock-data-static-results/run-history-static-result-standard.png)

---

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs-as-mock-data"></a>이전 출력을 모의 데이터로 다시 사용

출력이 있는 이전 워크플로 실행이 있는 경우 해당 실행에서 해당 출력을 복사하여 붙여넣어 이러한 출력을 모의 데이터로 다시 사용할 수 있습니다.

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 워크플로를 엽니다.

1. 논리 앱 리소스 메뉴에서 **개요를** 선택합니다.

1. **Essentials** 섹션에서 실행 **기록(아직** 선택하지 않은 경우)을 선택합니다. 표시되는 목록에서 원하는 워크플로 실행을 선택합니다.

   ![워크플로 실행 기록을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/select-run.png)

1. 실행 세부 정보 창이 열리면 원하는 출력이 있는 작업을 확장합니다.

1. **출력** 섹션에서 **원시 출력 표시를 선택합니다.**

1. **출력** 창에서 JSON(전체 JavaScript Object Notation) 개체 또는 사용할 특정 하위 섹션(예: 출력 섹션 또는 헤더 섹션)을 복사합니다.

1. 작업에 대한 [모의 데이터를 설정하는](#enable-mock-data) 방법에 대한 이전 섹션을 검토하고 단계에 따라 작업의 **테스트** 창을 엽니다.

1. **테스트** 창이 열리면 다음 단계 중 하나를 선택합니다.

   * 전체 JSON 개체를 붙여넣려면 **테스트** 레이블 옆에 있는 **JSON 모드로 전환("JSON 모드로** ![ 전환"의 아이콘)을 선택합니다. ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)

     ![전체 JSON 개체를 붙여넣기 위해 선택된 "JSON 모드로 전환" 아이콘을 보여주는 스크린샷](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * JSON 섹션만 붙여넣려면 **출력** 또는 **헤더와** 같은 해당 섹션의 레이블 옆에 **JSON 모드로 전환을** 선택합니다. 예를 들면 다음과 같습니다.

     ![JSON 개체에서 섹션을 붙여넣기 위해 선택된 "JSON 모드로 전환" 아이콘을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-output.png)

1. JSON 편집기에서 이전에 복사한 JSON을 붙여 넣습니다.

   ![편집기에서 붙여넣은 JSON을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. 완료되면 **완료** 를 선택합니다. 또는 디자이너로 돌아가서 **편집기 모드 전환("편집기 모드** ![ 전환"의 ](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png) 아이콘)을 선택합니다.

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 워크플로를 엽니다.

1. 워크플로 메뉴에서 **개요** 를 선택합니다.

1. 필수 **섹션** 아래에서 **실행 기록** 을 선택합니다(아직 선택하지 않은 경우).

1. 실행 **기록** 테이블에서 검토하려는 실행을 선택합니다.

   ![워크플로 실행 기록을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/select-run-standard.png)

1. 실행 세부 정보 창이 열리면 원하는 출력이 있는 작업을 선택합니다.

1. **출력** 섹션에서 **원시 출력 표시를 선택합니다.**

1. **출력** 창에서 JSON(전체 JavaScript Object Notation) 개체 또는 사용할 특정 하위 섹션(예: 출력 섹션 또는 헤더 섹션)을 복사합니다.

1. 작업에 대한 [모의 데이터를 설정하는](#enable-mock-data) 방법에 대한 이전 섹션을 검토하고 단계에 따라 작업의 **테스트** 탭을 엽니다.

1. **테스트** 탭이 열리면 다음 단계 중 하나를 선택합니다.

   * 전체 JSON 개체를 붙여넣려면 **테스트** 레이블 옆에 있는 **JSON 모드로 전환("JSON 모드로** ![ 전환"의 아이콘)을 선택합니다. ](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)

     ![전체 JSON 개체를 붙여넣기 위해 선택된 "JSON 모드로 전환" 아이콘을 보여주는 스크린샷](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete-standard.png)

   * JSON 섹션만 붙여넣려면 **출력** 또는 **헤더와** 같은 해당 섹션의 레이블 옆에 **JSON 모드로 전환을** 선택합니다. 예를 들면 다음과 같습니다.

     ![JSON 개체에서 섹션을 붙여넣기 위해 선택된 "JSON 모드로 전환" 아이콘을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-output-standard.png)

1. JSON 편집기에서 이전에 복사한 JSON을 붙여 넣습니다.

   ![편집기에서 붙여넣은 JSON을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/json-editing-mode-standard.png)

1. 완료되면 **완료** 를 선택합니다. 또는 디자이너로 돌아가서 **편집기 모드 전환("편집기 모드** ![ 전환"의 ](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png) 아이콘)을 선택합니다.

---

## <a name="disable-mock-data"></a>모의 데이터 사용 안 함

동작에서 정적 결과를 해제해도 마지막 설정에서 값이 제거되지는 않습니다. 따라서 동일한 작업에서 정적 결과를 다시 켜면 이전 값을 계속 사용할 수 있습니다.

### <a name="consumption"></a>[Consumption](#tab/consumption)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 워크플로를 엽니다. 모의 데이터를 사용하지 않도록 설정하려는 작업을 찾습니다.

1. 작업의 오른쪽 위 모서리에서 테스트 비커 아이콘()을 선택합니다.![정적 결과의 아이콘](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)).

   ![작업 및 선택한 테스트 비커 아이콘을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/disable-static-result.png)

1. 정적 결과 완료 **사용 안 함을**  >  선택합니다.

   ![선택된 "정적 결과 사용 안 함"을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/disable-static-result-button.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. [Azure Portal](https://portal.azure.com)디자이너에서 논리 앱 워크플로를 엽니다. 모의 데이터를 사용하지 않도록 설정할 작업을 선택합니다.

1. 작업 세부 정보 창에서 **테스트** 탭을 선택합니다.

1. 정적 결과 완료 **사용 안 함을**  >  선택합니다.

   ![표준에 대해 선택된 "정적 결과 사용 안 함"을 보여주는 스크린샷.](./media/test-logic-apps-mock-data-static-results/disable-static-result-button-standard.png)

---

## <a name="reference"></a>참조

기본 워크플로 정의에서 이 설정에 대한 자세한 내용은 [정적 결과 - 워크플로 정의 언어에 대한 스키마 참조](logic-apps-workflow-definition-language.md#static-results) 및 [runtimeConfiguration.staticResult - 런타임 구성 설정](logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)을 참조하세요

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps](logic-apps-overview.md)에 대해 자세히 알아봅니다