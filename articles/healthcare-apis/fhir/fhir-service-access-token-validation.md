---
title: FHIR 서비스 액세스 토큰 유효성 검사
description: 액세스 토큰 유효성 검사 절차 및 FHIR 서비스에 대 한 문제 해결 가이드
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: cavoeg
ms.openlocfilehash: 557cbb92f643e553c1dae0768e2891abf7327f03
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779420"
---
# <a name="fhir-service-access-token-validation"></a>FHIR 서비스 액세스 토큰 유효성 검사

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

Azure 의료 Api의 FHIR 서비스 (FHIR 서비스 라고 함)는 액세스 토큰의 유효성을 검사 하 고 구현 및 구성에 따라 결정 됩니다. 이 문서에서는 액세스 문제를 해결할 때 도움이 될 수 있는 유효성 검사 단계를 안내 합니다.

## <a name="validate-token-has-no-issues-with-identity-provider"></a>토큰 유효성 검사에 id 공급자 문제가 없습니다.

토큰 유효성 검사의 첫 번째 단계는 토큰이 올바른 id 공급자에 의해 발급 되었으며 수정 되지 않았는지 확인 하는 것입니다. FHIR 서버는 인증 기관 이라는 특정 id 공급자를 사용 하도록 구성 됩니다 `Authority` . FHIR 서버는 끝점에서 id 공급자에 대 한 정보를 검색 합니다 `/.well-known/openid-configuration` . Azure AD를 사용 하는 경우 전체 URL은 다음과 같습니다.

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

여기서 `<TENANT-ID>` 는 특정 AZURE AD 테 넌 트 (테 넌 트 ID 또는 도메인 이름)입니다.

Azure AD는 아래와 같은 문서를 FHIR 서버에 반환 합니다.

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
FHIR 서버에 대 한 중요 한 속성은 `jwks_uri` 로, 토큰 서명의 유효성을 검사 하는 데 필요한 암호화 키를 가져올 위치를 서버에 알려 주며 `issuer` , `iss` 이 서버에서 발급 한 토큰의 발급자 클레임 ()에 있는 것을 서버에 알려 줍니다. FHIR 서버는이를 사용 하 여 인증 된 토큰이 수신 되는지 확인할 수 있습니다.

## <a name="validate-claims-of-the-token"></a>토큰 클레임의 유효성을 검사 합니다.

서버가 토큰의 신뢰성을 확인 하면 FHIR 서버는 클라이언트에 토큰에 액세스 하는 데 필요한 클레임이 있는지 확인 하는 과정을 진행 합니다.

FHIR 서비스를 사용 하는 경우 서버는 다음의 유효성을 검사 합니다.

1. 토큰에 올바른 `Audience` ( `aud` 클레임)가 있습니다.
1. 토큰이 발급 된 사용자 또는 보안 주체가 FHIR 서버 데이터 평면에 액세스할 수 있습니다. `oid`토큰 클레임은 사용자 또는 보안 주체를 고유 하 게 식별 하는 id 개체 id를 포함 합니다.

Azure RBAC를 사용 하 여 데이터 평면 역할 할당을 관리 하도록 FHIR 서비스를 구성 하는 것이 좋습니다. 하지만 fhir 서비스가 외부 또는 보조 Azure Active Directory 테 넌 트를 사용 하는 경우 로컬 RBAC를 구성할 수도 있습니다. 

Azure 용 OSS Microsoft FHIR 서버를 사용 하는 경우 서버는 다음의 유효성을 검사 합니다.

1. 토큰에 올바른 `Audience` ( `aud` 클레임)가 있습니다.
1. 토큰에는 `roles` FHIR 서버에 대 한 액세스가 허용 된 클레임의 역할이 있습니다.

[FHIR 서버에서 역할을 정의](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md)하는 방법에 대 한 세부 정보를 참조 하세요.

또한 FHIR 서버는 액세스 토큰이 `scp` 클라이언트에서 액세스 하려고 하는 FHIR API의 일부에 액세스할 수 있는 범위 (토큰 클레임)가 있는지를 확인할 수 있습니다. 현재 FHIR 서비스는 토큰 범위의 유효성을 검사 하지 않습니다.