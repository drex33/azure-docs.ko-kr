---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0aef5f369d2d0acff6336cf189bedae8919c1d31
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655175"
---
|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Container Instance 컨테이너 그룹은 가상 네트워크에 배포해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8af8f826-edcb-4178-b35f-851ea6fea615) |Azure Virtual Network를 사용하여 컨테이너 간 통신을 보호합니다. 가상 네트워크를 지정하면 가상 네트워크 내의 리소스가 서로 안전하게 비공개적으로 통신할 수 있습니다. |감사, 사용 안 함, 거부 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_VNET_Audit.json) |
|[Azure Container Instance 컨테이너 그룹은 암호화에 고객 관리형 키를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0aa61e00-0a01-4a3c-9945-e93cffedf0e6) |고객 관리형 키를 사용하여 유연성이 뛰어난 컨테이너를 보호합니다. 고객 관리형 키를 지정하는 경우 해당 키는 데이터를 암호화하는 키에 대한 액세스를 보호하고 제어하는 데 사용됩니다. 고객 관리형 키를 사용하면 키 암호화 키의 회전을 제어하거나 데이터를 암호화 방식으로 지우는 추가 기능이 제공됩니다. |감사, 사용 안 함, 거부 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Instance/ContainerInstance_CMK_Audit.json) |
