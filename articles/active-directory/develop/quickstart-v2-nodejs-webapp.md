---
title: '빠른 시작: Node.js 웹앱에 사용자 로그인 추가 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 OpenID Connect를 사용하여 Node.js 웹 애플리케이션에서 인증을 구현하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ROBOTS: NOINDEX
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, "scenarios:getting-started", "languages:ASP.NET", devx-track-js, mode-api
ms.openlocfilehash: bde3b8b7aa581c8111c78ea00585caee2f269286
ms.sourcegitcommit: 8d56da997b0b3ab07f91f6bef0c5661847758c4a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2022
ms.locfileid: "138037330"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>빠른 시작: OpenID Connect를 사용하여 Node.js 웹앱에 로그인 추가

> [!div renderon="docs"]
> 환영합니다! 아마도 기대했던 페이지는 아닐 것입니다. 수정 작업을 진행하는 동안 이 링크를 통해 올바른 문서로 이동해야 합니다.
>
> > [빠른 시작: Express 프레임워크를 사용하여 빌드된 Node.js 웹앱에 사용자 로그인 추가 ](web-app-quickstart.md?pivots=devlang-nodejs-passport)
> 
> 이 문제를 해결하는 동안 불편을 끼쳐 드려 죄송하며 양해해 주셔서 감사합니다.

> [!div renderon="portal" class="sxs-lookup"]
> 이 빠른 시작에서는 Express와 함께 Node.js를 사용하여 빌드된 웹 애플리케이션에서 OpenID Connect 인증을 설정하는 방법을 보여주는 코드 샘플을 다운로드하고 실행합니다. 샘플은 모든 플랫폼에서 실행되도록 설계되었습니다.
> 
> ## <a name="prerequisites"></a>필수 구성 요소
> 
> - 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> - [Node.js](https://nodejs.org/en/download/).
> 
> ## <a name="register-your-application"></a>애플리케이션 등록
> 
> 1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
> 1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트로 전환합니다.
> 1. **Azure Active Directory** 를 검색하고 선택합니다.
> 1. **관리** 아래에서 **앱 등록** > **새 등록** 을 선택합니다.
> 1. 애플리케이션에 대한 **이름** 을 입력합니다(예: `MyWebApp`). 이 이름은 앱의 사용자에게 표시될 수 있으며 나중에 변경할 수 있습니다.
> 1. **지원되는 계정 유형** 섹션에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정(얘: Skype, Xbox, Outlook.com)** 을 선택합니다.
> 
>     리디렉션 URI가 두 개 이상 있는 경우 앱이 성공적으로 만들어진 후 나중에 **인증** 탭에서 URI를 추가합니다.
> 
> 1. **등록** 을 선택하여 앱을 만듭니다.
> 1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾아서 기록해 둡니다. 이 프로젝트의 뒷부분에서 애플리케이션을 구성할 때 이 > 값이 필요합니다.
> 1. **관리** 에서 **인증** 을 선택합니다.
> 1. **플랫폼 추가** > **웹** 을 선택합니다. 
> 1. **리디렉션 URI** 섹션에서 `http://localhost:3000/auth/openid/return`을 입력합니다.
> 1. **프런트 채널 로그 아웃 URL**`https://localhost:3000`을 입력합니다.
> 1. 이 샘플에서는 사용자를 로그인할 수 있도록 [암시적 권한 부여 흐름](./v2-oauth2-implicit-grant-flow.md)을 사용하도록 설정해야 하므로, **암시적 권한 부여 및 하이브리드 흐름** 섹션에서 **ID 토큰** 을 선택합니다.
> 1. **구성** 을 선택합니다.
> 1. **관리** 아래에서 **인증서 및 비밀** > **클라이언트 암호** > **새 클라이언트 암호** 를 선택합니다.
> 1. 키 설명(인스턴스 앱 비밀에 대한)을 입력합니다.
> 1. 키 기간을 **1년 후, 2년 후** 또는 **만료 기한 제한 없음** 으로 선택합니다.
> 1. **추가** 를 선택합니다. 키 값이 표시됩니다. 키 값을 복사하고 나중에 사용할 수 있도록 안전한 위치에 저장합니다.
> 
> 
> ## <a name="download-the-sample-application-and-modules"></a>샘플 애플리케이션 및 모듈 다운로드
> 
> 다음으로, 샘플 리포지토리를 복제하고 NPM 모듈을 설치합니다.
> 
> 셸 또는 명령줄에서 다음 명령을 실행합니다.
> 
> `$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`
> 
> 또는
> 
> `$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`
> 
> 프로젝트 루트 디렉터리에서 다음 명령을 실행합니다.
> 
> `$ npm install`
> 
> ## <a name="configure-the-application"></a>애플리케이션 구성
> 
> 지침에 따라 config.js의 `exports.creds`에 매개 변수를 입력합니다.
> 
> * `exports.identityMetadata`의 `<tenant_name>`을 \*.onmicrosoft.com 형식의 Azure AD 테넌트 이름으로 업데이트합니다.
> * `exports.clientID`를 앱 등록에서 적어둔 애플리케이션 ID로 업데이트합니다.
> * `exports.clientSecret`를 앱 등록에서 적어둔 애플리케이션 비밀로 업데이트합니다.
> * `exports.redirectUrl`을 앱 등록에서 적어둔 리디렉션 URI로 업데이트합니다.
> 
> **프로덕션 앱에 대한 선택적 구성:**
> 
> * 다른 `post_logout_redirect_uri`를 사용하려는 경우 config.js의 `exports.destroySessionUrl`을 업데이트합니다.
> 
> * [mongoDB](https://www.mongodb.com) 또는 다른 [호환 세션 저장소](https://github.com/expressjs/session#compatible-session-stores)를 사용하려는 경우 config.js의 `exports.useMongoDBSessionStore`를 true로 설정합니다.
> 이 샘플의 기본 세션 저장소는 `express-session`입니다. 기본 세션 저장소는 프로덕션에 적합하지 않습니다.
> 
> * mongoDB 세션 저장소 및 다른 데이터베이스 URI를 사용하려는 경우 `exports.databaseUri`를 업데이트합니다.
> 
> * `exports.mongoDBSessionMaxAge`를 업데이트합니다. 여기서 mongoDB에 세션을 유지하려는 시간을 지정할 수 있습니다. 단위는 초입니다.
> 
> ## <a name="build-and-run-the-application"></a>애플리케이션 빌드 및 실행
> 
> mongoDB 서비스를 시작합니다. 이 앱에서 mongoDB 세션 저장소를 사용하는 경우 [mongoDB](http://www.mongodb.org/)를 설치하고 서비스를 먼저 시작해야 합니다. 기본 세션 저장소를 사용하는 경우 이 단계를 건너뛰어도 됩니다.
> 
> 명령줄에서 다음 명령을 사용하여 앱을 실행합니다.
> 
> ```
> $ node app.js
> ```
> 
> **서버 출력을 이해하기 어려운가요?:** 이 샘플에서는 로깅에 `bunyan`를 사용합니다. 위와 같이 bunyan을 설치하고 서버를 실행하지만 bunyan 이진을 통해 파이핑하지 않는 한, 콘솔을 이해하기 어렵습니다.
> 
> ```
> $ npm install -g bunyan
> 
> $ node app.js | bunyan
> ```
> 
> ### <a name="youre-done"></a>완료되었습니다!
> 
> 서버가 `http://localhost:3000`에서 성공적으로 실행될 것입니다.
> 
> [!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
> 
> ## <a name="next-steps"></a>다음 단계
> Microsoft ID 플랫폼에서 지원하는 웹앱 시나리오에 대해 자세히 알아보세요.
> > [!div class="nextstepaction"]
> > [사용자를 로그인하는 웹앱 시나리오](scenario-web-app-sign-user-overview.md)
