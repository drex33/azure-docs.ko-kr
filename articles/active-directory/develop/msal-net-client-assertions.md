---
title: 클라이언트 어설션(MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: MSAL.NET(.NET용 Microsoft 인증 라이브러리)에서 기밀 클라이언트 애플리케이션에 대한 서명된 클라이언트 어설션 지원에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/18/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 92c1dbe34078442fa5ba84c00880e15502602bc6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787152"
---
# <a name="confidential-client-assertions"></a>기밀 클라이언트 어설션

기밀 클라이언트 애플리케이션은 ID를 증명하기 위해 Azure AD와 암호를 교환합니다. 암호는 다음과 같을 수 있습니다.
- 클라이언트 암호(애플리케이션 암호)
- 표준 클레임을 포함하는 서명된 어설션을 작성하는 데 사용되는 인증서입니다.

이 암호는 직접 서명된 어설션일 수도 있습니다.

MSAL.NET에는 기밀 클라이언트 앱에 자격 증명 또는 어설션을 제공하는 네 가지 메서드가 있습니다.
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> `WithClientAssertion()` API를 사용하여 기밀 클라이언트에 대한 토큰을 획득할 수 있지만 기본적으로는 사용하지 않는 것이 좋습니다. 일반적이지 않은 매우 구체적인 고급 시나리오를 처리하도록 설계되었기 때문입니다. `.WithCertificate()` API를 사용하면 MSAL.NET이 이를 처리할 수 있습니다. 이 API는 필요한 경우 인증 요청을 사용자 지정하는 기능을 제공하지만`.WithCertificate()`에서 만든 기본 어설션은 대부분의 인증 시나리오에서 충분합니다. MSAL.NET이 내부적으로 서명 작업을 수행하지 못하는 일부 시나리오에서는 이 API를 해결 방법으로 사용할 수도 있습니다.

### <a name="signed-assertions"></a>서명된 어설션

서명된 클라이언트 어설션은 Azure AD, Base64로 인코딩된 필수 인증 클레임을 포함하는 페이로드에 서명된 JWT 형식을 사용합니다. 이를 사용하려면:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

또한 대리자 양식을 사용하여 Just-In-Time 어설션을 컴퓨팅할 수 있습니다.

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(() => { return GetSignedClientAssertion(); } )
                                          .Build();
                                          
// or in async manner

app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(async cancellationToken => { return await GetClientAssertionAsync(cancellationToken); })
                                          .Build();
```

서명된 어설션에서 [Azure AD에 예상되는 클레임](active-directory-certificate-credentials.md)은 다음과 같습니다.

클레임 유형 | 값 | 설명
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | "aud"(대상) 클레임은 JWT가 대상으로 하는 수신자(여기서는 Azure AD)를 식별합니다. [RFC 7519, 섹션 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3)을 참조하세요.  이 경우 해당 수신자는 로그인 서버(login.microsoftonline.com)입니다.
exp | 1601519414 | "exp"(만료 시간) 클레임은 JWT가 그 이후에는 처리를 허용하지 않아야 하는 만료 시간을 식별합니다. [RFC 7519, 섹션 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4)를 참조하세요.  이렇게 하면 그 때까지 어설션을 사용할 수 있으므로 `nbf` 이후 최대한 짧게 5~10분간 유지합니다.  Azure AD는 현재 `exp` 시간에 제한을 두지 않습니다. 
iss | {ClientID} | "Iss"(발급자) 클레임은 JWT를 발급한 보안 주체(이 경우 클라이언트 애플리케이션)를 식별합니다.  GUID 애플리케이션 ID를 사용합니다.
jti | (GUID) | "jti"(JWT ID) 클레임은 JWT에 대한 고유 식별자를 제공합니다. 식별자 값은 동일한 값이 실수로 다른 데이터 개체에 할당될 가능성이 무시되는 방식으로 할당되어야 합니다. 애플리케이션이 여러 발급자를 사용하는 경우 서로 다른 발급자가 생성한 값 간에도 충돌을 방지해야 합니다. "jti" 값은 대/소문자를 구분하는 문자열입니다. [RFC 7519, 섹션 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
nbf | 1601519114 | "nbf"(not before) 클레임은 JWT가 그 이전에는 처리를 허용하지 않아야 하는 시간을 식별합니다. [RFC 7519, 섹션 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5)  현재 시간을 사용하는 것이 적절합니다. 
sub | {ClientID} | "Sub"(주체) 클레임은 JWT의 주체(이 경우에는 애플리케이션)도 식별합니다. `iss`와 동일한 값을 사용합니다. 

이러한 클레임을 작성하는 방법의 예는 다음과 같습니다.

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

서명된 클라이언트 어설션을 만드는 방법은 다음과 같습니다.

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims()).ToString()));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>대체 방법

[Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/)를 사용하여 어설션을 만들 수도 있습니다. 코드는 아래 예제에 표시된 것처럼 더 세련된 모양입니다.

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

서명된 클라이언트 어설션이 있으면 아래와 같이 MSAL API와 함께 사용할 수 있습니다.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` 기본적으로 Azure AD에서 예상하는 클레임과 보내려는 추가 클라이언트 클레임을 포함하는 서명된 어설션을 생성합니다. 이를 수행하는 방법에 대한 코드 조각이 있습니다.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

전달하는 사전의 클레임 중 하나가 필수 클레임 중 하나와 동일한 경우 추가 클레임의 값이 고려됩니다. MSAL.NET에서 컴퓨팅된 클레임을 재정의합니다.

Azure AD에서 예상하는 필수 클레임을 포함하여 고유한 클레임을 제공하려면 `mergeWithDefaultClaims` 매개 변수에 `false`를 전달합니다.
