---
title: Azure VMware Solution용 포트 미러링 구성
description: 한 네트워크 스위치 포트에서 다른 포트로 각 패킷의 복사본을 전달하는 것과 관련된 네트워크 트래픽을 모니터링하도록 포트 미러링을 구성할 수 있습니다.
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 07/16/2021
ms.openlocfilehash: 3f1b205cc75911132abeed83d1e52e3566919551
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567927"
---
# <a name="configure-port-mirroring-in-the-azure-portal"></a>Azure Portal에서 포트 미러링 구성

Azure VMware Solution을 배포한 후 Azure Portal에서 포트 미러링을 구성할 수 있습니다. 포트 미러링은 미러링된 데이터를 수신하는 포트에 프로토콜 분석기를 배치합니다. 원본, VM(가상 머신) 또는 VM 그룹의 트래픽을 분석한 후 정의된 대상으로 보냅니다. 

이 방법 가이드에서는 한 네트워크 스위치 포트에서 다른 포트로 각 패킷의 복사본을 전달하는 것과 관련된 네트워크 트래픽을 모니터링하도록 포트 미러링을 구성합니다. 

## <a name="prerequisites"></a>필수 구성 요소

vCenter 및 NSX-T Manager 인터페이스에 대한 액세스 권한이 있는 Azure VMware Solution 프라이빗 클라우드. 자세한 내용은 [네트워킹 구성](tutorial-configure-networking.md) 자습서를 참조하세요.

## <a name="create-the-vms-or-vm-groups"></a>VM 또는 VM 그룹 만들기

원본/대상 VM 또는 VM 그룹을 만듭니다. 원본 그룹에는 네트워크 트래픽이 미러링되는 VM이 한 개 또는 여러 개 있습니다.

1. Azure VMware Solution 프라이빗 클라우드의 **워크로드 네트워킹** 에서 **포트 미러링** > **VM 그룹** > **추가** 를 선택합니다.

   :::image type="content" source="media/networking/add-port-mirroring-vm-groups.png" alt-text="포트 미러링을 위한 VM 그룹을 만드는 방법을 보여 주는 스크린샷입니다.":::

1. 새 VM 그룹의 이름을 입력하고 목록에서 VM을 선택한 후 **확인** 을 선택합니다.

1. 이러한 단계를 반복하여 대상 VM 그룹을 만듭니다.

   >[!NOTE]
   >포트 미러링 프로필을 만들기 전에 원본/대상 VM 그룹을 모두 만들었는지 확인합니다.

## <a name="create-a-port-mirroring-profile"></a>포트 미러링 프로필 만들기

원본/대상 VM 그룹에 대한 트래픽 방향을 정의하는 포트 미러링 프로필을 만듭니다.

1. **포트 미러링** > **포트 미러링** > **추가** 를 선택한 후 다음을 제공합니다.

   :::image type="content" source="media/networking/add-port-mirroring-profile.png" alt-text="포트 미러링 프로필에 필요한 정보를 보여 주는 스크린샷입니다.":::

   - **포트 미러링 이름** -프로필에 대한 설명이 포함된 이름입니다.

   - **방향** -수신, 송신 또는 양방향 중에서 선택합니다.

   - **원본** - 원본 VM 그룹을 선택합니다.

   - **대상** - 대상 VM 그룹을 선택합니다.

   - **설명** -포트 미러링에 대한 설명을 입력합니다.

1. **확인** 을 선택하여 프로필을 완료합니다. 

   프로필 및 VM 그룹이 Azure VMware Solution 콘솔에 표시됩니다.
