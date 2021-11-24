---
title: 빠른 시작 - Azure Communication Services를 사용하여 iOS 앱에 통화 추가
description: 이 빠른 시작에서는 iOS용 Azure Communication Services Calling SDK를 사용하는 방법을 알아봅니다.
author: chpalm
ms.author: rifox
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: mode-other
ms.openlocfilehash: 9462fde91169bca858444af1307c95e111fafe41
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133039294"
---
이 빠른 시작에서는 iOS용 Azure Communication Services Calling SDK를 사용하여 통화를 시작하는 방법에 대해 알아봅니다.

## <a name="sample-code"></a>샘플 코드

샘플 앱은 [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling)에서 다운로드할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 키 집합에 설치된 유효한 개발자 인증서와 함께 [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)를 실행하는 Mac
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../../create-communication-resource.md).
- [사용자 액세스 토큰](../../../access-tokens.md)(Azure Communication Service용)

## <a name="setting-up"></a>설치

### <a name="creating-the-xcode-project"></a>Xcode 프로젝트 만들기

Xcode에서 새 iOS 프로젝트를 만들고 **단일 보기 앱** 템플릿을 선택합니다. 이 자습서에서는 [SwiftUI 프레임워크](https://developer.apple.com/xcode/swiftui/)를 사용하므로 **언어** 를 **Swift** 로, **사용자 인터페이스** 는 **SwiftUI** 로 설정해야 합니다. 이 빠른 시작 중에는 테스트를 만들지 않습니다. **테스트 포함** 을 선택 취소합니다.

:::image type="content" source="../../media/ios/xcode-new-ios-project.png" alt-text="Xcode 내에서 새 프로젝트 창을 보여 주는 스크린샷":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>CocoaPods를 사용하여 패키지 및 종속성 설치

1. 애플리케이션에 대한 Podfile을 만들려면 터미널을 열고 프로젝트 폴더로 이동하여 다음을 실행합니다.

   `pod init`

1. Podfile에 다음 코드를 추가하고 저장합니다. "target"이 프로젝트 이름과 일치하는지 확인합니다.

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0'
   end
   ```

1. `pod install`을 실행합니다.

1. Xcode로 `.xcworkspace`를 엽니다.

### <a name="request-access-to-the-microphone"></a>마이크에 대한 액세스 요청

디바이스의 마이크에 액세스하려면 앱의 정보 속성 목록을 `NSMicrophoneUsageDescription`으로 업데이트해야 합니다. 연결된 값을 시스템이 사용자의 액세스를 요청하는 데 사용하는 대화 상자에 포함될 `string`으로 설정합니다.

프로젝트 트리의 `Info.plist` 항목을 마우스 오른쪽 단추로 클릭하고 **다음 형식으로 열기** > **소스 코드** 를 선택합니다. 최상위 `<dict>` 섹션에 다음 줄을 추가한 다음, 파일을 저장합니다.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트의 **ContentView.swift** 파일을 열고 파일 맨 위에 `import` 선언을 추가하여 `AzureCommunicationCalling library`를 가져옵니다. 또한 `AVFoundation`을 가져옵니다. 코드에서 오디오 권한 요청에 필요합니다.

```swift
import AzureCommunicationCalling
import AVFoundation
```

`ContentView` 구문의 구현을 간단한 UI 컨트롤로 바꿉니다. 이를 통해 사용자가 통화를 시작하고 종료할 수 있습니다. 이 빠른 시작에서는 비즈니스 논리를 이러한 컨트롤에 연결합니다.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>개체 모델

Azure Communication Services Calling SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| 이름                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient는 Calling SDK의 주 진입점입니다.|
| CallAgent | CallAgent는 통화를 시작하고 관리하는 데 사용됩니다. |
| CommunicationTokenCredential | CommunicationTokenCredential은 CallAgent를 인스턴스화하기 위한 토큰 자격 증명으로 사용됩니다.| 
| CommunicationUserIdentifier | CommunicationUserIdentifier는 사용자의 ID를 나타내는 데 사용되며 다음 중 하나일 수 있습니다. CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>클라이언트 인증

사용자 액세스 토큰으로 `CallAgent` 인스턴스를 초기화하면 전화를 걸고 받을 수 있습니다. 

다음 코드에서 `<USER ACCESS TOKEN>`을 리소스의 유효한 사용자 액세스 토큰으로 바꿔야 합니다. 사용할 수 있는 토큰이 아직 없는 경우 [사용자 액세스 토큰](../../../access-tokens.md) 설명서를 참조하세요.

**ContentView.swift** 의 `onAppear` 콜백에 다음 코드를 추가합니다.

```swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = CallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential: userCredential!) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }
    else {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```



## <a name="start-a-call"></a>통화 시작

`startCall` 메서드는 *Start Call* 단추를 누르면 수행되는 작업으로 설정됩니다. `ASACallAgent`를 사용하여 통화를 시작하도록 구현을 업데이트합니다.

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(self.callee)]
            self.callAgent?.startCall(participants: callees, options: StartCallOptions()) { (call, error) in
                if (error == nil) {
                    self.call = call
                } else {
                    print("Failed to get call object")
                }
            }
        }
    }
}
```

또한 `StartCallOptions`의 속성을 사용하여 통화에 대한 초기 옵션을 설정할 수 있습니다(예: 마이크 음소거 상태에서 통화를 시작할 수 있음).

## <a name="end-a-call"></a>통화 종료

*End Call* 단추를 누르면 현재 통화를 종료하도록 `endCall` 메서드를 구현합니다.

```swift
func endCall()
{    
    self.call!.hangUp(options: HangUpOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>코드 실행

**제품** > **실행** 을 선택하거나 (&#8984;-R) 키보드 단축키를 사용하여 iOS 시뮬레이터에서 앱을 빌드하고 실행할 수 있습니다.

:::image type="content" source="../../media/ios/quick-start-make-call.png" alt-text="빠른 시작 앱의 최종 모양과 느낌":::

텍스트 필드에서 사용자 ID를 제공하고 **Start Call** 단추를 눌러서 아웃바운드 VOIP 전화를 걸 수 있습니다. `8:echo123`에 전화를 걸면 에코 봇과 연결됩니다. 이렇게 하면 통화를 시작하고 오디오 디바이스가 작동하는지 확인하는 데 유용합니다. 

> [!NOTE]
> 처음으로 전화를 걸면 마이크에 대한 액세스를 묻는 메시지가 표시됩니다. 프로덕션 애플리케이션에서는 `AVAudioSession` API를 사용하여 [권한 상태를 확인](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources)하고 권한이 부여되지 않은 경우 애플리케이션의 동작을 정상적으로 업데이트해야 합니다.
