---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: pafarley
ms.custom: devx-track-js
ms.openlocfilehash: afcf87ad782307b8f2210b544eabe49aa34ab7e3
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123543450"
---
## <a name="start-with-some-boilerplate-code"></a>몇 가지 상용구 코드로 시작

프로젝트에 대한 기본 사항으로 작동하는 몇 가지 코드를 추가해 보겠습니다.

```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Microsoft Cognitive Services Speech SDK JavaScript Quickstart</title>
    <meta charset="utf-8" />
    </head>
    <body style="font-family:'Helvetica Neue',Helvetica,Arial,sans-serif; font-size:13px;">
    </body>
    </html>
```
## <a name="add-ui-elements"></a>UI 요소 추가

이제 입력 상자에 기본 UI를 추가하고, Speech SDK의 JavaScript를 참조하고, 사용 가능한 경우 권한 부여 토큰을 가져옵니다.

```html
<body style="font-family:'Helvetica Neue',Helvetica,Arial,sans-serif; font-size:13px;">
  <div id="content" style="display:none">
    <table width="100%">
      <tr>
        <td></td>
        <td><h1 style="font-weight:500;">Microsoft Cognitive Services Speech SDK JavaScript Quickstart</h1></td>
      </tr>
      <tr>
        <td align="right"><a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started" target="_blank">Subscription</a>:</td>
        <td><input id="subscriptionKey" type="text" size="40" value="subscription"></td>
      </tr>
      <tr>
        <td align="right">Region</td>
        <td><input id="serviceRegion" type="text" size="40" value="YourServiceRegion"></td>
      </tr>
      <tr>
        <td align="right">Application ID:</td>
        <td><input id="appId" type="text" size="60" value="YOUR_LANGUAGE_UNDERSTANDING_APP_ID"></td>
      </tr>
      <tr>
        <td></td>
        <td><button id="startIntentRecognizeAsyncButton">Start Intent Recognition</button></td>
      </tr>
      <tr>
        <td align="right" valign="top">Input Text</td>
        <td><textarea id="phraseDiv" style="display: inline-block;width:500px;height:200px"></textarea></td>
      </tr>
      <tr>
        <td align="right" valign="top">Result</td>
        <td><textarea id="statusDiv" style="display: inline-block;width:500px;height:100px"></textarea></td>
      </tr>
    </table>
  </div>

  <script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>

  <script>
  // Note: Replace the URL with a valid endpoint to retrieve
  //       authorization tokens for your subscription.
  var authorizationEndpoint = "token.php";

  function RequestAuthorizationToken() {
    if (authorizationEndpoint) {
      var a = new XMLHttpRequest();
      a.open("GET", authorizationEndpoint);
      a.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
      a.send("");
      a.onload = function() {
                var token = JSON.parse(atob(this.responseText.split(".")[1]));
                serviceRegion.value = token.region;
                authorizationToken = this.responseText;
                subscriptionKey.disabled = true;
                subscriptionKey.value = "using authorization token (hit F5 to refresh)";
                console.log("Got an authorization token: " + token);
      }
    }
  }
  </script>

  <script>
    // status fields and start button in UI
    var phraseDiv;
    var statusDiv;
    var startIntentRecognizeAsyncButton;

    // subscription key, region, and appId for LUIS services.
    var subscriptionKey, serviceRegion, appId;
    var authorizationToken;
    var SpeechSDK;
    var recognizer;

    document.addEventListener("DOMContentLoaded", function () {
      startIntentRecognizeAsyncButton = document.getElementById("startIntentRecognizeAsyncButton");
      subscriptionKey = document.getElementById("subscriptionKey");
      serviceRegion = document.getElementById("serviceRegion");
      appId = document.getElementById("appId");
      phraseDiv = document.getElementById("phraseDiv");
      statusDiv = document.getElementById("statusDiv");

      startIntentRecognizeAsyncButton.addEventListener("click", function () {
        startIntentRecognizeAsyncButton.disabled = true;
        phraseDiv.innerHTML = "";
        statusDiv.innerHTML = "";
      });

      if (!!window.SpeechSDK) {
        SpeechSDK = window.SpeechSDK;
        startIntentRecognizeAsyncButton.disabled = false;

        document.getElementById('content').style.display = 'block';
        document.getElementById('warning').style.display = 'none';

        // in case we have a function for getting an authorization token, call it.
        if (typeof RequestAuthorizationToken === "function") {
          RequestAuthorizationToken();
        }
      }
    });
  </script>
```

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

`SpeechRecognizer` 개체를 초기화하기 전에 구독 키와 구독 지역을 사용하는 구성을 만들어야 합니다. `startRecognizeOnceAsyncButton.addEventListener()` 메서드에 이 코드를 삽입합니다.

> [!NOTE]
> Speech SDK는 기본적으로 언어에 en-us를 사용하여 인식합니다. 원본 언어 선택에 대한 자세한 내용은 [음성 텍스트 변환에 대한 원본 언어 지정](../../../../how-to-specify-source-language.md)을 참조하세요.


```JavaScript
        // if we got an authorization token, use the token. Otherwise use the provided subscription key
        var speechConfig;
        if (authorizationToken) {
          speechConfig = SpeechSDK.SpeechConfig.fromAuthorizationToken(authorizationToken, serviceRegion.value);
        } else {
          if (subscriptionKey.value === "" || subscriptionKey.value === "subscription") {
            alert("Please enter your Microsoft Cognitive Services Speech subscription key!");
            return;
          }
          startIntentRecognizeAsyncButton.disabled = false;
          speechConfig = SpeechSDK.SpeechConfig.fromSubscription(subscriptionKey.value, serviceRegion.value);
        }

        speechConfig.speechRecognitionLanguage = "en-US";
```

## <a name="create-an-audio-configuration"></a>오디오 구성 만들기

이제 입력 디바이스를 가리키는 `AudioConfig` 개체를 만들어야 합니다. `startIntentRecognizeAsyncButton.addEventListener()` 메서드(음성 구성 바로 아래)에 이 코드를 삽입합니다.

```JavaScript
        var audioConfig = SpeechSDK.AudioConfig.fromDefaultMicrophoneInput();
```

## <a name="initialize-a-intentrecognizer"></a>IntentRecognizer 초기화

이제 앞에서 만든 `SpeechConfig` 및 `AudioConfig` 개체를 사용하여 `IntentRecognizer` 개체를 만들어 보겠습니다. `startIntentRecognizeAsyncButton.addEventListener()` 메서드에 이 코드를 삽입합니다.

```JavaScript
        recognizer = new SpeechSDK.IntentRecognizer(speechConfig, audioConfig);
```

## <a name="add-a-languageunderstandingmodel-and-intents"></a>LanguageUnderstandingModel 및 의도 추가

`LanguageUnderstandingModel`을 의도 인식기와 연결하고 인식되길 원하는 의도를 추가해야 합니다. 홈 자동화를 위해 미리 빌드된 도메인에서 의도를 사용할 것입니다.

이 코드를 `IntentRecognizer` 아래에 삽입합니다. `"YourLanguageUnderstandingAppId"`를 LUIS 앱 ID로 바꾸어야 합니다.

```JavaScript
        if (appId.value !== "" && appId.value !== "YOUR_LANGUAGE_UNDERSTANDING_APP_ID") {
          var lm = SpeechSDK.LanguageUnderstandingModel.fromAppId(appId.value);

          recognizer.addAllIntents(lm);
        }
```

> [!NOTE]
> Speech SDK는 LUIS v2.0 엔드포인트만 지원합니다.
> v2.0 URL 패턴을 사용하려면 예제 쿼리 필드에 있는 v3.0 엔드포인트 URL을 수동으로 수정해야 합니다.
> LUIS v2.0 엔드포인트는 항상 다음 두 패턴 중 하나를 따릅니다.
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>의도 인식

`IntentRecognizer` 개체에서 `recognizeOnceAsync()` 메서드를 호출합니다. 이 메서드를 사용하면 Speech Service에서 사용자가 인식을 위해 단일 구를 보내고, 구가 식별되고 나면 음성 인식이 중지됨을 알 수 있습니다.

모델 추가 아래에 이 코드를 삽입합니다.

```JavaScript
        recognizer.recognizeOnceAsync(
          function (result) {
            window.console.log(result);

            phraseDiv.innerHTML = result.text + "\r\n";

            statusDiv.innerHTML += "(continuation) Reason: " + SpeechSDK.ResultReason[result.reason];
            switch (result.reason) {
              case SpeechSDK.ResultReason.RecognizedSpeech:
                statusDiv.innerHTML += " Text: " + result.text;
                break;
              case SpeechSDK.ResultReason.RecognizedIntent:
                statusDiv.innerHTML += " Text: " + result.text + " IntentId: " + result.intentId;

                // The actual JSON returned from Language Understanding is a bit more complex to get to, but it is available for things like
                // the entity name and type if part of the intent.
                statusDiv.innerHTML += " Intent JSON: " + result.properties.getProperty(SpeechSDK.PropertyId.LanguageUnderstandingServiceResponse_JsonResult);
                phraseDiv.innerHTML += result.properties.getProperty(SpeechSDK.PropertyId.LanguageUnderstandingServiceResponse_JsonResult) + "\r\n";
                break;
              case SpeechSDK.ResultReason.NoMatch:
                var noMatchDetail = SpeechSDK.NoMatchDetails.fromResult(result);
                statusDiv.innerHTML += " NoMatchReason: " + SpeechSDK.NoMatchReason[noMatchDetail.reason];
                break;
              case SpeechSDK.ResultReason.Canceled:
                var cancelDetails = SpeechSDK.CancellationDetails.fromResult(result);
                statusDiv.innerHTML += " CancellationReason: " + SpeechSDK.CancellationReason[cancelDetails.reason];

              if (cancelDetails.reason === SpeechSDK.CancellationReason.Error) {
                statusDiv.innerHTML += ": " + cancelDetails.errorDetails;
              }
            break;
            }
            statusDiv.innerHTML += "\r\n";
            startIntentRecognizeAsyncButton.disabled = false;
          },
          function (err) {
            window.console.log(err);

            phraseDiv.innerHTML += "ERROR: " + err;
            startIntentRecognizeAsyncButton.disabled = false;
          });
```

## <a name="check-your-code"></a>코드 확인

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/intent-recognition/index.html)]

## <a name="create-the-token-source-optional"></a>토큰 소스 만들기(선택 사항)

웹 서버에 웹 페이지를 호스트하려는 경우 필요에 따라 데모 애플리케이션의 토큰 소스를 제공하면 됩니다.
이 방식을 사용하면 사용자가 권한 부여 코드를 직접 입력하지 않고 음성 기능을 사용할 수 있으면서도 구독 키가 절대 서버를 벗어나지 않습니다.

이름이 `token.php`인 새 파일을 만듭니다. 이 예제에서는 웹 서버에서 curl이 활성화된 PHP 스크립팅 언어를 지원한다고 가정하겠습니다. 다음 코드를 입력합니다.

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> 권한 부여 토큰의 수명은 제한되어 있습니다.
> 이 간단한 예제에서는 권한 부여 토큰을 자동으로 새로 고치는 방법을 보여주지 않습니다. 사용자는 수동으로 페이지를 다시 로드하거나 F5 키를 눌러 새로 고칠 수 있습니다.

## <a name="build-and-run-the-sample-locally"></a>로컬로 샘플을 빌드 및 실행

앱을 시작하려면 index.html 파일을 두 번 클릭하거나 선호하는 웹 브라우저를 사용하여 index.html을 엽니다. LUIS 키, [LUIS 지역](../../../../regions.md) 및 LUIS 애플리케이션 ID를 입력할 수 있는 간단한 GUI를 제공합니다. 이러한 필드를 입력한 후에는 해당 단추를 클릭하여 마이크를 통해 인식을 트리거할 수 있습니다.

> [!NOTE]
> 이 메서드는 Safari 브라우저에서 작동하지 않습니다.
> Safari에서는 웹 서버에서 호스팅하려면 샘플 웹 페이지가 필요합니다. Safari에서는 로컬 파일에 로드된 웹 사이트를 통해 마이크를 사용할 수 없습니다.

## <a name="build-and-run-the-sample-via-a-web-server"></a>웹 서버를 통해 샘플 빌드 및 실행

앱을 시작하려면 선호하는 웹 브라우저를 열고 폴더를 호스트하는 공용 URL을 가리킨 다음, LUIS 애플리케이션 ID와 함께 [LUIS 지역](../../../../regions.md)을 입력하고, 마이크를 사용하여 인식을 트리거합니다. 구성되면 토큰 소스에서 토큰을 획득하고 음성 명령을 인식하기 시작합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](footer.md)]
