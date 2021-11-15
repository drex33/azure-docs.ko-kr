---
title: 요청 서비스 REST API(미리 보기)를 호출하는 방법
titleSuffix: Azure Active Directory Verifiable Credentials
description: 요청 서비스 REST API를 사용하여 발급하고 확인하는 방법 알아보기
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 805ba2fc9a24536a940e4baf02e2934d26c10d75
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474025"
---
# <a name="request-service-rest-api-preview"></a>요청 서비스 REST API(미리 보기)

Azure AD(Azure Active Directory) 확인 가능한 자격 증명에는 요청 서비스 REST API가 포함됩니다. 이 API를 사용하면 자격 증명을 발급하고 확인할 수 있습니다. 이 문서에서는 요청 서비스 REST API를 사용하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 요청 서비스 REST API는 현재 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 미리 보기에서 API의 주요 변경 내용 및 사용 중단을 예상할 수 있습니다. 프로덕션 워크로드에는 미리 보기 버전의 API를 권장하지 않습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="api-access-token"></a>API 액세스 토큰

애플리케이션이 요청 서비스 REST API에 액세스하려면 필요한 권한이 있는 유효한 액세스 토큰을 포함해야 합니다. Microsoft ID 플랫폼에서 발급한 액세스 토큰에는 요청 서비스 REST API에서 호출자의 유효성을 검사하는 데 사용하는 정보(범위)가 포함됩니다. 액세스 토큰은 호출자가 요청하는 작업을 수행할 수 있는 적절한 권한을 갖도록 합니다.

액세스 토큰을 얻으려면 앱이 Microsoft ID 플랫폼으로 등록되고 관리자가 요청 서비스 REST API에 대한 액세스 권한을 부여받아야 합니다. *verifiable-credentials-app* 애플리케이션을 등록하지 않은 경우 [앱을 등록하는 방법](verifiable-credentials-configure-tenant.md#register-an-application-in-azure-ad) 및 [애플리케이션 비밀 생성](verifiable-credentials-configure-issuer.md#configure-the-verifiable-credentials-app)을 참조하세요.

### <a name="get-an-access-token"></a>액세스 토큰 가져오기

[OAuth 2.0 클라이언트 자격 증명 부여 흐름](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)을 사용하여 Microsoft ID 플랫폼을 통해 액세스 토큰을 획득합니다. 이 목적을 위해 신뢰할 수 있는 라이브러리를 사용합니다. 이 자습서에서는 Microsoft 인증 라이브러리 [MSAL](../../active-directory/develop/msal-overview.md)을 사용합니다. MSAL은 보안 웹 API를 호출할 수 있는 앱에 인증 및 권한 부여를 추가하는 것을 간소화합니다.

# <a name="http"></a>[HTTP](#tab/http)

```http
Refer to to the Microsoft Authentication Library (MSAL) documentation for more information on how to acquire tokens via HTTP.
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Initialize MSAL library by using the following code
ConfidentialClientApplicationBuilder.Create(AppSettings.ClientId)
    .WithClientSecret(AppSettings.ClientSecret)
    .WithAuthority(new Uri(AppSettings.Authority))
    .Build();

// Acquire an access token
result = await app.AcquireTokenForClient(AppSettings.Scopes)
                .ExecuteAsync();
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```nodejs
// Initialize MSAL library by using the following code
const msalConfig = {
  auth: {
      clientId: config.azClientId,
      authority: `https://login.microsoftonline.com/${config.azTenantId}`,
      clientSecret: config.azClientSecret,
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
const msalClientCredentialRequest = {
  scopes: ["bbb94529-53a3-4be5-a069-7eaf2712b826/.default"],
  skipCache: false, 
};
module.exports.msalCca = cca;
module.exports.msalClientCredentialRequest = msalClientCredentialRequest;

// Acquire an access token
const result = await mainApp.msalCca.acquireTokenByClientCredential(mainApp.msalClientCredentialRequest);
    if ( result ) {
      accessToken = result.accessToken;
    }
```

---

위의 코드에서 다음 매개 변수를 제공합니다.

| 매개 변수 | 조건 | Description |
| --- | --- | --- |
| Authority | 필수 | 애플리케이션이 작동할 디렉터리 테넌트입니다. 예: `https://login.microsoftonline.com/{your-tenant}` (`your-tenant`를 [테넌트 ID 또는 이름](../fundamentals/active-directory-how-to-find-tenant.md)으로 바꿉니다.) |
| 클라이언트 ID | 필수 | 앱에 할당되는 애플리케이션 ID입니다. 앱을 등록한 Azure Portal에서 이 정보를 찾을 수 있습니다. |
| 클라이언트 암호 | 필수 | 앱에 대해 생성한 클라이언트 암호입니다.|
| 범위 | 필수 | `bbb94529-53a3-4be5-a069-7eaf2712b826/.default`로 설정해야 합니다. |

콘솔 앱의 ID를 사용하여 액세스 토큰을 얻는 방법에 대한 자세한 내용은 [C#](../develop/quickstart-v2-netcore-daemon.md), [Python](../develop/quickstart-v2-python-daemon.md), [Node.js](../develop/quickstart-v2-nodejs-console.md) 또는 [Java](../develop/quickstart-v2-java-daemon.md) 문서 중 하나를 참조하세요.

클라이언트 암호 대신 [인증서를 사용하여 토큰 요청에 액세스](../develop/v2-oauth2-client-creds-grant-flow.md)할 수도 있습니다.

# <a name="http"></a>[HTTP](#tab/http)

```http
POST /{tenant}/oauth2/v2.0/token HTTP/1.1   //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=12345678-0000-0000-00000000000000000
&scope=bbb94529-53a3-4be5-a069-7eaf2712b826/.default
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Initialize MSAL library by using the following code
X509Certificate2 certificate = AppSettings.ReadCertificate(AppSettings.CertificateName);
    app = ConfidentialClientApplicationBuilder.Create(AppSettings.ClientId)
        .WithCertificate(certificate)
        .WithAuthority(new Uri(AppSettings.Authority))
        .Build();

// Acquire an access token
result = await app.AcquireTokenForClient(AppSettings.Scopes)
                .ExecuteAsync();
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```nodejs
// Initialize MSAL library by using the following code
const msalConfig = {
  auth: {
      clientId: config.azClientId,
      authority: `https://login.microsoftonline.com/${config.azTenantId}`,
      clientCertificate: {
            thumbprint: "CERT_THUMBPRINT", // a 40-digit hexadecimal string
            privateKey: "CERT_PRIVATE_KEY"
        }
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
const msalClientCredentialRequest = {
  scopes: ["bbb94529-53a3-4be5-a069-7eaf2712b826/.default"],
  skipCache: false, 
};
module.exports.msalCca = cca;
module.exports.msalClientCredentialRequest = msalClientCredentialRequest;

// Acquire an access token
const result = await mainApp.msalCca.acquireTokenByClientCredential(mainApp.msalClientCredentialRequest);
    if ( result ) {
      accessToken = result.accessToken;
    }
```

---

## <a name="call-the-api"></a>API 호출

확인 가능한 자격 증명을 발급하거나 확인하려면 다음 단계를 수행합니다.

1. 요청 서비스 REST API에 대한 HTTP POST 요청을 생성합니다. `{tenantID}`를 테넌트 ID 또는 테넌트 이름으로 바꿉니다.

    ```http
    POST https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
    ```

1. HTTP 요청의 인증 헤더에 액세스 토큰을 전달자 토큰으로 연결합니다.

    ```http
    Authorization: Bearer <token>
    ```

1. `Content-Type` 헤더를 `Application/json`으로 설정합니다.

1. [발급](issuance-request-api.md#issuance-request-payload) 또는 [프레젠테이션](presentation-request-api.md#presentation-request-payload) 요청 페이로드를 준비하고 요청 본문에 연결합니다.

1. 요청 서비스 REST API에 대한 요청을 제출합니다.

## <a name="issuance-request-example"></a>발급 요청 예제

다음 예제에서는 확인 가능한 자격 증명 발급 요청을 보여 줍니다. 페이로드에 대한 자세한 내용은 [요청 서비스 REST API 발급 사양](issuance-request-api.md)을 참조하세요.

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{
    "includeQRCode": true,
    "callback": {
        "url": "https://www.contoso.com/api/issuer/issuanceCallback",
        "state": "de19cb6b-36c1-45fe-9409-909a51292a9c",
        "headers": {
            "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
        }
    },
    "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDek...",
    "registration": {
        "clientName": "Verifiable Credential Expert Sample"
    },
    "issuance": {
        "type": "VerifiedCredentialExpert",
        "manifest": "https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiableCredential/contracts/VerifiedCredentialExpert1",
        "pin": {
            "value": "3539",
            "length": 4
        },
        "claims": {
            "given_name": "Megan",
            "family_name": "Bowen"
        }
    }
}
```  

전체 코드는 다음 코드 샘플 중 하나를 참조하세요.

- [C#](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/AspNetCoreVerifiableCredentials/IssuerController.cs)
- [Node.JS](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/issuer.js)

## <a name="presentation-request-example"></a>프레젠테이션 요청 예제

다음 예제에서는 확인 가능한 자격 증명 프레젠테이션 요청을 보여 줍니다. 페이로드에 대한 자세한 내용은 [요청 서비스 REST API 프레젠테이션 사양](presentation-request-api.md)을 참조하세요.

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{
  "includeQRCode": true,
  "callback": {
    "url": "https://www.contoso.com/api/verifier/presentationCallback",
    "state": "92d076dd-450a-4247-aa5b-d2e75a1a5d58",
    "headers": {
      "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
    }
  },
  "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiOiJiRWo5MDY...",
  "registration": {
    "clientName": "Veritable Credential Expert Verifier"
  },
  "presentation": {
    "includeReceipt": true,
    "requestedCredentials": [
      {
        "type": "VerifiedCredentialExpert",
        "purpose": "So we can see that you a veritable credentials expert",
        "acceptedIssuers": [
          "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiO..."
        ]
      }
    ]
  }
}
```

전체 코드는 다음 코드 샘플 중 하나를 참조하세요.

- [C#](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/1-asp-net-core-api-idtokenhint/VerifierController.cs) 
- [Node.JS](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/verifier.js)

## <a name="callback-events"></a>콜백 이벤트

요청 페이로드에는 [발급](issuance-request-api.md#callback-events) 및 [프레젠테이션](presentation-request-api.md#callback-events) 콜백 엔드포인트가 포함됩니다. 엔드포인트는 웹 애플리케이션의 일부이며 공개적으로 사용할 수 있어야 합니다. Azure AD 확인 가능한 자격 증명은 엔드포인트를 호출하여 특정 이벤트에 대해 앱에 알릴 수 있습니다. 예를 들어 사용자가 QR 코드를 검색하거나, 인증자 앱의 딥 링크를 사용하거나, 프레젠테이션 프로세스를 완료하는 경우 이러한 이벤트가 발생할 수 있습니다.

다음 다이어그램에서는 요청 서비스 REST API에 대한 앱 호출 및 애플리케이션에 대한 콜백을 설명합니다.

![API 및 콜백 이벤트에 대한 호출을 설명하는 다이어그램입니다.](media/get-started-request-api/callback-events.png)

들어오는 HTTP POST 요청을 수신 대기하도록 엔드포인트를 구성합니다. 다음 코드 조각에서는 발급 콜백 HTTP 요청을 처리하는 방법과 그에 따라 UI를 업데이트하는 방법을 보여 줍니다.

# <a name="http"></a>[HTTP](#tab/http)

해당 사항 없음 다른 프로그래밍 언어 중 하나를 선택합니다.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[HttpPost]
public async Task<ActionResult> IssuanceCallback()
{
try
{
    string content = new System.IO.StreamReader(this.Request.Body).ReadToEndAsync().Result;
    _log.LogTrace("callback!: " + content);
    JObject issuanceResponse = JObject.Parse(content);
    
    // More code here
    if (issuanceResponse["code"].ToString() == "request_retrieved")
    {
        var cacheData = new
        {
            status = "request_retrieved",
            message = "QR Code is scanned. Waiting for issuance...",
        };
        _cache.Set(state, JsonConvert.SerializeObject(cacheData));

        // More code here
    }
}
```

전체 코드는 GitHub 리포지토리의 [발급](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/AspNetCoreVerifiableCredentials/IssuerController.cs) 및 [프레젠테이션](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/AspNetCoreVerifiableCredentials/VerifierController.cs) 코드를 참조하세요.

# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

```nodejs
mainApp.app.post('/api/issuer/issuance-request-callback', parser, async (req, res) => {
  var body = '';
  req.on('data', function (data) {
    body += data;
  });
  req.on('end', function () {
    requestTrace( req );
    console.log( body );
    var issuanceResponse = JSON.parse(body.toString());
    var message = null;
    
    if ( issuanceResponse.code == "request_retrieved" ) {
      message = "QR Code is scanned. Waiting for issuance to complete...";
    }
    if ( issuanceResponse.code == "issuance_successful" ) {
      message = "Credential successfully issued";
    }
    if ( issuanceResponse.code == "issuance_error" ) {
      message = issuanceResponse.error.message;
    }
    
    // More code here
    res.send()
  });  
  res.send()
})
```

전체 코드는 GitHub 리포지토리의 [발급](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/issuer.js) 및 [프레젠테이션](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/verifier.js) 코드를 참조하세요.

---

## <a name="next-steps"></a>다음 단계

다음 사양에 대해 자세히 알아봅니다.

- [발급 API 사양](issuance-request-api.md)
- [프레젠테이션 API 사양](presentation-request-api.md)
