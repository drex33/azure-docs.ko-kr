---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 09/17/2021
ms.author: amishu
ms.openlocfilehash: 01b63f92ba7cf36802c0d91e4930280a3d802ab7
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156738"
---
압축된 오디오 입력을 허용하도록 Speech SDK를 구성하려면 또는 을 `PullAudioInputStream` `PushAudioInputStream` 만듭니다. 그런 다음, 스트림 클래스의 인스턴스에서 `AudioConfig`를 만들어 스트림의 압축 형식을 지정합니다.

다음 예제에서는 사용 사례가 압축된 파일에 사용하는 것이라고 가정해 `PushStream` 보겠습니다. 

```go

package recognizer

import (
  "fmt"
  "time"
    "strings"

  "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
  "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
  "github.com/Microsoft/cognitive-services-speech-sdk-go/samples/helpers"
)

func RecognizeOnceFromCompressedFile(subscription string, region string, file string) {
  var containerFormat audio.AudioStreamContainerFormat
  if strings.Contains(file, ".mulaw") {
    containerFormat = audio.MULAW
  } else if strings.Contains(file, ".alaw") {
    containerFormat = audio.ALAW
  } else if strings.Contains(file, ".mp3") {
    containerFormat = audio.MP3
  } else if strings.Contains(file, ".flac") {
    containerFormat = audio.FLAC
  } else if strings.Contains(file, ".opus") {
    containerFormat = audio.OGGOPUS
  } else {
    containerFormat = audio.ANY
  }
  format, err := audio.GetCompressedFormat(containerFormat)
  if err != nil {
    fmt.Println("Got an error: ", err)
    return
  }
  defer format.Close()
  stream, err := audio.CreatePushAudioInputStreamFromFormat(format)
  if err != nil {
    fmt.Println("Got an error: ", err)
    return
  }
  defer stream.Close()
  audioConfig, err := audio.NewAudioConfigFromStreamInput(stream)
  if err != nil {
    fmt.Println("Got an error: ", err)
    return
  }
  defer audioConfig.Close()
  config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
  if err != nil {
    fmt.Println("Got an error: ", err)
    return
  }
  defer config.Close()
  speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
  if err != nil {
    fmt.Println("Got an error: ", err)
    return
  }
  defer speechRecognizer.Close()
  speechRecognizer.SessionStarted(func(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Started (ID=", event.SessionID, ")")
  })
  speechRecognizer.SessionStopped(func(event speech.SessionEventArgs) {
    defer event.Close()
    fmt.Println("Session Stopped (ID=", event.SessionID, ")")
  })
  helpers.PumpFileIntoStream(file, stream)
  task := speechRecognizer.RecognizeOnceAsync()
  var outcome speech.SpeechRecognitionOutcome
  select {
  case outcome = <-task:
  case <-time.After(40 * time.Second):
    fmt.Println("Timed out")
    return
  }
  defer outcome.Close()
  if outcome.Error != nil {
    fmt.Println("Got an error: ", outcome.Error)
  }
  fmt.Println("Got a recognition!")
  fmt.Println(outcome.Result.Text)
}

```
