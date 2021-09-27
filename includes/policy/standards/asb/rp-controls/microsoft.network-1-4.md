---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c14fe6cf6cebd7f086314e2b6d27f8070e41f333
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128908969"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[모든 인터넷 트래픽은 배포된 Azure Firewall을 통해 라우팅되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffc5e4038-4584-4632-8c85-c0448d374b2c) |Azure Security Center에서 일부 서브넷이 차세대 방화벽으로 보호되지 않는 것으로 확인되었습니다. Azure Firewall 또는 지원되는 차세대 방화벽으로 액세스를 제한하여 잠재적인 위협으로부터 서브넷을 보호합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ASC_All_Internet_traffic_should_be_routed_via_Azure_Firewall.json) |
|[Azure DDoS Protection 표준을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7aca53f-2ed4-4466-a25e-0b45ade68efd) |공용 IP를 사용하는 애플리케이션 게이트웨이의 일부인 서브넷이 포함된 모든 가상 네트워크에 DDoS 보호 표준을 사용하도록 설정해야 합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |
