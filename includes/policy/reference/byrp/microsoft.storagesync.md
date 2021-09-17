---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/03/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 90bb608a56744d15d474bd5d7b1c1f48a929bb9f
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123476577"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 파일 동기화는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d320205-c6a1-4ac6-873d-46224024e8e2) |표시된 Storage Sync Service 리소스의 프라이빗 엔드포인트를 만들면 인터넷에서 액세스할 수 있는 퍼블릭 엔드포인트를 사용하지 않고 조직 네트워크의 개인 IP 주소 공간 내에서 Storage Sync Service 리소스를 처리할 수 있습니다. 프라이빗 엔드포인트를 자체적으로 만든다고 해서 퍼블릭 엔드포인트가 비활성화되지 않습니다. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_AuditIfNotExists.json) |
|[프라이빗 엔드포인트를 사용하여 Azure 파일 동기화 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb35dddd9-daf7-423b-8375-5a5b86806d5a) |표시된 Storage Sync Service 리소스에 대해 프라이빗 엔드포인트가 배포됩니다. 이렇게 하면 인터넷에 액세스할 수 있는 퍼블릭 엔드포인트를 통하지 않고 조직 네트워크의 개인 IP 주소 공간 내에서 Storage Sync Service 리소스 주소를 지정할 수 있습니다. 하나 이상의 프라이빗 엔드포인트가 자체적으로 있다고 하더라도 퍼블릭 엔드포인트가 비활성화되지는 않습니다. |DeployIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_PrivateEndpoint_DeployIfNotExists.json) |
|[수정 - 공용 네트워크 액세스를 사용하지 않도록 Azure 파일 동기화 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e07b2e9-6cd9-4c40-9ccb-52817b95133b) |조직 정책에 따라 Azure 파일 동기화의 인터넷 액세스 가능 퍼블릭 엔드포인트가 비활성화되었습니다. 프라이빗 엔드포인트를 통해 Storage Sync Service에 계속 액세스할 수 있습니다. |수정, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_Modify.json) |
|[Azure 파일 동기화의 공용 네트워크 액세스를 사용하지 않도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F21a8cd35-125e-4d13-b82d-2e19b7208bb7) |퍼블릭 엔드포인트를 사용하지 않도록 설정하면 Storage Sync Service에 대한 액세스를 조직의 네트워크에서 승인된 프라이빗 엔드포인트로 향하는 요청으로 제한할 수 있습니다. 퍼블릭 엔드포인트에 대한 요청을 허용할 때 기본적으로 안전하지 않은 것은 없지만 규정, 법률 또는 조직 정책 요구 사항을 충족하기 위해 사용하지 않도록 설정할 수 있습니다. 리소스의 incomingTrafficPolicy를 AllowVirtualNetworksOnly로 설정하여 Storage Sync Service에 대한 퍼블릭 엔드포인트를 사용하지 않도록 설정할 수 있습니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageSync_IncomingTrafficPolicy_AuditDeny.json) |
