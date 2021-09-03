---
title: 개념 - API Management
description: API Management가 Azure VMware Solution VM(가상 머신)에서 실행되는 API를 보호하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 04/28/2021
ms.openlocfilehash: 14b2d81d0080ebf389c817d1c718d207aa922b0d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529452"
---
# <a name="publish-and-protect-apis-running-on-azure-vmware-solution-vms"></a>Azure VMware Solution VM에서 실행되는 API 게시 및 보호

Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/)를 사용하면 외부 또는 내부 소비자에게 안전하게 게시할 수 있습니다.  Developer(개발) 및 Premium(프로덕션) SKU에서만 Azure Virtual Network 통합을 통해 Azure VMware Solution 워크로드에서 실행되는 API를 게시할 수 있습니다. 또한 두 SKU 모두 API Management 서비스와 백 엔드 간의 연결을 사용하도록 설정합니다.

API Management 구성은 Azure VMware Solution VM(가상 머신) 및 온-프레미스에서 실행되는 백 엔드 서비스에 대해 동일합니다. 또한 API Management는 백 엔드 서버가 Azure VMware Solution의 NSX Load Balancer 뒤에 배치되는 경우 부하 분산 장치의 가상 IP를 두 배포 모두에 대해 백 엔드 엔드포인트로 구성합니다.

## <a name="external-deployment"></a>외부 배포

외부 배포는 퍼블릭 엔드포인트를 사용하는 외부 사용자가 사용하는 API를 게시합니다. 개발자와 DevOps 엔지니어는 Azure Portal 또는 PowerShell 및 API Management 개발자 포털을 통해 API를 관리할 수 있습니다.

외부 배포 다이어그램은 전체 프로세스와 관련된 작업자를 보여줍니다(맨 위에 표시). 작업자는 다음과 같습니다.

- **관리자:** Azure Portal과 PowerShell 또는 Azure DevOps 같은 자동화 메커니즘을 통해 Azure VMware Solution을 관리하는 관리자 또는 DevOps 팀을 나타냅니다.

- **사용자:** 노출된 API의 소비자를 나타내며 API를 사용하는 사용자와 서비스를 모두 나타냅니다.

트래픽 흐름은 허브 가상 네트워크에 연결된 백 엔드 서비스를 추상화하는 API Management 인스턴스를 통과합니다. ExpressRoute Gateway는 트래픽을 ExpressRoute Global Reach 채널로 라우팅하고 들어오는 트래픽을 다른 백 엔드 서비스 인스턴스에 배포하는 NSX Load Balancer에 도달합니다.

API Management에는 Azure Public API가 있으며 Azure DDOS 보호 서비스를 활성화하는 것이 좋습니다. 

:::image type="content" source="media/api-management/api-management-external-deployment.png" alt-text="Azure VMware Solution에 대한 외부 API Management 배포를 보여 주는 다이어그램" border="false":::


## <a name="internal-deployment"></a>내부 배포

내부 배포는 내부 사용자 또는 시스템에서 사용하는 API를 게시합니다. DevOps 팀 및 API 개발자는 외부 배포와 동일한 관리 도구 및 개발자 포털을 사용합니다.

내부 배포에 [Azure Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)를 사용하여 API에 대한 퍼블릭 및 보안 엔드포인트를 만듭니다.  게이트웨이의 기능은 다양한 시나리오를 가능하게 하는 하이브리드 배포를 만드는 데 사용됩니다.  

* 내부 소비자와 외부 소비자 모두 사용할 수 있도록 동일한 API Management 리소스를 사용합니다.

* API 하위 집합이 정의되어 있고 외부 소비자가 사용할 수 있는 단일 API Management 리소스가 있습니다.

* 퍼블릭 인터넷에서 API Management에 대한 액세스를 켜고 끌 수 있는 턴키 방법을 제공합니다.

아래 배포 다이어그램은 내부 또는 외부에 있을 수 있는 소비자를 보여줍니다. 각 유형은 동일한 API 또는 다른 API에 액세스합니다.

내부 배포에서 API는 동일한 API Management 인스턴스에 노출됩니다. API Management 앞에는 Application Gateway가 Azure Web Application Firewall(WAF) 기능이 활성화된 상태로 배포됩니다. 또한 트래픽을 필터링하여 Azure VMware Solution에서 실행되는 백 엔드 서비스의 하위 집합만 노출하도록 일련의 HTTP 수신기와 규칙을 배포합니다.


* 내부 트래픽은 ExpressRoute Gateway를 통해 Azure Firewall로 라우팅된 다음 직접 또는 트래픽 규칙을 통해 API Management로 라우팅됩니다.   

* 외부 트래픽은 API Management의 외부 보호 계층을 사용하는 Application Gateway를 통해 Azure에 들어갑니다.


:::image type="content" source="media/api-management/api-management-internal-deployment.png" alt-text="Azure VMware Solution에 대한 내부 API Management 배포를 보여 주는 다이어그램" lightbox="media/api-management/api-management-internal-deployment.png" border="false":::
