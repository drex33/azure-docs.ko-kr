---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7730da58426b63d0f562574fc837c5fc18da7f84
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123122082"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[구독에 Log Analytics 에이전트의 자동 프로비저닝을 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |보안 취약성 및 위협을 모니터링하기 위해 Azure Security Center는 Azure 가상 머신에서 데이터를 수집합니다. 데이터는 이전에 MMA(Microsoft Monitoring Agent)로 알려진 Log Analytics 에이전트에 의해 수집되며, 머신에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석을 위해 Log Analytics 작업 영역에 데이터를 복사합니다. 지원되는 모든 Azure VM과 생성된 모든 새 VM에 에이전트를 자동으로 배포하려면 자동 프로비저닝을 사용하도록 설정하는 것이 좋습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |
