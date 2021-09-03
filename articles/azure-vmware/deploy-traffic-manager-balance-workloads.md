---
title: Traffic Manager를 배포하여 Azure VMware Solution 워크로드 균형 조정
description: Azure VMware Solution과 Traffic Manager를 통합하여 여러 지역의 여러 엔드포인트에서 애플리케이션 워크로드의 균형을 조정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: ac8f402eadcced4e8592a68f13abd6d2693b8b0c
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601988"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Traffic Manager를 배포하여 Azure VMware Solution 워크로드 균형 조정

이 문서에서는 [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)를 Azure VMware Solution과 통합하는 방법의 단계를 안내합니다. 이 통합을 통해 여러 엔드포인트에서 애플리케이션 워크로드의 균형을 조정할 수 있습니다. 이 문서에서는 여러 Azure VMware Solution 지역에 걸쳐 있는 세 개의 [Azure Application Gateway](../application-gateway/overview.md) 간에 트래픽을 전달하도록 Traffic Manager를 구성하는 방법의 단계를 안내합니다. 

게이트웨이에는 들어오는 계층 7 요청의 부하를 분산하기 위해 백 엔드 풀 멤버로 구성된 Azure VMware Solution VM(가상 머신)이 있습니다. 자세한 내용은 [Azure Application Gateway를 사용하여 Azure VMware Solution에서 웹앱 보호](protect-azure-vmware-solution-with-application-gateway.md)를 참조하세요.

이 다이어그램에서는 Traffic Manager가 지역 엔드포인트 간의 DNS 수준에서 애플리케이션에 대한 부하 분산을 제공하는 방법을 보여 줍니다. 게이트웨이에는 IIS 서버로 구성된 백 엔드 풀 멤버가 있으며 Azure VMware Solution 외부 엔드포인트로 참조됩니다. 두 프라이빗 클라우드 지역 간에 가상 네트워크를 통해 연결할 때 ExpressRoute 게이트웨이를 사용합니다.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Azure VMware Solution과 Traffic Manager 통합 다이어그램" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

시작하기 전에 먼저 [필수 조건](#prerequisites)을 검토한 후 다음 절차를 안내합니다.

> [!div class="checklist"]
> * 애플리케이션 게이트웨이 및 NSX-T 세그먼트의 구성 확인
> * Traffic Manager 프로필 만들기
> * Traffic Manager 프로필에 외부 엔드포인트 추가

## <a name="prerequisites"></a>필수 구성 요소

- 서로 다른 Azure VMware Solution 지역에서 실행되는 Microsoft IIS 서버로 구성된 세 개의 VM: 
   - 미국 서부
   - 서유럽
   - 미국 동부(온-프레미스) 

- 위에서 언급한 Azure VMware Solution 지역에서 외부 엔드포인트를 사용하는 애플리케이션 게이트웨이입니다.

- 확인을 위해 인터넷에 연결된 호스트입니다. 

- [Azure VMware Solution에서 만들어진 NSX-T 네트워크 세그먼트](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>애플리케이션 게이트웨이 구성 확인

다음 단계는 애플리케이션 게이트웨이의 구성을 확인합니다.

1. Azure Portal에서 **애플리케이션 게이트웨이** 를 선택하여 현재 애플리케이션 게이트웨이 목록을 봅니다.

   - AVS-GW-WUS
   - AVS-GW-EUS(온-프레미스)
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="구성된 애플리케이션 게이트웨이 목록을 보여 주는 애플리케이션 게이트웨이 페이지의 스크린샷" lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. 이전에 배포된 애플리케이션 게이트웨이 중 하나를 선택합니다. 

   애플리케이션 게이트웨이에 대한 다양한 정보를 보여 주는 창이 열립니다. 

   :::image type="content" source="media/traffic-manager/backend-pool-configuration.png" alt-text="선택한 애플리케이션 게이트웨이의 세부 정보를 보여 주는 애플리케이션 게이트웨이 페이지의 스크린샷" lightbox="media/traffic-manager/backend-pool-configuration.png":::

1. 백 엔드 풀 중 하나의 구성을 확인하려면 **백 엔드 풀** 을 선택합니다. IP 주소(172.29.1.10)를 사용하여 웹 서버로 구성된 VM 백 엔드 풀 멤버가 하나 표시됩니다.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="대상 IP 주소가 강조 표시된 백 엔드 풀 편집 페이지의 스크린샷":::

1. 다른 애플리케이션 게이트웨이 및 백 엔드 풀 멤버의 구성을 확인합니다. 

## <a name="verify-the-nsx-t-segment-configuration"></a>NSX-T 세그먼트 구성 확인

다음 단계는 Azure VMware Solution 환경에서 NSX-T 세그먼트의 구성을 확인합니다.

1. 구성된 세그먼트를 보려면 **세그먼트** 를 선택합니다.  Contoso-T01 게이트웨이, Tier-1의 유연한 라우터에 연결된 Contoso-segment1이 표시됩니다.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-azure-vmware-solution.png" alt-text="NSX-T Manager의 세그먼트 프로필을 보여 주는 스크린샷" lightbox="media/traffic-manager/nsx-t-segment-azure-vmware-solution.png":::    

1. 연결된 세그먼트 수와 함께 Tier-1 게이트웨이 목록을 보려면 **Tier-1 게이트웨이** 를 선택합니다. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="선택한 세그먼트의 게이트웨이 주소를 보여 주는 스크린샷":::    

1. Contoso-T01에 연결된 세그먼트를 선택합니다. Tier-01 라우터에 구성된 논리 인터페이스를 보여 주는 창이 열립니다. 이는 세그먼트에 연결된 백 엔드 풀 멤버 VM에 대한 게이트웨이로 사용됩니다.

1. vSphere 클라이언트에서 VM을 선택하여 세부 정보를 확인합니다. 

   >[!NOTE]
   >해당 IP 주소는 이전 섹션에서 웹 서버로 구성된 VM 백 엔드 풀 멤버와 일치합니다(172.29.1.10).

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="vSphere Client의 VM 세부 정보를 보여 주는 스크린샷." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. VM을 선택한 다음 **작업 > 설정 편집** 을 선택하여 NSX-T 세그먼트에 대한 연결을 확인합니다.

## <a name="create-your-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

1. [Azure Portal](https://rc.portal.azure.com/#home)에 로그인합니다. **Azure 서비스 > 네트워킹** 에서 **Traffic Manager 프로필** 을 선택합니다.

2. **+ 추가** 를 선택하여 새 Traffic Manager 프로필을 만듭니다.
 
3. 다음 정보를 입력한 다음 **만들기** 를 선택합니다.

   - 프로필 이름
   - 라우팅 방법([가중치](../traffic-manager/traffic-manager-routing-methods.md) 적용)
   - Subscription
   - Resource group

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Traffic Manager 프로필에 외부 엔드포인트 추가

1. 검색 결과 창에서 Traffic Manager 프로필을 선택하고 **엔드포인트** 를 선택한 다음 **+ 추가** 를 선택합니다.

1. 다른 지역의 각 외부 엔드포인트에 대해 필수 세부 사항을 입력한 후 **추가** 를 선택합니다. 
   - Type
   - Name
   - FQDN(정규화된 도메인 이름) 또는 IP
   - 가중치(각 엔드포인트에 가중치 1을 할당) 

   만든 후에는 세 가지 모두 Traffic Manager 프로필에 표시됩니다. 세 가지 모두의 모니터 상태는 **온라인** 이어야 합니다.

3. **개요** 를 선택하고 **DNS 이름** 아래에 URL을 복사합니다.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="DNS 이름이 강조 표시된 Traffic Manager 엔드포인트 개요를 보여 주는 스크린샷" lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. DNS 이름 URL을 브라우저에 붙여넣습니다. 유럽 서부 영역으로 전송되는 트래픽을 보여 주는 스크린샷

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="서유럽으로 라우팅되는 트래픽을 보여 주는 브라우저 창의 스크린샷"::: 

5. 브라우저를 새로 고칩니다. 이 스크린샷에서는 미국 서부 지역에서 다른 백 엔드 풀 멤버 집합으로 전송되는 트래픽이 표시됩니다.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="미국 서부로 라우팅되는 트래픽을 보여 주는 브라우저 창의 스크린샷"::: 

6. 브라우저를 다시 새로 고칩니다. 스크린샷은 온-프레미스의 백 엔드 풀 멤버의 최종 집합으로 전송되는 트래픽을 보여 줍니다.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="온-프레미스로 라우팅되는 트래픽을 보여 주는 브라우저 창의 스크린샷":::

## <a name="next-steps"></a>다음 단계

Azure VMware Solution과 Azure Traffic Manager를 통합하는 방법에 대해 이해했으므로 다음에 대해 알아볼 수 있습니다.

- [Azure VMware Solution에서 Azure Application Gateway 사용](protect-azure-vmware-solution-with-application-gateway.md)
- [Traffic Manager 라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md)
- [Azure에서 부하 분산 서비스 결합](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Traffic Manager 성능 측정](../traffic-manager/traffic-manager-performance-considerations.md)
