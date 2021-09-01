---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d7762783cc7a3f5bbed85089a8b08f5a22c98341
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123123351"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |애플리케이션 제어를 활성화하여 머신에서 실행 중인 알려진 안전한 애플리케이션 목록을 정의하고, 다른 애플리케이션이 실행될 때 경고합니다. 이렇게 하면 맬웨어로부터 머신을 강화할 수 있습니다. 규칙을 구성하고 유지 관리하는 프로세스를 간소화하기 위해 Security Center는 기계 학습을 사용하여 각 머신에서 실행 중인 애플리케이션을 분석하고 알려진 안전한 애플리케이션의 목록을 제안합니다. |AuditIfNotExists, 사용 안 함 |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
