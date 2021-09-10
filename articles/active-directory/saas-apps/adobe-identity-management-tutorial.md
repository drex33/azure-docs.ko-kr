---
title: '자습서: Adobe Identity Management와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 Adobe Identity Management 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 0b5d0e406186156e1983f0008b973aa0448092d1
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181220"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-identity-management"></a>자습서: Adobe Identity Management와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Adobe Identity Management를 통합하는 방법을 알아봅니다. Adobe Identity Management를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Adobe Identity Management에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Adobe Identity Management에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Adobe Identity Management SSO(Single Sign-On)가 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Adobe Identity Management는 **SP** 시작 SSO를 지원합니다.
* Adobe Identity Management는 [**자동** 사용자 프로비저닝 및 프로비저닝 해제](adobe-identity-management-provisioning-tutorial.md)(권장)를 지원합니다.

## <a name="adding-adobe-identity-management-from-the-gallery"></a>갤러리에서 Adobe Identity Management 추가

Adobe Identity Management가 Azure AD에 통합되도록 구성하려면 갤러리의 Adobe Identity Management를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Adobe Identity Management** 를 입력합니다.
1. 결과 패널에서 **Adobe Identity Management** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-adobe-identity-management"></a>Adobe Identity Management에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Adobe Identity Management에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Adobe Identity Management의 관련 사용자 간에 링크 관계를 설정해야 합니다.

Adobe Identity Management에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Adobe Identity Management SSO 구성](#configure-adobe-identity-management-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Adobe Identity Management 테스트 사용자 만들기](#create-adobe-identity-management-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Adobe Identity Management에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Adobe Identity Management** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에 `https://adobe.com` URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://federatedid-na1.services.adobe.com/federated/saml/metadata/alias/<CUSTOM_ID>` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 식별자 값은 실제 값이 아닙니다. 실제 식별자로 값을 업데이트하세요. [Adobe Identity Management 클라이언트 지원 팀](mailto:identity@adobe.com)에 문의하여 값을 확인하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Adobe Identity Management 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Adobe Identity Management에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Adobe Identity Management** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-adobe-identity-management-sso"></a>Adobe Identity Management SSO 구성

1. Adobe Identity Management 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **Adobe Identity Management 설정** 을 클릭하면 Adobe Identity Management 애플리케이션으로 이동합니다. 여기서 관리자 자격 증명을 입력하여 Adobe Identity Management에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-8단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. Adobe Identity Management를 수동으로 설정하려면 다른 웹 브라우저 창에서 관리자 권한으로 Adobe Identity Management 회사 사이트에 로그인합니다.

4. **설정** 탭으로 이동하여 **디렉터리 만들기** 를 클릭합니다.

    ![Adobe Identity Management 설정](./media/adobe-identity-management-tutorial/settings.png)

5. 텍스트 상자에 디렉터리 이름을 지정하고 **페더레이션된 ID** 를 선택한 후 **다음** 를 클릭합니다.

    ![Adobe Identity Management 디렉터리 만들기](./media/adobe-identity-management-tutorial/create-directory.png)

6. **기타 SAML 공급자** 를 선택하고 **다음** 을 클릭합니다.
 
    ![Adobe Identity Management SAML 공급자](./media/adobe-identity-management-tutorial/saml-providers.png)

7. **선택** 을 클릭하여 Azure Portal에서 다운로드한 **메타데이터 XML** 파일을 업로드합니다.

    ![Adobe Identity Management SAML 구성](./media/adobe-identity-management-tutorial/saml-configuration.png)

8. **완료** 를 클릭합니다.

### <a name="create-adobe-identity-management-test-user"></a>Adobe Identity Management 테스트 사용자 만들기

1. **사용자** 탭으로 이동하여 **사용자 추가** 를 클릭합니다.

    ![Adobe Identity Management 사용자 추가](./media/adobe-identity-management-tutorial/add-user.png)

2. **사용자의 이메일 주소 입력** 텍스트 상자에 **이메일 주소** 를 입력합니다.

    ![Adobe Identity Management 사용자 저장](./media/adobe-identity-management-tutorial/save-user.png)

3. **저장** 을 클릭합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Adobe Identity Management 로그온 URL로 리디렉션됩니다.

* Adobe Identity Management 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Adobe Identity Management 타일을 클릭하면 Adobe Identity Management 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Adobe Identity Management가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).