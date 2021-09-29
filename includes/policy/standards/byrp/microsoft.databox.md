---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d8bc1fca7cf0b47fa018c95055103f3c3ef9eab6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553870"
---
## <a name="cmmc-level-3"></a>CMMC Level 3

모든 Azure 서비스에 사용 가능한 Azure Policy 기본 제공이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - CMMC Level 3](../../../../articles/governance/policy/samples/cmmc-l3.md)을 참조하세요.
이 규정 준수 표준에 대한 자세한 내용은 [CMMC(사이버 보안 완성 모델 인증)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|시스템 및 통신 보호 |SC.3.177 |CUI의 기밀성을 보호하기 위해 사용되는 경우 FIPS 인증 암호화를 사용합니다. |[Azure Data Box 작업에서 디바이스의 미사용 데이터에 대한 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|시스템 및 통신 보호 |SC.3.191 |휴지 상태의 CUI 비밀을 보호합니다. |[Azure Data Box 작업에서 디바이스의 미사용 데이터에 대한 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |

## <a name="fedramp-high"></a>FedRAMP High

모든 Azure 서비스에서 사용할 수 있는 Azure Policy 기본 제공 사항이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - FedRAMP High](../../../../articles/governance/policy/samples/fedramp-high.md)를 참조하세요.
이 규정 준수 표준에 관한 자세한 내용은 [FedRAMP High](https://www.fedramp.gov/)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|시스템 및 통신 보호 |SC-12 |암호화 키 설정 및 관리 |[Azure Data Box 작업에서 고객 관리형 키를 사용하여 디바이스 잠금 해제 암호를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
|시스템 및 통신 보호 |SC-28 |미사용 정보 보호 |[Azure Data Box 작업에서 디바이스의 미사용 데이터에 대한 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|시스템 및 통신 보호 |SC-28(1) |암호화 보호 |[Azure Data Box 작업에서 디바이스의 미사용 데이터에 대한 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |

## <a name="fedramp-moderate"></a>FedRAMP Moderate

모든 Azure 서비스에서 사용할 수 있는 Azure Policy 기본 제공 사항이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - FedRAMP Moderate](../../../../articles/governance/policy/samples/fedramp-moderate.md)를 참조하세요.
이 규정 준수 표준에 관한 자세한 내용은 [FedRAMP Moderate](https://www.fedramp.gov/)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|시스템 및 통신 보호 |SC-12 |암호화 키 설정 및 관리 |[Azure Data Box 작업에서 고객 관리형 키를 사용하여 디바이스 잠금 해제 암호를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
|시스템 및 통신 보호 |SC-28 |미사용 정보 보호 |[Azure Data Box 작업에서 디바이스의 미사용 데이터에 대한 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|시스템 및 통신 보호 |SC-28(1) |암호화 보호 |[Azure Data Box 작업에서 디바이스의 미사용 데이터에 대한 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |

## <a name="nist-sp-800-53-rev-4"></a>NIST SP 800-53 Rev. 4

모든 Azure 서비스에서 사용할 수 있는 Azure Policy 기본 제공 사항이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - NIST SP 800-53 Rev. 4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md)를 참조하세요.
이 규정 준수 표준에 관한 자세한 내용은 [NIST SP 800-53 Rev. 4](https://nvd.nist.gov/800-53)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|시스템 및 통신 보호 |SC-12 |암호화 키 설정 및 관리 |[Azure Data Box 작업에서 고객 관리형 키를 사용하여 디바이스 잠금 해제 암호를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
|시스템 및 통신 보호 |SC-28 |미사용 정보 보호 |[Azure Data Box 작업에서 디바이스의 미사용 데이터에 대한 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|시스템 및 통신 보호 |SC-28(1) |암호화 보호 |[Azure Data Box 작업에서 디바이스의 미사용 데이터에 대한 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |

## <a name="nist-sp-800-53-rev-5"></a>NIST SP 800-53 Rev. 5

모든 Azure 서비스에서 사용할 수 있는 Azure Policy 기본 제공 사항이 이 규정 준수 표준에 매핑되는 방식을 검토하려면 [Azure Policy 규정 준수 - NIST SP 800-53 Rev. 5](../../../../articles/governance/policy/samples/nist-sp-800-53-r5.md)를 참조하세요.
이 규정 준수 표준에 관한 자세한 내용은 [NIST SP 800-53 Rev. 5](https://nvd.nist.gov/800-53)를 참조하세요.

|도메인 |컨트롤 ID |컨트롤 제목 |정책<br /><sub>(Azure Portal)</sub> |정책 버전<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|시스템 및 통신 보호 |SC-12 |암호화 키 설정 및 관리 |[Azure Data Box 작업에서 고객 관리형 키를 사용하여 디바이스 잠금 해제 암호를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
|시스템 및 통신 보호 |SC-28 |미사용 정보 보호 |[Azure Data Box 작업에서 디바이스의 미사용 데이터에 대한 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|시스템 및 통신 보호 |SC-28(1) |암호화 보호 |[Azure Data Box 작업에서 디바이스의 미사용 데이터에 대한 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |

