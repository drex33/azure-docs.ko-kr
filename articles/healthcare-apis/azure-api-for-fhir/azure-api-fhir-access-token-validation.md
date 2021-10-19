---
title: Azure API for FHIR 액세스 토큰 유효성 검사
description: 토큰 유효성 검사를 안내하고 액세스 문제를 해결하는 방법에 대한 팁을 제공합니다.
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: cavoeg
ms.openlocfilehash: c823e74fa76b1b0d8d2b9974305e7f7df44c57a9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122436220"
---
# <a name="azure-api-for-fhir-access-token-validation"></a>Azure API for FHIR 액세스 토큰 유효성 검사

Azure API for FHIR 액세스 토큰의 유효성을 검사하는 방법은 구현 및 구성에 따라 달라집니다. 이 문서에서는 액세스 문제를 해결할 때 유용할 수 있는 유효성 검사 단계를 안내합니다.

## <a name="validate-token-has-no-issues-with-identity-provider"></a>ID 공급자에 문제가 없는 토큰 유효성 검사

토큰 유효성 검사의 첫 번째 단계는 토큰이 올바른 ID 공급자에 의해 발급되었고 수정되지 않았는지 확인하는 것입니다. FHIR 서버는 기관이라고 하는 특정 ID 공급자를 사용하도록 `Authority` 구성됩니다. FHIR 서버는 엔드포인트에서 ID 공급자에 대한 정보를 `/.well-known/openid-configuration` 검색합니다. Azure AD를 사용하는 경우 전체 URL은 다음과 같습니다.

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

여기서 `<TENANT-ID>` 은 특정 Azure AD 테넌트(테넌트 ID 또는 도메인 이름)입니다.

Azure AD는 아래와 같은 문서를 FHIR 서버에 반환합니다.

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
FHIR 서버의 중요한 속성은 토큰 `jwks_uri` 서명의 유효성을 검사하는 데 필요한 암호화 키를 가져올 위치를 서버에 알려주는 이며, 이 서버는 `issuer` `iss` 이 서버에서 발급한 토큰의 발급자 클레임( )에 무엇이 있는지 서버에 알릴 수 있습니다. FHIR 서버는 이를 사용하여 인증 토큰을 수신하고 있는지 확인할 수 있습니다.

## <a name="validate-claims-of-the-token"></a>토큰 클레임 유효성 검사

서버가 토큰의 신뢰성을 확인하면 FHIR 서버는 클라이언트에 토큰에 액세스하는 데 필요한 클레임이 있는지 계속 확인합니다.

Azure API for FHIR 사용하는 경우 서버는 다음의 유효성을 검사합니다.

1. 토큰에는 `Audience` 권한( `aud` 클레임)이 있습니다.
1. 토큰이 발급된 사용자 또는 보안 주체가 FHIR 서버 데이터 평면에 액세스할 수 있습니다. `oid`토큰의 클레임에는 사용자 또는 보안 주체를 고유하게 식별하는 ID 개체 ID가 포함됩니다.

[Azure RBAC를 사용하여](configure-azure-rbac.md) 데이터 평면 역할 할당을 관리하도록 FHIR 서비스를 구성하는 것이 좋습니다. 그러나 FHIR 서비스가 외부 또는 보조 Azure Active Directory 테넌트에서 사용하는 경우 [로컬 RBAC를 구성할](configure-local-rbac.md) 수도 있습니다. 

Azure용 OSS Microsoft FHIR 서버를 사용하는 경우 서버는 다음의 유효성을 검사합니다.

1. 토큰에는 `Audience` 권한( `aud` 클레임)이 있습니다.
1. 토큰에는 `roles` FHIR 서버에 대한 액세스를 허용하는 클레임의 역할이 있습니다.

[FHIR 서버에서 역할을 정의하는](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md)방법에 대한 세부 정보를 참조하세요.

또한 FHIR 서버는 `scp` 클라이언트가 액세스하려는 FHIR API의 부분에 액세스하기 위해 액세스 토큰에 범위(토큰 클레임)가 있는지 확인할 수 있습니다. 현재 azure용 Azure API for FHIR 및 FHIR 서버는 토큰 범위의 유효성을 검사하지 않습니다.

## <a name="next-steps"></a>다음 단계
이제 토큰 유효성 검사를 안내하는 방법을 알아보셨으므로 자습서를 완료하여 JavaScript 애플리케이션을 만들고 FHIR 데이터를 읽을 수 있습니다.

>[!div class="nextstepaction"]
>[웹 애플리케이션 자습서](tutorial-web-app-fhir-server.md)