---
title: 장애 조치(failover) VM에 대한 네트워킹 구성 사용자 지정 | Microsoft Docs
description: Azure Site Recovery를 사용한 Azure VM 복제 시 장애 조치(failover) VM에 대한 네트워킹 구성을 사용자 지정하는 방법의 개요를 제공합니다.
services: site-recovery
author: rishjai-msft
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 10/01/2021
ms.author: rishjai
ms.openlocfilehash: 79a33226da71071d8985daf723dde7314116ce37
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475156"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>대상 Azure VM의 네트워킹 구성 사용자 지정

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 한 지역에서 다른 지역으로 Azure VM을 복제 및 복구할 때 대상 Azure VM(가상 머신)에서 네트워킹 구성을 사용자 지정하는 방법에 대한 지침을 제공합니다.

## <a name="before-you-start"></a>시작하기 전에

Site Recovery가 [이 시나리오](azure-to-azure-architecture.md)에 재해 복구를 지원하는 방법을 알아봅니다.

## <a name="supported-networking-resources"></a>지원되는 네트워킹 리소스

Azure VM을 복제하는 동안 장애 조치(failover) VM에 대해 다음과 같은 주요 리소스 구성을 제공할 수 있습니다.

- [내부 부하 분산 장치](../load-balancer/load-balancer-overview.md)
- [공용 IP](../virtual-network/public-ip-addresses.md)
- [보조 IP](../virtual-network/ip-services/virtual-network-multiple-ip-addresses-portal.md)
- [네트워크 보안 그룹](../virtual-network/manage-network-security-group.md)(서브넷과 NIC 모두에 사용 가능)

## <a name="prerequisites"></a>필수 구성 요소

- 복구 쪽 구성을 미리 계획해야 합니다.
- 네트워킹 리소스를 미리 만듭니다. Azure Site Recovery 서비스에서 이러한 설정을 준수하고 장애 조치(failover) VM이 이러한 설정을 준수하도록 입력으로 제공합니다.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>장애 조치(failover) 및 테스트 장애 조치(failover) 네트워킹 구성 사용자 지정

1. **복제된 항목** 으로 이동합니다. 
2. 원하는 Azure VM을 선택합니다.
3. **네트워크** 를 선택 하 고 **편집** 을 선택 합니다. NIC 구성 설정에는 해당되는 원본 리소스가 포함됩니다. 

    :::image type="content" source="./media/azure-to-azure-customize-networking/edit-networking-properties.png" alt-text="장애 조치(failover) 네트워킹 구성 사용자 지정" lightbox="./media/azure-to-azure-customize-networking/edit-networking-properties-expanded.png":::

4. 테스트 장애 조치(failover) 가상 네트워크를 선택합니다.
5. 구성 하려는 NIC 탭을 선택 합니다. 이제 테스트 장애 조치 (failover) 및 장애 조치 (failover) 위치에서 미리 생성 된 해당 리소스를 선택 합니다.

    :::image type="content" source="./media/azure-to-azure-customize-networking/nic-drilldown.png" alt-text="NIC 구성 편집" lightbox="./media/azure-to-azure-customize-networking/nic-drilldown-expanded.png":::

6. **확인** 을 선택합니다.

이제 Site Recovery에서 이러한 설정을 적용하고, 장애 조치(failover) VM이 해당 NIC를 통해 선택한 리소스에 연결되어 있는지 확인합니다.

복구 계획을 통해 테스트 장애 조치(failover)를 트리거하면 항상 Azure 가상 네트워크에 요청합니다. 이 가상 네트워크는 미리 구성된 테스트 장애 조치(failover) 설정이 없는 컴퓨터의 테스트 장애 조치(failover)에 사용됩니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="unable-to-view-or-select-a-resource"></a>리소스를 보거나 선택할 수 없음

네트워킹 리소스를 선택하거나 볼 수 없는 경우 다음 확인 사항과 상태를 살펴보세요.

- 네트워킹 리소스의 대상 필드는 원본 VM에 해당되는 입력이 있는 경우에만 사용할 수 있습니다. 이는 재해 복구 시나리오에 대한 원칙을 기반으로 하며, 일치하거나 축소된 버전의 원본 중 하나를 사용할 수 있습니다.
- 각 네트워킹 리소스에 대해 일부 필터는 드롭다운 목록에서 적용되어 장애 조치(failover) VM이 선택된 리소스에 자동으로 연결되고 장애 조치(failover) 안정성이 유지되도록 합니다. 이러한 필터는 원본 VM을 구성할 때 확인된 것과 동일한 네트워킹 조건을 기반으로 합니다.

내부 부하 분산 장치 유효성 검사:

- 부하 분산 장치 및 대상 VM의 구독과 지역은 동일해야 합니다.
- 내부 부하 분산 장치와 연결된 가상 네트워크와 대상 VM의 가상 네트워크는 동일해야 합니다.
- 대상 VM의 공용 IP SKU와 내부 부하 분산 장치의 SKU는 동일해야 합니다.
- 대상 VM이 가용성 영역에 배치되도록 구성되어 있는 경우 부하 분산 장치가 영역 중복인지 또는 가용성 영역의 일부인지 확인합니다. (기본 SKU 부하 분산 장치는 영역을 지원하지 않으며, 이 경우 드롭다운 목록에 표시되지 않습니다.)
- 내부 부하 분산 장치에 미리 만든 백 엔드 풀과 프런트 엔드 구성이 있는지 확인합니다.

공용 IP 주소:

- 공용 IP와 대상 VM의 구독과 지역은 동일해야 합니다.
- 대상 VM의 공용 IP SKU와 내부 부하 분산 장치의 SKU는 동일해야 합니다.

네트워크 보안 그룹:
- 네트워크 보안 그룹과 대상 VM의 구독과 지역은 동일해야 합니다.


> [!WARNING]
> 대상 VM이 가용성 집합에 연결된 경우, 동일한 SKU의 공용 IP 및 내부 부하 분산 장치를 가용성 집합에 있는 다른 VM의 공용 IP 및 내부 부하 분산 장치와 연결해야 합니다. 그렇지 않으면 장애 조치(failover)가 실패할 수 있습니다.