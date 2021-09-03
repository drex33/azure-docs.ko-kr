---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 201aaae4089790bce6a697eccc7b131b3ad0d17e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453990"
---
## <a name="private-endpoints-and-virtual-network-service-endpoints"></a>프라이빗 엔드포인트 및 가상 네트워크 서비스 엔드포인트

Azure는 [프라이빗 Azure 백본 네트워크](https://azure.microsoft.com/global-infrastructure/global-network/)를 통해 터널링되는 트래픽에 대해 프라이빗 엔드포인트 및 가상 네트워크 서비스 엔드포인트를 제공합니다. 이러한 엔드포인트 유형의 목적과 기본 기술은 비슷합니다. 그러나 두 기술 사이에는 차이점이 있습니다. 네트워크를 설계하기 전에 두 가지의 장단점에 대해 알아보는 것이 좋습니다.

사용할 기술을 결정할 때 고려해야 할 몇 가지 사항이 있습니다.
- 두 기술 모두 가상 네트워크와 음성 리소스 간의 트래픽이 공용 인터넷을 통해 이동하지 않도록 합니다.
- 프라이빗 엔드포인트는 음성 리소스에 전용 개인 IP 주소를 제공합니다. 이 IP 주소는 특정 가상 네트워크 및 서브넷 내에서만 액세스할 수 있습니다. 네트워크 인프라 내에서 이 IP 주소에 대한 액세스를 완전히 제어할 수 있습니다.
- 가상 네트워크 서비스 엔드포인트는 음성 리소스에 대한 전용 개인 IP 주소를 제공하지 않습니다. 대신 음성 리소스로 전송된 모든 패킷을 캡슐화하고 Azure 백본 네트워크를 통해 직접 전달합니다.
- 두 기술 모두 온-프레미스 시나리오를 지원합니다. 기본적으로 가상 네트워크 서비스 엔드포인트를 사용하는 경우 가상 네트워크에 보호되는 Azure 서비스 리소스는 온-프레미스 네트워크에서 연결할 수 없습니다. 하지만 [이 동작을 변경](../../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-service-access-from-on-premises)할 수 있습니다.
- 가상 네트워크 서비스 엔드포인트는 트래픽이 시작되는 가상 네트워크를 기반으로 음성 리소스에 대한 액세스를 제한하는 데 자주 사용됩니다.
- Cognitive Services의 경우 가상 네트워크 서비스 엔드포인트를 사용하도록 설정하면 모든 Cognitive Services 리소스에 대한 트래픽이 프라이빗 백본 네트워크를 통과하게 됩니다. 이렇게 하려면 명시적인 네트워크 액세스 구성이 필요합니다. (자세한 내용은 [가상 네트워크 및 음성 리소스 네트워킹 설정 구성](../speech-service-vnet-service-endpoint.md#configure-virtual-networks-and-the-speech-resource-networking-settings)을 참조하세요.) 프라이빗 엔드포인트에는 이러한 제한이 없으며 네트워크 구성에 더 많은 유연성을 제공합니다. 동일한 가상 네트워크의 동일한 서브넷을 사용하여 프라이빗 백본을 통해 하나의 리소스에 액세스하고 공용 인터넷을 통해 다른 리소스에 액세스할 수 있습니다.
- 프라이빗 엔드포인트에는 [추가 비용](https://azure.microsoft.com/pricing/details/private-link)이 발생합니다. Virtual Network 서비스 엔드포인트는 무료입니다.
- 프라이빗 엔드포인트에는 [추가 DNS 구성](../speech-services-private-link.md#turn-on-private-endpoints)이 필요합니다.
- 하나의 음성 리소스는 프라이빗 엔드포인트 및 가상 네트워크 서비스 엔드포인트 모두에서 동시에 작동할 수 있습니다.

프로덕션 디자인에 대한 결정을 내리기 전에 두 엔드포인트 유형을 모두 시도하는 것이 좋습니다. 

이러한 응용 프로그램은 Azure AD Graph API를 사용할 수 있습니다. 자세한 내용은 다음 리소스를 참조하세요.

- [Azure Private Link 및 프라이빗 엔드포인트 설명서](../../../private-link/private-link-overview.md)
- [가상 네트워크 서비스 엔드포인트 문서](../../../virtual-network/virtual-network-service-endpoints-overview.md)
