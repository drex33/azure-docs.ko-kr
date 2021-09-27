---
title: 동일한 지역에 여러 Azure VMware 솔루션 사설 클라우드 커넥트
description: 동일한 지역에 있는 둘 이상의 Azure VMware Solution 프라이빗 클라우드 간에 네트워크 연결을 만드는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 09/20/2021
ms.openlocfilehash: daa4b60c0e42c77f61054d6d9a77898cc0448c37
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128567844"
---
# <a name="connect-multiple-azure-vmware-solution-private-clouds-in-the-same-region"></a>동일한 지역에 여러 Azure VMware 솔루션 사설 클라우드 커넥트

**AVS Interconnect** 기능을 사용하면 동일한 지역에 있는 둘 이상의 Azure VMware Solution 프라이빗 클라우드 간에 네트워크 연결을 만들 수 있습니다. 클라우드 간의 네트워크 통신을 가능하게 하기 위해 프라이빗 클라우드의 관리 및 워크로드 네트워크 사이에 라우팅 링크를 만듭니다.

프라이빗 클라우드를 여러 프라이빗 클라우드에 연결할 수 있으며 연결은 전이되지 않습니다. 예를 들어 _프라이빗 클라우드 1_ 이 _프라이빗 클라우드 2_ 에 연결되어 있고 _프라이빗 클라우드 2_ 가 _프라이빗 클라우드 3_ 에 연결되어 있으면 프라이빗 클라우드 1과 3은 직접 연결될 때까지 통신하지 않습니다.

동일한 지역의 프라이빗 클라우드만 연결할 수 있습니다. 다른 지역에 있는 프라이빗 클라우드를 연결하려면 [ExpressRoute Global Reach를 사용](tutorial-expressroute-global-reach-private-cloud.md)하여 프라이빗 클라우드를 온-프레미스 회로에 연결하는 것과 같은 방식으로 프라이빗 클라우드를 연결합니다. 

## <a name="supported-regions"></a>지원되는 지역

SAT20 (미국 DUB21), 동남 아시아 (S G 2) 및 영국 서부 (CWL20)를 제외한 모든 지역에서 AVS 상호 연결 기능을 사용할 수 있습니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 연결하는 각 프라이빗 클라우드에 대한 쓰기 액세스 권한
- 각 클라우드의 라우팅된 IP 주소 공간은 고유하며 겹치지 않습니다.

>[!NOTE]
>**AVS 상호 연결** 기능은 피어링을 만들기 전에 기본 Azure vNet 피어링이 수행하는 방식으로 겹치는 IP 공간을 확인하지 않습니다. 따라서 프라이빗 클라우드 간에 겹치지 않도록 하는 것은 사용자의 책임입니다.
>
>Azure VMware Solution 환경에서는 Azure로 라우팅되지 않는 NSX 세그먼트에 라우팅되지 않고 겹치는 IP 배포를 구성할 수 있습니다.  AVS Interconnect 기능은 각 프라이빗 클라우드의 NSX T0 간에만 라우팅되므로 이러한 기능은 문제를 일으키지 않습니다.


## <a name="add-connection-between-private-clouds"></a>프라이빗 클라우드 간 연결 추가

1. Azure VMware Solution 프라이빗 클라우드의 **관리** 에서 **연결** 을 선택합니다.

2. **AVS 상호 연결** 탭을 선택한 다음 **추가** 를 선택합니다.

   :::image type="content" source="media/networking/private-cloud-to-private-cloud-no-connections.png" alt-text="연결 아래의 AVS 상호 연결 탭을 보여 주는 스크린샷." border="true" lightbox="media/networking/private-cloud-to-private-cloud-no-connections.png":::

3. 새 연결에 대한 정보 및 Azure VMware Solution 프라이빗 클라우드를 선택합니다.

   >[!NOTE]
   >동일한 지역의 프라이빗 클라우드에만 연결할 수 있습니다. 다른 지역에 있는 프라이빗 클라우드에 연결하려면 [ExpressRoute Global Reach를 사용](tutorial-expressroute-global-reach-private-cloud.md)하여 프라이빗 클라우드를 온-프레미스 회로에 연결하는 것과 같은 방식으로 프라이빗 클라우드를 연결합니다. 

   :::image type="content" source="media/networking/add-connection-to-other-private-cloud.png" alt-text="다른 프라이빗 클라우드에 연결을 추가하는 데 필요한 정보를 보여 주는 스크린샷." border="true":::


4. 두 프라이빗 클라우드에 겹치는 라우팅된 IP 공간이 없음을 확인하는 **동의** 확인란을 선택합니다. 

5. **만들기** 를 선택합니다.  연결 만들기 상태를 확인할 수 있습니다.

   :::image type="content" source="media/networking/add-connection-to-other-private-cloud-notification.png" alt-text="진행 중인 연결 및 기존 연결에 대한 알림 정보를 보여 주는 스크린샷." border="true":::

   **AVS Private Cloud** 아래에 모든 연결이 표시됩니다.
   
   :::image type="content" source="media/networking/private-cloud-to-private-cloud-two-connections.png" alt-text="연결 아래의 AVS 상호 연결 탭과 두 개의 설정된 프라이빗 클라우드 연결을 보여 주는 스크린샷." border="true" lightbox="media/networking/private-cloud-to-private-cloud-two-connections.png":::


## <a name="remove-connection-between-private-clouds"></a>프라이빗 클라우드 간의 연결 제거

1. Azure VMware Solution 프라이빗 클라우드의 **관리** 에서 **연결** 을 선택합니다.

2. 제거하려는 연결에 대해 오른쪽에서 **삭제**(휴지통)를 선택한 다음 **예** 를 선택합니다.


## <a name="next-steps"></a>다음 단계

동일한 지역에서 여러 프라이빗 클라우드를 연결했으므로 이제 다음에 대해 알아볼 수 있습니다.

- [Azure VMware Solution 리소스를 다른 지역으로 이동](move-azure-vmware-solution-across-regions.md)
- [Azure VMware Solution 구독을 다른 구독으로 이동](move-ea-csp-subscriptions.md)
