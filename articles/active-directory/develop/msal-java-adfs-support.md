---
title: AD FS 지원(Java용 MSAL)
titleSuffix: Microsoft identity platform
description: MSAL4j(Java용 Microsoft 인증 라이브러리)의 AD FS(Active Directory Federation Services) 지원에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: marsma
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 9eea63573d2da462d055a784353a18bcee822998
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601162"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Java용 MSAL의 Active Directory Federation Services 지원

Windows Server에서 AD FS(Active Directory Federation Services)를 사용하면 OpenID Connect 및 OAuth 2.0 기반 인증 및 권한 부여를 Java용 MSAL(Java용 Microsoft 인증 라이브러리) 앱에 추가할 수 있습니다. 통합되면 앱은 AD FS에서 Azure AD를 통해 페더레이션된 사용자를 인증할 수 있습니다. 시나리오에 대한 자세한 내용은 [개발자를 위한 AD FS 시나리오](/windows-server/identity/ad-fs/ad-fs-development)를 참조하세요.

Java용 MSAL을 사용하는 앱은 Azure AD(Azure Active Directory)와 통신한 다음, AD FS에 페더레이션합니다.

Java용 MSAL은 Azure AD에서 관리되는 사용자(관리 사용자) 또는 AD FS와 같은 다른 ID 공급자로 관리되는 사용자(페더레이션 사용자)를 로그인시키는 Azure AD에 연결합니다. Java용 MSAL은 사용자가 페더레이션되었음을 인식하지 못합니다. 단지 Azure AD와만 통신합니다.

이 경우 사용하는 [기관](msal-client-application-configuration.md#authority)은 일반적인 기관(기관 호스트 이름 + 테넌트, 공용 또는 조직)입니다.

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>대화형으로 페더레이션된 사용자에 대한 토큰 획득

`AuthorizationCodeParameters` 또는 `DeviceCodeParameters`를 통해 `ConfidentialClientApplication.AcquireToken()` 또는 `PublicClientApplication.AcquireToken()`을 호출하는 경우 사용자 환경은 일반적으로 다음과 같습니다.

1. 사용자가 자신의 계정 ID를 입력합니다.
2. Azure AD에서 “조직의 페이지로 이동”이라는 메시지를 잠시 표시하고, 사용자는 ID 공급자의 로그인 페이지로 리디렉션됩니다. 로그인 페이지는 일반적으로 조직의 로고를 사용하여 사용자 지정됩니다.

이 페더레이션된 시나리오에서 지원되는 AD FS 버전은 다음과 같습니다.
- Active Directory Federation Services FS v2
- Active Directory Federation Services v3(Windows Server 2012 R2)
- Active Directory Federation Services v4(AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>사용자 이름과 암호를 통해 토큰 획득

`IntegratedWindowsAuthenticationParameters` 또는 `UsernamePasswordParameters`를 통해 `ConfidentialClientApplication.AcquireToken()` 또는 `PublicClientApplication.AcquireToken()`을 사용하여 토큰을 획득하는 경우 Java 용 MSAL은 사용자 이름에 따라 연락할 ID 공급자를 가져옵니다. Java용 MSAL은 ID 공급자로부터 [SAML 1.1 토큰](reference-saml-tokens.md)을 가져온 다음, JWT(JSON Web Token)를 반환하는 Azure AD에 제공합니다.

## <a name="next-steps"></a>다음 단계

페더레이션된 사례는 [홈 영역 검색 정책을 사용하여 애플리케이션에 대한 Azure Active Directory 로그인 동작 구성](../manage-apps/configure-authentication-for-federated-users-portal.md)을 참조하세요.