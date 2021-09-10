---
title: 자습서 - Azure VMware Solution에서 네트워크 세그먼트 추가
description: vCenter에서 VM(가상 머신)에 사용할 네트워크 세그먼트를 추가하는 방법을 알아봅니다.
ms.topic: tutorial
ms.custom: contperf-fy22q1
ms.date: 07/16/2021
ms.openlocfilehash: a678e29591bc3ca17a1ec6638e111ffeb8c264b0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734224"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>자습서: Azure VMware Solution에서 네트워크 세그먼트 추가 

Azure VMware Solution을 배포한 후 NSX-T Manager 또는 Azure Portal에서 NSX-T 네트워크 세그먼트를 구성할 수 있습니다. 구성된 세그먼트는 Azure VMware Solution, NSX-T Manager, vCenter에 표시됩니다. NSX-T는 기본적으로 **활성/활성** 모드의 NSX-T 계층 0 게이트웨이와 **활성/대기** 모드의 기본 NSX-T 계층 1 게이트웨이로 사전 프로비저닝됩니다.  이러한 게이트웨이를 통해 세그먼트(논리적 스위치)를 연결하고 동-서 및 북-남 연결을 제공할 수 있습니다. 

>[!TIP]
>Azure Portal은 VMware 관리자가 정기적으로 필요로 하고 NSX-T Manager에 익숙하지 않은 사용자를 대상으로 하는 NSX-T 작업의 간소화된 보기를 제공합니다. 


이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * NSX-T Manager 또는 Azure Portal을 사용하여 네트워크 세그먼트 추가
> * 새 네트워크 세그먼트 확인 

## <a name="prerequisites"></a>필수 구성 요소

vCenter 및 NSX-T Manager 인터페이스에 대한 액세스 권한이 있는 Azure VMware Solution 프라이빗 클라우드. 자세한 내용은 [네트워킹 구성](tutorial-configure-networking.md) 자습서를 참조하세요.

## <a name="use-nsx-t-manager-to-add-network-segment"></a>NSX-T Manager를 사용하여 네트워크 세그먼트 추가 

vCenter에서 만든 VM(가상 머신)은 NSX-T에서 만든 네트워크 세그먼트에 배치되고 vCenter에 표시됩니다.

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="use-azure-portal-to-add-an-nsx-t-segment"></a>Azure Portal을 사용하여 NSX-T 세그먼트 추가

[!INCLUDE [create-nsxt-segment-azure-portal-steps](includes/create-nsxt-segment-azure-portal-steps.md)]


## <a name="verify-the-new-network-segment"></a>새 네트워크 세그먼트 확인

새 네트워크 세그먼트가 있는지 확인합니다. 이 예제에서 **ls01** 은 새 네트워크 세그먼트입니다.

1. NSX-T Manager에서 **네트워킹** > **세그먼트** 를 선택합니다. 

    :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="NSX-T에 새 네트워크 세그먼트가 있다는 확인 및 상태 스크린샷":::

1. vCenter에서 **네트워킹** > **SDDC-Datacenter** 를 선택합니다.

    :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="vCenter에 새 네트워크 세그먼트가 있다는 확인 스크린샷":::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 vCenter의 VM에 사용할 NSX-T 네트워크 세그먼트를 만들었습니다. 

이제 다음을 수행할 수 있습니다. 

- [Azure VMware Solution에서 DHCP 구성 및 관리](configure-dhcp-azure-vmware-solution.md)
- [Azure VMware Solution에서 VM을 배포하는 콘텐츠 라이브러리 만들기](deploy-vm-content-library.md) 
- [온-프레미스 환경을 프라이빗 클라우드로 피어링](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
