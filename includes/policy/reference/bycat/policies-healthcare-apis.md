---
author: georgewallace
ms.service: azure-policy
ms.topic: include
ms.date: 10/11/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 6614e9ae7a83e897d1c52d5ccc1f540247b8573a
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129810559"
---
|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS에서 모든 도메인이 FHIR Service에 액세스하도록 허용하지 않아야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffe1c9040-c46a-4e81-9aea-c7850fbb3aa6) |CORS(교차 원본 리소스 공유)는 FHIR Service에 액세스하는 모든 도메인을 허용하지 않아야 합니다. FHIR Service를 보호하려면 모든 도메인에 대한 액세스를 제거하고 연결할 수 있는 도메인을 명시적으로 정의합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Healthcare%20APIs/HealthcareAPIs_FHIR_Service_RestrictCORSAccess_Audit.json) |
