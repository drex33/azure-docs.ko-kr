---
title: VMware HCX MON(Mobility Optimized Networking) 지침
description: MON(Mobility Optimized Networking)에 대한 Azure VMware Solution 관련 사용 사례에 대해 알아봅니다.
ms.topic: reference
ms.date: 10/04/2021
ms.openlocfilehash: 1b170615b5a0ec0df81a8be7c71b8ac5ae17a24a
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424732"
---
# <a name="vmware-hcx-mobility-optimized-networking-mon-guidance"></a>VMware HCX MON(Mobility Optimized Networking) 지침

>[!IMPORTANT] 
>HCX MON을 사용하도록 설정하기 전에 아래 제한 사항 및 지원되지 않는 구성을 읽어보세요.
>
>[HCX NE에 대해 지원되지 않는 원본 구성](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-DBDB4D1B-60B6-4D16-936B-4AC632606909.html)
> 
>[MON을 포함한 HCX 배포에 대한 제한 사항](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-BEC26054-D560-46D0-98B4-7FF09501F801.html)


[HCX MON(Mobility Optimized Networking)은](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-0E254D74-60A9-479C-825D-F373C41F40BC.html) [HCX NE(네트워크 확장)를](configure-hcx-network-extension.md)사용할 때 사용할 수 있는 선택적 기능입니다. MON은 특정 시나리오에서 최적의 트래픽 라우팅을 제공하여 확장된 네트워크의 온-프레미스 리소스와 클라우드 기반 리소스 간의 네트워크 트래픽을 차단합니다. 

마이그레이션 주기 전체에서 MON은 다음을 위해 애플리케이션 이동성을 최적화합니다.

- 확장된 네트워크를 사용하는 경우 VM(가상 머신)에서 VM L2 통신으로 최적화 

- 온-프레미스, Azure VMware Solution 및 Azure 간의 비대칭 트래픽 흐름 최적화 및 방지


이 문서에서는 MON에 대한 Azure VMware Solution 관련 사용 사례에 대해 알아봅니다.


## <a name="optimize-traffic-flows-across-standard-and-stretched-segments-on-the-private-cloud-side"></a>프라이빗 클라우드 쪽의 표준 및 확장된 세그먼트에서 트래픽 흐름 최적화 

이 시나리오에서는 VM1이 NE를 사용하여 클라우드로 마이그레이션되어 VM 대기 시간이 가장 적합한 VM을 제공합니다. 따라서 VM1은 로컬 Azure VMware Solution 세그먼트에서 VM3에 대한 짧은 대기 시간이 필요합니다. 트래픽에 대한 최적의 경로(파란색 선)를 보장하기 위해 VM1 게이트웨이를 온-프레미스에서 Azure VMware Solution(클라우드)로 마이그레이션합니다. 게이트웨이가 온-프레미스(빨간색 선)로 유지되는 경우 tromboning 효과와 더 높은 대기 시간이 관찰됩니다. 

>[!NOTE]
>VM 게이트웨이를 클라우드 쪽으로 마이그레이션하지 않고 MON을 사용하도록 설정하면 트래픽 흐름에 대한 최적의 경로가 보장되지 않습니다.  또한 정책 경로의 평가를 허용하지 않습니다.

:::image type="content" source="media/tutorial-vmware-hcx/hcx-mon-user-case-diagram-1.png" alt-text="확장된 네트워크를 사용하는 경우 VM 간 L2 통신에 대한 최적화를 보여 주는 다이어그램" border="false":::



## <a name="optimize-and-avoid-asymmetric-traffic-flows"></a>비대칭 트래픽 흐름 최적화 및 방지 

이 시나리오에서는 온-프레미스의 VM이 Azure VMware Solution 마이그레이션되고 L2에 참여하고 L3 트래픽이 서비스에 액세스하기 위해 다시 온-프레미스로 이동한다고 가정합니다. 또한 Azure의 일부 VM 통신(Azure VMware Solution 연결된 vNET)이 Azure VMware Solution 프라이빗 클라우드에 연결할 수 있다고 가정합니다.

>[!IMPORTANT]
>여기서 중요한 점은 비대칭 트래픽 흐름을 신중하게 계획하고 방지하는 것입니다. 

기본적으로 MON을 사용하지 않고 MON이 없는 확장된 네트워크에 Azure VMware Solution VM은 ExpressRoute 기본 설정 경로를 사용하여 온-프레미스로 다시 통신할 수 있습니다. 이상적으로 고객 사용 사례에 따라 MON을 사용하도록 설정된 Azure VMware Solution 확장 세그먼트의 VM이 EXPRESSRoute를 통해 NE 또는 T0 게이트웨이를 통해 온-프레미스로 다시 트래버스하는 방법을 평가해야 하지만 트래픽 흐름은 대칭적으로 유지됩니다.

예를 들어 NE 경로를 선택하는 경우 MON 정책 경로는 온-프레미스 쪽에서 서브넷을 구체적으로 처리해야 합니다. 그렇지 않으면 0.0/0 경로가 사용됩니다. 정책 경로는 NE 세그먼트 아래에서 찾을 수 있으며 고급을 선택합니다. 기본적으로 모든 RFC1918 IP 주소는 MON 정책 경로 정의에 포함됩니다. 

:::image type="content" source="media/tutorial-vmware-hcx/default-hcx-mon-policy-based-routes.png" alt-text="기본 정책 기반 경로를 보여주는 스크린샷.":::

정책 경로는 VM 게이트웨이가 클라우드로 마이그레이션되는 경우에만 평가됩니다. 이 구성의 효과는 대상에 대해 일치하는 서브넷이 NE 어플라이언스를 통해 터널화된다는 것입니다.  일치하지 않으면 T0 게이트웨이를 통해 라우팅됩니다.

>[!NOTE]
>Azure VMware Solution MON을 사용하기 위한 특별한 고려 사항은 BGP를 통해 보급된 /32 경로를 피어에 제공하는 것입니다. 여기에는 ExpressRoute 연결을 통해 온-프레미스 및 Azure가 포함됩니다. 예를 들어 Azure의 VM은 Azure VMware Solution MON 사용 세그먼트에서 Azure VMware Solution VM의 경로를 학습합니다. 반환 트래픽이 예상대로 T0으로 다시 전송되면 반환 서브넷이 RFC1918 일치인 경우 트래픽은 T0 대신 NE를 통해 강제됩니다.  그런 다음 ExpressRoute를 통해 온-프레미스 쪽의 Azure로 다시 송신합니다.  이로 인해 중간 및 비대칭 라우팅 동작에서 상태 비대칭 방화벽에 대한 혼동이 발생할 수 있습니다. 또한 NE MON 세그먼트의 VM이 Azure VMware Solution T0을 통해 또는 NE를 통해 온-프레미스로 다시 인터넷에 액세스해야 하는 방법을 결정하는 것이 좋습니다.

:::image type="content" source="media/tutorial-vmware-hcx/hcx-mon-user-case-diagram-3.png" alt-text="RFC1918 송신 및 송신 트래픽 흐름을 보여 주는 다이어그램" border="false":::

위의 다이어그램에 설명된 대로 각 필수 서브넷에 대한 정책 경로를 일치시켜야 합니다. 그렇지 않으면 트래픽이 NE가 아닌 T0을 통해 라우팅됩니다.

 
정책 경로에 대한 자세한 내용은 [모바일 최적화 네트워킹 정책 경로를 참조하세요.](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-F45B1DB5-C640-4A75-AEC5-45C58B1C9D63.html)

