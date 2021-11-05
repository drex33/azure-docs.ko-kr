---
title: Azure AD의 Azure AD Connect 클라우드 동기화에 대한 필수 구성 요소
description: 이 문서에서는 클라우드 동기화에 필요한 필수 조건 및 하드웨어 요구 사항을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31850f855cdf109e2337898736d273237ff65058
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018204"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-sync"></a>Azure AD Connect 클라우드 동기화에 대한 필수 구성 요소
이 문서에서는 ID 솔루션으로 Azure AD(Azure Active Directory) Connect 클라우드 동기화를 선택하고 사용하는 방법에 대한 지침을 제공합니다.

## <a name="cloud-provisioning-agent-requirements"></a>클라우드 프로비저닝 에이전트 요구 사항
Azure AD Connect 클라우드 동기화를 사용하려면 다음이 필요합니다.

- 에이전트 서비스를 실행을 위해 Azure AD Connect Cloud Sync gMSA(그룹 관리 서비스 계정)를 만드는 데 필요한 도메인 관리자 또는 엔터프라이즈 관리자 자격 증명. 
- 게스트 사용자가 아닌 Azure AD 테넌트의 전역 관리자 계정.
- Windows 2016 이상 버전을 사용하는 프로비저닝 에이전트에 대한 온-프레미스 서버.  이 서버는 [Active Directory 관리 계층 모델](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)을 기준으로 계층 0 서버여야 합니다.  도메인 컨트롤러에 에이전트 설치가 지원됩니다.
- 고가용성은 Azure AD Connect 클라우드 동기화가 오랜 시간 동안 장애 없이 지속적으로 작동할 수 있는 기능을 말합니다.  여러 활성 에이전트를 설치하고 실행하면 하나의 에이전트가 실패하더라도 Azure AD Connect 클라우드 동기화가 계속 작동할 수 있습니다.  Microsoft는 고가용성을 위해 3개의 활성 에이전트를 설치할 것을 권장합니다.
- 온-프레미스 방화벽 구성

## <a name="group-managed-service-accounts"></a>Group Managed Service Accounts
그룹 관리 서비스 계정은 자동 암호 관리, 간소화된 SPN(서비스 사용자 이름) 관리, 다른 관리자에게 관리를 위임하는 기능 및 여러 서버에서 이 기능을 확장하는 관리되는 도메인 계정입니다.  Azure AD Connect Cloud Sync는 에이전트를 실행하는 데 gMSA을 지원 및 사용합니다.  이 계정을 만들기 위해 설치 도중 관리자 자격 증명을 입력하라는 메시지가 표시됩니다.  계정이 (domain\provAgentgMSA$)로 표시됩니다.  gMSA에 대한 자세한 내용은 [그룹 관리 서비스 계정](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)을 참조하세요. 

### <a name="prerequisites-for-gmsa"></a>gMSA에 대한 필수 구성 요소:
1.  gMSA 도메인 포리스트의 Active Directory 스키마를 Windows Server 2012 이상으로 업데이트해야 합니다.
2.  도메인 컨트롤러의 [PowerShell RSAT 모듈](/windows-server/remote/remote-server-administration-tools)
3.  도메인에 있는 하나 이상의 도메인 컨트롤러가 Windows Server 2012 이상을 실행 중이어야 합니다.
4.  에이전트가 설치되는 도메인 가입 서버가 Windows Server 2016 이상이어야 합니다.

### <a name="custom-gmsa-account"></a>사용자 지정 gMSA 계정
사용자 지정 gMSA 계정을 만드는 경우 계정에 다음 사용 권한이 있는지 확인해야 합니다.

|Type |속성 |액세스 권한 |적용 대상| 
|-----|-----|-----|-----|
|Allow |gMSA 계정 |모든 속성 읽기 |하위 디바이스 개체| 
|Allow |gMSA 계정|모든 속성 읽기 |하위 InetOrgPerson 개체| 
|Allow |gMSA 계정 |모든 속성 읽기 |하위 Computer 개체| 
|Allow |gMSA 계정 |모든 속성 읽기 |하위 foreignSecurityPrincipal 개체| 
|Allow |gMSA 계정 |모든 권한 |하위 Group 개체| 
|Allow |gMSA 계정 |모든 속성 읽기 |하위 User 개체| 
|Allow |gMSA 계정 |모든 속성 읽기 |하위 Contact 개체| 
|허용 |gMSA 계정 |사용자 개체 만들기/삭제|이 개체 및 모든 하위 개체| 

gMSA 계정을 사용하도록 기존 에이전트를 업그레이드하는 방법에 대한 단계는 [그룹 관리 서비스 계정](how-to-install.md#group-managed-service-accounts)을 참조하세요.

#### <a name="create-gmsa-account-with-powershell"></a>PowerShell로 gMSA 계정 만들기
다음 PowerShell 스크립트를 사용하여 사용자 지정 gMSA 계정을 만들 수 있습니다.  그런 다음 [클라우드 동기화 gMSA cmdlet](how-to-gmsa-cmdlets.md)을 사용하여 더 세분화된 권한을 적용할 수 있습니다.

```powershell
# Filename:    1_SetupgMSA.ps1
# Description: Creates and installs a custom gMSA account for use with Azure AD Connect cloud sync.
#
# DISCLAIMER:
# Copyright (c) Microsoft Corporation. All rights reserved. This 
# script is made available to you without any express, implied or 
# statutory warranty, not even the implied warranty of 
# merchantability or fitness for a particular purpose, or the 
# warranty of title or non-infringement. The entire risk of the 
# use or the results from the use of this script remains with you.
#
#
#
#
# Declare variables
$Name = 'provAPP1gMSA'
$Description = "Azure AD Cloud Sync service account for APP1 server"
$Server = "APP1.contoso.com"
$Principal = Get-ADGroup 'Domain Computers'

# Create service account in Active Directory
New-ADServiceAccount -Name $Name `
-Description $Description `
-DNSHostName $Server `
-ManagedPasswordIntervalInDays 30 `
-PrincipalsAllowedToRetrieveManagedPassword $Principal `
-Enabled $True `
-PassThru

# Install the new service account on Azure AD Cloud Sync server
Install-ADServiceAccount -Identity $Name
```

위의 cmdlet에 대한 자세한 내용은 [그룹 관리 서비스 계정 시작하기](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)?redirectedfrom=MSDN)를 참조하세요.

### <a name="in-the-azure-active-directory-admin-center"></a>Azure Active Directory 관리 센터에서

1. Azure AD 테넌트에서 클라우드 전용 하이브리드 ID 관리자 계정을 만듭니다. 이러한 방식으로 온-프레미스 서비스가 실패하거나 사용할 수 없게 되면 테넌트의 구성을 관리할 수 있습니다. [클라우드 전용 하이브리드 ID 관리자 계정 추가](../fundamentals/add-users-azure-active-directory.md) 방법에 대해 자세히 알아봅니다. 테넌트에서 잠기지 않도록 하려면 이 단계를 완료하는 것이 중요합니다.
1. Azure AD 테넌트에 [사용자 지정 도메인 이름](../fundamentals/add-custom-domain.md)을 하나 이상 추가합니다. 사용자는 이러한 도메인 이름 중 하나로 로그인할 수 있습니다.

### <a name="in-your-directory-in-active-directory"></a>Active Directory의 디렉터리에서

[IdFix 도구](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)를 실행하여 동기화를 위한 디렉터리 특성을 준비합니다.

### <a name="in-your-on-premises-environment"></a>온-프레미스 환경에서

1. 4GB 이상의 RAM 및 .NET 4.7.1 이상의 런타임을 사용하여 Windows Server 2016 이상을 실행하는 도메인 조인 호스트 서버를 식별합니다.

2. 로컬 서버에 대한 PowerShell 실행 정책을 Undefined 또는 RemoteSigned로 설정해야 합니다.

3. 서버와 Azure AD 사이에 방화벽이 있는 경우 다음 항목을 구성합니다.
    - 에이전트에서 다음 포트를 통해 Azure AD에 대한 *아웃바운드* 요청을 수행할 수 있는지 확인합니다.

      | 포트 번호 | 사용 방법 |
      | --- | --- |
      | **80** | TLS/SSL 인증서의 유효성을 검사하는 동안 CRL(인증서 해지 목록)을 다운로드합니다.  |
      | **443** | 서비스와의 모든 아웃바운드 통신을 처리합니다. |
      | **8080**(선택 사항) | 443 포트를 사용할 수 없는 경우 에이전트는 8080 포트를 통해 10분마다 해당 상태를 보고합니다. 이 상태는 Azure AD 포털에 표시됩니다. |

    - 방화벽이 원래 사용자에 따라 규칙에 적용되는 경우 네트워크 서비스로 실행하는 Windows 서비스의 트래픽에 대해 이러한 포트를 엽니다.
    - 방화벽 또는 프록시를 통해 안전한 접미사를 지정할 수 있으면 \*.msappproxy.net 및 \*.servicebus.windows.net에 대한 연결을 추가합니다. 그렇지 않으면 매주 업데이트되는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 액세스하도록 허용합니다.
    - 에이전트는 초기 등록을 위해 login.windows.net 및 login.microsoftonline.com에 액세스해야 합니다. 이러한 URL에 대한 방화벽도 엽니다.
    - 인증서 유효성 검사를 위해 mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 및 www\.microsoft.com:80 URL을 차단 해제합니다. 이러한 URL은 다른 Microsoft 제품과의 인증서 유효성 검사에 사용되므로 이러한 URL을 이미 차단 해제했을 수 있습니다.

    >[!NOTE]
    > Windows Server Core에 클라우드 프로저닝 에이전트를 설치하는 것은 지원되지 않습니다.

### <a name="additional-requirements"></a>추가 요구 사항

- [Microsoft .NET Framework 4.7.1](https://dotnet.microsoft.com/download/dotnet-framework/net471) 

#### <a name="tls-requirements"></a>TLS 요구 사항

> [!NOTE]
> TLS(전송 계층 보안)는 보안 통신을 지원하는 프로토콜입니다. TLS 설정을 변경하면 전체 포리스트에 영향을 줍니다. 자세한 내용은 [TLS 1.1 및 TLS 1.2를 Windows의 WinHTTP에서 기본 보안 프로토콜로 사용하는 업데이트](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)를 참조하세요.

Azure AD Connect 클라우드 프로비저닝 에이전트를 호스트하는 Windows Server를 설치하기 전에 TLS 1.2를 사용하도록 설정해야 합니다.

TLS 1.2를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. 다음 레지스트리 키를 설정합니다.

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. 서버를 다시 시작합니다.
## <a name="ntlm-requirement"></a>NTLM 요구 사항

Azure AD Connect 프로비저닝 에이전트를 실행하는 Windows Server에서 NTLM을 활성화하면 안 됩니다. 활성화된 경우에는 비활성화해야 합니다. 

## <a name="known-limitations"></a>알려진 제한 사항

알려진 제한 사항은 다음과 같습니다.

### <a name="delta-synchronization"></a>델타 동기화

- 델타 동기화에 대한 그룹 범위 필터링은 50,000명 넘는 멤버를 지원하지 않습니다.
- 그룹 범위 지정 필터의 일부로 사용되는 그룹을 삭제하는 경우 그룹의 멤버인 사용자는 삭제되지 않습니다. 
- 범위에 있는 OU 또는 그룹의 이름을 바꾸면 델타 동기화에서 사용자가 제거되지 않습니다.

### <a name="provisioning-logs"></a>프로비저닝 로그
- 프로비저닝 로그는 만들기 및 업데이트 작업을 명확하게 구분하지 않습니다.  업데이트에 대한 만들기 작업과 만들기에 대한 업데이트 작업이 표시될 수 있습니다.

### <a name="group-re-naming-or-ou-re-naming"></a>그룹 이름 바꾸기 또는 OU 이름 바꾸기
- 지정된 구성의 범위에 속하는 AD의 그룹 또는 OU의 이름을 바꾸는 경우 클라우드 동기화 작업에서 AD의 이름 변경을 인식할 수 없습니다. 작업은 격리되지 않으며 정상 상태로 유지됩니다.

### <a name="scoping-filter"></a>범위 지정 필터
OU 범위 지정 필터를 사용하는 경우
- 지정된 구성에 대해 최대 59개의 개별 OU만 동기화할 수 있습니다. 
- 중첩 OU가 지원됩니다(즉, 130개의 중첩된 OU가 있는 OU를 **동기화할 수 있지** 만 동일한 구성에서 60개의 개별 OU를 동기화할 수는 **없음**). 

### <a name="password-hash-sync"></a>암호 해시 동기화
- 암호 해시 동기화를 InetOrgPerson과 함께 사용하는 기능은 지원되지 않습니다.


## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)
