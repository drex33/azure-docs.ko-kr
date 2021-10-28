---
title: TLS 및 암호화 도구 모음 요구 사항 - Azure AD B2C
titleSuffix: Azure AD B2C
description: 웹 API 엔드포인트와 상호 작용 시 HTTPS 암호화 도구 모음 및 TLS 요구 사항에 대한 개발자용 참고 자료입니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/30/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 353ab6e58e0c8f563a75d2bf7fa48d48a5d1dfc2
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036875"
---
# <a name="azure-active-directory-b2c-tls-and-cipher-suite-requirements"></a>Azure Active Directory B2C TLS 및 암호화 도구 모음 요구 사항

Azure AD B2C(Azure Active Directory B2C)는 [사용자 흐름](user-flow-overview.md) 내 [API 커넥터](api-connectors-overview.md) 및 [ID 공급자](oauth2-technical-profile.md)를 통해 엔드포인트에 연결합니다. 본 문서에서는 엔드포인트에 대한 TLS 및 암호화 도구 모음 요구 사항을 설명합니다.

API 커넥터 및 ID 공급자가 구성된 엔드포인트는 공개적으로 액세스할 수 있는 HTTPS URI에 게시되어야 합니다. 엔드포인트를 사용하여 보안 연결을 설정하기 전에 프로토콜과 암호화는 연결 양측의 기능에 따라 Azure AD B2C와 엔드포인트 간에 조정됩니다.

Azure AD B2C는 본 문서에 설명된 대로 TLS(전송 계층 보안) 및 암호화 도구 모음을 사용하여 엔드포인트에 연결할 수 있어야 합니다.

## <a name="tls-versions"></a>TLS 버전

TLS 버전 1.2는 서버와 클라이언트 간에 인증 및 데이터 암호화를 제공하는 암호화 프로토콜입니다. 엔드포인트는 **TLS 버전 1.2** 를 통해 보안 통신을 지원해야 합니다. 이전 TLS 버전 1.0 및 1.1은 사용되지 않습니다. 

## <a name="cipher-suites"></a>암호 그룹

암호화 도구 모음은 암호화 알고리즘 집합입니다. TLS를 통해 HTTPS 프로토콜을 사용할 때 데이터를 안전하게 통신하는 방법에 대한 필수 정보를 제공합니다.

엔드포인트는 다음 암호화 중 하나 이상을 지원해야 합니다.

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

## <a name="endpoints-in-scope"></a>범위 내 엔드포인트

Azure AD B2C 환경에서 사용되는 다음 엔드포인트는 이 문서에 설명된 요구 사항을 준수해야 합니다.

- [API 커넥터](api-connectors-overview.md) 
- OAuth1
    - 토큰 엔드포인트 
    - 사용자 정보 엔드포인트
- OAuth2 및 OpenId 연결 ID 공급자
    - OpenID Connect 검색 엔드포인트
    - OpenId Connect JWKS 엔드포인트
    - 토큰 엔드포인트 
    - 사용자 정보 엔드포인트
- [ID 토큰 힌트](id-token-hint.md)
    - OpenID Connect 검색 엔드포인트
    - OpenId Connect JWKS 엔드포인트
- [SAML ID 공급자](saml-service-provider.md) 메타데이터 엔드포인트
- [SAML 서비스 공급자](identity-provider-generic-saml.md) 메타데이터 엔드포인트

## <a name="check-your-endpoint-compatibility"></a>엔드포인트 호환성 확인

엔드포인트가 본 문서에 명시된 요구 사항을 준수하는지 확인하려면 TLS 암호화 및 검사기 도구를 사용하여 테스트를 수행합니다. [SSLLABS](https://www.ssllabs.com/ssltest/analyze.html)를 사용하여 엔드포인트를 테스트합니다.


## <a name="next-steps"></a>다음 단계

다음 문서를 참조하세요.

- [TLS 1.2를 지원하지 않는 애플리케이션 문제 해결](../cloud-services/applications-dont-support-tls-1-2.md)
- [TLS/SSL의 암호화 도구 모음(Schannel SSP)](/windows/win32/secauthn/cipher-suites-in-schannel)
- [TLS 1.2를 사용하도록 설정하는 방법](/mem/configmgr/core/plan-design/security/enable-tls-1-2)
- [TLS 1.0 문제 해결](/security/engineering/solving-tls1-problem)