---
title: '빠른 시작: 엔터프라이즈 애플리케이션에 Single Sign-On 사용'
titleSuffix: Azure AD
description: Azure Active Directory에서 엔터프라이즈 애플리케이션에 Single Sign-On을 사용하도록 설정합니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/21/2021
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: 337561853bb34c8ecc67483dc8b94448b0b853a1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548523"
---
# <a name="quickstart-enable-single-sign-on-for-an-enterprise-application"></a>빠른 시작: 엔터프라이즈 애플리케이션에 Single Sign-On 사용

이 빠른 시작에서는 Azure Active Directory 관리 센터를 사용하여 이전에 Azure AD(Azure Active Directory) 테넌트에 추가한 엔터프라이즈 애플리케이션에 SSO(Single Sign-On)를 사용하도록 구성합니다. SSO를 구성하면 사용자가 자신의 Azure AD 자격 증명을 사용하여 로그인할 수 있습니다. 

Azure AD의 갤러리에는 SSO를 사용하는 수천 개의 사전 통합 애플리케이션이 있습니다. 이 빠른 시작에서는 **Azure AD SAML Toolkit** 이라는 엔터프라이즈 애플리케이션을 예로 사용하지만, 개념은 [갤러리에 있는 대부분의 미리 구성된 엔터프라이즈 애플리케이션](../saas-apps/tutorial-list.md)에 적용됩니다.

이 빠른 시작의 단계를 테스트하려면 비 프로덕션 환경을 사용하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건

SSO를 구성하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.
- [빠른 시작: 사용자 계정 만들기 및 할당](add-application-portal-assign-users.md) 단계를 완료합니다.

## <a name="enable-single-sign-on"></a>Single Sign-On 사용

애플리케이션에 SSO를 사용하려면 다음을 수행합니다.

1. [Azure Active Directory 관리 센터](https://aad.portal.azure.com)로 이동하고, 필수 구성 요소에 나열된 역할 중 하나를 사용하여 로그인합니다.
1. 왼쪽 메뉴에서 **엔터프라이즈 애플리케이션** 을 선택합니다. **모든 애플리케이션** 창이 열리고, Azure AD 테넌트의 애플리케이션 목록이 표시됩니다. 사용하려는 애플리케이션을 검색하여 선택합니다. 예를 들어 **Azure AD SAML Toolkit 1** 입니다.
1. 왼쪽 메뉴의 **관리** 섹션에서 **Single Sign-On** 을 선택하여 편집할 **Single Sign-On** 창을 엽니다.
1. **SAML** 을 선택하여 SSO 구성 페이지를 엽니다. 애플리케이션이 구성되면 사용자는 Azure AD 테넌트에서 자신의 자격 증명을 사용하여 애플리케이션에 로그인할 수 있습니다.
1. SAML 기반 SSO에 Azure AD를 사용하도록 애플리케이션을 구성하는 프로세스는 애플리케이션에 따라 다릅니다. 갤러리에 있는 엔터프라이즈 애플리케이션의 경우 링크를 사용하여 애플리케이션을 구성하는 데 필요한 단계에 대한 정보를 찾습니다. **Azure AD SAML Toolkit** 에 대한 단계는 이 빠른 시작에 나열되어 있습니다.

    :::image type="content" source="media/add-application-portal-setup-sso/saml-configuration.png" alt-text="엔터프라이즈 애플리케이션에 대한 Single Sign-On 구성":::

1. **Azure AD SAML Toolkit 1 설정** 섹션에서 **로그인 URL**, **Azure AD 식별자** 및 **로그아웃 URL** 속성의 값을 기록해 둡니다. 나중에 필요합니다.

## <a name="configure-single-sign-on-in-the-tenant"></a>테넌트에서 Single Sign-On 구성

로그인 및 회신 URL 값을 추가하고 인증서를 다운로드하여 Azure AD에서 SSO 구성을 시작합니다.

Azure AD에서 SSO를 구성하려면 다음을 수행합니다.

1. Azure Portal의 **Single Sign-On 설정** 창에 있는 **기본 SAML 구성** 섹션에서 **편집** 을 선택합니다. 
1. **회신 URL(Assertion Consumer Service URL)** 로 `https://samltoolkit.azurewebsites.net/SAML/Consume`을 입력합니다.
1. **로그온 URL** 로 `https://samltoolkit.azurewebsites.net/`을 입력합니다.
1. **저장** 을 선택합니다.
1. **SAML 서명 인증서** 섹션에서 **인증서(원시)** 에 대해 **다운로드** 를 선택하여 SAML 서명 인증서를 다운로드하여 저장합니다. 나중에 필요합니다.

## <a name="configure-single-sign-on-in-the-application"></a>애플리케이션에서 Single Sign-On 구성

애플리케이션에서 Single Sign-On을 사용하려면 사용자 계정을 애플리케이션에 등록하고 앞에서 기록해 둔 SAML 구성 값을 추가해야 합니다.

### <a name="register-the-user-account"></a>사용자 계정 등록

애플리케이션에 사용자 계정을 등록하려면 다음을 수행합니다.

1. 새 브라우저 창을 열고 애플리케이션의 로그인 URL로 이동합니다. **Azure AD SAML Toolkit** 애플리케이션의 주소는 `https://samltoolkit.azurewebsites.net`입니다.
1. 페이지의 오른쪽 위에서 **등록** 을 선택합니다.

    :::image type="content" source="media/add-application-portal-setup-sso/toolkit-register.png" alt-text="Azure AD SAML Toolkit 애플리케이션에서 사용자 계정을 등록합니다.":::

1. **이메일** 은 애플리케이션에 액세스할 사용자의 이메일 주소를 입력합니다. 예를 들어 이전 빠른 시작에서는 `contosouser1@contoso.com`을 주소로 사용하는 사용자 계정을 만들었습니다. `contoso.com`을 해당하는 테넌트 도메인으로 변경해야 합니다.
1. **암호** 를 입력하고 확인합니다.
1. **등록** 을 선택합니다.

### <a name="configure-saml-settings"></a>SAML 설정 구성

애플리케이션의 SAML 설정을 구성하려면 다음을 수행합니다.

1. 앞에서 만든 사용자 계정의 자격 증명으로 로그인하고, 페이지의 왼쪽 위 모서리에서 **SAML 구성** 을 선택합니다.
1. 페이지 가운데에서 **만들기** 를 선택합니다.
1. **로그인 URL**, **Azure AD 식별자** 및 **로그아웃 URL** 에는 앞에서 기록해 둔 값을 입력합니다.
1. **파일 선택** 을 선택하여 앞에서 다운로드한 인증서를 업로드합니다.
1. **만들기** 를 선택합니다.
1. **SP 시작 로그인 URL** 및 **ACS(Assertion Consumer Service) URL** 을 복사합니다. 나중에 필요합니다.

## <a name="update-single-sign-on-values"></a>Single Sign-On 값 업데이트

기록해 둔 값을 **SP 시작 로그인 URL** 및 **ACS(Assertion Consumer Service) URL** 에 사용하여 테넌트의 Single Sign-On 값을 업데이트합니다.

Single Sign-On 값을 업데이트하려면 다음을 수행합니다.

1. Azure Portal의 **Single Sign-On 설정** 창에 있는 **기본 SAML 구성** 섹션에서 **편집** 을 선택합니다. 
1. 앞에서 기록해 둔 **ACS(Assertion Consumer Service) URL** 값을 **회신 URL(Assertion Consumer Service URL)** 에 입력합니다.
1. 앞에서 기록해 둔 **SP 시작 로그온 URL** 값을 **로그온 URL** 에 입력합니다.
1. **저장** 을 선택합니다.

## <a name="test-single-sign-on"></a>Single Sign-On 테스트

**Single Sign-On 설정** 창에서 Single Sign-On 구성을 테스트할 수 있습니다.

SSO를 테스트하려면 다음을 수행합니다.

1. **Azure AD SAML Toolkit 1으로 Single Sign-On 테스트** 섹션의 **Single Sign-On 설정** 창에서 **테스트** 를 선택합니다.
1. 애플리케이션에 할당한 사용자 계정의 Azure AD 자격 증명을 사용하여 애플리케이션에 로그인합니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 빠른 시작을 완료하려는 경우 여기서 만든 엔터프라이즈 애플리케이션을 유지합니다. 그렇지 않으면 해당 애플리케이션을 삭제하여 테넌트를 정리하는 것이 좋습니다. 자세한 내용은 [애플리케이션 삭제](delete-application-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

엔터프라이즈 애플리케이션의 속성을 구성하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [애플리케이션 구성](add-application-portal-configure.md)
