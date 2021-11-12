---
title: VMware HCX MON(모빌리티 최적화 네트워킹) 지침
description: 모바일에 최적화 된 네트워킹을 위한 Azure VMware 솔루션 관련 사용 사례에 대해 알아봅니다 (MON).
ms.topic: reference
ms.date: 10/04/2021
ms.openlocfilehash: 34840fedbf2ae2dbf32711f4dfee307fe3a3a5ca
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373330"
---
# <a name="vmware-hcx-mobility-optimized-networking-mon-guidance"></a>VMware HCX MON(모빌리티 최적화 네트워킹) 지침

>[!NOTE]
>
>HCX 모바일에 최적화 된 네트워킹은 HCX 버전 4.1.0의 VMware 및 Azure VMware 솔루션에서 공식적으로 지원 됩니다. 

>[!IMPORTANT] 
>
>HCX MON을 사용 하도록 설정 하기 전에 다음 제한 사항 및 지원 되지 않는 구성을 읽어 보세요.
>
>[HCX NE에 대해 지원 되지 않는 원본 구성](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-DBDB4D1B-60B6-4D16-936B-4AC632606909.html)
> 
>[MON을 포함 한 모든 HCX 배포에 대 한 제한 사항](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-BEC26054-D560-46D0-98B4-7FF09501F801.html)


[Hcx 모바일에 최적화 된 네트워킹 (MON)](https://docs.vmware.com/en/VMware-HCX/4.2/hcx-user-guide/GUID-0E254D74-60A9-479C-825D-F373C41F40BC.html) 은 [Hcx 네트워크 확장 (NE)](configure-hcx-network-extension.md)을 사용할 때 사용할 수 있는 선택적 기능입니다. MON은 특정 시나리오에서 최적의 트래픽 라우팅을 제공 하 여 확장 된 네트워크에서 온-프레미스와 클라우드 기반 리소스 간의 네트워크 tromboning 방지 합니다. 

마이그레이션 주기 전체에서 월요일은 다음에 대 한 응용 프로그램 이동성을 최적화 합니다.

- 스트레치 네트워크를 사용 하는 경우 vm (가상 머신)에서 VM L2 통신에 최적화 

- 온-프레미스, Azure VMware 솔루션 및 Azure 간 비대칭 트래픽 흐름 최적화 및 방지


이 문서에서는 MON의 Azure VMware 솔루션 관련 사용 사례에 대해 알아봅니다.


## <a name="optimize-traffic-flows-across-standard-and-stretched-segments-on-the-private-cloud-side"></a>사설 클라우드의 표준 및 확장 된 세그먼트에서 트래픽 흐름 최적화 

이 시나리오에서 VM1는 VM 대기 시간에 대 한 최적의 VM을 제공 하는 NE를 사용 하 여 클라우드로 마이그레이션됩니다. 결과적으로 VM1는 로컬 Azure VMware 솔루션 세그먼트의 V M 3에 대해 낮은 대기 시간이 필요 합니다. VM1 게이트웨이를 온-프레미스에서 Azure VMware 솔루션 (클라우드)으로 마이그레이션하여 트래픽 (파란색 선)의 최적 경로를 확인 합니다. 게이트웨이가 온-프레미스 (빨간색 선)로 유지 되는 경우 tromboning 효과와 대기 시간이 더 관찰 됩니다. 

>[!NOTE]
>VM 게이트웨이를 클라우드 쪽으로 마이그레이션하지 않고 MON을 사용 하도록 설정 하는 경우 트래픽 흐름에 대 한 최적의 경로를 보장 하지 않습니다.  또한 정책 경로를 평가할 수 없습니다.

:::image type="content" source="media/tutorial-vmware-hcx/hcx-mon-user-case-diagram-1.png" alt-text="스트레치 된 네트워크를 사용 하는 경우 vm에서 VM L2 통신을 최적화 하는 방법을 보여 주는 다이어그램입니다." border="false":::



## <a name="optimize-and-avoid-asymmetric-traffic-flows"></a>비대칭 트래픽 흐름 최적화 및 방지 

이 시나리오에서는 온-프레미스의 VM이 Azure VMware 솔루션으로 마이그레이션 되었으며 L2에 참여 하 고, L3 트래픽이 온-프레미스로 다시 이동 하 여 서비스에 액세스 하는 것으로 가정 합니다. Azure VMware 솔루션 연결 vNET에서 azure의 일부 VM 통신이 Azure VMware 솔루션 사설 클라우드로 연결 될 수 있다고 가정 합니다.

>[!IMPORTANT]
>여기서 주요 점은 비대칭 트래픽 흐름을 신중 하 게 계획 하 고 방지 하는 것입니다. 

기본적으로 또는 MON을 사용 하지 않고 확대 된 네트워크의 Azure VMware 솔루션에 있는 VM은 Express 경로 기본 설정 경로를 사용 하 여 온-프레미스로 다시 통신할 수 있습니다. 이상적으로, 고객 사용 사례를 기반으로 하 여 Azure VMware 솔루션에서 확장 된 세그먼트에 대 한 VM이 Express 경로를 통해 NE 또는 T0 게이트웨이를 통해 온-프레미스로 다시 트래버스 되어야 하지만 트래픽 흐름을 대칭으로 유지 하는 방법을 평가 해야 합니다.

예를 들어 NE 경로를 선택 하는 경우 MON 정책 경로는 온-프레미스 쪽의 서브넷 주소를 구체적으로 설정 해야 합니다. 그렇지 않으면 0.0/0 경로가 사용 됩니다. 정책 경로는 NE 세그먼트 아래에서 고급을 선택 하 여 찾을 수 있습니다. 기본적으로 모든 RFC1918 IP 주소는 MON 정책 경로 정의에 포함 됩니다. 

:::image type="content" source="media/tutorial-vmware-hcx/default-hcx-mon-policy-based-routes.png" alt-text="기본 정책 기반 경로를 보여 주는 스크린샷":::

정책 경로는 VM 게이트웨이가 클라우드로 마이그레이션되는 경우에만 평가 됩니다. 이 구성의 효과는 대상에 대 한 일치 하는 서브넷이 NE 어플라이언스를 통해 터널링 된다는 것입니다.  일치 하지 않는 경우 T0 게이트웨이를 통해 라우팅됩니다.

>[!NOTE]
>Azure VMware 솔루션의 MON 사용에 대 한 특별 고려 사항은 BGP를 통해 알린/32 경로를 피어에 게 제공 하는 것입니다. 이는 Express 경로 연결을 통해 온-프레미스 및 Azure를 포함 합니다. 예를 들어 azure의 VM은 azure VMware Solution MON 사용 세그먼트에서 Azure VMware 솔루션 VM에 대 한 경로를 학습 합니다. 반환 트래픽이 예상 대로 T0 다시 전송 되 면 반환 서브넷이 RFC1918 일치 하는 경우 T0 대신 NE를 통해 트래픽이 강제로 적용 됩니다.  그런 다음 온-프레미스 쪽에서 Azure로 다시 Express 경로를 egresses.  이로 인해 중간 및 비대칭 라우팅 동작의 상태 저장 방화벽이 혼동 될 수 있습니다. 또한 Azure VMware 솔루션의 T0을 통해 또는 온-프레미스로 다시 전환 하 여 NE MON 세그먼트의 Vm이 인터넷에 액세스 해야 하는 방법을 결정 하는 것이 좋습니다.

:::image type="content" source="media/tutorial-vmware-hcx/hcx-mon-user-case-diagram-3.png" alt-text="RFC1918 송신 및 송신 트래픽 흐름을 보여 주는 다이어그램입니다." border="false":::

위의 다이어그램에 설명 된 것 처럼 중요도는 정책 경로를 필요한 각 서브넷과 일치 시키는 것입니다. 그렇지 않으면 트래픽이 NE가 아닌 T0를 통해 라우팅됩니다.

 
정책 경로에 대해 자세히 알아보려면 모바일에 [최적화 된 네트워킹 정책 경로](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-F45B1DB5-C640-4A75-AEC5-45C58B1C9D63.html)를 참조 하세요.

