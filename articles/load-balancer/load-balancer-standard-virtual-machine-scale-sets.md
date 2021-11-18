---
title: Azure 표준 Load Balancer 및 가상 머신 확장 집합에 대한 규칙 추가
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: 이 학습 경로를 통해 Azure 표준 Load Balancer와 가상 머신 확장 집합을 시작합니다.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 2ba2d00d1cbb025b665c2c9c6cb929f8b46a1f33
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723668"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>가상 머신 확장 집합을 사용하여 Azure Load Balancer에 대한 규칙 추가

가상 머신 확장 집합 및 Azure Load Balancer를 사용하는 경우 다음 지침을 고려하세요.

## <a name="port-forwarding-and-inbound-nat-rules"></a>포트 전달 및 인바운드 NAT 규칙

확장 집합을 만든 후에는 부하 분산 장치의 상태 프로브에서 사용하는 부하 분산 규칙에 대해 백 엔드 포트를 수정할 수 없습니다. 포트를 변경하려면 가상 머신 확장 집합을 업데이트하고 포트를 업데이트하여 상태 프로브를 제거합니다. 그런 다음, 상태 프로브를 다시 구성합니다.

부하 분산 장치의 백 엔드 풀에 있는 가상 머신 확장 집합을 사용하면 기본 인바운드 NAT 규칙이 자동으로 만들어집니다.
  
## <a name="inbound-nat-pool"></a>인바운드 NAT 풀

각 가상 머신 확장 집합에는 인바운드 NAT 풀이 하나 이상 있어야 합니다. 인바운드 NAT 풀은 인바운드 NAT 규칙의 컬렉션입니다. 하나의 인바운드 NAT 풀에서 여러 가상 머신 확장 집합을 지원할 수 없습니다.

## <a name="load-balancing-rules"></a>부하 분산 규칙

부하 분산 장치의 백 엔드 풀에 있는 가상 머신 확장 집합을 사용하면 기본 부하 분산 규칙이 자동으로 만들어집니다.

## <a name="virtual-machine-scale-set-instance-level-ips"></a>Virtual Machine Scale Set 인스턴스 수준 IP

[인스턴스당 공용 IP가](../virtual-network/ip-services/public-ip-address-prefix.md) 있는 가상 머신 확장 집합이 부하 분산기를 앞에 두고 만들어지면 인스턴스 IP의 SKU는 Load Balancer SKU(예: 기본 또는 표준)에 의해 결정됩니다.  표준 Load Balancer 사용하는 경우 개별 인스턴스 IP는 모두 표준 "영역 없음" 형식입니다(Load Balancer 프런트 엔드는 영역 또는 영역 중복일 수 있지만).

## <a name="outbound-rules"></a>아웃바운드 규칙

부하 분산 규칙에서 이미 참조한 백 엔드 풀에 대한 아웃바운드 규칙을 만들려면 인바운드 부하 분산 규칙을 만들 때 Azure Portal에서 **암시적 아웃바운드 규칙 만들기** 에서 **아니요** 를 선택합니다.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="부하 분산 규칙 생성을 보여주는 스크린샷." border="true":::

다음 메서드를 사용하여 기존 Load Balancer 인스턴스를 통해 가상 머신 확장 집합을 배포합니다.

* [Azure Portal을 사용하여 기존 Azure Load Balancer 인스턴스를 통해 가상 머신 확장 집합 구성](./configure-vm-scale-set-portal.md)
* [Azure PowerShell을 사용하여 기존 Azure Load Balancer 인스턴스를 통해 가상 머신 확장 집합 구성](./configure-vm-scale-set-powershell.md)
* [Azure CLI를 사용하여 기존 Azure Load Balancer 인스턴스를 통해 가상 머신 확장 집합 구성](./configure-vm-scale-set-cli.md)
* [가상 머신 확장 집합에서 사용하는 기존 Azure Load Balancer 인스턴스 업데이트 또는 삭제](./update-load-balancer-with-vm-scale-set.md)
