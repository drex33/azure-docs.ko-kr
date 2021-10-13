---
author: georgewallace
ms.service: azure-policy
ms.topic: include
ms.date: 10/11/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 31ccc103ca780fd554ef5b832125818af8753439
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129810251"
---
|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Attestation 공급자는 프라이빗 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7b256a2d-058b-41f8-bed9-3f870541c40a) |프라이빗 엔드포인트는 공용 인터넷을 통해 트래픽을 보내지 않고 Azure Attestation 공급자를 Azure 리소스에 연결하는 방법을 제공합니다. 공용 액세스를 방지함으로써 프라이빗 엔드포인트는 원치 않는 익명 액세스로부터 보호합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Attestation/Attestation_PrivateLink_AuditIfNotExists.json) |
