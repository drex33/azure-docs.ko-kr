---
title: 온-프레미스 Azure AD 암호 보호 문제 해결
description: 온-프레미스 Active Directory Domain Services 환경의 Azure AD 암호 보호 문제를 해결하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3603bd100fe5d9b471d67896c98de4c0547c8ebb
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113551257"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>문제 해결: 온-프레미스 Azure AD 암호 보호

Azure AD 암호 보호를 배포한 후 문제를 해결해야 할 수도 있습니다. 이 문서에서는 몇 가지 일반적인 문제 해결 단계를 이해하는 데 유용한 세부 정보를 제공합니다.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>DC 에이전트가 디렉터리에서 프록시를 찾을 수 없음

이 문제의 주요 증상은 DC 에이전트 관리 이벤트 로그의 30017 이벤트입니다.

이 문제는 일반적으로 프록시가 아직 등록되지 않은 경우에 발생합니다. 프록시가 등록된 경우 특정 DC 에이전트가 해당 프록시를 볼 수 있을 때까지 AD 복제 대기 시간으로 인해 약간의 지연이 발생할 수 있습니다.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>DC 에이전트가 프록시와 통신할 수 없음

이 문제의 주요 증상은 DC 에이전트 관리 이벤트 로그의 30018 이벤트입니다. 이 문제에는 다음과 같은 몇 가지 원인이 있을 수 있습니다.

1. DC 에이전트가 등록된 프록시에 대한 네트워크 연결을 허용하지 않는 격리된 네트워크 부분에 있습니다. Azure에서 암호 정책을 다운로드하기 위해 다른 DC 에이전트가 프록시와 통신할 수 있으면 이 문제는 심각하지 않을 수 있습니다. 정책을 다운로드한 후에는 격리된 DC가 sysvol 공유의 정책 파일 복제를 통해 해당 정책을 가져옵니다.

1. 프록시 호스트 컴퓨터가 RPC 엔드포인트 매퍼 엔드포인트(포트 135)에 대한 액세스를 차단하고 있습니다.

   Azure AD 암호 보호 프록시 설치 관리자는 포트 135에 대한 액세스를 허용하는 Windows 방화벽 인바운드 규칙을 자동으로 만듭니다. 이 규칙을 나중에 삭제하거나 사용하지 않도록 설정하면 DC 에이전트는 프록시 서비스와 통신할 수 없습니다. 다른 방화벽 제품을 사용하기 위해 기본 제공 Windows 방화벽을 사용하지 않도록 설정한 경우 포트 135에 대한 액세스를 허용하도록 해당 방화벽을 구성해야 합니다.

1. 프록시 호스트 컴퓨터가 프록시 서비스에서 수신 대기하는 RPC 엔드포인트(동적 또는 정적)에 대한 액세스를 차단하고 있습니다.

   Azure AD 암호 보호 프록시 설치 관리자는 Azure AD 암호 보호 프록시 서비스에서 수신 대기하는 인바운드 포트에 대한 액세스를 허용하는 Windows 방화벽 인바운드 규칙을 자동으로 만듭니다. 이 규칙을 나중에 삭제하거나 사용하지 않도록 설정하면 DC 에이전트는 프록시 서비스와 통신할 수 없습니다. 다른 방화벽 제품을 사용하기 위해 기본 제공 Windows 방화벽을 사용하지 않도록 설정한 경우 Azure AD 암호 보호 프록시 서비스에서 수신 대기하는 모든 인바운드 포트에 대한 액세스를 허용하도록 해당 방화벽을 구성해야 합니다. 이 구성은 프록시 서비스가 (`Set-AzureADPasswordProtectionProxyConfiguration` cmdlet을 사용하여) 특정 정적 RPC 포트에서 수신 대기하도록 구성된 경우 더 구체적으로 지정할 수 있습니다.

1. 프록시 호스트 컴퓨터가 도메인 컨트롤러에서 컴퓨터에 로그온할 수 있도록 구성되어 있지 않습니다. 이 동작은 '네트워크에서 이 컴퓨터에 액세스' 권한 할당을 통해 제어됩니다. 포리스트 내 모든 도메인의 모든 도메인 컨트롤러에 이 권한을 부여해야 합니다. 이 설정은 대규모 네트워크 강화 작업의 일부로 제한되는 경우가 많습니다.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>프록시 서비스가 Azure와 통신할 수 없음

1. 프록시 컴퓨터가 [배포 요구 사항](howto-password-ban-bad-on-premises-deploy.md)에 나열된 엔드포인트에 연결되어 있는지 확인합니다.

1. 포리스트와 모든 프록시 서버가 동일한 Azure 테넌트에 등록되었는지 확인합니다.

   `Get-AzureADPasswordProtectionProxy` 및 `Get-AzureADPasswordProtectionDCAgent` PowerShell cmdlet을 실행하여 반환된 각 항목의 `AzureTenant` 속성을 비교하여 이 요구 사항을 확인할 수 있습니다. 올바른 작업인 경우 보고된 테넌트 이름이 모든 DC 에이전트 및 프록시 서버에서 동일해야 합니다.

   Azure 테넌트 등록 불일치 상태가 존재하는 경우 필요에 따라 `Register-AzureADPasswordProtectionProxy` 및/또는 `Register-AzureADPasswordProtectionForest` PowerShell cmdlet을 실행하여 모든 등록에 대해 동일한 Azure 테넌트의 자격 증명을 사용하여 이 문제를 해결할 수 있습니다.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>DC 에이전트가 암호 정책 파일을 암호화하거나 암호 해독할 수 없음

Azure AD 암호 보호는 Microsoft Key Distribution Service에서 제공하는 암호화 및 암호 해독 기능에 중요한 종속성이 있습니다. 암호화 또는 암호 해독 오류는 다양한 증상으로 나타날 수 있으며 몇 가지 가능한 원인이 있습니다.

1. KDS 서비스가 도메인의 모든 Windows Server 2012 이상 도메인 컨트롤러에서 사용하도록 설정되고 작동하는지 확인합니다.

   기본적으로 KDS 서비스의 서비스 시작 모드는 수동(트리거 시작)으로 구성됩니다. 이 구성은 클라이언트가 처음으로 서비스를 사용할 때 요청에 의해 서비스가 시작됨을 의미합니다. 이 기본 서비스 시작 모드에서 Azure AD 암호 보호가 작동할 수 있습니다.

   KDS 서비스 시작 모드가 사용 안 함으로 구성된 경우 이 구성을 수정해야 Azure AD 암호 보호가 제대로 작동할 수 있습니다.

   이 문제에 대한 간단한 테스트는 서비스 관리 MMC 콘솔을 통해 또는 다른 관리 도구(예: 명령 프롬프트 콘솔에서 'net start kdssvc.dll' 실행)를 사용하여 KDS 서비스를 수동으로 시작하는 것입니다. KDS 서비스가 성공적으로 시작되고 계속 실행되어야 합니다.

   KDS 서비스를 시작할 수 없는 가장 일반적인 근본 원인은 Active Directory 도메인 컨트롤러 개체가 기본 도메인 컨트롤러 OU의 외부에 위치하는 것입니다. 이 구성은 KDS 서비스에서 지원하지 않는 것이며 Azure AD 암호 보호에 적용되는 제한 사항이 아닙니다. 이에 대한 해결 방법은 도메인 컨트롤러 개체를 기본 도메인 컨트롤러 OU 아래의 위치로 이동하는 것입니다.

1. Windows Server 2012 R2에서 Windows Server 2016으로의 호환되지 않는 KDS 암호화된 버퍼 형식 변경

   KDS 암호화된 버퍼의 형식을 수정하는 KDS 보안 픽스가 Windows Server 2016에서 도입되었습니다. 이러한 버퍼는 Windows Server 2012 및 Windows Server 2012 R2에서 암호 해독이 실패하는 경우가 있습니다. 역방향은 문제가 없습니다. Windows Server 2012 및 Windows Server 2012 R2에서 KDS 암호화된 버퍼는 Windows Server 2016 이상에서 항상 암호 해독이 성공합니다. Active Directory 도메인의 도메인 컨트롤러에서 이러한 운영 체제를 혼합하여 실행하는 경우 Azure AD 암호 보호 암호 해독 실패가 가끔 보고될 수 있습니다. 보안 픽스의 특성상, 또한 특정 시점에 어느 도메인 컨트롤러의 어느 Azure AD 암호 보호 DC 에이전트가 데이터를 암호화할지 알 수 없으므로 이러한 오류의 타이밍 또는 증상을 정확하게 예측할 수는 없습니다.

   Active Directory 도메인에서 이러한 호환되지 운영 체제를 혼합하여 실행하지 않는 것 외에는 이 문제에 대한 해결 방법이 없습니다. 즉, Windows Server 2012 및 Windows Server 2012 R2 도메인 컨트롤러만 실행하거나 Windows Server 2016 이상 도메인 컨트롤러만 실행해야 합니다.

## <a name="dc-agent-thinks-the-forest-has-not-been-registered"></a>DC 에이전트가 포리스트를 등록되지 않은 것으로 간주

이 문제의 증상은 DC Agent\Admin 채널에 기록되는 30016 이벤트입니다. 다음은 기록되는 내용의 일부입니다.

```text
The forest has not been registered with Azure. Password policies cannot be downloaded from Azure unless this is corrected.
```

이 문제의 경우 두 가지 원인이 있을 수 있습니다.

1. 포리스트가 아직 등록되지 않았습니다. 이 문제를 해결하려면 [배포 요구 사항](howto-password-ban-bad-on-premises-deploy.md)에 설명된 대로 Register-AzureADPasswordProtectionForest 명령을 실행합니다.
1. 포리스트가 등록되었지만 DC 에이전트가 포리스트 등록 데이터를 암호 해독할 수 없습니다. 이 경우는 위의 [DC 에이전트가 암호 정책 파일을 암호화하거나 암호 해독할 수 없음](howto-password-ban-bad-on-premises-troubleshoot.md#dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files)에 나열된 문제 #2와 근본 원인이 동일합니다. 이 이론을 확인하는 쉬운 방법은 Windows Server 2012 또는 Windows Server 2012R2 도메인 컨트롤러에서 실행되는 DC 에이전트만 이 오류가 표시되고, Windows Server 2016 이상 도메인 컨트롤러에서 실행되는 DC 에이전트는 정상인지 확인하는 것입니다. 해결 방법은 동일합니다. 모든 도메인 컨트롤러를 Windows Server 2016 이상으로 업그레이드합니다.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>약한 암호가 허용되지만 그러면 안 됨

이 문제에는 여러 원인이 있을 수 있습니다.

1. DC 에이전트가 만료된 공개 미리 보기 소프트웨어 버전을 실행하고 있습니다. [공개 미리 보기 DC 에이전트 소프트웨어가 만료됨](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired)을 참조하세요.

1. DC 에이전트가 정책을 다운로드할 수 없거나 기존 정책의 암호를 해독할 수 없습니다. 위의 항목에서 가능한 원인을 확인하세요.

1. 암호 정책 강제 적용 모드가 여전히 감사로 설정되어 있습니다. 이 구성이 유효한 경우 Azure AD 암호 보호 포털을 사용하여 적용으로 다시 구성합니다. 자세한 내용은 [작업 모드](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)를 참조하세요.

1. 암호 정책이 사용하지 않도록 설정되어 있습니다. 이 구성이 유효한 경우 Azure AD 암호 보호 포털을 사용하여 사용으로 다시 구성합니다. 자세한 내용은 [작업 모드](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)를 참조하세요.

1. DC 에이전트 소프트웨어를 도메인의 모든 도메인 컨트롤러에 설치하지 않았습니다. 이 경우 암호 변경 작업 중에 원격 Windows 클라이언트가 특정 도메인 컨트롤러를 대상으로 하는지 확인하기 어렵습니다. DC 에이전트 소프트웨어가 설치된 특정 DC를 대상으로 분명히 지정했다고 생각되면 DC 에이전트 관리자 이벤트 로그를 다시 검토하여 확인할 수 있습니다. 결과와 상관없이 암호 유효성 검사의 결과를 문서화하는 이벤트가 하나 이상 있습니다. 암호가 변경된 사용자에게 존재하는 이벤트가 없는 경우 다른 도메인 컨트롤러에서 암호 변경을 처리했을 수 있습니다.

   또 다른 테스트로, DC 에이전트 소프트웨어가 설치된 DC에 직접 로그인한 상태에서 암호 변경을 시도합니다. 프로덕션 환경 Active Directory 도메인에서는 이 방법을 사용할 수 없습니다.

   이러한 제한 사항에 따라 DC 에이전트 소프트웨어 증분 배포를 지원하지만 가능한 한 빨리 도메인의 모든 도메인 컨트롤러에 DC 에이전트 소프트웨어를 설치하는 것이 좋습니다.

1. 암호 유효성 검사 알고리즘이 실제로는 예상대로 작동할 수도 있습니다. [암호 평가 방법](concept-password-ban-bad.md#how-are-passwords-evaluated)을 참조하세요.

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe에서 약한 DSRM 암호를 설정하지 못함

Active Directory는 항상 새 디렉터리 서비스 복구 모드 암호의 유효성을 검사하여 도메인의 암호 복잡성 요구 사항을 충족하는지 확인합니다. 이 유효성 검사는 또한 Azure AD 암호 보호와 같은 암호 필터 dll을 호출합니다. 새 DSRM 암호가 거부되면 다음과 같은 오류 메시지가 나타납니다.

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Azure AD 암호 보호에서 Active Directory DSRM 암호에 대한 암호 유효성 검사 이벤트 로그 이벤트를 기록하는 경우 이벤트 로그 메시지에 사용자 이름이 포함되지 않습니다. 이 동작은 DSRM 계정이 실제 Active Directory 도메인에 속하지 않는 로컬 계정이기 때문에 발생합니다.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>도메인 컨트롤러 복제본 승격이 약한 DSRM 암호로 인해 실패함

DC 승격 프로세스 도중 새 디렉터리 서비스 복구 모드 암호가 유효성 검사를 위해 도메인의 기존 DC로 제출됩니다. 새 DSRM 암호가 거부되면 다음과 같은 오류 메시지가 나타납니다.

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

위의 문제와 마찬가지로, 이 시나리오에서 Azure AD 암호 보호 암호 유효성 검사 결과 이벤트에 사용자 이름이 비어 있습니다.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>도메인 컨트롤러 수준 내리기가 약한 로컬 관리자 암호로 인해 실패함

여전히 DC 에이전트 소프트웨어를 실행하는 도메인 컨트롤러의 수준을 내리기 위해 지원됩니다. 하지만 관리자는 DC 에이전트 소프트웨어가 수준 내리기 절차 중 현재 암호 정책을 계속 실행하고 적용한다는 것을 인식해야 합니다. 새 로컬 관리자 계정 암호(수준 내리기 작업의 일부로 지정됨)는 다른 모든 암호와 같이 유효성을 검사합니다. Microsoft는 DC 수준 내리기 절차의 일부로 로컬 관리자 계정에 대해 보안 암호를 선택할 것을 권장합니다.

수준 내리기가 성공하고 도메인 컨트롤러가 다시 부팅되고 일반 멤버 서버로 다시 실행되면 DC 에이전트 소프트웨어는 수동 모드 실행으로 되돌아갑니다. 언제든 제거될 수 있습니다.

## <a name="booting-into-directory-services-repair-mode"></a>디렉터리 서비스 복구 모드로 부팅

도메인 컨트롤러가 디렉터리 서비스 복구 모드로 부팅되면 DC 에이전트 암호 필터 dll이 이 상태를 감지하여 현재 활성 정책 구성에 관계없이 모든 암호 유효성 검사 또는 적용 활동을 비활성화합니다. DC 에이전트 암호 필터 dll은 관리자 이벤트 로그에 10023 경고 이벤트를 기록합니다. 예:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>공개 미리 보기 DC 에이전트 소프트웨어가 만료됨

Azure AD 암호 보호 공개 미리 보기 기간 동안 DC 에이전트 소프트웨어는 다음 날짜에 암호 유효성 검사 요청 처리를 중단하도록 하드 코딩되었습니다.

* 버전 1.2.65.0은 2019년 9월 1일에 암호 유효성 검사 요청 처리를 중단합니다.
* 버전 1.2.25.0 이하는 2019년 7월 1일에 암호 유효성 검사 요청 처리를 중단했습니다.

최종 기한이 다가오면 모든 한시적 DC 에이전트 버전은 부팅 시 DC 에이전트 관리자 이벤트 로그에 10021 이벤트를 내보냅니다. 예를 들면 다음과 같습니다.

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

최종 기한이 지나면 모든 한시적 DC 에이전트 버전은 부팅 시 DC 에이전트 관리자 이벤트 로그에 10022 이벤트를 내보냅니다. 예를 들면 다음과 같습니다.

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

최종 기한은 최초 부팅 시에만 확인되므로 실제로 최종 기한이 지난 후에도 이러한 이벤트가 표시되지 않을 수 있습니다. 최종 기한이 인식된 후에는 모든 암호가 자동으로 승인되는 것 외에 도메인 컨트롤러 또는 더 큰 규모의 환경에 부정적인 영향을 주지는 않습니다.

> [!IMPORTANT]
> 만료된 공개 미리 보기 DC 에이전트는 즉시 최신 버전으로 업그레이드하는 것이 좋습니다.

사용자 환경에서 업그레이드해야 하는 DC 에이전트를 검색하는 쉬운 방법은 `Get-AzureADPasswordProtectionDCAgent` cmdlet을 실행하는 것입니다. 예를 들면 다음과 같습니다.

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

이 항목의 경우, 당연히 SoftwareVersion 필드가 확인해야 할 키 속성입니다. PowerShell 필터링을 사용하여 이미 필요한 기준 버전보다 높은 DC 에이전트를 필터링할 수도 있습니다. 예를 들면 다음과 같습니다.

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Azure AD 암호 보호 프록시 소프트웨어는 어떤 버전도 한시적이지 않습니다. DC 및 프록시 에이전트는 모두 릴리스되는 대로 최신 버전으로 업그레이드하는 것이 좋습니다. 위의 DC 에이전트 예제와 비슷하게 `Get-AzureADPasswordProtectionProxy` cmdlet을 사용하여 업그레이드가 필요한 프록시 에이전트를 찾을 수 있습니다.

구체적인 업그레이드 절차는 [DC 에이전트 업그레이드](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) 및 [프록시 서비스 업그레이드](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service)를 참조하세요.

## <a name="emergency-remediation"></a>응급 업데이트 관리

DC 에이전트 서비스가 문제를 일으키는 상황이 발생하면 DC 에이전트 서비스는 즉시 종료될 수 있습니다. DC 에이전트 암호 필터 dll은 여전히 실행 중이 아닌 서비스를 호출하고 경고 이벤트(10012, 10013)를 기록하지만 해당 시간 동안 들어오는 모든 암호는 허용됩니다. DC 에이전트 서비스는 필요에 따라 "Disabled" 시작 유형을 사용하여 Windows 서비스 제어 관리자를 통해 구성될 수 있습니다.

다른 수정 측정값을 Azure AD 암호 보호 포털에서 사용 모드를 안 함으로 설정할 수 있습니다. 업데이트된 정책이 다운로드되면, 각 DC 에이전트 서비스는 모든 암호가 있는 그대로 허용되는 정지 모드가 됩니다. 자세한 내용은 [작업 모드](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)를 참조하세요.

## <a name="removal"></a>제거

Azure AD 암호 보호 소프트웨어를 제거하고 도메인 및 포리스트에서 모든 관련 상태를 정리하기로 결정한 경우 다음 단계를 사용하여 이 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 이러한 단계를 순서대로 수행하는 것이 중요합니다. 프록시 서비스의 인스턴스가 실행되는 경우 주기적으로 해당 serviceConnectionPoint 개체를 다시 만듭니다. DC 에이전트 서비스의 인스턴스가 실행되는 경우 주기적으로 해당 serviceConnectionPoint 개체 및 sysvol 상태를 다시 만듭니다.

1. 모든 머신에서 프록시 소프트웨어를 제거합니다. 이 단계는 재부팅이 필요하지 **않습니다**.
2. 모든 도메인 컨트롤러에서 DC 에이전트 소프트웨어를 제거합니다. 이 단계는 재부팅이 **필요합니다**.
3. 각 도메인 명명 컨텍스트에서 모든 프록시 서비스 연결점을 수동으로 제거합니다. 다음 Active Directory PowerShell 명령을 사용하여 이러한 개체의 위치를 검색할 수 있습니다.

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   $keywords 변수 값의 끝에 별표("*")를 생략하지 마십시오.

   `Get-ADObject` 명령을 통해 찾은 결과 개체는 `Remove-ADObject`에 파이프되거나 수동으로 삭제될 수 있습니다.

4. 각 도메인 명명 컨텍스트에서 모든 DC 에이전트 연결점을 수동으로 제거합니다. 소프트웨어가 얼마나 넓게 배포됐는가에 따라 포리스트 내 도메인 컨트롤러마다 이러한 개체 중 하나가 있을 수 있습니다. 다음 Active Directory PowerShell 명령을 사용하여 해당 개체의 위치를 검색할 수 있습니다.

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   `Get-ADObject` 명령을 통해 찾은 결과 개체는 `Remove-ADObject`에 파이프되거나 수동으로 삭제될 수 있습니다.

   $keywords 변수 값의 끝에 별표("*")를 생략하지 마십시오.

5. 포리스트 수준의 구성 상태를 수동으로 제거합니다. 포리스트 구성 상태는 Active Directory 구성 명명 컨텍스트의 컨테이너에 유지됩니다. 다음과 같이 검색 및 삭제될 수 있습니다.

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. 수동으로 다음 폴더와 해당 내용 모두를 삭제하여 모든 sysvol 관련 상태를 수동으로 제거합니다.

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   필요한 경우 이 경로는 기존 도메인 컨트롤러에서 로컬로 액세스될 수도 있으며, 기본 위치는 다음 경로와 같이 표시됩니다.

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Sysvol 공유가 기본이 아닌 위치에 구성된 경우 이 경로는 다릅니다.

## <a name="health-testing-with-powershell-cmdlets"></a>PowerShell cmdlet을 사용하여 상태 테스트

AzureADPasswordProtection PowerShell 모듈에는 소프트웨어가 설치되어 작동하고 있는지에 대한 기본 확인을 수행하는 두 가지 상태 관련 cmdlet이 포함되어 있습니다. 새 배포를 설정한 후, 그 이후 정기적으로, 그리고 문제를 조사할 때 이러한 cmdlet을 실행하는 것이 좋습니다.

각 개별 상태 테스트는 기본적인 성공 또는 실패 결과 이외에 오류 발생 시 선택적 메시지를 반환합니다. 오류의 원인이 명확하지 않은 경우 오류를 설명하는 오류 이벤트 로그 메시지를 확인하세요. 텍스트 로그 메시지를 사용하는 것도 유용할 수 있습니다. 자세한 내용은 [Azure AD 암호 보호 모니터링](howto-password-ban-bad-on-premises-monitor.md)을 참조하세요.

## <a name="proxy-health-testing"></a>프록시 상태 테스트

Test-AzureADPasswordProtectionProxyHealth cmdlet은 개별적으로 실행할 수 있는 두 가지 상태 테스트를 지원합니다. 세 번째 모드에서는 매개 변수 입력이 필요하지 않은 모든 테스트를 실행할 수 있습니다.

### <a name="proxy-registration-verification"></a>프록시 등록 확인

이 테스트는 프록시 에이전트가 Azure에 제대로 등록되고 Azure에 인증할 수 있는지 확인합니다. 실행이 성공하면 다음과 같이 표시됩니다.

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -VerifyProxyRegistration

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyRegistration Passed
```

오류가 검색되면 테스트가 실패 결과와 선택적 오류 메시지를 반환합니다. 다음은 가능한 오류의 예입니다.

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -VerifyProxyRegistration

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyRegistration Failed No proxy certificates were found - please run the Register-AzureADPasswordProtectionProxy cmdlet to register the proxy.
```

### <a name="proxy-verification-of-end-to-end-azure-connectivity"></a>엔드투엔드 Azure 연결의 프록시 확인

이 테스트는 -VerifyProxyRegistration 테스트의 상위 집합입니다. 프록시 에이전트가 Azure에 올바르게 등록되고 Azure에 인증할 수 있어야 하며, 마지막으로 메시지가 Azure로 전송될 수 있는지 따라서 전체 엔드투엔드 통신이 작동하는지 확인하는 검사가 추가됩니다.

실행이 성공하면 다음과 같이 표시됩니다.

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -VerifyAzureConnectivity

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyAzureConnectivity Passed
```

### <a name="proxy-verification-of-all-tests"></a>모든 테스트의 프록시 확인

이 모드에서는 매개 변수 입력이 필요하지 않은 cmdlet에서 지원되는 모든 테스트를 대량으로 실행할 수 있습니다. 실행이 성공하면 다음과 같이 표시됩니다.

```powershell
PS C:\> Test-AzureADPasswordProtectionProxyHealth -TestAll

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyTLSConfiguration  Passed
VerifyProxyRegistration Passed
VerifyAzureConnectivity Passed
```

## <a name="dc-agent-health-testing"></a>DC 에이전트 상태 테스트

Test-AzureADPasswordProtectionDCAgentHealth cmdlet은 개별적으로 실행할 수 있는 몇 가지 상태 테스트를 지원합니다. 세 번째 모드에서는 매개 변수 입력이 필요하지 않은 모든 테스트를 실행할 수 있습니다.

### <a name="basic-dc-agent-health-tests"></a>기본 DC 에이전트 상태 테스트

다음 테스트는 모두 개별적으로 실행할 수 있으며 매개변수를 허용하지 않습니다. 각 테스트에 대한 간략한 설명은 다음 표에 나와 있습니다.

|DC 에이전트 상태 테스트|설명|
| --- | :---: |
|-VerifyPasswordFilterDll|암호 필터 dll이 현재 로드되어 있고 DC 에이전트 서비스를 호출할 수 있는지 확인합니다.|
|-VerifyForestRegistration|포리스트가 현재 등록되어 있는지 확인합니다.|
|-VerifyEncryptionDecryption|기본 암호화 및 암호 해독이 Microsoft KDS 서비스를 사용하여 작동하는지 확인합니다.|
|-VerifyDomainIsUsingDFSR|현재 도메인이 sysvol 복제를 위해 DFSR을 사용하는지 확인합니다.|
|-VerifyAzureConnectivity|Azure와의 엔드투엔드 통신이 사용 가능한 프록시를 사용하여 작동하는지 확인합니다.|

다음은 -VerifyPasswordFilterDll 테스트 통과의 예입니다. 다른 테스트도 성공 시 비슷하게 표시됩니다.

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyPasswordFilterDll

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyPasswordFilterDll Passed
```

### <a name="dc-agent-verification-of-all-tests"></a>모든 테스트의 DC 에이전트 확인

이 모드에서는 매개 변수 입력이 필요하지 않은 cmdlet에서 지원되는 모든 테스트를 대량으로 실행할 수 있습니다. 실행이 성공하면 다음과 같이 표시됩니다.

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -TestAll

DiagnosticName             Result AdditionalInfo
--------------             ------ --------------
VerifyPasswordFilterDll    Passed
VerifyForestRegistration   Passed
VerifyEncryptionDecryption Passed
VerifyDomainIsUsingDFSR    Passed
VerifyAzureConnectivity    Passed
```

### <a name="connectivity-testing-using-specific-proxy-servers"></a>특정 프록시 서버를 사용한 연결 테스트

많은 문제 해결 상황에는 DC 에이전트와 프록시 간의 네트워크 연결을 조사하는 작업이 포함됩니다. 이러한 문제에 초점을 맞춘 두 가지 상태 테스트를 사용할 수 있습니다. 이러한 테스트를 수행하려면 특정 프록시 서버를 지정해야 합니다.

#### <a name="verifying-connectivity-between-a-dc-agent-and-a-specific-proxy"></a>DC 에이전트와 특정 프록시 간의 연결 확인

이 테스트는 DC 에이전트에서 프록시까지 첫 번째 통신 구간에서 연결의 유효성을 검사합니다. 프록시가 호출을 수신하는지 확인하지만 Azure와의 통신은 포함되지 않습니다. 실행이 성공하면 다음과 같이 표시됩니다.

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyProxyConnectivity bpl2.bpl.com

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyConnectivity Passed
```

대상 서버에서 실행 중인 프록시 서비스가 중지된 실패 상태의 예는 다음과 같습니다.

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyProxyConnectivity bpl2.bpl.com

DiagnosticName          Result AdditionalInfo
--------------          ------ --------------
VerifyProxyConnectivity Failed The RPC endpoint mapper on the specified proxy returned no results; please check that the proxy service is running on that server.
```

#### <a name="verifying-connectivity-between-a-dc-agent-and-azure-using-a-specific-proxy"></a>DC 에이전트와 Azure 간의 연결 확인(특정 프록시를 사용)

이 테스트는 특정 프록시를 사용하여 DC 에이전트와 Azure 간의 전체 엔드투엔드 연결의 유효성을 검사합니다. 실행이 성공하면 다음과 같이 표시됩니다.

```powershell
PS C:\> Test-AzureADPasswordProtectionDCAgentHealth -VerifyAzureConnectivityViaSpecificProxy bpl2.bpl.com

DiagnosticName                          Result AdditionalInfo
--------------                          ------ --------------
VerifyAzureConnectivityViaSpecificProxy Passed
```

## <a name="next-steps"></a>다음 단계

[Azure AD 암호 보호에 대한 질문과 대답](howto-password-ban-bad-on-premises-faq.yml)

전역 및 사용자 지정 금지된 암호 목록에 대한 자세한 내용은 [잘못된 암호 금지](concept-password-ban-bad.md) 문서 참조
