---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/27/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 649578b574f6d00964e9489fcbd25e1848ab3323
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123097930"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API Management 서비스는 가상 네트워크를 지원하는 SKU를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73ef9241-5d81-4cd4-b483-8443d1730fe5) |API Management의 지원되는 SKU를 사용하여 가상 네트워크에 서비스를 배포하면 네트워크 보안 구성을 더욱 효과적으로 제어할 수 있는 고급 API Management 네트워킹 및 보안 기능을 활용할 수 있습니다. [https://aka.ms/apimvnet](../../../../articles/api-management/api-management-using-with-vnet.md)에서 자세히 알아보세요. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_AllowedVNETSkus_AuditDeny.json) |
|[API Management 서비스에서 가상 네트워크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef619a2c-cc4d-4d03-b2ba-8c94a834d85b) |Azure Virtual Network 배포는 향상된 보안, 격리를 제공하며, 액세스를 제어하는 인터넷 라우팅이 불가능한 네트워크에 API Management 서비스를 배치할 수 있습니다. 그런 다음, 다양한 VPN 기술을 사용하여 이러한 네트워크를 온-프레미스 네트워크에 연결할 수 있으며, 이를 통해 네트워크 및/또는 온-프레미스 내에서 백 엔드 서비스에 액세스할 수 있습니다. 개발자 포털 및 API 게이트웨이를 인터넷에서 또는 가상 네트워크 내에서만 액세스할 수 있게 구성할 수 있습니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20Management/ApiManagement_VNETEnabled_Audit.json) |