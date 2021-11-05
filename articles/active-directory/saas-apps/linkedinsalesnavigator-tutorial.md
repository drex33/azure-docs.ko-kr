---
title: '자습서: LinkedIn Sales Navigator와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 LinkedIn Sales Navigator 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/10/2021
ms.author: jeedes
ms.openlocfilehash: 5674e27332d96a70fd1d2825dd4910dce11b861f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057018"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-sales-navigator"></a>자습서: LinkedIn Sales Navigator와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 LinkedIn Sales Navigator를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다. Azure AD와 LinkedIn Sales Navigator를 통합하면 다음을 수행할 수 있습니다.

* LinkedIn Sales Navigator에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 LinkedIn Sales Navigator에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* LinkedIn Sales Navigator SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* LinkedIn Sales Navigator에서 **SP 및 IDP** 시작 SSO를 지원합니다.
* LinkedIn Sales Navigator에서 **Just-In-Time** 사용자 프로비저닝을 지원합니다.
* LinkedIn Sales Navigator에서 **자동화된** 사용자 프로비저닝을 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-linkedin-sales-navigator-from-the-gallery"></a>갤러리에서 LinkedIn Sales Navigator 추가

LinkedIn Sales Navigator가 Azure AD에 통합되도록 구성하려면 LinkedIn Sales Navigator를 갤러리에서 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **LinkedIn Sales Navigator** 를 입력합니다.
1. 결과 패널에서 **LinkedIn Sales Navigator** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-linkedin-sales-navigator"></a>LinkedIn Sales Navigator에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 LinkedIn Sales Navigator에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 LinkedIn Sales Navigator의 관련 사용자 간에 연결 관계를 설정해야 합니다.

LinkedIn Sales Navigator에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[LinkedIn Sales Navigator SSO 구성](#configure-linkedin-sales-navigator-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[LinkedIn Sales Navigator 테스트 사용자 만들기](#create-linkedin-sales-navigator-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 LinkedIn Sales Navigator에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **LinkedIn Sales Navigator** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에 **엔터티 ID** 값을 입력합니다. 이 자습서의 뒷부분에 나오는 Linkedin 포털에서 엔터티 ID 값을 복사할 것입니다.

    b. **회신 URL** 텍스트 상자에 **ACS(Assertion Consumer Access) URL** 값을 입력합니다. 이 자습서의 뒷부분에 나오는 Linkedin 포털에서 ACS(Assertion Consumer Access) Url 값을 복사할 것입니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator` 패턴을 사용하여 URL을 입력합니다.

1. LinkedIn Sales Navigator 애플리케이션에는 특정 형식의 SAML 어설션이 필요하므로 사용자 지정 특성 매핑을 SAML 토큰 특성 구성에 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 LinkedIn Sales Navigator 애플리케이션에는 아래에서 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | Name | 원본 특성|
    | --- | --- |
    | 이메일| user.mail |
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |
    | 고유한 사용자 ID | user.mail |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **LinkedIn Sales Navigator 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 LinkedIn Sales Navigator에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **LinkedIn Sales Navigator** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-linkedin-sales-navigator-sso"></a>LinkedIn Sales Navigator SSO 구성

1. 다른 웹 브라우저 창에서 **LinkedIn Sales Navigator** 웹 사이트에 관리자로 로그온합니다.

1. **계정 센터** 의 **설정** 아래에서 **전역 설정** 을 클릭합니다. 또한 드롭다운 목록에서 **Sales Navigator** 를 선택합니다.

    ![스크린샷은 Sales Navigator를 선택할 수 있는 애플리케이션 설정을 보여줍니다.](./media/linkedinsalesnavigator-tutorial/settings.png)

1. **OR Click Here to load and copy individual fields from the form**(또는 양식에서 개별 필드를 로드하여 복사하려면 여기를 클릭)을 클릭하고 다음 단계를 수행합니다.

    ![스크린샷은 설명된 값을 입력할 수 있는 Single Sign-On을 보여줍니다.](./media/linkedinsalesnavigator-tutorial/values.png)

    a. **엔터티 ID** 값을 복사하여 Azure Portal에 있는 **기본 SAML 구성** 의 **식별자** 텍스트 상자에 붙여넣습니다.

    b. **ACS(Assertion Consumer Access) Url** 값을 복사하여 Azure Portal에 있는 **기본 SAML 구성** 의 **회신 URL** 텍스트 상자에 붙여넣습니다.

1. **LinkedIn 관리 설정** 섹션으로 이동합니다. **XML 파일 업로드** 옵션을 클릭하여 Azure Portal에서 다운로드한 XML 파일을 업로드합니다.

    ![스크린샷은 XML 파일을 업로드할 수 있는 LinkedIn 서비스 공급자 SSO 설정 구성을 보여줍니다.](./media/linkedinsalesnavigator-tutorial/metadata.png)

1. **설정** 을 클릭하여 SSO를 사용하도록 설정합니다. SSO 상태가 **연결 안 됨** 에서 **연결됨** 으로 변경됩니다.

    ![스크린샷은 SSO로 사용자 인증을 활성화할 수 있는 Single Sign-On을 보여줍니다.](./media/linkedinsalesnavigator-tutorial/authentication.png)

### <a name="create-linkedin-sales-navigator-test-user"></a>LinkedIn Sales Navigator 테스트 사용자 만들기

LinkedIn Sales Navigator 애플리케이션이 JIT(Just-in-time) 사용자 프로비전을 지원하며 인증 후에 애플리케이션에서 사용자가 자동으로 만들어집니다. **Automatically assign licenses**(라이선스 자동 할당)를 활성화하여 사용자에게 라이선스를 할당합니다.

   ![Azure AD 테스트 사용자 만들기](./media/linkedinsalesnavigator-tutorial/provisioning.png)

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 LinkedIn Sales Navigator 로그온 URL로 리디렉션됩니다.  

* LinkedIn Sales Navigator 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 LinkedIn Sales Navigator에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 LinkedIn Sales Navigator 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우 SSO를 설정한 LinkedIn Sales Navigator에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

LinkedIn Sales Navigator가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
