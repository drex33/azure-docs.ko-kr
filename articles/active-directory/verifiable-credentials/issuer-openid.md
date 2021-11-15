---
title: 발급자 서비스 통신 예(미리 보기) - Azure Active Directory 확인 가능한 자격 증명
description: ID 공급자와 발급자 서비스 사이의 통신 세부 정보
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 6420825d8ed758658db0e652dc2b88fea91ce908
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994787"
---
# <a name="issuer-service-communication-examples-preview"></a>발급자 서비스 통신 예(미리 보기)

Azure AD 확인 가능한 자격 증명 서비스는 조직의 OpenID 준수 ID 공급자가 생성한 ID 토큰으로부터 클레임을 검색하여 확인 가능한 자격 증명을 발급할 수 있습니다. 이 문서에서는 인증자가 통신하고 올바른 ID 토큰을 검색하여 발급 서비스에 전달할 수 있도록 ID 공급자를 설정하는 방법을 설명합니다. 

> [!IMPORTANT]
> Azure Active Directory 확인 가능한 자격 증명은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


확인 가능한 자격 증명을 발급하려면 인증자는 계약서 다운로드를 통해 사용자로부터 입력 정보를 수집하고 해당 정보를 발급 서비스로 보내야 합니다. ID 토큰을 사용해야 하는 경우 인증자가 Openid Connect 프로토콜을 사용하여 사용자 로그인이 가능하도록 ID 공급자를 설정해야 합니다. 결과 ID 토큰의 클레임은 확인 가능한 자격 증명의 콘텐츠를 채우는 데 사용됩니다. 인증자는 Openid Connect 권한 부여 코드 흐름을 사용하여 사용자를 인증합니다. OpenID 공급자는 다음과 같은 Openid Connect 기능을 지원해야 합니다. 

| 기능 | Description |
| ------- | ----------- |
| 권한 부여 유형 | 인증 코드 권한 부여 유형을 지원해야 합니다. |
| 토큰 형식 | 암호화되지 않은 컴팩트 JWT를 생성해야 합니다. |
| 서명 알고리즘 | RS 256를 사용하여 서명된 JWT를 생성해야 합니다. |
| 구성 문서 | OpenID Connect 구성 문서 및 `jwks_uri`를 지원해야 합니다. | 
| 클라이언트 등록 | `vcclient://openid/`의 `redirect_uri` 값을 사용하는 공용 클라이언트 등록을 지원해야 합니다. | 
| PKCE | 보안상의 이유로 권장되지만 반드시 필요한 것은 아닙니다. |

ID 공급자로 전송되는 HTTP 요청의 예는 아래에 포함되어 있습니다. ID 공급자는 Openid Connect 인증 표준에 따라 이러한 요청을 수락하고 이 요청에 응답해야 합니다.

## <a name="client-registration"></a>클라이언트 등록

확인 가능한 자격 증명을 받으려면 사용자가 Microsoft Authenticator 앱에서 IDP에 로그인해야 합니다. 

이 교환을 사용하도록 설정하려면 ID 공급자에 애플리케이션을 등록합니다. Azure AD를 사용하는 경우 [여기](../develop/quickstart-register-app.md)에서 지침을 확인할 수 있습니다. 등록할 때 다음 값을 사용합니다.

| 설정 | 값 |
| ------- | ----- |
| 애플리케이션 이름 | `<Issuer Name> Verifiable Credential Service` |
| 리디렉션 URI | `vcclient://openid/ ` |


ID 공급자에 애플리케이션을 등록한 후에는 클라이언트 ID를 기록합니다. 다음 섹션에서 이를 사용합니다. 또한 OIDC 호환 ID 공급자에 대해 잘 알려진 엔드포인트에 대한 URL을 기록해야 합니다. 발급 서비스는 이 엔드포인트를 사용하여 인증자에 의해 전송된 ID 토큰의 유효성을 검사하는 데 필요한 공개 키를 다운로드합니다.

구성된 리디렉션 URI는 인증자가 사용하므로 로그인이 완료되고 ID 토큰을 검색할 수 있는 시기를 알 수 있습니다. 

## <a name="authorization-request"></a>권한 부여 요청

ID 공급자로 전송된 권한 부여 요청은 다음 형식을 사용합니다.

```HTTP
GET /authorize?client_id=<client-id>&redirect_uri=portableidentity%3A%2F%2Fverify&response_mode=query&response_type=code&scope=openid&state=12345&nonce=12345 HTTP/1.1
Host: www.contoso.com
Connection: Keep-Alive
```

| 매개 변수 | 값 |
| ------- | ----------- |
| `client_id` | 애플리케이션 등록 프로세스 중에 얻은 클라이언트 ID입니다. |
| `redirect_uri` | `vcclient://openid/`를 사용해야 합니다. |
| `response_mode` | `query`를 지원해야 합니다. |
| `response_type` | `code`를 지원해야 합니다. |
| `scope` | `openid`를 지원해야 합니다. |
| `state` | OpenID Connect 표준에 따라 클라이언트에 반환되어야 합니다. |
| `nonce` | OpenID Connect 표준에 따라 ID 토큰의 클레임으로 반환되어야 합니다. |

권한 부여 요청을 수신하는 경우 ID 공급자는 사용자를 인증하고 다단계 인증과 같은 로그인 완료에 필요한 모든 단계를 수행해야 합니다.

요구 사항에 맞게 로그인 프로세스를 사용자 지정할 수 있습니다. 사용자에게 추가 정보 제공, 서비스 약관 동의, 자격 증명에 대한 요금 지불 등을 요청할 수 있습니다. 모든 단계가 완료되면 아래와 같이 리디렉션 URI로 리디렉션하여 권한 부여 요청에 응답합니다. 

```HTTP
vcclient://openid/?code=nbafhjbh1ub1yhbj1h4jr1&state=12345
```

| 매개 변수 | 값 |
| ------- | ----------- |
| `code` |  ID 공급자가 반환하는 인증 코드입니다. |
| `state` | OpenID Connect 표준에 따라 클라이언트에 반환되어야 합니다. |

## <a name="token-request"></a>토큰 요청

ID 공급자에게 전송된 토큰 요청은 다음 형식을 갖습니다.

```HTTP
POST /token HTTP/1.1
Host: www.contoso.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 291

client_id=<client-id>&redirect_uri=vcclient%3A%2F%2Fopenid%2F&grant_type=authorization_code&code=nbafhjbh1ub1yhbj1h4jr1&scope=openid
```

| 매개 변수 | 값 |
| ------- | ----------- |
| `client_id` | 애플리케이션 등록 프로세스 중에 얻은 클라이언트 ID입니다. |
| `redirect_uri` | `vcclient://openid/`를 사용해야 합니다. |
| `scope` | `openid`를 지원해야 합니다. |
| `grant_type` | `authorization_code`를 지원해야 합니다. |
| `code` | ID 공급자가 반환하는 인증 코드입니다. |

토큰 요청을 받으면 ID 공급자가 ID 토큰으로 응답해야 합니다.

```HTTP
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
"id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.ewogImlzc
    yI6ICJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwKICJzdWIiOiAiMjQ4Mjg5
    NzYxMDAxIiwKICJhdWQiOiAiczZCaGRSa3F0MyIsCiAibm9uY2UiOiAibi0wUzZ
    fV3pBMk1qIiwKICJleHAiOiAxMzExMjgxOTcwLAogImlhdCI6IDEzMTEyODA5Nz
    AKfQ.ggW8hZ1EuVLuxNuuIJKX_V8a_OMXzR0EHR9R6jgdqrOOF4daGU96Sr_P6q
    Jp6IcmD3HP99Obi1PRs-cwh3LO-p146waJ8IhehcwL7F09JdijmBqkvPeB2T9CJ
    NqeGpe-gccMg4vfKjkM8FcGvnzZUN4_KSP0aAp1tOJ1zZwgjxqGByKHiOtX7Tpd
    QyHE5lcMiKPXfEIQILVq0pc_E2DzL7emopWoaoZTF_m0_N0YzFC6g6EJbOEoRoS
    K5hoDalrcvRYLSrQAZZKflyuVCyixEoV9GfNQC3_osjzw2PAithfubEEBLuVVk4
    XUVrWOLrLl0nx7RkKU8NXNHq-rvKMzqg"
}
```

ID 토큰은 JWT 컴팩트 직렬화 형식을 사용해야 하며, 암호화되지 않아야 합니다. ID 토큰은 다음 클레임을 포함해야 합니다.

| 클레임 | 값 |
| ------- | ----------- |
| `kid` | ID 토큰 서명에 사용되는 키의 키 ID로, OpenID 공급자의 `jwks_uri` 항목에 해당합니다. |
| `aud` | 애플리케이션 등록 프로세스 중에 얻은 클라이언트 ID입니다. |
| `iss` | OpenID Connect 구성 문서의 `issuer` 값이어야 합니다. |
| `exp` | ID 토큰의 만료 시간을 포함해야 합니다. |
| `iat` | ID 토큰이 발급된 시간을 포함해야 합니다. |
| `nonce` | 권한 부여 요청에 포함된 값입니다. |
| 추가 클레임 | ID 토큰에는 추가 클레임이 포함되어야 하고, 그 값은 발급될 확인 가능한 자격 증명에 포함됩니다. 이 섹션에서는 이름 등 사용자에 대한 특성을 포함해야 합니다. |

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 확인 가능한 자격 증명을 사용자 지정하는 방법](credential-design.md)
