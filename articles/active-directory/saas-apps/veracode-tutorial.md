---
title: '자습서: Veracode와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory 및 Veracode 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 2d430c9c09415b3c12abcef9bd213fdd07e3d126
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320198"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>자습서: Veracode와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Veracode를 연결하는 방법에 대해 알아봅니다. Veracode를 Azure AD와 연결하면 다음을 수행할 수 있습니다.

* Veracode에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Veracode에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Veracode SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. Veracode는 ID 공급자 시작 SSO 및 Just-In-Time 사용자 프로비저닝을 지원합니다.

## <a name="add-veracode-from-the-gallery"></a>갤러리에서 Veracode 추가

Veracode의 Azure AD 통합을 구성하려면 갤러리의 Veracode를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 “Veracode”를 입력합니다.
1. 결과 패널에서 **Veracode** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-veracode"></a>Veracode에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Veracode에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Veracode의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Veracode에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
    * **[Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)** 하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Veracode SSO 구성](#configure-veracode-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Veracode 테스트 사용자 만들기](#create-veracode-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Veracode에 만듭니다.
1. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Veracode** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾습니다. **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 선택하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 애플리케이션은 미리 구성되어 있으며 필요한 URL은 이미 Azure로 미리 채워져 있습니다. **저장** 을 선택합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾습니다. **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![다운로드 링크가 강조 표시된 SAML 서명 인증서 섹션의 스크린샷](common/certificatebase64.png)

1. Veracode에서는 특정 형식의 SAML 어설션이 필요합니다. 이는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![사용자 특성 및 클레임 섹션의 스크린샷](common/default-attributes.png)

1. 또한 Veracode를 사용하려면 몇 가지 추가 특성이 SAML 응답을 통해 다시 전달되어야 합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | Name | 원본 특성|
    | ---------------| --------------- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | 이메일 |User.mail |

1. **Veracode 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL이 강조 표시된 Veracode 설정 섹션의 스크린샷](common/copy-configuration-urls.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Veracode에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Veracode** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-veracode-sso"></a>Veracode SSO 구성

1. 다른 웹 브라우저 창에서 Veracode 회사 사이트에 관리자 권한으로 로그인합니다.

1. 위쪽의 메뉴에서 **설정** > **관리자** 를 선택합니다.
   
    ![설정 아이콘과 관리자가 강조 표시된 Veracode 관리의 스크린샷](./media/veracode-tutorial/admin.png "관리")

1. **SAML** 탭을 선택합니다.

1. **조직 SAML 설정** 섹션에서 다음 단계를 수행합니다.

    ![조직 SAML 설정 섹션의 스크린샷](./media/veracode-tutorial/saml.png "관리")

    a.  **발급자** 에 대해 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    b. **어설션 서명 인증서** 의 경우 **파일 선택** 을 선택하여 Azure Portal에서 다운로드한 인증서를 업로드합니다.

    다. **자체 등록** 에 대해 **자체 등록 사용** 을 선택합니다.

1. **자체 등록 설정** 섹션에서 다음 단계를 수행한 다음, **저장** 을 선택합니다.

    ![다양한 옵션이 강조 표시된 자체 등록 설정 섹션의 스크린샷](./media/veracode-tutorial/save.png "관리")

    a. **새 사용자 활성화** 에 대해 **활성화 필요 없음** 을 선택합니다.

    b. **사용자 데이터 업데이트** 에 대해 **기본 설정 Veracode 사용자 데이터** 를 선택합니다.

    다. **SAML 특성 세부 정보** 에 대해 다음을 선택합니다.
      * **사용자 역할**
      * **정책 관리자**
      * **검토자**
      * **보안 팀장**
      * **경영진**
      * **제출자**
      * **작성자**
      * **모든 검색 형식**
      * **팀 멤버 자격**
      * **기본 팀**

### <a name="create-veracode-test-user"></a>Veracode 테스트 사용자 만들기

이 섹션에서는 Veracode에서 B.Simon이라는 사용자를 만듭니다. Veracode는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에는 작업 항목이 없습니다. Veracode에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

> [!NOTE]
> 다른 Veracode 사용자 계정 생성 도구 또는 Veracode가 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비전할 수 있습니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 Veracode에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Veracode 타일을 클릭하면 SSO를 설정한 Veracode에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Veracode가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
