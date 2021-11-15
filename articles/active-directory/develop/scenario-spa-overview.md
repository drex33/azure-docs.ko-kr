---
title: JavaScript 단일 페이지 앱 시나리오
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼을 사용하여 단일 페이지 애플리케이션(시나리오 개요)을 빌드하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2021
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 19692739814a4db3ef2f981461254de415c9bdcc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444177"
---
# <a name="scenario-single-page-application"></a>시나리오: 단일 페이지 애플리케이션

SPA(단일 페이지 애플리케이션)를 빌드하는 데 필요한 모든 것을 알아보세요. Azure Static Web Apps에 대한 지침은 대신 [Static Web Apps에 대한 인증 및 권한 부여](../../static-web-apps/authentication-authorization.md)를 참조하세요.

## <a name="getting-started"></a>시작

아직 없는 경우 JavaScript SPA 빠른 시작을 완료하여 첫 번째 앱을 만듭니다.

[빠른 시작: 단일 페이지 애플리케이션](./quickstart-v2-javascript-auth-code.md)

## <a name="overview"></a>개요

많은 최신 웹 애플리케이션이 클라이언트 쪽 단일 페이지 애플리케이션으로 빌드됩니다. 개발자는 JavaScript 또는 Angular, Vue, React 같은 SPA 프레임워크를 사용하여 단일 페이지 애플리케이션을 작성합니다. 이러한 애플리케이션은 웹 브라우저에서 실행되며 기존 서버 쪽 웹 애플리케이션과 다른 인증 특징이 있습니다.

Microsoft ID 플랫폼은 단일 페이지 애플리케이션에서 사용자를 로그인하고 토큰을 가져와서 백 엔드 서비스 또는 웹 API에 액세스하는 다음과 같은 **두 가지** 옵션을 제공합니다.

- [OAuth 2.0 인증 코드 흐름(PKCE 사용)](./v2-oauth2-auth-code-flow.md). 권한 부여 코드 흐름을 사용하면 인증된 사용자를 나타내는 **ID** 토큰과 보호된 API를 호출하는 데 필요한 **액세스** 토큰의 권한 부여 코드를 애플리케이션에서 교환할 수 있습니다. 

    코드 교환용 증명 키 또는 _PKCE_ 는 권한 부여 코드 삽입 공격을 방지하기 위한 인증 코드 흐름의 확장입니다. 이 IETF 표준은 권한 부여 코드를 가로채는 위협을 완화하고 [RFC 7636](https://datatracker.ietf.org/doc/html/rfc7636)에 설명된 대로 퍼블릭 클라이언트에서 보안 OAuth 교환을 사용하도록 설정합니다. 뿐만 아니라 해당 사용자의 상호 작용을 요구하지 않고 사용자 대신 리소스에 대한 장기 액세스 권한을 제공하는 **새로 고침** 토큰을 반환합니다. 

    PKCE와 함께 권한 부여 코드 흐름을 사용하는 것이 네이티브 및 브라우저 기반 JavaScript 앱뿐만 아니라 다른 모든 유형의 OAuth 클라이언트에서도 더 안전하고 **권장되는** 권한 부여 방법입니다.

![단일 페이지 애플리케이션 인증](./media/scenarios/spa-app-auth.svg)

- [OAuth 2.0 암시적 흐름](./v2-oauth2-implicit-grant-flow.md). 암시적 권한 부여 흐름을 사용하면 애플리케이션에서 **ID** 및 **액세스** 토큰을 얻을 수 있습니다. 권한 부여 코드 흐름과 달리, 암시적 허용 흐름은 **새로 고침 토큰** 을 반환하지 않습니다.

![단일 페이지 애플리케이션 암시적](./media/scenarios/spa-app.svg)

이 인증 흐름에는 Electron 및 React-Native와 같은 플랫폼 간 JavaScript 프레임워크를 사용하는 애플리케이션 시나리오가 포함되지 않습니다. 이러한 시나리오에는 기본 플랫폼과의 상호 작용을 위한 추가 기능이 필요합니다.

## <a name="specifics"></a>특수 적용 사항

애플리케이션에 이 시나리오를 사용하려면 다음 항목이 필요합니다.

* Azure AD(Azure Active Directory)로 애플리케이션 등록 등록 단계는 암시적 허용 흐름과 인증 코드 흐름 간에 다릅니다.
* 애플리케이션 ID와 같은 등록된 애플리케이션 속성이 있는 애플리케이션 구성
* MSAL.js(JavaScript용 Microsoft 인증 라이브러리)를 사용하여 인증 흐름을 수행해 로그인하고 토큰을 얻습니다.

## <a name="recommended-reading"></a>추천 자료

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서인 [앱 등록](scenario-spa-app-registration.md)으로 이동합니다.
