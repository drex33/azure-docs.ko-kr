---
title: 'Azure AD Connect: 필수 조건 및 하드웨어 | Microsoft Docs'
description: 이 문서에서는 Azure AD Connect에 대한 필수 조건 및 하드웨어 요구 사항을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2ff57c06fba085fd28e7e0b13ec6e503517cab7
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768682"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Azure AD Connect에 대한 필수 조건
이 문서에서는 Azure AD(Azure Active Directory) Connect에 대한 필수 조건 및 하드웨어 요구 사항을 설명합니다.

## <a name="before-you-install-azure-ad-connect"></a>Azure AD Connect를 설치하기 전에
Azure AD Connect를 설치하기 전에 필요한 몇 가지 사항이 있습니다.

### <a name="azure-ad"></a>Azure AD
* Azure AD 테넌트가 필요합니다. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)에서 사용할 수 있습니다. 다음 포털 중 하나를 사용하여 Azure AD Connect를 관리할 수 있습니다.
  * [Azure Portal](https://portal.azure.com)
  * [Office 포털](https://portal.office.com)
* [도메인을 추가하고 확인합니다](../fundamentals/add-custom-domain.md) . 예를 들어, 사용자가 contoso.com을 사용하려는 경우 해당 도메인을 확인하고 contoso.onmicrosoft.com 기본 도메인을 사용하지 않도록 합니다.
* Azure AD 테넌트는 기본적으로 5만 개의 개체를 허용합니다. 도메인을 확인할 때 제한은 30만 개의 개체로 늘어납니다. Azure AD에서 더 많은 개체가 필요한 경우 지원 사례를 열어 제한을 더 늘립니다. 50만 개가 넘는 개체가 필요한 경우 Microsoft 365, Azure AD Premium 또는 Enterprise Mobility + Security와 같은 라이선스가 필요합니다.

### <a name="prepare-your-on-premises-data"></a>온-프레미스 데이터 준비
* [Azure AD 및 Microsoft 365](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac)로 동기화하기 전에 [IdFix](https://github.com/Microsoft/idfix)를 사용하여 디렉터리의 중복 및 서식 문제 등의 오류가 있는지 식별합니다.
* [Azure AD에서 사용하도록 설정할 수 있는 선택적 동기화 기능](how-to-connect-syncservice-features.md)을 검토하여 사용하도록 설정해야 할 기능을 평가합니다.

### <a name="on-premises-active-directory"></a>온-프레미스 Active Directory
* Active Directory 스키마의 버전 및 포리스트 기능 수준은 Windows Server 2003 이상이어야 합니다. 도메인 컨트롤러는 스키마 버전 및 포리스트 수준 요구 사항이 충족되는 한 모든 버전을 실행할 수 있습니다.
* *비밀번호 쓰기 저장* 기능을 사용하려면 도메인 컨트롤러가 Windows Server 2016 이상에 있어야 합니다.
* Azure AD에서 사용되는 도메인 컨트롤러는 쓰기 가능해야 합니다. RODC(읽기 전용 도메인 컨트롤러)를 사용하는 것은 지원되지 않으며 Azure AD Connect가 쓰기 리디렉션을 따르지 않습니다.
* NetBIOS 이름에 온-프레미스 포리스트를 사용하거나 "점"(이름에 마침표(".") 포함)을 사용하는 것은 지원되지 않습니다.
* [Active Directory 휴지통을 사용](how-to-connect-sync-recycle-bin.md)하는 것이 좋습니다.

### <a name="powershell-execution-policy"></a>PowerShell 실행 정책
Azure Active Directory Connect 설치의 일부로 서명된 PowerShell 스크립트가 실행됩니다. PowerShell 실행 정책이 스크립트 실행을 허용하는지 확인합니다.

설치하는 동안 권장되는 실행 정책은 "RemoteSigned"입니다.

PowerShell 실행 정책 설정에 대한 자세한 내용은 [Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy)를 참조하세요.


### <a name="azure-ad-connect-server"></a>Azure AD Connect 서버
Azure AD Connect 서버에는 중요한 ID 데이터가 포함되어 있습니다. 이 서버에 대한 관리 액세스는 적절히 보호해야 합니다. [권한 있는 액세스 보안](/windows-server/identity/securing-privileged-access/securing-privileged-access)의 지침을 따릅니다. 

Azure AD Connect 서버는 [Active Directory 관리 계층 모델](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)에 설명된 대로 계층 0 구성 요소로 취급되어야 합니다. 

Active Directory 환경 보안에 대한 자세한 내용은 [Active Directory 보안 모범 사례](/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory)를 참조하세요.

#### <a name="installation-prerequisites"></a>설치 필수 조건

- Azure AD Connect는 도메인에 조인된 Windows Server 2016 이상 버전에 설치해야 합니다. 
- Azure AD Connect는 Small Business Server 또는 2019 이전 Windows Server Essentials에 설치할 수 없습니다(Windows Server Essentials 2019는 지원됨). 서버는 Windows Server Standard 이상을 사용해야 합니다. 
- Azure AD Connect 서버에는 전체 GUI가 설치되어 있어야 합니다. Windows Server Core에 Azure AD Connect를 설치하는 것은 지원되지 않습니다. 
- Azure AD Connect 마법사를 사용하여 AD FS(Active Directory Federation Services) 구성을 관리하는 경우 Azure AD Connect 서버에서 PowerShell 기록 그룹 정책을 사용하도록 설정하지 않아야 합니다. Azure AD Connect 마법사를 사용하여 동기화 구성을 관리하는 경우 PowerShell 기록을 사용하도록 설정할 수 있습니다. 
- AD FS를 배포 중인 경우: 
    - AD FS 또는 웹 애플리케이션 프록시가 설치된 서버는 Windows Server 2012 R2 이상이어야 합니다. Windows 원격 관리 를 사용할 수 있어야 합니다. 
    - TLS/SSL 인증서를 구성해야 합니다. 자세한 내용은 [AD FS의 SSL/TLS 프로토콜 및 암호 그룹 관리](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)와 [AD FS에서 SSL 인증서 관리](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap)를 참조하세요.
    - 이름 확인을 구성해야 합니다. 
- 전역 관리자가 MFA를 사용하도록 설정한 경우 URL https://secure.aadcdn.microsoftonline-p.com 이 반드시 신뢰할 수 있는 사이트 목록에 있어야 합니다. MFA 챌린지를 묻는 메시지가 표시되기 전에 이 사이트를 추가하지 않은 경우 신뢰할 수 있는 사이트 목록에 추가하라는 메시지가 표시됩니다. Internet Explorer를 사용하여 신뢰할 수 있는 사이트에 추가할 수 있습니다.
- 동기화를 위해 Azure AD Connect Health를 사용하려는 경우 Azure AD Connect Health에 대한 필수 조건도 충족되는지 확인합니다. 자세한 내용은 [Azure AD Connect Health 에이전트 설치](how-to-connect-health-agent-install.md)를 참조하세요.

#### <a name="harden-your-azure-ad-connect-server"></a>Azure AD Connect 서버 강화 
IT 환경의 이 중요한 구성 요소에 대한 보안 공격 노출 영역을 줄이려면 Azure AD Connect 서버를 강화하는 것이 좋습니다. 이러한 권장 사항을 따르면 조직에 대한 일부 보안 위험을 완화하는 데 도움이 됩니다.

- Azure AD Connect를 도메인 컨트롤러 및 기타 계층 0 리소스와 동일하게 취급합니다. 자세한 내용은 [Active Directory 관리 계층 모델](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)을 참조하세요.
- 도메인 관리자 또는 기타 엄격하게 제어되는 보안 그룹으로만 Azure AD Connect 서버에 대한 관리 액세스를 제한합니다.
- [권한 있는 액세스가 있는 모든 직원에 대한 전용 계정](/windows-server/identity/securing-privileged-access/securing-privileged-access)을 만듭니다. 관리자는 높은 권한이 있는 계정을 사용하여 웹을 탐색하거나, 메일을 확인하거나, 일상적인 생산성 작업을 수행하면 안 됩니다.
- [권한 있는 액세스 보안](/windows-server/identity/securing-privileged-access/securing-privileged-access)에 제공된 지침을 따릅니다. 
- AADConnect 서버에서의 NTLM 인증 사용을 거부합니다. 이 작업을 수행하는 방법은 [AADConnect 서버에서 NTLM 제한](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-outgoing-ntlm-traffic-to-remote-servers) 및 [도메인에서 NTLM 제한](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-ntlm-authentication-in-this-domain)입니다.
- 모든 머신에 고유한 로컬 관리자 암호가 있는지 확인합니다. 자세한 내용은 [LAPS(로컬 관리자 암호 솔루션)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps)을 참조하세요. 이 기능을 사용하여 각 워크스테이션과 서버에서 고유한 임의의 암호를 구성함으로써 ACL로 보호되는 Active Directory에 저장할 수 있습니다. 권한 있는 적격 사용자만이 이러한 로컬 관리자 계정 암호를 읽거나 재설정을 요청할 수 있습니다. 워크스테이션 및 서버에 사용할 LAPS는 [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=46899)에서 받을 수 있습니다. LAPS 및 PAW(권한 있는 액세스 워크스테이션)를 사용하여 환경을 운영하는 방법에 대한 추가 지침은 [클린 소스 원칙을 기반으로 하는 운영 표준](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle)에서 찾을 수 있습니다. 
- 조직의 정보 시스템에 대해 권한 있는 액세스가 있는 모든 직원에 대해 전용 [권한 있는 액세스 워크스테이션](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)을 구현합니다. 
- 이러한 [추가 지침](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface)에 따라 Active Directory 환경의 공격 표면을 줄입니다.
- [페더레이션 구성에 대한 변경 내용 모니터링](how-to-connect-monitor-federation-changes.md)에 따라 경고를 설정하여 Idp와 Azure AD 간에 설정된 트러스트의 변경 내용을 모니터링합니다. 

### <a name="sql-server-used-by-azure-ad-connect"></a>Azure AD Connect에서 사용하는 SQL Server
* Azure AD Connect는 ID 데이터를 저장하기 위한 SQL Server 데이터베이스가 필요합니다. 기본적으로 SQL Server 2019 Express LocalDB(SQL Server Express의 라이트 버전)가 설치됩니다. SQL Server Express는 약 100,000개의 개체를 관리할 수 있는 10GB의 용량을 제공합니다. 더 큰 볼륨의 디렉터리 개체 관리가 필요한 경우 설치 마법사가 SQL Server의 다른 설치를 가리키도록 합니다. SQL Server 설치 유형은 [Azure AD Connect 성능](./plan-connect-performance-factors.md#sql-database-factors)에 영향을 줄 수 있습니다.
* 다른 SQL Server 설치를 사용하는 경우 다음 요구 사항이 적용됩니다.
  * Azure AD Connect는 SQL Server 2012(최신 서비스 팩)에서 SQL Server 2019까지 모든 SQL Server 버전을 지원합니다. Azure SQL Database는 데이터베이스로 지원되지 않습니다.
  * 대/소문자를 구분하지 않는 SQL 데이터 정렬을 사용해야 합니다. 이러한 데이터 정렬은 이름에 \_CI_를 사용하여 식별됩니다. 이름에 \_CS_를 사용하여 식별되는 대/소문자 구분 데이터 정렬을 사용하는 것은 *지원되지 않습니다*.
  * SQL 인스턴스당 동기화 엔진을 한 개만 사용할 수 있습니다. SQL 인스턴스를 FIM/MIM 동기화, DirSync 또는 Azure AD Sync와 공유하는 것은 지원되지 않습니다.

### <a name="accounts"></a>계정
* 통합하려는 Azure AD 테넌트에 대한 Azure AD 전역 관리자 계정이 있어야 합니다. 이 계정은 *학교 또는 조직 계정* 이어야 하며 *Microsoft 계정* 이 될 수 없습니다.
* [Express 설정](reference-connect-accounts-permissions.md#express-settings-installation)을 사용하거나 DirSync에서 업그레이드하는 경우 온-프레미스 Active Directory에 대한 엔터프라이즈 관리자 계정이 있어야 합니다.
* 사용자 지정 설정 설치 경로를 사용하는 경우 더 많은 옵션이 있습니다. 자세한 내용은 [사용자 지정 설치 설정](reference-connect-accounts-permissions.md#custom-installation-settings)을 참조하세요.

### <a name="connectivity"></a>연결
* Azure AD Connect 서버는 인트라넷 및 인터넷에 대해 DNS 확인을 해야 합니다. DNS 서버는 온-프레미스 Active Directory와 Azure AD 엔드포인트 모두에 대해 이름을 확인할 수 있어야 합니다.
* Azure AD Connect에는 구성된 모든 도메인에 대한 네트워크 연결이 필요합니다.
* 인트라넷에 방화벽이 있고 Azure AD Connect 서버와 도메인 컨트롤러 사이에서 포트를 열어야 하는 경우 자세한 내용은 [Azure AD Connect 포트](reference-connect-ports.md)를 참조하세요.
* 프록시 또는 방화벽에 액세스할 수 있는 URL을 제한하는 경우 [Office 365 URL 및 IP 주소 범위 ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)에서 설명한 URL이 열려야 합니다. [방화벽 또는 프록시 서버에서 Azure Portal URL 수신 허용 목록](../../azure-portal/azure-portal-safelist-urls.md?tabs=public-cloud)도 참조하세요.
  * 독일의 Microsoft Cloud 또는 Microsoft Azure Government 클라우드를 사용하는 경우 URL은 [Azure AD Connect 동기화 서비스 인스턴스가 고려 사항](reference-connect-instances.md)을 참조하세요.
* Azure AD Connect(1.1.614.0 버전 이상)는 기본적으로 TLS 1.2를 사용하여 동기화 엔진과 Azure AD 간의 통신을 암호화합니다. 기본 운영 체제에서 TLS 1.2를 사용할 수 없는 경우 Azure AD Connect에 이전 프로토콜(TLS 1.1 및 TLS 1.0)이 점차적으로 다시 적용됩니다. Azure AD Connect 버전 2.0 이상부터 TLS 1.0 및 1.1은 더 이상 지원되지 않으며 TLS 1.2를 사용할 수 없으면 설치가 실패합니다.
* 1.1.614.0 버전 이전의 Azure AD Connect는 기본적으로 TLS 1.0을 사용하여 동기화 엔진과 Azure AD 간의 통신을 암호화합니다. TLS 1.2로 변경하려면 [Azure AD Connect에 TLS 1.2 사용](#enable-tls-12-for-azure-ad-connect)의 단계를 수행합니다.
* 인터넷에 연결하는 데 아웃바운드 프록시를 사용하는 경우 설치 마법사 및 Azure AD Connect 동기화에서 인터넷 및 Azure AD에 연결하려면 **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** 파일에 다음 설정을 추가해야 합니다. 이 텍스트는 파일의 맨 아래에 입력해야 합니다. 이 코드에서 *&lt;PROXYADDRESS&gt;* 는 실제 프록시 IP 주소 또는 호스트 이름을 나타냅니다.

    ```
        <system.net>
            <defaultProxy>
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* 프록시 서버에 인증이 필요한 경우 [서비스 계정](reference-connect-accounts-permissions.md#adsync-service-account)이 도메인에 위치해야 합니다. 사용자 지정 설정 설치 경로를 사용하여 [사용자 지정 서비스 계정](how-to-connect-install-custom.md#install-required-components)을 지정합니다. 또한 machine.config에 대해 다른 변경이 필요합니다. machine.config에서 이 변경 내용을 적용하면 설치 마법사와 동기화 엔진이 프록시 서버의 인증 요청에 응답합니다. **구성** 페이지를 제외하고 모든 설치 마법사 페이지에서 로그인한 사용자의 자격 증명이 사용됩니다. 설치 마법사의 끝에 나오는 **구성** 페이지에서 컨텍스트가 이전에 만든 [서비스 계정](reference-connect-accounts-permissions.md#adsync-service-account)으로 전환됩니다. machine.config 섹션은 다음과 같이 표시됩니다.

    ```
        <system.net>
            <defaultProxy enabled="true" useDefaultCredentials="true">
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* 프록시 구성이 기존 설정에서 수행되는 경우 Azure AD Connect에서 프록시 구성을 읽고 동작을 업데이트하려면 **Microsoft Azure AD 동기화 서비스** 를 한 번 다시 시작해야 합니다. 
* Azure AD Connect에서 디렉터리 동기화의 일부로 웹 요청을 Azure AD로 보내면 Azure AD에서 응답하는 데 최대 5분이 걸릴 수 있습니다. 프록시 서버에서 연결 유휴 시간 제한 구성을 사용하는 것이 일반적입니다. 구성이 6분 이상으로 설정되었는지 확인합니다.

자세한 내용은 [기본 프록시 요소](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)에 대한 MSDN을 참조하세요.
연결에 문제가 있는 경우 [연결 문제 해결](tshoot-connect-connectivity.md)을 참조하세요.

### <a name="other"></a>기타
선택 사항: 동기화를 확인할 테스트 사용자 계정 사용

## <a name="component-prerequisites"></a>구성 요소 필수 조건
### <a name="powershell-and-net-framework"></a>PowerShell 및 .NET Framework
Azure AD Connect는 Microsoft PowerShell 5.0 및 .NET Framework 4.5.1에 따라 다릅니다. 서버에 이 버전 이상을 설치해야 합니다. 

### <a name="enable-tls-12-for-azure-ad-connect"></a>Azure AD Connect에 TLS 1.2 사용
1.1.614.0 버전 이전의 Azure AD Connect는 기본적으로 TLS 1.0을 사용하여 동기화 엔진 서버와 Azure AD 간의 통신을 암호화합니다. 서버에서 기본적으로 TLS 1.2를 사용하도록 .NET 애플리케이션을 구성할 수 있습니다. TLS 1.2에 대한 자세한 내용은 [Microsoft 보안 공지 2960358](/security-updates/SecurityAdvisories/2015/2960358)을 참조하세요.

1. 운영 체제에 대해 .NET 4.5.1 핫픽스를 설치했는지 확인합니다. 자세한 내용은 [Microsoft 보안 공지 2960358](/security-updates/SecurityAdvisories/2015/2960358)을 참조하세요. 이 핫픽스 또는 이후 릴리스를 서버에 이미 설치했을 수 있습니다.

1. 모든 운영 체제에 대해 이 레지스트리 키를 설정하고 서버를 다시 시작합니다.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. 동기화 엔진 서버와 원격 SQL Server 간에 TLS 1.2를 사용하도록 설정하려는 경우 [Microsoft SQL Server에 대한 TLS 1.2 지원](https://support.microsoft.com/kb/3135244)을 위해 필요한 버전이 설치되어 있는지 확인합니다.

### <a name="dcom-prerequisites-on-the-synchronization-server"></a>동기화 서버에 대한 DCOM 필수 조건
동기화 서비스를 설치하는 동안 Azure AD Connect는 다음 레지스트리 키가 있는지 확인합니다.

- HKEY_LOCAL_MACHINE:  Software\Microsoft\Ole

이 레지스트리 키에서 Azure AD Connect는 다음 값이 존재하는지와 손상되지 않았는지 확인합니다. 

- [MachineAccessRestriction](/windows/win32/com/machineaccessrestriction)
- [MachineLaunchRestriction](/windows/win32/com/machinelaunchrestriction)
- [DefaultLaunchPermission](/windows/win32/com/defaultlaunchpermission)

## <a name="prerequisites-for-federation-installation-and-configuration"></a>페더레이션 설치 및 구성을 위한 필수 조건
### <a name="windows-remote-management"></a>Windows 원격 관리
Azure AD Connect를 사용하여 AD FS 또는 WAP(웹 애플리케이션 프록시)를 배포하는 경우 다음 요구 사항을 확인하세요.

* 대상 서버가 도메인에 가입된 경우 Windows 원격 관리가 사용되는지 확인합니다.
  * 관리자 권한 PowerShell 명령 창에서 `Enable-PSRemoting –force` 명령을 사용합니다.
* 대상 서버가 도메인에 가입된 WAP 머신이 아닌 경우 몇 가지 추가 요구 사항이 있습니다.
  * 대상 컴퓨터(WAP 컴퓨터):
    * WinRM(Windows Remote Management/WS-Management) 서비스가 서비스 스냅인을 통해 실행되는지 확인합니다.
    * 관리자 권한 PowerShell 명령 창에서 `Enable-PSRemoting –force` 명령을 사용합니다.
  * 마법사를 실행 중인 머신(대상 머신이 도메인에 가입되지 않았거나 신뢰할 수 없는 도메인인 경우):
    * 관리자 권한 PowerShell 명령 창에서 `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` 명령을 사용합니다.
    * 서버 관리자에서 다음을 수행합니다.
      * 머신 풀에 DMZ WAP 호스트를 추가합니다. 서버 관리자에서 **관리** > **서버 추가** 를 선택하고 **DNS** 탭을 사용합니다.
      * **서버 관리자 모든 서버** 탭에서 WAP 서버를 마우스 오른쪽 단추로 클릭하고 **다음으로 관리** 를 선택합니다. WAP 머신에 대한 로컬(도메인 아님) 자격 증명을 입력합니다.
      * **서버 관리자 모든 서버** 탭에서 원격 PowerShell 연결 유효성을 검사하려면, WAP 서버를 마우스 오른쪽 단추로 클릭하고 **Windows PowerShell** 을 선택합니다. 원격 PowerShell 세션을 설정할 수 있도록 원격 PowerShell 세션을 열어야 합니다.

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 인증서 요구 사항
* AD FS 팜의 모든 노드 및 모든 웹 애플리케이션 프록시 서버에서 동일한 TLS/SSL 인증서를 사용하는 것이 가장 좋습니다.
* 인증서는 X509 인증서여야 합니다.
* 테스트 랩 환경의 페더레이션 서버에서 자체 서명된 인증서를 사용할 수 있습니다. 프로덕션 환경에서는 공용 인증 기관에서 인증서를 가져오는 것이 좋습니다.
  * 공개적으로 신뢰할 수 없는 인증서를 사용하면 각 웹 애플리케이션 프록시 서버에 설치된 인증서가 모든 페더레이션 서버 및 로컬 서버에서 신뢰할 수 있는 지 확인합니다.
* 인증서의 ID는 페더레이션 서비스 이름(예: sts.contoso.com)과 일치해야 합니다.
  * ID는 dNSName 유형의 SAN(주체 대체 이름)의 확장 유형이거나, SAN 항목이 없는 경우 공통 이름으로 지정된 주체 이름입니다.
  * 그 중 하나가 페더레이션 서비스 이름과 일치하는 경우 여러 SAN 항목이 인증서에 있을 수 있습니다.
  * Workplace Join을 사용할 예정인 경우 **enterpriseregistration** 값의 추가 SAN이 필요합니다. 이어서 조직의 UPN(사용자 이름) 접미사(예: enterpriseregistration.contoso.com)를 붙입니다.
* CryptoAPI 차세대(CNG) 키 및 KSP(키 스토리지 공급자)를 기준으로 하는 인증서는 지원되지 않습니다. 따라서 KSP가 아닌 CSP(암호화 서비스 공급자)를 기준으로 하는 인증서를 사용해야 합니다.
* 와일드카드 인증서가 지원됩니다.

### <a name="name-resolution-for-federation-servers"></a>페더레이션 서버에 대한 이름 확인
* 인트라넷(내부 DNS 서버) 및 엑스트라넷(도메인 등록 기관을 통한 퍼블릭 DNS) 모두의 AD FS 이름(예: sts.contoso.com)에 대한 DNS 레코드를 설정합니다. 인트라넷 DNS 레코드의 경우, CNAME 레코드가 아닌 A 레코드를 사용해야 합니다. Windows 인증이 도메인에 가입된 머신에서 제대로 작동하려면 A 레코드를 사용해야 합니다.
* 둘 이상의 AD FS 서버 또는 웹 애플리케이션 프록시 서버를 배포하는 경우 사용자 부하 분산 장치를 구성하고 AD FS 이름(예: sts.contoso.com)에 대한 DNS 레코드가 부하 분산 장치를 가리키는지 확인합니다.
* 인트라넷에서 Internet Explorer를 사용하는 브라우저 애플리케이션에서 Windows 통합 인증이 작동하려면 Internet Explorer에서 AD FS 이름(예: sts.contoso.com)이 인트라넷 영역에 추가되었는지를 확인합니다. 이러한 필요한 항목을 그룹 정책을 통해 제어하고 모든 도메인에 가입된 컴퓨터에 배포할 수 있습니다.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect 지원 구성 요소
Azure AD Connect는 Azure AD Connect가 설치된 서버에서 다음 구성 요소를 설치합니다. 이 목록은 기본 Express 설치에 해당합니다. **동기화 서비스 설치** 페이지에서 다른 SQL Server를 사용하도록 선택하는 경우 SQL Express LocalDB는 로컬로 설치되지 않습니다.

* Azure AD Connect Health
* Microsoft SQL Server 2019 명령줄 유틸리티
* Microsoft SQL Server 2019 Express LocalDB
* Microsoft SQL Server 2019 Native Client
* Microsoft Visual C++ 14 재배포 패키지

## <a name="hardware-requirements-for-azure-ad-connect"></a>Azure AD Connect의 하드웨어 요구 사항
다음 표는 Azure AD Connect 동기화 컴퓨터의 최소 요구 사항을 보여 줍니다.

| Active Directory의 개체 수 | CPU | 메모리 | 하드 드라이브 크기 |
| --- | --- | --- | --- |
| 10,000개 미만 |1.6GHz |4GB |70GB |
| 10,000–50,000개 |1.6GHz |4GB |70GB |
| 50,000–100,000개 |1.6GHz |16GB |100GB |
| 처음 사용자용 SQL Server가 필요한 100,000개 이상의 개체 성능상의 이유로 로컬로 설치하는 것이 좋습니다. | | | |
| 100,000–300,000개 |1.6GHz |32GB |300GB |
| 300,000–600,000개 |1.6GHz |32GB |450GB |
| 600,000개 초과 |1.6GHz |32GB |500GB |

AD FS 또는 웹 애플리케이션 프록시 서버를 실행하는 컴퓨터에 대한 최소 요구 사항은 다음과 같습니다.

* CPU: 듀얼 코어 1.6GHz 이상
* 메모리: 2GB 이상
* Azure VM: A2 구성 이상

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
