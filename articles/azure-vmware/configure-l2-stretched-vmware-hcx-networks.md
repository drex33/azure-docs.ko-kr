---
title: L2 확장 VMware HCX 네트워크에서 DHCP 구성
description: Azure VMware Solution VM에서 비 NSX-T DHCP 서버로 DHCP 요청을 보내는 방법을 알아봅니다.
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 05/28/2021
ms.openlocfilehash: c59df75e70bf8913575b70b80048b14ae42d62a2
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530598"
---
# <a name="configure-dhcp-on-l2-stretched-vmware-hcx-networks"></a>L2 확장 VMware HCX 네트워크에서 DHCP 구성

DHCP 서버가 온-프레미스 데이터 센터에 있는 경우 VMware HCX L2 스트레치 네트워크의 VM(가상 머신)에 대해 DHCP가 작동하지 않습니다. NSX는 기본적으로 모든 DHCP 요청이 L2 확장을 통과하는 것을 차단하기 때문입니다. 따라서 Azure VMware 솔루션 VM에서 비 NSX-T DHCP 서버로 DHCP 요청을 보내려면 L2 확장 VMware HCX 네트워크에서 DHCP를 구성해야 합니다.

1. (선택 사항) L2 확장의 세그먼트 이름을 찾아야 하는 경우:

   1. 온-프레미스 vCenter에 로그인하고, **홈** 에서 **HCX** 를 선택합니다.

   1. **서비스** 에 있는 **네트워크 확장** 을 선택합니다.

   1. Azure VMware Solution에서 온-프레미스로 DHCP 요청을 지원하려는 네트워크 확장을 선택합니다.

   1. 대상 네트워크 이름을 기록해 둡니다.

      :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="VMware vSphere 클라이언트의 네트워크 확장 스크린샷" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. NSX-T Manager에서 **네트워킹** > **세그먼트** > **세그먼트 프로필** 을 선택합니다.

1. **세그먼트 프로필 추가**, **세그먼트 보안** 을 차례로 선택합니다.

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="NSX-T에서 세그먼트 프로필을 추가하는 방법에 대한 스크린샷" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. 이름 및 태그를 제공한 다음 **BPDU 필터** 를 켜기로 설정하고 모든 DHCP를 끄기로 설정합니다.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="BPDU 필터가 켜져 있고 DHCP가 꺼져 있는 스크린샷" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::
    
   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="세그먼트 보안 필드 스크린샷" lightbox="media/manage-dhcp/edit-segment-security.png":::
