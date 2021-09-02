---
title: Azure Percept DK 디바이스 개봉 및 조립
description: Azure Percept DK를 언박싱 및 연결하고 기능을 활용하는 방법에 대해 알아봅니다.
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/16/2021
ms.custom: template-quickstart
ms.openlocfilehash: ce47b2463441fdd44c9db72c2dcbf2f8935b15d6
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222992"
---
# <a name="unbox-and-assemble-the-azure-percept-dk-device"></a>Azure Percept DK 디바이스 개봉 및 조립

Azure Percept DK를 받은 후에는 이 가이드에서 구성 요소를 연결하고 디바이스를 켜는 방법에 대한 정보를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Percept DK(Devkit)
- P7 드라이버(선택 사항, 캐리어 보드에 전원 케이블 커넥터를 고정하는 데 사용)

## <a name="unbox-and-assemble-your-device"></a>디바이스 언박싱 및 조립

1. Azure Percept DK 구성 요소를 언박싱합니다.

    Devkit에는 캐리어 보드, Azure Percept Vision SoM, 안테나와 케이블이 있는 액세서리 상자, 육각 키가 있는 환영 카드가 포함되어 있습니다.

1. Devkit 구성 요소를 연결합니다.

    > [!NOTE]
    > 전원 어댑터 포트는 캐리어 보드의 오른쪽에 있습니다. 나머지 포트(2x USB-A, 1x USB-C 및 1x 이더넷)와 전원 단추는 캐리어 보드의 왼쪽에 있습니다.

    1. Wi-Fi 안테나를 캐리어 보드에 나사로 고정합니다.

    1. USB-C 케이블을 사용하여 캐리어 보드의 USB-C 포트에 Vision SoM을 연결합니다.

    1. 전원 어댑터에 전원 케이블을 연결합니다.

    1. 나머지 플라스틱 패키징을 디바이스에서 제거합니다.

    1. 전원 어댑터/케이블을 캐리어 보드와 벽 콘센트에 연결합니다. 전원 케이블 커넥터를 캐리어 보드에 단단히 고정하기 위해 P7 드라이버(Devkit에 미포함)를 사용하여 커넥터 나사를 조입니다.

    1. 벽 콘센트에 전원 케이블을 연결하면 디바이스가 자동으로 켜집니다. 캐리어 보드의 왼쪽에 있는 전원 단추가 점등됩니다. 디바이스를 부팅하는 데는 어느 정도 시간이 걸릴 수 있습니다.

        > [!NOTE]
        > 전원 단추는 전원 콘센트에 연결된 상태에서 디바이스의 전원을 끄거나 다시 시작하는 데 사용됩니다. 정전이 발생하면 디바이스가 자동으로 다시 시작됩니다.

devkit 어셈블리의 시각적 데모는 다음 비디오의 0:00 ~ 0:50을 참조하세요.

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>다음 단계

이제 devkit가 연결되고 전원이 켜졌으므로 [Azure Percept DK 설정 환경 연습](./quickstart-percept-dk-set-up.md)을 참조하여 디바이스 설정을 완료하세요. 설정 환경에서는 Devkit를 Wi-Fi 네트워크에 연결하고, SSH 로그인을 설정하고, IoT Hub를 만들고, Devkit를 Azure 계정에 프로비저닝할 수 있습니다. 디바이스 설정을 완료하면 프로토타입 생성을 시작할 준비가 된 것입니다.