---
title: '자습서: Britive와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Britive 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2021
ms.author: jeedes
ms.openlocfilehash: 82230b0cb4f78e95dca7331ac9284503aea2fc48
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630592"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-britive"></a>자습서: Britive와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Britive를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다. Britive를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Britive에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Britive에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Britive SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Britive에서 **SP** 시작 SSO를 지원합니다.
* Britive에서 [자동화된 사용자 프로비저닝](britive-provisioning-tutorial.md)을 지원합니다.

## <a name="adding-britive-from-the-gallery"></a>갤러리에서 Britive 추가

Britive가 Azure AD에 통합되도록 구성하려면 갤러리의 Britive를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Britive** 를 입력합니다.
1. 결과 패널에서 **Britive** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-britive"></a>Britive에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Britive에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Britive의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Britive에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Britive SSO 구성](#configure-britive-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Britive 테스트 사용자 만들기](#create-britive-test-user)** - B.Simon의 Azure AD 표현과 연결된 사용자를 Britive에서 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Britive** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<TENANTNAME>.britive-app.com/sso` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `urn:amazon:cognito:sp:<UNIQUE_ID>` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이 자습서 뒷부분에 설명된 실제 로그온 URL 및 식별자로 값을 업데이트합니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Britive 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Britive에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Britive** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-britive-sso"></a>Britive SSO 구성

1. 다른 웹 브라우저 창에서 Britive 웹 사이트에 관리자로 로그인합니다.

1. **관리자 설정 아이콘** 을 클릭하고 **Security**(보안)을 선택합니다.

    ![스크린샷은 설정 및 보안이 선택된 Britive 웹 사이트를 보여줍니다.](./media/britive-tutorial/security.png)

1. **SSO Configuration**(SSO 구성)을 선택하고 다음 단계를 수행합니다.

    ![스크린샷은 이 단계에서 정보를 입력하는 SSO 구성을 보여줍니다.](./media/britive-tutorial/configuration.png)

    a. **Audience/Entity ID**(대상/엔터티 ID) 값을 복사하여 Azure Portal에 있는 **기본 SAML 구성** 섹션의 **식별자(엔터티 ID)** 텍스트 상자에 붙여넣습니다.

    b. **Initiate SSO URL**(SSO URL 시작) 값을 복사하여 Azure Portal에 있는 **기본 SAML 구성** 섹션의 **로그온 URL** 텍스트 상자에 붙여넣습니다.

    다. **UPLOAD SAML METADATA**(SAML 메타데이터 업로드)를 클릭하여 Azure Portal에서 다운로드한 메타데이터 XML 파일을 업로드합니다. 메타데이터 파일을 업로드하면 위의 값이 자동으로 채워지고 변경 내용이 저장됩니다.

### <a name="create-britive-test-user"></a>Britive 테스트 사용자 만들기

1. 다른 웹 브라우저 창에서 Britive 웹 사이트에 관리자로 로그인합니다.

1. **관리자 설정 아이콘** 을 클릭하고 **User Administration**(사용자 관리)을 선택합니다.

    ![스크린샷은 설정 및 사용자 관리가 선택된 Britive 웹 사이트를 보여줍니다.](./media/britive-tutorial/user.png)

1. **ADD USER**(사용자 추가)를 클릭합니다.

    ![스크린샷은 사용자 추가 단추를 보여줍니다.](./media/britive-tutorial/add-user.png)

1. 조직 요구 사항에 따라 사용자의 필요한 세부 정보를 모두 채우고 **ADD USER**(사용자 추가)를 클릭합니다.

    ![스크린샷은 사용자 정보를 입력하는 사용자 추가 페이지를 보여줍니다.](./media/britive-tutorial/user-fields.png)

> [!NOTE]
>Britive에서 자동화된 사용자 프로비저닝도 지원합니다. 자동화된 사용자 프로비저닝 구성 방법에 대한 자세한 내용은 [여기](./britive-provisioning-tutorial.md)를 참조하세요.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Britive 로그온 URL로 리디렉션됩니다. 

* Britive 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Britive 타일을 클릭하면 Britive 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Britive를 구성하면 세션 제어를 적용하여 조직의 중요한 데이터 반출과 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
