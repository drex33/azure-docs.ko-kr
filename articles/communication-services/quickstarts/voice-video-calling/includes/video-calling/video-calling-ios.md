---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: rifox
ms.openlocfilehash: c2d280de30997996902def6f04eb1d35c956999a
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966020"
---
Communication Services Calling SDK를 사용하여 앱에 1:1 영상 통화를 추가하여 Azure Communication Services를 시작하세요. iOS용 Azure Communication Services Calling SDK를 사용하여 영상 통화를 시작하고 응답하는 방법에 대해 알아봅니다.

## <a name="sample-code"></a>샘플 코드

끝으로 건너뛰려면 [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Video%20Calling)에서 이 빠른 시작을 샘플로 다운로드할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
- 활성 구독이 있는 Azure 계정을 얻습니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 키 집합에 설치된 유효한 개발자 인증서와 함께 [Xcode](https://developer.apple.com/xcode/)를 실행하는 Mac
- 활성 Communication Services 리소스를 만듭니다. [Communication Services 리소스를 만듭니다](../../../create-communication-resource.md?tabs=windows&pivots=platform-azp).
- 호출 클라이언트를 인스턴스화하기 위한 사용자 액세스 토큰을 만듭니다. [사용자 액세스 토큰 만들기 및 관리 방법에 대해 알아봅니다.](../../../access-tokens.md?pivots=programming-language-csharp)

## <a name="setting-up"></a>설치
### <a name="creating-the-xcode-project"></a>Xcode 프로젝트 만들기
Xcode에서 새 iOS 프로젝트를 만들고 단일 보기 앱 템플릿을 선택합니다. 이 자습서에서는 [SwiftUI 프레임워크](https://developer.apple.com/xcode/swiftui/)를 사용하므로 언어를 Swift로, 사용자 인터페이스는 SwiftUI로 설정해야 합니다. 이 빠른 시작 중에는 테스트를 만들지 않습니다. 테스트 포함을 선택 취소합니다.

:::image type="content" source="../../media/ios/xcode-new-ios-project.png" alt-text="Xcode 내에서 새 프로젝트 창을 보여 주는 스크린샷":::

### <a name="installing-cocoapods"></a>CocoaPods 설치
이 가이드를 사용하여 Mac에 [CocoaPods를 설치](https://guides.cocoapods.org/using/getting-started.html)합니다. 

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>CocoaPods를 사용하여 패키지 및 종속성 설치
1. 애플리케이션에 대한 Podfile을 만들려면 터미널을 열고 프로젝트 폴더로 이동한 후 pod init을 실행합니다.

2. 다음 코드를 Podfile에 추가하고 저장합니다.

```
platform :ios, '13.0'
use_frameworks!

target 'VideoCallingQuickstart' do
  pod 'AzureCommunicationCalling', '~> 1.0.0'
end
```

3. Pod 설치를 실행합니다.

4. Xcode로 .xcworkspace를 엽니다.


### <a name="request-access-to-the-microphone-and-camera"></a>마이크, 카메라에 대한 액세스 요청
디바이스의 마이크 및 카메라에 액세스하려면 앱의 정보 속성 목록을 `NSMicrophoneUsageDescription` 및 `NSCameraUsageDescription`으로 업데이트해야 합니다. 연결된 값을 시스템이 사용자의 액세스를 요청하는 데 사용하는 대화 상자에 포함될 문자열로 설정합니다.

프로젝트 트리의 `Info.plist` 항목을 마우스 오른쪽 단추로 클릭하고 다음 형식으로 열기> 소스 코드를 선택합니다. 최상위 `<dict>` 섹션에 다음 줄을 추가한 다음, 파일을 저장합니다.

```XML
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
<key>NSCameraUsageDescription</key>
<string>Need camera access for video calling</string>
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정
프로젝트의 `ContentView.swift` 파일을 열고 파일 맨 위에 가져오기 선언을 추가하여 `AzureCommunicationCalling` 라이브러리 및 `AVFoundation`을 가져옵니다. AvFoundation은 코드에서 오디오 권한을 캡처하는 데 사용됩니다.

```Swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>개체 모델
iOS용 Azure Communication Services Calling SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| Name                         | 설명                                                                                                                                                                        |
| :--------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                   | CallClient는 Calling SDK의 주 진입점입니다.                                                                                                                         |
| CallAgent                    | CallAgent는 통화를 시작하고 관리하는 데 사용됩니다.                                                                                                                                   |
| CommunicationTokenCredential | CommunicationTokenCredential은 CallAgent를 인스턴스화하기 위한 토큰 자격 증명으로 사용됩니다.                                                                                     |
| CommunicationIdentifier      | CommunicationIdentifier는 사용자의 ID를 나타내는 데 사용되며 CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication 중 하나일 수 있습니다. |

## <a name="create-the-call-agent"></a>통화 에이전트 만들기
ContentView 구문의 구현을 간단한 UI 컨트롤로 바꿉니다. 이를 통해 사용자가 통화를 시작하고 종료할 수 있습니다. 이 빠른 시작에서는 비즈니스 논리를 이러한 컨트롤에 연결합니다.

```Swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?
    @State var deviceManager: DeviceManager?
    @State var localVideoStream:[LocalVideoStream]?
    @State var incomingCall: IncomingCall?
    @State var sendingVideo:Bool = false
    @State var errorMessage:String = "Unknown"

    @State var remoteVideoStreamData:[Int32:RemoteVideoStreamData] = [:]
    @State var previewRenderer:VideoStreamRenderer? = nil
    @State var previewView:RendererView? = nil
    @State var remoteRenderer:VideoStreamRenderer? = nil
    @State var remoteViews:[RendererView] = []
    @State var remoteParticipant: RemoteParticipant?
    @State var remoteVideoSize:String = "Unknown"
    @State var isIncomingCall:Bool = false
    
    @State var callObserver:CallObserver?
    @State var remoteParticipantObserver:RemoteParticipantObserver?

    var body: some View {
        NavigationView {
            ZStack{
                Form {
                    Section {
                        TextField("Who would you like to call?", text: $callee)
                        Button(action: startCall) {
                            Text("Start Call")
                        }.disabled(callAgent == nil)
                        Button(action: endCall) {
                            Text("End Call")
                        }.disabled(call == nil)
                        Button(action: toggleLocalVideo) {
                            HStack {
                                Text(sendingVideo ? "Turn Off Video" : "Turn On Video")
                            }
                        }
                    }
                }
                // Show incoming call banner
                if (isIncomingCall) {
                    HStack() {
                        VStack {
                            Text("Incoming call")
                                .padding(10)
                                .frame(maxWidth: .infinity, alignment: .topLeading)
                        }
                        Button(action: answerIncomingCall) {
                            HStack {
                                Text("Answer")
                            }
                            .frame(width:80)
                            .padding(.vertical, 10)
                            .background(Color(.green))
                        }
                        Button(action: declineIncomingCall) {
                            HStack {
                                Text("Decline")
                            }
                            .frame(width:80)
                            .padding(.vertical, 10)
                            .background(Color(.red))
                        }
                    }
                    .frame(maxWidth: .infinity, alignment: .topLeading)
                    .padding(10)
                    .background(Color.gray)
                }
                ZStack{
                    VStack{
                        ForEach(remoteViews, id:\.self) { renderer in
                            ZStack{
                                VStack{
                                    RemoteVideoView(view: renderer)
                                        .frame(width: .infinity, height: .infinity)
                                        .background(Color(.lightGray))
                                }
                            }
                            Button(action: endCall) {
                                Text("End Call")
                            }.disabled(call == nil)
                            Button(action: toggleLocalVideo) {
                                HStack {
                                    Text(sendingVideo ? "Turn Off Video" : "Turn On Video")
                                }
                            }
                        }
                        
                    }.frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topLeading)
                    VStack{
                        if(sendingVideo)
                        {
                            VStack{
                                PreviewVideoStream(view: previewView!)
                                    .frame(width: 135, height: 240)
                                    .background(Color(.lightGray))
                            }
                        }
                    }.frame(maxWidth:.infinity, maxHeight:.infinity,alignment: .bottomTrailing)
                }
            }
     .navigationBarTitle("Video Calling Quickstart")
        }.onAppear{
            // Authenticate the client
            
            // Initialize the CallAgent and access Device Manager
            
            // Ask for permissions
        }
    }
}

//Functions and Observers

struct PreviewVideoStream: UIViewRepresentable {
    let view:RendererView
    func makeUIView(context: Context) -> UIView {
        return view
    }
    func updateUIView(_ uiView: UIView, context: Context) {}
}

struct RemoteVideoView: UIViewRepresentable {
    let view:RendererView
    func makeUIView(context: Context) -> UIView {
        return view
    }
    func updateUIView(_ uiView: UIView, context: Context) {}
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

### <a name="authenticate-the-client"></a>클라이언트 인증
CallAgent 인스턴스를 초기화하려면 통화를 주고받을 사용자 액세스 토큰이 필요합니다. 사용할 수 있는 토큰이 아직 없는 경우 [사용자 액세스 토큰](../../../access-tokens.md?pivots=programming-language-csharp) 설명서를 참조하세요.

토큰이 있으면 `ContentView.swift`의 `onAppear` 콜백에 다음 코드를 추가합니다. `<USER ACCESS TOKEN>`을 리소스의 유효한 사용자 액세스 토큰**으로 바꿔야 합니다.

```Swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}
```

### <a name="initialize-the-callagent-and-access-device-manager"></a>CallAgent 초기화 및 디바이스 관리자 액세스
CallClient에서 CallAgent 인스턴스를 만들려면 초기화된 후 비동기적으로 CallAgent 개체를 반환하는 `callClient.createCallAgent` 메서드를 사용합니다. DeviceManager를 사용하여 오디오/비디오 스트림을 전송하는 호출에 사용 가능한 로컬 디바이스를 열거할 수 있습니다. 또한 사용자에게 마이크/카메라 액세스 권한을 요청할 수 있습니다. 

```Swift
self.callClient = CallClient()
self.callClient?.createCallAgent(userCredential: userCredential!) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }

    else {
        self.callAgent = agent
        print("Call agent successfully created.")
        self.callAgent!.delegate = incomingCallHandler
        self.callClient?.getDeviceManager { (deviceManager, error) in
            if (error == nil) {
                print("Got device manager instance")
                self.deviceManager = deviceManager
            } else {
                print("Failed to get device manager instance")
            }
        }
    }
}
```

### <a name="ask-for-permissions"></a>권한 요청
`onAppear` 콜백에 다음 코드를 추가하여 오디오 및 비디오에 대한 권한을 요청해야 합니다. 

```Swift
AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
    if granted {
        AVCaptureDevice.requestAccess(for: .video) { (videoGranted) in
            /* NO OPERATION */
        }
    }
}
```

## <a name="display-local-video"></a>로컬 비디오 표시
통화를 시작하기 전에 비디오와 관련된 설정을 관리할 수 있습니다. 이 빠른 시작에서는 호출 전 또는 호출 중에 로컬 비디오를 전환하는 방법을 소개합니다. 

먼저 `deviceManager`를 사용하여 로컬 카메라에 액세스해야 합니다. 원하는 카메라가 선택되면 `LocalVideoStream`을 생성하고 `VideoStreamRenderer`를 만든 다음 `previewView`에 연결할 수 있습니다. 통화 중에 `startVideo` 또는 `stopVideo`를 사용하여 원격 참가자에 대한 `LocalVideoStream`을 시작하거나 중지할 수 있습니다. 이 기능은 수신 전화를 처리하는 경우에도 사용합니다. 

```Swift
func toggleLocalVideo() {
    // toggling video before call starts
    if (call == nil)
    {
        if(!sendingVideo)
        {
            self.callClient = CallClient()
            self.callClient?.getDeviceManager { (deviceManager, error) in
                if (error == nil) {
                    print("Got device manager instance")
                    self.deviceManager = deviceManager
                } else {
                    print("Failed to get device manager instance")
                }
            }
            guard let deviceManager = deviceManager else {
                return
            }
            let camera = deviceManager.cameras.first
            let scalingMode = ScalingMode.fit
            if (self.localVideoStream == nil) {
                self.localVideoStream = [LocalVideoStream]()
            }
            localVideoStream!.append(LocalVideoStream(camera: camera!))
            previewRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream!.first!)
            previewView = try! previewRenderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
            self.sendingVideo = true
        }
        else{
            self.sendingVideo = false
            self.previewView = nil
            self.previewRenderer!.dispose()
            self.previewRenderer = nil
        }
    }
    // toggle local video during the call
    else{
        if (sendingVideo) {
            call!.stopVideo(stream: localVideoStream!.first!) { (error) in
                if (error != nil) {
                    print("cannot stop video")
                }
                else {
                    self.sendingVideo = false
                    self.previewView = nil
                    self.previewRenderer!.dispose()
                    self.previewRenderer = nil
                }
            }
        }
        else {
            guard let deviceManager = deviceManager else {
                return
            }
            let camera = deviceManager.cameras.first
            let scalingMode = ScalingMode.fit
            if (self.localVideoStream == nil) {
                self.localVideoStream = [LocalVideoStream]()
            }
            localVideoStream!.append(LocalVideoStream(camera: camera!))
            previewRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream!.first!)
            previewView = try! previewRenderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
            call!.startVideo(stream:(localVideoStream?.first)!) { (error) in
                if (error != nil) {
                    print("cannot start video")
                }
                else {
                    self.sendingVideo = true
                }
            }
        }
    }
}
```

## <a name="place-an-outgoing-call"></a>발신 전화 걸기
`startCall` 메서드는 Start Call 단추를 누르면 수행되는 작업으로 설정됩니다. 이 빠른 시작에서 발신 전화는 기본적으로 오디오 전용입니다. 영상 통화를 시작하려면 `VideoOptions`를 `LocalVideoStream`으로 설정하고 `startCallOptions`로 전달하여 통화 초기 옵션을 설정해야 합니다.

```Swift
func startCall() {
        let startCallOptions = StartCallOptions()
        if(sendingVideo)
        {
            if (self.localVideoStream == nil) {
                self.localVideoStream = [LocalVideoStream]()
            }
            let videoOptions = VideoOptions(localVideoStreams: localVideoStream!)
            startCallOptions.videoOptions = videoOptions
        }
        let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(self.callee)]
        self.callAgent?.startCall(participants: callees, options: startCallOptions) { (call, error) in
            setCallAndObersever(call: call, error: error)
        }
    }
```

`CallObserver` 및 `RemotePariticipantObserver`는 통화 중 이벤트 및 원격 참가자를 관리하는 데 사용됩니다. `setCallAndOberserver` 함수에서 관찰자를 설정합니다. 

```Swift
func setCallAndObersever(call:Call!, error:Error?) {
    if (error == nil) {
        self.call = call
        self.callObserver = CallObserver(self)
        self.call!.delegate = self.callObserver
        self.remoteParticipantObserver = RemoteParticipantObserver(self)
    } else {
        print("Failed to get call object")
    }
}
```

## <a name="answer-an-incoming-call"></a>수신 전화 응답
수신 전화에 응답하려면 `IncomingCallHandler`를 구현하여 통화에 응답하거나 거절할 수 있는 수신 전화 배너를 표시합니다. `IncomingCallHandler.swift`의 다음 구현에 붙여 넣습니다. 

```Swift
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate {
    public var contentView: ContentView?
    private var incomingCall: IncomingCall?

    private static var instance: IncomingCallHandler?
    static func getOrCreateInstance() -> IncomingCallHandler {
        if let c = instance {
            return c
        }
        instance = IncomingCallHandler()
        return instance!
    }

    private override init() {}
    
    public func callAgent(_ callAgent: CallAgent, didRecieveIncomingCall incomingCall: IncomingCall) {
        self.incomingCall = incomingCall
        self.incomingCall?.delegate = self
        contentView?.showIncomingCallBanner(self.incomingCall!)
    }
    
    public func callAgent(_ callAgent: CallAgent, didUpdateCalls args: CallsUpdatedEventArgs) {
        if let removedCall = args.removedCalls.first {
            contentView?.callRemoved(removedCall)
            self.incomingCall = nil
        }
    }
}
```

다음 코드를 `ContentView.swift`의 `onAppear` 콜백에 추가하여 `IncomingCallHandler`의 인스턴스를 만들어야 합니다.

```Swift
let incomingCallHandler = IncomingCallHandler.getOrCreateInstance()
incomingCallHandler.contentView = self
```

CallAgent가 성공적으로 생성된 후 CallAgent에 대리자를 설정합니다.

```Swift
self.callAgent!.delegate = incomingCallHandler
```

수신 전화가 발생하면 `IncomingCallHandler`는 `showIncomingCallBanner` 기능을 호출하여 `answer` 및 `decline` 단추를 표시합니다. 

```Swift
func showIncomingCallBanner(_ incomingCall: IncomingCall?) {
    isIncomingCall = true
    self.incomingCall = incomingCall
}
```

`answer` 및 `decline`에 연결된 작업은 아래와 같이 구현됩니다. 영상 통화에 응답하기 위해서는 로컬 비디오를 켜고 `AcceptCallOptions` 옵션을 `localVideoStream`으로 설정해야 합니다. 

```Swift
func answerIncomingCall() {
    isIncomingCall = false
    let options = AcceptCallOptions()
    if (self.incomingCall != nil) {
        guard let deviceManager = deviceManager else {
            return
        }        
        if (self.localVideoStream == nil) {
            self.localVideoStream = [LocalVideoStream]()
        }
        if(sendingVideo)
        {
            let camera = deviceManager.cameras.first
            localVideoStream!.append(LocalVideoStream(camera: camera!))
            let videoOptions = VideoOptions(localVideoStreams: localVideoStream!)
            options.videoOptions = videoOptions
        }
        self.incomingCall!.accept(options: options) { (call, error) in
            setCallAndObersever(call: call, error: error)
        }
    }
}

func declineIncomingCall(){
    self.incomingCall!.reject { (error) in }
    isIncomingCall = false
}
```

## <a name="remote-participant-video-streams"></a>원격 참가자 비디오 스트림
원격 참가자의 렌더링 비디오 스트림을 처리하는 `RemoteVideoStreamData` 클래스를 만들 수 있습니다. 

```Swift
public class RemoteVideoStreamData : NSObject, RendererDelegate {
    public func videoStreamRenderer(didFailToStart renderer: VideoStreamRenderer) {
        owner.errorMessage = "Renderer failed to start"
    }
    
    private var owner:ContentView
    let stream:RemoteVideoStream
    var renderer:VideoStreamRenderer? {
        didSet {
            if renderer != nil {
                renderer!.delegate = self
            }
        }
    }
    
    var views:[RendererView] = []
    init(view:ContentView, stream:RemoteVideoStream) {
        owner = view
        self.stream = stream
    }
    
    public func videoStreamRenderer(didRenderFirstFrame renderer: VideoStreamRenderer) {
        let size:StreamSize = renderer.size
        owner.remoteVideoSize = String(size.width) + " X " + String(size.height)
    }
}
```

## <a name="subscribe-to-events"></a>이벤트 구독
`CallObserver` 클래스를 구현하여 통화 중에 값이 변경될 때 알림을 받을 이벤트 모음을 구독할 수 있습니다. 

```Swift
public class CallObserver: NSObject, CallDelegate, IncomingCallDelegate {
    private var owner: ContentView
    init(_ view:ContentView) {
            owner = view
    }
        
    public func call(_ call: Call, didChangeState args: PropertyChangedEventArgs) {
        if(call.state == CallState.connected) {
            initialCallParticipant()
        }
    }

    // render remote video streams when remote participant changes
    public func call(_ call: Call, didUpdateRemoteParticipant args: ParticipantsUpdatedEventArgs) {
        for participant in args.addedParticipants {
            participant.delegate = owner.remoteParticipantObserver
            for stream in participant.videoStreams {
                if !owner.remoteVideoStreamData.isEmpty {
                    return
                }
                let data:RemoteVideoStreamData = RemoteVideoStreamData(view: owner, stream: stream)
                let scalingMode = ScalingMode.fit
                data.renderer = try! VideoStreamRenderer(remoteVideoStream: stream)
                let view:RendererView = try! data.renderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
                data.views.append(view)
                self.owner.remoteViews.append(view)
                owner.remoteVideoStreamData[stream.id] = data
            }
            owner.remoteParticipant = participant
        }
    }

    // Handle remote video streams when the call is connected
    public func initialCallParticipant() {
        for participant in owner.call!.remoteParticipants {
            participant.delegate = owner.remoteParticipantObserver
            for stream in participant.videoStreams {
                renderRemoteStream(stream)
            }
            owner.remoteParticipant = participant
        }
    }
    
    //create render for RemoteVideoStream and attach it to view
    public func renderRemoteStream(_ stream: RemoteVideoStream!) {
        if !owner.remoteVideoStreamData.isEmpty {
            return
        }
        let data:RemoteVideoStreamData = RemoteVideoStreamData(view: owner, stream: stream)
        let scalingMode = ScalingMode.fit
        data.renderer = try! VideoStreamRenderer(remoteVideoStream: stream)
        let view:RendererView = try! data.renderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
        self.owner.remoteViews.append(view)
        owner.remoteVideoStreamData[stream.id] = data
    }
}
```

## <a name="remote-participant-management"></a>원격 참가자 관리
모든 원격 참가자는 `RemoteParticipant` 형식으로 표시되며 호출 인스턴스에서 `remoteParticipants` 컬렉션을 통해 사용할 수 있습니다. `RemoteParticipantObserver` 클래스를 구현하여 원격 참가자의 원격 비디오 스트림에 대한 업데이트를 구독할 수 있습니다. 

```Swift
public class RemoteParticipantObserver : NSObject, RemoteParticipantDelegate {
    private var owner:ContentView
    init(_ view:ContentView) {
        owner = view
    }

    public func renderRemoteStream(_ stream: RemoteVideoStream!) {
        let data:RemoteVideoStreamData = RemoteVideoStreamData(view: owner, stream: stream)
        let scalingMode = ScalingMode.fit
        data.renderer = try! VideoStreamRenderer(remoteVideoStream: stream)
        let view:RendererView = try! data.renderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
        self.owner.remoteViews.append(view)
        owner.remoteVideoStreamData[stream.id] = data
    }
    
    // render RemoteVideoStream when remote participant turns on the video, dispose the renderer when remote video is off
    public func remoteParticipant(_ remoteParticipant: RemoteParticipant, didUpdateVideoStreams args: RemoteVideoStreamsEventArgs) {
        for stream in args.addedRemoteVideoStreams {
            renderRemoteStream(stream)
        }
        for stream in args.removedRemoteVideoStreams {
            for data in owner.remoteVideoStreamData.values {
                data.renderer?.dispose()
            }
            owner.remoteViews.removeAll()
        }
    }
}
```

## <a name="run-the-code"></a>코드 실행
제품 > 실행을 선택하거나 (⌘-R) 키보드 단축키를 사용하여 iOS 시뮬레이터에서 앱을 빌드하고 실행할 수 있습니다.
