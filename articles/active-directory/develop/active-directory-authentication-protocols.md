---
title: Microsoft ID 플랫폼 인증 프로토콜
description: Microsoft ID 플랫폼에서 지원하는 인증 프로토콜에 대한 개요
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: d489dbad512af238601636e9f0c4f144f9b2216d
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129153941"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Microsoft ID 플랫폼 인증 프로토콜

Microsoft ID 플랫폼은 가장 널리 사용되는 다수의 인증 및 권한 부여 프로토콜을 지원합니다. 이 섹션의 항목에서는 Microsoft ID 플랫폼에서 지원하는 프로토콜과 해당 구현에 대해 설명합니다. 이 항목에서는 지원되는 클레임 유형을 검토하고, 페더레이션 메타데이터 사용에 대해 소개하고, 자세한 OAuth 2.0. 및 SAML 2.0 프로토콜 참조 설명서 및 문제 해결 섹션을 소개합니다.

## <a name="authentication-protocols-articles-and-reference"></a>인증 프로토콜 기사 및 참조

* [Microsoft ID 플랫폼의 서명 키 롤오버에 대한 중요한 정보](active-directory-signing-key-rollover.md) - Microsoft ID 플랫폼의 서명 키 롤오버 주기, 키를 자동으로 업데이트하기 위해 수행할 수 있는 변경 사항, 가장 일반적인 애플리케이션 시나리오를 업데이트하는 방법에 대해 알아봅니다.
* [지원되는 토큰 및 클레임 유형](id-tokens.md) - Microsoft ID 플랫폼에서 발급되는 토큰의 클레임에 대해 알아봅니다.
* [Microsoft ID 플랫폼의 OAuth 2.0](v2-oauth2-auth-code-flow.md) - Microsoft ID 플랫폼의 OAuth 2.0 구현에 대해 알아봅니다.
* [OpenID Connect 1.0](v2-protocols-oidc.md) - 권한 부여 프로토콜 OAuth 2.0을 인증에 사용하는 방법을 알아봅니다.
* [클라이언트 자격 증명을 사용한 서비스 간 호출](v2-oauth2-client-creds-grant-flow.md) - 서비스 간 호출을 위해 OAuth 2.0 클라이언트 자격 증명 부여 흐름을 사용하는 방법을 알아봅니다.
* [On-Behalf-Of 흐름을 사용한 서비스 간 호출](v2-oauth2-on-behalf-of-flow.md) - 서비스 간 호출을 위해 OAuth 2.0 On-Behalf-Of 흐름을 사용하는 방법을 알아봅니다.
* [SAML 프로토콜 참조](active-directory-saml-protocol-reference.md) - Microsoft ID 플랫폼의 Single Sign-On 및 Single Sign-out SAML 프로필에 대해 알아봅니다.

## <a name="see-also"></a>참고 항목

* [Microsoft ID 플랫폼 개요](v2-overview.md)
* [Active Directory 코드 샘플](sample-v2-code.md)
