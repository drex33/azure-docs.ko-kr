---
title: Azure Site Recovery를 사용 하 여 여러 IP 주소의 장애 조치 구성
description: Azure Vm에 대 한 보조 IP configs의 장애 조치 (failover)를 구성 하는 방법을 설명 합니다.
services: site-recovery
author: rishjai-msft
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: rishjai
ms.openlocfilehash: a7437650c11797d5a00c7c8684138632555b05a2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483074"
---
# <a name="configure-failover-of-multiple-ip-addresses-with-azure-site-recovery"></a>Azure Site Recovery를 사용 하 여 여러 IP 주소의 장애 조치 구성

VM에 연결된 모든 NIC에는 하나 이상의 IP 구성이 연결되어 있습니다. 각 구성에는 하나의 정적 또는 동적 개인 IP 주소가 할당됩니다. 각 구성에는 하나의 공용 IP 주소 리소스가 연결되어 있을 수도 있습니다. 공용 IP 주소 리소스에는 동적 또는 정적 공용 IP 주소가 할당되어 있습니다. [Azure의 ip 주소](../virtual-network/ip-services/public-ip-addresses.md)에 대 한 자세한 내용은 AZURE의 ip 주소 문서를 참조 하세요.

현재 Site Recovery는 기본 IP 구성의 장애 조치 (failover)를 자동으로 구성 합니다. 이 문서에서는 Site Recovery를 사용 하 여 보조 IP Configs 장애 조치 (failover)를 구성 하는 방법을 설명 합니다. Azure Vm에 대해서만 지원 됩니다.

## <a name="configure-secondary-ip-address-failover-via-azure-portal"></a>Azure Portal를 통해 보조 IP 주소 장애 조치 (Failover) 구성

Site Recovery VM에 대 한 복제를 사용 하도록 설정 하는 경우 기본 IP 구성의 장애 조치 (failover)를 자동으로 구성 합니다. 복제 사용이 완료 된 후에는 보조 IP 구성을 수동으로 구성 해야 합니다. 이렇게 하려면 하나 이상의 보조 IP 구성을 포함 하는 보호 된 VM이 있어야 합니다.

설명 된 단계를 수행 합니다.
1. 복제 된 항목 페이지에서 **네트워크** 블레이드로 이동 합니다.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/network-tab.png" alt-text="복제 된 항목 블레이드":::
    

2. 다음 강조 표시 된 텍스트가 표시 됩니다.  **편집** 을 클릭 하 여 수정 합니다.
 
    :::image type="content" source="./media/concepts-multiple-ip-address-failover/network-edit.png" alt-text="네트워크 탭 편집 모드" lightbox="./media/concepts-multiple-ip-address-failover/network-edit-expanded.png":::    

3. "+ IP 구성"을 클릭 합니다. 모든 IP 구성을 추가 하거나 선택적으로 IP 구성을 추가 하는 두 가지 옵션이 표시 됩니다.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/add-ip-configurations.png" alt-text="IP 구성 추가":::

4. **모든 보조 IP 구성 추가** 를 클릭 하면 아래 표에 모두 표시 되며, 원하는 대로 구성할 수 있습니다.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/add-all-ip-configurations.png" alt-text="모든 IP 구성 추가" lightbox="./media/concepts-multiple-ip-address-failover/add-all-ip-configurations-expanded.png":::    

5. 또는 선택을 클릭 하 **고 보조 IP 구성을 추가** 하는 경우 장애 조치 (failover)를 위해 구성 하려는 IP 구성을 선택 하 고 추가할 수 있는 블레이드가 열립니다.

    :::image type="content" source="./media/concepts-multiple-ip-address-failover/select-and-add-ip-configurations.png" alt-text="IP 구성 선택 및 추가":::

이제 추가한 각 IP 구성에 대해 개별적으로 장애 조치 (failover) 및 테스트 장애 조치 (Failover)를 위해 개인 IP, 공용 IP 및 백 엔드 풀에 대 한 값을 구성할 수 있습니다. 모든 작업을 완료 한 후 변경 내용을 저장 하는 것을 잊지 마세요.


## <a name="next-steps"></a>다음 단계
- [Azure Site Recovery와 함께 Traffic Manager 사용](../site-recovery/concepts-traffic-manager-with-site-recovery.md) 문서를 자세히 살펴봅니다.
- Traffic Manager [라우팅 메서드](../traffic-manager/traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.
- 애플리케이션 장애 조치(failover)를 자동화하는 [복구 계획](site-recovery-create-recovery-plans.md)에 대해 자세히 알아봅니다.
