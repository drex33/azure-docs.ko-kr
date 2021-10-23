---
title: 게이트웨이를 사용하여 5G 및 LTE 네트워크 커넥트 Azure Percept DK
description: 이 문서에서는 셀룰러 게이트웨이를 사용하여 5G 및 LTE 네트워크를 통해 Azure Percept DK 연결하는 방법을 설명합니다.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: b3096f4876f660c7be4ba1f17c190d0d48fea4ca
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248158"
---
# <a name="connect-azure-percept-dk-over-5g-and-lte-networks-by-using-a-gateway"></a>게이트웨이를 사용하여 5G 및 LTE 네트워크 커넥트 Azure Percept DK

Azure Percept 인터넷에 연결하는 간단한 방법은 5G 또는 LTE를 통해 인터넷에 연결하고 이더넷 포트를 제공하는 게이트웨이를 사용하는 것입니다. 이 경우 Azure Percept 5G 또는 LTE를 통해 연결되어 있다는 사실도 인식하지 못합니다. 이더넷 포트가 연결되어 있고 해당 포트를 통해 모든 트래픽을 라우팅한다는 것만 "알고 있습니다".  


## <a name="overview-of-5g-and-lte-gateway-topology"></a>5G 및 LTE 게이트웨이 토폴로지 개요

다음 다이어그램에서는 5G 또는 LTE 게이트웨이를 Azure Percept DK(개발 키트)와 쉽게 페어링할 수 있는 방법을 보여줍니다.

:::image type="Image" source="media/connect-over-cellular/topology.png" alt-text="Azure Percept DK 이더넷을 통해 5G 또는 LTE 게이트웨이에 연결하는 방법을 보여 주는 다이어그램" lightbox="media/connect-over-cellular/topology-expanded.png":::

## <a name="if-youre-connecting-to-a-5g-or-lte-gateway"></a>5G 또는 LTE 게이트웨이에 연결하는 경우

Azure Percept DK 5G 또는 LTE 게이트웨이에 연결하는 경우 다음과 같은 중요한 사항을 고려하세요.
- 먼저 게이트웨이를 설정한 다음, SIM을 통해 연결을 수신하고 있는지 확인합니다. 이 순서를 따르면 Azure Percept DK 연결할 때 찾은 문제를 보다 쉽게 해결할 수 있습니다.
- 이더넷 케이블의 양쪽 끝이 게이트웨이에 연결되고 Azure Percept DK.
- 이더넷을 통해 Azure Percept DK 연결하기 위한 [기본 지침을](./how-to-connect-over-ethernet.md) 따릅니다.
- 5G 또는 LTE 계획에 할당량이 있는 경우 Azure Percept DK 모델이 클라우드로 보내는 데이터의 양에 맞게 최적화하는 것이 좋습니다.
- 외부에서 시작된 인바운드 트래픽을 차단하는 [올바르게 구성된 방화벽이](./concept-security-configuration.md) 있는지 확인합니다.

## <a name="if-youre-connecting-to-the-dev-kit-via-ssh-protocol"></a>SSH 프로토콜을 통해 개발 키트에 연결하는 경우

SSH(Secure Shell) 네트워크 프로토콜을 사용하여 5G 또는 LTE 이더넷 게이트웨이를 통해 개발 키트에 연결하는 경우 다음 옵션 중 하나를 사용합니다.
- **개발 키트의 Wi-Fi 액세스 지점 사용:** Wi-Fi 사용하지 않도록 설정한 경우 개발 키트를 다시 부팅하여 다시 사용하도록 설정할 수 있습니다. 이 위치에서 개발 키트의 Wi-Fi 액세스 지점에 연결하고 커넥트 지침에 따라 [SSH를 통해 Azure Percept DK](./how-to-ssh-into-percept-dk.md)수 있습니다.
- **LAN(로컬 영역 네트워크)에 대한 이더넷 연결 사용:** 이 옵션을 사용하면 5G 또는 LTE 게이트웨이에서 개발 키트를 분리하고 LAN 라우터에 연결합니다. 자세한 내용은 [이더넷을 통해 Azure Percept DK 커넥트 참조하세요.](./how-to-connect-over-ethernet.md) 
- **게이트웨이의 원격 액세스 기능 사용:** 많은 5G 및 LTE 게이트웨이에는 SSH를 통해 네트워크의 디바이스에 연결하는 데 사용할 수 있는 원격 액세스 관리자가 포함됩니다. 이 기능이 있는지 확인하려면 5G 또는 LTE 게이트웨이 제조업체에 문의하세요. 원격 액세스 관리자의 예는 [5G 및 LTE 게이트웨이를 참조하세요.](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager)
- **개발 키트의 직렬 포트 사용:** Azure Percept DK 디바이스에 직접 연결하는 데 사용할 수 있는 직렬 연결 포트를 포함합니다. 자세한 내용은 [직렬 케이블을 통해 Azure Percept DK 커넥트 참조하세요.](./how-to-connect-to-percept-dk-over-serial.md)

## <a name="next-steps"></a>다음 단계
액세스 권한이 있는 셀룰러 디바이스에 따라 다음 두 가지 방법 중 하나로 연결할 수 있습니다.

* [USB 모뎀을 사용하여 커넥트](./connect-over-cellular-usb.md)
* [5G 또는 LTE를 사용하여 커넥트](./connect-over-cellular.md)
