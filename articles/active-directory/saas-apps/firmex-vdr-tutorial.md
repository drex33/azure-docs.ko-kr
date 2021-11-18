---
title: '자습서: Firmex VDR과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Firmex VDR 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.openlocfilehash: 9454456fb366b5c97f69f16cbc20b1739f8cfb32
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132334305"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-firmex-vdr"></a>자습서: Firmex VDR과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Firmex VDR을 통합하는 방법에 대해 알아봅니다. Azure AD와 Firmex VDR을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Firmex VDR에 액세스할 수 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Firmex VDR에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Firmex VDR SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Firmex VDR에서 **SP 및 IDP** 시작 SSO를 지원합니다.

* Firmex가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-firmex-vdr-from-the-gallery"></a>갤러리에서 Firmex VDR 추가

Firmex VDR이 Azure AD에 통합되도록 구성하려면 갤러리의 Firmex VDR을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Firmex VDR** 을 입력합니다.
1. 결과 패널에서 **Firmex VDR** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-single-sign-on-for-firmex-vdr"></a>Firmex VDR에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Firmex VDR에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Firmex VDR의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Firmex VDR에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Firmex VDR SSO 구성](#configure-firmex-vdr-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Firmex VDR 테스트 사용자 만들기](#create-firmex-vdr-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Firmex VDR에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Firmex VDR** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. 앱이 Azure와 이미 사전 통합되었으므로 사용자는 **기본 SAML 구성** 섹션에서 아무 단계도 수행할 필요가 없습니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그온 URL** 텍스트 상자에 `https://login.firmex.com` URL을 입력합니다.

1. **저장** 을 클릭합니다.

1. Firmex VDR 애플리케이션에는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 하는 특정 형식의 SAML 어설션이 필요합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 Firmex VDR 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | Name | 원본 특성|
    | ------------ | --------- |
    | 이메일 | user.mail |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Firmex VDR 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Firmex VDR에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Firmex VDR** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-firmex-vdr-sso"></a>Firmex VDR SSO 구성

### <a name="before-you-get-started"></a>시작하기 전에

#### <a name="what-youll-need"></a>필요한 항목

-   활성 Firmex 구독
-   SSO 서비스로서의 Azure AD
-   SSO를 구성할 IT 관리자
-   SSO를 사용하도록 설정되면 회사의 모든 사용자가 로그인/암호가 아니라 SSO를 사용하여 Firmex에 로그인해야 합니다.

#### <a name="how-long-will-this-take"></a>작업 수행 시간

SSO를 구현하는 데 몇 분 정도 걸립니다. 사이트에서 SSO를 사용하도록 설정하는 Firmex 지원 및 SSO를 사용하여 인증하는 회사의 사용자 간에는 거의 중단되지 않습니다. 아래의 단계를 따르기만 하면 됩니다.

### <a name="step-1---identify-your-companys-domains"></a>1단계 - 회사의 도메인 식별

회사의 사용자가 로그인하는 도메인을 식별합니다.

예를 들면 다음과 같습니다.

- @firmex.com
- @firmex.ca

### <a name="step-2---contact-firmex-support-with-your-domains"></a>2단계 - 도메인을 사용하여 Firmex 지원에 문의

이메일을 [Firmex 지원 팀](mailto:support@firmex.com)에 보내거나 1888 688 4042 x.11을 호출하여 Firmex 지원에 문의합니다. 도메인 정보를 전달합니다. Firmex 지원에서 해당 도메인을 **요청된 도메인** 으로 VDR에 추가합니다. 이제 관리자가 SSO를 구성해야 합니다.

경고: 사이트 관리자가 요청된 도메인을 구성할 때까지 회사의 사용자는 VDR에 로그인할 수 없습니다. 회사 이외의 사용자(즉, 게스트 사용자)는 여전히 자신의 이메일/암호를 사용하여 로그인할 수 있습니다. 구성하는 데 몇 분 정도 걸립니다.

### <a name="step-3---configure-the-claimed-domains"></a>3단계 - 요청된 도메인 구성

1. 사이트 관리자 권한으로 Firmex에 로그인합니다.
1. 왼쪽 위 모서리에서 회사 로고를 클릭합니다.
1. **SSO** 탭을 선택합니다. 그런 다음, **SSO Configuration(SSO 구성)** 을 선택합니다. 구성하려는 도메인을 클릭합니다.

    ![요청된 도메인](./media/firmex-vdr-tutorial/edit-sso.png)  

1. IT 관리자에게 다음 필드를 채우도록 요청합니다. 이러한 필드는 ID 공급자로부터 가져와야 합니다.  

    ![SSO 구성](./media/firmex-vdr-tutorial/SSO-config.png)

    a. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **엔터티 ID** 텍스트 상자에 붙여넣습니다.

    b. Azure Portal에서 복사한 **로그인 URL** 값을 **Identity Provider URL(ID 공급자 URL)** 텍스트 상자에 붙여넣습니다.

    c. **Public Key Certificate(공개 키 인증서)** - 인증을 위해 발급자가 SAML 메시지를 디지털 방식으로 서명할 수 있습니다. 메시지의 서명을 확인하기 위해 메시지 수신자가 발급자에게 속하는 것으로 알려진 공개 키를 사용합니다. 마찬가지로 메시지를 암호화하려면 최종 수신자에게 속한 공개 암호화 키를 발급자에게 알려야 합니다. 서명 및 암호화 모두에서 신뢰할 수 있는 공개 키를 미리 공유해야 합니다.  이는 **페더레이션 메타데이터 XML** 의 **X509Certificate** 입니다.

    d. **저장** 을 클릭하여 SSO 구성을 완료합니다. 변경 내용이 즉시 적용됩니다.

1. 지금은 사이트에서 SSO를 사용하도록 설정되어 있습니다.

### <a name="create-firmex-vdr-test-user"></a>Firmex VDR 테스트 사용자 만들기

이 섹션에서는 Firmex에서 B.Simon이라는 사용자를 만듭니다. [Firmex 지원 팀](mailto:support@firmex.com)과 협력하여 사용자를 Firmex 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Firmex VDR 타일을 클릭하면 SSO를 설정한 Firmex VDR에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](./tutorial-list.md)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory의 조건부 액세스란?](../conditional-access/overview.md)

- [Azure AD에서 Firmex VDR 사용해 보기](https://aad.portal.azure.com/)

- [Cloud Apps용 Microsoft Defender의 세션 제어란?](/cloud-app-security/proxy-intro-aad)

- [고급 표시 유형 및 제어를 사용하여 Firmex를 보호하는 방법](/cloud-app-security/proxy-intro-aad)
