---
title: MSAL에 대해 알아보기 | Azure
titleSuffix: Microsoft identity platform
description: MSAL(Microsoft 인증 라이브러리)을 통해 애플리케이션 개발자는 보안 웹 API를 호출하기 위한 토큰을 획득할 수 있습니다. 이러한 웹 API는 Microsoft Graph, 기타 Microsoft API, 타사 웹 API 또는 자체 웹 API일 수 있습니다. MSAL은 다수의 애플리케이션 아키텍처와 플랫폼을 지원합니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40, has-adal-ref
ms.openlocfilehash: 405843ad0d56a75dac20dc2a6c85d8bc15600312
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539138"
---
# <a name="overview-of-the-microsoft-authentication-library-msal"></a>MSAL(Microsoft 인증 라이브러리) 개요
MSAL(Microsoft 인증 라이브러리)을 통해 개발자는 사용자를 인증하고 보안 웹 API에 액세스하기 위해 Microsoft ID 플랫폼에서 [토큰](developer-glossary.md#security-token)을 획득할 수 있습니다. Microsoft Graph, 기타 Microsoft API, 타사 웹 API 또는 자체 웹 API에 대한 보안 액세스를 제공하는 데 사용할 수 있습니다. MSAL은 .NET, JavaScript, Java, Python, Android 및 iOS를 비롯한 다양한 애플리케이션 아키텍처 및 플랫폼을 지원합니다.

MSAL을 통해 여러 플랫폼에 대해 일관된 API를 사용하여 토큰을 얻을 수 있는 방법은 여러 가지입니다. MSAL을 사용하면 다음과 같은 이점이 있습니다.

* 애플리케이션의 프로토콜에 대해 OAuth 라이브러리나 코드를 직접 사용할 필요가 없습니다.
* 사용자 대신 또는 애플리케이션 대신 토큰을 획득합니다(플랫폼에 해당하는 경우).
* 토큰 캐시를 유지 관리하고 토큰 만료가 가까워지면 토큰을 새로 고쳐줍니다. 토큰 만료를 직접 처리할 필요가 없습니다.
* 애플리케이션에 로그인할 수 있는 대상을 지정할 수 있습니다(사용자의 조직, 여러 조직, 직장 및 학교와 Microsoft 개인 계정, Azure AD B2C의 소셜 ID, 소버린의 사용자 및 국가별 클라우드).
* 구성 파일에서 애플리케이션을 설정할 수 있습니다.
* 실행 가능한 예외, 로깅 및 원격 분석을 노출하여 앱 문제를 해결할 수 있습니다.

> [!VIDEO https://www.youtube.com/embed/zufQ0QRUHUk]

## <a name="application-types-and-scenarios"></a>애플리케이션 유형 및 시나리오
MSAL을 사용하면 웹 애플리케이션, 웹 API, 단일 페이지 앱(JavaScript), 모바일과 원시 애플리케이션 및 디먼과 서버 쪽 애플리케이션 등과 같은 다양한 애플리케이션 유형에서 토큰을 획득할 수 있습니다.

MSAL은 다음을 비롯한 여러 가지 애플리케이션 시나리오에 사용할 수 있습니다.

* [단일 페이지 애플리케이션(JavaScript)](scenario-spa-overview.md)
* [사용자가 로그인하는 웹앱](scenario-web-app-sign-user-overview.md)
* [사용자가 로그인하고 사용자를 대신하여 웹 API를 호출하는 웹 애플리케이션](scenario-web-app-call-api-overview.md)
* [인증된 사용자만 액세스할 수 있도록 웹 API 보호](scenario-protected-web-api-overview.md)
* [로그인한 사용자를 대신하여 또 다른 다운스트림 웹 API를 호출하는 웹 API](scenario-web-api-call-api-overview.md)
* [로그인한 사용자를 대신하여 웹 API를 호출하는 데스크톱 애플리케이션](scenario-desktop-overview.md)
* [대화형으로 로그인한 사용자를 대신하여 웹 API를 호출하는 모바일 애플리케이션](scenario-mobile-overview.md).
* [자체적으로 대신 웹 API를 호출하는 데스크톱/서비스 디먼 애플리케이션](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>언어 및 프레임워크

| 라이브러리 | 지원되는 플랫폼 및 프레임워크|
| --- | --- |
| [Android용 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular)| Angular 및 Angular.js 프레임워크가 있는 단일 페이지 앱|
| [iOS 및 macOS용 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS 및 macOS|
| [MSAL Go(미리 보기)](https://github.com/AzureAD/microsoft-authentication-library-for-go)|Windows, macOS, Linux|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows, macOS, Linux|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)| Vue.js, Ember.js, 또는 Durandal.js와 같은 JavaScript/TypeScript 프레임워크|
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, 유니버설 Windows 플랫폼|
| [MSAL 노드](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)|웹앱(Express 포함), 데스크톱 앱(Electron 포함), 플랫폼 간 콘솔 앱|
| [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows, macOS, Linux|
| [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| React 및 React 기반 라이브러리가 있는 단일 페이지 앱(Next.js, Gatsby.js)|

## <a name="migrate-apps-that-use-adal-to-msal"></a>ADAL을 사용하는 앱을 MSAL로 마이그레이션

ADAL(Active Directory 인증 라이브러리)은 MSAL이 Microsoft ID 플랫폼과 통합되는 개발자용 Azure AD(v1.0) 엔드포인트와 통합됩니다. v1.0 엔드포인트는 회사 계정을 지원하지만 개인 계정은 지원하지 않습니다. v2.0 엔드포인트는 Microsoft 개인 계정과 회사 계정을 단일 인증 시스템으로 통합한 것입니다. 또한 MSAL을 사용하면 Azure AD B2C에 대한 인증을 받을 수 있습니다.

MSAL로 마이그레이션하는 방법에 대한 자세한 내용은 [Microsoft 인증 라이브러리(MSAL)로 애플리케이션 마이그레이션](msal-migration.md)을 참조하세요.
