---
title: 애플리케이션 프록시를 사용하는 Azure Active Directory의 Kerberos 기반 SSO(Single Sign-On)
description: Azure Active Directory 애플리케이션 프록시를 사용하여 Single Sign-On을 제공하는 방법을 다룹니다.
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
ms.custom: contperf-fy21q2
ms.openlocfilehash: 8aaf08b3d94db9ada52fb80d1dcb1a0e94d39894
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989763"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-sso-to-your-apps-with-application-proxy"></a>애플리케이션 프록시를 사용하는 앱에 대한 SSO(Single Sign-On)의 Kerberos 제한 위임

Windows 통합 인증으로 보안되는 애플리케이션 프록시를 통해 게시된 온-프레미스 애플리케이션에 대한 Single Sign-On을 제공할 수 있습니다. 이러한 애플리케이션은 액세스를 위해 Kerberos 티켓이 필요합니다. 애플리케이션 프록시는 KCD(Kerberos 제한된 위임)을 사용하여 이러한 애플리케이션을 지원합니다. 

Active Directory에 애플리케이션 프록시 커넥터 사용 권한을 부여하여 사용자를 가장함으로써 IWA(Windows 통합 인증)를 사용하여 애플리케이션에 Single Sign On을 사용하게 할 수 있습니다. 커넥터는 이 사용 권한을 사용하여 사용자 대신 토큰을 주고받습니다.

## <a name="how-single-sign-on-with-kcd-works"></a>KCD를 사용하는 Single Sign-On 작동 방식
이 다이어그램은 사용자가 IWA를 사용하는 온-프레미스 애플리케이션에 액세스하려고 할 때의 흐름을 설명합니다.

![Microsoft AAD 인증 흐름 다이어그램](./media/application-proxy-configure-single-sign-on-with-kcd/authdiagram.png)

1. 사용자가 URL을 입력하여 애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 액세스합니다.
2. 애플리케이션 프록시는 사전 인증을 위해 Azure AD 인증 서비스에 요청을 리디렉션합니다. 이 시점에서 Azure AD는 다단계 인증 등, 모든 적용 가능한 인증 및 권한 부여 정책을 적용합니다. 사용자가 확인되면 Azure AD에서 토큰을 만들어서 사용자에게 보냅니다.
3. 사용자는 토큰을 애플리케이션 프록시로 전달합니다.
4. 애플리케이션 프록시가 토큰의 유효성을 검사하고 해당 토큰에서 UPN(사용자 계정 이름)을 검색하면, 커넥터는 이중 인증된 보안 채널을 통해 UPN 및 SPN(서비스 주체 이름)을 가져옵니다.
5. 그런 다음 온-프레미스 AD와 KCD(Kerberos 제한 위임) 협상을 수행하여 Kerberos 토큰을 애플리케이션에 가져오는 사용자를 가장합니다.
6. Active Directory는 애플리케이션에 대한 Kerberos 토큰을 커넥터로 보냅니다.
7. 그러면 커넥터에서 AD에서 받은 Kerberos 토큰을 사용하여 원래 요청을 애플리케이션 서버에 보냅니다.
8. 애플리케이션은 응답을 커넥터로 보냅니다. 그러면 해당 응답이 애플리케이션 프록시 서비스를 거쳐 마지막으로 사용자에게 반환됩니다.

## <a name="prerequisites"></a>사전 요구 사항
IWA 애플리케이션에 대한 Single Sign-On을 시작하기 전에 사용자 환경이 다음 설정 및 구성을 갖추고 준비되었는지 확인합니다.

* SharePoint 웹앱과 같은 앱이 Windows 통합 인증을 사용하도록 설정됩니다. 자세한 내용은 [Kerberos 인증 지원 사용](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd759186(v=ws.11)) 또는 SharePoint의 경우 [SharePoint 2013에서 Kerberos 인증 계획](/SharePoint/security-for-sharepoint-server/kerberos-authentication-planning)(영문)을 참조하세요.
* 모든 앱에는 [서비스 주체 이름](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx)이 있습니다.
* 커넥터를 실행하는 서버 및 앱을 실행하는 서버가 도메인 가입 상태이고 동일한 도메인 또는 신뢰하는 도메인의 일부입니다. 도메인 가입에 대한 자세한 내용은 [컴퓨터를 도메인에 가입](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dd807102(v=ws.11))을 참조하세요.
* 커넥터를 실행하는 서버는 사용자에 대한 TokenGroupsGlobalAndUniversal 특성 읽기 권한이 있습니다. 이 기본 설정은 환경을 강화하는 보안에 의해 영향을 받았을 수 있습니다.

### <a name="configure-active-directory"></a>Active Directory 구성
Active Directory 구성은 애플리케이션 프록시 커넥터와 애플리케이션 서버가 동일한 도메인에 있는지 여부에 따라 다양합니다.

#### <a name="connector-and-application-server-in-the-same-domain"></a>동일한 도메인 내의 커넥터와 애플리케이션 서버
1. Active Directory에서 **도구** > **사용자 및 컴퓨터** 로 이동합니다.
2. 커넥터를 실행하는 서버를 선택합니다.
3. 마우스 오른쪽 단추를 클릭하고 **속성** > **위임** 을 선택합니다.
4. **지정한 서비스에 대한 위임의 경우 이 컴퓨터 신뢰** 를 선택합니다. 
5. **모든 인증 프로토콜 사용** 을 선택합니다.
6. **이 계정으로 위임된 자격 증명을 사용할 수 있는 서비스** 아래에서 해당 애플리케이션 서버의 SPN ID 값을 추가합니다. 그러면 애플리케이션 프록시 커넥터가 목록에 정의된 애플리케이션에 대해 AD에서 사용자를 가장할 수 있습니다.

   ![커넥터 SVR 속성 창 스크린샷](./media/application-proxy-configure-single-sign-on-with-kcd/properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>다른 도메인 내의 커넥터와 애플리케이션 서버
1. 도메인에 걸쳐 KCD로 작업하기 위한 필수 구성 요소 목록은 [도메인 간의 Kerberos 제한 위임](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831477(v=ws.11))을 참조하세요.
2. 웹 애플리케이션의 서비스 계정(컴퓨터 또는 전용 도메인 사용자 계정)의 `principalsallowedtodelegateto` 속성을 사용하여 애플리케이션 프록시(커넥터)에서 Kerberos 인증 위임을 사용하도록 설정합니다. 애플리케이션 서버는 `webserviceaccount`의 컨텍스트에서 실행되고 있으며 위임 서버는 `connectorcomputeraccount`입니다. `webserviceaccount` 도메인의 도메인 컨트롤러(Windows Server 2012 R2 이상 실행)에서 아래 명령을 실행합니다. 두 계정에 모두 플랫 이름(비 UPN)을 사용합니다.

   `webserviceaccount` 계정이 컴퓨터 계정인 경우 다음 명령을 사용합니다.

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   `webserviceaccount` 계정이 사용자 계정인 경우 다음 명령을 사용합니다.

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Single Sign-On 구성 
1. [애플리케이션 프록시로 애플리케이션 게시](../app-proxy/application-proxy-add-on-premises-application.md)에 설명된 지침에 따라 애플리케이션을 게시합니다. **Azure Active Directory** 를 **사전 인증 방법** 으로 선택해야 합니다.
2. 애플리케이션이 엔터프라이즈 애플리케이션 목록에 나타나면 선택하고 **Single Sign-On** 을 클릭합니다.
3. Single Sign-On 모드를 **Windows 통합 인증** 으로 설정합니다.  
4. 응용 프로그램 서버의 **내부 응용 프로그램 SPN** 을 입력합니다. 이 예제에서는 게시된 애플리케이션에 대한 SPN이 `http/www.contoso.com`입니다. 이 SPN은 커넥터가 위임된 자격 증명을 제공할 수 있는 서비스 목록에 있어야 합니다.
5. 커넥터에 대한 **위임된 로그인 ID** 를 선택하여 사용자를 대신하여 사용합니다. 자세한 내용은 [다른 온-프레미스 및 클라우드 ID로 작업](#working-with-different-on-premises-and-cloud-identities)을 참조하세요.

   ![고급 애플리케이션 구성](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  

## <a name="sso-for-non-windows-apps"></a>비 Windows 앱에 대한 SSO

클라우드에서 Azure AD가 사용자를 인증할 때 Azure AD 애플리케이션 프록시에서 Kerberos 위임 흐름이 시작됩니다. 요청이 온-프레미스에 도착하면 Azure AD 애플리케이션 프록시 커넥터는 로컬 Active Directory와 상호 작용하여 사용자 대신 Kerberos 티켓을 발급합니다. 해당 과정은 Kerberos 제한 위임(KCD)이라고 합니다. 

다음 단계에서 요청은 백 엔드 애플리케이션에 Kerberos 티켓으로 전송됩니다. 

이러한 요청에서 Kerberos 티켓을 보내는 방법을 정의하는 몇 가지 메커니즘이 있습니다. 대부분의 비 Windows 서버는 SPNEGO 토큰의 형태로 수신하고자 합니다. 이 메커니즘은 Azure AD 애플리케이션 프록시에서 지원되지만 기본적으로 사용할 수 없습니다. SPNEGO 또는 표준 Kerberos 토큰에 대해 커넥터를 구성할 수 있지만 둘 다 구성할 수는 없습니다.

SPNEGO에 대한 커넥터 컴퓨터를 구성하는 경우 해당 커넥터 그룹의 다른 모든 커넥터가 SPNEGO를 사용하여 구성되는지 확인합니다. 표준 Kerberos 토큰을 원하는 애플리케이션은 SPNEGO에 대해 구성되지 않은 다른 커넥터를 통해 라우팅되어야 합니다. 일부 웹 애플리케이션은 어떠한 구성 변경도 요구하지 않고 두 형식을 모두 허용합니다. 
 

SPNEGO를 사용하도록 설정하려면

1. 관리자 권한으로 실행되는 명령 프롬프트를 엽니다.
2. 명령 프롬프트에서 SPNEGO가 필요한 커넥터 서버 상에서 다음 명령을 실행합니다.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

비 Windows 앱은 일반적으로 도메인 이메일 주소 대신 사용자 이름 또는 SAM 계정 이름을 사용합니다. 해당 경우가 애플리케이션에 적용되는 경우 클라우드 ID를 애플리케이션 ID에 연결하도록 위임된 로그인 ID 필드를 구성해야 합니다. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>다른 온-프레미스 및 클라우드 ID로 작업
애플리케이션 프록시는 사용자가 클라우드 및 온-프레미스에서 정확히 동일한 ID를 사용한다고 가정합니다. 그러나 일부 환경에서는 회사 정책 또는 애플리케이션 종속성으로 인해 조직이 대체 ID를 사용하여 로그인해야 할 수 있습니다. 이 경우에도 Single Sign-On에 KCD를 사용할 수 있습니다. 각 애플리케이션에 **위임된 로그인 ID** 를 구성하여 Single Sign-On을 수행할 때 어떤 ID를 사용해야 하는지 지정합니다.  

이 기능을 사용하면 다른 온-프레미스 및 클라우드 ID가 있는 여러 조직이 사용자에게 다른 사용자 이름 및 암호를 입력하도록 하지 않고 클라우드에서 온-프레미스 앱으로 SSO를 갖게 할 수 있습니다. 이 작업은 다음의 조직을 포함합니다.

* 내부적으로 여러 도메인(joe@us.contoso.com, joe@eu.contoso.com)과 클라우드에서 단일 도메인(joe@contoso.com)이 있습니다.
* 내부적으로 라우팅이 가능하지 않은 도메인 이름(joe@contoso.usa)과 클라우드에서 법적 도메인이 있습니다.
* 내부적으로 도메인 이름을 사용하지 마십시오(joe).
* 온-프레미스 및 클라우드에서 다른 별칭을 사용합니다. 예제: joe-johns@contoso.com 및 joej@contoso.com  

애플리케이션 프록시를 사용하여 Kerberos 티켓을 얻기 위해 어떤 ID를 사용할지 선택할 수 있습니다. 이 설정은 애플리케이션별입니다. 이러한 옵션 중 일부는 전자 메일 주소 형식을 받아들이지 않는 시스템에 적합한 반면 다른 것들은 대체 로그인을 위해 설계되었습니다.

![위임된 로그인 ID 매개 변수 스크린샷](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

위임된 로그인 ID가 사용되는 경우 값은 조직 내의 모든 도메인 또는 포리스트에 대해 고유하지 않을 수도 있습니다. 다른 두 가지 커넥터 그룹을 사용하여 이러한 애플리케이션을 두 번 게시함으로써 이 문제를 방지할 수 있습니다. 각 애플리케이션에는 다른 사용자 대상 그룹이 있으므로 다른 도메인에 해당 커넥터를 조인할 수 있습니다.

**온-프레미스 SAM 계정 이름** 을 로그온 ID로 사용하는 경우, 커넥터를 호스트하는 컴퓨터를 해당 사용자 계정이 있는 도메인에 추가해야 합니다.

### <a name="configure-sso-for-different-identities"></a>다른 ID에 대한 SSO 구성
1. 주 ID가 전자 메일 주소가 되도록 Azure AD Connect 설정을 구성합니다(mail). 이 작업은 동기화 설정에서 **사용자 계정 이름** 필드를 변경하여 사용자 지정 프로세스의 일부로 수행됩니다. 또한 이러한 설정은 사용자가 ID 스토리지로 Azure AD를 사용하는 Office 365, Windows 10 디바이스 및 다른 애플리케이션에 로그인하는 방법을 결정합니다.  
   ![사용자 식별 스크린샷 - 사용자 계정 이름 드롭다운](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. 수정하려는 애플리케이션에 대한 애플리케이션 구성 설정에서 사용할 **위임된 로그인 ID** 를 선택합니다.

   * 사용자 계정 이름(예: joe@contoso.com)
   * 대체 사용자 계정 이름(예: joed@contoso.local)
   * 사용자 계정 이름의 사용자 이름 일부(예: joe)
   * 대체 사용자 계정 이름의 사용자 이름 일부(예: joed)
   * 온-프레미스 SAM 계정 이름(도메인 컨트롤러 구성에 따라 지정)

### <a name="troubleshooting-sso-for-different-identities"></a>다른 ID에 대한 SSO 문제 해결
SSO 프로세스에 오류가 있으면 [문제 해결](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)에서 설명한 대로 커넥터 컴퓨터 이벤트 로그에 표시됩니다.
그러나 어떤 경우에는 백 엔드 애플리케이션에 요청을 성공적으로 보내는 반면 해당 애플리케이션에서 다양한 다른 HTTP 응답으로 회신합니다. 이러한 경우 문제를 해결하려면 애플리케이션 프록시 세션 이벤트 로그에서 커넥터 컴퓨터에 있는 이벤트 번호 24029의 검사를 시작해야 합니다. 위임에 사용된 사용자 ID는 이벤트 세부 정보의 "user" 필드에 표시됩니다. 세션 로그를 켜려면 이벤트 뷰어 보기 메뉴에서 **분석 및 디버그 로그 표시** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [Kerberos 제한 위임을 사용하도록 애플리케이션 프록시 애플리케이션을 구성하는 방법](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [애플리케이션 프록시에서 발생한 문제 해결](application-proxy-troubleshoot.md)