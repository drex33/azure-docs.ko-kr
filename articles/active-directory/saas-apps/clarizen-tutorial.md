---
title: '자습서: Clarizen One과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Clarizen One 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: ff7953abac61f08f6d609de5722db2bd7f6bc5b6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132321699"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen-one"></a>자습서: Clarizen One과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Clarizen One을 통합하는 방법에 대해 알아봅니다. Azure AD와 Clarizen One을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Clarizen One에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Clarizen One에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Clarizen One SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Clarizen One에서 **IDP** 시작 SSO를 지원합니다.
* Clarizen One은 [**자동** 사용자 프로비저닝 및 프로비저닝 해제](clarizen-one-provisioning-tutorial.md)(권장)를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-clarizen-one-from-the-gallery"></a>갤러리에서 Clarizen One 추가

Clarizen One이 Azure AD에 통합되도록 구성하려면 갤러리의 Clarizen One을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Clarizen One** 을 입력합니다.
1. 결과 패널에서 **Clarizen One** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-clarizen-one"></a>Clarizen One에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Clarizen One에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Clarizen One의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Clarizen One에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Clarizen One SSO 구성](#configure-clarizen-one-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Clarizen 테스트 사용자 만들기](#create-clarizen-one-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Clarizen One에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Clarizen One** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **SAML로 Single Sign-On 설정** 페이지에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에서 `Clarizen` 값을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 URL `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`을 입력합니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **Clarizen One 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Clarizen One에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Clarizen One** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-clarizen-one-sso"></a>Clarizen One SSO 구성

1. 다른 웹 브라우저 창에서 Clarizen One 회사 사이트에 관리자로 로그인합니다.

1. 사용자 이름을 클릭한 다음 **설정** 을 클릭합니다.

    ![사용자 이름 아래에서 "설정" 클릭](./media/clarizen-tutorial/setting.png "설정")

1. **전역 설정** 탭을 클릭합니다. 그런 다음 **페더레이션 인증** 옆에 있는 **편집** 을 클릭합니다.

    !["글로벌 설정" 탭](./media/clarizen-tutorial/authentication.png "전역 설정")

1. **페더레이션 인증** 대화 상자에서 다음 단계를 수행합니다.

    !["페더레이션 인증" 대화 상자](./media/clarizen-tutorial/federated-authentication.png "페더레이션 인증")

    a. **페더레이션 인증 사용** 을 선택합니다.

    b. **업로드** 를 클릭하여 다운로드한 인증서를 업로드합니다.

    다. **로그인 URL** 상자에 Azure AD 애플리케이션 구성 창의 **로그인 URL** 값을 입력합니다.

    d. **로그아웃 URL** 상자에 Azure AD 애플리케이션 구성 창의 **로그아웃 URL** 값을 입력합니다.

    e. **POST 사용** 을 선택합니다.

    f. **저장** 을 클릭합니다.

### <a name="create-clarizen-one-test-user"></a>Clarizen One 테스트 사용자 만들기

이 섹션은 Clarizen One에서 Britta Simon이라는 사용자를 만들기 위한 것입니다.

**사용자를 수동으로 만들어야 할 경우 다음 단계를 수행합니다.**

Azure AD 사용자가 Clarizen One에 로그인할 수 있도록 하려면 사용자 계정을 프로비저닝해야 합니다. Clarizen One의 경우 프로비저닝이 수동 작업입니다.

1. Clarizen One 회사 사이트에 관리자 권한으로 로그인합니다.

2. **피플** 을 클릭합니다.

    !["피플" 클릭](./media/clarizen-tutorial/people.png "사람")

3. **사용자 초대** 를 클릭합니다.

    !["사용자 초대" 단추](./media/clarizen-tutorial/user.png "사용자 초대")

1. **피플 초대** 대화 상자에서 다음 단계를 수행합니다.

    !["피플 초대" 대화 상자](./media/clarizen-tutorial/invite-people.png "피플 초대")

    a. **전자 메일** 상자에 Britta Simon 계정의 전자 메일 주소를 입력합니다.

    b. **초대** 를 클릭합니다.

    > [!NOTE]
    > Azure Active Directory 계정 보유자는 활성화되기 전에 전자 메일을 받고 링크를 따라 계정을 확인합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 Clarizen One에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Clarizen One 타일을 클릭하면 SSO를 설정한 Clarizen One에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Clarizen One이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
