---
title: '자습서: TeamSeer와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 TeamSeer 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 910eb170cc8edf46fe926731edf932e62faca08b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124761003"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>자습서: TeamSeer와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 TeamSeer를 통합하는 방법에 대해 알아봅니다.
TeamSeer를 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

* TeamSeer에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 TeamSeer에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](../manage-apps/what-is-single-sign-on.md)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

TeamSeer와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* TeamSeer Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* TeamSeer는 **SP** 시작 SSO를 지원합니다.

## <a name="adding-teamseer-from-the-gallery"></a>갤러리에서 TeamSeer 추가

TeamSeer와 Azure AD 통합을 구성하려면 갤러리의 TeamSeer를 관리형 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 TeamSeer를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **TeamSeer** 를 입력하고, 결과 패널에서 **TeamSeer** 를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 TeamSeer](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon** 이라는 테스트 사용자를 기반으로 TeamSeer에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 TeamSeer의 관련 사용자 간에 연결 관계를 설정해야 합니다.

TeamSeer에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[TeamSeer Single Sign-On 구성](#configure-teamseer-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[TeamSeer 테스트 사용자 만들기](#create-teamseer-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 TeamSeer에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

TeamSeer에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **TeamSeer** 애플리케이션 통합 페이지에서 **Single Sign-On** 을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![TeamSeer 도메인 및 URL Single Sign-On 정보](common/sp-signonurl.png)

    **로그인 URL** 텍스트 상자에서 `https://www.teamseer.com/<companyid>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 값을 얻으려면 [TeamSeer 클라이언트 지원 팀](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **TeamSeer 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-teamseer-single-sign-on&quot;></a>TeamSeer Single Sign-On 구성

1. 다른 웹 브라우저 창에서 TeamSeer 회사 사이트에 관리자로 로그인합니다.

1. **HR 관리자** 로 이동합니다.

    ![스크린샷은 TeamSeer 창에서 선택한 HR 관리자를 보여줍니다.](./media/teamseer-tutorial/ic789634.png &quot;HR 관리자")

1. **설치** 를 클릭합니다.

    ![설치](./media/teamseer-tutorial/ic789635.png "설치 프로그램")

1. **SAML 공급자 세부 정보** 를 클릭합니다.

    ![스크린샷은 선택된 SAML 공급자 세부 정보 설정을 보여줍니다.](./media/teamseer-tutorial/ic789636.png "SAML 설정")

1. SAML 공급자 세부 정보 섹션에서 다음 단계를 수행합니다.

    ![스크린샷은 설명된 값을 입력할 수 있는 SAML 공급자 세부 정보를 보여줍니다.](./media/teamseer-tutorial/ic789637.png "SAML 설정")

    a. **URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    b. Base 64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 전체 인증서를 **IdP 공용 인증서** 텍스트 상자에 붙여넣습니다.

1. SMAL 공급자 구성을 완료하려면 다음 단계를 수행합니다.

    ![스크린샷은 설명된 값을 입력할 수 있는 SAML 공급자 구성을 보여줍니다.](./media/teamseer-tutorial/ic789638.png "SAML 설정")

    a. **테스트 이메일 주소** 에 테스트 사용자의 이메일 주소를 입력합니다.
  
    b. **발급자** 텍스트 상자에 서비스 공급자의 발급자 URL을 입력합니다.
  
    다. **저장** 을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자** 를 차례로 선택하고 **모든 사용자** 를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자** 를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon** 을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension** 을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 TeamSeer에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **TeamSeer** 를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **TeamSeer** 를 선택합니다.

    ![애플리케이션 목록의 TeamSeer 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹** 을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon** 을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-teamseer-test-user&quot;></a>TeamSeer 테스트 사용자 만들기

Azure AD 사용자가 TeamSeer에 로그인할 수 있도록 하려면 ShiftPlanning으로 프로비저닝되어야 합니다. TeamSeer의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **TeamSeer** 회사 사이트에 관리자 권한으로 로그인합니다.

1. **HR 관리자 \> 사용자** 로 이동하여 **새 사용자 마법사 실행** 을 클릭합니다.

    ![스크린샷은 실행할 마법사를 선택할 수 있는 HR 관리자 탭을 보여줍니다.](./media/teamseer-tutorial/ic789640.png &quot;HR 관리자")

1. **사용자 세부 정보** 섹션에서 다음 단계를 수행합니다.

    ![사용자 세부 정보](./media/teamseer-tutorial/ic789641.png "사용자 세부 정보")

    a. 관련 텍스트 상자에 프로비저닝할 유효한 Azure AD 계정의 **이름**, **성**, **사용자 이름(이메일 주소)** 을 입력합니다.
  
    b. **다음** 을 클릭합니다.

1. 새 사용자를 추가하기 위한 화면의 지시를 따르고 **마침** 을 클릭합니다.

> [!NOTE]
> 다른 TeamSeer 사용자 계정 생성 도구 또는 TeamSeer가 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비전할 수 있습니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 TeamSeer 타일을 클릭하면 SSO를 설정한 TeamSeer에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](./tutorial-list.md)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory의 조건부 액세스란?](../conditional-access/overview.md)