---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 296755de12c837bdf98c09151a043956bfd0359e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910714"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Log Analytics 에이전트가 예상대로 연결되지 않은 Windows 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |필수 구성 요소가 정책 할당 범위에 배포되어야 합니다. 자세한 내용은 [https://aka.ms/gcpol](../../../../../articles/governance/policy/concepts/guest-configuration.md)을 방문하세요. 에이전트가 설치되지 않았거나 설치되었지만 정책 매개 변수에 지정된 ID 이외의 작업 영역에 등록된 COM 개체 AgentConfigManager.MgmtSvcCfg가 반환되는 경우 머신은 비규격입니다. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |
|[Virtual Machine Scale Sets에 Log Analytics 에이전트를 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |이 정책은 모든 Windows/Linux Virtual Machine Scale Sets를 감사하여 Log Analytics 에이전트가 설치되어 있지 않은지 확인합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |
|[가상 머신에 Log Analytics 에이전트를 설치해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |이 정책은 모든 Windows/Linux 가상 머신을 감사하여 Log Analytics 에이전트가 설치되어 있지 않은지 확인합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |