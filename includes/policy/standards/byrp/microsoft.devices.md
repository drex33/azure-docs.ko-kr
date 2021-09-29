---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b827789122ff2517f7ceb99ea939a83eed983784
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622622"
---
## <a name="azure-security-benchmark"></a>Azure 보안 벤치마크

[Azure Security Benchmark](/security/benchmark/azure/introduction)는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 이 서비스가 Azure Security Benchmark에 완전히 매핑되는 방법을 보려면 [Azure Security Benchmark 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|로깅 및 위협 탐지 |LT-4 |Azure 리소스에 대한 로깅 사용 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure Security Benchmark v1

[Azure Security Benchmark](/security/benchmark/azure/introduction)는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 이 서비스가 Azure Security Benchmark에 완전히 매핑되는 방법을 보려면 [Azure Security Benchmark 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|로깅 및 모니터링 |2.3 |Azure 리소스에 대한 감사 로깅 사용 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark-130"></a>CIS Microsoft Azure Foundations Benchmark 1.3.0

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - CIS Microsoft Azure Foundations Benchmark 1.3.0](../../../../articles/governance/policy/samples/cis-azure-1-3-0.md)을 참조하세요.
이러한 규정 준수 표준에 대한 자세한 내용은 [CIS Microsoft Azure Foundations 벤치마크](https://www.cisecurity.org/benchmark/azure/)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|로깅 및 모니터링 |5.3 |진단 로그를 지원하는 모든 서비스에 대해 사용할 수 있는지 확인합니다. |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC Level 3

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - CMMC Level 3](../../../../articles/governance/policy/samples/cmmc-l3.md)을 참조하세요.
이 규정 준수 표준에 대한 자세한 내용은 [CMMC(사이버 보안 완성 모델 인증)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|감사 및 책임 |AU.3.048 |감사 정보(예: 로그)를 하나 이상의 중앙 리포지토리로 수집합니다. |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

## <a name="fedramp-high"></a>FedRAMP High

모든 Azure 서비스에서 사용할 수 있는 Azure Policy 기본 제공 사항이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - FedRAMP High](../../../../articles/governance/policy/samples/fedramp-high.md)를 참조하세요.
이 규정 준수 표준에 관한 자세한 내용은 [FedRAMP High](https://www.fedramp.gov/)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Access Control |AC-4 |정보 흐름 적용 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|Access Control |AC-17 |원격 액세스 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|Access Control |AC-17(1) |자동화된 모니터링/제어 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|감사 및 책임 |AU-6(4) |중앙 검토 및 분석 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|감사 및 책임 |AU-6(5) |통합/검사 및 모니터링 기능 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|감사 및 책임 |AU-12 |감사 생성 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|감사 및 책임 |AU-12(1) |시스템 수준/시간 관련 감사 내역 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|시스템 및 통신 보호 |SC-7 |경계 보호 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|시스템 및 통신 보호 |SC-7(3) |액세스 지점 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|시스템 및 통신 보호 |SC-12 |암호화 키 설정 및 관리 |[IoT Hub Device Provisioning Service 데이터는 CMK(고객 관리형 키)를 사용하여 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47031206-ce96-41f8-861b-6a915f3de284) |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_CMKEncryptionEnabled_AuditDeny.json) |

## <a name="fedramp-moderate"></a>FedRAMP Moderate

모든 Azure 서비스에서 사용할 수 있는 Azure Policy 기본 제공 사항이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - FedRAMP Moderate](../../../../articles/governance/policy/samples/fedramp-moderate.md)를 참조하세요.
이 규정 준수 표준에 관한 자세한 내용은 [FedRAMP Moderate](https://www.fedramp.gov/)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Access Control |AC-4 |정보 흐름 적용 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|Access Control |AC-17 |원격 액세스 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|Access Control |AC-17(1) |자동화된 모니터링/제어 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|감사 및 책임 |AU-12 |감사 생성 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|시스템 및 통신 보호 |SC-7 |경계 보호 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|시스템 및 통신 보호 |SC-7(3) |액세스 지점 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|시스템 및 통신 보호 |SC-12 |암호화 키 설정 및 관리 |[IoT Hub Device Provisioning Service 데이터는 CMK(고객 관리형 키)를 사용하여 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47031206-ce96-41f8-861b-6a915f3de284) |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_CMKEncryptionEnabled_AuditDeny.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)를 참조하세요.
이 규정 준수 표준에 대한 자세한 내용은 [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|감사 로깅 |1204.09aa1System.3 - 09.aa |권한 있는 사용자(관리자, 운영자 등)의 활동에는 이벤트의 성공/실패, 이벤트가 발생한 시간, 관련된 계정, 관련된 프로세스 및 이벤트에 대한 추가 정보가 포함됩니다. |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

## <a name="new-zealand-ism-restricted"></a>뉴질랜드 ISM 제한됨

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - 뉴질랜드 ISM 제한됨](../../../../articles/governance/policy/samples/new-zealand-ism.md)을 참조하세요.
이 규정 준수 표준에 대한 자세한 내용은 [뉴질랜드 ISM 제한됨](https://www.nzism.gcsb.govt.nz/)을 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|액세스 제어 및 암호 |AC-17 |16.6.9 기록할 이벤트 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

## <a name="nist-sp-800-53-rev-4"></a>NIST SP 800-53 Rev. 4

모든 Azure 서비스에서 사용할 수 있는 Azure Policy 기본 제공 사항이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - NIST SP 800-53 Rev. 4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md)를 참조하세요.
이 규정 준수 표준에 관한 자세한 내용은 [NIST SP 800-53 Rev. 4](https://nvd.nist.gov/800-53)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Access Control |AC-4 |정보 흐름 적용 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|Access Control |AC-17 |원격 액세스 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|Access Control |AC-17(1) |자동화된 모니터링/제어 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|감사 및 책임 |AU-6(4) |중앙 검토 및 분석 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|감사 및 책임 |AU-6(5) |통합/검사 및 모니터링 기능 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|감사 및 책임 |AU-12 |감사 생성 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|감사 및 책임 |AU-12(1) |시스템 수준/시간 관련 감사 내역 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|시스템 및 통신 보호 |SC-7 |경계 보호 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|시스템 및 통신 보호 |SC-7(3) |액세스 지점 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|시스템 및 통신 보호 |SC-12 |암호화 키 설정 및 관리 |[IoT Hub Device Provisioning Service 데이터는 CMK(고객 관리형 키)를 사용하여 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47031206-ce96-41f8-861b-6a915f3de284) |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_CMKEncryptionEnabled_AuditDeny.json) |

## <a name="nist-sp-800-53-rev-5"></a>NIST SP 800-53 Rev. 5

모든 Azure 서비스에서 사용할 수 있는 Azure Policy 기본 제공 사항이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - NIST SP 800-53 Rev. 5](../../../../articles/governance/policy/samples/nist-sp-800-53-r5.md)를 참조하세요.
이 규정 준수 표준에 관한 자세한 내용은 [NIST SP 800-53 Rev. 5](https://nvd.nist.gov/800-53)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Access Control |AC-4 |정보 흐름 적용 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|Access Control |AC-17 |원격 액세스 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|Access Control |AC-17(1) |모니터링 및 제어 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|감사 및 책임 |AU-6(4) |중앙 검토 및 분석 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|감사 및 책임 |AU-6(5) |감사 레코드의 통합 분석 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|감사 및 책임 |AU-12 |감사 레코드 생성 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|감사 및 책임 |AU-12(1) |시스템 전체 및 시간 관련 감사 내역 |[IoT Hub에서 리소스 로그를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|시스템 및 통신 보호 |SC-7 |경계 보호 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|시스템 및 통신 보호 |SC-7(3) |액세스 지점 |[IoT Hub 디바이스 프로비저닝 서비스 인스턴스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf39c015-56a4-45de-b4a3-efe77bed320d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_EnablePrivateEndpoint_Audit.json) |
|시스템 및 통신 보호 |SC-12 |암호화 키 설정 및 관리 |[IoT Hub Device Provisioning Service 데이터는 CMK(고객 관리형 키)를 사용하여 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47031206-ce96-41f8-861b-6a915f3de284) |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTDps_CMKEncryptionEnabled_AuditDeny.json) |

