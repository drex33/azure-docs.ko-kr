---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 01491e12aeff8424e36305cc94884e3559d3f946
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123103494"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Box 작업에서 디바이스의 미사용 데이터에 대한 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |디바이스의 미사용 데이터에 대해 소프트웨어 기반 암호화의 두 번째 계층을 사용하도록 설정합니다. 디바이스는 미사용 데이터에 대해 Advanced Encryption Standard 256비트 암호화를 통해 이미 보호되고 있습니다. 이 옵션은 데이터 암호화의 두 번째 계층을 추가합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Data Box 작업에서 고객 관리형 키를 사용하여 디바이스 잠금 해제 암호를 암호화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |고객 관리형 키를 사용하여 Azure Data Box에 대한 디바이스 잠금 해제 암호의 암호화를 제어합니다. 고객 관리형 키는 디바이스를 준비하고 자동화된 방식으로 데이터를 복사하기 위해 Data Box 서비스에서 디바이스 잠금 해제 암호에 대한 액세스를 관리하는 데도 도움이 됩니다. 디바이스 자체의 데이터는 Advanced Encryption Standard 256비트 암호화를 사용하여 미사용 상태로 이미 암호화되어 있으며, 디바이스 잠금 해제 암호는 기본적으로 Microsoft 관리형 키를 사용하여 암호화됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
