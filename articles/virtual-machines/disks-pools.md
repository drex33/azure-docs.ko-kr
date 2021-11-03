---
title: Azure 디스크 풀(미리 보기) 개요
description: Azure 디스크 풀(미리 보기)에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: d6e2eda8fd7bc2ba3b41b911b5964c2a65e33c14
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074551"
---
# <a name="azure-disk-pools-preview"></a>Azure 디스크 풀(미리 보기)

Azure 디스크 풀(미리 보기)는 애플리케이션과 워크로드가 단일 엔드포인트에서 관리 디스크 그룹에 액세스할 수 있게 하는 Azure 리소스입니다. 디스크 풀은 iSCSI(Internet Small Computer Systems Interface)를 통해 이 풀 안의 디스크에 대한 데이터 액세스를 사용할 수 있도록, iSCSI 대상을 노출할 수 있습니다. 각 디스크 풀에는 하나의 iSCSI 대상이 있을 수 있고, 각 디스크는 iSCSI LUN으로 노출될 수 있습니다. 디스크 풀의 디스크를 Azure VMware 솔루션 호스트에 데이터 저장소로 연결할 수 있습니다. 이렇게 하면 Azure VMware Solution 호스트에 독립적으로 스토리지 크기를 조정할 수 있습니다. 데이터 저장소가 구성되면 그 위에 볼륨을 만들어 VMware 인스턴스에 연결할 수 있습니다.

## <a name="how-it-works"></a>작동 방식

디스크 풀이 배포되면 관리되는 리소스 그룹이 자동으로 만들어집니다. 이 관리되는 리소스 그룹은 디스크 풀 작업에 필요한 모든 Azure 리소스를 포함합니다. 이 리소스 그룹의 명명 규칙은 MSP_(리소스 그룹 이름)_(디스크 풀 이름)\_(지역 이름)입니다.

관리 디스크를 디스크 풀에 추가하는 경우 디스크는 관리형 iSCSI 컨트롤러에 연결됩니다. 여러 관리 디스크를 디스크 풀에 스토리지 대상으로 추가할 수 있으며, 각 스토리지 대상은 디스크 풀의 iSCSI 대상 아래 iSCSI LUN으로 표시됩니다. 디스크 풀은 Azure VMware 솔루션에 대한 기본 지원을 제공합니다. Azure VMware Solution 클러스터는 해당 환경의 모든 Azure VMware Solution 호스트를 포괄하는 디스크 풀에 연결할 수 있습니다. 다음 다이어그램은 Azure VMware Solution에 디스크 풀을 사용하는 방법을 보여 줍니다.

:::image type="content" source="media/disks-pools/disk-pool-diagram.png" alt-text="디스크 풀 작동 방식, 각 iSCSI 컨트롤러가 iSCSI를 통해 각각의 Ultra 디스크에 액세스할 수 있는 방법 및 Azure VMware Solution 호스트가 iSCSI를 통해 iSCSI 컨트롤러에 액세스하는 방법을 설명하는 다이어그램":::

## <a name="restrictions"></a>제한

미리 보기에서는 시스템 노드 풀에 다음과 같은 제한이 있습니다.

- 프리미엄 Ssd와 표준 Ssd 또는 ultra 디스크만 디스크 풀에 추가할 수 있습니다.
    - 디스크 풀은 ultra disks와 premium/standard Ssd를 모두 포함 하도록 구성할 수 없습니다. 디스크 풀이 ultra disks를 사용 하도록 구성 된 경우에는 ultra 디스크만 포함할 수 있습니다. 마찬가지로 premium 및 standard Ssd를 사용 하도록 구성 된 디스크 풀은 premium 및 standard Ssd만 포함할 수 있습니다.
- [ZRS(영역 중복 스토리지)](disks-redundancy.md#zone-redundant-storage-for-managed-disks)를 사용하는 디스크는 현재 지원되지 않습니다. 

### <a name="regional-availability"></a>국가별 가용성

디스크 풀은 현재 다음 지역에서 제공됩니다.

- 오스트레일리아 동부
- 캐나다 중부
- 미국 중부
- 미국 동부
- 미국 서부 2
- 일본 동부
- 북유럽
- 서유럽
- 동남아시아
- 영국 남부


## <a name="billing"></a>결제

디스크 풀을 배포할 때 청구 비용이 발생하는 두 가지 주요 영역은 다음과 같습니다.

- 디스크 풀에 추가된 디스크
- 디스크 풀과 함께 제공되는 관리되는 리소스 그룹에 배포된 Azure 리소스. 다음과 같은 리소스가 여기에 해당합니다.
    - 가상 머신
    - 관리 디스크
    - 네트워크 인터페이스 하나
    - 진단 로그 및 메트릭용 스토리지 계정 하나
        
이 관리되는 리소스 그룹에 있는 리소스와, 실제 데이터 스토리지인 개별 디스크에 대해 요금이 청구됩니다. 예를 들어 하나의 P30 디스크가 추가된 디스크 풀이 있는 경우 P30 디스크 및 관리되는 리소스 그룹에 배포된 모든 리소스에 대해 요금이 청구됩니다. 이러한 리소스 및 디스크 외에, 디스크 풀에 대한 추가 서비스 요금은 없습니다. 관리되는 리소스에 대한 세부 정보는 [작동 방식](#how-it-works) 섹션을 참조하세요.

디스크 풀 비용을 평가하려면 [Azure 요금 계산기](https://azure.microsoft.com/pricing/calculator/)에서 VM 및 디스크의 지역별 가격 책정을 참조하세요. 디스크 풀에서 사용하는 Azure 리소스는 Azure Reservations(있는 경우)에서 고려할 수 있습니다.


## <a name="next-steps"></a>다음 단계

[디스크 풀 계획 지침](disks-pools-planning.md)을 참조하세요.
