---
title: '자습서: LearnUpon과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 LearnUpon 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2021
ms.author: jeedes
ms.openlocfilehash: 84da4c02ec4631e9d9f33ac745c3721894cc5f29
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469515"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>자습서: LearnUpon과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 LearnUpon을 통합하는 방법에 대해 알아봅니다. Azure AD와 LearnUpon을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 LearnUpon에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 LearnUpon에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

LearnUpon과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* LearnUpon Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* LearnUpon은 **IDP** 시작 SSO를 지원합니다.

* LearnUpon은 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-learnupon-from-the-gallery"></a>갤러리에서 LearnUpon 추가

LearnUpon의 Azure AD 통합을 구성하려면 갤러리의 LearnUpon을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **LearnUpon** 을 입력합니다.
1. 결과 패널에서 **LearnUpon** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-learnupon"></a>LearnUpon에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 LearnUpon에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 LearnUpon의 관련 사용자 간에 연결 관계를 설정해야 합니다.

LearnUpon에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[LearnUpon SSO 구성](#configure-learnupon-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[LearnUpon 테스트 사용자 만들기](#create-learnupon-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 LearnUpon에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **LearnUpon** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    **회신 URL** 텍스트 상자에 `https://<companyname>.learnupon.com/saml/consumer` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 실제 회신 URL로 값을 업데이트합니다. 값을 얻으려면 [LearnUpon 클라이언트 지원 팀](https://www.learnupon.com/contact/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지에서 **지문** 을 찾습니다. 지문은 LearnUpon SAML 설정에 추가됩니다.

    ![인증서 다운로드 링크](common/certificateraw.png)

6. **LearnUpon 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 LearnUpon에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **LearnUpon** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-learnupon-sso"></a>LearnUpon SSO 구성

1. 다른 브라우저 인스턴스를 열고 관리자 계정으로 LearnUpon에 로그인합니다.

1. **설정** 탭을 클릭합니다.

    ![스크린샷은 설정 탭을 보여줍니다.](./media/learnupon-tutorial/settings.png)

1. **Single Sign On-SAML** 을 클릭한 다음 **일반 설정** 을 클릭하여 SAML 설정을 구성합니다.
   
    ![스크린샷은 일반 설정이 선택된 상태에서 선택된 Single Sign On - SAML을 보여줍니다.](./media/learnupon-tutorial/general-settings.png) 

1. **일반 설정** 섹션에서 다음 단계를 수행합니다.
   
    ![스크린샷은 설명된 값을 입력할 수 있는 일반 설정 섹션을 보여줍니다.](./media/learnupon-tutorial/values.png)  
  
    a. **사용** 을 선택합니다.

    b. **버전** 을 **2.0** 으로 선택합니다.

    다. **건너뛰기 조건** 으로 **아니오** 를 선택합니다.

    d. **SAML 토큰 게시 매개 변수 이름** 텍스트 상자에서 확인하고 인증할 SAML 어설션을 포함하는 위에 표시된 SAML 소비자 URL에 대한 요청 게시 매개 변수의 이름을 입력합니다. 예를 들어 **SAMLResponse** 입니다.

    e. **이름 식별자 형식** 텍스트 상자에서 사용자 식별자(이메일 주소)가 있는 SAML 어설션의 위치를 나타내는 값을 입력합니다(예: `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`).
  
    f. **ID 공급자 위치** 텍스트 상자에서 Azure Portal 로그인 화면에서 업로드된 아이콘을 클릭할 경우 사용자가 전송되는 위치를 나타내는 값을 입력합니다.
  
    g. **로그아웃 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.

    h. **지문 관리** 를 클릭한 다음 다운로드한 인증서의 지문을 업로드합니다.

1. **사용자 설정** 을 클릭한 후 다음 단계를 수행합니다.

     ![스크린샷은 설명된 값을 입력할 수 있는 사용자 설정 섹션을 보여줍니다.](./media/learnupon-tutorial/user-settings.png)  

    a. **이름 식별자 형식** 텍스트 상자에서 사용자 이름이 있는 SAML 어설션의 위치를 알려주는 값을 입력합니다(예: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`).
  
    b. **성 식별자 형식** 텍스트 상자에서 사용자 성이 있는 SAML 어설션의 위치를 알려주는 값을 입력합니다(예: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`).

### <a name="create-learnupon-test-user"></a>LearnUpon 테스트 사용자 만들기

이 섹션에서는 LearnUpon에서 Britta Simon이라는 사용자를 만듭니다. LearnUpon은 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. LearnUpon에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다. 사용자를 수동으로 만들어야 하는 경우 [LearnUpon 지원 팀](https://www.learnupon.com/contact/)에 문의해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 LearnUpon에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 LearnUpon 타일을 클릭하면 SSO를 설정한 LearnUpon에 자동으로 로그인되어야 합니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

LearnUpon이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
