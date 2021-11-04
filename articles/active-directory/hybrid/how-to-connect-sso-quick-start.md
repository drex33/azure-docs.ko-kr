---
title: 'Azure AD Connect: Seamless Single Sign-On - 빠른 시작 | Microsoft Docs'
description: 이 문서에서는 Azure Active Directory Seamless Single Sign-On을 시작하는 방법을 설명합니다.
services: active-directory
keywords: Azure AD Connect의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f691f2c6cff0abec7454282a239e8ede47277c6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068116"
---
# <a name="azure-active-directory-seamless-single-sign-on-quickstart"></a>Azure Active Directory Seamless Single Sign-On: 빠른 시작

## <a name="deploy-seamless-single-sign-on"></a>Seamless Single Sign-On 배포

Azure AD(Active Directory) Seamless SSO(Seamless Single Sign-On)는 회사 네트워크에 연결된 회사 데스크톱에 있을 때 사용자를 자동으로 서명합니다. Seamless SSO는 추가 온-프레미스 구성 요소가 없어도 사용자가 클라우드 기반 애플리케이션에 쉽게 액세스할 수 있습니다.

Seamless SSO를 배포하려면 다음 단계를 수행합니다.

## <a name="step-1-check-the-prerequisites"></a>1단계: 필수 구성 요소 확인

다음 필수 조건이 충족되는지 확인합니다.

* **Azure AD Connect 서버 설정**: 로그인 방법으로 [통과 인증](how-to-connect-pta.md)을 사용하는 경우 추가 필수 구성 요소 확인이 필요하지 않습니다. 로그인 방법으로 [암호 해시 동기화](how-to-connect-password-hash-synchronization.md)를 사용하고 Azure AD Connect와 Azure AD 사이에 방화벽이 있는 경우 다음을 확인합니다.
   - Azure AD Connect 버전 1.1.644.0 이상을 사용합니다. 
   - 방화벽 또는 프록시에서 허용하는 경우 포트 443을 통해 **\*.msappproxy.net** URL에 대해 허용된 목록에 연결을 추가합니다. 프록시 구성에 와일드 카드 대신 특정 URL이 필요한 경우 **tenantid.registration.msappproxy.net** 을 구성할 수 있습니다. 여기서 tenantid는 기능을 구성하는 테넌트의 GUID입니다. 조직에서 URL 기반 프록시 예외를 사용할 수 없는 경우에는 매주 업데이트되는 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 대한 액세스를 대신 허용할 수 있습니다. 이 필수 조건은 해당 기능을 사용하도록 설정한 경우에만 적용할 수 있습니다. 실제 사용자 로그인에서는 필요하지 않습니다.

    >[!NOTE]
    >Azure AD Connect 버전 1.1.557.0, 1.1.558.0, 1.1.561.0 및 1.1.614.0에는 암호 해시 동기화와 관련된 문제가 있습니다. 암호 해시 동기화를 통과 인증과 함께 사용하지 _않으려는_ 경우 자세한 내용은 [Azure AD Connect 릴리스 정보](./reference-connect-version-history.md)를 참조하세요.
    
    >[!NOTE]
    >나가는 HTTP 프록시가 있는 경우 이 URL (autologon.microsoftazuread-sso.com)이 허용 목록에 있는지 확인합니다. 와일드 카드를 사용 가능하지 않을 수 있으므로 이 URL을 명시적으로 지정해야 합니다. 

* **지원되는 Azure AD Connect 토폴로지 사용**: [여기](plan-connect-topologies.md)에서 설명한 Azure AD Connect의 지원되는 토폴로지 중 하나를 사용하는지 확인합니다.

    >[!NOTE]
    >Seamless SSO는 서로 간에 AD 트러스트가 있는지 여부를 나타내는 여러 AD 포리스트를 지원합니다.

* **도메인 관리자 자격 증명 설정**: 각 Active Directory 포리스트에 대한 도메인 관리자 자격 증명이 있어야 합니다.
    * Azure AD Connect를 통해 Azure AD에 동기화합니다.
    * Seamless SSO를 사용하도록 설정할 사용자를 포함합니다.
    
* **최신 인증 사용하도록 설정**: 이 기능에 대한 테넌트에서 [최신 인증](/office365/enterprise/modern-auth-for-office-2013-and-2016)을 사용하도록 설정해야 합니다.

* **최신 버전의 Microsoft 365 클라이언트 사용**: Microsoft 365 클라이언트(Outlook, Word, Excel 등)를 사용하여 자동 로그온 환경을 가져오려면 버전 16.0.8730.xxxx 이상을 사용해야 합니다.

## <a name="step-2-enable-the-feature"></a>2단계: 기능 활성화

[Azure AD Connect](whatis-hybrid-identity.md)를 통해 Seamless SSO를 사용하도록 설정합니다.

>[!NOTE]
> Azure AD Connect가 요구 사항을 충족하지 않는 경우 [PowerShell을 사용하여 Seamless SSO를 사용하도록 설정](tshoot-connect-sso.md#manual-reset-of-the-feature)할 수도 있습니다. Active Directory 포리스트당 둘 이상의 도메인이 있고 Seamless SSO를 사용하도록 설정하려는 도메인을 주로 사용하려는 경우 이 옵션을 사용합니다.

Azure AD Connect를 새로 설치하는 경우 [사용자 지정 설치 경로](how-to-connect-install-custom.md)를 선택합니다. **사용자 로그인** 페이지에서 **Single Sign-On 사용** 옵션을 선택합니다.

>[!NOTE]
> Sign On 메서드가 **암호 해시 동기화** 또는 **통과 인증** 인 경우만 선택에 대해 옵션을 사용할 수 있습니다.

![Azure AD Connect: 사용자 로그인](./media/how-to-connect-sso-quick-start/sso8.png)

Azure AD Connect가 이미 설치되어 있는 경우 Azure AD Connect에서 **사용자 로그인 변경** 페이지를 선택하고 **다음** 을 선택합니다. Azure AD Connect 버전 1.1.880.0 이상을 사용하는 경우 **Single Sign On 사용** 옵션이 기본적으로 선택됩니다. 이전 버전의 Azure AD Connect를 사용하는 경우 **Single Sign On 사용** 옵션을 선택합니다.

![Azure AD Connect: 사용자 로그인 변경](./media/how-to-connect-sso-quick-start/changeusersignin.png)

**Single Sign-On 사용** 페이지에 도달할 때까지 마법사를 계속 진행합니다. 각 Active Directory 포리스트에 대한 도메인 관리자 자격 증명을 제공합니다.

* Azure AD Connect를 통해 Azure AD에 동기화합니다.
* Seamless SSO를 사용하도록 설정할 사용자를 포함합니다.

마법사를 완료하면 테넌트에서 SSO Seamless를 사용하도록 설정됩니다.

>[!NOTE]
> 도메인 관리자 자격 증명은 Azure AD Connect 또는 Azure AD에 저장되지는 않습니다. 이 기능을 사용하도록 설정하는 데에만 사용됩니다.

다음 지침에 따라 Seamless SSO를 올바르게 설정했는지 확인합니다.

1. 테넌트에 대한 전역 관리자 자격 증명을 사용하여 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다.
3. **Azure AD Connect** 를 선택합니다.
4. **Seamless Single Sign-On** 기능이 **설정** 으로 표시되는지 확인합니다.

![Azure Portal: Azure AD Connect 창](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> Seamless SSO는 각 AD 포리스트의 온-프레미스 AD(Active Directory)에 Azure AD를 나타내는 `AZUREADSSOACC`라는 컴퓨터 계정을 만듭니다. 보안상의 이유로 `AZUREADSSOACC` 컴퓨터 계정은 강력하게 보호되어야 합니다. 도메인 관리자만 컴퓨터 계정을 관리할 수 있어야 합니다. 컴퓨터 계정에 대한 Kerberos 위임이 사용하지 않도록 설정되어 있고 Active Directory의 다른 계정에 컴퓨터 계정 `AZUREADSSOACC`에 대한 위임 권한이 없도록 확인하세요. 실수로 삭제될 위험이 없고 도메인 관리자만 액세스할 수 있는 OU(조직 구성 단위)에 컴퓨터 계정을 저장합니다.

>[!NOTE]
> 온-프레미스 환경에서 Pass-the-Hash 및 자격 증명 도난 완화 아키텍처를 사용하는 경우 `AZUREADSSOACC` 컴퓨터 계정이 격리 컨테이너에 저장되지 않도록 적절하게 조정합니다. 

## <a name="step-3-roll-out-the-feature"></a>3단계: 기능 배포

아래 제공된 지침을 사용하여 사용자에게 원활한 SSO를 점진적으로 롤아웃할 수 있습니다. Active Directory의 그룹 정책을 사용하여 모든 또는 선택된 사용자의 인트라넷 영역 설정에 다음 Azure AD URL을 추가하기 시작합니다.

- `https://autologon.microsoftazuread-sso.com`

또한 그룹 정책을 통해 **스크립트를 통해 상태 표시줄에 대한 업데이트 허용** 이라는 인트라넷 영역 정책 설정을 활성화해야 합니다. 

>[!NOTE]
> 다음 지침은 Windows의 Internet Explorer, Microsoft Edge 및 Google Chrome(Internet Explorer와 신뢰할 수 있는 사이트 URL 집합을 공유하는 경우)에서만 작동합니다. macOS에서 Mozilla Firefox 및 Google Chrome을 설정하는 방법에 대한 지침은 다음 섹션을 참조하세요.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>사용자의 인트라넷 영역 설정을 수정해야 하는 이유

기본적으로 브라우저는 특정 URL에서 올바른 영역(인터넷 또는 인트라넷)을 자동으로 계산합니다. 예를 들어 `http://contoso/`은 인트라넷 영역에 매핑되지만, `http://intranet.contoso.com/`은 인터넷 영역에 매핑됩니다(URL에 마침표가 포함되어 있기 때문). 브라우저는 URL이 브라우저의 인트라넷 영역에 명시적으로 추가되지 않는 한 클라우드 엔드포인트(예: Azure AD URL)에 Kerberos 티켓을 보내지 않습니다.

사용자의 인트라넷 영역 설정을 수정하는 두 가지 방법이 있습니다.

| 옵션 | 관리 고려 사항 | 사용자 환경 |
| --- | --- | --- |
| 그룹 정책 | 관리자가 인트라넷 영역 설정의 편집을 잡금니다. | 사용자가 고유한 설정을 수정할 수 없습니다. |
| 그룹 정책 기본 설정 |  관리자가 인트라넷 영역 설정을 편집하도록 허용합니다. | 사용자가 고유한 설정을 수정할 수 있습니다. |

### <a name="group-policy-option---detailed-steps"></a>"그룹 정책" 옵션 - 자세한 단계

1. 그룹 정책 관리 편집기 도구를 엽니다.
2. 일부 또는 모든 사용자에게 적용되는 그룹 정책을 편집합니다. 예를 들어 **기본 도메인 정책** 은 다음과 같습니다.
3. **사용자 구성** > **정책** > **관리 템플릿** > **Windows 구성 요소** > **Internet Explorer** > **인터넷 제어판** > **보안 페이지** 를 찾아봅니다. 그런 다음 **영역에 사이트 할당 목록** 을 선택합니다.
    !["영역에 사이트 할당 목록"이 선택된 "보안 페이지"를 보여 주는 스크린샷](./media/how-to-connect-sso-quick-start/sso6.png)
4. 정책을 사용하도록 설정한 다음, 대화 상자에서 다음 값을 입력합니다.
   - **값 이름**: Kerberos 티켓이 전달되는 Azure AD URL입니다.
   - **값**(데이터): **1** 은 인트라넷 영역을 나타냅니다.

     결과는 다음과 같습니다.

     값 이름: `https://autologon.microsoftazuread-sso.com`
  
     값(데이터): 1

   >[!NOTE]
   > 일부 사용자가 공유 키오스크에 로그인하는 경우와 같이 이러한 사용자가 Seamless SSO를 사용하지 못하게 하려면 이전 값을 **4** 로 설정합니다. 이 작업에서는 Azure AD URL이 제한된 영역에 추가되고 Seamless SSO가 항상 실패하게 됩니다.
   >

5. **확인** 을 선택한 후 **만들기** 를 선택합니다.

    ![영역 할당이 선택된 "콘텐츠 표시" 창을 보여 주는 스크린샷](./media/how-to-connect-sso-quick-start/sso7.png)

6. **사용자 구성** > **정책** > **관리 템플릿** > **Windows 구성 요소** > **Internet Explorer** > **인터넷 제어판** > **보안 페이지** > **인트라넷 영역** 을 찾아봅니다. 그런 다음 **스크립트를 통해 상태 표시줄에 대한 업데이트 허용** 을 선택합니다.

    !["스크립트를 통해 상태 표시줄에 대한 업데이트 허용"이 선택된 "인트라넷 영역" 페이지를 보여 주는 스크린샷](./media/how-to-connect-sso-quick-start/sso11.png)

7. 정책 설정을 활성화한 다음, **확인** 을 선택합니다.

    ![정책 설정이 사용하도록 설정된 "스크립트를 통해 상태 표시줄에 대한 업데이트 허용" 창을 보여 주는 스크린샷](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>"그룹 정책 기본 설정" 옵션 - 자세한 단계

1. 그룹 정책 관리 편집기 도구를 엽니다.
2. 일부 또는 모든 사용자에게 적용되는 그룹 정책을 편집합니다. 예를 들어 **기본 도메인 정책** 은 다음과 같습니다.
3. **사용자 구성** > **기본 설정** > **Windows 설정** > **레지스트리** > **새로 만들기** > **레지스트리 항목** 으로 이동합니다.

    ![선택된 "레지스트리" 및 "레지스트리 항목"을 보여 주는 스크린샷](./media/how-to-connect-sso-quick-start/sso15.png)

4. 적절한 필드에서 다음 값을 입력하고 **확인** 을 클릭합니다.
   - **키 경로**: **_Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon_**
   - **값 이름**: **_https_**
   - **값 형식**: **_REG_DWORD_**
   - **값 데이터**: **_00000001_**
 
     !["새 레지스트리 속성" 창을 보여 주는 스크린샷](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Single Sign-On](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>브라우저 고려 사항

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox(모든 플랫폼)

사용자 환경에서 [인증](https://github.com/mozilla/policy-templates/blob/master/README.md#authentication) 정책 설정을 사용하는 경우 Azure AD의 URL(`https://autologon.microsoftazuread-sso.com`)을 **SPNEGO** 섹션에 추가해야 합니다. **PrivateBrowsing** 옵션을 true로 설정하여 프라이빗 검색 모드에서 Seamless SSO를 허용할 수도 있습니다.

#### <a name="safari-macos"></a>Safari(macOS)

macOS를 실행하는 머신이 AD에 가입되어 있는지 확인합니다. macOS 디바이스의 AD 가입에 대한 지침은 이 문서에서 다루지 않습니다.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Chromium 기반 Microsoft Edge(모든 플랫폼)

환경에서 [AuthNegotiateDelegateAllowlist](/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) 또는 [AuthServerAllowlist](/DeployEdge/microsoft-edge-policies#authserverallowlist) 정책 설정을 재정의한 경우 Azure AD의 URL(`https://autologon.microsoftazuread-sso.com`)도 해당 정책 설정에 추가해야 합니다.

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Chromium 기반 Microsoft Edge(macOS 및 기타 Windows가 아닌 플랫폼)

macOS 및 기타 Windows가 아닌 플랫폼의 Chromium을 기반으로 하는 Microsoft Edge의 경우 통합 인증을 위해 허용 목록에 Azure AD URL을 추가하는 방법에 대한 자세한 내용은 [Microsoft Edge 기반 Chromium 정책 목록](/DeployEdge/microsoft-edge-policies#authserverallowlist)을 참조하세요.

#### <a name="google-chrome-all-platforms"></a>Google Chrome(모든 플랫폼)

환경에서 [AuthNegotiateDelegateAllowlist](https://chromeenterprise.google/policies/#AuthNegotiateDelegateAllowlist) 또는 [AuthServerAllowlist](https://chromeenterprise.google/policies/#AuthServerAllowlist) 정책 설정을 재정의한 경우 Azure AD의 URL(`https://autologon.microsoftazuread-sso.com`)도 해당 정책 설정에 추가해야 합니다.

#### <a name="macos"></a>macOS

타사 Active Directory 그룹 정책 확장을 사용하여 macOS 사용자의 Firefox 및 Google Chrome에 Azure AD URL을 롤아웃하는 방법은 이 문서에 포함되어 있지 않습니다.

#### <a name="known-browser-limitations"></a>알려진 브라우저 제한 사항

Seamless SSO는 브라우저가 고급 보호 모드에서 실행 중인 경우 Internet Explorer에서 작동하지 않습니다. 원활한 SSO는 Chromium을 기반으로 하는 Microsoft Edge의 다음 버전을 지원하고 설계에 따라 InPrivate 및 게스트 모드에서 작동합니다. Microsoft Edge(레거시)는 더 이상 지원되지 않습니다. 

 해당 설명서를 기준으로 하는 InPrivate 및/또는 게스트 사용자에 대해 `AmbientAuthenticationInPrivateModesEnabled`를 구성해야 할 수 있습니다.
 
   - [Microsoft Edge Chromium](/DeployEdge/microsoft-edge-policies#ambientauthenticationinprivatemodesenabled)
   - [Google Chrome](https://chromeenterprise.google/policies/?policy=AmbientAuthenticationInPrivateModesEnabled)

## <a name="step-4-test-the-feature"></a>4단계: 기능 테스트

특정 사용자에 대한 기능을 테스트하려면 다음 조건이 제대로 갖추어져 있는지 확인합니다.
  - 사용자가 회사 디바이스에 로그인합니다.
  - 디바이스가 Active Directory 도메인에 가입되어 있습니다. 디바이스가 [Azure AD 조인](../devices/overview.md)될 필요는 _없습니다_.
  - 디바이스가 회사의 유선/무선 네트워크 또는 원격 액세스 연결(예: VPN 연결)을 통해 DC(도메인 컨트롤러)에 직접 연결되어 있습니다.
  - 그룹 정책을 통해 해당 사용자에게 [기능을 롤아웃](#step-3-roll-out-the-feature)했습니다.

사용자가 암호가 아니라 사용자 이름만 입력하는 시나리오를 테스트하려면 다음을 수행합니다.
   - [https://manage.visualstudio.com](https://myapps.microsoft.com/ ) 에 로그인합니다. 지원되는 브라우저의 프라이빗 모드에서 브라우저 캐시를 삭제하거나, 새 프라이빗 브라우저 세션을 사용해야 합니다.

사용자가 사용자 이름이나 암호를 입력할 필요가 없는 시나리오를 테스트하려면 다음 중 하나를 수행합니다. 
   - `https://myapps.microsoft.com/contoso.onmicrosoft.com`에 로그인, 지원되는 브라우저의 프라이빗 모드에서 브라우저 캐시를 삭제하거나 새 프라이빗 브라우저 세션을 사용해야 합니다. *contoso* 를 테넌트의 이름으로 바꿉니다.
   - 새 프라이빗 브라우저 세션에서 `https://myapps.microsoft.com/contoso.com`에 로그인합니다. *contoso.com* 을 테넌트에서 확인된 도메인(페더레이션 도메인이 아님)으로 바꿉니다.

## <a name="step-5-roll-over-keys"></a>5단계: 키 롤오버

2단계에서, Azure AD Connect는 Seamless SSO를 사용하도록 설정한 모든 Active Directory 포리스트에서 컴퓨터 계정(Azure AD를 나타냄)을 만듭니다. 더 자세히 알아보려면 [Azure Active Directory Seamless Single Sign-On: 기술 심층 분석](how-to-connect-sso-how-it-works.md)을 참조하세요.

>[!IMPORTANT]
>컴퓨터 계정의 Kerberos 암호 해독 키가 유출된 경우 해당 AD 포리스트의 사용자에 대한 Kerberos 티켓을 생성하는 데 사용될 수 있습니다. 그런 다음, 악의적인 행위자는 손상된 사용자에 대한 Azure AD 로그인을 가장할 수 있습니다. 적어도 30일마다 한 번씩 Kerberos 암호 해독 키를 주기적으로 롤오버하는 것이 좋습니다.

키를 롤오버하는 방법에 대한 지침은 [Azure Active Directory Seamless Single Sign-On: 질문과 대답](how-to-connect-sso-faq.yml)을 참조하세요.

>[!IMPORTANT]
>이 기능을 사용하도록 설정한 후에는 이 단계를 _즉시_ 수행할 필요가 없습니다. 적어도 30일마다 Kerberos 암호 해독 키를 롤오버합니다.

## <a name="next-steps"></a>다음 단계

- [기술 심층 분석](how-to-connect-sso-how-it-works.md): Seamless Single Sign-On 기능의 작동 방식을 이해합니다.
- [질문과 대답](how-to-connect-sso-faq.yml): Azure Active Directory Seamless Single Sign-On에 대한 질문과 대답입니다.
- [문제 해결](tshoot-connect-sso.md): Seamless Single Sign-On 기능의 일반적인 문제를 해결하는 방법을 알아봅니다.
- [UserVoice](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789): Azure Active Directory 포럼을 사용하여 새 기능 요청을 제출합니다.
