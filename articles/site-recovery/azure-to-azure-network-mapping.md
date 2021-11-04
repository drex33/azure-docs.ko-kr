---
title: Azure Site Recovery에서 두 지역 간 가상 네트워크 매핑
description: Azure Site Recovery를 사용한 Azure VM 재해 복구 시 두 Azure 지역 간에 가상 네트워크를 매핑하는 방법에 대해 알아봅니다.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: harshacs
ms.openlocfilehash: afc8bd93704008860882150b53e1624072871778
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441815"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>VNet의 네트워크 매핑 및 IP 주소 설정

이 문서에서는 서로 다른 Azure 지역에 있는 Azure VNet(가상 네트워크)의 두 인스턴스를 매핑하는 방법과 네트워크 간에 IP 주소를 설정하는 방법을 설명합니다. 네트워크 매핑은 복제를 사용하도록 설정할 때 원본 네트워크를 기반으로 하는 대상 네트워크 선택에 대한 기본 동작을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

네트워크를 매핑하려면 원본 및 대상 Azure 지역에 [Azure VNet](../virtual-network/virtual-networks-overview.md)이 있어야 합니다.

## <a name="set-up-network-mapping-manually-optional"></a>네트워크 매핑 수동 설정(선택 사항)

>[!참고 이제 전 세계 두 Azure 지역 간에 복제를 수행할 수 있습니다. 고객은 더 이상 지역 내 복제로 제한되지 않습니다.

다음과 같이 네트워크를 매핑합니다.

1. **Site Recovery 인프라** 에서 **+네트워크 매핑** 을 클릭합니다.

    ![ 네트워크 매핑 만들기](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. **네트워크 매핑 추가** 에서 원본 및 대상 위치를 선택합니다. 이 예제에서는 원본 VM이 동아시아 지역에서 실행되고 동남 아시아 지역에 복제됩니다.

    ![원본 및 대상 선택](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. 이제 반대 방향에 네트워크 매핑을 만듭니다. 이 예제에서는 원본이 동남 아시아이고 대상이 동아시아입니다.

    ![네트워크 매핑 추가 창 - 대상 네트워크의 원본 및 대상 위치 선택](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>복제를 사용하도록 설정할 때 네트워크 매핑

Azure VM에 대한 재해 복구를 구성하기 전에 네트워크 매핑을 준비하지 않은 경우 [복제를 설정하고 활성화](azure-to-azure-how-to-enable-replication.md)할 때 대상 네트워크를 지정할 수 있습니다. 이렇게 하면 다음과 같은 상황이 발생합니다.

- 선택하는 대상에 따라 Site Recovery가 자동으로 원본 지역에서 대상 지역으로 그리고 대상 지역에서 원본 지역으로 네트워크 매핑을 만듭니다.
- 기본적으로 Site Recovery는 원본 네트워크와 동일한 대상 지역에 네트워크를 만듭니다. Site Recovery는 원본 네트워크의 이름에 **-asr** 을 접두사로 추가합니다. 대상 네트워크를 사용자 지정할 수 있습니다.
- 원본 네트워크에 대한 네트워크 매핑이 이미 발생한 경우, 더 많은 VM에 대해 복제를 사용하도록 설정할 때 매핑된 대상 네트워크가 항상 기본값이 됩니다. 드롭다운에서 사용 가능한 다른 옵션을 선택하여 대상 가상 네트워크를 변경하도록 선택할 수 있습니다.
- 새 복제에 대한 기본 대상 가상 네트워크를 변경하려면 기존 네트워크 매핑을 수정해야 합니다.
- 지역 A에서 지역 B로 네트워크 매핑을 수정하려면, 먼저 지역 B에서 지역 A로 네트워크 매핑을 삭제해야 합니다. 역방향 매핑을 삭제한 후 지역 A에서 지역 B로 네트워크 매핑을 수정하고, 관련 역방향 매핑을 만듭니다.

>[!NOTE]
>* 네트워크 매핑을 수정하면 새 VM 복제에 대한 기본값만 변경됩니다. 기존 복제에 대한 대상 가상 네트워크 선택에는 영향을 주지 않습니다.
>* 기존 복제에 대한 대상 네트워크를 수정하려면 복제된 항목의 **네트워크** 설정 이동합니다.

## <a name="specify-a-subnet"></a>서브넷 지정

원본 VM의 서브넷 이름에 따라 대상 VM의 서브넷이 선택됩니다.

- 원본 VM 서브넷과 이름이 같은 서브넷을 대상 네트워크에서 사용할 수 있는 경우 해당 서브넷은 대상 VM에 대해 설정됩니다.
- 대상 네트워크에 동일한 이름의 서브넷이 없는 경우 사전순으로 첫 번째 서브넷이 대상 서브넷으로 설정됩니다.
- VM에 대한 **네트워크** 설정에서 대상 서브넷을 수정할 수 있습니다.

    ![네트워크 컴퓨팅 속성 창](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>대상 VM의 IP 주소 설정

대상 가상 머신에 있는 각 NIC의 IP 주소는 다음과 같이 구성됩니다.

- **DHCP**: 원본 VM의 NIC에서 DHCP를 사용하는 경우 대상 VM의 NIC 역시 DHCP를 사용하도록 설정됩니다.
- **고정 IP 주소**: 원본 VM의 NIC에서 고정 IP 주소를 사용하는 경우 대상 VM NIC 역시 고정 IP 주소를 사용합니다.

보조 IP 구성에도 동일하게 적용됩니다.

## <a name="ip-address-assignment-during-failover"></a>장애 조치(failover) 중에 IP 주소 할당

**원본 및 대상 서브넷** | **세부 정보**
--- | ---
동일한 주소 공간 | 원본 VM NIC의 IP 주소는 대상 VM NIC IP 주소로 설정됩니다.<br/><br/> 해당 주소를 사용할 수 없는 경우 그 다음으로 사용 가능한 IP 주소가 대상으로 설정됩니다.
다른 주소 공간 | 대상 서브넷에서 그 다음으로 사용 가능한 IP 주소가 대상 VM NIC 주소로 설정됩니다.



## <a name="ip-address-assignment-during-test-failover"></a>테스트 장애 조치(failover) 중에 IP 주소 할당

**대상 네트워크** | **세부 정보**
--- | ---
대상 네트워크는 장애 조치(failover) VNet | - 대상 IP 주소는 동일한 IP 주소를 사용하는 고정 주소입니다. <br/><br/>  - 동일한 IP 주소가 이미 할당된 경우, 각 서브넷 범위 끝에서 사용 가능한 다음 IP 주소가 할당됩니다. 예를 들어 원본 IP 주소가 10.0.0.19이고 장애 조치(failover) 네트워크에서 10.0.0.0/24 범위를 사용하는 경우 대상 VM에 할당되는 그 다음 IP 주소는 10.0.0.254입니다.
대상 네트워크는 장애 조치(failover) VNet이 아님 | - 대상 IP 주소는 동일한 IP 주소를 사용하는 고정 주소입니다.<br/><br/>  - 동일한 IP 주소가 이미 할당된 경우, 각 서브넷 범위 끝에서 사용 가능한 다음 IP 주소가 할당됩니다.<br/><br/> 예: 원본 고정 IP 주소가 10.0.0.19이고 장애 조치(failover) 네트워크가 아니고 범위가 10.0.0.0/24인 네트워크에서 장애 조치(failover)되는 경우 대상 고정 IP 주소는 10.0.0.0.19(사용 가능한 경우)이고, 그렇지 않은 경우 10.0.0.254입니다.

- 장애 조치(failover) VNet은 재해 복구를 설정할 때 선택한 대상 네트워크입니다.
- 테스트 장애 조치(failover)에는 항상 비 프로덕션 네트워크를 사용하는 것이 좋습니다.
- VM의 **네트워크** 설정에서 대상 IP 주소를 수정할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- Azure VM 재해 복구와 관련된 [네트워킹 가이드](./azure-to-azure-about-networking.md)를 검토합니다.
- 장애 조치(failover) 후 IP 주소 유지에 대해 [자세히 알아봅니다](site-recovery-retain-ip-azure-vm-failover.md).
