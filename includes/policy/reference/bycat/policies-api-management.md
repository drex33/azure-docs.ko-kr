---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7a47b66ef14069f751efb873858b55a83134a418
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123477060"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API Management 서비스는 가상 네트워크를 지원하는 SKU를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |API Management의 지원되는 SKU를 사용하여 가상 네트워크에 서비스를 배포하면 네트워크 보안 구성을 더욱 효과적으로 제어할 수 있는 고급 API Management 네트워킹 및 보안 기능을 활용할 수 있습니다. [https://aka.ms/apimvnet](../../../../articles/api-management/api-management-using-with-vnet.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[API Management 서비스는 공용 네트워크 액세스를 비활성화해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf73bd95-24da-4a4f-96b9-4e8b94b402bd) |API Management 서비스의 보안을 개선하려면 엔드포인트가 공용 인터넷에 노출되지 않도록 합니다. 일부 퍼블릭 엔드포인트는 API Management 서비스에서 사용자 시나리오를 지원하기 위해 노출됩니다(예: Management API에 대한 직접 액세스, Git을 사용하여 구성 관리, 자체 호스팅 게이트웨이 구성). 이러한 기능을 사용하지 않는 경우 해당 엔드포인트를 비활성화해야 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_PublicEndpoint_AuditIfNotExist.json) |
|[API Management 서비스에서 가상 네트워크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure Virtual Network 배포는 향상된 보안, 격리를 제공하며, 액세스를 제어하는 인터넷 라우팅이 불가능한 네트워크에 API Management 서비스를 배치할 수 있습니다. 그런 다음, 다양한 VPN 기술을 사용하여 이러한 네트워크를 온-프레미스 네트워크에 연결할 수 있으며, 이를 통해 네트워크 및/또는 온-프레미스 내에서 백 엔드 서비스에 액세스할 수 있습니다. 개발자 포털 및 API 게이트웨이를 인터넷에서 또는 가상 네트워크 내에서만 액세스할 수 있게 구성할 수 있습니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |
|[공용 네트워크 액세스를 사용하지 않도록 API Management 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ca8c8ac-3a6e-493d-99ba-c5fa35347ff2) |API Management 서비스의 보안을 개선하려면 퍼블릭 엔드포인트를 비활성화합니다. 일부 퍼블릭 엔드포인트는 API Management 서비스에서 사용자 시나리오를 지원하기 위해 노출됩니다(예: Management API에 대한 직접 액세스, Git을 사용하여 구성 관리, 자체 호스팅 게이트웨이 구성). 이러한 기능을 사용하지 않는 경우 해당 엔드포인트를 비활성화해야 합니다. |DeployIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_PublicEndpoint_DeployIfNotExist.json) |