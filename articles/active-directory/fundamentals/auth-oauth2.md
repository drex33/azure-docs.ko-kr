---
title: Azure Active Directory로 OAUTH 2.0 인증
description: Azure Active Directory를 사용하여 OAUTH 2.0 인증을 구현하는 아키텍처 지침입니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bb7b35dc4a0e41278fcadd8ed487a7da0f00fc4
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045959"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>Azure Active Directory로 OAuth 2.0 인증

OAuth 2.0은 권한 부여를 위한 업계 프로토콜입니다. 이를 통해 사용자는 보호된 리소스에 대한 제한된 액세스 권한을 부여할 수 있습니다. HTTP(Hypertext Transfer Protocol)에서 특별히 작동하도록 설계된 OAuth는 리소스 소유자와 클라이언트의 역할을 구분합니다. 클라이언트는 리소스 소유자가 제어하고 리소스 서버에서 호스트하는 리소스에 대한 액세스를 요청합니다. 리소스 서버는 리소스 소유자의 승인을 받아 액세스 토큰을 발행합니다. 클라이언트는 액세스 토큰을 사용하여 리소스 서버에서 호스트하는 보호된 리소스에 액세스합니다. 

OAuth 2.0은 OIDC(OpenID Connect)와 직접적으로 관련이 있습니다. OIDC는 OAuth 2.0을 기반으로 구축된 인증 및 권한 부여 계층이므로 이전 버전인 OAuth 1.0과 호환되지 않습니다. Azure AD(Azure Active Directory)는 모든 OAuth 2.0 흐름을 지원합니다. 

## <a name="use-for"></a>다음과 같은 경우에 사용됩니다.

풍부한 클라이언트 및 최신 앱 시나리오와 RESTful 웹 API 액세스.

![아키텍처 다이어그램](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>시스템 구성 요소

* **사용자**: 웹 애플리케이션(앱)에서 서비스를 요청합니다. 사용자는 일반적으로 데이터를 소유하고 클라이언트가 데이터 또는 리소스에 액세스할 수 있도록 허용하는 권한이 있는 리소스 소유자입니다. 

* **웹 브라우저**: 사용자가 상호작용하는 웹 브라우저는 OAuth 클라이언트입니다. 

* **웹앱**: 웹앱 또는 리소스 서버는 리소스 또는 데이터가 상주하는 위치입니다. OAuth 클라이언트를 안전하게 인증하고 권한을 부여하는 권한 부여 서버를 신뢰합니다. 

* **Azure AD**: Azure AD는 IdP(ID 공급자)라고도 하는 권한 부여 서버입니다. Azure AD는 사용자 정보, 액세스 및 트러스트 관계와 관련 있는 모든 것을 안전하게 처리합니다. 리소스에 대한 액세스 권한을 부여하고 취소하는 토큰을 발급합니다.

## <a name="implement-oauth-20-with-azure-ad"></a>Azure AD를 사용하여 OAuth 2.0 구현

* [Azure AD와 애플리케이션 통합](../saas-apps/tutorial-list.md) 

* [Microsoft ID 플랫폼의 OAuth 2.0 및 OpenID Connect 프로토콜](../develop/active-directory-v2-protocols.md) 

* [애플리케이션 유형 및 OAuth2](../develop/v2-app-types.md) 

