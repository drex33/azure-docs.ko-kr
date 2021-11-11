---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: eur
ms.openlocfilehash: 642fa02cf8f40d7cc6a39a1951499cb7921ed100
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252404"
---
macOS용으로 개발하는 경우 다음 Speech SDK를 사용할 수 있습니다.

- Objective-C/Swift Speech SDK는 Mac x64 및 ARM 기반 실리콘용 CocoaPod 패키지로 기본적으로 제공됩니다. Swift와 함께 Objective-C Speech SDK를 사용하는 자세한 내용은 <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Objective-C를 Swift로 가져오기를 </a>참조하세요.
- .NET Speech SDK는 NuGet 패키지를 통해 사용할 수 있으며 **Xamarin.Mac** 및 **Unity** 애플리케이션 프레임워크에서 사용할 수 있습니다.
- Python Speech SDK는 Python 버전 3.7 이상용 PyPI 모듈로 사용할 수 있습니다.
- Java Speech SDK는 Maven 리포지토리를 통해 JAR 패키지로 사용할 수 있습니다.

### <a name="system-requirements"></a>시스템 요구 사항

- macOS 버전 10.14 이상

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        macOS CocoaPod 패키지는 <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1(이상)</a> IDE(통합 개발 환경)에서 다운로드하여 사용할 수 있습니다. 먼저 <a href="https://aka.ms/csspeech/macosbinary" target="_blank">이진 CocoaPod를 다운로드</a>합니다. 사용하기 위해 동일한 디렉터리에서 Pod를 추출하고, *Podfile* 을 만들고, `pod`를 `target`으로 나열합니다.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :osx, 10.14
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.15.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac은 .NET 개발자가 C#을 사용하여 원시 Mac 애플리케이션을 빌드하기 위한 전체 macOS SDK를 제공합니다. 자세한 내용은 <a href="/xamarin/mac/" target="_blank">Xamarin.Mac</a>을 참조하세요.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>추가 리소스

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS Speech SDK 빠른 시작 Objective-C 소스 코드</a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS Speech SDK 빠른 시작 Swift 소스 코드 </a>
