---
title: IRS 1075 2016년 9월 규정 준수 세부 정보(Azure Government)
description: IRS 1075 2016년 9월(Azure Government) 규정 준수 기본 제공 이니셔티브의 세부 정보 각 컨트롤은 평가를 지원하는 하나 이상의 Azure Policy 정의에 매핑됩니다.
ms.date: 09/17/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 78563496f849e05f31c86099de37713a69c7f0c5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666272"
---
# <a name="details-of-the-irs-1075-september-2016-azure-government-regulatory-compliance-built-in-initiative"></a>IRS 1075 2016년 9월(Azure Government) 규정 준수 기본 제공 이니셔티브의 세부 정보

다음 문서에서는 Azure Policy 규정 준수 기본 제공 이니셔티브 정의가 IRS 1075 2016년 9월(Azure Government)에서 **규정 준수 도메인** 및 **컨트롤** 에 매핑되는 방법을 자세히 설명합니다.
이 규정 준수 표준에 관한 자세한 내용은 [IRS 1075 2016년 9월](https://www.irs.gov/pub/irs-pdf/p1075.pdf)을 참조하세요. _소유권_ 을 이해하려면 [Azure Policy 정책 정의](../concepts/definition-structure.md#type) 및 [클라우드의 공동 책임](../../../security/fundamentals/shared-responsibility.md)을 참조하세요.

다음은 **IRS 1075 2016년 9월** 컨트롤에 대한 매핑입니다. 특정 **규정 준수 도메인** 으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 여러 컨트롤이 [Azure Policy](../overview.md) 이니셔티브 정의를 사용하여 구현됩니다. 전체 이니셔티브 정의를 검토하려면 Azure Portal에서 **정책** 을 열고 **정의** 페이지를 선택합니다.
그런 다음, **IRS 1075 2016년 9월** 규정 준수 기본 제공 이니셔티브 정의를 찾아서 선택합니다.

기본 제공 이니셔티브는 [IRS 1075 2016년 9월 청사진 샘플](../../blueprints/samples/irs-1075-sept2016.md)의 일부로 배포됩니다.

> [!IMPORTANT]
> 아래의 각 컨트롤은 하나 이상의 [Azure Policy](../overview.md) 정의와 연결되어 있습니다.
> 이러한 정책은 컨트롤을 사용한 [규정 준수 평가](../how-to/get-compliance-data.md)에 도움이 될 수 있지만, 컨트롤과 하나 이상의 정책 간에 일대일 또는 완벽한 일치 관계가 없는 경우도 많습니다. 따라서 Azure Policy의 **규정 준수** 는 정책 정의 자체만 가리킬 뿐, 컨트롤의 모든 요구 사항을 완벽하게 준수한다는 것은 아닙니다. 또한 규정 준수 표준에는 현재 Azure Policy 정의에서 처리되지 않은 컨트롤이 포함되어 있습니다. 따라서 Azure Policy의 규정 준수는 전반적인 규정 준수 상태를 부분적으로 표시할 뿐입니다. 이 규정 준수 표준에 대한 규정 준수 도메인, 컨트롤, Azure Policy 정의 간의 연결은 시간이 지나면 변경될 수 있습니다. 변경 기록을 보려면 [GitHub 커밋 기록](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Azure%20Government/Regulatory%20Compliance/IRS1075_audit.json)을 참조하세요.

## <a name="access-control"></a>Access Control

### <a name="account-management-ac-2"></a>계정 관리(AC-2)

**ID**: IRS 1075 9.3.1.2

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 서버에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |SQL 서버에 대한 Azure Active Directory 관리자 프로비전을 감사하여 Azure AD 인증을 활성화합니다. Azure AD 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[사용자 지정 RBAC 규칙 사용 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |오류가 발생하기 쉬운 사용자 지정 RBAC 역할 대신 '소유자, 기여자, 읽기 권한자' 같은 기본 제공 역할을 감사합니다. 사용자 지정 역할 사용은 예외로 처리되며 엄격한 검토 및 위협 모델링이 필요합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |더 이상 사용되지 않는 계정을 구독에서 제거해야 합니다.  사용되지 않는 계정은 로그인이 차단된 계정입니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.  사용되지 않는 계정은 로그인이 차단된 계정입니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |모니터링되지 않는 액세스를 방지하려면 소유자 권한이 있는 외부 계정을 구독에서 제거해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |모니터링되지 않는 액세스를 방지하려면 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |모니터링되지 않는 액세스를 방지하려면 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |
|[가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |가능한 네트워크 JIT(Just In Time) 액세스는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Service Fabric에서 Azure Active Directory를 통한 클라이언트 인증의 사용만 감사 |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

### <a name="information-flow-enforcement-ac-4"></a>정보 흐름 적용(AC-4)

**ID**: IRS 1075 9.3.1.4

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용해서는 안 됩니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |CORS(교차 원본 리소스 공유)는 웹 애플리케이션에 액세스하는 모든 도메인을 허용하지 않아야 합니다. 필요한 도메인만 웹앱과 상호 작용할 수 있도록 허용합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="separation-of-duties-ac-5"></a>의무 분리(AC-5)

**ID**: IRS 1075 9.3.1.5

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 최대 3명의 소유자를 지정해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |보안이 침해된 소유자의 위반 가능성을 줄이려면 최대 3명의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[시스템 할당 관리 ID를 추가하여 ID가 없는 가상 머신에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |이 정책은 게스트 구성에서 지원되지만 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[시스템이 할당한 관리 ID를 추가하여 사용자가 할당한 ID가 있는 VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |이 정책은 게스트 구성에서 지원되고 사용자 할당 ID가 하나 이상 있지만 시스템 할당 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[관리자 그룹에 지정된 구성원이 없는 Windows 머신 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 로컬 관리자 그룹에 정책 매개 변수에 나열된 구성원이 하나 이상 포함되어 있지 않은 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[관리자 그룹에 지정된 구성원이 있는 Windows 머신 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 로컬 관리자 그룹에 정책 매개 변수에 나열된 구성원이 하나 이상 포함된 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Windows 게스트 구성 확장을 배포하여 Windows VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Windows 가상 머신에 Windows 게스트 구성 확장을 배포합니다. Windows 게스트 구성 확장은 모든 Windows 게스트 구성 할당의 필수 조건이며 Windows 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[구독에 둘 이상의 소유자를 할당해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |관리자 액세스 중복성을 유지하려면 둘 이상의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege-ac-6"></a>최소 권한(AC-6)

**ID**: IRS 1075 9.3.1.6

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 최대 3명의 소유자를 지정해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |보안이 침해된 소유자의 위반 가능성을 줄이려면 최대 3명의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[시스템 할당 관리 ID를 추가하여 ID가 없는 가상 머신에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |이 정책은 게스트 구성에서 지원되지만 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[시스템이 할당한 관리 ID를 추가하여 사용자가 할당한 ID가 있는 VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |이 정책은 게스트 구성에서 지원되고 사용자 할당 ID가 하나 이상 있지만 시스템 할당 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[관리자 그룹에 지정된 구성원이 없는 Windows 머신 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 로컬 관리자 그룹에 정책 매개 변수에 나열된 구성원이 하나 이상 포함되어 있지 않은 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |
|[관리자 그룹에 지정된 구성원이 있는 Windows 머신 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 로컬 관리자 그룹에 정책 매개 변수에 나열된 구성원이 하나 이상 포함된 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |
|[Windows 게스트 구성 확장을 배포하여 Windows VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Windows 가상 머신에 Windows 게스트 구성 확장을 배포합니다. Windows 게스트 구성 확장은 모든 Windows 게스트 구성 할당의 필수 조건이며 Windows 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[구독에 둘 이상의 소유자를 할당해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |관리자 액세스 중복성을 유지하려면 둘 이상의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="remote-access-ac-17"></a>원격 액세스(AC-17)

**ID**: IRS 1075 9.3.1.12

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[시스템 할당 관리 ID를 추가하여 ID가 없는 가상 머신에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |이 정책은 게스트 구성에서 지원되지만 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[시스템이 할당한 관리 ID를 추가하여 사용자가 할당한 ID가 있는 VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |이 정책은 게스트 구성에서 지원되고 사용자 할당 ID가 하나 이상 있지만 시스템 할당 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[암호 없이 계정에서 원격 연결을 허용하는 Linux 머신 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea53dbee-c6c9-4f0e-9f9e-de0039b78023) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 암호 없이 계정에서 원격 연결을 허용하는 Linux 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_LinuxPassword110_AINE.json) |
|[Linux 게스트 구성 확장을 배포하여 Linux VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Linux 가상 머신에 Linux 게스트 구성 확장을 배포합니다. Linux 게스트 구성 확장은 모든 Linux 게스트 구성 할당의 필수 조건이며 Linux 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[API Apps에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |원격 디버깅에 API 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[함수 앱에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |원격 디버깅에 함수 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |원격 디버깅에 웹 애플리케이션에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[스토리지 계정은 네트워크 액세스를 제한해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |스토리지 계정에 대한 네트워크 액세스가 제한되어야 합니다. 허용되는 네트워크의 애플리케이션만 스토리지 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용하기 위해 특정 Azure 가상 네트워크 또는 공용 인터넷 IP 주소 범위의 트래픽에 대한 액세스 권한을 부여할 수 있습니다. |감사, 거부, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

## <a name="awareness-and-training"></a>인식 및 교육

### <a name="content-of-audit-records-au-3"></a>감사 레코드의 콘텐츠(AU-3)

**ID**: IRS 1075 9.3.3.3

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[나열된 가상 머신 이미지의 Log Analytics 에이전트를 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |가상 머신 이미지가 정의된 목록에 없고 에이전트가 설치되어 있지 않은 경우 가상 머신을 비규격으로 보고합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[가상 머신 확장 집합에서 나열된 가상 머신 이미지의 Log Analytics 에이전트를 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |가상 머신 이미지가 정의된 목록에 없고 에이전트가 설치되어 있지 않은 경우 가상 머신 확장 집합을 비규격으로 보고합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[가상 머신을 지정된 작업 영역에 연결해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |정책/이니셔티브 할당에 지정된 Log Analytics 작업 영역에 로그하지 않는 경우 가상 머신을 비규격으로 보고합니다. |AuditIfNotExists, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |

### <a name="response-to-audit-processing-failures-au-5"></a>감사 처리 실패에 대한 응답(AU-5)

**ID**: IRS 1075 9.3.3.5

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[진단 설정 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |선택한 리소스 종류에 대한 감사 진단 설정 |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[SQL 서버에 대한 감사가 사용되도록 설정되어야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |서버의 모든 데이터베이스에서 데이터베이스 활동을 추적하고 감사 로그에 저장하려면 SQL Server에서의 감사를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[보호되지 않는 Azure SQL 서버에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |

### <a name="audit-review-analysis-and-reporting-au-6"></a>감사 검토, 분석 및 보고(AU-6)

**ID**: IRS 1075 9.3.3.6

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[나열된 가상 머신 이미지의 Log Analytics 에이전트를 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |가상 머신 이미지가 정의된 목록에 없고 에이전트가 설치되어 있지 않은 경우 가상 머신을 비규격으로 보고합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[가상 머신 확장 집합에서 나열된 가상 머신 이미지의 Log Analytics 에이전트를 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |가상 머신 이미지가 정의된 목록에 없고 에이전트가 설치되어 있지 않은 경우 가상 머신 확장 집합을 비규격으로 보고합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[가상 머신을 지정된 작업 영역에 연결해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |정책/이니셔티브 할당에 지정된 Log Analytics 작업 영역에 로그하지 않는 경우 가상 머신을 비규격으로 보고합니다. |AuditIfNotExists, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |

### <a name="audit-generation-au-12"></a>감사 생성(AU-12)

**ID**: IRS 1075 9.3.3.11

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[진단 설정 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |선택한 리소스 종류에 대한 감사 진단 설정 |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[SQL 서버에 대한 감사가 사용되도록 설정되어야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |서버의 모든 데이터베이스에서 데이터베이스 활동을 추적하고 감사 로그에 저장하려면 SQL Server에서의 감사를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[보호되지 않는 Azure SQL 서버에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[나열된 가상 머신 이미지의 Log Analytics 에이전트를 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |가상 머신 이미지가 정의된 목록에 없고 에이전트가 설치되어 있지 않은 경우 가상 머신을 비규격으로 보고합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[가상 머신 확장 집합에서 나열된 가상 머신 이미지의 Log Analytics 에이전트를 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |가상 머신 이미지가 정의된 목록에 없고 에이전트가 설치되어 있지 않은 경우 가상 머신 확장 집합을 비규격으로 보고합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[가상 머신을 지정된 작업 영역에 연결해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |정책/이니셔티브 할당에 지정된 Log Analytics 작업 영역에 로그하지 않는 경우 가상 머신을 비규격으로 보고합니다. |AuditIfNotExists, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |

## <a name="configuration-management"></a>구성 관리

### <a name="least-functionality-cm-7"></a>최소한의 기능(CM-7)

**ID**: IRS 1075 9.3.5.7

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |애플리케이션 제어를 활성화하여 머신에서 실행 중인 알려진 안전한 애플리케이션 목록을 정의하고, 다른 애플리케이션이 실행될 때 경고합니다. 이렇게 하면 맬웨어로부터 머신을 강화할 수 있습니다. 규칙을 구성하고 유지 관리하는 프로세스를 간소화하기 위해 Security Center는 기계 학습을 사용하여 각 머신에서 실행 중인 애플리케이션을 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="user-installed-software-cm-11"></a>사용자가 설치한 소프트웨어(CM-11)

**ID**: IRS 1075 9.3.5.11

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |애플리케이션 제어를 활성화하여 머신에서 실행 중인 알려진 안전한 애플리케이션 목록을 정의하고, 다른 애플리케이션이 실행될 때 경고합니다. 이렇게 하면 맬웨어로부터 머신을 강화할 수 있습니다. 규칙을 구성하고 유지 관리하는 프로세스를 간소화하기 위해 Security Center는 기계 학습을 사용하여 각 머신에서 실행 중인 애플리케이션을 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

## <a name="contingency-planning"></a>대체 계획

### <a name="alternate-processing-site-cp-7"></a>대체 처리 사이트(CP-7)

**ID**: IRS 1075 9.3.6.6

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[재해 복구가 구성되어 있지 않은 가상 머신 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |재해 복구가 구성되지 않은 가상 머신을 감사합니다. 재해 복구에 대한 자세한 내용은 [https://aka.ms/asr-doc](../../../site-recovery/index.yml)를 참조하세요. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

## <a name="identification-and-authentication"></a>식별 및 인증

### <a name="identification-and-authentication-organizational-users-ia-2"></a>식별 및 인증(조직 사용자)(IA-2)

**ID**: IRS 1075 9.3.7.2

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |계정 또는 리소스 위반을 방지하려면 쓰기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |계정 또는 리소스 위반을 방지하려면 소유자 권한이 있는 모든 구독 계정에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |계정 또는 리소스 위반을 방지하려면 읽기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="authenticator-management-ia-5"></a>인증자 관리(IA-5)

**ID**: IRS 1075 9.3.7.5

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[시스템 할당 관리 ID를 추가하여 ID가 없는 가상 머신에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |이 정책은 게스트 구성에서 지원되지만 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[시스템이 할당한 관리 ID를 추가하여 사용자가 할당한 ID가 있는 VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |이 정책은 게스트 구성에서 지원되고 사용자 할당 ID가 하나 이상 있지만 시스템 할당 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[passwd 파일 권한이 0644로 설정되지 않은 Linux 머신 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6955644-301c-44b5-a4c4-528577de6861) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. passwd 파일 권한이 0644로 설정되지 않은 Linux 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_LinuxPassword121_AINE.json) |
|[암호가 없는 계정이 있는 Linux 머신 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6ec09a3-78bf-4f8f-99dc-6c77182d0f99) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 암호가 없는 계정이 있는 Linux 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_LinuxPassword232_AINE.json) |
|[이전 24개의 암호를 다시 사용할 수 있는 Windows 머신 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b054a0d-39e2-4d53-bea3-9734cad2c69b) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 이전 24개의 암호를 다시 사용할 수 있는 Windows 머신인 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_WindowsPasswordEnforce_AINE.json) |
|[암호 사용 최대 기간이 70일로 설정되지 않은 Windows 머신 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ceb8dc2-559c-478b-a15b-733fbf1e3738) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 암호 사용 최대 기간이 70일로 설정되지 않은 Windows 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_WindowsMaximumPassword_AINE.json) |
|[암호 사용 최소 기간이 1일로 설정되지 않은 Windows 머신 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F237b38db-ca4d-4259-9e47-7882441ca2c0) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 암호 사용 최소 기간이 1일로 설정되지 않은 Windows 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_WindowsMinimumPassword_AINE.json) |
|[암호 복잡성 설정이 활성화되지 않는 Windows 머신 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf16e0bb-31e1-4646-8202-60a235cc7e74) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 암호 복잡성 설정이 활성화되지 않는 Windows 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_WindowsPasswordComplexity_AINE.json) |
|[암호 최소 길이가 14자로 제한되지 않는 Windows 머신 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2d0e922-65d0-40c4-8f87-ea6da2d307a2) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 암호 최소 길이가 14자로 제한되지 않는 Windows 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_WindowsPasswordLength_AINE.json) |
|[해독 가능한 암호화를 사용하여 암호를 저장하지 않는 Windows 머신 감사](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 해독 가능한 암호화를 사용하여 암호를 저장하지 않는 Windows 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[Linux 게스트 구성 확장을 배포하여 Linux VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Linux 가상 머신에 Linux 게스트 구성 확장을 배포합니다. Linux 게스트 구성 확장은 모든 Linux 게스트 구성 할당의 필수 조건이며 Linux 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Windows 게스트 구성 확장을 배포하여 Windows VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Windows 가상 머신에 Windows 게스트 구성 확장을 배포합니다. Windows 게스트 구성 확장은 모든 Windows 게스트 구성 할당의 필수 조건이며 Windows 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |

## <a name="risk-assessment"></a>위험 평가

### <a name="vulnerability-scanning-ra-5"></a>취약성 검색(RA-5)

**ID**: IRS 1075 9.3.14.3

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[보호되지 않는 Azure SQL 서버에 대해 SQL용 Azure Defender를 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[SQL 데이터베이스가 발견한 취약성을 해결해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |취약성 평가 검사 결과 및 데이터베이스 취약성을 해결하는 방법에 대한 권장 사항을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[머신 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |구성된 기준을 충족하지 않는 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

## <a name="system-and-communications-protection"></a>시스템 및 통신 보호

### <a name="denial-of-service-protection-sc-5"></a>서비스 거부 방지(SC-5)

**ID**: IRS 1075 9.3.16.4

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure DDoS Protection 표준을 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |공용 IP를 사용하는 애플리케이션 게이트웨이의 일부인 서브넷이 포함된 모든 가상 네트워크에 DDoS 보호 표준을 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |

### <a name="boundary-protection-sc-7"></a>경계 보호(SC-7)

**ID**: IRS 1075 9.3.16.5

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |애플리케이션 제어를 활성화하여 머신에서 실행 중인 알려진 안전한 애플리케이션 목록을 정의하고, 다른 애플리케이션이 실행될 때 경고합니다. 이렇게 하면 맬웨어로부터 머신을 강화할 수 있습니다. 규칙을 구성하고 유지 관리하는 프로세스를 간소화하기 위해 Security Center는 기계 학습을 사용하여 각 머신에서 실행 중인 애플리케이션을 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[가상 머신과 연결된 네트워크 보안 그룹에서 모든 네트워크 포트를 제한해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center에서 네트워크 보안 그룹의 인바운드 규칙 중 일부가 너무 관대하다는 사실을 식별했습니다. 인바운드 규칙에서 '모두' 또는 '인터넷' 범위에서 들어오는 액세스를 허용해서는 안 됩니다. 그러면 리소스가 공격자의 표적이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[스토리지 계정은 네트워크 액세스를 제한해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |스토리지 계정에 대한 네트워크 액세스가 제한되어야 합니다. 허용되는 네트워크의 애플리케이션만 스토리지 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용하기 위해 특정 Azure 가상 네트워크 또는 공용 인터넷 IP 주소 범위의 트래픽에 대한 액세스 권한을 부여할 수 있습니다. |감사, 거부, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="transmission-confidentiality-and-integrity-sc-8"></a>전송 기밀성 및 무결성(SC-8)

**ID**: IRS 1075 9.3.16.6

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[시스템 할당 관리 ID를 추가하여 ID가 없는 가상 머신에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |이 정책은 게스트 구성에서 지원되지만 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[시스템이 할당한 관리 ID를 추가하여 사용자가 할당한 ID가 있는 VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |이 정책은 게스트 구성에서 지원되고 사용자 할당 ID가 하나 이상 있지만 시스템 할당 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Windows 게스트 구성 확장을 배포하여 Windows VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Windows 가상 머신에 Windows 게스트 구성 확장을 배포합니다. Windows 게스트 구성 확장은 모든 Windows 게스트 구성 할당의 필수 조건이며 Windows 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[함수 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Azure Cache for Redis에 보안 연결만 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |SSL을 통해 설정된 Azure Cache for Redis 연결만 감사 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[스토리지 계정에 보안 전송을 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |스토리지 계정의 보안 전송 요구 사항을 감사합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[웹 애플리케이션은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |
|[Windows 웹 서버는 보안 통신 프로토콜을 사용하도록 구성해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |인터넷을 통해 전달되는 정보의 개인 정보를 보호하려면 웹 서버에서 최신 버전의 업계 표준 암호화 프로토콜인 TLS(전송 계층 보안)를 사용해야 합니다. TLS는 보안 인증서를 사용하여 컴퓨터 간의 연결을 암호화하여 네트워크를 통한 통신을 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |

### <a name="protection-of-information-at-rest-sc-28"></a>미사용 정보 보호(SC-28)

**ID**: IRS 1075 9.3.16.15

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[보호되지 않는 Azure SQL 서버에 대해 SQL용 Azure Defender를 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |저장 데이터를 보호하고 규정 준수 요구 사항을 충족하려면 투명한 데이터 암호화를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
|[가상 머신은 Compute 및 Storage 리소스 간에 임시 디스크, 캐시 및 데이터 흐름을 암호화해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |기본적으로 가상 머신의 OS 및 데이터 디스크는 플랫폼 관리형 키를 사용하여 미사용 시 암호화됩니다. 임시 디스크, 데이터 캐시 및 컴퓨팅과 스토리지 간에 이동하는 데이터는 암호화되지 않습니다. 다음과 같은 경우 이 권장 사항을 무시합니다. 1. 호스트에서 암호화를 사용하는 경우 또는 2. Managed Disks의 서버 쪽 암호화는 보안 요구 사항을 충족합니다. [Azure Disk Storage의 서버 쪽 암호화](../../../virtual-machines/disk-encryption.md) 및 [다양한 디스크 암호화 제품](../../../virtual-machines/disk-encryption-overview.md#comparison)에서 자세히 알아봅니다. |AuditIfNotExists, 사용 안 함 |[2.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

## <a name="system-and-information-integrity"></a>시스템 및 정보 무결성

### <a name="flaw-remediation-si-2"></a>결함 수정(SI-2)

**ID**: IRS 1075 9.3.17.2

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 데이터베이스가 발견한 취약성을 해결해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |취약성 평가 검사 결과 및 데이터베이스 취약성을 해결하는 방법에 대한 권장 사항을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[시스템 업데이트를 머신에 설치해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |서버의 누락된 보안 시스템 업데이트는 Azure Security Center에서 권장 사항으로 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |
|[머신 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |구성된 기준을 충족하지 않는 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="malicious-code-protection-si-3"></a>악성 코드 방지(SI-3)

**ID**: IRS 1075 9.3.17.3

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Security Center에서 누락된 Endpoint Protection 모니터링](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Endpoint Protection 에이전트가 설치되어 있지 않은 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Government/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="information-system-monitoring-si-4"></a>정보 시스템 모니터링(SI-4)

**ID**: IRS 1075 9.3.17.4

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[보호되지 않는 Azure SQL 서버에 대해 SQL용 Azure Defender를 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[나열된 가상 머신 이미지의 Log Analytics 에이전트를 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32133ab0-ee4b-4b44-98d6-042180979d50) |가상 머신 이미지가 정의된 목록에 없고 에이전트가 설치되어 있지 않은 경우 가상 머신을 비규격으로 보고합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_Audit.json) |
|[가상 머신 확장 집합에서 나열된 가상 머신 이미지의 Log Analytics 에이전트를 사용하도록 설정해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c3bc7b8-a64c-4e08-a9cd-7ff0f31e1138) |가상 머신 이미지가 정의된 목록에 없고 에이전트가 설치되어 있지 않은 경우 가상 머신 확장 집합을 비규격으로 보고합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_OSImage_VMSS_Audit.json) |
|[가상 머신을 지정된 작업 영역에 연결해야 함](https://portal.azure.us/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff47b5582-33ec-4c5c-87c0-b010a6b2e917) |정책/이니셔티브 할당에 지정된 Log Analytics 작업 영역에 로그하지 않는 경우 가상 머신을 비규격으로 보고합니다. |AuditIfNotExists, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalytics_WorkspaceMismatch_VM_Audit.json) |

## <a name="next-steps"></a>다음 단계

Azure Policy에 대한 추가 문서:

- [규정 준수](../concepts/regulatory-compliance.md) 개요
- [이니셔티브 정의 구조](../concepts/initiative-definition-structure.md)를 참조합니다.
- [Azure Policy 샘플](./index.md)의 다른 예제를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.