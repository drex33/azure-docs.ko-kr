---
author: madsd
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: madsd
ms.openlocfilehash: 8d4c2968e3b21269d233f584747856804f8ff9c6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641936"
---
* 격리 계층을 제외한 모든 범위의 요금제를 지원하는 다중 테넌트 시스템입니다.
* App Service Environment는 VNet에 배포되고 격리된 가격 책정 계획 앱을 지원합니다.

VNet 통합 기능은 다중 테넌트 앱에서 사용됩니다. 앱이 [App Service Environment][ASEintro]에 있는 경우 이미 VNet에 있으므로 VNet 통합 기능을 사용하여 동일한 VNet의 리소스에 연결할 필요가 없습니다. 모든 네트워킹 기능에 대한 자세한 내용은 [App Service 네트워킹 기능][Networkingfeatures]을 참조하세요.

VNet 통합은 VNet의 리소스에 대한 액세스 권한을 앱에 부여하지만, VNet에서 앱에 대한 인바운드 프라이빗 액세스 권한은 부여하지 않습니다. 프라이빗 사이트 액세스는 Azure 가상 네트워크 내에서와 같은 프라이빗 네트워크에서만 앱에 액세스할 수 있도록 합니다. VNet 통합은 앱에서 VNet으로 아웃바운드 호출을 수행하는 데만 사용됩니다. VNet 통합 기능은 동일한 지역의 VNet에서 사용할 때와 다른 지역의 VNet에서 사용할 때 다르게 작동합니다. VNet 통합 기능에는 두 가지 변형이 있습니다.

* **지역 VNet 통합**: 동일한 지역의 Azure Resource Manager 가상 네트워크에 연결하는 경우 통합하는 VNet에 전용 서브넷이 있어야 합니다.
* **게이트웨이 필수 VNet 통합:** 다른 지역의 VNet 또는 동일한 지역의 클래식 가상 네트워크에 직접 연결하는 경우 대상 VNet에 프로비전된 Azure Virtual Network 게이트웨이가 필요합니다.

VNet 통합 기능에는 다음이 적용됩니다.

* Standard, Premium, PremiumV2, PremiumV3 또는 Elastic Premium 요금제가 필요합니다.
* TCP 및 UDP를 지원합니다.
* Azure App Service 앱 및 함수 앱을 사용합니다.

VNet 통합에서 지원하지 않는 다음과 같은 몇 가지 항목이 있습니다.

* 드라이브 탑재
* Windows Server Active Directory 통합.
* NetBIOS.

게이트웨이 필수 VNet 통합은 대상 VNet 또는 피어링 또는 VPN을 사용하여 대상 VNet에 연결된 네트워크에서만 리소스에 액세스할 수 있도록 지원합니다. 게이트웨이 필수 VNet 통합은 Azure ExpressRoute 연결에서 사용할 수 있는 리소스에 액세스할 수 없거나 서비스 엔드포인트를 사용할 수 없습니다.

사용된 버전에 관계없이 VNet 통합은 VNet의 리소스에 대한 액세스 권한을 앱에 부여하지만, VNet에서 앱에 대한 인바운드 프라이빗 액세스 권한은 부여하지 않습니다. 프라이빗 사이트 액세스는 Azure VNet 내에서와 같은 프라이빗 네트워크에서만 앱에 액세스할 수 있도록 합니다. VNet 통합은 앱에서 VNet으로 아웃바운드 호출만 수행하기 위한 것입니다.

<!--Links-->
[ASEintro]: ../articles/app-service/environment/intro.md
[Networkingfeatures]: ../articles/app-service/networking-features.md
