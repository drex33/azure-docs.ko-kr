---
title: 사용자 계정에 대한 Azure Active Directory 보안 작업
description: 기준 설정 지침 및 사용자 계정과 관련된 잠재적 보안 문제를 모니터링하고 경고하는 방법입니다.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a1ee521cdde76284c09f6bf34ad7945e188ee1c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042431"
---
# <a name="azure-active-directory-security-operations-for-user-accounts"></a>사용자 계정에 대한 Azure Active Directory 보안 작업

사용자 ID는 조직과 데이터 보호의 가장 중요한 측면 중 하나입니다. 이 문서에서는 계정 생성, 삭제, 계정 사용 모니터링 지침을 제공합니다. 첫 번째 부분에서는 비정상적 계정 생성과 삭제를 모니터링하는 방법을 설명합니다. 두 번째 부분에서는 비정상적 계정 사용을 모니터링하는 방법을 설명합니다. 

[Azure AD(Azure Active Directory) 보안 작업 개요](security-operations-introduction.md)를 아직 읽지 않은 경우 계속 진행하기 전에 읽어 보는 것이 좋습니다.

이 문서에서는 일반 사용자 계정에 대해 설명합니다. 권한 있는 계정의 경우 보안 작업 – 권한 있는 계정을 참조하세요.

## <a name="define-a-baseline"></a>기준 정의

비정상적 동작을 검색하려면 먼저 정상적이고 예상되는 동작을 정의해야 합니다. 조직의 예상 동작을 정의하면 예기치 않은 동작이 발생하는 경우를 확인하는 데 도움이 됩니다. 이 정의는 모니터링 및 경고 시 가양성의 노이즈 수준을 줄이는 데에도 도움이 됩니다. 

예상 동작을 정의한 후에는 예상 동작의 유효성을 검사하기 위해 기준 모니터링을 수행합니다. 이 정보를 사용하여 로그에서 정의한 허용 오차를 벗어나는 항목을 모니터링할 수 있습니다. 

Azure AD 감사 로그, Azure AD 로그인 로그, 디렉터리 특성을 정상적인 프로세스 외부에서 생성된 계정에 대한 데이터 원본으로 사용합니다. 조직의 정상 동작을 고려하고 정의하는 데 도움이 되는 제안 사항은 다음과 같습니다.

* **사용자 계정 생성** – 다음을 평가합니다.

   * 사용자 계정을 만들고 관리하는 데 사용되는 도구와 프로세스에 대한 전략 및 원칙. 예를 들어 표준 특성, 사용자 계정 특성에 적용되는 형식 등입니다. 

  * 계정 생성을 위해 승인된 원본. 예를 들어 AD(Active Directory, Azure Active Directory, Workday와 같은 HR 시스템 등입니다.

  * 승인된 원본 외부에서 생성된 계정에 대한 경고 전략. 조직에서 협업하는 조직의 제어된 목록이 있나요?

  * 권한 관리나 다른 정상적인 프로세스 외부에서 생성된 계정에 대한 게스트 계정 및 경고 매개 변수 프로비저닝

  * 승인된 사용자 관리자가 아닌 계정에서 생성, 수정 또는 사용하지 않도록 설정한 계정에 대한 전략 및 경고 매개 변수

  * 직원 ID와 같은 표준 특성이 누락되었거나 조직 명명 규칙을 따르지 않는 계정에 대한 모니터링 및 경고 전략

  * 계정 삭제 및 보존에 대한 전략, 원칙, 프로세스

* **온-프레미스 사용자 계정** – Azure AD Connect와 동기화된 계정의 경우 다음을 평가합니다.

  * 동기화 범위에 있는 포리스트, 도메인, OU(조직 구성 단위). 이 설정을 변경할 수 있는 승인된 관리자는 누구이고 범위 확인 빈도는 어떻게 되나요?

  * 동기화되는 계정 유형. 예를 들어 사용자 계정, 서비스 계정 등입니다. 

  * 권한 있는 온-프레미스 계정을 만드는 프로세스와 이 계정 유형의 동기화를 제어하는 방법

  * 온-프레미스 사용자 계정을 만드는 프로세스와 이 계정 유형의 동기화를 관리하는 방법

온-프레미스 계정을 보호하고 모니터링하는 방법에 대한 자세한 내용은 [온-프레미스 공격으로부터 Microsoft 365 보호](protect-m365-from-on-premises-attacks.md)를 참조하세요.

* **클라우드 사용자 계정** – 다음을 평가합니다.

  * Azure AD에서 직접 클라우드 계정을 프로비저닝하고 관리하는 프로세스

  * Azure AD 클라우드 계정으로 프로비저닝된 사용자 유형을 확인하는 프로세스. 예를 들어 권한 있는 계정만 허용하나요, 아니면 사용자 계정도 허용하나요?

  * 신뢰할 수 있는 개인 목록을 만들고 유지 관리하는 프로세스 또는 클라우드 사용자 계정을 만들고 관리하는 데 필요한 프로세스

  * 승인되지 않은 클라우드 기반 계정에 대한 경고 전략을 만들고 유지 관리하는 프로세스 

## <a name="where-to-look"></a>살펴볼 위치

조사 및 모니터링에 사용하는 로그 파일은 다음과 같습니다. 

* [Azure AD 감사 로그](../reports-monitoring/concept-audit-logs.md)

* [로그인 로그](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365 감사 로그](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault 로그](../../key-vault/general/logging.md?tabs=Vault)

* [위험 사용자 로그](../identity-protection/howto-identity-protection-investigate-risk.md)

* [UserRiskEvents 로그](../identity-protection/howto-identity-protection-investigate-risk.md)

Azure Portal에서 Azure AD 감사 로그를 보고, CSV(쉼표로 구분된 값) 또는 JSON(JavaScript Object Notation) 파일로 다운로드할 수 있습니다. Azure Portal에는 모니터링 및 경고를 더 효율적으로 자동화하도록 허용하는 다른 도구와 Azure AD 로그를 통합하는 몇 가지 방법이 있습니다.

* **[Azure Sentinel](../../sentinel/overview.md)** - SIEM(보안 정보 및 이벤트 관리) 기능을 제공하여 엔터프라이즈 수준에서 인텔리전트 보안 분석을 가능하게 합니다. 

* **[Azure Monitor](../../azure-monitor/overview.md)** - 다양한 조건에 대한 자동화된 모니터링 및 경고를 가능하게 합니다. 통합 문서를 만들거나 사용하여 다양한 원본의 데이터를 결합할 수 있습니다.

* **SIEM과 통합된 [Azure Event Hubs](../../event-hubs/event-hubs-about.md)** - Azure Event Hubs 통합을 통해 Splunk, ArcSight, QRadar, Sumo Logic 등의 [다른 SIEM에 Azure AD 로그를 통합할 수 있습니다](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).

* **[MCAS(Microsoft Cloud App Security)](/cloud-app-security/what-is-cloud-app-security)** - 앱을 검색 및 관리하고, 앱과 리소스를 제어하고, 클라우드 앱의 규정 준수를 확인할 수 있습니다. 

모니터링하고 경고할 항목의 대부분은 조건부 액세스 정책의 영향입니다. [조건부 액세스 인사이트 및 보고 통합 문서](../conditional-access/howto-conditional-access-insights-reporting.md)를 사용하여 로그인에 대한 하나 이상의 조건부 액세스 정책과 디바이스 상태를 포함한 정책 결과를 조사할 수 있습니다. 이 통합 문서를 사용하면 영향 요약을 보고 특정 기간 동안의 영향을 식별할 수 있습니다. 통합 문서를 사용하여 특정 사용자의 로그인을 조사할 수도 있습니다. 

 이 문서의 나머지 부분은 모니터링하고 경고하는 데 권장되는 대상에 대해 설명하며, 위협 유형별로 구성되어 있습니다. 미리 빌드된 특정 솔루션이 있는 경우 해당 솔루션에 연결하거나 표 뒤에서 샘플을 제공합니다. 제공하지 않으면 이전 도구를 사용하여 경고를 작성할 수 있습니다. 

## <a name="account-creation"></a>계정 만들기

비정상적 계정 생성은 보안 문제를 나타낼 수 있습니다. 수명이 짧은 계정, 명명 표준을 따르지 않는 계정, 정상적인 프로세스 외부에서 생성된 계정을 조사해야 합니다.

### <a name="short-lived-accounts"></a>수명이 짧은 계정

정상적인 ID 관리 프로세스 외부의 계정 생성과 삭제는 Azure AD에서 모니터링해야 합니다. 수명이 짧은 계정은 짧은 기간에 생성 및 삭제된 계정입니다. 이 유형의 계정 생성 및 빠른 삭제는 잘못된 작업자가 검색되지 않기 위해 계정을 만들고 사용한 후 삭제하는 경우일 수 있습니다.

수명이 짧은 계정 패턴은 승인되지 않은 사람이나 프로세스에서 설정된 프로세스와 정책에 해당하지 않는 계정을 만들고 삭제할 수 있음을 나타낼 수 있습니다. 이 유형의 동작은 디렉터리에서 보이는 표식을 제거합니다. 

계정 생성 및 삭제에 대한 데이터 내역이 빠르게 검색되지 않는 경우 인시던트를 조사하는 데 필요한 정보가 더 이상 존재하지 않을 수 있습니다. 예를 들어 계정이 삭제된 후 휴지통에서 제거되었을 수 있습니다. 감사 로그는 30일 동안 보존됩니다. 그러나 장기 보존을 위해 로그를 Azure Monitor 또는 SIEM(보안 정보 및 이벤트 관리) 솔루션으로 내보낼 수 있습니다. 

| 모니터링 대상                                                  | 위험 수준 | Where               | 필터/하위 필터                                                                                                                                         | 참고                                                                                                                               |
|------------------------------------------------------------------|------------|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| 종료 시간 프레임 내 계정 생성 및 삭제 이벤트  | 높음       | Azure AD 감사 로그 | 작업: 사용자 추가<br>상태 = 성공<br>및<br>작업: 사용자 삭제<br>상태 = 성공<br>                                                          | UPN(사용자 계정 이름) 이벤트를 검색합니다. 생성된 후 24시간 이내에 삭제된 계정을 찾습니다.                          |
| 승인되지 않은 사용자/프로세스에서 만들고 삭제한 계정 | 중간     | Azure AD 감사 로그 | 시작한 사람(작업자) = 사용자 계정 이름<br>및<br>작업: 사용자 추가<br>상태 = 성공<br>및/또는<br>작업: 사용자 삭제<br>상태 = 성공      | 작업자가 승인되지 않은 사용자인 경우 경고를 보내도록 구성합니다.                                                                    |
| 승인되지 않은 원본의 계정                              | 중간     | Azure AD 감사 로그 | 작업: 사용자 추가<br>상태 = 성공<br>대상 = 사용자 계정 이름                                                                                 | 항목이 승인된 도메인의 항목이 아니거나 알려진 차단된 도메인인 경우 경고를 보내도록 구성합니다.                               |
| 권한 있는 역할에 할당된 계정                          | 높음       | Azure AD 감사 로그 | 작업: 사용자 추가<br>상태 = 성공<br>및<br>작업: 사용자 삭제<br>상태 = 성공<br>및<br>작업: 역할에 구성원 추가<br>상태 = 성공 | 계정이 Azure AD 역할, Azure 역할 또는 권한 있는 그룹 멤버 자격에 할당된 경우 경고하고 조사에 우선 순위를 지정합니다. |

권한 있는 계정과 권한 없는 계정을 둘 다 모니터링하고 경고해야 합니다. 그러나 권한 있는 계정에는 관리 권한이 있으므로 모니터, 경고, 응답 프로세스에서 우선 순위가 더 높아야 합니다. 

### <a name="accounts-not-following-naming-policies"></a>명명 정책을 따르지 않는 계정

명명 정책을 따르지 않는 사용자 계정은 조직 정책 외부에서 생성되었을 수 있습니다. 

사용자 개체에 대한 명명 정책을 사용하는 것이 좋습니다. 명명 정책을 사용하면 관리가 더 용이하고 일관성이 유지됩니다. 정책은 사용자가 승인된 프로세스 외부에서 생성된 경우를 검색하는 데에도 도움이 됩니다. 명명 표준을 모르는 잘못된 작업자기 조직 프로세스 외부에서 프로비저닝된 계정을 더 쉽게 검색할 수 있습니다.

사용자 및 권한 있는 계정을 만드는 데 사용되는 특정 형식과 특성이 조직에 있는 경우가 많습니다. 예를 들면 다음과 같습니다.

* 관리자 계정 UPN = ADM_firstname.lastname@tenant.onmicrosoft.com

* 사용자 계정 UPN = Firstname.Lastname@contoso.com

사용자 계정에 실제 사용자를 식별하는 특성이 있는 경우도 많습니다. 예를 들어 EMPID = XXXNNN입니다. 계정이 조직의 명명 규칙을 따르지 않는 로그 항목에 대한 기준을 정의할 때 고려해야 할 사항뿐만 아니라 조직의 정상 동작을 고려하고 정의하는 데 도움이 되는 제안 사항은 다음과 같습니다.

* 명명 규칙을 따르지 않는 계정. 예를 들어 `nnnnnnn@contoso.com` 대 `firstname.lastname@contoso.com`입니다. 

* 표준 특성이 채워지지 않았거나 올바른 형식이 아닌 계정. 예를 들어 유효한 직원 ID가 없는 계정입니다.

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - | - | - | - | - |
| 필요한 특성이 정의되어 있지 않은 사용자 계정| 낮음| Azure AD 감사 로그| 작업: 사용자 추가<br>상태 = 성공| 표준 특성이 null이거나 잘못된 형식인 계정을 찾습니다. 예: EmployeeID |
| 잘못된 명명 형식을 사용하여 생성된 사용자 계정| 낮음| Azure AD 감사 로그| 작업: 사용자 추가<br>상태 = 성공| 명명 정책을 따르지 않는 UPN이 있는 계정을 찾습니다. |
| 명명 정책을 따르지 않는 권한 있는 계정| 높음| Azure 구독| [Azure Portal을 사용하여 Azure 역할 할당 나열 - Azure RBAC](../../role-based-access-control/role-assignments-list-portal.md)| 구독에 대한 역할 할당을 나열하고, 로그인 이름이 조직 형식과 일치하지 않는 경우 경고합니다. 예를 들어 접두사로 ADM_이 지정된 항목입니다. |
| 명명 정책을 따르지 않는 권한 있는 계정| 높음| Azure AD 디렉터리| [Azure AD 역할 할당 나열](../roles/view-assignments.md)| Azure AD 역할에 대한 역할 할당을 나열하고, UPN이 조직 형식과 일치하지 않는 경우 경고합니다. 예를 들어 접두사로 ADM_이 지정된 항목입니다. |



구문 분석에 대한 자세한 내용은 다음을 참조하세요.

* Azure AD 감사 로그 - [Azure Monitor 로그에서 텍스트 데이터 구문 분석](../../azure-monitor/logs/parse-text.md)

* Azure 구독 - [Azure PowerShell을 사용하여 Azure 역할 할당 나열](../../role-based-access-control/role-assignments-list-powershell.md)

* Azure Active Directory - [Azure AD 역할 할당 나열](../roles/view-assignments.md) 

### <a name="accounts-created-outside-normal-processes"></a>정상적인 프로세스 외부에서 생성된 계정

ID 수명 주기를 안전하게 제어할 수 있도록 사용자 및 권한 있는 계정을 만드는 표준 프로세스를 구축하는 것이 중요합니다. 설정된 프로세스 외부에서 사용자를 프로비저닝 및 프로비전 해제하면 보안 위험이 발생할 수 있습니다. 설정된 프로세스 외부에서 작업하는 경우 ID 관리 문제도 발생할 수 있습니다. 잠재적인 위험은 다음과 같습니다.

* 사용자 및 권한 있는 계정이 조직 정책을 준수하도록 관리되지 않았을 수 있습니다. 이 경우 올바르게 관리되지 않는 계정에서 공격 노출 영역이 더 넓어질 수 있습니다.

* 잘못된 작업자가 악의적인 목적으로 계정을 만드는 경우를 검색하기가 더 어려워집니다. 설정된 프로시저 외부에서 생성된 유효한 계정이 있으므로 악의적인 목적으로 계정이 생성되었거나 사용 권한이 수정된 경우를 검색하기가 더 어려워집니다. 

조직 정책에 따라 필요한 경우에만 사용자 및 권한 있는 계정을 만드는 것이 좋습니다. 예를 들어 올바른 명명 표준, 조직 정보, 적절한 ID 거버넌스 범위에서 계정을 만들어야 합니다. 조직은 ID를 생성, 관리, 삭제할 수 있는 권한이 있는 사용자를 엄격하게 제어해야 합니다. 계정을 만드는 역할을 철저하게 관리해야 하고, 설정된 워크플로에 따라 승인하고 사용 권한을 얻은 후에만 권한이 제공됩니다.

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - | - | - | - | - |
| 승인되지 않은 사용자/프로세스에서 만들거나 삭제한 사용자 계정| 중간| Azure AD 감사 로그| 작업: 사용자 추가<br>상태 = 성공<br>및/또는<br>작업: 사용자 삭제<br>상태 = 성공<br>및<br>시작한 사람(작업자) = 사용자 계정 이름| 승인되지 않은 사용자/프로세스에서 만든 계정에 대한 경고입니다. 높은 권한으로 생성된 계정에 우선 순위를 지정합니다. |
| 승인되지 않은 원본에서 생성 또는 삭제된 사용자 계정| 중간| Azure AD 감사 로그| 작업: 사용자 추가<br>상태 = 성공<br>또는<br>작업: 사용자 삭제<br>상태 = 성공<br>및<br>대상 = 사용자 계정 이름| 도메인이 승인되지 않았거나 알려진 차단된 도메인인 경우 경고합니다. |


## <a name="unusual-sign-ins"></a>비정상적 로그인

사용자 인증 오류 확인은 정상적인 동작입니다. 그러나 오류 패턴이나 오류 블록 확인은 사용자 ID에서 문제가 발생했음을 나타낼 수 있습니다. 예를 들어 암호 스프레이 또는 무차별 암호 대입 공격(brute force attack)이나 사용자 계정이 손상된 경우입니다. 패턴이 나타날 경우 모니터링하고 경고하는 것이 중요합니다. 이렇게 하면 사용자와 조직의 데이터를 보호할 수 있습니다. 

성공은 모두 정상임을 나타냅니다. 그러나 잘못된 작업자가 서비스에 성공적으로 액세스한 경우를 의미할 수 있습니다. 성공적인 로그인을 모니터링하면 액세스 권한이 제공되지만 액세스 권한이 있어야 하는 사용자 계정이 아닌 사용자 계정을 검색할 수 있습니다. 사용자 인증 성공은 Azure AD 로그인 로그의 일반 항목입니다. 패턴의 발생 시기를 감지하도록 모니터링하고 경고하는 것이 좋습니다. 이렇게 하면 사용자 계정과 조직의 데이터를 보호할 수 있습니다. 


로그 모니터링 및 경고 전략을 설계하고 운영하는 경우 Azure Portal을 통해 제공되는 도구를 고려합니다. ID 보호를 사용하면 ID 기반 위험을 검색, 보호, 수정하는 기능을 자동화할 수 있습니다. ID 보호는 인텔리전스 공급 기계 학습과 추론 시스템을 사용하여 위험을 탐지하고 사용자와 로그인에 대한 위험 점수를 할당합니다. 고객은 액세스를 허용 또는 거부하거나 사용자가 위험을 안전하게 자체 수정할 수 있는 경우에 대한 위험 수준의 정책을 구성할 수 있습니다. 현재 위험 수준을 알리는 ID 보호 위험 검색은 다음과 같습니다.

| 모니터링 대상 | 위험 수준 | Where | 필터/하위 필터 | 참고 |
| - | - | - | - | - |
| 유출된 자격 증명 사용자 위험 검색| 높음| Azure AD 위험 검색 로그| UX: 유출된 자격 증명 <br><br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |
| Azure AD 위협 인텔리전스 사용자 위험 검색| 높음| Azure AD 위험 검색 로그| UX: Azure AD 위협 인텔리전스 <br><br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |
| 익명 IP 주소 로그인 위험 검색| 상황에 따라 다름| Azure AD 위험 검색 로그| UX: 익명 IP 주소 <br><br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |
| 비정상적 이동 로그인 위험 검색| 상황에 따라 다름| Azure AD 위험 검색 로그| UX: 비정상적 이동 <br><br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |
| 비정상적 토큰| 상황에 따라 다름| Azure AD 위험 검색 로그| UX: 비정상적 토큰 <br><br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |
| 맬웨어 연결 IP 주소 로그인 위험 검색| 상황에 따라 다름| Azure AD 위험 검색 로그| UX: 맬웨어 연결 IP 주소 <br><br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |
| 의심스러운 브라우저 로그인 위험 검색| 상황에 따라 다름| Azure AD 위험 검색 로그| UX: 의심스러운 브라우저 <br><br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |
| 익숙하지 않은 로그인 속성 로그인 위험 검색| 상황에 따라 다름| Azure AD 위험 검색 로그| UX: 익숙하지 않은 로그인 속성 <br><br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |
| 악성 IP 주소 로그인 위험 검색| 상황에 따라 다름| Azure AD 위험 검색 로그| UX: 악성 IP 주소<br><br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |
| 의심스러운 받은 편지함 조작 규칙 로그인 위험 검색| 상황에 따라 다름| Azure AD 위험 검색 로그| UX: 의심스러운 받은 편지함 조작 규칙<br><br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |
| 암호 스프레이 로그인 위험 검색| 높음| Azure AD 위험 검색 로그| UX: 암호 스프레이<br><br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |
| 불가능한 이동 로그인 위험 검색| 상황에 따라 다름| Azure AD 위험 검색 로그| UX: 불가능한 이동<br><br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |
| 새 국가 로그인 위험 검색| 상황에 따라 다름| Azure AD 위험 검색 로그| UX: 새 국가<br><br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |
| 익명 IP 주소에서의 활동 로그인 위험 검색| 상황에 따라 다름| Azure AD 위험 검색 로그| UX: 익명 IP 주소에서의 활동<br><br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |
| 의심스러운 받은 편지함 전달 로그인 위험 검색| 상황에 따라 다름| Azure AD 위험 검색 로그| UX: 의심스러운 받은 편지함 전달<br><br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |
| Azure AD 위협 인텔리전스 로그인 위험 검색| 높음| Azure AD 위험 검색 로그| UX: Azure AD 위협 인텔리전스<br>API: [riskDetection 리소스 종류 - Microsoft Graph 베타](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true) 참조| [위험이란? Azure AD ID 보호](../identity-protection/concept-identity-protection-risks.md) 참조 |

자세한 내용은 [ID 보호란?](../identity-protection/overview-identity-protection.md)을 참조하세요. 


### <a name="what-to-look-for"></a>찾을 내용

Azure AD 로그인 로그 내 데이터에 대한 모니터링을 구성하여 경고가 발생하고 조직의 보안 정책을 준수하는지 확인합니다. 몇 가지 예는 다음과 같습니다.

* **실패한 인증**: 누구나 가끔 암호를 잘못 입력하는 경우가 있습니다. 그러나 인증 실패 횟수가 많으면 잘못된 작업자가 액세스하려는 경우를 나타낼 수 있습니다. 공격의 세기는 다르지만 시간당 몇 번에서 훨씬 높은 비율까지 다양할 수 있습니다. 예를 들어 암호 스프레이는 일반적으로 많은 계정에 대해 더 쉬운 암호를 사용하지만 무차별 암호 대입은 대상 계정에 대해 많은 암호를 시도합니다. 

* **중단된 인증**: Azure AD의 인터럽트는 CA 정책에서 제어를 적용하는 경우와 같이 인증을 충족하는 추가 프로세스 삽입을 나타냅니다. 정상적인 이벤트이며 애플리케이션이 올바르게 구성되지 않은 경우에 발생할 수 있습니다. 그러나 사용자 계정에 대해 많은 인터럽트가 표시되는 경우 해당 계정에서 문제가 발생하고 있음을 나타낼 수 있습니다. 

  * 예를 들어 로그인 로그에서 사용자를 필터링하고 로그인 상태 = 중단됨 및 조건부 액세스 = 실패를 대량 확인하는 경우입니다. 자세히 살펴보면 인증 세부 정보에서 암호는 맞지만 강력한 인증이 필요함을 알 수 있습니다. 사용자가 MFA(다단계 인증)를 완료하지 않았음을 의미하며, 사용자의 암호가 손상되었고 잘못된 작업자는 MFA를 처리할 수 없음을 나타낼 수 있습니다.

* **스마트 잠금**: Azure AD는 인증 프로세스에 익숙한 위치와 익숙하지 않은 위치의 개념을 도입하는 스마트 잠금 서비스를 제공합니다. 익숙한 위치를 방문하는 사용자 계정은 성공적으로 인증될 수 있지만 동일한 위치에 익숙하지 않은 잘못된 작업자는 여러 번 시도 후에 차단됩니다. 잠긴 계정을 찾아서 자세히 조사합니다. 

* **IP 변경**: 여러 IP 주소에서 시작된 사용자가 표시되는 것이 정상입니다. 그러나 제로 트러스트는 신뢰하지 않고 항상 확인을 나타냅니다. IP 주소와 실패한 로그인이 많이 표시되는 경우 침입을 나타낼 수 있습니다. 여러 IP 주소에서 발생하는 여러 실패한 인증의 패턴을 찾습니다. VPN(가상 사설망) 연결로 인해 가양성이 발생할 수 있습니다. 챌린지와 관계없이 IP 주소 변경을 모니터링하고, 가능한 경우 Azure AD ID 보호를 사용하여 위험을 자동으로 탐지하고 완화하는 것이 좋습니다.

* **위치**: 일반적으로 사용자 계정이 동일한 지리적 위치에 있어야 합니다. 또한 직원 또는 비즈니스 관계가 있는 위치에서 로그인해야 합니다. 사용자 계정이 다른 국제 위치에서 들어오는 데 걸리는 시간이 해당 위치로 이동하는 데 걸리는 시간보다 짧은 경우 사용자 계정이 남용되고 있음을 나타낼 수 있습니다. VPN으로 인해 가양성이 발생할 수 있습니다. 지리적으로 멀리 떨어진 위치에서 로그인하는 사용자 계정을 모니터링하고, 가능한 경우 Azure AD ID 보호를 사용하여 위험을 자동으로 탐지하고 완화하는 것이 좋습니다.

이 위험 영역의 경우 표준 사용자 계정과 권한 있는 계정을 모두 모니터링하되, 권한 있는 계정 조사에 우선 순위를 지정하는 것이 좋습니다. 권한 있는 계정은 Azure AD 테넌트에서 가장 중요한 계정입니다. 권한 있는 계정에 대한 특정 지침은 보안 작업 – 권한 있는 계정을 참조하세요. 

### <a name="how-to-detect"></a>검색 방법

Azure ID 보호 및 Azure AD 로그인 로그를 사용하여 비정상적 로그인 특성으로 표시된 위협을 검색할 수 있습니다. ID 보호에 대한 자세한 내용은 [ID 보호란?](../identity-protection/overview-identity-protection.md)을 참조하세요. 모니터링 및 경고 목적으로 데이터를 Azure Monitor 또는 SIEM에 복제할 수도 있습니다. 환경의 정상 동작을 정의하고 기준을 설정하려면 다음을 확인합니다.

* 사용자 기반에서 정상으로 간주하는 매개 변수

* 사용자가 서비스 데스크를 호출하거나 셀프 서비스 암호 재설정을 수행하기 전에 1시간 동안 수행된 평균 암호 시도 횟수

* 경고하기 전에 허용하려는 실패한 시도 횟수와 사용자 계정 및 권한 있는 계정에서 다른지 여부

* 경고하기 전에 허용하려는 MFA 시도 횟수와 사용자 계정 및 권한 있는 계정에서 다른지 여부

* 레거시 인증이 사용하도록 설정되었는지 여부와 사용 중단 페이지 로드맵 

* 조직의 알려진 송신 IP 주소

* 사용자 활동 국가

* 네트워크 위치나 국가 내에서 고정된 상태로 유지되는 사용자 그룹이 있는지 여부

* 조직과 관련된 비정상적 로그인의 다른 지표 식별. 예를 들어 조직이 운영되지 않는 요일, 일 또는 시간입니다.

사용자 환경의 계정 유형에 대한 정상 범위를 지정한 후에는 모니터링 및 경고할 시나리오를 결정하고 경고를 미세 조정하는 데 도움이 되는 다음 사항을 고려합니다.

* ID 보호가 구성되었는지 여부를 모니터링하고 경고해야 하나요?

* 모니터링 및 경고에 사용할 수 있는 권한 있는 계정에 더 엄격한 조건이 적용되나요? 예를 들어 권한 있는 계정은 신뢰할 수 있는 IP 주소에서만 사용하도록 요구합니다.

* 설정한 기준이 너무 공격적인가요? 경고가 너무 많으면 무시되거나 누락될 수 있습니다.

보안 기준 정책을 지원하는 보호가 설정되도록 ID 보호를 구성합니다. 예를 들어 위험 = 높음이면 사용자를 차단합니다. 이 위험 수준은 사용자 계정이 손상되었음을 높은 신뢰도로 나타냅니다. 로그인 위험 정책과 사용자 위험 정책을 설정하는 방법에 대한 자세한 내용은 [ID 보호 정책](../identity-protection/concept-identity-protection-policies.md)을 참조하세요. 조건부 액세스를 설정하는 방법에 대한 자세한 내용은 [조건부 액세스: 로그인 위험 기반 조건부 액세스](../conditional-access/howto-conditional-access-policy-risk.md)를 참조하세요.

항목의 영향과 심각도에 따라 중요도 순서로 나열되는 항목은 다음과 같습니다.

### <a name="monitoring-for-failed-unusual-sign-ins"></a>실패한 비정상적 로그인 모니터링

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - |- |- |- |- |
| 실패한 로그인 시도| 중간 - 격리된 인시던트인 경우<br>높음 - 많은 계정에 동일한 패턴이나 VIP가 발생하는 경우| Azure AD 로그인 로그| 상태 = 실패<br>및<br>로그인 오류 코드 50126 - <br>잘못된 사용자 이름 또는 암호로 인해 자격 증명 유효성 검사 오류가 발생했습니다.| 기준 임계값을 정의한 다음, 모니터링하여 조직 동작에 맞게 조정하고 false 경고가 생성되지 않도록 제한합니다. |
| 스마트 잠금 이벤트| 중간 - 격리된 인시던트인 경우<br>높음 - 많은 계정에 동일한 패턴이나 VIP가 발생하는 경우| Azure AD 로그인 로그| 상태 = 실패<br>및<br>로그인 오류 코드 = 50053 – IdsLocked| 기준 임계값을 정의한 다음, 모니터링하여 조직 동작에 맞게 조정하고 false 경고가 생성되지 않도록 제한합니다. |
| 인터럽트| 중간 - 격리된 인시던트인 경우<br>높음 - 많은 계정에 동일한 패턴이나 VIP가 발생하는 경우| Azure AD 로그인 로그| 500121, 강력한 인증 요청 중에 인증에 실패했습니다. <br>또는<br>50097, 디바이스 인증이 필요합니다. 또는 50074, 강력한 인증이 필요합니다. <br>또는<br>50155, DeviceAuthenticationFailed<br>또는<br>50158, ExternalSecurityChallenge - 외부 보안 챌린지가 충족되지 않았습니다.<br>또는<br>53003 및 실패 이유 = CA에서 차단됨| 인터럽트 모니터링 및 경고<br>기준 임계값을 정의한 다음, 모니터링하여 조직 동작에 맞게 조정하고 false 경고가 생성되지 않도록 제한합니다. |


항목의 영향과 심각도에 따라 중요도 순서로 나열되는 항목은 다음과 같습니다.

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - |- |- |- |- |
| MFA(다단계 인증) 사기 경고| 높음| Azure AD 로그인 로그| 상태 = 실패<br>및<br>세부 정보 = MFA 거부됨<br>| 모든 항목을 모니터링하고 경고합니다. |
| 운영하지 않는 국가에서 실패한 인증| 중간| Azure AD 로그인 로그| 위치 = \<unapproved location\>| 항목을 모니터링하고 경고합니다. |
| 레거시 프로토콜이나 사용되지 않는 프로토콜에 대한 인증 실패| 중간| Azure AD 로그인 로그| 상태 = 실패<br>및<br>클라이언트 앱 = 다른 클라이언트, POP, IMAP, MAPI, SMTP, ActiveSync| 항목을 모니터링하고 경고합니다. |
| CA에서 차단된 실패| 중간| Azure AD 로그인 로그| 오류 코드 = 53003 <br>및<br>실패 이유 = CA에서 차단됨| 항목을 모니터링하고 경고합니다. |
| 모든 형식의 증가한 인증 실패| 중간| Azure AD 로그인 로그| 보드 전체에서 실패 증가를 캡처합니다. 즉, 오늘의 총 실패 횟수는 지난주 동일한 날 대비 10%가 더 많습니다.| 설정된 임계값이 없는 경우 실패 횟수가 10% 이상 증가하는지 모니터링하고 경고합니다. |
| 국가에서 표준 비즈니스 작업을 수행하지 않는 시간과 요일에 인증 발생| 낮음| Azure AD 로그인 로그| 정상적 운영 요일\시간 외에 발생하는 대화형 인증을 캡처합니다. <br>상태 = 성공<br>및<br>위치 = \<location\><br>및<br>Day\Time = \<not normal working hours\>| 항목을 모니터링하고 경고합니다. |
| 계정 사용 안 함/로그인 차단됨| 낮음| Azure AD 로그인 로그| 상태 = 실패<br>및<br>오류 코드 = 50057, 사용자 계정이 사용하지 않도록 설정되었습니다.| 이 오류는 누군가가 조직을 떠난 후 계정에 액세스하려고 시도함을 나타낼 수 있습니다. 계정이 차단된 경우에도 이 작업을 기록하고 경고하는 것이 중요합니다. |


### <a name="monitoring-for-successful-unusual-sign-ins"></a>성공한 비정상적 로그인 모니터링

 | 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - |- |- |- |- |
| 필요한 컨트롤 외부의 권한 있는 계정 인증| 높음| Azure AD 로그인 로그| 상태 = 성공<br>및<br>UserPricipalName = \<Admin account\><br>및<br>위치 = \<unapproved location\><br>및<br>IP 주소 = \<unapproved IP\><br>디바이스 정보 = \<unapproved Browser, Operating System\><br>| 필요한 컨트롤 외부의 권한 있는 계정에 대한 인증 성공을 모니터링하고 경고합니다. 세 가지 공용 컨트롤이 나열됩니다. |
| 단일 단계 인증만 필요한 경우| 낮음| Azure AD 로그인 로그| 상태 = 성공<br>인증 요구 사항 = 단일 단계 인증| 정기적으로 모니터링하여 예상 동작인지 확인합니다. |
| MFA에 등록되지 않은 권한 있는 계정 검색| 높음| Azure Graph API| 관리자 계정에 대한 IsMFARegistered eq false를 쿼리합니다. <br>[credentialUserRegistrationDetails 나열 - Microsoft Graph 베타 | Microsoft Docs](/graph/api/reportroot-list-credentialuserregistrationdetails?view=graph-rest-beta&preserve-view=true&tabs=http)| 감사하고 조사하여 의도적인지 또는 실수인지 확인합니다. |
| 조직에서 운영하지 않는 국가에서의 인증 성공| 중간| Azure AD 로그인 로그| 상태 = 성공<br>위치 = \<unapproved country\>| 제공한 도시 이름과 같지 않은 항목을 모니터링하고 경고합니다. |
| 성공적인 인증, CA에서 세션 차단됨| 중간| Azure AD 로그인 로그| 상태 = 성공<br>및<br>오류 코드 = 53003 – 실패 이유, CA에서 차단됨| 인증에 성공했지만 세션이 CA에서 차단된 경우를 모니터링하고 조사합니다. |
| 레거시 인증을 사용하지 않도록 설정한 후 인증 성공| 중간| Azure AD 로그인 로그| 상태 = 성공 <br>및<br>클라이언트 앱 = 다른 클라이언트, POP, IMAP, MAPI, SMTP, ActiveSync| 조직에서 레거시 인증을 사용하지 않도록 설정한 경우 성공적인 레거시 인증이 수행된 경우를 모니터링하고 경고합니다. |


정기적으로 MBI(중간 비즈니스 영향) 및 HBI(높은 비즈니스 영향) 애플리케이션에 대한 인증을 검토하는 것이 좋습니다. 단, 단일 단계 인증만 필요합니다. 각각에 대해 단일 단계 인증이 필요한지 여부를 확인하려고 합니다. 또한 성공적인 인증 증가 또는 위치를 기준으로 예기치 않은 시간을 검토합니다. 

| 모니터링 대상| 위험 수준| Where| 필터/하위 필터| 참고 |
| - | - |- |- |- |
| 단일 단계 인증을 사용하는 MBI 및 HBI 애플리케이션 인증입니다.| 낮음| Azure AD 로그인 로그| 상태 = 성공<br>및<br>애플리케이션 ID = \<HBI app\> <br>및<br>인증 요구 사항 = 단일 단계 인증| 이 구성이 의도적인지 검토하고 유효성을 검사합니다. |
| 국가에서 표준 비즈니스 작업을 수행하지 않는 요일, 일, 시간에 인증 발생| 낮음| Azure AD 로그인 로그| 정상적 운영 요일\시간 외에 발생하는 대화형 인증을 캡처합니다. <br>상태 = 성공<br>위치 = \<location\><br>Date\Time = \<not normal working hours\>| 국가에서 표준 비즈니스 작업을 수행하지 않는 요일, 일, 시간에 인증을 모니터링하고 경고합니다. |
| 성공적인 로그인의 측정 가능한 증가| 낮음| Azure AD 로그인 로그| 보드 전체의 성공적인 인증 증가를 캡처합니다. 즉, 오늘의 총 성공 횟수는 지난주 동일한 날 대비 10%가 더 많습니다.| 설정된 임계값이 없는 경우 성공적인 인증 횟수가 10% 이상 증가하는지 모니터링하고 경고합니다. |

## <a name="next-steps"></a>다음 단계
다음 보안 작업 가이드 문서를 참조하세요.

[Azure AD 보안 작업 개요](security-operations-introduction.md)

[사용자 계정에 대한 보안 작업](security-operations-user-accounts.md)

[권한 있는 계정에 대한 보안 작업](security-operations-privileged-accounts.md)

[Privileged Identity Management에 대한 보안 작업](security-operations-privileged-identity-management.md)

[애플리케이션에 대한 보안 작업](security-operations-applications.md)

[디바이스에 대한 보안 작업](security-operations-devices.md)
 
[인프라에 대한 보안 작업](security-operations-infrastructure.md)
