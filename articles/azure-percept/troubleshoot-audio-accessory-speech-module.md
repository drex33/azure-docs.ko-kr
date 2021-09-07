---
title: Azure Percept Audio 및 음성 모듈 관련 문제 해결
description: Azure Percept Audio 및 azureearspeechclientmodule에 대한 문제 해결 팁 가져오기
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/03/2021
ms.custom: template-how-to
ms.openlocfilehash: ded0fe7150fae280bd2d2e33bc314c1cb8add106
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529669"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Azure Percept Audio 및 음성 모듈 문제 해결

음성 도우미 애플리케이션 문제를 해결하려면 아래 지침을 따르십시오.

## <a name="checking-runtime-status-of-the-speech-module"></a>음성 모듈의 런타임 상태를 확인하는 중

**azureearspeechclientmodule** 의 런타임 상태가 **실행 중** 으로 표시되는지 확인합니다. 디바이스 모듈의 런타임 상태를 찾으려면 [Azure Portal](https://portal.azure.com/)을 열고 **모든 리소스** ->  **[사용자의 IoT 허브]**  -> **IoT Edge** ->  **[사용자의 디바이스 ID]** 로 이동합니다. **모듈** 탭을 선택하여 설치된 모든 모듈의 런타임 상태를 확인합니다.

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Azure Portal의 에지 디바이스 페이지.":::

**azureearspeechclientmodule** 의 런타임 상태가 **실행 중** 으로 표시되지 않으면 **모듈 설정** -> **azureearspeechclientmodule** 을 선택합니다. **모듈 설정** 페이지에서 **원하는 상태** 를 **실행 중** 으로 설정하고 **업데이트** 를 선택합니다.

## <a name="voice-assistant-application-doesnt-load"></a>음성 도우미 애플리케이션이 로드되지 않음
[음성 도우미 템플릿 중 하나를 배포](./tutorial-no-code-speech.md)해보세요. 템플릿을 배포하면 음성 도우미 애플리케이션에 필요한 모든 지원 리소스가 만들어집니다.

## <a name="voice-assistant-template-doesnt-get-created"></a>음성 도우미 템플릿이 생성되지 않음
음성 도우미 템플릿을 만들지 못하는 문제는 일반적으로 지원 리소스 중 하나에 문제가 있기 때문입니다.
1. [이전에 만든 모든 음성 도우미 리소스를 삭제](./delete-voice-assistant-application.md)합니다.
1. 새 [음성 도우미 템플릿](./tutorial-no-code-speech.md)을 배포합니다.

## <a name="voice-assistant-was-created-but-doesnt-respond-to-commands"></a>음성 도우미를 만들었지만 명령에 응답하지 않음
[LED 동작 및 문제 해결 가이드](audio-button-led-behavior.md)의 지침에 따라 이 문제를 해결합니다.

## <a name="voice-assistant-doesnt-respond-to-custom-keywords-created-in-speech-studio"></a>음성 도우미가 Speech Studio에서 만든 사용자 지정 키워드에 응답하지 않음
음성 모듈이 오래된 경우에 이 문제가 발생할 수 있습니다. 다음 단계에 따라 음성 모듈을 최신 버전으로 업데이트합니다.

1. Azure Percept Studio 홈페이지의 왼쪽 메뉴 패널에서 **디바이스** 를 선택합니다.
1. 해당 디바이스를 찾아서 선택합니다.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Azure Percept Studio의 디바이스 목록을 보여 주는 스크린샷":::
1. 디바이스 창에서 **음성** 탭을 선택합니다.
1. 음성 모듈 버전을 확인합니다. 사용 가능한 업데이트가 있으면 버전 번호 옆에 **업데이트** 단추가 표시됩니다.
1. **업데이트** 를 선택하여 음성 모듈 업데이트를 배포합니다. 일반적으로 업데이트 프로세스를 완료하는 데 2-3분 정도 걸립니다.

## <a name="collecting-speech-module-logs"></a>음성 모듈 로그 수집
해당 명령을 실행하려면 [개발자 키트에 SSH](./how-to-ssh-into-percept-dk.md)를 실행하고 SSH 클라이언트 프롬프트에 명령을 입력합니다.

다음과 같이 음성 모듈 로그를 수집합니다.

```console
sudo iotedge logs azureearspeechclientmodule
```

추가 분석을 위해 출력을 .txt 파일로 리디렉션하려면 다음 구문을 사용합니다.

```console
sudo [command] > [file name].txt
```

복사할 수 있도록 .txt 파일의 사용 권한을 변경합니다.

```console
sudo chmod 666 [file name].txt
```

.txt 파일에 출력을 리디렉션하는 경우 SCP를 통해 호스트 PC에 파일을 복사합니다.

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[로컬 호스트 파일 경로]는 .txt 파일을 복사할 호스트 PC의 위치를 나타냅니다. [remote username]은 [설정 환경](./quickstart-percept-dk-set-up.md)에서 선택된 SSH 사용자 이름입니다.

## <a name="known-issues"></a>알려진 문제
- 무료 평가판을 사용하는 경우 음성 모델이 무료 평가판 가격 계획을 초과할 수 있습니다. 이 경우 모델은 오류 메시지 없이 작동을 중지합니다.
- 6개 이상의 IoT Edge 디바이스가 연결된 경우 보고서(원격 분석을 통해 IoT Hub 및 Speech Studio로 전송된 텍스트)가 차단될 수 있습니다.
- 디바이스가 리소스와 다른 지역에 있는 경우 보고서 메시지가 지연될 수 있습니다. 

## <a name="useful-links"></a>유용한 링크
- [Azure Percept Audio 설정](./quickstart-percept-audio-setup.md)
- [Azure Percept Audio 단추 및 LED 동작](./audio-button-led-behavior.md)
- [Azure Percept DK 및 Azure Percept Audio를 사용하여 음성 도우미 만들기](./tutorial-no-code-speech.md)
- [Azure Percept DK 일반 문제 해결 가이드](./troubleshoot-dev-kit.md)
- [이전에 만든 음성 도우미 애플리케이션으로 돌아가는 방법](return-to-voice-assistant-application-window.md)
