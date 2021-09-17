---
title: Microsoft ID 플랫폼에서 서명 키 롤오버
description: 이 문서에서는 Azure Active Directory에 대한 서명 키 롤오버 모범 사례를 설명합니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/03/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: f6073ac0da9163756be353c2ed695dfb20430160
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123469395"
---
# <a name="signing-key-rollover-in-the-microsoft-identity-platform"></a>Microsoft ID 플랫폼에서 서명 키 롤오버
이 문서에서는 Microsoft ID 플랫폼에서 보안 토큰에 서명하는 데 사용하는 공개 키에 대해 알아야 할 사항을 설명합니다. 이러한 키 롤오버가 정기적으로 있으며, 비상 시에는 곧바로 롤오버될 수 있습니다. Microsoft ID 플랫폼을 사용하는 모든 애플리케이션은 키 롤오버 프로세스를 프로그래매틱 방식으로 처리할 수 있어야 합니다. 키의 작동 방식과 롤오버가 애플리케이션에 미친 영향을 평가하는 방법, 필요한 경우 키 롤오버를 처리하도록 애플리케이션을 업데이트하거나 정기적인 수동 롤오버 프로세스를 설정하는 방법을 이해하려면 계속 읽어 보세요.

## <a name="overview-of-signing-keys-in-the-microsoft-identity-platform"></a>Microsoft ID 플랫폼의 서명 키 개요
Microsoft ID 플랫폼은 업계 표준을 기반으로 하는 공개 키 암호화를 사용하여 플랫폼 자체 및 이를 사용하는 애플리케이션 간의 신뢰를 구축합니다. 실질적인 측면에서 Microsoft ID 플랫폼은 공개 키 및 프라이빗 키 쌍으로 구성된 서명 키를 사용하는 방식으로 작동합니다. 사용자가 인증을 위해 Microsoft ID 플랫폼을 사용하는 애플리케이션에 로그인하면 Microsoft ID 플랫폼은 사용자에 대한 정보가 포함된 보안 토큰을 만듭니다. 이 토큰은 애플리케이션으로 다시 전송되기 전에 프라이빗 키를 사용하여 Microsoft ID 플랫폼에서 서명합니다. 해당 토큰이 유효하고 Microsoft ID 플랫폼에서 발생한 것인지 확인하기 위해 애플리케이션은 테넌트의 [OpenID Connect Discovery 문서](https://openid.net/specs/openid-connect-discovery-1_0.html) 또는 SAML/WS-Fed [페더레이션 메타데이터 문서](../azuread-dev/azure-ad-federation-metadata.md)에 포함된 Microsoft ID 플랫폼이 노출한 공개 키를 사용하여 토큰의 서명을 유효성 검사해야 합니다.

보안을 위해 Microsoft ID 플랫폼의 서명 키는 주기적으로 롤링하고, 비상 시에는 곧바로 롤오버될 수 있습니다. 이러한 키 롤오버 사이에는 설정되거나 보장된 시간이 없습니다. Microsoft ID 플랫폼과 통합되는 모든 애플리케이션은 발생 빈도에 상관없이 키 롤오버 이벤트를 처리할 준비가 되어 있어야 합니다. 애플리케이션이 갑작스러운 새로 고침을 처리하지 않고 만료된 키를 사용하여 토큰의 서명을 확인하려고 하면 애플리케이션에서 토큰을 잘못 거부합니다.  애플리케이션 캐시의 키로 유효성을 검사하지 않는 토큰이 있는 경우 키 문서의 즉시 새로 고침을 제한(최대 5분마다 한 번씩)하여 24시간마다 업데이트를 확인하는 것이 가장 좋습니다. 

OpenID Connect discovery 문서와 페더레이션 메타데이터 문서에는 사용 가능한 키가 항상 두 개 이상 있습니다. 하나의 키가 곧 롤오버되고 다른 키가 대체되는 방식으로 문서에 지정된 모든 키를 사용하도록 애플리케이션이 준비되어야 합니다.  새 플랫폼, 새 클라우드 또는 새 인증 프로토콜을 지원하므로 제공되는 키 수는 Microsoft ID 플랫폼 내부 아키텍처에 따라 점차 변경될 수 있습니다. JSON 응답에서 키의 순서와 키가 노출된 순서는 앱에 의미 있는 것으로 간주해서는 안 됩니다. 

단일 서명 키만 지원하거나 서명 키를 수동으로 업데이트해야 하는 애플리케이션은 기본적으로 보안성과 안정성이 떨어집니다.  [표준 라이브러리](reference-v2-libraries.md)를 사용하도록 업데이트하여 다른 모범 사례 중에서도 항상 최신 서명 키를 사용하도록 해야 합니다. 

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>애플리케이션이 영향을 받을지 여부와 그에 대한 대처 방안을 평가하는 방법
애플리케이션이 키 롤오버를 처리하는 방식은 애플리케이션 유형 또는 사용된 ID 프로토콜 및 라이브러리 버전과 같은 변수에 따라 달라집니다. 아래 섹션에서는 가장 일반적인 유형의 애플리케이션이 키 롤오버의 영향을 받는지 여부와 자동 롤오버를 지원하거나 수동으로 키를 업데이트하도록 애플리케이션을 업데이트하는 방법에 대한 지침을 제공하는지 여부를 평가합니다.

* [리소스에 액세스하는 네이티브 클라이언트 애플리케이션](#nativeclient)
* [리소스에 액세스하는 웹 애플리케이션/API](#webclient)
* [리소스를 보호하고 Azure App Services를 사용하여 빌드된 웹 애플리케이션/API](#appservices)
* [.NET OWIN OpenID Connect, WS-Fed 또는 WindowsAzureActiveDirectoryBearerAuthentication 미들웨어를 사용하여 리소스를 보호하는 웹 애플리케이션/API](#owin)
* [.NET Core OpenID Connect 또는 JwtBearerAuthentication 미들웨어를 사용하여 리소스를 보호하는 웹 애플리케이션/API](#owincore)
* [Node.js passport-azure-ad 모듈을 사용하여 리소스를 보호하는 웹 애플리케이션/API](#passport)
* [리소스를 보호하며 Visual Studio 2015 이상을 사용하여 만든 웹 애플리케이션/API](#vs2015)
* [리소스를 보호하며 Visual Studio 2013을 사용하여 만든 웹 애플리케이션](#vs2013)
* 리소스를 보호하며 Visual Studio 2013을 사용하여 만든 웹 API
* [리소스를 보호하며 Visual Studio 2012를 사용하여 만든 웹 애플리케이션](#vs2012)
* [다른 라이브러리를 사용하거나 지원되는 프로토콜을 수동으로 구현하여 리소스를 보호하는 웹 애플리케이션/API](#other)

이 설명서는 다음에 적용할 수 **없습니다** .

* Azure AD 애플리케이션 갤러리에서 추가된 애플리케이션(사용자 지정 포함)에는 서명 키와 관련하여 별도 설명서가 있습니다. [자세한 정보](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* 애플리케이션 프록시를 통해 게시된 온-프레미스 애플리케이션은 서명 키에 대해 걱정할 필요가 없습니다.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>리소스에 액세스하는 네이티브 클라이언트 애플리케이션
리소스에만 액세스하는 애플리케이션(즉, Microsoft Graph, KeyVault, Outlook API 및 기타 Microsoft API)은 일반적으로 토큰만 가져와서 리소스 소유자에게 전달합니다. 리소스를 보호하지 못한다면 토큰을 검사하지 않으므로 제대로 서명되었는지 확인할 필요도 없습니다.

데스크톱 또는 모바일의 네티이브 클라이언트 애플리케이션이 이 범주에 해당하므로 롤오버의 영향을 받지 않습니다.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>리소스에 액세스하는 웹 애플리케이션/API
리소스에만 액세스하는 애플리케이션(즉, Microsoft Graph, KeyVault, Outlook API 및 기타 Microsoft API)은 일반적으로 토큰만 가져와서 리소스 소유자에게 전달합니다. 리소스를 보호하지 못한다면 토큰을 검사하지 않으므로 제대로 서명되었는지 확인할 필요도 없습니다.

앱 전용 흐름을 사용하여 토큰을 요청하는 웹 애플리케이션 및 웹 API(클라이언트 자격 증명/클라이언트 인증서)가 이 범주에 해당하므로 롤오버의 영향을 받지 않습니다.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>리소스를 보호하고 Azure App Services를 사용하여 빌드된 웹 애플리케이션/API
Azure App Services의 인증/권한 부여(EasyAuth) 기능에는 이미 키 롤오버를 자동으로 처리하는 데 필요한 논리가 있습니다.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>.NET OWIN OpenID Connect, WS-Fed 또는 WindowsAzureActiveDirectoryBearerAuthentication 미들웨어를 사용하여 리소스를 보호하는 웹 애플리케이션/API
애플리케이션에서 .NET OWIN OpenID Connect, WS-Fed 또는 WindowsAzureActiveDirectoryBearerAuthentication 미들웨어를 사용 하는 경우, 자동으로 키 롤오버를 처리하는 데 필요한 논리가 이미 있는 것입니다.

애플리케이션의 Startup.cs 또는 Startup.Auth.cs 파일에서 다음 코드 조각을 찾아봄으로써 애플리케이션에서 이들 미들웨어를 사용하는지 확인할 수 있습니다.

```csharp
app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWindowsAzureActiveDirectoryBearerAuthentication(
    new WindowsAzureActiveDirectoryBearerAuthenticationOptions
    {
        // ...
    });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>.NET Core OpenID Connect 또는 JwtBearerAuthentication 미들웨어를 사용하여 리소스를 보호하는 웹 애플리케이션/API
애플리케이션에서 .NET Core OWIN OpenID Connect 또는 JwtBearerAuthentication 미들웨어를 사용 하는 경우, 자동으로 키 롤오버를 처리하는 데 필요한 논리가 이미 있는 것입니다.

애플리케이션의 Startup.cs 또는 Startup.Auth.cs에서 다음 코드 조각을 찾아봄으로써 애플리케이션에서 이들 미들웨어를 사용하는지 확인할 수 있습니다.

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Node.js passport-azure-ad 모듈을 사용하여 리소스를 보호하는 웹 애플리케이션/API
애플리케이션에서 Node.js passport-ad module을 사용하는 경우, 자동으로 키 롤오버를 처리하는 데 필요한 논리가 이미 있는 것입니다.

애플리케이션의 app.js에서 다음 코드 조각을 찾아봄으로써 애플리케이션 passport ad를 확인할 수 있습니다.

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>리소스를 보호하며 Visual Studio 2015 이상을 사용하여 만든 웹 애플리케이션/API
Visual Studio 2015 이상에서 웹 애플리케이션 템플릿을 사용하여 애플리케이션을 빌드했고 **인증 변경** 메뉴에서 **회사 및 학교 계정** 을 선택한 경우 자동으로 키 롤오버를 처리하는 데 필요한 논리가 이미 있는 것입니다. OWIN OpenID Connect 미들웨어에 포함된 이 논리는 OpenID Connect discovery 문서에서 키를 검색하고 캐시하며 주기적으로 새로 고칩니다.

인증을 솔루션에 수동으로 추가하면 애플리케이션에 필요한 키 롤오버 논리가 없을 수도 있습니다. 사용자가 직접 작성하거나 [웹 애플리케이션/다른 라이브러리를 사용하거나 지원되는 프로토콜을 수동으로 구현하는 API](#other)의 단계를 따라야 합니다.

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>리소스를 보호하며 Visual Studio 2013을 사용하여 만든 웹 애플리케이션
Visual Studio 2013에서 웹 애플리케이션 템플릿을 사용하여 애플리케이션을 빌드했고 **인증 변경** 메뉴에서 **조직 계정** 을 선택한 경우 자동으로 키 롤오버를 처리하는 데 필요한 논리가 이미 있는 것입니다. 이 논리는 조직의 고유 ID 및 서명 키 정보를 프로젝트와 연결된 두 데이터베이스 테이블에 저장합니다. 프로젝트의 Web.config 파일에서 데이터베이스에 대한 연결 문자열을 찾을 수 있습니다.

인증을 솔루션에 수동으로 추가하면 애플리케이션에 필요한 키 롤오버 논리가 없을 수도 있습니다. 사용자가 직접 작성하거나 [웹 애플리케이션/다른 라이브러리를 사용하거나 지원되는 프로토콜을 수동으로 구현하는 API](#other)의 단계를 따라야 합니다.

다음 단계는 애플리케이션에서 논리가 제대로 작동하는지 확인하는 데 도움이 됩니다.

1. Visual Studio 2013에서 솔루션을 연 다음 오른쪽 창에서 **서버 탐색기** 탭을 클릭합니다.
2. **데이터 연결**, **DefaultConnection** 및 **테이블** 을 차례로 확장합니다. **IssuingAuthorityKeys** 테이블을 찾아 마우스 오른쪽 단추로 클릭한 다음 **테이블 데이터 표시** 를 클릭합니다.
3. **IssuingAuthorityKeys** 테이블에는 각 키의 지문에 해당하는 행이 하나 이상 있습니다. 테이블의 모든 행을 삭제합니다.
4. **Tenants** 테이블을 마우스 오른쪽 단추로 클릭한 다음 **테이블 데이터 표시** 를 클릭합니다.
5. **Tenants** 테이블에는 고유한 디렉터리 테넌트 식별자에 해당하는 행이 하나 이상 있습니다. 테이블의 모든 행을 삭제합니다. **테넌트** 테이블 및 **IssuingAuthorityKeys** 테이블 모두에서 행을 삭제하지 않으면 런타임에 오류가 발생합니다.
6. 애플리케이션을 빌드 및 실행합니다. 계정에 로그인한 후에 애플리케이션을 중지할 수 있습니다.
7. **서버 탐색기** 로 돌아와서 **IssuingAuthorityKeys** 및 **Tenants** 테이블의 값을 확인합니다. 페더레이션 메타데이터 문서의 적절한 정보로 자동으로 다시 채워진 것을 알 수 있습니다.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>리소스를 보호하며 Visual Studio 2013을 사용하여 만든 웹 API
Web API 템플릿을 사용하여 Visual Studio 2013에서 Web API 응용 프로그램을 만들고 **인증 변경** 메뉴에서 **조직 계정** 을 선택한 경우 응용 프로그램에는 이미 필수 논리가 있습니다.

인증을 수동으로 구성한 경우 아래 지침을 따라 키 정보를 자동으로 업데이트하는 웹 API를 구성하는 방법을 알아봅니다.

다음 코드 조각은 페더레이션 메타데이터 문서에서 최신 키를 가져온 후 [JWT 토큰 처리기](/previous-versions/dotnet/framework/windows-identity-foundation/json-web-token-handler) 를 사용하여 토큰의 유효성을 검사하는 방법을 보여 줍니다. 이 코드 조각은 Microsoft ID 플랫폼에서 향후 토큰의 유효성을 검사하기 위해 키를 유지하는 데 키 위치에 관계없이(데이터베이스, 구성 파일 또는 다른 곳) 고유의 캐싱 메커니즘을 사용한다고 가정합니다.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>리소스를 보호하며 Visual Studio 2012를 사용하여 만든 웹 애플리케이션
Visual Studio 2012에서 애플리케이션을 빌드한 경우 애플리케이션을 구성하는 데 아마도 ID 및 액세스 도구를 사용한 것입니다. 또한 [발급자 이름 레지스트리 유효성 검사(VINR)](/previous-versions/dotnet/framework/windows-identity-foundation/validating-issuer-name-registry)를 사용할 가능성이 높습니다. VINR은 발급된 토큰의 유효성을 검사하는 데 사용된 키와 신뢰할 수 있는 ID 공급자(Microsoft ID 플랫폼)에 대한 정보의 유지 관리를 담당합니다. 또한 VINR을 통해 해당 디렉터리에 연결된 최신 페더레이션 메타데이터 문서를 다운로드하고 구성이 최신 문서와 함께 만료되었는지 확인하며 필요에 따라 새 키를 사용하도록 애플리케이션을 업데이트하여 Web.config 파일에 저장된 키 정보를 자동으로 간편하게 업데이트할 수 있습니다.

Microsoft에서 제공하는 코드 샘플 또는 연습 문서를 사용하여 애플리케이션을 만든 경우 키 롤오버 논리가 프로젝트에 이미 포함됩니다. 아래 코드가 프로젝트에 이미 있는 것을 확인할 수 있습니다. 애플리케이션에 이 논리가 아직 없는 경우 아래 단계에 따라 추가하고 제대로 작동하는지 확인합니다.

1. **솔루션 탐색기** 에서 해당 프로젝트의 **System.IdentityModel** 어셈블리에 참조를 추가합니다.
2. **Global.asax.cs** 파일을 열고 지시문을 사용하여 다음을 추가합니다.
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. 다음 메서드를 **Global.asax** 파일에 추가합니다.
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. 아래 표시된 것처럼 **Global.asax.cs** 의 **Application_Start()** 메서드에서 **RefreshValidationSettings()** 메서드를 호출합니다.
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

이러한 단계를 완료하면 애플리케이션의 Web.config가 최신 키를 포함한 페더레이션 메타데이터 문서의 최신 정보로 업데이트됩니다. 이 업데이트는 애플리케이션 풀이 IIS에서 재활용될 때마다 발생하며 기본적으로 IIS는 29시간마다 애플리케이션을 재활용하도록 설정됩니다.

아래 단계에 따라 키 롤오버 논리가 제대로 작동하는지 확인합니다.

1. 애플리케이션이 위의 코드를 사용 중인지를 확인한 후 **Web.config** 파일을 열고 **\<issuerNameRegistry>** 블록으로 이동하여 구체적으로 다음 몇 줄을 확인합니다.
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. **\<add thumbprint="">** 설정에서 모든 문자를 다른 것으로 바꿔서 지문 값을 변경합니다. **Web.config** 파일을 저장합니다.
3. 애플리케이션을 빌드하고 실행합니다. 로그인 프로세스를 완료할 수 있으면 애플리케이션은 디렉터리의 페더레이션 메타데이터 문서에서 필요한 정보를 다운로드하여 키를 성공적으로 업데이트합니다. 로그인하는 데 문제가 있는 경우 [을 사용하여 웹 애플리케이션에 로그온 추가](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) 문서를 읽거나 [Azure Active Directory에 대한 다중 테넌트 클라우드 애플리케이션](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b) 코드 샘플을 다운로드 및 검사하여 애플리케이션의 변경 내용이 올바른지 확인합니다.

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>다른 라이브러리를 사용하거나 지원되는 프로토콜을 수동으로 구현하여 리소스를 보호하는 웹 애플리케이션/API
다른 라이브러리를 사용하거나 지원되는 프로토콜을 수동으로 구현하는 경우, 키가 OpenID Connect discovery 문서 또는 페더레이션 메타데이터 문서에서 검색되는지 확인하기 위해 라이브러리나 구현을 검토할 필요가 있습니다. 이를 확인하는 하나의 방법은 OpenID discovery 문서 또는 페더레이션 메타데이터 문서에 대한 모든 호출 코드 또는 라이브러리 코드를 검색하는 것입니다.

키가 어딘가에 저장되거나 애플리케이션에 하드 코딩되는 경우 이 지침 문서의 끝에 있는 지침에 따라 수동 롤오버를 수행하여 수동으로 키를 검색하고 적절하게 업데이트할 수 있습니다. Microsoft ID 플랫폼의 롤오버 케이던스가 늘어나거나 긴급 대역 외 롤오버가 발생하는 경우, 향후 작업 중단 및 오버헤드를 방지하기 위해 이 문서에 설명된 방법 중 하나를 사용하여 **자동 롤오버를 지원하도록 애플리케이션을 향상하는 것이 아주 좋습니다**.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>애플리케이션을 테스트하여 영향을 받을지 확인하는 방법
스크립트를 다운로드하고 [이 GitHub 리포지토리](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>애플리케이션이 자동 롤오버를 지원하지 않는 경우 수동 롤오버를 수행하는 방법
애플리케이션이 자동 롤오버를 지원하지 **않는** 경우 주기적으로 Microsoft ID 플랫폼의 서명 키를 모니터링하고 적절하게 수동 롤오버를 수행하는 프로세스를 설정해야 합니다. [이 GitHub 리포지토리](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) 는 이 작업을 수행하는 방법에 대한 스크립트 및 지침을 포함합니다.
