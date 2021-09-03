---
title: MSAL.NET 및 Microsoft.Identity.Web으로 마이그레이션
titleSuffix: Microsoft identity platform
description: .NET용 Azure AD 인증 라이브러리(ADAL.NET)에서 .NET용 Microsoft 인증 라이브러리(MSAL.NET) 또는 Microsoft.Identity.Web으로 마이그레이션하는 이유와 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/08/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: cb62c7558f7811ce7123ba405cbf01a75d7a3392
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122530915"
---
# <a name="migrating-applications-to-msalnet-or-microsoftidentityweb"></a>MSAL.NET 또는 Microsoft.Identity.Web으로 애플리케이션 마이그레이션

## <a name="why-migrate-to-msalnet-or-microsoftidentityweb"></a>MSAL.NET 또는 Microsoft.Identity.Web으로 마이그레이션하는 이유

MSAL.NET(.NET용 Microsoft 인증 라이브러리) 및 ADAL.NET(.NET용 Azure AD 인증 라이브러리)은 모두 Azure AD 엔터티를 인증하고 Azure AD에서 토큰을 요청하는 데 사용됩니다. 지금까지 대부분의 개발자는 Azure ADAL(AD 인증 라이브러리)을 사용하여 개발자용 Azure AD 플랫폼(v1.0)에서 토큰을 요청했습니다. 이러한 토큰은 Azure AD ID(회사 및 학교 계정)를 인증하는 데 사용됩니다. 

MSAL은 ADAL에 비해 이점이 있습니다. 이러한 이점 중 일부는 다음과 같습니다.

- Azure AD B2C를 사용하여 회사 또는 학교 계정, 개인 Microsoft 계정, 소셜 또는 로컬 계정과 같은 광범위한 Microsoft ID 집합을 인증할 수 있습니다.
- 사용자가 최상의 Single Sign-On 환경을 이용할 수 있습니다.
- 애플리케이션에서 증분 동의, 조건부 액세스를 사용하도록 설정할 수 있습니다.
- 보안 및 복원력 측면에서 지속적인 혁신의 이점을 누릴 수 있습니다.
- 애플리케이션은 복원력 및 보안 측면에서 모범 사례를 구현합니다.

**MSAL.NET 또는 Microsoft.Identity.Web은 이제 Microsoft ID 플랫폼과 함께 사용할 수 있는 권장 인증 라이브러리입니다**. ADAL.NET에는 새로운 기능이 구현되지 않습니다. 대신 MSAL.NET 향상에 중점을 두었습니다. 자세한 내용은 다음 공지를 참조하세요. [Microsoft 인증 라이브러리 및 Microsoft Graph API를 사용하도록 애플리케이션 업데이트](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)

## <a name="should-you-migrate-to-msalnet-or-to-microsoftidentityweb"></a>MSAL.NET 또는 Microsoft.Identity.Web으로 마이그레이션해야 합니다.

MSAL.NET 및 ADAL.NET의 세부 정보를 자세히 알아보기 전에 MSAL.NET 또는 [Microsoft.Identity.Web](microsoft-identity-web.md)과 같은 상위 수준 추상화 사용 여부를 확인할 수 있습니다.

아래의 의사 결정 트리에 대한 자세한 내용은 [MSAL.NET 또는 Microsoft.Identity.Web?](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-or-Microsoft.Identity.Web)을 참조하세요.

!["ADAL.NET에서 마이그레이션하는 경우 MSAL.NET 및 Microsoft.Identity.Web을 사용해야 하는지 아니면 둘 다를 사용해야 하는지를 선택하는 방법을 설명하는 블록 다이어그램"](media/msal-net-migration/decision-diagram.png)

<!-- 1P
## Examples of 1P Migrations

[See examples](https://identitydivision.visualstudio.com/DevEx/_wiki/wikis/DevEx.wiki/20413/1P-ADAL.NET-to-MSAL.NET-migration-examples) of other 1P teams who have already, or are currently, migrating from ADAL to one of the MSAL+ solutions above. See their code, and in some cases read about their migration story.
 -->
 
## <a name="next-steps"></a>다음 단계

- [ASP.NET MVC 또는 .NET 클래식을 기반으로 빌드된 기밀 클라이언트 애플리케이션을 ADAL.NET에서 MSAL.NET으로 마이그레이션](msal-net-migration-confidential-client.md)하는 방법을 알아봅니다.
- [ADAL.NET과 MSAL.NET 앱의 차이점](msal-net-differences-adal-net.md)에 대해 자세히 알아봅니다.
- ASP.NET Core를 기반으로 빌드된 기밀 클라이언트 애플리케이션을 ADAL.NET에서 Microsoft.Identity.Web으로 마이그레이션하는 방법을 알아봅니다.
  -  [웹앱](https://github.com/AzureAD/microsoft-identity-web/wiki/web-apps#migrating-from-previous-versions--adding-authentication)
  -  [Web API](https://github.com/AzureAD/microsoft-identity-web/wiki/web-apis)