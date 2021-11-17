---
title: '자습서: Absorb LMS와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory 및 Absorb LMS 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 39341f9ca95ec0206b795e02cebc9fb32ffc2767
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305367"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>자습서: Absorb LMS와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Absorb LMS를 통합하는 방법에 대해 알아봅니다. Azure AD와 Absorb LMS를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Absorb LMS에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Absorb LMS에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

Absorb LMS와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Absorb LMS Single Sign-On이 설정된 구독.

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Absorb LMS가 **IDP** 에서 시작된 SSO를 지원

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-absorb-lms-from-the-gallery"></a>갤러리에서 Absorb LMS 추가

Absorb LMS가 Azure AD에 통합되도록 구성하려면 갤러리의 Absorb LMS를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Absorb LMS** 를 입력합니다.
1. 결과 패널에서 **Absorb LMS** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-absorb-lms"></a>Absorb LMS에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Absorb LMS에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Absorb LMS의 관련 사용자 간에 연결이 형성되어야 합니다.

Absorb LMS에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Absorb LMS SSO 구성](#configure-absorb-lms-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Absorb LMS 테스트 사용자 만들기](#create-absorb-lms-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Absorb LMS에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Absorb LMS** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    **Absorb 5 - UI** 를 사용하는 경우에는 다음 구성을 사용합니다.

    a. **식별자** 텍스트 상자에 `https://<SUBDOMAIN>.myabsorb.com/account/saml` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에 `https://<SUBDOMAIN>.myabsorb.com/account/saml` 패턴을 사용하여 URL을 입력합니다.

    **Absorb 5 - New Learner Experience** 를 사용하는 경우에는 다음 구성을 사용합니다.

    a. **식별자** 텍스트 상자에서 `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에 `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [Absorb LMS 클라이언트 지원 팀](https://support.absorblms.com/hc/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. 다음 스크린샷에서는 **nameidentifier** 가 **user.userprincipalname** 과 매핑되는 기본 특성 목록을 보여줍니다.

    ![이미지](common/edit-attribute.png)

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

7. **Absorb LMS 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Absorb LMS에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Absorb LMS** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-absorb-lms-sso"></a>Absorb LMS SSO 구성

1. 새 웹 브라우저 창에서 Absorb LMS 회사 사이트에 관리자로 로그인합니다.

2. 오른쪽 상단에서 **계정** 단추를 선택합니다.

    ![계정 단추](./media/absorblms-tutorial/account.png)

3. 계정 창에서 선택 **포털 설정** 을 선택합니다.

    ![포털 설정 링크](./media/absorblms-tutorial/portal.png)

4. **Manage SSO Settings**(SSO 설정 구성) 탭을 선택합니다.

    ![사용자 탭](./media/absorblms-tutorial/sso.png)

5. **Manage Single Sign-On Settings**(Single Sign-On 설정 구성) 페이지에서 다음을 수행합니다.

    ![Single Sign-On 구성 페이지](./media/absorblms-tutorial/settings.png)

    a. **Name**(이름) 텍스트 상자에 이름(예: Azure AD Marketplace SSO)을 입력합니다.

    b. **Method**(메서드)로 **SAML** 을 선택합니다.

    c. 메모장에서 Azure Portal에서 다운로드한 인증서를 엽니다. **---BEGIN CERTIFICATE---** 및 **---END CERTIFICATE---** 태그를 제거합니다. 그런 다음 **키** 상자에 나머지 콘텐츠를 붙여넣습니다.

    d. **모드** 상자에서 **ID 공급자 시작됨** 을 선택합니다.

    e. **ID 속성** 상자에 Azure AD에서 사용자 ID로 구성한 속성을 선택합니다. 예를 들어 Azure AD에서 *nameidentifier* 를 선택한 경우 **사용자 이름** 을 선택합니다.

    f. **Signature Type**(서명 유형)으로 **Sha256** 을 선택합니다.

    g. Azure Portal 애플리케이션의 **속성** 페이지에 있는 **사용자 액세스 URL** 을 **로그인 URL** 에 붙여넣습니다.

    h. Azure Portal의 **로그온 구성** 창에서 복사한 **로그아웃 URL** 값을 **로그아웃 URL** 에 붙여넣습니다.

    i. **Automatically Redirect**(자동으로 리디렉션)를 **On**(설정)으로 토글합니다.

6. **저장** 을 선택합니다.

    ![SSO 로그인만 허용 설정/해제](./media/absorblms-tutorial/save.png)

### <a name="create-absorb-lms-test-user"></a>Absorb LMS 테스트 사용자 만들기

Azure AD 사용자가 Absorb LMS에 로그인하려면 Absorb LMS에서 해당 사용자가 설정되어 있어야 합니다. Absorb LMS의 경우 프로비전은 수동 작업입니다.

**사용자 프로비전을 구성하려면 다음 단계를 수행합니다.**

1. Absorb LMS 회사 사이트에 관리자로 로그인합니다.

2. **사용자** 창에서 **사용자** 를 선택합니다.

    ![사용자 링크](./media/absorblms-tutorial/users.png)

3. **User**(사용자) 탭을 선택합니다.

    ![새로 추가 드롭다운 목록](./media/absorblms-tutorial/add.png)

4. **사용자 추가** 페이지에서 다음을수행합니다.

    ![사용자 추가 페이지](./media/absorblms-tutorial/user.png)

    a. **이름** 텍스트 상자에 **Britta** 등의 이름을 입력합니다.

    b. **성** 텍스트 상자에 **Simon** 등의 성을 입력합니다.

    c. **사용자 이름** 텍스트 상자에 **Britta Simon** 등의 성명을 입력합니다.

    d. **Password**(암호) 상자에 사용자 암호를 입력합니다.

    e. **암호 확인** 상자에 암호를 다시 입력합니다.

    f. **활성** 전환을 **활성** 으로 설정합니다.

5. **저장** 을 선택합니다.

    ![SSO 로그인만 허용 설정/해제](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > 기본적으로 SSO에서는 사용자 프로비전을 사용하도록 설정되어 있지 않습니다. 고객이 이 기능을 사용하도록 설정하려면, [현재](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) 문서의 설명에 따라 설정해야 합니다. 또한, 사용자 프로비전은 ACS URL-`https://company.myabsorb.com/api/rest/v2/authentication/saml`을 통해 **Absorb 5 - New Learner Experience** 에서만 사용할 수 있다는 점에 유의해야 합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 Absorb LMS에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Absorb LMS 타일을 클릭하면 SSO를 설정한 Absorb LMS에 자동으로 로그인되어야 합니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Absorb LMS가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
