---
title: '자습서: Hub Planner와 Azure AD SSO 통합'
description: Azure Active Directory와 Hub Planner 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/27/2021
ms.author: jeedes
ms.openlocfilehash: b08bec1f035a9b4d0c7bc6f1b7f8ac7361ba6ce4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285316"
---
# <a name="tutorial-azure-ad-sso-integration-with-hub-planner"></a>자습서: Hub Planner와 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Hub Planner를 연결하는 방법에 대해 알아봅니다. Azure AD와 Hub Planner를 연결하면 다음을 수행할 수 있습니다.

* Hub Planner에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Hub Planner에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Hub Planner SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Hub Planner에서 **SP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-hub-planner-from-the-gallery"></a>갤러리에서 Hub Planner 추가

Hub Planner의 Azure AD 통합을 구성하려면 갤러리의 Hub Planner를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Hub Planner** 를 입력합니다.
1. 결과 패널에서 **Hub Planner** 를 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-hub-planner"></a>Hub Planner에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Hub Planner에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Hub Planner의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Hub Planner에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Hub Planner SSO 구성](#configure-hub-planner-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Hub Planner 테스트 사용자 만들기](#create-hub-planner-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Hub Planner에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Hub Planner** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에서 `https://app.hubplanner.com/sso/metadata` 패턴을 사용하는 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://app.hubplanner.com/sso/callback` 패턴을 사용하여 URL을 입력합니다.

    다. **로그인 URL** 텍스트 상자에서 `https://<SUBDOMAIN>.hubplanner.com` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값을 사용하게 됩니다. 변경해야 하는 유일한 내용은 **로그인 URL** 의 \<SUBDOMAIN\>을(를) Hub Planner에 등록할 때 받은 하위 도메인으로 바꾸는 것입니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Hub Planner 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Hub Planner에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Hub Planner** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-hub-planner-sso"></a>Hub Planner SSO 구성

**Hub Planner** 쪽에서 Single Sign-On을 구성하려면 허브 플래너 계정에 로그인하고 다음 작업을 완료해야 합니다. 

### <a name="install-the-extension-in-hub-planner"></a>Hub Planner에 확장 설치

SSO 기능을 사용하도록 설정하려면 먼저 확장을 사용하도록 설정해야 합니다. 계정 소유자 또는 동등한 권한으로 다음 단계를 완료합니다.

1. **설정** 으로 이동합니다.
1. 측면 메뉴에서 **확장 관리** > **확장 추가/제거** 를 선택합니다.
1. Single Sign-on에 대한 확장을 찾고 무료로 추가하거나 사용해 보세요.
1. 메시지가 표시되면 사용 약관에 동의한 다음, **지금 추가** 를 선택합니다.

### <a name="enable-sso"></a>SSO 사용

확장을 사용하도록 설정한 후 계정에 SSO를 사용하도록 설정해야 합니다. 

1. **설정** 으로 이동합니다.
1. 측면 메뉴에서 **인증** 을 선택합니다.
1. **SSO(Single Sign-on)** 를 선택합니다.
1. 다음 이미지와 같이 추가 인증 정보를 입력한 다음, **저장** 을 선택합니다.

![SSO 설정의 스크린샷](media/hub-planner-tutorial/sso-settings.png)

### <a name="create-hub-planner-test-user"></a>Hub Planner 테스트 사용자 만들기

다른 사용자를 추가하려는 경우 **설정** > **리소스 관리** 로 이동하여 여기에서 사용자를 추가합니다. 이메일 주소를 추가하고 초대해야 합니다. 초대되면 이메일을 받고 SSO를 통해 입력할 수 있습니다. 

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Hub Planner 로그온 URL로 리디렉션됩니다. 

* Hub Planner 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Hub Planner 타일을 클릭하면 Hub Planner 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Hub Planner를 구성하면 세션 제어를 적용하여 조직의 중요한 데이터 반출과 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
