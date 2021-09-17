---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: pafarley
ms.openlocfilehash: e4377b4c69f484f1e248f7aa74731b9fbe956d52
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123540007"
---
macOS용으로 개발할 때 사용할 수 있는 세 가지 Speech SDK가 있습니다.

- Objective-C Speech SDK는 기본적으로 CocoaPod 패키지로 제공됩니다.
- .NET Speech SDK는 .NET Standard 2.0을 구현하므로 **Xamarin.Mac** 과 함께 사용할 수 있습니다.
- Python Speech SDK는 PyPI 모듈로 제공됩니다

> [!TIP]
> Swift와 함께 Objective-C Speech SDK를 사용하는 방법에 대한 자세한 내용은 <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Swift로 Objective-C 가져오기</a>를 참조하세요.

### <a name="system-requirements"></a>시스템 요구 사항

- macOS 버전 10.13 이상

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
platform :ios, '9.3'
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