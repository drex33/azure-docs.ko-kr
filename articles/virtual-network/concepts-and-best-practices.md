---
title: Azure Virtual Network - 개념 및 모범 사례
description: Azure Virtual Network 개념 및 모범 사례에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: d2f39d06046cd9d51baef0a2b09be00a37a35ca3
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993951"
---
# <a name="azure-virtual-network-concepts-and-best-practices"></a>Azure Virtual Network 개념 및 모범 사례

이 문서에서는 Azure VNet(Virtual Network)에 대한 주요 개념 및 모범 사례를 설명합니다.

## <a name="vnet-concepts"></a>VNet 개념

- **주소 공간:** VNet을 만들 때 공용 및 프라이빗(RFC 1918) 주소를 사용하여 사용자 지정 프라이빗 IP 주소 공간을 지정해야 합니다. Azure는 가상 네트워크의 리소스에 사용자가 할당한 주소 공간의 개인 IP 주소를 할당합니다. 예를 들어 주소 공간인 10.0.0.0/16이 있는 VNet에 VM을 배포하는 경우 VM에는 10.0.0.4 같은 프라이빗 IP가 할당됩니다.
- **서브넷:** 서브넷을 사용하면 가상 네트워크를 하나 이상의 하위 네트워크로 분할하고 가상 네트워크 주소 공간의 일부를 각 서브넷에 할당합니다. 그런 다음, 특정 서브넷에 Azure 리소스를 배포할 수 있습니다. 기존 네트워크와 마찬가지로 서브넷을 사용하여 VNet 주소 공간을 조직의 내부 네트워크에 적합한 세그먼트로 분할할 수 있습니다. 주소 할당 효율성도 향상됩니다. 네트워크 보안 그룹을 사용하여 서브넷 내의 리소스에 보안을 지정할 수 있습니다. 자세한 내용은 [네트워크 보안 그룹](./network-security-groups-overview.md)을 참조하세요.
- **지역**: VNet은 단일 지역/위치로 범위가 제한되지만 가상 네트워크 피어링을 사용하여 여러 지역의 여러 가상 네트워크를 연결할 수 있습니다.
- **구독:** VNet은 구독으로 범위가 제한됩니다. 각 Azure [구독](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) 및 Azure [지역](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) 내에서 여러 가상 네트워크를 구현할 수 있습니다.

## <a name="best-practices"></a>모범 사례

Azure에서 네트워크를 빌드할 때 다음 범용 디자인 원칙에 유의해야 합니다.

- 겹치지 않는 주소 공간을 확보합니다. VNet 주소 공간(CIDR 블록)이 조직의 다른 네트워크 범위와 겹치지 않는지 확인합니다.
- 서브넷은 VNet의 전체 주소 공간을 사용하지 않아야 합니다. 미래를 위해 미리 계획하고 일부 주소 공간을 예약합니다.
- 여러 개의 작은 VNet보다 적은 수의 큰 VNet을 사용하는 것이 좋습니다. 이렇게 하면 관리 오버헤드가 방지됩니다.
- 아래 서브넷에 NSG(네트워크 보안 그룹)를 할당하여 VNet을 보호합니다. 네트워크 보안 개념에 대한 자세한 내용은 [Azure 네트워크 보안 개요를 참조하세요.](../security/fundamentals/network-overview.md)

## <a name="next-steps"></a>다음 단계

 가상 네트워크를 사용하여 시작하려면 가상 네트워크를 만들고, 몇몇 VM을 배포하고 VM 간에 통신합니다. 방법을 자세히 알아보려면 [가상 네트워크 만들기](quick-create-portal.md) 빠른 시작을 참조하세요.