---
title: Azure Percept Audio 디바이스 설정
description: Azure Percept Audio 디바이스를 Azure Percept DK에 연결하는 방법을 알아봅니다.
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 8b6ec3ba8473be8e924d3c4b8ae17ccbdcf6568f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221835"
---
# <a name="set-up-the-azure-percept-audio-device"></a>Azure Percept Audio 디바이스 설정

Azure Percept Audio는 Azure Percept DK에서 기본 제공됩니다. 특별한 설정은 필요하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Percept DK(Devkit)
- Azure Percept Audio
- [Azure 구독](https://azure.microsoft.com/free/)
- [Azure Percept DK 설정 환경](./quickstart-percept-dk-set-up.md): devkit를 Wi-Fi 네트워크에 연결하고, IoT Hub를 만들고, devkit를 IoT Hub에 연결
- 3\.5mm 오디오 잭에 연결할 수 있는 스피커 또는 헤드폰(선택 사항)

## <a name="connecting-your-devices"></a>디바이스 연결

1. 포함된 Micro USB - USB Type-A 케이블로 Azure Percept Audio 디바이스를 Azure Percept DK 캐리어 보드에 연결합니다. 케이블의 Micro USB 쪽 끝을 오디오 인터포저(개발자) 보드에 연결하고 Type-A 쪽 끝을 Percept DK 캐리어 보드에 연결합니다.

1. (선택 사항) "라인 출력"이라고 표시된 오디오 잭을 통해 스피커 또는 헤드폰을 Azure Percept Audio 디바이스에 연결합니다. 그러면 오디오 응답을 들을 수 있습니다.

1. 개발 키트를 전원 어댑터에 연결하여 전원을 켭니다. LED L02가 흰색으로 깜박입니다. 이는 디바이스의 전원이 켜져 있고 인증 중임을 나타냅니다.

1. 인증 프로세스가 완료될 때까지 기다립니다. 최대 5분 정도 걸릴 수 있습니다.

1. 다음 LED 상태 중 하나가 표시되면 프로토타입 생성을 시작할 준비가 된 것입니다.

    - LED L02가 흰색으로 바뀌면 인증이 완료되고 devkit이 키워드 없이 구성되었음을 나타냅니다.
    - 세 개의 LED가 모두 파란색으로 켜지면 인증이 완료되었으며 devkit이 키워드로 구성되었음을 나타냅니다.

## <a name="next-steps"></a>다음 단계

[Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)에서 [코드 없는 음성 솔루션](./tutorial-no-code-speech.md) 만들기
