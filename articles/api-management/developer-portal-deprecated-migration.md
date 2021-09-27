---
title: 레거시 개발자 포털에서 새 개발자 포털로 마이그레이션
titleSuffix: Azure API Management
description: 레거시 개발자 포털에서 API Management의 새 개발자 포털로 마이그레이션하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: API Management
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: danlep
ms.openlocfilehash: f09f83fdac6de53f59801e33dd9e668b1009136c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642570"
---
# <a name="migrate-to-the-new-developer-portal"></a>새 개발자 포털로 마이그레이션

이 문서에서는 더 이상 사용되지 않는 레거시 포털에서 API Management의 새 개발자 포털로 마이그레이션하기 위해 수행해야 하는 단계를 설명합니다.

> [!IMPORTANT]
> 이전 개발자 포털은 더 이상 사용되지 않으며 보안 업데이트만 수신합니다. 모든 API Management 서비스에서 제거되는 2023년 10월 만료될 때까지 평상시와 같이 계속 사용할 수 있습니다.

![API Management 개발자 포털](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>새 개발자 포털의 향상된 기능

새 개발자 포털은 더 이상 사용되지 않는 포털의 많은 제한 사항을 해결합니다. 새 포털은 [콘텐츠 편집을 위한 시각적 끌어서 놓기 편집기](api-management-howto-developer-portal-customize.md)와 디자이너가 웹 사이트에 스타일을 적용할 수 있는 전용 패널이 특징입니다. 페이지, 사용자 지정, 구성은 API Management 서비스에 Azure Resource Manager 리소스로 저장되므로 [포털 배포를 자동화할](automate-portal-deployments.md)수 있습니다. 마지막으로, 포털의 코드베이스가 오픈 소스이므로 [사용자 지정 기능을 사용하여 확장할 수](api-management-howto-developer-portal.md#managed-vs-self-hosted) 있습니다.

## <a name="how-to-migrate-to-new-developer-portal"></a>새 개발자 포털로 마이그레이션하는 방법

새 개발자 포털은 더 이상 사용되지 않는 포털과 호환되지 않으며 자동으로 마이그레이션되지 않습니다. 콘텐츠(페이지, 텍스트, 미디어 파일)를 수동으로 다시 만들고 새 포털의 모양을 사용자 지정해야 합니다. 정확한 단계는 포털의 사용자 지정 및 복잡성에 따라 달라집니다. 지침은 [개발자 포털 자습서](api-management-howto-developer-portal-customize.md)를 참조하세요. API, 제품, 사용자, ID 공급자의 목록과 같은 나머지 구성은 두 포털에서 자동으로 공유됩니다.

> [!IMPORTANT]
> 이전에 새 개발자 포털을 실행했지만 아무 것도 변경하지 않은 경우 기본 콘텐츠를 다시 설정하여 최신 버전으로 업데이트합니다.

사용되지 않는 포털에서 마이그레이션하는 경우 다음 변경 사항을 염두에 두어야 합니다.

- 사용자 지정 도메인을 통해 개발자 포털을 노출하는 경우 새 개발자 포털에 [도메인을 할당](configure-custom-domain.md)합니다. Azure Portal의 드롭다운에서 **개발자 포털** 옵션을 사용합니다.
- API에 [CORS 정책을 적용](developer-portal-faq.md#cors)하여 대화형 테스트 콘솔을 사용하도록 설정합니다.
- 사용자 지정 CSS를 삽입하여 포털의 스타일을 지정하는 경우 [기본 제공 디자인 패널을 사용하여 스타일을 복제](api-management-howto-developer-portal-customize.md)해야 합니다. 새 포털에서는 CSS 삽입이 허용되지 않습니다.
- 사용자 지정 JavaScript는 [새 포털의 자체 호스트 버전](api-management-howto-developer-portal.md#managed-vs-self-hosted)에서만 삽입할 수 있습니다.
- API Management 가상 네트워크에 있고 Application Gateway를 통해 인터넷에 노출되는 경우 정확한 구성 단계는 [이 설명서 문서를 참조](api-management-howto-integrate-internal-vnet-appgateway.md). 그러려면 다음 작업을 수행해야 합니다.

    - API Management의 관리 엔드포인트에 대한 연결을 사용하도록 설정합니다.
    - 새 포털 엔드포인트에 대한 연결을 사용하도록 설정합니다.
    - 선택한 Web Application Firewall 규칙을 사용하지 않도록 설정합니다.

- 명시적으로 정의된 사용되지 않는 포털 URL을 포함하도록 기본 메일 알림 템플릿을 변경한 경우 포털 URL 매개 변수를 사용하거나 새 포털 URL을 가리키도록 변경합니다. 템플릿에서 기본 제공 포털 URL 매개 변수를 대신 사용하는 경우에는 변경이 필요하지 않습니다.
- 새 개발자 포털에서는 *문제* 및 *애플리케이션* 이 지원되지 않습니다.
- 새 개발자 포털에서는 ID 공급자로 Facebook, Microsoft, Twitter 및 Google과의 직접 통합을 지원하지 않습니다. Azure AD B2C를 통해 이러한 공급자와 통합할 수 있습니다.
- 위임을 사용하는 경우 애플리케이션에서 반환 URL을 변경하고 *SSO URL 생성* 엔드포인트 대신 [ *공유 액세스 토큰 가져오기* API 엔드포인트](/rest/api/apimanagement/2020-12-01/user/get-shared-access-token)를 사용합니다.
- Azure AD를 ID 공급자로 사용하는 경우:

    - 새 개발자 포털 도메인을 가리키도록 애플리케이션의 반환 URL을 변경합니다.
    - 애플리케이션의 반환 URL 접미사를`/signin-aad`에서 `/signin`으로 수정합니다.

- Azure AD B2C을 ID 공급자로 사용하는 경우:

    - 새 개발자 포털 도메인을 가리키도록 애플리케이션의 반환 URL을 변경합니다.
    - 애플리케이션의 반환 URL 접미사를`/signin-aad`에서 `/signin`으로 수정합니다.
    - 애플리케이션 클레임에 *지정된 이름*, *성*, *사용자의 개체 ID* 를 포함합니다.

- 대화형 테스트 콘솔에서 OAuth 2.0를 사용하는 경우 새 개발자 포털 도메인을 가리키도록 애플리케이션의 반환 URL을 변경하고 다음과 같이 접미사를 수정합니다.

    - 인증 코드 권한 부여 흐름의 경우 `/docs/services/[serverName]/console/oauth2/authorizationcode/callback`에서 `/signin-oauth/code/callback/[serverName]`으로
    - 암시적 허용 흐름의 경우 `/docs/services/[serverName]/console/oauth2/implicit/callback`에서 `/signin-oauth/implicit/callback`으로
- 대화형 테스트 콘솔에서 OpenID Connect를 사용하는 경우 새 개발자 포털 도메인을 가리키도록 애플리케이션의 반환 URL을 변경하고 접미사를 수정합니다.

    - 인증 코드 권한 부여 흐름의 경우 `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback`에서 `/signin-oauth/code/callback/[serverName]`으로
    - 암시적 허용 흐름의 경우 `/docs/services/[serverName]/console/openidconnect/implicit/callback`에서 `/signin-oauth/implicit/callback`으로

## <a name="next-steps"></a>다음 단계

다음 문서를 통해 개발자 포털에 대해 자세히 알아봅니다.

- [Azure API Management 개발자 포털 개요](api-management-howto-developer-portal.md)
- [개발자 포털 액세스 및 사용자 지정](api-management-howto-developer-portal-customize.md)
