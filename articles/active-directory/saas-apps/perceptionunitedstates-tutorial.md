---
title: '자습서: Azure Active Directory와 Perception United States(비 UltiPro) 통합 | Microsoft Docs'
description: Azure Active Directory와 Perception United States(비 UltiPro) 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 5b2e6ae3e7f5f3aa45be9aae219f14ed4ecfd228
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124738207"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>자습서: Azure Active Directory와 Perception United States(비 UltiPro) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Perception United States(비 UltiPro)를 통합하는 방법에 대해 알아봅니다.
Perception United States(비 UltiPro)를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Perception United States(비 UltiPro)에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정을 사용하여 Perception United States(비 UltiPro)(Single Sign-on)에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](../manage-apps/what-is-single-sign-on.md)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

Perception United States(비 UltiPro)와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Perception United States(비 UltiPro) Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Perception United States(비 UltiPro)는 **IDP** 에서 시작된 SSO를 지원합니다.

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>갤러리에서 Perception United States(비 UltiPro) 추가

Perception United States(비 UltiPro)의 Azure AD 통합을 구성하려면 갤러리의 Perception United States(비 UltiPro)를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Perception United States(비 UltiPro)를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Perception United States(비 UltiPro)** 를 입력하고 결과 패널에서 **Perception United States(비 UltiPro)** 를 선택한 다음 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Perception United States(비 UltiPro)](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon** 이라는 테스트 사용자를 기반으로 Perception United States(비 UltiPro)에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Perception United States(비 UltiPro)의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Perception United States(비 UltiPro)에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Perception United States(비 UltiPro) Single Sign-On 구성](#configure-perception-united-states-non-ultipro-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-on 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Perception United States(비 UltiPro) 테스트 사용자 만들기](#create-perception-united-states-non-ultipro-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Perception United States(비 UltiPro)에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Perception United States(비 UltiPro)에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Perception United States(비 UltiPro)** 애플리케이션 통합 페이지에서 **Single Sign-On** 을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **SAML로 Single Sign-On 설정** 페이지에서 다음 단계를 수행합니다.

    ![Perception United States(비 UltiPro) 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 텍스트 상자에 URL을 입력합니다. `https://perception.kanjoya.com/sp`

    b. **회신 URL** 텍스트 상자에서 `https://perception.kanjoya.com/sso?idp=<entity_id>` 패턴을 사용하여 URL을 입력합니다.

    다. **Perception United States(비 UltiPro)** 애플리케이션은 <entity_id>와 같은 **Azure AD 식별자** 값을 요구합니다. 이 값을 **Perception United States(비 UltiPro) 설정** 섹션에서 가져와서 URI로 인코딩할 수 있습니다. URI 인코딩 값을 가져오려면 **http://www.url-encode-decode.com/** 링크로 이동합니다.

    d. URI 인코딩된 값을 얻은 후 아래에서 설명된 것과 같이 **회신 URL** 과 결합합니다.

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. **회신 URL** 텍스트 상자에 위의 값을 붙여넣습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **Perception United States(비 UltiPro) 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Perception United States(비 UltiPro) Single Sign-On 구성

1. 다른 웹 브라우저 창에서 Perception United States(비 UltiPro) 회사 사이트에 관리자로 로그인합니다.

2. 주 도구 모음에서 **계정 설정** 을 클릭합니다.

    ![주 도구 모음에서 선택한 "계정 설정"을 보여주는 스크린샷.](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. **계정 설정** 페이지에서 다음 단계를 수행합니다.

    ![Perception United States(비 UltiPro) 사용자](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. **회사 이름** 텍스트 상자에 **회사** 의 이름을 입력합니다.
    
    b. **계정 이름** 텍스트 상자에 **계정** 의 이름을 입력합니다.

    다. **기본 회신 전자 메일** 텍스트 상자에 유효한 **전자 메일** 을 입력합니다.

    d. **SAML 2.0** 으로 **SSO ID 공급자** 를 선택합니다.

4. **SSO 구성** 페이지에서 다음 단계를 수행합니다.

    ![Perception United States(비 UltiPro) SSO 구성](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. **전자 메일** 로 **SAML NameID 유형** 을 선택합니다.

    b. **SSO 구성 이름** 텍스트 상자에 **구성** 의 이름을 입력합니다.
    
    다. **ID 공급자 이름** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다. 

    d. **SAML 도메인 텍스트 상자** 에 @contoso.com과 같은 도메인을 입력합니다.

    e. **다시 업로드** 를 클릭하여 **메타데이터 XML** 파일을 업로드합니다.

    f. **업데이트** 를 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자** 를 차례로 선택하고 **모든 사용자** 를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자** 를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon** 을 입력합니다.
  
    b. **사용자 이름** 필드에 brittasimon@yourcompanydomain.extension을 입력합니다. 예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 Perception United States(비 UltiPro)에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Perception United States(비 UltiPro)** 를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Perception United States(비 UltiPro)** 를 선택합니다.

    ![애플리케이션 목록의 Perception United States(비 UltiPro) 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹** 을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon** 을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Perception United States(비 UltiPro) 테스트 사용자 만들기

이 섹션에서는 Perception United States(비 UltiPro)에서 Britta Simon이라는 사용자를 만듭니다. Perception United States(비 UltiPro) 플랫폼에 사용자를 추가하려면 [Perception United States(비 UltiPro) 지원 팀](https://www.ultimatesoftware.com/Contact/ContactUs)에 문의하세요.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Perception United States(비 UltiPro) 타일을 클릭하면 SSO가 설정된 Perception United States(비 UltiPro)에 자동으로 로그온됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](./tutorial-list.md)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory의 조건부 액세스란?](../conditional-access/overview.md)