---
title: Microsoft Defender for Cloud의 워크플로 자동화 | Microsoft Docs
description: Microsoft Defender for Cloud에서 워크플로를 만들고 자동화 하는 방법을 알아봅니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 05/03/2021
ms.author: memildin
ms.openlocfilehash: cefeb17946f5723d7ea44ee89f8245eef01d28c3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131445151"
---
# <a name="automate-responses-to-microsoft-defender-for-cloud-triggers"></a>클라우드 트리거에 대해 Microsoft Defender에 대 한 응답 자동화

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

모든 보안 프로그램에는 인시던트 응답을 위한 여러 워크플로가 포함되어 있습니다. 이러한 프로세스에는 관련 이해 관계자에게 알리고, 변경 관리 프로세스를 시작하고, 특정 수정 단계를 적용하는 것이 포함될 수 있습니다. 보안 전문가는 가능한 한 해당 절차의 여러 단계를 자동화할 것을 권장합니다. Azure 자동화를 통해 오버헤드를 줄입니다. 또한 자동화는 프로세스 단계가 미리 정의된 요구 사항에 따라 빠르고 일관되게 수행되도록 하여 보안을 향상할 수 있습니다.

이 문서에서는 Microsoft Defender for Cloud의 워크플로 자동화 기능에 대해 설명 합니다. 이 기능은 보안 경고, 권장 사항 및 규정 준수 변경에 대해 Logic Apps를 트리거할 수 있습니다. 예를 들어, 경고가 발생 하는 경우 Defender for Cloud가 특정 사용자를 전자 메일로 보낼 수 있습니다. [Azure Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하여 Logic Apps를 만드는 방법도 알아봅니다.


## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|Free|
|필요한 역할 및 권한:|리소스 그룹의 **보안 관리자 역할** 또는 **소유자**<br>대상 리소스에 대한 쓰기 권한도 있어야 함<br><br>Azure Logic Apps 워크플로를 사용하려면 다음 Logic Apps 역할/권한도 있어야 합니다.<br> - [Logic App 운영자](../role-based-access-control/built-in-roles.md#logic-app-operator) 권한이 필요하거나 Logic App 읽기/트리거 액세스(이 역할은 논리 앱을 만들거나 편집할 수 없으며 기존 앱만 *실행*)<br> - [Logic App 기여자](../role-based-access-control/built-in-roles.md#logic-app-contributor) 권한은 Logic App 생성 및 수정에 필요합니다.<br>Logic App 커넥터를 사용하려면 해당 서비스(예: Outlook/Teams/Slack 인스턴스)에 로그인하기 위해 추가 자격 증명이 필요할 수 있습니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet)|
|||



## <a name="create-a-logic-app-and-define-when-it-should-automatically-run"></a>논리 앱을 생성된 뒤 자동으로 실행되는 시간을 정의합니다. 

1. 클라우드 용 Defender의 사이드바에서 **워크플로 자동화** 를 선택 합니다.

    :::image type="content" source="./media/workflow-automation/list-of-workflow-automations.png" alt-text="정의 된 자동화 목록을 보여 주는 워크플로 자동화 페이지의 스크린샷" lightbox="./media/workflow-automation/list-of-workflow-automations.png":::

    이 페이지에서 새 자동화 규칙을 만들거나 기존 규칙을 사용/사용하지 않도록 설정하거나 삭제할 수 있습니다.

1. 새로운 워크플로를 정의하려면 **워크플로 자동화 추가** 를 클릭합니다. 새 자동화에 대 한 옵션 창이 열립니다.

    :::image type="content" source="./media/workflow-automation/add-workflow.png" alt-text="워크플로 자동화 추가 창.":::

    여기에서 다음을 입력할 수 있습니다.
    1. 자동화에 대한 이름 및 설명입니다.
    1. 자동 워크플로를 시작하는 트리거입니다. 예를 들어, 당신은 “SQL”을 포함하는 보안 경고가 생성될 때, 논리 앱을 실행하려고 할 수 있습니다.

        > [!NOTE]
        > 트리거가 "하위 권장 사항"이 있는 권장 사항인 경우(예: **SQL 데이터베이스에 대한 취약성 평가 결과를 수정해야 함**) 논리 앱은 모든 새로운 보안 결과에 대해 트리거되지 않고 부모 권장 사항의 상태가 변경된 경우에만 트리거됩니다.

    1. 트리거 조건이 충족될 때 실행되는 논리 앱입니다. 

1. 작업 섹션에서 **Logic Apps 페이지 방문** 을 선택 하 여 논리 앱 만들기 프로세스를 시작 합니다.

    Azure Logic Apps로 이동합니다.

1. **추가** 를 선택합니다. 

    [![새 Logic App 만들기.](media/workflow-automation/logic-apps-create-new.png)](media/workflow-automation/logic-apps-create-new.png#lightbox)

1. 이름, 리소스 그룹 및 위치를 입력 하 고 **검토 및** 만들기 만들기를 선택  >  합니다.

    메시지 **배포가 진행 중** 입니다 .가 표시 됩니다. 배포 완료 알림이 표시 될 때까지 기다렸다가 알림에서 **리소스로 이동** 을 선택 합니다.

1. 새 논리 앱에서는 보안 범주 내의 기본 제공 및 미리 정의된 템플릿을 선택할 수 있습니다. 또는 해당 프로세스가 트리거될 때 발생하는 이벤트의 사용자 지정 흐름을 정의할 수 있습니다.

    > [!TIP]
    > 경우에 따라 논리 앱에서 매개 변수는 자체 필드가 아닌 문자열의 일부로 커넥터에 포함됩니다. 매개 변수를 추출 하는 방법에 대 한 예제는 [클라우드 워크플로 자동화 Microsoft Defender를 빌드하는 동안 논리 앱 매개 변수 작업](https://techcommunity.microsoft.com/t5/azure-security-center/working-with-logic-app-parameters-while-building-azure-security/ba-p/1342121)#14 단계를 참조 하세요.

    논리 앱 디자이너는 클라우드 트리거에 대해 이러한 Defender를 지원 합니다.

    - **클라우드 권장 사항에 대 한 Microsoft Defender가 만들어지거나 트리거되** 는 경우-논리 앱이 사용 되지 않거나 대체 되는 권장 사항을 사용 하는 경우 automation의 작동이 중지 되 고 트리거를 업데이트 해야 합니다. 권장 사항에 대 한 변경 내용을 추적 하려면 [릴리스 정보](release-notes.md)를 사용 합니다.

    - **클라우드 경고에 대 한 Defender가 만들어지거나 트리거될 때** 관심 있는 심각도 수준의 경고에만 관련 되도록 트리거를 사용자 지정할 수 있습니다.
    
    - 규정 준수 평가에 대 한 업데이트를 기반으로 **클라우드 규정 준수 평가에 대 한 Defender가 만들어지거나 트리거된 경우** 자동화.

    > [!NOTE]
    > 레거시 트리거 "Azure Security Center 경고에 대한 응답이 트리거되는 경우"를 사용하면 논리 앱이 워크플로 자동화 기능으로 실행되지 않습니다. 대신 위에서 언급한 트리거 중 하나를 사용하세요. 

    [![논리 앱 샘플.](media/workflow-automation/sample-logic-app.png)](media/workflow-automation/sample-logic-app.png#lightbox)

1. 논리 앱을 정의한 후 워크플로 자동화 정의 창(‘워크플로 자동화 추가’)으로 돌아갑니다. **새로 고침** 을 클릭하여 새 Logic App을 선택할 수 있는지 확인합니다.

    ![새로 고침.](media/workflow-automation/refresh-the-list-of-logic-apps.png)

1. 논리 앱을 선택하여 자동화를 저장합니다. 논리 앱 드롭다운은 위에서 언급 한 클라우드 커넥터용 지원 Defender Logic Apps 표시 합니다.


## <a name="manually-trigger-a-logic-app"></a>Logic App을 수동으로 트리거

보안 경고나 권장 사항을 볼 때 Azure Logic Apps를 수동으로 실행할 수도 있습니다.

Logic App을 수동으로 실행하려면, 경고 또는 권장 사항을 연 다음 **논리 앱 트리거** 를 선택합니다.

[![Logic App을 수동으로 트리거합니다.](media/workflow-automation/manually-trigger-logic-app.png)](media/workflow-automation/manually-trigger-logic-app.png#lightbox)


## <a name="configure-workflow-automation-at-scale-using-the-supplied-policies"></a>제공된 정책을 사용하여 대규모 워크플로 자동화 구성

조직의 모니터링 및 인시던트 대응 프로세스를 자동화하면 보안 인시던트를 조사하고 완화하는 데 걸리는 시간을 크게 향상시킬 수 있습니다.

조직 전체에 자동화 구성을 배포하려면 아래에 설명된 대로 제공된 Azure Policy 'DeployIfNotExist' 정책을 사용하여 워크플로 자동화 절차를 만들고 구성합니다.

[워크플로 자동화 템플릿](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)을 시작합니다.

이러한 정책을 구현하려면:

1. 아래의 표에서 적용하려는 정책을 선택합니다.

    |목표  |정책  |정책 ID  |
    |---------|---------|---------|
    |보안 경고에 대한 워크플로 자동화              |[클라우드 경고에 대해 Microsoft Defender에 대 한 워크플로 자동화 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
    |보안 추천 사항에 대한 워크플로 자동화     |[클라우드 권장 사항에 대 한 Microsoft Defender 워크플로 자동화 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
    |규정 준수 변경에 대한 워크플로 자동화|[클라우드 규정 준수를 위해 Microsoft Defender에 대 한 워크플로 자동화 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
    ||||

    > [!TIP]
    > 또한 Azure Policy를 검색하여 찾을 수 있습니다.
    > 1. Azure Policy를 엽니다.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Azure Policy에 액세스":::
    > 2. Azure Policy 메뉴에서 **정의** 를 선택하고 이름으로 검색합니다. 

1. 관련 Azure Policy 페이지에서 **할당** 을 선택합니다.
    :::image type="content" source="./media/workflow-automation/export-policy-assign.png" alt-text="Azure Policy 할당":::

1. 각 탭을 열고 원하는 대로 매개 변수를 설정합니다.
    1. **기본** 탭에서 정책 범위를 설정합니다. 중앙 집중식 관리를 사용하려면 워크플로 자동화 구성을 사용할 구독이 포함된 관리 그룹에 정책을 할당합니다. 
    1. **매개 변수** 탭에서 리소스 그룹 및 데이터 형식 세부 정보를 설정합니다. 
        > [!TIP]
        > 각 매개 변수에는 제공되는 옵션을 알려 주는 도구 설명이 있습니다.
        >
        > Azure Policy의 매개 변수 탭 (1)은 클라우드 워크플로 자동화 페이지 (2) 용 Defender와 유사한 구성 옵션에 대 한 액세스를 제공 합니다.
        > :::image type="content" source="./media/workflow-automation/azure-policy-next-to-workflow-automation.png" alt-text="Azure Policy를 사용하여 워크플로 자동화의 매개 변수 비교." lightbox="./media/workflow-automation/azure-policy-next-to-workflow-automation.png":::

    1. 필요한 경우 이 할당을 기존 구독에 적용하려면 **수정** 탭을 열고 수정 작업을 만드는 옵션을 선택합니다.

1. 요약 페이지를 검토하고 **만들기** 를 선택합니다.


## <a name="data-types-schemas"></a>데이터 형식 스키마

Logic App 인스턴스에 전달된 보안 경고 또는 권장 사항 이벤트의 원시 이벤트 스키마를 보려면 [워크플로 자동화 데이터 형식 스키마](https://aka.ms/ASCAutomationSchemas)를 참조하세요. 이는 위에서 언급한 클라우드용 Defender의 기본 제공 논리 앱 커넥터를 사용하지 않고 대신 논리 앱의 일반 HTTP 커넥터를 사용하는 경우에 유용할 수 있습니다. 이벤트 JSON 스키마를 사용하여 원하는 대로 수동으로 구문 분석할 수 있습니다.


## <a name="faq---workflow-automation"></a>FAQ - 워크플로 자동화

### <a name="does-workflow-automation-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>워크플로 자동화는 BCDR(비즈니스 연속성 또는 재해 복구) 시나리오를 지원하나요?

대상 리소스에 중단 또는 기타 재해가 발생하는 BCDR 시나리오의 환경에 대비하는 경우 Azure Event Hubs, Log Analytics 작업 영역 및 Logic App의 지침에 따라 백업을 설정하여 데이터 손실을 방지하는 것은 조직의 책임입니다.

모든 활성 자동화에 대해 동일한(비활성화된) 자동화를 만들어서 다른 위치에 저장하는 것이 좋습니다. 가동 중단이 발생하면 이러한 백업 자동화를 사용하도록 설정하고 정상적인 작업을 유지할 수 있습니다.

[Azure Logic Apps의 비즈니스 연속성 및 재해 복구](../logic-apps/business-continuity-disaster-recovery-guidance.md)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Logic Apps 만들고, Defender for Cloud에서 실행을 자동화하고, 수동으로 실행하는 것에 대해 배웠습니다.

관련 자료는 다음을 참조하세요. 

- [워크플로 자동화를 사용하여 보안 대응을 자동화하는 방법에 대한 Microsoft Learn 모듈](/learn/modules/resolve-threats-with-azure-security-center/)
- [클라우드용 Microsoft Defender의 보안 권장 사항](review-security-recommendations.md)
- [클라우드용 Microsoft Defender의 보안 경고](alerts-overview.md)
- [Azure Logic Apps 정보](../logic-apps/logic-apps-overview.md)
- [Azure Logic Apps용 커넥터](../connectors/apis-list.md)
- [워크플로 자동화 데이터 형식 스키마](https://aka.ms/ASCAutomationSchemas)