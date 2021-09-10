---
title: Azure Percept Audio 단추 및 LED 동작
description: Azure Percept Audio의 단추 및 LED 상태에 대해 자세히 알아봅니다.
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 04ee60d6ebe9c84d77ea7e5ead140f7930b8cfe4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224549"
---
# <a name="azure-percept-audio-button-and-led-states"></a>Azure Percept Audio 단추 및 LED 동작

Azure Percept Audio 디바이스의 단추 및 LED 상태에 대한 자세한 내용은 다음 지침을 참조하세요.

## <a name="button-behavior"></a>단추 동작

디바이스 동작을 제어하려면 단추를 사용합니다.

|단추 상태|동작|
|------------|----------|
|음소거|마이크 배열을 음소거/음소거 해제하려면 누릅니다. 단추 이벤트는 눌렀다 놓았을 때 트리거됩니다.|
|PTT/PTS|키워드 탐지 상태를 무시하고 명령 수신 상태를 활성화하려면 PTT를 누릅니다. 에이전트의 활성 대화 상자를 중지하고 키워드 탐지 상태로 돌아가려면 다시 누릅니다. 단추 이벤트는 눌렀다 놓았을 때 트리거됩니다. PTS는 에이전트가 듣거나 생각할 때가 아니라 말하는 동안 버튼을 눌렀을 때만 작동합니다.|

## <a name="led-states"></a>LED 상태

LED 표시기를 사용하여 디바이스의 상태를 파악할 수 있습니다.

|LED|LED 상태|Ear SoM 상태|
|---|------------|----------------|
|L02|1x 흰색, 정적 설정|전원 켜기 |
|L02|1x 흰색, 0.5Hz 깜박임|인증 진행 중 |
|L01 & L02 & L03|3x 파랑, 계속 켜져 있음|키워드를 기다리는 중|
|L01 & L02 & L03|LED 배열 깜박임, 20fps |수신 또는 말하기|
|L01 & L02 & L03|LED 배열 레이스, 20fps|생각|
|L01 & L02 & L03|3x 빨강, 계속 켜져 있음 |음소거|

## <a name="understanding-ear-som-led-indicators"></a>Ear SoM LED 표시기 이해
LED 표시기를 사용하여 디바이스의 상태를 파악할 수 있습니다. 디바이스 전원을 켜고 모듈이 완전히 초기화되기까지 4~5분 정도가 소요됩니다. 초기화 단계를 진행하는 동안 다음을 확인할 수 있습니다.

1. 가운데 흰색 LED 켜짐(정적): 디바이스의 전원이 켜져 있습니다.
1. 가운데 흰색 LED 켜짐(깜박임): 인증이 진행 중입니다.
1. 중앙 흰색 LED 켜짐(정적): 장치가 인증되었지만 키워드가 구성되지 않았습니다.
1. 데모가 배포되고 디바이스를 사용할 준비가 되면 세 개의 LED가 모두 파란색으로 바뀝니다.


## <a name="troubleshooting-led-issues"></a>LED 문제 해결
- **중앙 LED가 흰색이면** [템플릿을 사용하여 음성 도우미를 만드세요](./tutorial-no-code-speech.md).
- **중앙 LED가 항상 깜박이면** 인증 문제가 있다는 것입니다. 이러한 문제 해결 단계를 수행합니다.
    1. USB A 및 마이크로 USB 연결이 안전한지 확인합니다. 
    1. [음성 모듈이 실행 중인지](./troubleshoot-audio-accessory-speech-module.md#checking-runtime-status-of-the-speech-module) 확인합니다.
    1. 디바이스를 다시 시작합니다.
    1. [로그를 수집](./troubleshoot-audio-accessory-speech-module.md#collecting-speech-module-logs)하여 지원 요청에 첨부
    1. 개발 키트에서 최신 소프트웨어를 실행하고 있는지 확인하고 사용 가능한 경우 업데이트를 적용합니다.

## <a name="next-steps"></a>다음 단계

Azure Percept Audio 디바이스에 대한 문제 해결 팁은 이 [가이드](./troubleshoot-audio-accessory-speech-module.md)를 참조하세요.