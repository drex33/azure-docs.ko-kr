---
title: 웹 API 애플리케이션 추가 - Azure Active Directory B2C | Microsoft Docs
description: Active Directory B2C 테넌트에 웹 API 애플리케이션을 추가하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 185cc7de17957cba9edde5219602d85222c4bbb3
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777750"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C 테넌트에 웹 API 애플리케이션 추가

 액세스 토큰을 제공하는 클라이언트 애플리케이션의 요청을 수락하고 응답할 수 있도록 테넌트에 웹 API 리소스를 등록합니다. 이 문서에서는 Azure AD B2C(Azure Active Directory B2C)에 웹 API를 등록하는 방법을 보여 줍니다.

Azure AD B2C 테넌트에 애플리케이션을 등록하려면 새로운 통합 **앱 등록** 환경 또는 레거시 **애플리케이션(레거시)** 환경을 사용하면 됩니다. [새 환경에 대해 자세히 알아보세요](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[앱 등록](#tab/app-reg-ga/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예를 들어 *webapi1* 과 같습니다.
1. **리디렉션 URI** 에서 **Web** 을 선택한 다음, Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환해야 하는 엔드포인트를 입력합니다. 프로덕션 애플리케이션에서 리디렉션 URI를 `https://localhost:5000`과 같은 엔드포인트로 설정할 수 있습니다. 개발 또는 테스트 중에 토큰의 디코딩된 콘텐츠를 표시하는 Microsoft 자체 웹 애플리케이션인 `https://jwt.ms`로 설정할 수 있습니다(토큰의 콘텐츠는 브라우저를 떠나지 않음). 언제든지 등록된 애플리케이션에서 리디렉션 URI를 추가하고 수정할 수 있습니다.
1. **등록** 을 선택합니다.
1. 웹 API의 코드에서 사용할 **애플리케이션(클라이언트) ID** 를 기록합니다.

암시적 허용 흐름을 구현하는 애플리케이션(예: [JavaScript 기반 SPA(단일 페이지 애플리케이션)](tutorial-register-spa.md))이 있는 경우 다음 단계를 수행하여 흐름을 사용하도록 설정할 수 있습니다.

1. **관리** 에서 **인증** 을 선택합니다.
1. **암시적 권한 부여** 에서 **액세스 토큰** 및 **ID 토큰** 확인란을 둘 다 선택합니다.
1. **저장** 을 선택합니다.

#### <a name="applications-legacy"></a>[애플리케이션(레거시)](#tab/applications-legacy/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **애플리케이션(레거시)** 을 선택한 다음, **추가** 를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapi1* 과 같습니다.
1. **웹앱/웹 API 포함** 및 **암시적 흐름 허용** 에 **예** 를 선택합니다.
1. **회신 URL** 에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 프로덕션 애플리케이션에서 회신 URL을 `https://localhost:44332`와 같은 값으로 설정할 수 있습니다. 테스트를 위해 회신 URL을 `https://jwt.ms`로 설정합니다.
1. **앱 ID URI** 에 웹 API에 사용하는 식별자를 입력합니다. 도메인을 포함하는 전체 식별자 URI가 생성됩니다. 예들 들어 `https://contosotenant.onmicrosoft.com/api`입니다.
1. **만들기** 를 클릭합니다.
1. 속성 페이지에서 웹 애플리케이션을 구성할 때 사용할 애플리케이션 ID를 기록합니다.

* * *

## <a name="configure-scopes"></a>범위 구성

범위는 보호된 리소스에 대한 액세스를 제어하는 방법을 제공합니다. 범위는 웹 API에서 범위 기반 액세스 제어를 구현하는 데 사용됩니다. 예를 들어 웹 API 사용자가 읽기 및 쓰기 액세스 권한을 모두 갖고 있을 수도 있고, 읽기 권한만 갖고 있을 수도 있습니다. 이 자습서에서는 범위를 사용하여 웹 API에 대한 읽기 및 쓰기 권한을 정의합니다.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>권한 부여

애플리케이션에서 보호된 웹 API를 호출하려면 애플리케이션 사용 권한을 API에 부여해야 합니다. 예를 들어 [자습서: Azure Active Directory B2C에서 애플리케이션 등록](tutorial-register-applications.md)에서 *webapp1* 이라는 웹 애플리케이션이 Azure AD B2C에 등록됩니다. 이 애플리케이션을 사용하여 웹 API를 호출할 수 있습니다.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

애플리케이션이 보호된 웹 API를 호출하도록 등록됩니다. 사용자가 Azure AD B2C로 인증하여 애플리케이션을 사용합니다. 애플리케이션은 Azure AD B2C에서 권한을 부여받아 보호된 웹 API에 액세스합니다.