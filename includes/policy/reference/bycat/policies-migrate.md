---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 564572de5af1a07d7a146e90b047f9ecc9158612
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123477284"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[프라이빗 DNS 영역을 사용하도록 Azure Migrate 리소스 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7590a335-57cf-4c95-babd-ecbc8fafeb1f) |프라이빗 DNS 영역을 사용하여 프라이빗 엔드포인트에 대한 DNS 확인을 재정의합니다. 프라이빗 DNS 영역은 가상 네트워크에 연결되어 Azure Migrate 프로젝트를 확인합니다. [https://aka.ms/privatednszone](../../../../articles/private-link/private-endpoint-dns.md)에서 자세히 알아보세요. |DeployIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Migrate/Migrate_PrivateDNSZone_DeployIfNotExists.json) |