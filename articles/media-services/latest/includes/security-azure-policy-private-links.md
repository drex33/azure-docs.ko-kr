---
author: johndeu
ms.service: media-services
ms.topic: include
ms.date: 08/17/2021
ms.author: johndeu
ms.openlocfilehash: d6641a3ca8181f7b3c8538179623549cc92a098a
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539151"
---
## <a name="azure-policy-definitions-for-private-links"></a>Private Link에 대한 Azure Policy 정의

Azure Media Services는 몇 가지 기본 제공 사용 사례 기반 [Azure Policy](../../../governance/policy/overview.md) 정의를 지원합니다. 

Private Link에서 사용할 수 있는 정책 정의는 다음과 같습니다.

|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Media Services 계정은 Private Link를 지원하는 API를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa77d8bb4-8d22-4bc1-a884-f582a705b480) |Azure Media Services 계정은 프라이빗 링크를 지원하는 API를 통해 만들어야 합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_RequirePrivateLinkSupport_Audit.json) |
|[Azure Media Services는 프라이빗 링크를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4a591bf5-918e-4a5f-8dad-841863140d61) |Azure Private Link를 사용하면 원본 또는 대상에 공용 IP 주소가 없어도 가상 네트워크를 Azure 서비스에 연결할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 프라이빗 엔드포인트를 Media Services에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/mediaservicesprivatelinkdocs](../../../../articles/media-services/latest/security-private-link-how-to.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_PrivateLink_AuditIfNotExists.json) |
|[프라이빗 DNS 영역을 사용하도록 Azure Media Services 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4a7f6c1-585e-4177-ad5b-c2c93f4bb991) |프라이빗 DNS 영역을 사용하여 프라이빗 엔드포인트에 대한 DNS 확인을 재정의합니다. 프라이빗 DNS 영역은 가상 네트워크에 연결되어 Media Services 계정으로 확인됩니다. [https://aka.ms/mediaservicesprivatelinkdocs](../../../../articles/media-services/latest/security-private-link-how-to.md)에서 자세히 알아보세요. |DeployIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_PrivateLinkDns_DeployIfNotExists.json) |
|[프라이빗 엔드포인트를 사용하여 Azure Media Services 구성](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5632066-946d-4766-9544-cd79bcc1286e) |프라이빗 엔드포인트는 원본 또는 대상에서 공용 IP 주소 없이 Azure 서비스에 가상 네트워크를 연결합니다. 프라이빗 엔드포인트를 Media Services에 매핑하면 데이터 유출 위험을 줄일 수 있습니다. [https://aka.ms/mediaservicesprivatelinkdocs](../../../../articles/media-services/latest/security-private-link-how-to.md)에서 프라이빗 링크에 대해 자세히 알아보세요. |DeployIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_PrivateLink_DeployIfNotExists.json) |

모든 기본 제공 정책 정의 및 사용 사례의 전체 목록은 [Azure Policy for Media Services](../security-azure-policy.md)를 참조하세요.

Azure Policy 정의를 배포하고 사용하는 방법에 대한 자세한 내용은 ["Azure Policy란?"](../../../governance/policy/overview.md) 문서를 참조하세요.