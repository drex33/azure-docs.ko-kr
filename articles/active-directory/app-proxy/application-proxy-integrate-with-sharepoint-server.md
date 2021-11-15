---
title: SharePoint에 대한 원격 액세스 사용 - Azure Active Directory 애플리케이션 프록시
description: 온-프레미스 SharePoint Server를 Azure Active Directory 애플리케이션 프록시와 통합하는 방법에 대한 기본 사항을 다룹니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 261faaa471956a1f91bf09a21656a6d01203cdfc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989307"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-active-directory-application-proxy"></a>Azure Active Directory 애플리케이션 프록시를 사용하여 SharePoint에 대한 원격 액세스 활성화

이 단계별 가이드에서는 온-프레미스 SharePoint 팜을 Azure AD(Azure Active Directory) 애플리케이션 프록시와 통합하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

구성을 수행하려면 다음 리소스가 필요합니다.
- SharePoint 2013 팜 이상
- 애플리케이션 프록시를 포함하는 계획이 있는 Azure AD 테넌트. [Azure AD 요금제 및 가격 책정](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)에 대해 자세히 알아보세요.
- Azure AD 테넌트에서 [확인된 사용자 지정 도메인](../fundamentals/add-custom-domain.md)입니다.
- Azure AD Connect와 동기화된 온-프레미스 Active Directory. 이를 통해 사용자는 [Azure에 로그인](../hybrid/plan-connect-user-signin.md)할 수 있습니다.
- 회사 도메인 내의 컴퓨터에 설치되어 실행되는 애플리케이션 프록시 커넥터입니다.

애플리케이션 프록시로 SharePoint를 구성하려면 다음 두 개의 URL이 필요합니다.
- 최종 사용자에게 표시되고 Azure AD에서 결정되는 외부 URL. 이 URL은 사용자 지정 도메인을 사용할 수 있습니다. [Azure AD 애플리케이션 프록시에서 사용자 지정 도메인 작업](application-proxy-configure-custom-domain.md)에 대해 자세히 알아보세요.
- 회사 도메인 내에서만 알려져 있고 직접 사용되지 않는 내부 URL.

> [!IMPORTANT]
> 링크가 올바르게 매핑되도록 하려면 내부 URL에 대해 다음 권장 사항을 따르세요.
> - HTTPS 사용:
> - 사용자 지정 포트를 사용하지 않도록 합니다.
> - 회사 DNS(Domain Name System)에서 별칭(CName)이 아닌 SharePoint WFE(또는 부하 분산 장치)를 가리키는 호스트(A)를 만듭니다.

이 문서에서 사용하는 값은 다음과 같습니다.
- 내부 URL: `https://sharepoint`
- 외부 URL: `https://spsites-demo1984.msappproxy.net/`
- SharePoint 웹 애플리케이션에 대한 애플리케이션 풀 계정: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>1단계: Azure AD에서 애플리케이션 프록시를 사용하는 애플리케이션 구성

이 단계에서 애플리케이션 프록시를 사용하는 Azure Active Directory 테넌트에서 애플리케이션을 만듭니다. 외부 URL을 설정하고 내부 URL을 지정합니다. 둘 다 SharePoint에서 나중에 사용됩니다.

1. 다음 설정에 설명된 대로 앱을 만듭니다. 단계별 지침은 [Azure AD 애플리케이션 프록시를 사용하여 애플리케이션 게시](../app-proxy/application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)를 참조하세요.
   * **내부 URL**: SharePoint에서 나중에 설정할 SharePoint 내부 URL입니다(예: `https://sharepoint`).
   * **사전 인증**: Azure Active Directory
   * **헤더의 URL 변환**: 아니요
   * **애플리케이션 본문에서 URL 변환**: 아니요

   ![SharePoint를 애플리케이션으로 게시](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. 앱을 게시한 후에는 다음 단계에 따라 Single Sign-On 설정을 구성합니다.

   1. 포털의 애플리케이션 페이지에서 **Single Sign-On** 을 선택합니다.
   1. **Single Sign-On 모드** 로 **Windows 통합 인증** 을 선택합니다.
   1. **내부 애플리케이션 SPN** 을 이전에 설정한 값으로 설정합니다. 이 예제에서 값은 `HTTP/sharepoint`입니다.
   1. **위임된 로그인 ID** 에서 Active Directory 포리스트 구성에 가장 적합한 옵션을 선택합니다. 예를 들어, 포리스트에 단일 Active Directory 도메인이 있는 경우 다음 스크린샷에 표시된 것처럼 **온-프레미스 SAM 계정 이름** 을 선택합니다. 그러나 사용자가 SharePoint와 동일한 도메인 및 애플리케이션 프록시 커넥터 서버에 있지 않은 경우 **온-프레미스 사용자 계정 이름**(스크린샷에는 표시되지 않음)을 선택합니다.

   ![SSO용 Windows 통합 인증 구성](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. 애플리케이션 설정을 완료하려면 **사용자 및 그룹** 섹션으로 이동하고 이 애플리케이션에 액세스할 사용자를 할당합니다. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>2단계: SharePoint 웹 애플리케이션 구성

SharePoint 웹 애플리케이션이 Azure AD 애플리케이션 프록시에서 제대로 작동하려면 Kerberos 및 적절한 대체 액세스 매핑을 사용하여 구성해야 합니다. 다음과 같은 두 가지 옵션을 사용할 수 있습니다.

- 새 웹 애플리케이션을 만들고 기본 영역만 사용합니다. 이 옵션은 SharePoint에 대한 최상의 환경을 제공하므로 기본 설정으로 사용됩니다. 예를 들어 SharePoint에서 생성되는 이메일 경고의 링크는 항상 기본 영역을 가리킵니다.
- 기존 웹 애플리케이션을 확장하여 기본이 아닌 영역에서 Kerberos를 구성합니다.

> [!IMPORTANT]
> 사용되는 영역에 관계없이, Kerberos가 제대로 작동하려면 SharePoint 웹 애플리케이션의 애플리케이션 풀 계정이 도메인 계정이어야 합니다.

### <a name="provision-the-sharepoint-web-application"></a>SharePoint 웹 애플리케이션 프로비전

- 새 웹 애플리케이션을 만들고 기본 영역(기본 설정 옵션)만 사용하는 경우:

    1. **SharePoint 관리 셸** 을 시작하고 다음 스크립트를 실행합니다.

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. **SharePoint 중앙 관리** 사이트를 엽니다.
    1. **시스템 설정** 아래에서 **대체 액세스 매핑 구성** 을 선택합니다. **대체 액세스 매핑 컬렉션** 상자가 열립니다.
    1. 새 웹 애플리케이션으로 디스플레이를 필터링하고 다음과 같이 표시되는지 확인합니다.

       ![웹 애플리케이션의 대체 액세스 매핑](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- 기존 웹 애플리케이션을 새 영역으로 확장하는 경우(기본 영역을 사용할 수 없는 경우) 다음을 수행합니다.

    1. SharePoint 관리 셸을 시작하고 다음 스크립트를 실행합니다.

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. **SharePoint 중앙 관리** 사이트를 엽니다.
    1. **시스템 설정** 아래에서 **대체 액세스 매핑 구성** 을 선택합니다. **대체 액세스 매핑 컬렉션** 상자가 열립니다.
    1. 확장된 웹 애플리케이션을 사용하여 디스플레이를 필터링하고 다음과 같은 내용이 표시되는지 확인합니다.

        ![확장된 애플리케이션의 대체 액세스 매핑](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>SharePoint 웹 애플리케이션이 도메인 계정으로 실행되고 있는지 확인

SharePoint 웹 애플리케이션의 애플리케이션 풀을 실행하는 계정을 식별하고 도메인 계정인지 확인하려면 다음 단계를 수행합니다.

1. **SharePoint 중앙 관리** 사이트를 엽니다.
1. **보안** 으로 이동하고 **서비스 계정 구성** 을 선택합니다.
1. **웹 애플리케이션 풀 - YourWebApplicationName** 을 선택합니다.

   ![서비스 계정 구성 옵션](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. **이 구성 요소에 대한 계정 선택** 이 도메인 계정을 반환하는지 확인합니다. 다음 단계에서 필요하므로 이 작업을 반드시 수행해야 합니다.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>엑스트라넷 영역의 IIS 사이트에 대해 HTTPS 인증서가 구성되었는지 확인

내부 URL은 HTTPS 프로토콜(`https://SharePoint/`)을 사용하기 때문에 IIS(인터넷 정보 서비스) 사이트에서 인증서를 설정해야 합니다.

1. Windows PowerShell 콘솔을 엽니다.
1. 다음 스크립트를 실행하여 자체 서명된 인증서를 생성하고 컴퓨터의 내 저장소에 추가합니다.

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > 자체 서명된 인증서는 테스트 목적으로만 적합합니다. 프로덕션 환경에서는 인증 기관에서 발급한 인증서를 대신 사용하는 것이 좋습니다.

1. 인터넷 정보 서비스 관리자 콘솔을 엽니다.
1. 트리 뷰에서 서버를 확장하고 **사이트** 를 확장한 다음, **SharePoint - AAD 프록시** 사이트를 선택하고 **바인딩** 을 선택합니다.
1. **https 바인딩** 을 선택한 다음, **편집** 을 선택합니다.
1. TLS/SSL 인증서 필드에서 **SharePoint** 인증서를 선택한 다음, **확인** 을 선택합니다.

Azure AD 애플리케이션 프록시를 통해 SharePoint 사이트를 외부에서 액세스할 수 있습니다.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>3단계: Kerberos 제한 위임 구성

사용자는 처음에 Azure AD에서 인증을 받은 후 Azure AD 프록시 커넥터를 통해 Kerberos를 사용하여 SharePoint에서 인증을 받습니다. 커넥터가 Azure AD 사용자를 대신하여 Kerberos 토큰을 얻을 수 있도록 하려면 프로토콜 전환을 사용하여 KCD(Kerberos 제한 위임)를 구성해야 합니다. KCD에 대한 자세한 내용은 [Kerberos 제한 위임 개요](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11))를 참조하세요.

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>SharePoint 서비스 계정에 대한 SPN 설정

이 문서에서 내부 URL은 `https://sharepoint`이므로 SPN(서비스 사용자 이름)은 `HTTP/sharepoint`입니다. 이러한 값을 사용자 환경에 해당하는 값으로 바꾸어야 합니다.
SharePoint 애플리케이션 풀 계정 `Contoso\spapppool`에 SPN `HTTP/sharepoint`를 등록하려면 도메인 관리자 권한으로 명령 프롬프트에서 다음 명령을 실행합니다.

`setspn -S HTTP/sharepoint Contoso\spapppool`

`Setspn` 명령은 SPN을 추가하기 전에 먼저 검색합니다. SPN이 이미 있으면 **중복된 SPN 값** 오류가 표시됩니다. 이 경우 올바른 애플리케이션 풀 계정 아래에 설정되어 있지 않으면 기존 SPN을 제거하는 것이 좋습니다. -L 옵션을 통해 `Setspn` 명령을 실행하여 SPN이 성공적으로 추가되었는지 확인할 수 있습니다. 이 명령에 대해 자세히 알아보려면 [Setspn](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11))을 참조하세요.

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>커넥터가 SharePoint 애플리케이션 풀 계정에 추가된 SPN에 위임되도록 신뢰할 수 있는지 확인

Azure AD 애플리케이션 프록시 서비스가 사용자 ID를 SharePoint 애플리케이션 풀 계정에 위임할 수 있도록 KCD를 구성합니다. 애플리케이션 프록시 커넥터에서 Azure AD에서 인증된 사용자에 대한 Kerberos 티켓을 검색할 수 있도록 하여 KCD를 구성합니다. 그런 다음, 해당 서버에서 컨텍스트를 대상 애플리케이션(이 경우 SharePoint)에 전달합니다.

KCD를 구성하려면 각 커넥터 머신에 대해 다음 단계를 수행합니다.

1. 도메인 관리자로 도메인 컨트롤러에 로그인하고 Active Directory 사용자 및 컴퓨터를 엽니다.
1. Azure AD 프록시 커넥터를 실행하는 컴퓨터를 찾습니다. 이 예제에서는 SharePoint Server를 실행하는 컴퓨터입니다.
1. 컴퓨터를 두 번 클릭한 후 **위임** 탭을 선택합니다.
1. 위임 옵션이 **지정된 서비스에 대한 위임용으로만 이 컴퓨터 트러스트** 로 설정되어 있는지 확인합니다. 그런 다음 **모든 인증 프로토콜 사용** 을 선택합니다.
1. **추가** 단추를 선택하고, **사용자 또는 컴퓨터** 를 선택하고, SharePoint 애플리케이션 풀 계정을 찾습니다. 예: `Contoso\spapppool`
1. SPN 목록에서 서비스 계정에 대해 이전에 만든 SPN을 선택합니다.
1. **확인** 을 선택한 다음, **확인** 을 다시 선택하여 변경 내용을 저장합니다.
  
   ![위임 설정](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

이제 외부 URL을 사용하여 SharePoint에 로그인하고 Azure에서 인증을 받을 준비가 되었습니다.

## <a name="troubleshoot-sign-in-errors"></a>로그인 오류 문제 해결

사이트에 대한 로그인이 작동하지 않는 경우 커넥터 로그에서 이슈에 대한 자세한 정보를 얻을 수 있습니다. 커넥터를 실행하는 머신에서 이벤트 뷰어를 열고 **애플리케이션 및 서비스 로그** > **Microsoft** > **AadApplicationProxy** > **커넥터** 로 이동하여 **관리자** 로그를 검사합니다.

## <a name="next-steps"></a>다음 단계

* [Azure AD 애플리케이션 프록시에서 사용자 지정 도메인 작업](application-proxy-configure-custom-domain.md)
* [Azure AD 애플리케이션 프록시 커넥터 이해](application-proxy-connectors.md)
