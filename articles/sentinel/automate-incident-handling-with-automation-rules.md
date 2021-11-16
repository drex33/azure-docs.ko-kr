---
title: Microsoft 센티널에서 인시던트 처리 자동화 | Microsoft Docs
description: 이 문서에서는 보안 위협에 대응하여 SOC의 효율성과 효과를 극대화하기 위해 자동화 규칙을 사용하여 인시던트 처리를 자동화하는 방법을 설명합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 52f716bf8a5a4457f1f61545e079c17f16c708c3
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522159"
---
# <a name="automate-incident-handling-in-microsoft-sentinel-with-automation-rules"></a>Automation 규칙을 사용 하 여 Microsoft 센티널에서 인시던트 처리 자동화

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에서는 Microsoft 센티널 automation 규칙의 정의 및이 규칙을 사용 하 여 보안 오케스트레이션, Automation and Response (대화 충성도) 작업을 구현 하 고 SOC의 효율성을 높이고 시간과 리소스를 절약 하는 방법에 대해 설명 합니다.

> [!IMPORTANT]
>
> - **자동화 규칙** 기능은 현재 **미리 보기** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="what-are-automation-rules"></a>자동화 규칙이란?

Automation 규칙은 Microsoft 센티널의 새로운 개념입니다. 이 기능을 통해 사용자는 인시던트 처리의 자동화를 중앙에서 관리할 수 있습니다. 자동화 규칙을 사용하면 이전처럼 경고가 아닌 인시던트에 플레이북을 할당하는 것 외에도, 한 번에 여러 분석 규칙에 대한 응답을 자동화하고, 플레이북 없이도 인시던트에 자동으로 태그를 지정하거나, 할당하거나, 종료하고, 실행되는 작업의 순서를 제어할 수 있습니다. Automation 규칙은 Microsoft 센티널에서 자동화 사용을 간소화 하 고 인시던트 오케스트레이션 프로세스에 대 한 복잡 한 워크플로를 단순화할 수 있습니다.

## <a name="components"></a>구성 요소

자동화 규칙은 다음과 같은 여러 구성 요소로 구성됩니다.

### <a name="trigger"></a>트리거

인시던트를 만들면 자동화 규칙이 트리거됩니다. 

검토-인시던트는 분석 규칙에 따라 생성 되며,이는 [Microsoft 센티널의 기본 제공 분석 규칙을 사용 하 여 위협 검색](detect-threats-built-in.md)자습서에 설명 된 대로 여러 가지 유형이 있습니다.

### <a name="conditions"></a>조건

복잡한 조건 집합을 정의하여 작업(아래 참조)이 실행될 시점을 제어할 수 있습니다. 조건은 일반적으로 인시던트 및 해당 엔터티의 특성 상태 또는 값을 기반으로 하며, `AND`/`OR`/`NOT`/`CONTAINS` 연산자를 포함할 수 있습니다.

### <a name="actions"></a>동작

작업은 조건(위 참조)이 충족될 때 실행되도록 정의할 수 있습니다. 규칙에서 많은 작업을 정의하고 작업이 실행되는 순서를 선택할 수 있습니다(아래 참조). 다음 작업은 [플레이북의 고급 기능](automate-responses-with-playbooks.md)이 없어도 자동화 규칙을 사용하여 정의할 수 있습니다.

- 인시던트 상태를 변경하여 워크플로를 최신 상태로 유지합니다.

  - "종료됨"으로 변경할 때 [종료 이유](investigate-cases.md#closing-an-incident)를 지정하고 주석을 추가합니다. 이렇게 하면 성능 및 효율성을 추적하고 미세 조정하여 [가양성](false-positives.md)을 줄일 수 있습니다.

- 인시던트의 심각도 변경 – 인시던트에 포함된 엔터티의 존재, 부재, 값 또는 특성에 따라 다시 평가하고 우선 순위를 다시 지정할 수 있습니다.

- 소유자에게 인시던트 할당 - 인시던트를 처리하는 데 가장 적합한 직원 또는 가장 지원이 가능한 직원에게 인시던트 유형을 지시할 수 있습니다.

- 인시던트에 태그 추가 – 주체, 공격자 또는 기타 공통 분모로 인시던트를 분류하는 데 유용합니다.

또한 외부 시스템과 관련된 작업을 포함하여 더욱 복잡한 대응 조치를 수행하기 위해 [**플레이북을 실행**](tutorial-respond-threats-playbook.md)하는 작업을 정의할 수 있습니다. [**인시던트 트리거**](automate-responses-with-playbooks.md#azure-logic-apps-basic-concepts)에 의해 활성화된 플레이북 **만** 자동화 규칙에서 사용할 수 있습니다. 여러 플레이북, 플레이북과 다른 작업의 조합 및 작업이 실행되는 순서를 포함하는 작업을 정의할 수 있습니다.

### <a name="expiration-date"></a>만료 날짜

자동화 규칙의 만료 날짜를 정의할 수 있습니다. 해당 날짜 이후에는 규칙이 사용하지 않도록 설정됩니다. 침투 테스트와 같이 계획된 시간 제한 활동으로 인해 발생하는 "노이즈" 인시던트를 처리(즉, 종료)하는 데 유용합니다.

### <a name="order"></a>주문

자동화 규칙이 실행되는 순서를 정의할 수 있습니다. 이후 자동화 규칙은 이전 자동화 규칙에 따라 처리된 후의 상태에 따라 인시던트의 조건을 평가합니다.

예를 들어 "첫 번째 자동화 규칙"이 인시던트의 심각도를 중간에서 낮음으로 변경하고 "두 번째 자동화 규칙"이 중간 이상의 심각도가 있는 인시던트에 대해서만 실행되도록 정의된 경우 해당 인시던트에 대해 실행되지 않습니다.

## <a name="common-use-cases-and-scenarios"></a>일반적인 사용 사례 및 시나리오

### <a name="incident-triggered-automation"></a>인시던트 트리거 자동화

지금까지 경고만 플레이북을 사용하여 자동화된 응답을 트리거할 수 있습니다. 이제 인시던트는 자동화 규칙을 사용하여 인시던트를 만들 때 새로운 인시던트 트리거 플레이북([특수 권한 필요](#permissions-for-automation-rules-to-run-playbooks))을 포함할 수 있는 자동화된 응답 체인을 트리거할 수 있습니다. 

### <a name="trigger-playbooks-for-microsoft-providers"></a>Microsoft 공급자에 대해 플레이북 트리거

자동화 규칙은 경고를 통해 생성된 인시던트에 규칙을 적용하여 Microsoft 보안 경고 처리를 자동화하는 방법을 제공합니다. 자동화 규칙은 플레이북([특수 권한이 필요함](#permissions-for-automation-rules-to-run-playbooks))을 호출하고 경고 및 엔터티를 비롯한 모든 세부 정보와 함께 인시던트를 전달합니다. 일반적으로 Microsoft 센티널 모범 사례는 보안 작업의 초점으로 인시던트 큐를 사용 하는 것입니다.

Microsoft 보안 경고에 다음이 포함됩니다.

- 클라우드 앱 용 Microsoft Defender
- Azure AD ID 보호
- Microsoft Defender for Cloud
- IoT 용 Defender (이전의 IoT 용 Azure Security Center)
- Microsoft Defender for Office 365(이전의 Office 365 ATP)
- Microsoft Defender for Endpoint(이전 명칭 MDATP)
- ID용 Microsoft Defender(이전 Azure ATP)

### <a name="multiple-sequenced-playbooksactions-in-a-single-rule"></a>단일 규칙의 여러 개 순차적 플레이북/작업

이제 단일 자동화 규칙에서 작업 및 플레이북의 실행 순서를 거의 완전히 제어할 수 있습니다. 또한 자동화 규칙 자체의 실행 순서를 제어합니다. 이 경우 플레이북을 크게 간소화하여 단일 작업 또는 작고 간단한 일련의 작업으로 축소하고, 여러 조합으로 된 작은 플레이북을 다른 자동화 규칙에 결합할 수 있습니다.

### <a name="assign-one-playbook-to-multiple-analytics-rules-at-once"></a>한 번에 하나의 플레이북을 여러 분석 규칙에 할당

모든 분석 규칙에 자동화하려는 작업(예: 외부 티켓 시스템에서 지원 티켓 생성)이 있는 경우 모든 향후 규칙을 포함하여 임의 또는 모든 분석 규칙에 단일 플레이북을 한 번에 적용할 수 있습니다. 이렇게 하면 간단하지만 반복적인 유지 관리 및 정리 작업을 훨씬 쉽게 할 수 있습니다.

### <a name="automatic-assignment-of-incidents"></a>인시던트 자동 할당

인시던트를 올바른 소유자에게 자동으로 할당할 수 있습니다. SOC에 특정 플랫폼을 전문으로 하는 분석가가 있는 경우 해당 플랫폼과 관련된 모든 인시던트를 해당 분석가에게 자동으로 할당할 수 있습니다.

### <a name="incident-suppression"></a>인시던트 제거

규칙을 사용하면 플레이북을 사용하지 않고 가양성/무해한 양성이라고 하는 인시던트를 자동으로 해결할 수 있습니다. 예를 들어 침투 테스트를 실행하거나 예약된 유지 관리 또는 업그레이드를 수행하거나 자동화 절차를 테스트할 때 SOC에서 무시하려는 다수의 가양성 인시던트가 만들어질 수 있습니다. 시간이 제한된 자동화 규칙은 생성될 때 생성 원인의 설명자로 태그를 지정하면서 인시던트를 자동으로 종료할 수 있습니다.

### <a name="time-limited-automation"></a>시간이 제한된 자동화

자동화 규칙의 만료 날짜를 추가할 수 있습니다. 인시던트 제거 사례 이외에 시간이 제한된 자동화를 보증하는 사례가 있을 수 있습니다. 특정 시간 프레임에서 특정 사용자(예: 인턴 또는 컨설턴트)에게 특정 유형의 인시던트를 할당하는 것이 좋습니다. 시간 프레임을 미리 알고 있는 경우에는 연관성이 종료될 때 규칙을 사용하지 않도록 효과적으로 설정할 수 있으므로 이러한 작업을 기억할 필요가 없습니다.

### <a name="automatically-tag-incidents"></a>인시던트에 자동으로 태그 지정

선택한 조건에 따라 그룹화하거나 분류할 인시던트에 자유 텍스트 태그를 자동으로 추가할 수 있습니다.

## <a name="automation-rules-execution"></a>자동화 규칙 실행

자동화 규칙은 지정하는 순서에 따라 순차적으로 실행됩니다. 각 자동화 규칙은 이전 실행이 완료된 후 실행됩니다. 자동화 규칙 내에서 모든 작업은 정의된 순서대로 순차적으로 실행됩니다.

자동화 규칙 내의 플레이북 작업은 다음 기준에 따라 일부 상황에서 다르게 처리될 수 있습니다.

| 플레이북 런타임 | 자동화 규칙이 다음 작업으로 진행됩니다... |
| ----------------- | --------------------------------------------------- |
| 1초 미만 | 플레이북이 완료된 직후 |
| 2분 미만 | 플레이북 실행이 시작된 후 최대 2분<br>하지만 플레이북이 완료된 후 10초 이내 |
| 2분 초과 | 플레이북 실행이 시작된 후 2분<br>완료 여부와 관계없이 |
|

### <a name="permissions-for-automation-rules-to-run-playbooks"></a>플레이북을 실행할 자동화 규칙에 대한 권한

Microsoft 센티널 automation 규칙은 플레이 북를 실행 하는 경우이 작업에 대해 특별히 권한이 부여 된 특수 Microsoft 센티널 서비스 계정을 사용 합니다. 사용자 계정이 아닌 이 계정을 사용하면 서비스의 보안 수준이 향상됩니다.

자동화 규칙에서 플레이북을 실행하려면 플레이북이 있는 리소스 그룹에 대한 명시적 권한을 이 계정에 부여해야 합니다. 이때 모든 자동화 규칙은 해당 리소스 그룹에서 모든 플레이북을 실행할 수 있습니다.

자동화 규칙을 구성하고 **플레이북 실행** 작업을 추가하는 경우 플레이북 드롭다운 목록이 표시됩니다. Microsoft 센티널에 권한이 없는 플레이 북은 사용할 수 없는 것으로 표시 됩니다 ("회색으로 표시 됨"). **플레이 북 권한 관리** 링크를 선택 하 여 해당 위치에서 playbooks의 리소스 그룹에 Microsoft 센티널 권한을 부여할 수 있습니다.

#### <a name="permissions-in-a-multi-tenant-architecture"></a>다중 테넌트 아키텍처의 사용 권한

자동화 규칙은 작업 영역 간 배포 및 [다중 테넌트 배포](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)를 완벽하게 지원합니다(다중 테넌트의 경우 [Azure Lighthouse](../lighthouse/index.yml) 사용).

따라서 Microsoft Sentinel 배포에서 다중 테넌트 아키텍처를 사용하는 경우 한 테넌트에서 다른 테넌트에서 플레이북을 실행하는 자동화 규칙을 사용할 수 있지만, 플레이북을 실행하는 Sentinel에 대한 권한은 자동화 규칙이 정의된 테넌트에서가 아니라 플레이북이 상주하는 테넌트에서 정의되어야 합니다.

서비스 공급자 테넌트에서 고객 테넌트에서 Microsoft Sentinel 작업 영역을 관리하는 MSSP(관리 보안 서비스 공급자)의 특정 경우 주의가 필요한 두 가지 특정 시나리오가 있습니다.

- **고객 테넌트에서 만들어진 자동화 규칙은 서비스 공급자 테넌트에 있는 플레이북을 실행하도록 구성됩니다.** 

    이 접근 방식은 일반적으로 플레이북의 지적 재산권을 보호하는 데 사용됩니다. 이 시나리오가 작동하는 데 특별한 사항은 없습니다. 자동화 규칙에서 플레이북 작업을 정의하고 플레이북이 있는 관련 리소스 그룹에 대한 Microsoft Sentinel 권한을 부여하는 단계로 가는 **경우(플레이북 관리 권한** 패널 사용) 선택할 수 있는 리소스 그룹 중에서 서비스 공급자 테넌트 에 속하는 리소스 그룹이 표시됩니다. [여기에 설명된 전체 프로세스를 참조하세요](tutorial-respond-threats-playbook.md#respond-to-incidents).

- **고객 작업 영역에서 만들어진 자동화 규칙(서비스 공급자 테넌트에 로그인한 동안)은 고객 테넌트에 있는 플레이북을 실행하도록 구성됩니다**.

    이 구성은 지적 재산권을 보호할 필요가 없을 때 사용됩니다. 이 시나리오가 작동하려면 ***두 테넌트** _의 Microsoft Sentinel에 플레이북을 실행할 수 있는 권한을 부여해야 합니다. 고객 테넌트의 경우 위 시나리오와 같이 _ *플레이북 권한 관리** 패널에서 권한을 부여합니다. 서비스 공급자 테넌트에서 관련 권한을 부여하려면 플레이북이 있는 리소스 그룹에 **Microsoft Sentinel Automation 기여자** 역할을 사용하여 **Azure 보안 Insights** 앱에 대한 액세스 권한을 부여하는 추가 Azure Lighthouse 위임을 추가해야 합니다.

    시나리오는 다음과 같습니다.

    :::image type="content" source="./media/automate-incident-handling-with-automation-rules/automation-rule-multi-tenant.png" alt-text="다중 테넌트 자동화 규칙 아키텍처":::

    이를 설정하는 방법은 [지침](tutorial-respond-threats-playbook.md#permissions-to-run-playbooks)을 참조하세요.

## <a name="creating-and-managing-automation-rules"></a>자동화 규칙 만들기 및 관리

특정 필요 및 사용 사례에 따라 Microsoft Sentinel 환경의 여러 지점에서 자동화 규칙을 만들고 관리할 수 있습니다.

- **자동화 블레이드**

    자동화 규칙은 **자동화 규칙** 탭의 새 **자동화** 블레이드(**플레이북** 블레이드 대체)에서 중앙에서 관리할 수 있습니다. (이제 **플레이북** 탭에서 이 블레이드의 플레이북을 관리할 수도 있습니다.) 여기에서 새 자동화 규칙을 만들고 기존 규칙을 편집할 수 있습니다. 자동화 규칙을 끌어 실행 순서를 변경하고 사용하거나 사용하지 않도록 설정할 수도 있습니다.

    **자동화** 블레이드에서 작업 영역에 정의된 모든 규칙과 함께 상태(사용/사용 안 함) 및 적용되는 분석 규칙을 볼 수 있습니다.

    많은 분석 규칙에 적용되는 자동화 규칙이 필요한 경우 **자동화** 블레이드에서 직접 만듭니다. 상단 메뉴에서 **만들기** 및 **새 규칙 추가** 를 클릭하면 **새 자동화 규칙 만들기** 패널이 열립니다. 여기에서 규칙을 유연하게 구성할 수 있습니다. 규칙을 모든 분석 규칙(향후 분석 규칙 포함)에 적용하고 가장 광범위한 조건 및 작업을 정의할 수 있습니다.

- **분석 규칙 마법사**

    분석 규칙 마법사의 **자동화된 응답** 탭에서는 마법사에서 만들거나 편집 중인 특정 분석 규칙에 적용되는 자동화 규칙을 보고, 관리하고, 만들 수 있습니다.

    **분석** 블레이드의 상단 메뉴에서 **만들기** 및 규칙 유형 중 하나(**예약된 쿼리 규칙** 또는 **Microsoft 인시던트 만들기 규칙**)를 클릭하거나 기존 분석 규칙을 선택하고 **편집** 을 클릭하는 경우 규칙 마법사가 열립니다. **자동화된 응답** 탭을 선택하면 **인시던트 자동화** 라는 섹션이 표시됩니다. 이 섹션에는 현재 이 규칙에 적용되는 자동화 규칙이 표시됩니다. 기존 자동화 규칙을 선택하여 편집하거나 **새로 추가** 를 클릭하여 새 규칙을 만들 수 있습니다.

    여기에서 자동화 규칙을 만드는 경우 **새 자동화 규칙 만들기** 패널에 **분석 규칙** 조건이 사용할 수 없음으로 표시됩니다. 이 규칙이 이미 마법사에서 편집 중인 분석 규칙에만 적용되도록 설정되어 있기 때문입니다. 다른 모든 구성 옵션은 계속 사용할 수 있습니다.

- **인시던트 블레이드**

    단일 되풀이 인시던트에 응답하기 위해 **인시던트** 블레이드에서 자동화 규칙을 만들 수도 있습니다. 이 규칙은 "노이즈" 인시던트를 자동으로 종료하기 위해 [제거 규칙](#incident-suppression)을 만들 때 유용합니다. 큐에서 인시던트를 선택하고 최상위 메뉴에서 **자동화 규칙 만들기** 를 클릭합니다.

    **새 자동화 규칙 만들기** 패널에서 인시던트의 값으로 모든 필드가 채워진 것을 확인할 수 있습니다. 규칙 이름을 인시던트의 이름과 동일하게 지정하고, 인시던트를 생성한 분석 규칙에 적용한 다음 인시던트에서 사용 가능한 모든 엔터티를 규칙 조건으로 사용합니다. 또한 제거(종료) 작업을 기본적으로 제안하고 규칙의 만료 날짜를 제안합니다. 조건 및 작업을 추가하거나 제거하고 만료 날짜를 원하는 대로 변경할 수 있습니다.

## <a name="auditing-automation-rule-activity"></a>감사 자동화 규칙 작업

지정된 인시던트의 변경된 내용 및 특정 자동화 규칙이 수행하거나 수행하지 않았을 수 있는 작업을 확인하는 데 관심이 있을 수 있습니다. **로그** 블레이드의 *SecurityIncident* 테이블에서 인시던트 기록에 대한 전체 기록을 사용할 수 있습니다. 다음 쿼리를 사용하여 모든 자동화 규칙 작업을 볼 수 있습니다.

```kusto
SecurityIncident
| where ModifiedBy contains "Automation"
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 자동화 규칙을 사용하여 Microsoft Sentinel 인시던트 큐를 관리하고 몇 가지 기본적인 인시던트 처리 자동화를 구현하는 방법을 배웠습니다.

- 고급 자동화 옵션에 대한 자세한 내용은 [Microsoft Sentinel에서 플레이북을 사용하여 위협 대응 자동화를](automate-responses-with-playbooks.md)참조하세요.
- 자동화 규칙 및 플레이북 구현에 대한 도움말은 [자습서: 플레이북을 사용하여 Microsoft Sentinel에서 위협 대응 자동화를](tutorial-respond-threats-playbook.md)참조하세요.
