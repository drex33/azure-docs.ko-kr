---
title: '자습서: TINFOIL SECURITY와 Azure AD SSO 통합'
description: Azure Active Directory와 TINFOIL SECURITY 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.author: jeedes
ms.openlocfilehash: 48d46916143fd219c81354a2d3f62ea260c9a2fb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132303967"
---
# <a name="tutorial-azure-ad-sso-integration-with-tinfoil-security"></a>자습서: TINFOIL SECURITY와 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 TINFOIL SECURITY를 통합하는 방법에 대해 알아봅니다. TINFOIL SECURITY를 Azure AD와 통합하는 경우 다음을 수행할 수 있습니다.

* TINFOIL SECURITY에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 TINFOIL SECURITY에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* TINFOIL SECURITY SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* TINFOIL SECURITY에서 **IDP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-tinfoil-security-from-the-gallery"></a>갤러리에서 TINFOIL SECURITY 추가

TINFOIL SECURITY가 Azure AD로 통합되도록 구성하려면 갤러리에서 TINFOIL SECURITY를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **TINFOIL SECURITY** 를 입력합니다.
1. 결과 패널에서 **TINFOIL SECURITY** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-tinfoil-security"></a>TINFOIL SECURITY에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 TINFOIL SECURITY에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 TINFOIL SECURITY의 관련 사용자 간에 연결 관계를 설정해야 합니다.

TINFOIL SECURITY에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[TINFOIL SECURITY SSO 구성](#configure-tinfoil-security-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[TINFOIL SECURITY 테스트 사용자 만들기](#create-tinfoil-security-test-user)** - B.Simon의 Azure AD 표현과 연결되는 대응 사용자를 TINFOIL SECURITY에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **TINFOIL SECURITY** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 애플리케이션은 미리 구성되어 있으며 필요한 URL은 이미 Azure로 미리 채워져 있습니다. 사용자는 **저장** 단추를 클릭하여 구성을 저장해야 합니다.

1. Visitly 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 Visitly 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | Name | 원본 특성 |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    > [!NOTE]
    > accountid 값은 자습서의 뒷부분에서 설명합니다.

1. **SAML 서명 인증서** 섹션에서 **편집** 단추를 클릭하여 **SAML 서명 인증서** 대화 상자를 엽니다.

    ![SAML 서명 인증서 편집](common/edit-certificate.png)

1. **SAML 서명 인증서** 섹션에서 **지문 값** 을 복사하여 컴퓨터에 저장합니다.

    ![지문 값 복사](common/copy-thumbprint.png)

1. **TINFOIL SECURITY 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 B.Simon이 Azure Single Sign-On을 사용할 수 있도록 TINFOIL SECURITY에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **TINFOIL SECURITY** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-tinfoil-security-sso"></a>TINFOIL SECURITY SSO 구성

1. 다른 웹 브라우저 창에서 TINFOIL SECURITY 회사 사이트에 관리자로 로그인합니다.

1. 위쪽에 도구 모음에서 **내 계정** 을 클릭합니다.

    ![대시보드](./media/tinfoil-security-tutorial/account.png "대시보드")

1. **보안** 을 클릭합니다.

    ![보안](./media/tinfoil-security-tutorial/details.png "보안")

1. **Single Sign-On** 구성 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-On](./media/tinfoil-security-tutorial/certificate.png "Single Sign-On")

    a. **SAML 사용** 을 선택합니다.

    b. **수동 구성** 을 클릭합니다.

    다. **SAML 게시물 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    d. **SAML Certificate Fingerprint**(SAML 인증서 지문) 텍스트 상자에 **SAML 서명 인증서** 섹션에서 복사한 **지문** 값을 붙여넣습니다.
  
    e. **계정 ID** 값을 복사하여 Azure Portal에서 **사용자 특성 및 클레임** 섹션의 **원본 특성** 텍스트 상자에 붙여넣습니다.

    f. **저장** 을 클릭합니다.

### <a name="create-tinfoil-security-test-user"></a>TINFOIL SECURITY 테스트 사용자 만들기

Azure AD 사용자가 TINFOIL SECURITY에 로그인하려면 TINFOIL SECURITY로 프로비저닝되어야 합니다. TINFOIL SECURITY의 경우 프로비전이 수동 작업입니다.

**사용자를 프로비전하려면 다음 단계를 따르십시오.**

1. 사용자가 엔터프라이즈 계정의 일부라면 [TINFOIL SECURITY 지원 팀에 문의](https://www.tinfoilsecurity.com/contact)하여 사용자 계정을 만들어야 합니다.

1. 사용자가 일반적인 TINFOIL SECURITY SaaS 사용자라면 사용자는 원하는 사용자 사이트에 협력자를 추가할 수 있습니다. 그러면 지정된 이메일로 초대장을 보내는 프로세스가 트리거되고 새로운 TINFOIL SECURITY 사용자 계정이 만들어집니다.

> [!NOTE]
> 다른 TINFOIL SECURITY 사용자 계정 생성 도구 또는 TINFOIL SECURITY가 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비전할 수 있습니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭합니다. 그러면 SSO를 설정한 TINFOIL SECURITY에 자동으로 로그인되어야 합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 TINFOIL SECURITY 타일을 클릭합니다. 그러면 SSO를 설정한 TINFOIL SECURITY에 자동으로 로그인되어야 합니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

TINFOIL SECURITY를 구성하면 세션 제어를 적용하여 조직의 중요한 데이터 반출과 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
