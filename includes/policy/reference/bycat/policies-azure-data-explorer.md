---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f9759b4f6359328c7edea51582ac02caa890a8a6
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123477092"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Explorer 저장 데이터 암호화는 고객 관리형 키를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F81e74cea-30fd-40d5-802f-d72103c2aaaa) |Azure Data Explorer 클러스터에서 고객 관리형 키를 사용하여 저장 데이터 암호화를 활성화하면 저장 데이터 암호화에 사용되는 키를 추가로 제어할 수 있습니다. 이 기능은 종종 특별한 규정 준수 요구 사항을 충족하는 고객에게 적용되며, 키 관리를 위해 Key Vault가 필요합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_CMK.json) |
|[Azure Data Explorer에서 디스크 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4b53539-8df9-40e4-86c6-6b607703bd4e) |디스크 암호화를 사용하도록 설정하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_disk_encrypted.json) |
|[Azure Data Explorer에서 이중 암호화를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fec068d99-e9c7-401f-8cef-5bdde4e6ccf1) |이중 암호화를 사용하도록 설정하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. 이중 암호화를 사용하도록 설정하면 스토리지 계정의 데이터가 두 개의 다른 암호화 알고리즘과 두 개의 다른 키를 사용하여 두 번 암호화됩니다(서비스 수준에서 한 번, 인프라 수준에서 한 번). |감사, 거부, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_doubleEncryption.json) |
|[Azure Data Explorer에 대해 가상 네트워크 삽입을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9ad2fd1f-b25f-47a2-aa01-1a5a779e6413) |네트워크 보안 그룹 규칙을 적용하고, 온-프레미스에 연결하고, 서비스 엔드포인트를 사용하여 데이터 연결 원본을 보호할 수 있는 가상 네트워크 삽입으로 네트워크 경계를 보호합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Data%20Explorer/ADX_VNET_configured.json) |
