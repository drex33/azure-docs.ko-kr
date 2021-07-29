---
title: 확인 방법으로 Microsoft Authenticator 앱 설정 - Azure AD
description: Microsoft Authenticator 앱을 확인 방법으로 사용하여 ID를 확인하는 보안 정보(미리 보기) 페이지를 설정하는 방법입니다.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 06/10/2021
ms.author: curtand
ms.openlocfilehash: 4e03fe3ca7a66dc361988151b558fef0c4b4f390
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111982577"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>확인 방법으로 Microsoft Authenticator 앱 설정

이 단계를 수행하여 2단계 확인 및 암호 재설정 방법을 추가할 수 있습니다. 이 정보를 처음 설정한 후 **보안 정보** 페이지로 돌아가서 보안 정보를 추가, 업데이트 또는 삭제할 수 있습니다.

회사 또는 학교 계정에 로그인한 직후에 이 정보를 설정하라는 메시지가 표시되는 경우 [로그인 페이지 프롬프트에서 보안 정보 설정](security-info-setup-signin.md) 문서의 자세한 단계를 참조하세요.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> 이 옵션이 보이지 않는 경우 사용자의 조직에서 인증에 인증 앱 사용을 허용하지 않았을 가능성이 있습니다. 이 경우 다른 방법을 선택하거나 조직의 지원 데스크에 문의하여 추가 지원을 받아야 합니다.

## <a name="security-vs-password-reset-verification"></a>보안 및 암호 재설정 확인

보안 정보 방법은 2단계 보안 확인 및 암호 재설정에 모두 사용됩니다. 그러나 모든 보안 정보 방법을 둘 모두에 사용할 수 있는 것은 아닙니다.

| 방법 | 사용 대상 |
| ------ | -------- |
| 인증자 앱 | 2단계 인증 및 암호 재설정 인증입니다. |
| 문자 메시지 | 2단계 인증 및 암호 재설정 인증입니다. |
| 전화 통화 | 2단계 인증 및 암호 재설정 인증입니다. |
| 보안 키 | 2단계 인증 및 암호 재설정 인증입니다. |
| 이메일 계정 | 암호 재설정 인증 전용입니다. 2단계 인증에는 다른 보안 정보 방법을 선택해야 합니다. |
| 보안 질문 | 암호 재설정 인증 전용입니다. 2단계 인증에는 다른 보안 정보 방법을 선택해야 합니다. |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>보안 정보 페이지에서 Microsoft Authenticator 앱 설정

조직 설정에 따라 인증 앱을 보안 정보 메서드 중 하나로 사용할 수 있습니다. Microsoft Authenticator 앱을 사용할 필요는 없으며 설정 과정에서 다른 앱을 선택할 수 있습니다. 단, 이 문서에서는 Microsoft Authenticator 앱을 사용합니다.

> [!IMPORTANT]
> 서로 다른 5개의 디바이스에서 Microsoft Authenticator 앱을 설정했거나 하드웨어 토큰을 5개 사용한 경우 여섯 번째는 설정할 수 없으며 다음 오류 메시지가 표시됩니다.
> 
> **이미 5개의 인증자 앱 또는 하드웨어 토큰이 있기 때문에 Microsoft Authenticator를 설정할 수 없습니다. 관리자에게 문의하여 인증자 앱 또는 하드웨어 토큰 중 하나를 삭제하세요.**

### <a name="to-set-up-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱을 설정하려면

1. 회사 또는 학교 계정에 로그인한 다음, https://myaccount.microsoft.com/ 페이지로 이동합니다.

    ![보안 정보 링크가 강조 표시된 내 프로필 페이지](media/security-info/securityinfo-myprofile-page.png)

2. 왼쪽 메뉴에서 선택하거나 **보안 정보** 창에서 링크를 사용하여 **보안 정보** 를 선택합니다. 이미 등록한 경우 2단계 인증을 요구하는 메시지가 표시됩니다. 그러면 **보안 정보** 창에서 **메서드 추가** 를 선택합니다.

    ![메서드 추가 옵션이 강조 표시된 보안 정보 페이지](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. **메서드 추가** 페이지의 드롭다운 목록에서 **Authenticator 앱** 을 선택한 다음, **추가** 를 선택합니다.

    ![Authenticator 앱이 선택된 상태에서 메서드 상자 추가](media/security-info/securityinfo-myprofile-addauthapp.png)

4. **앱을 받고 시작하기** 페이지에서 **지금 다운로드** 를 선택하여 Microsoft Authenticator 앱을 모바일 디바이스에 다운로드하여 설치하고, **다음** 을 선택합니다.

    앱을 다운로드하여 설치하는 방법에 대한 자세한 내용은 [Microsoft Authenticator 앱 다운로드 및 설치](user-help-auth-app-download-install.md)를 참조하세요.

    ![앱을 받고 시작하기 페이지](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > Microsoft Authenticator 앱 이외의 인증 앱을 사용하려면 **다른 인증 앱 사용** 링크를 선택합니다.
   >
   > 조직에서 인증 앱 이외의 다른 메서드를 선택할 수 있게 하려면 **다른 메서드 설정 링크** 를 선택하면 됩니다.

5. 모바일 디바이스에서 Microsoft Authenticator 앱을 설정하는 동안 **계정 설정** 페이지를 열어 둡니다.

    ![인증 앱 설정 페이지](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. Microsoft Authenticator 앱을 열고, 알림을 허용하도록 선택하고(프롬프트되는 경우), 오른쪽 위에 있는 **사용자 지정 및 제어** 아이콘에서 **계정 추가** 를 선택한 다음, **회사 또는 학교 계정** 을 선택합니다.

    >[!Note]
    >Microsoft Authenticator 앱을 처음으로 설정하는 경우 앱에서 카메라에 액세스할 수 있도록 허용할지(iOS) 아니면 앱에서 사진을 촬영하고 비디오를 녹화할 수 있도록 허용할지(Android) 묻는 메시지를 받을 수 있습니다. 인증자 앱이 카메라에 액세스하여 다음 단계에서 QR 코드의 사진을 찍을 수 있도록 **허용** 을 선택해야 합니다. 카메라를 허용하지 않는 경우에도 여전히 인증자 앱을 설정할 수 있지만 코드 정보를 수동으로 추가해야 합니다. 코드를 수동으로 추가하는 방법에 대한 자세한 내용은 [수동으로 앱에 계정 추가](user-help-auth-app-add-account-manual.md)를 참조하세요.

7. 컴퓨터에서 **계정 설정** 페이지로 돌아가서 **다음** 을 선택합니다.

    **QR 코드 스캔** 페이지가 나타납니다.

    ![Authenticator 앱을 사용하여 QR 코드 스캔](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. 6단계에서 회사 또는 학교 계정이 만들어지면 모바일 디바이스에 표시된 Microsoft Authenticator 앱 QR 코드 판독기를 사용하여 제공된 코드를 검사합니다.

    인증 앱은 추가 정보 없이도 회사 또는 학교 계정을 성공적으로 추가해야 합니다. 하지만 QR 코드 판독기에서 코드를 읽을 수 없으면 **QR 코드를 스캔할 수 없습니까?** 링크를 선택하고, Microsoft Authenticator 앱에 코드와 URL을 수동으로 입력하면 됩니다. 코드를 수동으로 추가하는 방법에 대한 자세한 내용은 [수동으로 앱에 계정 추가](user-help-auth-app-add-account-manual.md)를 참조하세요.

9. 컴퓨터의 **QR 코드 스캔** 페이지에서 **다음** 을 선택합니다.

    계정을 테스트하기 위한 알림이 모바일 디바이스의 Microsoft Authenticator 앱으로 보내집니다.

    ![인증 앱으로 계정 테스트](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. Microsoft Authenticator 앱에서 알림을 승인하고, **다음** 을 선택합니다.

     ![앱과 계정 연결에 대한 성공 알림](media/security-info/securityinfo-myprofile-successauthapp.png)

     2단계 인증 또는 암호 재설정을 사용하면 기본적으로 Microsoft Authenticator 앱을 사용하여 사용자의 ID를 확인하도록 보안 정보가 업데이트됩니다.

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>보안 정보 메서드에서 Authenticator 앱 삭제

인증 앱을 보안 정보 메서드로 더 이상 사용하지 않으려는 경우 **보안 정보** 페이지에서 제거할 수 있습니다. 이 기능은 Microsoft Authenticator 앱뿐만 아니라 모든 인증 앱에도 작동합니다. 앱을 삭제한 후에는 모바일 디바이스의 인증 앱으로 이동하여 계정을 삭제해야 합니다.

>[!Important]
>인증 앱을 실수로 삭제하면 실행을 취소할 방법이 없습니다. 이 문서의 [인증 앱 설정](#set-up-the-microsoft-authenticator-app-from-the-security-info-page) 섹션에 있는 단계에 따라 인증 앱을 다시 추가해야 합니다.

### <a name="to-delete-the-authenticator-app"></a>인증 앱을 삭제하려면

1. **보안 정보** 페이지에서 Authenticator 앱 옆에 있는 **삭제** 링크를 선택합니다.

    ![보안 정보에서 인증 앱을 삭제하는 링크](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. 확인 상자에서 **예** 를 선택하여 인증 앱을 삭제합니다. 인증 앱이 삭제되면 보안 정보에서 제거되고 **보안 정보** 페이지에서 사라집니다. 인증 앱이 기본 메서드인 경우, 사용 가능한 다른 메서드로 기본값이 바뀝니다.

3. 모바일 디바이스에서 인증 앱을 열고 **계정 편집** 을 선택한 다음, 인증 앱에서 회사 또는 학교 계정을 삭제합니다.

    2단계 인증 및 암호 재설정 요청을 위한 인증 앱에서 계정이 완전히 삭제됩니다.

## <a name="change-your-default-security-info-method"></a>기본 보안 인증 방식 변경

2단계 인증을 사용하거나 암호 재설정을 요청하기 위해 회사 또는 학교 계정에 로그인할 때 사용되는 기본 메서드로 인증 앱을 사용하려면 **보안 정보** 페이지에서 설정하면 됩니다.

>[!NOTE]
>기본 로그인 방법이 전화번호에 대한 문자 또는 통화인 경우에는 다단계 인증 중에 SMS 코드 또는 음성 통화가 자동으로 전송됩니다. 2021년 6월 현재, 일부 앱은 사용자에게 먼저 **문자** 또는 **통화** 를 선택하도록 요청합니다. 이 옵션은 다른 앱에 대해 너무 많은 보안 코드를 보내지 못하도록 합니다. 기본 로그인 방법이 Microsoft Authenticator 앱([매우 권장](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752))인 경우 앱 알림이 자동으로 전송됩니다.

### <a name="to-change-your-default-security-info-method"></a>기본 보안 정보 방법을 변경하려면 다음을 수행합니다.

1. **보안 정보** 페이지에서 **기본 로그인 방법** 정보 옆에 있는 **변경** 링크를 선택합니다.

    ![기본 로그인 메서드에 대한 링크 변경](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. 사용 가능한 메서드 드롭다운 목록에서 **Microsoft Authenticator-알림** 을 선택합니다. Microsoft Authenticator 앱을 사용하지 않으려면 **인증 앱 또는 하드웨어 토큰** 옵션을 선택합니다.

    ![기본 로그인 메서드 선택](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. **확인** 을 선택합니다.

    로그인에 사용되는 기본 메서드가 Microsoft Authenticator 앱으로 변경됩니다.

## <a name="additional-security-info-methods"></a>추가 보안 정보 메서드

수행하려는 작업에 따라, 조직이 본인 여부를 확인하기 위해 사용자에게 연락하는 방식에 대한 추가 옵션이 제공됩니다. 옵션에는 다음이 포함됩니다.

- **모바일 디바이스 텍스트.** 모바일 디바이스 번호를 입력하면 2단계 인증 또는 암호 재설정에 사용할 코드가 포함된 문자를 받습니다. 문자 메시지(SMS)를 사용하여 본인 여부를 확인하는 방법에 대한 단계별 지침은 [문자 메시지(SMS)를 사용하도록 보안 정보 설정](security-info-setup-text-msg.md)을 참조하세요.

- **모바일 디바이스 또는 회사 전화 통화.** 모바일 디바이스 번호를 입력하면 2단계 인증 또는 암호 재설정을 위한 전화가 걸려옵니다. 전화 번호를 사용하여 본인 여부를 확인하는 방법에 대한 단계별 지침은 [전화 통화를 사용하도록 보안 정보 설정](security-info-setup-phone-number.md)을 참조하세요.

- **보안 키.** Microsoft 호환 보안 키를 등록하고 2단계 인증 또는 암호 재설정을 위해 PIN과 함께 사용합니다. 보안 키를 사용하여 ID를 확인하는 방법에 대한 단계별 지침은 [보안 키를 사용하도록 보안 정보 설정](security-info-setup-security-key.md)을 참조하세요.

- **이메일 주소.** 회사 또는 학교 이메일 주소를 입력하여 암호 재설정을 위한 이메일을 받습니다. 이 옵션은 2단계 인증에 사용할 수 없습니다. 이메일을 설정하는 방법에 대한 단계별 지침은 [이메일을 사용하도록 보안 정보 설정](security-info-setup-email.md)을 참조하세요.

- **보안 질문.** 조직의 관리자가 만든 몇 가지 보안 질문에 대답합니다. 이 옵션은 2단계 인증이 아닌 암호 재설정에만 사용할 수 있습니다. 보안 질문을 설정하는 방법에 대한 단계별 지침은 [보안 질문을 사용하도록 보안 정보 설정](security-info-setup-questions.md) 문서를 참조하세요.

    >[!Note]
    >이러한 옵션 중 일부가 누락된 경우 사용자의 조직에서 해당 방법을 허용하지 않았을 가능성이 있습니다. 이 경우 사용 가능한 방법을 선택하거나 관리자에게 문의하여 자세한 도움을 받아야 합니다.

## <a name="next-steps"></a>다음 단계

- [2단계 인증 또는 보안 정보를 사용하여 로그인](security-info-setup-signin.md) 문서의 단계에 따라 Microsoft Authenticator 앱을 사용하여 로그인합니다.

- 분실했거나 잊어버린 경우 [암호 재설정 포털](https://passwordreset.microsoftonline.com/)에서 암호를 다시 설정하거나 [회사 또는 학교 암호 재설정](active-directory-passwords-update-your-own-password.md) 문서의 단계를 수행합니다.

- [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) 문서에서 로그인 문제에 대한 문제 해결 팁 및 도움말을 확인합니다.
