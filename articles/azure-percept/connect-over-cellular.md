---
title: 5G 또는 LTE 네트워크를 통해 Azure Percept 연결
description: 이 문서에서는 5G 또는 LTE 네트워크를 통해 Azure Percept DK를 연결하는 방법을 설명합니다.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 07/28/2021
ms.custom: template-how-to
ms.openlocfilehash: 202bec8182cc2ae04e9fadffe01977150ea93dda
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222319"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks"></a>5G 또는 LTE 네트워크를 통해 Azure Percept 연결

5G/LTE 네트워크를 통해 Edge AI 디바이스를 연결하면 많은 이점이 있습니다. Edge AI가 가장 효율적인 시나리오는 Wi-Fi 및 LAN 연결이 제한되는 위치(예: 스마트 도시, 자율 차량 및 농업)입니다. 또한 5G/LTE 네트워크는 Wi-Fi보다 더 나은 보안을 제공합니다. 마지막으로 Edge에서 AI를 실행하는 IoT 디바이스를 사용하여 5G/LTE 네트워크에서 대역폭을 최적화하는 방법을 제공합니다. 대부분의 데이터가 디바이스에서 처리되는 동안 필요한 정보만 클라우드로 전송됩니다. 현재 Azure Percept DK는 5G/LTE 네트워크에 직접 연결할 수 없습니다. 그러나 기본 제공 이더넷 및 Wi-Fi 기능을 사용하여 5G/LTE 게이트웨이에 연결할 수 있습니다. 이 문서에서는 이러한 작동 방식에 대해 설명합니다.

## <a name="options-for-connecting-the-azure-percept-dk-over-5g-or-lte-networks"></a>5G/LTE 네트워크를 통해 Azure Percept DK를 연결하기 위한 옵션
추가 하드웨어를 사용하여 5G/LTE 연결을 사용하여 Azure Percept DK를 연결할 수 있습니다. 현재 다음과 같은 두 가지 기본 옵션이 지원됩니다.
- **5G/LTE Wi-Fi 핫스팟 디바이스** - 개발 키트는 Wi-Fi 핫스팟이 제공하는 Wi-Fi 네트워크에 연결됩니다. 이 경우 개발 키트는 다른 Wi-Fi 네트워크와 같은 네트워크에 연결됩니다. 자세한 내용은 [Azure Percept DK 설치 가이드](./quickstart-percept-dk-set-up.md)를 따르고 핫스팟에서 5G/LTE Wi-Fi 네트워크 브로드캐스트를 선택합니다.
- **5G/LTE 이더넷 게이트웨이 디바이스** - 여기에서 개발 키트는 이더넷을 통해 5G/LTE 게이트웨이에 연결되며, Wi-Fi 연결에 비해 향상된 보안을 활용합니다. 이 문서의 나머지 부분에서는 이와 같은 네트워크를 구성하는 방법에 대해 자세히 설명합니다.

## <a name="5glte-gateway-topology"></a>5G/LTE 게이트웨이 토폴로지
:::image type="Image" source="media/connect-over-cellular/topology.png" alt-text="이 다이어그램에서는 Azure Percept DK에서 이더넷을 통해 5G/LTE 게이트웨이에 연결하는 방법을 보여 줍니다.":::

위의 다이어그램에서 5G/LTE 게이트웨이를 Azure Percept DK와 쉽게 연결할 수 있는 방법을 확인할 수 있습니다.

## <a name="considerations-when-connecting-to-a-5g-or-lte-gateway"></a>5G 또는 LTE 게이트웨이에 연결할 때의 고려 사항
다음은 Azure Percept DK를 5G/LTE 게이트웨이에 연결할 때 고려해야 할 몇 가지 중요 사항입니다.
- 먼저 게이트웨이를 설정한 다음, SIM을 통해 연결을 수신하는지 확인합니다. 그러면 Azure Percept DK에 연결하는 동안 발견된 문제를 더 쉽게 해결할 수 있습니다.
- 이더넷 케이블의 양쪽 끝이 게이트웨이 및 Azure Percept DK에 단단히 연결되어 있는지 확인합니다.
- 이더넷을 통한 Azure Percept DK 연결에 대한 [기본 지침](./how-to-connect-over-ethernet.md)을 따릅니다.
- 5G/LTE 플랜에 할당량이 있는 경우 Azure Percept DK 모델이 클라우드로 전송하는 데이터의 양을 최적화하는 것이 좋습니다.
- 외부에서 시작된 인바운드 트래픽을 차단하는 [적절하게 구성된 방화벽](./concept-security-configuration.md)이 있는지 확인합니다.

## <a name="ssh-over-a-5g-or-lte-network"></a>5G 또는 LTE 네트워크를 통해 SSH
5G/LTE 이더넷 게이트웨이를 통해 개발 키트로 SSH하려면 다음 옵션을 사용할 수 있습니다.
- **개발 키트의 Wi-Fi 액세스 지점 사용** Wi-Fi를 사용하지 않도록 설정한 경우 개발 키트를 다시 부팅하여 다시 사용하도록 설정할 수 있습니다. 여기에서 개발 키트의 Wi-Fi 액세스 지점에 연결하고 [다음 SSH 절차](./how-to-ssh-into-percept-dk.md)를 수행할 수 있습니다.
- **로컬 네트워크(LAN)에 대한 이더넷 연결 사용** 이 옵션을 사용하여 5G/LTE 게이트웨이에서 개발 키트를 분리하고 LAN 라우터에 연결합니다. 자세한 내용은 [이더넷을 통해 연결하는 방법](./how-to-connect-over-ethernet.md)을 참조하세요. 
- **게이트웨이의 원격 액세스 기능 사용** 많은 5G/LTE 게이트웨이는 SSH를 통해 네트워크에서 디바이스에 연결하는 데 사용할 수 있는 원격 액세스 관리자를 포함합니다. 5G/LTE 게이트웨이의 제조업체에 문의하여 이 기능이 있는지 확인합니다. [Cradlepoint 5G/LTE 게이트웨이](https://customer.cradlepoint.com/s/article/NCM-Remote-Connect-LAN-Manager)의 원격 액세스 관리자의 예제는 다음과 같습니다.
- **개발 키트의 직렬 포트 사용** Azure Percept DK는 디바이스에 직접 연결하는 데 사용할 수 있는 직렬 연결 포트를 포함합니다. 자세한 지침은 [직렬을 통한 Azure Percept DK 연결](./how-to-connect-to-percept-dk-over-serial.md)을 참조하세요.

## <a name="considerations-when-selecting-a-5g-or-lte-gateway-device"></a>5G 또는 LTE 게이트웨이 디바이스를 선택할 때의 고려 사항
5G/LTE 게이트웨이는 다운로드 및 업로드에 대한 최대 데이터 요금에 영향을 주는 다양한 기술을 지원합니다. 보급된 데이터 요금은 의사 결정에 대한 지침을 제공하지만 일반적으로는 도달하지 않습니다. 다음은 요구 사항에 적합한 게이트웨이를 선택하기 위한 몇 가지 지침입니다.
 
- **LTE CAT-1** 은 최대 10Mbps 다운 및 5Mbps 업을 제공합니다. 개체 검색 및 음성 도우미 작성과 같은 기본 Azure Percept Devkit 기능에는 충분합니다. 그러나 클라우드에 비디오 스트리밍 데이터를 요구하는 솔루션에는 충분하지 않을 수 있습니다.
- **LTE CAT-3 및 4** 는 최대 100Mbps 다운 및 50Mbps 업을 제공하며, 클라우드로 비디오를 스트리밍하는 데 충분합니다. 그러나 전체 HD 품질 비디오를 스트리밍하는 것 만으로는 충분하지 않습니다.
- **LTE CAT-5 이상** 에서는 단일 디바이스에 대한 스트리밍 HD 비디오에 충분한 데이터 속도를 제공합니다. 여러 디바이스를 단일 게이트웨이에 연결해야 하는 경우 5G를 고려하는 것이 좋습니다.
- **5G** 게이트웨이는 나중에 시나리오를 가장 적합하게 배치합니다. 한 번에 여러 디바이스에 대한 높은 데이터 처리량을 지원할 수 있는 데이터 속도와 대역폭이 있습니다. 또한 데이터 전송에 대한 대기 시간을 줄입니다.


## <a name="next-steps"></a>다음 단계
5G/LTE 게이트웨이가 있고 Azure Percept DK를 연결하려는 경우 다음 단계를 수행합니다.
- [이더넷을 통해 Azure Percept DK를 연결하는 방법](./how-to-connect-over-ethernet.md)
