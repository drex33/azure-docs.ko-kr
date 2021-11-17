---
title: '자습서: Screencast-O-Matic과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Screencast-O-Matic 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: c1c64606600679e49ffc63c97f58a4a9a7764687
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279639"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>자습서: Screencast-O-Matic과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Screencast-O-Matic을 통합하는 방법에 대해 알아봅니다. Azure AD와 Screencast-O-Matic을 통합하는 경우 다음을 수행할 수 있습니다.

* Screencast-O-Matic에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정을 사용하여 Screencast-O-Matic에 자동으로 로그온되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Screencast-O-Matic SSO(Single Sign-On)가 설정된 구독입니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Screencast-O-Matic에서 **SP** 시작 SSO를 지원합니다.
* Screencast-O-Matic에서 **Just In Time** 사용자 프로비저닝을 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-screencast-o-matic-from-the-gallery"></a>갤러리에서 Screencast-O-Matic 추가

Screencast-O-Matic이 Azure AD에 통합되도록 구성하려면 갤러리의 Screencast-O-Matic을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Screencast-O-Matic** 을 입력합니다.
1. 결과 패널에서 **Screencast-O-Matic** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-screencast-o-matic"></a>Screencast-O-Matic에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Screencast-O-Matic에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Screencast-O-Matic의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Screencast-O-Matic에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Screencast-O-Matic SSO 구성](#configure-screencast-o-matic-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Screencast-O-Matic 테스트 사용자 만들기](#create-screencast-o-matic-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Screencast-O-Matic에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Screencast-O-Matic** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://screencast-o-matic.com/<InstanceName>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [Screencast-O-Matic 클라이언트 지원 팀](mailto:support@screencast-o-matic.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Screencast-O-Matic 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Screencast-O-Matic에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Screencast-O-Matic** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-screencast-o-matic-sso"></a>Screencast-O-Matic SSO 구성

1. Screencast-O-Matic 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

1. 브라우저에 확장을 추가한 후 **Screencast-O-Matic 설정** 을 클릭하면 Screencast-O-Matic 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Screencast-O-Matic에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3~10단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

1. Screencast-O-Matic을 수동으로 설정하려면 새 웹 브라우저 창을 열고 Screencast-O-Matic 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

1. **Subscription**(구독)을 클릭합니다.

    ![구독 옵션을 보여주는 스크린샷](./media/screencast-tutorial/subscribe.png)

1. **액세스 페이지** 섹션에서 **설정** 을 클릭합니다.

    !["설정" 단추가 선택된 "액세스 페이지" 섹션을 보여주는 스크린샷.](./media/screencast-tutorial/setup.png)

1. **액세스 설정 페이지** 에서 다음 단계를 수행합니다.

1. **Access URL**(액세스 URL) 섹션 아래에 지정된 텍스트 상자에서 인스턴스 이름을 입력합니다.

    ![인스턴스 이름 텍스트 상자가 강조 표시된 "액세스 URL" 섹션을 보여주는 스크린샷.](./media/screencast-tutorial/access-page.png)

1. **SAML User Restriction (optional)** (SAML 사용자 제한(선택 사항)) 섹션 아래에서 **Require Domain User**(도메인 사용자 필요)를 선택합니다.

1. Azure Portal에서 다운로드한 메타데이터를 업로드하기 위해 **Upload IDP Metadata XML File**(IDP 메타데이터 XML 파일 업로드) 아래에서 **Choose File**(파일 선택)을 클릭합니다.

1. **확인** 을 클릭합니다.

    ![액세스를 보여주는 스크린샷](./media/screencast-tutorial/metadata.png)

### <a name="create-screencast-o-matic-test-user"></a>Screencast-O-Matic 테스트 사용자 만들기

이 섹션에서는 Screencast-O-Matic에서 Britta Simon이라는 사용자를 만듭니다. Screencast-O-Matic은 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Screencast-O-Matic에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다. 사용자를 수동으로 만들어야 하는 경우 [Screencast-O-Matic 클라이언트 지원 팀](mailto:support@screencast-o-matic.com)에 문의하세요.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Screencast-O-Matic 로그온 URL로 리디렉션됩니다. 

* Screencast-O-Matic 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Screencast-O-Matic 타일을 클릭하면 Screencast-O-Matic 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Screencast-O-Matic을 구성하면 세션 제어를 적용하여 조직의 중요한 데이터 반출과 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
