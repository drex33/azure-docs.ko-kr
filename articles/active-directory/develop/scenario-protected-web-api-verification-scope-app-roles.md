---
title: 범위 및 앱 역할 확인 보호된 웹 API | Azure
titleSuffix: Microsoft identity platform
description: API가 올바른 범위를 가진 사용자와 적절한 애플리케이션 역할이 있는 디먼 앱을 대신하여 애플리케이션에서만 호출되는지 확인합니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2021
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 23b9f2e8bb14d74a7f2b722945251acb182b71db
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222574"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>보호된 웹 API: 범위 및 앱 역할 확인

이 문서에서는 웹 API에 권한 부여를 추가할 수 있는 방법을 설명합니다. 이 보호는 API가 다음에 의해서만 호출되도록 합니다.

- 올바른 범위를 가진 사용자를 대신하는 애플리케이션
- 올바른 애플리케이션 역할이 있는 디먼 앱

이 문서의 코드 조각은 GitHub의 다음 코드 샘플에서 추출됩니다.

- [ASP.NET Core 웹 API 증분 자습서](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
- [ASP.NET Web API 샘플](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

ASP.NET 또는 ASP.NET Core 웹 API를 보호하려면 `[Authorize]` 특성을 다음 항목 중 하나에 추가해야 합니다.

- 모든 컨트롤러 작업을 보호하려는 경우 컨트롤러 자체
- API에 대한 개별 컨트롤러 작업

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     // ...
    }
```

그러나 이 보호는 충분하지 않습니다. ASP.NET 및 ASP.NET Core가 토큰의 유효성을 검사한다는 것만 보장합니다. API는 API를 호출하는 데 사용되는 토큰이 예상된 클레임으로 요청되는지 확인해야 합니다. 특히 다음과 같은 클레임을 확인해야 합니다.

- 사용자를 대신하여 API를 호출하는 경우 ‘범위’
- 디먼 앱에서 API를 호출할 수 있는 경우 ‘앱 역할’

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>사용자를 대신하여 호출된 API의 범위 확인

클라이언트 앱이 사용자를 대신하여 API를 호출하는 경우 API는 API에 대한 특정 범위를 가진 전달자 토큰을 요청해야 합니다. 자세한 내용은 [코드 구성 | 전달자 토큰](scenario-protected-web-api-app-configuration.md#bearer-token)을 참조하세요.

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core에서 Microsoft.Identity.Web을 사용하여 각 컨트롤러 작업의 범위를 확인할 수 있습니다. 컨트롤러 수준이나 전체 애플리케이션의 범위도 확인할 수 있습니다.

#### <a name="verify-the-scopes-on-each-controller-action"></a>각 컨트롤러 작업의 범위 확인

`[RequiredScope]` 특성을 사용하여 컨트롤러 작업의 범위를 확인할 수 있습니다. 이 특성에는 몇 가지 재정의가 있습니다. 필수 범위를 직접 사용하는 재정의, 구성에 대한 키를 사용하는 재정의도 있습니다.

##### <a name="verify-the-scopes-on-a-controller-action-with-hardcoded-scopes"></a>하드 코드된 범위가 있는 컨트롤러 작업의 범위 확인

다음 코드 조각은 하드 코드된 범위가 있는 `[RequiredScope]` 특성을 사용하는 방법을 보여 줍니다.

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    [RequiredScope(scopeRequiredByApi)]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-the-scopes-on-a-controller-action-with-scopes-defined-in-configuration"></a>구성에서 범위가 정의된 컨트롤러 작업의 범위 확인

구성에서 다음과 같은 필수 범위를 선언하고 구성 키를 참조할 수도 있습니다.

예를 들어 appsettings.json에 다음 구성이 있다고 가정합니다.

```JSon
{
 "AzureAd" : {
   // more settings
   "Scopes" : "access_as_user access_as_admin"
  }
}
```

이 경우 `[RequiredScope]` 특성에서 구성을 참조합니다.

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    // GET: api/values
    [HttpGet]
    [RequiredScope(RequiredScopesConfigurationKey = "AzureAd:Scopes")]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-scopes-conditionally"></a>조건부로 범위 확인

조건부로 범위를 확인하려는 경우도 있습니다. `HttpContext`의 `VerifyUserHasAnyAcceptedScope` 확장 메서드를 사용하면 됩니다.

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

#### <a name="verify-the-scopes-at-the-level-of-the-controller"></a>컨트롤러 수준에서 범위 확인

전체 컨트롤러의 범위도 확인할 수 있습니다.

##### <a name="verify-the-scopes-on-a-controller-with-hardcoded-scopes"></a>하드 코드된 범위가 있는 컨트롤러의 범위 확인

다음 코드 조각은 컨트롤러에서 하드 코드된 범위가 있는 `[RequiredScope]` 특성을 사용하는 방법을 보여 줍니다.

```csharp
using Microsoft.Identity.Web

[Authorize]
[RequiredScope(scopeRequiredByApi)]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

##### <a name="verify-the-scopes-on-a-controller-with-scopes-defined-in-configuration"></a>구성에서 범위가 정의된 컨트롤러의 범위 확인

작업과 마찬가지로 구성에서 다음과 같은 필수 범위를 선언하고 구성 키를 참조할 수도 있습니다.

```csharp
using Microsoft.Identity.Web

[Authorize]
[RequiredScope(RequiredScopesConfigurationKey = "AzureAd:Scopes")
public class TodoListController : Controller
{
    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        // Do the work and return the result.
        // ...
    }
 // ...
}
```

#### <a name="verify-the-scopes-more-globally"></a>더 전역적으로 범위 확인

웹 API에 대해 세분화된 범위를 정의하고 각 컨트롤러 작업에서 범위를 확인하는 것이 좋습니다. 그러나 애플리케이션이나 컨트롤러 수준에서 범위를 확인할 수도 있습니다. 자세한 내용은 ASP.NET Core 설명서의 [클레임 기반 권한 부여](/aspnet/core/security/authorization/claims)를 참조하세요.

#### <a name="what-is-verified"></a>확인되는 항목

`[RequiredScope]` 특성과 `VerifyUserHasAnyAcceptedScope` 메서드는 다음 단계와 같은 작업을 수행합니다.

- `http://schemas.microsoft.com/identity/claims/scope` 또는 `scp`라는 클레임이 있는지 확인합니다.
- 클레임에 API에서 예상하는 범위를 포함하는 값이 있는지 확인합니다.

### <a name="aspnet-classic"></a>[ASP.NET 클래식](#tab/aspnet)

ASP.NET 애플리케이션에서 다음과 같은 방식으로 범위의 유효성을 검사할 수 있습니다.

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
       ValidateScopes(new[] {"read"; "admin" } );
       // ...
    }
```

다음은 `ValidateScopes`의 간소화된 버전입니다.

```csharp
private void ValidateScopes(IEnumerable<string> acceptedScopes)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim scopeClaim = ClaimsPrincipal.Current.FindFirst("scp");
    if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Forbidden,
            ReasonPhrase = $"The 'scp' claim does not contain '{scopeClaim}' or was not found"
        });
    }
}
```

ASP.NET Core용 `ValidateScopes`의 전체 버전은 [_ScopesRequiredHttpContextExtensions.cs_](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/ScopesRequiredHttpContextExtensions.cs)입니다.

---

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>디먼 앱에서 호출된 API의 앱 역할 확인

웹 API가 [디먼 앱](scenario-daemon-overview.md)에 의해 호출되는 경우 해당 앱에서 웹 API에 대한 애플리케이션 권한을 요구해야 합니다. [애플리케이션 사용 권한(앱 역할) 노출](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles)에 나온 것처럼 API는 이러한 사용 권한을 노출합니다. 한 가지 예는 `access_as_application` 앱 역할입니다.

이제 API에서 받은 토큰에 `roles` 클레임이 포함되어 있고 이 클레임에 예상한 값이 있는지 확인하도록 해야 합니다. 확인 코드는 컨트롤러 작업이 범위 대신 역할을 테스트한다는 것을 제외하면 위임된 권한을 확인하는 코드와 비슷합니다.

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

다음 코드 조각은 애플리케이션 역할을 확인하는 방법을 보여 줍니다.

```csharp
using Microsoft.Identity.Web

[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        // ...
    }
```

대신, 컨트롤러나 작업(또는 razor 페이지)의 [Authorize(“role”)] 특성을 사용할 수 있습니다.

```CSharp
[Authorize("role")]
MyController : ApiController
{
    // ...
}
```

그러나 이 경우에는 Startup.cs 파일의 “roles”에 역할 클레임을 매핑해야 합니다.

```CSharp
 services.Configure<OpenIdConnectOptions>(OpenIdConnectDefaults.AuthenticationScheme, options =>
 {
    // The claim in the Jwt token where App roles are available.
    options.TokenValidationParameters.RoleClaimType = "roles";
 });
```

그룹에 따라 권한 부여도 수행해야 하는 경우 최상의 솔루션은 아닙니다.

자세한 내용은 [역할과 그룹으로 권한 부여](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ)에 대한 웹앱 증분 자습서를 참조하세요.

### <a name="aspnet-classic"></a>[ASP.NET 클래식](#tab/aspnet)

ASP.NET 애플리케이션에서는 다음과 같은 방식으로 앱 역할의 유효성을 검사할 수 있습니다.

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
       ValidateAppRole("access_as_application");
       // ...
    }
```

`ValidateAppRole`의 간소화된 버전은 다음과 같습니다.

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
```

ASP.NET Core용 `ValidateAppRole`의 전체 버전은 [_RolesRequiredHttpContextExtensions.cs_](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs) 코드를 참조하세요.

---

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>웹 API가 디먼 앱에 의해서만 호출되어야 하는 경우 앱 전용 토큰 수락

사용자는 사용자 할당 패턴에서 역할 클레임을 사용할 수도 있습니다. 이 내용은 [방법: 애플리케이션에 앱 역할 추가 및 토큰으로 수신](howto-add-app-roles-in-azure-ad-apps.md)에 나와 있습니다. 역할을 둘 다에 할당할 수 있는 경우 역할을 확인하면 앱은 사용자로 로그인하고 사용자는 앱으로 로그인할 수 있습니다. 이러한 혼동을 방지하기 위해 사용자와 앱에 대해 서로 다른 역할을 선언하는 것이 좋습니다.

디먼 앱만 웹 API를 호출하도록 하려면 앱 역할의 유효성을 검사할 때 토큰이 앱 전용 토큰이라는 조건을 추가합니다.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnly = oid != null && sub != null && oid == sub;
```

역 조건을 확인하면 사용자로 로그인하는 앱만 API를 호출할 수 있습니다.

### <a name="using-acl-based-authorization"></a>ACL 기반 권한 부여 사용

앱 역할 기반 권한 부여의 대안으로, ACL(액세스 제어 목록) 기반 권한 부여 패턴을 사용해서 웹 API를 보호하여 [`roles` 클레임 없이 토큰을 제어](v2-oauth2-client-creds-grant-flow.md#controlling-tokens-without-the-roles-claim)할 수 있습니다.

ASP.NET Core에서 Microsoft.Identity.Web을 사용하는 경우 ACL 기반 권한 부여를 사용 중이라고 선언해야 합니다. 그러지 않으면 제공한 클레임에 역할과 범위가 둘 다 없는 경우 Microsoft Identity Web에서 예외를 throw합니다.

```Text
System.UnauthorizedAccessException: IDW10201: Neither scope or roles claim was found in the bearer token.
```

이 예외를 방지하려면 appsettings.json에서나 프로그래매틱 방식으로 `AllowWebApiToBeAuthorizedByACL` 구성 속성을 true로 설정합니다.

```Json
{
 "AzureAD"
 {
  // other properties
  "AllowWebApiToBeAuthorizedByACL" : true,
  // other properties
 }
}
```

`AllowWebApiToBeAuthorizedByACL`을 true로 설정하는 경우 ACL 메커니즘 보장은 **사용자 책임** 입니다.

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서인 [프로덕션으로 이동](scenario-protected-web-api-production.md)으로 이동합니다.
