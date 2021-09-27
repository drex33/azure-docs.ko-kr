---
title: Azure VMware Solution용 DHCP 구성
description: NSX-T Manager를 사용하여 DHCP 서버를 호스팅하거나 타사의 외부 DHCP 서버를 사용하도록 DHCP를 구성하는 방법을 알아봅니다.
ms.topic: how-to
ms.custom: contperf-fy21q2, contperf-fy22q1
ms.date: 09/13/2021
ms.openlocfilehash: 10234147303ab9959fa1a907b0c558be9e3fe0f6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600965"
---
# <a name="configure-dhcp-for-azure-vmware-solution"></a>Azure VMware Solution용 DHCP 구성

[!INCLUDE [dhcp-dns-in-azure-vmware-solution-description](includes/dhcp-dns-in-azure-vmware-solution-description.md)]

이 방법 문서에서는 NSX-T Manager 사용하여 다음 방법 중 하나로 Azure VMware Solution DHCP를 구성합니다. 


- [Azure Portal 사용하여 DHCP 서버 또는 릴레이 만들기](#use-the-azure-portal-to-create-a-dhcp-server-or-relay)

- [NSX-T를 사용하여 DHCP 서버 호스트](#use-nsx-t-to-host-your-dhcp-server)

- [타사 외부 DHCP 서버 사용](#use-a-third-party-external-dhcp-server)

>[!TIP]
>NSX-T 작업의 간소화된 보기를 사용하여 DHCP를 구성하려면 [Azure VMware Solution에 대한 DHCP 구성](configure-dhcp-azure-vmware-solution.md)을 참조하세요.


>[!IMPORTANT]
>2021년 7월 1일 이후에 생성된 클라우드의 경우 환경의 기본 계층 1 게이트웨이에서 DHCP를 구성하는 데 NSX-T 작업의 간소화된 보기를 사용해야 합니다.
>
>DHCP 서버가 온-프레미스 데이터 센터에 있는 경우 VMware HCX L2 스트레치 네트워크의 VM(가상 머신)에 대해 DHCP가 작동하지 않습니다.  NSX는 기본적으로 모든 DHCP 요청이 L2 스트레치를 통과하지 못하도록 차단합니다. 이 솔루션은 [L2 확장 VMware HCX 네트워크에서 DHCP 구성](configure-l2-stretched-vmware-hcx-networks.md) 절차를 참조하세요.

## <a name="use-the-azure-portal-to-create-a-dhcp-server-or-relay"></a>Azure Portal 사용하여 DHCP 서버 또는 릴레이 만들기

DHCP 서버를 만들거나 Azure Portal Azure VMware Solution 직접 릴레이할 수 있습니다. DHCP 서버 또는 릴레이는 Azure VMware Solution 배포할 때 생성된 계층 1 게이트웨이에 연결됩니다. DHCP 범위를 제공한 모든 세그먼트는 이 DHCP의 일부가 됩니다. DHCP 서버 또는 DHCP 릴레이를 만든 후 이를 사용하려면 세그먼트 수준에서 서브넷 또는 범위를 정의해야 합니다.

1. Azure VMware Solution 프라이빗 클라우드의 **워크로드 네트워킹** 에서 **DHCP** > **추가** 를 선택합니다.

2. **DHCP 서버** 또는  **릴레이** 를 선택한 후 서버 또는 릴레이의 이름과 3개의 IP 주소를 제공합니다. 

   >[!NOTE]
   >DHCP 릴레이의 경우 성공적인 구성을 위해 하나의 IP 주소만 필요합니다.

   :::image type="content" source="media/networking/add-dhcp-server-relay.png" alt-text="Azure VMware Solutions에서 DHCP 서버 또는 DHCP 릴레이를 추가하는 방법을 보여 주는 스크린샷입니다.":::

4. [논리 세그먼트에 DHCP 범위를 제공](tutorial-nsx-t-network-segment.md#use-azure-portal-to-add-an-nsx-t-segment)하여 DHCP 구성을 완료한 후 **확인** 을 선택합니다.



## <a name="use-nsx-t-to-host-your-dhcp-server"></a>NSX-T를 사용하여 DHCP 서버 호스트
NSX-T를 사용하여 DHCP 서버를 호스트하려는 경우 DHCP 서버 및 릴레이 서비스를 만듭니다. 그런 다음 네트워크 세그먼트를 추가하고 DHCP IP 주소 범위를 지정합니다.

### <a name="create-a-dhcp-server"></a>DHCP 서버 만들기

1. NSX-T 관리자에서 **네트워킹** > **DHCP** 를 선택한 다음, **서버 추가** 를 선택합니다.

1. **서버 형식** 에 **DHCP** 를 선택하고 서버 이름 및 IP 주소를 입력한 후, **저장** 을 선택합니다.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="NSX-T Manager에서 DHCP 서버를 추가하는 방법을 보여 주는 스크린샷." border="true":::

1. **계층 1 게이트웨이** 를 선택하고 계층 1 게이트웨이에서 세로 줄임표를 선택한 다음 **편집** 을 선택합니다.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="DHCP 서버 사용을 위해 계층 1 게이트웨이를 편집하는 방법을 보여 주는 스크린샷." border="true":::

1. 서브넷을 추가하려면 **IP 할당 집합 없음** 을 선택합니다.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="DHCP 서버 사용을 위해 계층 1 게이트웨이에 서브넷을 추가하는 방법을 보여 주는 스크린샷." border="true":::

1. **형식** 은 **DHCP 로컬 서버** 를 선택합니다. 
   
1. **DHCP 서버** 는 **기본 DHCP** 를 선택한 다음 **저장** 을 선택합니다.

1. 다시 **저장** 을 선택하고 **편집 닫기** 를 선택합니다.

### <a name="add-a-network-segment"></a>네트워크 세그먼트 추가

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

### <a name="specify-the-dhcp-ip-address-range"></a>DHCP IP 주소 범위 지정
 
DHCP 서버에 대한 릴레이를 만들 때 DHCP IP 주소 범위를 지정합니다.

>[!NOTE]
>IP 주소 범위는 구독과 온-프레미스 네트워크의 다른 가상 네트워크에서 사용되는 IP 범위와 겹치지 않아야 합니다.

1. NSX-T Manager에서 **네트워킹** > **세그먼트** 를 선택합니다. 
   
1. 세그먼트 이름에서 세로 줄임표를 선택하고 **편집** 을 선택합니다.
   
1. **서브넷 설정** 을 선택하고 서브넷에 DHCP IP 주소를 지정합니다. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="DHCP 서버 사용을 위한 DHCP IP 주소를 지정하는 서브넷을 설정하는 방법을 보여 주는 스크린샷." border="true":::
      
1. 필요한 경우 게이트웨이 IP 주소를 수정하고 DHCP 범위 IP를 입력합니다. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="DHCP 서버 사용을 위한 게이트웨이 IP 주소 및 DHCP 범위를 보여 주는 스크린샷." border="true":::
      
1. **적용** 을 선택하고 **저장** 을 선택합니다. 세그먼트에는 DHCP 서버 풀이 할당됩니다.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="DHCP 서버 사용을 위해 세그먼트에 할당된 DHCP 서버 풀을 보여 주는 스크린샷." border="true":::


## <a name="use-a-third-party-external-dhcp-server"></a>타사 외부 DHCP 서버 사용

타사의 외부 DHCP 서버를 사용하려는 경우 NSX-T Manager에서 DHCP 릴레이 서비스를 만들어야 합니다. 또한 DHCP IP 주소 범위를 지정합니다.


>[!IMPORTANT]
>2021년 7월 1일 이후에 생성된 클라우드의 경우 환경의 기본 계층 1 게이트웨이에서 DHCP를 구성하는 데 NSX-T 작업의 간소화된 보기를 사용해야 합니다.


### <a name="create-dhcp-relay-service"></a>DHCP 릴레이 서비스 만들기

NSX 기반이 아닌 DHCP 서비스에 DHCP 릴레이를 사용합니다. 예를 들어 Azure VMware Solution, Azure IaaS 또는 온-프레미스에서 DHCP를 실행하는 VM입니다.

1. NSX-T 관리자에서 **네트워킹** > **DHCP** 를 선택한 다음, **서버 추가** 를 선택합니다.

1. **서버 형식** 에 **DHCP 릴레이** 를 선택하고 서버 이름 및 IP 주소를 입력한 후, **저장** 을 선택합니다.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="NSX-T Manager에서 DHCP 릴레이 서비스를 만드는 방법을 보여 주는 스크린샷." border="true":::

1. **계층 1 게이트웨이** 를 선택하고 계층 1 게이트웨이에서 세로 줄임표를 선택한 다음 **편집** 을 선택합니다.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="계층 1 게이트웨이를 편집하는 방법을 보여 주는 스크린샷." border="true":::

1. **IP 할당 집합 없음** 을 선택하여 IP 주소 할당을 정의합니다.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="계층 1 게이트웨이에 서브넷을 추가하는 방법을 보여 주는 스크린샷." border="true":::

1. **형식** 은 **DHCP 서버** 를 선택합니다. 
   
1. **DHCP 서버** 는 **DHCP 릴레이**, **저장** 을 차례로 선택합니다.

1. 다시 **저장** 을 선택하고 **편집 닫기** 를 선택합니다.


### <a name="specify-the-dhcp-ip-address-range"></a>DHCP IP 주소 범위 지정

DHCP 서버에 대한 릴레이를 만들 때 DHCP IP 주소 범위를 지정합니다.

>[!NOTE]
>IP 주소 범위는 구독과 온-프레미스 네트워크의 다른 가상 네트워크에서 사용되는 IP 범위와 겹치지 않아야 합니다.

1. NSX-T Manager에서 **네트워킹** > **세그먼트** 를 선택합니다. 
   
1. 세그먼트 이름에서 세로 줄임표를 선택하고 **편집** 을 선택합니다.
   
1. **서브넷 설정** 을 선택하고 서브넷에 DHCP IP 주소를 지정합니다. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="DHCP IP 주소를 지정하는 서브넷을 설정하는 방법을 보여 주는 스크린샷." border="true":::
      
1. 필요한 경우 게이트웨이 IP 주소를 수정하고 DHCP 범위 IP를 입력합니다. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="게이트웨이 IP 주소 및 DHCP 범위를 보여 주는 스크린샷." border="true":::
      
1. **적용** 을 선택하고 **저장** 을 선택합니다. 세그먼트에는 DHCP 서버 풀이 할당됩니다.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="세그먼트에 할당된 DHCP 서버 풀을 보여 주는 스크린샷." border="true":::


## <a name="next-steps"></a>다음 단계

Azure VMware Solution VM에서 비 NSX-T DHCP 서버로 DHCP 요청을 보내려면 [L2 확장 VMware HCX 네트워크에서 DHCP 구성](configure-l2-stretched-vmware-hcx-networks.md) 절차를 참조하세요.
