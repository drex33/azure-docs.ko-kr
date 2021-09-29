---
title: '자습서: ScreenSteps와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 ScreenSteps 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: f3310e1569cd04950c768b24a333dc6d6c7bbe09
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746109"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>자습서: ScreenSteps와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 ScreenSteps를 통합하는 방법에 대해 알아봅니다.
ScreenSteps를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* ScreenSteps에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 ScreenSteps에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 연결에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On](../manage-apps/what-is-single-sign-on.md)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

ScreenSteps와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* ScreenSteps Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* ScreenSteps에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-screensteps-from-the-gallery"></a>갤러리에서 ScreenSteps 추가

ScreenSteps가 Azure AD에 통합되도록 구성하려면 갤러리에서 ScreenSteps를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 ScreenSteps를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **ScreenSteps** 를 입력하고 결과 패널에서 **ScreenSteps** 를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 ScreenSteps](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon** 이라는 테스트 사용자를 기반으로 ScreenSteps에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 ScreenSteps의 관련 사용자 간에 연결 관계를 설정해야 합니다.

ScreenSteps에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[ScreenSteps Single Sign-On 구성](#configure-screensteps-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[ScreenSteps 테스트 사용자 만들기](#create-screensteps-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 ScreenSteps에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

ScreenSteps에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **ScreenSteps** 애플리케이션 통합 페이지에서 **Single Sign-On** 을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![ScreenSteps 도메인 및 URL Single Sign-On 정보](common/sp-signonurl.png)

    **로그인 URL** 텍스트 상자에서 `https://<tenantname>.ScreenSteps.com` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 자습서 뒷부분에 설명된 실제 로그온 URL로 이 값을 업데이트합니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **ScreenSteps 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-screensteps-single-sign-on&quot;></a>ScreenSteps Single Sign-On 구성

1. 다른 웹 브라우저 창에서 ScreenSteps 회사 사이트에 관리자로 로그인합니다.

1. **계정 설정** 을 클릭합니다.

    ![계정 관리](./media/screensteps-tutorial/ic778523.png &quot;계정 관리")

1. **Single Sign-On** 을 클릭합니다.

    ![선택된 "Single Sign-On"을 보여주는 스크린샷.](./media/screensteps-tutorial/ic778524.png "원격 인증")

1. **Single Sign-on 엔드포인트 만들기** 를 클릭합니다.

    ![원격 인증](./media/screensteps-tutorial/ic778525.png "원격 인증")

1. **Single Sign-on 엔드포인트 만들기** 섹션에서 다음 단계를 수행합니다.

    ![인증 엔드포인트 만들기](./media/screensteps-tutorial/ic778526.png "인증 엔드포인트 만들기")

    a. **제목** 텍스트 상자에 제목을 입력합니다.

    b. **모드** 목록에서 **SAML** 을 선택합니다.

    다. **만들기** 를 클릭합니다.

1. 새 엔드포인트를 **편집** 합니다.

    ![엔드포인트 편집](./media/screensteps-tutorial/ic778528.png "엔드포인트 편집")

1. **Single Sign-on 엔드포인트 편집** 섹션에서 다음 단계를 수행합니다.

    ![원격 인증 엔드포인트](./media/screensteps-tutorial/ic778527.png "원격 인증 엔드포인트")

    a. **새 SAML 인증서 파일 업로드** 를 클릭한 후 Azure Portal에서 다운로드한 인증서를 업로드합니다.

    b. Azure Portal에서 복사한 **로그인 URL** 값을 **원격 로그인 URL** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    d. 사용자가 프로비전될 때 사용자를 할당할 **그룹** 을 선택합니다.

    e. **업데이트** 를 클릭합니다.

    f. **SAML 소비자 URL** 을 클립보드로 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **로그온 URL** 텍스트 상자에 붙여넣습니다.

    g. **Single Sign-On 엔드포인트 편집** 으로 돌아갑니다.

    h. **계정의 기본값으로 지정** 단추를 클릭하여 ScreenSteps에 로그온하는 모든 사용자에 대해 이 엔드포인트를 사용합니다. 또는 **사이트에 추가** 단추를 클릭하여 이 엔드포인트를 **ScreenSteps** 의 특정 사이트에 대해 사용할 수 있습니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 ScreenSteps에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **ScreenSteps** 를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **ScreenSteps** 를 선택합니다.

    ![애플리케이션 목록의 ScreenSteps 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹** 을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon** 을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-screensteps-test-user"></a>ScreenSteps 테스트 사용자 만들기

이 섹션에서는 ScreenSteps에서 Britta Simon이라는 사용자를 만듭니다. ScreenSteps 플랫폼에서 사용자를 추가하려면 [ScreenSteps 클라이언트 지원 팀](https://www.screensteps.com/contact)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 ScreenSteps 타일을 클릭하면 SSO를 설정한 ScreenSteps에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](./tutorial-list.md)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory의 조건부 액세스란?](../conditional-access/overview.md)