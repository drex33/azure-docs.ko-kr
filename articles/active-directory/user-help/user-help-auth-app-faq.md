---
title: Microsoft Authenticator 앱에 대한 질문과 대답 - Azure AD
description: Microsoft Authenticator 앱 및 2단계 인증에 대한 FAQ(질문과 대답)입니다.
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 06/21/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: f593141d425573888458b4b00858b17888c28e52
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537242"
---
# <a name="frequently-asked-questions-faq-about-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에 대한 FAQ(자주 묻는 질문)

이 문서에서는 Microsoft Authenticator 앱에 대한 일반적인 질문에 대답합니다. 질문에 대한 답변을 찾을 수 없는 경우 [Microsoft Authenticator 앱 포럼](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)으로 이동하세요.

Microsoft Authenticator 앱은 Azure Authenticator 앱을 대신하며, Azure AD Multi-Factor Authentication을 사용하는 경우 권장됩니다. [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) 및 [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458) 디바이스의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="permission-to-access-your-location"></a>위치 액세스 권한

**Q**: 앱에서 내 위치에 액세스할 수 있는 권한을 부여하라는 메시지가 표시됩니다. 왜 이것이 표시되나요?

**A**: IT 관리자가 특정 리소스에 액세스하기 전에 GPS 위치를 공유하도록 요구하는 정책을 만든 경우 Microsoft Authenticator에서 사용자 위치에 대한 액세스를 요청하는 메시지가 표시됩니다. 리소스에 액세스할 수 있는 국가 내에 있는지 확인하려면 매시간 한 번씩 위치를 공유해야 합니다.

iOS에서는 앱이 항상 위치에 액세스할 수 있도록 허용하는 것이 좋습니다. iOS 프롬프트에 따라 해당 사용 권한을 부여합니다. 각 권한 수준이 의미하는 바는 다음과 같습니다.

- **앱을 사용하는 동안 허용**: 이 옵션을 선택하면 두 가지 옵션을 더 선택하라는 메시지가 표시됩니다.
- **항상 허용(권장)** : 앞으로 24시간 동안 보호된 리소스에 계속 액세스하는 동안 디바이스에서 한 시간에 한 번씩 위치가 자동으로 공유되므로 휴대폰을 꺼내서 매시간 수동으로 승인할 필요가 없습니다.
- **사용 동안만 유지**: 보호된 리소스에 계속 액세스하는 동안 매시간 디바이스를 꺼내 요청을 수동으로 승인해야 합니다.
- **한 번 허용**: 리소스에 계속 액세스하는 동안 1시간마다 또는 다음 번에 리소스에 액세스하려고 할 때 권한을 다시 부여해야 합니다. 설정으로 이동하여 권한을 수동으로 활성화해야 합니다. 
- **허용 안 함**: 이 옵션을 선택하면 리소스 액세스가 차단됩니다. 생각이 바뀌면 설정으로 이동하여 권한을 수동으로 활성화해야 합니다.

Android에서는 앱이 항상 위치에 액세스할 수 있도록 허용하는 것이 좋습니다. Android 프롬프트에 따라 해당 사용 권한을 부여합니다. 각 권한 수준이 의미하는 바는 다음과 같습니다.

- **항상 허용(권장)** : 앞으로 24시간 동안 보호된 리소스에 계속 액세스하는 동안 디바이스에서 한 시간에 한 번씩 위치가 자동으로 공유되므로 휴대폰을 꺼내서 매시간 수동으로 승인할 필요가 없습니다.
- **앱을 사용하는 동안에만 허용**: 보호된 리소스에 계속 액세스하는 동안 매시간 디바이스를 꺼내 요청을 수동으로 승인해야 합니다.
- **거부하고 다시 묻지 않음**: 이 옵션을 선택하면 리소스 액세스가 차단됩니다.

**Q**: 내 위치 정보는 어떻게 사용되고 저장되나요?

**A**: Authenticator 앱은 GPS 정보를 수집하여 사용자가 거주하는 국가를 확인합니다. 보호된 리소스에 액세스할 수 있는지 확인하기 위해 국가 이름 및 위치 좌표가 시스템으로 다시 전송됩니다. 국가 이름이 저장되고 IT 관리자에게 다시 보고되지만 실제 좌표는 Microsoft 서버에 저장되지 않습니다.

### <a name="notification-blocks-sign-in"></a>알림 블록 로그인

**Q**: 로그인하려면 로그인 화면에 표시되는 내 앱의 번호를 선택해야 합니다. 그러나 Authenticator의 알림 프롬프트가 로그인 화면을 차단하고 있습니다. 어떻게 해야 합니까?  

**A**: 알림에서 "숨기기" 옵션을 선택하면 로그인 화면과 선택해야 하는 번호를 볼 수 있습니다. 5초 후에 프롬프트가 다시 나타나고 올바른 번호를 선택할 수 있습니다.

### <a name="registering-a-device"></a>디바이스 등록

**Q**: 디바이스를 등록하면 회사 또는 서비스에서 디바이스에 액세스하도록 허용하게 되나요?

**A**: 디바이스를 등록하면 디바이스에서 조직의 서비스에 액세스할 수 있으며 조직에서는 디바이스에 액세스할 수 없습니다.

### <a name="error-adding-account"></a>계정 추가 오류

**Q**: 내 계정을 추가하려고 하면 “추가하려는 계정이 현재 유효하지 않습니다. 이 문제를 해결하려면 관리자에게 문의하세요(고유성 유효성 검사).”라는 오류 메시지가 표시됩니다.   어떻게 해야 합니까?

**A**: 관리자에게 연락하여 고유성 유효성 검사 문제로 인해 Authenticator에 계정을 추가할 수 없음을 알립니다. 관리자가 조직에서 사용자를 찾을 수 있도록 로그인 사용자 이름을 제공해야 합니다.

### <a name="legacy-apns-support-deprecated"></a>레거시 APNs이 지원되지 않음

**Q**: Apple Push Notification Service에 대한 레거시 이진 인터페이스가 2020년 11월부터 더 이상 지원되지 않는데 Microsoft Authenticator/Phone Factor를 계속 사용하여 로그인하려면 어떻게 해야 하나요?

**A**: [Apple](https://developer.apple.com/news/?id=11042019a)은 Phone Factor에 사용되는 것과 같이 iOS 디바이스용 이진 인터페이스를 사용하는 푸시 알림의 지원 중단을 발표했습니다. 푸시 알림을 계속 받으려면 사용자가 Authenticator 앱을 최신 버전의 앱으로 업데이트하는 것이 좋습니다. 그동안 Authenticator 앱에서 알림을 수동으로 확인하여 문제를 해결할 수 있습니다.

### <a name="app-lock-feature"></a>앱 잠금 기능

**Q**: 앱 잠금이란 무엇이며 보안 강화를 위해 어떻게 사용할 수 있나요?

**A**: 앱 잠금은 일회성 인증 코드, 앱 정보, 앱 설정을 더 안전하게 유지하는 데 도움이 됩니다. 앱 잠금을 사용하면 Authenticator를 열 때마다 디바이스 PIN이나 생체 인식 정보를 사용하여 인증을 하라는 메시지가 표시됩니다. 앱 잠금은 로그인 알림을 승인할 때마다 PIN 또는 생체 인식을 입력하라는 메시지를 표시하여 해당 사용자가 알림을 승인할 수 있는 유일한 사용자인지 확인하는 데도 도움이 됩니다. Authenticator 설정 페이지에서 앱 잠금을 설정하거나 해제할 수 있습니다. 기본적으로 디바이스에서 PIN 또는 생체 인식을 설정하면 앱 잠금이 켜집니다.<br><br>안타깝지만 앱 잠금을 사용한다고 해서 누군가가 Authenticator에 액세스하지 못하게 할 수 있는 것은 아닙니다. 디바이스 등록은 Android 계정 설정이나 회사 포털 앱 등 Authenticator 외부의 다른 위치에서 발생할 수 있기 때문입니다.

### <a name="windows-mobile-retired"></a>Windows Mobile 사용 중지

**Q**: Windows Mobile 디바이스를 사용하고 있으며 Windows Mobile에서 Microsoft Authenticator가 지원 중단됐습니다. 앱을 사용하여 계속 인증할 수 있나요?

**A**: Windows Mobile에서 Microsoft Authenticator를 사용하는 모든 인증은 2020년 7월 15일 이후 사용이 중지됩니다. 계정에서 잠기지 않도록 대체 인증 방법을 사용하는 것이 좋습니다.<br>엔터프라이즈 사용자를 위한 대체 옵션은 다음과 같습니다.<br><ul><li>[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 또는 [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)용 Microsoft Authenticator를 설정합니다.</li><li>확인 코드를 수신하도록 [SMS를 설정](multi-factor-authentication-setup-phone-number.md)합니다.</li><li>[신원 확인을 위한 전화](multi-factor-authentication-setup-office-phone.md)를 받을 전화번호를 설정합니다.</li></ul><br>개인 Microsoft 계정 사용자를 위한 대체 옵션은 다음과 같습니다.<br><ul><li>[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 또는 [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)용 Microsoft Authenticator를 설정합니다.</li><li>[Microsoft 계정 보안 페이지](https://account.microsoft.com/security/)에서 보안 정보를 업데이트하여 대체 로그인 방법(SMS 또는 메일)을 설정합니다.</li></ul>

### <a name="android-screenshots"></a>Android 스크린샷

**Q**: Android Authenticator에서 OTP(일회성 암호) 코드 스크린샷을 찍을 수 있나요?

**A**: Authenticator Android 릴리스 6.2003.1704부터 Authenticator의 스크린샷을 찍을 때마다 기본적으로 모든 OTP 코드가 숨겨집니다. 스크린샷에서 OTP 코드가 보이도록 하거나 다른 앱이 Authenticator 화면을 캡처하도록 허용할 수 있습니다. Authenticator에서 **화면 캡처** 설정을 켜고 앱을 다시 시작하면 됩니다.

### <a name="delete-stored-data"></a>저장된 데이터 삭제

**Q**: Authenticator는 사용자를 대신하여 어떤 데이터를 저장하며, 해당 데이터는 어떻게 삭제할 수 있나요?

**A**: Authenticator 앱은 다음 세 가지 유형의 정보를 수집합니다.

- 사용자 계정을 추가할 때 제공하는 계정 정보 계정을 추가한 후 계정에 대해 사용하도록 설정한 기능에 따라 계정 데이터가 앱에 동기화될 수 있습니다. 계정을 제거하여 이 데이터를 제거할 수 있습니다.
- 앱의 상단 메뉴에서 **피드백 보내기** 를 통해 Microsoft에 로그를 보낼 때까지만 앱에 남아 있는 진단 로그 데이터. 해당 로그에는 메일 주소, 서버 주소, IP 주소 등의 개인 데이터가 포함될 수 있습니다. 또한 디바이스 이름 및 운영 체제 버전과 같은 디바이스 데이터를 포함할 수 있습니다. 수집하는 모든 개인 데이터는 앱 문제를 해결하는 데 필요한 정보로 제한됩니다. 수집된 정보를 확인하려면 언제든지 앱에서 해당 로그 파일을 찾아볼 수 있습니다. 로그 파일을 보내는 경우 인증 앱 엔지니어는 고객이 보고한 문제를 해결하는 데만 해당 파일을 사용합니다.
- "계정 흐름 추가 시작됨/성공적으로 계정을 추가함" 또는 "알림 승인됨"과 같은 비-개인 식별이 가능한 사용량 데이터. 해당 데이터는 엔지니어링 결정의 필수적인 부분입니다. 사용량에 따라 사용자에게 중요한 방식으로 앱을 개선할 수 있는 부분을 결정하는 데 도움이 됩니다. 앱을 처음 사용하는 경우 해당 데이터 수집에 대한 알림이 표시됩니다. 앱의  **설정** 페이지에서 해당 기능을 해제할 수 있음을 알려줍니다. 언제든지 해당 설정을 켜거나 끌 수 있습니다.

### <a name="codes-in-the-app"></a>앱 내 코드

**Q**: 앱에서 코드란 무엇인가요?

**A**: Authenticator를 열면 추가한 계정이 타일로 표시됩니다. 회사 또는 학교 계정 및 개인 Microsoft 계정의 전체 화면 보기에 6자리 또는 8자리 숫자가 표시됩니다(계정 타일을 눌러 액세스). 다른 계정의 경우 앱의 **계정** 페이지에 6자리 또는 8자리 숫자가 표시됩니다.<br>해당 코드는 일회용 암호로 사용되어 사용자 신원을 확인합니다. 사용자 이름 및 암호를 사용하여 로그인한 후 해당 계정과 연결된 확인 코드를 입력합니다. 예를 들어 Katy 님이 Contoso 계정에 로그인하는 경우 계정 타일을 탭한 다음 확인 코드 895823을 사용합니다. Outlook 계정의 경우 동일한 단계를 따릅니다.<br>Contoso 계정 타일을 탭합니다.<br>![Authenticator 앱의 계정 타일](media/user-help-auth-app-faq/katy-signin.png)<br>Contoso 계정 타일을 탭하면 확인 코드가 전체 화면에 표시됩니다.<br>![Authenticator의 계정 타일에 있는 확인 코드](media/user-help-auth-app-faq/verification-code.png)

### <a name="countdown-timer"></a>카운트다운 타이머

**Q**: 코드 옆에 있는 숫자가 계속 카운트다운되는 이유는 무엇인가요?

**A**: 활성 확인 코드 옆에 30초 타이머가 카운트다운되는 것을 볼 수 있습니다. 이 타이머는 같은 코드를 두 번 사용하여 로그인하는 일이 없도록 방지하기 위한 것입니다. 암호와는 달리, 이 숫자를 기억할 필요가 없습니다. 휴대폰에 액세스할 수 있는 사람만이 확인 코드를 알 수 있다는 점에 착안하였습니다.

### <a name="grayed-account-tile"></a>회색 표시된 계정 타일

**Q**: 계정 타일이 회색으로 표시되는 이유는 무엇인가요?

**A**: 일부 조직에서는 Authenticator를 Single Sign-On과 함께 사용하여 조직의 리소스를 보호할 것을 요구합니다. 이 경우 계정이 2단계 인증에 사용되지 않고 회색 또는 비활성 상태로 표시됩니다. 이러한 종류의 계정을 일반적으로 "브로커" 계정이라고 부릅니다.

### <a name="device-registration"></a>디바이스 등록

**Q**: 디바이스 등록이란?

**A**: 조직에서 파일 및 앱과 같은 보안 리소스에 대한 액세스를 추적하기 위해 디바이스를 등록하도록 요구할 수 있습니다. 또한 조건부 액세스를 설정하여 해당 리소스가 원치 않는 방식으로 액세스될 위험을 줄일 수 있습니다. **설정** 에서 디바이스 등록을 취소할 수 있지만, Outlook의 이메일과 OneDrive의 파일에 액세스할 수 없게 될 수 있고 휴대폰 로그인 기능을 잃게 됩니다.

### <a name="verification-codes-when-connected"></a>연결된 경우의 확인 코드

**Q**: 확인 코드를 가져와서 사용하려면 인터넷 또는 네트워크에 연결되어야 하나요?

**A**: 인터넷 또는 데이터에 연결되지 않아도 코드를 사용할 수 있으므로 전화 서비스에 로그인할 필요가 없습니다. 또한 앱을 닫는 즉시 앱 실행이 중지되므로 배터리가 소모되지 않습니다.

### <a name="no-notifications-when-app-is-closed"></a>앱이 닫히면 알림이 오지 않음

**Q**: 앱이 열려 있는 경우에만 알림이 오는 이유가 무엇인가요? 앱이 닫히면 알림이 오지 않습니다.

**A**: 벨 소리가 켜져 있어도 알림이 도착할 때 경고가 울리지 않으면 앱 설정을 확인하세요. 알림이 도착할 때 소리를 내거나 진동이 울리도록 앱을 설정해야 합니다. 알림이 전혀 수신되지 않으면 다음 조건을 확인합니다.<ul><li>휴대폰이 방해 금지 또는 자동 모드인가요? 이 모드에서는 앱이 알림을 보내지 않을 수 있습니다.</li><li>다른 앱에서 알림을 받을 수 있나요? 받을 수 없으면 휴대폰의 네트워크 연결 문제이거나 Android 또는 Apple의 알림 채널에 문제가 있는 것일 수 있습니다. 전화 설정을 통해 네트워크 연결을 해결할 수 있습니다. Android 또는 Apple 알림 채널에 대한 도움을 받으려면 서비스 공급자에게 문의해야 할 수 있습니다.</li><li>앱에서 일부 계정의 알림을 받을 수 있고 나머지 계정의 알림을 받을 수 없나요? 그렇다면 앱에서 문제가 있는 계정을 제거했다가 다시 추가하고 알림을 허용한 다음, 문제가 해결되었는지 확인하세요.</li></ul>이러한 단계를 모두 시도한 후에도 문제가 지속되면 로그 파일을 보내주세요. 앱을 열고 앱의 최상위 메뉴로 이동한 다음  **피드백 보내기** 를 선택합니다. 그런 다음 [Microsoft Authenticator 앱 포럼](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp)으로 이동하여 발생한 문제와 시도한 단계를 Microsoft에 알려주세요.

### <a name="switch-to-push-notifications"></a>푸시 알림으로 전환

**Q**: 앱에서 확인 코드를 사용하고 있는데, 푸시 알림으로 전환하려면 어떻게 해야 하나요?

**A**: 회사 또는 학교 계정(관리자가 허용한 경우) 또는 개인 Microsoft 계정에 대한 알림을 설정할 수 있습니다. Google 또는 Facebook과 같은 타사 계정에서는 알림이 작동하지 않습니다.<br>개인 계정을 알림으로 전환하려면 계정에 디바이스를 다시 등록해야 합니다. 이렇게 하려면 **계정 추가** 로 이동하여 **개인 Microsoft 계정** 을 선택한 다음, 사용자 이름과 암호를 사용하여 로그인합니다.<br>회사 또는 학교 계정의 경우 조직에서 원클릭 알림 허용 여부를 결정합니다.

### <a name="notifications-for-other-accounts"></a>다른 계정에 대한 알림

**Q**: Microsoft 이외의 계정에서도 알림이 작동하나요?

**A**: 아니요. 알림은 Microsoft 계정 및 Azure Active Directory 계정에서만 작동합니다. 회사 또는 학교에서 Azure AD 계정을 사용하는 경우 회사 또는 학교에서 해당 기능을 끌 수 있습니다.

### <a name="backup-and-recovery"></a>Backup 및 복구

**Q**: 새 디바이스가 생겼습니다/백업으로 디바이스를 복원했습니다. Authenticator에서 내 계정을 다시 설정하려면 어떻게 해야 하나요?

**A**: 이전 디바이스에서 **클라우드 백업** 을 설정한 경우 이전 백업을 사용하여 새 iOS 또는 Android 디바이스에서 계정 자격 증명을 복구할 수 있습니다. 자세한 내용은 [Authenticator를 사용하여 계정 자격 증명 백업 및 복구](user-help-auth-app-backup-recovery.md) 문서를 참조하세요.

### <a name="lost-device"></a>디바이스 분실

**Q**: 디바이스를 잃어버렸습니다/새 디바이스로 이동했습니다. 알림이 계속해서 기존 디바이스로 이동하지 않게 하려면 어떻게 해야 하나요?

**A**: 새 디바이스에 Authenticator를 추가해도 기존 디바이스에서 앱이 자동으로 제거되지는 않습니다. 기존 디바이스에서 앱을 삭제하는 것만으로는 충분하지 않습니다. 기존 디바이스에서 앱을 삭제하고, Microsoft 또는 소속 조직에 기존 디바이스를 제거하고 등록을 취소해 달라고 요청해야 합니다.<ul><li>**개인 Microsoft 계정을 사용하여 디바이스에서 앱을 제거하려면:** [계정 보안](https://account.microsoft.com/security) 페이지의 2단계 확인 영역으로 이동하여 기존 디바이스의 확인 기능을 끕니다.</li><li>**회사 또는 학교 Microsoft 계정을 사용하여 디바이스에서 앱을 제거하려면:** [내 앱](https://myapps.microsoft.com/) 페이지 또는 소속 조직의 사용자 지정 포털에서 2단계 확인 영역으로 이동하여 기존 디바이스의 확인 기능을 끕니다.</li></ul>

### <a name="remove-account-from-app"></a>앱에서 계정 제거

**Q**: 앱에서 계정을 제거하려면 어떻게 해야 하나요?

**A**: 앱에서 제거하려는 계정에 대한 계정 타일을 탭하여 계정 전체 화면을 확인합니다. **계정 제거** 를 탭하여 앱에서 계정을 제거합니다.<br>조직에 등록된 디바이스가 있는 경우 계정을 제거하려면 추가 단계를 수행해야 할 수 있습니다. 해당 디바이스에서 Authenticator는 디바이스 관리자로 자동 등록됩니다. 앱을 완전히 제거하려면 먼저 앱 설정에서 앱 등록을 취소해야 합니다.

### <a name="too-many-permissions"></a>권한이 너무 많음

**Q**: 앱이 너무 많은 권한을 요청하는 이유는 무엇인가요?

**A**: 요청할 수 있는 권한의 전체 목록 및 앱에서 사용되는 방식은 다음과 같습니다. 표시되는 특정 사용 권한은 사용하는 휴대폰의 종류에 따라 달라집니다.<ul><li>**위치** - 경우에 따라 조직에서 특정 리소스에 대한 액세스를 허용하기 전에 사용자의 위치를 알고 싶을 수 있습니다. 앱은 조직에 위치를 요구하는 정책이 있는 경우에만 해당 권한을 요청합니다.</li><li>**생체 인식 하드웨어를 사용합니다.** 일부 회사 및 학교 계정에는 ID를 인증할 때마다 추가 PIN이 필요합니다. 앱을 사용하려면 PIN을 입력하는 대신 생체 인식 또는 얼굴 인식을 사용하는 데 동의해야 합니다.</li><li>**카메라.** 회사 계정, 학교 계정 또는 Microsoft 이외의 계정을 추가할 때 QR 코드를 검사하는 데 사용됩니다.</li><li>**연락처 및 휴대폰.** 휴대폰에서 회사 또는 학교 Microsoft 계정을 검색하고 앱에 추가하려면 해당 권한이 필요합니다.</li><li>**SMS.** 개인 Microsoft 계정으로 처음 로그인하는 경우 기록된 전화번호와 일치하는지 확인하는 데 사용됩니다. 앱을 설치한 휴대폰으로 6~8자리 확인 코드가 포함된 문자 메시지가 전송됩니다. Authenticator가 문자 메시지에서 자동으로 해당 코드를 찾기 때문에 따로 코드를 찾아 입력할 필요가 없습니다.</li><li>**다른 앱을 통해 가져오기.** 사용자의 ID를 확인하는 알림은 실행 중인 다른 앱에도 표시됩니다.</li><li>**인터넷의 데이터 수신.** 알림을 보내기 위해 이 사용 권한이 필요합니다.</li><li>**휴대폰 절전 모드 전환 방지.** 디바이스를 조직에 등록하면 조직에서는 휴대폰에서 이 정책을 변경할 수 있습니다.</li><li>**진동 제어.** ID를 인증하는 알림을 수신할 때마다 진동을 울릴지 여부를 선택할 수 있습니다.</li><li>**지문 하드웨어 사용.** 일부 회사 및 학교 계정에는 ID를 인증할 때마다 추가 PIN이 필요합니다. 프로세스를 보다 쉽게 수행하려면 PIN을 입력하는 대신 지문을 사용할 수 있습니다.</li><li> **네트워크 연결 보기.** Microsoft 계정을 추가할 때 앱에는 네트워크/인터넷 연결이 필요합니다.</li><li>**스토리지의 내용 읽기.** 앱 설정을 통해 기술 문제를 보고하는 경우에만 이 사용 권한을 사용하게 됩니다. 스토리지의 일부 정보를 수집하여 문제를 진단합니다.</li><li>**전체 네트워크 액세스.** ID를 인증하는 알림을 보내는 데 이 사용 권한이 필요합니다.</li><li>**시작 시 실행.** 전화를 다시 시작하는 경우 이 사용 권한을 사용하면 ID를 인증하는 알림을 계속 수신할 수 있습니다.</li></ul>

### <a name="approve-requests-without-unlocking"></a>잠금 해제하지 않고 요청 승인

**Q**: Authenticator에서 사용자가 디바이스를 잠금 해제하지 않아도 요청을 승인할 수 있도록 허용하는 이유는 무엇인가요?

**A**: 사용자가 휴대폰을 가졌는지만 증명하면 되므로 확인 요청을 승인하도록 디바이스의 잠금을 해제할 필요가 없습니다. 2단계 인증은 두 가지를 요구합니다. 하나는 사용자가 알고 있는 것이고, 다른 하나는 사용자가 가진 것입니다. 사용자가 알고 있는 것은 암호입니다. 사용자가 가진 것은 Authenticator로 설정하고 다단계 인증 증명으로 등록된 휴대폰입니다. 따라서 휴대폰을 갖고 있고 요청을 승인하면 인증의 두 번째 단계에 대한 조건을 충족합니다.

### <a name="activity-notifications"></a>작업 알림

**Q**: 내 계정 작업에 대한 알림이 수신되는 이유는 무엇인가요?

**A**: 개인 Microsoft 계정이 변경될 때마다 즉시 작업 알림을 Authenticator로 전송해 보안을 강화합니다. 이전에는 해당 알림을 메일과 SMS로만 전송했습니다. 이러한 작업 알림에 대한 자세한 내용은 [비정상적인 계정 로그인이 있는 경우 어떻게 되나요?](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in)를 참조하세요. 알림을 받는 위치를 변경하려면 계정의 [단순 계정 알림과 관련하여 연락할 수 있는 메일 주소를 입력하세요.](https://account.live.com/SecurityNotifications/Update) 페이지에 로그인합니다.

### <a name="one-time-passcodes"></a>일회성 암호

**Q**: 일회성 암호가 작동하지 않습니다.   어떻게 해야 합니까?

**A**: 디바이스의 날짜와 시간이 올바르고 자동으로 동기화되고 있는지 확인합니다. 날짜 및 시간이 잘못되었거나 동기화되지 않으면 코드가 작동하지 않습니다.

### <a name="windows-10-mobile"></a>Windows 10 Mobile

**Q**: Windows 10 Mobile 운영 체제는 2019년 12월에 지원 종료되었습니다. Windows Mobile 운영 체제의 Microsoft Authenticator는 더 이상 사용되지 않습니다.

**A**: 2020년 2월 28일 이후부터 모든 Windows Mobile 운영 체제의 Authenticator는 지원되지 않습니다. 사용자는 앞서 언급한 앱 게시물에 대한 새 업데이트를 받지 못합니다. 2020년 2월 28일 이후부터 모든 Windows Mobile 운영 체제에서 현재 Microsoft Authenticator를 사용하여 인증을 지원하는 Microsoft 서비스는 지원이 중지됩니다. Microsoft 서비스에서 인증을 받으려면 모든 사용자는 이 날짜 이전에 대체 인증 메커니즘으로 전환하는 것이 좋습니다.

### <a name="default-mail-app"></a>기본 메일 앱

**Q**: iOS에 함께 제공되는 기본 메일 앱을 사용하여 회사 또는 학교 계정에 로그인하는 동안 Authenticator에서 보안 확인 정보를 요청하는 메시지가 표시됩니다. 해당 정보를 입력하고 메일 앱으로 돌아가면 오류가 발생합니다. 어떻게 해야 하나요?

**A**: 로그인과 메일 앱이 서로 다른 두 앱에서 발생하여 초기 백그라운드 로그인 프로세스의 작동이 중지되고 실패하는 경우에 이러한 문제가 발생하기 쉽습니다. 이 문제를 해결하려면 메일 앱에 로그인하는 동안 화면의 오른쪽 아래에서 **Safari** 아이콘을 선택하는 것이 좋습니다. Safari로 이동하면 전체 로그인 프로세스가 단일 앱에서 발생하므로 앱에 성공적으로 로그인할 수 있습니다.

### <a name="apple-watch-watchos-7"></a>Apple Watch watchOS 7

**Q**: watchOS 7을 사용하는 Apple Watch에서 문제가 발생하는 이유는 무엇인가요?

**A**: 때때로 watchOS 7에서 세션 승인 또는 거부가 "휴대 전화와 통신하지 못했습니다. 이후 요청 시 Watch 화면이 계속 켜져 있는지 확인합니다. 자세한 내용은 FAQ를 참조하세요."라는 오류 메시지와 함께 실패하는 경우가 있습니다. 앱 잠금을 사용하거나 번호 매치가 필요한 경우 알림에 대해 알려진 문제가 있으며 이를 해결하기 위해 Apple과 협력하고 있습니다. 그동안 Microsoft Authenticator watchOS 앱을 요청하는 모든 알림을 휴대폰에서 승인해야 합니다.

### <a name="signing-into-an-ios-app"></a>iOS 앱에 로그인

**Q**: iOS 앱에 로그인하려고 하는데, Authenticator 앱에서 알림을 승인해야 합니다. iOS 앱으로 돌아가면 중단됩니다. 어떻게 해야 하나요?

**A**: 이는 iOS 13+의 알려진 문제입니다. 지원 관리자에게 도움을 요청하고 다음 세부 정보를 제공하세요. `Use Azure MFA, not MFA server.`

### <a name="apple-watch-doesnt-show-accounts"></a>Apple Watch에 계정이 표시되지 않음

**Q**: Apple Watch에서 Authenticator를 열 때 일부 계정만 표시되는 이유는 무엇인가요?

**A**: Authenticator는 Apple Watch 도우미 앱에서 푸시 알림을 사용하는 Microsoft 개인 계정 또는 회사/학교 계정만 지원합니다. Google 또는 Facebook 같은 다른 계정의 경우 확인 코드를 보려면 휴대폰에서 Authenticator 앱을 열어야 합니다.

### <a name="apple-watch-notifications"></a>Apple Watch 알림

**Q**: Apple Watch에서 알림을 승인 또는 거부할 수 없는 이유는 무엇인가요?

**A**: 먼저 iPhone에서 Authenticator를 버전 6.0.0 이상으로 업그레이드했는지 확인합니다. 그 후 Apple Watch에서 Microsoft Authenticator 도우미 앱을 열고 아래에 **설정** 단추가 있는 계정을 찾습니다. 해당 계정에 대한 알림을 승인하려면 설정 프로세스를 완료합니다.

### <a name="apple-watch-communication-error"></a>Apple Watch 통신 오류

**Q**: Apple Watch와 휴대폰 사이에 통신 오류가 발생합니다. 어떻게 하면 문제를 해결할 수 있나요?

**A**: 이 오류는 Watch가 휴대폰과 통신을 마치기 전에 해당 화면이 절전 모드로 전환되는 경우 발생합니다.<br><b>설치하는 동안 오류가 발생하는 경우:</b><br>설치를 다시 실행하고 프로세스가 완료될 때가지 Watch가 활성화 상태를 유지하도록 합니다. 동시에, 휴대폰에서 앱을 열고 표시되는 모든 메시지에 응답합니다.<br>이렇게 해도 휴대폰과 Watch가 서로 통신할 수 없으면 다음 작업을 시도할 수 있습니다.<ol><li>Microsoft Authenticator 휴대폰 앱을 강제로 종료하여 iPhone에서 다시 엽니다.</li><li>Apple Watch 도우미 앱을 강제로 종료합니다.<ol><li> Watch에서 Microsoft Authenticator 도우미 앱을 엽니다.</li><li>**종료** 화면이 나타날 때까지 옆면의 단추를 길게 누릅니다.</li><li>옆면의 단추를 놓고 Digital Crown을 길게 눌러 활성 앱을 강제로 종료합니다.</li></ol></li><li>휴대폰과 Watch에서 Bluetooth와 Wi-Fi를 모두 껐다가 다시 켭니다.</li><li>iPhone 및 Watch를 다시 시작합니다.</li></ol><b>알림을 승인하려고 할 때 오류가 발생하는 경우:</b><br>다음 번에 Apple Watch에서 알림을 승인하려고 할 때, 요청이 완료되고 성공적으로 완료되었음을 알리는 소리가 들릴 때까지 화면을 활성화 상태로 유지합니다.

### <a name="apple-watch-companion-app-not-syncing"></a>Apple Watch 도우미 앱이 동기화되지 않음

**Q**: Apple Watch용 Microsoft Authenticator 도우미 앱이 동기화되지 않거나 Watch에 표시되지 않는 이유는 무엇인가요?

**A**: 앱이 Watch에 표시되지 않는 경우 다음 작업을 시도합니다. <ol><li>Watch가 watchOS 4.0 이상을 실행 중인지 확인합니다.</li><li>Watch를 다시 동기화합니다.</li></ol>

### <a name="apple-watch-companion-app-crashed"></a>Apple Watch 도우미 앱 충돌

**Q**: Apple Watch 도우미 앱이 충돌했습니다. 크래시 로그를 보내서 조사할 수 있나요?

**A**: 먼저 분석 정보를 Microsoft와 공유하도록 선택했는지 확인합니다. TestFlight 사용자인 경우 이미 등록되어 있습니다. 아닌 경우 **설정 > 개인 정보 > 분석** 으로 이동하여 **iPhone 및 Watch 분석 공유** 및 **앱 개발자와 공유** 옵션을 모두 선택합니다.<br>가입한 후 크래시를 재현하면 자동으로 크래시 로그가 Microsoft로 전송됩니다. 하지만 크래시를 재현할 수 없는 경우 로그 파일을 수동으로 복사해서 보내주시면 됩니다.<ol><li>휴대폰에서 Watch 앱을 열고 **설정 > 일반** 으로 이동한 다음, **Watch 분석 복사** 를 클릭합니다.</li><li>**설정 > 개인 정보 > 분석 > 분석 데이터** 아래에서 해당 크래시를 찾은 다음, 전체 텍스트를 수동으로 복사합니다.</li><li>휴대폰에서 Authenticator를 열고  **피드백 보내기** 페이지에서 **문제가 있으신가요?** 에 있는  **발생한 문제를 설명해 주세요** 상자에 복사한 텍스트를 붙여넣습니다. </li></ol>

## <a name="autofill-with-authenticator"></a>Authenticator로 자동 채우기

**Q**: Authenticator로 자동 채우기란 무엇인가요?

**A**: Authenticator 앱은 이제 휴대폰에서 방문한 앱 및 웹 사이트의 암호를 안전하게 저장하고 자동으로 채웁니다. 자동 채우기를 사용하여 iOS 및 Android 디바이스에서 암호를 동기화하고 자동으로 채울 수 있습니다. 휴대폰에서 Authenticator 앱을 자동 채우기 공급자로 설정하면 사이트 또는 앱 로그인 페이지에 암호를 입력할 때 암호를 저장하는 기능을 제공합니다. 암호는 [개인 Microsoft 계정](https://account.microsoft.com/account)의 일부로 저장되며 개인 Microsoft 계정을 사용하여 Microsoft Edge에 로그인하는 경우에도 사용할 수 있습니다.

**Q**: Authenticator는 어떤 정보를 자동으로 채울 수 있나요?

**A**: Authenticator는 휴대폰에서 방문하는 사이트와 앱의 사용자 이름 및 암호를 자동으로 채울 수 있습니다.

**Q**: 내 휴대폰의 Authenticator에서 암호 자동 채우기를 켜려면 어떻게 해야 하나요?

**A**: 다음 단계를 수행합니다.

1. Authenticator 앱을 엽니다.
1. Authenticator의 **암호** 탭에서 **Microsoft로 로그인** 을 선택하고 [Microsoft 계정](https://account.microsoft.com/account)을 사용하여 로그인합니다. 해당 기능은 현재 Microsoft 계정만 지원하며 회사 또는 학교 계정은 아직 지원되지 않습니다.

**Q**: 내 휴대폰에서 Authenticator를 기본 자동 채우기 공급자로 설정하려면 어떻게 해야 하나요?

**A**: 다음 단계를 수행합니다.

1. Authenticator 앱을 엽니다.
1. 앱 내부의 **암호** 탭에서 **Microsoft로 로그인** 을 선택하고 [Microsoft 계정](https://account.microsoft.com/account)을 사용하여 로그인합니다.
1. 다음 중 하나를 수행합니다.

   - iOS에서 **설정** 의 자동 채우기 설정 섹션에서 **자동 채우기 설정 방법** 을 선택하여 기본 자동 채우기 공급자로 Authenticator를 설정하는 방법을 알아봅니다.
   - Android에서 **설정** 의 자동 채우기 설정 섹션에서 **자동 채우기 공급자로 설정** 을 선택합니다.

**Q**: 설정에서 **자동 채우기** 를 사용할 수 없는 경우 어떻게 해야 하나요?

**A**: Authenticator에서 자동 채우기를 사용할 수 없는 경우 조직 또는 계정 유형에 대해 자동 채우기가 아직 허용되지 않았기 때문일 수 있습니다. 회사 또는 학교 계정이 추가되지 않은 디바이스에서 해당 기능을 사용할 수 있습니다. 조직에서 자동 채우기를 허용하는 방법에 대한 자세한 내용은 [IT 관리자를 위한 자동 채우기](#autofill-for-it-admins)를 참조하세요.

**Q**: 암호 동기화를 중지하려면 어떻게 해야 하나요?

**A**: Authenticator 앱에서 암호 동기화를 중지하려면 **설정** > **자동 채우기 설정** > **계정 동기화** 를 엽니다. 다음 화면에서 **동기화 중지 및 모든 자동 채우기 데이터 제거** 를 선택하면 됩니다. 그러면 디바이스에서 암호 및 기타 자동 채우기 데이터가 제거됩니다. 자동 채우기 데이터를 제거해도 다단계 인증에는 영향을 주지 않습니다.

**Q**: Authenticator 앱에서 암호를 어떻게 보호하나요?

**A**: Authenticator 앱은 이미 다단계 인증 및 계정 관리에 높은 수준의 보안을 제공하고 있으며, 동일한 높은 보안 수준을 확장해 암호를 관리하고 있습니다.

- **Authenticator 앱에 필요한 강력한 인증**: Authenticator에 로그인하려면 두 번째 단계가 필요합니다. 즉, 누군가가 Microsoft 계정 암호를 가지고 있어도 Authenticator 앱 내의 암호가 보호됩니다.
- **생체 인식 및 암호를 통해 보호되는 자동 채우기 데이터**: 앱 또는 사이트에서 암호를 자동으로 채우려면 Authenticator는 생체 인식 또는 디바이스 암호를 요청합니다. 따라서 다른 사람이 디바이스에 액세스하더라도 생체 인식 또는 디바이스 PIN 입력을 제공할 수 없기 때문에 암호를 채우거나 볼 수 없도록 보안을 강화할 수 있습니다. 또한 사용자가 앱 설정에서 앱 잠금을 해제하는 경우에도 생체 인식 또는 PIN을 제공하지 않으면 암호 페이지를 열 수 없습니다.
- **암호화된 디바이스 암호**: 디바이스의 암호는 암호화되고, 암호화/암호 해독 키는 저장되지 않으며 필요할 때 항상 생성됩니다. 암호는 사용자가 원할 때, 즉 자동 채우기 중이거나 사용자가 암호를 확인하려는 경우에만 해독됩니다. 그리고 두 경우 모두 생체 인식 또는 PIN이 필요합니다.
- **클라우드 및 네트워크 보안**: 클라우드의 암호는 디바이스에 도달하는 경우에만 암호화 및 암호 해독됩니다. 암호는 SSL로 보호되는 HTTPS 연결을 통해 동기화되므로 중요한 데이터를 동기화할 때 공격자의 데이터 도청을 방지할 수 있습니다. 암호화 해시 기능(특히 해시 기반 메시지 인증 코드)을 사용하여 네트워크를 통해 동기화되는 데이터의 온전성도 확인합니다.

## <a name="autofill-for-it-admins"></a>IT 관리자를 위한 자동 채우기

**Q**: 직원이나 학생이 Authenticator 앱에서 암호 자동 채우기를 사용할 수 있나요?

**A**: 예, [개인 Microsoft 계정](https://go.microsoft.com/fwlink/?linkid=2144423)에 대한 자동 채우기는 이제 회사 또는 학교 계정이 Authenticator 앱에 추가된 경우에도 대부분의 엔터프라이즈 사용자에 대해 작동합니다. 양식을 작성하여 조직에서 자동 채우기를 허용하거나 거부하고 [Authenticator 팀에 보낼 수 있습니다](https://aka.ms/ConfigureAutofillInAuthenticator). 현재 회사 또는 학교 계정에서는 자동 채우기를 사용할 수 없습니다.

**Q**: 내 사용자의 회사 또는 학교 계정 암호를 자동으로 동기화하나요?

**A**: 아니요. 암호 자동 채우기는 사용자의 회사 또는 학교 계정 암호를 동기화하지 않습니다. 사용자가 사이트 또는 앱을 방문하면 Authenticator가 해당 사이트나 앱의 암호를 저장하도록 제안하고, 사용자가 선택하는 경우에만 암호가 저장됩니다.
  
**Q**: 내 조직의 특정 사용자만 자동 채우기 허용 목록에 추가할 수 있나요?

**A**: 아니요. 기업에서는 현재 직원 모두에 대해서만 암호 자동 채우기를 사용하거나 사용하지 못하도록 설정할 수 있습니다.

**Q**: 직원 또는 학생에게 여러 회사 또는 학교 계정이 있는 경우 어떻게 되나요? 예를 들어 직원의 Microsoft Authenticator에는 여러 기업 또는 학교 계정이 있습니다.

**A**: Authenticator 앱에 추가된 모든 기업 또는 학교는 앱 소유자가 자동 채우기를 사용할 수 있도록 Authenticator에서 자동 채우기를 허용 목록에 추가해야 합니다. 해당 제한에 대한 한 가지 예외는 직원 또는 학생이 회사 또는 학교 계정을 [외부 또는 타사 계정](user-help-auth-app-add-non-ms-account.md)으로 Microsoft 클라우드 기반 다단계 인증에 추가하는 경우입니다.

## <a name="next-steps"></a>다음 단계

- 개인 Microsoft 계정에 대한 확인 코드를 가져오는 데 문제가 있는 경우 **Microsoft 계정 보안 정보 및 확인 코드** 문서에서 [ 확인 코드 문제 해결](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes) 섹션을 참조하세요.

- 2단계 인증에 대해 더 자세히 알아보려면 [2단계 인증에 내 계정 설정](multi-factor-authentication-end-user-first-time.md)을 참조하세요.

- 보안 정보에 대한 자세한 내용은 [보안 정보(미리 보기) 개요](./security-info-setup-signin.md)를 참조하세요.

- 궁금한 내용에 대한 답변이 여기에 없으면 직접 질문을 남겨주세요. [Microsoft Authenticator 앱 포럼](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp)으로 이동하여 질문을 게시하고, 커뮤니티의 도움을 받고, 이 페이지에 의견을 남기세요.
