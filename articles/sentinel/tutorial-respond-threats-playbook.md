---
title: Microsoft Sentinel에서 자동화 규칙으로 플레이북 사용
description: 이 자습서는 Microsoft Sentinel의 자동화 규칙과 함께 플레이북을 사용하여 인시던트 응답을 자동화하고 보안 위협을 해결하는 데 도움이 됩니다.
author: yelevin
ms.topic: tutorial
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 06da046a2ccd6be414693525cacba2fcb7e50e68
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132756460"
---
# <a name="tutorial-use-playbooks-with-automation-rules-in-microsoft-sentinel"></a>자습서: Microsoft Sentinel에서 자동화 규칙으로 플레이북 사용

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 자습서에서는 자동화 규칙과 함께 플레이북을 사용하여 인시던트 응답을 자동화하고 Microsoft Sentinel이 탐지한 보안 위협을 해결하는 방법을 보여줍니다. 이 자습서를 완료하고 나면 다음을 수행할 수 있습니다.

> [!div class="checklist"]
>
> * 자동화 규칙 만들기
> * 플레이북 만들기
> * 플레이북에 작업 추가
> * 자동화 규칙 또는 분석 규칙에 플레이북을 연결하여 위협 대응 자동화

> [!NOTE]
> 이 자습서는 주요 고객 작업인 인시던트 분류를 위한 자동화 만들기에 대한 기본 지침을 제공합니다. 자세한 내용은 [Microsoft Sentinel에서 플레이북으로 위협 대응 자동화](automate-responses-with-playbooks.md) 및 [Microsoft Sentinel 플레이북에서 트리거 및 작업 사용](playbook-triggers-actions.md)과 같은 **방법** 섹션을 참조하세요.
>

## <a name="what-are-automation-rules-and-playbooks"></a>자동화 규칙 및 플레이북이란?

자동화 규칙은 Microsoft Sentinel에서 인시던트를 심사하는 데 도움이 됩니다. 자동화 규칙을 사용하여 적절한 직원에게 인시던트를 자동으로 할당하고, 잡음이 있는 인시던트 또는 알려진 [가양성](false-positives.md)을 종결하고, 심각도를 변경하고, 태그를 추가할 수 있습니다. 자동화 규칙은 인시던트에 대응하여 플레이북을 실행할 수 있는 메커니즘이기도 합니다.

플레이북은 경고 또는 인시던트에 대응하여 Microsoft Sentinel에서 실행할 수 있는 프로시저 컬렉션입니다. 플레이북은 대응을 자동화하고 오케스트레이션하는 데 도움이 될 수 있으며, 각각 분석 규칙 또는 자동화 규칙에 연결하여 특정 경고나 인시던트가 생성될 때 자동으로 실행되도록 설정할 수 있습니다. 요청 시 수동으로 실행할 수도 있습니다.

Microsoft Sentinel의 플레이북은 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)에 내장된 워크플로를 기반으로 합니다. 즉, Logic Apps의 모든 성능, 사용자 지정 기능은 물론, 기본 제공되는 템플릿을 활용할 수 있습니다. 플레이북이 속한 구독마다 플레이북이 만들어지지만, **플레이북** 에는 선택한 구독에서 사용할 수 있는 모든 플레이북이 표시됩니다.

> [!NOTE]
> 플레이북은 Azure Logic Apps를 사용하기 때문에 추가 요금이 부과될 수 있습니다. 자세한 내용은 [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) 가격 책정 페이지를 방문하세요.

예를 들어 손상 가능성이 있는 사용자가 네트워크를 돌아다니면서 정보를 훔치지 못하게 막으려면 손상된 사용자를 탐지하는 규칙에서 생성한 인시던트에 대해 자동으로 대응하는 자동화되고 다면적인 응답을 만들면 됩니다. 먼저 다음 작업을 수행하는 플레이북을 만듭니다.

1. 인시던트를 전달하는 자동화 규칙에서 플레이북을 호출하면 플레이북은 [ServiceNow](/connectors/service-now/) 또는 다른 IT 티켓 시스템에서 티켓을 엽니다.

1. 플레이북은 [Microsoft Teams](/connectors/teams/) 또는 [Slack](/connectors/slack/)의 보안 운영 채널로 메시지를 전송하여 보안 분석가에게 이 인시던트를 알립니다.

1. 또한 이메일 메시지에 포함된 인시던트의 모든 정보를 선임 네트워크 관리자 및 보안 관리자에게 보냅니다. 이메일 메시지에는 **차단** 및 **무시** 사용자 옵션 단추가 포함되어 있습니다.

1. 플레이북은 관리자로부터 응답이 수신될 때까지 기다렸다가 다음 단계를 계속합니다.

1. 관리자가 **차단** 을 선택하면 플레이북은 사용자를 비활성화하는 명령을 Azure AD에 보내고, IP 주소를 차단하는 명령을 방화벽에 보냅니다.

1. 관리자가 **무시** 를 선택하면 플레이북은 Microsoft Sentinel의 인시던트와 ServiceNow의 티켓을 닫습니다.

플레이북을 트리거하려면 이러한 인시던트가 생성될 때 실행되는 자동화 규칙을 만듭니다. 이 규칙은 다음 단계를 수행합니다.

1. 규칙이 인시던트 상태를 **활성** 으로 변경합니다.

1. 이 유형의 인시던트를 관리하는 분석가에게 인시던트를 할당합니다.

1. "손상된 사용자" 태그를 추가합니다.

1. 마지막으로 방금 만든 플레이북을 호출합니다. ([이 단계를 수행하려면 특별한 권한이 필요합니다](automate-responses-with-playbooks.md#incident-creation-automated-response).)

위의 예제처럼 플레이북을 작업으로 호출하는 자동화 규칙을 만들어서 인시던트에 대한 응답으로 플레이북을 자동으로 실행할 수 있습니다. 또한 경고가 생성될 때 하나 이상의 플레이북을 자동으로 실행하도록 분석 규칙에 지시하여 경고에 대한 응답으로 플레이북을 자동으로 실행할 수 있습니다. 

선택한 경고에 대한 응답으로 요청 시 수동으로 플레이북을 실행하도록 선택할 수도 있습니다.

Microsoft Sentinel에서 [자동화 규칙](automate-incident-handling-with-automation-rules.md) 및 [플레이북](automate-responses-with-playbooks.md)을 사용하여 위협 대응을 자동화하는 방법에 대한 완전하고 구체적인 방법을 확인하세요.

> [!IMPORTANT]
>
> - **자동화 규칙** 과 플레이북에 대한 **인시던트 트리거** 사용은 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="create-a-playbook"></a>플레이북 만들기

다음 단계에 따라 Microsoft Sentinel에서 새 플레이북을 만듭니다.

### <a name="prepare-the-playbook-and-logic-app"></a>플레이북 및 논리 앱 준비

1. **Microsoft Sentinel** 탐색 메뉴에서 **자동화** 를 선택합니다.

1. 상단 메뉴에서 **만들기** 를 선택하고 **새 플레이북 추가** 를 선택합니다.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-playbook.png" alt-text="새 플레이북 추가":::

    새 브라우저 탭이 열리고 **논리 앱 만들기** 마법사로 이동됩니다.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-playbook.png" alt-text="논리 앱 만들기":::

1. **구독** 및 **리소스 그룹** 을 입력하고, **논리 앱 이름** 에서 플레이북 이름을 지정합니다.

1. **지역** 에서 논리 앱 정보를 저장할 Azure 지역을 선택합니다.

1. 진단 목적으로 이 플레이북의 작업을 모니터링하려는 경우 **로그 분석 사용** 확인란을 선택하고 **Log Analytics 작업 영역** 이름을 입력합니다.

1. 플레이북에 태그를 적용하려면 **다음: 태그 >** (자동화 규칙에서 적용한 태그에 연결되지 않음. [태그에 대한 자세한 정보](../azure-resource-manager/management/tag-resources.md))를 클릭합니다. 그렇지 않으면 **검토 + 만들기** 를 클릭합니다. 입력한 세부 정보를 확인하고 **만들기** 를 클릭합니다.

1. 플레이북을 만들고 배포하는 동안(몇 분이 소요됨) **Microsoft.EmptyWorkflow** 라는 화면으로 이동됩니다. "배포가 완료되었습니다"라는 메시지가 표시되면 **리소스로 이동** 을 클릭합니다.

1. 워크플로 디자인을 시작할 수 있는 새 플레이북의 [Logic Apps 디자이너](../logic-apps/logic-apps-overview.md)로 이동됩니다. 간단한 소개 비디오와 자주 사용되는 논리 앱 트리거 및 템플릿이 있는 화면이 표시됩니다. Logic Apps로 플레이북을 만드는 방법에 대해 [자세히 알아보세요](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

1. **빈 논리 앱** 템플릿을 선택합니다.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-playbook-template.png" alt-text="Logic Apps 디자이너 템플릿 갤러리":::

### <a name="choose-the-trigger"></a>트리거 선택

모든 플레이북은 트리거로 시작해야 합니다. 트리거는 플레이북을 시작하는 작업과 플레이북에서 받게 될 스키마를 정의합니다.

1. 검색 창에서 Microsoft Sentinel을 검색합니다. 결과에 표시되는 **Microsoft Sentinel** 을 선택합니다.

1. **트리거** 탭에는 다음과 같은 경우에 Microsoft Sentinel에서 제공하는 두 개의 트리거가 표시됩니다.
    - Microsoft Sentinel 경고에 대한 응답이 트리거되는 경우
    - Microsoft Sentinel 인시던트 만들기 규칙이 트리거된 경우

   만들고 있는 플레이북의 유형과 일치하는 트리거를 선택합니다.

    > [!NOTE]
    > **인시던트 트리거** 를 기반으로 하는 플레이북만 자동화 규칙을 통해 호출할 수 있습니다. **경고 트리거** 를 기반으로 하는 플레이북은 [분석 규칙](detect-threats-custom.md#set-automated-responses-and-create-the-rule)에서 직접 실행되도록 정의해야 하며 수동으로 실행할 수도 있습니다.
    > 
    > 사용할 트리거에 대한 자세한 내용은 [**Microsoft Sentinel 플레이북에서 트리거 및 작업 사용**](playbook-triggers-actions.md)을 참조하세요.

    :::image type="content" source="./media/tutorial-respond-threats-playbook/choose-trigger.png" alt-text="플레이북의 트리거 선택":::

> [!NOTE]
> 트리거 또는 후속 작업을 선택하면 조작하는 리소스 공급자에 인증하라는 메시지가 표시됩니다. 이 경우 공급자는 Microsoft Sentinel입니다. 인증에 사용할 수 있는 몇 가지 방법이 있습니다. 자세한 내용과 지침은 [**Microsoft Sentinel에 플레이북 인증**](authenticate-playbooks-to-sentinel.md)을 참조하세요.

### <a name="add-actions"></a>작업 추가

이제 플레이북을 호출할 때 발생하는 상황을 정의할 수 있습니다. **새 단계** 를 선택하여 작업, 논리 조건, 루프 또는 전환 사례 조건을 추가할 수 있습니다. 새 단계를 선택하면 디자이너에서 새 프레임이 열리는데, 여기서 상호 작용할 시스템 또는 애플리케이션을 선택하거나 설정할 조건을 선택할 수 있습니다. 프레임 맨 위에 있는 검색 창에 시스템 또는 애플리케이션 이름을 입력한 다음, 제공되는 결과 중에서 선택합니다.

이 모든 단계에서 아무 필드를 클릭하면 **동적 콘텐츠** 및 **식** 이라는 두 가지 메뉴가 있는 패널이 표시됩니다. **동적 콘텐츠** 메뉴에서는 관련된 모든 엔터티의 값과 특성을 포함하여 플레이북에 전달된 경고 또는 인시던트의 특성에 대한 참조를 추가할 수 있습니다. **식** 메뉴에서는 함수 라이브러리에서 선택하여 단계에 논리를 더 추가할 수 있습니다.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/logic-app.png" alt-text="논리 앱 디자이너":::

이 스크린샷은 이 문서의 시작 부분에 나오는 예제에서 설명한 플레이북을 만들 때 추가할 작업과 조건을 보여줍니다. 유일한 차이점으로, 여기에 표시된 플레이북에서는 **인시던트 트리거** 대신 **경고 트리거** 를 사용합니다. 즉, 자동화 규칙이 아닌 분석 규칙에서 직접 이 플레이북을 호출합니다. 플레이북을 호출하는 두 가지 방법은 아래에서 설명합니다.

## <a name="automate-threat-responses"></a>위협 응답 자동화

플레이북을 만들고, 트리거를 정의하고, 조건을 설정하고, 플레이북이 수행할 작업과 생성할 출력을 규정했습니다. 이제 플레이북이 실행되는 조건을 결정하고 해당 조건이 충족될 때 실행할 자동화 메커니즘을 설정해야 합니다.

### <a name="respond-to-incidents"></a>인시던트에 대응

플레이북을 사용하여 인시던트가 생성될 때 실행되는 [자동화 규칙](automate-incident-handling-with-automation-rules.md)을 만들어 **인시던트** 에 대응하면 자동화 규칙은 플레이북을 호출합니다.

자동화 규칙을 만들려면 다음을 수행합니다.

1. Microsoft Sentinel 탐색 메뉴의 **자동화** 블레이드에서 상단 메뉴에서 **만들기** 를 선택한 다음, **새 규칙 추가** 를 선택합니다.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/add-new-rule.png" alt-text="새 규칙 추가":::

1. **새 자동화 규칙 만들기** 패널이 열립니다. 규칙 이름을 입력합니다.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/create-automation-rule.png" alt-text="자동화 규칙 만들기":::

1. 자동화 규칙을 특정 분석 규칙에만 적용하려면 **분석 규칙 이름** 조건을 수정하여 분석 규칙을 지정합니다.

1. 이 자동화 규칙이 활성화되는 다른 조건을 추가합니다. **조건 추가** 를 클릭하고 드롭다운 목록에서 조건을 선택합니다. 조건 목록은 경고 세부 정보 및 엔터티 식별자 필드로 채워집니다.

1. 이 자동화 규칙을 적용할 작업을 선택합니다. 사용 가능한 작업으로는 **소유자 할당**, **상태 변경**, **심각도 변경**, **태그 추가** 및 **플레이북 실행** 이 있습니다. 원하는 만큼 작업을 추가할 수 있습니다.

1. **플레이북 실행** 작업을 추가하는 경우 사용 가능한 플레이북 드롭다운 목록에서 선택하라는 메시지가 표시됩니다. **인시던트 트리거** 로 시작하는 플레이북만 자동화 규칙에서 실행할 수 있으므로 이러한 플레이북만 목록에만 표시됩니다.<a name="permissions-to-run-playbooks"></a>

    > [!IMPORTANT]
    > 자동화 규칙에서 플레이북을 실행하려면 명시적 권한을 Microsoft Sentinel에 부여해야 합니다. 드롭다운 목록에서 플레이북이 "회색으로 표시"되면 해당 플레이북의 리소스 그룹에 대한 권한이 Sentinel에 없다는 뜻입니다. 권한을 할당하려면 **플레이북 권한 관리** 링크를 클릭합니다.
    > 열리는 **권한 관리** 패널에서, 실행하려는 플레이북이 포함된 리소스 그룹의 확인란을 선택하고 **적용** 을 클릭합니다.
    > :::image type="content" source="./media/tutorial-respond-threats-playbook/manage-permissions.png" alt-text="사용 권한 관리":::
    > - 권한을 Microsoft Sentinel에 부여하려는 리소스 그룹에 대한 **소유자** 권한을 본인이 갖고 있어야 하며, 실행하려는 플레이북이 포함된 리소스 그룹에 대한 **논리 앱 기여자** 역할을 본인이 갖고 있어야 합니다.
    > - 다중 테넌트 배포에서 실행하려는 플레이북이 다른 테넌트에 있는 경우 플레이북의 테넌트에서 플레이북을 실행할 수 있는 권한을 Microsoft Sentinel에 부여해야 합니다.
    >    1. 플레이북 테넌트의 Microsoft Sentinel 탐색 메뉴에서 **설정** 을 선택합니다.
    >    1. **설정** 블레이드에서 **설정** 탭을 선택한 다음, **플레이북 권한** 확장기를 선택합니다.
    >    1. **권한 구성** 단추를 클릭하여 위에서 언급한 **권한 관리** 패널을 열고, 패널의 설명에 따라 계속합니다.
    > - **MSSP** 시나리오에서 서비스 공급자 테넌트에 로그인하는 동안 생성된 자동화 규칙에서 [고객 테넌트에서 플레이북을 실행](automate-incident-handling-with-automation-rules.md#permissions-in-a-multi-tenant-architecture)하려면 **_두 테넌트_*모두에서 플레이북을 실행할 수 있는 Microsoft Sentinel 권한을 부여해야 합니다.* 고객** 테넌트에서 이전 글머리 기호 지점의 다중 테넌트 배포에 대한 지침을 따릅니다. **서비스 공급자** 테넌트에서 Azure Lighthouse 온보딩 템플릿에 Azure Security Insights 앱을 추가해야 합니다.
    >    1. Azure Portal에서 **Azure Active Directory** 로 이동합니다.
    >    1. **엔터프라이즈 애플리케이션** 을 클릭합니다.
    >    1. **애플리케이션 유형** 을 선택하고 **Microsoft 애플리케이션** 에서 필터링합니다.
    >    1. 검색 상자에 **Azure Security Insights** 를 입력합니다.
    >    1. **개체 ID** 필드를 복사합니다. 기존 Azure Lighthouse 위임에 이 추가 권한 부여를 추가해야 합니다.
    >
    >    **Microsoft Sentinel Automation 기여자** 역할에는 고정 GUID(`f4c81013-99ee-4d62-a7ee-b3f1f648599a`)가 있습니다. 샘플 Azure Lighthouse 권한 부여는 매개 변수 템플릿에서 다음과 같습니다.
    >    
    >    ```json
    >    {
    >        "principalId": "<Enter the Azure Security Insights app Object ID>", 
    >        "roleDefinitionId": "f4c81013-99ee-4d62-a7ee-b3f1f648599a",
    >        "principalIdDisplayName": "Microsoft Sentinel Automation Contributors" 
    >    }
    >    ```

1. 원한다면 자동화 규칙의 만료 날짜를 설정합니다.

1. 자동화 규칙 시퀀스에서 이 규칙이 실행될 위치를 결정하는 숫자를 **순서** 에 입력합니다.

1. **적용** 을 클릭합니다. 완료되었습니다!

자동화 규칙을 만드는 [다른 방법을 알아보세요](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules).

### <a name="respond-to-alerts"></a>경고에 대응

플레이북을 사용하여 **경고** 에 대응할 수 있습니다. [분석 규칙 마법사](detect-threats-custom.md)에서 경고가 생성될 때 실행되는 **분석 규칙** 을 만들거나 기존 규칙을 편집하고 플레이북을 자동 응답으로 선택하면 됩니다.

1. Microsoft Sentinel 탐색 메뉴의 **분석** 블레이드에서 대응을 자동화할 분석 규칙을 선택하고, 세부 정보 창에서 **편집** 을 클릭합니다.

1. **분석 규칙 마법사 - 기존 규칙 편집** 페이지에서 **자동 응답** 탭을 선택합니다.

   :::image type="content" source="./media/tutorial-respond-threats-playbook/automated-response-tab.png" alt-text="자동 응답 탭":::

1. 드롭다운 목록에서 플레이북을 선택합니다. 플레이북을 여러 개 선택해도 되지만, **경고 트리거** 를 사용하는 플레이북만 사용할 수 있습니다.

1. **검토 및 만들기** 탭에서 **저장** 을 선택합니다.

## <a name="run-a-playbook-on-demand"></a>온디맨드로 플레이북 실행

요청 시 플레이북을 실행할 수도 있습니다.

 > [!NOTE]
 > **경고 트리거** 를 사용하는 플레이북만 요청 시 실행할 수 있습니다.

요청 시 플레이북을 실행하려면

1. **인시던트** 페이지에서 인시던트를 선택하고 **전체 세부 정보 보기** 를 클릭합니다.

2. **경고** 탭에서 플레이북을 실행하려는 경고를 클릭하고 오른쪽으로 스크롤하여 **플레이북 보기** 를 클릭한 후 구독에서 사용 가능한 플레이북 목록에서 **실행** 할 플레이북을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Microsoft Sentinel에서 플레이북과 자동화 규칙을 사용하여 위협에 대응하는 방법을 알아보았습니다. 
- Microsoft Sentinel을 사용하여 [사전에 위협을 탐지하는 방법](hunting.md)을 알아보세요.
