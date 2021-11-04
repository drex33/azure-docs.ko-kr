---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 9af5bf37e007422b38da371b95c9b86d4341ce95
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521186"
---
## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에:

> [!div class="checklist"]
> * [Azure Speech 리소스 만들기](../../../../overview.md#try-the-speech-service-for-free)
> * [개발 환경을 설정하고 빈 프로젝트 만들기](../../../../quickstarts/setup-platform.md)
> * [Direct Line Speech 채널](/azure/bot-service/bot-service-channel-connect-directlinespeech)에 연결된 봇 만들기
> * 오디오 캡처를 위해 마이크에 액세스할 수 있는지 확인합니다.
>
  > [!NOTE]
  > [음성 도우미에 대한 지원되는 지역 목록](~/articles/cognitive-services/speech-service/regions.md#voice-assistants)을 참조하고 리소스가 해당 지역 중 하나에 배포되었는지 확인하세요.

## <a name="setup-your-environment"></a>사용자 환경 설정

이 줄을 추가하여 최신 SDK 버전으로 go.mod 파일을 업데이트합니다.
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.15.0
)
```

## <a name="start-with-some-boilerplate-code"></a>몇 가지 상용구 코드로 시작
원본 파일의 내용(예: `quickstart.go`)을 다음을 포함하는 아래와 같이 바꿉니다.

- "main" 패키지 정의
- Speech SDK에서 필요한 모듈 가져오기
- 이 빠른 시작의 뒷부분에서 바꿀 봇 정보를 저장하는 변수
- 오디오 입력에 마이크를 사용하는 간단한 구현
- 음성 상호 작용 중에 발생하는 다양한 이벤트에 대한 이벤트 처리기

```sh
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/dialog"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_BOT_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := dialog.NewBotFrameworkConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    connector, err := dialog.NewDialogServiceConnectorFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer connector.Close()
    activityReceivedHandler := func(event dialog.ActivityReceivedEventArgs) {
        defer event.Close()
        fmt.Println("Received an activity.")
    }
    connector.ActivityReceived(activityReceivedHandler)
    recognizedHandle := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognized ", event.Result.Text)
    }
    connector.Recognized(recognizedHandle)
    recognizingHandler := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognizing ", event.Result.Text)
    }
    connector.Recognizing(recognizingHandler)
    connector.ListenOnceAsync()
    <-time.After(10 * time.Second)
}
```

`YOUR_SUBSCRIPTION_KEY` 및 `YOUR_BOT_REGION` 값을 음성 리소스의 실제 값으로 바꿉니다.

- Azure Portal로 이동하여 음성 리소스를 엽니다.
- 왼쪽의 **키 및 엔드포인트** 에는 사용 가능한 두 개의 구독 키가 있습니다.
    - `YOUR_SUBSCRIPTION_KEY` 값 대체로 하나를 사용합니다.
- 왼쪽의 **개요** 에서 지역을 확인하고 지역 식별자에 매핑합니다.
    - 지역 식별자를 `YOUR_BOT_REGION` 값 대체로 사용합니다(예: **미국 서부** 의 경우 `"westus"`).

   > [!NOTE]
   > [음성 도우미에 대한 지원되는 지역 목록](~/articles/cognitive-services/speech-service/regions.md#voice-assistants)을 참조하고 리소스가 해당 지역 중 하나에 배포되었는지 확인하세요.

   > [!NOTE]
   > 봇 구성에 대한 자세한 내용은 [Direct Line Speech 채널](/azure/bot-service/bot-service-channel-connect-directlinespeech)에 대한 Bot Framework 설명서를 참조하세요.

## <a name="code-explanation"></a>코드 설명
음성 구성 개체를 만들려면 음성 구독 키와 지역이 필요합니다. 음성 인식기 개체를 인스턴스화하려면 구성 개체가 필요합니다.

인식기 인스턴스는 음성을 인식하는 여러 가지 방법을 제공합니다. 이 예제에서는 음성이 지속적으로 인식됩니다. 이 기능을 통해 Speech Service는 인식을 위해 많은 구를 보내고 프로그램이 종료되어 음식 인식을 중지할 때 알려줍니다. 결과가 생성되면 코드에서 콘솔에 해당 결과를 씁니다.

## <a name="build-and-run"></a>빌드 및 실행
이제 Speech Service를 사용하여 프로젝트를 빌드하고 사용자 지정 음성 도우미를 테스트할 수 있도록 설정했습니다.
1. 프로젝트 빌드(예: **"go build"** )
2. 모듈을 실행하고 디바이스의 마이크에 구나 문장을 말합니다. 음성은 Direct Line Speech 채널로 전송되어 출력으로 표시되는 텍스트로 전환됩니다.


> [!NOTE]
> Speech SDK는 기본적으로 언어에 en-us를 사용하여 인식합니다. 원본 언어 선택에 대한 자세한 내용은 [음성 텍스트 변환에 대한 원본 언어 지정](../../../../how-to-specify-source-language.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]
