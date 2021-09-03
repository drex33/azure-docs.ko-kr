---
title: VNet 피어링 및 Azure Bastion 아키텍처
description: VNet 피어링과 Azure Bastion을 함께 사용하여 VM에 연결하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: cherylmc
ms.openlocfilehash: ccbee86e8f5ae2ab514c8ec7ece4d41e1e890e4e
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733289"
---
# <a name="vnet-peering-and-azure-bastion"></a>VNet 피어링 및 Azure Bastion

Azure Bastion과 VNet 피어링을 함께 사용할 수 있습니다. VNet 피어링이 구성되면 피어링된 각 VNet에 Azure Bastion을 배포할 필요가 없습니다. 즉, 하나의 VNet(가상 네트워크)에 구성된 Azure Bastion 호스트가 있는 경우 추가 Bastion 호스트를 배포하지 않고도 피어링된 VNet에 배포된 VM에 연결하는 데 사용할 수 있습니다. VNet 피어링에 대한 자세한 내용은 [가상 네트워크 피어링 정보](../virtual-network/virtual-network-peering-overview.md)를 참조하세요.

Azure Bastion은 다음과 같은 피어링 유형을 사용합니다.

* **가상 네트워크 피어링**: 동일한 Azure 지역 내에서 가상 네트워크를 연결합니다.
* **글로벌 가상 네트워크 피어링**: Azure 지역에서 가상 네트워크를 연결합니다.

## <a name="architecture"></a>Architecture

VNet 피어링이 구성되면 Azure Bastion을 허브 및 스포크 또는 전체 메시 토폴로지에 배포할 수 있습니다. Azure Bastion 배포는 구독/계정 또는 가상 머신이 아닌 가상 네트워크별로 수행됩니다.

가상 네트워크에서 Azure Bastion 서비스를 프로비전한 후에는 같은 VNet 및 피어링된 VNet의 모든 VM에서 RDP/SSH 환경을 사용할 수 있습니다. 즉, Bastion 배포를 단일 VNet에 통합하고 피어링된 VNet에 배포된 VM에 연결할 수 있으므로 전체 배포를 중앙 집중화할 수 있습니다.

:::image type="content" source="./media/vnet-peering/design.png" alt-text="디자인 및 아키텍처 다이어그램":::

이 그림은 허브 및 스포크 모델의 Azure Bastion 배포 아키텍처를 보여줍니다. 이 다이어그램에서 다음과 같은 구성을 볼 수 있습니다.

* Bastion 호스트는 중앙 집중식 Hub 가상 네트워크에 배포됩니다.
* 중앙 집중식 NSG(네트워크 보안 그룹)가 배포됩니다.
* Azure VM에는 공용 IP가 필요하지 않습니다.

**단계:**

1. HTML5 브라우저를 사용하여 Azure Portal에 연결합니다.
2. 대상 VM과 피어링된 VNet 모두에 대한 **읽기** 액세스 권한이 있는지 확인합니다. 또한 다음 리소스에 대한 읽기 액세스 권한이 있는지 IAM에서 확인합니다.
   * 가상 머신에 대한 읽기 권한자 역할
   * 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
   * Azure Bastion 리소스에 대한 읽기 권한자 역할
   * Virtual Network에 대한 리더 역할(피어링된 Virtual Network가 없는 경우 필요하지 않음).
3. **연결** 드롭다운 메뉴에서 Bastion을 보려면 **구독 > 전역 구독** 에서 액세스할 수 있는 하위 항목을 선택해야 합니다.
4. 연결할 가상 머신을 선택합니다.
5. Azure Bastion은 피어링된 VNet에서 원활하게 검색됩니다.
6. 한 번의 클릭으로 RDP/SSH 세션이 브라우저에서 열립니다.

  :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="연결":::

   Azure Bastion을 통해 VM에 연결하는 방법에 대한 자세한 내용은 다음을 참조하세요.

   * [VM에 연결 - RDP](bastion-connect-vm-rdp.md).
   * [VM에 연결 - SSH](bastion-connect-vm-ssh.md).

## <a name="faq"></a>FAQ

질문과 대답은 Bastion VNet 피어링 [FAQ](bastion-faq.md#peering)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Bastion FAQ](bastion-faq.md)를 참조하세요.
