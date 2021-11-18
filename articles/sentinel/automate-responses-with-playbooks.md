---
title: Microsoft Sentinel | 플레이북을 사용하여 위협 대응 자동화 Microsoft Docs
description: 이 문서에서는 Microsoft Sentinel의 자동화에 대해 설명하고 플레이북을 사용하여 위협 방지 및 대응을 자동화하는 방법을 보여줍니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: ce0edbdd0464772264e29315e463d989b79bbaa3
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718092"
---
# <a name="automate-threat-response-with-playbooks-in-microsoft-sentinel"></a>Microsoft Sentinel에서 플레이북을 사용하여 위협 대응 자동화

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 Microsoft Sentinel 플레이북의 정의와 이를 사용하여 SOAR(보안 오케스트레이션, 자동화 및 응답) 작업을 구현하여 시간과 리소스를 절약하면서 더 나은 결과를 달성하는 방법을 설명합니다.

## <a name="what-is-a-playbook"></a>플레이북이란?

일반적으로 SIEM/SOC 팀은 가용 직원을 압도할 만큼 아주 많은 양의 보안 경고 및 인시던트를 정기적으로 받습니다. 그 결과 수많은 경고를 무시하고 많은 인시던트를 미처 조사할 수 없게 되어 조직이 파악하지 못한 공격에 취약한 상태로 노출됩니다.

대부분의 경우 경고 및 인시던트는 특정 및 정의된 수정 작업으로 해결할 수 있는 반복 패턴을 따릅니다.

플레이북은 Microsoft Sentinel에서 루틴으로 실행할 수 있는 이러한 수정 작업의 컬렉션입니다. 플레이북은 [**위협 대응을 자동화하고 오케스트레이션**](tutorial-respond-threats-playbook.md)하는 데 도움이 될 수 있습니다. 각각 분석 규칙 또는 자동화 규칙에 따라 트리거되는 경우 특정 경고나 인시던트에 대응하여 플레이북을 자동으로 실행되도록 설정하거나 수동으로 실행할 수 있습니다.

예를 들어 계정과 머신이 손상된 경우 플레이북은 SOC 팀이 인시던트 알림을 받을 때까지 네트워크에서 머신을 격리하고 계정을 차단할 수 있습니다.

플레이북은 속한 구독 내에서 사용할 수 **있지만, 플레이북** 탭(Automation  블레이드)에는 선택한 구독에서 사용할 수 있는 모든 플레이북이 표시됩니다.

### <a name="playbook-templates"></a>플레이북 템플릿

> [!IMPORTANT]
>
> **플레이북 템플릿은** 현재 **미리 보기로** 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

플레이북 템플릿은 요구 사항에 맞게 사용자 지정할 수 있는 미리 빌드되고 테스트되고 즉시 사용할 수 있는 워크플로입니다. 템플릿은 플레이북을 처음부터 개발할 때 모범 사례에 대한 참조로 사용하거나 새로운 자동화 시나리오에 대한 영감을 제공할 수도 있습니다.

플레이북 템플릿은 플레이북(템플릿의 편집 가능한 복사본)을 만들 때까지 활성 플레이북 자체가 아닙니다.

다음 원본에서 플레이북 템플릿을 얻을 수 있습니다.

- **플레이북 템플릿** 탭(Automation 아래)은 Microsoft Sentinel 커뮤니티에서 제공하는 주요 시나리오를 제공합니다. 동일한 템플릿에서 여러 활성 플레이북을 만들 수 있습니다.

    새 버전의 템플릿이 게시되면 해당 템플릿에서 만든 활성 **플레이북(플레이북** 탭)에 업데이트를 사용할 수 있다는 알림이 레이블로 지정됩니다.

- 플레이북 템플릿은 특정 제품의 컨텍스트에서 [Microsoft Sentinel 솔루션의](sentinel-solutions.md) 일부로 가져올 수도 있습니다. 솔루션을 배포하면 활성 플레이북이 생성됩니다.

- [Microsoft Sentinel GitHub 리포지토리에는](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) 많은 플레이북 템플릿이 포함되어 있습니다. Azure에 배포 단추를 선택하여 Azure 구독에 **배포할** 수 있습니다.

기술적으로 플레이북 템플릿은 관련된 각 연결에 대한 Azure Logic Apps 워크플로 및 API 연결과 같은 여러 리소스로 구성된 [ARM 템플릿입니다.](../azure-resource-manager/templates/index.yml)

### <a name="azure-logic-apps-basic-concepts"></a>Azure Logic Apps 기본 개념

Microsoft Sentinel의 플레이북은 엔터프라이즈 전체의 시스템에서 작업 및 워크플로를 예약, 자동화 및 오케스트레이션하는 데 도움이 되는 클라우드 서비스인 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)기본 제공 워크플로를 기반으로 합니다. 즉, 플레이북은 Logic Apps 기본 제공 템플릿을 모두 활용하고 사용자 지정할 수 있습니다.

> [!NOTE]
> Azure Logic Apps는 별도의 리소스이므로 추가 요금이 적용될 수 있습니다. 자세한 내용은 [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) 가격 책정 페이지를 방문하세요.

Azure Logic Apps는 커넥터를 사용하여 다른 시스템 및 서비스와 통신합니다. 커넥터에 대한 간략한 설명 및 커넥터의 몇 가지 중요한 특성은 다음과 같습니다.

- **관리형 커넥터:** 특정 제품 또는 서비스에 대한 API 호출을 래핑하는 작업 및 트리거 세트입니다. Azure Logic Apps는 Microsoft 및 타사 서비스와 통신할 수 있는 수백 개의 커넥터를 제공합니다.
  - [모든 Logic Apps 커넥터 및 해당 설명서의 목록](/connectors/connector-reference/)

- **사용자 지정 커넥터:** 미리 빌드된 커넥터로 사용할 수 없는 서비스와 통신하는 것이 좋습니다. 사용자 지정 커넥터는 커넥터를 만들고 공유하고 자체 트리거와 작업을 정의할 수 있도록 하여 이러한 요구를 해결합니다.
  - [고유한 사용자 지정 Logic Apps 커넥터 만들기](/connectors/custom-connectors/create-logic-apps-connector)

- **Microsoft Sentinel 커넥터:** Microsoft Sentinel과 상호 작용하는 플레이북을 만들려면 Microsoft Sentinel 커넥터를 사용합니다.
  - [Microsoft Sentinel 커넥터 설명서](/connectors/azuresentinel/)

- **트리거:** 플레이북을 시작하는 커넥터 구성 요소입니다. 플레이북이 트리거될 때 수신하는 데 필요한 스키마를 정의합니다. Microsoft Sentinel 커넥터에는 현재 두 개의 트리거가 있습니다.
  - [경고 트리거](/connectors/azuresentinel/#triggers): 플레이북이 경고를 입력으로 받습니다.
  - [인시던트 트리거](/connectors/azuresentinel/#triggers): 플레이북이 포함된 모든 경고 및 엔터티와 함께 인시던트를 입력으로 받습니다.

    > [!IMPORTANT]
    >
    > 플레이북의 **인시던트 트리거** 기능은 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

- **작업:** 트리거 후 발생하는 모든 단계가 작업입니다. 병렬 또는 복합 조건의 매트릭스에서 순차적으로 정렬할 수 있습니다.

- **동적 필드:** 다음 작업에 사용할 수 있는 트리거 및 작업의 출력 스키마에 의해 결정되고 실제 출력으로 채워진 임시 필드입니다.

### <a name="permissions-required"></a>필요한 사용 권한

 SecOps 팀에게 SOAR(보안 오케스트레이션, 자동화 및 응답) 작업에 Logic Apps 사용할 수 있는 기능( 즉, 플레이북을 만들고 실행하는 기능)을 Microsoft Sentinel에서 보안 운영 팀의 특정 멤버 또는 전체 팀에 할당할 수 있습니다. 다음은 사용 가능한 여러 역할과 해당 역할에 할당해야 하는 작업에 대한 설명입니다.

#### <a name="azure-roles-for-logic-apps"></a>Logic Apps에 대한 Azure 역할

- **논리 앱 기여자** 역할을 사용하면 논리 앱을 관리하고 플레이북을 실행할 수 있지만, 액세스 권한을 변경할 수 없습니다(변경하려면 **소유자** 역할이 필요함).
- **논리 앱 운영자** 역할을 사용하면 논리 앱을 읽고 사용하거나 사용하지 않도록 설정할 수 있지만, 편집하거나 업데이트할 수 없습니다.

#### <a name="azure-roles-for-sentinel"></a>Sentinel에 대한 Azure 역할

- **Microsoft Sentinel 기여자** 역할을 사용하면 분석 규칙에 플레이북을 연결할 수 있습니다.
- **Microsoft Sentinel 응답기** 역할을 사용하면 플레이북을 수동으로 실행할 수 있습니다.
- **Microsoft Sentinel Automation Contributor를** 사용하면 자동화 규칙이 플레이북을 실행할 수 있습니다. 다른 용도로는 사용되지 않습니다.

#### <a name="learn-more"></a>자세한 정보

- [Azure Logic Apps의 Azure 역할에 대해 자세히 알아보세요](../logic-apps/logic-apps-securing-a-logic-app.md#access-to-logic-app-operations).
- [Microsoft Sentinel의 Azure 역할에 대해 자세히 알아봅니다.](roles.md)

## <a name="steps-for-creating-a-playbook"></a>플레이북을 만드는 단계

- [자동화 시나리오 정의](#use-cases-for-playbooks)

- [Azure 논리 앱 빌드](tutorial-respond-threats-playbook.md)

- [논리 앱 테스트](#run-a-playbook-manually-on-an-alert)

- 플레이북을 [자동화 규칙](#incident-creation-automated-response) 또는 [분석 규칙](#alert-creation-automated-response)에 연결하거나 [필요할 때 수동으로 실행](#run-a-playbook-manually-on-an-alert)합니다.

### <a name="use-cases-for-playbooks"></a>플레이북 사용 사례

Azure Logic Apps 플랫폼은 수백 개의 작업과 트리거를 제공하므로 거의 모든 자동화 시나리오를 만들 수 있습니다. Microsoft Sentinel은 다음 SOC 시나리오부터 권장합니다.

#### <a name="enrichment"></a>보강

**더 나은 결정을 내릴 수 있도록 데이터를 수집하고 인시던트에 연결합니다.**

예를 들면 다음과 같습니다.

MICROSOFT Sentinel 인시던트 IP 주소 엔터티를 생성하는 분석 규칙에 의해 경고에서 생성되었습니다.

인시던트는 다음 단계에 따라 플레이북을 실행하는 자동화 규칙을 트리거합니다.

- [새 Microsoft Sentinel 인시던트 를 만들 때 시작합니다.](/connectors/azuresentinel/#triggers) 인시던트에 표시되는 엔터티는 인시던트 트리거의 동적 필드에 저장됩니다.

- 각 IP 주소에 대해 [바이러스 합계](https://www.virustotal.com/)와 같은 외부 위협 인텔리전스 공급자를 쿼리하여 더 많은 데이터를 검색합니다.

- 반환된 데이터 및 인사이트를 인시던트의 주석으로 추가합니다.

#### <a name="bi-directional-sync"></a>양방향 동기화

**플레이북을 사용하여 Microsoft Sentinel 인시던트 및 다른 티켓 시스템을 동기화할 수 있습니다.**

예를 들면 다음과 같습니다.

모든 인시던트 생성에 대한 자동화 규칙을 만들고 ServiceNow에서 티켓을 여는 플레이북을 연결합니다.

- [새 Microsoft Sentinel 인시던트 를 만들 때 시작합니다.](/connectors/azuresentinel/#triggers)

- [ServiceNow](/connectors/service-now/)에서 새 티켓을 만듭니다.

- 간편한 피벗을 위해 인시던트 이름, 중요 필드 및 Microsoft Sentinel 인시던트 URL을 티켓에 포함합니다.

#### <a name="orchestration"></a>오케스트레이션

**SOC 채팅 플랫폼을 사용하여 인시던트 큐를 보다 효과적으로 제어할 수 있습니다.**

예를 들면 다음과 같습니다.

Microsoft Sentinel 인시던트 는 사용자 이름 및 IP 주소 엔터티를 생성하는 분석 규칙에 의해 경고에서 생성되었습니다.

인시던트는 다음 단계에 따라 플레이북을 실행하는 자동화 규칙을 트리거합니다.

- [새 Microsoft Sentinel 인시던트 를 만들 때 시작합니다.](/connectors/azuresentinel/#triggers)

- [Microsoft Teams](/connectors/teams/) 또는 [Slack](/connectors/slack/)의 보안 운영 채널로 메시지를 전송하여 보안 분석가가 이 인시던트를 알게 합니다.

- 메일로 경고에 나와 있는 모든 정보를 선임 네트워크 관리자 및 보안 관리자에게 보냅니다. 메일 메시지에는 **차단** 및 **무시** 사용자 옵션 단추가 포함되어 있습니다.

- 관리자로부터 응답을 받을 때까지 기다렸다가 계속 실행합니다.

- 관리자가 **차단** 을 선택한 경우 방화벽에 명령을 보내 경고에 나와 있는 IP 주소를 차단하고, 다른 사용자를 사용하지 않도록 설정하려면 Azure AD에 다른 명령을 보냅니다.

#### <a name="response"></a>응답

**최소한의 사용자 종속성으로 위협에 즉시 대응합니다.**

두 가지 예:

**예 1:** [Azure AD ID 보호](../active-directory/identity-protection/overview-identity-protection.md)에서 검색한 손상된 사용자를 나타내는 분석 규칙에 응답합니다.

   - [새 Microsoft Sentinel 인시던트 를 만들 때 시작합니다.](/connectors/azuresentinel/#triggers)

   - 손상된 것으로 의심되는 인시던트의 각 사용자 엔터티에 대해 다음을 수행합니다.

     - 사용자에게 의심스러운 조치를 취했는지 확인을 요청하는 Teams 메시지를 보냅니다.

     - Azure AD ID 보호에서 [사용자의 상태가 손상된 것으로 확인](/connectors/azureadip/#confirm-a-risky-user-as-compromised)되는지 파악합니다. Azure AD ID 보호는 이 사용자에게 **위험** 레이블을 지정하고 이미 구성된 적용 정책을 적용합니다. 예를 들어 다음에 로그인할 때 사용자에게 MFA를 사용하도록 요구합니다.

        > [!NOTE]
        > 플레이북은 사용자에 대한 적용 작업을 시작하지 않으며 적용 정책 구성도 시작하지 않습니다. 이미 정의된 정책을 적절하게 적용하도록 Azure AD ID 보호에만 지시합니다. 적용 사항은 전적으로 Azure AD ID 보호에 정의되는 정책에 따라 달라집니다.

**예 2:** [엔드포인트용 Microsoft Defender](/windows/security/threat-protection/)에서 검색한 손상된 머신을 나타내는 분석 규칙에 응답합니다.

   - [새 Microsoft Sentinel 인시던트 를 만들 때 시작합니다.](/connectors/azuresentinel/#triggers)

   - Microsoft Sentinel의 **엔터티 - 호스트 받기** 작업을 사용하여 인시던트 엔터티에 포함된 의심스러운 머신을 구문 분석합니다.

   - 엔드포인트용 Microsoft Defender에 대해 명령을 실행하여 경고의 [머신을 격리](/connectors/wdatp/#actions---isolate-machine)합니다.

## <a name="how-to-run-a-playbook"></a>플레이북을 실행하는 방법

플레이북은 **수동** 또는 **자동** 으로 실행할 수 있습니다.

수동으로 실행한다는 것은 경고를 받으면 선택한 경고에 대한 응답으로 요청 시 플레이북을 실행하도록 선택할 수 있다는 것을 의미합니다. 현재 이 기능은 인시던트가 아니라 경고에 대해서만 지원됩니다.

이러한 작업을 자동으로 실행하는 것은 분석 규칙의 자동 응답으로 설정(경고의 경우)하거나 자동화 규칙의 작업으로 설정(인시던트의 경우)하는 것을 의미합니다. [자동화 규칙에 대해 자세히 알아보세요](automate-incident-handling-with-automation-rules.md).

### <a name="set-an-automated-response"></a>자동화된 응답 설정

보안 운영 팀은 반복되는 인시던트 및 경고 유형에 대한 일상적인 응답을 완벽하게 자동화하여 워크로드를 대폭 줄일 수 있으므로 고유한 인시던트 및 경고, 패턴 분석, 위협 요소 파악 등에 집중할 수 있습니다.

자동화된 응답을 설정하면 경고를 생성하는 것 외에도 분석 규칙이 트리거될 때마다 규칙은 플레이북을 실행하여 규칙에 따라 생성된 경고를 입력으로 받게 됩니다.

경고가 인시던트를 만드는 경우 인시던트는 경고를 통해 생성된 인시던트를 입력으로 받게 되는 플레이북을 실행할 수 있는 자동화 규칙을 트리거합니다.

#### <a name="alert-creation-automated-response"></a>경고 만들기 자동화된 응답

경고 생성에 의해 트리거되고 해당 입력으로 경고를 수신하는 플레이북의 경우(첫 번째 단계는 "Microsoft Sentinel 경고가 트리거되는 경우") 플레이북을 분석 규칙에 연결합니다.

1. 자동화된 응답을 정의하려는 경고를 생성하는 [분석 규칙](detect-threats-custom.md)을 편집합니다.

1. **자동화된 응답** 탭의 **경고 자동화** 에서 경고가 만들어질 때 이 분석 규칙에서 트리거할 플레이북을 선택합니다.

#### <a name="incident-creation-automated-response"></a>인시던트 만들기 자동화된 응답

인시던트 생성에 의해 트리거되고 인시던트 입력으로 수신되는 플레이북의 경우(첫 번째 단계는 "Microsoft Sentinel 인시던트 트리거되는 경우") 자동화 규칙을 만들고 **플레이북 실행** 작업을 정의합니다. 이 작업은 두 가지 방법으로 수행할 수 있습니다.

- 자동화된 응답을 정의하려는 인시던트를 생성하는 분석 규칙을 편집합니다. **자동화된 응답** 탭의 **인시던트 자동화** 에서 자동화 규칙을 만듭니다. 그러면 이 분석 규칙에 대한 자동화된 응답만 생성됩니다.

- **자동화** 블레이드의 **자동화 규칙** 탭에서 새 자동화 규칙을 만들고 적절한 조건 및 원하는 작업을 지정합니다. 이 자동화 규칙은 지정된 조건을 충족하는 모든 분석 규칙에 적용됩니다.

    > [!NOTE]
    > **Microsoft Sentinel 자동화 규칙에는 플레이북을 실행할 수 있는 권한이 필요합니다.**
    >
    > 자동화 규칙에서 플레이북을 실행하기 위해 Microsoft Sentinel은 특별히 권한이 부여된 서비스 계정을 사용합니다. 사용자 계정과 달리 이 계정을 사용하면 서비스의 보안 수준을 높이고 자동화 규칙 API를 사용하여 CI/CD 사용 사례를 지원할 수 있습니다.
    >
    > 이 계정에는 플레이북이 있는 리소스 그룹에 대한 명시적 **권한(Microsoft Sentinel Automation 기여자** 역할 형식)이 부여되어야 합니다. 이때 모든 자동화 규칙은 해당 리소스 그룹에서 모든 플레이북을 실행할 수 있습니다.
    >
    > **플레이북 실행** 작업을 자동화 규칙에 추가하면 선택한 플레이북의 드롭다운 목록이 표시됩니다. Microsoft Sentinel에 권한이 없는 플레이북은 사용할 수 없음("회색으로 표시")으로 표시됩니다. 플레이북 권한 관리 링크를 선택하여 해당 지점에서 Microsoft Sentinel에 권한을 부여할 수 **있습니다.**
    >
    > 다중 테넌트([Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)) 시나리오에서 플레이북을 호출하는 자동화 규칙이 다른 테넌트에 있는 경우에도 플레이북이 있는 테넌트에 대한 권한을 정의해야 합니다. 권한을 정의하려면 플레이북의 리소스 그룹에 대한 **소유자** 권한이 있어야 합니다.
    >
    > **MSSP(관리형 보안 서비스 공급자)** 가 있는 고유한 시나리오가 있습니다. 여기서 서비스 공급자는 자체 테넌트에 로그인한 상태에서 [Azure Lighthouse](../lighthouse/index.yml)를 사용하여 고객의 작업 영역에 자동화 규칙을 만듭니다. 그러면 이 자동화 규칙이 고객의 테넌트에 속한 플레이북을 호출합니다. 이 경우 Microsoft Sentinel에 **_두 테넌트_ _에 대한 사용 권한을 부여해야 *합니다. 고객 테넌트에서는*** 일반적인 다중 테넌트 시나리오와 마찬가지로 _ 플레이북 관리 권한 패널에서 권한을 부여합니다. 서비스 공급자 테넌트에서 관련 권한을 부여하려면 플레이북이 있는 리소스 그룹에 **Microsoft Sentinel Automation 기여자** 역할을 사용하여 **Azure 보안 Insights** 앱에 대한 액세스 권한을 부여하는 추가 Azure Lighthouse 위임을 추가해야 합니다. [이 위임을 추가하는 방법을 알아봅니다](tutorial-respond-threats-playbook.md#permissions-to-run-playbooks).

[자동화 규칙을 만드는 방법에 대한 전체 지침](tutorial-respond-threats-playbook.md#respond-to-incidents)을 참조하세요.

### <a name="run-a-playbook-manually-on-an-alert"></a>경고에서 수동으로 플레이북 실행

수동 트리거는 다음 블레이드의 Microsoft Sentinel 포털에서 사용할 수 있습니다.

- **인시던트** 보기에서 특정 인시던트를 선택하고, **경고** 탭을 연 다음 경고를 선택합니다.

- **조사** 에서 특정 경고를 선택합니다.

1. 선택한 경고에 대해 **플레이북 보기** 를 클릭합니다. **Microsoft Sentinel 경고가 트리거되고** 액세스할 수 있는 경우로 시작하는 모든 플레이북 목록이 표시됩니다.

1. 특정 플레이북의 줄에서 **실행** 을 클릭하여 트리거합니다.

1. **실행** 탭을 선택하여 이 경고에 대해 플레이북이 실행된 모든 시간 목록을 확인합니다. 완료된 모든 실행이 이 목록에 표시되는 데 몇 초 정도 걸릴 수 있습니다.

1. 특정 실행을 클릭하면 전체 실행 로그가 Logic Apps에 열립니다.

### <a name="run-a-playbook-manually-on-an-incident"></a>인시던트에서 수동으로 플레이북 실행

아직 지원되지 않습니다.

## <a name="manage-your-playbooks"></a>플레이북 관리

**플레이북** 탭에는 사용자가 액세스할 수 있는 모든 플레이북 목록이 표시되고, 현재 Azure에 표시되는 구독으로 필터링됩니다. 구독 필터는 전역 페이지 헤더의 **디렉터리 + 구독** 메뉴에서 사용할 수 있습니다.

플레이북 이름을 클릭하면 Logic Apps의 플레이북 기본 페이지로 이동합니다. **상태** 열에 사용 여부가 표시됩니다.

**트리거 종류** 는 이 플레이북을 시작하는 Logic Apps 트리거를 나타냅니다.

| 트리거 종류 | 플레이북의 구성 요소 유형을 나타냅니다. |
|-|-|
| **Microsoft Sentinel 인시던트/경고** | 플레이북이 Sentinel 트리거 중 하나(경고, 인시던트)로 시작됩니다. |
| **Microsoft Sentinel 작업 사용** | 플레이북은 비 Sentinel 트리거로 시작되지만 Microsoft Sentinel 작업을 사용합니다. |
| **기타** | 플레이북에는 Sentinel 구성 요소가 포함되어 있지 않습니다. |
| **초기화 안 됨** | 플레이북이 만들어졌지만, 구성 요소(트리거 또는 작업)가 포함되어 있지 않습니다. |
|

플레이북의 논리 앱 페이지에서 실행된 모든 시간에 대한 로그 및 결과(성공 또는 실패 및 기타 세부 정보)를 포함하여 플레이북에 대한 자세한 정보를 볼 수 있습니다. 적절한 권한이 있는 경우 Logic Apps 디자이너를 입력하고 플레이북을 직접 편집할 수도 있습니다.

### <a name="api-connections"></a>API 연결

API 연결은 Logic Apps를 다른 서비스에 연결하는 데 사용됩니다. Logic Apps 커넥터에 대해 새 인증을 수행할 때마다 **API 연결** 형식의 새 리소스가 생성되고 서비스에 대한 액세스를 구성할 때 제공되는 정보가 포함됩니다.

모든 API 연결을 보려면 Azure Portal의 헤더 검색 상자에 *API 연결* 을 입력합니다. 관심 있는 열을 기록해 둡니다.

- 표시 이름 - 연결할 때마다 연결에 제공하는 "친숙한" 이름입니다.
- 상태 - 연결 상태(오류, 연결됨)를 나타냅니다.
- 리소스 그룹 - API 연결은 플레이북(Logic Apps) 리소스의 리소스 그룹에 만들어집니다.

API 연결을 보는 또 다른 방법은 **모든 리소스** 블레이드로 이동하여 *API 연결* 형식으로 필터링하는 것입니다. 이러한 방식으로 여러 연결을 한 번에 선택하고, 태그를 지정하고, 삭제할 수 있습니다.

기존 연결에 대한 권한 부여를 변경하려면 연결 리소스를 입력하고 **API 연결 편집** 을 선택합니다.

## <a name="recommended-playbooks"></a>권장 플레이북

[Microsoft Sentinel GitHub 리포지토리에서](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)다음과 같은 권장 플레이북 및 기타 유사한 플레이북을 사용할 수 있습니다.

- **알림 플레이북** 은 경고나 인시던트가 생성될 때 트리거되고 구성된 대상으로 알림을 보냅니다.

    - [Microsoft Teams 채널에 메시지 게시](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Post-Message-Teams)
    - [Outlook 메일 알림 보내기](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification)
    - [Slack 채널에 메시지 게시](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Post-Message-Slack)

- **차단 플레이북** 은 경고나 인시던트가 생성될 때 트리거되고 계정, IP 주소, 호스트와 같은 엔터티 정보를 수집하며 추가 작업에서 차단합니다.

    - [IP 주소를 차단하라는 메시지 표시](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Block-IPs-on-MDATP-Using-GraphSecurity).
    - [AAD 사용자 차단](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Block-AADUser)
    - [AAD 사용자 암호 재설정](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Reset-AADUserPassword/)
    - [머신을 격리하라는 메시지 표시](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Isolate-AzureVMtoNSG)

- 플레이 **북 만들기, 업데이트 또는 닫기** 는 Microsoft 센티널, Microsoft 365 보안 서비스 또는 기타 티켓 시스템에서 인시던트를 생성, 업데이트 또는 닫을 수 있습니다.

    - [인시던트의 심각도 변경](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Change-Incident-Severity)
    - [ServiceNow 인시던트 만들기](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Create-SNOW-record)


## <a name="next-steps"></a>다음 단계

- [자습서: 플레이 북을 사용 하 여 Microsoft 센티널에서 위협 응답 자동화](tutorial-respond-threats-playbook.md)
