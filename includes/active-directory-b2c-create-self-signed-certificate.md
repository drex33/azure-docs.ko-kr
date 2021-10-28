---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: kengaderdus
ms.openlocfilehash: 90c564956db3cfba02c9adee8c4f2fa2c5bac4fa
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130050787"
---
인증서가 아직 없는 경우 자체 서명된 인증서를 사용할 수 있습니다. 자체 서명된 인증서는 CA(인증 기관)가 서명하지 않은 보안 인증서이며, CA가 서명한 인증서의 보안 보장을 제공하지 않습니다. 

# <a name="windows"></a>[Windows](#tab/windows)

Windows에서는 PowerShell에서 [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate) cmdlet을 사용하여 인증서를 생성합니다.

1. 다음 PowerShell 명령을 실행하여 자체 서명된 인증서를 실행합니다. `-Subject` 인수를 애플리케이션 및 Azure AD B2C 테넌트 이름에 적절하게 수정합니다. `-NotAfter` 날짜를 조정하여 인증서에 다른 만료 날짜를 지정할 수도 있습니다.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. **사용자 인증서 관리** > **현재 사용자** > **개인** > **인증서** > *yourappname.yourtenant.onmicrosoft.com* 을 엽니다.
1. 인증서를 선택한 다음 **작업** > **모든 작업** > **내보내기** 를 선택합니다.
1. **예** > **다음** > **예, 프라이빗 키를 내보냅니다.**  > **다음** 을 선택합니다.
1. **내보내기 파일 형식** 의 기본값을 적용합니다.
1. 인증서의 암호를 제공합니다.

Azure AD B2C가 .pfx 파일 암호를 수락하려면 AES256-SHA256이 아니라 Windows 인증서 저장소 내보내기 유틸리티의 TripleDES-SHA1 옵션을 사용하여 암호를 암호화해야 합니다.

# <a name="macos"></a>[macOS](#tab/macos)

macOS에서는 키체인 접근의 [인증서 지원](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0)을 사용하여 인증서를 생성합니다.

1. [Mac의 키 집합 액세스에서 자체 서명 인증서 만들기](https://support.apple.com/guide/keychain-access/kyca8916/mac) 방법의 지침을 따르세요.
1. Mac의 키 집합 액세스 앱에서 만든 인증서를 선택합니다.
1. **파일** > **항목 내보내기** 를 선택합니다.
1. 인증서를 저장할 파일 이름을 선택합니다. 예: **self-signed-certificate.p12**.
1. **파일 형식** 으로 **개인 정보 교환(.p12)** 을 선택합니다.
1. **저장** 을 선택합니다.
1. **암호** 및 **확인** 상자에 암호를 입력합니다.
1. 파일 확장명을 .pfx로 바꿉니다. 예: **self-signed-certificate.pfx**.

---
