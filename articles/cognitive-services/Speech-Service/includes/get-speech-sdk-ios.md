---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: eur
ms.openlocfilehash: 942bf206454047f54765d096a2ac97fcabb895ab
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252352"
---
:::row:::
    :::column span="3":::
        iOS용으로 개발하는 경우 다음 Speech SDK를 사용할 수 있습니다. Objective-C/Swift Speech SDK는 기본적으로 iOS CocoaPod 패키지로 제공됩니다. 또는 .NET Speech SDK를 Xamarin.iOS 및 Unity 애플리케이션 프레임워크와 함께 사용할 수 있습니다.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> Swift와 함께 Objective-C Speech SDK를 사용하는 방법에 대한 자세한 내용은 <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">Swift로 Objective-C 가져오기</a>를 참조하세요.

### <a name="system-requirements"></a>시스템 요구 사항

- macOS 버전 10.3 이상
- 대상 iOS 9.3 이상

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        iOS CocoaPod 패키지는 <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1(이상)</a> IDE(통합 개발 환경)에서 다운로드하여 사용할 수 있습니다. 먼저 <a href="https://aka.ms/csspeech/iosbinary" target="_blank">이진 CocoaPod를 다운로드</a>합니다. 사용하기 위해 동일한 디렉터리에서 Pod를 추출하고, *Podfile* 을 만들고, `pod`를 `target`으로 나열합니다.
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

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.iOS는 .NET 개발자를 위한 전체 iOS SDK를 제공합니다. Visual Studio C#을 사용하여 완전 네이티브 iOS 앱을 빌드합니다. 자세한 내용은 <a href="/xamarin/ios/" target="_blank">Xamarin.iOS</a>를 참조하세요.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;&nbsp;❤️         <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>추가 리소스

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS Speech SDK 빠른 시작 Objective-C 소스 코드</a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS Speech SDK 빠른 시작 Swift 소스 코드 </a>
