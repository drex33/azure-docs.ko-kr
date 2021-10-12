---
author: georgewallace
ms.service: azure-policy
ms.topic: include
ms.date: 10/11/2021
ms.author: gwallace
ms.custom: generated
ms.openlocfilehash: 4ef70e935301533d46ac29c71ea8dfb840229724
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129809099"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[프라이빗 DNS 영역을 사용하도록 Azure Migrate 리소스 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7590a335-57cf-4c95-babd-ecbc8fafeb1f) |프라이빗 DNS 영역을 사용하여 프라이빗 엔드포인트에 대한 DNS 확인을 재정의합니다. 프라이빗 DNS 영역은 가상 네트워크에 연결되어 Azure Migrate 프로젝트를 확인합니다. [https://aka.ms/privatednszone](../../../../articles/private-link/private-endpoint-dns.md)에서 자세히 알아보세요. |DeployIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Migrate/Migrate_PrivateDNSZone_DeployIfNotExists.json) |