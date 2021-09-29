---
title: '자습서: Predictix Assortment Planning과 Azure Active Directory 통합 | Microsoft Docs'
description: 이 자습서에서는 Azure Active Directory와 Predictix Assortment Planning 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 93b3305a08f7440a7ac79f349caea192a637af3e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124789886"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>자습서: Predictix Assortment Planning과 Azure Active Directory 통합

이 자습서에서는 Predictix Assortment Planning을 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.
이 통합은 다음과 같은 이점을 제공합니다.

* Azure AD를 사용하여 Predictix Assortment Planning에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Predictix Assortment Planning에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](../manage-apps/what-is-single-sign-on.md)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

Predictix Assortment Planning과 Azure AD 통합을 구성하려면 다음이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 얻을 수 있습니다.
* Single Sign-On이 설정된 Predictix Assortment Planning 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Predictix Assortment Planning에서 SP 시작 SSO를 지원합니다.

## <a name="add-predictix-assortment-planning-from-the-gallery"></a>갤러리에서 Predictix Assortment Planning 추가

Azure AD로 Predictix Assortment Planning 통합을 설정하려면 갤러리의 Predictix Assortment Planning을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory** 를 선택합니다.

    ![Azure Active Directory 선택](common/select-azuread.png)

2. **Enterprise 애플리케이션** > **모든 애플리케이션** 으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 애플리케이션을 추가하려면 창의 위쪽에서 **새 애플리케이션** 을 선택합니다.

    ![새 애플리케이션 선택](common/add-new-app.png)

4. 검색 상자에 **Predictix Assortment Planning** 을 입력합니다. 검색 결과에서 **Predictix Assortment Planning** 을 선택하고 **추가** 를 선택합니다.

     ![검색 결과](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 Britta Simon이라는 테스트 사용자를 사용하여 Predictix Assortment Planning에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On을 사용하도록 설정하려면 Azure AD 사용자와 Predictix Assortment Planning의 해당 사용자 간에 연결 관계를 설정해야 합니다.

Predictix Assortment Planning에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 단계를 완료해야 합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD Single Sign-On을 구성](#configure-azure-ad-single-sign-on)** 합니다.
2. 애플리케이션 쪽에서 **[Predictix Assortment Planning Single Sign-On을 구성](#configure-predictix-assortment-planning-single-sign-on)** 합니다.
3. **[Azure AD 테스트 사용자를 만들어](#create-an-azure-ad-test-user)** Azure AD Single Sign-On을 테스트합니다.
4. **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 사용자가 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
5. 사용자의 Azure AD 표현과 연결된 **[Predictix Assortment Planning 테스트 사용자를 만듭니다](#create-a-predictix-assortment-planning-test-user)** .
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Predictix Assortment Planning에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Predictix Assortment Planning** 애플리케이션 통합 페이지에서 **Single Sign-On** 을 클릭합니다.

    ![Single Sign-On 선택](common/select-sso.png)

2. **Single Sign-On 선택 방법** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 방법 선택](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 선택하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![편집 아이콘](common/edit-urls.png)

4. **기본 SAML 구성** 대화 상자에서 다음 단계를 수행합니다.

    ![기본 SAML 구성 대화 상자](common/sp-identifier.png)

    1. **로그온 URL** 상자에 다음 패턴으로 URL을 입력합니다.

        ```https
        https://<sub-domain>.ap.predictix.com/sso/request
        https://<sub-domain>.dev.ap.predictix.com/
        ```

    1. **식별자(엔터티 ID)** 상자에 다음 패턴으로 URL을 입력합니다.

        ```https
        https://<sub-domain>.ap.predictix.com
        https://<sub-domain>.dev.ap.predictix.com
        ```

    > [!NOTE]
    > 이러한 값은 자리 표시자입니다. 실제 로그온 URL 및 식별자를 사용해야 합니다. 이러한 값을 얻으려면 [Predictix Assortment Planning 지원 팀](https://www.infor.com/support)에 문의하세요. Azure Portal의 **기본 SAML 구성** 대화 상자에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 요구 사항에 따라 **인증서(Base64)** 옆의 **다운로드** 링크를 선택하고 해당 인증서를 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **Predictix Assortment Planning 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    1. **로그인 URL**

    1. **Azure AD 식별자**

    1. **로그아웃 URL**

### <a name="configure-predictix-assortment-planning-single-sign-on"></a>Predictix Assortment Planning Single Sign-On 구성

Predictix Assortment Planning 쪽에서 Single Sign-On을 구성하려면 Azure Portal에서 다운로드한 인증서와 복사한 URL을 [Predictix Assortment Planning 지원 팀](https://www.infor.com/support)으로 보내야 합니다. 이 팀은 SAML SSO 연결이 양쪽에서 올바르게 설정되도록 보장합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자** 를 차례로 선택하고 **모든 사용자** 를 선택합니다.

    ![모든 사용자 선택](common/users.png)

2. 다음과 같이 화면의 위쪽에서 **새 사용자** 를 선택합니다.

    ![새 사용자 선택](common/new-user.png)

3. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    1. **이름** 상자에 **BrittaSimon** 을 입력합니다.
  
    1. **사용자 이름** 상자에 **BrittaSimon@\<yourcompanydomain>\<extension>.** 을 입력합니다. (예: BrittaSimon@contoso.com)

    1. **암호 표시** 를 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.

    1. **만들기** 를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure AD Single Sign-On을 사용할 수 있도록 Britta Simon에게 Predictix Assortment Planning에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택하고 **모든 애플리케이션** 을 선택한 다음, **Predictix Assortment Planning** 을 선택합니다.

    ![Enterprise 애플리케이션](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Predictix Assortment Planning** 을 선택합니다.

    ![애플리케이션 목록](common/all-applications.png)

3. 다음과 같이 왼쪽 창에서 **사용자 및 그룹** 을 선택합니다.

    ![사용자 및 그룹 선택](common/users-groups-blade.png)

4. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.

    ![사용자 추가 선택](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon** 을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자에서, 목록에 있는 사용자에게 적절한 역할을 선택합니다. 화면의 아래쪽에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 을 선택합니다.

### <a name="create-a-predictix-assortment-planning-test-user"></a>Predictix Assortment Planning 테스트 사용자 만들기

다음으로 Predictix Assortment Planning에서 Britta Simon이라는 사용자를 만들어야 합니다. [Predictix Assortment Planning 지원 팀](https://www.infor.com/support)과 협력하여 사용자를 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

> [!NOTE]
> Azure AD 계정 보유자는 메일을 수신하고 계정을 확인하기 위한 링크를 선택해야 활성화됩니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이제 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트해야 합니다.

액세스 패널에서 Predictix Assortment Planning 타일을 선택하면 SSO를 설정한 Predictix Assortment Planning 인스턴스에 자동으로 로그온됩니다. 자세한 내용은 [내 앱 포털에서 앱 액세스 및 사용](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [SaaS 애플리케이션과 Azure Active Directory 통합을 위한 자습서](./tutorial-list.md)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory의 조건부 액세스란?](../conditional-access/overview.md)