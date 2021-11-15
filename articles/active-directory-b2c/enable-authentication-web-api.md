---
title: Azure Active Directory B2C를 사용하여 웹 API에서 인증을 사용하도록 설정
description: 이 문서에서는 Azure Active Directory B2C를 사용하여 웹 API를 보호 하는 방법을 설명 합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/26/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 38a60dee9d4a5b7c0516b8e3278e848e0be26c2e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424442"
---
# <a name="enable-authentication-in-your-own-web-api-by-using-azure-ad-b2c"></a>Azure AD B2C를 사용하여 사용자 자체 웹 API에서 인증을 사용합니다.

웹 API에 대한 액세스 권한을 부여 하려면 유효한 Azure Active Directory B2C(Azure AD B2C) 발급 된 액세스 토큰을 포함하는 요청만 제공합니다. 이 문서에서 웹 API에 대한 Azure AD B2C 권한 부여를 사용 하는 방법을 보여줍니다. 이 문서의 단계들을 완료 한 후에는 유효한 액세스 토큰을 획득 한 사용자에게만 웹 API 엔드포인트를 호출할 수 있는 권한이 부여 됩니다.  

## <a name="prerequisites"></a>필수 구성 요소

시작 하기 전에 웹 API를 호출 하는 앱에 대한 인증을 구성하는 방법에 대해 설명 하는 다음 문서 중 하나를 참조하세요. 그 후 샘플 웹 API를 고유 웹 API로 바꾸기 위한 단계들을 따르세요.   

- [샘플 ASP.NET Core 응용 프로그램에서 인증을 구성합니다.](configure-authentication-sample-web-app-with-api.md)
- [샘플 단일 페이지 어플리케이션에서 인증을 구성합니다.](configure-authentication-sample-spa-app.md)

## <a name="overview"></a>개요

토큰 기반 인증을 사용하면 웹 API에 대한 요청이 유효한 액세스 토큰과 함께 제공되는 것을 확인합니다. 

앱은 다음 작업을 수행합니다.

1. Azure AD B2C를 사용하는 사용자를 인증합니다.
1. 웹 API 엔드포인트에 대해 필요한 권한(범위)이 포함된 액세스 토큰을 가져옵니다.
1. 다음 형식을 사용하여 HTTP 요청의 인증 헤더에서 전달자 토큰으로 액세스 토큰을 전달 합니다. 

    ```http
    Authorization: Bearer <token>
    ```    

이 웹 API는 다음을 수행합니다.

1. HTTP 요청의 인증 헤더에서 전달자 토큰을 읽습니다.

1. 이는 토큰의 유효성을 검사합니다. 
1. 토큰에서 사용 권한(범위)의 유효성을 검사합니다.
1. 토큰에서 인코딩된 클레임을 읽습니다(선택 사항).
1. 이는 HTTP 요청에 응답합니다. 

### <a name="app-registration-overview"></a>앱 등록 개요

앱이 Azure AD B2C에 로그인하고 웹 API를 호출할 수 있도록 하려면 Azure AD B2C 디렉터리에 두 개의 애플리케이션을 등록해야 합니다.  

- *웹 혹은 모바일 또는 단일 페이지 어플리케이션* 등록을 사용하면 앱이 Azure AD B2C로 로그인할 수 있습니다. 앱 등록 프로세스는 *클라이언트 ID* 라고도 불리는 앱을 고유하게 식별하는 는 *애플리케이션 ID* 를 생성합니다(예시 *앱 ID: 1*).

- *웹 API* 등록을 사용하면 앱에서 보안 웹 API를 호출할 수 있습니다. 등록은 웹 API 권한(범위)을 노출합니다. 앱 등록 프로세스는 웹 API를 고유하게 식별하는 *애플리케이션 ID* 를 생성합니다(예시 *앱 ID: 2*). 웹 API 범위 (앱 ID: 2)에 앱 (앱 ID: 1) 사용 권한을 부여 합니다. 

응용 프로그램 등록 및 응용 프로그램 아키텍처는 다음 다이어그램에 설명 되어 있습니다.

![웹 API를 사용 하는 앱의 응용 프로그램 등록 및 응용 프로그램 아키텍처에 대한 다이어그램입니다.](./media/enable-authentication-web-api/app-with-api-architecture.png) 

## <a name="prepare-your-development-environment"></a>개발 환경 준비  

다음 섹션에서는 새로운 웹 API 프로젝트를 만듭니다. ASP.NET Core 또는 Node.js 프로그래밍 언어를 선택 합니다. 다음 중 하나를 실행하는 컴퓨터가 있는지 확인 합니다. 

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [Visual Studio Code용 C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) (최신 버전)
* [.NET 5.0 SDK](https://dotnet.microsoft.com/download/dotnet)

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

* [Visual Studio Code](https://code.visualstudio.com/) 또는 다른 코드 편집기
* [Node.js 런타임](https://nodejs.org/en/download/)

---

## <a name="step-1-create-a-protected-web-api"></a>1 단계: 보호된 웹 API 만들기

새 웹 API 프로젝트 만들기. 먼저 **ASP.NET Core** 또는 **Node.js** 중 사용 하려는 프로그래밍 언어를 선택합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

[`dotnet new`](/dotnet/core/tools/dotnet-new) 명령 사용 `dotnet new`명령은 웹 API 프로젝트 자산을 사용 하 여 *TodoList* 라는 새 폴더를 만듭니다. [Visual Studio Code](https://code.visualstudio.com/)에서 디렉터리를 엽니다. 

```dotnetcli
dotnet new webapi -o TodoList
cd TodoList
code . 
```

‘프로젝트에 필요한 자산을 추가’하라는 메시지가 표시되면 **예** 를 선택 합니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

[Node.JS](https://nodejs.org/)로 웹 API 컴파일하기 위해서는 [기본](https://expressjs.com/)을 사용합니다. 웹 API를 만들려면 다음을 수행합니다.

1. *TodoList* 라는 새 폴더를 만듭니다. 
1. *TodoList* 폴더에서 *app.js* 라는 파일을 만듭니다.
1. 명령 셸에서 실행합니다`npm init -y`. 이 명령은 Node.js 프로젝트에 대한 기본 *package.json* 파일을 만듭니다.
1. 명령 셸에서 `npm install express`를 실행합니다. 이 명령은 Express 프레임워크를 설치합니다.

--- 

## <a name="step-2-install-the-dependencies"></a>두 번째 단계에서 종속성을 설치합니다.

웹 API 프로젝트에 인증 라이브러리를 추가 합니다. 인증 라이브러리는 HTTP 인증 헤더를 구문 분석하고, 토큰의 유효성을 검사하 고, 클레임을 추출합니다. 자세한 내용은 라이브러리를 위한 설명서를 검토하세요.


# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

인증 라이브러리를 추가 하려면 다음 명령을 실행하여 패키지를 설치합니다.

```dotnetcli
dotnet add package Microsoft.Identity.Web
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

인증 라이브러리를 추가 하려면 다음 명령을 실행하여 패키지를 설치합니다.

```
npm install passport
npm install passport-azure-ad
npm install morgan
```
 
[morgen package](https://www.npmjs.com/package/morgan)는 Node.js에 대한 HTTP 요청 로거 미들웨어입니다.

---

## <a name="step-3-initiate-the-authentication-library"></a>세 번째 단계는 인증 라이브러리 시작하는 것입니다.

인증 라이브러리를 시작하는 데 필요한 코드를 추가합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

*Startup.cs* 를 열고 클래스의 시작 부분에 다음 `using`선언을 추가합니다.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Identity.Web;
```

`ConfigureServices(IServiceCollection services)` 함수를 찾습니다. 그런 다음 `services.AddControllers();`코드 줄 앞에 다음 코드 조각을 추가 합니다.


```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Adds Microsoft Identity platform (Azure AD B2C) support to protect this Api
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAdB2C", options);

        options.TokenValidationParameters.NameClaimType = "name";
    },
    options => { Configuration.Bind("AzureAdB2C", options); });
    // End of the Microsoft Identity platform block    

    services.AddControllers();
}
```

`Configure` 함수를 찾습니다. 그 후, `app.UseRouting();`코드 줄 바로 뒤에 다음 코드 조각을 추가합니다.


```csharp
app.UseAuthentication();
```

변경 후에 코드는 다음 코드 조각과 같아야 합니다.

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseHttpsRedirection();

    app.UseRouting();
    
    // Add the following line 
    app.UseAuthentication();
    // End of the block you add
    
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

다음 JavaScript 코드를 *app.js* 파일에 추가합니다.

```javascript
// Import the required libraries
const express = require('express');
const morgan = require('morgan');
const passport = require('passport');
const config = require('./config.json');

// Import the passport Azure AD library
const BearerStrategy = require('passport-azure-ad').BearerStrategy;

// Set the Azure AD B2C options
const options = {
    identityMetadata: `https://${config.credentials.tenantName}.b2clogin.com/${config.credentials.tenantName}.onmicrosoft.com/${config.policies.policyName}/${config.metadata.version}/${config.metadata.discovery}`,
    clientID: config.credentials.clientID,
    audience: config.credentials.clientID,
    issuer: config.credentials.issuer,
    policyName: config.policies.policyName,
    isB2C: config.settings.isB2C,
    scope: config.resource.scope,
    validateIssuer: config.settings.validateIssuer,
    loggingLevel: config.settings.loggingLevel,
    passReqToCallback: config.settings.passReqToCallback
}

// Instantiate the passport Azure AD library with the Azure AD B2C options
const bearerStrategy = new BearerStrategy(options, (token, done) => {
        // Send user info using the second argument
        done(null, { }, token);
    }
);

// Use the required libraries
const app = express();

app.use(morgan('dev'));

app.use(passport.initialize());

passport.use(bearerStrategy);

//enable CORS (for testing only -remove in production/deployment)
app.use((req, res, next) => {
    res.header('Access-Control-Allow-Origin', '*');
    res.header('Access-Control-Allow-Headers', 'Authorization, Origin, X-Requested-With, Content-Type, Accept');
    next();
});
```
--- 

## <a name="step-4-add-the-endpoints"></a>네 번째 단계에서 엔드포인트를 추가합니다.

웹 API에 두 개의 엔드포인트들을 추가합니다.

- 익명의 `/public`엔드포인트 이 엔드포인트는 현재 날짜 및 시간을 반환합니다. 익명 호출을 사용하여 웹 API를 디버깅 하는 데 사용합니다.
- 보호된 `/hello`엔드포인트 이 엔드포인트는 `name`액세스 토큰 내의 클레임 값을 반환 합니다.

**익명 엔드포인트를 추가 하려면**

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

*/컨트롤러* 폴더 하위에 *PublicController.cs* 파일을 추가하고 다음 코드 조각을 추가합니다.

```csharp
using System;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;

namespace TodoList.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class PublicController : ControllerBase
    {
        private readonly ILogger<PublicController> _logger;

        public PublicController(ILogger<PublicController> logger)
        {
            _logger = logger;
        }

        [HttpGet]
        public ActionResult Get()
        {
            return Ok( new {date = DateTime.UtcNow.ToString()});
        }
    }
}
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

*app.js* 파일에서 다음 JavaScript 코드를 추가합니다.


```javascript
// API anonymous endpoint
app.get('/public', (req, res) => res.send( {'date': new Date() } ));
```

--- 

**보호 된 엔드포인트를 추가 하려면**

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

*/컨트롤러* 폴더에서 *HelloController.cs* 파일을 추가한 후에 다음 코드를 추가합니다.

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Microsoft.Identity.Web.Resource;

namespace TodoList.Controllers
{
    [Authorize]
    [RequiredScope("tasks.read")]
    [ApiController]
    [Route("[controller]")]
    public class HelloController : ControllerBase
    {

        private readonly ILogger<HelloController> _logger;
        private readonly IHttpContextAccessor _contextAccessor;

        public HelloController(ILogger<HelloController> logger, IHttpContextAccessor contextAccessor)
        {
            _logger = logger;
            _contextAccessor = contextAccessor;
        }

        [HttpGet]
        public ActionResult Get()
        {
            return Ok( new { name = User.Identity.Name});
        }
    }
}
```

`HelloController` 컨트롤러는 인증 된 사용자 에게만 액세스를 제한하는 [AuthorizeAttribute](/aspnet/core/security/authorization/simple)로 데코레이트 됩니다. 

또한 컨트롤러도 `[RequiredScope("tasks.read")]`로 데코레이트 됩니다. [RequiredScopeAttribute](/dotnet/api/microsoft.identity.web.resource.requiredscopeattribute.-ctor) 는 올바른 범위를 사용하여 웹 API가 호출 되었는지 확인`tasks.read` 합니다. 

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

*app.js* 파일에서 다음 JavaScript 코드를 추가합니다. 

```javascript
// API protected endpoint
app.get('/hello',
    passport.authenticate('oauth-bearer', {session: false}),
    (req, res) => {
        console.log('Validated claims: ', req.authInfo);
        
        // Service relies on the name claim.  
        res.status(200).json({'name': req.authInfo['name']});
    }
);
```

함수는 `/hello`엔드포인트가 먼저 호출합니다`passport.authenticate()`. 인증 함수는 인증된 사용자에 대한 액세스만 제한합니다. 

또한 인증 함수는 웹 API가 올바른 범위로 호출되는지 확인합니다. 허용된 범위는 [구성 파일](#step-6-configure-the-web-api)에 있습니다. 

--- 

## <a name="step-5-configure-the-web-server"></a>다섯 번째 단계에서는 웹 서버 구성을 합니다.

개발 환경에서 들어오는 HTTP 또는 HTTPS 요청 포트 번호에서 수신 대기하도록 웹 API를 설정합니다. 이 예제에서는 HTTP 포트 6000 및 HTTPS 포트 6001을 사용합니다. 웹 API의 기본 URI는 HTTP에 대해서 `http://localhost:6000`이며 HTTPS에 대해서 `https://localhost:6001`입니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

JSON 코드 조각을 *appSettings.json* 파일에 추가합니다. 

```json
"Kestrel": {
    "EndPoints": {
      "Http": {
        "Url": "http://localhost:6000"
      },
      "Https": {
         "Url": "https://localhost:6001"   
        }
    }
  }
```

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

다음 JavaScript 코드를 *app.js* 파일에 추가합니다. [노드 애플리케이션에 대한 HTTP 및 HTTPS 엔드포인트를 설정](https://github.com/expressjs/express/wiki/Migrating-from-2.x-to-3.x#application-function)할 수 있습니다. 

```javascript
// Starts listening on port 6000
const port = process.env.PORT || 6000;

app.listen(port, () => {
    console.log('Listening on port ' + port);
});
```

---

## <a name="step-6-configure-the-web-api"></a>여섯 번째 단계에서는 웹 API를 구성합니다.

구성 파일에 구성을 추가합니다. 이 파일에는 Azure AD B2C ID 공급자에 대한 정보가 포함되어 있습니다. 웹 API 앱은 이 정보를 사용하여 웹앱이 전달자 토큰으로 전달하는 액세스 토큰의 유효성을 검사합니다.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

프로젝트 루트 폴더에서 *appsettings.json* 파일을 열고 다음 설정을 추가합니다.

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-api-app-application-id>",
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  // More settings here
}
```

*appsettings.json* 파일에서, 다음 속성을 수정합니다. 

|섹션  |Key  |값  |
|---------|---------|---------|
|AzureAdB2C|인스턴스| Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)의 첫 번째 부분입니다.(예를 들어 `https://contoso.b2clogin.com`)|
|AzureAdB2C|도메인| Azure AD B2C 테넌트 전체 [테넌트 이름](tenant-management.md#get-your-tenant-name)입니다. (예를 들어 `contoso.onmicrosoft.com`)|
|AzureAdB2C|clientid| 웹 API 애플리케이션 ID입니다. [위의 다이어그램](#app-registration-overview)에서 애플리케이션은 *앱 ID: 2* 가 있습니다. 웹 API 애플리케이션 등록 ID를 얻는 방법을 알아보려면 [필수 요구 사항](#prerequisites)을 참조하세요. |
|AzureAdB2C|SignUpSignInPolicyId|사용자 흐름 또는 사용자 지정 정책입니다. 사용자 흐름 또는 정책을 얻는 방법을 알아보려면 [필수 요구 사항](#prerequisites)을 참조하세요.  |

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

프로젝트 루트 폴더에서 *config.json* 파일을 만들고 JSON 코드 조각에 추가합니다.  

```json
{
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>",
        "issuer": "https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"]
    },
    "metadata": {
        "discovery": ".well-known/openid-configuration",
        "version": "v2.0"
    },
    "settings": {
        "isB2C": true,
        "validateIssuer": true,
        "passReqToCallback": false,
        "loggingLevel": "info"
    }
}
```

*config.json* 파일에는 다음 속성을 업데이트 합니다.

|섹션  |Key  |값  |
|---------|---------|---------|
| 자격 증명 | tenantName | Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)의 첫 번째 부분입니다.(예를 들어 `contoso`)|
| 자격 증명 |clientID | 웹 API 애플리케이션 ID입니다. [위의 다이어그램](#app-registration-overview)에서 애플리케이션은 *앱 ID: 2* 가 있습니다. 웹 API 애플리케이션 등록 ID를 얻는 방법을 알아보려면 [필수 요구 사항](#prerequisites)을 참조하세요. |
| 자격 증명 | 발급자| 토큰 발급자가 값을 `iss` 클레임 합니다. 기본적으로 Azure AD B2C `https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/` 형식으로 토큰을 반환합니다. Azure AD B2C [테넌트 이름](tenant-management.md#get-your-tenant-name)의 첫번째 부분을 `<your-tenant-name>`로 바꿉니다. `<your-tenant-ID>`을 [Azure AD B2C 테넌트 ID](tenant-management.md#get-your-tenant-id)로 바꿉니다. |
| 정책 | policyName | 사용자 흐름 또는 사용자 지정 정책입니다. 사용자 흐름 또는 정책을 얻는 방법을 알아보려면 [필수 요구 사항](#prerequisites)을 참조하세요.|
| resource | scope | 웹 API 애플리케이션 등록의 범위입니다. 웹 API 범위를 얻는 방법을 알아보려면 [필수 구성 요소](#prerequisites)를 알아보세요.|

---

## <a name="step-7-run-and-test-the-web-api"></a>일곱 번째 단계는 웹 API를 실행 및 테스트합니다.

마지막으로 Azure AD B2C 환경 설정을 통해 웹 API를 실행합니다. 

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

명령 셸에서 다음 명령을 실행하여 웹앱을 시작합니다.

```bush
 dotnet run
```

앱이 실행 중이고 요청을 받을 준비가 되었다는 의미의 다음 출력이 표시됩니다.

```
Now listening on: http://localhost:6000
```

프로그램을 중지하려면 명령 셸에서 Ctrl+C를 선택합니다. `node app.js`명령을 사용하여 앱을 다시 실행하면 됩니다.

> [!TIP]
> 또는 `dotnet run`명령을 실행하려면 [Visual Studio Code 디버거](https://code.visualstudio.com/docs/editor/debugging)를 사용할 수 있습니다. Visual Studio Code의 기본 제공 디버거는 편집, 컴파일 및 디버그 루프를 가속화하는데 도움이 됩니다.

브라우저를 열고 `http://localhost:6000/public`으로 이동합니다. 브라우저 창에 현재 날짜 및 시간과 함께 다음 텍스트가 표시됩니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)

명령 셸에서 다음 명령을 실행하여 웹앱을 시작합니다.

```bush
node app.js
```

앱이 실행 중이고 요청을 받을 준비가 되었다는 의미의 다음 출력이 표시됩니다.

```
Example app listening on port 6000!
```

프로그램을 중지하려면 명령 셸에서 Ctrl+C를 선택합니다. `node app.js`명령을 사용하여 앱을 다시 실행하면 됩니다.

> [!TIP]
> 또는 `node app.js`명령을 실행하려면 [Visual Studio Code 디버거](https://code.visualstudio.com/docs/editor/debugging)를 사용할 수 있습니다. Visual Studio Code의 기본 제공 디버거는 편집, 컴파일 및 디버그 루프를 가속화하는데 도움이 됩니다.

브라우저를 열고 `http://localhost:6000/public`으로 이동합니다. 브라우저 창에 현재 날짜 및 시간과 함께 다음 텍스트가 표시됩니다.

---

## <a name="step-8-call-the-web-api-from-your-app"></a>여덟 번째 단계는 앱에서 웹 API 호출을 합니다.

액세스 토큰 없이 보호된 웹 API 엔드포인트를 호출해 보세요. 브라우저를 열고 `http://localhost:6000/hello`으로 이동합니다. API는 권한 없는 HTTP 오류 메시지를 반환하여 웹 API가 전달자 토큰이 보호하고 있는 것을 확인합니다.

웹 API를 호출하도록 앱을 계속 구성합니다. 자세한 내용은 [필수 요구 사항](#prerequisites) 섹션을 참조하세요.

이 비디오를 시청하여 Azure AD B2C가 API와 통합하는 몇 가지 모범 사례에 대해 알아보세요.

>[!Video https://www.youtube.com/embed/wuUu71RcsIo]

## <a name="next-steps"></a>다음 단계

GitHub에서 전체 예제를 얻으세요.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)
* [Microsoft ID 라이브러리](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C/TodoListService)를 사용하여 웹 API를 얻습니다.

# <a name="nodejs"></a>[Node.JS](#tab/nodejsgeneric)
* [Passport.js 라이브러리](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)를 사용하여 웹 API를 얻습니다.
