---
author: madsd
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: madsd
ms.openlocfilehash: 2064586fafd82cfc67e14b289797a562e3512afd
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245155"
---
* Basic, Standard, Premium, PremiumV2 및 PremiumV3을 포함하는 전용 컴퓨팅 가격 책정 계층입니다.
* 이 App Service Environment 전용 지원 인프라를 사용하여 VNet에 직접 배포되며 Isolated 및 IsolatedV2 가격 책정 계층을 사용하고 있습니다.

VNet 통합 기능은 App Service 전용 컴퓨팅 가격 책정 계층에서 사용됩니다. 앱이 [App Service Environment](../articles/app-service/environment/overview.md)경우 이미 VNet에 있으며 동일한 VNet의 리소스에 도달하기 위해 VNet 통합 기능을 사용할 필요가 없습니다. 모든 네트워킹 기능에 대한 자세한 내용은 [App Service 네트워킹 기능](../articles/app-service/networking-features.md)을 참조하세요.

VNet 통합은 앱에 VNet의 리소스에 대한 액세스 권한을 제공하지만 VNet에서 앱에 대한 인바운드 프라이빗 액세스 권한을 부여하지는 않습니다. 프라이빗 사이트 액세스는 Azure 가상 네트워크 내에서와 같은 프라이빗 네트워크에서만 앱에 액세스할 수 있도록 합니다. VNet 통합은 앱에서 VNet으로 아웃바운드 호출을 수행하는 데만 사용됩니다. VNet 통합 기능은 동일한 지역의 VNet 및 다른 지역의 VNet과 함께 사용될 때 다르게 동작합니다. VNet 통합 기능에는 두 가지 변형이 있습니다.

* **지역 VNet 통합:** 동일한 지역의 가상 네트워크에 연결하는 경우 통합하는 VNet에 전용 서브넷이 있어야 합니다.
* **게이트웨이 필수 VNet 통합:** 다른 지역의 VNet 또는 동일한 지역의 클래식 가상 네트워크에 직접 연결하는 경우 대상 VNet에서 만든 Azure Virtual Network 게이트웨이가 필요합니다.

VNet 통합 기능:

* **표준**, **Premium**, **PremiumV2,** **PremiumV3** 또는 **탄력적 Premium** App Service 가격 책정 계층이 필요합니다.
* TCP 및 UDP를 지원합니다.
* Azure App Service 앱 및 함수 앱에서 작동합니다.

다음과 같이 VNet 통합에서 지원하지 않는 몇 가지 사항이 있습니다.

* 드라이브 탑재
* Windows Server Active Directory 통합.
* NetBIOS.

게이트웨이 필수 VNet 통합은 피어링 또는 VPN을 사용하여 대상 VNet 또는 대상 VNet에 연결된 네트워크에서만 리소스에 대한 액세스를 제공합니다. 게이트웨이 필수 VNet 통합은 Azure ExpressRoute 연결에서 사용할 수 있는 리소스에 대한 액세스를 사용하도록 설정하거나 서비스 엔드포인트로 작업할 수 없습니다.

사용된 버전에 관계없이 VNet 통합은 앱에 VNet의 리소스에 대한 액세스 권한을 제공하지만 VNet에서 앱에 대한 인바운드 프라이빗 액세스 권한을 부여하지는 않습니다. 프라이빗 사이트 액세스는 Azure 가상 네트워크 내에서와 같은 프라이빗 네트워크에서만 앱에 액세스할 수 있도록 하는 것을 의미합니다. VNet 통합은 앱에서 VNet으로 아웃바운드 호출을 수행하기 위한 것입니다.
