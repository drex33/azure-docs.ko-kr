---
title: 게이트웨이를 사용 하 여 5g 및 LTE 네트워크를 통해 커넥트 Azure Percept DK
description: 이 문서에서는 셀룰러 게이트웨이를 사용 하 여 5g 및 LTE 네트워크를 통해 Azure Percept DK을 연결 하는 방법을 설명 합니다.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 5b66885afdec8e32b938c735625bd48c9c62535a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439983"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-networks-by-using-a-gateway"></a>게이트웨이를 사용 하 여 5g 및 LTE 네트워크를 통해 커넥트 Azure Percept DK

Azure Percept를 인터넷에 연결 하는 간단한 방법은 5G 또는 LTE를 통해 인터넷에 연결 하는 게이트웨이를 사용 하 고 이더넷 포트를 제공 하는 것입니다. 이 경우 Azure Percept는 5G 또는 LTE를 통해 연결 된 것을 인식 하지 못합니다. 이더넷 포트가 연결 되어 있고 해당 포트를 통해 모든 트래픽을 라우팅하는 것만 "알고 있습니다".  


## <a name="overview-of-5g-and-lte-gateway-topology"></a>5G 및 LTE 게이트웨이 토폴로지 개요

다음 다이어그램에서는 5g 또는 LTE 게이트웨이를 Azure Percept DK (개발 키트)와 쉽게 쌍으로 연결할 수 있는 방법을 보여 줍니다.

:::image type="Image" source="media/connect-over-cellular/topology-v2.png" alt-text="Azure Percept DK 이더넷을 통해 5g 또는 LTE 게이트웨이에 연결 하는 방법을 보여 주는 다이어그램입니다." lightbox="media/connect-over-cellular/topology-expanded-v2.png":::

## <a name="if-youre-connecting-to-a-5g-or-lte-gateway"></a>5G 또는 LTE 게이트웨이에 연결 하는 경우

Azure Percept DK를 5g 또는 LTE 게이트웨이에 연결 하는 경우 다음과 같은 중요 한 사항을 고려해 야 합니다.
- 먼저 게이트웨이를 설정한 다음, SIM을 통해 연결을 수신 하는지 확인 합니다. 이 순서를 따라 Azure Percept DK 연결할 때 발견 한 문제를 더 쉽게 해결할 수 있습니다.
- 이더넷 케이블의 양쪽 끝이 게이트웨이 및 Azure Percept DK에 단단히 연결 되어 있는지 확인 합니다.
- 이더넷을 통한 Azure Percept DK 연결에 대 한 [기본 지침](./how-to-connect-over-ethernet.md) 을 따릅니다.
- 5g 또는 LTE 계획에 할당량이 있는 경우 Azure Percept DK 모델에서 클라우드로 전송 하는 데이터의 양을 최적화 하는 것이 좋습니다.
- 외부에서 시작 된 인바운드 트래픽을 차단 하는 적절 하 게 [구성 된 방화벽이](./concept-security-configuration.md) 있는지 확인 합니다.

## <a name="if-youre-connecting-to-the-dev-kit-via-ssh-protocol"></a>SSH 프로토콜을 통해 dev kit에 연결 하는 경우

SSH (Secure Shell) 네트워크 프로토콜을 사용 하 여 5G 또는 LTE 이더넷 게이트웨이를 통해 dev kit에 연결 하는 경우 다음 옵션 중 하나를 사용 합니다.
- **Dev kit의 Wi-Fi 액세스 지점 사용**: Wi-Fi 사용 하지 않도록 설정 된 경우 dev kit를 다시 부팅 하 여 다시 사용 하도록 설정할 수 있습니다. 여기에서 dev kit의 Wi-Fi 액세스 지점에 연결 하 고 커넥트의 지침에 따라 [SSH를 통해 Azure Percept DK](./how-to-ssh-into-percept-dk.md)수 있습니다.
- **Lan (local area network)에 대 한 이더넷 연결 사용**:이 옵션을 사용 하면 5g 또는 LTE 게이트웨이에서 dev kit를 분리 하 고 LAN 라우터에 연결 합니다. 자세한 내용은 [이더넷을 통한 Azure Percept DK 커넥트을](./how-to-connect-over-ethernet.md)참조 하세요. 
- **게이트웨이의 원격 액세스 기능 사용**: 많은 5g 및 LTE 게이트웨이는 SSH를 통해 네트워크에서 장치에 연결 하는 데 사용할 수 있는 원격 액세스 관리자를 포함 합니다. 5G 또는 LTE gateway 제조업체에 문의 하 여이 기능이 있는지 확인 합니다. 원격 액세스 관리자의 예는 [Cradlepoint 5G 및 LTE](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager)gateway를 참조 하세요.
- **dev kit 직렬 포트 사용**: Azure Percept DK 장치에 직접 연결 하는 데 사용할 수 있는 직렬 연결 포트를 포함 합니다. 자세한 내용은 [직렬 케이블을 통한 Azure Percept DK 커넥트을](./how-to-connect-to-percept-dk-over-serial.md)참조 하세요.

## <a name="next-steps"></a>다음 단계
액세스 권한이 있는 셀룰러 장치에 따라 다음 두 가지 방법 중 하나로 연결할 수 있습니다.

* [USB 모뎀을 사용 하 여 커넥트](./connect-over-cellular-usb.md)
* [5G 또는 LTE를 사용 하 여 커넥트](./connect-over-cellular.md)
