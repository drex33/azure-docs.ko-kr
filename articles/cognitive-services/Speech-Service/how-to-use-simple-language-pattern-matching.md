---
title: C++ Speech SDK로 간단한 언어 패턴 매칭을 사용하는 방법
titleSuffix: Azure Cognitive Services
description: 이 가이드에서는 간단한 패턴에서 의도와 엔터티를 인식하는 방법을 배웁니다.
services: cognitive-services
author: chschrae
manager: travisw
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: chschrae
ms.custom: devx-track-cpp
ms.openlocfilehash: abb89fc460915d60402e6b259a8d34c0c5cdf05c
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713360"
---
# <a name="how-to-use-simple-language-pattern-matching-with-the-c-speech-sdk"></a>C++ Speech SDK로 간단한 언어 패턴 매칭을 사용하는 방법

Cognitive Services [Speech SDK](speech-sdk.md)에는 **단순 언어 패턴 일치** 와 함께 **의도 인식** 을 제공하는 기능이 내장되어 있습니다. 의도는 창 닫기, 확인란 표시, 텍스트 삽입 등을 사용자가 수행하려는 작업입니다.

이 가이드에서는 Speech SDK를 사용하여 디바이스의 마이크를 통해 사용자의 말에서 의도를 추론하는 C++ 콘솔 애플리케이션을 개발합니다. 이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
>
> - Speech SDK NuGet 패키지를 참조하는 Visual Studio 프로젝트 만들기
> - 음성 구성을 만들고 의도 인식기 가져오기
> - Speech SDK API를 통해 의도 및 패턴 추가
> - 파일에서 음성 인식
> - 비동기, 이벤트 중심 연속 인식 사용

## <a name="when-should-you-use-this"></a>이를 언제 사용해야 하나요?

다음과 같은 경우 이 샘플 코드를 사용합니다. 
* 사용자가 말한 것을 매우 엄격하게 일치시키는 데에만 관심이 있습니다. 이러한 패턴은 LUIS보다 더 적극적으로 일치합니다.
* LUIS 앱에 대한 액세스 권한이 없지만 여전히 의도를 원합니다. 이는 SDK에 포함되어 있으므로 유용할 수 있습니다.
* LUIS 앱을 만들 수 없거나 만들고 싶지 않지만 여전히 일부 음성 명령 기능이 필요합니다.

LUIS 앱에 대한 액세스 권한이 없지만 여전히 의도가 필요한 경우 SDK에 포함되어 있으므로 유용할 수 있습니다.


## <a name="prerequisites"></a>사전 요구 사항

이 가이드를 시작하기 전에, 다음 항목을 갖추고 있는지 확인합니다.

- [Cognitive Services Azure 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) 또는 [통합 Speech 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 모든 버전.

## <a name="speech-and-simple-patterns"></a>Speech와 간단한 패턴

단순 패턴은 Speech SDK의 기능이며 Cognitive Services 리소스 또는 통합 Speech 리소스가 필요합니다.

패턴은 그 안의 어딘가에 엔터티를 포함하는 구문입니다. 엔터티는 단어를 중괄호로 묶음으로써 정의됩니다. 예를 들면 다음과 같습니다.

```
    Take me to the {floorName}
```

이는 대/소문자를 구분하는 "floorName" ID로 엔터티를 정의합니다.

다른 모든 특수 문자와 구두점은 무시됩니다.

IntentRecognizer->AddIntent() API에 대한 호출을 사용하여 의도가 추가됩니다.

## <a name="create-a-speech-project-in-visual-studio"></a>Visual Studio에서 음성 프로젝트 만들기

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

Visual Studio에서 프로젝트 열기 다음으로 Visual Studio에서 프로젝트를 엽니다.

Visual Studio 2019를 시작합니다.
프로젝트를 로드하고 helloworld.cpp를 엽니다.
일부 상용구 코드로 시작하기 프로젝트의 골격으로 작동하는 코드를 추가해 보겠습니다. recognizeIntent()라는 비동기 메서드를 만듭니다.

## <a name="open-your-project-in-visual-studio"></a>Visual Studio에서 프로젝트 열기

그런 다음, Visual Studio에서 프로젝트를 엽니다.

1. Visual Studio 2019를 시작합니다.
2. 프로젝트를 로드하고 `helloworld.cpp`를 엽니다.

## <a name="start-with-some-boilerplate-code"></a>몇 가지 상용구 코드로 시작

프로젝트에 대한 기본 사항으로 작동하는 몇 가지 코드를 추가해 보겠습니다.

```cpp
    #include <iostream>
    #include <speechapi_cxx.h>

    using namespace Microsoft::CognitiveServices::Speech;
    using namespace Microsoft::CognitiveServices::Speech::Intent;

    int main()
    {
        std::cout << "Hello World!\n";

        auto config = SpeechConfig::FromSubscription("YOUR_SUBSCRIPTION_KEY", "YOUR_SUBSCRIPTION_REGION");
    }
```

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

`IntentRecognizer` 개체를 초기화하기 전에 Cognitive Services 예측 리소스의 키와 위치를 사용하는 구성을 만들어야 합니다.

* `"YOUR_SUBSCRIPTION_KEY"`를 Cognitive Services 예측 키로 바꿉니다.
* `"YOUR_SUBSCRIPTION_REGION"`을 Cognitive Services 리소스 지역으로 바꿉니다.

이 샘플에서는 `FromSubscription()` 메서드를 사용하여 `SpeechConfig`를 빌드합니다. 사용 가능한 메서드의 전체 목록은 [SpeechConfig 클래스](/cpp/cognitive-services/speech/speechconfig)를 참조하세요.

## <a name="initialize-an-intentrecognizer"></a>IntentRecognizer 초기화

이제 `IntentRecognizer`를 만듭니다. 음성 구성 바로 아래에 이 코드를 삽입합니다.

```cpp
    auto intentRecognizer = IntentRecognizer::FromConfig(config);
```

## <a name="add-some-intents"></a>의도 추가

`AddIntent()`를 호출하여 일부 패턴을 `IntentRecognizer`에 연결해야 합니다.
층을 변경하기 위해 동일한 ID를 가진 2개의 의도를 추가하고 문을 열고 닫는 별도의 ID를 가진 다른 의도를 추가합니다.

```cpp
    intentRecognizer->AddIntent("Take me to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("Go to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("{action} the door.", "OpenCloseDoor");
```

> [!NOTE]
> 선언할 수 있는 엔터티의 수에는 제한이 없지만 느슨하게 일치합니다. "{action} door"와 같은 구문을 추가하면 "door"라는 단어 앞에 텍스트가 있을 때마다 일치합니다. 의도는 엔터티 수를 기준으로 평가됩니다. 두 패턴이 일치하면 더 많은 엔터티가 정의된 패턴이 반환됩니다.

## <a name="recognize-an-intent"></a>의도 인식

`IntentRecognizer` 개체에서 `RecognizeOnceAsync()` 메서드를 호출합니다. 이 방법은 음성 서비스에 단일 구문의 음성을 인식하도록 요청하고 구문이 식별되면 음성 인식을 중지합니다. 번거로움을 피하기 위해 완료될 때까지 기다립니다.

이 코드를 사용자 의도 아래에 삽입합니다.

```cpp
    std::cout << "Say something ..." << std::endl;
    auto result = intentRecognizer->RecognizeOnceAsync().get();
```

## <a name="display-the-recognition-results-or-errors"></a>인식 결과(또는 오류) 표시

음성 서비스에서 인식 결과가 반환되면 결과를 출력해 보겠습니다.

이 코드를 `auto result = intentRecognizer->RecognizeOnceAsync().get();` 아래에 삽입합니다.

```cpp
auto entities = result->GetEntities();

switch (result->Reason)
{
case ResultReason::RecognizedSpeech:
case ResultReason::RecognizedIntent:
    std::cout << "RECOGNIZED: Text = " << result->Text.c_str() << std::endl;
    std::cout << "  Intent Id = " << result->IntentId.c_str() << std::endl;
    if (entities.find("floorName") != entities.end())
    {
        std::cout << "  Floor name: = " << entities["floorName"].c_str() << std::endl;
    }

    if (entities.find("action") != entities.end())
    {
        std::cout << "  Action: = " << entities["action"].c_str() << std::endl;
    }

    break;
case ResultReason::NoMatch:
{
    auto noMatch = NoMatchDetails::FromResult(result);
    switch (noMatch->Reason)
    {
    case NoMatchReason::NotRecognized:
        std::cout << "NOMATCH: Speech was detected, but not recognized." << std::endl;
        break;
    case NoMatchReason::InitialSilenceTimeout:
        std::cout << "NOMATCH: The start of the audio stream contains only silence, and the service timed out waiting for speech." << std::endl;
        break;
    case NoMatchReason::InitialBabbleTimeout:
        std::cout << "NOMATCH: The start of the audio stream contains only noise, and the service timed out waiting for speech." << std::endl;
        break;
    case NoMatchReason::KeywordNotRecognized:
        std::cout << "NOMATCH: Keyword not recognized" << std::endl;
        break;
    }
    break;
}
case ResultReason::Canceled:
{
    auto cancellation = CancellationDetails::FromResult(result);

    if (!cancellation->ErrorDetails.empty())
    {
        std::cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails.c_str() << std::endl;
        std::cout << "CANCELED: Did you update the subscription info?" << std::endl;
    }
}
default:
    break;
}
```

## <a name="check-your-code"></a>코드 확인

이 시점에서 코드는 다음과 같습니다.

```cpp
#include <iostream>
#include <speechapi_cxx.h>

using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Intent;

int main()
{
    auto config = SpeechConfig::FromSubscription("YOUR_SUBSCRIPTION_KEY", "YOUR_SUBSCRIPTION_REGION");
    auto intentRecognizer = IntentRecognizer::FromConfig(config);

    intentRecognizer->AddIntent("Take me to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("Go to floor {floorName}.", "ChangeFloors");
    intentRecognizer->AddIntent("{action} the door.", "OpenCloseDoor");

    std::cout << "Say something ..." << std::endl;

    auto result = intentRecognizer->RecognizeOnceAsync().get();
    auto entities = result->GetEntities();

    switch (result->Reason)
    {
    case ResultReason::RecognizedSpeech:
    case ResultReason::RecognizedIntent:
        std::cout << "RECOGNIZED: Text = " << result->Text.c_str() << std::endl;
        std::cout << "  Intent Id = " << result->IntentId.c_str() << std::endl;
        if (entities.find("floorName") != entities.end())
        {
            std::cout << "  Floor name: = " << entities["floorName"].c_str() << std::endl;
        }

        if (entities.find("action") != entities.end())
        {
            std::cout << "  Action: = " << entities["action"].c_str() << std::endl;
        }

        break;
    case ResultReason::NoMatch:
    {
        auto noMatch = NoMatchDetails::FromResult(result);
        switch (noMatch->Reason)
        {
        case NoMatchReason::NotRecognized:
            std::cout << "NOMATCH: Speech was detected, but not recognized." << std::endl;
            break;
        case NoMatchReason::InitialSilenceTimeout:
            std::cout << "NOMATCH: The start of the audio stream contains only silence, and the service timed out waiting for speech." << std::endl;
            break;
        case NoMatchReason::InitialBabbleTimeout:
            std::cout << "NOMATCH: The start of the audio stream contains only noise, and the service timed out waiting for speech." << std::endl;
            break;
        case NoMatchReason::KeywordNotRecognized:
            std::cout << "NOMATCH: Keyword not recognized." << std::endl;
            break;
        }
        break;
    }
    case ResultReason::Canceled:
    {
        auto cancellation = CancellationDetails::FromResult(result);

        if (!cancellation->ErrorDetails.empty())
        {
            std::cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails.c_str() << std::endl;
            std::cout << "CANCELED: Did you update the subscription info?" << std::endl;
        }
    }
    default:
        break;
    }
}
```
## <a name="build-and-run-your-app"></a>앱 빌드 및 실행

이제 앱을 빌드하고 음성 서비스를 사용하여 음성 인식을 테스트할 준비가 되었습니다.

1. **코드 컴파일** - Visual Studio의 메뉴 모음에서 **빌드** > **빌드 솔루션** 을 선택합니다.
2. **앱 시작** - 메뉴 모음에서 **디버그** > **디버깅 시작** 을 선택하거나 <kbd>F5</kbd> 키를 누릅니다.
3. **인식 시작** - 무언가를 말하라는 메시지가 표시됩니다. 기본 언어는 한국어(Korean)입니다. 음성은 Speech Service로 전송되어 텍스트로 변환되고 콘솔에 렌더링됩니다.

예를 들어 "Take me to floor 7"이라고 말하면 다음과 같이 출력됩니다.

```
Say something ...
RECOGNIZED: Text = Take me to floor 7.
  Intent Id = ChangeFloors
  Floor name: = seven
```
