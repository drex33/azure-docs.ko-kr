---
title: Azure Percept Audio 디바이스 개요
description: Azure Percept Audio에 대해 자세히 알아보기
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: 6afe4da59b837d4901bfb7243d5e0734b4c8df28
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226902"
---
# <a name="azure-percept-audio-device-overview"></a>Azure Percept Audio 디바이스 개요

Azure Percept Audio는 [Azure Percept DK](./overview-azure-percept-dk.md)에 음성 AI 기능을 추가하는 액세서리 디바이스입니다. 여기에는 미리 구성된 오디오 프로세서와 4개의 마이크 선형 배열이 포함되어 있으며, Azure Cognitive Services를 활용하여 음성 명령, 키워드 포착 및 원거리 음성 지원을 사용할 수 있습니다. Azure Percept DK, [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 및 기타 Azure Edge 관리 서비스와 기본적으로 통합됩니다. Azure Percept Audio는 [Microsoft 온라인 스토어](https://go.microsoft.com/fwlink/p/?LinkId=2155270)에서 구매할 수 있습니다.

> [!div class="nextstepaction"]
> [지금 구입](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="azure-percept-audio-components"></a>Azure Percept Audio 구성 요소

Azure Percept Audio에는 다음과 같은 주요 구성 요소가 포함되어 있습니다.

- XMOS 코덱을 통해 4개의 마이크 선형 배열 및 오디오 처리를 제공하는 프로덕션 사용 가능 Azure Percept Audio 디바이스(SoM)
- 개발자(중재자) 보드: 2개 단추, 3개 LED, 마이크로 USB 및 3.5mm 오디오 잭
- 필요한 케이블: FPC 케이블, USB 마이크로 유형 B-USB A
- 환영 카드
- 통합 80/20 1010 시리즈 탑재를 사용하는 기계식 탑재 플레이트

## <a name="compute-capabilities"></a>컴퓨팅 기능 

Azure Percept Audio는 하이브리드 에지 클라우드 방식으로 Azure Percept DK 캐리어 보드의 CPU에서 실행되는 음성 스택을 통해 오디오 입력을 전달합니다. 따라서 Azure Percept Audio에는 작동을 위해 음성 스택을 지원하는 OS가 포함된 캐리어 보드가 필요합니다. 

오디오 처리는 다음과 같이 수행됩니다. 

- Azure Percept Audio: 오디오를 캡처 및 변환하고 DK 및 오디오 잭에 보냅니다.

- Azure Percept DK: 음성 스택은 빔 형성 및 에코 취소를 수행하고 들어오는 오디오를 처리하여 음성을 위해 최적화합니다. 처리 후에는 키워드 포착을 수행합니다.

- 클라우드: 자연어 명령 및 문구, 키워드 확인 및 재학습을 처리합니다. 

- 오프라인: 디바이스가 오프라인 상태인 경우 키워드를 검색하고 인터넷 연결 상태 원격 분석을 캡처합니다. 클라우드의 키워드 확인을 수행할 수 없기 때문에 키워드 포착의 잘못된 수락률이 증가하는 현상이 확인될 수 있습니다. 

## <a name="getting-started"></a>시작

- [Azure Percept DK 조합](./quickstart-percept-dk-unboxing.md)
- [Azure Percept Audio 디바이스를 devkit에 연결](./quickstart-percept-audio-setup.md)
- [Azure Percept DK 설정 환경 완료](./quickstart-percept-dk-set-up.md)

## <a name="build-a-no-code-prototype"></a>코드 없는 프로토타입 빌드

숙박, 의료, 재고 및 자동차 시나리오에 대한 Azure Percept 음성 지원 템플릿을 사용하여 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)에서 [코드 없는 음성 솔루션](./tutorial-no-code-speech.md)을 빌드합니다.

### <a name="manage-your-no-code-speech-solution"></a>코드 없는 음성 솔루션 관리

- [Azure Percept Studio에서 음성 도우미 구성](./how-to-manage-voice-assistant.md)
- [IoT Hub에서 음성 도우미 구성](./how-to-configure-voice-assistant.md)
- [Azure Percept Audio 문제 해결](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>추가 기술 정보

- [Azure Percept Audio 데이터시트](./azure-percept-audio-datasheet.md)
- [단추 및 LED 동작](./audio-button-led-behavior.md)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Microsoft 온라인 스토어에서 Azure Percept Audio 디바이스 구입](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
