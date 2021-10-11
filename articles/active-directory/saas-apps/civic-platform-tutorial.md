---
title: '자습서: Civic Platform과 Azure AD SSO 통합'
description: Azure Active Directory 및 Civic Platform 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2021
ms.author: jeedes
ms.openlocfilehash: 1a966c99cc167180cd47406c8909bfbd48a11422
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402447"
---
# <a name="tutorial-azure-ad-sso-integration-with-civic-platform"></a>자습서: Civic Platform과 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Civic Platform을 통합하는 방법에 대해 알아봅니다. Azure AD와 Civic Platform을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Civic Platform에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Civic Platform에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Civic Platform SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Civic Platform에서 **SP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-civic-platform-from-the-gallery"></a>갤러리에서 Civic Platform 추가

Civic Platform이 Azure AD에 통합되도록 구성하려면 갤러리에서 Civic Platform을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Civic Platform** 을 입력합니다.
1. 결과 패널에서 **Civic Platform** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-civic-platform"></a>Civic Platform에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Civic Platform에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Civic Platform의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Civic Platform에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Civic Platform SSO 구성](#configure-civic-platform-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Civic Platform 테스트 사용자 만들기](#create-civic-platform-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Civic Platform에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Civic Platform** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자(엔터티 ID)** 텍스트 상자에 값을 `civicplatform.accela.com`로 입력합니다.

    b. **로그온 URL** 텍스트 상자에서 `https://<SUBDOMAIN>.accela.com` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 로그온 URL 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이 값을 얻으려면 [Civic Platform 지원 팀](mailto:skale@accela.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL** 을 복사한 후 컴퓨터에 저장합니다.

    ![스크린샷은 앱 페더레이션 메타데이터 URL을 복사할 수 있는 SAML 서명 인증서 페이지를 보여줍니다.](common/copy-metadataurl.png)

1. Azure AD에서 **Azure Active Directory** > **앱 등록** 으로 이동하고, 애플리케이션을 선택합니다.

1. **디렉터리(테넌트) ID** 를 복사하여 메모장에 저장합니다.

    ![디렉터리(테넌트 ID)를 복사하여 앱 코드에 저장](media/civic-platform-tutorial/directory.png)

1. **애플리케이션 ID** 를 복사하여 메모장에 저장합니다.

   ![애플리케이션(클라이언트) ID 복사](media/civic-platform-tutorial/application.png)

1. Azure AD에서 **Azure Active Directory** > **앱 등록** 으로 이동하고, 애플리케이션을 선택합니다. **인증서 및 비밀** 을 선택합니다.

1. **클라이언트 비밀 -> 새 클라이언트 비밀** 을 차례로 선택합니다.

1. 비밀에 대한 설명과 기간을 제공합니다. 완료되면 **추가** 를 선택합니다.

   > [!NOTE]
   > 클라이언트 비밀이 저장되면 클라이언트 비밀의 값이 표시됩니다. 나중에 키를 검색할 수 없으므로 이 값을 복사해둡니다.

   ![나중에 이 키를 검색할 수 없으므로 비밀 값 복사](media/civic-platform-tutorial/secret-key.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Civic Platform에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Civic Platform** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-civic-platform-sso"></a>Civic Platform SSO 구성

1. 새 웹 브라우저 창을 열고, Atlassian Cloud 회사 사이트에 관리자 권한으로 로그인합니다.

1. **Standard Choices**(표준 선택 항목)를 클릭합니다.

    ![스크린샷은 관리자 도구에서 호출된 표준 선택 항목이 있는 Atlassian 클라우드 사이트를 보여줍니다.](media/civic-platform-tutorial/standard-choices.png)

1. **ssoconfig** 표준 선택 항목을 만듭니다.

1. **ssoconfig** 를 검색하고 제출합니다.

    ![스크린샷은 SSO 구성 이름이 입력된 표준 선택 항목 검색을 보여줍니다.](media/civic-platform-tutorial/item.png)

1. 빨간색 점을 클릭하여 SSOCONFIG를 확장합니다.

    ![스크린샷은 SSO CONFIG를 사용할 수 있는 표준 항목 선택 브라우저를 보여줍니다.](media/civic-platform-tutorial/details.png)

1. 다음 단계에서 SSO 관련 구성 정보를 제공합니다.

    ![스크린샷은 SSO CONFIG에 대한 표준 선택 항목 편집을 보여줍니다.](media/civic-platform-tutorial/values.png)

    1. **applicationid** 필드에서 Azure Portal에서 복사한 **애플리케이션 ID** 값을 입력합니다.

    1. **clientSecret** 필드에서 Azure Portal에서 복사한 **비밀** 값을 입력합니다.

    1. **directoryId** 필드에서 Azure Portal에서 복사한 **디렉터리(테넌트) ID** 값을 입력합니다.

    1. idpName을 입력합니다. 예를 들어 `Azure`입니다.

### <a name="create-civic-platform-test-user"></a>Civic Platform 테스트 사용자 만들기

이 섹션에서는 Civic Platform에서 B.Simon이라는 사용자를 만듭니다. Civic Platform 지원 팀과 협력하여 사용자를 [Civic Platform Client 지원 팀](mailto:skale@accela.com)에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Civic Platform 로그온 URL로 리디렉션됩니다. 

* Civic Platform 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Civic Platform 타일을 클릭하면 Civic Platform 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Civic Platform이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).