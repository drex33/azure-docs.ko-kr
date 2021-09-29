---
title: '자습서: 애플리케이션 등록'
titleSuffix: Azure AD B2C
description: 이 자습서에 따라 Azure Portal을 사용하여 Azure Active Directory B2C에서 웹 애플리케이션을 등록하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 913393b36ec91b1db576a39711deeddca0f98258
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128581624"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C에서 웹 애플리케이션 등록

[애플리케이션](application-types.md)이 Azure AD B2C(Azure Active Directory B2C)와 상호 작용하려면 먼저 사용자가 관리하는 테넌트에 등록되어야 합니다. 이 자습서에서는 Azure Portal을 사용하여 웹 애플리케이션을 등록하는 방법을 보여줍니다. 

"웹 애플리케이션"은 서버에서 대부분의 애플리케이션 로직을 수행하는 기존의 웹 애플리케이션을 말합니다. ASP.NET Core, Maven(Java), Flask(Python), Express(Node.js)와 같은 프레임워크를 사용하여 빌드할 수 있습니다.

> [!IMPORTANT]
> "SPA"(단일 페이지 애플리케이션)를 대신 사용하는 경우(예:Angular, Vue 또는 React 사용) [단일 페이지 애플리케이션을 등록하는 방법](tutorial-register-spa.md)을 알아보세요.
> 
> 네이티브 앱을 대신 사용하는 경우(예: iOS, Android, 모바일 및 데스크톱) [네이티브 클라이언트 애플리케이션을 등록하는 방법](add-native-application.md)을 알아보세요.

## <a name="prerequisites"></a>사전 요구 사항
Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

아직 고유한 [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 만들지 않았다면 지금 만듭니다. 기존 Azure AD B2C 테넌트를 사용해도 됩니다.

## <a name="register-a-web-application"></a>웹 애플리케이션 등록

웹 애플리케이션을 Azure AD B2C 테넌트에 등록하려면 새로운 통합 **앱 등록** 환경 또는 레거시 **애플리케이션(레거시)** 환경을 사용할 수 있습니다. [새 환경에 대해 자세히 알아보세요](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[앱 등록](#tab/app-reg-ga/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예를 들어 *webapp1* 과 같습니다.
1. **지원되는 계정 유형** 아래에서 **모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도)** 을 선택합니다.
1. **리디렉션 URI** 에서 **웹** 을 선택한 다음 URL 텍스트 상자에 `https://jwt.ms`를 입력합니다.

    리디렉션 URI는 사용자와의 상호 작용을 완료한 후 권한 부여 서버(이 경우에는 Azure AD B2C)에서 사용자를 보내고, 성공적인 권한 부여 시 액세스 토큰 또는 권한 부여 코드를 전송하는 엔드포인트입니다. 프로덕션 애플리케이션에서는 일반적으로 `https://contoso.com/auth-response`와 같이 앱이 실행되는 공개적으로 액세스할 수 있는 엔드포인트입니다. 이 자습서와 같은 테스트 목적으로 토큰을 디코딩된 토큰 콘텐츠를 표시하는 Microsoft 소유의 웹 애플리케이션 `https://jwt.ms`로 설정할 수 있습니다(토큰의 콘텐츠가 브라우저에서 벗어나면 안 됨). 앱을 개발하는 동안 애플리케이션이 로컬에서 수신 대기하는 엔드포인트(예: `https://localhost:5000`)를 추가할 수 있습니다. 언제든지 등록된 애플리케이션에서 리디렉션 URI를 추가하고 수정할 수 있습니다.

    리디렉션 URI에는 다음 제한 사항이 적용됩니다.

    * 회신 URL은 스키마 `https`로 시작해야 합니다.
    * 회신 URL은 대/소문자를 구분합니다. 해당 사례는 실행 중인 애플리케이션의 URL 경로에 대한 대/소문자와 일치해야 합니다. 예를 들어 애플리케이션의 경로 `.../abc/response-oidc`의 일부로 포함하는 경우 회신 URL에 `.../ABC/response-oidc`를 지정하지 마세요. 웹 브라우저는 경로를 대/소문자 구분하여 처리하므로 `.../abc/response-oidc`와 연결된 쿠키는 대/소문자가 일치하지 않는 `.../ABC/response-oidc` URL로 리디렉션되는 경우 제외될 수 있습니다.

1. **사용 권한** 아래에서 *openid 및 offline_access 권한에 대한 관리자 동의 허용* 확인란을 선택합니다.
1. **등록** 을 선택합니다.

#### <a name="applications-legacy"></a>[애플리케이션(레거시)](#tab/applications-legacy/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **애플리케이션(레거시)** 을 선택한 다음, **추가** 를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapp1* 과 같습니다.
1. **웹앱/웹 API 포함** 에 대해 **예** 를 선택합니다.
1. **회신 URL** 에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 예를 들어, `http://localhost:5000`에서 로컬로 수신 대기하도록 설정할 수 있습니다. 언제든지 등록된 애플리케이션에서 리디렉션 URI를 추가하고 수정할 수 있습니다.

    리디렉션 URI에는 다음 제한 사항이 적용됩니다.

    * `localhost`를 사용하지 않는 이상, 회신 URL은 `https` 스키마로 시작해야 합니다.
    * 회신 URL은 대/소문자를 구분합니다. 해당 사례는 실행 중인 애플리케이션의 URL 경로에 대한 대/소문자와 일치해야 합니다. 예를 들어 애플리케이션의 경로 `.../abc/response-oidc`의 일부로 포함하는 경우 회신 URL에 `.../ABC/response-oidc`를 지정하지 마세요. 웹 브라우저는 경로를 대/소문자 구분하여 처리하므로 `.../abc/response-oidc`와 연결된 쿠키는 대/소문자가 일치하지 않는 `.../ABC/response-oidc` URL로 리디렉션되는 경우 제외될 수 있습니다.

1. **만들기** 를 선택하여 애플리케이션 등록을 완료합니다.

* * *

## <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

웹 애플리케이션의 경우 애플리케이션 비밀을 만들어야 합니다. 클라이언트 암호는 *애플리케이션 암호* 라고도 합니다. 이 비밀은 애플리케이션에서 액세스 토큰의 권한 부여 코드를 교환하는 데 사용됩니다.

#### <a name="app-registrations"></a>[앱 등록](#tab/app-reg-ga/)

1. **Azure AD B2C - 앱 등록** 페이지에서 만든 애플리케이션을 선택합니다(예: *webapp1*).
1. 왼쪽 메뉴의 **관리** 에서 **인증서 및 비밀** 을 선택합니다.
1. **새 클라이언트 비밀** 을 선택합니다.
1. **설명** 상자에 클라이언트 암호에 대한 설명을 입력합니다. 예: *clientsecret1*.
1. **만료** 에서 암호가 유효한 기간을 선택한 다음 **추가** 를 선택합니다.
1. 클라이언트 애플리케이션 코드에서 사용할 비밀 **값** 을 기록합니다. 이 비밀 값은 이 페이지에서 나가면 다시 표시되지 않습니다. 이 값을 애플리케이션의 코드에서 애플리케이션 비밀로 사용합니다.

#### <a name="applications-legacy"></a>[애플리케이션(레거시)](#tab/applications-legacy/)

1. **Azure AD B2C - 애플리케이션** 페이지에서 만든 애플리케이션을 선택합니다(예: *webapp1*).
1. **키** 를 선택한 다음 **키 생성** 을 선택합니다.
1. **저장** 을 선택하여 키를 확인합니다. **앱 키** 값을 기록해 둡니다. 이 값을 애플리케이션의 코드에서 애플리케이션 비밀로 사용합니다.

* * *

> [!NOTE]
> 보안을 위해 주기적으로 또는 응급 상황이 발생할 경우 즉시 애플리케이션 비밀을 롤오버할 수 있습니다. Azure AD B2C와 통합되는 모든 애플리케이션은 발생 빈도에 관계없이 비밀 롤오버 이벤트를 처리할 준비가 되어 있어야 합니다. 두 개의 애플리케이션 비밀을 설정하여 애플리케이션이 애플리케이션 비밀 회전 이벤트 동안 이전 비밀을 계속 사용할 있습니다. 다른 클라이언트 암호를 추가하려면 이 섹션의 단계를 반복합니다. 

## <a name="enable-id-token-implicit-grant"></a>ID 토큰 암시적 허용 사용

암시적 허용의 정의 특성은 ID 및 액세스 토큰과 같은 토큰이 Azure AD B2C에서 애플리케이션으로 직접 반환된다는 것입니다. 권한 부여 엔드포인트에서 직접 ID 토큰을 요청하는 ASP.NET Core 웹앱 및 [https://jwt.ms](https://jwt.ms)와 같은 웹앱의 경우 앱 등록에서 암시적 허용 흐름을 사용하도록 설정합니다.

1. 왼쪽 메뉴의 **관리** 아래에서 **인증** 을 선택합니다.
1. 암시적 허용에서 **액세스 토큰** 및 **ID 토큰** 확인란을 둘 다 선택합니다.
1. **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 웹 애플리케이션 등록
> * 클라이언트 비밀 만들기

다음으로 사용자가 자신의 프로필을 등록, 로그인 및 관리할 수 있도록 사용자 흐름을 만드는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C >에서 사용자 흐름 만들기](tutorial-create-user-flows.md)
