---
title: '자습서: Sprinklr와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Sprinklr 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2021
ms.author: jeedes
ms.openlocfilehash: e87a80df2ca97cf50a855af9422308f440a9dd4d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124800938"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>자습서: Sprinklr와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Sprinklr을 통합하는 방법에 대해 알아봅니다. Sprinklr을 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Sprinklr에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Sprinklr에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Sprinklr SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Sprinklr은 **SP** 시작 SSO를 지원합니다.

## <a name="add-sprinklr-from-the-gallery"></a>갤러리에서 Sprinklr 추가

Sprinklr의 Azure AD 통합을 구성하려면 갤러리의 Sprinklr을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Sprinklr** 를 입력합니다.
1. 결과 패널에서 **Sprinklr** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-sprinklr"></a>Sprinklr에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Sprinklr에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Sprinklr의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Sprinklr에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Sprinklr SSO 구성](#configure-sprinklr-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Sprinklr 테스트 사용자 만들기](#create-sprinklr-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Sprinklr에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Sprinklr** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<SUBDOMAIN>.sprinklr.com` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<SUBDOMAIN>.sprinklr.com` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Sprinklr 클라이언트 지원 팀](https://www.sprinklr.com/contact-us/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **Sprinklr 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Sprinklr에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Sprinklr** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 &quot;기본 액세스&quot; 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name=&quot;configure-sprinklr-sso&quot;></a>Sprinklr SSO 구성

1. 다른 웹 브라우저 창에서 Sprinklr 회사 사이트에 관리자로 로그인합니다.

1. **관리 \> 설정** 으로 이동합니다.

    ![관리](./media/sprinklr-tutorial/settings.png &quot;관리")

1. 왼쪽 창에서 **파트너 관리 \> Single Sign on** 으로 이동합니다.

    ![파트너 관리](./media/sprinklr-tutorial/users.png "파트너 관리")

1. **+Single Sign On 추가** 를 클릭합니다.

    ![스크린샷은 Single Sign-On 추가 단추를 보여줍니다.](./media/sprinklr-tutorial/add-user.png "SSO(Single Sign-On)")

1. **Single Sign on** 페이지에서 다음 단계를 수행합니다.

    ![스크린샷은 설명된 값을 입력할 수 있는 Single Sign-On 페이지를 보여줍니다.](./media/sprinklr-tutorial/configuration.png "SSO(Single Sign-On)")

    a. **이름** 텍스트 상자에 구성에 사용할 이름을 입력합니다(예: **WAADSSOTest**).

    b. **사용** 을 선택합니다.

    다. **새 SSO 인증서 사용** 을 선택합니다.

    d. Base 64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 **ID 공급자 인증서** 텍스트 상자에 붙여넣습니다.

    e. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **엔터티 ID** 텍스트 상자에 붙여넣습니다.

    f. Azure Portal에서 복사한 **로그인 URL** 값을 **ID 공급 기업 로그인 URL** 텍스트 상자에 붙여넣습니다.

    g. Azure Portal에서 복사한 **로그아웃 URL** 값을 **ID 공급 기업 로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    h. **SAML 사용자 ID 형식** 에서 **어설션에 사용자의 sprinklr.com 사용자 이름 포함** 을 선택합니다.

    i. **SAML 사용자 ID 위치** 로 **Subject 문의 NameIdentifier 요소에 사용자 ID 포함** 을 선택합니다.

    j. **저장** 을 클릭합니다.

    ![SAML](./media/sprinklr-tutorial/save-configuration.png "SAML")

### <a name="create-sprinklr-test-user"></a>Sprinklr 테스트 사용자 만들기

1. Sprinklr 회사 사이트에 관리자 권한으로 로그인합니다.

1. **관리 \> 설정** 으로 이동합니다.

    ![관리](./media/sprinklr-tutorial/settings.png "관리")

1. 왼쪽 창에서 **클라이언트 관리 \> 사용자** 로 이동합니다.

    ![스크린샷은 설정/사용자의 사용자 추가 단추를 보여줍니다.](./media/sprinklr-tutorial/client.png "설정")

1. **사용자 추가** 를 클릭합니다.

    ![스크린샷은 설명된 값을 입력할 수 있는 사용자 편집 대화 상자를 보여줍니다.](./media/sprinklr-tutorial/search-users.png "설정")

1. **사용자 편집** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 편집](./media/sprinklr-tutorial/update-users.png "사용자 편집")

    a. **메일**, **이름** 및 **성** 텍스트 상자에 프로비전하려는 Azure AD 사용자 계정 정보를 입력합니다.

    b. **암호 사용 안 함** 을 선택합니다.

    다. **언어** 를 선택합니다.

    d. **사용자 유형** 을 선택합니다.

    e. **업데이트** 를 클릭합니다.

    > [!IMPORTANT]
    > **암호 사용 안 함** 을 선택해야 합니다. 

1. **역할** 로 이동하고 다음 단계를 수행하십시오.

    ![파트너 역할](./media/sprinklr-tutorial/role.png "파트너 역할")

    a. **글로벌** 목록에서 **ALL_Permissions** 를 선택합니다.  

    b. **업데이트** 를 클릭합니다.

> [!NOTE]
> 다른 Sprinklr 사용자 계정 생성 도구 또는 Sprinklr가 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비전할 수 있습니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Sprinklr 로그온 URL로 리디렉션됩니다. 

* Sprinklr 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Sprinklr 타일을 클릭하면 Sprinklr 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Sprinklr이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).