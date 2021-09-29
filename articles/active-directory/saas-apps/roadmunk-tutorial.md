---
title: '자습서: Roadmunk와 Azure Active Directory SSO(Single Sign-On) 통합'
description: Azure Active Directory와 Roadmunk 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: f0c139ac775cf0761e1866853c959d7e98b20cf3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124821754"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>자습서: Roadmunk와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Roadmunk를 통합하는 방법에 대해 알아봅니다. Roadmunk를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Roadmunk에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정을 사용하여 Roadmunk에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SSO(Single Sign-On)를 사용하도록 설정된 Roadmunk 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

Roadmunk는 SP(서비스 공급자) 및 IDP(ID 공급자)에서 시작한 SSO를 지원합니다. 

## <a name="add-roadmunk-from-the-gallery"></a>갤러리에서 Roadmunk 추가

Roadmunk를 Azure AD에 통합하려면 갤러리에서 관리형 SaaS 앱 목록에 Roadmunk를 추가합니다.

1. 회사 계정, 학교 계정 또는 개인 Microsoft 계정을 사용하여 Azure Portal에 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Roadmunk** 를 입력합니다.
1. 결과에서 **Roadmunk** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>Roadmunk에 대한 Azure AD SSO 구성 및 테스트

*B.Simon* 이라는 테스트 사용자를 사용하여 Roadmunk에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하도록 하려면 Azure AD 사용자와 Roadmunk의 관련 사용자 간에 링크 관계를 설정해야 합니다.

Roadmunk에서 Azure AD SSO를 구성하고 테스트하는 방법에 대한 개요는 다음과 같습니다.

1. [Azure AD SSO 구성](#configure-azure-ad-sso) - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - B.Simon을 사용하여 Azure AD SSO를 테스트합니다.
    1. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - B.Simon이 Azure AD SSO를 사용할 수 있도록 할당합니다.
1. [Roadmunk SSO 구성](#configure-roadmunk-sso) - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
    1. [Roadmunk 테스트 사용자 만들기](#create-roadmunk-test-user) - 그러면 Roadmunk의 B.Simon에 해당하는 사용자를 이 사용자의 Azure AD 표현에 연결할 수 있습니다.
1. [SSO 테스트](#test-sso) - 구성이 작동하는지 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Roadmunk** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 펜 아이콘을 선택하여 설정을 편집합니다.

   ![기본 SAML 구성의 편집 아이콘을 보여주는 스크린샷](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서, SP 메타데이터 파일이 있고 IDP 시작 모드에서 구성하려면 다음 단계를 수행합니다.

    a. **메타데이터 파일 업로드** 를 선택합니다.

    ![메타데이터 파일 업로드를 위한 링크를 보여주는 스크린샷](common/upload-metadata.png)

    b. 폴더 아이콘을 선택하여 "Roadmunk SSO 구성" 절차의 4단계에서 다운로드한 메타데이터 파일을 선택합니다. 그런 다음, **업로드** 를 선택합니다.

    ![메타데이터 파일을 선택하는 방법을 보여주는 스크린샷](common/browse-upload-metadata.png)

    메타데이터 파일이 업로드되면 **기본 SAML 구성** 섹션의 **식별자** 및 **회신 URL** 값이 자동으로 채워집니다.

    ![기본 SAML 구성 섹션을 보여주는 스크린샷 식별자 필드 및 회신 URL 필드가 강조 표시됩니다.](common/idp-intiated.png)

    > [!Note]
    > **식별자** 및 **회신 URL** 값이 자동으로 채워지지 않으면 값을 수동으로 입력합니다.

1. SP 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 을 선택합니다. **로그온 URL** 필드에 `https://login.roadmunk.com`을 입력합니다.

    ![SP 시작 모드에 대한 로그온 URL을 설정하는 위치를 보여주는 스크린샷](common/metadata-upload-additional-signon.png)

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾습니다. 그런 다음, **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![SAML 서명 인증서에 대한 다운로드 링크를 보여주는 스크린샷](common/metadataxml.png)

1. **Roadmunk 설정** 창에서 필요한 URL를 복사합니다.

    ![구성 URL을 복사할 위치를 보여주는 스크린샷](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 테스트 사용자를 만듭니다. 사용자의 이름은 *B.Simon* 이라고 지정합니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자** 를 차례로 선택합니다.
1. 창 맨 위에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예를 들어 다음과 같이 입력합니다. `B.Simon@contoso.com`
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure SSO를 사용할 수 있도록 B.Simon에게 Roadmunk에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 차례로 선택합니다.
1. 애플리케이션 목록에서 **Roadmunk** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾은 다음, **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **B.Simon** 을 선택합니다. 그런 다음, 대화 상자 맨 아래에서 **선택** 을 선택합니다.
1. 사용자에게 역할이 할당될 것으로 예상하는 경우 **역할 선택** 드롭다운 메뉴에서 역할을 선택합니다. 이 앱에 대해 설정된 역할이 없으면 **기본 액세스** 역할이 선택됩니다.
1. **할당 추가** 대화 상자에서 **할당** 을 선택합니다.

## <a name="configure-roadmunk-sso"></a>Roadmunk SSO 구성

1. Roadmunk 웹 사이트에 관리자 권한으로 로그인합니다.

1. 페이지 맨 아래에서 사용자 아이콘을 선택하고 **계정 설정** 을 선택합니다.

    ![사용자 계정 설정을 선택하는 위치를 보여주는 스크린샷](./media/roadmunk-tutorial/account.png)

1. **회사** > **인증 설정** 으로 이동합니다.

1. **인증 설정** 페이지에서 다음 단계를 수행합니다.

    ![인증 설정 페이지를 보여주는 스크린샷](./media/roadmunk-tutorial/saml-sso.png)

    a. **SAML SSO(Single Sign-On)** 를 켭니다.

    b. **1단계** 섹션에서 메타데이터 XML 파일을 업로드하거나 메타데이터의 URL을 제공합니다.

    다. **2단계** 섹션에서 Roadmunk 메타데이터 파일을 다운로드한 다음, 컴퓨터에 저장합니다.

    d. SSO를 사용하여 로그인하려면 **3단계** 섹션에서 **Enforce SAML Sign-In Only**(SAML 로그인만 적용)을 선택합니다.

    e. **저장** 을 선택합니다.


### <a name="create-roadmunk-test-user"></a>Roadmunk 테스트 사용자 만들기

1. Roadmunk 웹 사이트에 관리자 권한으로 로그인합니다.

1. 페이지 맨 아래에서 사용자 아이콘을 선택한 다음, **계정 설정** 을 선택합니다.

    ![테스트 사용자에 대한 계정 설정을 여는 방법을 보여주는 스크린샷](./media/roadmunk-tutorial/account.png)

1. **사용자** 탭을 열고 **사용자 초대** 를 선택합니다.

    ![사용자 탭을 보여주는 스크린샷 사용자 초대 단추가 강조 표시됩니다. 열린 창에서 이메일 및 역할 필드가 강조 표시됩니다.](./media/roadmunk-tutorial/create-user.png)

1. 양식이 나타나면 필요한 정보를 채운 다음, **초대** 를 선택합니다.


## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트합니다.

내 앱 포털에서 **Roadmunk** 타일을 선택하면 SSO를 설정한 Roadmunk 계정에 자동으로 로그인됩니다. 자세한 내용은 [내 앱 포털에서 앱에 로그인하여 시작](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Roadmunk가 구성되면 세션 제어를 적용할 수 있습니다. 세션 제어는 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호합니다. 세션 제어는 조건부 액세스에서 확장됩니다. 

[Microsoft Cloud App Security를 사용하여 세션 제어를 적용](/cloud-app-security/proxy-deployment-any-app)하는 방법을 알아봅니다.