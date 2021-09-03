---
title: Azure AD B2C와 함께 MSAL.js 사용
titleSuffix: Microsoft identity platform
description: MSAL.js(JavaScript용 Microsoft 인증 라이브러리)는 애플리케이션이 Azure AD B2C와 함께 작업하고 토큰을 획득하여 보안 웹 API를 호출할 수 있도록 합니다. 이러한 웹 API에는 Microsoft Graph, 기타 Microsoft API, 타사 웹 API 또는 사용자의 웹 API가 있습니다.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev devx-track-js
ms.openlocfilehash: 1aa07a4135452ae4303ecfee410b840cc75efffb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528406"
---
# <a name="use-the-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>Azure AD B2C와 함께 작업하려면 JavaScript용 Microsoft 인증 라이브러리 사용

[MSAL.js(JavaScript용 Microsoft 인증 라이브러리)](https://github.com/AzureAD/microsoft-authentication-library-for-js)를 사용하면 JavaScript 개발자가 Azure AD B2C([Azure Active Directory B2C](../../active-directory-b2c/overview.md))를 사용하여 소셜 및 로컬 ID로 사용자를 인증할 수 있습니다.

Azure AD B2C를 ID 관리 서비스로 사용하면 고객이 애플리케이션을 사용할 때 프로필을 등록, 로그인, 관리하는 방법을 사용자 정의하고 제어할 수 있습니다. 

또한 Azure AD B2C를 사용하면 인증 프로세스 중에 애플리케이션이 표시하는 UI를 브랜딩 및 사용자 지정할 수 있습니다.

## <a name="supported-app-types-and-scenarios"></a>지원되는 앱 형식 및 시나리오

MSAL.js를 사용하면 [단일 페이지 애플리케이션](../../active-directory-b2c/application-types.md#single-page-applications)이 [PKCE가 부여된 인증 코드 흐름](../../active-directory-b2c/authorization-code-flow.md)을 사용하여 Azure AD B2C로 사용자를 로그인할 수 있습니다. MSAL.js 및 Azure AD B2C:

- 사용자는 소셜 및 로컬 ID로 인증할 수 **있습니다**.
- 사용자에게 Azure AD B2C 보호 리소스(Azure AD 보호 리소스 제외)에 액세스하는 권한을 부여할 수 **있습니다**.
- 사용자는 [위임된 권한](./v2-permissions-and-consent.md#permission-types)을 사용하여 Microsoft API(예: MS Graph API)에 대한 토큰을 얻을 수 **없습니다**.
- 관리자 권한이 있는 사용자는 [위임된 권한](./v2-permissions-and-consent.md#permission-types)을 사용하여 Microsoft API(예: MS Graph API)에 대한 토큰을 얻을 수 **있습니다**.

자세한 내용은 [Azure AD B2C 작업](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/working-with-b2c.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 방법에 대한 자습서를 따르세요.

- [단일 페이지 애플리케이션에서 Azure AD B2C로 사용자 로그인](../../active-directory-b2c/configure-authentication-sample-spa-app.md)
- [Azure AD B2C 보호 웹 API 호출](../../active-directory-b2c/enable-authentication-web-api.md)