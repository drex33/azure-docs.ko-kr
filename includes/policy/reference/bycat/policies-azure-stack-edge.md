---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7e4b98d74ff0f0a4b6ca3739d4e5423a51f594ee
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102739"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Stack Edge 디바이스는 이중 암호화를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |디바이스에 저장된 데이터를 보호하려면 데이터가 이중 암호화되고, 데이터에 대한 액세스가 제어되고, 디바이스가 비활성화되면 데이터를 데이터 디스크에서 안전하게 삭제해야 합니다. 이중 암호화는 두 계층의 암호화를 사용하는 것입니다. 데이터 볼륨에 대한 BitLocker XTS-AES 256비트 암호화 및 하드 드라이브의 기본 제공 암호화. 특정 Stack Edge 디바이스에 대한 보안 개요 설명서에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
