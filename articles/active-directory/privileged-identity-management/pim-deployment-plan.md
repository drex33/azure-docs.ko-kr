---
title: Privileged Identity Management 배포를 계획하나요? - Azure AD | Microsoft Docs
description: Azure AD 조직에 PIM(Privileged Identity Management)을 배포하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: BarbaraSelden
manager: martinco
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: baselden
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99bdfeff59f26f59c9d64bcca9226d9b1f70ec1d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529303"
---
# <a name="plan-a-privileged-identity-management-deployment"></a>Privileged Identity Management 배포 계획

**PIM(Privileged Identity Management)** 은 중요한 리소스에 대한 과도한, 불필요한 또는 잘못 사용된 액세스 권한의 위험을 완화할 수 있도록 시간 기반 및 승인 기반 역할 활성화를 제공합니다. 여기에는 Azure AD(Azure Active Directory), Azure, Microsoft 365 또는 Microsoft Intune과 같은 기타 Microsoft Online Services의 리소스가 포함됩니다. 서비스(SaaS) 애플리케이션과 함께 PIM을 사용할 수도 있습니다.

PIM을 사용하면 특정 범위에서 특정 작업 세트를 허용할 수 있습니다. 주요 기능은 다음과 같습니다.

* 리소스에 대한 **JIT(Just-In-Time)** 권한 있는 액세스 제공

* 권한 있는 액세스 그룹의 **멤버십 또는 소유권 자격** 할당

* 시작 및 종료 날짜를 사용하여 리소스에 **시간 범위** 액세스 할당

* 권한 있는 역할을 활성화하기 위해 **승인** 필요

* 역할 활성화를 위해 **다단계 인증** 적용

* **근거** 를 사용하여 사용자가 활성화하는 이유 이해

* 권한 있는 역할이 활성화되면 **알림** 가져오기

* **액세스 검토** 를 수행하여 사용자에게 여전히 역할이 필요한지 확인

* 내부 또는 외부 감사를 위해 **감사 기록** 다운로드

이 배포 계획을 최대한 활용하려면 [Privileged Identity Management란 무엇인가](pim-configure.md)에 대한 전체 개요를 파악하는 것이 중요합니다.

## <a name="understand-pim"></a>PIM 이해

이 섹션의 PIM 개념은 조직의 권한 있는 ID 요구 사항을 이해하는 데 도움이 됩니다.

### <a name="what-can-you-manage-in-pim"></a>PIM에서 관리할 수 있는 것

현재 다음과 함께 PIM을 사용할 수 있습니다.

* **Azure AD 역할** – 디렉터리 역할이라고도 하는 Azure AD 역할에는 Azure AD 및 기타 Microsoft 365 온라인 서비스를 관리하기 위한 기본 제공 역할과 사용자 지정 역할이 포함됩니다.

* **Azure 역할** – 관리 그룹, 구독, 리소스 그룹, 리소스에 대한 액세스 권한을 부여하는 Azure의 RBAC(역할 기반 액세스 제어) 역할입니다.

* **권한 있는 액세스 그룹** – Azure AD 보안 그룹의 구성원 및 소유자 역할에 대한 JIT 액세스 권한을 설정합니다. 권한 있는 액세스 그룹은 Azure AD 역할 및 Azure 역할에 대한 PIM을 설정하는 대체 방법을 제공합니다. 사용자는 Intune, Azure Key Vault, Azure Information Protection 같은 Microsoft 온라인 서비스 전반에 걸쳐 다른 권한에 대한 PIM을 설정할 수도 있습니다. 

이러한 역할 또는 그룹에 다음을 할당할 수 있습니다. 

* **사용자** - Azure AD 역할, Azure 역할, 권한 있는 액세스 그룹에 대한 JIT 액세스 권한을 얻습니다. 

* **그룹** - Azure AD 역할 및 Azure 역할에 대한 JIT 액세스 권한을 얻기 위한 그룹의 사용자입니다. Azure AD 역할의 경우 그룹은 역할에 할당 가능한 것으로 표시된 새로 생성된 클라우드 그룹이어야 합니다. 반면 Azure 역할의 경우 그룹은 Azure AD 보안 그룹일 수 있습니다. 권한 있는 액세스 그룹에 그룹을 할당/중첩하지 않는 것이 좋습니다. 

> [!NOTE] 
>Azure AD 역할, Azure 역할, 권한 있는 액세스 그룹에 적격으로 서비스 주체를 할당할 수는 없지만, 세 가지 모두에 시간이 제한된 활성 할당을 부여할 수 있습니다.

### <a name="principle-of-least-privilege"></a>최소 권한 원칙

[작업을 수행하는 데 필요한 최소 권한](../roles/delegate-by-task.md)이 있는 역할을 사용자에게 할당합니다. 이 방법은 글로벌 관리자 수를 최소화하고, 그 대신 특정 시나리오에 대해 구체적 관리자 역할을 사용합니다.

> [!NOTE] 
> Microsoft에는 전역 관리자가 거의 없습니다. [Microsoft에서 Privileged Identity Management를 사용하는 방법](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)에 관해 자세히 알아보세요.

### <a name="type-of-assignments"></a>할당 유형 

**적격** 및 **활성** 의 두 가지 할당 유형이 있습니다. 사용자가 역할에 대한 자격을 얻은 경우 권한 있는 작업을 수행해야 할 때 해당 역할을 활성화할 수 있음을 의미합니다. 

각 할당 형식에 대해 시작과 종료 시간을 설정할 수도 있습니다. 이를 통해 네 가지 가능한 할당 유형이 제공됩니다.

* 영구 적격

* 영구 활성

* 시간 범위 적격(할당의 시작 및 종료 날짜가 지정됨)

* 시간 범위 활성(할당의 시작 및 종료 날짜가 지정됨)

역할이 만료되는 경우 이러한 할당을 **연장** 하거나 **갱신** 할 수 있습니다. 

권장되는 [두 개의 비상 긴급 액세스 계정](../roles/security-emergency-access.md)(영구적 전역 관리자 역할을 가져야 함) 이외에 역할에 대해 영구적으로 활성화된 할당을 유지하지 않는 것이 **좋습니다.** 

## <a name="plan-the-project"></a>프로젝트 계획

기술 프로젝트가 실패하는 이유는 일반적으로 영향, 결과, 책임에 대한 기대 수준이 일치하지 않기 때문입니다. 이러한 문제를 방지하려면 [올바른 관련자를 참여](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders)시키고 프로젝트의 관련자 역할이 명확하게 이해되도록 해야 합니다.

### <a name="plan-a-pilot"></a>파일럿 계획

배포의 각 단계에서 결과가 예상된 것인지를 평가해야 합니다. [파일럿에 대한 모범 사례](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)를 참조하세요.

* 작은 사용자 세트(파일럿 그룹)로 시작하여 PIM이 예상대로 작동하는지 확인합니다.

* 역할 또는 권한 있는 액세스 그룹에 대해 설정한 모든 구성이 올바르게 작동하는지 확인합니다. 

* 철저하게 테스트한 후에만 프로덕션으로 전달합니다. 

### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자의 경험이 어떻게 변화하고 언제 변할 것인지, 문제가 발생할 경우 지원을 받는 방법에 대해 사용자와 사전에 소통합니다.

내부 IT 지원팀과 시간을 정하여 PIM 워크플로를 안내합니다. 지원팀에 적절한 설명서 및 연락처 정보를 제공합니다.

## <a name="plan-testing-and-rollback"></a>테스트 및 롤백 계획

> [!NOTE] 
> Azure AD 역할의 경우 조직은 종종 전역 관리자를 먼저 테스트하고 롤아웃하지만, Azure 리소스의 경우 일반적으로 한 번에 하나의 Azure 구독에서 PIM을 테스트합니다. 

### <a name="plan-testing"></a>테스트 계획

실제 사용자에게 영향을 주고 앱 및 리소스에 대한 액세스를 잠재적으로 중단하기 전에 PIM 설정이 예상대로 작동하는지 확인하기 위해 테스트 사용자를 만듭니다. 예상 결과와 실제 결과를 비교할 수 있도록 테스트 계획을 만듭니다. 

다음 표는 예제 테스트 사례를 보여 줍니다. 

| 역할| 활성화하는 동안 예상되는 동작| 실제 결과 |
| --- | --- | --- |
|전역 관리자| <li> MFA 요구 <br><li>  승인 필요 <br><li>  승인자가 알림을 수신하고 승인할 수 있음 <br><li>  미리 설정된 시간이 경과한 후 역할 만료|

Azure AD와 Azure 리소스 역할 모두에서 역할을 맡을 사용자를 지정했는지 확인합니다. 또한 스테이징 환경에서 PIM을 테스트할 때 다음 역할을 고려해야 합니다.

| 역할| Azure AD 역할| Azure 리소스 역할| 권한 있는 액세스 그룹 |
| --- | --- | --- |--- |
| 그룹의 구성원| | | x |
| 역할의 구성원| x| x|  |
| IT 서비스 소유자| x| | x |
| 구독 또는 리소스 소유자| | x| x |
| 권한 있는 액세스 그룹 소유자| | | x |

### <a name="plan-rollback"></a>롤백 계획

PIM이 프로덕션 환경에서 원하는 대로 작동하지 않으면 역할 할당을 적격에서 활성으로 다시 변경할 수 있습니다. 구성한 각 역할에서 할당 유형이 **적격** 인 모든 사용자에 대해 줄임표(...)를 선택합니다. 그런 다음 **Make active**(활성화) 옵션을 선택하여 돌아가서 역할 할당을 **활성** 상태로 만들 수 있습니다.

## <a name="plan-and-implement-pim-for-azure-ad-roles"></a>Azure AD 역할에 대한 PIM 계획 및 구현

Azure AD 역할을 관리하기 위해 PIM을 준비하려면 다음 작업을 수행합니다. 

### <a name="discover-and-mitigate-privileged-roles"></a>권한 있는 역할 검색 및 완화

조직에서 권한 있는 역할을 가진 담당자를 나열합니다. 할당된 사용자를 검토하고, 역할이 더 이상 필요하지 않은 관리자를 식별하여 할당에서 제거합니다. 

[Azure AD 역할 액세스 검토](pim-how-to-start-security-review.md)를 사용하여 할당의 검색, 검토, 승인 또는 제거를 자동화할 수 있습니다.

### <a name="determine-roles-to-be-managed-by-pim"></a>PIM으로 관리할 역할 결정

권한이 가장 많은 Azure AD 역할의 보호 우선 순위를 지정합니다. 조직에 가장 중요한 데이터 및 사용 권한을 고려하는 것도 중요합니다. 

우선, PIM을 사용하여 모든 전역 및 보안 관리자 역할을 관리해야 합니다. 이들이 손상 시 가장 큰 피해를 입힐 수 있는 사용자이기 때문입니다. 그런 다음 공격에 취약할 수 있는 관리해야 하는 다른 역할을 고려합니다.

### <a name="configure-pim-settings-for-azure-ad-roles"></a>Azure AD 역할에 대한 PIM 설정 구성

조직에서 사용하는 모든 권한 있는 Azure AD 역할에 대해 [PIM 설정을 구상 및 구성합니다](pim-how-to-change-default-settings.md). 

다름 표는 예제 설정을 보여 줍니다.

| 역할| MFA 요구| 알림| 인시던트 티켓| 승인 필요| 승인자| 활성화 기간| 영구 관리자 |
| --- | --- | --- |--- |--- |--- |--- |--- |
| 전역 관리자| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| 기타 전역 관리자| 1시간| 응급 액세스 계정 |
| Exchange 관리| :heavy_check_mark:| :heavy_check_mark:| :x:| :x:| None| 2시간| None |
| 기술 지원팀 관리자| :x:| :x:| :heavy_check_mark:| :x:| None| 8시간| None |


### <a name="assign-and-activate-azure-ad-roles"></a>Azure AD 역할 할당 및 활성화 

PIM의 Azure AD 역할의 경우 권한 있는 역할 관리자 또는 전역 관리자 역할에 있는 사용자만이 다른 관리자에 대한 할당을 관리할 수 있습니다. 또한 전역 관리자, 보안 관리자, 전역 읽기 권한자, 보안 읽기 권한자는 PIM에서 Azure AD 역할에 대한 할당을 볼 수 있습니다. 

아래 링크의 지침을 따르세요.

1.[적격 할당 제공](pim-how-to-add-role-to-user.md).

2.[적격 사용자가 해당 Azure AD 역할을 적시에 활성화할 수 있도록 허용](pim-how-to-activate-role.md)

역할의 만료가 가까워지면 [PIM을 사용하여 역할을 확장하거나 갱신](pim-resource-roles-renew-extend.md)합니다. 사용자가 시작하는 두 작업은 글로벌 관리자 또는 권한 있는 역할 관리자의 승인이 필요합니다.  사용자가 시작하는 두 작업은 글로벌 관리자 또는 권한 있는 역할 관리자의 승인이 필요합니다. 

Azure AD 역할에서 이런 중요한 이벤트가 발생하면 PIM은 역할, 이벤트, 알림 설정에 따라 권한 관리자에게 [메일 알림 및 주간 다이제스트 메일을 보냅니다](pim-email-notifications.md). 이러한 이메일에는 역할 활성화 또는 갱신과 같은 관련 작업에 대한 링크도 포함될 수 있습니다. 

> [!NOTE] 
>[Azure AD 역할용 Microsoft Graph API를 사용](pim-apis.md)하여 이러한 PIM 작업을 수행할 수도 있습니다. 

### <a name="approve-or-deny-pim-activation-requests"></a>PIM 활성화 요청 승인 또는 거부 

위임된 승인자는 요청이 승인 보류 중일 때 메일 알림을 받습니다. [Azure 리소스 역할 활성화 요청을 승인 또는 거부](pim-resource-roles-approval-workflow.md)하려면 다음 단계를 수행합니다.

### <a name="view-audit-history-for-azure-ad-roles"></a>Azure AD 역할에 대한 감사 기록 보기

Azure AD 역할에 대해 지난 30일 이내의 [모든 역할 할당 및 활성화에 대한 감사 기록을 봅니다](pim-how-to-use-audit-log.md). 전역 관리자 또는 권한 있는 역할 권리자인 경우 이 감사 로그에 액세스할 수 있습니다. 

적어도 한 명의 관리자가 매주 모든 감사 이벤트를 읽고 매월 감사 이벤트를 내보내도록 하는 것이 **좋습니다**.

### <a name="security-alerts-for-azure-ad-roles"></a>Azure AD 역할에 대한 보안 경고

의심스럽고 안전하지 않은 활동의 경우 경고를 트리거할 [Azure AD 역할에 대한 보안 경고를 구성합니다](pim-how-to-configure-security-alerts.md).

## <a name="plan-and-implement-pim-for-azure-resource-roles"></a>Azure 리소스 역할에 대한 PIM의 계획 및 구현

Azure 리소스 역할을 관리하기 위해 PIM을 준비하려면 다음 작업을 수행합니다.

### <a name="discover-and-mitigate-privileged-roles"></a>권한 있는 역할 검색 및 완화

각 구독 또는 리소스에 연결된 소유자 및 사용자 액세스 관리자 할당을 최소화하고 불필요한 할당을 제거합니다.

전역 관리자는 [액세스 권한을 상승시켜 모든 Azure 구독을 관리](../../role-based-access-control/elevate-access-global-admin.md)할 수 있습니다. 그런 다음, 각 구독 소유자를 찾고 해당 소유자와 협력하여 구독 내에서 불필요한 할당을 제거할 수 있습니다.

[Azure 리소스에 대한 액세스 검토](pim-resource-roles-start-access-review.md)를 사용하여 불필요한 역할 할당을 감사하고 제거합니다. 

### <a name="determine-roles-to-be-managed-by-pim"></a>PIM으로 관리할 역할 결정

Azure 리소스에 대해 PIM을 사용하여 관리해야 하는 역할 할당을 결정할 경우에는 먼저 조직에 가장 중요한 [관리 그룹](../../governance/management-groups/overview.md), 구독, 리소스 그룹, 리소스를 식별해야 합니다. 관리 그룹을 사용하여 조직 내에서 모든 리소스를 구성하는 것이 좋습니다.

PIM을 사용하여 모든 구독 소유자 및 사용자 액세스 관리자 역할을 관리하는 것이 **좋습니다**. 

구독 소유자와 협력하여 각 구독에서 관리하는 리소스를 문서화하고 손상된 경우 각 리소스의 위험 수준을 분류합니다. 위험 수준을 기반으로 PIM을 사용해 리소스 관리의 우선 순위를 지정합니다. 여기에는 구독에 연결된 사용자 지정 리소스도 포함됩니다.

또한 중요 서비스의 구독 또는 리소스 소유자와 협력하여 중요한 구독 또는 리소스 내의 모든 역할에 대해 PIM 워크플로를 설정하는 것이 **좋습니다**.

중요하지 않은 구독 또는 리소스의 경우 모든 역할에 대해 PIM을 설정할 필요가 없습니다. 그러나 여전히 PIM을 사용하여 소유자 및 사용자 액세스 관리자 역할을 보호하는 것이 좋습니다.

### <a name="configure-pim-settings-for-azure-resource-roles"></a>Azure 리소스 역할에 대한 PIM 설정 구성

PIM으로 보호하려는 Azure 리소스 역할에 대해 [설정을 구상 및 구성합니다](pim-resource-roles-configure-role-settings.md). 

다름 표는 예제 설정을 보여 줍니다.

| 역할| MFA 요구| 알림| 승인 필요| 승인자| 활성화 기간| 활성 관리자| 활성화 만료| 자격 만료|
| --- | --- | --- |--- |--- |--- |--- |---|---|
| 중요한 구독 소유자| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| 구독의 기타 소유자| 1시간| None| 해당 없음| 3개월 |
| 덜 중요한 구독의 사용자 액세스 관리자| :heavy_check_mark:| :heavy_check_mark:| :x:| None| 1시간| None| 해당 없음| 3개월 |

### <a name="assign-and-activate-azure-resource-role"></a>Azure 리소스 역할 할당 및 활성화

PIM의 Azure 리소스 역할의 경우 소유자 또는 사용자 액세스 관리자만 다른 관리자에 대한 할당을 관리할 수 있습니다. 권한 있는 역할 관리자, 보안 관리자 또는 보안 읽기 권한자인 사용자는 기본적으로 Azure 리소스 역할에 대한 할당을 볼 수 있는 액세스 권한을 가지고 있지 않습니다.

아래 링크의 지침을 따르세요.

1.[적격 할당 제공](pim-resource-roles-assign-roles.md).

2.[적격 사용자가 해당 Azure 역할을 적시에 활성화할 수 있도록 허용](pim-resource-roles-activate-your-roles.md)

권한 있는 역할 할당의 만료가 가까워지면 [PIM을 사용하여 역할을 확장하거나 갱신](pim-resource-roles-renew-extend.md)합니다. 사용자가 시작한 두 작업 모두 리소스 소유자 또는 사용자 액세스 관리자의 승인이 필요합니다. 

Azure 리소스 역할에서 이런 중요한 이벤트가 발생하면 PIM은 소유자 및 사용자 액세스 관리자에게 [메일 알림](pim-email-notifications.md)을 보냅니다. 이러한 이메일에는 역할 활성화 또는 갱신과 같은 관련 작업에 대한 링크도 포함될 수 있습니다.

>[!NOTE]
>[Azure 리소스 역할에 대한 Microsoft Azure Resource Manager API를 사용](pim-apis.md)하여 이러한 PIM 작업을 수행할 수도 있습니다. 

### <a name="approve-or-deny-pim-activation-requests"></a>PIM 활성화 요청 승인 또는 거부

[Azure AD 역할에 대한 활성화 요청 승인 또는 거부](azure-ad-pim-approval-workflow.md) - 위임된 승인자는 요청이 승인 보류 중일 때 메일 알림을 받습니다.

### <a name="view-audit-history-for-azure-resource-roles"></a>Azure 리소스 역할에 대한 감사 기록 보기

Azure 리소스 역할에 대해 지난 30일 이내의 [모든 할당 및 활성화에 대한 감사 기록을 봅니다](azure-pim-resource-rbac.md).

### <a name="security-alerts-for-azure-resource-roles"></a>Azure 리소스 역할에 대한 보안 경고

의심스럽고 안전하지 않은 활동의 경우 경고를 트리거할 [Azure 리소스 역할에 대한 보안 경고를 구성합니다](pim-resource-roles-configure-alerts.md).

## <a name="plan-and-implement-pim-for-privileged-access-groups"></a>권한 있는 액세스 그룹에 대한 PIM의 계획 및 구현

권한 있는 액세스 그룹을 관리하기 위해 PIM을 준비하려면 다음 작업을 수행합니다.

### <a name="discover-privileged-access-groups"></a>권한 있는 액세스 그룹 검색

한 개인에게 PIM을 통해 Azure AD 역할에 대한 5개 또는 6개의 적격 할당이 있는 경우일 수 있습니다. 각 역할을 개별적으로 활성화해야 하므로 생산성이 저하될 수 있습니다. 더욱 심각한 것은 수십 또는 수백 개의 Azure 리소스가 할당될 수 있으므로 문제가 악화됩니다.

이 경우 권한 있는 액세스 그룹을 사용해야 합니다. 권한 있는 액세스 그룹을 만들고 여러 역할에 영구적 활성 액세스 권한을 부여합니다. [권한 있는 액세스 그룹 관리 기능](groups-features.md)을 참조하세요.

Azure AD 역할 할당 그룹을 권한 있는 액세스 그룹으로 관리하려면 [해당 그룹을 PIM의 관리 하에 두어야](groups-discover-groups.md) 합니다.

### <a name="configure-pim-settings-for-privileged-access-groups"></a>권한 있는 액세스 그룹에 대한 PIM 설정 구성

PIM으로 보호하려는 권한 있는 액세스 그룹에 대해 [설정을 구상 및 구성합니다](groups-role-settings.md).

다름 표는 예제 설정을 보여 줍니다.

| 역할| MFA 요구| 알림| 승인 필요| 승인자| 활성화 기간| 활성 관리자| 활성화 만료| 자격 만료 |
| --- | --- | --- |--- |--- |--- |--- |---|---|
| 소유자| :heavy_check_mark:| :heavy_check_mark:| :heavy_check_mark:| 리소스의 기타 소유자| 1시간| None| 해당 없음| 3개월 |
| 멤버| :heavy_check_mark:| :heavy_check_mark:| :x:| None| 5시간| None| 해당 없음| 3개월 |

### <a name="assign-eligibility-for-privileged-access-groups"></a>권한 있는 액세스 그룹에 대한 자격 할당

[권한 있는 액세스 그룹의 구성원이나 소유자에게 자격을 할당](groups-assign-member-owner.md)할 수 있습니다. 하나만 활성화하면 연결된 모든 리소스에 액세스할 수 있습니다. 

>[!NOTE] 
>사용자에게 역할을 할당하는 것과 동일한 방식으로 권한 있는 그룹을 하나 이상의 Azure AD 및 Azure 리소스 역할에 할당할 수 있습니다. 단일 Azure AD 조직(테넌트)에서 최대 250개의 역할 할당 가능 그룹을 만들 수 있습니다.

![권한 있는 액세스 그룹에 대한 자격 할당](media/pim-deployment-plan/privileged-access-groups.png)


권한 있는 그룹 할당의 만료가 가까워지면 [PIM을 사용하여 그룹 할당을 확장하거나 갱신](groups-renew-extend.md)합니다. 그룹 소유자의 승인이 필요합니다.

### <a name="approve-or-deny-pim-activation-request"></a>PIM 활성화 요청 승인 또는 거부

활성화 승인이 필요하도록 권한 있는 액세스 그룹 구성원 및 소유자를 구성하고 Azure AD 조직의 사용자 또는 그룹을 위임된 승인자로 선택합니다. 권한 있는 역할 관리자의 워크로드를 줄이기 위해 각 그룹에 대해 둘 이상의 승인자를 선택하는 것이 좋습니다. 

[권한 있는 액세스 그룹에 대한 역할 활성화 요청 승인 또는 거부](groups-approval-workflow.md) 위임된 승인자는 요청이 승인 보류 중일 때 메일 알림을 받게 됩니다.

### <a name="view-audit-history-for-privileged-access-groups"></a>권한 있는 액세스 그룹에 대한 감사 기록 보기

권한 있는 액세스 그룹에 대해 지난 30일 이내의 [모든 할당 및 활성화에 대한 감사 기록을 봅니다](groups-audit.md).

## <a name="next-steps"></a>다음 단계

* PIM 관련 문제가 있는 경우 [PIM 문제 해결](pim-troubleshoot.md)을 참조하세요.

* [다른 ID 기능 배포](../fundamentals/active-directory-deployment-plans.md)

 

