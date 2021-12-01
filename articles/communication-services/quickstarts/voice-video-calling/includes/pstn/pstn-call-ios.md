---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: nikuklic
ms.openlocfilehash: 2cf9fa4b5e6deecc72a53f795a71dd69d10b42dc
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133406004"
---
[!INCLUDE [Emergency Calling Notice](../../../../includes/emergency-calling-notice-include.md)]

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 배포된 Communication Services 리소스. [Communication Services 리소스를 만듭니다](../../../create-communication-resource.md).
- Communication Services 리소스에서 가져온 전화 번호입니다. [전화 번호를 가져오는 방법](../../../telephony/get-phone-number.md).
- 호출 클라이언트를 사용하도록 설정하는 `User Access Token`입니다. [`User Access Token`를 가져오는 방법](../../../access-tokens.md)에 대한 자세한 정보
- [애플리케이션에 통화 추가 시작](../../getting-started-with-calling.md)에 대한 빠른 시작을 완료합니다.

### <a name="prerequisite-check"></a>필수 구성 요소 확인

- Communication Services 리소스와 연결된 전화 번호를 보려면 [Azure Portal](https://portal.azure.com/)에 로그인하고 Communication Services 리소스를 찾아 왼쪽 탐색 창에서 **전화 번호** 탭을 엽니다.
- iOS용 Azure Communication Services Calling SDK를 사용하여 앱을 빌드하고 실행할 수 있습니다.

## <a name="setting-up"></a>설치

## <a name="start-a-call-to-phone"></a>휴대폰에 전화 걸기 시작

통화를 시작하는 데 사용할 Communication Services 리소스에서 획득한 전화 번호를 지정합니다.
> [!WARNING]
> 전화 번호는 E.164 국제 표준 형식으로 제공되어야 합니다. (예: +12223334444)

*통화 시작* 단추를 누를 때 수행되는 `startCall` 이벤트 처리기를 수정합니다.

```swift
func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                let startCallOptions = StartCallOptions()
                startCallOptions.alternateCallerId = PhoneNumberIdentifier(phoneNumber: "<YOUR AZURE REGISTERED PHONE NUMBER>")
                self.callAgent!.startCall(participants: [PhoneNumberIdentifier(phoneNumber: self.callee)], options: startCallOptions) { (call, error) in
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

## <a name="run-the-code"></a>코드 실행

**제품** > **실행** 을 선택하거나 (&#8984;-R) 키보드 단축키를 사용하여 iOS 시뮬레이터에서 앱을 빌드하고 실행할 수 있습니다.

![빠른 시작 앱의 최종 모양과 느낌](../../media/ios/quick-start-make-call.png)

추가된 텍스트 필드에 전화 번호를 제공하고 **통화 시작** 단추를 클릭하여 휴대폰에 전화를 걸 수 있습니다.
> [!WARNING]
> 전화 번호는 E.164 국제 표준 형식으로 제공되어야 합니다. (예: +12223334444)

> [!NOTE]
> 처음으로 전화를 걸면 마이크에 대한 액세스를 묻는 메시지가 표시됩니다. 프로덕션 애플리케이션에서는 `AVAudioSession` API를 사용하여 [권한 상태를 확인](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources)하고 권한이 부여되지 않은 경우 애플리케이션의 동작을 정상적으로 업데이트해야 합니다.
