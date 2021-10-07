---
title: NSX-T 네트워크 세그먼트 추가
description: NSX-T Manager에서 Azure VMware Solution의 NSX-T 네트워크 세그먼트를 추가하는 단계입니다.
ms.topic: include
ms.date: 03/13/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 1e6d50e1ca05ea15c4d6690965c112565dae76f5
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638588"
---
<!-- Used in configure-dhcp-azure-vmware-solution.md and tutorial-nsx-t-network-segment.md -->

1. NSX-T 관리자에서 **네트워킹** > **세그먼트** 를 선택한 다음, **세그먼트 추가** 를 선택합니다. 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="NSX-T Manager에서 새 세그먼트를 추가하는 방법을 보여 주는 스크린샷":::

1. 세그먼트의 이름을 입력합니다.

1. **연결된 게이트웨이** 로 Tier-1 Gateway(TNTxx-T1)를 선택하고 **형식** 을 Flexible로 유지합니다.

1. 미리 구성된 오버레이 **전송 영역**(TNTxx-OVERLAY-TZ)을 선택한 다음, **서브넷 설정** 을 선택합니다. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="새 NSX-T 네트워크 세그먼트를 추가하기 위한 세그먼트 세부 정보를 보여 주는 스크린샷":::

1. 게이트웨이 IP 주소를 입력한 다음, **추가** 를 선택합니다. 

   >[!IMPORTANT]
   >IP 주소는 겹치지 않는 RFC1918 주소 블록에 있어야 합니다. 그래야 새 세그먼트의 VM에 대한 연결을 보장합니다.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="새 세그먼트에 대한 게이트웨이의 IP 주소를 보여 주는 스크린샷":::

1. **적용** 을 선택한 다음, **저장** 을 선택합니다.

1. 세그먼트를 계속 구성하는 옵션을 거부하려면 **아니요** 를 선택합니다. 


