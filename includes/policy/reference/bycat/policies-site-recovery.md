---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 88ca0edcf06e83a3baaf7cecd6bae018af7d5edd
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123477316"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[\[미리 보기\]: 프라이빗 DNS 영역 사용을 위해 Azure Recovery Services 자격 증명 모음 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F942bd215-1a66-44be-af65-6a1c0318dbe2) |프라이빗 DNS 영역을 사용하여 프라이빗 엔드포인트에 대한 DNS 확인을 재정의합니다. 프라이빗 DNS 영역은 가상 네트워크에 연결되어 Recovery Services 자격 증명 모음으로 확인됩니다. [https://aka.ms/privatednszone](../../../../articles/private-link/private-endpoint-dns.md)에서 자세히 알아보세요. |DeployIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Site%20Recovery/SiteRecovery_PrivateDnsZones_DeployIfNotExist.json) |
|[\[미리 보기\]: Azure Recovery Services 자격 증명 모음에서 프라이빗 엔드포인트 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe95a8a5c-0987-421f-84ab-df4d88ebf7d1) |프라이빗 엔드포인트는 원본 또는 대상에서 공용 IP 주소 없이 Azure 서비스에 가상 네트워크를 연결합니다. 프라이빗 엔드포인트를 Recovery Services 자격 증명 모음의 사이트 복구 리소스에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. 프라이빗 링크를 사용하려면 Recovery Services 자격 증명 모음에 관리 서비스 ID를 할당해야 합니다. [https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-private-endpoints](../../../../articles/site-recovery/azure-to-azure-how-to-enable-replication-private-endpoints.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |DeployIfNotExists, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Site%20Recovery/SiteRecovery_PrivateEndpoints_DeployIfNotExist.json) |
|[\[미리 보기\]: Recovery Services 자격 증명 모음은 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F11e3da8c-1d68-4392-badd-0ff3c43ab5b0) |Azure Private Link를 통해 원본 또는 대상의 공용 IP 주소가 없어도 Azure 서비스에 가상 네트워크를 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Recovery Services 자격 증명 모음에 매핑하면 데이터 누출 위험이 줄어듭니다. [https://aka.ms/HybridScenarios-PrivateLink](../../../../articles/site-recovery/hybrid-how-to-enable-replication-private-endpoints.md) 및 [https://aka.ms/AzureToAzure-PrivateLink](../../../../articles/site-recovery/azure-to-azure-how-to-enable-replication-private-endpoints.md)에서 Azure Site Recovery의 프라이빗 링크에 대해 자세히 알아봅니다. |감사, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Site%20Recovery/RecoveryServices_SiteRecovery_PrivateEndpoint_Audit.json) |