---
title: Azure Virtual Network Manager의 연결 구성 (미리 보기)
description: Azure Virtual Network Manager에서 연결 구성을 사용 하 여 만들 수 있는 다양 한 유형의 네트워크 토폴로지에 대해 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: face052462909e755771f12ff19fed8139675183
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484602"
---
# <a name="connectivity-configuration-in-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager의 연결 구성 (미리 보기)

이 문서에서는 Azure Virtual Network Manager를 사용 하 여 만들고 배포할 수 있는 다양 한 유형의 구성에 대해 알아봅니다. 현재 사용할 수 있는 구성에는 *연결* 및 *보안 관리자* 의 두 가지 유형이 있습니다. 

> [!IMPORTANT]
> Azure Virtual Network Manager는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="connectivity-configuration"></a>연결 구성

*연결* 구성을 통해 네트워크 요구 사항에 따라 서로 다른 네트워크 토폴로지를 만들 수 있습니다. *메시 네트워크* 와 *허브 및 스포크* 의 두 가지 토폴로지를 선택할 수 있습니다. 가상 네트워크 간 Connectivities 구성 설정 내에서 정의 됩니다.

## <a name="mesh-network-topology"></a>메시 네트워크 토폴로지

메시 네트워크는 [네트워크 그룹](concept-network-groups.md) 의 모든 가상 네트워크가 서로 연결 되는 토폴로지입니다. 모든 가상 네트워크를 연결 하 고 양방향 트래픽을 서로 전달할 수 있습니다. 기본적으로 메시는 지역 메시 이므로 동일한 지역의 가상 네트워크만 서로 통신할 수 있습니다. **전역 메시** 를 사용 하 여 모든 Azure 지역에서 가상 네트워크의 연결을 설정할 수 있습니다. 가상 네트워크는 최대 5 개의 연결 된 그룹에 속할 수 있습니다. 가상 네트워크 주소 공간은 구성에서 겹칠 수 없으며 다른 사용자의 리소스는 네트워크 충돌로 인해 서로 통신할 수 없습니다.

:::image type="content" source="./media/concept-configuration-types/mesh-topology.png" alt-text="메시 네트워크 토폴로지의 다이어그램입니다.":::

### <a name="connected-group"></a><a name="connectedgroup"></a> 연결 된 그룹

메시 토폴로지를 만들 때 *연결 된 그룹* 이라는 새로운 연결 구문이 생성 됩니다. 연결 된 그룹의 가상 네트워크는 수동으로 가상 네트워크를 연결 하는 경우와 마찬가지로 서로 통신할 수 있습니다. 네트워크 인터페이스에 대 한 유효 경로를 살펴보면 **ConnectedGroup** 의 다음 홉 유형이 표시 됩니다. 연결 된 그룹에 연결 된 가상 네트워크는 가상 네트워크 *의 피어 링 아래에* 피어 링 구성이 나열 되지 않습니다.

> [!NOTE]
> * 둘 이상의 가상 네트워크에 충돌 하는 서브넷이 있는 경우 해당 서브넷의 리소스는 동일한 메시 네트워크의 일부인 경우에도 서로 통신할 수 *없습니다* .
> * 가상 네트워크는 최대 **두 개의** 메시 구성에 포함 될 수 있습니다.

## <a name="hub-and-spoke-topology"></a>허브 및 스포크 토폴로지

허브 및 스포크는 가상 네트워크를 허브 가상 네트워크로 선택한 네트워크 토폴로지입니다. 이 가상 네트워크는 구성의 모든 스포크 가상 네트워크와 함께 양방향 피어 링을 가져옵니다. 이 토폴로지는 가상 네트워크를 격리 하지만 허브 가상 네트워크의 공통 리소스에 대 한 연결을 유지 하려는 경우에 유용 합니다. 

이 구성에서는 스포크 가상 네트워크 간의 *직접 연결* 등의 설정을 사용 하도록 설정할 수 있습니다. 기본적으로이 연결은 동일한 지역의 가상 네트워크에만 해당 됩니다. 서로 다른 Azure 지역 간 연결을 허용 하려면 *전역 메시* 를 사용 하도록 설정 해야 합니다. 또한 *게이트웨이* 전송 기능을 사용 하도록 설정 하 여 스포크 가상 네트워크에서 허브에 배포 된 VPN 또는 express 경로 게이트웨이를 사용할 수 있습니다.

### <a name="direct-connectivity"></a>직접 연결

*직접 연결* 을 사용 하도록 설정 하면 동일한 지역의 스포크 가상 네트워크를 포함 하는 [*연결 된 그룹이*](#connectedgroup) 만들어집니다. 동일한 네트워크 그룹의 가상 네트워크에 대해서만이 연결이 설정 됩니다. 

예를 들어 두 개의 네트워크 그룹을 만듭니다. *프로덕션* 네트워크 그룹에 대 한 직접 연결을 사용 하도록 설정 하 고 *테스트* 네트워크 그룹에 대해서는 사용 하지 않습니다. 이 설정을 사용 하면 *프로덕션* 네트워크 그룹의 가상 네트워크와만 통신할 수 있고 *테스트* 네트워크 그룹의 가상 네트워크는 통신할 수 없습니다. 

아래 예제 다이어그램을 참조 하세요.

:::image type="content" source="./media/concept-configuration-types/hub-and-spoke.png" alt-text="네트워크 그룹이 두 개인 허브 및 스포크 토폴로지의 다이어그램입니다.":::

VM에서 유효 경로를 살펴보면 허브와 스포크 가상 네트워크 간의 경로에 다음 홉 유형인  *Vnetpeering* 링 또는 *globalvnetpeering 링* 이 있습니다. 스포크 가상 네트워크 간의 경로는 *ConnectedGroup* 의 다음 홉 유형으로 표시 됩니다. 위의 예제를 사용 하는 경우 *직접 연결* 을 사용 하기 때문에 *프로덕션* 네트워크 그룹에만 *ConnectedGroup* 있습니다.

> [!NOTE]
> *직접 연결* 을 **사용 하도록 설정** 하면 허브 네트워크 주소 공간이 *ConnectedGroup* 에 추가 됩니다. 따라서 허브와 스포크 가상 네트워크 간의 가상 네트워크 피어 링이 실패 하면 여전히 *ConnectedGroup* 에서 통신할 수 있습니다.

#### <a name="use-cases"></a>사용 사례

스포크 가상 네트워크 간의 직접 연결을 사용 하도록 설정 하면 허브 가상 네트워크에서 NVA 또는 공통 서비스를 사용 하려는 경우 허브에 항상 액세스할 필요가 없는 경우에 유용할 수 있습니다. 하지만 네트워크 그룹의 스포크 가상 네트워크는 서로 통신 해야 합니다. 기존 허브 및 스포크 네트워크와 비교할 때이 토폴로지는 허브 가상 네트워크를 통해 추가 홉을 제거 하 여 성능을 향상 시킵니다.

### <a name="global-mesh"></a>글로벌 메시

전역 메시는 스포크 가상 네트워크가 지역에서 서로 통신 하도록 하려는 경우에 필요 합니다. 이러한 연결은 동일한 네트워크 그룹의 가상 네트워크로 제한 됩니다. 여러 지역에서 가상 네트워크에 대 한 연결을 사용 하도록 설정 하려면 네트워크 그룹의 **지역 간에 메시 연결을 사용 하도록 설정** 해야 합니다. 스포크 가상 네트워크 간에 만들어진 연결은 [*연결 된 그룹*](#connectedgroup)에 있습니다. 

### <a name="use-hub-as-a-gateway"></a>허브를 게이트웨이로 사용

허브 및 스포크 구성에서 사용할 수 있는 또 다른 옵션은 허브를 게이트웨이로 사용 하는 것입니다. 이 설정을 사용 하면 네트워크 그룹의 모든 가상 네트워크가 허브 가상 네트워크에서 VPN 또는 Express 경로 게이트웨이를 사용 하 여 트래픽을 전달할 수 있습니다. [게이트웨이 및 온-프레미스 연결](/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity)을 참조 하세요.

Azure Portal에서 허브 및 스포크 토폴로지를 배포 하는 경우 네트워크 그룹의 스포크 가상 네트워크에 대해 기본적으로 **허브 사용이 게이트웨이로 사용** 됩니다. Azure Virtual Network Manager는 리소스 그룹의 스포크 가상 네트워크와 허브 간에 가상 네트워크 피어 링 연결을 만들려고 합니다. 게이트웨이가 허브 가상 네트워크에 존재 하지 않는 경우 스포크 가상 네트워크에서 허브로의 피어 링 만들기가 실패 합니다. 허브에서 스포크로의 피어 링 연결은 설정 된 연결 없이도 계속 생성 됩니다. 

> [!NOTE]
> 스포크 네트워크 그룹에 대 한 *직접 연결* 을 사용 하도록 설정 하면 허브도 연결 된 그룹에 포함 됩니다. 따라서 스포크에서 허브로의 피어 링 연결이 생성 되지 않더라도 스포크 가상 네트워크는 연결 된 그룹을 통해 허브 가상 네트워크와 통신할 수 있습니다.
>

## <a name="next-steps"></a>다음 단계

- [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) 인스턴스를 만듭니다.
- Azure Virtual Network Manager의 [구성 배포](concept-deployments.md) 에 대해 알아봅니다.
- [SecurityAdmin 구성을](how-to-block-network-traffic-portal.md)사용 하 여 네트워크 트래픽을 차단 하는 방법에 대해 알아봅니다.
