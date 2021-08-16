---
title: Microsoft Authenticator 앱에 회사 또는 학교 계정 추가 - Azure AD
description: 2단계 인증을 사용하는 동안 Microsoft Authenticator 앱에 회사 또는 학교 계정을 추가하여 ID를 확인합니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 05/11/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 329ddb32282d91ec5e990753057aea606622a266
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110535188"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Microsoft Authenticator 앱에 회사 또는 학교 계정 추가

조직에서 2단계 인증을 사용하는 경우 인증 방법 중 하나로 Microsoft Authenticator 앱을 사용하도록 회사 또는 학교 계정을 설정할 수 있습니다.

>[!Important]
>계정을 추가하려면 Microsoft Authenticator 앱을 다운로드하여 설치해야 합니다. [앱 다운로드 및 설치](user-help-auth-app-download-install.md) 문서의 단계를 아직 진행하지 않은 경우 지금 진행하세요.

## <a name="add-your-work-or-school-account"></a>회사 또는 학교 계정 추가

다음 중 하나를 수행하여 Microsoft Authenticator 앱에 회사 또는 학교 계정을 추가할 수 있습니다.

- 회사 또는 학교 계정 자격 증명을 사용하여 로그인(미리 보기)
- QR 코드 스캔

### <a name="sign-in-with-your-credentials"></a>자격 증명으로 로그인

>[!Note]
>이제 Microsoft Authenticator 앱에 로그인하여 회사 또는 학교 계정을 추가할 수 있습니다.

자격 증명을 사용하여 회사 또는 학교 계정에 로그인하여 계정을 추가하려면

1. Microsoft Authenticator 앱을 열고 **+** 단추를 선택하고 **회사 또는 학교 계정 추가** 를 탭합니다. **로그인** 을 선택합니다.

1. 회사 또는 학교 계정 자격 증명을 입력합니다. TAP(임시 액세스 패스)가 있는 경우 로그인하는 데 사용할 수 있습니다. 이 경우 다음 조건 중 하나로 인해 계속 진행하지 못할 수 있습니다.

   - 계정에 대해 강력한 인증 토큰을 얻기 위한 충분한 인증 방법이 없는 경우에는 계정을 계속 추가하지 못할 수 있습니다.

   - `You might be signing in from a location that is restricted by your admin` 메시지를 받으면 관리자가 이 기능을 활성화하지 않은 것입니다. **추가 보안 확인** 페이지 또는  [보안 정보](https://mysignins.microsoft.com/security-info)에서 QR 코드를 검사하여 계정 설정을 시도할 수 있습니다.

1. 관리자가 Authenticator 앱을 사용하는 휴대폰 로그인을 허용한 경우 사용자는 디바이스 등록을 통해 암호 없는 휴대폰 로그인 및 Azure MFA(Multi-Factor Authentication)를 설정할 수 있습니다. 그러나 전화 로그인을 사용하도록 설정했는지 여부에 관계없이 MFA를 설정할 수 있습니다.

1. 이 경우 조직에서 제공한 QR 코드를 스캔하여 앱에서 온-프레미스 Multi-Factor Authentication 계정을 설정하라는 메시지가 표시될 수 있습니다. 조직에서 온-프레미스 MFA 서버를 사용하는 경우에만 이 작업을 수행하도록 요구됩니다.

1. 디바이스에서 계정을 탭하고 전체 화면 보기에서 계정이 올바른지 확인합니다. 보안 수준을 높이기 위해 확인 코드는 30초마다 변경되므로 코드 하나를 여러 번 사용할 수 없습니다.

## <a name="sign-in-with-a-qr-code"></a>QR 코드를 사용하여 로그인

QR 코드를 스캔하여 계정을 추가하려면 다음을 수행합니다.

1. 컴퓨터에서 **추가 보안 확인** 페이지로 이동합니다.

   >[!Note]
   >**추가 보안 인증** 페이지가 표시되지 않는 경우에는 관리자가 보안 정보(미리 보기) 환경을 켰을 수 있습니다. 이 경우 [인증자 앱을 사용하도록 보안 정보 설정](security-info-setup-auth-app.md) 섹션의 지침을 따라야 합니다. 이외의 경우에는 조직의 지원 센터에 도움을 요청해야 합니다. 보안 정보에 대한 자세한 내용은 [로그인 프롬프트에서 보안 정보 설정](security-info-setup-signin.md)을 참조하세요.

1. Authenticator 앱 옆에 있는 확인란을 선택한 후 **구성** 을 선택합니다. **모바일 앱 구성** 페이지가 나타납니다.

   ![QR 코드를 제공하는 화면](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. Microsoft Authenticator 앱을 열고, 더하기 아이콘 ![iOS 또는 Android 디바이스에서 더하기 아이콘 선택](media/user-help-auth-app-add-work-school-account/plus-icon.png)을 선택하고, **계정 추가** 를 선택한 다음, **회사 또는 학교 계정**, **QR 코드 스캔** 을 차례로 선택합니다.
   Authenticator 앱에 계정이 설정되어 있지 않으면 **계정 추가** 라는 파란색 단추가 표시됩니다.

카메라를 사용하여 QR 코드를 스캔하라는 메시지가 표시되지 않으면 휴대폰의 설정에서 Authenticator 앱이 휴대폰 카메라에 액세스할 수 있는지 확인합니다.

## <a name="sign-in-on-a-remote-computer"></a>원격 컴퓨터에서 로그인

많은 앱에서 PC와 같은 다른 디바이스에 코드를 입력하여 인증할 수 있습니다. 원격 컴퓨터에 로그인하여 Microsoft Authenticator 앱을 설치하려는 경우:

1. Microsoft Authenticator 앱을 열고 **+** 단추 &gt; **회사 또는 학교 계정 추가** &gt; **로그인** 을 선택합니다.
1. **다른 디바이스에서 로그인** 을 선택합니다.
1. 원격 화면에서 [**계정에 로그인** 페이지](https://microsoft.com/devicelogin)를 열고 Authenticator 앱에 표시되는 코드를 입력합니다.
1. 원격 화면에서 회사 또는 학교 계정 자격 증명을 사용하여 로그인합니다. TAP(임시 액세스 패스)가 있는 경우 로그인하는 데 사용할 수 있습니다.
1. 원격 화면에서 인증을 완료한 후 Authenticator 앱으로 돌아가 설정을 완료합니다.

 ## <a name="next-steps"></a>다음 단계

- 앱에 계정을 추가한 후에는 디바이스에서 Authenticator 앱을 사용하여 로그인할 수 있습니다. 자세한 내용은 [앱을 사용하여 로그인](user-help-auth-app-sign-in.md)을 참조하세요.

- iOS를 실행하는 디바이스의 경우에는 계정 자격 증명 및 관련 앱 설정(예: 계정 순서)을 클라우드에 백업할 수도 있습니다. 자세한 내용은 [Microsoft Authenticator 앱을 통한 백업 및 복구](user-help-auth-app-backup-recovery.md)를 참조하세요.
