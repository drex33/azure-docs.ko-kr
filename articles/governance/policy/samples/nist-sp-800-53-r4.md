---
title: NIST SP 800-53 개정 4에 대한 규정 준수 세부 정보
description: NIST SP 800-53 개정 4 규정 준수 기본 제공 이니셔티브의 세부 정보 각 컨트롤은 평가를 지원하는 하나 이상의 Azure Policy 정의에 매핑됩니다.
ms.date: 09/17/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 27fbe443a5e93181005d15a58c58f42ccba9cec4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620619"
---
# <a name="details-of-the-nist-sp-800-53-rev-4-regulatory-compliance-built-in-initiative"></a>NIST SP 800-53 개정 4 규정 준수 기본 제공 이니셔티브의 세부 정보

다음 문서에서는 Azure Policy 규정 준수 기본 제공 이니셔티브 정의가 NIST SP 800-53 개정 4에서 **규정 준수 도메인** 및 **컨트롤** 에 매핑되는 방법을 자세히 설명합니다.
이 규정 준수 표준에 관한 자세한 내용은 [NIST SP 800-53 Rev. 4](https://nvd.nist.gov/800-53)를 참조하세요. _소유권_ 을 이해하려면 [Azure Policy 정책 정의](../concepts/definition-structure.md#type) 및 [클라우드의 공동 책임](../../../security/fundamentals/shared-responsibility.md)을 참조하세요.

다음은 **NIST SP 800-53 개정 4** 컨트롤에 대한 매핑입니다. 특정 **규정 준수 도메인** 으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 여러 컨트롤이 [Azure Policy](../overview.md) 이니셔티브 정의를 사용하여 구현됩니다. 전체 이니셔티브 정의를 검토하려면 Azure Portal에서 **정책** 을 열고 **정의** 페이지를 선택합니다.
그런 다음, **NIST SP 800-53 Rev. 4** 규정 준수 기본 제공 이니셔티브 정의를 찾아서 선택합니다.

> [!IMPORTANT]
> 아래의 각 컨트롤은 하나 이상의 [Azure Policy](../overview.md) 정의와 연결되어 있습니다.
> 이러한 정책은 컨트롤을 사용한 [규정 준수 평가](../how-to/get-compliance-data.md)에 도움이 될 수 있지만, 컨트롤과 하나 이상의 정책 간에 일대일 또는 완벽한 일치 관계가 없는 경우도 많습니다. 따라서 Azure Policy의 **규정 준수** 는 정책 정의 자체만 가리킬 뿐, 컨트롤의 모든 요구 사항을 완벽하게 준수한다는 것은 아닙니다. 또한 규정 준수 표준에는 현재 Azure Policy 정의에서 처리되지 않은 컨트롤이 포함되어 있습니다. 따라서 Azure Policy의 규정 준수는 전반적인 규정 준수 상태를 부분적으로 표시할 뿐입니다. 이 규정 준수 표준에 대한 규정 준수 도메인, 컨트롤, Azure Policy 정의 간의 연결은 시간이 지나면 변경될 수 있습니다. 변경 기록을 보려면 [GitHub 커밋 기록](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/NIST80053_audit.json)을 참조하세요.

## <a name="access-control"></a>Access Control

### <a name="access-control-policy-and-procedures"></a>액세스 제어 정책 및 절차

**ID**: NIST SP 800-53 Rev. 4 AC-1

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1000 - 액세스 제어 정책 및 프로시저](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ef3cc79-733e-48ed-ab6f-7bf439e9b406) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1000.json) |
|[Microsoft 관리형 컨트롤 1001 - 액세스 제어 정책 및 프로시저](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e26f8c3-4bf3-4191-b8fc-d888805101b7) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1001.json) |

### <a name="account-management"></a>계정 관리

**ID**: NIST SP 800-53 Rev. 4 AC-2

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 최대 3명의 소유자를 지정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |보안이 침해된 소유자의 위반 가능성을 줄이려면 최대 3명의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[SQL 서버에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |SQL 서버에 대한 Azure Active Directory 관리자 프로비전을 감사하여 Azure AD 인증을 활성화합니다. Azure AD 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[사용자 지정 RBAC 규칙 사용 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |오류가 발생하기 쉬운 사용자 지정 RBAC 역할 대신 '소유자, 기여자, 읽기 권한자' 같은 기본 제공 역할을 감사합니다. 사용자 지정 역할 사용은 예외로 처리되며 엄격한 검토 및 위협 모델링이 필요합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Cognitive Services 계정은 로컬 인증 방법을 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |로컬 인증 방법을 사용하지 않도록 설정하면 Cognitive Services 계정에 인증 전용 Azure Active Directory ID가 필요하므로 보안이 강화됩니다. [https://aka.ms/cs/auth](../../../cognitive-services/authentication.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[더 이상 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1cbf55-e8b6-442f-ba4c-7246b6381474) |더 이상 사용되지 않는 계정을 구독에서 제거해야 합니다.  사용되지 않는 계정은 로그인이 차단된 계정입니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |
|[소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.  사용되지 않는 계정은 로그인이 차단된 계정입니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |
|[소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |모니터링되지 않는 액세스를 방지하려면 소유자 권한이 있는 외부 계정을 구독에서 제거해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |모니터링되지 않는 액세스를 방지하려면 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |모니터링되지 않는 액세스를 방지하려면 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |
|[API 앱에서 관리 ID를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |인증 보안 강화를 위해 관리 ID 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[함수 앱에서 관리 ID를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |인증 보안 강화를 위해 관리 ID 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[웹앱에서 관리 ID를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |인증 보안 강화를 위해 관리 ID 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |
|[Microsoft 관리형 컨트롤 1002 - 계정 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F632024c2-8079-439d-a7f6-90af1d78cc65) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1002.json) |
|[Microsoft 관리형 컨트롤 1003 - 계정 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b68b179-3704-4ff7-b51d-7d65374d165d) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1003.json) |
|[Microsoft 관리형 컨트롤 1004 - 계정 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc17822dc-736f-4eb4-a97d-e6be662ff835) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1004.json) |
|[Microsoft 관리형 컨트롤 1005 - 계정 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b626abc-26d4-4e22-9de8-3831818526b1) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1005.json) |
|[Microsoft 관리형 컨트롤 1006 - 계정 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faae8d54c-4bce-4c04-b3aa-5b65b67caac8) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1006.json) |
|[Microsoft 관리형 컨트롤 1007 - 계정 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17200329-bf6c-46d8-ac6d-abf4641c2add) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1007.json) |
|[Microsoft 관리형 컨트롤 1008 - 계정 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8356cfc6-507a-4d20-b818-08038011cd07) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1008.json) |
|[Microsoft 관리형 컨트롤 1009 - 계정 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb26f8610-e615-47c2-abd6-c00b2b0b503a) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1009.json) |
|[Microsoft 관리형 컨트롤 1010 - 계정 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F784663a8-1eb0-418a-a98c-24d19bc1bb62) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1010.json) |
|[Microsoft 관리형 컨트롤 1011 - 계정 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7e6a54f3-883f-43d5-87c4-172dfd64a1f5) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1011.json) |
|[Microsoft 관리형 컨트롤 1012 - 계정 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefd7b9ae-1db6-4eb6-b0fe-87e6565f9738) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1012.json) |
|[Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Service Fabric에서 Azure Active Directory를 통한 클라이언트 인증의 사용만 감사 |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

### <a name="automated-system-account-management"></a>자동화된 시스템 계정 관리

**ID**: NIST SP 800-53 Rev. 4 AC-2 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 서버에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |SQL 서버에 대한 Azure Active Directory 관리자 프로비전을 감사하여 Azure AD 인증을 활성화합니다. Azure AD 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[Cognitive Services 계정은 로컬 인증 방법을 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |로컬 인증 방법을 사용하지 않도록 설정하면 Cognitive Services 계정에 인증 전용 Azure Active Directory ID가 필요하므로 보안이 강화됩니다. [https://aka.ms/cs/auth](../../../cognitive-services/authentication.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[Microsoft 관리형 컨트롤 1013 - 계정 관리 \| 자동화된 시스템 계정 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fd7b917-d83b-4379-af60-51e14e316c61) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1013.json) |
|[Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Service Fabric에서 Azure Active Directory를 통한 클라이언트 인증의 사용만 감사 |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

### <a name="removal-of-temporary--emergency-accounts"></a>임시/비상 계정 제거

**ID**: NIST SP 800-53 Rev. 4 AC-2 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1014 - 계정 관리 \| 임시/비상용 계정 제거](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5dee936c-8037-4df1-ab35-6635733da48c) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1014.json) |

### <a name="disable-inactive-accounts"></a>비활성 계정 사용 안 함

**ID**: NIST SP 800-53 Rev. 4 AC-2 (3)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1015 - 계정 관리 \| 비활성 계정 사용 안 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F544a208a-9c3f-40bc-b1d1-d7e144495c14) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1015.json) |

### <a name="automated-audit-actions"></a>자동화된 감사 작업

**ID**: NIST SP 800-53 Rev. 4 AC-2 (4)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1016 - 계정 관리 \| 자동화된 감사 작업](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8b43277-512e-40c3-ab00-14b3b6e72238) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1016.json) |

### <a name="inactivity-logout"></a>비활성 로그아웃

**ID**: NIST SP 800-53 Rev. 4 AC-2 (5)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1017 - 계정 관리 \| 비활성 로그아웃](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fc3db37-e59a-48c1-84e9-1780cedb409e) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1017.json) |

### <a name="role-based-schemes"></a>역할 기반 체계

**ID**: NIST SP 800-53 Rev. 4 AC-2 (7)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 서버에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |SQL 서버에 대한 Azure Active Directory 관리자 프로비전을 감사하여 Azure AD 인증을 활성화합니다. Azure AD 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[사용자 지정 RBAC 규칙 사용 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |오류가 발생하기 쉬운 사용자 지정 RBAC 역할 대신 '소유자, 기여자, 읽기 권한자' 같은 기본 제공 역할을 감사합니다. 사용자 지정 역할 사용은 예외로 처리되며 엄격한 검토 및 위협 모델링이 필요합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Cognitive Services 계정은 로컬 인증 방법을 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |로컬 인증 방법을 사용하지 않도록 설정하면 Cognitive Services 계정에 인증 전용 Azure Active Directory ID가 필요하므로 보안이 강화됩니다. [https://aka.ms/cs/auth](../../../cognitive-services/authentication.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[Microsoft 관리형 컨트롤 1018 - 계정 관리 \| 역할 기반 스키마](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9121abf-e698-4ee9-b1cf-71ee528ff07f) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1018.json) |
|[Microsoft 관리형 컨트롤 1019 - 계정 관리 \| 역할 기반 스키마](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a3ee9b2-3977-459c-b8ce-2db583abd9f7) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1019.json) |
|[Microsoft 관리형 컨트롤 1020 - 계정 관리 \| 역할 기반 스키마](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b291ee8-3140-4cad-beb7-568c077c78ce) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1020.json) |
|[Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Service Fabric에서 Azure Active Directory를 통한 클라이언트 인증의 사용만 감사 |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|[관리 인증서 대신 서비스 주체를 사용하여 구독을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6646a0bd-e110-40ca-bb97-84fcee63c414) |관리 인증서를 사용하면 해당 인증서로 인증된 사람은 누구나 연결된 구독을 관리할 수 있습니다. 구독을 보다 안전하게 관리하려면 Resource Manager와 함께 서비스 주체를 사용하여 인증서 손상의 영향을 제한하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UseServicePrincipalToProtectSubscriptions.json) |

### <a name="restrictions-on-use-of-shared--group-accounts"></a>공유/그룹 계정 사용에 대한 제한 사항

**ID**: NIST SP 800-53 Rev. 4 AC-2 (9)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1021 - 계정 관리 \| 공유/그룹 계정 사용에 대한 제한 사항](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a3eb0a3-428d-4669-baff-20a14eb4b551) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1021.json) |

### <a name="shared--group-account-credential-termination"></a>공유/그룹 계정 자격 증명 종료

**ID**: NIST SP 800-53 Rev. 4 AC-2 (10)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1022 - 계정 관리 \| 공유/그룹 계정 자격 증명 종료](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F411f7e2d-9a0b-4627-a0b9-1700432db47d) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1022.json) |

### <a name="usage-conditions"></a>사용 조건

**ID**: NIST SP 800-53 Rev. 4 AC-2 (11)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1023 - 계정 관리 \| 사용 조건](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe55698b6-3dea-4aa9-99b9-d8218c6ab6e5) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1023.json) |

### <a name="account-monitoring--atypical-usage"></a>계정 모니터링/비정상적 사용

**ID**: NIST SP 800-53 Rev. 4 AC-2 (12)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Arc 사용 Kubernetes 클러스터에 Azure Defender의 확장이 설치되어 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Arc용 Azure Defender 확장은 Arc 지원 Kubernetes 클러스터에 대한 위협 보호를 제공합니다. 이 확장은 클러스터의 노드에서 데이터를 수집하고 추가 분석을 위해 클라우드의 Azure Defender for Kubernetes 백 엔드로 보냅니다. [https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[Azure Defender for App Service를 사용해야 합니다](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service는 클라우드의 규모와 Azure가 클라우드 공급자로서 가지고 있는 가시성을 활용하여 일반적인 웹앱 공격을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure SQL Database 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[컨테이너 레지스트리용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |컨테이너 레지스트리용 Azure Defender는 지난 30일 내에 끌어와서 레지스트리에 푸시한 이미지의 취약성을 검사하고, 각 이미지에 대해 발견한 자세한 내용을 공개합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS는 Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링하여 클라우드 리소스에 대한 추가 보호 계층을 제공합니다. Azure Defender는 DNS 계층에서 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)에서 Azure Defender for DNS의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault는 키 자격 증명 모음 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하여 보안 인텔리전스에 추가 보호 계층을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager는 조직의 리소스 관리 작업을 자동으로 모니터링합니다. Azure Defender는 위협을 감지하고 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)에서 Azure Defender for Resource Manager의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[머신의 SQL 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 SQL용 Azure Defender를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[스토리지용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |가능한 네트워크 JIT(Just In Time) 액세스는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Microsoft 관리형 컨트롤 1024 - 계정 관리 \| 계정 모니터링/비정상적 사용](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84914fb4-12da-4c53-a341-a9fd463bed10) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1024.json) |
|[Microsoft 관리형 컨트롤 1025 - 계정 관리 \| 계정 모니터링/비정상적 사용](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fadfe020d-0a97-45f4-a39c-696ef99f3a95) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1025.json) |

### <a name="disable-accounts-for-high-risk-individuals"></a>위험 수준이 높은 개인 계정 사용 안 함

**ID**: NIST SP 800-53 Rev. 4 AC-2 (13)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1026 - 계정 관리 \| 위험도가 높은 개인 계정을 사용하지 않도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55419419-c597-4cd4-b51e-009fd2266783) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1026.json) |

### <a name="access-enforcement"></a>액세스 적용

**ID**: NIST SP 800-53 Rev. 4 AC-3

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[시스템 할당 관리 ID를 추가하여 ID가 없는 가상 머신에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |이 정책은 게스트 구성에서 지원되지만 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[시스템이 할당한 관리 ID를 추가하여 사용자가 할당한 ID가 있는 VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |이 정책은 게스트 구성에서 지원되고 사용자 할당 ID가 하나 이상 있지만 시스템 할당 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[SQL 서버에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |SQL 서버에 대한 Azure Active Directory 관리자 프로비전을 감사하여 Azure AD 인증을 활성화합니다. Azure AD 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[암호가 없는 계정이 있는 Linux 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6ec09a3-78bf-4f8f-99dc-6c77182d0f99) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 암호가 없는 계정이 있는 Linux 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword232_AINE.json) |
|[Linux 머신에 대한 인증에 SSH 키가 필요함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F630c64f9-8b6b-4c64-b511-6544ceff6fd6) |SSH 자체에서 암호화된 연결을 제공하지만 SSH와 함께 암호를 사용하면 VM은 여전히 무차별 암호 대입 공격에 취약합니다. SSH를 통해 Azure Linux 가상 머신에 인증하는 가장 안전한 옵션은 SSH 키라고도 하는 퍼블릭-프라이빗 키 쌍을 사용하는 것입니다. 자세한 정보: [https://docs.microsoft.com/azure/virtual-machines/linux/create-ssh-keys-detailed](../../../virtual-machines/linux/create-ssh-keys-detailed.md). |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxNoPasswordForSSH_AINE.json) |
|[Cognitive Services 계정은 로컬 인증 방법을 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |로컬 인증 방법을 사용하지 않도록 설정하면 Cognitive Services 계정에 인증 전용 Azure Active Directory ID가 필요하므로 보안이 강화됩니다. [https://aka.ms/cs/auth](../../../cognitive-services/authentication.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[Linux 게스트 구성 확장을 배포하여 Linux VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Linux 가상 머신에 Linux 게스트 구성 확장을 배포합니다. Linux 게스트 구성 확장은 모든 Linux 게스트 구성 할당의 필수 조건이며 Linux 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[API 앱에서 관리 ID를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |인증 보안 강화를 위해 관리 ID 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[함수 앱에서 관리 ID를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |인증 보안 강화를 위해 관리 ID 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[웹앱에서 관리 ID를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |인증 보안 강화를 위해 관리 ID 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |
|[구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |계정 또는 리소스 위반을 방지하려면 쓰기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |계정 또는 리소스 위반을 방지하려면 소유자 권한이 있는 모든 구독 계정에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |계정 또는 리소스 위반을 방지하려면 읽기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |
|[Microsoft 관리형 컨트롤 1027 - 액세스 적용](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa76ca9b0-3f4a-4192-9a38-b25e4f8ae48c) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1027.json) |
|[Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Service Fabric에서 Azure Active Directory를 통한 클라이언트 인증의 사용만 감사 |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|[스토리지 계정을 새 Azure Resource Manager 리소스로 마이그레이션해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37e0d2fe-28a5-43d6-a273-67d37d1f5606) |스토리지 계정에 새로운 Azure Resource Manager를 사용하여 더 강력한 액세스 제어(RBAC), 더 나은 감사, Azure Resource Manager 기반 배포 및 관리, 관리 ID 액세스, 비밀을 위해 Key Vault에 액세스, Azure AD 기반 인증, 보다 쉬운 보안 관리를 위한 태그 및 리소스 그룹 지원과 같은 보안 기능 향상을 제공합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Classic_AuditForClassicStorages_Audit.json) |
|[가상 머신을 새 Azure Resource Manager 리소스로 마이그레이션해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |가상 머신에 새 Azure Resource Manager를 사용하여 더 강력한 액세스 제어(RBAC), 더 나은 감사, Azure Resource Manager 기반 배포 및 관리, 관리 ID 액세스, 비밀을 위해 키 자격 증명 모음에 액세스, Azure AD 기반 인증, 보다 쉬운 보안 관리를 위한 태그 및 리소스 그룹 지원과 같은 보안 기능 향상을 제공합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="role-based-access-control"></a>역할 기반 액세스 제어

**ID**: NIST SP 800-53 Rev. 4 AC-3 (7)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kubernetes Services에서 RBAC(역할 기반 액세스 제어)를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |사용자가 수행할 수 있는 작업에 대한 세부적인 필터링을 제공하려면 RBAC(역할 기반 액세스 제어)를 사용하여 Kubernetes Service 클러스터에서 권한을 관리하고 관련 권한 부여 정책을 구성합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="information-flow-enforcement"></a>정보 흐름 적용

**ID**: NIST SP 800-53 Rev. 4 AC-4

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[인터넷 연결 가상 머신에 적응형 네트워크 강화 권장 사항을 적용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center는 인터넷 연결 가상 머신의 트래픽 패턴을 분석하고 잠재적 공격 노출 영역을 줄이는 네트워크 보안 그룹 규칙 권장 사항을 제공합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[모든 인터넷 트래픽은 배포된 Azure Firewall을 통해 라우팅되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure Security Center에서 일부 서브넷이 차세대 방화벽으로 보호되지 않는 것으로 확인되었습니다. Azure Firewall 또는 지원되는 차세대 방화벽으로 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[가상 머신과 연결된 네트워크 보안 그룹에서 모든 네트워크 포트를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center에서 네트워크 보안 그룹의 인바운드 규칙 중 일부가 너무 관대하다는 사실을 식별했습니다. 인바운드 규칙에서 '모두' 또는 '인터넷' 범위에서 들어오는 액세스를 허용해서는 안 됩니다. 그러면 리소스가 공격자의 표적이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[API Management 서비스에서 가상 네트워크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure Virtual Network 배포는 향상된 보안, 격리를 제공하며, 액세스를 제어하는 인터넷 라우팅이 불가능한 네트워크에 API Management 서비스를 배치할 수 있습니다. 그런 다음, 다양한 VPN 기술을 사용하여 이러한 네트워크를 온-프레미스 네트워크에 연결할 수 있으며, 이를 통해 네트워크 및/또는 온-프레미스 내에서 백 엔드 서비스에 액세스할 수 있습니다. 개발자 포털 및 API 게이트웨이를 인터넷에서 또는 가상 네트워크 내에서만 액세스할 수 있게 구성할 수 있습니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
|[App Configuration은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 앱 구성 인스턴스에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/appconfig/private-endpoint](../../../azure-app-configuration/concept-private-endpoint.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[권한 있는 IP 범위는 Kubernetes Services에 정의되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |특정 범위의 IP 주소에만 API 액세스 권한을 부여하여 Kubernetes Service Management API에 대한 액세스를 제한합니다. 허용된 네트워크의 애플리케이션만 클러스터에 액세스할 수 있도록 인증된 IP 범위에 대한 액세스를 제한하는 것이 좋습니다. |감사, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|[Azure API for FHIR은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR에는 승인된 프라이빗 엔드포인트 연결이 하나 이상 있어야 합니다. 가상 네트워크의 클라이언트는 프라이빗 링크를 통해 프라이빗 엔드포인트 연결이 있는 리소스에 안전하게 액세스할 수 있습니다. 자세한 내용은 [https://aka.ms/fhir-privatelink](../../../healthcare-apis/azure-api-for-fhir/configure-private-link.md)를 방문하세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Azure Cache for Redis는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |프라이빗 엔드포인트를 사용하면 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 엔드포인트를 Azure Cache for Redis 인스턴스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](../../../azure-cache-for-redis/cache-private-link.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Cognitive Search 서비스는 프라이빗 링크를 지원하는 SKU를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |Azure Cognitive Search의 지원되는 SKU를 통해 Azure Private Link를 사용하면 원본 또는 대상에서 공용 IP 주소 없이 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Search Service에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure Cognitive Search 서비스는 공용 네트워크 액세스를 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee980b6d-0eca-4501-8d54-f6290fd512c3) |공용 네트워크 액세스를 사용하지 않도록 설정하면 Azure Cognitive Search 서비스가 공용 인터넷에 노출되지 않도록 하여 보안이 향상됩니다. 프라이빗 엔드포인트를 만들면 Search Service의 노출을 제한할 수 있습니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePublicNetworkAccessDisabled_Deny.json) |
|[Azure Cognitive Search 서비스에서 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fda3595-9f2b-4592-8675-4231d6fa82fe) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Cognitive Search에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateEndpoints_Audit.json) |
|[Azure Cosmos DB 계정에 방화벽 규칙이 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |권한 없는 원본의 트래픽을 방지하기 위해 Azure Cosmos DB 계정에 방화벽 규칙을 정의해야 합니다. 가상 네트워크 필터를 사용하도록 정의된 IP 규칙이 하나 이상 있는 계정은 규정을 준수하는 것으로 간주됩니다. 퍼블릭 액세스를 비활성화한 계정도 규정을 준수하는 것으로 간주됩니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|[Azure Data Factory는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Data Factory에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/data-factory/data-factory-private-link](../../../data-factory/data-factory-private-link.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Azure Event Grid 도메인은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 Event Grid 도메인에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Domains_PrivateEndpoint_Audit.json) |
|[Azure Event Grid 토픽은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 Event Grid 토픽에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Topics_PrivateEndpoint_Audit.json) |
|[Azure 파일 동기화는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |표시된 Storage Sync Service 리소스의 프라이빗 엔드포인트를 만들면 인터넷에서 액세스할 수 있는 퍼블릭 엔드포인트를 사용하지 않고 조직 네트워크의 개인 IP 주소 공간 내에서 Storage Sync Service 리소스를 처리할 수 있습니다. 프라이빗 엔드포인트를 자체적으로 만든다고 해서 퍼블릭 엔드포인트가 비활성화되지 않습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Key Vault에서 공용 네트워크 액세스를 사용할 수 없음](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |공용 인터넷을 통해 액세스할 수 없도록 키 자격 증명 모음에 대한 공용 네트워크 액세스를 사용하지 않도록 설정합니다. 이를 통해 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/akvprivatelink](../../../key-vault/general/private-link-service.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|[Azure Machine Learning 작업 영역은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Machine Learning 작업 영역에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link](../../../machine-learning/how-to-configure-private-link.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateEndpoint_Audit.json) |
|[Azure Service Bus 네임스페이스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Service Bus 네임스페이스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../service-bus-messaging/private-link-service.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[Azure SignalR Service는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 전체 서비스가 아닌 Azure SignalR Service 리소스에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/asrs/privatelink](../../../azure-signalr/howto-private-endpoints.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure Synapse 작업 영역은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Synapse 작업 영역에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/synapse-analytics/security/how-to-connect-to-workspace-with-private-links](../../../synapse-analytics/security/how-to-connect-to-workspace-with-private-links.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Azure Web PubSub 서비스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |Azure Private Link를 사용하면 원본 또는 대상에 공용 IP 주소가 없어도 가상 네트워크를 Azure 서비스에 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Web PubSub 서비스에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[Cognitive Services 계정은 공용 네트워크 액세스를 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0725b4dd-7e76-479c-a735-68e7ee23d5ca) |공용 네트워크 액세스를 사용하지 않도록 설정하면 Cognitive Services 계정이 공용 인터넷에 노출되지 않도록 하여 보안이 향상됩니다. 프라이빗 엔드포인트를 만들면 Cognitive Services 계정의 노출을 제한할 수 있습니다. [https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)에서 자세히 알아보세요.  |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisablePublicNetworkAccess_Audit.json) |
|[Cognitive Services 계정은 네트워크 액세스를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F037eea7a-bd0a-46c5-9a66-03aea78705d3) |Cognitive Services 계정에 대한 네트워크 액세스는 제한되어야 합니다. 허용되는 네트워크의 애플리케이션만 Cognitive Services 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용하기 위해 특정 Azure 가상 네트워크에서의 트래픽 또는 공용 인터넷 IP 주소 범위에 액세스 권한을 부여할 수 있습니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_NetworkAcls_Audit.json) |
|[Cognitive Services에서 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01) |Azure Private Link를 사용하면 원본 또는 대상에 공용 IP 주소가 없어도 가상 네트워크를 Azure 서비스에 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Cognitive Services로 매핑하면 데이터 누출 가능성이 줄어듭니다. [https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_EnablePrivateEndpoints_Audit.json) |
|[컨테이너 레지스트리는 무제한 네트워크 액세스를 허용하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |기본적으로 Azure Container Registry는 모든 네트워크에 있는 호스트로부터의 인터넷 연결을 수락합니다. 잠재적 위협으로부터 레지스트리를 보호하려면 특정 공용 IP 주소 또는 주소 범위에서만 액세스를 허용합니다. 레지스트리에 IP/방화벽 규칙이나 구성된 가상 네트워크가 없는 경우 상태가 좋지 않은 리소스에 표시됩니다. Container Registry 네트워크 규칙에 대한 자세한 내용은 [https://aka.ms/acr/portal/public-network](../../../container-registry/container-registry-access-selected-networks.md) 및 [https://aka.ms/acr/vnet](../../../container-registry/container-registry-vnet.md)을 참조하세요. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_AuditDeny.json) |
|[컨테이너 레지스트리는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스 대신 프라이빗 엔드포인트를 컨테이너 레지스트리에 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/acr/private-link](../../../container-registry/container-registry-private-link.md)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용해서는 안 됩니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |CORS(교차 원본 리소스 공유)는 웹 애플리케이션에 액세스하는 모든 도메인을 허용하지 않아야 합니다. 필요한 도메인만 웹앱과 상호 작용할 수 있도록 허용합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[CosmosDB 계정은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58440f8a-10c5-4151-bdce-dfbaad4a20b7) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 CosmosDB 계정에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints](../../../cosmos-db/how-to-configure-private-endpoints.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_PrivateEndpoint_Audit.json) |
|[디스크 액세스 리소스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff39f5f49-4abf-44de-8c70-0756997bfb51) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 diskAccesses에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/disksprivatelinksdoc](../../../virtual-machines/disks-enable-private-links-for-import-export-portal.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 프라이빗 링크에 대해 자세히 알아보세요.  |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DiskAccesses_PrivateEndpoints_Audit.json) |
|[Event Hub 네임스페이스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb8564268-eb4a-4337-89be-a19db070c59d) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Event Hub 네임스페이스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/event-hubs/private-link-service](../../../event-hubs/private-link-service.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_PrivateEndpoint_Audit.json) |
|[네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |NSG(네트워크 보안 그룹)를 통해 가상 머신에 대한 액세스를 제한하여 잠재적인 위협으로부터 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 IoT Hub 디바이스 프로비저닝 서비스에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://aka.ms/iotdpsvnet](../../../iot-dps/virtual-network-support.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|[가상 머신에서 IP 전달을 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |가상 머신의 NIC에서 IP 전달을 사용하도록 설정하면 머신이 다른 대상으로 주소가 지정된 트래픽을 수신할 수 있습니다. IP 전달은 거의 필요하지 않으므로(예: VM을 네트워크 가상 어플라이언스로 사용하는 경우), 네트워크 보안 팀에서 검토해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |가능한 네트워크 JIT(Just In Time) 액세스는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[가상 머신에서 관리 포트를 닫아야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |열려 있는 원격 관리 포트는 위험도가 높은 인터넷 기반 공격에 VM을 노출시킵니다. 이러한 공격은 자격 증명을 무차별적으로 대입하여 머신에 대한 관리자 액세스 권한을 획득하려고 시도합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
|[Microsoft 관리형 컨트롤 1028 - 정보 흐름 적용](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff171df5c-921b-41e9-b12b-50801c315475) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1028.json) |
|[네트워크 보안 그룹을 사용하여 비인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb91dfba-c30d-4263-9add-9c2384e659a6) |NSG(네트워크 보안 그룹)를 통해 액세스를 제한하여 잠재적인 위협으로부터 비인터넷 연결 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternalVirtualMachines_Audit.json) |
|[Azure SQL Database에서 프라이빗 엔드포인트 연결을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7698e800-9299-47a6-b3b6-5a0fee576eed) |프라이빗 엔드포인트 연결은 Azure SQL Database에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. |감사, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PrivateEndpoint_Audit.json) |
|[Key Vault의 프라이빗 엔드포인트를 구성해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |프라이빗 링크는 공용 인터넷을 통해 트래픽을 보내지 않고 Key Vault를 Azure 리소스에 연결하는 방법을 제공합니다. 프라이빗 링크는 데이터 반출에 대한 심층 방어 기능을 제공합니다. |감사, 거부, 사용 안 함 |[1.1.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|[프라이빗 엔드포인트를 MariaDB 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |프라이빗 엔드포인트 연결은 Azure Database for MariaDB에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[프라이빗 엔드포인트를 MySQL 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |프라이빗 엔드포인트 연결은 Azure Database for MySQL에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[프라이빗 엔드포인트를 PostgreSQL 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |프라이빗 엔드포인트 연결은 Azure Database for PostgreSQL에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[Azure SQL Database에서 공용 네트워크 액세스를 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b8ca024-1d5c-4dec-8995-b1a932b41780) |공용 네트워크 액세스 속성을 사용하지 않도록 설정하면 프라이빗 엔드포인트에서만 Azure SQL Database에 액세스할 수 있도록 하여 보안이 향상됩니다. 이 구성은 IP 또는 가상 네트워크 기반 방화벽 규칙과 일치하는 모든 로그인을 거부합니다. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PublicNetworkAccess_Audit.json) |
|[MariaDB 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |공용 네트워크 액세스 속성을 사용하지 않도록 설정하여 보안을 강화하고 프라이빗 엔드포인트에서만 Azure Database for MariaDB에 액세스할 수 있도록 합니다. 이 구성은 Azure IP 범위를 벗어나는 공용 주소 공간의 액세스를 엄격하게 차단하고, IP 또는 가상 네트워크 기반 방화벽 규칙에 부합하는 모든 로그인을 거부합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
|[MySQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |공용 네트워크 액세스 속성을 사용하지 않도록 설정하여 보안을 강화하고 프라이빗 엔드포인트에서만 Azure Database for MySQL에 액세스할 수 있도록 합니다. 이 구성은 Azure IP 범위를 벗어나는 공용 주소 공간의 액세스를 엄격하게 차단하고, IP 또는 가상 네트워크 기반 방화벽 규칙에 부합하는 모든 로그인을 거부합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
|[PostgreSQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb52376f7-9612-48a1-81cd-1ffe4b61032c) |공용 네트워크 액세스 속성을 사용하지 않도록 설정하여 보안을 강화하고 프라이빗 엔드포인트에서만 Azure Database for PostgreSQL에 액세스할 수 있도록 합니다. 이 구성은 Azure IP 범위를 벗어나는 공용 주소 공간에서의 액세스를 사용하지 않도록 설정하고, IP 또는 가상 네트워크 기반 방화벽 규칙과 일치하는 모든 로그인을 거부합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_DisablePublicNetworkAccess_Audit.json) |
|[스토리지 계정 공용 액세스가 허용되지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Azure Storage의 컨테이너 및 BLOB에 대한 익명 공용 읽기 액세스는 데이터를 공유하는 편리한 방법이지만 보안 위험이 있을 수도 있습니다. 원치 않는 익명 액세스로 인해 발생하는 데이터 위반을 방지하기 위해 Microsoft는 시나리오에 필요한 경우가 아니면 스토리지 계정에 대한 공개 액세스를 방지하는 것이 좋습니다 |감사, 거부, 사용 안 함 |[3.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |
|[스토리지 계정은 네트워크 액세스를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |스토리지 계정에 대한 네트워크 액세스가 제한되어야 합니다. 허용되는 네트워크의 애플리케이션만 스토리지 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용하기 위해 특정 Azure 가상 네트워크 또는 공용 인터넷 IP 주소 범위의 트래픽에 대한 액세스 권한을 부여할 수 있습니다. |감사, 거부, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[스토리지 계정은 가상 네트워크 규칙을 사용하여 네트워크 액세스를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |IP 기반 필터링 대신 기본 방법으로 가상 네트워크 규칙을 사용하여 잠재적인 위협으로부터 스토리지 계정을 보호합니다. IP 기반 필터링을 사용하지 않도록 설정하면 공용 IP가 스토리지 계정에 액세스할 수 없습니다. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |
|[스토리지 계정은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6edd7eda-6dd8-40f7-810d-67160c639cd9) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 스토리지 계정에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://aka.ms/azureprivatelinkoverview](../../../private-link/private-link-overview.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountPrivateEndpointEnabled_Audit.json) |
|[서브넷을 네트워크 보안 그룹과 연결해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |NSG(네트워크 보안 그룹)를 통해 VM에 대한 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. NSG는 서브넷에 대한 네트워크 트래픽을 허용 또는 거부하는 ACL(액세스 제어 목록) 규칙의 목록을 포함합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[VM Image Builder 템플릿은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 VM Image Builder에 매핑하여 리소스를 구성하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함, 거부 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |

### <a name="dynamic-information-flow-control"></a>동적 정보 흐름 제어

**ID**: NIST SP 800-53 Rev. 4 AC-4 (3)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[인터넷 연결 가상 머신에 적응형 네트워크 강화 권장 사항을 적용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center는 인터넷 연결 가상 머신의 트래픽 패턴을 분석하고 잠재적 공격 노출 영역을 줄이는 네트워크 보안 그룹 규칙 권장 사항을 제공합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |가능한 네트워크 JIT(Just In Time) 액세스는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="security-policy-filters"></a>보안 정책 필터

**ID**: NIST SP 800-53 Rev. 4 AC-4 (8)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1029 - 정보 흐름 적용 \| 보안 정책 필터](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53ac8f8e-c2b5-4d44-8a2d-058e9ced9b69) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1029.json) |

### <a name="physical--logical-separation-of-information-flows"></a>정보 흐름의 물리적/논리적 분리

**ID**: NIST SP 800-53 Rev. 4 AC-4 (21)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1030 - 정보 흐름 적용 \| 정보 흐름의 물리적/논리적 분리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3531453-b869-4606-9122-29c1cd6e7ed1) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1030.json) |

### <a name="separation-of-duties"></a>의무 분리

**ID**: NIST SP 800-53 Rev. 4 AC-5

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1031 - 의무 분리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b93a801-fe25-4574-a60d-cb22acffae00) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1031.json) |
|[Microsoft 관리형 컨트롤 1032 - 의무 분리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5aa85661-d618-46b8-a20f-ca40a86f0751) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1032.json) |
|[Microsoft 관리형 컨트롤 1033 - 의무 분리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48540f01-fc11-411a-b160-42807c68896e) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1033.json) |
|[구독에 둘 이상의 소유자를 할당해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |관리자 액세스 중복성을 유지하려면 둘 이상의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="least-privilege"></a>최소 권한

**ID**: NIST SP 800-53 Rev. 4 AC-6

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 최대 3명의 소유자를 지정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |보안이 침해된 소유자의 위반 가능성을 줄이려면 최대 3명의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[사용자 지정 RBAC 규칙 사용 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |오류가 발생하기 쉬운 사용자 지정 RBAC 역할 대신 '소유자, 기여자, 읽기 권한자' 같은 기본 제공 역할을 감사합니다. 사용자 지정 역할 사용은 예외로 처리되며 엄격한 검토 및 위협 모델링이 필요합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Microsoft 관리형 컨트롤 1034 - 최소 권한](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02a5ed00-6d2e-4e97-9a98-46c32c057329) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1034.json) |

### <a name="authorize-access-to-security-functions"></a>보안 기능에 대한 액세스 권한 부여

**ID**: NIST SP 800-53 Rev. 4 AC-6 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1035 - 최소 권한 \| 보안 기능에 대한 액세스 권한 부여](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca94b046-45e2-444f-a862-dc8ce262a516) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1035.json) |

### <a name="non-privileged-access-for-nonsecurity-functions"></a>비보안 기능에 대한 권한 없는 액세스

**ID**: NIST SP 800-53 Rev. 4 AC-6 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1036 - 최소 권한 \| 비보안 기능에 대한 권한 없는 액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a16d673-8cf0-4dcf-b1d5-9b3e114fef71) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1036.json) |

### <a name="network-access-to-privileged-commands"></a>권한 있는 명령에 대한 네트워크 액세스

**ID**: NIST SP 800-53 Rev. 4 AC-6 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1037 - 최소 권한 \| 권한 있는 명령에 대한 네트워크 액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa4c2a3d-1294-41a3-9ada-0e540471e9fb) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1037.json) |

### <a name="privileged-accounts"></a>권한 있는 계정

**ID**: NIST SP 800-53 Rev. 4 AC-6 (5)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1038 - 최소 권한 \| 권한 있는 계정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26692e88-71b7-4a5f-a8ac-9f31dd05bd8e) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1038.json) |

### <a name="review-of-user-privileges"></a>사용자 권한 검토

**ID**: NIST SP 800-53 Rev. 4 AC-6 (7)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 최대 3명의 소유자를 지정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |보안이 침해된 소유자의 위반 가능성을 줄이려면 최대 3명의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |
|[사용자 지정 RBAC 규칙 사용 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |오류가 발생하기 쉬운 사용자 지정 RBAC 역할 대신 '소유자, 기여자, 읽기 권한자' 같은 기본 제공 역할을 감사합니다. 사용자 지정 역할 사용은 예외로 처리되며 엄격한 검토 및 위협 모델링이 필요합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Microsoft 관리형 컨트롤 1039 - 최소 권한 \| 사용자 권한 검토](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a7b9de4-a8a2-4672-914d-c5f6752aa7f9) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1039.json) |
|[Microsoft 관리형 컨트롤 1040 - 최소 권한 \| 사용자 권한 검토](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F54205576-cec9-463f-ba44-b4b3f5d0a84c) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1040.json) |

### <a name="privilege-levels-for-code-execution"></a>코드 실행 권한 수준

**ID**: NIST SP 800-53 Rev. 4 AC-6 (8)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1041 - 최소 권한 \| 코드 실행을 위한 권한 수준](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb3d8d15b-627a-4219-8c96-4d16f788888b) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1041.json) |

### <a name="auditing-use-of-privileged-functions"></a>권한 있는 기능 사용 감사

**ID**: NIST SP 800-53 Rev. 4 AC-6 (9)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1042 - 최소 권한 \| 권한 있는 기능 사용 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F319dc4f0-0fed-4ac9-8fc3-7aeddee82c07) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1042.json) |

### <a name="prohibit-non-privileged-users-from-executing-privileged-functions"></a>권한 없는 사용자가 권한 있는 기능을 실행하지 못하도록 방지

**ID**: NIST SP 800-53 Rev. 4 AC-6 (10)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1043 - 최소 권한 \| 권한 없는 사용자가 권한 있는 기능을 실행할 수 없도록 방지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F361a77f6-0f9c-4748-8eec-bc13aaaa2455) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1043.json) |

### <a name="unsuccessful-logon-attempts"></a>실패한 로그온 시도

**ID**: NIST SP 800-53 Rev. 4 AC-7

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1044 - 실패한 로그온 시도](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0abbac52-57cf-450d-8408-1208d0dd9e90) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1044.json) |
|[Microsoft 관리형 컨트롤 1045 - 실패한 로그온 시도](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F554d2dd6-f3a8-4ad5-b66f-5ce23bd18892) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1045.json) |

### <a name="purge--wipe-mobile-device"></a>모바일 디바이스 제거/초기화

**ID**: NIST SP 800-53 Rev. 4 AC-7 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1046 - 자동 계정 잠금 \| 모바일 디바이스 제거/초기화](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b1aa965-7502-41f9-92be-3e2fe7cc392a) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1046.json) |

### <a name="system-use-notification"></a>시스템 사용 알림

**ID**: NIST SP 800-53 Rev. 4 AC-8

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1047 - 시스템 사용 알림](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1ff6d62-a55c-41ab-90ba-90bb5b7b6f62) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1047.json) |
|[Microsoft 관리형 컨트롤 1048 - 시스템 사용 알림](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F483e7ca9-82b3-45a2-be97-b93163a0deb7) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1048.json) |
|[Microsoft 관리형 컨트롤 1049 - 시스템 사용 알림](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9adf7ba7-900a-4f35-8d57-9f34aafc405c) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1049.json) |

### <a name="concurrent-session-control"></a>동시 세션 제어

**ID**: NIST SP 800-53 Rev. 4 AC-10

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1050 - 동시 세션 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd20184c-b4ec-4ce5-8db6-6e86352d183f) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1050.json) |

### <a name="session-lock"></a>세션 잠금

**ID**: NIST SP 800-53 Rev. 4 AC-11

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1051 - 세션 잠금](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7cac6ee9-b58b-40c8-a5ce-f0efc3d9b339) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1051.json) |
|[Microsoft 관리형 컨트롤 1052 - 세션 잠금](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F027cae1c-ec3e-4492-9036-4168d540c42a) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1052.json) |

### <a name="pattern-hiding-displays"></a>패턴 숨기기 표시

**ID**: NIST SP 800-53 Rev. 4 AC-11 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1053 - 세션 잠금 \| 패턴 숨기기 표시](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7582b19c-9dba-438e-aed8-ede59ac35ba3) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1053.json) |

### <a name="session-termination"></a>세션 종료

**ID**: NIST SP 800-53 Rev. 4 AC-12

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1054 - 세션 종료](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5807e1b4-ba5e-4718-8689-a0ca05a191b2) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1054.json) |

### <a name="user-initiated-logouts--message-displays"></a>사용자가 시작한 로그아웃/메시지 표시

**ID**: NIST SP 800-53 Rev. 4 AC-12 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1055 - 세션 종료\| 사용자가 시작한 로그아웃/메시지 표시](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F769efd9b-3587-4e22-90ce-65ddcd5bd969) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1055.json) |
|[Microsoft 관리형 컨트롤 1056 - 세션 종료\| 사용자가 시작한 로그아웃/메시지 표시](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac43352f-df83-4694-8738-cfce549fd08d) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1056.json) |

### <a name="permitted-actions-without-identification-or-authentication"></a>ID 또는 인증 없이 허용되는 작업

**ID**: NIST SP 800-53 Rev. 4 AC-14

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1057 - ID 또는 인증 없이 허용되는 작업](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78255758-6d45-4bf0-a005-7016bc03b13c) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1057.json) |
|[Microsoft 관리형 컨트롤 1058 - ID 또는 인증 없이 허용되는 작업](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76e85d08-8fbb-4112-a1c1-93521e6a9254) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1058.json) |

### <a name="security-attributes"></a>보안 특성

**ID**: NIST SP 800-53 Rev. 4 AC-16

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[보호되지 않는 Azure SQL 서버에 대해 SQL용 Azure Defender를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |

### <a name="remote-access"></a>원격 액세스

**ID**: NIST SP 800-53 Rev. 4 AC-17

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[시스템 할당 관리 ID를 추가하여 ID가 없는 가상 머신에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |이 정책은 게스트 구성에서 지원되지만 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[시스템이 할당한 관리 ID를 추가하여 사용자가 할당한 ID가 있는 VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |이 정책은 게스트 구성에서 지원되고 사용자 할당 ID가 하나 이상 있지만 시스템 할당 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[App Configuration은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 앱 구성 인스턴스에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/appconfig/private-endpoint](../../../azure-app-configuration/concept-private-endpoint.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[암호 없이 계정에서 원격 연결을 허용하는 Linux 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea53dbee-c6c9-4f0e-9f9e-de0039b78023) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 암호 없이 계정에서 원격 연결을 허용하는 Linux 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword110_AINE.json) |
|[Azure API for FHIR은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR에는 승인된 프라이빗 엔드포인트 연결이 하나 이상 있어야 합니다. 가상 네트워크의 클라이언트는 프라이빗 링크를 통해 프라이빗 엔드포인트 연결이 있는 리소스에 안전하게 액세스할 수 있습니다. 자세한 내용은 [https://aka.ms/fhir-privatelink](../../../healthcare-apis/azure-api-for-fhir/configure-private-link.md)를 방문하세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Azure Cache for Redis는 가상 네트워크 내에 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Azure Virtual Network 배포는 Azure Cache for Redis 외에도 서브넷, 액세스 제어 정책 및 기타 기능에 대한 향상된 보안 및 격리를 제공하여 액세스를 추가로 제한합니다. Azure Cache for Redis 인스턴스가 가상 네트워크를 사용하여 구성되면 주소를 공개적으로 지정할 수 없으며 가상 네트워크 내의 가상 머신 및 애플리케이션에서만 액세스할 수 있습니다. |감사, 거부, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Azure Cache for Redis는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |프라이빗 엔드포인트를 사용하면 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 엔드포인트를 Azure Cache for Redis 인스턴스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](../../../azure-cache-for-redis/cache-private-link.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Cognitive Search 서비스는 프라이빗 링크를 지원하는 SKU를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |Azure Cognitive Search의 지원되는 SKU를 통해 Azure Private Link를 사용하면 원본 또는 대상에서 공용 IP 주소 없이 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Search Service에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure Cognitive Search 서비스에서 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fda3595-9f2b-4592-8675-4231d6fa82fe) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Cognitive Search에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateEndpoints_Audit.json) |
|[Azure Data Factory는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Data Factory에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/data-factory/data-factory-private-link](../../../data-factory/data-factory-private-link.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Azure Event Grid 도메인은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 Event Grid 도메인에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Domains_PrivateEndpoint_Audit.json) |
|[Azure Event Grid 토픽은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 Event Grid 토픽에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Topics_PrivateEndpoint_Audit.json) |
|[Azure 파일 동기화는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |표시된 Storage Sync Service 리소스의 프라이빗 엔드포인트를 만들면 인터넷에서 액세스할 수 있는 퍼블릭 엔드포인트를 사용하지 않고 조직 네트워크의 개인 IP 주소 공간 내에서 Storage Sync Service 리소스를 처리할 수 있습니다. 프라이빗 엔드포인트를 자체적으로 만든다고 해서 퍼블릭 엔드포인트가 비활성화되지 않습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Machine Learning 작업 영역은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Machine Learning 작업 영역에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link](../../../machine-learning/how-to-configure-private-link.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateEndpoint_Audit.json) |
|[Azure Service Bus 네임스페이스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Service Bus 네임스페이스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../service-bus-messaging/private-link-service.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[Azure SignalR Service는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 전체 서비스가 아닌 Azure SignalR Service 리소스에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/asrs/privatelink](../../../azure-signalr/howto-private-endpoints.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure Spring Cloud는 네트워크 주입을 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure Spring Cloud 인스턴스는 다음과 같은 용도로 가상 네트워크 주입을 사용해야 합니다. 1. Azure Spring Cloud를 인터넷에서 격리합니다. 2. Azure Spring Cloud를 사용하여 온-프레미스 데이터 센터의 시스템 또는 다른 가상 네트워크의 Azure 서비스와 상호 작용할 수 있습니다. 3. 고객이 Azure Spring Cloud에 대한 인바운드 및 아웃바운드 네트워크 통신을 제어할 수 있습니다. |감사, 사용 안 함, 거부 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
|[Azure Synapse 작업 영역은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Synapse 작업 영역에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/synapse-analytics/security/how-to-connect-to-workspace-with-private-links](../../../synapse-analytics/security/how-to-connect-to-workspace-with-private-links.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Azure Web PubSub 서비스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |Azure Private Link를 사용하면 원본 또는 대상에 공용 IP 주소가 없어도 가상 네트워크를 Azure 서비스에 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Web PubSub 서비스에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[Cognitive Services에서 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01) |Azure Private Link를 사용하면 원본 또는 대상에 공용 IP 주소가 없어도 가상 네트워크를 Azure 서비스에 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Cognitive Services로 매핑하면 데이터 누출 가능성이 줄어듭니다. [https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_EnablePrivateEndpoints_Audit.json) |
|[컨테이너 레지스트리는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스 대신 프라이빗 엔드포인트를 컨테이너 레지스트리에 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/acr/private-link](../../../container-registry/container-registry-private-link.md)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[CosmosDB 계정은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58440f8a-10c5-4151-bdce-dfbaad4a20b7) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 CosmosDB 계정에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints](../../../cosmos-db/how-to-configure-private-endpoints.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_PrivateEndpoint_Audit.json) |
|[Linux 게스트 구성 확장을 배포하여 Linux VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Linux 가상 머신에 Linux 게스트 구성 확장을 배포합니다. Linux 게스트 구성 확장은 모든 Linux 게스트 구성 할당의 필수 조건이며 Linux 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Windows 게스트 구성 확장을 배포하여 Windows VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Windows 가상 머신에 Windows 게스트 구성 확장을 배포합니다. Windows 게스트 구성 확장은 모든 Windows 게스트 구성 할당의 필수 조건이며 Windows 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[디스크 액세스 리소스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff39f5f49-4abf-44de-8c70-0756997bfb51) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 diskAccesses에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/disksprivatelinksdoc](../../../virtual-machines/disks-enable-private-links-for-import-export-portal.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 프라이빗 링크에 대해 자세히 알아보세요.  |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DiskAccesses_PrivateEndpoints_Audit.json) |
|[Event Hub 네임스페이스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb8564268-eb4a-4337-89be-a19db070c59d) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Event Hub 네임스페이스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/event-hubs/private-link-service](../../../event-hubs/private-link-service.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_PrivateEndpoint_Audit.json) |
|[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 IoT Hub 디바이스 프로비저닝 서비스에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://aka.ms/iotdpsvnet](../../../iot-dps/virtual-network-support.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|[Microsoft 관리형 컨트롤 1059 - 원격 액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa29b5d9f-4953-4afe-b560-203a6410b6b4) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1059.json) |
|[Microsoft 관리형 컨트롤 1060 - 원격 액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a987fd-2003-45de-a120-014956581f2b) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1060.json) |
|[Azure SQL Database에서 프라이빗 엔드포인트 연결을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7698e800-9299-47a6-b3b6-5a0fee576eed) |프라이빗 엔드포인트 연결은 Azure SQL Database에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. |감사, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PrivateEndpoint_Audit.json) |
|[Key Vault의 프라이빗 엔드포인트를 구성해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |프라이빗 링크는 공용 인터넷을 통해 트래픽을 보내지 않고 Key Vault를 Azure 리소스에 연결하는 방법을 제공합니다. 프라이빗 링크는 데이터 반출에 대한 심층 방어 기능을 제공합니다. |감사, 거부, 사용 안 함 |[1.1.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|[프라이빗 엔드포인트를 MariaDB 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |프라이빗 엔드포인트 연결은 Azure Database for MariaDB에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[프라이빗 엔드포인트를 MySQL 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |프라이빗 엔드포인트 연결은 Azure Database for MySQL에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[프라이빗 엔드포인트를 PostgreSQL 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |프라이빗 엔드포인트 연결은 Azure Database for PostgreSQL에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[API Apps에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |원격 디버깅에 API 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[함수 앱에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |원격 디버깅에 함수 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |원격 디버깅에 웹 애플리케이션에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[스토리지 계정은 네트워크 액세스를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |스토리지 계정에 대한 네트워크 액세스가 제한되어야 합니다. 허용되는 네트워크의 애플리케이션만 스토리지 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용하기 위해 특정 Azure 가상 네트워크 또는 공용 인터넷 IP 주소 범위의 트래픽에 대한 액세스 권한을 부여할 수 있습니다. |감사, 거부, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[스토리지 계정은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6edd7eda-6dd8-40f7-810d-67160c639cd9) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 스토리지 계정에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://aka.ms/azureprivatelinkoverview](../../../private-link/private-link-overview.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountPrivateEndpointEnabled_Audit.json) |
|[VM Image Builder 템플릿은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 VM Image Builder에 매핑하여 리소스를 구성하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함, 거부 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |

### <a name="automated-monitoring--control"></a>자동화된 모니터링/제어

**ID**: NIST SP 800-53 Rev. 4 AC-17 (1)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[시스템 할당 관리 ID를 추가하여 ID가 없는 가상 머신에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |이 정책은 게스트 구성에서 지원되지만 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[시스템이 할당한 관리 ID를 추가하여 사용자가 할당한 ID가 있는 VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |이 정책은 게스트 구성에서 지원되고 사용자 할당 ID가 하나 이상 있지만 시스템 할당 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[App Configuration은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 앱 구성 인스턴스에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/appconfig/private-endpoint](../../../azure-app-configuration/concept-private-endpoint.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[암호 없이 계정에서 원격 연결을 허용하는 Linux 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea53dbee-c6c9-4f0e-9f9e-de0039b78023) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 암호 없이 계정에서 원격 연결을 허용하는 Linux 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword110_AINE.json) |
|[Azure API for FHIR은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR에는 승인된 프라이빗 엔드포인트 연결이 하나 이상 있어야 합니다. 가상 네트워크의 클라이언트는 프라이빗 링크를 통해 프라이빗 엔드포인트 연결이 있는 리소스에 안전하게 액세스할 수 있습니다. 자세한 내용은 [https://aka.ms/fhir-privatelink](../../../healthcare-apis/azure-api-for-fhir/configure-private-link.md)를 방문하세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Azure Cache for Redis는 가상 네트워크 내에 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |Azure Virtual Network 배포는 Azure Cache for Redis 외에도 서브넷, 액세스 제어 정책 및 기타 기능에 대한 향상된 보안 및 격리를 제공하여 액세스를 추가로 제한합니다. Azure Cache for Redis 인스턴스가 가상 네트워크를 사용하여 구성되면 주소를 공개적으로 지정할 수 없으며 가상 네트워크 내의 가상 머신 및 애플리케이션에서만 액세스할 수 있습니다. |감사, 거부, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Azure Cache for Redis는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |프라이빗 엔드포인트를 사용하면 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 엔드포인트를 Azure Cache for Redis 인스턴스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](../../../azure-cache-for-redis/cache-private-link.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Cognitive Search 서비스는 프라이빗 링크를 지원하는 SKU를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |Azure Cognitive Search의 지원되는 SKU를 통해 Azure Private Link를 사용하면 원본 또는 대상에서 공용 IP 주소 없이 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Search Service에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure Cognitive Search 서비스에서 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fda3595-9f2b-4592-8675-4231d6fa82fe) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Cognitive Search에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateEndpoints_Audit.json) |
|[Azure Data Factory는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Data Factory에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/data-factory/data-factory-private-link](../../../data-factory/data-factory-private-link.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Azure Event Grid 도메인은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 Event Grid 도메인에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Domains_PrivateEndpoint_Audit.json) |
|[Azure Event Grid 토픽은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 Event Grid 토픽에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Topics_PrivateEndpoint_Audit.json) |
|[Azure 파일 동기화는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |표시된 Storage Sync Service 리소스의 프라이빗 엔드포인트를 만들면 인터넷에서 액세스할 수 있는 퍼블릭 엔드포인트를 사용하지 않고 조직 네트워크의 개인 IP 주소 공간 내에서 Storage Sync Service 리소스를 처리할 수 있습니다. 프라이빗 엔드포인트를 자체적으로 만든다고 해서 퍼블릭 엔드포인트가 비활성화되지 않습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Machine Learning 작업 영역은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Machine Learning 작업 영역에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link](../../../machine-learning/how-to-configure-private-link.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateEndpoint_Audit.json) |
|[Azure Service Bus 네임스페이스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Service Bus 네임스페이스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../service-bus-messaging/private-link-service.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[Azure SignalR Service는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 전체 서비스가 아닌 Azure SignalR Service 리소스에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/asrs/privatelink](../../../azure-signalr/howto-private-endpoints.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure Spring Cloud는 네트워크 주입을 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure Spring Cloud 인스턴스는 다음과 같은 용도로 가상 네트워크 주입을 사용해야 합니다. 1. Azure Spring Cloud를 인터넷에서 격리합니다. 2. Azure Spring Cloud를 사용하여 온-프레미스 데이터 센터의 시스템 또는 다른 가상 네트워크의 Azure 서비스와 상호 작용할 수 있습니다. 3. 고객이 Azure Spring Cloud에 대한 인바운드 및 아웃바운드 네트워크 통신을 제어할 수 있습니다. |감사, 사용 안 함, 거부 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
|[Azure Synapse 작업 영역은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Synapse 작업 영역에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/synapse-analytics/security/how-to-connect-to-workspace-with-private-links](../../../synapse-analytics/security/how-to-connect-to-workspace-with-private-links.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Azure Web PubSub 서비스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |Azure Private Link를 사용하면 원본 또는 대상에 공용 IP 주소가 없어도 가상 네트워크를 Azure 서비스에 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Web PubSub 서비스에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[Cognitive Services에서 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01) |Azure Private Link를 사용하면 원본 또는 대상에 공용 IP 주소가 없어도 가상 네트워크를 Azure 서비스에 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Cognitive Services로 매핑하면 데이터 누출 가능성이 줄어듭니다. [https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_EnablePrivateEndpoints_Audit.json) |
|[컨테이너 레지스트리는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스 대신 프라이빗 엔드포인트를 컨테이너 레지스트리에 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/acr/private-link](../../../container-registry/container-registry-private-link.md)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[CosmosDB 계정은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58440f8a-10c5-4151-bdce-dfbaad4a20b7) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 CosmosDB 계정에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints](../../../cosmos-db/how-to-configure-private-endpoints.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_PrivateEndpoint_Audit.json) |
|[Linux 게스트 구성 확장을 배포하여 Linux VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Linux 가상 머신에 Linux 게스트 구성 확장을 배포합니다. Linux 게스트 구성 확장은 모든 Linux 게스트 구성 할당의 필수 조건이며 Linux 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Windows 게스트 구성 확장을 배포하여 Windows VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Windows 가상 머신에 Windows 게스트 구성 확장을 배포합니다. Windows 게스트 구성 확장은 모든 Windows 게스트 구성 할당의 필수 조건이며 Windows 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[디스크 액세스 리소스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff39f5f49-4abf-44de-8c70-0756997bfb51) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 diskAccesses에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/disksprivatelinksdoc](../../../virtual-machines/disks-enable-private-links-for-import-export-portal.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 프라이빗 링크에 대해 자세히 알아보세요.  |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DiskAccesses_PrivateEndpoints_Audit.json) |
|[Event Hub 네임스페이스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb8564268-eb4a-4337-89be-a19db070c59d) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Event Hub 네임스페이스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/event-hubs/private-link-service](../../../event-hubs/private-link-service.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_PrivateEndpoint_Audit.json) |
|[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 IoT Hub 디바이스 프로비저닝 서비스에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://aka.ms/iotdpsvnet](../../../iot-dps/virtual-network-support.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|[Microsoft 관리형 컨트롤 1061 - 원격 액세스 \| 자동화된 모니터링/제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ac22808-a2e8-41c4-9d46-429b50738914) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1061.json) |
|[Azure SQL Database에서 프라이빗 엔드포인트 연결을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7698e800-9299-47a6-b3b6-5a0fee576eed) |프라이빗 엔드포인트 연결은 Azure SQL Database에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. |감사, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PrivateEndpoint_Audit.json) |
|[Key Vault의 프라이빗 엔드포인트를 구성해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |프라이빗 링크는 공용 인터넷을 통해 트래픽을 보내지 않고 Key Vault를 Azure 리소스에 연결하는 방법을 제공합니다. 프라이빗 링크는 데이터 반출에 대한 심층 방어 기능을 제공합니다. |감사, 거부, 사용 안 함 |[1.1.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|[프라이빗 엔드포인트를 MariaDB 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |프라이빗 엔드포인트 연결은 Azure Database for MariaDB에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[프라이빗 엔드포인트를 MySQL 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |프라이빗 엔드포인트 연결은 Azure Database for MySQL에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[프라이빗 엔드포인트를 PostgreSQL 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |프라이빗 엔드포인트 연결은 Azure Database for PostgreSQL에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[API Apps에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |원격 디버깅에 API 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[함수 앱에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |원격 디버깅에 함수 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |원격 디버깅에 웹 애플리케이션에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[스토리지 계정은 네트워크 액세스를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |스토리지 계정에 대한 네트워크 액세스가 제한되어야 합니다. 허용되는 네트워크의 애플리케이션만 스토리지 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용하기 위해 특정 Azure 가상 네트워크 또는 공용 인터넷 IP 주소 범위의 트래픽에 대한 액세스 권한을 부여할 수 있습니다. |감사, 거부, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[스토리지 계정은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6edd7eda-6dd8-40f7-810d-67160c639cd9) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 스토리지 계정에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://aka.ms/azureprivatelinkoverview](../../../private-link/private-link-overview.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountPrivateEndpointEnabled_Audit.json) |
|[VM Image Builder 템플릿은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 VM Image Builder에 매핑하여 리소스를 구성하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함, 거부 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |

### <a name="protection-of-confidentiality--integrity-using-encryption"></a>암호화를 사용하여 기밀성/무결성 보호

**ID**: NIST SP 800-53 Rev. 4 AC-17 (2)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1062 - 원격 액세스 \| 암호화를 사용하여 기밀성/무결성 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4708723f-e099-4af1-bbf9-b6df7642e444) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1062.json) |

### <a name="managed-access-control-points"></a>관리형 액세스 제어 지점

**ID**: NIST SP 800-53 Rev. 4 AC-17 (3)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1063 - 원격 액세스 \| 관리형 액세스 제어 지점](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F593ce201-54b2-4dd0-b34f-c308005d7780) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1063.json) |

### <a name="privileged-commands--access"></a>권한 있는 명령/액세스

**ID**: NIST SP 800-53 Rev. 4 AC-17 (4)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1064 - 원격 액세스 \| 권한 있는 명령/액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb4d9508-cbf0-4a3c-bb5c-6c95b159f3fb) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1064.json) |
|[Microsoft 관리형 컨트롤 1065 - 원격 액세스 \| 권한 있는 명령/액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff87b8085-dca9-4cf1-8f7b-9822b997797c) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1065.json) |

### <a name="disconnect--disable-access"></a>액세스 연결 끊기/사용 안 함

**ID**: NIST SP 800-53 Rev. 4 AC-17 (9)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1066 - 원격 액세스 \| 액세스 연결 끊기/사용 안 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4455c2e8-c65d-4acf-895e-304916f90b36) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1066.json) |

### <a name="wireless-access"></a>무선 액세스

**ID**: NIST SP 800-53 Rev. 4 AC-18

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1067 - 무선 액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5e54f6-0127-44d0-8b61-f31dc8dd6190) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1067.json) |
|[Microsoft 관리형 컨트롤 1068 - 무선 액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d045bca-a0fd-452e-9f41-4ec33769717c) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1068.json) |

### <a name="authentication-and-encryption"></a>인증 및 암호화

**ID**: NIST SP 800-53 Rev. 4 AC-18 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1069 - 무선 액세스 \| 인증 및 암호화](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F91c97b44-791e-46e9-bad7-ab7c4949edbb) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1069.json) |

### <a name="disable-wireless-networking"></a>무선 네트워킹 사용 안 함

**ID**: NIST SP 800-53 Rev. 4 AC-18 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1070 - 무선 액세스 \| 무선 네트워킹 사용 안 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68f837d0-8942-4b1e-9b31-be78b247bda8) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1070.json) |

### <a name="restrict-configurations-by-users"></a>사용자의 구성 제한

**ID**: NIST SP 800-53 Rev. 4 AC-18 (4)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1071 - 무선 액세스 \| 사용자의 구성 제한](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a437f5b-9ad6-4f28-8861-de404d511ae4) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1071.json) |

### <a name="antennas--transmission-power-levels"></a>안테나/전송 전원 수준

**ID**: NIST SP 800-53 Rev. 4 AC-18 (5)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1072 - 무선 액세스 \| 안테나/송전 수준](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ca29e41-34ec-4e70-aba9-6248aca18c31) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1072.json) |

### <a name="access-control-for-mobile-devices"></a>모바일 디바이스에 대한 액세스 제어

**ID**: NIST SP 800-53 Rev. 4 AC-19

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1073 - 모바일 디바이스에 대한 액세스 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fab55cdb0-c7dd-4bd8-ae22-a7cea7594e9c) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1073.json) |
|[Microsoft 관리형 컨트롤 1074 - 모바일 디바이스에 대한 액세스 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F27a69937-af92-4198-9b86-08d355c7e59a) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1074.json) |

### <a name="full-device--container-based-encryption"></a>디바이스/컨테이너 기반 전체 암호화

**ID**: NIST SP 800-53 Rev. 4 AC-19 (5)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1075 - 모바일 디바이스에 대한 액세스 제어 \| 완전한 디바이스/컨테이너 기반 암호화](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc933d22-04df-48ed-8f87-22a3773d4309) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1075.json) |

### <a name="use-of-external-information-systems"></a>외부 정보 시스템의 사용

**ID**: NIST SP 800-53 Rev. 4 AC-20

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1076 - 외부 정보 시스템 사용](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98a4bd5f-6436-46d4-ad00-930b5b1dfed4) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1076.json) |
|[Microsoft 관리형 컨트롤 1077 - 외부 정보 시스템 사용](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2dad3668-797a-412e-a798-07d3849a7a79) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1077.json) |

### <a name="limits-on-authorized-use"></a>권한 있는 사용에 대한 제한

**ID**: NIST SP 800-53 Rev. 4 AC-20 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1078 - 외부 정보 시스템 사용 \| 권한 있는 사용에 대한 제한](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb25faf85-8a16-4f28-8e15-d05c0072d64d) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1078.json) |
|[Microsoft 관리형 컨트롤 1079 - 외부 정보 시스템 사용 \| 권한 있는 사용에 대한 제한](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F85c32733-7d23-4948-88da-058e2c56b60f) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1079.json) |

### <a name="portable-storage-devices"></a>휴대용 스토리지 디바이스

**ID**: NIST SP 800-53 Rev. 4 AC-20 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1080 - 외부 정보 시스템 사용 \| 휴대용 스토리지 디바이스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F852981b4-a380-4704-aa1e-2e52d63445e5) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1080.json) |

### <a name="information-sharing"></a>정보 공유

**ID**: NIST SP 800-53 Rev. 4 AC-21

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1081 - 정보 공유](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3867f2a9-23bb-4729-851f-c3ad98580caf) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1081.json) |
|[Microsoft 관리형 컨트롤 1082 - 정보 공유](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F24d480ef-11a0-4b1b-8e70-4e023bf2be23) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1082.json) |

### <a name="publicly-accessible-content"></a>공개적으로 액세스할 수 있는 콘텐츠

**ID**: NIST SP 800-53 Rev. 4 AC-22

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1083 - 공개적으로 액세스할 수 있는 콘텐츠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e319cb6-2ca3-4a58-ad75-e67f484e50ec) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1083.json) |
|[Microsoft 관리형 컨트롤 1084 - 공개적으로 액세스할 수 있는 콘텐츠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0eb15db-dd1c-4d1d-b200-b12dd6cd060c) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1084.json) |
|[Microsoft 관리형 컨트롤 1085 - 공개적으로 액세스할 수 있는 콘텐츠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d117e0-38b0-4bbb-aaab-563be5dd10ba) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1085.json) |
|[Microsoft 관리형 컨트롤 1086 - 공개적으로 액세스할 수 있는 콘텐츠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb321e6f-16a0-4be3-878f-500956e309c5) |Microsoft에서 구현하는 액세스 제어 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1086.json) |

## <a name="awareness-and-training"></a>인식 및 교육

### <a name="security-awareness-and-training-policy-and-procedures"></a>보안 인식과 교육 정책 및 절차

**ID**: NIST SP 800-53 Rev. 4 AT-1

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1087 - 보안 인식과 교육 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F100c82ba-42e9-4d44-a2ba-94b209248583) |Microsoft에서 구현하는 인식 및 교육 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1087.json) |
|[Microsoft 관리형 컨트롤 1088 - 보안 인식과 교육 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d50f99d-1356-49c0-934a-45f742ba7783) |Microsoft에서 구현하는 인식 및 교육 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1088.json) |

### <a name="security-awareness-training"></a>보안 인식 교육

**ID**: NIST SP 800-53 Rev. 4 AT-2

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1089 - 보안 인식 교육](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef080e67-0d1a-4f76-a0c5-fb9b0358485e) |Microsoft에서 구현하는 인식 및 교육 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1089.json) |
|[Microsoft 관리형 컨트롤 1090 - 보안 인식 교육](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fb740e5-cbc7-4d10-8686-d1bf826652b1) |Microsoft에서 구현하는 인식 및 교육 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1090.json) |
|[Microsoft 관리형 컨트롤 1091 - 보안 인식 교육](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb23bd715-5d1c-4e5c-9759-9cbdf79ded9d) |Microsoft에서 구현하는 인식 및 교육 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1091.json) |

### <a name="insider-threat"></a>내부자 위협

**ID**: NIST SP 800-53 Rev. 4 AT-2 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1092 - 보안 인식 교육 \| 내부자 위협](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8a29d47b-8604-4667-84ef-90d203fcb305) |Microsoft에서 구현하는 인식 및 교육 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1092.json) |

### <a name="role-based-security-training"></a>역할 기반 보안 교육

**ID**: NIST SP 800-53 Rev. 4 AT-3

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1093 - 역할 기반 보안 교육](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a0bdeeb-15f4-47e8-a1da-9f769f845fdf) |Microsoft에서 구현하는 인식 및 교육 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1093.json) |
|[Microsoft 관리형 컨트롤 1094 - 역할 기반 보안 교육](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b1853e0-8973-446b-b567-09d901d31a09) |Microsoft에서 구현하는 인식 및 교육 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1094.json) |
|[Microsoft 관리형 컨트롤 1095 - 역할 기반 보안 교육](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc3f6f7a-057b-433e-9834-e8c97b0194f6) |Microsoft에서 구현하는 인식 및 교육 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1095.json) |

### <a name="practical-exercises"></a>실습

**ID**: NIST SP 800-53 Rev. 4 AT-3 (3)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1096 - 역할 기반 보안 교육 \| 연습](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F420c1477-aa43-49d0-bd7e-c4abdd9addff) |Microsoft에서 구현하는 인식 및 교육 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1096.json) |

### <a name="suspicious-communications-and-anomalous-system-behavior"></a>의심스러운 통신 및 비정상 시스템 동작

**ID**: NIST SP 800-53 Rev. 4 AT-3 (4)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1097 - 역할 기반 보안 교육 \| 의심스러운 통신 및 비정상적인 시스템 동작](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3e4836-f19e-47eb-a8cd-c3ca150452c0) |Microsoft에서 구현하는 인식 및 교육 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1097.json) |

### <a name="security-training-records"></a>보안 교육 기록

**ID**: NIST SP 800-53 Rev. 4 AT-4

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1098 - 보안 교육 기록](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84363adb-dde3-411a-9fc1-36b56737f822) |Microsoft에서 구현하는 인식 및 교육 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1098.json) |
|[Microsoft 관리형 컨트롤 1099 - 보안 교육 기록](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01910bab-8639-4bd0-84ef-cc53b24d79ba) |Microsoft에서 구현하는 인식 및 교육 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1099.json) |

## <a name="audit-and-accountability"></a>감사 및 책임

### <a name="audit-and-accountability-policy-and-procedures"></a>감사 및 책임 정책 및 절차

**ID**: NIST SP 800-53 Rev. 4 AU-1

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1100 - 감사 및 책임 정책과 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4057863c-ca7d-47eb-b1e0-503580cba8a4) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1100.json) |
|[Microsoft 관리형 컨트롤 1101 - 감사 및 책임 정책과 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7327b708-f0e0-457d-9d2a-527fcc9c9a65) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1101.json) |

### <a name="audit-events"></a>감사 이벤트

**ID**: NIST SP 800-53 Rev. 4 AU-2

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1102 - 감사 이벤트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9943c16a-c54c-4b4a-ad28-bfd938cdbf57) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1102.json) |
|[Microsoft 관리형 컨트롤 1103 - 감사 이벤트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16feeb31-6377-437e-bbab-d7f73911896d) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1103.json) |
|[Microsoft 관리형 컨트롤 1104 - 감사 이벤트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcdd8d244-18b2-4306-a1d1-df175ae0935f) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1104.json) |
|[Microsoft 관리형 컨트롤 1105 - 감사 이벤트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b73f57b-587d-4470-a344-0b0ae805f459) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1105.json) |

### <a name="reviews-and-updates"></a>검토 및 업데이트

**ID**: NIST SP 800-53 Rev. 4 AU-2 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1106 - 감사 이벤트 \| 검토 및 업데이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd2b4feae-61ab-423f-a4c5-0e38ac4464d8) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1106.json) |

### <a name="content-of-audit-records"></a>감사 레코드의 콘텐츠

**ID**: NIST SP 800-53 Rev. 4 AU-3

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1107 - 감사 레코드의 콘텐츠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb29ed931-8e21-4779-8458-27916122a904) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1107.json) |

### <a name="additional-audit-information"></a>추가 감사 정보

**ID**: NIST SP 800-53 Rev. 4 AU-3 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1108 - 감사 레코드의 콘텐츠 \| 추가 감사 정보](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9ad559e-c12d-415e-9a78-e50fdd7da7ba) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1108.json) |

### <a name="centralized-management-of-planned-audit-record-content"></a>계획된 감사 레코드 콘텐츠의 중앙 집중식 관리

**ID**: NIST SP 800-53 Rev. 4 AU-3 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1109 - 감사 레코드의 콘텐츠 \| 계획된 감사 레코드 콘텐츠의 중앙 집중식 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d9ffa23-ad92-4d0d-b1f4-7db274cc2aec) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1109.json) |

### <a name="audit-storage-capacity"></a>감사 스토리지 용량

**ID**: NIST SP 800-53 Rev. 4 AU-4

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1110 - 감사 스토리지 용량](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6182bfa7-0f2a-43f5-834a-a2ddf31c13c7) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1110.json) |

### <a name="response-to-audit-processing-failures"></a>감사 처리 실패에 대한 응답

**ID**: NIST SP 800-53 Rev. 4 AU-5

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1111 - 감사 처리 실패에 대한 응답](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21de687c-f15e-4e51-bf8d-f35c8619965b) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1111.json) |
|[Microsoft 관리형 컨트롤 1112 - 감사 처리 실패에 대한 응답](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd530aad8-4ee2-45f4-b234-c061dae683c0) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1112.json) |

### <a name="audit-storage-capacity"></a>감사 스토리지 용량

**ID**: NIST SP 800-53 Rev. 4 AU-5 (1)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1113 - 감사 처리 실패에 대한 응답 \| 감사 스토리지 용량](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F562afd61-56be-4313-8fe4-b9564aa4ba7d) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1113.json) |

### <a name="real-time-alerts"></a>실시간 경고

**ID**: NIST SP 800-53 Rev. 4 AU-5 (2)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1114 - 감사 처리 실패에 대한 응답 \| 실시간 경고](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c090801-59bc-4454-bb33-e0455133486a) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1114.json) |

### <a name="audit-review-analysis-and-reporting"></a>감사 검토, 분석 및 보고

**ID**: NIST SP 800-53 Rev. 4 AU-6

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Arc 사용 Kubernetes 클러스터에 Azure Defender의 확장이 설치되어 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Arc용 Azure Defender 확장은 Arc 지원 Kubernetes 클러스터에 대한 위협 보호를 제공합니다. 이 확장은 클러스터의 노드에서 데이터를 수집하고 추가 분석을 위해 클라우드의 Azure Defender for Kubernetes 백 엔드로 보냅니다. [https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[Azure Defender for App Service를 사용해야 합니다](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service는 클라우드의 규모와 Azure가 클라우드 공급자로서 가지고 있는 가시성을 활용하여 일반적인 웹앱 공격을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure SQL Database 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[컨테이너 레지스트리용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |컨테이너 레지스트리용 Azure Defender는 지난 30일 내에 끌어와서 레지스트리에 푸시한 이미지의 취약성을 검사하고, 각 이미지에 대해 발견한 자세한 내용을 공개합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS는 Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링하여 클라우드 리소스에 대한 추가 보호 계층을 제공합니다. Azure Defender는 DNS 계층에서 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)에서 Azure Defender for DNS의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault는 키 자격 증명 모음 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하여 보안 인텔리전스에 추가 보호 계층을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager는 조직의 리소스 관리 작업을 자동으로 모니터링합니다. Azure Defender는 위협을 감지하고 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)에서 Azure Defender for Resource Manager의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[머신의 SQL 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[보호되지 않는 Azure SQL 서버에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[스토리지용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[Microsoft 관리형 컨트롤 1115 - 감사 검토, 분석 및 보고](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b653845-2ad9-4e09-a4f3-5a7c1d78353d) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1115.json) |
|[Microsoft 관리형 컨트롤 1116 - 감사 검토, 분석 및 보고](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e47bc51-35d1-44b8-92af-e2f2d8b67635) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1116.json) |
|[Linux 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[Windows 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Network Watcher를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. 시나리오 수준 모니터링을 사용하면 종단 간 네트워크 수준 보기에서 문제를 진단할 수 있습니다. 가상 네트워크가 있는 모든 지역에서 Network Watcher 리소스 그룹을 만들어야 합니다. 특정 지역에서 Network Watcher 리소스 그룹을 사용할 수 없는 경우 경고가 활성화됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="process-integration"></a>프로세스 통합

**ID**: NIST SP 800-53 Rev. 4 AU-6 (1)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1117 - 감사 검토, 분석 및 보고 \| 프로세스 통합](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fbfe680-6dbb-4037-963c-a621c5635902) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1117.json) |

### <a name="correlate-audit-repositories"></a>감사 리포지토리 상호 연결

**ID**: NIST SP 800-53 Rev. 4 AU-6 (3)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1118 - 감사 검토, 분석 및 보고 \| 감사 리포지토리 상호 연결](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96f743d-a195-420d-983a-08aa06bc441e) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1118.json) |

### <a name="central-review-and-analysis"></a>중앙 검토 및 분석

**ID**: NIST SP 800-53 Rev. 4 AU-6 (4)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 서버에 대한 감사가 사용되도록 설정되어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |서버의 모든 데이터베이스에서 데이터베이스 활동을 추적하고 감사 로그에 저장하려면 SQL Server에서의 감사를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[구독에 Log Analytics 에이전트의 자동 프로비저닝을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |보안 취약성 및 위협을 모니터링하기 위해 Azure Security Center는 Azure 가상 머신에서 데이터를 수집합니다. 데이터는 이전에 MMA(Microsoft Monitoring Agent)로 알려진 Log Analytics 에이전트에 의해 수집되며, 머신에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석을 위해 Log Analytics 작업 영역에 데이터를 복사합니다. 지원되는 모든 Azure VM과 생성된 모든 새 VM에 에이전트를 자동으로 배포하려면 자동 프로비저닝을 사용하도록 설정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Azure Arc 사용 Kubernetes 클러스터에 Azure Defender의 확장이 설치되어 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Arc용 Azure Defender 확장은 Arc 지원 Kubernetes 클러스터에 대한 위협 보호를 제공합니다. 이 확장은 클러스터의 노드에서 데이터를 수집하고 추가 분석을 위해 클라우드의 Azure Defender for Kubernetes 백 엔드로 보냅니다. [https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[Azure Defender for App Service를 사용해야 합니다](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service는 클라우드의 규모와 Azure가 클라우드 공급자로서 가지고 있는 가시성을 활용하여 일반적인 웹앱 공격을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure SQL Database 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[컨테이너 레지스트리용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |컨테이너 레지스트리용 Azure Defender는 지난 30일 내에 끌어와서 레지스트리에 푸시한 이미지의 취약성을 검사하고, 각 이미지에 대해 발견한 자세한 내용을 공개합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS는 Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링하여 클라우드 리소스에 대한 추가 보호 계층을 제공합니다. Azure Defender는 DNS 계층에서 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)에서 Azure Defender for DNS의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault는 키 자격 증명 모음 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하여 보안 인텔리전스에 추가 보호 계층을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager는 조직의 리소스 관리 작업을 자동으로 모니터링합니다. Azure Defender는 위협을 감지하고 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)에서 Azure Defender for Resource Manager의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[머신의 SQL 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[보호되지 않는 Azure SQL 서버에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[스토리지용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[App Services의 진단 로그를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |앱에서 진단 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[머신에 게스트 구성 확장을 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae89ebca-1c92-4898-ac2c-9f63decb045c) |머신의 게스트 내 설정을 안전하게 구성하려면 게스트 구성 확장을 설치합니다. 확장에서 모니터링하는 게스트 내 설정에는 운영 체제 구성, 애플리케이션 구성 또는 현재 상태 및 환경 설정이 포함됩니다. 설치되면 'Windows Exploit Guard를 사용하도록 설정해야 합니다'와 같은 게스트 내 정책을 사용할 수 있습니다. [https://aka.ms/gcpol](../concepts/guest-configuration.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVm.json) |
|[머신에서 Log Analytics 에이전트 상태 문제를 해결해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd62cfe2b-3ab0-4d41-980d-76803b58ca65) |Security Center는 Log Analytics 에이전트(이전의 MMA(Microsoft Monitoring Agent))를 사용합니다. 가상 머신이 모니터링되는 것을 확인하려면 에이전트가 가상 머신에 설치되어 있고 구성된 작업 영역에 대한 보안 이벤트를 제대로 수집하는지 확인해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ResolveLaHealthIssues.json) |
|[Linux Azure Arc 머신에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F842c54e8-c2f9-4d79-ae8d-38d8b8019373) |이 정책은 Log Analytics 에이전트가 설치되지 않은 경우 Linux Azure Arc 머신을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Linux_LogAnalytics_Audit.json) |
|[Azure Security Center를 모니터링하려면 가상 머신에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4fe33eb-e377-4efb-ab31-0784311bc499) |이 정책은 Security Center가 보안 취약성 및 위협을 모니터링하는 데 사용할 Log Analytics 에이전트가 설치되어 있지 않은 경우 Windows/Linux VMs(Virtual Machines)를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVm.json) |
|[Azure Security Center를 모니터링하려면 가상 머신 확장 집합에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa3a6ea0c-e018-4933-9ef0-5aaa1501449b) |Security Center는 Azure VMs(Virtual Machines)에서 데이터를 수집하여 보안 취약성 및 위협을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVmss.json) |
|[Windows Azure Arc 머신에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd69b1763-b96d-40b8-a2d9-ca31e9fd0d3e) |이 정책은 Log Analytics 에이전트가 설치되지 않은 경우 Windows Azure Arc 머신을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Windows_LogAnalytics_Audit.json) |
|[Microsoft 관리형 컨트롤 1119 - 감사 검토, 분석 및 보고 \| 중앙 검토 및 분석](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F845f6359-b764-4b40-b579-657aefe23c44) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1119.json) |
|[Linux 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[Windows 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Network Watcher를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. 시나리오 수준 모니터링을 사용하면 종단 간 네트워크 수준 보기에서 문제를 진단할 수 있습니다. 가상 네트워크가 있는 모든 지역에서 Network Watcher 리소스 그룹을 만들어야 합니다. 특정 지역에서 Network Watcher 리소스 그룹을 사용할 수 없는 경우 경고가 활성화됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|[Azure Data Lake Store에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Azure Stream Analytics에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Batch 계정에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Data Lake Analytics에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[Event Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Key Vault에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Logic Apps에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Search Services에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Service Bus에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Virtual Machine Scale Sets에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |인시던트 또는 손상이 발생하는 경우 조사가 필요할 때 활동 내역을 다시 만들 수 있도록 로그를 사용하도록 설정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |
|[가상 머신의 게스트 구성 확장은 시스템이 할당한 관리 ID를 사용하여 배포해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd26f7642-7545-4e18-9b75-8c9bbdee3a9a) |게스트 구성 확장에는 시스템이 할당한 관리 ID가 필요합니다. 게스트 구성 확장이 설치되어 있지만 시스템이 할당한 관리 ID가 없는 경우 이 정책 범위에 속한 Azure 가상 머신은 비준수입니다. [https://aka.ms/gcpol](../concepts/guest-configuration.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVmWithNoSAMI.json) |

### <a name="integration--scanning-and-monitoring-capabilities"></a>통합/검사 및 모니터링 기능

**ID**: NIST SP 800-53 Rev. 4 AU-6 (5)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 서버에 대한 감사가 사용되도록 설정되어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |서버의 모든 데이터베이스에서 데이터베이스 활동을 추적하고 감사 로그에 저장하려면 SQL Server에서의 감사를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[구독에 Log Analytics 에이전트의 자동 프로비저닝을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |보안 취약성 및 위협을 모니터링하기 위해 Azure Security Center는 Azure 가상 머신에서 데이터를 수집합니다. 데이터는 이전에 MMA(Microsoft Monitoring Agent)로 알려진 Log Analytics 에이전트에 의해 수집되며, 머신에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석을 위해 Log Analytics 작업 영역에 데이터를 복사합니다. 지원되는 모든 Azure VM과 생성된 모든 새 VM에 에이전트를 자동으로 배포하려면 자동 프로비저닝을 사용하도록 설정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Azure Arc 사용 Kubernetes 클러스터에 Azure Defender의 확장이 설치되어 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Arc용 Azure Defender 확장은 Arc 지원 Kubernetes 클러스터에 대한 위협 보호를 제공합니다. 이 확장은 클러스터의 노드에서 데이터를 수집하고 추가 분석을 위해 클라우드의 Azure Defender for Kubernetes 백 엔드로 보냅니다. [https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[Azure Defender for App Service를 사용해야 합니다](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service는 클라우드의 규모와 Azure가 클라우드 공급자로서 가지고 있는 가시성을 활용하여 일반적인 웹앱 공격을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure SQL Database 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[컨테이너 레지스트리용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |컨테이너 레지스트리용 Azure Defender는 지난 30일 내에 끌어와서 레지스트리에 푸시한 이미지의 취약성을 검사하고, 각 이미지에 대해 발견한 자세한 내용을 공개합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS는 Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링하여 클라우드 리소스에 대한 추가 보호 계층을 제공합니다. Azure Defender는 DNS 계층에서 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)에서 Azure Defender for DNS의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault는 키 자격 증명 모음 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하여 보안 인텔리전스에 추가 보호 계층을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager는 조직의 리소스 관리 작업을 자동으로 모니터링합니다. Azure Defender는 위협을 감지하고 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)에서 Azure Defender for Resource Manager의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[머신의 SQL 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[보호되지 않는 Azure SQL 서버에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[스토리지용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[App Services의 진단 로그를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |앱에서 진단 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[머신에 게스트 구성 확장을 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae89ebca-1c92-4898-ac2c-9f63decb045c) |머신의 게스트 내 설정을 안전하게 구성하려면 게스트 구성 확장을 설치합니다. 확장에서 모니터링하는 게스트 내 설정에는 운영 체제 구성, 애플리케이션 구성 또는 현재 상태 및 환경 설정이 포함됩니다. 설치되면 'Windows Exploit Guard를 사용하도록 설정해야 합니다'와 같은 게스트 내 정책을 사용할 수 있습니다. [https://aka.ms/gcpol](../concepts/guest-configuration.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVm.json) |
|[머신에서 Log Analytics 에이전트 상태 문제를 해결해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd62cfe2b-3ab0-4d41-980d-76803b58ca65) |Security Center는 Log Analytics 에이전트(이전의 MMA(Microsoft Monitoring Agent))를 사용합니다. 가상 머신이 모니터링되는 것을 확인하려면 에이전트가 가상 머신에 설치되어 있고 구성된 작업 영역에 대한 보안 이벤트를 제대로 수집하는지 확인해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ResolveLaHealthIssues.json) |
|[Linux Azure Arc 머신에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F842c54e8-c2f9-4d79-ae8d-38d8b8019373) |이 정책은 Log Analytics 에이전트가 설치되지 않은 경우 Linux Azure Arc 머신을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Linux_LogAnalytics_Audit.json) |
|[Azure Security Center를 모니터링하려면 가상 머신에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4fe33eb-e377-4efb-ab31-0784311bc499) |이 정책은 Security Center가 보안 취약성 및 위협을 모니터링하는 데 사용할 Log Analytics 에이전트가 설치되어 있지 않은 경우 Windows/Linux VMs(Virtual Machines)를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVm.json) |
|[Azure Security Center를 모니터링하려면 가상 머신 확장 집합에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa3a6ea0c-e018-4933-9ef0-5aaa1501449b) |Security Center는 Azure VMs(Virtual Machines)에서 데이터를 수집하여 보안 취약성 및 위협을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVmss.json) |
|[Windows Azure Arc 머신에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd69b1763-b96d-40b8-a2d9-ca31e9fd0d3e) |이 정책은 Log Analytics 에이전트가 설치되지 않은 경우 Windows Azure Arc 머신을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Windows_LogAnalytics_Audit.json) |
|[Microsoft 관리형 컨트롤 1120 - 감사 검토, 분석 및 보고 \| 통합/검사 및 모니터링 기능](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc69b870e-857b-458b-af02-bb234f7a00d3) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1120.json) |
|[Linux 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[Windows 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Network Watcher를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. 시나리오 수준 모니터링을 사용하면 종단 간 네트워크 수준 보기에서 문제를 진단할 수 있습니다. 가상 네트워크가 있는 모든 지역에서 Network Watcher 리소스 그룹을 만들어야 합니다. 특정 지역에서 Network Watcher 리소스 그룹을 사용할 수 없는 경우 경고가 활성화됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|[Azure Data Lake Store에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Azure Stream Analytics에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Batch 계정에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Data Lake Analytics에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[Event Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Key Vault에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Logic Apps에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Search Services에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Service Bus에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Virtual Machine Scale Sets에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |인시던트 또는 손상이 발생하는 경우 조사가 필요할 때 활동 내역을 다시 만들 수 있도록 로그를 사용하도록 설정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |
|[가상 머신의 게스트 구성 확장은 시스템이 할당한 관리 ID를 사용하여 배포해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd26f7642-7545-4e18-9b75-8c9bbdee3a9a) |게스트 구성 확장에는 시스템이 할당한 관리 ID가 필요합니다. 게스트 구성 확장이 설치되어 있지만 시스템이 할당한 관리 ID가 없는 경우 이 정책 범위에 속한 Azure 가상 머신은 비준수입니다. [https://aka.ms/gcpol](../concepts/guest-configuration.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVmWithNoSAMI.json) |

### <a name="correlation-with-physical-monitoring"></a>물리적 모니터링과의 상관 관계

**ID**: NIST SP 800-53 Rev. 4 AU-6 (6)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1121 - 감사 검토, 분석 및 보고 \| 물리적 모니터링과의 상관 관계](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc72b0eb9-1fc2-44e5-a866-e7cb0532f7c1) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1121.json) |

### <a name="permitted-actions"></a>허용되는 작업

**ID**: NIST SP 800-53 Rev. 4 AU-6 (7)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1122 - 감사 검토, 분석 및 보고 \| 허용되는 작업](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F243ec95e-800c-49d4-ba52-1fdd9f6b8b57) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1122.json) |

### <a name="audit-level-adjustment"></a>감사 수준 조정

**ID**: NIST SP 800-53 Rev. 4 AU-6 (10)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1123 - 감사 검토, 분석 및 보고 \| 감사 수준 조정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03996055-37a4-45a5-8b70-3f1caa45f87d) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1123.json) |

### <a name="audit-reduction-and-report-generation"></a>감사 감소 및 보고서 생성

**ID**: NIST SP 800-53 Rev. 4 AU-7

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1124 - 감사 감소 및 보고서 생성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10152dd-78f8-4335-ae2d-ad92cc028da4) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1124.json) |
|[Microsoft 관리형 컨트롤 1125 - 감사 감소 및 보고서 생성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6ce745a-670e-47d3-a6c4-3cfe5ef00c10) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1125.json) |

### <a name="automatic-processing"></a>자동 처리

**ID**: NIST SP 800-53 Rev. 4 AU-7 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1126 - 감사 감소 및 보고서 생성 \| 자동 처리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f37f71b-420f-49bf-9477-9c0196974ecf) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1126.json) |

### <a name="time-stamps"></a>타임스탬프

**ID**: NIST SP 800-53 Rev. 4 AU-8

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1127 - 타임스탬프](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ce328db-aef3-48ed-9f81-2ab7cf839c66) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1127.json) |
|[Microsoft 관리형 컨트롤 1128 - 타임스탬프](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef212163-3bc4-4e86-bcf8-705127086393) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1128.json) |

### <a name="synchronization-with-authoritative-time-source"></a>신뢰할 수 있는 시간 원본과의 동기화

**ID**: NIST SP 800-53 Rev. 4 AU-8 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1129 - 타임스탬프 \| 신뢰할 수 있는 시간 원본과의 동기화](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71bb965d-4047-4623-afd4-b8189a58df5d) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1129.json) |
|[Microsoft 관리형 컨트롤 1130 - 타임스탬프 \| 신뢰할 수 있는 시간 원본과의 동기화](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd7c4c1d-51ee-4349-9dab-89a7f8c8d102) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1130.json) |

### <a name="protection-of-audit-information"></a>감사 정보 보호

**ID**: NIST SP 800-53 Rev. 4 AU-9

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1131 - 감사 정보 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb472a17e-c2bc-493f-b50b-42d55a346962) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1131.json) |

### <a name="audit-backup-on-separate-physical-systems--components"></a>개별 물리적 시스템/구성 요소에 대한 감사 백업

**ID**: NIST SP 800-53 Rev. 4 AU-9 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1132 - 감사 정보 보호 \| 개별 물리적 시스템/구성 요소에 대한 감사 백업](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05938e10-cdbd-4a54-9b2b-1cbcfc141ad0) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1132.json) |

### <a name="cryptographic-protection"></a>암호화 보호

**ID**: NIST SP 800-53 Rev. 4 AU-9 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1133 - 감사 정보 보호 \| 암호화 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90b60a09-133d-45bc-86ef-b206a6134bbe) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1133.json) |

### <a name="access-by-subset-of-privileged-users"></a>권한 있는 사용자 하위 세트의 액세스

**ID**: NIST SP 800-53 Rev. 4 AU-9 (4)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1134 - 감사 정보 보호 \| 권한 있는 사용자 하위 세트의 액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e95f70e-181c-4422-9da2-43079710c789) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1134.json) |

### <a name="non-repudiation"></a>부인 방지

**ID**: NIST SP 800-53 Rev. 4 AU-10

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1135 - 부인 방지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c308b6b-2429-4b97-86cf-081b8e737b04) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1135.json) |

### <a name="audit-record-retention"></a>감사 레코드 보존

**ID**: NIST SP 800-53 Rev. 4 AU-11

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1136 - 감사 레코드 보존](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97ed5bac-a92f-4f6d-a8ed-dc094723597c) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1136.json) |
|[스토리지 계정 대상에 대한 감사 기능이 있는 SQL Server는 보존 기간을 90일 이상으로 구성해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |인시던트 조사를 위해 SQL Server 감사를 위한 데이터 보존 기간을 스토리지 계정 대상으로 90일 이상으로 설정하는 것이 좋습니다. 운영 중인 지역에 필요한 보존 규칙을 충족하는지 확인합니다. 이는 규정 표준을 준수해야 하는 경우도 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="audit-generation"></a>감사 생성

**ID**: NIST SP 800-53 Rev. 4 AU-12

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 서버에 대한 감사가 사용되도록 설정되어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |서버의 모든 데이터베이스에서 데이터베이스 활동을 추적하고 감사 로그에 저장하려면 SQL Server에서의 감사를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[구독에 Log Analytics 에이전트의 자동 프로비저닝을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |보안 취약성 및 위협을 모니터링하기 위해 Azure Security Center는 Azure 가상 머신에서 데이터를 수집합니다. 데이터는 이전에 MMA(Microsoft Monitoring Agent)로 알려진 Log Analytics 에이전트에 의해 수집되며, 머신에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석을 위해 Log Analytics 작업 영역에 데이터를 복사합니다. 지원되는 모든 Azure VM과 생성된 모든 새 VM에 에이전트를 자동으로 배포하려면 자동 프로비저닝을 사용하도록 설정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Azure Arc 사용 Kubernetes 클러스터에 Azure Defender의 확장이 설치되어 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Arc용 Azure Defender 확장은 Arc 지원 Kubernetes 클러스터에 대한 위협 보호를 제공합니다. 이 확장은 클러스터의 노드에서 데이터를 수집하고 추가 분석을 위해 클라우드의 Azure Defender for Kubernetes 백 엔드로 보냅니다. [https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[Azure Defender for App Service를 사용해야 합니다](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service는 클라우드의 규모와 Azure가 클라우드 공급자로서 가지고 있는 가시성을 활용하여 일반적인 웹앱 공격을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure SQL Database 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[컨테이너 레지스트리용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |컨테이너 레지스트리용 Azure Defender는 지난 30일 내에 끌어와서 레지스트리에 푸시한 이미지의 취약성을 검사하고, 각 이미지에 대해 발견한 자세한 내용을 공개합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS는 Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링하여 클라우드 리소스에 대한 추가 보호 계층을 제공합니다. Azure Defender는 DNS 계층에서 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)에서 Azure Defender for DNS의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault는 키 자격 증명 모음 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하여 보안 인텔리전스에 추가 보호 계층을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager는 조직의 리소스 관리 작업을 자동으로 모니터링합니다. Azure Defender는 위협을 감지하고 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)에서 Azure Defender for Resource Manager의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[머신의 SQL 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[보호되지 않는 Azure SQL 서버에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[스토리지용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[App Services의 진단 로그를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |앱에서 진단 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[머신에 게스트 구성 확장을 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae89ebca-1c92-4898-ac2c-9f63decb045c) |머신의 게스트 내 설정을 안전하게 구성하려면 게스트 구성 확장을 설치합니다. 확장에서 모니터링하는 게스트 내 설정에는 운영 체제 구성, 애플리케이션 구성 또는 현재 상태 및 환경 설정이 포함됩니다. 설치되면 'Windows Exploit Guard를 사용하도록 설정해야 합니다'와 같은 게스트 내 정책을 사용할 수 있습니다. [https://aka.ms/gcpol](../concepts/guest-configuration.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVm.json) |
|[머신에서 Log Analytics 에이전트 상태 문제를 해결해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd62cfe2b-3ab0-4d41-980d-76803b58ca65) |Security Center는 Log Analytics 에이전트(이전의 MMA(Microsoft Monitoring Agent))를 사용합니다. 가상 머신이 모니터링되는 것을 확인하려면 에이전트가 가상 머신에 설치되어 있고 구성된 작업 영역에 대한 보안 이벤트를 제대로 수집하는지 확인해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ResolveLaHealthIssues.json) |
|[Linux Azure Arc 머신에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F842c54e8-c2f9-4d79-ae8d-38d8b8019373) |이 정책은 Log Analytics 에이전트가 설치되지 않은 경우 Linux Azure Arc 머신을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Linux_LogAnalytics_Audit.json) |
|[Azure Security Center를 모니터링하려면 가상 머신에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4fe33eb-e377-4efb-ab31-0784311bc499) |이 정책은 Security Center가 보안 취약성 및 위협을 모니터링하는 데 사용할 Log Analytics 에이전트가 설치되어 있지 않은 경우 Windows/Linux VMs(Virtual Machines)를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVm.json) |
|[Azure Security Center를 모니터링하려면 가상 머신 확장 집합에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa3a6ea0c-e018-4933-9ef0-5aaa1501449b) |Security Center는 Azure VMs(Virtual Machines)에서 데이터를 수집하여 보안 취약성 및 위협을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVmss.json) |
|[Windows Azure Arc 머신에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd69b1763-b96d-40b8-a2d9-ca31e9fd0d3e) |이 정책은 Log Analytics 에이전트가 설치되지 않은 경우 Windows Azure Arc 머신을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Windows_LogAnalytics_Audit.json) |
|[Microsoft 관리형 컨트롤 1137 - 감사 생성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4344df62-88ab-4637-b97b-bcaf2ec97e7c) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1137.json) |
|[Microsoft 관리형 컨트롤 1138 - 감사 생성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9c284fc0-268a-4f29-af44-3c126674edb4) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1138.json) |
|[Microsoft 관리형 컨트롤 1139 - 감사 생성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ed62522-de00-4dda-9810-5205733d2f34) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1139.json) |
|[Linux 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[Windows 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Network Watcher를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. 시나리오 수준 모니터링을 사용하면 종단 간 네트워크 수준 보기에서 문제를 진단할 수 있습니다. 가상 네트워크가 있는 모든 지역에서 Network Watcher 리소스 그룹을 만들어야 합니다. 특정 지역에서 Network Watcher 리소스 그룹을 사용할 수 없는 경우 경고가 활성화됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|[Azure Data Lake Store에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Azure Stream Analytics에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Batch 계정에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Data Lake Analytics에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[Event Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Key Vault에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Logic Apps에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Search Services에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Service Bus에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Virtual Machine Scale Sets에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |인시던트 또는 손상이 발생하는 경우 조사가 필요할 때 활동 내역을 다시 만들 수 있도록 로그를 사용하도록 설정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |
|[가상 머신의 게스트 구성 확장은 시스템이 할당한 관리 ID를 사용하여 배포해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd26f7642-7545-4e18-9b75-8c9bbdee3a9a) |게스트 구성 확장에는 시스템이 할당한 관리 ID가 필요합니다. 게스트 구성 확장이 설치되어 있지만 시스템이 할당한 관리 ID가 없는 경우 이 정책 범위에 속한 Azure 가상 머신은 비준수입니다. [https://aka.ms/gcpol](../concepts/guest-configuration.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVmWithNoSAMI.json) |

### <a name="system-wide--time-correlated-audit-trail"></a>시스템 수준/시간 관련 감사 내역

**ID**: NIST SP 800-53 Rev. 4 AU-12 (1)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 서버에 대한 감사가 사용되도록 설정되어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |서버의 모든 데이터베이스에서 데이터베이스 활동을 추적하고 감사 로그에 저장하려면 SQL Server에서의 감사를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[구독에 Log Analytics 에이전트의 자동 프로비저닝을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |보안 취약성 및 위협을 모니터링하기 위해 Azure Security Center는 Azure 가상 머신에서 데이터를 수집합니다. 데이터는 이전에 MMA(Microsoft Monitoring Agent)로 알려진 Log Analytics 에이전트에 의해 수집되며, 머신에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석을 위해 Log Analytics 작업 영역에 데이터를 복사합니다. 지원되는 모든 Azure VM과 생성된 모든 새 VM에 에이전트를 자동으로 배포하려면 자동 프로비저닝을 사용하도록 설정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Azure Arc 사용 Kubernetes 클러스터에 Azure Defender의 확장이 설치되어 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Arc용 Azure Defender 확장은 Arc 지원 Kubernetes 클러스터에 대한 위협 보호를 제공합니다. 이 확장은 클러스터의 노드에서 데이터를 수집하고 추가 분석을 위해 클라우드의 Azure Defender for Kubernetes 백 엔드로 보냅니다. [https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[Azure Defender for App Service를 사용해야 합니다](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service는 클라우드의 규모와 Azure가 클라우드 공급자로서 가지고 있는 가시성을 활용하여 일반적인 웹앱 공격을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure SQL Database 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[컨테이너 레지스트리용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |컨테이너 레지스트리용 Azure Defender는 지난 30일 내에 끌어와서 레지스트리에 푸시한 이미지의 취약성을 검사하고, 각 이미지에 대해 발견한 자세한 내용을 공개합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS는 Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링하여 클라우드 리소스에 대한 추가 보호 계층을 제공합니다. Azure Defender는 DNS 계층에서 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)에서 Azure Defender for DNS의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault는 키 자격 증명 모음 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하여 보안 인텔리전스에 추가 보호 계층을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager는 조직의 리소스 관리 작업을 자동으로 모니터링합니다. Azure Defender는 위협을 감지하고 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)에서 Azure Defender for Resource Manager의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[머신의 SQL 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[보호되지 않는 Azure SQL 서버에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[스토리지용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[App Services의 진단 로그를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |앱에서 진단 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[머신에 게스트 구성 확장을 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae89ebca-1c92-4898-ac2c-9f63decb045c) |머신의 게스트 내 설정을 안전하게 구성하려면 게스트 구성 확장을 설치합니다. 확장에서 모니터링하는 게스트 내 설정에는 운영 체제 구성, 애플리케이션 구성 또는 현재 상태 및 환경 설정이 포함됩니다. 설치되면 'Windows Exploit Guard를 사용하도록 설정해야 합니다'와 같은 게스트 내 정책을 사용할 수 있습니다. [https://aka.ms/gcpol](../concepts/guest-configuration.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVm.json) |
|[머신에서 Log Analytics 에이전트 상태 문제를 해결해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd62cfe2b-3ab0-4d41-980d-76803b58ca65) |Security Center는 Log Analytics 에이전트(이전의 MMA(Microsoft Monitoring Agent))를 사용합니다. 가상 머신이 모니터링되는 것을 확인하려면 에이전트가 가상 머신에 설치되어 있고 구성된 작업 영역에 대한 보안 이벤트를 제대로 수집하는지 확인해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ResolveLaHealthIssues.json) |
|[Linux Azure Arc 머신에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F842c54e8-c2f9-4d79-ae8d-38d8b8019373) |이 정책은 Log Analytics 에이전트가 설치되지 않은 경우 Linux Azure Arc 머신을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Linux_LogAnalytics_Audit.json) |
|[Azure Security Center를 모니터링하려면 가상 머신에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4fe33eb-e377-4efb-ab31-0784311bc499) |이 정책은 Security Center가 보안 취약성 및 위협을 모니터링하는 데 사용할 Log Analytics 에이전트가 설치되어 있지 않은 경우 Windows/Linux VMs(Virtual Machines)를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVm.json) |
|[Azure Security Center를 모니터링하려면 가상 머신 확장 집합에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa3a6ea0c-e018-4933-9ef0-5aaa1501449b) |Security Center는 Azure VMs(Virtual Machines)에서 데이터를 수집하여 보안 취약성 및 위협을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVmss.json) |
|[Windows Azure Arc 머신에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd69b1763-b96d-40b8-a2d9-ca31e9fd0d3e) |이 정책은 Log Analytics 에이전트가 설치되지 않은 경우 Windows Azure Arc 머신을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Windows_LogAnalytics_Audit.json) |
|[Microsoft 관리형 컨트롤 1140 - 감사 생성 \| 시스템 수준/시간 관련 감사 내역](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90d8b8ad-8ee3-4db7-913f-2a53fcff5316) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1140.json) |
|[Linux 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[Windows 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Network Watcher를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. 시나리오 수준 모니터링을 사용하면 종단 간 네트워크 수준 보기에서 문제를 진단할 수 있습니다. 가상 네트워크가 있는 모든 지역에서 Network Watcher 리소스 그룹을 만들어야 합니다. 특정 지역에서 Network Watcher 리소스 그룹을 사용할 수 없는 경우 경고가 활성화됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|[Azure Data Lake Store에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[Azure Stream Analytics에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Batch 계정에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[Data Lake Analytics에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[Event Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[Key Vault에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[Logic Apps에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[Search Services에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Service Bus에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Virtual Machine Scale Sets에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |인시던트 또는 손상이 발생하는 경우 조사가 필요할 때 활동 내역을 다시 만들 수 있도록 로그를 사용하도록 설정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |
|[가상 머신의 게스트 구성 확장은 시스템이 할당한 관리 ID를 사용하여 배포해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd26f7642-7545-4e18-9b75-8c9bbdee3a9a) |게스트 구성 확장에는 시스템이 할당한 관리 ID가 필요합니다. 게스트 구성 확장이 설치되어 있지만 시스템이 할당한 관리 ID가 없는 경우 이 정책 범위에 속한 Azure 가상 머신은 비준수입니다. [https://aka.ms/gcpol](../concepts/guest-configuration.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVmWithNoSAMI.json) |

### <a name="changes-by-authorized-individuals"></a>권한 있는 개인의 변경

**ID**: NIST SP 800-53 Rev. 4 AU-12 (3)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1141 - 감사 생성 \| 권한 있는 개인의 변경](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fdefbf4-93e7-4513-bc95-c1858b7093e0) |Microsoft에서 구현하는 감사 및 책임 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1141.json) |

## <a name="security-assessment-and-authorization"></a>보안 평가 및 권한 부여

### <a name="security-assessment-and-authorization-policy-and-procedures"></a>보안 평가 및 권한 부여 정책과 절차

**ID**: NIST SP 800-53 Rev. 4 CA-1

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1142 - 보안 평가 및 권한 부여 정책과 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01524fa8-4555-48ce-ba5f-c3b8dcef5147) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1142.json) |
|[Microsoft 관리형 컨트롤 1143 - 보안 평가 및 권한 부여 정책과 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c6de11b-5f51-4f7c-8d83-d2467c8a816e) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1143.json) |

### <a name="security-assessments"></a>보안 평가

**ID**: NIST SP 800-53 Rev. 4 CA-2

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1144 - 보안 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2fa15ff1-a693-4ee4-b094-324818dc9a51) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1144.json) |
|[Microsoft 관리형 컨트롤 1145 - 보안 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0724970-9c75-4a64-a225-a28002953f28) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1145.json) |
|[Microsoft 관리형 컨트롤 1146 - 보안 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd83410c-ecb6-4547-8f14-748c3cbdc7ac) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1146.json) |
|[Microsoft 관리형 컨트롤 1147 - 보안 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fef824a-29a8-4a4c-88fc-420a39c0d541) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1147.json) |

### <a name="independent-assessors"></a>독립 평가자

**ID**: NIST SP 800-53 Rev. 4 CA-2 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1148 - 보안 평가 \| 독립 평가자](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e62650-c7c2-4786-bdfa-17edc1673902) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1148.json) |

### <a name="specialized-assessments"></a>특별 평가

**ID**: NIST SP 800-53 Rev. 4 CA-2 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1149 - 보안 평가 \| 특별 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e1b855b-a013-481a-aeeb-2bcb129fd35d) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1149.json) |

### <a name="external-organizations"></a>외부 조직

**ID**: NIST SP 800-53 Rev. 4 CA-2 (3)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1150 - 보안 평가 \| 외부 조직](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd630429d-e763-40b1-8fba-d20ba7314afb) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1150.json) |

### <a name="system-interconnections"></a>시스템 상호 연결

**ID**: NIST SP 800-53 Rev. 4 CA-3

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1151 - 시스템 상호 연결](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F347e3b69-7fb7-47df-a8ef-71a1a7b44bca) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1151.json) |
|[Microsoft 관리형 컨트롤 1152 - 시스템 상호 연결](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbeff0acf-7e67-40b2-b1ca-1a0e8205cf1b) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1152.json) |
|[Microsoft 관리형 컨트롤 1153 - 시스템 상호 연결](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61cf3125-142c-4754-8a16-41ab4d529635) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1153.json) |

### <a name="unclassified-non-national-security-system-connections"></a>국가 소유가 아닌 미분류 보안 시스템 연결

**ID**: NIST SP 800-53 Rev. 4 CA-3 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1154 - 시스템 상호 연결 \| 국가 소유가 아닌 미분류 보안 시스템 연결](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe757ceb9-93b3-45fe-a4f4-f43f64f1ac5a) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1154.json) |

### <a name="restrictions-on-external-system-connections"></a>외부 시스템 연결 제한

**ID**: NIST SP 800-53 Rev. 4 CA-3 (5)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1155 - 시스템 상호 연결 \| 외부 시스템 연결 제한](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d33f9f1-12d0-46ad-9fbd-8f8046694977) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1155.json) |

### <a name="plan-of-action-and-milestones"></a>작업 계획 및 마일스톤

**ID**: NIST SP 800-53 Rev. 4 CA-5

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1156 - 작업 계획 및 마일스톤](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d52e864-9a3b-41ee-8f03-520815fe5378) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1156.json) |
|[Microsoft 관리형 컨트롤 1157 - 작업 계획 및 마일스톤](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F15495367-cf68-464c-bbc3-f53ca5227b7a) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1157.json) |

### <a name="security-authorization"></a>보안 권한 부여

**ID**: NIST SP 800-53 Rev. 4 CA-6

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1158 - 보안 권한 부여](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffff50cf2-28eb-45b4-b378-c99412688907) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1158.json) |
|[Microsoft 관리형 컨트롤 1159 - 보안 권한 부여](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0925f098-7877-450b-8ba4-d1e55f2d8795) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1159.json) |
|[Microsoft 관리형 컨트롤 1160 - 보안 권한 부여](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e797ca6-2aa8-4333-b335-7036f1110c05) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1160.json) |

### <a name="continuous-monitoring"></a>연속 모니터링

**ID**: NIST SP 800-53 Rev. 4 CA-7

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1161 - 지속적인 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2f8f6c6-dde4-436b-a79d-bc50e129eb3a) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1161.json) |
|[Microsoft 관리형 컨트롤 1162 - 지속적인 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5770f3d6-8c2b-4f6f-bf0e-c8c8fc36d592) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1162.json) |
|[Microsoft 관리형 컨트롤 1163 - 지속적인 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F961663a1-8a91-4e59-b6f5-1eee57c0f49c) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1163.json) |
|[Microsoft 관리형 컨트롤 1164 - 지속적인 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fb8d3ce-9e96-481c-9c68-88d4e3019310) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1164.json) |
|[Microsoft 관리형 컨트롤 1165 - 지속적인 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47e10916-6c9e-446b-b0bd-ff5fd439d79d) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1165.json) |
|[Microsoft 관리형 컨트롤 1166 - 지속적인 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb02733d-3cc5-4bb0-a6cd-695ba2c2272e) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1166.json) |
|[Microsoft 관리형 컨트롤 1167 - 지속적인 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcbb2be76-4891-430b-95a7-ca0b0a3d1300) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1167.json) |

### <a name="independent-assessment"></a>독립 평가

**ID**: NIST SP 800-53 Rev. 4 CA-7 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1168 - 지속적인 모니터링 \| 독립적 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82409f9e-1f32-4775-bf07-b99d53a91b06) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1168.json) |

### <a name="trend-analyses"></a>추세 분석

**ID**: NIST SP 800-53 Rev. 4 CA-7 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1169 - 지속적인 모니터링 \| 추세 분석](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7ba2cb3-5675-4468-8b50-8486bdd998a5) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1169.json) |

### <a name="penetration-testing"></a>침투 테스트

**ID**: NIST SP 800-53 Rev. 4 CA-8

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1170 - 침투 테스트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b78b9b3-ee3c-48e0-a243-ed6dba5b7a12) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1170.json) |

### <a name="independent-penetration-agent-or-team"></a>독립 침투 에이전트 또는 팀

**ID**: NIST SP 800-53 Rev. 4 CA-8 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1171 - 침투 테스트 \| 독립적 침투 에이전트 또는 팀](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d4820bc-8b61-4982-9501-2123cb776c00) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1171.json) |

### <a name="internal-system-connections"></a>내부 시스템 연결

**ID**: NIST SP 800-53 Rev. 4 CA-9

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1172 - 내부 시스템 연결](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb43e946e-a4c8-4b92-8201-4a39331db43c) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1172.json) |
|[Microsoft 관리형 컨트롤 1173 - 내부 시스템 연결](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4aff9e7-2e60-46fa-86be-506b79033fc5) |Microsoft에서 구현하는 보안 평가 및 권한 부여 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1173.json) |

## <a name="configuration-management"></a>구성 관리

### <a name="configuration-management-policy-and-procedures"></a>구성 관리 정책 및 절차

**ID**: NIST SP 800-53 Rev. 4 CM-1

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1174 - 구성 관리 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42a9a714-8fbb-43ac-b115-ea12d2bd652f) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1174.json) |
|[Microsoft 관리형 컨트롤 1175 - 구성 관리 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6dab4254-c30d-4bb7-ae99-1d21586c063c) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1175.json) |

### <a name="baseline-configuration"></a>기준 구성

**ID**: NIST SP 800-53 Rev. 4 CM-2

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1176 - 기준 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc30690a5-7bf3-467f-b0cd-ef5c7c7449cd) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1176.json) |

### <a name="reviews-and-updates"></a>검토 및 업데이트

**ID**: NIST SP 800-53 Rev. 4 CM-2 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1177 - 기준 구성 \| 검토 및 업데이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63dbc7a8-e20b-4d38-b857-a7f6c0cd94bc) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1177.json) |
|[Microsoft 관리형 컨트롤 1178 - 기준 구성 \| 검토 및 업데이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7818b8f4-47c6-441a-90ae-12ce04e99893) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1178.json) |
|[Microsoft 관리형 컨트롤 1179 - 기준 구성 \| 검토 및 업데이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f9ce557-c8ab-4e6c-bb2c-9b8ed002c46c) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1179.json) |

### <a name="automation-support-for-accuracy--currency"></a>정확성/최신성을 위한 자동화 지원

**ID**: NIST SP 800-53 Rev. 4 CM-2 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1180 - 기준 구성\| 정확성/최신성을 위한 자동화 지원](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F874e7880-a067-42a7-bcbe-1a340f54c8cc) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1180.json) |

### <a name="retention-of-previous-configurations"></a>이전 구성 보존

**ID**: NIST SP 800-53 Rev. 4 CM-2 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1181 - 기준 구성 \| 이전 구성 보존](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21839937-d241-4fa5-95c6-b669253d9ab9) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1181.json) |

### <a name="configure-systems-components-or-devices-for-high-risk-areas"></a>고위험 영역의 시스템, 구성 요소 또는 디바이스 구성

**ID**: NIST SP 800-53 Rev. 4 CM-2 (7)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1182 - 기준 구성 \| 고위험 영역의 시스템, 구성 요소 또는 디바이스 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f34f554-da4b-4786-8d66-7915c90893da) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1182.json) |
|[Microsoft 관리형 컨트롤 1183 - 기준 구성 \| 고위험 영역의 시스템, 구성 요소 또는 디바이스 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5352e3e0-e63a-452e-9e5f-9c1d181cff9c) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1183.json) |

### <a name="configuration-change-control"></a>구성 변경 제어

**ID**: NIST SP 800-53 Rev. 4 CM-3

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1184 - 구성 변경 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13579d0e-0ab0-4b26-b0fb-d586f6d7ed20) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1184.json) |
|[Microsoft 관리형 컨트롤 1185 - 구성 변경 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6420cd73-b939-43b7-9d99-e8688fea053c) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1185.json) |
|[Microsoft 관리형 컨트롤 1186 - 구성 변경 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb95ba3bd-4ded-49ea-9d10-c6f4b680813d) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1186.json) |
|[Microsoft 관리형 컨트롤 1187 - 구성 변경 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9f2b2f9e-4ba6-46c3-907f-66db138b6f85) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1187.json) |
|[Microsoft 관리형 컨트롤 1188 - 구성 변경 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb20548a-c926-4e4d-855c-bcddc6faf95e) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1188.json) |
|[Microsoft 관리형 컨트롤 1189 - 구성 변경 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee45e02a-4140-416c-82c4-fecfea660b9d) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1189.json) |
|[Microsoft 관리형 컨트롤 1190 - 구성 변경 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc66a3d1e-465b-4f28-9da5-aef701b59892) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1190.json) |

### <a name="automated-document--notification--prohibition-of-changes"></a>자동화된 문서/알림/변경 금지

**ID**: NIST SP 800-53 Rev. 4 CM-3 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1191 - 구성 변경 제어 \| 자동화 문서/알림/변경 금지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f26a61b-a74d-467c-99cf-63644db144f7) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1191.json) |
|[Microsoft 관리형 컨트롤 1192 - 구성 변경 제어 \| 자동화 문서/알림/변경 금지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ebd97f7-b105-4f50-8daf-c51465991240) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1192.json) |
|[Microsoft 관리형 컨트롤 1193 - 구성 변경 제어 \| 자동화 문서/알림/변경 금지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5fd629f-3075-4cae-ab53-bad65495a4ac) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1193.json) |
|[Microsoft 관리형 컨트롤 1194 - 구성 변경 제어 \| 자동화 문서/알림/변경 금지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc34667f-397e-4a65-9b72-d0358f0b6b09) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1194.json) |
|[Microsoft 관리형 컨트롤 1195 - 구성 변경 제어 \| 자동화 문서/알림/변경 금지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1e1d65c-1013-4484-bd54-991332e6a0d2) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1195.json) |
|[Microsoft 관리형 컨트롤 1196 - 구성 변경 제어 \| 자동화 문서/알림/변경 금지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e7f4ea4-dd62-44f6-8886-ac6137cf52b0) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1196.json) |

### <a name="test--validate--document-changes"></a>변경 테스트/유효성 검사/문서화

**ID**: NIST SP 800-53 Rev. 4 CM-3 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1197 - 구성 변경 제어 \| 변경 테스트/유효성 검사/문서화](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa20d2eaa-88e2-4907-96a2-8f3a05797e5c) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1197.json) |

### <a name="security-representative"></a>보안 담당자

**ID**: NIST SP 800-53 Rev. 4 CM-3 (4)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1198 - 구성 변경 제어 \| 보안 담당자](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff56be5c3-660b-4c61-9078-f67cf072c356) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1198.json) |

### <a name="cryptography-management"></a>암호화 관리

**ID**: NIST SP 800-53 Rev. 4 CM-3 (6)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1199 - 구성 변경 제어 \| 암호화 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9a08d1c-09b1-48f1-90ea-029bbdf7111e) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1199.json) |

### <a name="security-impact-analysis"></a>보안 영향 분석

**ID**: NIST SP 800-53 Rev. 4 CM-4

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1200 - 보안 영향 분석](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe98fe9d7-2ed3-44f8-93b7-24dca69783ff) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1200.json) |

### <a name="separate-test-environments"></a>개별 테스트 환경

**ID**: NIST SP 800-53 Rev. 4 CM-4 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1201 - 보안 영향 분석 \| 별도의 테스트 환경](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7daef997-fdd3-461b-8807-a608a6dd70f1) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1201.json) |

### <a name="access-restrictions-for-change"></a>변경 액세스 제한

**ID**: NIST SP 800-53 Rev. 4 CM-5

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1202 - 변경 액세스 제한](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40a2a83b-74f2-4c02-ae65-f460a5d2792a) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1202.json) |

### <a name="automated-access-enforcement--auditing"></a>자동화된 액세스 적용/감사

**ID**: NIST SP 800-53 Rev. 4 CM-5 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1203 - 변경 액세스 제한 \| 자동 액세스 적용/감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9012d14-e3e6-4d7b-b926-9f37b5537066) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1203.json) |

### <a name="review-system-changes"></a>시스템 변경 내용 검토

**ID**: NIST SP 800-53 Rev. 4 CM-5 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1204 - 변경 액세스 제한 \| 시스템 변경 내용 검토](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f4f6750-d1ab-4a4c-8dfd-af3237682665) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1204.json) |

### <a name="signed-components"></a>서명된 구성 요소

**ID**: NIST SP 800-53 Rev. 4 CM-5 (3)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1205 - 변경 액세스 제한 \| 서명된 구성 요소](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b070cab-0fb8-4e48-ad29-fc90b4c2797c) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1205.json) |

### <a name="limit-production--operational-privileges"></a>프로덕션/작업 권한 제한

**ID**: NIST SP 800-53 Rev. 4 CM-5 (5)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1206 - 변경 액세스 제한 \| 프로덕션/작업 권한 제한](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0de232d-02a0-4652-872d-88afb4ae5e91) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1206.json) |
|[Microsoft 관리형 컨트롤 1207 - 변경 액세스 제한 \| 프로덕션/작업 권한 제한](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8713a0ed-0d1e-4d10-be82-83dffb39830e) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1207.json) |

### <a name="configuration-settings"></a>구성 설정

**ID**: NIST SP 800-53 Rev. 4 CM-6

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[클러스터에 AKS(Azure Kubernetes Service)용 Azure Policy 추가 기능을 설치하고 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a15ec92-a229-4763-bb14-0ea34a568f8d) |AKS(Azure Kubernetes Service)용 Azure Policy 추가 기능은 OPA(Open Policy Agent)용 허용 컨트롤러 웹후크인 Gatekeeper v3를 확장하여 일관된 중앙 집중식 방법으로 클러스터에 대규모 규약 및 세이프가드를 적용합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_AzurePolicyAddOn_Audit.json) |
|[CORS에서 모든 리소스가 API 앱에 액세스하도록 허용해서는 안 됩니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |CORS(교차 원본 리소스 공유)는 API 앱에 액세스하는 모든 도메인을 허용하지 않아야 합니다. 필요한 도메인만 API 앱과 상호 작용할 수 있도록 허용합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |
|[CORS에서 모든 리소스가 함수 앱에 액세스하도록 허용해서는 안 됩니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |CORS(교차 원본 리소스 공유)는 함수 앱에 액세스하는 모든 도메인을 허용하지 않아야 합니다. 필요한 도메인만 함수 앱과 상호 작용할 수 있도록 허용합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |
|[CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용해서는 안 됩니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |CORS(교차 원본 리소스 공유)는 웹 애플리케이션에 액세스하는 모든 도메인을 허용하지 않아야 합니다. 필요한 도메인만 웹앱과 상호 작용할 수 있도록 허용합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |
|[API 앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |클라이언트 인증서는 앱이 들어오는 요청에 대한 인증서를 요청하도록 허용합니다. 유효한 인증서가 있는 클라이언트만 앱에 연결할 수 있습니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[웹앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |클라이언트 인증서는 앱이 들어오는 요청에 대한 인증서를 요청하도록 허용합니다. 유효한 인증서가 있는 클라이언트만 앱에 연결할 수 있습니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[함수 앱은 '클라이언트 인증서(들어오는 클라이언트 인증서)'를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |클라이언트 인증서는 앱이 들어오는 요청에 대한 인증서를 요청하도록 허용합니다. 유효한 인증서가 있는 클라이언트만 앱에 연결할 수 있습니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |
|[Kubernetes 클러스터 컨테이너 CPU 및 메모리 리소스 한도는 지정된 한도를 초과하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe345eecc-fa47-480f-9e88-67dcc122b164) |Kubernetes 클러스터에서 리소스 소모 공격을 방지하기 위해 컨테이너 CPU 및 메모리 리소스 제한을 적용합니다. 이 정책은 일반적으로 AKS(Kubernetes Service)에서 사용할 수 있으며 AKS 엔진 및 Azure Arc 지원 Kubernetes에 대한 미리 보기입니다. 자세한 내용은 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)을(를) 참조하세요. |감사, 거부, 사용 안 함 |[7.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerResourceLimits.json) |
|[Kubernetes 클러스터 컨테이너는 호스트 프로세스 ID 또는 호스트 IPC 네임스페이스를 공유해서는 안 됨](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8) |Kubernetes 클러스터에서 호스트 프로세스 ID 네임스페이스 및 호스트 IPC 네임스페이스를 공유하지 못하도록 Pod 컨테이너를 차단합니다. 이 권장 사항은 Kubernetes 환경의 보안을 향상시키기 위한 CIS 5.2.2 및 CIS 5.2.3의 일부입니다. 이 정책은 일반적으로 AKS(Kubernetes Service)에서 사용할 수 있으며 AKS 엔진 및 Azure Arc 지원 Kubernetes에 대한 미리 보기입니다. 자세한 내용은 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)을(를) 참조하세요. |감사, 거부, 사용 안 함 |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/BlockHostNamespace.json) |
|[Kubernetes 클러스터 컨테이너는 허용된 포트만 수신 대기해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F440b515e-a580-421e-abeb-b159a61ddcbc) |Kubernetes 클러스터에 대한 액세스를 보호하기 위해 컨테이너가 허용된 포트에서만 수신 대기하도록 제한합니다. 이 정책은 일반적으로 AKS(Kubernetes Service)에서 사용할 수 있으며 AKS 엔진 및 Azure Arc 지원 Kubernetes에 대한 미리 보기입니다. 자세한 내용은 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)을(를) 참조하세요. |감사, 거부, 사용 안 함 |[6.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedPorts.json) |
|[Kubernetes 클러스터 컨테이너는 허용된 AppArmor 프로필만 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) |컨테이너는 Kubernetes 클러스터에서 허용된 AppArmor 프로필만 사용해야 합니다. 이 권장 사항은 Kubernetes 환경의 보안을 향상시키기 위한 Pod 보안 정책의 일부입니다. 이 정책은 일반적으로 AKS(Kubernetes Service)에서 사용할 수 있으며 AKS 엔진 및 Azure Arc 지원 Kubernetes에 대한 미리 보기입니다. 자세한 내용은 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)을(를) 참조하세요. |감사, 거부, 사용 안 함 |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/EnforceAppArmorProfile.json) |
|[Kubernetes 클러스터 컨테이너는 허용된 기능만 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) |Kubernetes 클러스터에서 컨테이너의 공격 노출 영역을 줄이기 위해 기능을 제한합니다. 이 권장 사항은 Kubernetes 환경의 보안을 향상시키기 위한 CIS 5.2.8 및 CIS 5.2.9의 일부입니다. 이 정책은 일반적으로 AKS(Kubernetes Service)에서 사용할 수 있으며 AKS 엔진 및 Azure Arc 지원 Kubernetes에 대한 미리 보기입니다. 자세한 내용은 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)을(를) 참조하세요. |감사, 거부, 사용 안 함 |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedCapabilities.json) |
|[Kubernetes 클러스터 컨테이너는 허용된 이미지만 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffebd0533-8e55-448f-b837-bd0e06f16469) |신뢰할 수 있는 레지스트리의 이미지를 사용하여 알 수 없는 취약성, 보안 문제 및 악성 이미지에 대한 Kubernetes 클러스터의 노출 위험을 줄입니다. 이 정책은 일반적으로 AKS(Kubernetes Service)에서 사용할 수 있으며 AKS 엔진 및 Azure Arc 지원 Kubernetes에 대한 미리 보기입니다. 자세한 내용은 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)을(를) 참조하세요. |감사, 거부, 사용 안 함 |[7.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerAllowedImages.json) |
|[Kubernetes 클러스터 컨테이너는 읽기 전용 루트 파일 시스템에서 실행되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) |읽기 전용 루트 파일 시스템을 통해 컨테이너를 실행하여 Kubernetes 클러스터의 PATH에 추가된 악성 이진 파일로 인한 런타임 시 변경으로부터 보호합니다. 이 정책은 일반적으로 AKS(Kubernetes Service)에서 사용할 수 있으며 AKS 엔진 및 Azure Arc 지원 Kubernetes에 대한 미리 보기입니다. 자세한 내용은 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)을(를) 참조하세요. |감사, 거부, 사용 안 함 |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ReadOnlyRootFileSystem.json) |
|[Kubernetes 클러스터 Pod hostPath 볼륨은 허용된 호스트 경로만 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75) |Pod HostPath 볼륨 탑재를 Kubernetes 클러스터에서 허용된 호스트 경로로 제한합니다. 이 권장 사항은 Kubernetes 환경의 보안을 향상시키기 위한 Pod 보안 정책의 일부입니다. 이 정책은 일반적으로 AKS(Kubernetes Service)에서 사용할 수 있으며 AKS 엔진 및 Azure Arc 지원 Kubernetes에 대한 미리 보기입니다. 자세한 내용은 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)을(를) 참조하세요. |감사, 거부, 사용 안 함 |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AllowedHostPaths.json) |
|[Kubernetes 클러스터 Pod 및 컨테이너는 승인된 사용자 및 그룹 ID로만 실행해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) |Pod 및 컨테이너가 Kubernetes 클러스터에서 실행하는 데 사용할 수 있는 사용자, 기본 그룹, 보조 그룹 및 파일 시스템 그룹 ID를 제어합니다. 이 권장 사항은 Kubernetes 환경의 보안을 향상시키기 위한 Pod 보안 정책의 일부입니다. 이 정책은 일반적으로 AKS(Kubernetes Service)에서 사용할 수 있으며 AKS 엔진 및 Azure Arc 지원 Kubernetes에 대한 미리 보기입니다. 자세한 내용은 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)을(를) 참조하세요. |감사, 거부, 사용 안 함 |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AllowedUsersGroups.json) |
|[Kubernetes 클러스터 Pod는 승인된 호스트 네트워크와 포트 범위만 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe) |Kubernetes 클러스터에서 호스트 네트워크 및 허용 가능한 호스트 포트 범위에 대한 Pod 액세스를 제한합니다. 이 권장 사항은 Kubernetes 환경의 보안을 향상시키기 위한 CIS 5.2.4의 일부입니다. 이 정책은 일반적으로 AKS(Kubernetes Service)에서 사용할 수 있으며 AKS 엔진 및 Azure Arc 지원 Kubernetes에 대한 미리 보기입니다. 자세한 내용은 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)을(를) 참조하세요. |감사, 거부, 사용 안 함 |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/HostNetworkPorts.json) |
|[Kubernetes 클러스터 서비스는 허용된 포트만 수신 대기해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F233a2a17-77ca-4fb1-9b6b-69223d272a44) |Kubernetes 클러스터에 대한 액세스를 보호하기 위해 서비스가 허용된 포트에서만 수신 대기하도록 제한합니다. 이 정책은 일반적으로 AKS(Kubernetes Service)에서 사용할 수 있으며 AKS 엔진 및 Azure Arc 지원 Kubernetes에 대한 미리 보기입니다. 자세한 내용은 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)을(를) 참조하세요. |감사, 거부, 사용 안 함 |[6.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ServiceAllowedPorts.json) |
|[Kubernetes 클러스터는 권한 있는 컨테이너를 허용하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4) |Kubernetes 클러스터는 권한 있는 컨테이너를 만드는 것을 허용하지 않습니다. 이 권장 사항은 Kubernetes 환경의 보안을 향상시키기 위한 CIS 5.2.1의 일부입니다. 이 정책은 일반적으로 AKS(Kubernetes Service)에서 사용할 수 있으며 AKS 엔진 및 Azure Arc 지원 Kubernetes에 대한 미리 보기입니다. 자세한 내용은 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)을(를) 참조하세요. |감사, 거부, 사용 안 함 |[7.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilege.json) |
|[Kubernetes 클러스터는 컨테이너 권한 상승을 허용해서는 안 됨](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) |컨테이너가 Kubernetes 클러스터 루트로의 권한 상승을 통해 실행되는 것을 허용하지 않습니다. 이 권장 사항은 Kubernetes 환경의 보안을 향상시키기 위한 CIS 5.2.5의 일부입니다. 이 정책은 일반적으로 AKS(Kubernetes Service)에서 사용할 수 있으며 AKS 엔진 및 Azure Arc 지원 Kubernetes에 대한 미리 보기입니다. 자세한 내용은 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)을(를) 참조하세요. |감사, 거부, 사용 안 함 |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ContainerNoPrivilegeEscalation.json) |
|[Linux 머신이 Azure 컴퓨팅 보안 기준에 대한 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc9b3da7-8347-4380-8e70-0a0361d8dedd) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. Azure 컴퓨팅 보안 기준의 권장 사항 중 하나에 맞게 올바르게 구성되지 않은 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.1.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AzureLinuxBaseline_AINE.json) |
|[Microsoft 관리형 컨트롤 1208 - 구성 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5ea87673-d06b-456f-a324-8abcee5c159f) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1208.json) |
|[Microsoft 관리형 컨트롤 1209 - 구성 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fce669c31-9103-4552-ae9c-cdef4e03580d) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1209.json) |
|[Microsoft 관리형 컨트롤 1210 - 구성 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3502c968-c490-4570-8167-1476f955e9b8) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1210.json) |
|[Microsoft 관리형 컨트롤 1211 - 구성 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a8b9dc8-6b00-4701-aa96-bba3277ebf50) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1211.json) |
|[API Apps에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |원격 디버깅에 API 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |
|[함수 앱에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |원격 디버깅에 함수 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |
|[웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |원격 디버깅에 웹 애플리케이션에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |
|[Windows 머신은 Azure 컴퓨팅 보안 기준의 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72650e9f-97bc-4b2a-ab5f-9781a9fcecbc) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. Azure 컴퓨팅 보안 기준의 권장 사항 중 하나에 맞게 올바르게 구성되지 않은 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AzureWindowsBaseline_AINE.json) |

### <a name="automated-central-management--application--verification"></a>자동 중앙 관리/애플리케이션/확인

**ID**: NIST SP 800-53 Rev. 4 CM-6 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1212 - 구성 설정 \| 자동 중앙 관리/애플리케이션/확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56d970ee-4efc-49c8-8a4e-5916940d784c) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1212.json) |

### <a name="respond-to-unauthorized-changes"></a>권한 없는 변경에 대응

**ID**: NIST SP 800-53 Rev. 4 CM-6 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1213 - 구성 설정 \| 권한 없는 변경에 대응](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81f11e32-a293-4a58-82cd-134af52e2318) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1213.json) |

### <a name="least-functionality"></a>최소한의 기능

**ID**: NIST SP 800-53 Rev. 4 CM-7

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |애플리케이션 제어를 활성화하여 머신에서 실행 중인 알려진 안전한 애플리케이션 목록을 정의하고, 다른 애플리케이션이 실행될 때 경고합니다. 이렇게 하면 맬웨어로부터 머신을 강화할 수 있습니다. 규칙을 구성하고 유지 관리하는 프로세스를 간소화하기 위해 Security Center는 기계 학습을 사용하여 각 머신에서 실행 중인 애플리케이션을 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[적응형 애플리케이션 제어 정책의 허용 목록 규칙을 업데이트해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F123a3936-f020-408a-ba0c-47873faf1534) |Azure Security Center의 적응형 애플리케이션 제어를 통한 감사를 위해 구성된 머신 그룹의 동작 변경 내용을 모니터링합니다. Security Center는 기계 학습을 사용하여 머신에서 실행 중인 프로세스를 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. 이러한 앱은 적응형 애플리케이션 제어 정책에서 허용하도록 권장되는 앱으로 제공됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControlsUpdate_Audit.json) |
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Microsoft 관리형 컨트롤 1214 - 최소한의 기능](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff714a4e2-b580-47b6-ae8c-f2812d3750f3) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1214.json) |
|[Microsoft 관리형 컨트롤 1215 - 최소한의 기능](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88fc93e8-4745-4785-b5a5-b44bb92c44ff) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1215.json) |

### <a name="periodic-review"></a>주기적 검토

**ID**: NIST SP 800-53 Rev. 4 CM-7 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1216 - 최소한의 기능 \| 주기적으로 검토](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7894fe6a-f5cb-44c8-ba90-c3f254ff9484) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1216.json) |
|[Microsoft 관리형 컨트롤 1217 - 최소한의 기능 \| 주기적으로 검토](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fedea4f20-b02c-4115-be75-86c080e5c0ed) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1217.json) |

### <a name="prevent-program-execution"></a>프로그램 실행 방지

**ID**: NIST SP 800-53 Rev. 4 CM-7 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |애플리케이션 제어를 활성화하여 머신에서 실행 중인 알려진 안전한 애플리케이션 목록을 정의하고, 다른 애플리케이션이 실행될 때 경고합니다. 이렇게 하면 맬웨어로부터 머신을 강화할 수 있습니다. 규칙을 구성하고 유지 관리하는 프로세스를 간소화하기 위해 Security Center는 기계 학습을 사용하여 각 머신에서 실행 중인 애플리케이션을 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[적응형 애플리케이션 제어 정책의 허용 목록 규칙을 업데이트해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F123a3936-f020-408a-ba0c-47873faf1534) |Azure Security Center의 적응형 애플리케이션 제어를 통한 감사를 위해 구성된 머신 그룹의 동작 변경 내용을 모니터링합니다. Security Center는 기계 학습을 사용하여 머신에서 실행 중인 프로세스를 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. 이러한 앱은 적응형 애플리케이션 제어 정책에서 허용하도록 권장되는 앱으로 제공됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControlsUpdate_Audit.json) |
|[Microsoft 관리형 컨트롤 1218 - 최소한의 기능 \| 프로그램 실행 금지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a1d0394-b9f5-493e-9e83-563fd0ac4df8) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1218.json) |

### <a name="authorized-software--whitelisting"></a>권한이 부여된 소프트웨어/허용 목록

**ID**: NIST SP 800-53 Rev. 4 CM-7 (5)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |애플리케이션 제어를 활성화하여 머신에서 실행 중인 알려진 안전한 애플리케이션 목록을 정의하고, 다른 애플리케이션이 실행될 때 경고합니다. 이렇게 하면 맬웨어로부터 머신을 강화할 수 있습니다. 규칙을 구성하고 유지 관리하는 프로세스를 간소화하기 위해 Security Center는 기계 학습을 사용하여 각 머신에서 실행 중인 애플리케이션을 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[적응형 애플리케이션 제어 정책의 허용 목록 규칙을 업데이트해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F123a3936-f020-408a-ba0c-47873faf1534) |Azure Security Center의 적응형 애플리케이션 제어를 통한 감사를 위해 구성된 머신 그룹의 동작 변경 내용을 모니터링합니다. Security Center는 기계 학습을 사용하여 머신에서 실행 중인 프로세스를 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. 이러한 앱은 적응형 애플리케이션 제어 정책에서 허용하도록 권장되는 앱으로 제공됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControlsUpdate_Audit.json) |
|[Microsoft 관리형 컨트롤 1219 - 최소한의 기능 \| 권한이 부여된 소프트웨어/허용 목록](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a39ac75-622b-4c88-9a3f-45b7373f7ef7) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1219.json) |
|[Microsoft 관리형 컨트롤 1220 - 최소한의 기능 \| 권한이 부여된 소프트웨어/허용 목록](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc40f31a7-81e1-4130-99e5-a02ceea2a1d6) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1220.json) |
|[Microsoft 관리형 컨트롤 1221 - 최소한의 기능 \| 권한이 부여된 소프트웨어/허용 목록](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22589a07-0007-486a-86ca-95355081ae2a) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1221.json) |

### <a name="information-system-component-inventory"></a>정보 시스템 구성 요소 인벤토리

**ID**: NIST SP 800-53 Rev. 4 CM-8

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1222 - 정보 시스템 구성 요소 인벤토리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb39e62f-6bda-4558-8088-ec03d5670914) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1222.json) |
|[Microsoft 관리형 컨트롤 1223 - 정보 시스템 구성 요소 인벤토리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a1bb01-ad5a-49c1-aad3-b0c893b2ec3a) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1223.json) |

### <a name="updates-during-installations--removals"></a>설치 중 업데이트/제거

**ID**: NIST SP 800-53 Rev. 4 CM-8 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1224 - 정보 시스템 구성 요소 인벤토리 \| 설치 중 업데이트/제거](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28cfa30b-7f72-47ce-ba3b-eed26c8d2c82) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1224.json) |

### <a name="automated-maintenance"></a>자동 유지 관리

**ID**: NIST SP 800-53 Rev. 4 CM-8 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1225 - 정보 시스템 구성 요소 인벤토리 \| 자동 유지 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d096fe0-f510-4486-8b4d-d17dc230980b) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1225.json) |

### <a name="automated-unauthorized-component-detection"></a>권한 없는 구성 요소 자동 검색

**ID**: NIST SP 800-53 Rev. 4 CM-8 (3)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1226 - 정보 시스템 구성 요소 인벤토리 \| 권한 없는 구성 요소 자동 검색](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc158eb1c-ae7e-4081-8057-d527140c4e0c) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1226.json) |
|[Microsoft 관리형 컨트롤 1227 - 정보 시스템 구성 요소 인벤토리 \| 권한 없는 구성 요소 자동 검색](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03b78f5e-4877-4303-b0f4-eb6583f25768) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1227.json) |

### <a name="accountability-information"></a>책임 정보

**ID**: NIST SP 800-53 Rev. 4 CM-8 (4)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1228 - 정보 시스템 구성 요소 인벤토리 \| 책임 정보](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F39c54140-5902-4079-8bb5-ad31936fe764) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1228.json) |

### <a name="no-duplicate-accounting-of-components"></a>중복된 구성 요소 계정 없음

**ID**: NIST SP 800-53 Rev. 4 CM-8 (5)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1229 - 정보 시스템 구성 요소 인벤토리 \| 구성 요소의 이중 회계 없음](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03752212-103c-4ab8-a306-7e813022ca9d) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1229.json) |

### <a name="configuration-management-plan"></a>구성 관리 계획

**ID**: NIST SP 800-53 Rev. 4 CM-9

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1230 - 구성 관리 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11158848-f679-4e9b-aa7b-9fb07d945071) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1230.json) |
|[Microsoft 관리형 컨트롤 1231 - 구성 관리 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F244e0c05-cc45-4fe7-bf36-42dcf01f457d) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1231.json) |
|[Microsoft 관리형 컨트롤 1232 - 구성 관리 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F396ba986-eac1-4d6d-85c4-d3fda6b78272) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1232.json) |
|[Microsoft 관리형 컨트롤 1233 - 구성 관리 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d79001f-95fe-45d0-8736-f217e78c1f57) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1233.json) |

### <a name="software-usage-restrictions"></a>소프트웨어 사용 제한

**ID**: NIST SP 800-53 Rev. 4 CM-10

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |애플리케이션 제어를 활성화하여 머신에서 실행 중인 알려진 안전한 애플리케이션 목록을 정의하고, 다른 애플리케이션이 실행될 때 경고합니다. 이렇게 하면 맬웨어로부터 머신을 강화할 수 있습니다. 규칙을 구성하고 유지 관리하는 프로세스를 간소화하기 위해 Security Center는 기계 학습을 사용하여 각 머신에서 실행 중인 애플리케이션을 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[적응형 애플리케이션 제어 정책의 허용 목록 규칙을 업데이트해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F123a3936-f020-408a-ba0c-47873faf1534) |Azure Security Center의 적응형 애플리케이션 제어를 통한 감사를 위해 구성된 머신 그룹의 동작 변경 내용을 모니터링합니다. Security Center는 기계 학습을 사용하여 머신에서 실행 중인 프로세스를 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. 이러한 앱은 적응형 애플리케이션 제어 정책에서 허용하도록 권장되는 앱으로 제공됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControlsUpdate_Audit.json) |
|[Microsoft 관리형 컨트롤 1234 - 소프트웨어 사용 제한](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb293f881-361c-47ed-b997-bc4e2296bc0b) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1234.json) |
|[Microsoft 관리형 컨트롤 1235 - 소프트웨어 사용 제한](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc49c610b-ece4-44b3-988c-2172b70d6e46) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1235.json) |
|[Microsoft 관리형 컨트롤 1236 - 소프트웨어 사용 제한](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ba3ed84-c768-4e18-b87c-34ef1aff1b57) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1236.json) |

### <a name="open-source-software"></a>공개 소스 소프트웨어

**ID**: NIST SP 800-53 Rev. 4 CM-10 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1237 - 소프트웨어 사용 제한 \| 오픈 소스 소프트웨어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe80b6812-0bfa-4383-8223-cdd86a46a890) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1237.json) |

### <a name="user-installed-software"></a>사용자가 설치한 소프트웨어

**ID**: NIST SP 800-53 Rev. 4 CM-11

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |애플리케이션 제어를 활성화하여 머신에서 실행 중인 알려진 안전한 애플리케이션 목록을 정의하고, 다른 애플리케이션이 실행될 때 경고합니다. 이렇게 하면 맬웨어로부터 머신을 강화할 수 있습니다. 규칙을 구성하고 유지 관리하는 프로세스를 간소화하기 위해 Security Center는 기계 학습을 사용하여 각 머신에서 실행 중인 애플리케이션을 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[적응형 애플리케이션 제어 정책의 허용 목록 규칙을 업데이트해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F123a3936-f020-408a-ba0c-47873faf1534) |Azure Security Center의 적응형 애플리케이션 제어를 통한 감사를 위해 구성된 머신 그룹의 동작 변경 내용을 모니터링합니다. Security Center는 기계 학습을 사용하여 머신에서 실행 중인 프로세스를 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. 이러한 앱은 적응형 애플리케이션 제어 정책에서 허용하도록 권장되는 앱으로 제공됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControlsUpdate_Audit.json) |
|[Microsoft 관리형 컨트롤 1238 - 사용자가 설치한 소프트웨어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa36cedd4-3ffd-4b1f-8b18-aa71d8d87ce1) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1238.json) |
|[Microsoft 관리형 컨트롤 1239 - 사용자가 설치한 소프트웨어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0be51298-f643-4556-88af-d7db90794879) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1239.json) |
|[Microsoft 관리형 컨트롤 1240 - 사용자가 설치한 소프트웨어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F129eb39f-d79a-4503-84cd-92f036b5e429) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1240.json) |

### <a name="alerts-for-unauthorized-installations"></a>권한 없는 설치 경고

**ID**: NIST SP 800-53 Rev. 4 CM-11 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1241 - 사용자가 설치한 소프트웨어 \| 권한 없는 설치 경고](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feca4d7b2-65e2-4e04-95d4-c68606b063c3) |Microsoft에서 구현하는 구성 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1241.json) |

## <a name="contingency-planning"></a>대체 계획

### <a name="contingency-planning-policy-and-procedures"></a>대체 계획 정책 및 절차

**ID**: NIST SP 800-53 Rev. 4 CP-1

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1242 - 대체 계획 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf3b3293-667a-445e-a722-fa0b0afc0958) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1242.json) |
|[Microsoft 관리형 컨트롤 1243 - 대체 계획 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca9a4469-d6df-4ab2-a42f-1213c396f0ec) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1243.json) |

### <a name="contingency-plan"></a>대체 계획

**ID**: NIST SP 800-53 Rev. 4 CP-2

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1244 - 대체 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a13a8f8-c163-4b1b-8554-d63569dab937) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1244.json) |
|[Microsoft 관리형 컨트롤 1245 - 대체 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0e45314-57b8-4623-80cd-bbb561f59516) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1245.json) |
|[Microsoft 관리형 컨트롤 1246 - 대체 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F398eb61e-8111-40d5-a0c9-003df28f1753) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1246.json) |
|[Microsoft 관리형 컨트롤 1247 - 대체 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e666db5-b2ef-4b06-aac6-09bfce49151b) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1247.json) |
|[Microsoft 관리형 컨트롤 1248 - 대체 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50fc602d-d8e0-444b-a039-ad138ee5deb0) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1248.json) |
|[Microsoft 관리형 컨트롤 1249 - 대체 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd3bf4251-0818-42db-950b-afd5b25a51c2) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1249.json) |
|[Microsoft 관리형 컨트롤 1250 - 대체 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8de614d8-a8b7-4f70-a62a-6d37089a002c) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1250.json) |

### <a name="coordinate-with-related-plans"></a>관련 계획과 조정

**ID**: NIST SP 800-53 Rev. 4 CP-2 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1251 - 대체 계획 \| 관련 계획을 사용하여 조정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5e2b3730-8c14-4081-8893-19dbb5de7348) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1251.json) |

### <a name="capacity-planning"></a>용량 계획

**ID**: NIST SP 800-53 Rev. 4 CP-2 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1252 - 대체 계획 \| 용량 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa328fd72-8ff5-4f96-8c9c-b30ed95db4ab) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1252.json) |

### <a name="resume-essential-missions--business-functions"></a>필수 업무/비즈니스 기능 다시 시작

**ID**: NIST SP 800-53 Rev. 4 CP-2 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1253 - 대체 계획 \| 필수 업무/비즈니스 기능 다시 시작](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0afce0b3-dd9f-42bb-af28-1e4284ba8311) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1253.json) |

### <a name="resume-all-missions--business-functions"></a>모든 업무/비즈니스 기능 다시 시작

**ID**: NIST SP 800-53 Rev. 4 CP-2 (4)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1254 - 대체 계획 \| 모든 업무/비즈니스 기능 다시 시작](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F704e136a-4fe0-427c-b829-cd69957f5d2b) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1254.json) |

### <a name="continue-essential-missions--business-functions"></a>필수 업무/비즈니스 기능 계속 수행

**ID**: NIST SP 800-53 Rev. 4 CP-2 (5)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1255 - 대체 계획 \| 필수 업무/비즈니스 기능 계속 수행](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff3793f5e-937f-44f7-bfba-40647ef3efa0) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1255.json) |

### <a name="identify-critical-assets"></a>중요 자산 식별

**ID**: NIST SP 800-53 Rev. 4 CP-2 (8)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1256 - 대체 계획 \| 중요 자산 식별](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F232ab24b-810b-4640-9019-74a7d0d6a980) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1256.json) |

### <a name="contingency-training"></a>대체 교육

**ID**: NIST SP 800-53 Rev. 4 CP-3

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1257 - 대체 교육](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb958b241-4245-4bd6-bd2d-b8f0779fb543) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1257.json) |
|[Microsoft 관리형 컨트롤 1258 - 대체 교육](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7814506c-382c-4d33-a142-249dd4a0dbff) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1258.json) |
|[Microsoft 관리형 컨트롤 1259 - 대체 교육](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9e18f7-bad9-4d30-8806-a0c9d5e26208) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1259.json) |

### <a name="simulated-events"></a>시뮬레이션된 이벤트

**ID**: NIST SP 800-53 Rev. 4 CP-3 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1260 - 대체 교육 \| 시뮬레이션된 이벤트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F42254fc4-2738-4128-9613-72aaa4f0d9c3) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1260.json) |

### <a name="contingency-plan-testing"></a>대체 계획 테스트

**ID**: NIST SP 800-53 Rev. 4 CP-4

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1261 - 대체 계획 테스트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65aeceb5-a59c-4cb1-8d82-9c474be5d431) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1261.json) |
|[Microsoft 관리형 컨트롤 1262 - 대체 계획 테스트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F831e510e-db41-4c72-888e-a0621ab62265) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1262.json) |
|[Microsoft 관리형 컨트롤 1263 - 대체 계획 테스트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41472613-3b05-49f6-8fe8-525af113ce17) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1263.json) |

### <a name="coordinate-with-related-plans"></a>관련 계획과 조정

**ID**: NIST SP 800-53 Rev. 4 CP-4 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1264 - 대체 계획 테스트 \| 관련 계획을 사용하여 조정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd280d4b-50a1-42fb-a479-ece5878acf19) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1264.json) |

### <a name="alternate-processing-site"></a>대체 처리 사이트

**ID**: NIST SP 800-53 Rev. 4 CP-4 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1265 - 대체 계획 테스트 \| 대체 처리 사이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa18adb5b-1db6-4a5b-901a-7d3797d12972) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1265.json) |
|[Microsoft 관리형 컨트롤 1266 - 대체 계획 테스트 \| 대체 처리 사이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b4a3eb2-c25d-40bf-ad41-5094b6f59cee) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1266.json) |

### <a name="alternate-storage-site"></a>대체 스토리지 사이트

**ID**: NIST SP 800-53 Rev. 4 CP-6

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB를 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL을 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL을 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
|[스토리지 계정에 대해 지역 중복 스토리지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf045164-79ba-4215-8f95-f8048dc1780b) |지역 중복을 사용하여 고가용성 애플리케이션 만들기 |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |
|[Azure SQL Database에 대해 장기 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |이 정책은 장기 지역 중복 백업이 사용하도록 설정되지 않은 모든 Azure SQL Database를 감사합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |
|[Microsoft 관리형 컨트롤 1267 - 대체 스토리지 사이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e97ba1d-be5d-4953-8da4-0cccf28f4805) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1267.json) |
|[Microsoft 관리형 컨트롤 1268 - 대체 스토리지 사이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F23f6e984-3053-4dfc-ab48-543b764781f5) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1268.json) |

### <a name="separation-from-primary-site"></a>기본 사이트에서 분리

**ID**: NIST SP 800-53 Rev. 4 CP-6 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB를 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL을 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL을 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
|[스토리지 계정에 대해 지역 중복 스토리지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf045164-79ba-4215-8f95-f8048dc1780b) |지역 중복을 사용하여 고가용성 애플리케이션 만들기 |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/GeoRedundant_StorageAccounts_Audit.json) |
|[Azure SQL Database에 대해 장기 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |이 정책은 장기 지역 중복 백업이 사용하도록 설정되지 않은 모든 Azure SQL Database를 감사합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |
|[Microsoft 관리형 컨트롤 1269 - 대체 스토리지 사이트 \| 기본 사이트에서 분리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F19b9439d-865d-4474-b17d-97d2702fdb66) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1269.json) |

### <a name="recovery-time--point-objectives"></a>복구 시간/지점 목표

**ID**: NIST SP 800-53 Rev. 4 CP-6 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1270 - 대체 스토리지 사이트 \| 복구 시간/지점 목표](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53c76a39-2097-408a-b237-b279f7b4614d) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1270.json) |

### <a name="accessibility"></a>접근성

**ID**: NIST SP 800-53 Rev. 4 CP-6 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1271 - 대체 스토리지 사이트 \| 접근성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3bfb53-9c46-4010-b3db-a7ba1296dada) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1271.json) |

### <a name="alternate-processing-site"></a>대체 처리 사이트

**ID**: NIST SP 800-53 Rev. 4 CP-7

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[재해 복구가 구성되어 있지 않은 가상 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |재해 복구가 구성되지 않은 가상 머신을 감사합니다. 재해 복구에 대한 자세한 내용은 [https://aka.ms/asr-doc](../../../site-recovery/index.yml)를 참조하세요. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Microsoft 관리형 컨트롤 1272 - 대체 처리 사이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae46cf7a-e3fd-427b-9b91-44bc78e2d9d8) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1272.json) |
|[Microsoft 관리형 컨트롤 1273 - 대체 처리 사이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe77fcbf2-a1e8-44f1-860e-ed6583761e65) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1273.json) |
|[Microsoft 관리형 컨트롤 1274 - 대체 처리 사이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2aee175f-cd16-4825-939a-a85349d96210) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1274.json) |

### <a name="separation-from-primary-site"></a>기본 사이트에서 분리

**ID**: NIST SP 800-53 Rev. 4 CP-7 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1275 - 대체 처리 사이트 \| 기본 사이트에서 분리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa23d9d53-ad2e-45ef-afd5-e6d10900a737) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1275.json) |

### <a name="accessibility"></a>접근성

**ID**: NIST SP 800-53 Rev. 4 CP-7 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1276 - 대체 처리 사이트 \| 접근성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe214e563-1206-4a43-a56b-ac5880c9c571) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1276.json) |

### <a name="priority-of-service"></a>서비스 우선 순위

**ID**: NIST SP 800-53 Rev. 4 CP-7 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1277 - 대체 처리 사이트 \| 서비스 우선 순위](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdc43e829-3d50-4a0a-aa0f-428d551862aa) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1277.json) |

### <a name="preparation-for-use"></a>사용 준비

**ID**: NIST SP 800-53 Rev. 4 CP-7 (4)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1278 - 대체 처리 사이트 \| 사용 준비](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8e5ef485-9e16-4c53-a475-fbb8107eac59) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1278.json) |

### <a name="telecommunications-services"></a>통신 서비스

**ID**: NIST SP 800-53 Rev. 4 CP-8

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1279 - 통신 서비스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d00bcd6-963d-4c02-ad8e-b45fa50bf3b0) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1279.json) |

### <a name="priority-of-service-provisions"></a>서비스 프로비전 우선 순위

**ID**: NIST SP 800-53 Rev. 4 CP-8 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1280 - 통신 서비스 \| 서비스 우선 순위 규정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa108498-b3a8-4ffb-9e79-1107e76afad3) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1280.json) |
|[Microsoft 관리형 컨트롤 1281 - 통신 서비스 \| 서비스 우선 순위 규정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dc459b3-0e77-45af-8d71-cfd8c9654fe2) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1281.json) |

### <a name="single-points-of-failure"></a>단일 실패 지점

**ID**: NIST SP 800-53 Rev. 4 CP-8 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1282 - 통신 서비스 \| 단일 실패 지점](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34042a97-ec6d-4263-93d2-8c1c46823b2a) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1282.json) |

### <a name="separation-of-primary--alternate-providers"></a>기본/대체 공급자 분리

**ID**: NIST SP 800-53 Rev. 4 CP-8 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1283 - 통신 서비스 \| 기본/대체 공급자 분리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9172e76-7f56-46e9-93bf-75d69bdb5491) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1283.json) |

### <a name="provider-contingency-plan"></a>공급자 대체 계획

**ID**: NIST SP 800-53 Rev. 4 CP-8 (4)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1284 - 통신 서비스 \| 공급자 대체 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F942b3e97-6ae3-410e-a794-c9c999b97c0b) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1284.json) |
|[Microsoft 관리형 컨트롤 1285 - 통신 서비스 \| 공급자 대체 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F01f7726b-db54-45c2-bcb5-9bd7a43796ee) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1285.json) |
|[Microsoft 관리형 컨트롤 1286 - 통신 서비스 \| 공급자 대체 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4f9b47a-2116-4e6f-88db-4edbf22753f1) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1286.json) |

### <a name="information-system-backup"></a>정보 시스템 백업

**ID**: NIST SP 800-53 Rev. 4 CP-9

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Azure Backup을 사용하도록 설정하여 Azure Virtual Machines를 보호하세요. Azure Backup은 Azure를 위한 안전하고 경제적인 데이터 보호 솔루션입니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB를 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL을 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL을 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |
|[키 자격 증명 모음에 제거 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |키 자격 증명 모음을 악의적으로 삭제하면 데이터가 영구적으로 손실될 수 있습니다. 조직의 악의적인 내부자가 잠재적으로 키 자격 증명 모음을 삭제하고 제거할 수 있습니다. 제거 보호는 일시 삭제된 키 자격 증명 모음에 대해 필수 보존 기간을 적용하여 내부자 공격으로부터 보호합니다. 일시 삭제 보존 기간 동안에는 조직 또는 Microsoft 내부의 어느 누구도 키 자격 증명 모음을 제거할 수 없습니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |
|[키 자격 증명 모음에 일시 삭제를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1e66c121-a66a-4b1f-9b83-0fd99bf0fc2d) |일시 삭제를 사용하지 않고 키 자격 증명 모음을 삭제하면 키 자격 증명 모음에 저장된 모든 비밀, 키 및 인증서가 영구적으로 삭제됩니다. 키 자격 증명 모음을 실수로 삭제하면 데이터가 영구적으로 손실될 수 있습니다. 일시 삭제를 사용하면 실수로 삭제된 키 자격 증명 모음을 구성 가능한 보존 기간 동안 복구할 수 있습니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_SoftDeleteMustBeEnabled_Audit.json) |
|[Microsoft 관리형 컨트롤 1287 - 정보 시스템 백업](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F819dc6da-289d-476e-8500-7e341ef8677d) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1287.json) |
|[Microsoft 관리형 컨트롤 1288 - 정보 시스템 백업](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8d854c3b-a3e6-4ec9-9f0c-c7274dbaeb2f) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1288.json) |
|[Microsoft 관리형 컨트롤 1289 - 정보 시스템 백업](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a724864-956a-496c-b778-637cb1d762cf) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1289.json) |
|[Microsoft 관리형 컨트롤 1290 - 정보 시스템 백업](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F92f85ce9-17b7-49ea-85ee-ea7271ea6b82) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1290.json) |

### <a name="testing-for-reliability--integrity"></a>안정성/무결성 테스트

**ID**: NIST SP 800-53 Rev. 4 CP-9 (1)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1291 - 정보 시스템 백업 \| 안정성/무결성 테스트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8fd073-9c85-4ee2-a9d0-2e4ec9eb8912) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1291.json) |

### <a name="test-restoration-using-sampling"></a>샘플링을 사용하여 복원 테스트

**ID**: NIST SP 800-53 Rev. 4 CP-9 (2)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1292 - 정보 시스템 백업 \| 샘플링을 사용하여 복원 테스트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd03516cf-0293-489f-9b32-a18f2a79f836) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1292.json) |

### <a name="separate-storage-for-critical-information"></a>중요 정보를 위한 별도의 스토리지

**ID**: NIST SP 800-53 Rev. 4 CP-9 (3)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1293 - 정보 시스템 백업 \| 중요 정보를 위한 별도의 스토리지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87f7cd82-2e45-4d0f-9e2f-586b0962d142) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1293.json) |

### <a name="transfer-to-alternate-storage-site"></a>대체 스토리지 사이트로 전송

**ID**: NIST SP 800-53 Rev. 4 CP-9 (5)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1294 - 정보 시스템 백업 \| 대체 스토리지 사이트로 전송](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49dbe627-2c1e-438c-979e-dd7a39bbf81d) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1294.json) |

### <a name="information-system-recovery-and-reconstitution"></a>정보 시스템 복구 및 재구성

**ID**: NIST SP 800-53 Rev. 4 CP-10

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1295 - 정보 시스템 복구 및 재구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa895fbdb-204d-4302-9689-0a59dc42b3d9) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1295.json) |

### <a name="transaction-recovery"></a>트랜잭션 복구

**ID**: NIST SP 800-53 Rev. 4 CP-10 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1296 - 정보 시스템 복구 및 재구성 \| 트랜잭션 복구](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe57b98a0-a011-4956-a79d-5d17ed8b8e48) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1296.json) |

### <a name="restore-within-time-period"></a>기간 내 복원

**ID**: NIST SP 800-53 Rev. 4 CP-10 (4)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1297 - 정보 시스템 복구 및 재구성 \| 기간 내 복구](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93fd8af1-c161-4bae-9ba9-f62731f76439) |Microsoft에서 구현하는 대체 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1297.json) |

## <a name="identification-and-authentication"></a>식별 및 인증

### <a name="identification-and-authentication-policy-and-procedures"></a>식별 및 인증 정책과 절차

**ID**: NIST SP 800-53 Rev. 4 IA-1

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1298 - 식별 및 인증 정책과 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1dc784b5-4895-4d27-9d40-a06b032bd1ee) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1298.json) |
|[Microsoft 관리형 컨트롤 1299 - 식별 및 인증 정책과 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4e54f7-9ab0-4bae-b6cc-457809948a89) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1299.json) |

### <a name="identification-and-authentication-organizational-users"></a>식별 및 인증(조직 사용자)

**ID**: NIST SP 800-53 Rev. 4 IA-2

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 서버에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |SQL 서버에 대한 Azure Active Directory 관리자 프로비전을 감사하여 Azure AD 인증을 활성화합니다. Azure AD 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[Cognitive Services 계정은 로컬 인증 방법을 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |로컬 인증 방법을 사용하지 않도록 설정하면 Cognitive Services 계정에 인증 전용 Azure Active Directory ID가 필요하므로 보안이 강화됩니다. [https://aka.ms/cs/auth](../../../cognitive-services/authentication.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[API 앱에서 관리 ID를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |인증 보안 강화를 위해 관리 ID 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[함수 앱에서 관리 ID를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |인증 보안 강화를 위해 관리 ID 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[웹앱에서 관리 ID를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |인증 보안 강화를 위해 관리 ID 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |
|[구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |계정 또는 리소스 위반을 방지하려면 쓰기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |계정 또는 리소스 위반을 방지하려면 소유자 권한이 있는 모든 구독 계정에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |계정 또는 리소스 위반을 방지하려면 읽기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |
|[Microsoft 관리형 컨트롤 1300 - 식별 및 인증(조직 사용자)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99deec7d-5526-472e-b07c-3645a792026a) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1300.json) |
|[Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Service Fabric에서 Azure Active Directory를 통한 클라이언트 인증의 사용만 감사 |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|[관리 인증서 대신 서비스 주체를 사용하여 구독을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6646a0bd-e110-40ca-bb97-84fcee63c414) |관리 인증서를 사용하면 해당 인증서로 인증된 사람은 누구나 연결된 구독을 관리할 수 있습니다. 구독을 보다 안전하게 관리하려면 Resource Manager와 함께 서비스 주체를 사용하여 인증서 손상의 영향을 제한하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UseServicePrincipalToProtectSubscriptions.json) |

### <a name="network-access-to-privileged-accounts"></a>권한 있는 계정에 대한 네트워크 액세스

**ID**: NIST SP 800-53 Rev. 4 IA-2 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |계정 또는 리소스 위반을 방지하려면 쓰기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |계정 또는 리소스 위반을 방지하려면 소유자 권한이 있는 모든 구독 계정에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |
|[Microsoft 관리형 컨트롤 1301 - 식별 및 인증(조직 사용자) \| 권한 있는 계정에 대한 네트워크 액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8e0cc-ac23-468b-abe4-a8a1cc6d7a08) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1301.json) |

### <a name="network-access-to-non-privileged-accounts"></a>권한 없는 계정에 대한 네트워크 액세스

**ID**: NIST SP 800-53 Rev. 4 IA-2 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |계정 또는 리소스 위반을 방지하려면 읽기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |
|[Microsoft 관리형 컨트롤 1302 - 식별 및 인증(조직 사용자) \| 권한 없는 계정에 대한 네트워크 액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09828c65-e323-422b-9774-9d5c646124da) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1302.json) |

### <a name="local-access-to-privileged-accounts"></a>권한 있는 계정에 대한 로컬 액세스

**ID**: NIST SP 800-53 Rev. 4 IA-2 (3)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1303 - 식별 및 인증(조직 사용자) \| 권한 있는 계정에 대한 로컬 액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F80ca0a27-918a-4604-af9e-723a27ee51e8) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1303.json) |

### <a name="local-access-to-non-privileged-accounts"></a>권한 없는 계정에 대한 로컬 액세스

**ID**: NIST SP 800-53 Rev. 4 IA-2 (4)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1304 - 식별 및 인증(조직 사용자) \| 권한 없는 계정에 대한 로컬 액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6ca71be3-16cb-4d39-8b50-7f8fd5e2f11b) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1304.json) |

### <a name="group-authentication"></a>그룹 인증

**ID**: NIST SP 800-53 Rev. 4 IA-2 (5)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1305 - 식별 및 인증(조직 사용자) \| 그룹 인증](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d9166a8-1722-4b8f-847c-2cf3f2618b3d) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1305.json) |

### <a name="network-access-to-privileged-accounts---replay-resistant"></a>권한 있는 계정에 대한 네트워크 액세스 - 재생 방지

**ID**: NIST SP 800-53 Rev. 4 IA-2 (8)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1306 - 식별 및 인증(조직 사용자) \| 권한 있는 계정에 대한 네트워크 액세스 - 재생](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcafc6c3c-5fc5-4c5e-a99b-a0ccb1d34eff) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1306.json) |

### <a name="network-access-to-non-privileged-accounts---replay-resistant"></a>권한 없는 계정에 대한 네트워크 액세스 - 재생 방지

**ID**: NIST SP 800-53 Rev. 4 IA-2 (9)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1307 - 식별 및 인증(조직 사용자) \| 권한 없는 계정에 대한 네트워크 액세스 - 재생](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F84e622c8-4bed-417c-84c6-b2fb0dd73682) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1307.json) |

### <a name="remote-access---separate-device"></a>원격 액세스 - 별도의 디바이스

**ID**: NIST SP 800-53 Rev. 4 IA-2 (11)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1308 - 식별 및 인증(조직 사용자) \| 원격 액세스 - 별도의 디바이스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81817e1c-5347-48dd-965a-40159d008229) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1308.json) |

### <a name="acceptance-of-piv-credentials"></a>PIV 자격 증명 수락

**ID**: NIST SP 800-53 Rev. 4 IA-2 (12)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1309 - 식별 및 인증(조직 사용자) \| Piv 자격 증명 수락](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff355d62b-39a8-4ba3-abf7-90f71cb3b000) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1309.json) |

### <a name="device-identification-and-authentication"></a>디바이스 식별 및 인증

**ID**: NIST SP 800-53 Rev. 4 IA-3

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1310 - 디바이스 식별 및 인증](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F450d7ede-823d-4931-a99d-57f6a38807dc) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1310.json) |

### <a name="identifier-management"></a>식별자 관리

**ID**: NIST SP 800-53 Rev. 4 IA-4

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 서버에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |SQL 서버에 대한 Azure Active Directory 관리자 프로비전을 감사하여 Azure AD 인증을 활성화합니다. Azure AD 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |
|[Cognitive Services 계정은 로컬 인증 방법을 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71ef260a-8f18-47b7-abcb-62d0673d94dc) |로컬 인증 방법을 사용하지 않도록 설정하면 Cognitive Services 계정에 인증 전용 Azure Active Directory ID가 필요하므로 보안이 강화됩니다. [https://aka.ms/cs/auth](../../../cognitive-services/authentication.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisableLocalAuth_Audit.json) |
|[API 앱에서 관리 ID를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |인증 보안 강화를 위해 관리 ID 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[함수 앱에서 관리 ID를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |인증 보안 강화를 위해 관리 ID 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[웹앱에서 관리 ID를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |인증 보안 강화를 위해 관리 ID 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |
|[Microsoft 관리형 컨트롤 1311 - 식별자 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe7568697-0c9e-4ea3-9cec-9e567d14f3c6) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1311.json) |
|[Microsoft 관리형 컨트롤 1312 - 식별자 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d6a5968-9eef-4c18-8534-376790ab7274) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1312.json) |
|[Microsoft 관리형 컨트롤 1313 - 식별자 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36220f5b-79a1-4cdb-8c74-2d2449f9a510) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1313.json) |
|[Microsoft 관리형 컨트롤 1314 - 식별자 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef0c8530-efd9-45b8-b753-f03083d06295) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1314.json) |
|[Microsoft 관리형 컨트롤 1315 - 식별자 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3aa87116-f1a1-4edb-bfbf-14e036f8d454) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1315.json) |
|[Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Service Fabric에서 Azure Active Directory를 통한 클라이언트 인증의 사용만 감사 |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|[관리 인증서 대신 서비스 주체를 사용하여 구독을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6646a0bd-e110-40ca-bb97-84fcee63c414) |관리 인증서를 사용하면 해당 인증서로 인증된 사람은 누구나 연결된 구독을 관리할 수 있습니다. 구독을 보다 안전하게 관리하려면 Resource Manager와 함께 서비스 주체를 사용하여 인증서 손상의 영향을 제한하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UseServicePrincipalToProtectSubscriptions.json) |

### <a name="identify-user-status"></a>사용자 상태 식별

**ID**: NIST SP 800-53 Rev. 4 IA-4 (4)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1316 - 식별자 관리\| 사용자 상태 식별](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8ce14753-66e5-465d-9841-26ef55c09c0d) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1316.json) |

### <a name="authenticator-management"></a>인증자 관리

**ID**: NIST SP 800-53 Rev. 4 IA-5

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[시스템 할당 관리 ID를 추가하여 ID가 없는 가상 머신에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |이 정책은 게스트 구성에서 지원되지만 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[시스템이 할당한 관리 ID를 추가하여 사용자가 할당한 ID가 있는 VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |이 정책은 게스트 구성에서 지원되고 사용자 할당 ID가 하나 이상 있지만 시스템 할당 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[passwd 파일 권한이 0644로 설정되지 않은 Linux 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6955644-301c-44b5-a4c4-528577de6861) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. passwd 파일 권한이 0644로 설정되지 않은 Linux 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword121_AINE.json) |
|[해독 가능한 암호화를 사용하여 암호를 저장하지 않는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 해독 가능한 암호화를 사용하여 암호를 저장하지 않는 Windows 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[Linux 머신에 대한 인증에 SSH 키가 필요함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F630c64f9-8b6b-4c64-b511-6544ceff6fd6) |SSH 자체에서 암호화된 연결을 제공하지만 SSH와 함께 암호를 사용하면 VM은 여전히 무차별 암호 대입 공격에 취약합니다. SSH를 통해 Azure Linux 가상 머신에 인증하는 가장 안전한 옵션은 SSH 키라고도 하는 퍼블릭-프라이빗 키 쌍을 사용하는 것입니다. 자세한 정보: [https://docs.microsoft.com/azure/virtual-machines/linux/create-ssh-keys-detailed](../../../virtual-machines/linux/create-ssh-keys-detailed.md). |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxNoPasswordForSSH_AINE.json) |
|[인증서에 지정된 최대 유효 기간이 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a075868-4c26-42ef-914c-5bc007359560) |키 자격 증명 모음 내에서 인증서가 유효한 최대 시간을 지정하여 조직의 규정 준수 요구 사항을 관리합니다. |감사, 거부, 사용 안 함 |[2.1.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Certificates_ValidityPeriod.json) |
|[Linux 게스트 구성 확장을 배포하여 Linux VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Linux 가상 머신에 Linux 게스트 구성 확장을 배포합니다. Linux 게스트 구성 확장은 모든 Linux 게스트 구성 할당의 필수 조건이며 Linux 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Windows 게스트 구성 확장을 배포하여 Windows VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Windows 가상 머신에 Windows 게스트 구성 확장을 배포합니다. Windows 게스트 구성 확장은 모든 Windows 게스트 구성 할당의 필수 조건이며 Windows 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Key Vault 키에는 만료 날짜가 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |암호화 키에는 정의된 만료 날짜가 있어야 하며 영구적이지 않아야 합니다. 영구적으로 유효한 키는 잠재적인 공격자에게 키를 손상시킬 수 있는 시간을 더 많이 제공합니다. 보안상 암호화 키에 대한 만료 날짜를 설정하는 것이 좋습니다. |감사, 거부, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |
|[Key Vault 비밀에는 만료 날짜가 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |비밀에는 정의된 만료 날짜가 있어야 하며 영구적이지 않아야 합니다. 영구적으로 유효한 비밀은 잠재적인 공격자에게 비밀을 손상시킬 수 있는 시간을 더 많이 제공합니다. 보안상 비밀에 대한 만료 날짜를 설정하는 것이 좋습니다. |감사, 거부, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |
|[Microsoft 관리형 컨트롤 1317 - 인증자 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8877f519-c166-47b7-81b7-8a8eb4ff3775) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1317.json) |
|[Microsoft 관리형 컨트롤 1318 - 인증자 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffced5fda-3bdb-4d73-bfea-0e2c80428b66) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1318.json) |
|[Microsoft 관리형 컨트롤 1319 - 인증자 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F66f7ae57-5560-4fc5-85c9-659f204e7a42) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1319.json) |
|[Microsoft 관리형 컨트롤 1320 - 인증자 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f54c732-71d4-4f93-a696-4e373eca3a77) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1320.json) |
|[Microsoft 관리형 컨트롤 1321 - 인증자 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb627cc6-3a9d-46b5-96b7-5fca49178a37) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1321.json) |
|[Microsoft 관리형 컨트롤 1322 - 인증자 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d1d971e-467e-4278-9633-c74c3d4fecc4) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1322.json) |
|[Microsoft 관리형 컨트롤 1323 - 인증자 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabe8f70b-680f-470c-9b86-a7edfb664ecc) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1323.json) |
|[Microsoft 관리형 컨트롤 1324 - 인증자 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cfea2b3-7f77-497e-ac20-0752f2ff6eee) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1324.json) |
|[Microsoft 관리형 컨트롤 1325 - 인증자 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1845796a-7581-49b2-ae20-443121538e19) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1325.json) |
|[Microsoft 관리형 컨트롤 1326 - 인증자 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8605fc00-1bf5-4fb3-984e-c95cec4f231d) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1326.json) |

### <a name="password-based-authentication"></a>암호 기반 인증

**ID**: NIST SP 800-53 Rev. 4 IA-5 (1)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[시스템 할당 관리 ID를 추가하여 ID가 없는 가상 머신에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e) |이 정책은 게스트 구성에서 지원되지만 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenNone_Prerequisite.json) |
|[시스템이 할당한 관리 ID를 추가하여 사용자가 할당한 ID가 있는 VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6) |이 정책은 게스트 구성에서 지원되고 사용자 할당 ID가 하나 이상 있지만 시스템 할당 관리 ID가 없는 Azure에서 호스트되는 가상 머신에 시스템 할당 관리 ID를 추가합니다. 시스템 할당 관리 ID는 모든 게스트 구성 할당에 대한 필수 구성 요소이며 게스트 구성 정책 정의를 사용하기 전에 머신에 추가해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |수정 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AddSystemIdentityWhenUser_Prerequisite.json) |
|[passwd 파일 권한이 0644로 설정되지 않은 Linux 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6955644-301c-44b5-a4c4-528577de6861) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. passwd 파일 권한이 0644로 설정되지 않은 Linux 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_LinuxPassword121_AINE.json) |
|[이전 24개의 암호를 다시 사용할 수 있는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b054a0d-39e2-4d53-bea3-9734cad2c69b) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 이전 24개의 암호를 다시 사용할 수 있는 Windows 머신인 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEnforce_AINE.json) |
|[암호 사용 최대 기간이 70일로 설정되지 않은 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ceb8dc2-559c-478b-a15b-733fbf1e3738) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 암호 사용 최대 기간이 70일로 설정되지 않은 Windows 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMaximumPassword_AINE.json) |
|[암호 사용 최소 기간이 1일로 설정되지 않은 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F237b38db-ca4d-4259-9e47-7882441ca2c0) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 암호 사용 최소 기간이 1일로 설정되지 않은 Windows 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsMinimumPassword_AINE.json) |
|[암호 복잡성 설정이 활성화되지 않는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf16e0bb-31e1-4646-8202-60a235cc7e74) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 암호 복잡성 설정이 활성화되지 않는 Windows 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordComplexity_AINE.json) |
|[암호 최소 길이가 14자로 제한되지 않는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2d0e922-65d0-40c4-8f87-ea6da2d307a2) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 암호 최소 길이가 14자로 제한되지 않는 Windows 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordLength_AINE.json) |
|[해독 가능한 암호화를 사용하여 암호를 저장하지 않는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda0f98fe-a24b-4ad5-af69-bd0400233661) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 해독 가능한 암호화를 사용하여 암호를 저장하지 않는 Windows 머신의 경우 머신은 비규격입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordEncryption_AINE.json) |
|[Linux 게스트 구성 확장을 배포하여 Linux VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F331e8ea8-378a-410f-a2e5-ae22f38bb0da) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Linux 가상 머신에 Linux 게스트 구성 확장을 배포합니다. Linux 게스트 구성 확장은 모든 Linux 게스트 구성 할당의 필수 조건이며 Linux 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionLinux_Prerequisite.json) |
|[Windows 게스트 구성 확장을 배포하여 Windows VM에서 게스트 구성 할당을 사용하도록 설정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F385f5831-96d4-41db-9a3c-cd3af78aaae6) |이 정책은 게스트 구성에서 지원되는 Azure에서 호스트되는 Windows 가상 머신에 Windows 게스트 구성 확장을 배포합니다. Windows 게스트 구성 확장은 모든 Windows 게스트 구성 할당의 필수 조건이며 Windows 게스트 구성 정책 정의를 사용하기 전에 머신에 배포해야 합니다. 게스트 구성에 대한 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |deployIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_DeployExtensionWindows_Prerequisite.json) |
|[Microsoft 관리형 컨트롤 1327 - 인증자 관리 \| 암호 기반 인증](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03188d8f-1ae5-4fe1-974d-2d7d32ef937d) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1327.json) |
|[Microsoft 관리형 컨트롤 1328 - 인증자 관리 \| 암호 기반 인증](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff5c66fdc-3d02-4034-9db5-ba57802609de) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1328.json) |
|[Microsoft 관리형 컨트롤 1329 - 인증자 관리 \| 암호 기반 인증](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F498f6234-3e20-4b6a-a880-cbd646d973bd) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1329.json) |
|[Microsoft 관리형 컨트롤 1330 - 인증자 관리 \| 암호 기반 인증](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff75cedb2-5def-4b31-973e-b69e8c7bd031) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1330.json) |
|[Microsoft 관리형 컨트롤 1331 - 인증자 관리 \| 암호 기반 인증](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05460fe2-301f-4ed1-8174-d62c8bb92ff4) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1331.json) |
|[Microsoft 관리형 컨트롤 1332 - 인증자 관리 \| 암호 기반 인증](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068260be-a5e6-4b0a-a430-cd27071c226a) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1332.json) |

### <a name="pki-based-authentication"></a>Pki 기반 인증

**ID**: NIST SP 800-53 Rev. 4 IA-5 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1333 - 인증자 관리 \| Pki 기반 인증](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3298d6bf-4bc6-4278-a95d-f7ef3ac6e594) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1333.json) |
|[Microsoft 관리형 컨트롤 1334 - 인증자 관리 \| Pki 기반 인증](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44bfdadc-8c2e-4c30-9c99-f005986fabcd) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1334.json) |
|[Microsoft 관리형 컨트롤 1335 - 인증자 관리 \| Pki 기반 인증](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F382016f3-d4ba-4e15-9716-55077ec4dc2a) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1335.json) |
|[Microsoft 관리형 컨트롤 1336 - 인증자 관리 \| Pki 기반 인증](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F77f56280-e367-432a-a3b9-8ca2aa636a26) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1336.json) |

### <a name="in-person-or-trusted-third-party-registration"></a>직접 또는 신뢰할 수 있는 타사 등록

**ID**: NIST SP 800-53 Rev. 4 IA-5 (3)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1337 - 인증자 관리 \| 직접 또는 신뢰할 수 있는 타사 등록](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F463e5220-3f79-4e24-a63f-343e4096cd22) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1337.json) |

### <a name="automated-support-for-password-strength-determination"></a>암호 강도 결정에 대한 자동화된 지원

**ID**: NIST SP 800-53 Rev. 4 IA-5 (4)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1338 - 인증자 관리 \| 암호 강도 결정에 대한 지원 자동화](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c59a207-6aed-41dc-83a2-e1ff66e4a4db) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1338.json) |

### <a name="protection-of-authenticators"></a>인증자 보호

**ID**: NIST SP 800-53 Rev. 4 IA-5 (6)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1339 - 인증자 관리 \| 인증자 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F367ae386-db7f-4167-b672-984ff86277c0) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1339.json) |

### <a name="no-embedded-unencrypted-static-authenticators"></a>암호화되지 않은 정적 인증자가 포함되지 않음

**ID**: NIST SP 800-53 Rev. 4 IA-5 (7)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1340 - 인증자 관리 \| 암호화되지 않은 정적 인증자가 포함되지 않음](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe51ff84b-e5ea-408f-b651-2ecc2933e4c6) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1340.json) |

### <a name="multiple-information-system-accounts"></a>여러 정보 시스템 계정

**ID**: NIST SP 800-53 Rev. 4 IA-5 (8)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1341 - 인증자 관리 \| 여러 정보 시스템 계정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34cb7e92-fe4c-4826-b51e-8cd203fa5d35) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1341.json) |

### <a name="hardware-token-based-authentication"></a>하드웨어 토큰 기반 인증

**ID**: NIST SP 800-53 Rev. 4 IA-5 (11)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1342 - 인증자 관리 \| 하드웨어 토큰 기반 인증](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F283a4e29-69d5-4c94-b99e-29acf003c899) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1342.json) |

### <a name="expiration-of-cached-authenticators"></a>캐시된 인증자 만료

**ID**: NIST SP 800-53 Rev. 4 IA-5 (13)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1343 - 인증자 관리 \| 캐시된 인증자 만료](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c251a55-31eb-4e53-99c6-e9c43c393ac2) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1343.json) |

### <a name="authenticator-feedback"></a>인증자 피드백

**ID**: NIST SP 800-53 Rev. 4 IA-6

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1344 - 인증자 피드백](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c895fe7-2d8e-43a2-838c-3a533a5b355e) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1344.json) |

### <a name="cryptographic-module-authentication"></a>암호화 모듈 인증

**ID**: NIST SP 800-53 Rev. 4 IA-7

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1345 - 암호화 모듈 인증](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff86aa129-7c07-4aa4-bbf5-792d93ffd9ea) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1345.json) |

### <a name="identification-and-authentication-non-organizational-users"></a>식별 및 인증(비 조직 사용자)

**ID**: NIST SP 800-53 Rev. 4 IA-8

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1346 - 식별 및 인증(비 조직 사용자)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F464dc8ce-2200-4720-87a5-dc5952924cc6) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1346.json) |

### <a name="acceptance-of-piv-credentials-from-other-agencies"></a>다른 기관의 PIV 자격 증명 수락

**ID**: NIST SP 800-53 Rev. 4 IA-8 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1347 - 식별 및 인증(비 조직 사용자) \| 다른 기관의 PIV 자격 증명 수락](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F131a2706-61e9-4916-a164-00e052056462) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1347.json) |

### <a name="acceptance-of-third-party-credentials"></a>타사 자격 증명 수락

**ID**: NIST SP 800-53 Rev. 4 IA-8 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1348 - 식별 및 인증(비 조직 사용자) \| 타사 자격 증명 수락](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F855ced56-417b-4d74-9d5f-dd1bc81e22d6) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1348.json) |

### <a name="use-of-ficam-approved-products"></a>Ficam 승인을 받은 제품 사용

**ID**: NIST SP 800-53 Rev. 4 IA-8 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1349 - 식별 및 인증(비 조직 사용자) \| FICAM 승인을 받은 제품 사용](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17641f70-94cd-4a5d-a613-3d1143e20e34) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1349.json) |

### <a name="use-of-ficam-issued-profiles"></a>Ficam에서 발급한 프로필 사용

**ID**: NIST SP 800-53 Rev. 4 IA-8 (4)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1350 - 식별 및 인증(비 조직 사용자) \| FICAM에서 발행한 프로필 사용](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd77fd943-6ba6-4a21-ba07-22b03e347cc4) |Microsoft에서 구현하는 식별 및 인증 컨트롤 Authentication control |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1350.json) |

## <a name="incident-response"></a>사고 대응

### <a name="incident-response-policy-and-procedures"></a>인시던트 응답 정책 및 절차

**ID**: NIST SP 800-53 Rev. 4 IR-1

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1351 - 인시던트 대응 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbcfb6683-05e5-4ce6-9723-c3fbe9896bdd) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1351.json) |
|[Microsoft 관리형 컨트롤 1352 - 인시던트 대응 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F518cb545-bfa8-43f8-a108-3b7d5037469a) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1352.json) |

### <a name="incident-response-training"></a>인시던트 응답 교육

**ID**: NIST SP 800-53 Rev. 4 IR-2

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1353 - 인시던트 대응 교육](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc785ad59-f78f-44ad-9a7f-d1202318c748) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1353.json) |
|[Microsoft 관리형 컨트롤 1354 - 인시던트 대응 교육](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9fd92c17-163a-4511-bb96-bbb476449796) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1354.json) |
|[Microsoft 관리형 컨트롤 1355 - 인시던트 대응 교육](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90e01f69-3074-4de8-ade7-0fef3e7d83e0) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1355.json) |

### <a name="simulated-events"></a>시뮬레이션된 이벤트

**ID**: NIST SP 800-53 Rev. 4 IR-2 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1356 - 인시던트 대응 교육 \| 시뮬레이션된 이벤트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8829f8f5-e8be-441e-85c9-85b72a5d0ef3) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1356.json) |

### <a name="automated-training-environments"></a>자동화된 교육 환경

**ID**: NIST SP 800-53 Rev. 4 IR-2 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1357 - 인시던트 대응 교육 \| 자동화된 교육 환경](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe4213689-05e8-4241-9d4e-8dd1cdafd105) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1357.json) |

### <a name="incident-response-testing"></a>인시던트 응답 테스트

**ID**: NIST SP 800-53 Rev. 4 IR-3

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1358 - 인시던트 대응 테스트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feffbaeef-5bf4-400d-895e-ef8cbc0e64c7) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1358.json) |

### <a name="coordination-with-related-plans"></a>관련 계획과의 조정

**ID**: NIST SP 800-53 Rev. 4 IR-3 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1359 - 인시던트 대응 테스트 \| 관련 계획에 맞게 조정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47bc7ea0-7d13-4f7c-a154-b903f7194253) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1359.json) |

### <a name="incident-handling"></a>인시던트 처리

**ID**: NIST SP 800-53 Rev. 4 IR-4

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender for App Service를 사용해야 합니다](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service는 클라우드의 규모와 Azure가 클라우드 공급자로서 가지고 있는 가시성을 활용하여 일반적인 웹앱 공격을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure SQL Database 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[컨테이너 레지스트리용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |컨테이너 레지스트리용 Azure Defender는 지난 30일 내에 끌어와서 레지스트리에 푸시한 이미지의 취약성을 검사하고, 각 이미지에 대해 발견한 자세한 내용을 공개합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS는 Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링하여 클라우드 리소스에 대한 추가 보호 계층을 제공합니다. Azure Defender는 DNS 계층에서 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)에서 Azure Defender for DNS의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault는 키 자격 증명 모음 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하여 보안 인텔리전스에 추가 보호 계층을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager는 조직의 리소스 관리 작업을 자동으로 모니터링합니다. Azure Defender는 위협을 감지하고 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)에서 Azure Defender for Resource Manager의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[머신의 SQL 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[보호되지 않는 Azure SQL 서버에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[스토리지용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[심각도가 높은 경고에 대해 이메일 알림을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |구독 중 하나에 잠재적인 보안 위반이 있을 때 조직의 관련 인원에게 알리려면 Security Center에서 심각도가 높은 경고에 대한 이메일 알림을 사용하도록 설정합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |
|[심각도가 높은 경고에 대해 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |구독에 잠재적인 보안 위반이 있을 때 구독 소유자에게 알리려면 Security Center에서 심각도가 높은 경고에 대해 구독 소유자에게 이메일로 알리도록 설정합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |
|[Microsoft 관리형 컨트롤 1360 - 인시던트 처리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbe5b05e7-0b82-4ebc-9eda-25e447b1a41e) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1360.json) |
|[Microsoft 관리형 컨트롤 1361 - 인시던트 처리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ed3be1-7276-4452-9a5d-e4168565ac67) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1361.json) |
|[Microsoft 관리형 컨트롤 1362 - 인시던트 처리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5d169442-d6ef-439b-8dca-46c2c3248214) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1362.json) |
|[구독에 보안 문제에 대한 연락처 이메일 주소가 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |구독 중 하나에 잠재적인 보안 위반이 있을 때 조직의 관련 인원에게 알리려면 Security Center에서 이메일 알림을 받을 수 있도록 보안 연락처를 설정합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="automated-incident-handling-processes"></a>자동화된 인시던트 처리 프로세스

**ID**: NIST SP 800-53 Rev. 4 IR-4 (1)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1363 - 인시던트 처리 \| 자동화된 인시던트 처리 프로세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea3e8156-89a1-45b1-8bd6-938abc79fdfd) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1363.json) |

### <a name="dynamic-reconfiguration"></a>동적 재구성

**ID**: NIST SP 800-53 Rev. 4 IR-4 (2)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1364 - 인시던트 처리\| 동적 재구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c615c2a-dc83-4dda-8220-abce7b50c9bc) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1364.json) |

### <a name="continuity-of-operations"></a>작업 연속성

**ID**: NIST SP 800-53 Rev. 4 IR-4 (3)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1365 - 인시던트 처리 \| 작업 연속성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4116891d-72f7-46ee-911c-8056cc8dcbd5) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1365.json) |

### <a name="information-correlation"></a>정보 상관 관계

**ID**: NIST SP 800-53 Rev. 4 IR-4 (4)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1366 - 인시던트 처리\| 정보 상관 관계](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06c45c30-ae44-4f0f-82be-41331da911cc) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1366.json) |

### <a name="insider-threats---specific-capabilities"></a>내부자 위협 - 특정 기능

**ID**: NIST SP 800-53 Rev. 4 IR-4 (6)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1367 - 인시던트 처리 \| 내부자 위협 - 특정 기능](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F435b2547-6374-4f87-b42d-6e8dbe6ae62a) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1367.json) |

### <a name="correlation-with-external-organizations"></a>외부 조직과의 상관 관계

**ID**: NIST SP 800-53 Rev. 4 IR-4 (8)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1368 - 인시던트 처리 \| 외부 조직과의 상관 관계](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F465f32da-0ace-4603-8d1b-7be5a3a702de) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1368.json) |

### <a name="incident-monitoring"></a>인시던트 모니터링

**ID**: NIST SP 800-53 Rev. 4 IR-5

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender for App Service를 사용해야 합니다](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service는 클라우드의 규모와 Azure가 클라우드 공급자로서 가지고 있는 가시성을 활용하여 일반적인 웹앱 공격을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure SQL Database 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[컨테이너 레지스트리용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |컨테이너 레지스트리용 Azure Defender는 지난 30일 내에 끌어와서 레지스트리에 푸시한 이미지의 취약성을 검사하고, 각 이미지에 대해 발견한 자세한 내용을 공개합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS는 Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링하여 클라우드 리소스에 대한 추가 보호 계층을 제공합니다. Azure Defender는 DNS 계층에서 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)에서 Azure Defender for DNS의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault는 키 자격 증명 모음 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하여 보안 인텔리전스에 추가 보호 계층을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager는 조직의 리소스 관리 작업을 자동으로 모니터링합니다. Azure Defender는 위협을 감지하고 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)에서 Azure Defender for Resource Manager의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[머신의 SQL 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[보호되지 않는 Azure SQL 서버에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[스토리지용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[심각도가 높은 경고에 대해 이메일 알림을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |구독 중 하나에 잠재적인 보안 위반이 있을 때 조직의 관련 인원에게 알리려면 Security Center에서 심각도가 높은 경고에 대한 이메일 알림을 사용하도록 설정합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |
|[심각도가 높은 경고에 대해 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |구독에 잠재적인 보안 위반이 있을 때 구독 소유자에게 알리려면 Security Center에서 심각도가 높은 경고에 대해 구독 소유자에게 이메일로 알리도록 설정합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |
|[Microsoft 관리형 컨트롤 1369 - 인시던트 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18cc35ed-a429-486d-8d59-cb47e87304ed) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1369.json) |
|[구독에 보안 문제에 대한 연락처 이메일 주소가 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |구독 중 하나에 잠재적인 보안 위반이 있을 때 조직의 관련 인원에게 알리려면 Security Center에서 이메일 알림을 받을 수 있도록 보안 연락처를 설정합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="automated-tracking--data-collection--analysis"></a>자동화된 추적/데이터 수집/분석

**ID**: NIST SP 800-53 Rev. 4 IR-5 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1370 - 인시던트 모니터링 \| 자동 추적/데이터 수집/분석](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F924e1b2d-c502-478f-bfdb-a7e09a0d5c01) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1370.json) |

### <a name="incident-reporting"></a>인시던트 보고

**ID**: NIST SP 800-53 Rev. 4 IR-6

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1371 - 인시던트 보고](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9447f354-2c85-4700-93b3-ecdc6cb6a417) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1371.json) |
|[Microsoft 관리형 컨트롤 1372 - 인시던트 보고](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F25b96717-c912-4c00-9143-4e487f411726) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1372.json) |

### <a name="automated-reporting"></a>자동화된 보고

**ID**: NIST SP 800-53 Rev. 4 IR-6 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1373 - 인시던트 보고 \| 자동 보고](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4cca950f-c3b7-492a-8e8f-ea39663c14f9) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1373.json) |

### <a name="vulnerabilities-related-to-incidents"></a>인시던트 관련 취약성

**ID**: NIST SP 800-53 Rev. 4 IR-6 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[심각도가 높은 경고에 대해 이메일 알림을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |구독 중 하나에 잠재적인 보안 위반이 있을 때 조직의 관련 인원에게 알리려면 Security Center에서 심각도가 높은 경고에 대한 이메일 알림을 사용하도록 설정합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |
|[심각도가 높은 경고에 대해 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |구독에 잠재적인 보안 위반이 있을 때 구독 소유자에게 알리려면 Security Center에서 심각도가 높은 경고에 대해 구독 소유자에게 이메일로 알리도록 설정합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |
|[구독에 보안 문제에 대한 연락처 이메일 주소가 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |구독 중 하나에 잠재적인 보안 위반이 있을 때 조직의 관련 인원에게 알리려면 Security Center에서 이메일 알림을 받을 수 있도록 보안 연락처를 설정합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="incident-response-assistance"></a>인시던트 응답 지원

**ID**: NIST SP 800-53 Rev. 4 IR-7

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1374 - 인시던트 대응 지원](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc5c8616-52ef-4e5e-8000-491634ed9249) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1374.json) |

### <a name="automation-support-for-availability-of-information--support"></a>정보 가용성에 대한 자동화된 지원/지원

**ID**: NIST SP 800-53 Rev. 4 IR-7 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1375 - 인시던트 대응 지원 \| 정보의 가용성에 대한 자동 지원/지원](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F00379355-8932-4b52-b63a-3bc6daf3451a) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1375.json) |

### <a name="coordination-with-external-providers"></a>외부 공급자와의 조정

**ID**: NIST SP 800-53 Rev. 4 IR-7 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1376 - 인시던트 대응 지원 \| 외부 공급자와 조율](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F493a95f3-f2e3-47d0-af02-65e6d6decc2f) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1376.json) |
|[Microsoft 관리형 컨트롤 1377 - 인시던트 대응 지원 \| 외부 공급자와 조율](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68434bd1-e14b-4031-9edb-a4adf5f84a67) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1377.json) |

### <a name="incident-response-plan"></a>인시던트 응답 계획

**ID**: NIST SP 800-53 Rev. 4 IR-8

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1378 - 인시던트 대응 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F97fceb70-6983-42d0-9331-18ad8253184d) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1378.json) |
|[Microsoft 관리형 컨트롤 1379 - 인시던트 대응 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9442dd2c-a07f-46cd-b55a-553b66ba47ca) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1379.json) |
|[Microsoft 관리형 컨트롤 1380 - 인시던트 대응 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4319b7e-ea8d-42ff-8a67-ccd462972827) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1380.json) |
|[Microsoft 관리형 컨트롤 1381 - 인시던트 대응 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe5368258-9684-4567-8126-269f34e65eab) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1381.json) |
|[Microsoft 관리형 컨트롤 1382 - 인시던트 대응 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F841392b3-40da-4473-b328-4cde49db67b3) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1382.json) |
|[Microsoft 관리형 컨트롤 1383 - 인시던트 대응 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4558451-e16a-4d2d-a066-fe12a6282bb9) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1383.json) |

### <a name="information-spillage-response"></a>정보 유출 대응

**ID**: NIST SP 800-53 Rev. 4 IR-9

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1384 - 정보 유출 대응](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79fbc228-461c-4a45-9004-a865ca0728a7) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1384.json) |
|[Microsoft 관리형 컨트롤 1385 - 정보 유출 대응](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3e495e65-8663-49ca-9b38-9f45e800bc58) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1385.json) |
|[Microsoft 관리형 컨트롤 1386 - 정보 유출 대응](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5120193e-91fd-4f9d-bc6d-194f94734065) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1386.json) |
|[Microsoft 관리형 컨트롤 1387 - 정보 유출 대응](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3007185-3857-43a9-8237-06ca94f1084c) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1387.json) |
|[Microsoft 관리형 컨트롤 1388 - 정보 유출 대응](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c7c575a-d4c5-4f6f-bd49-dee97a8cba55) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1388.json) |
|[Microsoft 관리형 컨트롤 1389 - 정보 유출 대응](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39e6fda-ae70-4891-a739-be7bba6d1062) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1389.json) |

### <a name="responsible-personnel"></a>책임자

**ID**: NIST SP 800-53 Rev. 4 IR-9 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1390 - 정보 유출 대응 \| 책임자](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3b65b63-09ec-4cb5-8028-7dd324d10eb0) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1390.json) |

### <a name="training"></a>학습

**ID**: NIST SP 800-53 Rev. 4 IR-9 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1391 - 정보 유출 대응 \| 교육](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd6ac1a1-660e-4810-baa8-74e868e2ed47) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1391.json) |

### <a name="post-spill-operations"></a>스필 후 작업

**ID**: NIST SP 800-53 Rev. 4 IR-9 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1392 - 정보 유출 대응 \| 유출 후 작업](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86dc819f-15e1-43f9-a271-41ae58d4cecc) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1392.json) |

### <a name="exposure-to-unauthorized-personnel"></a>권한 없는 사람에게 노출

**ID**: NIST SP 800-53 Rev. 4 IR-9 (4)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1393 - 정보 유출 대응 \| 권한 없는 사람에게 노출](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F731856d8-1598-4b75-92de-7d46235747c0) |Microsoft에서 구현하는 인시던트 대응 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1393.json) |

## <a name="maintenance"></a>유지 관리

### <a name="system-maintenance-policy-and-procedures"></a>시스템 유지 관리 정책 및 절차

**ID**: NIST SP 800-53 Rev. 4 MA-1

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1394 - 시스템 유지 관리 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4db56f68-3f50-45ab-88f3-ca46f5379a94) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1394.json) |
|[Microsoft 관리형 컨트롤 1395 - 시스템 유지 관리 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7207a023-a517-41c5-9df2-09d4c6845a05) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1395.json) |

### <a name="controlled-maintenance"></a>제어된 유지 관리

**ID**: NIST SP 800-53 Rev. 4 MA-2

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1396 - 제어된 유지 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F276af98f-4ff9-4e69-99fb-c9b2452fb85f) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1396.json) |
|[Microsoft 관리형 컨트롤 1397 - 제어된 유지 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391af4ab-1117-46b9-b2c7-78bbd5cd995b) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1397.json) |
|[Microsoft 관리형 컨트롤 1398 - 제어된 유지 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F443e8f3d-b51a-45d8-95a7-18b0e42f4dc4) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1398.json) |
|[Microsoft 관리형 컨트롤 1399 - 제어된 유지 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2256e638-eb23-480f-9e15-6cf1af0a76b3) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1399.json) |
|[Microsoft 관리형 컨트롤 1400 - 제어된 유지 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa96d5098-a604-4cdf-90b1-ef6449a27424) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1400.json) |
|[Microsoft 관리형 컨트롤 1401 - 제어된 유지 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb78ee928-e3c1-4569-ad97-9f8c4b629847) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1401.json) |

### <a name="automated-maintenance-activities"></a>자동화된 유지 관리 작업

**ID**: NIST SP 800-53 Rev. 4 MA-2 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1402 - 제어된 유지 관리 \| 자동 유지 관리 작업](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a560d32-8075-4fec-9615-9f7c853f4ea9) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1402.json) |
|[Microsoft 관리형 컨트롤 1403 - 제어된 유지 관리 \| 자동 유지 관리 작업](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F57149289-d52b-4f40-9fe6-5233c1ef80f7) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1403.json) |

### <a name="maintenance-tools"></a>유지 관리 도구

**ID**: NIST SP 800-53 Rev. 4 MA-3

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1404 - 유지 관리 도구](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13d8f903-0cd6-449f-a172-50f6579c182b) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1404.json) |

### <a name="inspect-tools"></a>도구 검사

**ID**: NIST SP 800-53 Rev. 4 MA-3 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1405 - 유지 관리 도구 \| 도구 검사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe1a0bf3-409a-4b00-b60d-0b1f917f7e7b) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1405.json) |

### <a name="inspect-media"></a>미디어 검사

**ID**: NIST SP 800-53 Rev. 4 MA-3 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1406 - 유지 관리 도구 \| 미디어 검사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa0f5339c-9292-43aa-a0bc-d27c6b8e30aa) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1406.json) |

### <a name="prevent-unauthorized-removal"></a>무단 제거 방지

**ID**: NIST SP 800-53 Rev. 4 MA-3 (3)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1407 - 유지 관리 도구 \| 무단 제거 방지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fff9fbd83-1d8d-4b41-aac2-94cb44b33976) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1407.json) |
|[Microsoft 관리형 컨트롤 1408 - 유지 관리 도구 \| 무단 제거 방지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5f56ac6-4bb2-4086-bc41-ad76344ba2c2) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1408.json) |
|[Microsoft 관리형 컨트롤 1409 - 유지 관리 도구 \| 무단 제거 방지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd1880188-e51a-4772-b2ab-68f5e8bd27f6) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1409.json) |
|[Microsoft 관리형 컨트롤 1410 - 유지 관리 도구 \| 무단 제거 방지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2596a9f-e59f-420d-9625-6e0b536348be) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1410.json) |

### <a name="nonlocal-maintenance"></a>비로컬 유지 관리

**ID**: NIST SP 800-53 Rev. 4 MA-4

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1411 - 비로컬 유지 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F898d4fe8-f743-4333-86b7-0c9245d93e7d) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1411.json) |
|[Microsoft 관리형 컨트롤 1412 - 비로컬 유지 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3492d949-0dbb-4589-88b3-7b59601cc764) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1412.json) |
|[Microsoft 관리형 컨트롤 1413 - 비로컬 유지 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faeedddb6-6bc0-42d5-809b-80048033419d) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1413.json) |
|[Microsoft 관리형 컨트롤 1414 - 비로컬 유지 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce63a52-e47b-4ae2-adbb-6e40d967f9e6) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1414.json) |
|[Microsoft 관리형 컨트롤 1415 - 비로컬 유지 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F61a1dd98-b259-4840-abd5-fbba7ee0da83) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1415.json) |

### <a name="document-nonlocal-maintenance"></a>비 로컬 유지 관리 문서화

**ID**: NIST SP 800-53 Rev. 4 MA-4 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1416 - 비로컬 유지 관리 \| 비로컬 유지 관리 문서화](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38dfd8a3-5290-4099-88b7-4081f4c4d8ae) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1416.json) |

### <a name="comparable-security--sanitization"></a>비교 가능한 보안/삭제

**ID**: NIST SP 800-53 Rev. 4 MA-4 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1417 - 비로컬 유지 관리 \| 비교 가능한 보안/삭제](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7522ed84-70d5-4181-afc0-21e50b1b6d0e) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1417.json) |
|[Microsoft 관리형 컨트롤 1418 - 비로컬 유지 관리 \| 비교 가능한 보안/삭제](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28e633fd-284e-4ea7-88b4-02ca157ed713) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1418.json) |

### <a name="cryptographic-protection"></a>암호화 보호

**ID**: NIST SP 800-53 Rev. 4 MA-4 (6)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1419 - 비로컬 유지 관리 \| 암호화 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6747bf9-2b97-45b8-b162-3c8becb9937d) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1419.json) |

### <a name="maintenance-personnel"></a>유지 관리 담당자

**ID**: NIST SP 800-53 Rev. 4 MA-5

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1420 - 유지 관리 담당자](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05ae08cc-a282-413b-90c7-21a2c60b8404) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1420.json) |
|[Microsoft 관리형 컨트롤 1421 - 유지 관리 담당자](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe539caaa-da8c-41b8-9e1e-449851e2f7a6) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1421.json) |
|[Microsoft 관리형 컨트롤 1422 - 유지 관리 담당자](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea556850-838d-4a37-8ce5-9d7642f95e11) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1422.json) |

### <a name="individuals-without-appropriate-access"></a>적절한 액세스 권한이 없는 개인

**ID**: NIST SP 800-53 Rev. 4 MA-5 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1423 - 유지 관리 담당자 \| 적절한 액세스 권한이 없는 개인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7741669e-d4f6-485a-83cb-e70ce7cbbc20) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1423.json) |
|[Microsoft 관리형 컨트롤 1424 - 유지 관리 담당자 \| 적절한 액세스 권한이 없는 개인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf55fc87-48e1-4676-a2f8-d9a8cf993283) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1424.json) |

### <a name="timely-maintenance"></a>시기 적절한 유지 관리

**ID**: NIST SP 800-53 Rev. 4 MA-6

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1425 - 시기 적절한 유지 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5983d99c-f39b-4c32-a3dc-170f19f6941b) |Microsoft에서 구현하는 유지 관리 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1425.json) |

## <a name="media-protection"></a>미디어 보호

### <a name="media-protection-policy-and-procedures"></a>미디어 보호 정책 및 절차

**ID**: NIST SP 800-53 Rev. 4 MP-1

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1426 - 미디어 보호 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21f639bc-f42b-46b1-8f40-7a2a389c291a) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1426.json) |
|[Microsoft 관리형 컨트롤 1427 - 미디어 보호 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbc90e44f-d83f-4bdf-900f-3d5eb4111b31) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1427.json) |

### <a name="media-access"></a>미디어 액세스

**ID**: NIST SP 800-53 Rev. 4 MP-2

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1428 - 미디어 액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a77fcc7-b8d8-451a-ab52-56197913c0c7) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1428.json) |

### <a name="media-marking"></a>미디어 표시

**ID**: NIST SP 800-53 Rev. 4 MP-3

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1429 - 미디어 표시](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb07c9b24-729e-4e85-95fc-f224d2d08a80) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1429.json) |
|[Microsoft 관리형 컨트롤 1430 - 미디어 표시](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f559588-5e53-4b14-a7c4-85d28ebc2234) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1430.json) |

### <a name="media-storage"></a>미디어 스토리지

**ID**: NIST SP 800-53 Rev. 4 MP-4

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1431 - 미디어 스토리지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7173c52-2b99-4696-a576-63dd5f970ef4) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1431.json) |
|[Microsoft 관리형 컨트롤 1432 - 미디어 스토리지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1140e542-b80d-4048-af45-3f7245be274b) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1432.json) |

### <a name="media-transport"></a>미디어 전송

**ID**: NIST SP 800-53 Rev. 4 MP-5

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1433 - 미디어 전송](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b879b41-2728-41c5-ad24-9ee2c37cbe65) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1433.json) |
|[Microsoft 관리형 컨트롤 1434 - 미디어 전송](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c18f06b-a68d-41c3-8863-b8cd3acb5f8f) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1434.json) |
|[Microsoft 관리형 컨트롤 1435 - 미디어 전송](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa8d221b-d130-4637-ba16-501e666628bb) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1435.json) |
|[Microsoft 관리형 컨트롤 1436 - 미디어 전송](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F28aab8b4-74fd-4b7c-9080-5a7be525d574) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1436.json) |

### <a name="cryptographic-protection"></a>암호화 보호

**ID**: NIST SP 800-53 Rev. 4 MP-5 (4)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1437 - 미디어 전송 \| 암호화 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d1eb6ed-bf13-4046-b993-b9e2aef0f76c) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1437.json) |

### <a name="media-sanitization"></a>미디어 삭제

**ID**: NIST SP 800-53 Rev. 4 MP-6

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1438 - 미디어 삭제](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40fcc635-52a2-4dbc-9523-80a1f4aa1de6) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1438.json) |
|[Microsoft 관리형 컨트롤 1439 - 미디어 삭제](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdce72873-c5f1-47c3-9b4f-6b8207fd5a45) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1439.json) |

### <a name="review--approve--track--document--verify"></a>검토/승인/추적/문서화/확인

**ID**: NIST SP 800-53 Rev. 4 MP-6 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1440 - 미디어 삭제 \| 검토/승인/추적/문서화/확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F881299bf-2a5b-4686-a1b2-321d33679953) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1440.json) |

### <a name="equipment-testing"></a>장비 테스트

**ID**: NIST SP 800-53 Rev. 4 MP-6 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1441 - 미디어 삭제 \| 장비 테스트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6519d7f3-e8a2-4ff3-a935-9a9497152ad7) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1441.json) |

### <a name="nondestructive-techniques"></a>비파괴 기술

**ID**: NIST SP 800-53 Rev. 4 MP-6 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1442 - 미디어 삭제 \| 비파괴 기술](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f26049b-2c5a-4841-9ff3-d48a26aae475) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1442.json) |

### <a name="media-use"></a>미디어 사용

**ID**: NIST SP 800-53 Rev. 4 MP-7

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1443 - 미디어 사용](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd0ec6fa-a2e7-4361-aee4-a8688659a9ed) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1443.json) |

### <a name="prohibit-use-without-owner"></a>소유자 없을 때 사용 금지

**ID**: NIST SP 800-53 Rev. 4 MP-7 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1444 - 미디어 사용 \| 소유자 없을 때 사용 금지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F666143df-f5e0-45bd-b554-135f0f93e44e) |Microsoft에서 구현하는 미디어 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1444.json) |

## <a name="physical-and-environmental-protection"></a>물리적 및 환경적 보호

### <a name="physical-and-environmental-protection-policy-and-procedures"></a>물리적 및 환경적 보호 정책 및 절차

**ID**: NIST SP 800-53 Rev. 4 PE-1

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1445 - 물리적 및 환경적 보호 정책과 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32d07d59-2716-4972-b37b-214a67ac4a37) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1445.json) |
|[Microsoft 관리형 컨트롤 1446 - 물리적 및 환경적 보호 정책과 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf6850fe-abba-468e-9ef4-d09ec7d983cd) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1446.json) |

### <a name="physical-access-authorizations"></a>물리적 액세스 권한 부여

**ID**: NIST SP 800-53 Rev. 4 PE-2

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1447 - 물리적 액세스 권한 부여](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9783a99-98fe-4a95-873f-29613309fe9a) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1447.json) |
|[Microsoft 관리형 컨트롤 1448 - 물리적 액세스 권한 부여](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F825d6494-e583-42f2-a3f2-6458e6f0004f) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1448.json) |
|[Microsoft 관리형 컨트롤 1449 - 물리적 액세스 권한 부여](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff784d3b0-5f2b-49b7-b9f3-00ba8653ced5) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1449.json) |
|[Microsoft 관리형 컨트롤 1450 - 물리적 액세스 권한 부여](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F134d7a13-ba3e-41e2-b236-91bfcfa24e01) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1450.json) |

### <a name="physical-access-control"></a>물리적 액세스 제어

**ID**: NIST SP 800-53 Rev. 4 PE-3

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1451 - 물리적 액세스 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3f1e5a3-25c1-4476-8cb6-3955031f8e65) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1451.json) |
|[Microsoft 관리형 컨트롤 1452 - 물리적 액세스 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82c76455-4d3f-4e09-a654-22e592107e74) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1452.json) |
|[Microsoft 관리형 컨트롤 1453 - 물리적 액세스 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9693b564-3008-42bc-9d5d-9c7fe198c011) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1453.json) |
|[Microsoft 관리형 컨트롤 1454 - 물리적 액세스 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad58985d-ab32-4f99-8bd3-b7e134c90229) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1454.json) |
|[Microsoft 관리형 컨트롤 1455 - 물리적 액세스 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F068a88d4-e520-434e-baf0-9005a8164e6a) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1455.json) |
|[Microsoft 관리형 컨트롤 1456 - 물리적 액세스 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F733ba9e3-9e7c-440a-a7aa-6196a90a2870) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1456.json) |
|[Microsoft 관리형 컨트롤 1457 - 물리적 액세스 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff2d9d3e6-8886-4305-865d-639163e5c305) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1457.json) |

### <a name="information-system-access"></a>정보 시스템 액세스

**ID**: NIST SP 800-53 Rev. 4 PE-3 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1458 - 물리적 액세스 제어 \| 정보 시스템 액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c19ceb7-56e9-4488-8ddb-b1eb3aa6d203) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1458.json) |

### <a name="access-control-for-transmission-medium"></a>전송 매체에 대한 액세스 제어

**ID**: NIST SP 800-53 Rev. 4 PE-4

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1459 - 전송 매체에 대한 액세스 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75cc73c7-5cdb-479d-a06f-7b4d0dbb1da0) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1459.json) |

### <a name="access-control-for-output-devices"></a>출력 디바이스에 대한 액세스 제어

**ID**: NIST SP 800-53 Rev. 4 PE-5

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1460 - 출력 디바이스에 대한 액세스 제어](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6f3ce1bb-4f77-4695-8355-70b08d54fdda) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1460.json) |

### <a name="monitoring-physical-access"></a>물리적 액세스 모니터링

**ID**: NIST SP 800-53 Rev. 4 PE-6

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1461 - 물리적 액세스 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faafef03e-fea8-470b-88fa-54bd1fcd7064) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1461.json) |
|[Microsoft 관리형 컨트롤 1462 - 물리적 액세스 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9b1f3a9a-13a1-4b40-8420-36bca6fd8c02) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1462.json) |
|[Microsoft 관리형 컨트롤 1463 - 물리적 액세스 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F59721f87-ae25-4db0-a2a4-77cc5b25d495) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1463.json) |

### <a name="intrusion-alarms--surveillance-equipment"></a>침입 경보/감시 장비

**ID**: NIST SP 800-53 Rev. 4 PE-6 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1464 - 물리적 액세스 모니터링 \| 침입 경보/감시 장비](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41256567-1795-4684-b00b-a1308ce43cac) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1464.json) |

### <a name="monitoring-physical-access-to-information-systems"></a>정보 시스템에 대한 물리적 액세스 모니터링

**ID**: NIST SP 800-53 Rev. 4 PE-6 (4)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1465 - 물리적 액세스 모니터링 \| 정보 시스템에 대한 물리적 액세스 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe6e41554-86b5-4537-9f7f-4fc41a1d1640) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1465.json) |

### <a name="visitor-access-records"></a>방문자 액세스 기록

**ID**: NIST SP 800-53 Rev. 4 PE-8

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1466 - 방문자 액세스 기록](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d943a9c-a6f1-401f-a792-740cdb09c451) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1466.json) |
|[Microsoft 관리형 컨트롤 1467 - 방문자 액세스 기록](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5350cbf9-8bdd-4904-b22a-e88be84ca49d) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1467.json) |

### <a name="automated-records-maintenance--review"></a>자동화된 기록 유지 관리/검토

**ID**: NIST SP 800-53 Rev. 4 PE-8 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1468 - 방문자 액세스 기록 \| 자동화된 기록 유지 관리/검토](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F75603f96-80a1-4757-991d-5a1221765ddd) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1468.json) |

### <a name="power-equipment-and-cabling"></a>전원 장비 및 케이블 연결

**ID**: NIST SP 800-53 Rev. 4 PE-9

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1469 - 전원 장비 및 케이블 연결](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff509c5b6-0de0-4a4e-9b2e-cd9cbf3a58fd) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1469.json) |

### <a name="emergency-shutoff"></a>긴급 차단

**ID**: NIST SP 800-53 Rev. 4 PE-10

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1470 - 긴급 차단](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc89ba09f-2e0f-44d0-8095-65b05bd151ef) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1470.json) |
|[Microsoft 관리형 컨트롤 1471 - 긴급 차단](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7dd0e9ce-1772-41fb-a50a-99977071f916) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1471.json) |
|[Microsoft 관리형 컨트롤 1472 - 긴급 차단](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef869332-921d-4c28-9402-3be73e6e50c8) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1472.json) |

### <a name="emergency-power"></a>긴급 전원

**ID**: NIST SP 800-53 Rev. 4 PE-11

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1473 - 긴급 전원](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd7047705-d719-46a7-8bb0-76ad233eba71) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1473.json) |

### <a name="long-term-alternate-power-supply---minimal-operational-capability"></a>장기 대체 전원 공급 장치 - 최소 작동 기능

**ID**: NIST SP 800-53 Rev. 4 PE-11 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1474 - 긴급 전원 \| 장기 대체 전원 공급 장치 - 최소 작동 기능](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F03ad326e-d7a1-44b1-9a76-e17492efc9e4) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1474.json) |

### <a name="emergency-lighting"></a>비상 조명

**ID**: NIST SP 800-53 Rev. 4 PE-12

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1475 - 비상 조명](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34a63848-30cf-4081-937e-ce1a1c885501) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1475.json) |

### <a name="fire-protection"></a>화재 방지

**ID**: NIST SP 800-53 Rev. 4 PE-13

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1476 - 화재 방지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f3c4ac2-3e35-4906-a80b-473b12a622d7) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1476.json) |

### <a name="detection-devices--systems"></a>검색 디바이스/시스템

**ID**: NIST SP 800-53 Rev. 4 PE-13 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1477 - 화재 방지 \| 감지 디바이스/시스템](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4862a63c-6c74-4a9d-a221-89af3c374503) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1477.json) |

### <a name="suppression-devices--systems"></a>진압 디바이스/시스템

**ID**: NIST SP 800-53 Rev. 4 PE-13 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1478 - 화재 방지 \| 진압 디바이스/시스템](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff997df46-cfbb-4cc8-aac8-3fecdaf6a183) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1478.json) |

### <a name="automatic-fire-suppression"></a>자동 화재 진압

**ID**: NIST SP 800-53 Rev. 4 PE-13 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1479 - 화재 방지 \| 자동 화재 진압](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe327b072-281d-4f75-9c28-4216e5d72f26) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1479.json) |

### <a name="temperature-and-humidity-controls"></a>온도 및 습도 제어

**ID**: NIST SP 800-53 Rev. 4 PE-14

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1480 - 온도 및 습도 컨트롤](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18a767cc-1947-4338-a240-bc058c81164f) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1480.json) |
|[Microsoft 관리형 컨트롤 1481 - 온도 및 습도 컨트롤](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F717a1c78-a267-4f56-ac58-ee6c54dc4339) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1481.json) |

### <a name="monitoring-with-alarms--notifications"></a>경보/알림을 사용하여 모니터링

**ID**: NIST SP 800-53 Rev. 4 PE-14 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1482 - 온도 및 습도 컨트롤 \| 경보/알림을 사용하여 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9df4277e-8c88-4d5c-9b1a-541d53d15d7b) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1482.json) |

### <a name="water-damage-protection"></a>물 피해 방지

**ID**: NIST SP 800-53 Rev. 4 PE-15

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1483 - 물 피해 방지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5cb81060-3c8a-4968-bcdc-395a1801f6c1) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1483.json) |

### <a name="automation-support"></a>자동화 지원

**ID**: NIST SP 800-53 Rev. 4 PE-15 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1484 - 물 피해 방지 \| 자동 지원](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F486b006a-3653-45e8-b41c-a052d3e05456) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1484.json) |

### <a name="delivery-and-removal"></a>배달 및 제거

**ID**: NIST SP 800-53 Rev. 4 PE-16

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1485 - 배달 및 제거](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50301354-95d0-4a11-8af5-8039ecf6d38b) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1485.json) |

### <a name="alternate-work-site"></a>대체 작업 사이트

**ID**: NIST SP 800-53 Rev. 4 PE-17

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1486 - 대체 작업 사이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb790345-a51f-43de-934e-98dbfaf9dca5) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1486.json) |
|[Microsoft 관리형 컨트롤 1487 - 대체 작업 사이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c3371d-c30c-4f58-abd9-30b8a8199571) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1487.json) |
|[Microsoft 관리형 컨트롤 1488 - 대체 작업 사이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd8ef30eb-a44f-47af-8524-ac19a36d41d2) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1488.json) |

### <a name="location-of-information-system-components"></a>정보 시스템 구성 요소의 위치

**ID**: NIST SP 800-53 Rev. 4 PE-18

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1489 - 정보 시스템 구성 요소 위치](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0a794f-1444-4c96-9534-e35fc8c39c91) |Microsoft에서 구현하는 물리적 및 환경적 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1489.json) |

## <a name="planning"></a>계획

### <a name="security-planning-policy-and-procedures"></a>보안 계획 정책 및 절차

**ID**: NIST SP 800-53 Rev. 4 PL-1

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1490 - 보안 계획 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e61da80-0957-4892-b70c-609d5eaafb6b) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1490.json) |
|[Microsoft 관리형 컨트롤 1491 - 보안 계획 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1571dd40-dafc-4ef4-8f55-16eba27efc7b) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1491.json) |

### <a name="system-security-plan"></a>시스템 보안 계획

**ID**: NIST SP 800-53 Rev. 4 PL-2

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1492 - 시스템 보안 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ad5f307-e045-46f7-8214-5bdb7e973737) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1492.json) |
|[Microsoft 관리형 컨트롤 1493 - 시스템 보안 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22b469b3-fccf-42da-aa3b-a28e6fb113ce) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1493.json) |
|[Microsoft 관리형 컨트롤 1494 - 시스템 보안 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed09d84-3311-4853-8b67-2b55dfa33d09) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1494.json) |
|[Microsoft 관리형 컨트롤 1495 - 시스템 보안 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4978d0e-a596-48e7-9f8c-bbf52554ce8d) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1495.json) |
|[Microsoft 관리형 컨트롤 1496 - 시스템 보안 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ca96127-2f87-46ab-a4fc-0d2a786df1c8) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1496.json) |

### <a name="plan--coordinate-with-other-organizational-entities"></a>다른 조직 주체와 계획/조정

**ID**: NIST SP 800-53 Rev. 4 PL-2 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1497 - 시스템 보안 계획 \| 다른 조직 주체와 함께 계획/조정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e3c5583-1729-4d36-8771-59c32f090a22) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1497.json) |

### <a name="rules-of-behavior"></a>행동 규칙

**ID**: NIST SP 800-53 Rev. 4 PL-4

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1498 - 동작 규칙](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F633988b9-cf2f-4323-8394-f0d2af9cd6e1) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1498.json) |
|[Microsoft 관리형 컨트롤 1499 - 동작 규칙](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe59671ab-9720-4ee2-9c60-170e8c82251e) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1499.json) |
|[Microsoft 관리형 컨트롤 1500 - 동작 규칙](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9dd5b241-03cb-47d3-a5cd-4b89f9c53c92) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1500.json) |
|[Microsoft 관리형 컨트롤 1501 - 동작 규칙](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88817b58-8472-4f6c-81fa-58ce42b67f51) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1501.json) |

### <a name="social-media-and-networking-restrictions"></a>소셜 미디어 및 네트워킹 제한 사항

**ID**: NIST SP 800-53 Rev. 4 PL-4 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1502 - 행동 규칙 \| 소셜 미디어 및 네트워킹 제한 사항](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe901375c-8f01-4ac8-9183-d5312f47fe63) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1502.json) |

### <a name="information-security-architecture"></a>정보 보안 아키텍처

**ID**: NIST SP 800-53 Rev. 4 PL-8

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1503 - 정보 보안 아키텍처](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc1fa9c2f-d439-4ab9-8b83-81fb1934f81d) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1503.json) |
|[Microsoft 관리형 컨트롤 1504 - 정보 보안 아키텍처](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e7c35d0-12d4-4e0c-80a2-8a352537aefd) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1504.json) |
|[Microsoft 관리형 컨트롤 1505 - 정보 보안 아키텍처](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F813a10a7-3943-4fe3-8678-00dc52db5490) |Microsoft에서 구현하는 계획 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1505.json) |

## <a name="personnel-security"></a>직원 보안

### <a name="personnel-security-policy-and-procedures"></a>직원 보안 정책 및 절차

**ID**: NIST SP 800-53 Rev. 4 PS-1

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1506 - 직원 보안 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d2ff17-d604-4dd9-b607-9ecf63f28ad2) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1506.json) |
|[Microsoft 관리형 컨트롤 1507 - 직원 보안 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ccd1bf-e7ad-4851-93ce-6ec817469c1e) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1507.json) |

### <a name="position-risk-designation"></a>위험 위치 지정

**ID**: NIST SP 800-53 Rev. 4 PS-2

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1508 - 위험 위치 지정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F76f500cc-4bca-4583-bda1-6d084dc21086) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1508.json) |
|[Microsoft 관리형 컨트롤 1509 - 위험 위치 지정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70792197-9bfc-4813-905a-bd33993e327f) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1509.json) |
|[Microsoft 관리형 컨트롤 1510 - 위험 위치 지정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F79da5b09-0e7e-499e-adda-141b069c7998) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1510.json) |

### <a name="personnel-screening"></a>직원 차단

**ID**: NIST SP 800-53 Rev. 4 PS-3

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1511 - 직원 차단](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9eae324-d327-4539-9293-b48e122465f8) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1511.json) |
|[Microsoft 관리형 컨트롤 1512 - 직원 차단](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5a8324ad-f599-429b-aaed-f9c6e8c987a8) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1512.json) |

### <a name="information-with-special-protection-measures"></a>특별한 보호 조치가 있는 정보

**ID**: NIST SP 800-53 Rev. 4 PS-3 (3)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1513 - 직원 차단 \| 특별한 보호 조치가 있는 정보](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc416970d-b12b-49eb-8af4-fb144cd7c290) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1513.json) |
|[Microsoft 관리형 컨트롤 1514 - 직원 차단 \| 특별한 보호 조치가 있는 정보](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ed5ca00-0e43-434e-a018-7aab91461ba7) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1514.json) |

### <a name="personnel-termination"></a>직원 고용 종료

**ID**: NIST SP 800-53 Rev. 4 PS-4

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1515 - 직원 고용 종료](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02dd141a-a2b2-49a7-bcbd-ca31142f6211) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1515.json) |
|[Microsoft 관리형 컨트롤 1516 - 직원 고용 종료](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fda3cd269-156f-435b-b472-c3af34c032ed) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1516.json) |
|[Microsoft 관리형 컨트롤 1517 - 직원 고용 종료](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8f5ad423-50d6-4617-b058-69908f5586c9) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1517.json) |
|[Microsoft 관리형 컨트롤 1518 - 직원 고용 종료](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d58f734-c052-40e9-8b2f-a1c2bff0b815) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1518.json) |
|[Microsoft 관리형 컨트롤 1519 - 직원 고용 종료](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f13915a-324c-4ab8-b45c-2eefeeefb098) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1519.json) |
|[Microsoft 관리형 컨트롤 1520 - 직원 고용 종료](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f2c513b-eb16-463b-b469-c10e5fa94f0a) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1520.json) |

### <a name="automated-notification"></a>자동화된 알림

**ID**: NIST SP 800-53 Rev. 4 PS-4 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1521 - 직원 고용 종료 \| 자동 알림](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cbddf9c-a3aa-4330-a0f5-4c0c1f1862e5) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1521.json) |

### <a name="personnel-transfer"></a>직원 이전

**ID**: NIST SP 800-53 Rev. 4 PS-5

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1522 - 직원 이전](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F38b470cc-f939-4a15-80e0-9f0c74f2e2c9) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1522.json) |
|[Microsoft 관리형 컨트롤 1523 - 직원 이전](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5577a310-2551-49c8-803b-36e0d5e55601) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1523.json) |
|[Microsoft 관리형 컨트롤 1524 - 직원 이전](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72f1cb4e-2439-4fe8-88ea-b8671ce3c268) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1524.json) |
|[Microsoft 관리형 컨트롤 1525 - 직원 이전](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9be2f688-7a61-45e3-8230-e1ec93893f66) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1525.json) |

### <a name="access-agreements"></a>액세스 계약

**ID**: NIST SP 800-53 Rev. 4 PS-6

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1526 - 액세스 계약](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F953e6261-a05a-44fd-8246-000e1a3edbb9) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1526.json) |
|[Microsoft 관리형 컨트롤 1527 - 액세스 계약](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2823de66-332f-4bfd-94a3-3eb036cd3b67) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1527.json) |
|[Microsoft 관리형 컨트롤 1528 - 액세스 계약](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdeb9797c-22f8-40e8-b342-a84003c924e6) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1528.json) |

### <a name="third-party-personnel-security"></a>타사 직원 보안

**ID**: NIST SP 800-53 Rev. 4 PS-7

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1529 - 타사 직원 보안](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd74fdc92-1cb8-4a34-9978-8556425cd14c) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1529.json) |
|[Microsoft 관리형 컨트롤 1530 - 타사 직원 보안](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e8f9566-29f1-49cd-b61f-f8628a3cf993) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1530.json) |
|[Microsoft 관리형 컨트롤 1531 - 타사 직원 보안](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0643e0c-eee5-4113-8684-c608d05c5236) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1531.json) |
|[Microsoft 관리형 컨트롤 1532 - 타사 직원 보안](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2c66299-9017-4d95-8040-8bdbf7901d52) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1532.json) |
|[Microsoft 관리형 컨트롤 1533 - 타사 직원 보안](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbba2a036-fb3b-4261-b1be-a13dfb5fbcaa) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1533.json) |

### <a name="personnel-sanctions"></a>직원 제재

**ID**: NIST SP 800-53 Rev. 4 PS-8

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1534 - 직원 제재](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b2b263e-cd05-4488-bcbf-4debec7a17d9) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1534.json) |
|[Microsoft 관리형 컨트롤 1535 - 직원 제재](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9a165d2-967d-4733-8399-1074270dae2e) |Microsoft에서 구현하는 직원 보안 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1535.json) |

## <a name="risk-assessment"></a>위험 평가

### <a name="risk-assessment-policy-and-procedures"></a>위험 평가 정책 및 절차

**ID**: NIST SP 800-53 Rev. 4 RA-1

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1536 - 위험 평가 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e40d9de-2ad4-4cb5-8945-23143326a502) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1536.json) |
|[Microsoft 관리형 컨트롤 1537 - 위험 평가 정책 및 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb19454ca-0d70-42c0-acf5-ea1c1e5726d1) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1537.json) |

### <a name="security-categorization"></a>보안 분류

**ID**: NIST SP 800-53 Rev. 4 RA-2

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1538 - 보안 분류](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d7658b2-e827-49c3-a2ae-6d2bd0b45874) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1538.json) |
|[Microsoft 관리형 컨트롤 1539 - 보안 분류](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faabb155f-e7a5-4896-a767-e918bfae2ee0) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1539.json) |
|[Microsoft 관리형 컨트롤 1540 - 보안 분류](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff771f8cb-6642-45cc-9a15-8a41cd5c6977) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1540.json) |

### <a name="risk-assessment"></a>위험 평가

**ID**: NIST SP 800-53 Rev. 4 RA-3

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1541 - 위험 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F70f6af82-7be6-44aa-9b15-8b9231b2e434) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1541.json) |
|[Microsoft 관리형 컨트롤 1542 - 위험 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feab340d0-3d55-4826-a0e5-feebfeb0131d) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1542.json) |
|[Microsoft 관리형 컨트롤 1543 - 위험 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd00b778-b5b5-49c0-a994-734ea7bd3624) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1543.json) |
|[Microsoft 관리형 컨트롤 1544 - 위험 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43ced7c9-cd53-456b-b0da-2522649a4271) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1544.json) |
|[Microsoft 관리형 컨트롤 1545 - 위험 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3f4b171a-a56b-4328-8112-32cf7f947ee1) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1545.json) |

### <a name="vulnerability-scanning"></a>취약점 검색

**ID**: NIST SP 800-53 Rev. 4 RA-5

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[취약성 평가 솔루션을 가상 머신에서 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |가상 머신을 감사하여 지원되는 취약성 평가 솔루션을 실행하고 있는지 검색합니다. 모든 사이버 위험 및 보안 프로그램의 핵심 구성 요소는 취약성을 식별하고 분석하는 것입니다. Azure Security Center의 표준 가격 책정 계층에는 추가 비용 없이 가상 머신에 대한 취약성 검사가 포함됩니다. 또한 Security Center가 자동으로 도구를 배포할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Azure Defender for App Service를 사용해야 합니다](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service는 클라우드의 규모와 Azure가 클라우드 공급자로서 가지고 있는 가시성을 활용하여 일반적인 웹앱 공격을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure SQL Database 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[컨테이너 레지스트리용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |컨테이너 레지스트리용 Azure Defender는 지난 30일 내에 끌어와서 레지스트리에 푸시한 이미지의 취약성을 검사하고, 각 이미지에 대해 발견한 자세한 내용을 공개합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS는 Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링하여 클라우드 리소스에 대한 추가 보호 계층을 제공합니다. Azure Defender는 DNS 계층에서 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)에서 Azure Defender for DNS의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault는 키 자격 증명 모음 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하여 보안 인텔리전스에 추가 보호 계층을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager는 조직의 리소스 관리 작업을 자동으로 모니터링합니다. Azure Defender는 위협을 감지하고 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)에서 Azure Defender for Resource Manager의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[머신의 SQL 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[보호되지 않는 Azure SQL 서버에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[스토리지용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[Microsoft 관리형 컨트롤 1546 - 취약성 검사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2ce1ea7e-4038-4e53-82f4-63e8859333c1) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1546.json) |
|[Microsoft 관리형 컨트롤 1547 - 취약성 검사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58abf9b8-c6d4-4b4b-bfb9-fe98fe295f52) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1547.json) |
|[Microsoft 관리형 컨트롤 1548 - 취약성 검사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3afe6c78-6124-4d95-b85c-eb8c0c9539cb) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1548.json) |
|[Microsoft 관리형 컨트롤 1549 - 취약성 검사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd6976a08-d969-4df2-bb38-29556c2eb48a) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1549.json) |
|[Microsoft 관리형 컨트롤 1550 - 취약성 검사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F902908fb-25a8-4225-a3a5-5603c80066c9) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1550.json) |
|[SQL 데이터베이스가 발견한 취약성을 해결해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |취약성 평가 검사 결과 및 데이터베이스 취약성을 해결하는 방법에 대한 권장 사항을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[컴퓨터의 SQL Server는 발견한 취약성을 해결해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6ba6d016-e7c3-4842-b8f2-4992ebc0d72d) |SQL 취약성 평가는 데이터베이스를 검사하여 보안 취약성을 찾아내고, 구성 오류, 과도한 권한, 보호되지 않는 중요한 데이터 등과 같이 모범 사례를 따르지 않는 부분을 공개합니다. 발견된 취약성을 해결하면 데이터베이스 보안 상태가 크게 향상될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerSQLVulnerabilityAssessment_Audit.json) |
|[Azure Container Registry 이미지의 취약성을 수정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0f936f-2f01-4bf5-b6be-d423792fa562) |컨테이너 이미지 취약성 평가는 레지스트리를 검사하여 푸시된 각 컨테이너 이미지의 보안 취약성을 확인하고 각 이미지에 대한 결과를 공개합니다(Qualys에서 제공). 취약성을 해결하면 컨테이너의 보안 상태가 크게 개선되며 공격으로부터 보호할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerRegistryVulnerabilityAssessment_Audit.json) |
|[컨테이너 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Docker가 설치된 머신에서 보안 구성의 취약성을 감사하고 Azure Security Center에서 권장 사항으로 표시합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[머신 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |구성된 기준을 충족하지 않는 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |가상 머신 확장 집합의 OS 취약성을 감사하여 공격으로부터 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[SQL Managed Instance에서 취약성 평가를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |반복 취약성 평가 검사를 사용하도록 설정하지 않은 각 SQL Managed Instance를 감사합니다. 취약성 평가는 잠재적 데이터베이스 취약성을 검색 및 추적할 수 있고 해결하는 데 도움이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |반복 취약성 평가 검사를 사용하도록 설정하지 않은 Azure SQL 서버를 감사합니다. 취약성 평가는 잠재적 데이터베이스 취약성을 검색 및 추적할 수 있고 해결하는 데 도움이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[Synapse 작업 영역에서 취약성 평가를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0049a6b3-a662-4f3e-8635-39cf44ace45a) |Synapse 작업 영역에서 반복되는 SQL 취약성 평가 검사를 구성하여 잠재적인 취약성을 검색하고 추적 및 수정합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/ASC_SQLVulnerabilityAssessmentOnSynapse_Audit.json) |

### <a name="update-tool-capability"></a>도구 기능 업데이트

**ID**: NIST SP 800-53 Rev. 4 RA-5 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1551 - 취약성 검사 \| 도구 기능 업데이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bbda922-0172-4095-89e6-5b4a0bf03af7) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1551.json) |

### <a name="update-by-frequency--prior-to-new-scan--when-identified"></a>빈도에 따라/새 검사 전에/발견될 때 업데이트

**ID**: NIST SP 800-53 Rev. 4 RA-5 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1552 - 취약성 검사 \| 빈도에 따라/새 검사 전에/발견될 때 업데이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F43684572-e4f1-4642-af35-6b933bc506da) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1552.json) |

### <a name="breadth--depth-of-coverage"></a>적용 범위 너비/깊이

**ID**: NIST SP 800-53 Rev. 4 RA-5 (3)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1553 - 취약성 검사 \| 적용 폭/깊이](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e5225fe-cdfb-4fce-9aec-0fe20dd53b62) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1553.json) |

### <a name="discoverable-information"></a>검색 가능한 정보

**ID**: NIST SP 800-53 Rev. 4 RA-5 (4)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1554 - 취약성 검사 \| 검색 가능한 정보](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10984b4e-c93e-48d7-bf20-9c03b04e9eca) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1554.json) |

### <a name="privileged-access"></a>권한 있는 액세스

**ID**: NIST SP 800-53 Rev. 4 RA-5 (5)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1555 - 취약성 검사 \| 권한 있는 액세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5afa8cab-1ed7-4e40-884c-64e0ac2059cc) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1555.json) |

### <a name="automated-trend-analyses"></a>자동화된 추세 분석

**ID**: NIST SP 800-53 Rev. 4 RA-5 (6)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1556 - 취약성 검사 \| 자동화된 추세 분석](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F391ff8b3-afed-405e-9f7d-ef2f8168d5da) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1556.json) |

### <a name="review-historic-audit-logs"></a>기록 감사 로그 검토

**ID**: NIST SP 800-53 Rev. 4 RA-5 (8)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1557 - 취약성 검사 \| 기록 감사 로그 검토](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36fbe499-f2f2-41b6-880e-52d7ea1d94a5) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1557.json) |

### <a name="correlate-scanning-information"></a>검사 정보 상호 연결

**ID**: NIST SP 800-53 Rev. 4 RA-5 (10)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1558 - 취약성 검사 \| 검사 정보 상호 연결](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F65592b16-4367-42c5-a26e-d371be450e17) |Microsoft에서 구현하는 위험 평가 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1558.json) |

## <a name="system-and-services-acquisition"></a>시스템 및 서비스 획득

### <a name="system-and-services-acquisition-policy-and-procedures"></a>시스템 및 서비스 취득 정책 및 절차

**ID**: NIST SP 800-53 Rev. 4 SA-1

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1559 - 시스템 및 서비스 취득 정책과 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45692294-f074-42bd-ac54-16f1a3c07554) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1559.json) |
|[Microsoft 관리형 컨트롤 1560 - 시스템 및 서비스 취득 정책과 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe29e0915-5c2f-4d09-8806-048b749ad763) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1560.json) |

### <a name="allocation-of-resources"></a>리소스 할당

**ID**: NIST SP 800-53 Rev. 4 SA-2

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1561 - 리소스 할당](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40364c3f-c331-4e29-b1e3-2fbe998ba2f5) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1561.json) |
|[Microsoft 관리형 컨트롤 1562 - 리소스 할당](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd4142013-7964-4163-a313-a900301c2cef) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1562.json) |
|[Microsoft 관리형 컨트롤 1563 - 리소스 할당](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9afe2edf-232c-4fdf-8e6a-e867a5c525fd) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1563.json) |

### <a name="system-development-life-cycle"></a>시스템 개발 수명 주기

**ID**: NIST SP 800-53 Rev. 4 SA-3

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1564 - 시스템 개발 수명 주기](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F157f0ef9-143f-496d-b8f9-f8c8eeaad801) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1564.json) |
|[Microsoft 관리형 컨트롤 1565 - 시스템 개발 수명 주기](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45ce2396-5c76-4654-9737-f8792ab3d26b) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1565.json) |
|[Microsoft 관리형 컨트롤 1566 - 시스템 개발 수명 주기](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F50ad3724-e2ac-4716-afcc-d8eabd97adb9) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1566.json) |
|[Microsoft 관리형 컨트롤 1567 - 시스템 개발 수명 주기](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe72edbf6-aa61-436d-a227-0f32b77194b3) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1567.json) |

### <a name="acquisition-process"></a>취득 프로세스

**ID**: NIST SP 800-53 Rev. 4 SA-4

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1568 - 취득 프로세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6a8eae8-9854-495a-ac82-d2cd3eac02a6) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1568.json) |
|[Microsoft 관리형 컨트롤 1569 - 취득 프로세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fad2f8e61-a564-4dfd-8eaa-816f5be8cb34) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1569.json) |
|[Microsoft 관리형 컨트롤 1570 - 취득 프로세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7fcf38d-bb09-4600-be7d-825046eb162a) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1570.json) |
|[Microsoft 관리형 컨트롤 1571 - 취득 프로세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb11c985b-f2cd-4bd7-85f4-b52426edf905) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1571.json) |
|[Microsoft 관리형 컨트롤 1572 - 취득 프로세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04f5fb00-80bb-48a9-a75b-4cb4d4c97c36) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1572.json) |
|[Microsoft 관리형 컨트롤 1573 - 취득 프로세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58c93053-7b98-4cf0-b99f-1beb985416c2) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1573.json) |
|[Microsoft 관리형 컨트롤 1574 - 취득 프로세스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f935dab-83d6-47b8-85ef-68b8584161b9) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1574.json) |

### <a name="functional-properties-of-security-controls"></a>보안 제어의 기능적 속성

**ID**: NIST SP 800-53 Rev. 4 SA-4 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1575 - 취득 프로세스 \| 보안 컨트롤의 기능 속성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e1bb73-1b08-4dbe-9c62-8e2e92e7ec41) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1575.json) |

### <a name="design--implementation-information-for-security-controls"></a>보안 제어에 대한 디자인/구현 정보

**ID**: NIST SP 800-53 Rev. 4 SA-4 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1576 - 취득 프로세스 \| 보안 컨트롤에 대한 디자인/구현 정보](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f18c885-ade3-48c5-80b1-8f9216019c18) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1576.json) |

### <a name="continuous-monitoring-plan"></a>지속적인 모니터링 계획

**ID**: NIST SP 800-53 Rev. 4 SA-4 (8)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1577 - 취득 프로세스 \| 지속적인 모니터링 계획](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd922484a-8cfc-4a6b-95a4-77d6a685407f) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1577.json) |

### <a name="functions--ports--protocols--services-in-use"></a>사용 중인 기능/포트/프로토콜/서비스

**ID**: NIST SP 800-53 Rev. 4 SA-4 (9)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1578 - 취득 프로세스 \| 사용 중인 기능/포트/프로토콜/서비스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F45b7b644-5f91-498e-9d89-7402532d3645) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1578.json) |

### <a name="use-of-approved-piv-products"></a>승인된 PIV 제품 사용

**ID**: NIST SP 800-53 Rev. 4 SA-4 (10)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1579 - 취득 프로세스 \| 승인된 Piv 제품 사용](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4e54c7ef-7457-430b-9a3e-ef8881d4a8e0) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1579.json) |

### <a name="information-system-documentation"></a>정보 시스템 설명서

**ID**: NIST SP 800-53 Rev. 4 SA-5

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1580 - 정보 시스템 설명서](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F854db8ac-6adf-42a0-bef3-b73f764f40b9) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1580.json) |
|[Microsoft 관리형 컨트롤 1581 - 정보 시스템 설명서](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F742b549b-7a25-465f-b83c-ea1ffb4f4e0e) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1581.json) |
|[Microsoft 관리형 컨트롤 1582 - 정보 시스템 설명서](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcd9e2f38-259b-462c-bfad-0ad7ab4e65c5) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1582.json) |
|[Microsoft 관리형 컨트롤 1583 - 정보 시스템 설명서](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0882d488-8e80-4466-bc0f-0cd15b6cb66d) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1583.json) |
|[Microsoft 관리형 컨트롤 1584 - 정보 시스템 설명서](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5864522b-ff1d-4979-a9f8-58bee1fb174c) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1584.json) |

### <a name="security-engineering-principles"></a>보안 엔지니어링 원칙

**ID**: NIST SP 800-53 Rev. 4 SA-8

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1585 - 보안 엔지니어링 원칙](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd57f8732-5cdc-4cda-8d27-ab148e1f3a55) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1585.json) |

### <a name="external-information-system-services"></a>외부 정보 시스템 서비스

**ID**: NIST SP 800-53 Rev. 4 SA-9

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1586 - 외부 정보 시스템 서비스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e3b2fbd-8f37-4766-a64d-3f37703dcb51) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1586.json) |
|[Microsoft 관리형 컨트롤 1587 - 외부 정보 시스템 서비스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F32820956-9c6d-4376-934c-05cd8525be7c) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1587.json) |
|[Microsoft 관리형 컨트롤 1588 - 외부 정보 시스템 서비스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68ebae26-e0e0-4ecb-8379-aabf633b51e9) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1588.json) |

### <a name="risk-assessments--organizational-approvals"></a>위험 평가/조직 승인

**ID**: NIST SP 800-53 Rev. 4 SA-9 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1589 - 외부 정보 시스템 서비스 \| 위험 평가/조직 승인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86ec7f9b-9478-40ff-8cfd-6a0d510081a8) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1589.json) |
|[Microsoft 관리형 컨트롤 1590 - 외부 정보 시스템 서비스 \| 위험 평가/조직 승인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf296b8c-f391-4ea4-9198-be3c9d39dd1f) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1590.json) |

### <a name="identification-of-functions--ports--protocols--services"></a>기능/포트/프로토콜/서비스 식별

**ID**: NIST SP 800-53 Rev. 4 SA-9 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1591 - 외부 정보 시스템 서비스 \| 기능/ 포트/프로토콜/서비스 식별](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff751cdb7-fbee-406b-969b-815d367cb9b3) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1591.json) |

### <a name="consistent-interests-of-consumers-and-providers"></a>소비자와 공급자의 일관적인 관심

**ID**: NIST SP 800-53 Rev. 4 SA-9 (4)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1592 - 외부 정보 시스템 서비스 \| 소비자와 공급자의 일관적인 관심](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d01ba6c-289f-42fd-a408-494b355b6222) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1592.json) |

### <a name="processing-storage-and-service-location"></a>처리, 스토리지 및 서비스 위치

**ID**: NIST SP 800-53 Rev. 4 SA-9 (5)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1593 - 외부 정보 시스템 서비스 \| 처리, 스토리지 및 서비스 위치](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cd0a426-b5f5-4fe0-9539-a6043cdbc6fa) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1593.json) |

### <a name="developer-configuration-management"></a>개발자 구성 관리

**ID**: NIST SP 800-53 Rev. 4 SA-10

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1594 - 개발자 구성 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F042ba2a1-8bb8-45f4-b080-c78cf62b90e9) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1594.json) |
|[Microsoft 관리형 컨트롤 1595 - 개발자 구성 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1e0414e7-6ef5-4182-8076-aa82fbb53341) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1595.json) |
|[Microsoft 관리형 컨트롤 1596 - 개발자 구성 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21e25e01-0ae0-41be-919e-04ce92b8e8b8) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1596.json) |
|[Microsoft 관리형 컨트롤 1597 - 개발자 구성 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F68b250ec-2e4f-4eee-898a-117a9fda7016) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1597.json) |
|[Microsoft 관리형 컨트롤 1598 - 개발자 구성 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae7e1f5e-2d63-4b38-91ef-bce14151cce3) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1598.json) |

### <a name="software--firmware-integrity-verification"></a>소프트웨어/펌웨어 무결성 확인

**ID**: NIST SP 800-53 Rev. 4 SA-10 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1599 - 개발자 구성 관리 \| 소프트웨어/펌웨어 무결성 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0004bbf0-5099-4179-869e-e9ffe5fb0945) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1599.json) |

### <a name="developer-security-testing-and-evaluation"></a>개발자 보안 테스트 및 평가

**ID**: NIST SP 800-53 Rev. 4 SA-11

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1600 - 개발자 보안 테스트 및 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc53f3123-d233-44a7-930b-f40d3bfeb7d6) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1600.json) |
|[Microsoft 관리형 컨트롤 1601 - 개발자 보안 테스트 및 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ee79a0c-addf-4ce9-9b3c-d9576ed5e20e) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1601.json) |
|[Microsoft 관리형 컨트롤 1602 - 개발자 보안 테스트 및 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fddae2e97-a449-499f-a1c8-aea4a7e52ec9) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1602.json) |
|[Microsoft 관리형 컨트롤 1603 - 개발자 보안 테스트 및 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b909c26-162f-47ce-8e15-0c1f55632eac) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1603.json) |
|[Microsoft 관리형 컨트롤 1604 - 개발자 보안 테스트 및 평가](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44dbba23-0b61-478e-89c7-b3084667782f) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1604.json) |

### <a name="static-code-analysis"></a>정적 코드 분석

**ID**: NIST SP 800-53 Rev. 4 SA-11 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1605 - 개발자 보안 테스트 및 평가 \| 정적 코드 분석](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0062eb8b-dc75-4718-8ea5-9bb4a9606655) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1605.json) |

### <a name="threat-and-vulnerability-analyses"></a>위협 및 취약성 분석

**ID**: NIST SP 800-53 Rev. 4 SA-11 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1606 - 개발자 보안 테스트 및 평가 \| 위협 및 취약성 분석](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaa8a9a4-5bbe-4c72-98f6-a3a47ae2b1ca) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1606.json) |

### <a name="dynamic-code-analysis"></a>동적 코드 분석

**ID**: NIST SP 800-53 Rev. 4 SA-11 (8)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1607 - 개발자 보안 테스트 및 평가 \| 동적 코드 분석](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F976a74cf-b192-4d35-8cab-2068f272addb) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1607.json) |

### <a name="supply-chain-protection"></a>공급 체인 보호

**ID**: NIST SP 800-53 Rev. 4 SA-12

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1608 - 공급망 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb73b7b3b-677c-4a2a-b949-ad4dc4acd89f) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1608.json) |

### <a name="development-process-standards-and-tools"></a>개발 프로세스, 표준 및 도구

**ID**: NIST SP 800-53 Rev. 4 SA-15

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1609 - 개발 프로세스, 표준 및 도구](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9e93fa71-42ac-41a7-b177-efbfdc53c69f) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1609.json) |
|[Microsoft 관리형 컨트롤 1610 - 개발 프로세스, 표준 및 도구](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9f3fb54-4222-46a1-a308-4874061f8491) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1610.json) |

### <a name="developer-provided-training"></a>개발자가 제공한 교육

**ID**: NIST SP 800-53 Rev. 4 SA-16

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1611 - 개발자가 제공한 교육](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdda8a0c-ac32-43f6-b2f4-7dc1df03f43f) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1611.json) |

### <a name="developer-security-architecture-and-design"></a>개발자 보안 아키텍처 및 디자인

**ID**: NIST SP 800-53 Rev. 4 SA-17

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1612 - 개발자 보안 아키텍처 및 디자인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2037b3d-8b04-4171-8610-e6d4f1d08db5) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1612.json) |
|[Microsoft 관리형 컨트롤 1613 - 개발자 보안 아키텍처 및 디자인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe2ad78b-8748-4bff-a924-f74dfca93f30) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1613.json) |
|[Microsoft 관리형 컨트롤 1614 - 개발자 보안 아키텍처 및 디자인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8154e3b3-cc52-40be-9407-7756581d71f6) |Microsoft에서 구현하는 시스템 및 서비스 취득 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1614.json) |

## <a name="system-and-communications-protection"></a>시스템 및 통신 보호

### <a name="system-and-communications-protection-policy-and-procedures"></a>시스템 및 통신 보호 정책과 절차

**ID**: NIST SP 800-53 Rev. 4 SC-1

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1615 - 시스템 및 통신 보호 정책과 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff35e02aa-0a55-49f8-8811-8abfa7e6f2c0) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1615.json) |
|[Microsoft 관리형 컨트롤 1616 - 시스템 및 통신 보호 정책과 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2006457a-48b3-4f7b-8d2e-1532287f9929) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1616.json) |

### <a name="application-partitioning"></a>애플리케이션 분할

**ID**: NIST SP 800-53 Rev. 4 SC-2

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1617 - 애플리케이션 분할](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa631d8f5-eb81-4f9d-9ee1-74431371e4a3) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1617.json) |

### <a name="security-function-isolation"></a>보안 기능 격리

**ID**: NIST SP 800-53 Rev. 4 SC-3

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |가상 머신 확장 집합에서 엔드포인트 보호 솔루션의 존재 및 상태를 감사하여 위협 및 취약성으로부터 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft 관리형 컨트롤 1618 - 보안 기능 격리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff52f89aa-4489-4ec4-950e-8c96a036baa9) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1618.json) |
|[Azure Security Center에서 누락된 Endpoint Protection 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Endpoint Protection 에이전트가 설치되어 있지 않은 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[Windows Defender Exploit Guard를 머신에서 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbed48b13-6647-468e-aa2f-1af1d3f4dd40) |Windows Defender Exploit Guard는 Azure Policy 게스트 구성 에이전트를 사용합니다. Exploit Guard에는 다양한 공격 벡터에 대해 디바이스를 잠그고 맬웨어 공격에서 일반적으로 사용되는 동작을 차단하면서 기업에서 보안 위험과 생산성 요구 사항 사이의 균형을 맞출 수 있도록 설계된 네 가지 구성 요소가 있습니다(Windows에만 해당). |AuditIfNotExists, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDefenderExploitGuard_AINE.json) |

### <a name="information-in-shared-resources"></a>공유 리소스의 정보

**ID**: NIST SP 800-53 Rev. 4 SC-4

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1619 - 공유 리소스의 정보](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc722e569-cb52-45f3-a643-836547d016e1) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1619.json) |

### <a name="denial-of-service-protection"></a>서비스 거부 보호

**ID**: NIST SP 800-53 Rev. 4 SC-5

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure DDoS Protection 표준을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |공용 IP를 사용하는 애플리케이션 게이트웨이의 일부인 서브넷이 포함된 모든 가상 네트워크에 DDoS 보호 표준을 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
|[가상 머신에서 IP 전달을 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |가상 머신의 NIC에서 IP 전달을 사용하도록 설정하면 머신이 다른 대상으로 주소가 지정된 트래픽을 수신할 수 있습니다. IP 전달은 거의 필요하지 않으므로(예: VM을 네트워크 가상 어플라이언스로 사용하는 경우), 네트워크 보안 팀에서 검토해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[Microsoft 관리형 컨트롤 1620 - 서비스 거부 방지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd17c826b-1dec-43e1-a984-7b71c446649c) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1620.json) |
|[Application Gateway에 WAF(웹 애플리케이션 방화벽)를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F564feb30-bf6a-4854-b4bb-0d2d2d1e6c66) |들어오는 트래픽의 추가 검사를 위해 공용 웹 애플리케이션 앞에 Azure WAF(웹 애플리케이션 방화벽)를 배포합니다. WAF(웹 애플리케이션 방화벽)는 SQL 삽입, 교차 사이트 스크립팅, 로컬 및 원격 파일 실행과 같은 일반적인 악용과 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호합니다. 사용자 지정 규칙을 통해 국가, IP 주소 범위 및 기타 http 매개 변수별로 웹 애플리케이션에 대한 액세스를 제한할 수도 있습니다. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AppGatewayEnabled_Audit.json) |
|[WAF(Web Application Firewall)를 Azure Front Door Service 서비스에 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F055aa869-bc98-4af8-bafc-23f1ab6ffe2c) |들어오는 트래픽의 추가 검사를 위해 공용 웹 애플리케이션 앞에 Azure WAF(웹 애플리케이션 방화벽)를 배포합니다. WAF(웹 애플리케이션 방화벽)는 SQL 삽입, 교차 사이트 스크립팅, 로컬 및 원격 파일 실행과 같은 일반적인 악용과 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호합니다. 사용자 지정 규칙을 통해 국가, IP 주소 범위 및 기타 http 매개 변수별로 웹 애플리케이션에 대한 액세스를 제한할 수도 있습니다. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AFD_Enabled_Audit.json) |

### <a name="resource-availability"></a>리소스 가용성

**ID**: NIST SP 800-53 Rev. 4 SC-6

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1621 - 리소스 가용성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cb9f731-744a-4691-a481-ca77b0411538) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1621.json) |

### <a name="boundary-protection"></a>경계 보호

**ID**: NIST SP 800-53 Rev. 4 SC-7

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[인터넷 연결 가상 머신에 적응형 네트워크 강화 권장 사항을 적용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center는 인터넷 연결 가상 머신의 트래픽 패턴을 분석하고 잠재적 공격 노출 영역을 줄이는 네트워크 보안 그룹 규칙 권장 사항을 제공합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[모든 인터넷 트래픽은 배포된 Azure Firewall을 통해 라우팅되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure Security Center에서 일부 서브넷이 차세대 방화벽으로 보호되지 않는 것으로 확인되었습니다. Azure Firewall 또는 지원되는 차세대 방화벽으로 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[가상 머신과 연결된 네트워크 보안 그룹에서 모든 네트워크 포트를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center에서 네트워크 보안 그룹의 인바운드 규칙 중 일부가 너무 관대하다는 사실을 식별했습니다. 인바운드 규칙에서 '모두' 또는 '인터넷' 범위에서 들어오는 액세스를 허용해서는 안 됩니다. 그러면 리소스가 공격자의 표적이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[API Management 서비스에서 가상 네트워크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure Virtual Network 배포는 향상된 보안, 격리를 제공하며, 액세스를 제어하는 인터넷 라우팅이 불가능한 네트워크에 API Management 서비스를 배치할 수 있습니다. 그런 다음, 다양한 VPN 기술을 사용하여 이러한 네트워크를 온-프레미스 네트워크에 연결할 수 있으며, 이를 통해 네트워크 및/또는 온-프레미스 내에서 백 엔드 서비스에 액세스할 수 있습니다. 개발자 포털 및 API 게이트웨이를 인터넷에서 또는 가상 네트워크 내에서만 액세스할 수 있게 구성할 수 있습니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
|[App Configuration은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 앱 구성 인스턴스에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/appconfig/private-endpoint](../../../azure-app-configuration/concept-private-endpoint.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[권한 있는 IP 범위는 Kubernetes Services에 정의되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |특정 범위의 IP 주소에만 API 액세스 권한을 부여하여 Kubernetes Service Management API에 대한 액세스를 제한합니다. 허용된 네트워크의 애플리케이션만 클러스터에 액세스할 수 있도록 인증된 IP 범위에 대한 액세스를 제한하는 것이 좋습니다. |감사, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|[Azure API for FHIR은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR에는 승인된 프라이빗 엔드포인트 연결이 하나 이상 있어야 합니다. 가상 네트워크의 클라이언트는 프라이빗 링크를 통해 프라이빗 엔드포인트 연결이 있는 리소스에 안전하게 액세스할 수 있습니다. 자세한 내용은 [https://aka.ms/fhir-privatelink](../../../healthcare-apis/azure-api-for-fhir/configure-private-link.md)를 방문하세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Azure Cache for Redis는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |프라이빗 엔드포인트를 사용하면 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 엔드포인트를 Azure Cache for Redis 인스턴스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](../../../azure-cache-for-redis/cache-private-link.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Cognitive Search 서비스는 프라이빗 링크를 지원하는 SKU를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |Azure Cognitive Search의 지원되는 SKU를 통해 Azure Private Link를 사용하면 원본 또는 대상에서 공용 IP 주소 없이 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Search Service에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure Cognitive Search 서비스는 공용 네트워크 액세스를 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee980b6d-0eca-4501-8d54-f6290fd512c3) |공용 네트워크 액세스를 사용하지 않도록 설정하면 Azure Cognitive Search 서비스가 공용 인터넷에 노출되지 않도록 하여 보안이 향상됩니다. 프라이빗 엔드포인트를 만들면 Search Service의 노출을 제한할 수 있습니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePublicNetworkAccessDisabled_Deny.json) |
|[Azure Cognitive Search 서비스에서 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fda3595-9f2b-4592-8675-4231d6fa82fe) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Cognitive Search에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateEndpoints_Audit.json) |
|[Azure Cosmos DB 계정에 방화벽 규칙이 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |권한 없는 원본의 트래픽을 방지하기 위해 Azure Cosmos DB 계정에 방화벽 규칙을 정의해야 합니다. 가상 네트워크 필터를 사용하도록 정의된 IP 규칙이 하나 이상 있는 계정은 규정을 준수하는 것으로 간주됩니다. 퍼블릭 액세스를 비활성화한 계정도 규정을 준수하는 것으로 간주됩니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|[Azure Data Factory는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Data Factory에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/data-factory/data-factory-private-link](../../../data-factory/data-factory-private-link.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Azure Event Grid 도메인은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 Event Grid 도메인에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Domains_PrivateEndpoint_Audit.json) |
|[Azure Event Grid 토픽은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 Event Grid 토픽에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Topics_PrivateEndpoint_Audit.json) |
|[Azure 파일 동기화는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |표시된 Storage Sync Service 리소스의 프라이빗 엔드포인트를 만들면 인터넷에서 액세스할 수 있는 퍼블릭 엔드포인트를 사용하지 않고 조직 네트워크의 개인 IP 주소 공간 내에서 Storage Sync Service 리소스를 처리할 수 있습니다. 프라이빗 엔드포인트를 자체적으로 만든다고 해서 퍼블릭 엔드포인트가 비활성화되지 않습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Key Vault에서 공용 네트워크 액세스를 사용할 수 없음](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |공용 인터넷을 통해 액세스할 수 없도록 키 자격 증명 모음에 대한 공용 네트워크 액세스를 사용하지 않도록 설정합니다. 이를 통해 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/akvprivatelink](../../../key-vault/general/private-link-service.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|[Azure Machine Learning 작업 영역은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Machine Learning 작업 영역에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link](../../../machine-learning/how-to-configure-private-link.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateEndpoint_Audit.json) |
|[Azure Service Bus 네임스페이스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Service Bus 네임스페이스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../service-bus-messaging/private-link-service.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[Azure SignalR Service는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 전체 서비스가 아닌 Azure SignalR Service 리소스에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/asrs/privatelink](../../../azure-signalr/howto-private-endpoints.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure Synapse 작업 영역은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Synapse 작업 영역에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/synapse-analytics/security/how-to-connect-to-workspace-with-private-links](../../../synapse-analytics/security/how-to-connect-to-workspace-with-private-links.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Azure Web PubSub 서비스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |Azure Private Link를 사용하면 원본 또는 대상에 공용 IP 주소가 없어도 가상 네트워크를 Azure 서비스에 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Web PubSub 서비스에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[Cognitive Services 계정은 공용 네트워크 액세스를 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0725b4dd-7e76-479c-a735-68e7ee23d5ca) |공용 네트워크 액세스를 사용하지 않도록 설정하면 Cognitive Services 계정이 공용 인터넷에 노출되지 않도록 하여 보안이 향상됩니다. 프라이빗 엔드포인트를 만들면 Cognitive Services 계정의 노출을 제한할 수 있습니다. [https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)에서 자세히 알아보세요.  |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisablePublicNetworkAccess_Audit.json) |
|[Cognitive Services 계정은 네트워크 액세스를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F037eea7a-bd0a-46c5-9a66-03aea78705d3) |Cognitive Services 계정에 대한 네트워크 액세스는 제한되어야 합니다. 허용되는 네트워크의 애플리케이션만 Cognitive Services 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용하기 위해 특정 Azure 가상 네트워크에서의 트래픽 또는 공용 인터넷 IP 주소 범위에 액세스 권한을 부여할 수 있습니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_NetworkAcls_Audit.json) |
|[Cognitive Services에서 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01) |Azure Private Link를 사용하면 원본 또는 대상에 공용 IP 주소가 없어도 가상 네트워크를 Azure 서비스에 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Cognitive Services로 매핑하면 데이터 누출 가능성이 줄어듭니다. [https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_EnablePrivateEndpoints_Audit.json) |
|[컨테이너 레지스트리는 무제한 네트워크 액세스를 허용하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |기본적으로 Azure Container Registry는 모든 네트워크에 있는 호스트로부터의 인터넷 연결을 수락합니다. 잠재적 위협으로부터 레지스트리를 보호하려면 특정 공용 IP 주소 또는 주소 범위에서만 액세스를 허용합니다. 레지스트리에 IP/방화벽 규칙이나 구성된 가상 네트워크가 없는 경우 상태가 좋지 않은 리소스에 표시됩니다. Container Registry 네트워크 규칙에 대한 자세한 내용은 [https://aka.ms/acr/portal/public-network](../../../container-registry/container-registry-access-selected-networks.md) 및 [https://aka.ms/acr/vnet](../../../container-registry/container-registry-vnet.md)을 참조하세요. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_AuditDeny.json) |
|[컨테이너 레지스트리는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스 대신 프라이빗 엔드포인트를 컨테이너 레지스트리에 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/acr/private-link](../../../container-registry/container-registry-private-link.md)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[CosmosDB 계정은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58440f8a-10c5-4151-bdce-dfbaad4a20b7) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 CosmosDB 계정에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints](../../../cosmos-db/how-to-configure-private-endpoints.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_PrivateEndpoint_Audit.json) |
|[디스크 액세스 리소스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff39f5f49-4abf-44de-8c70-0756997bfb51) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 diskAccesses에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/disksprivatelinksdoc](../../../virtual-machines/disks-enable-private-links-for-import-export-portal.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 프라이빗 링크에 대해 자세히 알아보세요.  |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DiskAccesses_PrivateEndpoints_Audit.json) |
|[Event Hub 네임스페이스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb8564268-eb4a-4337-89be-a19db070c59d) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Event Hub 네임스페이스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/event-hubs/private-link-service](../../../event-hubs/private-link-service.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_PrivateEndpoint_Audit.json) |
|[네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |NSG(네트워크 보안 그룹)를 통해 가상 머신에 대한 액세스를 제한하여 잠재적인 위협으로부터 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 IoT Hub 디바이스 프로비저닝 서비스에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://aka.ms/iotdpsvnet](../../../iot-dps/virtual-network-support.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|[가상 머신에서 IP 전달을 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |가상 머신의 NIC에서 IP 전달을 사용하도록 설정하면 머신이 다른 대상으로 주소가 지정된 트래픽을 수신할 수 있습니다. IP 전달은 거의 필요하지 않으므로(예: VM을 네트워크 가상 어플라이언스로 사용하는 경우), 네트워크 보안 팀에서 검토해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |가능한 네트워크 JIT(Just In Time) 액세스는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[가상 머신에서 관리 포트를 닫아야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |열려 있는 원격 관리 포트는 위험도가 높은 인터넷 기반 공격에 VM을 노출시킵니다. 이러한 공격은 자격 증명을 무차별적으로 대입하여 머신에 대한 관리자 액세스 권한을 획득하려고 시도합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
|[Microsoft 관리형 컨트롤 1622 - 경계 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fecf56554-164d-499a-8d00-206b07c27bed) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1622.json) |
|[Microsoft 관리형 컨트롤 1623 - 경계 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F02ce1b22-412a-4528-8630-c42146f917ed) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1623.json) |
|[Microsoft 관리형 컨트롤 1624 - 경계 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F37d079e3-d6aa-4263-a069-dd7ac6dd9684) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1624.json) |
|[네트워크 보안 그룹을 사용하여 비인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb91dfba-c30d-4263-9add-9c2384e659a6) |NSG(네트워크 보안 그룹)를 통해 액세스를 제한하여 잠재적인 위협으로부터 비인터넷 연결 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternalVirtualMachines_Audit.json) |
|[Azure SQL Database에서 프라이빗 엔드포인트 연결을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7698e800-9299-47a6-b3b6-5a0fee576eed) |프라이빗 엔드포인트 연결은 Azure SQL Database에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. |감사, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PrivateEndpoint_Audit.json) |
|[Key Vault의 프라이빗 엔드포인트를 구성해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |프라이빗 링크는 공용 인터넷을 통해 트래픽을 보내지 않고 Key Vault를 Azure 리소스에 연결하는 방법을 제공합니다. 프라이빗 링크는 데이터 반출에 대한 심층 방어 기능을 제공합니다. |감사, 거부, 사용 안 함 |[1.1.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|[프라이빗 엔드포인트를 MariaDB 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |프라이빗 엔드포인트 연결은 Azure Database for MariaDB에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[프라이빗 엔드포인트를 MySQL 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |프라이빗 엔드포인트 연결은 Azure Database for MySQL에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[프라이빗 엔드포인트를 PostgreSQL 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |프라이빗 엔드포인트 연결은 Azure Database for PostgreSQL에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[Azure SQL Database에서 공용 네트워크 액세스를 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b8ca024-1d5c-4dec-8995-b1a932b41780) |공용 네트워크 액세스 속성을 사용하지 않도록 설정하면 프라이빗 엔드포인트에서만 Azure SQL Database에 액세스할 수 있도록 하여 보안이 향상됩니다. 이 구성은 IP 또는 가상 네트워크 기반 방화벽 규칙과 일치하는 모든 로그인을 거부합니다. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PublicNetworkAccess_Audit.json) |
|[MariaDB 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |공용 네트워크 액세스 속성을 사용하지 않도록 설정하여 보안을 강화하고 프라이빗 엔드포인트에서만 Azure Database for MariaDB에 액세스할 수 있도록 합니다. 이 구성은 Azure IP 범위를 벗어나는 공용 주소 공간의 액세스를 엄격하게 차단하고, IP 또는 가상 네트워크 기반 방화벽 규칙에 부합하는 모든 로그인을 거부합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
|[MySQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |공용 네트워크 액세스 속성을 사용하지 않도록 설정하여 보안을 강화하고 프라이빗 엔드포인트에서만 Azure Database for MySQL에 액세스할 수 있도록 합니다. 이 구성은 Azure IP 범위를 벗어나는 공용 주소 공간의 액세스를 엄격하게 차단하고, IP 또는 가상 네트워크 기반 방화벽 규칙에 부합하는 모든 로그인을 거부합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
|[PostgreSQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb52376f7-9612-48a1-81cd-1ffe4b61032c) |공용 네트워크 액세스 속성을 사용하지 않도록 설정하여 보안을 강화하고 프라이빗 엔드포인트에서만 Azure Database for PostgreSQL에 액세스할 수 있도록 합니다. 이 구성은 Azure IP 범위를 벗어나는 공용 주소 공간에서의 액세스를 사용하지 않도록 설정하고, IP 또는 가상 네트워크 기반 방화벽 규칙과 일치하는 모든 로그인을 거부합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_DisablePublicNetworkAccess_Audit.json) |
|[스토리지 계정 공용 액세스가 허용되지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Azure Storage의 컨테이너 및 BLOB에 대한 익명 공용 읽기 액세스는 데이터를 공유하는 편리한 방법이지만 보안 위험이 있을 수도 있습니다. 원치 않는 익명 액세스로 인해 발생하는 데이터 위반을 방지하기 위해 Microsoft는 시나리오에 필요한 경우가 아니면 스토리지 계정에 대한 공개 액세스를 방지하는 것이 좋습니다 |감사, 거부, 사용 안 함 |[3.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |
|[스토리지 계정은 네트워크 액세스를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |스토리지 계정에 대한 네트워크 액세스가 제한되어야 합니다. 허용되는 네트워크의 애플리케이션만 스토리지 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용하기 위해 특정 Azure 가상 네트워크 또는 공용 인터넷 IP 주소 범위의 트래픽에 대한 액세스 권한을 부여할 수 있습니다. |감사, 거부, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[스토리지 계정은 가상 네트워크 규칙을 사용하여 네트워크 액세스를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |IP 기반 필터링 대신 기본 방법으로 가상 네트워크 규칙을 사용하여 잠재적인 위협으로부터 스토리지 계정을 보호합니다. IP 기반 필터링을 사용하지 않도록 설정하면 공용 IP가 스토리지 계정에 액세스할 수 없습니다. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |
|[스토리지 계정은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6edd7eda-6dd8-40f7-810d-67160c639cd9) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 스토리지 계정에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://aka.ms/azureprivatelinkoverview](../../../private-link/private-link-overview.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountPrivateEndpointEnabled_Audit.json) |
|[서브넷을 네트워크 보안 그룹과 연결해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |NSG(네트워크 보안 그룹)를 통해 VM에 대한 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. NSG는 서브넷에 대한 네트워크 트래픽을 허용 또는 거부하는 ACL(액세스 제어 목록) 규칙의 목록을 포함합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[VM Image Builder 템플릿은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 VM Image Builder에 매핑하여 리소스를 구성하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함, 거부 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
|[Application Gateway에 WAF(웹 애플리케이션 방화벽)를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F564feb30-bf6a-4854-b4bb-0d2d2d1e6c66) |들어오는 트래픽의 추가 검사를 위해 공용 웹 애플리케이션 앞에 Azure WAF(웹 애플리케이션 방화벽)를 배포합니다. WAF(웹 애플리케이션 방화벽)는 SQL 삽입, 교차 사이트 스크립팅, 로컬 및 원격 파일 실행과 같은 일반적인 악용과 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호합니다. 사용자 지정 규칙을 통해 국가, IP 주소 범위 및 기타 http 매개 변수별로 웹 애플리케이션에 대한 액세스를 제한할 수도 있습니다. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AppGatewayEnabled_Audit.json) |
|[WAF(Web Application Firewall)를 Azure Front Door Service 서비스에 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F055aa869-bc98-4af8-bafc-23f1ab6ffe2c) |들어오는 트래픽의 추가 검사를 위해 공용 웹 애플리케이션 앞에 Azure WAF(웹 애플리케이션 방화벽)를 배포합니다. WAF(웹 애플리케이션 방화벽)는 SQL 삽입, 교차 사이트 스크립팅, 로컬 및 원격 파일 실행과 같은 일반적인 악용과 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호합니다. 사용자 지정 규칙을 통해 국가, IP 주소 범위 및 기타 http 매개 변수별로 웹 애플리케이션에 대한 액세스를 제한할 수도 있습니다. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AFD_Enabled_Audit.json) |

### <a name="access-points"></a>액세스 지점

**ID**: NIST SP 800-53 Rev. 4 SC-7 (3)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[인터넷 연결 가상 머신에 적응형 네트워크 강화 권장 사항을 적용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center는 인터넷 연결 가상 머신의 트래픽 패턴을 분석하고 잠재적 공격 노출 영역을 줄이는 네트워크 보안 그룹 규칙 권장 사항을 제공합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[모든 인터넷 트래픽은 배포된 Azure Firewall을 통해 라우팅되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure Security Center에서 일부 서브넷이 차세대 방화벽으로 보호되지 않는 것으로 확인되었습니다. Azure Firewall 또는 지원되는 차세대 방화벽으로 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[가상 머신과 연결된 네트워크 보안 그룹에서 모든 네트워크 포트를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center에서 네트워크 보안 그룹의 인바운드 규칙 중 일부가 너무 관대하다는 사실을 식별했습니다. 인바운드 규칙에서 '모두' 또는 '인터넷' 범위에서 들어오는 액세스를 허용해서는 안 됩니다. 그러면 리소스가 공격자의 표적이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[API Management 서비스에서 가상 네트워크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure Virtual Network 배포는 향상된 보안, 격리를 제공하며, 액세스를 제어하는 인터넷 라우팅이 불가능한 네트워크에 API Management 서비스를 배치할 수 있습니다. 그런 다음, 다양한 VPN 기술을 사용하여 이러한 네트워크를 온-프레미스 네트워크에 연결할 수 있으며, 이를 통해 네트워크 및/또는 온-프레미스 내에서 백 엔드 서비스에 액세스할 수 있습니다. 개발자 포털 및 API 게이트웨이를 인터넷에서 또는 가상 네트워크 내에서만 액세스할 수 있게 구성할 수 있습니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
|[App Configuration은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 앱 구성 인스턴스에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/appconfig/private-endpoint](../../../azure-app-configuration/concept-private-endpoint.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[권한 있는 IP 범위는 Kubernetes Services에 정의되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |특정 범위의 IP 주소에만 API 액세스 권한을 부여하여 Kubernetes Service Management API에 대한 액세스를 제한합니다. 허용된 네트워크의 애플리케이션만 클러스터에 액세스할 수 있도록 인증된 IP 범위에 대한 액세스를 제한하는 것이 좋습니다. |감사, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|[Azure API for FHIR은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1ee56206-5dd1-42ab-b02d-8aae8b1634ce) |Azure API for FHIR에는 승인된 프라이빗 엔드포인트 연결이 하나 이상 있어야 합니다. 가상 네트워크의 클라이언트는 프라이빗 링크를 통해 프라이빗 엔드포인트 연결이 있는 리소스에 안전하게 액세스할 수 있습니다. 자세한 내용은 [https://aka.ms/fhir-privatelink](../../../healthcare-apis/azure-api-for-fhir/configure-private-link.md)를 방문하세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_PrivateLink_Audit.json) |
|[Azure Cache for Redis는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7803067c-7d34-46e3-8c79-0ca68fc4036d) |프라이빗 엔드포인트를 사용하면 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 엔드포인트를 Azure Cache for Redis 인스턴스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/azure-cache-for-redis/cache-private-link](../../../azure-cache-for-redis/cache-private-link.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Cognitive Search 서비스는 프라이빗 링크를 지원하는 SKU를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa049bf77-880b-470f-ba6d-9f21c530cf83) |Azure Cognitive Search의 지원되는 SKU를 통해 Azure Private Link를 사용하면 원본 또는 대상에서 공용 IP 주소 없이 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Search Service에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePrivateLinkSupportedResource_Deny.json) |
|[Azure Cognitive Search 서비스는 공용 네트워크 액세스를 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee980b6d-0eca-4501-8d54-f6290fd512c3) |공용 네트워크 액세스를 사용하지 않도록 설정하면 Azure Cognitive Search 서비스가 공용 인터넷에 노출되지 않도록 하여 보안이 향상됩니다. 프라이빗 엔드포인트를 만들면 Search Service의 노출을 제한할 수 있습니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_RequirePublicNetworkAccessDisabled_Deny.json) |
|[Azure Cognitive Search 서비스에서 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fda3595-9f2b-4592-8675-4231d6fa82fe) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Cognitive Search에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/azure-cognitive-search/inbound-private-endpoints](../../../search/service-create-private-endpoint.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_PrivateEndpoints_Audit.json) |
|[Azure Cosmos DB 계정에 방화벽 규칙이 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |권한 없는 원본의 트래픽을 방지하기 위해 Azure Cosmos DB 계정에 방화벽 규칙을 정의해야 합니다. 가상 네트워크 필터를 사용하도록 정의된 IP 규칙이 하나 이상 있는 계정은 규정을 준수하는 것으로 간주됩니다. 퍼블릭 액세스를 비활성화한 계정도 규정을 준수하는 것으로 간주됩니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|[Azure Data Factory는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8b0323be-cc25-4b61-935d-002c3798c6ea) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Data Factory에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/data-factory/data-factory-private-link](../../../data-factory/data-factory-private-link.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_PrivateEndpoints_Audit.json) |
|[Azure Event Grid 도메인은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 Event Grid 도메인에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Domains_PrivateEndpoint_Audit.json) |
|[Azure Event Grid 토픽은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스가 아닌 Event Grid 토픽에 프라이빗 엔드포인트를 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/privateendpoints](../../../event-grid/configure-private-endpoints.md)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/Topics_PrivateEndpoint_Audit.json) |
|[Azure 파일 동기화는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |표시된 Storage Sync Service 리소스의 프라이빗 엔드포인트를 만들면 인터넷에서 액세스할 수 있는 퍼블릭 엔드포인트를 사용하지 않고 조직 네트워크의 개인 IP 주소 공간 내에서 Storage Sync Service 리소스를 처리할 수 있습니다. 프라이빗 엔드포인트를 자체적으로 만든다고 해서 퍼블릭 엔드포인트가 비활성화되지 않습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[Azure Key Vault에서 공용 네트워크 액세스를 사용할 수 없음](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F55615ac9-af46-4a59-874e-391cc3dfb490) |공용 인터넷을 통해 액세스할 수 없도록 키 자격 증명 모음에 대한 공용 네트워크 액세스를 사용하지 않도록 설정합니다. 이를 통해 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/akvprivatelink](../../../key-vault/general/private-link-service.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[2.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultFirewallEnabled_Audit.json) |
|[Azure Machine Learning 작업 영역은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Machine Learning 작업 영역에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link](../../../machine-learning/how-to-configure-private-link.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateEndpoint_Audit.json) |
|[Azure Service Bus 네임스페이스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c06e275-d63d-4540-b761-71f364c2111d) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Service Bus 네임스페이스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/service-bus-messaging/private-link-service](../../../service-bus-messaging/private-link-service.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_PrivateEndpoint_Audit.json) |
|[Azure SignalR Service는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53503636-bcc9-4748-9663-5348217f160f) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 전체 서비스가 아닌 Azure SignalR Service 리소스에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/asrs/privatelink](../../../azure-signalr/howto-private-endpoints.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SignalR/SignalR_PrivateEndpointEnabled_Audit.json) |
|[Azure Synapse 작업 영역은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F72d11df1-dd8a-41f7-8925-b05b960ebafc) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Synapse 작업 영역에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/synapse-analytics/security/how-to-connect-to-workspace-with-private-links](../../../synapse-analytics/security/how-to-connect-to-workspace-with-private-links.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceUsePrivateLinks_Audit.json) |
|[Azure Web PubSub 서비스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |Azure Private Link를 사용하면 원본 또는 대상에 공용 IP 주소가 없어도 가상 네트워크를 Azure 서비스에 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Web PubSub 서비스에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[Cognitive Services 계정은 공용 네트워크 액세스를 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0725b4dd-7e76-479c-a735-68e7ee23d5ca) |공용 네트워크 액세스를 사용하지 않도록 설정하면 Cognitive Services 계정이 공용 인터넷에 노출되지 않도록 하여 보안이 향상됩니다. 프라이빗 엔드포인트를 만들면 Cognitive Services 계정의 노출을 제한할 수 있습니다. [https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)에서 자세히 알아보세요.  |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_DisablePublicNetworkAccess_Audit.json) |
|[Cognitive Services 계정은 네트워크 액세스를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F037eea7a-bd0a-46c5-9a66-03aea78705d3) |Cognitive Services 계정에 대한 네트워크 액세스는 제한되어야 합니다. 허용되는 네트워크의 애플리케이션만 Cognitive Services 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용하기 위해 특정 Azure 가상 네트워크에서의 트래픽 또는 공용 인터넷 IP 주소 범위에 액세스 권한을 부여할 수 있습니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_NetworkAcls_Audit.json) |
|[Cognitive Services에서 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcddd188c-4b82-4c48-a19d-ddf74ee66a01) |Azure Private Link를 사용하면 원본 또는 대상에 공용 IP 주소가 없어도 가상 네트워크를 Azure 서비스에 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Cognitive Services로 매핑하면 데이터 누출 가능성이 줄어듭니다. [https://go.microsoft.com/fwlink/?linkid=2129800](../../../private-link/index.yml)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_EnablePrivateEndpoints_Audit.json) |
|[컨테이너 레지스트리는 무제한 네트워크 액세스를 허용하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |기본적으로 Azure Container Registry는 모든 네트워크에 있는 호스트로부터의 인터넷 연결을 수락합니다. 잠재적 위협으로부터 레지스트리를 보호하려면 특정 공용 IP 주소 또는 주소 범위에서만 액세스를 허용합니다. 레지스트리에 IP/방화벽 규칙이나 구성된 가상 네트워크가 없는 경우 상태가 좋지 않은 리소스에 표시됩니다. Container Registry 네트워크 규칙에 대한 자세한 내용은 [https://aka.ms/acr/portal/public-network](../../../container-registry/container-registry-access-selected-networks.md) 및 [https://aka.ms/acr/vnet](../../../container-registry/container-registry-vnet.md)을 참조하세요. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_AuditDeny.json) |
|[컨테이너 레지스트리는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 전체 서비스 대신 프라이빗 엔드포인트를 컨테이너 레지스트리에 매핑하면 데이터 유출 위험으로부터 보호받을 수 있습니다. [https://aka.ms/acr/private-link](../../../container-registry/container-registry-private-link.md)에서 자세히 알아보세요. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[CosmosDB 계정은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58440f8a-10c5-4151-bdce-dfbaad4a20b7) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 CosmosDB 계정에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints](../../../cosmos-db/how-to-configure-private-endpoints.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_PrivateEndpoint_Audit.json) |
|[디스크 액세스 리소스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff39f5f49-4abf-44de-8c70-0756997bfb51) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 diskAccesses에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/disksprivatelinksdoc](../../../virtual-machines/disks-enable-private-links-for-import-export-portal.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 프라이빗 링크에 대해 자세히 알아보세요.  |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DiskAccesses_PrivateEndpoints_Audit.json) |
|[Event Hub 네임스페이스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb8564268-eb4a-4337-89be-a19db070c59d) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Event Hub 네임스페이스에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/event-hubs/private-link-service](../../../event-hubs/private-link-service.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_PrivateEndpoint_Audit.json) |
|[네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |NSG(네트워크 보안 그룹)를 통해 가상 머신에 대한 액세스를 제한하여 잠재적인 위협으로부터 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 IoT Hub 디바이스 프로비저닝 서비스에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://aka.ms/iotdpsvnet](../../../iot-dps/virtual-network-support.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|[가상 머신에서 IP 전달을 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbd352bd5-2853-4985-bf0d-73806b4a5744) |가상 머신의 NIC에서 IP 전달을 사용하도록 설정하면 머신이 다른 대상으로 주소가 지정된 트래픽을 수신할 수 있습니다. IP 전달은 거의 필요하지 않으므로(예: VM을 네트워크 가상 어플라이언스로 사용하는 경우), 네트워크 보안 팀에서 검토해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_IPForwardingOnVirtualMachines_Audit.json) |
|[가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |가능한 네트워크 JIT(Just In Time) 액세스는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[가상 머신에서 관리 포트를 닫아야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |열려 있는 원격 관리 포트는 위험도가 높은 인터넷 기반 공격에 VM을 노출시킵니다. 이러한 공격은 자격 증명을 무차별적으로 대입하여 머신에 대한 관리자 액세스 권한을 획득하려고 시도합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |
|[Microsoft 관리형 컨트롤 1625 - 경계 보호 \| 액세스 지점](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb9b66a4d-70a1-4b47-8fa1-289cec68c605) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1625.json) |
|[네트워크 보안 그룹을 사용하여 비인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbb91dfba-c30d-4263-9add-9c2384e659a6) |NSG(네트워크 보안 그룹)를 통해 액세스를 제한하여 잠재적인 위협으로부터 비인터넷 연결 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternalVirtualMachines_Audit.json) |
|[Azure SQL Database에서 프라이빗 엔드포인트 연결을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7698e800-9299-47a6-b3b6-5a0fee576eed) |프라이빗 엔드포인트 연결은 Azure SQL Database에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. |감사, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PrivateEndpoint_Audit.json) |
|[Key Vault의 프라이빗 엔드포인트를 구성해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0bc445-3935-4915-9981-011aa2b46147) |프라이빗 링크는 공용 인터넷을 통해 트래픽을 보내지 않고 Key Vault를 Azure 리소스에 연결하는 방법을 제공합니다. 프라이빗 링크는 데이터 반출에 대한 심층 방어 기능을 제공합니다. |감사, 거부, 사용 안 함 |[1.1.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/AzureKeyVaultPrivateEndpointEnabled_Audit.json) |
|[프라이빗 엔드포인트를 MariaDB 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |프라이빗 엔드포인트 연결은 Azure Database for MariaDB에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[프라이빗 엔드포인트를 MySQL 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |프라이빗 엔드포인트 연결은 Azure Database for MySQL에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[프라이빗 엔드포인트를 PostgreSQL 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0564d078-92f5-4f97-8398-b9f58a51f70b) |프라이빗 엔드포인트 연결은 Azure Database for PostgreSQL에 대한 프라이빗 연결을 사용하도록 설정하여 보안 통신을 강화합니다. 알려진 네트워크에서 들어오는 트래픽에만 액세스할 수 있고 Azure 내부를 비롯한 다른 IP 주소의 액세스를 차단하도록 프라이빗 엔드포인트 연결을 구성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnablePrivateEndPoint_Audit.json) |
|[Azure SQL Database에서 공용 네트워크 액세스를 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b8ca024-1d5c-4dec-8995-b1a932b41780) |공용 네트워크 액세스 속성을 사용하지 않도록 설정하면 프라이빗 엔드포인트에서만 Azure SQL Database에 액세스할 수 있도록 하여 보안이 향상됩니다. 이 구성은 IP 또는 가상 네트워크 기반 방화벽 규칙과 일치하는 모든 로그인을 거부합니다. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_PublicNetworkAccess_Audit.json) |
|[MariaDB 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |공용 네트워크 액세스 속성을 사용하지 않도록 설정하여 보안을 강화하고 프라이빗 엔드포인트에서만 Azure Database for MariaDB에 액세스할 수 있도록 합니다. 이 구성은 Azure IP 범위를 벗어나는 공용 주소 공간의 액세스를 엄격하게 차단하고, IP 또는 가상 네트워크 기반 방화벽 규칙에 부합하는 모든 로그인을 거부합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
|[MySQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |공용 네트워크 액세스 속성을 사용하지 않도록 설정하여 보안을 강화하고 프라이빗 엔드포인트에서만 Azure Database for MySQL에 액세스할 수 있도록 합니다. 이 구성은 Azure IP 범위를 벗어나는 공용 주소 공간의 액세스를 엄격하게 차단하고, IP 또는 가상 네트워크 기반 방화벽 규칙에 부합하는 모든 로그인을 거부합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
|[PostgreSQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb52376f7-9612-48a1-81cd-1ffe4b61032c) |공용 네트워크 액세스 속성을 사용하지 않도록 설정하여 보안을 강화하고 프라이빗 엔드포인트에서만 Azure Database for PostgreSQL에 액세스할 수 있도록 합니다. 이 구성은 Azure IP 범위를 벗어나는 공용 주소 공간에서의 액세스를 사용하지 않도록 설정하고, IP 또는 가상 네트워크 기반 방화벽 규칙과 일치하는 모든 로그인을 거부합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_DisablePublicNetworkAccess_Audit.json) |
|[스토리지 계정 공용 액세스가 허용되지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Azure Storage의 컨테이너 및 BLOB에 대한 익명 공용 읽기 액세스는 데이터를 공유하는 편리한 방법이지만 보안 위험이 있을 수도 있습니다. 원치 않는 익명 액세스로 인해 발생하는 데이터 위반을 방지하기 위해 Microsoft는 시나리오에 필요한 경우가 아니면 스토리지 계정에 대한 공개 액세스를 방지하는 것이 좋습니다 |감사, 거부, 사용 안 함 |[3.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |
|[스토리지 계정은 네트워크 액세스를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |스토리지 계정에 대한 네트워크 액세스가 제한되어야 합니다. 허용되는 네트워크의 애플리케이션만 스토리지 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용하기 위해 특정 Azure 가상 네트워크 또는 공용 인터넷 IP 주소 범위의 트래픽에 대한 액세스 권한을 부여할 수 있습니다. |감사, 거부, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[스토리지 계정은 가상 네트워크 규칙을 사용하여 네트워크 액세스를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |IP 기반 필터링 대신 기본 방법으로 가상 네트워크 규칙을 사용하여 잠재적인 위협으로부터 스토리지 계정을 보호합니다. IP 기반 필터링을 사용하지 않도록 설정하면 공용 IP가 스토리지 계정에 액세스할 수 없습니다. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |
|[스토리지 계정은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6edd7eda-6dd8-40f7-810d-67160c639cd9) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 스토리지 계정에 매핑하면 데이터 유출 위험이 줄어듭니다. [https://aka.ms/azureprivatelinkoverview](../../../private-link/private-link-overview.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountPrivateEndpointEnabled_Audit.json) |
|[서브넷을 네트워크 보안 그룹과 연결해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |NSG(네트워크 보안 그룹)를 통해 VM에 대한 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. NSG는 서브넷에 대한 네트워크 트래픽을 허용 또는 거부하는 ACL(액세스 제어 목록) 규칙의 목록을 포함합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[VM Image Builder 템플릿은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 VM Image Builder에 매핑하여 리소스를 구성하면 데이터 누출 위험이 줄어듭니다. [https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-networking#deploy-using-an-existing-vnet](../../../virtual-machines/linux/image-builder-networking.md#deploy-using-an-existing-vnet)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 사용 안 함, 거부 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
|[Application Gateway에 WAF(웹 애플리케이션 방화벽)를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F564feb30-bf6a-4854-b4bb-0d2d2d1e6c66) |들어오는 트래픽의 추가 검사를 위해 공용 웹 애플리케이션 앞에 Azure WAF(웹 애플리케이션 방화벽)를 배포합니다. WAF(웹 애플리케이션 방화벽)는 SQL 삽입, 교차 사이트 스크립팅, 로컬 및 원격 파일 실행과 같은 일반적인 악용과 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호합니다. 사용자 지정 규칙을 통해 국가, IP 주소 범위 및 기타 http 매개 변수별로 웹 애플리케이션에 대한 액세스를 제한할 수도 있습니다. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AppGatewayEnabled_Audit.json) |
|[WAF(Web Application Firewall)를 Azure Front Door Service 서비스에 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F055aa869-bc98-4af8-bafc-23f1ab6ffe2c) |들어오는 트래픽의 추가 검사를 위해 공용 웹 애플리케이션 앞에 Azure WAF(웹 애플리케이션 방화벽)를 배포합니다. WAF(웹 애플리케이션 방화벽)는 SQL 삽입, 교차 사이트 스크립팅, 로컬 및 원격 파일 실행과 같은 일반적인 악용과 취약성으로부터 웹 애플리케이션을 중앙 집중식으로 보호합니다. 사용자 지정 규칙을 통해 국가, IP 주소 범위 및 기타 http 매개 변수별로 웹 애플리케이션에 대한 액세스를 제한할 수도 있습니다. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/WAF_AFD_Enabled_Audit.json) |

### <a name="external-telecommunications-services"></a>외부 원격 통신 서비스

**ID**: NIST SP 800-53 Rev. 4 SC-7 (4)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1626 - 경계 보호 \| 외부 원격 통신 서비스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8f6bddd-6d67-439a-88d4-c5fe39a79341) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1626.json) |
|[Microsoft 관리형 컨트롤 1627 - 경계 보호 \| 외부 원격 통신 서비스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd73310d-76fc-422d-bda4-3a077149f179) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1627.json) |
|[Microsoft 관리형 컨트롤 1628 - 경계 보호 \| 외부 원격 통신 서비스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67de62b4-a737-4781-8861-3baed3c35069) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1628.json) |
|[Microsoft 관리형 컨트롤 1629 - 경계 보호 \| 외부 원격 통신 서비스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc171b095-7756-41de-8644-a062a96043f2) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1629.json) |
|[Microsoft 관리형 컨트롤 1630 - 경계 보호 \| 외부 원격 통신 서비스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3643717a-3897-4bfd-8530-c7c96b26b2a0) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1630.json) |

### <a name="deny-by-default--allow-by-exception"></a>기본적으로 거부/예외적으로 허용

**ID**: NIST SP 800-53 Rev. 4 SC-7 (5)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1631 - 경계 보호 \| 기본적으로 거부/예외적으로 허용](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74ae9b8e-e7bb-4c9c-992f-c535282f7a2c) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1631.json) |

### <a name="prevent-split-tunneling-for-remote-devices"></a>원격 디바이스에 대한 분할 터널링 방지

**ID**: NIST SP 800-53 Rev. 4 SC-7 (7)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1632 - 경계 보호 \| 원격 디바이스에 대한 분할 터널링 방지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ce9073a-77fa-48f0-96b1-87aa8e6091c2) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1632.json) |

### <a name="route-traffic-to-authenticated-proxy-servers"></a>인증된 프록시 서버로 트래픽 라우팅

**ID**: NIST SP 800-53 Rev. 4 SC-7 (8)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1633 - 경계 보호 \| 인증된 프록시 서버로 트래픽 라우팅](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F07557aa0-e02f-4460-9a81-8ecd2fed601a) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1633.json) |

### <a name="prevent-unauthorized-exfiltration"></a>무단 반출 방지

**ID**: NIST SP 800-53 Rev. 4 SC-7 (10)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1634 - 경계 보호 \| 무단 반출 방지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F292a7c44-37fa-4c68-af7c-9d836955ded2) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1634.json) |

### <a name="host-based-protection"></a>호스트 기반 보호

**ID**: NIST SP 800-53 Rev. 4 SC-7 (12)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1635 - 경계 보호 \| 호스트 기반 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87551b5d-1deb-4d0f-86cc-9dc14cb4bf7e) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1635.json) |

### <a name="isolation-of-security-tools--mechanisms--support-components"></a>보안 도구/메커니즘/지원 구성 요소 격리

**ID**: NIST SP 800-53 Rev. 4 SC-7 (13)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1636 - 경계 보호 \| 보안 도구/메커니즘/지원 구성 요소 격리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7b694eed-7081-43c6-867c-41c76c961043) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1636.json) |

### <a name="fail-secure"></a>보안 실패

**ID**: NIST SP 800-53 Rev. 4 SC-7 (18)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1637 - 경계 보호 \| 보안 실패](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4075bedc-c62a-4635-bede-a01be89807f3) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1637.json) |

### <a name="dynamic-isolation--segregation"></a>동적 격리/분리

**ID**: NIST SP 800-53 Rev. 4 SC-7 (20)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1638 - 경계 보호 \| 동적 격리/분리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F49b99653-32cd-405d-a135-e7d60a9aae1f) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1638.json) |

### <a name="isolation-of-information-system-components"></a>정보 시스템 구성 요소 격리

**ID**: NIST SP 800-53 Rev. 4 SC-7 (21)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1639 - 경계 보호 \| 정보 시스템 구성 요소 격리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F78e8e649-50f6-4fe3-99ac-fedc2e63b03f) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1639.json) |

### <a name="transmission-confidentiality-and-integrity"></a>전송 기밀성 및 무결성

**ID**: NIST SP 800-53 Rev. 4 SC-8

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Azure HDInsight 클러스터는 전송 중 암호화를 사용하여 Azure HDInsight 클러스터 노드 간 통신을 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |데이터는 Azure HDInsight 클러스터 노드 간 전송 중에 변조될 수 있습니다. 전송 중 암호화를 사용하도록 설정하면 전송 중 오용 및 변조 문제가 해결됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
|[MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL용 Azure Database는 SSL(Secure Sockets Layer)을 사용한 MySQL용 Azure Database 서버와 클라이언트 애플리케이션 간 연결을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL은 SSL(Secure Sockets Layer)을 사용하여 Azure Database for PostgreSQL 서버를 클라이언트 애플리케이션에 연결하는 것을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[API 앱에서 FTPS만 요구해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |강화된 보안을 위해 FTPS 적용 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[함수 앱에서 FTPS만 요구해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |강화된 보안을 위해 FTPS 적용 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[웹앱에서 FTPS를 요구해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |강화된 보안을 위해 FTPS 적용 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |
|[함수 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Kubernetes 클러스터는 HTTPS를 통해서만 액세스할 수 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a5b4dca-0b6f-4cf5-907c-56316bc1bf3d) |HTTPS를 사용하여 인증을 보장하고, 네트워크 계층 도청 공격으로부터 전송 중인 데이터를 보호합니다. 이 기능은 현재 AKS(Kubernetes Service)로 일반 공급되며, AKS 엔진 및 Azure Arc 지원 Kubernetes에서 미리 보기로 제공됩니다. 자세한 내용은 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)을 방문하세요. |감사, 거부, 사용 안 함 |[6.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/IngressHttpsOnly.json) |
|[API 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[함수 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[웹앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Microsoft 관리형 컨트롤 1640 - 전송 기밀성 및 무결성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F05a289ce-6a20-4b75-a0f3-dc8601b6acd0) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1640.json) |
|[Azure Cache for Redis에 보안 연결만 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |SSL을 통해 설정된 Azure Cache for Redis 연결만 감사 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[스토리지 계정에 보안 전송을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |스토리지 계정의 보안 전송 요구 사항을 감사합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[웹 애플리케이션은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |
|[Windows 웹 서버는 보안 통신 프로토콜을 사용하도록 구성해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |인터넷을 통해 전달되는 정보의 개인 정보를 보호하려면 웹 서버에서 최신 버전의 업계 표준 암호화 프로토콜인 TLS(전송 계층 보안)를 사용해야 합니다. TLS는 보안 인증서를 사용하여 컴퓨터 간의 연결을 암호화하여 네트워크를 통한 통신을 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |

### <a name="cryptographic-or-alternate-physical-protection"></a>암호화 또는 대체 물리적 보호

**ID**: NIST SP 800-53 Rev. 4 SC-8 (1)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Azure HDInsight 클러스터는 전송 중 암호화를 사용하여 Azure HDInsight 클러스터 노드 간 통신을 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9da03a1-f3c3-412a-9709-947156872263) |데이터는 Azure HDInsight 클러스터 노드 간 전송 중에 변조될 수 있습니다. 전송 중 암호화를 사용하도록 설정하면 전송 중 오용 및 변조 문제가 해결됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionInTransit_Audit.json) |
|[MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL용 Azure Database는 SSL(Secure Sockets Layer)을 사용한 MySQL용 Azure Database 서버와 클라이언트 애플리케이션 간 연결을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL은 SSL(Secure Sockets Layer)을 사용하여 Azure Database for PostgreSQL 서버를 클라이언트 애플리케이션에 연결하는 것을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[API 앱에서 FTPS만 요구해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |강화된 보안을 위해 FTPS 적용 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[함수 앱에서 FTPS만 요구해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |강화된 보안을 위해 FTPS 적용 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[웹앱에서 FTPS를 요구해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |강화된 보안을 위해 FTPS 적용 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |
|[함수 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[Kubernetes 클러스터는 HTTPS를 통해서만 액세스할 수 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a5b4dca-0b6f-4cf5-907c-56316bc1bf3d) |HTTPS를 사용하여 인증을 보장하고, 네트워크 계층 도청 공격으로부터 전송 중인 데이터를 보호합니다. 이 기능은 현재 AKS(Kubernetes Service)로 일반 공급되며, AKS 엔진 및 Azure Arc 지원 Kubernetes에서 미리 보기로 제공됩니다. 자세한 내용은 [https://aka.ms/kubepolicydoc](../concepts/policy-for-kubernetes.md)을 방문하세요. |감사, 거부, 사용 안 함 |[6.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/IngressHttpsOnly.json) |
|[API 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[함수 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[웹앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Microsoft 관리형 컨트롤 1641 - 전송 기밀성 및 무결성 \| 암호화 또는 대체 물리적 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd39d4f68-7346-4133-8841-15318a714a24) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1641.json) |
|[Azure Cache for Redis에 보안 연결만 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |SSL을 통해 설정된 Azure Cache for Redis 연결만 감사 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[스토리지 계정에 보안 전송을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |스토리지 계정의 보안 전송 요구 사항을 감사합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[웹 애플리케이션은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |
|[Windows 웹 서버는 보안 통신 프로토콜을 사용하도록 구성해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5752e6d6-1206-46d8-8ab1-ecc2f71a8112) |인터넷을 통해 전달되는 정보의 개인 정보를 보호하려면 웹 서버에서 최신 버전의 업계 표준 암호화 프로토콜인 TLS(전송 계층 보안)를 사용해야 합니다. TLS는 보안 인증서를 사용하여 컴퓨터 간의 연결을 암호화하여 네트워크를 통한 통신을 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecureWebProtocol_AINE.json) |

### <a name="network-disconnect"></a>네트워크 연결 끊기

**ID**: NIST SP 800-53 Rev. 4 SC-10

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1642 - 네트워크 연결 해제](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53397227-5ee3-4b23-9e5e-c8a767ce6928) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1642.json) |

### <a name="cryptographic-key-establishment-and-management"></a>암호화 키 설정 및 관리

**ID**: NIST SP 800-53 Rev. 4 SC-12

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure API for FHIR은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F051cba44-2429-45b9-9649-46cec11c7119) |규정 또는 규정 준수 요구 사항인 경우 고객 관리형 키를 사용하여 Azure API for FHIR에 저장된 데이터의 저장 데이터 암호화를 제어합니다. 또한 고객 관리형 키는 서비스 관리형 키를 사용하여 수행되는 기본 암호화 계층 위에 두 번째 암호화 계층을 추가하여 이중 암호화를 제공합니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_EnableByok_Audit.json) |
|[Azure Automation 계정은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56a5ee18-2ae6-4810-86f7-18e39ce5629b) |고객 관리형 키를 사용하여 Azure Automation 계정의 미사용 데이터 암호화를 관리합니다. 기본적으로 고객 데이터는 서비스 관리형 키로 암호화되지만, 고객 관리형 키는 일반적으로 규정 준수 기준을 충족하는 데 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. [https://aka.ms/automation-cmk](../../../automation/automation-secure-asset-encryption.md#:~:text=Secure assets in Azure Automation include credentials, certificates, connections,,Using Microsoft-managed keys)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/AutomationAccount_CMK_Audit.json) |
|[Azure Batch 계정은 고객 관리형 키를 사용하여 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F99e9ccd8-3db9-4592-b0d1-14b1715a4d8a) |고객 관리형 키를 사용하여 Batch 계정 데이터의 미사용 데이터 암호화를 관리합니다. 기본적으로 고객 데이터는 서비스 관리형 키로 암호화되지만, 고객 관리형 키는 일반적으로 규정 준수 기준을 충족하는 데 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. [https://aka.ms/Batch-CMK](https://aka.ms/Batch-CMK)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_CustomerManagedKey_Audit.json) |
|[Azure Container Instance 컨테이너 그룹은 암호화에 고객 관리형 키를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0aa61e00-0a01-4a3c-9945-e93cffedf0e6) |고객 관리형 키를 사용하여 유연성이 뛰어난 컨테이너를 보호합니다. 고객 관리형 키를 지정하는 경우 해당 키는 데이터를 암호화하는 키에 대한 액세스를 보호하고 제어하는 데 사용됩니다. 고객 관리형 키를 사용하면 키 암호화 키의 회전을 제어하거나 데이터를 암호화 방식으로 지우는 추가 기능이 제공됩니다. |감사, 사용 안 함, 거부 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_CMK_Audit.json) |
|[Azure Cosmos DB 계정은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f905d99-2ab7-462c-a6b0-f709acca6c8f) |고객 관리형 키를 사용하여 Azure Cosmos DB의 미사용 데이터 암호화를 관리합니다. 기본적으로 저장 데이터는 서비스 관리형 키를 사용하여 암호화되지만, 일반적으로 규정 준수 표준을 충족하려면 고객 관리형 키가 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. [https://aka.ms/cosmosdb-cmk](../../../cosmos-db/how-to-setup-cmk.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_CMK_Deny.json) |
|[Azure Data Box 작업에서 고객 관리형 키를 사용하여 디바이스 잠금 해제 암호를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |고객 관리형 키를 사용하여 Azure Data Box에 대한 디바이스 잠금 해제 암호의 암호화를 제어합니다. 고객 관리형 키는 디바이스를 준비하고 자동화된 방식으로 데이터를 복사하기 위해 Data Box 서비스에서 디바이스 잠금 해제 암호에 대한 액세스를 관리하는 데도 도움이 됩니다. 디바이스 자체의 데이터는 Advanced Encryption Standard 256비트 암호화를 사용하여 미사용 상태로 이미 암호화되어 있으며, 디바이스 잠금 해제 암호는 기본적으로 Microsoft 관리형 키를 사용하여 암호화됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
|[Azure Data Explorer 저장 데이터 암호화는 고객 관리형 키를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Azure Data Explorer 클러스터에서 고객 관리형 키를 사용하여 저장 데이터 암호화를 활성화하면 저장 데이터 암호화에 사용되는 키를 추가로 제어할 수 있습니다. 이 기능은 종종 특별한 규정 준수 요구 사항을 충족하는 고객에게 적용되며, 키 관리를 위해 Key Vault가 필요합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[Azure Data Factory는 고객 관리형 키로 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4ec52d6d-beb7-40c4-9a9e-fe753254690e) |고객 관리형 키를 사용하여 Azure Data Factory의 미사용 데이터 암호화를 관리합니다. 기본적으로 고객 데이터는 서비스 관리형 키로 암호화되지만, 고객 관리형 키는 일반적으로 규정 준수 기준을 충족하는 데 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. [https://aka.ms/adf-cmk](../../../data-factory/enable-customer-managed-key.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Factory/DataFactory_CustomerManagedKey_Audit.json) |
|[Azure HDInsight 클러스터는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F64d314f6-6062-4780-a861-c23e8951bee5) |고객 관리형 키를 사용하여 Azure HDInsight 클러스터의 미사용 데이터 암호화를 관리합니다. 기본적으로 고객 데이터는 서비스 관리형 키로 암호화되지만, 고객 관리형 키는 일반적으로 규정 준수 기준을 충족하는 데 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. [https://aka.ms/hdi.cmk](../../../hdinsight/disk-encryption.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_CMK_Audit.json) |
|[Azure HDInsight 클러스터는 호스트에서 암호화를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fd32ebd-e4c3-4e13-a54a-d7422d4d95f6) |호스트에서 암호화를 사용하도록 설정하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. 호스트에서 암호화를 사용하도록 설정하면 VM 호스트에 저장된 데이터는 미사용 및 스토리지 서비스로 암호화된 흐름으로 암호화됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/HDInsight/HDInsight_EncryptionAtHost_Audit.json) |
|[Azure Machine Learning 작업 영역은 고객 관리형 키를 사용하여 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fba769a63-b8cc-4b2d-abf6-ac33c7204be8) |고객 관리형 키를 사용하여 Azure Machine Learning 작업 영역의 미사용 데이터 암호화를 관리합니다. 기본적으로 고객 데이터는 서비스 관리형 키로 암호화되지만, 고객 관리형 키는 일반적으로 규정 준수 기준을 충족하는 데 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. [https://aka.ms/azureml-workspaces-cmk](../../../machine-learning/how-to-create-workspace-template.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_CMKEnabled_Audit.json) |
|[Azure Monitor 로그 클러스터는 고객 관리형 키로 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f68a601-6e6d-4e42-babf-3f643a047ea2) |고객 관리형 키 암호화를 사용하여 Azure Monitor 로그 클러스터를 만듭니다. 기본적으로 로그 데이터는 서비스 관리형 키를 사용하여 암호화되지만, 일반적으로 규정 준수를 충족하려면 고객 관리형 키가 필요합니다. Azure Monitor에서 고객 관리형 키를 사용하면 데이터에 대한 액세스를 보다 강력하게 제어할 수 있습니다. [https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys](../../../azure-monitor/logs/customer-managed-keys.md)를 참조하세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalyticsClusters_CMKEnabled_Deny.json) |
|[Azure Recovery Services 자격 증명 모음에서 백업 데이터를 암호화하려면 고객 관리형 키를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e94d99a-8a36-4563-bc77-810d8893b671) |고객 관리형 키를 사용하여 백업 데이터의 미사용 데이터 암호화를 관리합니다. 기본적으로 고객 데이터는 서비스 관리형 키를 사용하여 암호화되지만, 일반적으로 규정 준수 표준을 충족하려면 고객 관리형 키가 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. [https://aka.ms/AB-CmkEncryption](../../../backup/encryption-at-rest-with-cmk.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/AzBackupRSVault_CMKEnabled_Audit.json) |
|[Azure Stream Analytics 작업은 고객 관리형 키를 사용하여 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F87ba29ef-1ab3-4d82-b763-87fcd4f531f7) |스토리지 계정에 Stream Analytics 작업의 메타데이터 및 프라이빗 데이터 자산을 모두 안전하게 저장하려면 고객 관리형 키를 사용합니다. 이를 통해 Stream Analytics 데이터가 암호화되는 방식을 완전히 제어할 수 있습니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_CMK_Audit.json) |
|[Azure Synapse 작업 영역은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff7d52b2d-e161-4dfa-a82b-55e564167385) |고객 관리형 키를 사용하여 Azure Synapse 작업 영역에 저장된 데이터의 저장 데이터 암호화를 제어합니다. 고객 관리형 키는 서비스 관리형 키를 사용하여 수행되는 기본 암호화 위에 두 번째 암호화 계층을 추가하여 이중 암호화를 제공합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Synapse/SynapseWorkspaceCMK_Audit.json) |
|[Bot Service는 고객 관리형 키로 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service는 리소스를 자동으로 암호화하여 데이터를 보호하고 조직의 보안 및 규정 준수 약정을 충족합니다. 기본적으로 Microsoft 관리형 암호화 키가 사용됩니다. 키 관리 또는 구독에 대한 액세스 제어의 유연성을 향상하려면 BYOK(Bring Your Own Key)라고도 하는 고객 관리형 키를 선택합니다. Azure Bot Service 암호화에 대한 자세한 정보: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](/azure/bot-service/bot-service-encryption). |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
|[Azure Kubernetes Service 클러스터의 운영 체제와 데이터 디스크를 모두 고객 관리형 키로 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d7be79c-23ba-4033-84dd-45e2a5ccdd67) |고객 관리형 키를 사용하여 OS 및 데이터 디스크를 암호화하면 키 관리를 더 효율적으로 제어하고 더 유연하게 수행할 수 있습니다. 이는 다양한 규정 및 업계 규정 준수 표준의 공통 요구 사항입니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_CMK_Deny.json) |
|[Cognitive Services 계정은 고객 관리형 키를 사용하여 데이터를 암호화하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F67121cc7-ff39-4ab8-b7e3-95b84dab487d) |고객 관리형 키는 일반적으로 규정 준수 표준을 충족하는 데 필요합니다. 고객 관리형 키를 사용하면 Cognitive Services에 저장된 데이터를 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. [https://go.microsoft.com/fwlink/?linkid=2121321](../../../cognitive-services/encryption/cognitive-services-encryption-keys-portal.md)에서 고객 관리형 키에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cognitive%20Services/CognitiveServices_CustomerManagedKey_Audit.json) |
|[컨테이너 레지스트리는 고객 관리형 키를 사용하여 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |고객 관리형 키를 사용하여 레지스트리 콘텐츠의 미사용 데이터 암호화를 관리합니다. 기본적으로 저장 데이터는 서비스 관리형 키를 사용하여 암호화되지만, 일반적으로 규정 준수 표준을 충족하려면 고객 관리형 키가 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. [https://aka.ms/acr/CMK](../../../container-registry/container-registry-customer-managed-keys.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.1.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Event Hub 네임스페이스는 암호화에 고객 관리형 키를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1ad735a-e96f-45d2-a7b2-9a4932cab7ec) |Azure Event Hubs는 Microsoft 관리형 키(기본값) 또는 고객 관리형 키를 사용하여 미사용 데이터를 암호화하는 옵션을 지원합니다. 고객 관리형 키를 사용하여 데이터를 암호화하도록 선택하면 Event Hub가 네임스페이스의 데이터를 암호화하는 데 사용할 키에 대한 액세스 권한을 할당, 회전, 비활성화 및 취소할 수 있습니다. Event Hub는 전용 클러스터의 네임스페이스에 대해 고객 관리형 키를 사용한 암호화만 지원합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_CustomerManagedKeyEnabled_Audit.json) |
|[HPC Cache 계정은 암호화에 고객 관리형 키를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F970f84d8-71b6-4091-9979-ace7e3fb6dbb) |고객 관리형 키를 사용하여 Azure HPC Cache의 미사용 데이터 암호화를 관리합니다. 기본적으로 고객 데이터는 서비스 관리형 키를 사용하여 암호화되지만, 일반적으로 규정 준수 표준을 충족하려면 고객 관리형 키가 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. |감사, 사용 안 함, 거부 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageCache_CMKEnabled.json) |
|[IoT Hub Device Provisioning Service 데이터는 CMK(고객 관리형 키)를 사용하여 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47031206-ce96-41f8-861b-6a915f3de284) |고객 관리형 키를 사용하여 IoT Hub 디바이스 프로비저닝 서비스의 미사용 데이터 암호화를 관리합니다. 데이터는 서비스 관리형 키를 사용하여 자동으로 미사용 데이터가 암호화되지만, CMK(고객 관리형 키)는 일반적으로 규정 준수 기준을 충족하는 데 필요합니다. CMK를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키로 데이터를 암호화할 수 있습니다. [https://aka.ms/dps/CMK](../../../iot-dps/iot-dps-customer-managed-keys.md)에서 CMK 암호화에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_CMKEncryptionEnabled_AuditDeny.json) |
|[Logic Apps 통합 서비스 환경은 고객 관리형 키로 암호화되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1fafeaf6-7927-4059-a50a-8eb2a7a6f2b5) |통합 서비스 환경에 배포하여 고객 관리형 키를 사용하여 미사용 Logic Apps 데이터의 암호화를 관리합니다. 기본적으로 고객 데이터는 서비스 관리형 키를 사용하여 암호화되지만, 일반적으로 규정 준수 표준을 충족하려면 고객 관리형 키가 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_ISEWithCustomerManagedKey_AuditDeny.json) |
|[관리 디스크는 플랫폼 관리형 및 고객 관리형 키를 둘 다 사용하여 이중 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca91455f-eace-4f96-be59-e6e2c35b4816) |특정 암호화 알고리즘, 구현 또는 손상되는 키와 연결된 위험에 관해 우려하는 높은 수준의 보안을 중요시하는 고객은 플랫폼 관리형 암호화 키를 사용하는 인프라 계층에서 다른 암호화 알고리즘/모드를 사용하는 추가적인 암호화 계층을 선택할 수 있습니다. 이중 암호화를 사용하려면 디스크 암호화 집합이 필요합니다. [https://aka.ms/disks-doubleEncryption](../../../virtual-machines/disk-encryption.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/DoubleEncryptionRequired_Deny.json) |
|[Microsoft 관리형 컨트롤 1643 - 암호화 키 설정 및 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d8d492c-dd7a-46f7-a723-fa66a425b87c) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1643.json) |
|[MySQL 서버는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83cef61d-dbd1-4b20-a4fc-5fbc7da10833) |고객 관리형 키를 사용하여 MySQL 서버의 미사용 데이터 암호화를 관리합니다. 기본적으로 저장 데이터는 서비스 관리형 키를 사용하여 암호화되지만, 일반적으로 규정 준수 표준을 충족하려면 고객 관리형 키가 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.4](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableByok_Audit.json) |
|[OS 및 데이터 디스크는 고객 관리형 키로 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F702dd420-7fcc-42c5-afe8-4026edd20fe0) |고객 관리형 키를 사용하여 관리 디스크 콘텐츠의 미사용 데이터 암호화를 관리합니다. 기본적으로 미사용 데이터는 플랫폼 관리형 키를 사용하여 암호화되지만, 일반적으로 규정 준수 표준을 충족하려면 고객 관리형 키가 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. [https://aka.ms/disks-cmk](../../../virtual-machines/disk-encryption.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/OSAndDataDiskCMKRequired_Deny.json) |
|[PostgreSQL 서버는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F18adea5e-f416-4d0f-8aa8-d24321e3e274) |고객 관리형 키를 사용하여 PostgreSQL 서버의 미사용 데이터 암호화를 관리합니다. 기본적으로 저장 데이터는 서비스 관리형 키를 사용하여 암호화되지만, 일반적으로 규정 준수 표준을 충족하려면 고객 관리형 키가 필요합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure Key Vault 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.4](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableByok_Audit.json) |
|[로그 암호화를 위해 Azure Monitor에 저장된 쿼리를 고객 스토리지 계정에 저장해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffa298e57-9444-42ba-bf04-86e8470e32c7) |저장소 계정을 Log Analytics 작업 영역에 연결하여 저장소 계정 암호화로 저장된 쿼리를 보호합니다. 고객 관리형 키는 일반적으로 규정 준수를 충족하고 Azure Monitor에 저장된 쿼리에 대한 액세스를 보다 강력하게 제어하는 데 필요합니다. 위의 항목에 대한 자세한 내용은 [https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys?tabs=portal](../../../azure-monitor/logs/customer-managed-keys.md?tabs=portal)를 참조하세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalyticsWorkspaces_CMKBYOSQueryEnabled_Deny.json) |
|[Service Bus 프리미엄 네임스페이스는 암호화에 고객 관리형 키를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F295fc8b1-dc9f-4f53-9c61-3f313ceab40a) |Azure Service Bus는 Microsoft 관리형 키(기본값) 또는 고객 관리형 키를 사용하여 미사용 데이터를 암호화하는 옵션을 지원합니다. 고객 관리형 키를 사용하여 데이터를 암호화하도록 선택하면 Service Bus가 네임스페이스의 데이터를 암호화하는 데 사용할 키에 대한 액세스 권한을 할당, 회전, 비활성화 및 취소할 수 있습니다. Service Bus는 프리미엄 네임스페이스에 대해 고객 관리형 키를 사용한 암호화만 지원합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_CustomerManagedKeyEnabled_Audit.json) |
|[SQL Managed Instance는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |자체 키를 사용하여 TDE(투명한 데이터 암호화)를 구현하면 TDE 보호기에 대한 투명성과 제어력이 향상되고, HSM 지원 외부 서비스를 통한 보안이 강화되며, 업무 분리 프로모션을 제공합니다. 이 권장 사항은 관련 규정 준수 요구 사항이 있는 조직에 적용됩니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL 서버는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |자체 키를 사용하여 TDE(투명한 데이터 암호화)를 구현하면 TDE 보호기에 대한 투명성과 제어력이 향상되고, HSM 지원 외부 서비스를 통해 보안이 강화되며, 업무 분리 프로모션을 제공합니다. 이 권장 사항은 관련 규정 준수 요구 사항이 있는 조직에 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[스토리지 계정 암호화 범위에서 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5ec538c-daa0-4006-8596-35468b9148e8) |고객 관리형 키를 사용하여 스토리지 계정 암호화 범위의 미사용 데이터 암호화를 관리합니다. 고객 관리형 키를 사용하면 사용자가 만들고 소유한 Azure 키 자격 증명 모음 키를 사용하여 데이터를 암호화할 수 있습니다. 순환 및 관리를 포함하여 키의 수명 주기를 고객이 모두 제어하고 책임져야 합니다. [https://aka.ms/encryption-scopes-overview](../../../storage/blobs/encryption-scope-overview.md)에서 스토리지 계정 암호화 범위에 대해 자세히 알아봅니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_EncryptionScopesShouldUseCMK_Audit.json) |
|[스토리지 계정은 고객 관리형 키를 사용하여 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fac406b-40ca-413b-bf8e-0bf964659c25) |고객 관리형 키를 사용하여 유연성이 뛰어난 Blob 및 파일 스토리지 계정을 보호합니다. 고객 관리형 키를 지정하는 경우 해당 키는 데이터를 암호화하는 키에 대한 액세스를 보호하고 제어하는 데 사용됩니다. 고객 관리형 키를 사용하면 키 암호화 키의 회전을 제어하거나 데이터를 암호화 방식으로 지우는 추가 기능이 제공됩니다. |감사, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountCustomerManagedKeyEnabled_Audit.json) |

### <a name="availability"></a>가용성

**ID**: NIST SP 800-53 Rev. 4 SC-12 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1644 - 암호화 키 설정 및 관리 \| 가용성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7211477-c970-446b-b4af-062f37461147) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1644.json) |

### <a name="symmetric-keys"></a>대칭 키

**ID**: NIST SP 800-53 Rev. 4 SC-12 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1645 - 암호화 키 설정 및 관리 \| 대칭 키](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafbd0baf-ff1a-4447-a86f-088a97347c0c) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1645.json) |

### <a name="asymmetric-keys"></a>비대칭 키

**ID**: NIST SP 800-53 Rev. 4 SC-12 (3)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1646 - 암호화 키 설정 및 관리 \| 비대칭 키](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F506814fa-b930-4b10-894e-a45b98c40e1a) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1646.json) |

### <a name="cryptographic-protection"></a>암호화 보호

**ID**: NIST SP 800-53 Rev. 4 SC-13

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1647 - 암호화 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F791cfc15-6974-42a0-9f4c-2d4b82f4a78c) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1647.json) |

### <a name="collaborative-computing-devices"></a>공동 컴퓨팅 디바이스

**ID**: NIST SP 800-53 Rev. 4 SC-15

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1648 - 협업 컴퓨팅 디바이스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a9eb14b-495a-4ebb-933c-ce4ef5264e32) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1648.json) |
|[Microsoft 관리형 컨트롤 1649 - 협업 컴퓨팅 디바이스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26d292cc-b0b8-4c29-9337-68abc758bf7b) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1649.json) |

### <a name="public-key-infrastructure-certificates"></a>공개 키 인프라 인증서

**ID**: NIST SP 800-53 Rev. 4 SC-17

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1650 - 공개 키 인프라 인증서](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F201d3740-bd16-4baf-b4b8-7cda352228b7) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1650.json) |

### <a name="mobile-code"></a>모바일 코드

**ID**: NIST SP 800-53 Rev. 4 SC-18

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1651 - 모바일 코드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6db63528-c9ba-491c-8a80-83e1e6977a50) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1651.json) |
|[Microsoft 관리형 컨트롤 1652 - 모바일 코드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6998e84a-2d29-4e10-8962-76754d4f772d) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1652.json) |
|[Microsoft 관리형 컨트롤 1653 - 모바일 코드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b1c00a7-7fd0-42b0-8c5b-c45f6fa1f71b) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1653.json) |

### <a name="voice-over-internet-protocol"></a>Voice Over Internet Protocol

**ID**: NIST SP 800-53 Rev. 4 SC-19

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1654 - Voice Over Internet Protocol](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a2ee16e-ab1f-414a-800b-d1608835862b) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1654.json) |
|[Microsoft 관리형 컨트롤 1655 - Voice Over Internet Protocol](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F121eab72-390e-4629-a7e2-6d6184f57c6b) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1655.json) |

### <a name="secure-name--address-resolution-service-authoritative-source"></a>이름/주소 확인 서비스(신뢰할 수 있는 원본) 보호

**ID**: NIST SP 800-53 Rev. 4 SC-20

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1656 - 이름/주소 확인 서비스(신뢰할 수 있는 원본) 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1cb067d5-c8b5-4113-a7ee-0a493633924b) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1656.json) |
|[Microsoft 관리형 컨트롤 1657 - 이름/주소 확인 서비스(신뢰할 수 있는 원본) 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F90f01329-a100-43c2-af31-098996135d2b) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1657.json) |

### <a name="secure-name--address-resolution-service-recursive-or-caching-resolver"></a>이름/주소 확인 서비스(재귀 또는 캐싱 확인자) 보호

**ID**: NIST SP 800-53 Rev. 4 SC-21

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1658 - 이름/주소 확인 서비스(재귀 또는 캐싱 확인자) 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063b540e-4bdc-4e7a-a569-3a42ddf22098) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1658.json) |

### <a name="architecture-and-provisioning-for-name--address-resolution-service"></a>이름/주소 확인 서비스의 아키텍처 및 프로비전

**ID**: NIST SP 800-53 Rev. 4 SC-22

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1659 - 이름/주소 확인 서비스의 아키텍처 및 프로비저닝](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35a4102f-a778-4a2e-98c2-971056288df8) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1659.json) |

### <a name="session-authenticity"></a>세션 신뢰성

**ID**: NIST SP 800-53 Rev. 4 SC-23

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1660 - 세션 신뢰성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F63096613-ce83-43e5-96f4-e588e8813554) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1660.json) |

### <a name="invalidate-session-identifiers-at-logout"></a>로그아웃 시 세션 식별자 무효화

**ID**: NIST SP 800-53 Rev. 4 SC-23 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1661 - 세션 신뢰성 \| 로그아웃 시 세션 식별자 무효화](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4c643c9a-1be7-4016-a5e7-e4bada052920) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1661.json) |

### <a name="fail-in-known-state"></a>알려진 상태로 실패

**ID**: NIST SP 800-53 Rev. 4 SC-24

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1662 - 알려진 상태로 실패](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F165cb91f-7ea8-4ab7-beaf-8636b98c9d15) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1662.json) |

### <a name="protection-of-information-at-rest"></a>미사용 정보 보호

**ID**: NIST SP 800-53 Rev. 4 SC-28

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service Environment에서 내부 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb74e86f-d351-4b8d-b034-93da7391c01f) |InternalEncryption을 true로 설정하면 App Service Environment의 프런트 엔드와 작업자 간의 페이지 파일, 작업자 디스크 및 내부 네트워크 트래픽이 암호화됩니다. 자세히 알아보려면 [https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-custom-settings#enable-internal-encryption](../../../app-service/environment/app-service-app-service-environment-custom-settings.md#enable-internal-encryption)을 참조하세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_HostingEnvironment_InternalEncryption_Audit.json) |
|[Automation 계정 변수를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |중요한 데이터를 저장할 때 Automation 계정 변수 자산의 암호화를 사용하도록 설정해야 합니다. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[Azure Data Box 작업에서 디바이스의 미사용 데이터에 대한 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |디바이스의 미사용 데이터에 대해 소프트웨어 기반 암호화의 두 번째 계층을 사용하도록 설정합니다. 디바이스는 미사용 데이터에 대해 Advanced Encryption Standard 256비트 암호화를 통해 이미 보호되고 있습니다. 이 옵션은 데이터 암호화의 두 번째 계층을 추가합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Monitor 로그 클러스터는 인프라 암호화를 사용하도록 설정한 상태에서 만들어야 함(이중 암호화)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea0dfaed-95fb-448c-934e-d6e713ce393d) |보안 데이터 암호화가 서비스 수준 및 인프라 수준에서 두 개의 다른 암호화 알고리즘과 두 개의 다른 키를 통해 사용하도록 설정하려면 Azure Monitor 전용 클러스터를 사용합니다. 이 옵션은 지역에서 지원되는 경우 기본적으로 사용하도록 설정됩니다. [https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#customer-managed-key-overview](../../../azure-monitor/logs/customer-managed-keys.md#customer-managed-key-overview)를 참조하세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalyticsClusters_CMKDoubleEncryptionEnabled_Deny.json) |
|[Azure Stack Edge 디바이스는 이중 암호화를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |디바이스에 저장된 데이터를 보호하려면 데이터가 이중 암호화되고, 데이터에 대한 액세스가 제어되고, 디바이스가 비활성화되면 데이터를 데이터 디스크에서 안전하게 삭제해야 합니다. 이중 암호화는 두 계층의 암호화를 사용하는 것입니다. 데이터 볼륨에 대한 BitLocker XTS-AES 256비트 암호화 및 하드 드라이브의 기본 제공 암호화. 특정 Stack Edge 디바이스에 대한 보안 개요 설명서에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
|[Azure Data Explorer에서 디스크 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |디스크 암호화를 사용하도록 설정하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Azure Data Explorer에서 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |이중 암호화를 사용하도록 설정하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. 이중 암호화를 사용하도록 설정하면 스토리지 계정의 데이터가 두 개의 다른 암호화 알고리즘과 두 개의 다른 키를 사용하여 두 번 암호화됩니다(서비스 수준에서 한 번, 인프라 수준에서 한 번). |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[인프라 암호화를 Azure Database for MySQL에 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a58212a-c829-4f13-9872-6371df2fd0b4) |인프라 암호화를 Azure Database for MySQL에 사용하도록 설정하여 데이터 보안을 더 높은 수준으로 보장합니다. 인프라 암호화를 사용하도록 설정하면 저장 데이터가 FIPS 140-2 준수 Microsoft 관리형 키를 사용하여 두 번 암호화됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_InfrastructureEncryption_Audit.json) |
|[인프라 암호화를 Azure Database for PostgreSQL에 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F24fba194-95d6-48c0-aea7-f65bf859c598) |인프라 암호화를 Azure Database for PostgreSQL에 사용하도록 설정하여 데이터 보안을 더 높은 수준으로 보장합니다. 인프라 암호화를 사용하도록 설정하면 저장 데이터가 FIPS 140-2 준수 Microsoft 관리형 키를 사용하여 두 번 암호화됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_InfrastructureEncryption_Audit.json) |
|[Microsoft 관리형 컨트롤 1663 - 저장 정보 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60171210-6dde-40af-a144-bf2670518bfa) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1663.json) |
|[SQL 데이터베이스의 중요한 데이터를 분류해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc9835f2-9f6b-4cc8-ab4a-f8ef615eb349) |Azure Security Center는 SQL 데이터베이스에 대해 데이터 검색 및 분류 검사 결과를 모니터링하고 더 나은 모니터링 및 보안을 위해 데이터베이스에서 중요한 데이터를 분류하기 위한 권장 사항을 제공합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbDataClassification_Audit.json) |
|[Service Fabric 클러스터는 ClusterProtectionLevel 속성을 EncryptAndSign으로 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric은 기본 클러스터 인증서를 사용하여 노드 간 통신을 위한 3단계 보호(None, Sign 및 EncryptAndSign)를 제공합니다. 모든 노드 간 메시지가 암호화되고 디지털로 서명될 수 있게 보호 수준을 설정합니다. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[스토리지 계정에는 인프라 암호화가 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4733ea7b-a883-42fe-8cac-97454c2a9e4a) |데이터의 보안 수준을 높이기 위한 인프라 암호화를 사용하도록 설정합니다. 인프라 암호화를 사용하도록 설정하면 스토리지 계정의 데이터가 두 번 암호화됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountInfrastructureEncryptionEnabled_Audit.json) |
|[Azure Kubernetes Service 클러스터의 에이전트 노드 풀에 대한 임시 디스크 및 캐시는 호스트에서 암호화되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41425d9f-d1a5-499a-9932-f8ed8453932c) |데이터 보안을 강화하려면 Azure Kubernetes Service 노드 VM의 VM(가상 머신) 호스트에 저장된 데이터는 미사용 시 암호화해야 합니다. 이는 다양한 규정 및 업계 규정 준수 표준의 공통 요구 사항입니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_EncryptionAtHost_Deny.json) |
|[SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |저장 데이터를 보호하고 규정 준수 요구 사항을 충족하려면 투명한 데이터 암호화를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
|[가상 머신 및 가상 머신 확장 집합에서는 호스트에서 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc4d8e41-e223-45ea-9bf5-eada37891d87) |호스트에서 암호화를 사용하여 가상 머신 및 가상 머신 확장 집합 데이터에 대한 엔드투엔트 암호화를 가져옵니다. 호스트에서 암호화를 사용하면 임시 디스크 및 OS/데이터 디스크 캐시에 대해 미사용 데이터를 암호화할 수 있습니다. 임시 및 사용 후 삭제 OS 디스크는 호스트에서 암호화가 활성화될 때 플랫폼 관리형 키로 암호화됩니다. OS/데이터 디스크 캐시는 디스크에서 선택한 암호화 유형에 따라 고객 관리형 키 또는 플랫폼 관리형 키를 사용하여 미사용 데이터를 암호화합니다. [https://aka.ms/vm-hbe](../../../virtual-machines/disks-enable-host-based-encryption-portal.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/HostBasedEncryptionRequired_Deny.json) |
|[가상 머신은 Compute 및 Storage 리소스 간에 임시 디스크, 캐시 및 데이터 흐름을 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |기본적으로 가상 머신의 OS 및 데이터 디스크는 플랫폼 관리형 키를 사용하여 미사용 시 암호화됩니다. 임시 디스크, 데이터 캐시 및 컴퓨팅과 스토리지 간에 이동하는 데이터는 암호화되지 않습니다. 다음과 같은 경우 이 권장 사항을 무시합니다. 1. 호스트에서 암호화를 사용하는 경우 또는 2. Managed Disks의 서버 쪽 암호화는 보안 요구 사항을 충족합니다. [Azure Disk Storage의 서버 쪽 암호화](../../../virtual-machines/disk-encryption.md) 및 [다양한 디스크 암호화 제품](../../../virtual-machines/disk-encryption-overview.md#comparison)에서 자세히 알아봅니다. |AuditIfNotExists, 사용 안 함 |[2.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="cryptographic-protection"></a>암호화 보호

**ID**: NIST SP 800-53 Rev. 4 SC-28 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service Environment에서 내부 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb74e86f-d351-4b8d-b034-93da7391c01f) |InternalEncryption을 true로 설정하면 App Service Environment의 프런트 엔드와 작업자 간의 페이지 파일, 작업자 디스크 및 내부 네트워크 트래픽이 암호화됩니다. 자세히 알아보려면 [https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-custom-settings#enable-internal-encryption](../../../app-service/environment/app-service-app-service-environment-custom-settings.md#enable-internal-encryption)을 참조하세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_HostingEnvironment_InternalEncryption_Audit.json) |
|[Automation 계정 변수를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |중요한 데이터를 저장할 때 Automation 계정 변수 자산의 암호화를 사용하도록 설정해야 합니다. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |
|[Azure Data Box 작업에서 디바이스의 미사용 데이터에 대한 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |디바이스의 미사용 데이터에 대해 소프트웨어 기반 암호화의 두 번째 계층을 사용하도록 설정합니다. 디바이스는 미사용 데이터에 대해 Advanced Encryption Standard 256비트 암호화를 통해 이미 보호되고 있습니다. 이 옵션은 데이터 암호화의 두 번째 계층을 추가합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Monitor 로그 클러스터는 인프라 암호화를 사용하도록 설정한 상태에서 만들어야 함(이중 암호화)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea0dfaed-95fb-448c-934e-d6e713ce393d) |보안 데이터 암호화가 서비스 수준 및 인프라 수준에서 두 개의 다른 암호화 알고리즘과 두 개의 다른 키를 통해 사용하도록 설정하려면 Azure Monitor 전용 클러스터를 사용합니다. 이 옵션은 지역에서 지원되는 경우 기본적으로 사용하도록 설정됩니다. [https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#customer-managed-key-overview](../../../azure-monitor/logs/customer-managed-keys.md#customer-managed-key-overview)를 참조하세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/LogAnalyticsClusters_CMKDoubleEncryptionEnabled_Deny.json) |
|[Azure Stack Edge 디바이스는 이중 암호화를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |디바이스에 저장된 데이터를 보호하려면 데이터가 이중 암호화되고, 데이터에 대한 액세스가 제어되고, 디바이스가 비활성화되면 데이터를 데이터 디스크에서 안전하게 삭제해야 합니다. 이중 암호화는 두 계층의 암호화를 사용하는 것입니다. 데이터 볼륨에 대한 BitLocker XTS-AES 256비트 암호화 및 하드 드라이브의 기본 제공 암호화. 특정 Stack Edge 디바이스에 대한 보안 개요 설명서에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
|[Azure Data Explorer에서 디스크 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |디스크 암호화를 사용하도록 설정하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Azure Data Explorer에서 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |이중 암호화를 사용하도록 설정하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. 이중 암호화를 사용하도록 설정하면 스토리지 계정의 데이터가 두 개의 다른 암호화 알고리즘과 두 개의 다른 키를 사용하여 두 번 암호화됩니다(서비스 수준에서 한 번, 인프라 수준에서 한 번). |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[인프라 암호화를 Azure Database for MySQL에 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3a58212a-c829-4f13-9872-6371df2fd0b4) |인프라 암호화를 Azure Database for MySQL에 사용하도록 설정하여 데이터 보안을 더 높은 수준으로 보장합니다. 인프라 암호화를 사용하도록 설정하면 저장 데이터가 FIPS 140-2 준수 Microsoft 관리형 키를 사용하여 두 번 암호화됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_InfrastructureEncryption_Audit.json) |
|[인프라 암호화를 Azure Database for PostgreSQL에 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F24fba194-95d6-48c0-aea7-f65bf859c598) |인프라 암호화를 Azure Database for PostgreSQL에 사용하도록 설정하여 데이터 보안을 더 높은 수준으로 보장합니다. 인프라 암호화를 사용하도록 설정하면 저장 데이터가 FIPS 140-2 준수 Microsoft 관리형 키를 사용하여 두 번 암호화됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_InfrastructureEncryption_Audit.json) |
|[Microsoft 관리형 컨트롤 1664 - 저장 정보 보호 \| 암호화 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2cdf6b8-9505-4619-b579-309ba72037ac) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1664.json) |
|[SQL 데이터베이스의 중요한 데이터를 분류해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcc9835f2-9f6b-4cc8-ab4a-f8ef615eb349) |Azure Security Center는 SQL 데이터베이스에 대해 데이터 검색 및 분류 검사 결과를 모니터링하고 더 나은 모니터링 및 보안을 위해 데이터베이스에서 중요한 데이터를 분류하기 위한 권장 사항을 제공합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbDataClassification_Audit.json) |
|[Service Fabric 클러스터는 ClusterProtectionLevel 속성을 EncryptAndSign으로 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |Service Fabric은 기본 클러스터 인증서를 사용하여 노드 간 통신을 위한 3단계 보호(None, Sign 및 EncryptAndSign)를 제공합니다. 모든 노드 간 메시지가 암호화되고 디지털로 서명될 수 있게 보호 수준을 설정합니다. |감사, 거부, 사용 안 함 |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[스토리지 계정에는 인프라 암호화가 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4733ea7b-a883-42fe-8cac-97454c2a9e4a) |데이터의 보안 수준을 높이기 위한 인프라 암호화를 사용하도록 설정합니다. 인프라 암호화를 사용하도록 설정하면 스토리지 계정의 데이터가 두 번 암호화됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountInfrastructureEncryptionEnabled_Audit.json) |
|[Azure Kubernetes Service 클러스터의 에이전트 노드 풀에 대한 임시 디스크 및 캐시는 호스트에서 암호화되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41425d9f-d1a5-499a-9932-f8ed8453932c) |데이터 보안을 강화하려면 Azure Kubernetes Service 노드 VM의 VM(가상 머신) 호스트에 저장된 데이터는 미사용 시 암호화해야 합니다. 이는 다양한 규정 및 업계 규정 준수 표준의 공통 요구 사항입니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/AKS_EncryptionAtHost_Deny.json) |
|[SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |저장 데이터를 보호하고 규정 준수 요구 사항을 충족하려면 투명한 데이터 암호화를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |
|[가상 머신 및 가상 머신 확장 집합에서는 호스트에서 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc4d8e41-e223-45ea-9bf5-eada37891d87) |호스트에서 암호화를 사용하여 가상 머신 및 가상 머신 확장 집합 데이터에 대한 엔드투엔트 암호화를 가져옵니다. 호스트에서 암호화를 사용하면 임시 디스크 및 OS/데이터 디스크 캐시에 대해 미사용 데이터를 암호화할 수 있습니다. 임시 및 사용 후 삭제 OS 디스크는 호스트에서 암호화가 활성화될 때 플랫폼 관리형 키로 암호화됩니다. OS/데이터 디스크 캐시는 디스크에서 선택한 암호화 유형에 따라 고객 관리형 키 또는 플랫폼 관리형 키를 사용하여 미사용 데이터를 암호화합니다. [https://aka.ms/vm-hbe](../../../virtual-machines/disks-enable-host-based-encryption-portal.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/HostBasedEncryptionRequired_Deny.json) |
|[가상 머신은 Compute 및 Storage 리소스 간에 임시 디스크, 캐시 및 데이터 흐름을 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |기본적으로 가상 머신의 OS 및 데이터 디스크는 플랫폼 관리형 키를 사용하여 미사용 시 암호화됩니다. 임시 디스크, 데이터 캐시 및 컴퓨팅과 스토리지 간에 이동하는 데이터는 암호화되지 않습니다. 다음과 같은 경우 이 권장 사항을 무시합니다. 1. 호스트에서 암호화를 사용하는 경우 또는 2. Managed Disks의 서버 쪽 암호화는 보안 요구 사항을 충족합니다. [Azure Disk Storage의 서버 쪽 암호화](../../../virtual-machines/disk-encryption.md) 및 [다양한 디스크 암호화 제품](../../../virtual-machines/disk-encryption-overview.md#comparison)에서 자세히 알아봅니다. |AuditIfNotExists, 사용 안 함 |[2.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="process-isolation"></a>프로세스 격리

**ID**: NIST SP 800-53 Rev. 4 SC-39

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1665 - 프로세스 격리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5df3a55c-8456-44d4-941e-175f79332512) |Microsoft에서 구현하는 시스템 및 통신 보호 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1665.json) |

## <a name="system-and-information-integrity"></a>시스템 및 정보 무결성

### <a name="system-and-information-integrity-policy-and-procedures"></a>시스템 및 정보 무결성 정책 및 절차

**ID**: NIST SP 800-53 Rev. 4 SI-1

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1666 - 시스템 및 정보 무결성 정책과 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12e30ee3-61e6-4509-8302-a871e8ebb91e) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1666.json) |
|[Microsoft 관리형 컨트롤 1667 - 시스템 및 정보 무결성 정책과 절차](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd61880dc-6e38-4f2a-a30c-3406a98f8220) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1667.json) |

### <a name="flaw-remediation"></a>결함 수정

**ID**: NIST SP 800-53 Rev. 4 SI-2

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[취약성 평가 솔루션을 가상 머신에서 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |가상 머신을 감사하여 지원되는 취약성 평가 솔루션을 실행하고 있는지 검색합니다. 모든 사이버 위험 및 보안 프로그램의 핵심 구성 요소는 취약성을 식별하고 분석하는 것입니다. Azure Security Center의 표준 가격 책정 계층에는 추가 비용 없이 가상 머신에 대한 취약성 검사가 포함됩니다. 또한 Security Center가 자동으로 도구를 배포할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[Azure Defender for App Service를 사용해야 합니다](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service는 클라우드의 규모와 Azure가 클라우드 공급자로서 가지고 있는 가시성을 활용하여 일반적인 웹앱 공격을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure SQL Database 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[컨테이너 레지스트리용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |컨테이너 레지스트리용 Azure Defender는 지난 30일 내에 끌어와서 레지스트리에 푸시한 이미지의 취약성을 검사하고, 각 이미지에 대해 발견한 자세한 내용을 공개합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS는 Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링하여 클라우드 리소스에 대한 추가 보호 계층을 제공합니다. Azure Defender는 DNS 계층에서 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)에서 Azure Defender for DNS의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault는 키 자격 증명 모음 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하여 보안 인텔리전스에 추가 보호 계층을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager는 조직의 리소스 관리 작업을 자동으로 모니터링합니다. Azure Defender는 위협을 감지하고 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)에서 Azure Defender for Resource Manager의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[머신의 SQL 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[스토리지용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[API 앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |보안 결함이 있거나 추가 기능을 포함하기 위해 HTTP에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 HTTP 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[함수 앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |보안 결함이 있거나 추가 기능을 포함하기 위해 HTTP에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 HTTP 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[웹앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |보안 결함이 있거나 추가 기능을 포함하기 위해 HTTP에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 HTTP 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |
|[API 앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |보안 결함이 있거나 추가 기능을 포함하기 위해 Java에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 API 앱에 최신 Python 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[함수 앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |보안 결함이 있거나 추가 기능을 포함하기 위해 Java 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 함수 앱에 최신 Java 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[웹앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |보안 결함이 있거나 추가 기능을 포함하기 위해 Java 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 Java 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |
|[API 앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |보안 결함이 있거나 추가 기능을 포함하기 위해 PHP 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 API Apps에 최신 PHP 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[웹앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |보안 결함이 있거나 추가 기능을 포함하기 위해 PHP 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 PHP 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |
|[API 앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |보안 결함이 있거나 추가 기능을 포함하기 위해 Python 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 API 앱에 최신 Python 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[함수 앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |보안 결함이 있거나 추가 기능을 포함하기 위해 Python 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 함수 앱에 최신 Python 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[웹앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |보안 결함이 있거나 추가 기능을 포함하기 위해 Python 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 Python 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |
|[Kubernetes Services를 취약하지 않은 Kubernetes 버전으로 업그레이드해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |현재 Kubernetes 버전의 알려진 취약성으로부터 보호하려면 Kubernetes 서비스 클러스터를 최신 Kubernetes 버전으로 업그레이드하세요. 취약성 CVE-2019-9946이 Kubernetes 버전 1.11.9+, 1.12.7+, 1.13.5+ 및 1.14.0+에서 패치되었습니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |
|[Microsoft 관리형 컨트롤 1668 - 결함 수정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8fb0966e-be1d-42c3-baca-60df5c0bcc61) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1668.json) |
|[Microsoft 관리형 컨트롤 1669 - 결함 수정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48f2f62b-5743-4415-a143-288adc0e078d) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1669.json) |
|[Microsoft 관리형 컨트롤 1670 - 결함 수정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc6108469-57ee-4666-af7e-79ba61c7ae0c) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1670.json) |
|[Microsoft 관리형 컨트롤 1671 - 결함 수정](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c5bbef7-a316-415b-9b38-29753ce8e698) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1671.json) |
|[SQL 데이터베이스가 발견한 취약성을 해결해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |취약성 평가 검사 결과 및 데이터베이스 취약성을 해결하는 방법에 대한 권장 사항을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[가상 머신 확장 집합에 대한 시스템 업데이트를 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Windows 및 Linux 가상 머신 확장 집합의 보안 유지를 위해 설치해야 하는 시스템 보안 업데이트 및 중요 업데이트가 누락되었는지 감사합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |
|[시스템 업데이트를 머신에 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |서버의 누락된 보안 시스템 업데이트는 Azure Security Center에서 권장 사항으로 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |
|[머신 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |구성된 기준을 충족하지 않는 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |가상 머신 확장 집합의 OS 취약성을 감사하여 공격으로부터 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="central-management"></a>중앙 관리

**ID**: NIST SP 800-53 Rev. 4 SI-2 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1672 - 결함 수정 \| 중앙 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb45fe972-904e-45a4-ac20-673ba027a301) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1672.json) |

### <a name="automated-flaw-remediation-status"></a>자동화된 결함 수정 상태

**ID**: NIST SP 800-53 Rev. 4 SI-2 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1673 - 결함 수정 \| 자동화된 결함 수정 상태](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdff0b90d-5a6f-491c-b2f8-b90aa402d844) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1673.json) |

### <a name="time-to-remediate-flaws--benchmarks-for-corrective-actions"></a>결함 수정 시간/정정 작업 벤치마크

**ID**: NIST SP 800-53 Rev. 4 SI-2 (3)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1674 - 결함 수정 \| 결함 수정 시간/정정 작업 벤치마크](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F93e9e233-dd0a-4bde-aea5-1371bce0e002) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1674.json) |
|[Microsoft 관리형 컨트롤 1675 - 결함 수정 \| 결함 수정 시간/정정 작업 벤치마크](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffacb66e0-1c48-478a-bed5-747a312323e1) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1675.json) |

### <a name="removal-of-previous-versions-of-software--firmware"></a>소프트웨어/펌웨어의 이전 버전 제거

**ID**: NIST SP 800-53 Rev. 4 SI-2 (6)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |보안 결함이 있거나 추가 기능을 포함하기 위해 HTTP에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 HTTP 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[함수 앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |보안 결함이 있거나 추가 기능을 포함하기 위해 HTTP에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 HTTP 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[웹앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |보안 결함이 있거나 추가 기능을 포함하기 위해 HTTP에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 HTTP 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |
|[API 앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |보안 결함이 있거나 추가 기능을 포함하기 위해 Java에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 API 앱에 최신 Python 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[함수 앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |보안 결함이 있거나 추가 기능을 포함하기 위해 Java 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 함수 앱에 최신 Java 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[웹앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |보안 결함이 있거나 추가 기능을 포함하기 위해 Java 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 Java 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |
|[API 앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |보안 결함이 있거나 추가 기능을 포함하기 위해 PHP 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 API Apps에 최신 PHP 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[웹앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |보안 결함이 있거나 추가 기능을 포함하기 위해 PHP 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 PHP 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |
|[API 앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |보안 결함이 있거나 추가 기능을 포함하기 위해 Python 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 API 앱에 최신 Python 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[함수 앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |보안 결함이 있거나 추가 기능을 포함하기 위해 Python 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 함수 앱에 최신 Python 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[웹앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |보안 결함이 있거나 추가 기능을 포함하기 위해 Python 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 Python 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |
|[Kubernetes Services를 취약하지 않은 Kubernetes 버전으로 업그레이드해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |현재 Kubernetes 버전의 알려진 취약성으로부터 보호하려면 Kubernetes 서비스 클러스터를 최신 Kubernetes 버전으로 업그레이드하세요. 취약성 CVE-2019-9946이 Kubernetes 버전 1.11.9+, 1.12.7+, 1.13.5+ 및 1.14.0+에서 패치되었습니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

### <a name="malicious-code-protection"></a>악성 코드 방지

**ID**: NIST SP 800-53 Rev. 4 SI-3

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |가상 머신 확장 집합에서 엔드포인트 보호 솔루션의 존재 및 상태를 감사하여 위협 및 취약성으로부터 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft 관리형 컨트롤 1676 - 악성 코드 차단](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc10fb58b-56a8-489e-9ce3-7ffe24e78e4b) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1676.json) |
|[Microsoft 관리형 컨트롤 1677 - 악성 코드 차단](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a248e1e-040f-43e5-bff2-afc3a57a3923) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1677.json) |
|[Microsoft 관리형 컨트롤 1678 - 악성 코드 차단](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd533cb0-b416-4be7-8e86-4d154824dfd7) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1678.json) |
|[Microsoft 관리형 컨트롤 1679 - 악성 코드 차단](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2cf42a28-193e-41c5-98df-7688e7ef0a88) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1679.json) |
|[Azure Security Center에서 누락된 Endpoint Protection 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Endpoint Protection 에이전트가 설치되어 있지 않은 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[Windows Defender Exploit Guard를 머신에서 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbed48b13-6647-468e-aa2f-1af1d3f4dd40) |Windows Defender Exploit Guard는 Azure Policy 게스트 구성 에이전트를 사용합니다. Exploit Guard에는 다양한 공격 벡터에 대해 디바이스를 잠그고 맬웨어 공격에서 일반적으로 사용되는 동작을 차단하면서 기업에서 보안 위험과 생산성 요구 사항 사이의 균형을 맞출 수 있도록 설계된 네 가지 구성 요소가 있습니다(Windows에만 해당). |AuditIfNotExists, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDefenderExploitGuard_AINE.json) |

### <a name="central-management"></a>중앙 관리

**ID**: NIST SP 800-53 Rev. 4 SI-3 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |가상 머신 확장 집합에서 엔드포인트 보호 솔루션의 존재 및 상태를 감사하여 위협 및 취약성으로부터 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Microsoft 관리형 컨트롤 1680 - 악성 코드 차단 \| 중앙 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399cd6ee-0e18-41db-9dea-cde3bd712f38) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1680.json) |
|[Azure Security Center에서 누락된 Endpoint Protection 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Endpoint Protection 에이전트가 설치되어 있지 않은 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[Windows Defender Exploit Guard를 머신에서 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbed48b13-6647-468e-aa2f-1af1d3f4dd40) |Windows Defender Exploit Guard는 Azure Policy 게스트 구성 에이전트를 사용합니다. Exploit Guard에는 다양한 공격 벡터에 대해 디바이스를 잠그고 맬웨어 공격에서 일반적으로 사용되는 동작을 차단하면서 기업에서 보안 위험과 생산성 요구 사항 사이의 균형을 맞출 수 있도록 설계된 네 가지 구성 요소가 있습니다(Windows에만 해당). |AuditIfNotExists, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDefenderExploitGuard_AINE.json) |

### <a name="automatic-updates"></a>자동 업데이트

**ID**: NIST SP 800-53 Rev. 4 SI-3 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1681 - 악성 코드 차단 \| 자동 업데이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12623e7e-4736-4b2e-b776-c1600f35f93a) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1681.json) |

### <a name="nonsignature-based-detection"></a>비서명 기반 검색

**ID**: NIST SP 800-53 Rev. 4 SI-3 (7)

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1682 - 악성 코드 차단 \| 비서명 기반 감지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F62b638c5-29d7-404b-8d93-f21e4b1ce198) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1682.json) |

### <a name="information-system-monitoring"></a>정보 시스템 모니터링

**ID**: NIST SP 800-53 Rev. 4 SI-4

|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[모든 인터넷 트래픽은 배포된 Azure Firewall을 통해 라우팅되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure Security Center에서 일부 서브넷이 차세대 방화벽으로 보호되지 않는 것으로 확인되었습니다. Azure Firewall 또는 지원되는 차세대 방화벽으로 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[구독에 Log Analytics 에이전트의 자동 프로비저닝을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |보안 취약성 및 위협을 모니터링하기 위해 Azure Security Center는 Azure 가상 머신에서 데이터를 수집합니다. 데이터는 이전에 MMA(Microsoft Monitoring Agent)로 알려진 Log Analytics 에이전트에 의해 수집되며, 머신에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석을 위해 Log Analytics 작업 영역에 데이터를 복사합니다. 지원되는 모든 Azure VM과 생성된 모든 새 VM에 에이전트를 자동으로 배포하려면 자동 프로비저닝을 사용하도록 설정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
|[Azure Arc 사용 Kubernetes 클러스터에 Azure Defender의 확장이 설치되어 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8dfab9c4-fe7b-49ad-85e4-1e9be085358f) |Azure Arc용 Azure Defender 확장은 Arc 지원 Kubernetes 클러스터에 대한 위협 보호를 제공합니다. 이 확장은 클러스터의 노드에서 데이터를 수집하고 추가 분석을 위해 클라우드의 Azure Defender for Kubernetes 백 엔드로 보냅니다. [https://docs.microsoft.com/azure/security-center/defender-for-kubernetes-azure-arc](../../../security-center/defender-for-kubernetes-azure-arc.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Kubernetes/ASC_Azure_Defender_Kubernetes_Arc_Extension_Audit.json) |
|[Azure Defender for App Service를 사용해야 합니다](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service는 클라우드의 규모와 Azure가 클라우드 공급자로서 가지고 있는 가시성을 활용하여 일반적인 웹앱 공격을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |
|[Azure SQL Database 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |
|[컨테이너 레지스트리용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |컨테이너 레지스트리용 Azure Defender는 지난 30일 내에 끌어와서 레지스트리에 푸시한 이미지의 취약성을 검사하고, 각 이미지에 대해 발견한 자세한 내용을 공개합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |
|[Azure Defender for DNS를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbdc59948-5574-49b3-bb91-76b7c986428d) |Azure Defender for DNS는 Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링하여 클라우드 리소스에 대한 추가 보호 계층을 제공합니다. Azure Defender는 DNS 계층에서 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-dns](../../../security-center/defender-for-dns-introduction.md)에서 Azure Defender for DNS의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnDns_Audit.json) |
|[Azure Defender for Key Vault를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault는 키 자격 증명 모음 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하여 보안 인텔리전스에 추가 보호 계층을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |
|[Azure Defender for Kubernetes를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |
|[Azure Defender for Resource Manager를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3d20c29-b36d-48fe-808b-99a87530ad99) |Azure Defender for Resource Manager는 조직의 리소스 관리 작업을 자동으로 모니터링합니다. Azure Defender는 위협을 감지하고 의심스러운 활동에 대해 경고합니다. [https://aka.ms/defender-for-resource-manager](../../../security-center/defender-for-resource-manager-introduction.md)에서 Azure Defender for Resource Manager의 기능에 대해 자세히 알아보세요. 이 Azure Defender 계획을 사용하도록 설정하면 요금이 청구됩니다. Security Center의 가격 책정 페이지: [https://aka.ms/pricing-security-center](https://aka.ms/pricing-security-center)에서 지역별 가격 정보에 대해 알아봅니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAzureDefenderOnResourceManager_Audit.json) |
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[머신의 SQL 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |
|[보호되지 않는 Azure SQL 서버에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[스토리지용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |
|[머신에 게스트 구성 확장을 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae89ebca-1c92-4898-ac2c-9f63decb045c) |머신의 게스트 내 설정을 안전하게 구성하려면 게스트 구성 확장을 설치합니다. 확장에서 모니터링하는 게스트 내 설정에는 운영 체제 구성, 애플리케이션 구성 또는 현재 상태 및 환경 설정이 포함됩니다. 설치되면 'Windows Exploit Guard를 사용하도록 설정해야 합니다'와 같은 게스트 내 정책을 사용할 수 있습니다. [https://aka.ms/gcpol](../concepts/guest-configuration.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVm.json) |
|[머신에서 Log Analytics 에이전트 상태 문제를 해결해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd62cfe2b-3ab0-4d41-980d-76803b58ca65) |Security Center는 Log Analytics 에이전트(이전의 MMA(Microsoft Monitoring Agent))를 사용합니다. 가상 머신이 모니터링되는 것을 확인하려면 에이전트가 가상 머신에 설치되어 있고 구성된 작업 영역에 대한 보안 이벤트를 제대로 수집하는지 확인해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ResolveLaHealthIssues.json) |
|[Linux Azure Arc 머신에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F842c54e8-c2f9-4d79-ae8d-38d8b8019373) |이 정책은 Log Analytics 에이전트가 설치되지 않은 경우 Linux Azure Arc 머신을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Linux_LogAnalytics_Audit.json) |
|[Azure Security Center를 모니터링하려면 가상 머신에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4fe33eb-e377-4efb-ab31-0784311bc499) |이 정책은 Security Center가 보안 취약성 및 위협을 모니터링하는 데 사용할 Log Analytics 에이전트가 설치되어 있지 않은 경우 Windows/Linux VMs(Virtual Machines)를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVm.json) |
|[Azure Security Center를 모니터링하려면 가상 머신 확장 집합에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa3a6ea0c-e018-4933-9ef0-5aaa1501449b) |Security Center는 Azure VMs(Virtual Machines)에서 데이터를 수집하여 보안 취약성 및 위협을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_InstallLaAgentOnVmss.json) |
|[Windows Azure Arc 머신에 Log Analytics 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd69b1763-b96d-40b8-a2d9-ca31e9fd0d3e) |이 정책은 Log Analytics 에이전트가 설치되지 않은 경우 Windows Azure Arc 머신을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Arc_Windows_LogAnalytics_Audit.json) |
|[Microsoft 관리형 컨트롤 1683 - 정보 시스템 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c79fee4-88dd-44ce-bbd4-4de88948c4f8) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1683.json) |
|[Microsoft 관리형 컨트롤 1684 - 정보 시스템 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16bfdb59-db38-47a5-88a9-2e9371a638cf) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1684.json) |
|[Microsoft 관리형 컨트롤 1685 - 정보 시스템 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F36b0ef30-366f-4b1b-8652-a3511df11f53) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1685.json) |
|[Microsoft 관리형 컨트롤 1686 - 정보 시스템 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe17085c5-0be8-4423-b39b-a52d3d1402e5) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1686.json) |
|[Microsoft 관리형 컨트롤 1687 - 정보 시스템 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a87fc7f-301e-49f3-ba2a-4d74f424fa97) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1687.json) |
|[Microsoft 관리형 컨트롤 1688 - 정보 시스템 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F063c3f09-e0f0-4587-8fd5-f4276fae675f) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1688.json) |
|[Microsoft 관리형 컨트롤 1689 - 정보 시스템 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fde901f2f-a01a-4456-97f0-33cda7966172) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1689.json) |
|[Linux 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |
|[Windows 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[Network Watcher를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. 시나리오 수준 모니터링을 사용하면 종단 간 네트워크 수준 보기에서 문제를 진단할 수 있습니다. 가상 네트워크가 있는 모든 지역에서 Network Watcher 리소스 그룹을 만들어야 합니다. 특정 지역에서 Network Watcher 리소스 그룹을 사용할 수 없는 경우 경고가 활성화됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |
|[가상 머신의 게스트 구성 확장은 시스템이 할당한 관리 ID를 사용하여 배포해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd26f7642-7545-4e18-9b75-8c9bbdee3a9a) |게스트 구성 확장에는 시스템이 할당한 관리 ID가 필요합니다. 게스트 구성 확장이 설치되어 있지만 시스템이 할당한 관리 ID가 없는 경우 이 정책 범위에 속한 Azure 가상 머신은 비준수입니다. [https://aka.ms/gcpol](../concepts/guest-configuration.md)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_GCExtOnVmWithNoSAMI.json) |

### <a name="system-wide-intrusion-detection-system"></a>시스템 차원 침입 탐지 시스템

**ID**: NIST SP 800-53 Rev. 4 SI-4 (1)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1690 - 정보 시스템 모니터링 \| 시스템 차원의 침입 감지 시스템](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2567a23-d1c3-4783-99f3-d471302a4d6b) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1690.json) |

### <a name="automated-tools-for-real-time-analysis"></a>자동화된 실시간 분석 도구

**ID**: NIST SP 800-53 Rev. 4 SI-4 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1691 - 정보 시스템 모니터링 \| 실시간 분석용 자동화된 도구](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F71475fb4-49bd-450b-a1a5-f63894c24725) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1691.json) |

### <a name="inbound-and-outbound-communications-traffic"></a>인바운드 및 아웃바운드 통신 트래픽

**ID**: NIST SP 800-53 Rev. 4 SI-4 (4)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1692 - 정보 시스템 모니터링 \| 인바운드 및 아웃바운드 통신 트래픽](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ecda928-9df4-4dd7-8f44-641a91e470e8) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1692.json) |

### <a name="system-generated-alerts"></a>시스템 생성 경고

**ID**: NIST SP 800-53 Rev. 4 SI-4 (5)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1693 - 정보 시스템 모니터링 \| 시스템에서 생성한 경고](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa450eba6-2efc-4a00-846a-5804a93c6b77) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1693.json) |

### <a name="analyze-communications-traffic-anomalies"></a>통신 트래픽 변칙 분석

**ID**: NIST SP 800-53 Rev. 4 SI-4 (11)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1694 - 정보 시스템 모니터링 \| 통신 트래픽 이상 분석](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F426c4ac9-ff17-49d0-acd7-a13c157081c0) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1694.json) |

### <a name="automated-alerts"></a>자동화된 경고

**ID**: NIST SP 800-53 Rev. 4 SI-4 (12)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[심각도가 높은 경고에 대해 이메일 알림을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |구독 중 하나에 잠재적인 보안 위반이 있을 때 조직의 관련 인원에게 알리려면 Security Center에서 심각도가 높은 경고에 대한 이메일 알림을 사용하도록 설정합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |
|[심각도가 높은 경고에 대해 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |구독에 잠재적인 보안 위반이 있을 때 구독 소유자에게 알리려면 Security Center에서 심각도가 높은 경고에 대해 구독 소유자에게 이메일로 알리도록 설정합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |
|[구독에 보안 문제에 대한 연락처 이메일 주소가 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |구독 중 하나에 잠재적인 보안 위반이 있을 때 조직의 관련 인원에게 알리려면 Security Center에서 이메일 알림을 받을 수 있도록 보안 연락처를 설정합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="wireless-intrusion-detection"></a>무선 침입 탐지

**ID**: NIST SP 800-53 Rev. 4 SI-4 (14)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1695 - 정보 시스템 모니터링 \| 무선 침입 감지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F13fcf812-ec82-4eda-9b89-498de9efd620) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1695.json) |

### <a name="correlate-monitoring-information"></a>모니터링 정보 상호 연결

**ID**: NIST SP 800-53 Rev. 4 SI-4 (16)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1696 - 정보 시스템 모니터링 \| 모니터링 정보 상호 연결](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69d2a238-20ab-4206-a6dc-f302bf88b1b8) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1696.json) |

### <a name="analyze-traffic--covert-exfiltration"></a>트래픽 분석/비밀 반출

**ID**: NIST SP 800-53 Rev. 4 SI-4 (18)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1697 - 정보 시스템 모니터링 \| 트래픽 분석/비밀 반출](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9873db2-18ad-46b3-a11a-1a1f8cbf0335) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1697.json) |

### <a name="individuals-posing-greater-risk"></a>위험도가 높은 개인

**ID**: NIST SP 800-53 Rev. 4 SI-4 (19)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1698 - 정보 시스템 모니터링 \| 위험도가 높은 개인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F31b752c1-05a9-432a-8fce-c39b56550119) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1698.json) |

### <a name="privileged-users"></a>권한 있는 사용자

**ID**: NIST SP 800-53 Rev. 4 SI-4 (20)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1699 - 정보 시스템 모니터링 \| 권한 있는 사용자](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69c7bee8-bc19-4129-a51e-65a7b39d3e7c) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1699.json) |

### <a name="unauthorized-network-services"></a>권한 없는 네트워크 서비스

**ID**: NIST SP 800-53 Rev. 4 SI-4 (22)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1700 - 정보 시스템 모니터링 \| 권한 없는 네트워크 서비스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7831b4ba-c3f4-4cb1-8c11-ef8d59438cd5) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1700.json) |

### <a name="host-based-devices"></a>호스트 기반 디바이스

**ID**: NIST SP 800-53 Rev. 4 SI-4 (23)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1701 - 정보 시스템 모니터링 \| 호스트 기반 디바이스](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff25bc08f-27cb-43b6-9a23-014d00700426) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1701.json) |

### <a name="indicators-of-compromise"></a>손상 지표

**ID**: NIST SP 800-53 Rev. 4 SI-4 (24)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1702 - 정보 시스템 모니터링 \| 손상 지표](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4dfc0855-92c4-4641-b155-a55ddd962362) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1702.json) |

### <a name="security-alerts-advisories-and-directives"></a>보안 경고, 권고 및 지시

**ID**: NIST SP 800-53 Rev. 4 SI-5

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1703 - 보안 경고, 권고 및 지시](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F804faf7d-b687-40f7-9f74-79e28adf4205) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1703.json) |
|[Microsoft 관리형 컨트롤 1704 - 보안 경고, 권고 및 지시](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d44b6fa-1134-4ea6-ad4e-9edb68f65429) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1704.json) |
|[Microsoft 관리형 컨트롤 1705 - 보안 경고, 권고 및 지시](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff82e3639-fa2b-4e06-a786-932d8379b972) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1705.json) |
|[Microsoft 관리형 컨트롤 1706 - 보안 경고, 권고 및 지시](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff475ee0e-f560-4c9b-876b-04a77460a404) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1706.json) |

### <a name="automated-alerts-and-advisories"></a>자동화된 경고 및 권고

**ID**: NIST SP 800-53 Rev. 4 SI-5 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1707 - 보안 경고, 권고 및 지시 \| 자동화된 경고 및 권고](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffd4a2ac8-868a-4702-a345-6c896c3361ce) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1707.json) |

### <a name="security-function-verification"></a>보안 기능 확인

**ID**: NIST SP 800-53 Rev. 4 SI-6

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1708 - 보안 기능 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7a1e2c88-13de-4959-8ee7-47e3d74f1f48) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1708.json) |
|[Microsoft 관리형 컨트롤 1709 - 보안 기능 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F025992d6-7fee-4137-9bbf-2ffc39c0686c) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1709.json) |
|[Microsoft 관리형 컨트롤 1710 - 보안 기능 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf2a93c8-e6dd-4c94-acdd-4a2eedfc478e) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1710.json) |
|[Microsoft 관리형 컨트롤 1711 - 보안 기능 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb083a535-a66a-41ec-ba7f-f9498bf67cde) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1711.json) |

### <a name="software-firmware-and-information-integrity"></a>소프트웨어, 펌웨어 및 정보 무결성

**ID**: NIST SP 800-53 Rev. 4 SI-7

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1712 - 소프트웨어, 펌웨어 및 정보 무결성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44e543aa-41db-42aa-98eb-8a5eb1db53f0) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1712.json) |

### <a name="integrity-checks"></a>무결성 검사

**ID**: NIST SP 800-53 Rev. 4 SI-7 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1713 - 소프트웨어, 펌웨어 및 정보 무결성 \| 무결성 검사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d87c70b-5012-48e9-994b-e70dd4b8def0) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1713.json) |

### <a name="automated-notifications-of-integrity-violations"></a>자동화된 무결성 위반 알림

**ID**: NIST SP 800-53 Rev. 4 SI-7 (2)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1714 - 소프트웨어, 펌웨어 및 정보 무결성 \| 자동화된 무결성 위반 알림](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe12494fa-b81e-4080-af71-7dbacc2da0ec) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1714.json) |

### <a name="automated-response-to-integrity-violations"></a>무결성 위반에 대한 자동화된 응답

**ID**: NIST SP 800-53 Rev. 4 SI-7 (5)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1715 - 소프트웨어, 펌웨어 및 정보 무결성 \| 무결성 위반에 자동으로 대응](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdd469ae0-71a8-4adc-aafc-de6949ca3339) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1715.json) |

### <a name="integration-of-detection-and-response"></a>탐지 및 응답의 통합

**ID**: NIST SP 800-53 Rev. 4 SI-7 (7)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1716 - 소프트웨어, 펌웨어 및 정보 무결성 \| 감지와 대응의 통합](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe54c325e-42a0-4dcf-b105-046e0f6f590f) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1716.json) |

### <a name="binary-or-machine-executable-code"></a>이진 또는 컴퓨터 실행 코드

**ID**: NIST SP 800-53 Rev. 4 SI-7 (14)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1717 - 소프트웨어, 펌웨어 및 정보 무결성 \| 이진 또는 머신 실행 코드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967773fc-d9ab-4a4e-8ff6-f5e9e3f5dbef) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1717.json) |
|[Microsoft 관리형 컨트롤 1718 - 소프트웨어, 펌웨어 및 정보 무결성 \| 이진 또는 머신 실행 코드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0dced7ab-9ce5-4137-93aa-14c13e06ab17) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1718.json) |

### <a name="spam-protection"></a>스팸 방지

**ID**: NIST SP 800-53 Rev. 4 SI-8

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1719 - 스팸 방지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc13da9b4-fe14-4fe2-853a-5997c9d4215a) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1719.json) |
|[Microsoft 관리형 컨트롤 1720 - 스팸 방지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F44b9a7cd-f36a-491a-a48b-6d04ae7c4221) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1720.json) |

### <a name="central-management"></a>중앙 관리

**ID**: NIST SP 800-53 Rev. 4 SI-8 (1)

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1721 - 스팸 방지 \| 중앙 관리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd207aaef-7c4d-4f8c-9dce-4d62dfa3d29a) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1721.json) |

### <a name="automatic-updates"></a>자동 업데이트

**ID**: NIST SP 800-53 Rev. 4 SI-8 (2)

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1722 - 스팸 방지 \| 자동 업데이트](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1da06bd-25b6-4127-a301-c313d6873fff) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1722.json) |

### <a name="information-input-validation"></a>정보 입력 유효성 검사

**ID**: NIST SP 800-53 Rev. 4 SI-10

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1723 - 정보 입력 유효성 검사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe91927a0-ac1d-44a0-95f8-5185f9dfce9f) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1723.json) |

### <a name="error-handling"></a>오류 처리

**ID**: NIST SP 800-53 Rev. 4 SI-11

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1724 - 오류 처리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd07594d1-0307-4c08-94db-5d71ff31f0f6) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1724.json) |
|[Microsoft 관리형 컨트롤 1725 - 오류 처리](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fafc234b5-456b-4aa5-b3e2-ce89108124cc) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1725.json) |

### <a name="information-handling-and-retention"></a>정보 처리 및 보존

**ID**: NIST SP 800-53 Rev. 4 SI-12

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Microsoft 관리형 컨트롤 1726 - 정보 처리 및 보존](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbaff1279-05e0-4463-9a70-8ba5de4c7aa4) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1726.json) |

### <a name="memory-protection"></a>메모리 보호

**ID**: NIST SP 800-53 Rev. 4 SI-16

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |
|[Microsoft 관리형 컨트롤 1727 - 메모리 보호](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F697175a7-9715-4e89-b98b-c6f605888fa3) |Microsoft에서 구현하는 시스템 및 정보 무결성 컨트롤 |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Regulatory%20Compliance/MicrosoftManagedControl1727.json) |
|[Windows Defender Exploit Guard를 머신에서 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbed48b13-6647-468e-aa2f-1af1d3f4dd40) |Windows Defender Exploit Guard는 Azure Policy 게스트 구성 에이전트를 사용합니다. Exploit Guard에는 다양한 공격 벡터에 대해 디바이스를 잠그고 맬웨어 공격에서 일반적으로 사용되는 동작을 차단하면서 기업에서 보안 위험과 생산성 요구 사항 사이의 균형을 맞출 수 있도록 설계된 네 가지 구성 요소가 있습니다(Windows에만 해당). |AuditIfNotExists, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsDefenderExploitGuard_AINE.json) |

## <a name="next-steps"></a>다음 단계

Azure Policy에 대한 추가 문서:

- [규정 준수](../concepts/regulatory-compliance.md) 개요
- [이니셔티브 정의 구조](../concepts/initiative-definition-structure.md)를 참조합니다.
- [Azure Policy 샘플](./index.md)의 다른 예제를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.