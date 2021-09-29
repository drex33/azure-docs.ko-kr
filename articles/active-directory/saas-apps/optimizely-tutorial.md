---
title: '자습서: Optimizely와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Optimizely 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/24/2021
ms.author: jeedes
ms.openlocfilehash: eafd4002726e6ee25a8ad37e4fc2713b38d09af3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124808624"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>자습서: Optimizely와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Optimizely를 통합하는 방법에 대해 알아봅니다. Azure AD와 Optimizely를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Optimizely에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Optimizely에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Optimizely SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Optimizely는 **SP** 시작 SSO를 지원합니다.

## <a name="add-optimizely-from-the-gallery"></a>갤러리에서 Optimizely 추가

Optimizely의 Azure AD 통합을 구성하려면 갤러리의 Optimizely를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Optimizely** 를 입력합니다.
1. 결과 패널에서 **Optimizely** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-optimizely"></a>Optimizely에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Optimizely에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Optimizely의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Optimizely에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Optimizely SSO 구성](#configure-optimizely-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Optimizely 테스트 사용자 만들기](#create-optimizely-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Optimizely에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Optimizely** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://app.optimizely.net/<INSTANCE_NAME>` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `urn:auth0:optimizely:contoso` 패턴을 사용하는 값을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 자습서 뒷부분에 설명된 실제 로그온 URL 및 식별자로 값을 업데이트하게 됩니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. Optimizely 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집** 아이콘을 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![오른쪽 상단에 있는 "편집" 아이콘이 선택된 "사용자 특성" 대화 상자를 보여주는 스크린샷.](common/edit-attribute.png)

6. 위에서 언급한 특성 외에도, Optimizely 애플리케이션에는 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 다음 단계를 수행하여 아래 표와 같은 SAML 토큰 특성을 추가합니다.

    | 속성 | 원본 특성 |
    | ---------------| --------------- |
    | 이메일 | user.mail |
    
    a. **새 클레임 추가** 를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    !["새 클레임 추가" 및 "저장" 작업이 강조 표시된 "사용자 클레임" 대화 상자를 보여주는 스크린샷.](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스** 를 비워 둡니다.

    d. 원본을 **특성** 으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인** 을 클릭합니다.

    g. **저장** 을 클릭합니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **Optimizely 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Optimizely에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Optimizely** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-optimizely-sso"></a>Optimizely SSO 구성

1. **Optimizely** 쪽에서 Single Sign-On을 구성하려면 Optimizely 계정 관리자에게 문의하고 다운로드한 **인증서(Base64)** 및 적절히 복사한 URL을 제공합니다.

2. 전자 메일에 대한 응답으로 Optimizely는 로그온 URL(SP에서 시작한 SSO) 및 식별자(서비스 공급자 엔터티 ID) 값을 제공합니다.

    a. Optimizely에서 제공한 **SP 시작 SSO URL** 을 복사하여 Azure Portal **기본 SAML 구성** 섹션의 **로그온 URL** 텍스트 상자에 붙여넣습니다.

    b. Optimizely에서 제공한 **서비스 공급 기업 엔터티 ID** 를 복사하여 Azure Portal에서 **기본 SAML 구성** 섹션의 **식별자** 텍스트 상자에 붙여넣습니다.

3. 다른 브라우저 창에서 Optimizely 애플리케이션에 로그온합니다.

4. 오른쪽 위 구석에 있는 계정 이름을 클릭하고 **계정 설정** 을 클릭합니다.

    ![메뉴에서 "계정 설정"이 선택된 상태에서 오른쪽 위 모서리에서 선택된 계정 이름을 보여주는 스크린샷.](./media/optimizely-tutorial/settings.png)

5. 계정 탭에서 **개요** 섹션의 Single Sign-On 아래에 있는 **SSO 사용** 확인란을 선택합니다.
  
    ![Azure AD Single Sign-On](./media/optimizely-tutorial/account.png)

6. **저장** 을 클릭합니다.

### <a name="create-optimizely-test-user"></a>Optimizely 테스트 사용자 만들기

이 섹션에서는 Optimizely에서 Britta Simon이라는 사용자를 만듭니다.

1. 홈페이지에서 **Collaborators** 탭을 선택합니다.

2. 프로젝트에 새 공동 작업자를 추가하려면 **New Collaborator** 를 클릭합니다.
   
    !["협력자" 탭과 "새 협력자" 단추가 선택된 Optimizely 홈페이지를 보여주는 스크린샷.](./media/optimizely-tutorial/collaborator.png)

3. 전자 메일 주소를 입력하고 역할을 할당합니다. **초대** 를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/optimizely-tutorial/invite-collaborator.png)

4. 테스트 사용자는 이메일 초대를 받게 됩니다. 테스트 사용자는 이메일 주소를 사용하여 Optimizely에 로그인해야 합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Optimizely 로그온 URL로 리디렉션됩니다. 

* Optimizely 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Optimizely 타일을 클릭하면 Optimizely 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Optimizely를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).