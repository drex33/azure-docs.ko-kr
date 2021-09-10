---
title: Windows 디바이스의 셀프 서비스 암호 재설정 - Azure Active Directory
description: Windows 로그인 화면에서 Azure Active Directory 셀프 서비스 암호 재설정을 사용하도록 설정하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc3defd1bf3e3052f45f00eeeec0078ce0f6bbde
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528136"
---
# <a name="enable-azure-active-directory-self-service-password-reset-at-the-windows-sign-in-screen"></a>Windows 로그인 화면에서 Azure Active Directory 셀프 서비스 암호 재설정을 사용하도록 설정

Azure AD(Azure Active Directory)에서 SSPR(셀프 서비스 암호 재설정)을 사용하면 관리자 또는 기술 지원팀에서 개입하지 않고도 사용자가 암호를 변경하거나 다시 설정할 수 있습니다. 일반적으로 사용자는 다른 디바이스에서 웹 브라우저를 열어 [SSPR 포털](https://aka.ms/sspr)에 액세스합니다. Windows 7, 8, 8.1, 10을 실행하는 컴퓨터에서 환경을 개선하려면 사용자가 Windows 로그인 화면에서 암호를 재설정하도록 할 수 있습니다.

![SSPR 링크가 표시된 Windows 7 및 10 로그인 화면 예제](./media/howto-sspr-windows/windows-reset-password.png)

> [!IMPORTANT]
> 이 자습서에서는 관리자에게 엔터프라이즈의 Windows 디바이스에 SSPR을 사용하도록 설정하는 방법을 보여 줍니다.
>
> IT 팀이 Windows 디바이스에서 SSPR을 사용하는 기능을 사용하도록 설정하지 않았거나 로그인하는 동안 문제가 발생한 경우 추가 지원을 받으려면 기술 지원팀에 문의하세요.

## <a name="general-limitations"></a>일반적인 제한 사항

Windows 로그인 화면에서 SSPR을 사용하는 경우 다음과 같은 제한이 적용됩니다.

- 현재 원격 데스크톱 또는 Hyper-V 고급 세션에서는 암호 재설정이 지원되지 않습니다.
- 일부 타사 자격 증명 공급자는 이 기능에서 문제를 일으키는 것으로 알려져 있습니다.
- [EnableLUA 레지스트리 키](/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) 수정을 통해 UAC를 사용하지 않도록 설정하면 문제가 발생합니다.
- 이 기능은 802.1x 네트워크 인증이 배포된 네트워크에 및 “사용자가 로그온하기 직전에 수행” 옵션에는 작동하지 않습니다. 802.1x 네트워크 인증이 배포된 네트워크의 경우 머신 인증을 사용하여 이 기능을 사용하는 것이 좋습니다.
- Hybrid Azure AD에 조인된 컴퓨터에는 새 암호를 사용하고 된 자격 증명을 업데이트하기 위해 도메인 컨트롤러를 향한 네트워크 연결 라인이 있어야 합니다. 즉, 디바이스가 조직의 내부 네트워크에 있거나, 온-프레미스 도메인 컨트롤러에 대한 네트워크 액세스가 있는 VPN에 있어야 합니다.
- 이미지를 사용하는 경우 sysprep을 실행하기 전에 기본 제공 관리자에 대한 웹 캐시를 지운 후에 CopyProfile 단계를 수행해야 합니다. 이 단계에 대한 자세한 내용은 [사용자 지정 기본 사용자 프로필을 사용할 때 성능 저하](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile) 지원 문서에서 찾을 수 있습니다.
- 다음 설정은 Windows 10 디바이스에서의 암호 사용 및 재설정 기능을 방해하는 것으로 알려져 있습니다.
    - Windows 10에서 정책이 Ctrl+Alt+Del을 요구하는 경우 **암호 재설정** 이 작동하지 않습니다.
    - 잠금 화면 알림이 꺼진 경우 **암호 재설정** 이 작동하지 않습니다.
    - *HideFastUserSwitching* 이 사용하도록 설정 또는 1로 설정됨
    - *DontDisplayLastUserName* 이 사용하도록 설정 또는 1로 설정됨
    - *NoLockScreen* 이 사용하도록 설정 또는 1로 설정됨
    - ‘BlockNonAdminUserInstall’이 사용하도록 설정 또는 1로 설정됨
    - *EnableLostMode* 가 디바이스에서 설정됨
    - Explorer.exe는 사용자 지정 셸로 바뀜
- 다음 3개의 특정 설정을 조합하면 이 기능이 작동하지 않을 수 있습니다.
    - 대화형 로그온: CTRL + ALT + DEL = Disabled가 필요하지 않음
    - *DisableLockScreenAppNotifications* = 1 또는 Enabled
    - Windows SKU가 Home 또는 Professional 버전이 아님

> [!NOTE]
> 해당 제한은 디바이스 잠금 화면에서 비즈니스용 Windows Hello PIN 재설정에도 적용됩니다.
>

## <a name="windows-10-password-reset"></a>Windows 10 암호 재설정

로그인 화면에서 SSPR에 대한 Windows 10 디바이스를 구성하려면 다음 필수 조건 및 구성 단계를 검토합니다.

### <a name="windows-10-prerequisites"></a>Windows 10 필수 조건

- 관리자는 [Azure Portal에서 Azure AD 셀프 서비스 암호 재설정을 사용하도록 설정해야 합니다](tutorial-enable-sspr.md).
- 사용자는 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)에서 이 기능을 사용하기 전에 SSPR에 등록해야 합니다.
    - Windows 로그인 화면에서 SSPR을 사용하는 경우뿐 아니라, 모든 사용자는 인증 연락처 정보를 제공해야 암호를 재설정할 수 있습니다.
- 네트워크 프록시 요구 사항:
    - 포트 443에서 `passwordreset.microsoftonline.com` 및 `ajax.aspnetcdn.com`
    - Windows 10 디바이스는 머신 수준 프록시 구성만을 지원합니다.
- Windows 10, 2018년 4월 업데이트(v1803) 이상의 버전에서 실행하고 디바이스 상태는 다음 중 하나여야 합니다.
    - Azure AD 가입
    - 하이브리드 Azure AD 가입

### <a name="enable-for-windows-10-using-intune"></a>Intune을 사용하여 Windows 10에 사용

Intune을 사용하여 로그인 화면에서 SSPR을 사용하도록 설정하는 가장 유연한 방법은 구성 변경을 배포하는 것입니다. Intune을 사용하면 여러분이 정의하는 특정 머신 그룹에 구성 변경 내용을 배포할 수 있습니다. 이 메서드는 디바이스의 Intune 등록이 필요합니다.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Intune에서 디바이스 구성 정책 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **Intune** 을 선택합니다.
1. **디바이스 구성** > **프로필** 로 이동한 다음, **+ 프로필 만들기** 를 선택하여 새 디바이스 구성 프로필을 만듭니다.
   - **플랫폼** 에서 *Windows 10 이상* 을 선택합니다.
   - **프로필 유형** 으로 *사용자 지정* 을 선택합니다.
1. **만들기** 를 선택한 다음, 프로필에 대한 의미 있는 이름(예: *Windows 10 로그인 화면 SSPR*)을 입력합니다.

    원하는 경우, 프로필에 대한 의미 있는 설명을 입력하고 **다음** 을 선택합니다.
1. *구성 설정* 에서 **추가** 를 선택하고 다음 OMA-URI 설정을 제공하여 암호 재설정 링크를 사용하도록 설정합니다.
      - 설정이 하는 일을 설명하는 의미 있는 이름(예: ‘SSPR 링크 추가’)을 입력합니다.
      - 필요에 따라 설정에 대한 의미 있는 설명을 입력합니다.
      - **OMA URI** 을 `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`으로 설정
      - **데이터 형식** 을 **정수** 로 설정
      - **값** 을 **1** 로 설정

    **추가** 를 선택한 다음, **다음** 을 선택합니다.
1. 이 정책은 특정 사용자, 디바이스 또는 그룹에 할당할 수 있습니다. 사용자 환경에 대해 원하는 대로 프로필을 할당합니다. 가급적 먼저 디바이스의 테스트 그룹에 할당한 후 **다음** 을 선택합니다.

    자세한 내용은 [Microsoft Intune에서 사용자 및 디바이스 프로필 할당](/mem/intune/configuration/device-profile-assign) 문서를 참조하세요.

1. ‘OS 버전이 Windows 10 Enterprise인 경우 프로필 할당’과 같은 적용 가능성 규칙을 환경에 원하는 대로 구성한 후 **다음** 을 선택합니다.
1. 프로필을 검토한 다음, **만들기** 를 선택합니다.

### <a name="enable-for-windows-10-using-the-registry"></a>레지스트리를 사용하여 Windows 10에 사용

레지스트리 키를 사용하여 로그인 화면에서 SSPR을 사용하도록 설정하려면 다음 단계를 완료합니다.

1. 관리 자격 증명을 사용하여 Windows PC에 로그인합니다.
1. **Windows** + **R** 을 눌러 ‘실행’ 대화 상자를 연 다음, 관리자 권한으로 **regedit** 을 실행합니다.
1. 다음 레지스트리 키를 설정합니다.

    ```cmd
    HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount
       "AllowPasswordReset"=dword:00000001
    ```

#### <a name="troubleshooting-windows-10-password-reset"></a>Windows 10 암호 재설정 문제 해결

Windows 로그인 화면에서 SSPR를 사용하는 데 문제가 있는 경우 다음 예제 출력과 같이 Azure AD 감사 로그에는 암호 재설정이 발생한 IP 주소와 *ClientType* 에 대한 정보가 포함됩니다.

![Azure AD 감사 로그의 Windows 7 암호 재설정 예제](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

사용자가 Windows 10 디바이스 로그인 화면에서 암호를 재설정하면 `defaultuser1`이라는 낮은 권한의 임시 계정이 생성됩니다. 이 계정은 암호 재설정 프로세스를 안전하게 유지하는 데 사용됩니다.

계정 자체는 무작위로 생성된 암호를 가지고 있으며, 디바이스 로그인 시 표시되지 않으며, 사용자가 암호를 설정하면 자동으로 제거됩니다. 여러 `defaultuser` 프로필이 있을 수 있지만 무시해도 무방합니다.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8 및 8.1 암호 재설정

로그인 화면에서 SSPR에 대한 Windows 7, 8, 8.1 디바이스를 구성하려면 다음 필수 조건 및 구성 단계를 검토합니다.

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7, 8 및 8.1 필수 구성 요소

- 관리자는 [Azure Portal에서 Azure AD 셀프 서비스 암호 재설정을 사용하도록 설정해야 합니다](tutorial-enable-sspr.md).
- 사용자는 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)에서 이 기능을 사용하기 전에 SSPR에 등록해야 합니다.
    - Windows 로그인 화면에서 SSPR을 사용하는 경우뿐 아니라, 모든 사용자는 인증 연락처 정보를 제공해야 암호를 재설정할 수 있습니다.
- 네트워크 프록시 요구 사항:
    - 포트 443에서 `passwordreset.microsoftonline.com`
- 패치가 적용된 Windows 7 또는 Windows 8.1 운영 체제
- [TLS(전송 계층 보안) 레지스트리 설정](/windows-server/security/tls/tls-registry-settings#tls-12)에 있는 지침을 사용하여 TLS 1.2 사용하도록 설정
- 머신에 둘 이상의 타사 자격 증명 공급자를 사용하도록 설정한 경우 로그인 화면에서 사용자에게 여러 사용자 프로필이 표시됩니다.

> [!WARNING]
> TLS 1.2를 자동 인식할 뿐만 아니라 사용하도록 설정해야 합니다.

### <a name="install"></a>설치

Windows 7, 8, 8.1의 경우 로그인 화면에서 SSPR을 사용하도록 설정하려면 작은 구성 요소가 머신에 설치되어 있어야 합니다. SSPR 구성 요소를 설치하려면 다음 단계를 완료합니다.

1. 사용할 Windows 버전에 대해 적절한 설치 관리자를 다운로드합니다.

    [https://aka.ms/sspraddin](https://aka.ms/sspraddin)의 Microsoft 다운로드 센터에서 소프트웨어 설치 프로그램을 지원합니다.
1. 설치하고 설치 관리자를 실행할 머신에 로그인합니다.
1. 설치 후에 다시 부팅하는 것이 좋습니다.
1. 다시 부팅한 후에 로그인 화면에서 사용자를 선택하고 “암호 찾기”를 선택합니다. 암호 재설정 워크플로를 시작하려면
1. 암호를 재설정하는 화면 단계를 따라 워크플로를 완료합니다.

![예제 Windows 7 "암호 찾기" 클릭 SSPR 흐름](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>자동 설치

다음 명령을 사용하여 메시지를 표시하지 않고 SSPR 구성 요소를 설치하거나 제거할 수 있습니다.

- 자동 설치의 경우 "msiexec /i SsprWindowsLogon.PROD.msi /qn" 명령을 사용합니다.
- 자동 제거의 경우 "msiexec /x SsprWindowsLogon.PROD.msi /qn" 명령을 사용합니다.

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Windows 7, 8 및 8.1 암호 재설정 문제 해결

Windows 로그인 화면에서 SSPR를 사용하는 데 문제가 있는 경우 이벤트는 머신과 Azure AD 모두에 로그됩니다. Azure AD 이벤트에는 다음 예제 출력과 같이 암호 재설정이 발생한 IP 주소 및 ClientType에 대한 정보가 포함됩니다.

![Azure AD 감사 로그의 Windows 7 암호 재설정 예제](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

추가 로깅이 필요한 경우 머신의 레지스트리 키를 변경하여 자세한 정보 표시 로깅을 사용할 수 있습니다. 다음 레지스트리 키 값을 사용하는 경우에만 문제 해결을 위해 verbose 로깅을 사용하도록 설정합니다.

```cmd
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

- 자세한 정보 표시 로깅을 사용하려면 `REG_DWORD: "EnableLogging"`을 만들어 1로 설정합니다.
- 자세한 정보 로깅을 사용하지 않으려면 `REG_DWORD: "EnableLogging"`을 0으로 변경합니다.

## <a name="what-do-users-see"></a>사용자에게 표시되는 내용

Windows 디바이스에 SSPR을 구성하면 사용자에게 무엇이 바뀌나요? 로그인 화면에서 암호를 재설정할 수 있다는 사실을 사용자가 어떻게 알 수 있을까요? 다음 예제 스크린샷에서는 SSPR을 사용하여 암호를 재설정하는 사용자에 대한 추가 옵션을 보여 줍니다.

![SSPR 링크가 표시된 Windows 7 및 10 로그인 화면 예제](./media/howto-sspr-windows/windows-reset-password.png)

사용자가 로그인을 시도하면 셀프 서비스 암호 재설정 환경을 제공하는 **암호 재설정** 또는 **암호 찾기** 링크가 로그인 화면에 표시됩니다. 사용자는 이 기능을 사용하면 다른 디바이스를 사용하여 웹 브라우저에 액세스할 필요 없이 암호를 재설정할 수 있습니다.

이 기능을 사용하는 사용자를 위한 자세한 내용은 [회사 또는 학교 암호 재설정](../user-help/active-directory-passwords-update-your-own-password.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

사용자 등록 환경을 간소화하기 위해 [SSPR에 대한 사용자 인증 연락처 정보를 미리 입력](howto-sspr-authenticationdata.md)할 수 있습니다.
