---
title: Azure Active Directory B2C의 인증 프로토콜
description: Azure Active Directory B2C에서 지원하는 프로토콜을 사용하여 앱을 직접 빌드하는 방법입니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: c01602182972c375843fde5a1720dcff2cc6d341
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130035641"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: 인증 프로토콜
Azure AD B2C(Azure Active Directory B2C)는 두 개의 업계 표준 프로토콜인 OpenID Connect 및 OAuth 2.0을 지원하여 앱에 대한 Identity-as-a-Service를 제공합니다. 서비스는 표준을 준수하지만 이러한 프로토콜의 두 구현에는 약간의 차이가 있을 수 있습니다.

이 가이드의 정보는 오픈 소스 라이브러리를 사용하지 않고 HTTP 요청을 직접 전송하고 처리하여 코드를 작성하는 경우에 유용합니다. 각 특정 프로토콜의 세부 정보를 자세히 살펴보기 전에 이 페이지를 참조하는 것이 좋습니다. Azure AD B2C에 대해 이미 잘 알고 있는 경우 [프로토콜 참조 가이드](#protocols)로 바로 이동해도 됩니다.

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>기본 사항
Azure AD B2C를 사용하는 모든 앱은 [Azure 포털](https://portal.azure.com)의 B2C 디렉터리에 등록해야 합니다. 앱 등록 프로세스는 몇 개의 값을 수집하고 앱에 할당합니다.

* 앱을 고유하게 식별하는 **애플리케이션 ID**
* 응답을 다시 앱으로 보내는 데 사용할 수 있는 **리디렉션 URI** 또는 **패키지 식별자**.
* 다른 몇 가지 시나리오 관련 값. 자세한 내용은 [애플리케이션을 등록하는 방법](tutorial-register-applications.md)을 참조하세요.

앱을 등록하면 앱이 엔드포인트에 요청을 전송하여 Azure AD(Azure Active Directory)와 통신합니다.

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

거의 모든 OAuth 및 OpenID Connect 흐름에서 다음과 같이 네 주체가 교환에 포함됩니다.




:::image type="content" source="./media/protocols-overview/protocols_roles.png" alt-text="네 가지 OAuth 2.0 역할을 보여주는 다이어그램.":::

* **권한 부여 서버** 는 Azure AD 엔드포인트입니다. 사용자 정보 및 액세스와 관련된 모든 것을 안전하게 처리합니다. 또한 흐름에서 당사자 간의 트러스트 관계를 처리합니다. 사용자 ID를 확인하고 리소스에 대한 액세스 권한을 부여 및 해지하고, 토큰을 발급합니다. ID 공급자라고도 합니다.

* **리소스 소유자** 는 일반적으로 최종 사용자입니다. 데이터를 소유하는 당사자이며 제3자가 해당 데이터 또는 리소스에 액세스하도록 허용할 권한이 있습니다.

* **OAuth 클라이언트** 는 앱입니다. 애플리케이션 ID로 식별됩니다. 일반적으로 최종 사용자가 상호 작용하는 당사자입니다. 권한 부여 서버에서 토큰도 요청합니다. 리소스 소유자가 리소스에 액세스할 수 있는 클라이언트 권한을 부여해야 합니다.

* **리소스 서버** 는 리소스 또는 데이터가 있는 곳입니다. OAuth 클라이언트를 안전하게 인증하고 권한을 부여하는 권한 부여 서버를 신뢰합니다. 또한 전달자 액세스 토큰을 사용하여 리소스에 액세스 권한이 부여되어야 합니다.

## <a name="policies-and-user-flows"></a>정책 및 사용자 흐름
Azure AD B2C 정책은 분명히 서비스의 가장 중요한 기능입니다. Azure AD B2C에서 정책이 도입되어 표준 OAuth 2.0 및 OpenID Connect 프로토콜이 확장됩니다. 이렇게 하면 Azure AD B2C에서 더 쉽게 인증 및 권한 부여를 수행할 수 있습니다.

가장 일반적인 ID 작업을 설정하는 데 도움을 주기 위해 Azure AD B2C 포털은 **사용자 흐름** 이라는 미리 정의되고 구성 가능한 정책을 포함합니다. 사용자 흐름은 가입, 로그인, 프로필 편집 등의 소비자 ID 경험을 완벽하게 설명합니다. 사용자 흐름은 관리 UI에서 정의될 수 있습니다. HTTP 인증 요청에서 특별한 쿼리 매개 변수를 사용하여 실행될 수 있습니다.

정책 및 사용자 흐름은 OAuth 2.0 및 OpenID Connect의 표준 기능이 아니므로 이해하는 데 시간이 필요합니다. 자세한 내용은 [Azure AD B2C 사용자 흐름 참조 가이드](user-flow-overview.md)를 참조하세요.

## <a name="tokens"></a>토큰
OAuth 2.0 및 OpenID Connect의 Azure AD B2C 구현은 JWT(JSON 웹 토큰)로 표현되는 전달자 토큰을 포함하여 전달자 토큰을 광범위하게 활용합니다. 전달자 토큰은 보호된 리소스에 대한 "전달자" 액세스 권한을 부여하는 간단한 보안 토큰입니다.

전달자는 토큰을 제공할 수 있는 당사자입니다. Azure AD에서 전달자 토큰을 받으려면 먼저 당사자를 인증해야 합니다. 하지만 전송 및 스토리지에 토큰을 보호하는 데 필요한 단계를 수행하지 않으면 의도하지 않은 당사자가 가로채서 사용할 수 있습니다.

일부 보안 토큰에는 권한이 없는 당사자가 토큰을 사용하지 못하게 하는 메커니즘이 기본 제공되지만 전달자 토큰에는 이 메커니즘이 없습니다. 전송 계층 보안(HTTPS) 등의 보안 채널로 전송해야 합니다.

전달자 토큰이 보안 채널 밖으로 전송되는 경우 악의적인 당사자가 메시지 가로채기(man-in-the-middle) 공격으로 토큰을 획득하고 이를 사용하여 보호된 리소스에 무단으로 액세스할 수 있습니다. 나중에 사용하기 위해 전달자 토큰을 저장하거나 캐싱할 때 동일한 보안 원칙이 적용됩니다. 항상 앱이 안전한 방식으로 전달자 토큰을 전송하고 저장하도록 합니다.

전달자 토큰의 보안 고려 사항에 대한 자세한 내용은 [RFC 6750 Section 5](https://tools.ietf.org/html/rfc6750)를 참조하세요.

Azure AD B2C에서 사용되는 다양한 토큰 형식에 대한 자세한 내용은 [Azure AD 토큰 참조](tokens-overview.md)를 참조하세요.

## <a name="protocols"></a>프로토콜
몇 가지 예제 요청을 검토할 준비가 되면 다음 자습서 중 하나를 시작할 수 있습니다. 각각 특정 인증 시나리오에 해당합니다. 사용자에게 맞는 흐름을 결정하는 데 도움이 필요하면 [Azure AD B2C를 사용하여 빌드할 수 있는 앱 형식](application-types.md)을 확인합니다.

* [OAuth 2.0을 사용하여 모바일 및 네이티브 애플리케이션 빌드](authorization-code-flow.md)
* [OpenID Connect를 사용하여 웹앱 빌드](openid-connect.md)
* [OAuth 2.0 암시적 흐름을 사용하여 단일 페이지 앱 구축](implicit-flow-single-page-application.md)

