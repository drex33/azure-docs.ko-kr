---
title: '자습서: Image Relay와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory와 Image Relay 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/21/2021
ms.author: jeedes
ms.openlocfilehash: 3e2430f7317f50ef1eaa042da99aaeef4511a813
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124768404"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>자습서: Image Relay와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Image Relay를 통합하는 방법을 알아봅니다. Azure AD와 Image Relay를 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서는 Image Relay에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Image Relay에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Image Relay SSO(Single Sign-On) 사용이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Image Relay에서 **SP** 시작 SSO를 지원합니다.

## <a name="add-image-relay-from-the-gallery"></a>갤러리에서 Image Relay 추가

Azure AD에 Image Relay를 통합하도록 구성하려면 갤러리의 Image Relay를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Image Relay** 를 입력합니다.
1. 결과 패널에서 **Image Relay** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-image-relay"></a>Image Relay에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Image Relay에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Image Relay의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Image Relay에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Image Relay SSO 구성](#configure-image-relay-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Image Relay 테스트 사용자 만들기](#create-image-relay-test-user)** - B.Simon의 Azure AD 표현과 연결되는 대응 사용자를 Image Relay에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Image Relay** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<COMPANYNAME>.imagerelay.com/sso/metadata` 패턴을 사용하는 URL을 입력합니다.

    b. **로그온 URL** 텍스트 상자에서 `https://<COMPANYNAME>.imagerelay.com/` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 해당 값을 실제 식별자 및 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [Image Relay 클라이언트 지원 팀](http://support.imagerelay.com/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **Image Relay 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Image Relay에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Image Relay** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-image-relay-sso"></a>Image Relay SSO 구성

1. 다른 브라우저 창에서 Image Relay 회사 사이트에 관리자로 로그인합니다.

2. 위쪽의 도구 모음에서 **사용자 및 권한** 워크로드를 클릭합니다.

    ![스크린샷은 도구 모음에서 선택된 사용자 및 사용 권한을 보여줍니다.](./media/imagerelay-tutorial/users.png) 

3. **새 사용 권한 만들기** 를 클릭합니다.

    ![스크린샷은 사용 권한 제목을 입력하는 텍스트 상자와 사용 권한 유형을 선택하는 옵션을 보여줍니다.](./media/imagerelay-tutorial/create-permission.png)

4. **Single Sign-On 설정** 워크로드에서 **이 그룹은 Single Sign On을 통해서만 로그인 가능** 확인란을 선택한 다음 **저장** 을 클릭합니다.

    ![스크린샷은 옵션을 선택할 수 있는 Single Sign-On 설정을 보여줍니다.](./media/imagerelay-tutorial/save-settings.png) 

5. **계정 설정** 으로 이동합니다.

    ![스크린샷은 계정 설정 도구 모음 옵션을 보여줍니다.](./media/imagerelay-tutorial/account.png) 

6. **Single Sign On 설정** 작업으로 이동합니다.

    ![스크린샷은 Single Sign-On 설정 메뉴 옵션을 보여줍니다.](./media/imagerelay-tutorial/settings.png)

7. **SAML 설정** 대화 상자에서 다음 단계를 수행합니다.

    ![스크린샷은 정보를 입력할 수 있는 SAML 설정 대화 상자를 보여줍니다.](./media/imagerelay-tutorial/information.png)

    a. **로그인 URL** 텍스트 상자에, Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    b. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    다. **이름 ID 형식** 으로 **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress** 를 선택합니다.

    d. **서비스 공급자(Image Relay) 요청에 대한 바인딩 옵션** 으로 **POST 바인딩** 을 선택합니다.

    e. **x.509 인증서** 에서 **인증서 업데이트** 를 클릭합니다.

    ![스크린샷은 인증서를 업데이트하는 옵션을 보여줍니다.](./media/imagerelay-tutorial/certificate.png)

    f. 다운로드한 인증서를 메모장에서 열고, 내용을 복사한 다음, 전체 인증서를 **x.509 인증서** 텍스트 상자에 붙여넣습니다.

    ![스크린샷은 x dot 509 인증서를 보여줍니다.](./media/imagerelay-tutorial/update-certificate.png)

    g. **Just-In-Time 사용자 프로비전** 섹션에서 **Just-In-Time 사용자 프로비전 사용** 을 선택합니다.

    ![스크린샷은 활성화 컨트롤이 선택된 Just-In-Time 사용자 프로비저닝 섹션을 보여줍니다.](./media/imagerelay-tutorial/provisioning.png)

    h. Single Sign-On을 통해서만 로그인할 수 있는 사용 권한 그룹(예: **SSO 기본**)을 선택합니다.

    ![스크린샷은 SSO 기본 사항이 선택된 Just-In-Time 사용자 프로비저닝 섹션을 보여줍니다.](./media/imagerelay-tutorial/user-provisioning.png)

    i. **저장** 을 클릭합니다.

### <a name="create-image-relay-test-user"></a>Image Relay 테스트 사용자 만들기

이 섹션에서는 Image Relay에서 Britta Simon이라는 사용자를 만듭니다.

**Image Relay에서 Britta Simon이라는 사용자를 만들려면 다음 단계를 수행합니다.**

1. Image Relay 회사 사이트에 관리자로 로그인합니다.

2. **사용자 및 권한** 으로 이동하여 **SSO 사용자 만들기** 를 선택합니다.

    ![스크린샷은 메뉴에서 선택한 SSO 사용자 생성을 보여줍니다.](./media/imagerelay-tutorial/create-user.png) 

3. 프로비전하려는 사용자의 **전자 메일**, **이름**, **성** 및 **회사** 를 입력하고 Single Sign-On을 통해서만 로그인할 수 있는 그룹인 권한 그룹(예: SSO 기본)을 선택합니다.

    ![스크린샷은 필요한 정보를 입력할 수 있는 SSO 사용자 만들기 페이지를 보여줍니다.](./media/imagerelay-tutorial/user-details.png)

4. **만들기** 를 클릭합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Image Relay 로그온 URL로 리디렉션됩니다. 

* Image Relay 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Image Relay 타일을 클릭하면 Image Relay 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Image Relay가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).