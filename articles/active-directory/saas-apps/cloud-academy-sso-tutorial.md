---
title: '자습서: Cloud Academy - SSO와 Azure Active Directory SSO 통합'
description: 이 자습서에서는 Azure Active Directory 및 Cloud Academy - SSO 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/03/2021
ms.author: jeedes
ms.openlocfilehash: 7cbc5a5bc6cdacc6828d8609b409cfa9674fc331
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370054"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-cloud-academy---sso"></a>자습서: Cloud Academy - SSO와 Azure Active Directory SSO 통합

이 자습서에서는 Cloud Academy - SSO와 Azure AD(Azure Active Directory)를 통합하는 방법에 대해 알아봅니다. Azure AD와 Cloud Academy - SSO를 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD를 사용하여 Cloud Academy - SSO에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Cloud Academy - SSO에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SSO(Single Sign-On)가 설정된 Cloud Academy - SSO 구독

## <a name="tutorial-description"></a>자습서 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Cloud Academy - SSO에서 **SP** 시작 SSO를 지원합니다.
* Cloud Academy - SSO에서 **Just In Time** 사용자 프로비저닝을 지원합니다.
* Cloud Academy - SSO는 [자동 사용자 프로비저닝](cloud-academy-sso-provisioning-tutorial.md)을 지원합니다.

## <a name="add-cloud-academy---sso-from-the-gallery"></a>갤러리에서 Cloud Academy - SSO 추가

Cloud Academy - SSO가 Azure AD에 통합되도록 구성하려면 갤러리에서 Cloud Academy - SSO를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. 회사 계정, 학교 계정 또는 개인 Microsoft 계정을 사용하여 Azure Portal에 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Cloud Academy - SSO** 를 입력합니다.
1. 결과 패널에서 **Cloud Academy - SSO** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Cloud Academy - SSO용 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Cloud Academy - SSO에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Cloud Academy - SSO의 해당 사용자 간에 연결 관계를 설정해야 합니다.

Academy - SSO에서 Azure AD SSO를 구성하고 테스트하려면 다음과 같은 개략적인 단계를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 설정합니다.
    1. **[Azure AD 테스트 사용자를 만들어](#create-an-azure-ad-test-user)** Azure AD Single Sign-On을 테스트합니다.
    1. **[테스트 사용자에게 액세스 권한 부여](#grant-access-to-the-test-user)** - 사용자가 Azure AD Single Sign-On을 사용할 수 있도록 설정합니다.
1. 애플리케이션 쪽에서 **[Cloud Academy - SSO에 대한 Single Sign-On을 구성](#configure-single-sign-on-for-cloud-academy)** 합니다.
    1. **[Cloud Academy - SSO 테스트 사용자 만들기](#create-a-cloud-academy-test-user)** - 사용자의 Azure AD 표현에 해당하는 사용자를 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Cloud Academy - SSO** 애플리케이션 통합 페이지의 **관리** 섹션에서 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 선택하여 설정을 편집합니다.

   ![기본 SAML 구성을 편집하기 위한 연필 단추를 보여 주는 스크린샷](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 다음 URL 중 하나를 입력합니다.
    
    | 로그온 URL |
    |--------------|
    | `https://cloudacademy.com/login/enterprise/` |
    | `https://app.qa.com/login/enterprise/` |
    |
    
    b. **회신 URL** 텍스트 상자에서 다음 URL 중 하나를 입력합니다.
    
    | 회신 URL |
    |--------------|
    | `https://cloudacademy.com/labs/social/complete/saml/` |
    | `https://app.qa.com/labs/social/complete/saml/` |
    |
1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 선택하여 **앱 페더레이션 메타데이터 URL** 을 복사합니다. URL을 저장합니다.

    ![앱 페더레이션 메타데이터 URL에 대한 복사 단추를 보여 주는 스크린샷](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 를 선택합니다. **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 완료합니다.
   1. **이름** 상자에서 **B.Simon** 을 입력합니다.  
   1. **사용자 이름** 상자에 \<username>@\<companydomain>.\<extension>을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 를 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 선택합니다.

### <a name="grant-access-to-the-test-user"></a>테스트 사용자에게 액세스 권한 부여

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Cloud Academy - SSO에 대한 사용자 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.
1. 애플리케이션 목록에서 **Cloud Academy - SSO** 를 선택합니다.
1. 앱의 개요 페이지에 있는 **관리** 섹션에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **B.Simon** 을 선택하고, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 을 선택합니다.

## <a name="configure-single-sign-on-for-cloud-academy"></a>Cloud Academy에 대한 Single Sign-On 구성

1. 다른 브라우저 창에서 Cloud Academy - SSO 회사 사이트에 관리자로 로그인합니다.

1. 홈페이지에서 **Azure 통합 팀** 아이콘을 클릭한 다음, 왼쪽 메뉴에서 **설정** 을 선택합니다.

1. **INTEGRATIONS** 탭에서 **SSO** 카드를 선택합니다.

    ![통합 및 설정 옵션을 보여 주는 스크린샷](./media/cloud-academy-sso-tutorial/integrations.png)

1. **구성 시작** 을 클릭하여 SSO를 설정합니다.

    ![통합 > SSO 페이지를 보여주는 스크린샷.](./media/cloud-academy-sso-tutorial/start-configuring.png)

1. 일반 설정 페이지에서 다음 단계를 완료합니다.

    ![일반 설정의 통합을 보여주는 스크린샷.](./media/cloud-academy-sso-tutorial/general-settings.png)

    a. **SSO URL(위치)** 상자에 Azure Portal에서 복사한 로그인 URL 값을 붙여넣습니다.

    c. Azure Portal에서 다운로드한 Base64 인증서를 메모장에서 엽니다. **인증서** 상자에 콘텐츠를 붙여넣습니다.

    d. **이메일 도메인** 상자에 회사에서 사용자 이메일에 사용하는 모든 도메인 값을 입력합니다.

1. 아래 페이지의 다음 단계를 수행합니다.

    ![추가 설정의 통합을 보여주는 스크린샷.](./media/cloud-academy-sso-tutorial/additional-settings.png)

    a. **SAML 특성 매핑** 섹션에서 필요한 필드를 원본 특성 값으로 채웁니다.

    b. **보안 설정** 섹션에서 **인증 요청 서명 여부** 확인란을 선택하여 이 값을 **True** 로 설정합니다.

    다. **추가 설정(선택 사항)** 섹션에 Azure Portal에서 복사한 로그아웃 URL 값을 사용하여 **로그아웃 URL** 상자를 채웁니다.

1. **저장 및 테스트** 를 클릭합니다.

> [!NOTE]
> Cloud Academy - SSO를 구성하는 방법에 대한 자세한 내용은 [Single Sign-On 설정](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On)을 참조하세요.

### <a name="create-a-cloud-academy-test-user"></a>Cloud Academy 테스트 사용자 만들기

이 섹션에서는 Cloud Academy - SSO에서 Britta Simon이라는 사용자를 만듭니다. Cloud Academy - SSO는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Cloud Academy - SSO에 사용자가 아직 없는 경우 인증 후에 새로운 사용자가 생성됩니다.

Cloud Academy - SSO는 자동 사용자 프로비저닝도 지원합니다. 자동 사용자 프로비저닝 구성 방법에 대한 자세한 내용은 [여기](./cloud-academy-sso-provisioning-tutorial.md)에서 제공합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Cloud Academy - SSO 로그온 URL로 리디렉션됩니다. 

* Cloud Academy - SSO 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Cloud Academy - SSO 타일을 클릭하면 Cloud Academy - SSO 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.


## <a name="next-steps"></a>다음 단계

Cloud Academy - SSO가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
