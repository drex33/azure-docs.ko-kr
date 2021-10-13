---
title: Azure VMware Solution을 사용하여 NSX-T 네트워크 세그먼트 추가
description: Azure Portal에서 Azure VMware Solution의 NSX-T 네트워크 세그먼트를 추가하는 단계입니다.
ms.topic: include
ms.date: 07/16/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 812266ef6c7ed937c21c1c78c2c9b2aa5ff1043d
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638176"
---
<!-- Used in configure-dhcp-azure-vmware-solution.md and tutorial-nsx-t-network-segment.md -->


>[!NOTE]
>DHCP를 사용하려는 경우 NSX-T 네트워크 세그먼트를 구성하기 전에 [DHCP 서버 또는 DHCP 릴레이를 구성](../configure-dhcp-azure-vmware-solution.md)해야 합니다. 

1. Azure VMware Solution 프라이빗 클라우드의 **워크로드 네트워킹** 에서 **세그먼트** > **추가** 를 선택합니다. 

2. 새 논리 세그먼트에 대한 세부 정보를 제공하고 **확인** 을 선택합니다.

   :::image type="content" source="../media/networking/add-new-nsxt-segment.png" alt-text="Azure Portal에서 새 NSX-T 세그먼트를 추가하는 방법을 보여 주는 스크린샷":::

   - **세그먼트 이름** - vCenter에 표시되는 세그먼트의 이름입니다.

   - **서브넷 게이트웨이** - 서브넷 마스크가 있는 세그먼트의 서브넷 게이트웨이 IP 주소입니다. VM은 논리 세그먼트에 연결되고, 이 세그먼트에 연결된 모든 VM은 동일한 서브넷에 속합니다.  또한 이 논리 세그먼트에 연결된 모든 VM은 동일한 세그먼트의 IP 주소를 가져야 합니다.

   - **DHCP**(선택 사항) - 논리적 세그먼트의 DHCP 범위입니다. 세그먼트에서 DHCP를 사용하도록 [DHCP 서버 또는 DHCP 릴레이](../configure-dhcp-azure-vmware-solution.md)를 구성해야 합니다.    

   >[!NOTE]
   >**연결된 게이트웨이** 는 기본적으로 선택되며 읽기 전용입니다.  계층 1 게이트웨이와 세그먼트 유형 정보를 표시합니다. 
   >
   >- **T1** - NSX-T Manager의 계층 1 게이트웨이 이름입니다. 프라이빗 클라우드는 활성/활성 모드의 NSX-T 계층 0 게이트웨이와 활성/대기 모드의 기본 NSX-T 계층 1 게이트웨이로 제공됩니다.  Azure VMware Solution 콘솔을 통해 생성된 세그먼트는 기본 계층 1 게이트웨이에만 연결되며 이 세그먼트의 워크로드는 동-서 및 북-남 연결을 제공합니다. NSX-T Manager를 통해 더 많은 계층 1 게이트웨이를 만들 수 있습니다. NSX-T Manager 콘솔에서 생성된 계층 1 게이트웨이는 Azure VMware Solution 콘솔에 표시되지 않습니다. 
   >
   >- **유형** - Azure VMware Solution에서 지원하는 오버레이 세그먼트입니다.

이제 세그먼트가 Azure VMware Solution, NSX-T Manger, vCenter에 표시됩니다.
