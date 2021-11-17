---
title: '자습서: EasySSO for BitBucket과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 EasySSO for BitBucket 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 7c3fd04c02e05c90673b007d7d05d61c786c9a04
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132296179"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>자습서: EasySSO for BitBucket과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 EasySSO for BitBucket을 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다. Azure AD와 EasySSO for BitBucket을 통합하면 다음을 수행할 수 있습니다.

* EasySSO for BitBucket에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 EasySSO for BitBucket에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SSO(Single Sign-On)가 가능한 EasySSO for BitBucket 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* EasySSO for BitBucket에서 SP 시작 및 IdP 시작 SSO를 지원합니다.
* EasySSO for BitBucket에서 "Just-In-Time" 사용자 프로비저닝을 지원합니다.

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>갤러리에서 EasySSO for BitBucket 추가

EasySSO for BitBucket이 Azure AD에 통합되도록 구성하려면 갤러리에서 EasySSO for BitBucket을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **EasySSO for BitBucket** 을 입력합니다.
1. 결과에서 **EasySSO for BitBucket** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-easysso-for-bitbucket"></a>EasySSO for BitBucket용 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 EasySSO for BitBucket에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 EasySSO for BitBucket의 관련 사용자 간에 연결된 관계를 설정해야 합니다.

EasySSO for BitBucket에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. [Azure AD SSO 구성](#configure-azure-ad-sso) - 사용자가 이 기능을 사용할 수 있도록 구성합니다.
    1. [Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
    1. [Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. [EasySSO for BitBucket SSO 구성](#configure-easysso-for-bitbucket-sso) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. [EasySSO for BitBucket 테스트 사용자 만들기](#create-an-easysso-for-bitbucket-test-user) - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 EasySSO for BitBucket에 만듭니다.
1. [SSO 테스트](#test-sso) - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **EasySSO for BitBucket** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾습니다. **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 선택하여 설정을 편집합니다.

   ![연필 아이콘이 강조 표시된 SAML로 Single Sign-On 설정 페이지의 스크린샷](common/edit-urls.png)

1. **IdP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. **회신 URL** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/easysso/saml` 패턴을 사용하는 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 을 선택하고, 다음 단계를 수행합니다.

    - **로그온 URL** 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 확실치 않은 경우 [EasySSO 지원 팀](mailto:support@techtime.co.nz)에 문의하여 이러한 값을 구합니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. EasySSO for BitBucket 애플리케이션에는 특정 형식의 SAML 어설션이 필요하므로, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![기본 특성의 스크린샷](common/default-attributes.png)

1. EasySSO for BitBucket 애플리케이션에는 SAML 응답에서 다시 전달되는 몇 가지 특성도 추가로 필요합니다. 이러한 내용은 다음 표에 나와 있습니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.
    
    | Name | 원본 특성|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Azure AD 사용자가 **sAMAccountName** 을 구성한 경우 **urn:oid:0.9.2342.19200300.100.1.1** 을 **sAMAccountName** 특성에 매핑해야 합니다.
    
1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 또는 **페더레이션 메타데이터 XML** 옵션의 다운로드 링크를 선택합니다. 하나 또는 둘 다를 컴퓨터에 저장합니다. 나중에 BitBucket EasySSO를 구성하는 데 필요합니다.

    ![다운로드 링크가 강조 표시된 SAML 서명 인증서 섹션 스크린샷](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    인증서를 사용하여 EasySSO for BitBucket을 수동으로 구성하려면 **로그인 URL** 과 **Azure AD 식별자** 도 복사하여 컴퓨터에 저장해야 합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 에 `B.Simon`를 입력합니다.
   1. **사용자 이름** 으로 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, 암호를 적어둡니다.
   1. **만들기** 를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 EasySSO for BitBucket에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 차례로 선택합니다.
1. 애플리케이션 목록에서 **EasySSO for BitBucket** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고, **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택합니다. **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 을 선택합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 을 선택합니다.

## <a name="configure-easysso-for-bitbucket-sso"></a>EasySSO for BitBucket SSO 구성

1. Zoom 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **Zoom 설정** 을 클릭하면 Zoom 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Zoom에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3~10단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. Zoom을 수동으로 설정하려면 다른 웹 브라우저 창에서 관리자 권한으로 Zoom 회사 사이트에 로그인합니다.

1. **관리** 섹션으로 이동합니다.

    ![기어 아이콘이 강조 표시된 BitBucket 인스턴스 스크린샷](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. **EasySSO** 를 찾아서 선택합니다.

    ![Easy SSO 옵션 스크린샷](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. **SAML** 을 선택합니다. SAML 구성 섹션으로 이동됩니다.

    ![SAML이 강조 표시된 EasySSO 관리 페이지 스크린샷](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. **인증서** 탭을 선택하면 다음 화면이 표시됩니다.

    ![다양한 옵션이 강조 표시된 인증서 탭 스크린샷](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. 이 자습서의 이전 섹션에서 저장한 **인증서(Base64)** 또는 **메타데이터 파일** 을 찾습니다. 다음 중 한 가지 방법으로 진행할 수 있습니다.

    - 컴퓨터에 로컬 파일로 다운로드한 앱 페더레이션 **메타데이터 파일** 을 사용합니다. **업로드** 라디오 단추를 선택하고 운영 체제별 경로를 따릅니다.

    - 일반 텍스트 편집기에서 앱 페더레이션 **메타데이터 파일** 을 열어서 파일 내용을 볼 수 있습니다. 클립보드에 복사합니다. **입력** 을 선택하고 텍스트 필드에 클립보드 콘텐츠를 붙여넣습니다.
 
    - 완전 수동 구성을 수행합니다. 일반 텍스트 편집기에서 앱 페더레이션 **인증서(Base64)** 를 열어서 파일 내용을 볼 수 있습니다. 클립보드에 복사하고 **IdP 토큰 서명 인증서** 텍스트 필드에 붙여넣습니다. 그런 다음, **일반** 탭으로 이동하여 **POST 바인딩 URL** 및 **엔터티 ID** 필드를 각각 앞에서 저장한 **로그인 URL** 및 **Azure AD 식별자** 값으로 채웁니다.
 
1. 페이지 아래쪽에서 **저장** 을 선택합니다. 구성 필드로 구문 분석된 메타데이터 및 인증서 파일의 내용을 볼 수 있습니다. EasySSO for BitBucket 구성이 완료되었습니다.

1. 구성을 테스트하려면 **모양 및 느낌** 탭으로 이동하여 **SAML 로그인 단추** 를 선택합니다. 이렇게 하면 특히 BitBucket 로그인 화면에서 별도의 단추를 사용하여 Azure AD SAML 통합을 엔드투엔드로 테스트할 수 있습니다. 이 단추를 켜 놓고 프로덕션 모드의 배치, 색 및 변환을 구성할 수도 있습니다.

    ![SAML 로그인 단추가 강조 표시된 SAML 페이지의 모양 및 느낌 탭 스크린샷](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >문제가 있으면 [EasySSO 지원 팀](mailto:support@techtime.co.nz)에 문의하세요.

### <a name="create-an-easysso-for-bitbucket-test-user"></a>EasySSO for BitBucket 테스트 사용자 만들기

이 섹션에서는 BitBucket에서 Britta Simon이라는 사용자를 만듭니다. EasySSO for BitBucket은 Just-In-Time 사용자 프로비저닝을 지원하며 기본적으로 사용하지 않도록 설정되어 있습니다. 사용하도록 설정하려면 EasySSO 플러그 인 구성의 **일반** 섹션에서 **성공적인 로그인 시 사용자 만들기** 옵션을 명시적으로 선택해야 합니다. BitBucket에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

하지만 사용자가 처음 로그인할 때 자동 사용자 프로비저닝이 가능하도록 설정하지 않으려면 BitBucket 인스턴스가 사용하는 사용자 디렉터리에 사용자가 있어야 합니다. 예를 들어 이 디렉터리는 LDAP 또는 Atlassian Crowd일 수 있습니다.

![성공적인 로그인 시 사용자 만들기가 강조 표시된 EasySSO 플러그 인 구성의 일반 섹션 스크린샷](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 EasySSO for BitBucket 로그온 URL로 리디렉션됩니다.

* EasySSO for BitBucket 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 EasySSO for BitBucket에 자동으로 로그인됩니다.

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 EasySSO for BitBucket 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 EasySSO for BitBucket에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

EasySSO for BitBucket이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
