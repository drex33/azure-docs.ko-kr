---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 0b3d249f64e51e90dff7b67eb95f4f5ed534dfd2
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123123570"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Managed Instance에서 취약성 평가를 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |반복 취약성 평가 검사를 사용하도록 설정하지 않은 각 SQL Managed Instance를 감사합니다. 취약성 평가는 잠재적 데이터베이스 취약성을 검색 및 추적할 수 있고 해결하는 데 도움이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |반복 취약성 평가 검사를 사용하도록 설정하지 않은 Azure SQL 서버를 감사합니다. 취약성 평가는 잠재적 데이터베이스 취약성을 검색 및 추적할 수 있고 해결하는 데 도움이 될 수 있습니다. |AuditIfNotExists, 사용 안 함 |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
