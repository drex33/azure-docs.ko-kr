---
title: Azure 네트워킹 리소스를 새 구독 또는 리소스 그룹으로 이동
description: Azure Resource Manager를 사용하여 가상 네트워크 및 다른 네트워킹 리소스를 새 리소스 그룹 또는 구독으로 이동합니다.
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 5a609b8c6a08136827062e0ae4107905aa1b8fe8
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530391"
---
# <a name="move-guidance-for-networking-resources"></a>네트워킹 리소스 이동 지침

이 문서에서는 특정 시나리오의 가상 네트워크와 다른 네트워킹 리소스를 이동하는 방법을 설명합니다.

이동하는 동안 네트워킹 리소스가 중단 없이 작동합니다.

## <a name="dependent-resources"></a>종속 리소스

> [!NOTE]
> 공용 IP 표준 SKU 주소와 연결된 VPN 게이트웨이에서는 현재 리소스 그룹 또는 구독 간에 이동이 불가능합니다.

리소스를 이동하는 경우 종속 리소스(예: 공용 IP 주소, 가상 네트워크 게이트웨이, 모든 관련된 연결 리소스)도 이동해야 합니다. 로컬 네트워크 게이트웨이는 다른 리소스 그룹에 있을 수 있습니다.

네트워크 인터페이스 카드를 사용하는 가상 머신을 새 구독으로 이동하려면 모든 종속 리소스를 이동해야 합니다. 네트워크 인터페이스 카드의 가상 네트워크, 가상 네트워크의 다른 모든 네트워크 인터페이스 카드, VPN 게이트웨이를 이동합니다.

자세한 내용은 [구독 간 이동 시나리오](../move-resource-group-and-subscription.md#scenario-for-move-across-subscriptions)를 참조하세요.

## <a name="peered-virtual-network"></a>피어링된 가상 네트워크

피어링된 가상 네트워크를 이동하려면 먼저 가상 네트워크 피어링을 사용하지 않도록 설정해야 합니다. 사용하지 않도록 설정되면 가상 네트워크를 이동할 수 있습니다. 이동 후에는 가상 네트워크 피어링을 사용하도록 다시 설정합니다.

## <a name="subnet-links"></a>서브넷 링크

리소스 탐색 링크가 있는 서브넷이 가상 네트워크에 있는 경우 가상 네트워크를 다른 구독으로 이동할 수 없습니다. 예를 들어 Azure Cache for Redis 리소스가 서브넷에 배포된 경우 해당 서브넷에는 리소스 탐색 링크가 있습니다.

## <a name="next-steps"></a>다음 단계

리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](../move-resource-group-and-subscription.md)을 참조하세요.
