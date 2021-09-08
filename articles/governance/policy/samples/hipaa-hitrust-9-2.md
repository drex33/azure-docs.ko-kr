---
title: HIPAA HITRUST 9.2 규정 준수에 대한 세부 정보
description: HIPAA HITRUST 9.2 규정 준수 기본 제공 이니셔티브에 대한 세부 정보입니다. 각 컨트롤은 평가를 지원하는 하나 이상의 Azure Policy 정의에 매핑됩니다.
ms.date: 09/03/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 4e251bf4578f7a7efaa1b18dea375a4cf50b45d2
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123470835"
---
# <a name="details-of-the-hipaa-hitrust-92-regulatory-compliance-built-in-initiative"></a>HIPAA HITRUST 9.2 규정 준수 기본 제공 이니셔티브에 대한 세부 정보

다음 문서에서는 Azure Policy 규정 준수 기본 제공 이니셔티브 정의가 HIPAA HITRUST 9.2의 **규정 준수 도메인** 및 **제어** 에 매핑되는 방법에 대해 자세히 설명합니다.
이 규정 준수 표준에 대한 자세한 내용은 [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)를 참조하세요. _소유권_ 을 이해하려면 [Azure Policy 정책 정의](../concepts/definition-structure.md#type) 및 [클라우드의 공동 책임](../../../security/fundamentals/shared-responsibility.md)을 참조하세요.

다음은 **HIPAA HITRUST 9.2** 제어에 대한 매핑입니다. 특정 **규정 준수 도메인** 으로 바로 이동하려면 오른쪽의 탐색 기능을 사용하세요. 여러 컨트롤이 [Azure Policy](../overview.md) 이니셔티브 정의를 사용하여 구현됩니다. 전체 이니셔티브 정의를 검토하려면 Azure Portal에서 **정책** 을 열고 **정의** 페이지를 선택합니다.
그런 다음, **HITRUST/HIPAA** 규정 준수 기본 제공 이니셔티브 정의를 찾아서 선택합니다.

기본 제공 이니셔티브는 [HIPAA HITRUST 9.2 청사진 샘플](../../blueprints/samples/hipaa-hitrust-9-2.md)의 일부로 배포됩니다.

> [!IMPORTANT]
> 아래의 각 컨트롤은 하나 이상의 [Azure Policy](../overview.md) 정의와 연결되어 있습니다.
> 이러한 정책은 컨트롤을 사용한 [규정 준수 평가](../how-to/get-compliance-data.md)에 도움이 될 수 있지만, 컨트롤과 하나 이상의 정책 간에 일대일 또는 완벽한 일치 관계가 없는 경우도 많습니다. 따라서 Azure Policy의 **규정 준수** 는 정책 정의 자체만 가리킬 뿐, 컨트롤의 모든 요구 사항을 완벽하게 준수한다는 것은 아닙니다. 또한 규정 준수 표준에는 현재 Azure Policy 정의에서 처리되지 않은 컨트롤이 포함되어 있습니다. 따라서 Azure Policy의 규정 준수는 전반적인 규정 준수 상태를 부분적으로 표시할 뿐입니다. 이 규정 준수 표준에 대한 규정 준수 도메인, 컨트롤, Azure Policy 정의 간의 연결은 시간이 지나면 변경될 수 있습니다. 변경 기록을 보려면 [GitHub 커밋 기록](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/HIPAA_HITRUST_audit.json)을 참조하세요.

## <a name="privilege-management"></a>권한 관리

### <a name="access-to-management-functions-or-administrative-consoles-for-systems-hosting-virtualized-systems-are-restricted-to-personnel-based-upon-the-principle-of-least-privilege-and-supported-through-technical-controls"></a>가상화된 시스템을 호스팅하는 시스템의 관리 기능 또는 관리 콘솔에 대한 액세스는 최소 권한 원칙에 따라 담당자로 제한되고 기술 제어를 통해 지원됩니다.

**ID**: 11180.01c3System.6 - 01.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |가능한 네트워크 JIT(Just In Time) 액세스는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="privileges-are-formally-authorized-and-controlled-allocated-to-users-on-a-need-to-use-and-event-by-event-basis-for-their-functional-role-eg-user-or-administrator-and-documented-for-each-system-productelement"></a>권한은 공식적으로 부여되고, 제어되며, 사용자의 기능적 역할(예: 사용자 또는 관리자)에 대한 사용 필요성 및 이벤트별 기준으로 사용자에게 할당되고, 각 시스템 제품/요소에 대해 문서화됩니다.

**ID**: 1143.01c1System.123 - 01.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신에서 관리 포트를 닫아야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |열려 있는 원격 관리 포트는 위험도가 높은 인터넷 기반 공격에 VM을 노출시킵니다. 이러한 공격은 자격 증명을 무차별적으로 대입하여 머신에 대한 관리자 액세스 권한을 획득하려고 시도합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-explicitly-authorizes-access-to-specific-security-relevant-functions-deployed-in-hardware-software-and-firmware-and-security-relevant-information"></a>조직은 특정 보안 관련 기능(하드웨어, 소프트웨어 및 펌웨어에 배포됨) 및 보안 관련 정보에 대한 액세스 권한을 명시적으로 부여합니다.

**ID**: 1144.01c1System.4 - 01.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 최대 3명의 소유자를 지정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |보안이 침해된 소유자의 위반 가능성을 줄이려면 최대 3명의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="role-based-access-control-is-implemented-and-capable-of-mapping-each-user-to-one-or-more-roles-and-each-role-to-one-or-more-system-functions"></a>역할 기반 액세스 제어를 구현하고, 이를 통해 각 사용자를 하나 이상의 역할에 매핑하고 각 역할을 하나 이상의 시스템 기능에 매핑할 수 있습니다.

**ID**: 1145.01c2System.1 - 01.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 둘 이상의 소유자를 할당해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |관리자 액세스 중복성을 유지하려면 둘 이상의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="the-organization-promotes-the-development-and-use-of-programs-that-avoid-the-need-to-run-with-elevated-privileges-and-system-routines-to-avoid-the-need-to-grant-privileges-to-users"></a>조직은 사용자에게 권한을 부여하지 않도록 하기 위해 상승된 권한 및 시스템 루틴을 사용하여 실행할 필요가 없는 프로그램을 개발하고 사용하도록 촉진합니다.

**ID**: 1146.01c2System.23 - 01.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |모니터링되지 않는 액세스를 방지하려면 소유자 권한이 있는 외부 계정을 구독에서 제거해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |

### <a name="elevated-privileges-are-assigned-to-a-different-user-id-from-those-used-for-normal-business-use-all-users-access-privileged-services-in-a-single-role-and-such-privileged-access-is-minimized"></a>상승된 권한은 일반 비즈니스 사용에 사용되는 권한과 다른 사용자 ID에 할당되며, 모든 사용자는 단일 역할의 권한 있는 서비스에 액세스하며, 이러한 권한 있는 액세스는 최소화됩니다.

**ID**: 1147.01c2System.456 - 01.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.  사용되지 않는 계정은 로그인이 차단된 계정입니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |

### <a name="the-organization-restricts-access-to-privileged-functions-and-all-security-relevant-information"></a>조직은 권한 있는 기능 및 모든 보안 관련 정보에 대한 액세스를 제한합니다.

**ID**: 1148.01c2System.78 - 01.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[사용자 지정 RBAC 규칙 사용 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |오류가 발생하기 쉬운 사용자 지정 RBAC 역할 대신 '소유자, 기여자, 읽기 권한자' 같은 기본 제공 역할을 감사합니다. 사용자 지정 역할 사용은 예외로 처리되며 엄격한 검토 및 위협 모델링이 필요합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Windows 컴퓨터는 '보안 옵션 - 계정' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee984370-154a-4ee8-9726-19d900e56fc0) |Windows 컴퓨터에는 빈 암호 및 게스트 계정 상태의 로컬 계정 사용을 제한하는 그룹 정책 설정이 '보안 옵션 - 계정' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAccounts_AINE.json) |

### <a name="the-organization-facilitates-information-sharing-by-enabling-authorized-users-to-determine-a-business-partners-access-when-discretion-is-allowed-as-defined-by-the-organization-and-by-employing-manual-processes-or-automated-mechanisms-to-assist-users-in-making-information-sharingcollaboration-decisions"></a>조직은 조직에서 정의한 대로 결정이 허용되는 경우 권한 있는 사용자가 비즈니스 파트너의 액세스를 결정할 수 있도록 하고 사용자가 정보 공유/협업을 결정하는 데 도움이 되는 수동 프로세스 또는 자동화된 메커니즘을 사용하여 정보 공유를 용이하게 합니다.

**ID**: 1149.01c2System.9 - 01.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kubernetes Services에서 RBAC(역할 기반 액세스 제어)를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |사용자가 수행할 수 있는 작업에 대한 세부적인 필터링을 제공하려면 RBAC(역할 기반 액세스 제어)를 사용하여 Kubernetes Service 클러스터에서 권한을 관리하고 관련 권한 부여 정책을 구성합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="the-access-control-system-for-the-system-components-storing-processing-or-transmitting-covered-information-is-set-with-a-default-quotdeny-allquot-setting"></a>적용 정보를 저장, 처리 또는 전송하는 시스템 구성 요소에 대한 액세스 제어 시스템은 &quot;모두 거부&quot;(기본값) 설정으로 설정됩니다.

**ID**: 1150.01c2System.10 - 01.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신에서 관리 포트를 닫아야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |열려 있는 원격 관리 포트는 위험도가 높은 인터넷 기반 공격에 VM을 노출시킵니다. 이러한 공격은 자격 증명을 무차별적으로 대입하여 머신에 대한 관리자 액세스 권한을 획득하려고 시도합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-limits-authorization-to-privileged-accounts-on-information-systems-to-a-pre-defined-subset-of-users"></a>조직은 정보 시스템의 권한 있는 계정에 대한 권한 부여를 미리 정의된 사용자의 하위 집합으로 제한합니다.

**ID**: 1151.01c3System.1 - 01.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 최대 3명의 소유자를 지정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |보안이 침해된 소유자의 위반 가능성을 줄이려면 최대 3명의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-audits-the-execution-of-privileged-functions-on-information-systems-and-ensures-information-systems-prevent-non-privileged-users-from-executing-privileged-functions"></a>조직은 정보 시스템의 권한 있는 기능에 대한 실행을 감사하고, 정보 시스템에서 권한 없는 사용자가 권한 있는 기능을 실행하지 못하도록 방지합니다.

**ID**: 1152.01c3System.2 - 01.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 둘 이상의 소유자를 할당해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |관리자 액세스 중복성을 유지하려면 둘 이상의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="all-file-system-access-not-explicitly-required-is-disabled-and-only-authorized-users-are-permitted-access-to-only-that-which-is-expressly-required-for-the-performance-of-the-users-job-duties"></a>명시적으로 필요하지 않은 모든 파일 시스템 액세스는 사용하지 않도록 설정되며, 권한 있는 사용자만 사용자의 업무 수행에 명시적으로 필요한 액세스 권한만 부여받을 수 있습니다.

**ID**: 1153.01c3System.35 - 01.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kubernetes Services에서 RBAC(역할 기반 액세스 제어)를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |사용자가 수행할 수 있는 작업에 대한 세부적인 필터링을 제공하려면 RBAC(역할 기반 액세스 제어)를 사용하여 Kubernetes Service 클러스터에서 권한을 관리하고 관련 권한 부여 정책을 구성합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="contractors-are-provided-with-minimal-system-and-physical-access-only-after-the-organization-assesses-the-contractors-ability-to-comply-with-its-security-requirements-and-the-contractor-agrees-to-comply"></a>조직에서 해당 보안 요구 사항을 준수하는 계약자의 능력을 평가하고, 계약자가 이를 준수하는 데 동의한 후에만 계약자에게 최소한의 시스템 및 물리적 액세스 권한을 제공합니다.

**ID**: 1154.01c3System.4 - 01.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 최대 3명의 소유자를 지정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |보안이 침해된 소유자의 위반 가능성을 줄이려면 최대 3명의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

## <a name="user-authentication-for-external-connections"></a>외부 연결에 대한 사용자 인증

### <a name="strong-authentication-methods-such-as-multi-factor-radius-or-kerberos-for-privileged-access-and-chap-for-encryption-of-credentials-for-dialup-methods-are-implemented-for-all-external-connections-to-the-organizations-network"></a>다단계, Radius 또는 Kerberos(권한 있는 액세스용) 및 CHAP(전화 접속 방법에 대한 자격 증명 암호화용)와 같은 강력한 인증 방법은 조직 네트워크에 대한 모든 외부 연결에 대해 구현됩니다.

**ID**: 1116.01j1Organizational.145 - 01.j **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |계정 또는 리소스 위반을 방지하려면 소유자 권한이 있는 모든 구독 계정에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="remote-access-by-vendors-and-business-partners-eg-for-remote-maintenance-is-disableddeactivated-when-not-in-use"></a>공급업체 및 비즈니스 파트너의 원격 액세스(예: 원격 유지 관리용)는 사용하지 않을 때 사용하지 않도록 설정되거나 비활성화됩니다.

**ID**: 1117.01j1Organizational.23 - 01.j **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |계정 또는 리소스 위반을 방지하려면 쓰기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="organizations-implement-encryption-eg-nbspvpn-solutions-or-private-lines-and-logs-remote-access-to-the-organizations-network-by-employees-contractors-or-third-party-eg-vendors"></a>조직은 암호화(예: &nbsp;VPN 솔루션 또는 프라이빗 회선)를 구현하고 직원, 계약자 또는 타사(예: 공급 업체)에 의한 조직의 네트워크에 대한 원격 액세스를 기록합니다.

**ID**: 1118.01j2Organizational.124 - 01.j **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |계정 또는 리소스 위반을 방지하려면 읽기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="network-equipment-is-checked-for-unanticipated-dial-up-capabilities"></a>네트워크 장비에 예기치 않은 전화 접속 기능이 있는지 확인합니다.

**ID**: 1119.01j2Organizational.3 - 01.j **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |가능한 네트워크 JIT(Just In Time) 액세스는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="remote-administration-sessions-are-authorized-encrypted-and-employ-increased-security-measures"></a>원격 관리 세션은 권한이 부여되고, 암호화되며, 강화된 보안 조치를 사용합니다.

**ID**: 1121.01j3Organizational.2 - 01.j **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |계정 또는 리소스 위반을 방지하려면 소유자 권한이 있는 모든 구독 계정에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="if-encryption-is-not-used-for-dial-up-connections-the-cio-or-hisher-designated-representative-provides-specific-written-authorization"></a>암호화를 전화 접속 연결에 사용하지 않는 경우 CIO 또는 해당 지정 담당자는 특정 서면 권한 부여를 제공합니다.

**ID**: 1173.01j1Organizational.6 - 01.j **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |계정 또는 리소스 위반을 방지하려면 쓰기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="the-organization-protects-wireless-access-to-systems-containing-sensitive-information-by-authenticating-both-users-and-devices"></a>조직은 사용자와 디바이스를 모두 인증하여 중요한 정보가 포함된 시스템에 대한 무선 액세스를 보호합니다.

**ID**: 1174.01j1Organizational.7 - 01.j **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |계정 또는 리소스 위반을 방지하려면 읽기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="remote-access-to-business-information-across-public-networks-only-takes-place-after-successful-identification-and-authentication"></a>공용 네트워크를 통한 비즈니스 정보에 대한 원격 액세스는 식별 및 인증이 성공한 후에만 수행합니다.

**ID**: 1175.01j1Organizational.8 - 01.j **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |가능한 네트워크 JIT(Just In Time) 액세스는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="the-organization-requires-a-callback-capability-with-re-authentication-to-verify-dial-up-connections-from-authorized-locations"></a>조직은 권한 부여된 위치에서 전화 접속 연결을 확인하기 위해 재인증을 사용하는 콜백 기능이 필요합니다.

**ID**: 1176.01j2Organizational.5 - 01.j **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |계정 또는 리소스 위반을 방지하려면 소유자 권한이 있는 모든 구독 계정에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="user-ids-assigned-to-vendors-are-reviewed-in-accordance-with-the-organizations-access-review-policy-at-a-minimum-annually"></a>공급업체에 할당된 사용자 ID는 최소한 매년 조직의 액세스 검토 정책에 따라 검토됩니다.

**ID**: 1177.01j2Organizational.6 - 01.j **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |계정 또는 리소스 위반을 방지하려면 쓰기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="node-authentication-including-cryptographic-techniques-eg-machine-certificates-serves-as-an-alternative-means-of-authenticating-groups-of-remote-users-where-they-are-connected-to-a-secure-shared-computer-facility"></a>암호화 기술(예: 컴퓨터 인증서)을 포함한 노드 인증은 보안 공유 컴퓨터 장치에 연결된 원격 사용자 그룹을 인증하는 대체 수단으로 사용됩니다.

**ID**: 1178.01j2Organizational.7 - 01.j **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |계정 또는 리소스 위반을 방지하려면 읽기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-monitors-and-controls-remote-access-methods"></a>정보 시스템은 원격 액세스 방법을 모니터링하고 제어합니다.

**ID**: 1179.01j3Organizational.1 - 01.j **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |가능한 네트워크 JIT(Just In Time) 액세스는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

## <a name="remote-diagnostic-and-configuration-port-protection"></a>원격 진단 및 구성 포트 보호

### <a name="access-to-network-equipment-is-physically-protected"></a>네트워크 장비에 대한 액세스는 물리적으로 보호됩니다.

**ID**: 1192.01l1Organizational.1 - 01.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |가능한 네트워크 JIT(Just In Time) 액세스는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="controls-for-the-access-to-diagnostic-and-configuration-ports-include-the-use-of-a-key-lock-and-the-implementation-of-supporting-procedures-to-control-physical-access-to-the-port"></a>진단 및 구성 포트에 대한 액세스 제어에는 키 잠금 사용 및 포트에 대한 물리적 액세스를 제어하도록 지원하는 절차의 구현이 포함됩니다.

**ID**: 1193.01l2Organizational.13 - 01.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신에서 관리 포트를 닫아야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |열려 있는 원격 관리 포트는 위험도가 높은 인터넷 기반 공격에 VM을 노출시킵니다. 이러한 공격은 자격 증명을 무차별적으로 대입하여 머신에 대한 관리자 액세스 권한을 획득하려고 시도합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="ports-services-and-similar-applications-installed-on-a-computer-or-network-systems-which-are-not-specifically-required-for-business-functionality-are-disabled-or-removed"></a>비즈니스 기능에 특별히 필요하지 않은 컴퓨터 또는 네트워크 시스템에 설치된 포트, 서비스 및 유사한 애플리케이션은 사용하지 않도록 설정되거나 제거됩니다.

**ID**: 1194.01l2Organizational.2 - 01.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |원격 디버깅에 웹 애플리케이션에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="the-organization-reviews-the-information-system-within-every-three-hundred-and-sixty--five-365-days-to-identify-and-disables-unnecessary-and-non-secure-functions-ports-protocols-andor-services"></a>조직은 365일마다 정보 시스템을 검토하여 불필요하고 안전하지 않은 기능, 포트, 프로토콜 및/또는 서비스를 식별하고 사용하지 않도록 설정합니다.

**ID**: 1195.01l3Organizational.1 - 01.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[함수 앱에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |원격 디버깅에 함수 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-identifies-unauthorized-blacklisted-software-on-the-information-system-prevents-program-execution-in-accordance-with-a-list-of-unauthorized-blacklisted-software-programs-employs-an-allow-all-deny-by-exception-policy-to-prohibit-execution-of-known-unauthorized-blacklisted-software-and-reviews-and-updates-the-list-of-unauthorized-blacklisted-software-programs-annually"></a>조직은 정보 시스템에서 무단(금지 목록에 추가된) 소프트웨어를 식별하고, 무단(금지 목록에 추가된) 소프트웨어 프로그램 목록에 따라 프로그램 실행을 방지하고, 모두 허용, 예외 기준 거부 정책을 사용하여 알려진 무단(금지 목록에 추가된) 소프트웨어의 실행을 금지하고, 매년 무단(금지 목록에 추가된) 소프트웨어 프로그램 목록을 검토하고 업데이트합니다.

**ID**: 1196.01l3Organizational.24 - 01.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API Apps에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |원격 디버깅에 API 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-disables-bluetooth-and-peer-to-peer-networking-protocols-within-the-information-system-determined-to-be-unnecessary-or-non-secure"></a>조직은 정보 시스템 내에서 불필요하거나 안전하지 않다고 결정된 Bluetooth 및 피어 투 피어 네트워킹 프로토콜을 사용하지 않도록 설정합니다.

**ID**: 1197.01l3Organizational.3 - 01.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |애플리케이션 제어를 활성화하여 머신에서 실행 중인 알려진 안전한 애플리케이션 목록을 정의하고, 다른 애플리케이션이 실행될 때 경고합니다. 이렇게 하면 맬웨어로부터 머신을 강화할 수 있습니다. 규칙을 구성하고 유지 관리하는 프로세스를 간소화하기 위해 Security Center는 기계 학습을 사용하여 각 머신에서 실행 중인 애플리케이션을 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

## <a name="segregation-in-networks"></a>네트워크 분리

### <a name="the-organizations-security-gateways-eg-firewalls-enforce-security-policies-and-are-configured-to-filter-traffic-between-domains-block-unauthorized-access-and-are-used-to-maintain-segregation-between-internal-wired-internal-wireless-and-external-network-segments-eg-the-internet-including-dmzs-and-enforce-access-control-policies-for-each-of-the-domains"></a>조직의 보안 게이트웨이(예: 방화벽)는 보안 정책을 적용하고, 도메인 간의 트래픽을 필터링하고 무단 액세스를 차단하도록 구성되며, DMZ를 포함하여 내부 유선, 내부 무선 및 외부 네트워크 세그먼트(예: 인터넷) 간의 분리를 유지하고 각 도메인에 대한 액세스 제어 정책을 적용하는 데 사용됩니다.

**ID**: 0805.01m1Organizational.12 - 01.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 App Service를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Container Registry를 감사합니다. |감사, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Cosmos DB를 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[이벤트 허브는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 이벤트 허브를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[게이트웨이 서브넷은 네트워크 보안 그룹을 사용하여 구성해서는 안 됨](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |이 정책은 게이트웨이 서브넷이 네트워크 보안 그룹으로 구성된 경우 거부합니다. 네트워크 보안 그룹을 게이트웨이 서브넷에 할당하면 게이트웨이가 작동을 중지합니다. |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |NSG(네트워크 보안 그룹)를 통해 가상 머신에 대한 액세스를 제한하여 잠재적인 위협으로부터 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Key Vault를 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[SQL Server는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 SQL Server를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[스토리지 계정은 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 스토리지 계정을 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[서브넷을 네트워크 보안 그룹과 연결해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |NSG(네트워크 보안 그룹)를 통해 VM에 대한 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. NSG는 서브넷에 대한 네트워크 트래픽을 허용 또는 거부하는 ACL(액세스 제어 목록) 규칙의 목록을 포함합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[가상 머신은 승인된 가상 네트워크에 연결되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |이 정책은 승인되지 않은 가상 네트워크에 연결된 모든 가상 머신을 감사합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="the-organizations-network-is-logically-and-physically-segmented-with-a-defined-security-perimeter-and-a-graduated-set-of-controls-including-subnetworks-for-publicly-accessible-system-components-that-are-logically-separated-from-the-internal-network-based-on-organizational-requirements-and-traffic-is-controlled-based-on-functionality-required-and-classification-of-the-datasystems-based-on-a-risk-assessment-and-their-respective-security-requirements"></a>조직 네트워크는 조직 요구 사항에 따라 내부 네트워크와 논리적으로 분리되어 공개적으로 액세스할 수 있는 시스템 구성 요소에 대한 하위 네트워크를 포함하여 정의된 보안 경계 및 분할된 제어 세트를 사용하여 논리적 및 물리적으로 분할됩니다. 트래픽은 위험 평가 및 각각의 보안 요구 사항에 기반한 필요한 기능 및 데이터/시스템의 분류에 따라 제어됩니다.

**ID**: 0806.01m2Organizational.12356 - 01.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 App Service를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Container Registry를 감사합니다. |감사, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Cosmos DB를 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[이벤트 허브는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 이벤트 허브를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[게이트웨이 서브넷은 네트워크 보안 그룹을 사용하여 구성해서는 안 됨](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |이 정책은 게이트웨이 서브넷이 네트워크 보안 그룹으로 구성된 경우 거부합니다. 네트워크 보안 그룹을 게이트웨이 서브넷에 할당하면 게이트웨이가 작동을 중지합니다. |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |NSG(네트워크 보안 그룹)를 통해 가상 머신에 대한 액세스를 제한하여 잠재적인 위협으로부터 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Key Vault를 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[SQL Server는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 SQL Server를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[스토리지 계정은 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 스토리지 계정을 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[서브넷을 네트워크 보안 그룹과 연결해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |NSG(네트워크 보안 그룹)를 통해 VM에 대한 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. NSG는 서브넷에 대한 네트워크 트래픽을 허용 또는 거부하는 ACL(액세스 제어 목록) 규칙의 목록을 포함합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[가상 머신은 승인된 가상 네트워크에 연결되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |이 정책은 승인되지 않은 가상 네트워크에 연결된 모든 가상 머신을 감사합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="networks-are-segregated-from-production-level-networks-when-migrating-physical-servers-applications-or-data-to-virtualized-servers"></a>네트워크는 물리적 서버, 애플리케이션 또는 데이터를 가상화된 서버로 마이그레이션할 때 프로덕션 수준 네트워크에서 분리됩니다.

**ID**: 0894.01m2Organizational.7 - 01.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 App Service를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Container Registry를 감사합니다. |감사, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Cosmos DB를 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[가상 네트워크를 만들 때 네트워크 감시자 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9b99dd8-06c5-4317-8629-9d86a3c6e7d9) |이 정책은 가상 네트워크를 사용하여 지역에 네트워크 감시자 리소스를 만듭니다. 네트워크 감시자 인스턴스를 배포하는 데 사용되는 networkWatcherRG라는 리소스 그룹이 있는지 확인해야 합니다. |DeployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |
|[이벤트 허브는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 이벤트 허브를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[게이트웨이 서브넷은 네트워크 보안 그룹을 사용하여 구성해서는 안 됨](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |이 정책은 게이트웨이 서브넷이 네트워크 보안 그룹으로 구성된 경우 거부합니다. 네트워크 보안 그룹을 게이트웨이 서브넷에 할당하면 게이트웨이가 작동을 중지합니다. |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |NSG(네트워크 보안 그룹)를 통해 가상 머신에 대한 액세스를 제한하여 잠재적인 위협으로부터 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Key Vault를 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[SQL Server는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 SQL Server를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[스토리지 계정은 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 스토리지 계정을 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[서브넷을 네트워크 보안 그룹과 연결해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |NSG(네트워크 보안 그룹)를 통해 VM에 대한 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. NSG는 서브넷에 대한 네트워크 트래픽을 허용 또는 거부하는 ACL(액세스 제어 목록) 규칙의 목록을 포함합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[가상 머신은 승인된 가상 네트워크에 연결되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |이 정책은 승인되지 않은 가상 네트워크에 연결된 모든 가상 머신을 감사합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

## <a name="network-connection-control"></a>네트워크 연결 제어

### <a name="network-traffic-is-controlled-in-accordance-with-the-organizations-access-control-policy-through-firewall-and-other-network-related-restrictions-for-each-network-access-point-or-external-telecommunication-services-managed-interface"></a>네트워크 트래픽은 조직 액세스 제어 정책에 따라 방화벽 및 각 네트워크 액세스 지점 또는 외부 통신 서비스의 관리형 인터페이스에 대한 기타 네트워크 관련 제한 사항을 통해 제어됩니다.

**ID**: 0809.01n2Organizational.1234 - 01.n **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[인터넷 연결 가상 머신에 적응형 네트워크 강화 권장 사항을 적용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center는 인터넷 연결 가상 머신의 트래픽 패턴을 분석하고 잠재적 공격 노출 영역을 줄이는 네트워크 보안 그룹 규칙 권장 사항을 제공합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL용 Azure Database는 SSL(Secure Sockets Layer)을 사용한 MySQL용 Azure Database 서버와 클라이언트 애플리케이션 간 연결을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL은 SSL(Secure Sockets Layer)을 사용하여 Azure Database for PostgreSQL 서버를 클라이언트 애플리케이션에 연결하는 것을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[함수 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |NSG(네트워크 보안 그룹)를 통해 가상 머신에 대한 액세스를 제한하여 잠재적인 위협으로부터 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[API 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[함수 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[웹앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Azure Cache for Redis에 보안 연결만 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |SSL을 통해 설정된 Azure Cache for Redis 연결만 감사 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[스토리지 계정에 보안 전송을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |스토리지 계정의 보안 전송 요구 사항을 감사합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[서브넷을 네트워크 보안 그룹과 연결해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |NSG(네트워크 보안 그룹)를 통해 VM에 대한 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. NSG는 서브넷에 대한 네트워크 트래픽을 허용 또는 거부하는 ACL(액세스 제어 목록) 규칙의 목록을 포함합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[가상 머신은 승인된 가상 네트워크에 연결되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |이 정책은 승인되지 않은 가상 네트워크에 연결된 모든 가상 머신을 감사합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[웹 애플리케이션은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="transmitted-information-is-secured-and-at-a-minimum-encrypted-over-open-public-networks"></a>전송된 정보는 보안이 유지되고, 최소한 개방형 공용 네트워크를 통해 암호화됩니다.

**ID**: 0810.01n2Organizational.5 - 01.n **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[인터넷 연결 가상 머신에 적응형 네트워크 강화 권장 사항을 적용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center는 인터넷 연결 가상 머신의 트래픽 패턴을 분석하고 잠재적 공격 노출 영역을 줄이는 네트워크 보안 그룹 규칙 권장 사항을 제공합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL용 Azure Database는 SSL(Secure Sockets Layer)을 사용한 MySQL용 Azure Database 서버와 클라이언트 애플리케이션 간 연결을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL은 SSL(Secure Sockets Layer)을 사용하여 Azure Database for PostgreSQL 서버를 클라이언트 애플리케이션에 연결하는 것을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[함수 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |NSG(네트워크 보안 그룹)를 통해 가상 머신에 대한 액세스를 제한하여 잠재적인 위협으로부터 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[API 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[함수 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[웹앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Azure Cache for Redis에 보안 연결만 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |SSL을 통해 설정된 Azure Cache for Redis 연결만 감사 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[스토리지 계정에 보안 전송을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |스토리지 계정의 보안 전송 요구 사항을 감사합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[서브넷을 네트워크 보안 그룹과 연결해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |NSG(네트워크 보안 그룹)를 통해 VM에 대한 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. NSG는 서브넷에 대한 네트워크 트래픽을 허용 또는 거부하는 ACL(액세스 제어 목록) 규칙의 목록을 포함합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[가상 머신은 승인된 가상 네트워크에 연결되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |이 정책은 승인되지 않은 가상 네트워크에 연결된 모든 가상 머신을 감사합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[웹 애플리케이션은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="exceptions-to-the-traffic-flow-policy-are-documented-with-a-supporting-missionbusiness-need-duration-of-the-exception-and-reviewed-at-least-annually-traffic-flow-policy-exceptions-are-removed-when-no-longer-supported-by-an-explicit-missionbusiness-need"></a>트래픽 흐름 정책에 대한 예외는 지원 업무/비즈니스 요구 사항, 예외 기간을 사용하여 문서화되고, 최소한 매년 검토됩니다. 트래픽 흐름 정책 예외는 명시적 업무/비즈니스 요구 사항에 따라 더 이상 지원되지 않는 경우 제거됩니다.

**ID**: 0811.01n2Organizational.6 - 01.n **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[인터넷 연결 가상 머신에 적응형 네트워크 강화 권장 사항을 적용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center는 인터넷 연결 가상 머신의 트래픽 패턴을 분석하고 잠재적 공격 노출 영역을 줄이는 네트워크 보안 그룹 규칙 권장 사항을 제공합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL용 Azure Database는 SSL(Secure Sockets Layer)을 사용한 MySQL용 Azure Database 서버와 클라이언트 애플리케이션 간 연결을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL은 SSL(Secure Sockets Layer)을 사용하여 Azure Database for PostgreSQL 서버를 클라이언트 애플리케이션에 연결하는 것을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[함수 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |NSG(네트워크 보안 그룹)를 통해 가상 머신에 대한 액세스를 제한하여 잠재적인 위협으로부터 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[API 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[함수 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[웹앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Azure Cache for Redis에 보안 연결만 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |SSL을 통해 설정된 Azure Cache for Redis 연결만 감사 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[스토리지 계정에 보안 전송을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |스토리지 계정의 보안 전송 요구 사항을 감사합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[서브넷을 네트워크 보안 그룹과 연결해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |NSG(네트워크 보안 그룹)를 통해 VM에 대한 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. NSG는 서브넷에 대한 네트워크 트래픽을 허용 또는 거부하는 ACL(액세스 제어 목록) 규칙의 목록을 포함합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[가상 머신은 승인된 가상 네트워크에 연결되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |이 정책은 승인되지 않은 가상 네트워크에 연결된 모든 가상 머신을 감사합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[웹 애플리케이션은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="remote-devices-establishing-a-non-remote-connection-are-not-allowed-to-communicate-with-external-remote-resources"></a>비원격 연결을 설정하는 원격 디바이스는 외부(원격) 리소스와 통신할 수 없습니다.

**ID**: 0812.01n2Organizational.8 - 01.n **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[인터넷 연결 가상 머신에 적응형 네트워크 강화 권장 사항을 적용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center는 인터넷 연결 가상 머신의 트래픽 패턴을 분석하고 잠재적 공격 노출 영역을 줄이는 네트워크 보안 그룹 규칙 권장 사항을 제공합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL용 Azure Database는 SSL(Secure Sockets Layer)을 사용한 MySQL용 Azure Database 서버와 클라이언트 애플리케이션 간 연결을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL은 SSL(Secure Sockets Layer)을 사용하여 Azure Database for PostgreSQL 서버를 클라이언트 애플리케이션에 연결하는 것을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[함수 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |NSG(네트워크 보안 그룹)를 통해 가상 머신에 대한 액세스를 제한하여 잠재적인 위협으로부터 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[API 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[함수 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[웹앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Azure Cache for Redis에 보안 연결만 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |SSL을 통해 설정된 Azure Cache for Redis 연결만 감사 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[스토리지 계정에 보안 전송을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |스토리지 계정의 보안 전송 요구 사항을 감사합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[서브넷을 네트워크 보안 그룹과 연결해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |NSG(네트워크 보안 그룹)를 통해 VM에 대한 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. NSG는 서브넷에 대한 네트워크 트래픽을 허용 또는 거부하는 ACL(액세스 제어 목록) 규칙의 목록을 포함합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[가상 머신은 승인된 가상 네트워크에 연결되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |이 정책은 승인되지 않은 가상 네트워크에 연결된 모든 가상 머신을 감사합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[웹 애플리케이션은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="the-ability-of-users-to-connect-to-the-internal-network-is-restricted-using-a-deny-by-default-and-allow-by-exception-policy-at-managed-interfaces-according-to-the-access-control-policy-and-the-requirements-of-clinical-and-business-applications"></a>사용자가 내부 네트워크에 연결하는 기능은 액세스 제어 정책과 임상 및 비즈니스 애플리케이션의 요구 사항에 따라 관리형 인터페이스에서 기본적으로 거부 및 예외 허용 정책을 사용하여 제한됩니다.

**ID**: 0814.01n1Organizational.12 - 01.n **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[인터넷 연결 가상 머신에 적응형 네트워크 강화 권장 사항을 적용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center는 인터넷 연결 가상 머신의 트래픽 패턴을 분석하고 잠재적 공격 노출 영역을 줄이는 네트워크 보안 그룹 규칙 권장 사항을 제공합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL용 Azure Database는 SSL(Secure Sockets Layer)을 사용한 MySQL용 Azure Database 서버와 클라이언트 애플리케이션 간 연결을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL은 SSL(Secure Sockets Layer)을 사용하여 Azure Database for PostgreSQL 서버를 클라이언트 애플리케이션에 연결하는 것을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[함수 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |NSG(네트워크 보안 그룹)를 통해 가상 머신에 대한 액세스를 제한하여 잠재적인 위협으로부터 가상 머신을 보호합니다. [https://aka.ms/nsg-doc](../../../virtual-network/network-security-groups-overview.md)에서 NSG를 통한 트래픽 제어에 대해 자세히 알아보기 |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[API 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[함수 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[웹앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Azure Cache for Redis에 보안 연결만 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |SSL을 통해 설정된 Azure Cache for Redis 연결만 감사 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[스토리지 계정에 보안 전송을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |스토리지 계정의 보안 전송 요구 사항을 감사합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[서브넷을 네트워크 보안 그룹과 연결해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |NSG(네트워크 보안 그룹)를 통해 VM에 대한 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. NSG는 서브넷에 대한 네트워크 트래픽을 허용 또는 거부하는 ACL(액세스 제어 목록) 규칙의 목록을 포함합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[가상 머신은 승인된 가상 네트워크에 연결되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |이 정책은 승인되지 않은 가상 네트워크에 연결된 모든 가상 머신을 감사합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[웹 애플리케이션은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="user-identification-and-authentication"></a>사용자 식별 및 인증

### <a name="the-organization-ensures-that-redundant-user-ids-are-not-issued-to-other-users-and-that-all-users-are-uniquely-identified-and-authenticated-for-both-local-and-remote-access-to-information-systems"></a>조직은 중복 사용자 ID가 다른 사용자에게 발급되지 않도록 하고, 모든 사용자가 정보 시스템에 대한 로컬 및 원격 액세스 모두에 대해 고유하게 식별되고 인증되도록 합니다.

**ID**: 11109.01q1Organizational.57 - 01.q **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |계정 또는 리소스 위반을 방지하려면 소유자 권한이 있는 모든 구독 계정에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="non-organizational-users-all-information-system-users-other-than-organizational-users-such-as-patients-customers-contractors-or-foreign-nationals-or-processes-acting-on-behalf-of-non-organizational-users-determined-to-need-access-to-information-residing-on-the-organizations-information-systems-are-uniquely-identified-and-authenticated"></a>비조직 사용자(환자, 고객, 계약자 또는 외국 국적자와 같은 조직 사용자 이외의 모든 정보 시스템 사용자) 또는 조직의 정보 시스템에 있는 정보에 대한 액세스 권한이 필요하다고 결정된 비조직 사용자를 대신하여 작동하는 프로세스가 고유하게 식별되고 인증됩니다.

**ID**: 11110.01q1Organizational.6 - 01.q **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |계정 또는 리소스 위반을 방지하려면 쓰기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="when-pki-based-authentication-is-used-the-information-system-validates-certificates-by-constructing-and-verifying-a-certification-path-to-an-accepted-trust-anchor-including-checking-certificate-status-information-enforces-access-to-the-corresponding-private-key-maps-the-identity-to-the-corresponding-account-of-the-individual-or-group-and-implements-a-local-cache-of-revocation-data-to-support-path-discovery-and-validation-in-case-of-an-inability-to-access-revocation-information-via-the-network"></a>PKI 기반 인증을 사용하는 경우 정보 시스템은 인증서 상태 정보 확인을 포함하여 허용된 신뢰 앵커에 대한 인증 경로를 구성하고 확인하여 인증서의 유효성을 검사합니다. 해당 프라이빗 키에 대한 액세스를 적용합니다. ID를 개별 또는 그룹의 해당 계정에 매핑합니다. 그리고 네트워크를 통해 해지 정보에 액세스할 수 없는 경우 경로 검색 및 유효성 검사를 지원하기 위해 해지 데이터의 로컬 캐시를 구현합니다.

**ID**: 11111.01q2System.4 - 01.q **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |계정 또는 리소스 위반을 방지하려면 읽기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-employs-replay-resistant-authentication-mechanisms-such-as-nonce-one-time-passwords-or-time-stamps-to-secure-network-access-for-privileged-accounts-and-for-hardware-token-based-authentication-employs-mechanisms-that-satisfy-minimum-token-requirements-discussed-in-nist-sp-800-63-2-electronic-authentication-guideline"></a>정보 시스템은 nonce, 일회용 암호 또는 타임스탬프와 같은 재생 방지 인증 메커니즘을 사용하여 권한 있는 계정에 대한 네트워크 액세스를 보호합니다. 하드웨어 토큰 기반 인증의 경우 NIST SP 800-63-2 전자 인증 지침에 설명된 최소 토큰 요구 사항을 충족하는 메커니즘을 사용합니다.

**ID**: 11112.01q2Organizational.67 - 01.q **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 최대 3명의 소유자를 지정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |보안이 침해된 소유자의 위반 가능성을 줄이려면 최대 3명의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-requires-that-electronic-signatures-unique-to-one-individual-cannot-be-reused-by-or-reassigned-to-anyone-else"></a>조직은 다른 사용자가 한 개인에게 고유한 전자 서명을 다시 사용하거나 다시 할당할 수 없도록 해야 합니다.

**ID**: 11208.01q1Organizational.8 - 01.q **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 둘 이상의 소유자를 할당해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |관리자 액세스 중복성을 유지하려면 둘 이상의 구독 소유자를 지정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="electronic-signatures-and-handwritten-signatures-executed-to-electronic-records-shall-be-linked-to-their-respective-electronic-records"></a>전자 레코드에 실행되는 전자 서명 및 필기 서명은 각 전자 레코드에 연결됩니다.

**ID**: 11210.01q2Organizational.10 - 01.q **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[관리자 그룹에 지정된 구성원이 있는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 로컬 관리자 그룹에 정책 매개 변수에 나열된 구성원이 하나 이상 포함된 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="signed-electronic-records-shall-contain-information-associated-with-the-signing-in-human-readable-format"></a>서명된 전자 레코드에는 사람이 읽을 수 있는 형식의 서명과 연결된 정보가 포함됩니다.

**ID**: 11211.01q2Organizational.11 - 01.q **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[관리자 그룹에 지정된 구성원이 없는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 로컬 관리자 그룹에 정책 매개 변수에 나열된 구성원이 하나 이상 포함되어 있지 않은 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

### <a name="users-who-performed-privileged-functions-eg-system-administration-use-separate-accounts-when-performing-those-privileged-functions"></a>권한 있는 기능(예: 시스템 관리)을 수행한 사용자는 권한 있는 기능을 수행할 때 별도의 계정을 사용합니다.

**ID**: 1123.01q1System.2 - 01.q **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[관리자 그룹에 추가 계정이 있는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 로컬 관리자 그룹에 정책 매개 변수에 나열되지 않은 구성원이 포함된 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |

### <a name="multi-factor-authentication-methods-are-used-in-accordance-with-organizational-policy-eg-for-remote-network-access"></a>다단계 인증 방법은 조직 정책(예: 원격 네트워크 액세스용)에 따라 사용됩니다.

**ID**: 1125.01q2System.1 - 01.q **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[관리자 그룹에 지정된 구성원이 있는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 로컬 관리자 그룹에 정책 매개 변수에 나열된 구성원이 하나 이상 포함된 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="where-tokens-are-provided-for-multi-factor-authentication-in-person-verification-is-required-prior-to-granting-access"></a>다단계 인증을 위해 토큰이 제공되는 경우 액세스 권한을 부여하기 전에 사용자가 직접 확인해야 합니다.

**ID**: 1127.01q2System.3 - 01.q **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[관리자 그룹에 지정된 구성원이 없는 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 로컬 관리자 그룹에 정책 매개 변수에 나열된 구성원이 하나 이상 포함되어 있지 않은 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

## <a name="identification-of-risks-related-to-external-parties"></a>외부 당사자와 관련된 위험 식별

### <a name="access-to-the-organizations-information-and-systems-by-external-parties-is-not-permitted-until-due-diligence-has-been-conducted-the-appropriate-controls-have-been-implemented-and-a-contractagreement-reflecting-the-security-requirements-is-signed-acknowledging-they-understand-and-accept-their-obligations"></a>외부 당사자는 직접 조사를 수행하고, 적절한 제어를 구현하고, 보안 요구 사항을 반영하는 계약/규약에 서명하여 해당 의무를 이해하고 수락한다고 인정할 때까지 조직 정보 및 시스템에 액세스할 수 없습니다.

**ID**: 1401.05i1Organizational.1239 - 05.i **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[스토리지 계정에 보안 전송을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |스토리지 계정의 보안 전송 요구 사항을 감사합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="remote-access-connections-between-the-organization-and-external-parties-are-encrypted"></a>조직과 외부 당사자 간의 원격 액세스 연결은 암호화됩니다.

**ID**: 1402.05i1Organizational.45 - 05.i **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[함수 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |

### <a name="access-granted-to-external-parties-is-limited-to-the-minimum-necessary-and-granted-only-for-the-duration-required"></a>외부 당사자에게 부여된 액세스 권한은 필요한 최소 권한으로 제한되며, 필요한 기간 동안만 부여됩니다.

**ID**: 1403.05i1Organizational.67 - 05.i **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[웹 애플리케이션은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="due-diligence-of-the-external-party-includes-interviews-document-review-checklists-certification-reviews-eg-hitrustnbspor-other-remote-means"></a>외부 당사자의 직접 조사에는 면접, 문서 검토, 검사 목록, 인증 검토(예: HITRUST)&nbsp; 또는 기타 원격 수단이 포함됩니다.

**ID**: 1404.05i2Organizational.1 - 05.i **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |

### <a name="the-identification-of-risks-related-to-external-party-access-takes-into-account-a-minimal-set-of-specifically-defined-issues"></a>외부 당사자 액세스와 관련된 위험 식별은 구체적으로 정의된 문제를 최소화하는 데 사용됩니다.

**ID**: 1418.05i1Organizational.8 - 05.i **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL용 Azure Database는 SSL(Secure Sockets Layer)을 사용한 MySQL용 Azure Database 서버와 클라이언트 애플리케이션 간 연결을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-organization-obtains-satisfactory-assurances-that-reasonable-information-security-exists-across-their-information-supply-chain-by-performing-an-annual-review-which-includes-all-partnersthird-party-providers-upon-which-their-information-supply-chain-depends"></a>조직은 연례 검토를 수행하여 정보 공급망 전반에 걸쳐 적절한 정보 보안을 유지한다는 만족스러운 적절한 보증을 획득합니다. 여기에는 해당 정보 공급망이 종속된 모든 파트너/타사 공급자가 포함됩니다.

**ID**: 1450.05i2Organizational.2 - 05.i **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL은 SSL(Secure Sockets Layer)을 사용하여 Azure Database for PostgreSQL 서버를 클라이언트 애플리케이션에 연결하는 것을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="cloud-service-providers-design-and-implement-controls-to-mitigate-and-contain-data-security-risks-through-proper-separation-of-duties-role-based-access-and-least-privilege-access-for-all-personnel-within-their-supply-chain"></a>클라우드 서비스 공급자는 적절한 업무 분리, 역할 기반 액세스 및 공급망 내의 모든 직원에 대한 최소 권한 액세스를 통해 데이터 보안 위험을 완화하고 억제하기 위한 제어를 설계하고 구현합니다.

**ID**: 1451.05iCSPOrganizational.2 - 05.i **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Cache for Redis에 보안 연결만 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |SSL을 통해 설정된 Azure Cache for Redis 연결만 감사 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="audit-logging"></a>감사 로깅

### <a name="a-secure-audit-record-is-created-for-all-activities-on-the-system-create-read-update-delete-involving-covered-information"></a>적용 정보와 관련된 시스템의 모든 활동(만들기, 읽기, 업데이트, 삭제)에 대한 보안 감사 레코드가 만들어집니다.

**ID**: 1202.09aa1System.1 - 09.aa **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Lake Store에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[가상 머신 확장 집합에 대한 시스템 업데이트를 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |Windows 및 Linux 가상 머신 확장 집합의 보안 유지를 위해 설치해야 하는 시스템 보안 업데이트 및 중요 업데이트가 누락되었는지 감사합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |

### <a name="audit-records-include-the-unique-user-id-unique-data-subject-id-function-performed-and-datetime-the-event-was-performed"></a>감사 레코드에는 고유한 사용자 ID, 고유한 데이터 주체 ID, 수행된 기능 및 이벤트가 수행된 날짜/시간이 포함됩니다.

**ID**: 1203.09aa1System.2 - 09.aa **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Logic Apps에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |

### <a name="the-activities-of-privileged-users-administrators-operators-etc-include-the-successfailure-of-the-event-time-the-event-occurred-the-account-involved-the-processes-involved-and-additional-information-about-the-event"></a>권한 있는 사용자(관리자, 운영자 등)의 활동에는 이벤트의 성공/실패, 이벤트가 발생한 시간, 관련된 계정, 관련된 프로세스 및 이벤트에 대한 추가 정보가 포함됩니다.

**ID**: 1204.09aa1System.3 - 09.aa **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

### <a name="logs-of-messages-sent-and-received-are-maintained-including-the-date-time-origin-and-destination-of-the-message-but-not-its-contents"></a>메시지의 날짜, 시간, 원본 및 대상을 포함하여 보내고 받은 메시지의 로그는 유지 관리되지만, 해당 내용은 유지 관리되지 않습니다.

**ID**: 1205.09aa2System.1 - 09.aa **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Batch 계정에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |

### <a name="auditing-is-always-available-while-the-system-is-active-and-tracks-key-events-successfailed-data-access-system-security-configuration-changes-privileged-or-utility-use-any-alarms-raised-nbspactivation-and-de-activation-of-protection-systems-eg-av-and-ids-activation-and-deactivation-of-identification-and-authentication-mechanisms-and-creation-and-deletion-of-system-level-objects"></a>감사는 시스템이 활성 상태인 동안 항상 사용할 수 있으며, 주요 이벤트, 데이터 액세스 성공/실패, 시스템 보안 구성 변경, 권한 있는 사용 또는 유틸리티 사용, 경보 발생, &nbsp;보호 시스템(예: A/V 및 IDS) 활성화/비활성화, 식별/인증 메커니즘 활성화/비활성화 및 시스템 수준 개체 만들기/삭제를 추적합니다.

**ID**: 1206.09aa2System.23 - 09.aa **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtual Machine Scale Sets에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |인시던트 또는 손상이 발생하는 경우 조사가 필요할 때 활동 내역을 다시 만들 수 있도록 로그를 사용하도록 설정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

### <a name="audit-records-are-retained-for-90-days-and-older-audit-records-are-archived-for-one-year"></a>감사 레코드는 90일 동안 보존되며, 이전 감사 레코드는 1년 동안 보관됩니다.

**ID**: 1207.09aa2System.4 - 09.aa **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Stream Analytics에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[Event Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

### <a name="audit-logs-are-maintained-for-management-activities-system-and-application-startupshutdownerrors-file-changes-and-security-policy-changes"></a>감사 로그는 관리 활동, 시스템 및 애플리케이션 시작/종료/오류, 파일 변경 및 보안 정책 변경에 대해 유지 관리됩니다.

**ID**: 1208.09aa3System.1 - 09.aa **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Search Services에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Service Bus에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

### <a name="the-information-system-generates-audit-records-containing-the-following-detailed-information-i-filename-accessed-ii-program-or-command-used-to-initiate-the-event-and-iii-source-and-destination-addresses"></a>정보 시스템은 (i) 액세스되는 파일 이름, (ii) 이벤트를 시작하는 데 사용되는 프로그램 또는 명령, (iii) 원본 및 대상 주소와 같은 세부 정보가 포함된 감사 레코드를 생성합니다.

**ID**: 1209.09aa3System.2 - 09.aa **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Services의 진단 로그를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |앱에서 진단 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |

### <a name="all-disclosures-of-covered-information-within-or-outside-of-the-organization-are-logged-including-type-of-disclosure-datetime-of-the-event-recipient-and-sender"></a>공개 유형, 이벤트 날짜/시간, 받는 사람 및 보낸 사람을 포함하여 조직 내부 또는 외부의 모든 적용 정보에 대한 공개는 기록됩니다.

**ID**: 1210.09aa3System.3 - 09.aa **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[진단 설정 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |선택한 리소스 종류에 대한 감사 진단 설정 |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Data Lake Analytics에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |

### <a name="the-organization-verifies-every-ninety-90-days-for-each-extract-of-covered-information-recorded-that-the-data-is-erased-or-its-use-is-still-required"></a>조직은 데이터를 지우거나 해당 데이터의 사용이 계속 필요한 것으로 기록된 적용 정보의 각 추출에 대해 90일마다 확인합니다.

**ID**: 1211.09aa3System.4 - 09.aa **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 서버에 대한 감사가 사용되도록 설정되어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |서버의 모든 데이터베이스에서 데이터베이스 활동을 추적하고 감사 로그에 저장하려면 SQL Server에서의 감사를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Azure Key Vault Managed HSM의 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2a5b911-5617-447e-a49e-59dbe0e0434b) |보안 인시던트가 발생하거나 네트워크가 손상되었을 때 조사를 위해 활동 기록을 다시 만들려면 Managed HSM에서 리소스 로그를 사용하도록 설정하여 감사할 수 있습니다. 여기([https://docs.microsoft.com/azure/key-vault/managed-hsm/logging](../../../key-vault/managed-hsm/logging.md))의 지침을 따르세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_AuditDiagnosticLog_Audit.json) |
|[Key Vault의 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

## <a name="monitoring-system-use"></a>시스템 사용 모니터링

### <a name="unauthorized-remote-connections-to-the-information-systems-are-monitored-and-reviewed-at-least-quarterly-and-appropriate-action-is-taken-if-an-unauthorized-connection-is-discovered"></a>정보 시스템에 대한 무단 원격 연결은 최소한 분기별로 모니터링 및 검토되며, 무단 연결이 검색되면 적절한 작업이 수행됩니다.

**ID**: 1120.09ab3System.9 - 09.ab **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor는 모든 지역의 활동 로그를 수집해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |이 정책은 글로벌 지역을 포함하여 모든 Azure 지원 지역에서 활동을 내보내지 않는 Azure Monitor 로그 프로필을 감사합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="the-organization-monitors-the-information-system-to-identify-irregularities-or-anomalies-that-are-indicators-of-a-system-malfunction-or-compromise-and-help-confirm-the-system-is-functioning-in-an-optimal-resilient-and-secure-state"></a>조직은 정보 시스템을 모니터링하여 시스템 오작동 또는 손상에 대한 지표인 변칙 또는 이상값을 식별하고 시스템이 복원력 있는 최적의 보안 상태에서 작동하는지 확인하도록 지원합니다.

**ID**: 12100.09ab2System.15 - 09.ab **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신에 Log Analytics 에이전트를 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |이 정책은 모든 Windows/Linux 가상 머신을 감사하여 Log Analytics 에이전트가 설치되어 있지 않은지 확인합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-specifies-how-often-audit-logs-are-reviewed-how-the-reviews-are-documented-and-the-specific-roles-and-responsibilities-of-the-personnel-conducting-the-reviews-including-the-professional-certifications-or-other-qualifications-required"></a>조직은 감사 로그를 검토하는 빈도, 검토를 문서화하는 방법 및 검토를 수행하는 직원의 특정 역할과 책임(전문가 인증 또는 기타 필요한 자격 포함)을 지정합니다.

**ID**: 12101.09ab1Organizational.3 - 09.ab **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtual Machine Scale Sets에 Log Analytics 에이전트를 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |이 정책은 모든 Windows/Linux Virtual Machine Scale Sets를 감사하여 Log Analytics 에이전트가 설치되어 있지 않은지 확인합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-shall-periodically-test-its-monitoring-and-detection-processes-remediate-deficiencies-and-improve-its-processes"></a>조직은 모니터링 및 검색 프로세스를 정기적으로 테스트하고, 결함을 수정하고, 프로세스를 향상시켜야 합니다.

**ID**: 12102.09ab1Organizational.4 - 09.ab **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Log Analytics 에이전트가 예상대로 연결되지 않은 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 에이전트가 설치되지 않았거나 설치되었지만 정책 매개 변수에 지정된 ID 이외의 작업 영역에 등록된 COM 개체 AgentConfigManager.MgmtSvcCfg가 반환되는 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="all-applicable-legal-requirements-related-to-monitoring-authorized-access-and-unauthorized-access-attempts-are-met"></a>승인된 액세스 및 무단 액세스 시도를 모니터링하는 데 관련된 모든 해당 법적 요구 사항을 충족합니다.

**ID**: 1212.09ab1System.1 - 09.ab **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor 로그 프로필은 'write'(쓰기), 'delete'(삭제) 및 'action'(작업) 범주에 대한 로그를 수집해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |이 정책을 사용하면 로그 프로필이 '쓰기,' '삭제' 및 '작업' 범주에 대한 로그를 수집합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="automated-systems-deployed-throughout-the-organizations-environment-are-used-to-monitor-key-events-and-anomalous-activity-and-analyze-system-logs-the-results-of-which-are-reviewed-regularly"></a>조직의 환경 전체에 배포된 자동화된 시스템은 주요 이벤트 및 비정상 활동을 모니터링하고 시스템 로그를 분석하는 데 사용되며, 그 결과는 정기적으로 검토됩니다.

**ID**: 1213.09ab2System.128 - 09.ab **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 Log Analytics 에이전트의 자동 프로비저닝을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |보안 취약성 및 위협을 모니터링하기 위해 Azure Security Center는 Azure 가상 머신에서 데이터를 수집합니다. 데이터는 이전에 MMA(Microsoft Monitoring Agent)로 알려진 Log Analytics 에이전트에 의해 수집되며, 머신에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석을 위해 Log Analytics 작업 영역에 데이터를 복사합니다. 지원되는 모든 Azure VM과 생성된 모든 새 VM에 에이전트를 자동으로 배포하려면 자동 프로비저닝을 사용하도록 설정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="monitoring-includes-privileged-operations-authorized-access-or-unauthorized-access-attempts-including-attempts-to-access-deactivated-accounts-and-system-alerts-or-failures"></a>모니터링에는 비활성화된 계정에 대한 액세스 시도, 시스템 경고 또는 오류를 포함하여 권한 있는 작업, 승인된 액세스 또는 무단 액세스 시도가 포함됩니다.

**ID**: 1214.09ab2System.3456 - 09.ab **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor는 모든 지역의 활동 로그를 수집해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |이 정책은 글로벌 지역을 포함하여 모든 Azure 지원 지역에서 활동을 내보내지 않는 Azure Monitor 로그 프로필을 감사합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="auditing-and-monitoring-systems-employed-by-the-organization-support-audit-reduction-and-report-generation"></a>조직에서 사용하는 감사 및 모니터링 시스템은 감사 감소 및 보고서 생성을 지원합니다.

**ID**: 1215.09ab2System.7 - 09.ab **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신에 Log Analytics 에이전트를 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |이 정책은 모든 Windows/Linux 가상 머신을 감사하여 Log Analytics 에이전트가 설치되어 있지 않은지 확인합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="automated-systems-are-used-to-review-monitoring-activities-of-security-systems-eg-ipsids-and-system-records-on-a-daily-basis-and-identify-and-document-anomalies"></a>자동화된 시스템을 사용하여 보안 시스템(예: IPS/IDS) 및 시스템 레코드의 모니터링 활동을 매일 검토하고 변칙을 식별하고 문서화합니다.

**ID**: 1216.09ab3System.12 - 09.ab **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtual Machine Scale Sets에 Log Analytics 에이전트를 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |이 정책은 모든 Windows/Linux Virtual Machine Scale Sets를 감사하여 Log Analytics 에이전트가 설치되어 있지 않은지 확인합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="alerts-are-generated-for-technical-personnel-to-analyze-and-investigate-suspicious-activity-or-suspected-violations"></a>기술 담당자가 의심스러운 활동 또는 위반을 분석하고 조사할 수 있도록 경고가 생성됩니다.

**ID**: 1217.09ab3System.3 - 09.ab **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Log Analytics 에이전트가 예상대로 연결되지 않은 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 에이전트가 설치되지 않았거나 설치되었지만 정책 매개 변수에 지정된 ID 이외의 작업 영역에 등록된 COM 개체 AgentConfigManager.MgmtSvcCfg가 반환되는 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="the-information-system-is-able-to-automatically-process-audit-records-for-events-of-interest-based-on-selectable-criteria"></a>정보 시스템은 선택 가능한 조건에 따라 관심 있는 이벤트에 대한 감사 레코드를 자동으로 처리할 수 있습니다.

**ID**: 1219.09ab3System.10 - 09.ab **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor 로그 프로필은 'write'(쓰기), 'delete'(삭제) 및 'action'(작업) 범주에 대한 로그를 수집해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |이 정책을 사용하면 로그 프로필이 '쓰기,' '삭제' 및 '작업' 범주에 대한 로그를 수집합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="monitoring-includes-inbound-and-outbound-communications-and-file-integrity-monitoring"></a>모니터링에는 인바운드/아웃바운드 통신 및 파일 무결성 모니터링이 포함됩니다.

**ID**: 1220.09ab3System.56 - 09.ab **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 Log Analytics 에이전트의 자동 프로비저닝을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |보안 취약성 및 위협을 모니터링하기 위해 Azure Security Center는 Azure 가상 머신에서 데이터를 수집합니다. 데이터는 이전에 MMA(Microsoft Monitoring Agent)로 알려진 Log Analytics 에이전트에 의해 수집되며, 머신에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석을 위해 Log Analytics 작업 영역에 데이터를 복사합니다. 지원되는 모든 Azure VM과 생성된 모든 새 VM에 에이전트를 자동으로 배포하려면 자동 프로비저닝을 사용하도록 설정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

## <a name="administrator-and-operator-logs"></a>관리자 및 운영자 로그

### <a name="the-organization-ensures-proper-logging-is-enabled-in-order-to-audit-administrator-activities-and-reviews-system-administrator-and-operator-logs-on-a-regular-basis"></a>조직에서는 관리자 활동을 감사하기 위해 적절한 로깅을 사용하도록 설정하고, 시스템 관리자 및 운영자 로그를 정기적으로 검토합니다.

**ID**: 1270.09ad1System.12 - 09.ad **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[특정 관리 작업의 활동 로그 경고가 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |이 정책은 활동 로그 경고가 구성되지 않은 특정 관리 작업을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="an-intrusion-detection-system-managed-outside-of-the-control-of-system-and-network-administrators-is-used-to-monitor-system-and-network-administration-activities-for-compliance"></a>시스템 및 네트워크 관리자의 제어 외부에서 관리되는 침입 탐지 시스템은 규정 준수를 위해 시스템 및 네트워크 관리 활동을 모니터링하는 데 사용됩니다.

**ID**: 1271.09ad1System.1 - 09.ad **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[특정 관리 작업의 활동 로그 경고가 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |이 정책은 활동 로그 경고가 구성되지 않은 특정 관리 작업을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

## <a name="segregation-of-duties"></a>의무 분리

### <a name="separation-of-duties-is-used-to-limit-the-risk-of-unauthorized-or-unintentional-modification-of-information-and-systems"></a>의무 분리는 정보 및 시스템의 무단 또는 의도하지 않은 수정의 위험을 제한하는 데 사용됩니다.

**ID**: 1229.09c1Organizational.1 - 09.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kubernetes Services에서 RBAC(역할 기반 액세스 제어)를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |사용자가 수행할 수 있는 작업에 대한 세부적인 필터링을 제공하려면 RBAC(역할 기반 액세스 제어)를 사용하여 Kubernetes Service 클러스터에서 권한을 관리하고 관련 권한 부여 정책을 구성합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="no-single-person-is-able-to-access-modify-or-use-information-systems-without-authorization-or-detection"></a>어떤 사용자도 권한 부여 또는 검색 없이 정보 시스템을 액세스하거나, 수정하거나, 사용할 수 없습니다.

**ID**: 1230.09c2Organizational.1 - 09.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[사용자 지정 RBAC 규칙 사용 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |오류가 발생하기 쉬운 사용자 지정 RBAC 역할 대신 '소유자, 기여자, 읽기 권한자' 같은 기본 제공 역할을 감사합니다. 사용자 지정 역할 사용은 예외로 처리되며 엄격한 검토 및 위협 모델링이 필요합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

### <a name="access-for-individuals-responsible-for-administering-nbspaccess-controls-is-limited-to-the-minimum-necessary-based-upon-each-users-role-and-responsibilities-and-these-individuals-cannot-access-audit-functions-related-to-these-controls"></a>액세스 제어 관리를 담당하는 개인에 대한 액세스는 &nbsp;각 사용자의 역할과 책임에 따라 필요한 최소한으로 제한되며, 이러한 개인은 이러한 제어와 관련된 감사 기능에 액세스할 수 없습니다.

**ID**: 1232.09c3Organizational.12 - 09.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 컴퓨터는 '사용자 권한 할당' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe068b215-0026-4354-b347-8fb2766f73a2) |Windows 컴퓨터에는 로컬 로그온, RDP, 네트워크 액세스 및 기타 다양한 사용자 활동을 허용하는 그룹 정책 설정이 '사용자 권한 할당' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_UserRightsAssignment_AINE.json) |

### <a name="security-audit-activities-are-independent"></a>보안 감사 활동은 독립적입니다.

**ID**: 1276.09c2Organizational.2 - 09.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[사용자 지정 구독 소유자 역할이 없어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |이 정책은 사용자 지정 구독 소유자 역할이 없도록 합니다. |감사, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

### <a name="the-initiation-of-an-event-is-separated-from-its-authorization-to-reduce-the-possibility-of-collusion"></a>이벤트의 시작은 공모의 가능성을 줄이기 위해 해당 권한 부여와 구분됩니다.

**ID**: 1277.09c2Organizational.4 - 09.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 컴퓨터는 '보안 옵션 - 사용자 계정 컨트롤' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F492a29ed-d143-4f03-b6a4-705ce081b463) |Windows 컴퓨터에는 관리자 모드, 권한 상승 프롬프트 동작, 파일 및 레지스트리 쓰기 오류 가상화에 대한 그룹 정책 설정이 '보안 옵션 - 사용자 계정 컨트롤' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsUserAccountControl_AINE.json) |

### <a name="the-organization-identifies-duties-that-require-separation-and-defines-information-system-access-authorizations-to-support-separation-of-duties-and-incompatible-duties-are-segregated-across-multiple-users-to-minimize-the-opportunity-for-misuse-or-fraud"></a>조직은 분리가 필요한 업무를 식별하고, 업무 분리를 지원하기 위해 정보 시스템 액세스 권한을 정의합니다. 호환되지 않는 업무는 오용 또는 사기 행위의 기회를 최소화하기 위해 여러 사용자 간에 분리됩니다.

**ID**: 1278.09c2Organizational.56 - 09.c **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[사용자 지정 구독 소유자 역할이 없어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |이 정책은 사용자 지정 구독 소유자 역할이 없도록 합니다. |감사, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="controls-against-malicious-code"></a>악성 코드에 대한 제어

### <a name="anti-virus-and-anti-spyware-are-installed-operating-and-updated-on-all-end-user-devices-to-conduct-periodic-scans-of-the-systems-to-identify-and-remove-unauthorized-software-server-environments-for-which-the-server-software-developer-specifically-recommends-not-installing-host-based-anti-virus-and-anti-spyware-software-may-address-the-requirement-via-a-network-based-malware-detection-nbmd-solution"></a>바이러스 백신 및 스파이웨어 방지 프로그램은 모든 최종 사용자 디바이스에 설치, 작동 및 업데이트되어 권한 없는 소프트웨어를 식별하고 제거하기 위해 시스템을 정기적으로 검사합니다. 서버 소프트웨어 개발자가 호스트 기반 바이러스 백신 및 스파이웨어 방지 소프트웨어를 설치하지 않도록 특별히 추천하는 서버 환경은 NBMD(네트워크 기반 맬웨어 검색) 솔루션을 통해 요구 사항을 처리할 수 있습니다.

**ID**: 0201.09j1Organizational.124 - 09.j **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |애플리케이션 제어를 활성화하여 머신에서 실행 중인 알려진 안전한 애플리케이션 목록을 정의하고, 다른 애플리케이션이 실행될 때 경고합니다. 이렇게 하면 맬웨어로부터 머신을 강화할 수 있습니다. 규칙을 구성하고 유지 관리하는 프로세스를 간소화하기 위해 Security Center는 기계 학습을 사용하여 각 머신에서 실행 중인 애플리케이션을 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Windows Server용 기본 Microsoft IaaSAntimalware 확장 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2835b622-407b-4114-9198-6f7064cbe0dc) |이 정책은 VM이 맬웨어 방지 프로그램 확장으로 구성되지 않은 경우 기본 구성으로 Microsoft IaaSAntimalware 확장을 배포합니다. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |
|[가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |가상 머신 확장 집합에서 엔드포인트 보호 솔루션의 존재 및 상태를 감사하여 위협 및 취약성으로부터 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[보호 서명을 자동으로 업데이트하려면 Azure용 Microsoft Antimalware를 구성해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |이 정책은 Microsoft Antimalware 보호 서명의 자동 업데이트로 구성되지 않은 Windows 가상 머신을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[Azure Security Center에서 누락된 Endpoint Protection 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Endpoint Protection 에이전트가 설치되어 있지 않은 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[시스템 업데이트를 머신에 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |서버의 누락된 보안 시스템 업데이트는 Azure Security Center에서 권장 사항으로 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

## <a name="back-up"></a>백업

### <a name="backup-copies-of-information-and-software-are-made-and-tests-of-the-media-and-restoration-procedures-are-regularly-performed-at-appropriate-intervals"></a>정보 및 소프트웨어의 백업 복사본을 만들고, 미디어 및 복원 절차를 적절한 간격으로 정기적으로 테스트합니다.

**ID**: 1616.09l1Organizational.16 - 09.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure SQL Database에 대해 장기 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |이 정책은 장기 지역 중복 백업이 사용하도록 설정되지 않은 모든 Azure SQL Database를 감사합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="a-formal-definition-of-the-level-of-backup-required-for-each-system-is-defined-and-documented-including-how-each-system-will-be-restored-the-scope-of-data-to-be-imaged-frequency-of-imaging-and-duration-of-retention-based-on-relevant-contractual-legal-regulatory-and-business-requirements"></a>각 시스템에 필요한 백업 수준에 대한 공식적인 정의를 정의하고, 관련 계약, 법률, 규정 및 비즈니스 요구 사항에 따라 각 시스템을 복원하는 방법, 이미징되는 데이터의 범위, 이미징 빈도 및 보존 기간을 포함하여 문서화합니다.

**ID**: 1617.09l1Organizational.23 - 09.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL을 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="the-backups-are-stored-in-a-physically-secure-remote-location-at-a-sufficient-distance-to-make-them-reasonably-immune-from-damage-to-data-at-the-primary-site-and-reasonable-physical-and-environmental-controls-are-in-place-to-ensure-their-protection-at-the-remote-location"></a>백업은 물리적으로 안전한 원격 위치에 저장됩니다. 이 위치는 기본 사이트의 데이터 손상으로부터 적절하게 영향을 받지 않도록 충분한 거리를 두고 원격 위치에서 보호할 수 있도록 적절한 물리적 및 환경 제어를 갖추고 있습니다.

**ID**: 1618.09l1Organizational.45 - 09.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL을 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="inventory-records-for-the-backup-copies-including-content-and-current-location-are-maintained"></a>콘텐츠 및 현재 위치를 포함하여 백업 복사본에 대한 인벤토리 레코드는 유지 관리됩니다.

**ID**: 1619.09l1Organizational.7 - 09.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB를 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="when-the-backup-service-is-delivered-by-the-third-party-the-service-level-agreement-includes-the-detailed-protections-to-control-confidentiality-integrity-and-availability-of-the-backup-information"></a>타사에서 백업 서비스를 제공하는 경우 서비스 수준 계약에는 백업 정보의 기밀성, 무결성 및 가용성을 제어하는 세부 보호 기능이 포함됩니다.

**ID**: 1620.09l1Organizational.8 - 09.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Azure Backup을 사용하도록 설정하여 Azure Virtual Machines를 보호하세요. Azure Backup은 Azure를 위한 안전하고 경제적인 데이터 보호 솔루션입니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="automated-tools-are-used-to-track-all-backups"></a>자동화된 도구는 모든 백업을 추적하는 데 사용됩니다.

**ID**: 1621.09l2Organizational.1 - 09.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure SQL Database에 대해 장기 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |이 정책은 장기 지역 중복 백업이 사용하도록 설정되지 않은 모든 Azure SQL Database를 감사합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="the-integrity-and-security-of-the-backup-copies-are-maintained-to-ensure-future-availability-and-any-potential-accessibility-problems-with-the-backup-copies-are-identified-and-mitigated-in-the-event-of-an-area-wide-disaster"></a>백업 복사본의 무결성 및 보안은 향후 가용성을 보장하기 위해 유지 관리되며, 지역 전체 재해가 발생하는 경우 백업 복사본의 잠재적 접근성 문제가 식별되고 완화됩니다.

**ID**: 1622.09l2Organizational.23 - 09.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL을 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="covered-information-is-backed-up-in-an-encrypted-format-to-ensure-confidentiality"></a>적용 정보는 기밀성을 보장하기 위해 암호화된 형식으로 백업됩니다.

**ID**: 1623.09l2Organizational.4 - 09.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL을 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-performs-incremental-or-differential-backups-daily-and-full-backups-weekly-to-separate-media"></a>조직은 매일 증분 또는 차등 백업을 수행하고, 매주 전체 백업을 별도의 미디어에 수행합니다.

**ID**: 1624.09l3Organizational.12 - 09.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB를 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="three-3-generations-of-backups-full-plus-all-related-incremental-or-differential-backups-are-stored-off-site-and-both-on-site-and-off-site-backups-are-logged-with-name-date-time-and-action"></a>3세대 백업(전체 백업 및 관련된 모든 증분 또는 차등 백업)은 오프사이트에 저장되고, 온사이트 백업 및 오프사이트 백업은 모두 이름, 날짜, 시간 및 작업과 함께 기록됩니다.

**ID**: 1625.09l3Organizational.34 - 09.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Azure Backup을 사용하도록 설정하여 Azure Virtual Machines를 보호하세요. Azure Backup은 Azure를 위한 안전하고 경제적인 데이터 보호 솔루션입니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="the-organization-ensures-a-current-retrievable-copy-of-covered-information-is-available-before-movement-of-servers"></a>조직은 서버를 이동하기 전에 적용 정보에 대한 검색 가능한 최신 복사본을 사용할 수 있도록 합니다.

**ID**: 1626.09l3Organizational.5 - 09.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL을 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-tests-backup-information-following-each-backup-to-verify-media-reliability-and-information-integrity-and-at-least-annually-thereafter"></a>조직은 각 백업 후에 백업 정보를 테스트하여 미디어 안정성 및 정보 무결성을 확인하고, 그 이후 적어도 매년 확인합니다.

**ID**: 1627.09l3Organizational.6 - 09.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB를 사용하면 데이터베이스 서버에 대한 중복성 옵션을 선택할 수 있습니다. 서버가 호스트되는 지역 내에 저장될 뿐만 아니라 지역 장애 발생 시 복구 옵션을 제공하기 위해 쌍을 이루는 지역에도 복제되는 데이터가 있는 지역 중복 백업 스토리지로 설정할 수 있습니다. 백업을 위한 지역 중복 스토리지 구성은 서버 생성 중에만 허용됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="workforce-members-roles-and-responsibilities-in-the-data-backup-process-are-identified-and-communicated-to-the-workforce-in-particular-bring-your-own-device-byod-users-are-required-to-perform-backups-of-organizational-andor-client-data-on-their-devices"></a>데이터 백업 프로세스에서 직원 멤버의 역할과 책임을 식별하여 직원에게 전달합니다. 특히 BYOD(Bring Your Own Device) 사용자는 자신의 디바이스에서 조직 및/또는 클라이언트 데이터의 백업을 수행해야 합니다.

**ID**: 1699.09l1Organizational.10 - 09.l **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Azure Backup을 사용하도록 설정하여 Azure Virtual Machines를 보호하세요. Azure Backup은 Azure를 위한 안전하고 경제적인 데이터 보호 솔루션입니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="network-controls"></a>네트워크 제어

### <a name="the-organization-monitors-for-all-authorized-and-unauthorized-wireless-access-to-the-information-system-and-prohibits-installation-of-wireless-access-points-waps-unless-explicitly-authorized-in-writing-by-the-cio-or-hisher-designated-representative"></a>조직은 정보 시스템에 대한 모든 승인된 무선 액세스 및 무단 무선 액세스를 모니터링하고, CIO 또는 해당 지정 담당자가 명시적으로 서면으로 권한을 부여하지 않는 한 WAP(무선 액세스 지점)의 설치를 금지합니다.

**ID**: 0858.09m1Organizational.4 - 09.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신과 연결된 네트워크 보안 그룹에서 모든 네트워크 포트를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center에서 네트워크 보안 그룹의 인바운드 규칙 중 일부가 너무 관대하다는 사실을 식별했습니다. 인바운드 규칙에서 '모두' 또는 '인터넷' 범위에서 들어오는 액세스를 허용해서는 안 됩니다. 그러면 리소스가 공격자의 표적이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |가능한 네트워크 JIT(Just In Time) 액세스는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Windows 컴퓨터는 'Windows 방화벽 속성' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35d9882c-993d-44e6-87d2-db66ce21b636) |Windows 컴퓨터에는 방화벽 상태, 연결, 규칙 관리 및 알림에 대한 그룹 정책 설정이 'Windows 방화벽 속성' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsFirewallProperties_AINE.json) |

### <a name="the-organization-ensures-the-security-of-information-in-networks-availability-of-network-services-and-information-services-using-the-network-and-the-protection-of-connected-services-from-unauthorized-access"></a>조직은 네트워크의 정보 보안, 네트워크를 사용하는 네트워크 서비스/정보 서비스의 가용성 및 무단 액세스로부터의 연결된 서비스 보호를 보장합니다.

**ID**: 0859.09m1Organizational.78 - 09.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[인터넷 연결 가상 머신에 적응형 네트워크 강화 권장 사항을 적용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center는 인터넷 연결 가상 머신의 트래픽 패턴을 분석하고 잠재적 공격 노출 영역을 줄이는 네트워크 보안 그룹 규칙 권장 사항을 제공합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="the-organization-formally-manages-equipment-on-the-network-including-equipment-in-user-areas"></a>조직은 사용자 영역의 장비를 포함하여 네트워크의 장비를 공식적으로 관리합니다.

**ID**: 0860.09m1Organizational.9 - 09.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[네트워크 보안 그룹에 대한 진단 설정 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9c29499-c1d1-4195-99bd-2ec9e3a9dc89) |이 정책은 네트워크 보안 그룹에 진단 설정을 자동으로 배포합니다. 이름이 '{storagePrefixParameter} {NSGLocation}' 인 스토리지 계정이 자동으로 생성됩니다. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForNSG_Deploy.json) |

### <a name="to-identify-and-authenticate-devices-on-local-andor-wide-area-networks-including-wireless-networks-nbspthe-information-system-uses-either-a-i-shared-known-information-solution-or-ii-an-organizational-authentication-solution-the-exact-selection-and-strength-of-which-is-dependent-on-the-security-categorization-of-the-information-system"></a>무선 네트워크를 포함하여 근거리 및/또는 광역 네트워크에서 디바이스를 식별하고 인증하기 위해 &nbsp;정보 시스템은 (i) 알려진 정보 공유 솔루션 또는 (ii) 조직 인증 솔루션을 사용하고, 정보 시스템의 보안 분류에 따라 결정되는 정확한 선택 및 강도를 사용합니다.

**ID**: 0861.09m2Organizational.67 - 09.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 App Service를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Windows 컴퓨터는 '보안 옵션 - 네트워크 액세스' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ff60f98-7fa4-410a-9f7f-0b00f5afdbdd) |Windows 컴퓨터에는 익명 사용자, 로컬 계정 및 레지스트리에 대한 원격 액세스에 대한 액세스를 포함하는 그룹 정책 설정이 '보안 옵션 - 네트워크 액세스' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkAccess_AINE.json) |

### <a name="the-organization-ensures-information-systems-protect-the-confidentiality-and-integrity-of-transmitted-information-including-during-preparation-for-transmission-and-during-reception"></a>조직은 정보 시스템에서 전송 준비 중 및 수신 중을 포함하여 전송된 정보의 기밀성과 무결성을 보호하도록 보장합니다.

**ID**: 0862.09m2Organizational.8 - 09.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 SQL Server를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |

### <a name="the-organization-builds-a-firewall-configuration-that-restricts-connections-between-un-trusted-networks-and-any-system-components-in-the-covered-information-environment-and-any-changes-to-the-firewall-configuration-are-updated-in-the-network-diagram"></a>조직은 신뢰할 수 없는 네트워크와 적용 정보 환경의 모든 시스템 구성 요소 간의 연결을 제한하는 방화벽 구성을 빌드합니다. 방화벽 구성에 대한 모든 변경 내용은 네트워크 다이어그램에서 업데이트됩니다.

**ID**: 0863.09m2Organizational.910 - 09.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[이벤트 허브는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 이벤트 허브를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

### <a name="usage-restrictions-and-implementation-guidance-are-formally-defined-for-voip-including-the-authorization-and-monitoring-of-the-service"></a>VoIP에 대한 사용 제한 및 구현 지침은 서비스의 권한 부여 및 모니터링을 포함하여 공식적으로 정의됩니다.

**ID**: 0864.09m2Organizational.12 - 09.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Cosmos DB는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Cosmos DB를 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

### <a name="the-organization-i-authorizes-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-through-the-use-of-interconnection-security-agreements-or-other-formal-agreement-ii-documents-each-connection-the-interface-characteristics-security-requirements-and-the-nature-of-the-information-communicated-iii-employs-a-deny-all-permit-by-exception-policy-for-allowing-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-and-iv-applies-a-default-deny-rule-that-drops-all-traffic-via-host-based-firewalls-or-port-filtering-tools-on-its-endpoints-workstations-servers-etc-except-those-services-and-ports-that-are-explicitly-allowed"></a>조직은 (i) 상호 연결 보안 규약 또는 기타 공식 규약을 사용하여 정보 시스템에서 조직 외부의 다른 정보 시스템으로의 연결에 대한 권한을 부여합니다. (ii) 각 연결, 인터페이스 특성, 보안 요구 사항 및 전달되는 정보의 특성을 문서화합니다. (iii) 정보 시스템에서 조직 외부의 다른 정보 시스템으로의 연결을 허용하기 위해 모두 거부, 예외 기준 허용 정책을 사용합니다. (iv) 명시적으로 허용되는 서비스와 포트를 제외하고 해당 엔드포인트(워크스테이션, 서버 등)에서 호스트 기반 방화벽 또는 포트 필터링 도구를 통해 모든 트래픽을 삭제하는 기본 거부 규칙을 적용합니다.

**ID**: 0865.09m2Organizational.13 - 09.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Key Vault를 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |

### <a name="the-organization-describes-the-groups-roles-and-responsibilities-for-the-logical-management-of-network-components-and-ensures-coordination-of-and-consistency-in-the-elements-of-the-network-infrastructure"></a>조직은 네트워크 구성 요소의 논리적 관리를 위한 그룹, 역할 및 책임을 설명하고, 네트워크 인프라 요소의 조정 및 일관성을 보장합니다.

**ID**: 0866.09m3Organizational.1516 - 09.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[스토리지 계정은 네트워크 액세스를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |스토리지 계정에 대한 네트워크 액세스가 제한되어야 합니다. 허용되는 네트워크의 애플리케이션만 스토리지 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용하기 위해 특정 Azure 가상 네트워크 또는 공용 인터넷 IP 주소 범위의 트래픽에 대한 액세스 권한을 부여할 수 있습니다. |감사, 거부, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="wireless-access-points-are-placed-in-secure-areas-and-shut-down-when-not-in-use-eg-nights-weekends"></a>무선 액세스 지점은 보안 영역에 배치되고, 사용하지 않을 때(예: 야간, 주말) 종료됩니다.

**ID**: 0867.09m3Organizational.17 - 09.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[스토리지 계정은 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 스토리지 계정을 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |

### <a name="the-organization-builds-a-firewall-configuration-to-restrict-inbound-and-outbound-traffic-to-that-which-is-necessary-for-the-covered-data-environment"></a>조직은 인바운드 및 아웃바운드 트래픽을 해당 데이터 환경에 필요한 트래픽으로 제한하는 방화벽 구성을 빌드합니다.

**ID**: 0868.09m3Organizational.18 - 09.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Container Registry를 감사합니다. |감사, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="the-router-configuration-files-are-secured-and-synchronized"></a>라우터 구성 파일을 보호하고 동기화합니다.

**ID**: 0869.09m3Organizational.19 - 09.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Container Registry를 감사합니다. |감사, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="access-to-all-proxies-is-denied-except-for-those-hosts-ports-and-services-that-are-explicitly-required"></a>명시적으로 필요한 호스트, 포트 및 서비스를 제외하고 모든 프록시에 대한 액세스는 거부됩니다.

**ID**: 0870.09m3Organizational.20 - 09.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Container Registry를 감사합니다. |감사, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="authoritative-dns-servers-are-segregated-into-internal-and-external-roles"></a>권한 있는 DNS 서버는 내부 및 외부 역할로 분리됩니다.

**ID**: 0871.09m3Organizational.22 - 09.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 모든 Container Registry를 감사합니다. |감사, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="security-of-network-services"></a>Network Services 보안

### <a name="agreed-services-provided-by-a-network-service-providermanager-are-formally-managed-and-monitored-to-ensure-they-are-provided-securely"></a>네트워크 서비스 공급자/관리자가 제공하는 합의된 서비스는 안전하게 제공되도록 공식적으로 관리 및 모니터링됩니다.

**ID**: 0835.09n1Organizational.1 - 09.n **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[가상 머신을 새 Azure Resource Manager 리소스로 마이그레이션해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |가상 머신에 새 Azure Resource Manager를 사용하여 더 강력한 액세스 제어(RBAC), 더 나은 감사, Azure Resource Manager 기반 배포 및 관리, 관리 ID 액세스, 비밀을 위해 키 자격 증명 모음에 액세스, Azure AD 기반 인증, 보다 쉬운 보안 관리를 위한 태그 및 리소스 그룹 지원과 같은 보안 기능 향상을 제공합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="the-organization-formally-authorizes-and-documents-the-characteristics-of-each-connection-from-an-information-system-to-other-information-systems-outside-the-organization"></a>조직은 정보 시스템에서 조직 외부의 다른 정보 시스템으로의 각 연결에 대한 특성을 공식적으로 권한 부여하고 문서화합니다.

**ID**: 0836.09.n2Organizational.1 - 09.n **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Linux 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="formal-agreements-with-external-information-system-providers-include-specific-obligations-for-security-and-privacy"></a>외부 정보 시스템 공급자와의 공식 규약에는 보안 및 개인 정보에 대한 특정 의무가 포함됩니다.

**ID**: 0837.09.n2Organizational.2 - 09.n **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. 시나리오 수준 모니터링을 사용하면 종단 간 네트워크 수준 보기에서 문제를 진단할 수 있습니다. 가상 네트워크가 있는 모든 지역에서 Network Watcher 리소스 그룹을 만들어야 합니다. 특정 지역에서 Network Watcher 리소스 그룹을 사용할 수 없는 경우 경고가 활성화됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-reviews-and-updates-the-interconnection-security-agreements-on-an-ongoing-basis-verifying-enforcement-of-security-requirements"></a>조직은 보안 요구 사항을 적용하는 것을 확인하면서 상호 연결 보안 규약을 지속적으로 검토하고 업데이트합니다.

**ID**: 0885.09n2Organizational.3 - 09.n **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Linux 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="the-organization-employs-and-documents-in-a-formal-agreement-or-other-document-either-i-allow-all-deny-by-exception-or-ii-deny-all-permit-by-exception-preferred-policy-for-allowing-specific-information-systems-to-connect-to-external-information-systems"></a>조직은 특정 정보 시스템에서 외부 정보 시스템에 연결할 수 있도록 허용하는 i) 모두 허용, 예외 기준 거부 또는 ii) 모두 거부, 예외 기준 허용(기본 설정) 정책을 공식 규약 또는 기타 문서에서 사용하고 문서화합니다.

**ID**: 0886.09n2Organizational.4 - 09.n **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. 시나리오 수준 모니터링을 사용하면 종단 간 네트워크 수준 보기에서 문제를 진단할 수 있습니다. 가상 네트워크가 있는 모든 지역에서 Network Watcher 리소스 그룹을 만들어야 합니다. 특정 지역에서 Network Watcher 리소스 그룹을 사용할 수 없는 경우 경고가 활성화됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-requires-externaloutsourced-service-providers-to-identify-the-specific-functions-ports-and-protocols-used-in-the-provision-of-the-externaloutsourced-services"></a>조직은 외부/아웃소싱 서비스 공급자에서 외부/아웃소싱 서비스를 프로비저닝하는 데 사용되는 특정 기능, 포트 및 프로토콜을 식별해야 합니다.

**ID**: 0887.09n2Organizational.5 - 09.n **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center에서는 Microsoft Dependency Agent를 사용하여 Azure 가상 머신에서 네트워크 트래픽 데이터를 수집함으로써 네트워크 맵의 트래픽 시각화, 네트워크 강화 권장 사항 및 특정 네트워크 위협과 같은 고급 네트워크 보호 기능을 활성화합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |

### <a name="the-contract-with-the-externaloutsourced-service-provider-includes-the-specification-that-the-service-provider-is-responsible-for-the-protection-of-covered-information-shared"></a>외부/아웃소싱 서비스 공급자와의 계약에는 서비스 공급자가 공유된 적용 정보를 보호해야 한다는 사양이 포함됩니다.

**ID**: 0888.09n2Organizational.6 - 09.n **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. 시나리오 수준 모니터링을 사용하면 종단 간 네트워크 수준 보기에서 문제를 진단할 수 있습니다. 가상 네트워크가 있는 모든 지역에서 Network Watcher 리소스 그룹을 만들어야 합니다. 특정 지역에서 Network Watcher 리소스 그룹을 사용할 수 없는 경우 경고가 활성화됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="management-of-removable-media"></a>이동식 미디어 관리

### <a name="the-organization-based-on-the-data-classification-level-registers-media-including-laptops-prior-to-use-places-reasonable-restrictions-on-how-such-media-be-used-and-provides-an-appropriate-level-of-physical-and-logical-protection-including-encryption-for-media-containing-covered-information-until-properly-destroyed-or-sanitized"></a>조직에서는 데이터 분류 수준에 따라 사용하기 전에 미디어(랩톱 포함)를 등록하고, 이러한 미디어를 사용하는 방법을 적절히 제한하며, 적절히 제거되거나 삭제될 때까지 적절한 수준의 물리적 및 논리적 보호(암호화 포함)를 적용된 정보가 포함된 미디어에 제공합니다.

**ID**: 0301.09o1Organizational.123 - 09.o **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |저장 데이터를 보호하고 규정 준수 요구 사항을 충족하려면 투명한 데이터 암호화를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="the-organization-protects-and-controls-media-containing-sensitive-information-during-transport-outside-of-controlled-areas"></a>조직은 제어되는 영역 외부에서 전송하는 동안 중요한 정보가 포함된 미디어를 보호하고 제어합니다.

**ID**: 0302.09o2Organizational.1 - 09.o **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신은 Compute 및 Storage 리소스 간에 임시 디스크, 캐시 및 데이터 흐름을 암호화해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |디스크 암호화를 사용하도록 설정되지 않은 가상 머신은 추천 사항으로 Azure Security Center에서 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="digital-and-non-digital-media-requiring-restricted-use-and-the-specific-safeguards-used-to-restrict-their-use-are-identified"></a>제한된 사용이 필요한 디지털 및 비 디지털 미디어와 해당 사용을 제한하는 데 사용되는 특정 보호 기능이 식별됩니다.

**ID**: 0303.09o2Organizational.2 - 09.o **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[연결되지 않은 디스크는 암호화되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |이 정책은 암호화를 사용하지 않고 연결되지 않은 모든 디스크를 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="the-organization-restricts-the-use-of-writable-removable-media-and-personally-owned-removable-media-in-organizational-systems"></a>조직은 조직 시스템에서 쓰기 가능한 이동식 미디어 및 개인 소유 이동식 미디어의 사용을 제한합니다.

**ID**: 0304.09o3Organizational.1 - 09.o **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Data Lake Store 계정에서 암호화 필요](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |이 정책은 모든 Data Lake Store 계정에서 암호화를 사용할 수 있도록 합니다. |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
|[SQL Managed Instance는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |자체 키를 사용하여 TDE(투명한 데이터 암호화)를 구현하면 TDE 보호기에 대한 투명성과 제어력이 향상되고, HSM 지원 외부 서비스를 통한 보안이 강화되며, 업무 분리 프로모션을 제공합니다. 이 권장 사항은 관련 규정 준수 요구 사항이 있는 조직에 적용됩니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL 서버는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |자체 키를 사용하여 TDE(투명한 데이터 암호화)를 구현하면 TDE 보호기에 대한 투명성과 제어력이 향상되고, HSM 지원 외부 서비스를 통해 보안이 강화되며, 업무 분리 프로모션을 제공합니다. 이 권장 사항은 관련 규정 준수 요구 사항이 있는 조직에 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="information-exchange-policies-and-procedures"></a>정보 교환 정책 및 절차

### <a name="cloud-service-providers-use-an-industry-recognized-virtualization-platform-and-standard-virtualization-formats-eg-open-virtualization-format-ovf-to-help-ensure-interoperability-and-has-documented-custom-changes-made-to-any-hypervisor-in-use-and-all-solution-specific-virtualization-hooks-available-for-customer-review"></a>클라우드 서비스 공급자는 업계에서 인정된 가상화 플랫폼과 표준 가상화 형식(예: OVF(Open Virtualization Format))을 사용하여 상호 운용성을 보장하고, 사용 중인 하이퍼바이저와 고객 검토에 사용할 수 있는 모든 솔루션 관련 가상화 후크에 대한 사용자 지정 변경 내용을 문서화했습니다.

**ID**: 0662.09sCSPOrganizational.2 - 09.s **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[웹앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |클라이언트 인증서는 앱이 들어오는 요청에 대한 인증서를 요청하도록 허용합니다. 유효한 인증서가 있는 클라이언트만 앱에 연결할 수 있습니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-organization-formally-addresses-multiple-safeguards-before-allowing-the-use-of-information-systems-for-information-exchange"></a>조직은 여러 보호 기능을 공식적으로 처리한 후에 정보 시스템을 정보 교환에 사용하도록 허용합니다.

**ID**: 0901.09s1Organizational.1 - 09.s **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용해서는 안 됩니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |CORS(교차 원본 리소스 공유)는 웹 애플리케이션에 액세스하는 모든 도메인을 허용하지 않아야 합니다. 필요한 도메인만 웹앱과 상호 작용할 수 있도록 허용합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="remote-external-access-to-the-organizations-information-assets-and-access-to-external-information-assets-for-which-the-organization-has-no-control-is-based-on-clearly-defined-terms-and-conditions"></a>조직의 정보 자산에 대한 원격(외부) 액세스 및 외부 정보 자산에 대한 액세스(조직에서 제어할 수 없음)는 명확하게 정의된 사용 약관을 기반으로 합니다.

**ID**: 0902.09s2Organizational.13 - 09.s **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS에서 모든 리소스가 함수 앱에 액세스하도록 허용해서는 안 됩니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |CORS(교차 원본 리소스 공유)는 함수 앱에 액세스하는 모든 도메인을 허용하지 않아야 합니다. 필요한 도메인만 함수 앱과 상호 작용할 수 있도록 허용합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="the-organization-establishes-terms-and-conditions-consistent-with-any-trust-relationship-established-with-other-organizations-owning-operating-andor-maintaining-external-information-systems-allowing-authorized-individuals-to-i-access-the-information-system-from-external-information-systems-and-ii-process-store-or-transmit-organization-controlled-information-using-external-information-systems"></a>조직은 외부 정보 시스템을 소유, 운영 및/또는 유지 관리하는 다른 조직과 설정된 신뢰 관계와 일치하는 사용 약관을 설정하여 권한 있는 개인이 (i) 외부 정보 시스템에서 정보 시스템에 액세스하고, (ii) 외부 정보 시스템을 사용하여 조직에서 제어하는 정보를 처리, 저장 또는 전송할 수 있도록 허용합니다.

**ID**: 0911.09s1Organizational.2 - 09.s **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS에서 모든 리소스가 API 앱에 액세스하도록 허용해서는 안 됩니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |CORS(교차 원본 리소스 공유)는 API 앱에 액세스하는 모든 도메인을 허용하지 않아야 합니다. 필요한 도메인만 API 앱과 상호 작용할 수 있도록 허용합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |

### <a name="cryptography-is-used-to-protect-the-confidentiality-and-integrity-of-remote-access-sessions-to-the-internal-network-and-to-external-systems"></a>암호화는 내부 네트워크 및 외부 시스템에 대한 원격 액세스 세션의 기밀성과 무결성을 보호하는 데 사용됩니다.

**ID**: 0912.09s1Organizational.4 - 09.s **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |원격 디버깅에 웹 애플리케이션에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="strong-cryptography-protocols-are-used-to-safeguard-covered-information-during-transmission-over-less-trusted--open-public-networks"></a>강력한 암호화 프로토콜은 신뢰 수준이 낮거나 개방형 공용 네트워크를 통해 전송되는 동안 적용 정보를 보호하는 데 사용됩니다.

**ID**: 0913.09s1Organizational.5 - 09.s **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[함수 앱에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |원격 디버깅에 함수 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-ensures-that-communication-protection-requirements-including-the-security-of-exchanges-of-information-is-the-subject-of-policy-development-and-compliance-audits"></a>조직은 정보 교환의 보안을 포함한 통신 보호 요구 사항이 정책 개발 및 규정 준수 감사의 대상이 되도록 보장합니다.

**ID**: 0914.09s1Organizational.6 - 09.s **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API Apps에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |원격 디버깅에 API 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-limits-the-use-of-organization-controlled-portable-storage-media-by-authorized-individuals-on-external-information-systems"></a>조직에서는 외부 정보 시스템에서 권한 있는 개인이 조직에서 제어하는 휴대용 스토리지 미디어의 사용을 제한합니다.

**ID**: 0915.09s2Organizational.2 - 09.s **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[웹앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |클라이언트 인증서는 앱이 들어오는 요청에 대한 인증서를 요청하도록 허용합니다. 유효한 인증서가 있는 클라이언트만 앱에 연결할 수 있습니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-information-system-prohibits-remote-activation-of-collaborative-computing-devices-and-provides-an-explicit-indication-of-use-to-users-physically-present-at-the-devices"></a>정보 시스템은 협업 컴퓨팅 디바이스의 원격 활성화를 금지하고, 실제로 디바이스에 있는 사용자에게 사용을 명시적으로 표시합니다.

**ID**: 0916.09s2Organizational.4 - 09.s **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용해서는 안 됩니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |CORS(교차 원본 리소스 공유)는 웹 애플리케이션에 액세스하는 모든 도메인을 허용하지 않아야 합니다. 필요한 도메인만 웹앱과 상호 작용할 수 있도록 허용합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="cloud-service-providers-use-secure-eg-non-clear-text-and-authenticated-standardized-network-protocols-for-the-import-and-export-of-data-and-to-manage-the-service-and-make-available-a-document-to-consumers-tenants-detailing-the-relevant-interoperability-and-portability-standards-that-are-involved"></a>클라우드 서비스 공급자는 데이터 가져오기/내보내기 및 서비스 관리를 위해 표준화된 보안(예: 비 일반 텍스트 및 인증) 네트워크 프로토콜을 사용하고, 관련 상호 운용성 및 이식성 표준을 자세히 설명하는 문서를 소비자(테넌트)에게 제공합니다.

**ID**: 0960.09sCSPOrganizational.1 - 09.s **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS에서 모든 리소스가 함수 앱에 액세스하도록 허용해서는 안 됩니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |CORS(교차 원본 리소스 공유)는 함수 앱에 액세스하는 모든 도메인을 허용하지 않아야 합니다. 필요한 도메인만 함수 앱과 상호 작용할 수 있도록 허용합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="personnel-are-appropriately-trained-on-leading-principles-and-practices-for-all-types-of-information-exchange-oral-paper-and-electronic"></a>직원은 모든 유형의 정보 교환(구두, 종이 및 전자)에 대한 주요 원칙 및 사례를 적절하게 학습합니다.

**ID**: 1325.09s1Organizational.3 - 09.s **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[함수 앱에 대해 원격 디버깅을 해제해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |원격 디버깅에 함수 앱에서 열리는 인바운드 포트가 필요합니다. 원격 디버깅을 해제해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

## <a name="on-line-transactions"></a>온라인 거래

### <a name="data-involved-in-electronic-commerce-and-online-transactions-is-checked-to-determine-if-it-contains-covered-information"></a>전자 상거래 및 온라인 거래와 관련된 데이터를 확인하여 적용 정보가 포함되어 있는지 확인합니다.

**ID**: 0943.09y1Organizational.1 - 09.y **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[스토리지 계정에 보안 전송을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |스토리지 계정의 보안 전송 요구 사항을 감사합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="protocols-used-to-communicate-between-all-involved-parties-are-secured-using-cryptographic-techniques-eg-ssl"></a>모든 관련 당사자 간의 통신에 사용되는 프로토콜은 암호화 기술(예: SSL)을 통해 보호됩니다.

**ID**: 0945.09y1Organizational.3 - 09.y **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[신뢰할 수 있는 루트에 지정한 인증서가 포함되지 않은 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F934345e1-4dfb-4c70-90d7-41990dc9608b) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. 머신의 신뢰할 수 있는 루트 인증서 저장소(Cert:\LocalMachine\Root)에 정책 매개 변수에 나열된 인증서 중 하나 이상이 포함되어 있지 않으면 머신은 비규격입니다. |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsCertificateInTrustedRoot_AINE.json) |

### <a name="the-organization-requires-the-use-of-encryption-between-and-the-use-of-electronic-signatures-by-each-of-the-parties-involved-in-the-transaction"></a>조직은 거래와 관련된 각 당사자 간에 암호화를 사용하고 각 당사자의 전자 서명을 사용해야 합니다.

**ID**: 0946.09y2Organizational.14 - 09.y **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Cache for Redis에 보안 연결만 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |SSL을 통해 설정된 Azure Cache for Redis 연결만 감사 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

### <a name="the-organization-ensures-the-storage-of-the-transaction-details-are-located-outside-of-any-publicly-accessible-environments-eg-on-a-storage-platform-existing-on-the-organizations-intranet-and-not-retained-and-exposed-on-a-storage-medium-directly-accessible-from-the-internet"></a>조직은 거래 세부 정보 스토리지가 공개적으로 액세스할 수 있는 환경 외부(예: 조직의 인트라넷에 있는 스토리지 플랫폼)에 있고 인터넷에서 직접 액세스할 수 있는 스토리지 매체에 보관 및 공개되지 않도록 합니다.

**ID**: 0947.09y2Organizational.2 - 09.y **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL은 SSL(Secure Sockets Layer)을 사용하여 Azure Database for PostgreSQL 서버를 클라이언트 애플리케이션에 연결하는 것을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="where-a-trusted-authority-is-used-eg-for-the-purposes-of-issuing-and-maintaining-digital-signatures-andor-digital-certificates-security-is-integrated-and-embedded-throughout-the-entire-end-to-end-certificatesignature-management-process"></a>신뢰할 수 있는 인증 기관을 사용하는 경우(예: 디지털 서명 및/또는 디지털 인증서를 발급 및 유지 관리하기 위해) 보안은 전체 엔드투엔드 인증서/서명 관리 프로세스에서 통합되고 포함됩니다.

**ID**: 0948.09y2Organizational.3 - 09.y **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL용 Azure Database는 SSL(Secure Sockets Layer)을 사용한 MySQL용 Azure Database 서버와 클라이언트 애플리케이션 간 연결을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-protocols-used-for-communications-are-enhanced-to-address-any-new-vulnerability-and-the-updated-versions-of-the-protocols-are-adopted-as-soon-as-possible"></a>통신에 사용되는 프로토콜은 새로운 취약점을 해결하기 위해 향상되었으며 업데이트된 버전의 프로토콜이 최대한 빨리 채택됩니다.

**ID**: 0949.09y2Organizational.5 - 09.y **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[함수 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[API 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[함수 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[웹앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[웹 애플리케이션은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="control-of-operational-software"></a>운영 소프트웨어 제어

### <a name="only-authorized-administrators-are-allowed-to-implement-approved-upgrades-to-software-applications-and-program-libraries-based-on-business-requirements-and-the-security-implications-of-the-release"></a>권한 있는 관리자만 비즈니스 요구 사항 및 릴리스의 보안 영향에 따라 소프트웨어, 애플리케이션 및 프로그램 라이브러리에 대한 승인된 업그레이드를 구현할 수 있습니다.

**ID**: 0605.10h1System.12 - 10.h **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |구성된 기준을 충족하지 않는 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Windows 컴퓨터는 '보안 옵션 - 감사' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33936777-f2ac-45aa-82ec-07958ec9ade4) |Windows 컴퓨터에는 감사 정책 하위 범주를 강제로 적용하고 보안 감사를 기록할 수 없는 경우 종료하는 그룹 정책 설정이 '보안 옵션 - 감사' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAudit_AINE.json) |
|[Windows 컴퓨터는 '시스템 감사 정책 - 계정 관리' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94d9aca8-3757-46df-aa51-f218c5f11954) |Windows 컴퓨터에는 애플리케이션, 보안, 사용자 그룹 관리 및 기타 관리 이벤트를 감사하는 그룹 정책 설정이 '시스템 감사 정책 - 계정 관리' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesAccountManagement_AINE.json) |

### <a name="applications-and-operating-systems-are-successfully-tested-for-usability-security-and-impact-prior-to-production"></a>애플리케이션 및 운영 체제는 프로덕션 이전에 유용성, 보안 및 영향에 대해 성공적으로 테스트됩니다.

**ID**: 0606.10h2System.1 - 10.h **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[컨테이너 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Docker가 설치된 머신에서 보안 구성의 취약성을 감사하고 Azure Security Center에서 권장 사항으로 표시합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-uses-its-configuration-control-program-to-maintain-control-of-all-implemented-software-and-its-system-documentation-and-archive-prior-versions-of-implemented-software-and-associated-system-documentation"></a>조직은 구성 제어 프로그램을 사용하여 구현된 모든 소프트웨어 및 해당 시스템 설명서의 제어를 유지하고, 이전 버전의 구현된 소프트웨어 및 관련 시스템 설명서를 보관합니다.

**ID**: 0607.10h2System.23 - 10.h **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |애플리케이션 제어를 활성화하여 머신에서 실행 중인 알려진 안전한 애플리케이션 목록을 정의하고, 다른 애플리케이션이 실행될 때 경고합니다. 이렇게 하면 맬웨어로부터 머신을 강화할 수 있습니다. 규칙을 구성하고 유지 관리하는 프로세스를 간소화하기 위해 Security Center는 기계 학습을 사용하여 각 머신에서 실행 중인 애플리케이션을 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |가상 머신 확장 집합의 OS 취약성을 감사하여 공격으로부터 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

## <a name="change-control-procedures"></a>변경 제어 절차

### <a name="managers-responsible-for-application-systems-are-also-responsible-for-the-strict-control-security-of-the-project-or-support-environment-and-ensure-that-all-proposed-system-changes-are-reviewed-to-check-that-they-do-not-compromise-the-security-of-either-the-system-or-the-operating-environment"></a>또한 애플리케이션 시스템을 담당하는 관리자는 프로젝트 또는 지원 환경의 엄격한 제어(보안)를 담당하고, 제안된 모든 시스템 변경 내용을 검토하여 이로 인해 시스템 또는 운영 환경의 보안이 손상되지 않는지 확인해야 합니다.

**ID**: 0635.10k1Organizational.12 - 10.k **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 컴퓨터는 '시스템 감사 정책 - 세부 추적' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 컴퓨터에는 DPAPI, 프로세스 만들기/종료, RPC 이벤트 및 PNP 활동을 감사하는 그룹 정책 설정이 '시스템 감사 정책 - 세부 추적' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-formally-addresses-purpose-scope-roles-responsibilities-management-commitment-coordination-among-organizational-entities-and-compliance-for-configuration-management-eg-through-policies-standards-processes"></a>조직은 목적, 범위, 역할, 책임, 관리 약정, 조직 엔터티 간의 조정 및 구성 관리 규정 준수(예: 정책, 표준, 프로세스를 통해)를 공식적으로 처리합니다.

**ID**: 0636.10k2Organizational.1 - 10.k **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 컴퓨터는 '시스템 감사 정책 - 세부 추적' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 컴퓨터에는 DPAPI, 프로세스 만들기/종료, RPC 이벤트 및 PNP 활동을 감사하는 그룹 정책 설정이 '시스템 감사 정책 - 세부 추적' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-has-developed-documented-and-implemented-a-configuration-management-plan-for-the-information-system"></a>조직은 정보 시스템에 대한 구성 관리 계획을 개발, 문서화 및 구현했습니다.

**ID**: 0637.10k2Organizational.2 - 10.k **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 컴퓨터는 '시스템 감사 정책 - 세부 추적' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 컴퓨터에는 DPAPI, 프로세스 만들기/종료, RPC 이벤트 및 PNP 활동을 감사하는 그룹 정책 설정이 '시스템 감사 정책 - 세부 추적' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="changes-are-formally-controlled-documented-and-enforced-in-order-to-minimize-the-corruption-of-information-systems"></a>변경 내용은 정보 시스템의 손상을 최소화하기 위해 공식적으로 제어, 문서화 및 적용됩니다.

**ID**: 0638.10k2Organizational.34569 - 10.k **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 컴퓨터는 '시스템 감사 정책 - 세부 추적' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 컴퓨터에는 DPAPI, 프로세스 만들기/종료, RPC 이벤트 및 PNP 활동을 감사하는 그룹 정책 설정이 '시스템 감사 정책 - 세부 추적' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="installation-checklists-and-vulnerability-scans-are-used-to-validate-the-configuration-of-servers-workstations-devices-and-appliances-and-ensure-the-configuration-meets-minimum-standards"></a>설치 검사 목록 및 취약성 검사는 서버, 워크스테이션, 디바이스 및 어플라이언스의 구성에 대한 유효성을 검사하고 구성이 최소 표준을 충족하는지 확인하는 데 사용됩니다.

**ID**: 0639.10k2Organizational.78 - 10.k **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 컴퓨터는 '시스템 감사 정책 - 세부 추적' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 컴퓨터에는 DPAPI, 프로세스 만들기/종료, RPC 이벤트 및 PNP 활동을 감사하는 그룹 정책 설정이 '시스템 감사 정책 - 세부 추적' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="where-development-is-outsourced-change-control-procedures-to-address-security-are-included-in-the-contracts-and-specifically-require-the-developer-to-track-security-flaws-and-flaw-resolution-within-the-system-component-or-service-and-report-findings-to-organization-defined-personnel-or-roles"></a>개발을 아웃소싱하는 경우 보안을 처리하기 위한 변경 제어 절차는 계약에 포함되며, 특히 개발자가 시스템, 구성 요소 또는 서비스 내에서 보안 결함 및 결함 해결을 추적하고 결과를 조직에서 정의한 담당자 또는 역할에 보고하도록 해야 합니다.

**ID**: 0640.10k2Organizational.1012 - 10.k **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 컴퓨터는 '시스템 감사 정책 - 세부 추적' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 컴퓨터에는 DPAPI, 프로세스 만들기/종료, RPC 이벤트 및 PNP 활동을 감사하는 그룹 정책 설정이 '시스템 감사 정책 - 세부 추적' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-does-not-use-automated-updates-on-critical-systems"></a>조직은 중요한 시스템에 대해 자동화된 업데이트를 사용하지 않습니다.

**ID**: 0641.10k2Organizational.11 - 10.k **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 컴퓨터는 '시스템 감사 정책 - 세부 추적' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 컴퓨터에는 DPAPI, 프로세스 만들기/종료, RPC 이벤트 및 PNP 활동을 감사하는 그룹 정책 설정이 '시스템 감사 정책 - 세부 추적' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-develops-documents-and-maintains-under-configuration-control-a-current-baseline-configuration-of-the-information-system-and-reviews-and-updates-the-baseline-as-required"></a>조직은 구성 제어에 따라 정보 시스템의 현재 기준 구성을 개발, 문서화 및 유지 관리하고, 필요에 따라 기준을 검토 및 업데이트합니다.

**ID**: 0642.10k3Organizational.12 - 10.k **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 컴퓨터는 '시스템 감사 정책 - 세부 추적' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 컴퓨터에는 DPAPI, 프로세스 만들기/종료, RPC 이벤트 및 PNP 활동을 감사하는 그룹 정책 설정이 '시스템 감사 정책 - 세부 추적' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-i-establishes-and-documents-mandatory-configuration-settings-for-information-technology-products-employed-within-the-information-system-using-the-latest-security-configuration-baselines-ii-identifies-documents-and-approves-exceptions-from-the-mandatory-established-configuration-settings-for-individual-components-based-on-explicit-operational-requirements-and-iii-monitors-and-controls-changes-to-the-configuration-settings-in-accordance-with-organizational-policies-and-procedures"></a>조직은 (i) 최신 보안 구성 기준을 사용하여 정보 시스템 내에서 사용되는 정보 기술 제품에 대한 필수 구성 설정을 설정하고 문서화합니다. (ii) 명시적 작업 요구 사항에 따라 개별 구성 요소에 대해 설정된 필수 구성 설정에서 예외를 식별, 문서화 및 승인합니다. (iii) 조직 정책 및 절차에 따라 구성 설정 변경을 모니터링하고 제어합니다.

**ID**: 0643.10k3Organizational.3 - 10.k **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 컴퓨터는 '시스템 감사 정책 - 세부 추적' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 컴퓨터에는 DPAPI, 프로세스 만들기/종료, RPC 이벤트 및 PNP 활동을 감사하는 그룹 정책 설정이 '시스템 감사 정책 - 세부 추적' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-employs-automated-mechanisms-to-i-centrally-manage-apply-and-verify-configuration-settings-ii-respond-to-unauthorized-changes-to-network-and-system-security-related-configuration-settings-and-iii-enforce-access-restrictions-and-auditing-of-the-enforcement-actions"></a>조직은 자동화된 메커니즘을 사용하여 (i) 구성 설정을 중앙에서 관리, 적용 및 확인합니다. (ii) 네트워크 및 시스템 보안 관련 구성 설정에 대한 무단 변경에 대응합니다. (iii) 적용 작업에 대한 액세스 제한 및 감사를 적용합니다.

**ID**: 0644.10k3Organizational.4 - 10.k **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 컴퓨터는 '시스템 감사 정책 - 세부 추적' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 컴퓨터에는 DPAPI, 프로세스 만들기/종료, RPC 이벤트 및 PNP 활동을 감사하는 그룹 정책 설정이 '시스템 감사 정책 - 세부 추적' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

## <a name="control-of-technical-vulnerabilities"></a>기술 취약성 제어

### <a name="technical-vulnerabilities-are-identified-evaluated-for-risk-and-corrected-in-a-timely-manner"></a>기술 취약성을 식별하고, 위험을 평가하고, 적시에 수정합니다.

**ID**: 0709.10m1Organizational.1 - 10.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[취약성 평가 솔루션을 가상 머신에서 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |가상 머신을 감사하여 지원되는 취약성 평가 솔루션을 실행하고 있는지 검색합니다. 모든 사이버 위험 및 보안 프로그램의 핵심 구성 요소는 취약성을 식별하고 분석하는 것입니다. Azure Security Center의 표준 가격 책정 계층에는 추가 비용 없이 가상 머신에 대한 취약성 검사가 포함됩니다. 또한 Security Center가 자동으로 도구를 배포할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[SQL 데이터베이스가 발견한 취약성을 해결해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |취약성 평가 검사 결과 및 데이터베이스 취약성을 해결하는 방법에 대한 권장 사항을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[컨테이너 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Docker가 설치된 머신에서 보안 구성의 취약성을 감사하고 Azure Security Center에서 권장 사항으로 표시합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[머신 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |구성된 기준을 충족하지 않는 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |가상 머신 확장 집합의 OS 취약성을 감사하여 공격으로부터 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[SQL Managed Instance에서 취약성 평가를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |반복 취약성 평가 검사를 사용하도록 설정하지 않은 각 SQL Managed Instance를 감사합니다. 취약성 평가는 잠재적 데이터베이스 취약성을 검색 및 추적할 수 있고 해결하는 데 도움이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |반복 취약성 평가 검사를 사용하도록 설정하지 않은 Azure SQL 서버를 감사합니다. 취약성 평가는 잠재적 데이터베이스 취약성을 검색 및 추적할 수 있고 해결하는 데 도움이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[Windows 컴퓨터는 '보안 옵션 - Microsoft 네트워크 서버' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcaf2d518-f029-4f6b-833b-d7081702f253) |Windows 컴퓨터에는 SMB v1 서버를 사용하지 않도록 설정하는 그룹 정책 설정이 '보안 옵션 - Microsoft 네트워크 서버' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_AINE.json) |

### <a name="a-hardened-configuration-standard-exists-for-all-system-and-network-components"></a>모든 시스템 및 네트워크 구성 요소에 대해 강화된 구성 표준이 있습니다.

**ID**: 0710.10m2Organizational.1 - 10.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Managed Instance에서 취약성 평가를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |반복 취약성 평가 검사를 사용하도록 설정하지 않은 각 SQL Managed Instance를 감사합니다. 취약성 평가는 잠재적 데이터베이스 취약성을 검색 및 추적할 수 있고 해결하는 데 도움이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

### <a name="a-technical-vulnerability-management-program-is-in-place-to-monitor-assess-rank-and-remediate-vulnerabilities-identified-in-systems"></a>기술 취약성 관리 프로그램은 시스템에서 식별된 취약성을 모니터링, 평가, 순위 지정 및 수정하는 데 적용됩니다.

**ID**: 0711.10m2Organizational.23 - 10.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[취약성 평가 솔루션을 가상 머신에서 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |가상 머신을 감사하여 지원되는 취약성 평가 솔루션을 실행하고 있는지 검색합니다. 모든 사이버 위험 및 보안 프로그램의 핵심 구성 요소는 취약성을 식별하고 분석하는 것입니다. Azure Security Center의 표준 가격 책정 계층에는 추가 비용 없이 가상 머신에 대한 취약성 검사가 포함됩니다. 또한 Security Center가 자동으로 도구를 배포할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="patches-are-tested-and-evaluated-before-they-are-installed"></a>패치는 설치하기 전에 테스트 및 평가됩니다.

**ID**: 0713.10m2Organizational.5 - 10.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |구성된 기준을 충족하지 않는 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-technical-vulnerability-management-program-is-evaluated-on-a-quarterly-basis"></a>기술 취약성 관리 프로그램은 분기별로 평가됩니다.

**ID**: 0714.10m2Organizational.7 - 10.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |가상 머신 확장 집합의 OS 취약성을 감사하여 공격으로부터 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="systems-are-appropriately-hardened-eg-configured-with-only-necessary-and-secure-services-ports-and-protocols-enabled"></a>시스템은 적절하게 강화됩니다(예: 필수적이고 안전한 서비스, 포트 및 프로토콜만 사용하도록 구성됨).

**ID**: 0715.10m2Organizational.8 - 10.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[컨테이너 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Docker가 설치된 머신에서 보안 구성의 취약성을 감사하고 Azure Security Center에서 권장 사항으로 표시합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-conducts-an-enterprise-security-posture-review-as-needed-but-no-less-than-once-within-every-three-hundred-sixty-five-365-days-in-accordance-with-organizational-is-procedures"></a>조직은 필요에 따라 엔터프라이즈 보안 상태 검토를 수행하지만, 조직의 IS 절차에 따라 365일마다 한 번 이상 수행합니다.

**ID**: 0716.10m3Organizational.1 - 10.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 데이터베이스가 발견한 취약성을 해결해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |취약성 평가 검사 결과 및 데이터베이스 취약성을 해결하는 방법에 대한 권장 사항을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning-tools-include-the-capability-to-readily-update-the-information-system-vulnerabilities-scanned"></a>취약성 검사 도구에는 검사된 정보 시스템의 취약성을 쉽게 업데이트할 수 있는 기능이 포함됩니다.

**ID**: 0717.10m3Organizational.2 - 10.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |가상 머신 확장 집합의 OS 취약성을 감사하여 공격으로부터 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="the-organization-scans-for-vulnerabilities-in-the-information-system-and-hosted-applications-to-determine-the-state-of-flaw-remediation-monthly-automatically-and-again-manually-or-automatically-when-new-vulnerabilities-potentially-affecting-the-systems-and-networked-environments-are-identified-and-reported"></a>조직은 정보 시스템 및 호스팅된 애플리케이션의 취약성을 검사하여 결함 수정 상태를 매월 확인하고(자동), 시스템 및 네트워크 환경에 잠재적으로 영향을 줄 수 있는 새로운 취약성이 식별되어 보고되면 다시 확인합니다(수동 또는 자동).

**ID**: 0718.10m3Organizational.34 - 10.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신 보안 구성의 취약성을 수정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |구성된 기준을 충족하지 않는 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-organization-updates-the-list-of-information-system-vulnerabilities-scanned-within-every-thirty-30-days-or-when-new-vulnerabilities-are-identified-and-reported"></a>조직은 30일마다 또는 새로운 취약성이 식별되어 보고될 때 검사되는 정보 시스템 취약성의 목록을 업데이트합니다.

**ID**: 0719.10m3Organizational.5 - 10.m **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Managed Instance에서 취약성 평가를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |반복 취약성 평가 검사를 사용하도록 설정하지 않은 각 SQL Managed Instance를 감사합니다. 취약성 평가는 잠재적 데이터베이스 취약성을 검색 및 추적할 수 있고 해결하는 데 도움이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

## <a name="business-continuity-and-risk-assessment"></a>비즈니스 연속성 및 위험 평가

### <a name="the-organization-identifies-the-critical-business-processes-requiring-business-continuity"></a>조직은 비즈니스 연속성이 필요한 중요 비즈니스 프로세스를 식별합니다.

**ID**: 1634.12b1Organizational.1 - 12.b **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[재해 복구가 구성되어 있지 않은 가상 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |재해 복구가 구성되지 않은 가상 머신을 감사합니다. 재해 복구에 대한 자세한 내용은 [https://aka.ms/asr-doc](../../../site-recovery/index.yml)를 참조하세요. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

### <a name="information-security-aspects-of-business-continuity-are-i-based-on-identifying-events-or-sequence-of-events-that-can-cause-interruptions-to-the-organizations-critical-business-processes-eg-equipment-failure-human-errors-theft-fire-natural-disasters-acts-of-terrorism-ii-followed-by-a-risk-assessment-to-determine-the-probability-and-impact-of-such-interruptions-in-terms-of-time-damage-scale-and-recovery-period-iii-based-on-the-results-of-the-risk-assessment-a-business-continuity-strategy-is-developed-to-identify-the-overall-approach-to-business-continuity-and-iv-once-this-strategy-has-been-created-endorsement-is-provided-by-management-and-a-plan-created-and-endorsed-to-implement-this-strategy"></a>비즈니스 연속성의 정보 보안 측면은 (i) 조직의 중요 비즈니스 프로세스를 중단시킬 수 있는 이벤트 또는 이벤트 시퀀스(예: 장비 오류, 인간 오류, 도난, 화재, 자연 재해 테러 행위)의 식별을 기반으로 합니다. (ii) 위험 평가 후에 시간, 손상 규모 및 복구 기간 측면에서 이러한 중단의 가능성과 영향을 결정합니다. (iii) 위험 평가의 결과에 따라 비즈니스 연속성에 대한 전반적인 접근 방식을 식별하기 위한 비즈니스 연속성 전략을 개발합니다. (iv) 이 전략이 만들어지면 경영진에서 보증을 제공하고 이 전략을 구현하기 위한 계획을 만들고 승인합니다.

**ID**: 1635.12b1Organizational.2 - 12.b **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Key Vault Managed HSM에 제거 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39ba22d-4428-4149-b981-70acb31fc383) |Azure Key Vault Managed HSM을 악의적으로 삭제하면 데이터가 영구적으로 손실될 수 있습니다. 조직의 악의적인 내부자가 잠재적으로 Azure Key Vault Managed HSM을 삭제하고 제거할 수 있습니다. 제거 보호는 일시 삭제된 Azure Key Vault Managed HSM에 대해 필수 보존 기간을 적용하여 내부자 공격으로부터 보호합니다. 일시 삭제 보존 기간 동안에는 조직 또는 Microsoft 내부의 어느 누구도 Azure Key Vault Managed HSM을 제거할 수 없습니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_Recoverable_Audit.json) |
|[키 자격 증명 모음에 제거 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |키 자격 증명 모음을 악의적으로 삭제하면 데이터가 영구적으로 손실될 수 있습니다. 조직의 악의적인 내부자가 잠재적으로 키 자격 증명 모음을 삭제하고 제거할 수 있습니다. 제거 보호는 일시 삭제된 키 자격 증명 모음에 대해 필수 보존 기간을 적용하여 내부자 공격으로부터 보호합니다. 일시 삭제 보존 기간 동안에는 조직 또는 Microsoft 내부의 어느 누구도 키 자격 증명 모음을 제거할 수 없습니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="business-impact-analysis-are-used-to-evaluate-the-consequences-of-disasters-security-failures-loss-of-service-and-service-availability"></a>비즈니스 영향 분석은 재해, 보안 오류, 서비스 손실 및 서비스 가용성의 결과를 평가하는 데 사용됩니다.

**ID**: 1637.12b2Organizational.2 - 12.b **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 컴퓨터는 '보안 옵션 - 복구 콘솔' 요구 사항을 충족해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff71be03e-e25b-4d0f-b8bc-9b3e309b66c0) |Windows 컴퓨터에는 모든 드라이브와 폴더에 대한 플로피 복사 및 액세스를 허용하는 그룹 정책 설정이 '보안 옵션 - 복구 콘솔' 범주에 지정되어야 합니다. 이 정책을 사용하려면 게스트 구성 필수 구성 요소가 정책 할당 범위에 배포되어 있어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../concepts/guest-configuration.md)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsRecoveryconsole_AINE.json) |

### <a name="business-continuity-risk-assessments-i-are-carried-out-annually-with-full-involvement-from-owners-of-business-resources-and-processes-ii-consider-all-business-processes-and-is-not-limited-to-the-information-assets-but-includes-the-results-specific-to-information-security-and-iii-identifies-quantifies-and-prioritizes-risks-against-key-business-objectives-and-criteria-relevant-to-the-organization-including-critical-resources-impacts-of-disruptions-allowable-outage-times-and-recovery-priorities"></a>비즈니스 연속성 위험 평가는 (i) 비즈니스 리소스 및 프로세스 소유자의 모든 참여를 통해 매년 수행됩니다. (ii) 모든 비즈니스 프로세스를 고려하고 정보 자산에 국한되지 않지만 정보 보안과 관련된 결과를 포함합니다. (iii) 중요한 리소스, 중단의 영향, 허용 가능한 중단 시간 및 복구 우선 순위를 포함하여 조직과 관련된 주요 비즈니스 목표 및 기준에 대한 위험을 식별하고, 정량화하고, 우선 순위를 지정합니다.

**ID**: 1638.12b2Organizational.345 - 12.b **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[재해 복구가 구성되어 있지 않은 가상 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |재해 복구가 구성되지 않은 가상 머신을 감사합니다. 재해 복구에 대한 자세한 내용은 [https://aka.ms/asr-doc](../../../site-recovery/index.yml)를 참조하세요. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

## <a name="next-steps"></a>다음 단계

Azure Policy에 대한 추가 문서:

- [규정 준수](../concepts/regulatory-compliance.md) 개요
- [이니셔티브 정의 구조](../concepts/initiative-definition-structure.md)를 참조합니다.
- [Azure Policy 샘플](./index.md)의 다른 예제를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.