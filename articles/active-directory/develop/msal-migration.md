---
title: MSAL(Microsoft 인증 라이브러리)로 마이그레이션
titleSuffix: Microsoft identity platform
description: MSAL(Microsoft 인증 라이브러리)과 ADAL(Azure AD 인증 라이브러리)의 차이점과 MSAL로 마이그레이션하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 145302d3569c2fc9c5d5bd58a5f9b7ccefac0b8e
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122538994"
---
# <a name="migrate-applications-to-the-microsoft-authentication-library-msal"></a>MSAL(Microsoft 인증 라이브러리)로 애플리케이션 마이그레이션

애플리케이션에서 인증 및 권한 부여 기능에 ADAL(Azure Active Directory 인증 라이브러리)을 사용하는 경우 이제 [MSAL(Microsoft 인증 라이브러리)](msal-overview.md#languages-and-frameworks)로 마이그레이션합니다.

- 보안 수정을 포함하여 ADAL에 대한 모든 Microsoft 지원과 개발은 2022년 6월 30일에 종료됩니다.
- 2020년 6월 30일 이후에는 새로운 기능이 ADAL에 추가되지 않았습니다.

> [!WARNING]
> 2022년 6월 30일에 ADAL 지원이 종료되기 전에 MSAL로 마이그레이션하지 않도록 선택하면 앱의 보안이 위험에 노출됩니다. ADAL을 사용하는 기존 앱은 지원 종료 날짜 후에도 계속 작동하지만 Microsoft는 ADAL에 대한 보안 수정을 더 이상 릴리스하지 않습니다.

## <a name="why-switch-to-msal"></a>MSAL로 전환하는 이유

MSAL은 다음 기능을 포함하여 ADAL에 비해 여러 가지 이점을 제공합니다. 

|기능|MSAL|ADAL|
|---------|---------|---------|
|**보안**|||
|2022년 6월 30일 이후 보안 수정|![2022년 6월 30일 이후 보안 수정 - MSAL은 이 기능을 제공합니다.][y]|![2022년 6월 30일 이후 보안 수정 - ADAL은 이 기능을 제공하지 않습니다.][n]|
| Microsoft Graph와 [CAE(지속적인 액세스 권한 평가)](app-resilience-continuous-access-evaluation.md)를 지원하는 기타 API에 대한 정책이나 중요 이벤트에 따라 사전에 토큰을 새로 고치고 철회합니다.|![Microsoft Graph와 CAE(지속적인 액세스 권한 평가)를 지원하는 기타 API에 대한 정책이나 중요 이벤트에 따라 사전에 토큰을 새로 고치고 철회함 - MSAL은 기능을 제공합니다.][y]|![Microsoft Graph와 CAE(지속적인 액세스 권한 평가)를 지원하는 기타 API에 대한 정책이나 중요 이벤트에 따라 사전에 토큰을 새로 고치고 철회함 - ADAL은 기능을 제공하지 않습니다.][n]|
| OAuth v2.0 및 OIDC(OpenID Connect)를 준수하는 표준 |![OAuth v2.0 및 OIDC(OpenID Connect)를 준수하는 표준 - MSAL은 기능을 제공합니다.][y]|![OAuth v2.0 및 OIDC(OpenID Connect)를 준수하는 표준 - ADAL은 기능을 제공하지 않습니다.][n]|
|**사용자 계정 및 환경**|||
|Azure AD(Azure Active Directory) 계정|![Azure Active Directory(Azure AD) 계정 - MSAL은 기능을 제공합니다.][y]|![Azure Active Directory(Azure AD) 계정 - ADAL은 기능을 제공합니다.][y]|
| MSA(Microsoft 계정) |![MSA(Microsoft 계정) - MSAL은 기능을 제공합니다.][y]|![Microsoft 계정(MSA) - ADAL은 기능을 제공하지 않습니다.][n]|
| Azure AD B2C 계정 |![Azure AD B2C 계정 - MSAL은 기능을 제공합니다.][y]|![Azure AD B2C 계정 - ADAL은 기능을 제공하지 않습니다.][n]|
| 최상의 Single Sign-On 환경 |![최상의 Single Sign-On 환경 - MSAL은 기능을 제공합니다.][y]|![최상의 Single Sign-On 환경 - ADAL은 기능을 제공하지 않습니다.][n]|
|**복원력**|||
| 자동 관리 토큰 갱신 |![자동 관리 토큰 갱신 - MSAL은 기능을 제공합니다.][y]|![자동 관리 토큰 갱신 - ADAL은 기능을 제공하지 않습니다.][n]|
| 제한 |![제한 - MSAL은 기능을 제공합니다.][y]|![제한 - ADAL은 기능을 제공하지 않습니다.][n]|

## <a name="ad-fs-support-in-msalnet"></a>MSAL.NET의 AD FS 지원

MSAL.NET, MSAL Java, MSAL Python을 사용하여 AD FS(Active Directory Federation Services) 2019 이상에서 토큰을 가져올 수 있습니다. AD FS 2016을 포함한 이전 버전의 AD FS는 MSAL에서 지원되지 않습니다.

AD FS를 계속 사용해야 하는 경우 ADAL에서 MSAL로 애플리케이션을 업데이트하기 전에 AD FS 2019 이상으로 업그레이드해야 합니다.

## <a name="how-to-migrate-to-msal"></a>MSAL로 마이그레이션하는 방법

마이그레이션을 시작하기 전에 인증에 ADAL을 사용하는 앱을 식별해야 합니다. 이 문서의 단계에 따라 Azure Portal을 사용하여 목록을 가져옵니다.
- [방법: 테넌트에서 ADAL을 사용하여 앱의 전체 목록 가져오기](howto-get-list-of-all-active-directory-auth-library-apps.md)

ADAL을 사용하는 앱을 식별한 후 아래 그림과 같이 애플리케이션 유형에 따라 MSAL로 마이그레이션합니다.

[!INCLUDE [application type](includes/adal-msal-migration.md)]

## <a name="migration-help"></a>마이그레이션 도움말

ADAL에서 MSAL로 앱을 마이그레이션하는 방법에 관한 질문이 있는 경우 다음과 같은 몇 가지 옵션을 사용할 수 있습니다.

- `[azure-ad-adal-deprecation]`을 사용하여 [Microsoft Q&A](/answers/topics/azure-ad-adal-deprecation.html)에 질문을 게시합니다.
- 라이브러리의 GitHub 리포지토리에서 이슈를 엽니다. 라이브러리 별 리포지토리로의 링크는 MSAL 개요 문서의 [언어 및 프레임워크](msal-overview.md#languages-and-frameworks) 섹션을 참조하세요.

애플리케이션 개발 시 ISV(독립 소프트웨어 공급업체)와 파트너 관계를 맺은 경우 MSAL로 마이그레이션 과정을 이해하려면 직접 ISV에 문의하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

사용 정보, 다양한 프로그래밍 언어와 애플리케이션 유형에 사용할 수 있는 라이브러리 등을 포함하여 MSAL에 관한 자세한 내용은 다음을 참조하세요.

- [MSAL을 사용하여 토큰 획득 및 캐시](msal-acquire-cache-tokens.md)
- [애플리케이션 구성 옵션](msal-client-application-configuration.md)
- [MSAL 인증 라이브러리](reference-v2-libraries.md)

<!--
 ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->
[y]: media/common/yes.png
[n]: media/common/no.png