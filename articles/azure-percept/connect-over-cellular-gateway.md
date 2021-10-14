---
title: 커넥트 게이트웨이를 사용 하는 5G 또는 LTE 네트워크를 통한 Azure Percept
description: 이 문서에서는 셀룰러 게이트웨이를 사용 하 여 5g 또는 LTE 네트워크를 통해 Azure Percept DK을 연결 하는 방법을 설명 합니다.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 00596a23e086f801b152e1a3129ecaf7ef44a0df
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008735"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks-using-a-gateway"></a>커넥트 게이트웨이를 사용 하는 5G 또는 LTE 네트워크를 통한 Azure Percept
5G 또는 LTE를 통해 인터넷에 연결 하 고 이더넷 포트를 제공 하는 게이트웨이는 Azure Percept를 인터넷에 연결 하는 간단한 방법입니다. 이 경우 Azure Percept는 5G 또는 LTE를 통해 연결 된 것을 인식 하지 못합니다. 이더넷 포트가 연결 되어 있고이를 통해 모든 트래픽을 라우팅하는 것을 알고 있습니다.  


## <a name="5glte-gateway-topology-overview"></a>5G/LTE 게이트웨이 토폴로지 개요
아래에서 5G/LTE 게이트웨이를 Azure Percept DK 쉽게 연결할 수 있는 방법을 확인할 수 있습니다.

:::image type="Image" source="media/connect-over-cellular/topology.png" alt-text="이 다이어그램에서는 Azure Percept DK에서 이더넷을 통해 5G/LTE 게이트웨이에 연결하는 방법을 보여 줍니다." lightbox="media/connect-over-cellular/topology-expanded.png":::

## <a name="considerations-when-connecting-to-a-5g-or-lte-gateway"></a>5G 또는 LTE 게이트웨이에 연결할 때의 고려 사항
다음은 Azure Percept DK를 5G/LTE 게이트웨이에 연결할 때 고려해야 할 몇 가지 중요 사항입니다.
- 먼저 게이트웨이를 설정한 다음, SIM을 통해 연결을 수신하는지 확인합니다. 그러면 Azure Percept DK에 연결하는 동안 발견된 문제를 더 쉽게 해결할 수 있습니다.
- 이더넷 케이블의 양쪽 끝이 게이트웨이 및 Azure Percept DK에 단단히 연결되어 있는지 확인합니다.
- 이더넷을 통한 Azure Percept DK 연결에 대한 [기본 지침](./how-to-connect-over-ethernet.md)을 따릅니다.
- 5G/LTE 플랜에 할당량이 있는 경우 Azure Percept DK 모델이 클라우드로 전송하는 데이터의 양을 최적화하는 것이 좋습니다.
- 외부에서 시작된 인바운드 트래픽을 차단하는 [적절하게 구성된 방화벽](./concept-security-configuration.md)이 있는지 확인합니다.

## <a name="considerations-when-doing-ssh-to-the-devkit"></a>Devkit에 SSH를 수행할 때의 고려 사항
5G/LTE 이더넷 게이트웨이를 통해 개발 키트로 SSH하려면 다음 옵션을 사용할 수 있습니다.
- **개발 키트의 Wi-Fi 액세스 지점 사용** Wi-Fi를 사용하지 않도록 설정한 경우 개발 키트를 다시 부팅하여 다시 사용하도록 설정할 수 있습니다. 여기에서 dev kit의 Wi-Fi 액세스 지점에 연결 하 고 [Azure Percept DK에 SSH를 수행 하는 방법을](./how-to-ssh-into-percept-dk.md)따를 수 있습니다.
- **로컬 네트워크(LAN)에 대한 이더넷 연결 사용** 이 옵션을 사용하여 5G/LTE 게이트웨이에서 개발 키트를 분리하고 LAN 라우터에 연결합니다. 자세한 내용은 [이더넷을 통해 연결하는 방법](./how-to-connect-over-ethernet.md)을 참조하세요. 
- **게이트웨이의 원격 액세스 기능 사용** 많은 5G/LTE 게이트웨이는 SSH를 통해 네트워크에서 디바이스에 연결하는 데 사용할 수 있는 원격 액세스 관리자를 포함합니다. 5G/LTE 게이트웨이의 제조업체에 문의하여 이 기능이 있는지 확인합니다. [Cradlepoint 5G/LTE 게이트웨이](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager)의 원격 액세스 관리자의 예제는 다음과 같습니다.
- **개발 키트의 직렬 포트 사용** Azure Percept DK는 디바이스에 직접 연결하는 데 사용할 수 있는 직렬 연결 포트를 포함합니다. 자세한 지침은 [직렬을 통한 Azure Percept DK 연결](./how-to-connect-to-percept-dk-over-serial.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
액세스할 수 있는 셀룰러 장치에 따라 USB 모드를 통해 연결 하는 것을 고려할 수 있습니다.

[USB 모뎀을 사용 하 여 커넥트](./connect-over-cellular-usb.md)합니다.

5G 또는 LTE의 주 문서로 돌아갑니다.

[5g 또는 LTE를 사용 하 여 커넥트](./connect-over-cellular.md)합니다.