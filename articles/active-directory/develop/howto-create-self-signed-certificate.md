---
title: 자체 서명된 공용 인증서를 만들어 애플리케이션 인증 | Azure
titleSuffix: Microsoft identity platform
description: 자체 서명된 공용 인증서를 만들어 애플리케이션을 인증합니다.
services: active-directory
author: FaithOmbongi
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/10/2021
ms.author: ombongifaith
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: scenarios:getting-started
ms.openlocfilehash: 55076f382b24c8bcf994b452e8c26e5bb6cc23b9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536991"
---
# <a name="how-to-create-a-self-signed-public-certificate-to-authenticate-your-application"></a>방법: 자체 서명된 공용 인증서를 만들어 애플리케이션 인증

Azure AD(Azure Active Directory)는 **암호 기반 인증**(앱 비밀)과 **인증서 기반 인증** 등 두 가지 유형의 서비스 사용자 인증을 지원합니다. 앱 비밀을 Azure Portal에서 간편하게 만들 수 있지만 애플리케이션에서 인증서를 사용하는 것이 좋습니다.

테스트를 위해 CA(인증 기관)에서 서명한 인증서 대신 자체 서명된 공용 인증서를 사용할 수 있습니다. 이 문서에서는 Windows PowerShell을 사용하여 자체 서명된 인증서를 만들고 내보내는 방법을 설명합니다.

> [!CAUTION]
> 프로덕션이 아닌 개발에 자체 서명된 인증서를 사용하는 것이 좋습니다.

다양한 인증서 매개 변수를 구성합니다. 예를 들어 암호화 및 해시 알고리즘, 인증서 유효 기간 및 도메인 이름이 있습니다. 그런 다음, 애플리케이션 요구 사항에 따라 프라이빗 키 유무에 관계없이 인증서를 내보냅니다. 

인증 세션을 시작하는 애플리케이션에는 프라이빗 키가 필요하고 인증을 확인하는 애플리케이션에는 공개 키가 필요합니다. 따라서 PowerShell 데스크톱 앱에서 Azure AD로 인증하는 경우 공개 키(`.cer` 파일)만 내보내 Azure Portal에 업로드합니다. PowerShell 앱은 로컬 인증서 저장소의 프라이빗 키를 사용하여 인증을 시작하고 Microsoft Graph의 액세스 토큰을 가져옵니다.

애플리케이션이 Azure Automation과 같은 다른 컴퓨터에서 실행 중일 수도 있습니다. 이 시나리오에서는 로컬 인증서 저장소에서 공개 키 및 프라이빗 키 쌍을 내보내고 공개 키를 Azure Portal에, 프라이빗 키(`.pfx` 파일)를 Azure Automation에 업로드합니다. Azure Automation에서 실행되는 애플리케이션은 프라이빗 키를 사용하여 인증을 시작하고 Microsoft Graph의 액세스 토큰을 가져옵니다.

이 문서에서는 `New-SelfSignedCertificate` PowerShell cmdlet를 사용하여 자체 서명된 인증서와 `Export-Certificate` cmdlet를 만들어 간편하게 액세스할 수 있는 위치로 내보냅니다. 이러한 cmdlet는 최신 버전의 Windows(Windows 8.1 이상 및 Windows Server 2012R2 이상)에서 기본 제공됩니다. 자체 서명된 인증서에는 다음과 같은 구성이 있습니다.

+ 2048비트 키 길이. 더 긴 값을 지원하지만 최고의 보안 및 성능 조합을 위해 2048비트 크기를 사용하는 것이 좋습니다.
+ RSA 암호화 알고리즘을 사용합니다. Azure AD는 현재 RSA만 지원합니다.
+ 인증서는 SHA256 해시 알고리즘으로 서명됩니다. Azure AD는 SHA384 및 SHA512 해시 알고리즘으로 서명된 인증서도 지원합니다.
+ 인증서 유효 기간은 1년입니다.
+ 인증서를 클라이언트 및 서버 인증 모두에 사용할 수 있습니다.

> [!NOTE]
> 인증서의 시작 날짜와 만료 날짜뿐만 아니라 다른 속성을 사용자 지정하려면 [`New-SelfSignedCertificate` 참조](/powershell/module/pki/new-selfsignedcertificate?view=windowsserver2019-ps&preserve-view=true)를 참조하세요.


## <a name="option-1--create-and-export-your-public-certificate-without-a-private-key"></a>옵션 1: 프라이빗 키를 사용하지 않고 공용 인증서 만들기 및 내보내기

이 방법으로 만든 인증서를 사용하여 컴퓨터에서 실행되는 애플리케이션에서 인증합니다. 예를 들어 Windows PowerShell에서 인증합니다.

관리자 권한 PowerShell 프롬프트에서 다음 명령을 실행하고 PowerShell 콘솔 세션을 열어 둡니다. `{certificateName}`을 인증서에 제공하려는 이름으로 바꿉니다.

```powershell

$cert = New-SelfSignedCertificate -Subject "CN={certificateName}" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256    ## Replace {certificateName}

```

이전 명령의 **$cert** 변수는 인증서를 현재 세션에 저장하고 내보낼 수 있게 합니다. 다음 명령은 인증서를 `.cer` 형식으로 내보냅니다. `.pem` 및 `.crt`를 포함하여 Azure Portal에서 지원되는 다른 형식으로 내보낼 수도 있습니다.

```powershell

Export-Certificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{certificateName}.cer"   ## Specify your preferred location and replace {certificateName}

```

이제 인증서를 Azure Portal에 업로드할 수 있습니다. 업로드되면 애플리케이션을 인증하는 데 사용할 인증서 지문을 검색합니다.


## <a name="option-2-create-and-export-your-public-certificate-with-its-private-key"></a>옵션 2: 프라이빗 키를 사용하여 공용 인증서 만들기 및 내보내기

애플리케이션이 다른 컴퓨터나 클라우드(예: Azure Automation)에서 실행되는 경우 이 옵션을 사용하여 인증서와 프라이빗 키를 만듭니다.

관리자 권한 PowerShell 프롬프트에서 다음 명령을 실행하고 PowerShell 콘솔 세션을 열어 둡니다. `{certificateName}`을 인증서를 제공하려는 이름으로 바꿉니다.

```powershell

$cert = New-SelfSignedCertificate -Subject "CN={certificateName}" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256    ## Replace {certificateName}

```

이전 명령의 **$cert** 변수는 인증서를 현재 세션에 저장하고 내보낼 수 있게 합니다. 다음 명령은 인증서를 `.cer` 형식으로 내보냅니다. `.pem` 및 `.crt`를 포함하여 Azure Portal에서 지원되는 다른 형식으로 내보낼 수도 있습니다.


```powershell

Export-Certificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{certificateName}.cer"   ## Specify your preferred location and replace {certificateName}

```

동일한 세션에서 인증서 프라이빗 키의 암호를 만들고 변수에 저장합니다. 다음 명령에서 `{myPassword}`를 인증서 프라이빗 키를 보호하는 데 사용하려는 암호로 바꿉니다.

```powershell

$mypwd = ConvertTo-SecureString -String "{myPassword}" -Force -AsPlainText  ## Replace {myPassword}

```

이제 `$mypwd` 변수에 저장한 암호를 사용하여 프라이빗 키를 보호하고 내보냅니다.

```powershell

Export-PfxCertificate -Cert $cert -FilePath "C:\Users\admin\Desktop\{privateKeyName}.pfx" -Password $mypwd   ## Specify your preferred location and replace {privateKeyName}

```

이제 인증서(`.cer` 파일)를 Azure Portal에 업로드할 수 있습니다. 또한 암호화되어 다른 당사자가 읽을 수 없는 프라이빗 키(`.pfx` 파일)도 있습니다. 업로드되면 애플리케이션을 인증하는 데 사용할 인증서 지문을 검색합니다.


## <a name="optional-task-delete-the-certificate-from-the-keystore"></a>선택적 작업: 키 저장소에서 인증서 삭제

옵션 2를 수행하여 인증서를 만든 경우 개인 저장소에서 키 쌍을 삭제할 수 있습니다. 먼저 다음 명령을 실행하여 인증서 지문을 검색합니다.

```powershell

Get-ChildItem -Path "Cert:\CurrentUser\My" | Where-Object {$_.Subject -Match "{certificateName}"} | Select-Object Thumbprint, FriendlyName    ## Replace {privateKeyName} with the name you gave your certificate

```

그런 다음, 표시된 지문을 복사하고 사용하여 인증서와 해당 프라이빗 키를 삭제합니다.

```powershell

Remove-Item -Path Cert:\CurrentUser\My\{pasteTheCertificateThumbprintHere} -DeleteKey

```

### <a name="know-your-certificate-expiry-date"></a>인증서 만료 날짜 확인

위 단계를 수행하여 만든 자체 서명된 인증서의 수명은 만료 전으로 제한됩니다. Azure Portal **앱 등록** 섹션의 **인증서 및 비밀** 화면에 인증서 만료 날짜가 표시됩니다. Azure Automation을 사용하는 경우 Automation 계정의 **인증서** 화면에 인증서 만료 날짜가 표시됩니다. 이전 단계를 수행하여 자체 서명된 새 인증서를 만듭니다.

## <a name="next-steps"></a>다음 단계

[Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
