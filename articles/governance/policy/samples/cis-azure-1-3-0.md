---
title: CIS Microsoft Azure Foundations Benchmark 1.3.0에 대한 규정 준수 세부 정보
description: CIS Microsoft Azure Foundations Benchmark 1.3.0 규정 준수 기본 제공 이니셔티브에 대한 세부 정보. 각 컨트롤은 평가를 지원하는 하나 이상의 Azure Policy 정의에 매핑됩니다.
ms.date: 09/17/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: c61e62ca4e65167d4f939d3cebae4ff67c78ade3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128586217"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-130-regulatory-compliance-built-in-initiative"></a>CIS Microsoft Azure Foundations Benchmark 1.3.0 규정 준수 기본 제공 이니셔티브에 대한 세부 정보

다음 문서에서는 Azure Policy 규정 준수 기본 제공 이니셔티브 정의가 CIS Microsoft Azure Foundations Benchmark 1.3.0의 **규정 준수 도메인** 및 **컨트롤** 에 매핑되는 방법을 자세히 설명합니다.
이러한 규정 준수 표준에 대한 자세한 내용은 [CIS Microsoft Azure Foundations Benchmark 1.3.0](https://www.cisecurity.org/benchmark/azure/)을 참조하세요. _소유권_ 을 이해하려면 [Azure Policy 정책 정의](../concepts/definition-structure.md#type) 및 [클라우드의 공동 책임](../../../security/fundamentals/shared-responsibility.md)을 참조하세요.

다음 매핑은 **CIS Microsoft Azure Foundations Benchmark 1.3.0** 컨트롤에 대한 것입니다. 특정 **규정 준수 도메인** 으로 바로 점프하려면 오른쪽의 탐색 기능을 사용합니다. 여러 컨트롤이 [Azure Policy](../overview.md) 이니셔티브 정의를 사용하여 구현됩니다. 전체 이니셔티브 정의를 검토하려면 Azure Portal에서 **정책** 을 열고 **정의** 페이지를 선택합니다.
그런 다음, **CIS Microsoft Azure Foundations Benchmark v1.3.0** 규정 준수 기본 제공 이니셔티브 정의를 찾아 선택합니다.

> [!IMPORTANT]
> 아래의 각 컨트롤은 하나 이상의 [Azure Policy](../overview.md) 정의와 연결되어 있습니다.
> 이러한 정책은 컨트롤을 사용한 [규정 준수 평가](../how-to/get-compliance-data.md)에 도움이 될 수 있지만, 컨트롤과 하나 이상의 정책 간에 일대일 또는 완벽한 일치 관계가 없는 경우도 많습니다. 따라서 Azure Policy의 **규정 준수** 는 정책 정의 자체만 가리킬 뿐, 컨트롤의 모든 요구 사항을 완벽하게 준수한다는 것은 아닙니다. 또한 규정 준수 표준에는 현재 Azure Policy 정의에서 처리되지 않은 컨트롤이 포함되어 있습니다. 따라서 Azure Policy의 규정 준수는 전반적인 규정 준수 상태를 부분적으로 표시할 뿐입니다. 이 규정 준수 표준에 대한 규정 준수 도메인, 컨트롤, Azure Policy 정의 간의 연결은 시간이 지나면 변경될 수 있습니다. 변경 기록을 보려면 [GitHub 커밋 기록](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_3_0.json)을 참조하세요.

## <a name="identity-and-access-management"></a>ID 및 액세스 관리

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>권한이 있는 모든 사용자에 대한 다단계 인증이 설정되어 있는지 확인

**ID**: CIS Azure 1.1 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 쓰기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |계정 또는 리소스 위반을 방지하려면 쓰기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |계정 또는 리소스 위반을 방지하려면 소유자 권한이 있는 모든 구독 계정에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>권한이 없는 모든 사용자에 대한 다단계 인증이 설정되어 있는지 확인

**ID**: CIS Azure 1.2 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |계정 또는 리소스 위반을 방지하려면 읽기 권한이 있는 모든 구독 계정에서 MFA(Multi-Factor Authentication)를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-guest-users-are-reviewed-on-a-monthly-basis"></a>월 단위로 게스트 사용자를 검토하는지 확인

**ID**: CIS Azure 1.3 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |모니터링되지 않는 액세스를 방지하려면 소유자 권한이 있는 외부 계정을 구독에서 제거해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |모니터링되지 않는 액세스를 방지하려면 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |모니터링되지 않는 액세스를 방지하려면 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>사용자 지정 구독 소유자 역할이 생성되지 않았는지 확인

**ID**: CIS Azure 1.21 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[사용자 지정 구독 소유자 역할이 없어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |이 정책은 사용자 지정 구독 소유자 역할이 없도록 합니다. |감사, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-azure-defender-is-set-to-on-for-servers"></a>Azure Defender가 서버에 대해 On으로 설정되어 있는지 확인

**ID**: CIS Azure 2.1 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |서버용 Azure Defender는 서버 워크로드에 대한 실시간 위협 방지 기능을 제공하고 강화된 권장 사항과 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-app-service"></a>Azure Defender가 App Service에 대해 On으로 설정되어 있는지 확인

**ID**: CIS Azure 2.2 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender for App Service를 사용해야 합니다](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender for App Service는 클라우드의 규모와 Azure가 클라우드 공급자로서 가지고 있는 가시성을 활용하여 일반적인 웹앱 공격을 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-azure-sql-database-servers"></a>Azure SQL 데이터베이스 서버에 대해 Azure Defender가 On으로 설정되어 있는지 확인

**ID**: CIS Azure 2.3 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure SQL Database 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-sql-servers-on-machines"></a>머신의 SQL 서버에 대해 Azure Defender가 On으로 설정 되어있는지 확인

**ID**: CIS Azure 2.4 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신의 SQL 서버용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender for SQL은 잠재적인 데이터베이스 취약성을 표시 및 완화하고, SQL 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 감지하고, 중요한 데이터를 검색 및 분류하는 기능을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-storage"></a>Azure Defender가 스토리지에 대해 On으로 설정되어 있는지 확인

**ID**: CIS Azure 2.5 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[스토리지용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-kubernetes"></a>Azure Defender가 Kubernetes에 대해 On으로 설정되어 있는지 확인

**ID**: CIS Azure 2.6 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender for Kubernetes를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender for Kubernetes는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-container-registries"></a>컨테이너 레지스트리에 대해 Azure Defender가 On으로 설정되어 있는지 확인

**ID**: CIS Azure 2.7 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[컨테이너 레지스트리용 Azure Defender를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |컨테이너 레지스트리용 Azure Defender는 지난 30일 내에 끌어와서 레지스트리에 푸시한 이미지의 취약성을 검사하고, 각 이미지에 대해 발견한 자세한 내용을 공개합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-key-vault"></a>Azure Defender가 Key Vault에 대해 On으로 설정되어 있는지 확인

**ID**: CIS Azure 2.8 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender for Key Vault를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender for Key Vault는 키 자격 증명 모음 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하여 보안 인텔리전스에 추가 보호 계층을 제공합니다. |AuditIfNotExists, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>'모니터링 에이전트의 자동 프로비저닝'이 '켜기'로 설정되어 있는지 확인

**ID**: CIS Azure 2.11 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 Log Analytics 에이전트의 자동 프로비저닝을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |보안 취약성 및 위협을 모니터링하기 위해 Azure Security Center는 Azure 가상 머신에서 데이터를 수집합니다. 데이터는 이전에 MMA(Microsoft Monitoring Agent)로 알려진 Log Analytics 에이전트에 의해 수집되며, 머신에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석을 위해 Log Analytics 작업 영역에 데이터를 복사합니다. 지원되는 모든 Azure VM과 생성된 모든 새 VM에 에이전트를 자동으로 배포하려면 자동 프로비저닝을 사용하도록 설정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-additional-email-addresses-is-configured-with-a-security-contact-email"></a>'추가 이메일 주소'가 보안 연락처 이메일로 구성되어 있는지 확인

**ID**: CIS Azure 2.13 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 보안 문제에 대한 연락처 이메일 주소가 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |구독 중 하나에 잠재적인 보안 위반이 있을 때 조직의 관련 인원에게 알리려면 Security Center에서 이메일 알림을 받을 수 있도록 보안 연락처를 설정합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-notify-about-alerts-with-the-following-severity-is-set-to-high"></a>'다음 심각도로 경고 알림'이 '높음'으로 설정되어 있는지 확인

**ID**: CIS Azure 2.14 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[심각도가 높은 경고에 대해 이메일 알림을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |구독 중 하나에 잠재적인 보안 위반이 있을 때 조직의 관련 인원에게 알리려면 Security Center에서 심각도가 높은 경고에 대한 이메일 알림을 사용하도록 설정합니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

## <a name="storage-accounts"></a>스토리지 계정

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>'보안 전송 필요'가 '사용'으로 설정되어 있는지 확인

**ID**: CIS Azure 3.1 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[스토리지 계정에 보안 전송을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |스토리지 계정의 보안 전송 요구 사항을 감사합니다. 보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS를 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>Blob 컨테이너에 대해 '퍼블릭 액세스 수준'이 프라이빗으로 설정되어 있는지 확인합니다.

**ID**: CIS Azure 3.5 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[스토리지 계정 공용 액세스가 허용되지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Azure Storage의 컨테이너 및 BLOB에 대한 익명 공용 읽기 액세스는 데이터를 공유하는 편리한 방법이지만 보안 위험이 있을 수도 있습니다. 원치 않는 익명 액세스로 인해 발생하는 데이터 위반을 방지하기 위해 Microsoft는 시나리오에 필요한 경우가 아니면 스토리지 계정에 대한 공개 액세스를 방지하는 것이 좋습니다 |감사, 거부, 사용 안 함 |[3.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>스토리지 계정에 대한 기본 네트워크 액세스 규칙이 거부로 설정되어 있는지 확인

**ID**: CIS Azure 3.6 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[스토리지 계정은 네트워크 액세스를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |스토리지 계정에 대한 네트워크 액세스가 제한되어야 합니다. 허용되는 네트워크의 애플리케이션만 스토리지 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용하기 위해 특정 Azure 가상 네트워크 또는 공용 인터넷 IP 주소 범위의 트래픽에 대한 액세스 권한을 부여할 수 있습니다. |감사, 거부, 사용 안 함 |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[스토리지 계정은 가상 네트워크 규칙을 사용하여 네트워크 액세스를 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |IP 기반 필터링 대신 기본 방법으로 가상 네트워크 규칙을 사용하여 잠재적인 위협으로부터 스토리지 계정을 보호합니다. IP 기반 필터링을 사용하지 않도록 설정하면 공용 IP가 스토리지 계정에 액세스할 수 없습니다. |감사, 거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>'신뢰할 수 있는 Microsoft 서비스'에 스토리지 계정 액세스가 사용하도록 설정되어 있는지 확인

**ID**: CIS Azure 3.7 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[스토리지 계정은 신뢰할 수 있는 Microsoft 서비스의 액세스를 허용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |스토리지 계정과 상호 작용하는 일부 Microsoft 서비스는 네트워크 규칙을 통해 액세스 권한을 부여할 수 없는 네트워크에서 작동합니다. 이러한 유형의 서비스가 의도한 대로 작동하도록 하려면 신뢰할 수 있는 Microsoft 서비스 세트에서 네트워크 규칙을 무시하도록 허용합니다. 그러면 이러한 서비스에서 강력한 인증을 사용하여 스토리지 계정에 액세스합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

### <a name="ensure-storage-for-critical-data-are-encrypted-with-customer-managed-key"></a>중요한 데이터의 스토리지가 고객 관리형 키로 암호화되어 있는지 확인

**ID**: CIS Azure 3.9 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[스토리지 계정은 고객 관리형 키를 사용하여 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fac406b-40ca-413b-bf8e-0bf964659c25) |고객 관리형 키를 사용하여 유연성이 뛰어난 Blob 및 파일 스토리지 계정을 보호합니다. 고객 관리형 키를 지정하는 경우 해당 키는 데이터를 암호화하는 키에 대한 액세스를 보호하고 제어하는 데 사용됩니다. 고객 관리형 키를 사용하면 키 암호화 키의 회전을 제어하거나 데이터를 암호화 방식으로 지우는 추가 기능이 제공됩니다. |감사, 사용 안 함 |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountCustomerManagedKeyEnabled_Audit.json) |

## <a name="database-services"></a>데이터베이스 서비스

### <a name="ensure-that-auditing-is-set-to-on"></a>'감사'가 '켜기'로 설정되어 있는지 확인

**ID**: CIS Azure 4.1.1 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 서버에 대한 감사가 사용되도록 설정되어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |서버의 모든 데이터베이스에서 데이터베이스 활동을 추적하고 감사 로그에 저장하려면 SQL Server에서의 감사를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>SQL Database에서 '데이터 암호화'가 '켜기'로 설정되어 있는지 확인

**ID**: CIS Azure 4.1.2 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |저장 데이터를 보호하고 규정 준수 요구 사항을 충족하려면 투명한 데이터 암호화를 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>'감사' 보존 기간이 '90일 이상'인지 확인

**ID**: CIS Azure 4.1.3 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[스토리지 계정 대상에 대한 감사 기능이 있는 SQL Server는 보존 기간을 90일 이상으로 구성해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |인시던트 조사를 위해 SQL Server 감사를 위한 데이터 보존 기간을 스토리지 계정 대상으로 90일 이상으로 설정하는 것이 좋습니다. 운영 중인 지역에 필요한 보존 규칙을 충족하는지 확인합니다. 이는 규정 표준을 준수해야 하는 경우도 있습니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-threat-protection-atp-on-a-sql-server-is-set-to-enabled"></a>SQL 서버의 ATP(Advanced Threat Protection)가 '사용'으로 설정되어 있는지 확인

**ID**: CIS Azure 4.2.1 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[보호되지 않는 Azure SQL 서버에 대해 SQL용 Azure Defender를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security 없이 SQL 서버 감사 |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |
|[보호되지 않는 SQL Managed Instance에 대해 Azure Defender for SQL을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security를 사용하지 않고 각 SQL Managed Instance를 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-vulnerability-assessment-va-is-enabled-on-a-sql-server-by-setting-a-storage-account"></a>스토리지 계정을 설정하여 SQL 서버에서 VA(취약성 평가)가 활성화되어 있는지 확인

**ID**: CIS Azure 4.2.2 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Managed Instance에서 취약성 평가를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |반복 취약성 평가 검사를 사용하도록 설정하지 않은 각 SQL Managed Instance를 감사합니다. 취약성 평가는 잠재적 데이터베이스 취약성을 검색 및 추적할 수 있고 해결하는 데 도움이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |반복 취약성 평가 검사를 사용하도록 설정하지 않은 Azure SQL 서버를 감사합니다. 취약성 평가는 잠재적 데이터베이스 취약성을 검색 및 추적할 수 있고 해결하는 데 도움이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

### <a name="ensure-that-va-setting-send-scan-reports-to-is-configured-for-a-sql-server"></a>SQL 서버에 대해 VA 설정 송신 스캔 보고서가 구성되어 있는지 확인

**ID**: CIS Azure 4.2.4 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 서버에 대한 취약성 평가 설정에는 검사 보고서를 수신할 이메일 주소를 포함해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057d6cfe-9c4f-4a6d-bc60-14420ea1f1a9) |취약점 평가 설정에서 '검사 보고서 전송 대상' 필드에 이메일 주소가 지정되었는지 확인합니다. 이 이메일 주소는 SQL 서버에 대한 정기적인 검사기 실행된 후 검사 결과 요약을 받습니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_VulnerabilityAssessmentEmails_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>PostgreSQL Database Server에 'SSL 연결 적용'이 '사용'으로 설정되어 있는지 확인

**ID**: CIS Azure 4.3.1 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL용 Azure Database는 SSL(Secure Sockets Layer)을 사용한 MySQL용 Azure Database 서버와 클라이언트 애플리케이션 간 연결을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>MySQL Database Server에 'SSL 연결 적용'이 '사용'으로 설정되어 있는지 확인

**ID**: CIS Azure 4.3.2 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL은 SSL(Secure Sockets Layer)을 사용하여 Azure Database for PostgreSQL 서버를 클라이언트 애플리케이션에 연결하는 것을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>PostgreSQL Database Server에 'log_checkpoints' 서버 매개 변수가 'ON'으로 설정되어 있는지 확인

**ID**: CIS Azure 4.3.3 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL 데이터베이스 서버에 대해 로그 검사점을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |이 정책은 log_checkpoints 설정을 사용하도록 설정하지 않고 사용자 환경에서 PostgreSQL 데이터베이스를 감사하는 데 도움이 됩니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>PostgreSQL Database Server에 'log_connections' 서버 매개 변수가 'ON'으로 설정되어 있는지 확인

**ID**: CIS Azure 4.3.4 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL 데이터베이스 서버에 대해 로그 연결을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |이 정책은 log_connections 설정을 사용하도록 설정하지 않고 사용자 환경에서 PostgreSQL 데이터베이스를 감사하는 데 도움이 됩니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>PostgreSQL Database Server에 'log_disconnections' 서버 매개 변수가 'ON'으로 설정되어 있는지 확인

**ID**: CIS Azure 4.3.5 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL 데이터베이스 서버에 대한 연결 끊김을 기록해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |이 정책은 log_disconnections를 사용하도록 설정하지 않고 사용자 환경에서 PostgreSQL 데이터베이스를 감사하는 데 도움이 됩니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>PostgreSQL Database Server에 'connection_throtling' 서버 매개 변수가 'ON'으로 설정되어 있는지 확인

**ID**: CIS Azure 4.3.6 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL 데이터베이스 서버에 대해 연결 제한을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |이 정책은 연결 제한을 사용하도록 설정하지 않고 사용자 환경에서 PostgreSQL 데이터베이스를 감사하는 데 도움이 됩니다. 이 설정은 잘못된 암호 로그인 실패가 너무 많을 경우 IP당 임시 연결 제한을 사용하도록 설정합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Azure Active Directory 관리자가 구성되어 있는지 확인

**ID**: CIS Azure 4.4 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 서버에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |SQL 서버에 대한 Azure Active Directory 관리자 프로비전을 감사하여 Azure AD 인증을 활성화합니다. Azure AD 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-customer-managed-key"></a>SQL 서버의 TDE 보호기가 고객 관리형 키로 암호화되어 있는지 확인

**ID**: CIS Azure 4.5 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Managed Instance는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |자체 키를 사용하여 TDE(투명한 데이터 암호화)를 구현하면 TDE 보호기에 대한 투명성과 제어력이 향상되고, HSM 지원 외부 서비스를 통한 보안이 강화되며, 업무 분리 프로모션을 제공합니다. 이 권장 사항은 관련 규정 준수 요구 사항이 있는 조직에 적용됩니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL 서버는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |자체 키를 사용하여 TDE(투명한 데이터 암호화)를 구현하면 TDE 보호기에 대한 투명성과 제어력이 향상되고, HSM 지원 외부 서비스를 통해 보안이 강화되며, 업무 분리 프로모션을 제공합니다. 이 권장 사항은 관련 규정 준수 요구 사항이 있는 조직에 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>활동 로그를 저장하는 스토리지 컨테이너에 공개적으로 액세스할 수 없는지 확인

**ID**: CIS Azure 5.1.3 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[스토리지 계정 공용 액세스가 허용되지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Azure Storage의 컨테이너 및 BLOB에 대한 익명 공용 읽기 액세스는 데이터를 공유하는 편리한 방법이지만 보안 위험이 있을 수도 있습니다. 원치 않는 익명 액세스로 인해 발생하는 데이터 위반을 방지하기 위해 Microsoft는 시나리오에 필요한 경우가 아니면 스토리지 계정에 대한 공개 액세스를 방지하는 것이 좋습니다 |감사, 거부, 사용 안 함 |[3.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>활동 로그가 있는 컨테이너를 포함하는 스토리지 계정이 BYOK(사용자 고유 키 사용)로 암호화되어 있는지 확인

**ID**: CIS Azure 5.1.4 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[활동 로그가 있는 컨테이너를 포함하는 스토리지 계정은 BYOK로 암호화해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |이 정책은 활동 로그가 있는 컨테이너를 포함하는 스토리지 계정이 BYOK로 암호화되었는지 감사합니다. 이 정책은 스토리지 계정이 기본적으로 활동 로그와 같은 구독에 있는 경우에만 작동합니다. Azure Storage 저장 데이터 암호화에 대한 자세한 내용은 [https://aka.ms/azurestoragebyok](../../../storage/common/customer-managed-keys-configure-key-vault.md)를 참조하세요.  |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Azure KeyVault에 대한 로깅이 '사용'으로 설정되어 있는지 확인

**ID**: CIS Azure 5.1.5 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |리소스 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 사용할 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[5.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>정책 할당 만들기에 대한 활동 로그 경고가 있는지 확인

**ID**: CIS Azure 5.2.1 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[특정 정책 작업의 활동 로그 경고가 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |이 정책은 활동 로그 경고가 구성되지 않은 특정 정책 작업을 감사합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-policy-assignment"></a>정책 할당 삭제에 대한 활동 로그 경고가 있는지 확인

**ID**: CIS Azure 5.2.2 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[특정 정책 작업의 활동 로그 경고가 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |이 정책은 활동 로그 경고가 구성되지 않은 특정 정책 작업을 감사합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>네트워크 보안 그룹 만들기 또는 업데이트에 대한 활동 로그 경고가 존재하는지 확인

**ID**: CIS Azure 5.2.3 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[특정 관리 작업의 활동 로그 경고가 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |이 정책은 활동 로그 경고가 구성되지 않은 특정 관리 작업을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>네트워크 보안 그룹 삭제에 대한 활동 로그 경고가 존재하는지 확인

**ID**: CIS Azure 5.2.4 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[특정 관리 작업의 활동 로그 경고가 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |이 정책은 활동 로그 경고가 구성되지 않은 특정 관리 작업을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>네트워크 보안 그룹 규칙 만들기 또는 업데이트에 대한 활동 로그 경고가 존재하는지 확인

**ID**: CIS Azure 5.2.5 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[특정 관리 작업의 활동 로그 경고가 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |이 정책은 활동 로그 경고가 구성되지 않은 특정 관리 작업을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>네트워크 보안 그룹 규칙 삭제에 대한 활동 로그 경고가 존재하는지 확인

**ID**: CIS Azure 5.2.6 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[특정 관리 작업의 활동 로그 경고가 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |이 정책은 활동 로그 경고가 구성되지 않은 특정 관리 작업을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>보안 솔루션 만들기 또는 업데이트에 대한 활동 로그 경고가 존재하는지 확인

**ID**: CIS Azure 5.2.7 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[특정 보안 작업의 활동 로그 경고가 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |이 정책은 활동 로그 경고가 구성되지 않은 특정 보안 작업을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>보안 솔루션 삭제에 대한 활동 로그 경고가 존재하는지 확인

**ID**: CIS Azure 5.2.8 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[특정 보안 작업의 활동 로그 경고가 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |이 정책은 활동 로그 경고가 구성되지 않은 특정 보안 작업을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>SQL Server 방화벽 규칙 만들기 또는 업데이트에 대한 활동 로그 경고가 존재하는지 확인

**ID**: CIS Azure 5.2.9 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[특정 관리 작업의 활동 로그 경고가 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |이 정책은 활동 로그 경고가 구성되지 않은 특정 관리 작업을 감사합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-diagnostic-logs-are-enabled-for-all-services-which-support-it"></a>진단 로그를 지원하는 모든 서비스에 대해 사용할 수 있는지 확인합니다.

**ID**: CIS Azure 5.3 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Services의 진단 로그를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |앱에서 진단 로그 사용을 감사합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
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

## <a name="networking"></a>네트워킹

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>인터넷의 RDP 액세스가 제한되는지 확인

**ID**: CIS Azure 6.1 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[인터넷에서 RDP 액세스를 차단해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |이 정책은 인터넷에서 RDP 액세스를 허용하는 모든 네트워크 보안 규칙을 감사합니다. |감사, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>인터넷의 SSH 액세스가 제한되는지 확인

**ID**: CIS Azure 6.2 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[인터넷에서 SSH 액세스를 차단해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |이 정책은 인터넷에서 SSH 액세스를 허용하는 모든 네트워크 보안 규칙을 감사합니다. |감사, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Network Watcher가 '사용'으로 설정되어 있는지 확인

**ID**: CIS Azure 6.5 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. 시나리오 수준 모니터링을 사용하면 종단 간 네트워크 수준 보기에서 문제를 진단할 수 있습니다. 가상 네트워크가 있는 모든 지역에서 Network Watcher 리소스 그룹을 만들어야 합니다. 특정 지역에서 Network Watcher 리소스 그룹을 사용할 수 없는 경우 경고가 활성화됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtual Machines

### <a name="ensure-virtual-machines-are-utilizing-managed-disks"></a>Virtual Machines가 Managed Disks를 활용하는지 확인

**ID**: CIS Azure 7.1 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[관리 디스크를 사용하지 않는 VM 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |이 정책은 관리 디스크를 사용하지 않는 VM을 감사합니다. |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |

### <a name="ensure-that-os-and-data-disks-are-encrypted-with-cmk"></a>'OS 및 데이터' 디스크가 CMK로 암호화되어 있는지 확인

**ID**: CIS Azure 7.2 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[가상 머신은 Compute 및 Storage 리소스 간에 임시 디스크, 캐시 및 데이터 흐름을 암호화해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |기본적으로 가상 머신의 OS 및 데이터 디스크는 플랫폼 관리형 키를 사용하여 미사용 시 암호화됩니다. 임시 디스크, 데이터 캐시 및 컴퓨팅과 스토리지 간의 데이터 흐름은 암호화되지 않습니다. 다음과 같은 경우 이 권장 사항을 무시합니다. 1. 호스트에서 암호화를 사용하는 경우 또는 2. Managed Disks의 서버 쪽 암호화는 보안 요구 사항을 충족합니다. [Azure Disk Storage의 서버 쪽 암호화](../../../virtual-machines/disk-encryption.md) 및 [다양한 디스크 암호화 제품](../../../virtual-machines/disk-encryption-overview.md#comparison)에서 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[2.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted-with-cmk"></a>'연결되지 않은 디스크'가 CMK로 암호화되어 있는지 확인

**ID**: CIS Azure 7.3 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[연결되지 않은 디스크는 암호화되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |이 정책은 암호화를 사용하지 않고 연결되지 않은 모든 디스크를 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>승인된 확장만 설치되어 있는지 확인

**ID**: CIS Azure 7.4 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[승인된 VM 확장만 설치해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |이 정책은 승인되지 않은 가상 머신 확장을 관리합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>모든 Virtual Machines에 최신 OS 패치가 적용되어 있는지 확인

**ID**: CIS Azure 7.5 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[시스템 업데이트를 머신에 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |서버의 누락된 보안 시스템 업데이트는 Azure Security Center에서 권장 사항으로 모니터링합니다. |AuditIfNotExists, 사용 안 함 |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>모든 Virtual Machines에 대한 엔드포인트 보호가 설치되어 있는지 확인

**ID**: CIS Azure 7.6 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Security Center에서 누락된 Endpoint Protection 모니터링](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Endpoint Protection 에이전트가 설치되어 있지 않은 서버는 Azure Security Center에서 권장 사항으로 모니터링됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>기타 보안 고려 사항

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>모든 키에 만료 날짜가 설정되어 있는지 확인

**ID**: CIS Azure 8.1 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault 키에는 만료 날짜가 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |암호화 키에는 정의된 만료 날짜가 있어야 하며 영구적이지 않아야 합니다. 영구적으로 유효한 키는 잠재적인 공격자에게 키를 손상시킬 수 있는 시간을 더 많이 제공합니다. 보안상 암호화 키에 대한 만료 날짜를 설정하는 것이 좋습니다. |감사, 거부, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>모든 비밀에 만료 날짜가 설정되어 있는지 확인

**ID**: CIS Azure 8.2 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault 비밀에는 만료 날짜가 있어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |비밀에는 정의된 만료 날짜가 있어야 하며 영구적이지 않아야 합니다. 영구적으로 유효한 비밀은 잠재적인 공격자에게 비밀을 손상시킬 수 있는 시간을 더 많이 제공합니다. 보안상 비밀에 대한 만료 날짜를 설정하는 것이 좋습니다. |감사, 거부, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>키 자격 증명 모음을 복구할 수 있는지 확인

**ID**: CIS Azure 8.4 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[키 자격 증명 모음에 제거 방지를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |키 자격 증명 모음을 악의적으로 삭제하면 데이터가 영구적으로 손실될 수 있습니다. 조직의 악의적인 내부자가 잠재적으로 키 자격 증명 모음을 삭제하고 제거할 수 있습니다. 제거 보호는 일시 삭제된 키 자격 증명 모음에 대해 필수 보존 기간을 적용하여 내부자 공격으로부터 보호합니다. 일시 삭제 보존 기간 동안에는 조직 또는 Microsoft 내부의 어느 누구도 키 자격 증명 모음을 제거할 수 없습니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Azure Kubernetes Services 내에서 RBAC(역할 기반 액세스 제어) 사용

**ID**: CIS Azure 8.5 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kubernetes Services에서 RBAC(역할 기반 액세스 제어)를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |사용자가 수행할 수 있는 작업에 대한 세부적인 필터링을 제공하려면 RBAC(역할 기반 액세스 제어)를 사용하여 Kubernetes Service 클러스터에서 권한을 관리하고 관련 권한 부여 정책을 구성합니다. |감사, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="app-service"></a>App Service

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>App Service 인증이 Azure App Service에 설정되어 있는지 확인

**ID**: CIS Azure 9.1 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 앱에서 인증을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Azure App Service 인증은 익명 HTTP 요청이 API 앱에 도달하는 것을 방지하거나 API 앱에 도달하기 전에 토큰이 있는 요청을 인증할 수 있는 기능입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[함수 앱에서 인증을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Azure App Service 인증은 익명 HTTP 요청이 함수 앱에 도달하는 것을 방지하거나 함수 앱에 도달하기 전에 토큰이 있는 요청을 인증할 수 있는 기능입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[웹앱에서 인증을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Azure App Service 인증은 익명 HTTP 요청이 웹앱에 도달하는 것을 방지하거나 웹앱에 도달하기 전에 토큰이 있는 요청을 인증할 수 있는 기능입니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Azure App Service에서 웹앱이 모든 HTTP 트래픽을 HTTPS로 리디렉션하는지 확인

**ID**: CIS Azure 9.2 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[웹 애플리케이션은 HTTPS를 통해서만 액세스할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS를 사용하여 서버/서비스 인증을 보장하고 전송 중인 데이터를 네트워크 계층 도청 공격으로부터 보호합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>웹앱에서 최신 버전의 TLS 암호화를 사용하고 있는지 확인

**ID**: CIS Azure 9.3 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[함수 앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[웹앱에서 최신 TLS 버전을 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |최신 TLS 버전으로 업그레이드 |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>웹앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인

**ID**: CIS Azure 9.4 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |클라이언트 인증서는 앱이 들어오는 요청에 대한 인증서를 요청하도록 허용합니다. 유효한 인증서가 있는 클라이언트만 앱에 연결할 수 있습니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[웹앱에서 '클라이언트 인증서(들어오는 클라이언트 인증서)'가 '켜기'로 설정되어 있는지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |클라이언트 인증서는 앱이 들어오는 요청에 대한 인증서를 요청하도록 허용합니다. 유효한 인증서가 있는 클라이언트만 앱에 연결할 수 있습니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[함수 앱은 '클라이언트 인증서(들어오는 클라이언트 인증서)'를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |클라이언트 인증서는 앱이 들어오는 요청에 대한 인증서를 요청하도록 허용합니다. 유효한 인증서가 있는 클라이언트만 앱에 연결할 수 있습니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>App Service에 [Azure Active Directory에 등록]이 설정되어 있는지 확인

**ID**: CIS Azure 9.5 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 앱에서 관리 ID를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |인증 보안 강화를 위해 관리 ID 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[함수 앱에서 관리 ID를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |인증 보안 강화를 위해 관리 ID 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[웹앱에서 관리 ID를 사용해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |인증 보안 강화를 위해 관리 ID 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>웹앱을 실행하는 데 사용되는 경우 최신 'PHP 버전'인지 확인

**ID**: CIS Azure 9.6 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |보안 결함이 있거나 추가 기능을 포함하기 위해 PHP 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 API Apps에 최신 PHP 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[웹앱의 일부로 사용되는 경우 최신의 'PHP 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |보안 결함이 있거나 추가 기능을 포함하기 위해 PHP 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 PHP 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>웹앱을 실행하는 데 사용되는 경우 최신 'Python 버전'인지 확인

**ID**: CIS Azure 9.7 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |보안 결함이 있거나 추가 기능을 포함하기 위해 Python 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 API 앱에 최신 Python 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[함수 앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |보안 결함이 있거나 추가 기능을 포함하기 위해 Python 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 함수 앱에 최신 Python 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[웹앱의 일부로 사용되는 경우 최신의 'Python 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |보안 결함이 있거나 추가 기능을 포함하기 위해 Python 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 Python 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>웹앱을 실행하는 데 사용되는 경우 최신 'Java 버전'인지 확인

**ID**: CIS Azure 9.8 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |보안 결함이 있거나 추가 기능을 포함하기 위해 Java에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 API 앱에 최신 Python 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[함수 앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |보안 결함이 있거나 추가 기능을 포함하기 위해 Java 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 함수 앱에 최신 Java 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[웹앱의 일부로 사용되는 경우 최신의 'Java 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |보안 결함이 있거나 추가 기능을 포함하기 위해 Java 소프트웨어에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 Java 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>웹앱을 실행하는 데 사용되는 경우 최신 'HTTP 버전'인지 확인

**ID**: CIS Azure 9.9 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |보안 결함이 있거나 추가 기능을 포함하기 위해 HTTP에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 HTTP 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[함수 앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |보안 결함이 있거나 추가 기능을 포함하기 위해 HTTP에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 HTTP 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[웹앱을 실행하는 데 사용되는 경우 최신의 'HTTP 버전'인지 확인합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |보안 결함이 있거나 추가 기능을 포함하기 위해 HTTP에 대한 최신 버전이 주기적으로 릴리스됩니다. 최신 버전의 보안 픽스(있는 경우) 및/또는 새로운 기능을 활용하려면 웹앱에 최신 HTTP 버전을 사용하는 것이 좋습니다. 현재 이 정책은 Linux 웹앱에만 적용됩니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

### <a name="ensure-ftp-deployments-are-disabled"></a>FTP 배포가 비활성화되었는지 확인

**ID**: CIS Azure 9.10 **소유권**: Customer

|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 앱에서 FTPS만 요구해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |강화된 보안을 위해 FTPS 적용 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[함수 앱에서 FTPS만 요구해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |강화된 보안을 위해 FTPS 적용 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[웹앱에서 FTPS를 요구해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |강화된 보안을 위해 FTPS 적용 사용 |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |

## <a name="next-steps"></a>다음 단계

Azure Policy에 대한 추가 문서:

- [규정 준수](../concepts/regulatory-compliance.md) 개요
- [이니셔티브 정의 구조](../concepts/initiative-definition-structure.md)를 참조합니다.
- [Azure Policy 샘플](./index.md)의 다른 예제를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.