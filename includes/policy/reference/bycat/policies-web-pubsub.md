---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/17/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fca687bae89cd1db7a7420abf5454e6cba2e91cf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655431"
---
|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Web PubSub 서비스는 공용 네트워크 액세스를 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fbf45113f-264e-4a87-88f9-29ac8a0aca6a) |공용 네트워크 액세스를 사용하지 않도록 설정하면 Azure Web PubSub 서비스가 퍼블릭 인터넷에 노출되지 않으므로 보안이 강화됩니다. 프라이빗 엔드포인트를 만들면 Azure Web PubSub 서비스의 노출을 제한할 수 있습니다. [https://aka.ms/awps/networkacls](https://aka.ms/awps/networkacls)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PublicNetworkAccessDisabled_AuditDeny.json) |
|[Azure Web PubSub 서비스는 프라이빗 링크를 지원하는 SKU를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82909236-25f3-46a6-841c-fe1020f95ae1) |지원되는 SKU로 Azure Private Link를 사용하면 원본 또는 대상에서 공용 IP 주소 없이 가상 네트워크를 Azure 서비스에 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Web PubSub 서비스에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_AllowedSKU_AuditDeny.json) |
|[Azure Web PubSub 서비스는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F52630df9-ca7e-442b-853b-c6ce548b31a2) |Azure Private Link를 사용하면 원본 또는 대상에 공용 IP 주소가 없어도 가상 네트워크를 Azure 서비스에 연결할 수 있습니다. 프라이빗 링크 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Azure Web PubSub 서비스에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)에서 프라이빗 링크에 대해 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_Audit.json) |
|[공용 네트워크 액세스를 사용하지 않도록 Azure Web PubSub 서비스 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b1213e4-06e4-4ccc-81de-4201f2f7131a) |퍼블릭 인터넷을 통해 액세스할 수 없도록 Azure Web PubSub 리소스에 대한 공용 네트워크 액세스를 사용하지 않도록 설정합니다. 이를 통해 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/awps/networkacls](https://aka.ms/awps/networkacls)에서 자세히 알아보세요.  |수정, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PublicNetworkAccessDisabled_Modify.json) |
|[프라이빗 DNS 영역을 사용하도록 Azure Web PubSub 서비스 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b026355-49cb-467b-8ac4-f777874e175a) |프라이빗 DNS 영역을 사용하여 프라이빗 엔드포인트에 대한 DNS 확인을 재정의합니다. 프라이빗 DNS 영역은 가상 네트워크에 연결되어 Azure Web PubSub 서비스로 확인됩니다. [https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)에서 자세히 알아보세요. |DeployIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointDNSZone_DeployIfNotExists.json) |
|[프라이빗 엔드포인트로 Azure Web PubSub 서비스 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b9c0b58-fc7b-42c8-8010-cdfa1d1b8544) |프라이빗 엔드포인트는 원본 또는 대상에서 공용 IP 주소 없이 Azure 서비스에 가상 네트워크를 연결합니다. 프라이빗 엔드포인트를 Azure Web PubSub 서비스에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/awps/privatelink](https://aka.ms/awps/privatelink)에서 프라이빗 링크에 대해 자세히 알아보세요.  |DeployIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Web%20PubSub/WebPubSub_PrivateEndpointEnabled_DeployIfNotExists.json) |
