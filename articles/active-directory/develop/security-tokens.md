---
title: 보안 토큰 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼의 보안 토큰에 대한 기본 사항에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2021
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 2567a75c0accd7e2bb932d8578e1a225518f5ce3
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092759"
---
# <a name="security-tokens"></a>보안 토큰

중앙 집중식 ID 공급자는 기업 네트워크에서 반드시 로그인하지 않아도 되는 전 세계에 사용자가 있는 앱에 특히 유용합니다. Microsoft ID 플랫폼은 사용자를 인증하며 [액세스 토큰](developer-glossary.md#access-token), [새로 고침 토큰](developer-glossary.md#refresh-token) 및 [ID 토큰](developer-glossary.md#id-token) 등의 보안 토큰을 제공합니다. 보안 토큰을 사용하면 [클라이언트 애플리케이션](developer-glossary.md#client-application)이 [리소스 서버](developer-glossary.md#resource-server)에서 보호되는 리소스에 액세스할 수 있습니다.

**액세스 토큰**: 액세스 토큰은 [OAuth 2.0](active-directory-v2-protocols.md) 흐름의 일환으로 [인증 서버](developer-glossary.md#authorization-server)를 통해 발급되는 보안 토큰입니다. 토큰에 사용할 사용자 및 리소스에 대한 정보를 포함합니다. 이 정보를 사용하여 웹 API 및 기타 보호되는 리소스에 액세스할 수 있습니다. 클라이언트 앱에 액세스 권한을 부여하기 위해 리소스에서 액세스 토큰의 유효성을 검사합니다. Microsoft ID 플랫폼에서 액세스 토큰을 발급하는 방법에 대해 자세히 알아보려면 [액세스 토큰](access-tokens.md)을 참조하세요.

**새로 고침 토큰**: 액세스 토큰은 단기간 동안에만 유효하기 때문에 인증 서버가 액세스 토큰을 발급하는 동시에 새로 고침 토큰을 발급하는 경우도 있습니다. 클라이언트 애플리케이션은 필요한 경우 새 액세스 토큰을 이 새로 고침 토큰으로 교환할 수 있습니다. Microsoft ID 플랫폼이 권한을 철회하기 위해 새로 고침 토큰을 사용하는 방법에 대해 자세히 알아보려면 [토큰 새로 고침](refresh-tokens.md)을 참조하세요.

**ID 토큰**: ID 토큰은 [OpenID Connect](v2-protocols-oidc.md) 흐름의 일환으로 클라이언트 애플리케이션에 전송됩니다. 액세스 토큰 대신 또는 함께 전송할 수 있습니다. ID 토큰은 클라이언트에서 사용자를 인증하는 데 사용됩니다. Microsoft ID 플랫폼에서 ID 토큰을 발급하는 방법에 대해 자세히 알아보려면 [ID 토큰](id-tokens.md)을 참조하세요.

> [!NOTE]
> 이 문서에서는 OAuth2 및 OpenID Connect 프로토콜에서 사용하는 보안 토큰을 설명합니다. 많은 엔터프라이즈 애플리케이션이 SAML을 사용하여 사용자를 인증합니다. SAML 어설션에 대한 자세한 내용은 [Azure Active Directory SAML 토큰 참조](reference-saml-tokens.md)를 참조하세요.

## <a name="validate-security-tokens"></a>보안 토큰 유효성 검사

이는 토큰이 생성된 앱, 사용자가 로그인한 웹앱 또는 토큰의 유효성을 검사하기 위해 호출되는 웹 API에 따라 결정됩니다. 토큰은 권한 부여 서버에서 프라이빗 키를 사용하여 서명됩니다. 권한 부여 서버는 해당하는 공개 키를 게시합니다. 토큰의 유효성을 검사하기 위해 앱은 권한 부여 서버 공개 키를 사용하여 서명을 확인하고 프라이빗 키를 사용하여 서명을 만들었는지 확인합니다.

토큰은 제한된 시간 동안만 유효합니다. 일반적으로 권한 부여 서버는 다음과 같은 토큰 쌍을 제공합니다.

* 애플리케이션 또는 보호된 리소스에 액세스하는 액세스 토큰.
* 액세스 토큰이 만료될 무렵 액세스 토큰을 새로 고치는 데 사용되는 새로 고침 토큰.

액세스 토큰은 웹 API에 `Authorization` 헤더의 전달자 토큰으로 전달됩니다. 앱은 권한 부여 서버에 새로 고침 토큰을 제공할 수 있습니다. 앱에 대한 사용자 액세스가 취소되지 않은 경우 새 액세스 토큰과 새 새로 고침 토큰을 다시 받게 됩니다. 이는 엔터프라이즈를 떠나는 사람의 시나리오를 처리하는 방법입니다. 권한 부여 서버에서 새로 고침 토큰을 받으면 사용자에게 더 이상 권한이 부여되지 않은 경우 다른 유효한 액세스 토큰을 발급하지 않습니다.

## <a name="json-web-tokens-and-claims"></a>JSON Web Token 및 클레임

Microsoft ID 플랫폼은 *클레임* 을 포함하는 JWT(JSON Web Token)로 보안 토큰을 구현합니다. JWT는 보안 토큰으로 사용되므로 이러한 형식의 인증을 *JWT 인증* 이라고도 합니다.

[클레임](developer-glossary.md#claim)은 클라이언트 애플리케이션과 같은 한 엔터티 또는 [리소스 소유자](developer-glossary.md#resource-owner)와 같은 다른 엔터티에 대한 어설션을 제공합니다. 클레임은 JWT 클레임 또는 JSON Web Token 클레임이라고 할 수도 있습니다.

클레임은 토큰 주체에 대한 팩트를 릴레이하는 이름 또는 값 쌍입니다. 예를 들어, 클레임은 권한 부여 서버에서 인증된 보안 주체에 대한 팩트를 포함할 수 있습니다. 특정 토큰에 있는 클레임은 토큰 유형, 주체를 인증하는 데 사용되는 자격 증명 유형 및 애플리케이션 구성과 같은 다양한 사항에 따라 달라 집니다.

애플리케이션은 다음과 같은 다양한 작업에 대한 클레임을 사용할 수 있습니다.

* 토큰의 유효성을 검사합니다.
* 토큰 주체의 [테넌트](developer-glossary.md#tenant)를 식별합니다.
* 사용자 정보를 표시합니다.
* 주체의 권한 부여를 확인합니다.

클레임은 다음과 같은 정보를 제공하는 키-값 쌍으로 구성됩니다.

* 토큰을 생성한 보안 토큰 서버.
* 토큰이 생성된 날짜.
* 주체(예: 디먼을 제외한 사용자).
* 토큰이 생성된 앱인 대상.
* 토큰을 요청한 앱(클라이언트). 웹앱의 경우 이 앱이 대상과 동일할 수 있습니다.

Microsoft ID 플랫폼에서 토큰 및 클레임 정보를 구현하는 방법에 대한 자세한 내용은 [액세스 토큰](access-tokens.md) 및 [ID 토큰](id-tokens.md)을 참조하세요.

## <a name="how-each-flow-emits-tokens-and-codes"></a>각 흐름에서 토큰 및 코드를 내보내는 방법

클라이언트를 구축하는 방법에 따라 Microsoft ID 플랫폼에서 지원되는 인증 흐름 중 하나(또는 여러 개)를 사용할 수 있습니다. 이러한 흐름은 다양한 토큰(ID 토큰, 새로 고침 토큰, 액세스 토큰) 및 인증 코드를 생성할 수 있습니다. 작업을 수행하려면 서로 다른 토큰이 필요합니다. 이 테이블에서는 개요를 제공합니다.

|흐름 | 필수 사항 | ID 토큰 | 액세스 토큰 | 새로 고침 토큰 | 인증 코드 |
|-----|----------|----------|--------------|---------------|--------------------|
|[인증 코드 흐름](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[암시적 흐름](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[하이브리드 OIDC 흐름](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[새로 고침 토큰 상환](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 새로 고침 토큰 | x | x | x| |
|[On-Behalf-Of 흐름](v2-oauth2-on-behalf-of-flow.md) | 액세스 토큰| x| x| x| |
|[클라이언트 자격 증명](v2-oauth2-client-creds-grant-flow.md) | | | x(앱 전용)| | |

암시적 모드를 통해 발급된 토큰은 URL을 통해 브라우저에 다시 전달되기 때문에 길이 제한이 있으며 여기서 `response_mode`는 `query` 또는 `fragment`입니다. 일부 브라우저는 브라우저 표시줄에 배치할 수 있는 URL의 크기에 제한이 있으며 너무 길면 실패할 수 있습니다. 따라서 이러한 토큰에는 `groups` 또는 `wids` 클레임이 없습니다.

## <a name="next-steps"></a>다음 단계

Microsoft ID 플랫폼의 인증 및 권한 부여에 대한 자세한 내용은 다음 문서를 참조하세요.

* 인증 및 권한 부여의 기본 개념에 대한 자세한 내용은 [인증 및 권한 부여](authentication-vs-authorization.md)를 참조하세요.
* 통합을 위해 애플리케이션을 등록하는 방법에 대한 자세한 내용은 [애플리케이션 모델](application-model.md)을 참조하세요.
* 웹, 데스크톱 및 모바일 앱의 로그인 흐름에 대해 알아보려면 [앱 로그인 흐름](app-sign-in-flow.md)을 참조하세요.
