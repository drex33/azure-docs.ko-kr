---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e1f163c0d13e19e04b09f433188ddf70fad74e43
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123474050"
---
## <a name="azure-security-benchmark"></a>Azure 보안 벤치마크

[Azure Security Benchmark](/security/benchmark/azure/introduction)는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 이 서비스가 Azure Security Benchmark에 완전히 매핑되는 방법을 보려면 [Azure Security Benchmark 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|백업 및 복구 |BR-1 |정기적인 자동 백업 보장 |[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|백업 및 복구 |BR-2 |백업 데이터 암호화 |[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure Security Benchmark v1

[Azure Security Benchmark](/security/benchmark/azure/introduction)는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 이 서비스가 Azure Security Benchmark에 완전히 매핑되는 방법을 보려면 [Azure Security Benchmark 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|데이터 복구 |9.1 |자동화된 정기 백업 보장 |[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|데이터 복구 |9.2 |전체 시스템 백업 수행 및 고객 관리형 키 백업 |[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC Level 3

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - CMMC Level 3](../../../../articles/governance/policy/samples/cmmc-l3.md)을 참조하세요.
이 규정 준수 표준에 대한 자세한 내용은 [CMMC(사이버 보안 완성 모델 인증)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|복구 |RE.2.137 |데이터 백업을 정기적으로 수행하고 테스트합니다. |[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|복구 |RE.3.139 |조직에서 정의한 대로 완전하고, 종합적이며, 복원력 있는 데이터 백업을 정기적으로 수행합니다. |[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="fedramp-high"></a>FedRAMP High

모든 Azure 서비스에서 사용할 수 있는 Azure Policy 기본 제공 사항이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - FedRAMP High](../../../../articles/governance/policy/samples/fedramp-high.md)를 참조하세요.
이 규정 준수 표준에 관한 자세한 내용은 [FedRAMP High](https://www.fedramp.gov/)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|대체 계획 |CP-9 |정보 시스템 백업 |[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|시스템 및 통신 보호 |SC-12 |암호화 키 설정 및 관리 |[Azure Recovery Services 자격 증명 모음에서 백업 데이터를 암호화하려면 고객 관리형 키를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e94d99a-8a36-4563-bc77-810d8893b671) |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/AzBackupRSVault_CMKEnabled_Audit.json) |

## <a name="fedramp-moderate"></a>FedRAMP Moderate

모든 Azure 서비스에서 사용할 수 있는 Azure Policy 기본 제공 사항이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - FedRAMP Moderate](../../../../articles/governance/policy/samples/fedramp-moderate.md)를 참조하세요.
이 규정 준수 표준에 관한 자세한 내용은 [FedRAMP Moderate](https://www.fedramp.gov/)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|대체 계획 |CP-9 |정보 시스템 백업 |[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|시스템 및 통신 보호 |SC-12 |암호화 키 설정 및 관리 |[Azure Recovery Services 자격 증명 모음에서 백업 데이터를 암호화하려면 고객 관리형 키를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e94d99a-8a36-4563-bc77-810d8893b671) |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/AzBackupRSVault_CMKEnabled_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)를 참조하세요.
이 규정 준수 표준에 대한 자세한 내용은 [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|백업 |1620.09l1Organizational.8 - 09.l |타사가 백업 서비스를 제공하는 경우 서비스 수준 계약에는 백업 정보의 기밀성, 무결성 및 가용성을 제어하는 세부 보호 기능이 포함됩니다. |[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|백업 |1625.09l3Organizational.34 - 09.l |3세대 백업(전체 및 모든 관련 증분 또는 차등 백업)은 오프사이트에 저장되며, 온사이트 백업과 오프사이트 백업 모두 이름, 날짜, 시간 및 작업으로 기록됩니다. |[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|백업 |1699.09l1Organizational.10 - 09.l |데이터 백업 프로세스에서 직원 멤버의 역할과 책임을 식별하여 직원에게 전달합니다. 특히 BYOD(Bring Your Own Device) 사용자는 자신의 디바이스에서 조직 및/또는 클라이언트 데이터의 백업을 수행해야 합니다. |[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="nist-sp-800-53-rev-4"></a>NIST SP 800-53 Rev. 4

모든 Azure 서비스에서 사용할 수 있는 Azure Policy 기본 제공 사항이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - NIST SP 800-53 Rev. 4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md)를 참조하세요.
이 규정 준수 표준에 관한 자세한 내용은 [NIST SP 800-53 Rev. 4](https://nvd.nist.gov/800-53)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|대체 계획 |CP-9 |정보 시스템 백업 |[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|시스템 및 통신 보호 |SC-12 |암호화 키 설정 및 관리 |[Azure Recovery Services 자격 증명 모음에서 백업 데이터를 암호화하려면 고객 관리형 키를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e94d99a-8a36-4563-bc77-810d8893b671) |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/AzBackupRSVault_CMKEnabled_Audit.json) |

## <a name="nist-sp-800-53-rev-5"></a>NIST SP 800-53 Rev. 5

모든 Azure 서비스에서 사용할 수 있는 Azure Policy 기본 제공 사항이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - NIST SP 800-53 Rev. 5](../../../../articles/governance/policy/samples/nist-sp-800-53-r5.md)를 참조하세요.
이 규정 준수 표준에 관한 자세한 내용은 [NIST SP 800-53 Rev. 5](https://nvd.nist.gov/800-53)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|대체 계획 |CP-9 |시스템 백업: |[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|시스템 및 통신 보호 |SC-12 |암호화 키 설정 및 관리 |[Azure Recovery Services 자격 증명 모음에서 백업 데이터를 암호화하려면 고객 관리형 키를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2e94d99a-8a36-4563-bc77-810d8893b671) |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/AzBackupRSVault_CMKEnabled_Audit.json) |

