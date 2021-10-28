---
title: Azure Active Directory 앱 갤러리에 Azure Active Directory B2C 앱 게시
description: Azure Active Directory 앱 갤러리에 Single Sign-On을 지원하는 Azure AD B2C 앱을 나열하는 방법을 알아봅니다.
titleSuffix: Azure AD B2C
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/15/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 7ac57eb6e52acee3154a1970947e17e1f5947283
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130035673"
---
# <a name="publish-your-azure-ad-b2c-app-to-the-azure-ad-app-gallery"></a>Azure AD 앱 갤러리에 Azure AD B2C 앱 게시

Azure AD(Azure Active Directory) 앱 갤러리는 수천 개 앱의 카탈로그입니다. 앱 갤러리를 사용하면 SSO(Single Sign-On)를 쉽게 배포 및 구성하고 사용자 설정을 자동화할 수 있습니다. 갤러리에서 Workday, ServiceNow, Zoom과 같은 인기 있는 클라우드 앱을 찾을 수 있습니다.

이 문서에서는 Azure AD 앱 갤러리에 Azure Active Directory B2C(Azure AD B2C) 앱을 게시하는 방법을 설명합니다. 앱이 게시되면 고객이 Azure AD 테넌트에 앱을 추가할 때 선택할 수 있는 옵션에 나열됩니다.

Azure AD B2C 앱을 앱 갤러리에 추가하면 다음과 같은 이점이 있습니다.  

- 사용자의 앱은 Microsoft와의 검증된 통합입니다.
- 사용자 앱과 Azure AD 앱 간에 SSO 액세스가 사용하도록 설정됩니다.
- 고객은 빠른 검색을 통해 갤러리에서 앱을 찾을 수 있습니다.
- 앱 구성이 간단하고 최소화됩니다.
- 고객을 위한 단계별 구성 자습서가 있습니다.
- 고객은 조직 내의 다양한 사용자 및 그룹에 앱을 할당할 수 있습니다.
- 테넌트 관리자는 테넌트 전체 앱 사용 권한 관리자 동의를 부여할 수 있습니다.

## <a name="sign-in-flow-overview"></a>로그인 흐름 개요

로그인 흐름에 포함되는 단계는 다음과 같습니다.

1. 사용자가 [내 앱 포털](https://myapps.microsoft.com/)로 이동하여 앱을 선택하면 앱 로그인 URL이 열립니다.
1. 앱 로그인 URL은 권한 부여 요청을 시작하고 사용자를 Azure AD B2C 권한 부여 엔드포인트로 리디렉션합니다.
1. 사용자는 Azure AD "회사" 계정으로 로그인하도록 선택합니다. Azure AD B2C가 Azure AD 권한 부여 엔드포인트로 이동하면 사용자는 회사 계정으로 로그인합니다.
1. Azure AD SSO 세션이 활성화된 경우 Azure AD에서 사용자에게 다시 로그인하라는 메시지를 표시하지 않고 액세스 토큰을 발급합니다. Azure AD 세션이 만료되거나 무효화되면 사용자에게 다시 로그인하라는 메시지가 표시됩니다.

![로그인 OpenID 연결 흐름의 다이어그램입니다.](./media/publish-app-to-azure-ad-app-gallery/app-gallery-sign-in-flow.png)

사용자의 SSO 세션 및 Azure AD ID 설정에 따라 다음을 수행하라는 메시지가 표시될 수 있습니다.

- 이메일 주소나 전화번호를 제공하세요.
- 암호를 입력하거나 [Microsoft Authenticator 앱](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)으로 로그인하세요.
- 다단계 인증을 완료하세요.
- 동의 페이지에 동의하세요. 고객의 테넌트 관리자는 [테넌트 전체 앱 사용 권한 관리자 동의를 부여](../active-directory/manage-apps/grant-admin-consent.md)할 수 있습니다. 동의가 부여되면 사용자에게 동의 페이지가 표시되지 않습니다.

로그인에 성공하면 Azure AD는 Azure AD B2C에 토큰을 반환합니다. Azure AD B2C는 토큰 클레임의 유효성을 확인하고 읽은 다음 애플리케이션에 토큰을 반환합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="step-1-register-your-application-in-azure-ad-b2c"></a>1단계: Azure AD B2C에 애플리케이션 등록

Azure AD B2C를 사용해 앱에 로그인할 수 있도록 하려면 Azure AD B2C 디렉터리에 앱을 등록합니다. 앱을 등록하면 앱과 Azure AD B2C 간에 트러스트 관계가 설정됩니다. 

아직 수행하지 않은 경우 [웹 애플리케이션을 등록](tutorial-register-applications.md)하고 [ID 토큰 암시적 부여를 사용하도록 설정](tutorial-register-applications.md#enable-id-token-implicit-grant)합니다. 나중에 Azure 앱 갤러리에 이 앱을 등록합니다.

## <a name="step-2-set-up-sign-in-for-multitenant-azure-ad"></a>2단계: 다중 테넌트 Azure AD에 대한 로그인 설정

모든 Azure AD 테넌트의 직원 및 소비자가 Azure AD B2C를 사용하여 로그인할 수 있도록 하려면 [다중 테넌트 Azure AD에 대한 로그인 설정](identity-provider-azure-ad-multi-tenant.md?pivots=b2c-custom-policy) 지침을 따르세요.

## <a name="step-3-prepare-your-app"></a>3단계: 앱 준비

앱에서 로그인 엔드포인트의 URL을 복사합니다. [웹 애플리케이션 샘플](configure-authentication-sample-web-app.md)을 사용하는 경우 로그인 URL은 `https://localhost:5001/MicrosoftIdentity/Account/SignIn?`입니다. 이 URL은 Azure AD 앱 갤러리에서 사용자가 앱에 로그인하도록 안내하는 위치입니다.

프로덕션 환경에서 앱 등록 리디렉션 URI는 일반적으로 앱이 실행되고 있는 공개적으로 액세스 가능한 엔드포인트입니다(예: `https://woodgrovedemo.com/Account/SignIn`). 회신 URL은 `https`로 시작해야 합니다.

## <a name="step-4-publish-your-azure-ad-b2c-app"></a>4단계: Azure AD B2C 앱 게시

마지막으로 Azure AD 앱 갤러리에 다중 테넌트 앱을 추가합니다. [Azure AD 앱 갤러리에 앱 게시](../active-directory/develop/v2-howto-app-gallery-listing.md)의 지침을 따릅니다. 앱 갤러리에 앱을 추가하려면 다음을 수행합니다.

1. [설명서를 만들고 게시](../active-directory/develop/v2-howto-app-gallery-listing.md#step-5---create-and-publish-documentation)합니다.
1. 다음 정보를 사용하여 [앱을 제출](../active-directory/develop/v2-howto-app-gallery-listing.md#step-6---submit-your-app)합니다.

    |질문  |제공해야 하는 답변  |
    |---------|---------|
    |어떤 유형의 요청을 제출하고 싶으세요?| **갤러리에 내 애플리케이션 나열** 을 선택합니다.|
    |갤러리에 애플리케이션을 나열할 때 어떤 기능을 사용하도록 설정하실건가요? | **페더레이션 SSO(SAML, WS-Fed 및 OpenID Connect)** 를 선택합니다. | 
    | 애플리케이션 페더레이션 프로토콜 선택| **OpenID Connect 및 OAuth 2.0** 을 선택합니다. |
    | 애플리케이션(클라이언트) ID | [Azure AD B2C 애플리케이션](#step-1-register-your-application-in-azure-ad-b2c)의 ID를 제공합니다. |
    | 애플리케이션 로그인 URL|[3단계. 앱 준비](#step-3-prepare-your-app)에서 구성된 대로 앱 로그인 URL을 제공합니다.|
    | 다중 테넌트| **예** 를 선택합니다. |
    | | |

## <a name="next-steps"></a>다음 단계

- [Azure AD 앱 갤러리에 앱을 게시](../active-directory/develop/v2-howto-app-gallery-listing.md)하는 방법을 알아봅니다.
