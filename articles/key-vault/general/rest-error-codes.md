---
title: REST API 오류 코드 - Azure Key Vault
description: 이러한 오류 코드는 Azure Key Vault 웹 서비스의 작업에서 반환될 수 있습니다.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: de0aa3f3149141ddacd7906297656d58b8fad24c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634004"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Azure Key Vault REST API 오류 코드
 
다음 오류 코드는 Azure Key Vault 웹 서비스의 작업에서 반환될 수 있습니다.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: 인증되지 않은 요청

401은 요청이 Key Vault에 대해 인증되지 않았음을 의미합니다. 

다음 경우 요청이 인증됩니다.

- Key Vault가 호출자의 ID를 알고 있는 경우
- 호출자가 Key Vault 리소스에 대한 액세스를 시도할 수 있는 경우 

요청이 401을 반환하는 이유에는 여러 가지가 있습니다.

### <a name="no-authentication-token-attached-to-the-request"></a>요청에 연결된 인증 토큰이 없습니다. 

다음은 암호 값을 설정하는 PUT 요청 예제입니다.

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

"인증" 헤더는 data-plane 작업에 대한 Key Vault를 호출할 때마다 필요한 액세스 토큰입니다. 헤더가 누락된 경우 응답은 401이어야 합니다.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>토큰에 연결된 올바른 리소스가 부족합니다. 

Azure OAUTH 엔드포인트에서 액세스 토큰을 요청하는 경우 "resource"라는 매개 변수는 필수입니다. 토큰 공급자는 토큰의 용도에 맞게 토큰의 범위를 사용하므로 이 값은 중요합니다. Key Vault에 액세스하는 **모든** 토큰에 대한 리소스는 *https:\//vault.keyvault.net*(후행 슬래시 없음)입니다.

### <a name="the-token-is-expired"></a>토큰이 만료되었습니다.

토큰은 base64로 인코딩되고 값은 [http://jwt.calebb.net](http://jwt.calebb.net)과 같은 웹 사이트에서 디코딩할 수 있습니다. 다음은 위의 디코딩된 토큰입니다.

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

이 토큰에서 여러 가지 중요한 부분을 확인할 수 있습니다.

- aud(대상 그룹): 토큰의 리소스입니다. `https://vault.azure.net`이 그것입니다. 이 토큰은 이 값과 명시적으로 일치하지 않는 리소스(예: 그래프)에 대해서는 작동하지 않습니다.
- iat(issued at): 토큰이 발급될 때 Epoch가 시작된 이후 발생되는 틱 수입니다.
- nbf(not before): 이 토큰이 유효해질 때 Epoch가 시작된 이후 발생되는 틱 수입니다.
- exp(expiration): 이 토큰이 만료될 때 Epoch가 시작된 이후 발생되는 틱 수입니다.
- appid(application ID):이 요청을 만드는 응용 프로그램 ID의 GUID입니다.
- tid(tenant ID): 이 요청을 만드는 보안 주체 테넌트 ID의 GUID입니다.

요청을 처리하기 위해 토큰에서 모든 값을 올바르게 식별하는 것이 중요합니다. 모든 항목이 올바르면 요청에서 401이 발생하지 않습니다.

### <a name="troubleshooting-401"></a>401 문제 해결

401은 Key Vault에 대한 요청이 만들어지기 전에 토큰 생성 지점에서 조사해야 합니다. 일반적으로 코드를 사용하여 토큰을 요청합니다. 토큰이 수신되면 Key Vault 요청에 전달됩니다. 코드를 로컬로 실행하는 경우 Fiddler를 사용하여 `https://login.microsoftonline.com`에 대한 요청/응답을 캡처할 수 있습니다. 요청은 다음과 같은 형태입니다.

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

다음 사용자 제공 정보가 정확해야 합니다.

- Key Vault 테넌트 ID
- https%3A%2F%2Fvault.azure.net(URL 인코딩)으로 설정된 리소스 값
- 클라이언트 ID
- 클라이언트 암호

나머지 요청도 거의 동일해야 합니다.

응답 액세스 토큰만 가져올 수 있는 경우에는 이 토큰을 디코딩(위에 표시)하여 테넌트 ID, 클라이언트 ID(앱 ID) 및 리소스를 확인할 수 있습니다.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: 권한 부족

HTTP 403은 요청이 인증되었지만(요청 ID를 알고 있음), 이 ID에는 요청된 리소스에 대한 액세스 권한이 없음을 의미합니다. 다음 두 가지 원인이 있습니다.

- 이 ID에 대한 액세스 정책이 없습니다.
- 요청하는 리소스의 IP 주소가 Key Vault의 방화벽 설정에서 승인되지 않습니다.

HTTP 403은 고객의 응용 프로그램이 고객이 생각하는 클라이언트 ID를 사용하지 않을 때 발생하는 경우가 많습니다. 이는 일반적으로 실제 호출 ID에 대해 액세스 정책이 올바르게 설정되지 않았음을 의미합니다.

액세스 정책에 id를 추가한 후 즉시 403 오류가 표시 되 면 주기적으로 재시도를 추가 하 여 처리할 수 있습니다.

### <a name="troubleshooting-403"></a>403 문제 해결

먼저 로깅을 켭니다. 그렇게 수행하는 방법에 대한 지침은 [Azure Key Vault 로깅](logging.md)을 참조하세요.

로깅을 활성화하면 403이 액세스 정책으로 인한 오류인지 아니면 방화벽 정책으로 인한 오류인지 확인할 수 있습니다.

#### <a name="error-due-to-firewall-policy"></a>방화벽 정책으로 인한 오류

"클라이언트 주소(00.00.00.00)가 인증되지 않았고 호출자는 신뢰할 수 있는 서비스가 아닙니다."

제한된 목록의 "Azure Trusted Services"가 있습니다. Azure 웹 사이트가 신뢰할 수 있는 Azure 서비스가 **아닙니다**. 자세한 내용은 블로그 게시물 [Key Vault Firewall access by Azure App Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services)(Azure App Services에서 Key Vault 방화벽 액세스)를 참조하세요.

Azure 웹 사이트가 작동하려면 해당 웹 사이트의 IP 주소를 Key Vault에 추가해야 합니다.

액세스 정책으로 인한 경우: 요청의 개체 ID를 찾고 이 개체 ID가 사용자가 액세스 정책을 할당하려는 개체와 일치하는지 확인합니다. AAD에는 이름이 같은 개체가 여러 개 있을 수 있으므로 올바른 항목을 선택하는 것이 매우 중요합니다. 액세스 정책을 삭제하고 다시 추가하면 이름이 같은 개체가 여러 개 있는지 확인할 수 있습니다.

또한 대부분의 액세스 정책에는 포털에 표시된 것처럼 "권한 있는 응용 프로그램"을 사용할 필요가 없습니다. 권한 있는 응용 프로그램은 거의 발생하지 않는 "On-Behalf-Of" 인증 시나리오에 사용됩니다. 


## <a name="http-429-too-many-requests"></a>HTTP 429: 요청이 너무 많음

요청 수가 해당 기간에 대해 명시된 최댓값을 초과할 때 제한이 발생합니다. 제한이 발생하면 Key Vault의 응답은 HTTP 429가 됩니다. 수행된 요청 유형에 대해 규정된 최대값이 있습니다. 예: HSM 2048비트 키 생성은 요청 5개/10초이지만, 다른 모든 HSM 트랜잭션은 요청 1000개/10초의 제한을 갖습니다. 따라서 제한의 원인을 확인할 때 어떤 유형의 호출을 수행하는지 파악하는 것이 중요합니다.
일반적으로 Key Vault에 대한 요청은 요청 2000개/10초로 제한됩니다. 예외는 [Key Vault 서비스 제한](service-limits.md)에 설명된 것처럼 키 작업입니다.

### <a name="troubleshooting-429"></a>429 문제 해결
다음 기술을 사용하여 제한을 해결할 수 있습니다.

- 요청된 리소스에 대한 패턴이 있는지 확인하고 호출 애플리케이션에서 캐시하려고 시도하여 Key Vault에 대한 요청 수를 줄입니다. 

- Key Vault 제한이 발생하면 다시 시도를 위해 지수 백오프를 사용하도록 요청하는 코드를 조정합니다. 이 알고리즘은 [앱을 제한하는 방법](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)에 설명되어 있습니다.

- 캐싱을 통해 요청 수를 줄일 수 없고 시간이 제한된 백오프가 작동하지 않는 경우 키를 여러 Key Vault로 분할하는 것이 좋습니다. 단일 구독에 대한 서비스 제한은 개별 Key Vault 제한의 5배입니다. 5개를 초과하는 Key Vault를 사용하는 경우 여러 구독을 사용하는 것이 좋습니다. 

제한 증가 요청을 포함한 자세한 지침은 [Key Vault 제한 지침](overview-throttling.md)에서 찾을 수 있습니다.
