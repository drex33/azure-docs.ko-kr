---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 0ca40e760e34be8ef4299e042949f7a36b54f36f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838735"
---
## <a name="set-up-your-system"></a>시스템 설정

### <a name="create-the-xcode-project"></a>Xcode 프로젝트 만들기

Xcode에서 새 iOS 프로젝트를 만들고 **단일 보기 앱** 템플릿을 선택합니다. 이 빠른 시작에서는 [SwiftUI 프레임워크](https://developer.apple.com/xcode/swiftui/)를 사용하므로 **언어** 를 **Swift** 로 설정하고 **사용자 인터페이스** 를 **SwiftUI** 로 설정해야 합니다. 

이 빠른 시작 중에는 단위 테스트 또는 UI 테스트를 만들지 않습니다. **단위 테스트 포함** 및 **UI 테스트 포함** 텍스트 상자 선택을 취소할 수 있습니다.

:::image type="content" source="../../../../quickstarts/voice-video-calling/media/ios/xcode-new-ios-project.png" alt-text="Xcode 내에서 프로젝트를 만드는 창을 보여주는 스크린샷.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>CocoaPods를 사용하여 패키지 및 종속성 설치

1. 다음과 같이 애플리케이션에 대한 Podfile을 만듭니다.

    ```
    platform :ios, '13.0'
    use_frameworks!
    target 'AzureCommunicationCallingSample' do
        pod 'AzureCommunicationCalling', '~> 1.0.0'
    end
    ```
2. `pod install`을 실행합니다.
3. Xcode에서 `.xcworkspace`를 엽니다.

### <a name="request-access-to-the-microphone"></a>마이크에 대한 액세스 요청

디바이스의 마이크에 액세스하려면 앱의 정보 속성 목록을 `NSMicrophoneUsageDescription`으로 업데이트해야 합니다. 연결된 값을 시스템이 사용자의 액세스를 요청하는 데 사용하는 대화 상자에 포함될 `string`으로 설정합니다.

프로젝트 트리의 `Info.plist` 항목을 마우스 오른쪽 단추로 클릭하고 **다음 형식으로 열기** > **소스 코드** 를 선택합니다. 최상위 `<dict>` 섹션에 다음 줄을 추가한 다음, 파일을 저장합니다.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트의 *ContentView.swift* 파일을 열고 파일 맨 위에 `import` 선언을 추가하여 `AzureCommunicationCalling` 라이브러리를 가져옵니다. 추가로 `AVFoundation`을 가져옵니다. 코드에서 오디오 권한 요청에 필요합니다.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="initialize-callagent"></a>CallAgent 초기화

`CallAgent`에서 `CallClient` 인스턴스를 만들려면 초기화된 후 비동기적으로 `callClient.createCallAgent` 개체를 반환하는 `CallAgent` 메서드를 사용해야 합니다.

통화 클라이언트를 만들려면 `CommunicationTokenCredential` 개체를 전달해야 합니다.

```swift
import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
do {
    let options = CommunicationTokenRefreshOptions(initialToken: token, refreshProactively: true, tokenRefresher: self.fetchTokenSync)
    userCredential = try CommunicationTokenCredential(withOptions: options)
} catch {
    updates("Couldn't created Credential object", false)
    initializationDispatchGroup!.leave()
    return
}

// tokenProvider needs to be implemented by Contoso, which fetches a new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

만든 `CommunicationTokenCredential` 개체를 `CallClient`에 전달하고 표시 이름을 설정합니다.

```swift
self.callClient = CallClient()
let callAgentOptions = CallAgentOptions()
options.displayName = " iOS ACS User"

self.callClient!.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})
```