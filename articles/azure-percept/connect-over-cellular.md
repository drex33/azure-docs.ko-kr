---
title: 5G 또는 LTE 네트워크를 통해 Azure Percept 연결
description: 이 문서에서는 5G 또는 LTE 네트워크를 통해 Azure Percept DK를 연결하는 방법을 설명합니다.
author: juhaluoto
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 09/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 0df673937ebd195c250a0f0a8880ecf89d42c844
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129997234"
---
# <a name="connect-azure-percept-over-5g-or-lte-networks"></a>5G 또는 LTE 네트워크를 통해 Azure Percept 연결

5G/LTE 네트워크를 통해 Edge AI 디바이스를 연결하면 많은 이점이 있습니다. Edge AI가 가장 효율적인 시나리오는 Wi-Fi 및 LAN 연결이 제한되는 위치(예: 스마트 도시, 자율 차량 및 농업)입니다. 또한 5G/LTE 네트워크는 Wi-Fi보다 더 나은 보안을 제공합니다. 마지막으로 Edge에서 AI를 실행하는 IoT 디바이스를 사용하여 5G/LTE 네트워크에서 대역폭을 최적화하는 방법을 제공합니다. 대부분의 데이터가 장치에서 처리 되는 동안 필요한 정보만 클라우드에 전송 됩니다. 현재 Azure Percept DK에서는 간단한 USB 모뎀을 사용 하 여 5g/LTE 네트워크에 직접 연결할 수도 있습니다. 다른 옵션에 대해 자세히 알아봅니다.

## <a name="options-for-connecting-azure-percept-dk-over-5g-or-lte-networks"></a>5g 또는 LTE 네트워크를 통한 Azure Percept DK 연결에 대 한 옵션
추가 하드웨어를 사용하여 5G/LTE 연결을 사용하여 Azure Percept DK를 연결할 수 있습니다. 현재 지원 되는 세 가지 옵션이 있습니다. 각 옵션에 대 한 자세한 정보 링크를 찾을 수 있습니다.
- **Usb 5G/LTE 모뎀 장치** -이제 Linux 운영 체제에 usb 모뎀 지원을 추가 하는 오픈 소스 ModemManger sw를 지 원하는 새로운 SW 이미지를 출시 했습니다. 이를 통해 다양 한 저렴 한 USB 모뎀을 사용 하 여 LTE 또는 5G 네트워크를 통해 Azure Percept에 연결할 수 있습니다. 자세한 내용은 [USB 모뎀을 사용 하 여 연결](./connect-over-cellular-usb.md)을 참조 하세요.   
- **5g/Lte 이더넷 게이트웨이 장치** -여기에서 Azure Percept는 이더넷을 통해 5G/lte 게이트웨이에 연결 됩니다. [셀룰러 게이트웨이를 사용 하 여 연결 하](./connect-over-cellular-gateway.md)는 추가 정보가 있습니다.
- **5G/LTE Wi-Fi 핫스팟 장치** -Azure Percept는 Wi-Fi 핫스팟이 제공 하는 Wi-Fi 네트워크에 연결 됩니다. 이 경우 개발 키트는 다른 Wi-Fi 네트워크와 같은 네트워크에 연결됩니다. 자세한 내용은 [Azure Percept DK 설치 가이드](./quickstart-percept-dk-set-up.md)를 따르고 핫스팟에서 5G/LTE Wi-Fi 네트워크 브로드캐스트를 선택합니다.


## <a name="considerations-when-selecting-a-5g-or-lte-device-in-general"></a>일반적으로 5G 또는 LTE 장치를 선택할 때의 고려 사항
USB 모뎀 인지, 아니면 이더넷 게이트웨이 든, 다운로드 및 업로드에 대 한 최대 데이터 요금에 영향을 주는 다양 한 기술을 지 원하는 5G/LTE 장치입니다. 보급 된 데이터 요금은 의사 결정에 대 한 지침을 제공 하지만 실제 세계에서는 거의 도달 하지 않습니다. 다음은 요구 사항에 적합 한 장치를 선택 하기 위한 몇 가지 지침입니다.
 
- **LTE CAT-1** 은 최대 10Mbps 다운 및 5Mbps 업을 제공합니다. 개체 검색 및 음성 도우미 작성과 같은 기본 Azure Percept Devkit 기능에는 충분합니다. 그러나 클라우드에 비디오 스트리밍 데이터를 요구하는 솔루션에는 충분하지 않을 수 있습니다.
- **LTE CAT-3 및 4** 는 최대 100Mbps 다운 및 50Mbps 업을 제공하며, 클라우드로 비디오를 스트리밍하는 데 충분합니다. 그러나 전체 HD 품질 비디오를 스트리밍하는 것 만으로는 충분하지 않습니다.
- **LTE CAT-5 이상** 에서는 단일 디바이스에 대한 스트리밍 HD 비디오에 충분한 데이터 속도를 제공합니다. 여러 디바이스를 단일 게이트웨이에 연결해야 하는 경우 5G를 고려하는 것이 좋습니다.
- **5G** 게이트웨이는 나중에 시나리오를 가장 적합하게 배치합니다. 한 번에 여러 디바이스에 대한 높은 데이터 처리량을 지원할 수 있는 데이터 속도와 대역폭이 있습니다. 또한 데이터 전송에 대한 대기 시간을 줄입니다.


## <a name="next-steps"></a>다음 단계
액세스할 수 있는 셀룰러 장치에 따라 다음 링크를 따라 Azure Percept dev kit에 연결 합니다.

[셀룰러 게이트웨이를 사용 하 여 커넥트](./connect-over-cellular-gateway.md)합니다.

[USB 모뎀을 사용 하 여 커넥트](./connect-over-cellular-usb.md)합니다.
