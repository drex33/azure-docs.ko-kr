---
title: v1.0 앱(MSAL)에 대한 범위 | Azure
description: MSAL(Microsoft 인증 라이브러리)을 사용하여 v1.0 애플리케이션에 대한 범위를 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 03926f656bd96205057703e610bfab17c144dd5e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059379"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>v1.0 토큰을 허용하는 웹 API에 대한 범위

OAuth2 권한은 개발자용 Azure AD(Azure Active Directory)(v1.0) 웹 API(리소스) 애플리케이션에서 클라이언트 애플리케이션에 공개하는 권한 범위입니다. 이러한 권한 범위를 동의를 통해 클라이언트 애플리케이션에 부여할 수 있습니다. [Azure Active Directory 애플리케이션 매니페스트 참조](reference-app-manifest.md#manifest-reference)에서 `oauth2Permissions` 관련 섹션을 참조하세요.

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>v1.0 애플리케이션의 특정 OAuth2 권한에 대한 액세스를 요청하는 범위

v1.0 애플리케이션의 특정 범위(예: Microsoft Graph API - https://graph.microsoft.com) 에 대한 토큰을 획득하려면 원하는 리소스 식별자를 해당 리소스에 대해 원하는 OAuth2 권한과 연결하여 범위를 만들어야 합니다.

예를 들어 앱 ID URI가 `ResourceId`인 v1.0 web API에 사용자를 대신하여 액세스하려면 다음과 같습니다.

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Microsoft Graph API(https:\//graph.microsoft.com/)를 사용하여 MSAL.NET Azure AD에서 읽고 쓰려면 다음 예제와 같이 범위 목록을 만듭니다.

```csharp
string ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.microsoft.com/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Azure Resource Manager API(https:\//management.core.windows.net/)에 해당하는 범위를 작성하려면 다음 범위를 요청합니다(이중 슬래시에 주의).

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Azure Resource Manager API에는 해당 대상 그룹 클레임(aud)에 슬래시가 필요하고, 범위에서 API 이름을 분리하기 위한 슬래시가 있기 때문에 슬래시를 두 개 사용합니다.

Azure AD에서 사용하는 논리는 다음과 같습니다.

- v1.0 액세스 토큰을 사용하는 ADAL(Azure AD v1.0) 엔드포인트의 경우(유일하게 가능한 경우) audio=resource
- MSAL(Microsoft ID 플랫폼)에서 v2.0 토큰을 허용하는 리소스에 대한 액세스 토큰을 요청하는 경우 `aud=resource.AppId`
- MSAL(v2.0 엔드포인트)에서 v1.0 액세스 토큰을 허용하는 리소스(위의 경우)에 대한 액세스 토큰을 요청하는 경우 Azure AD는 마지막 슬래시 앞의 모든 항목을 가져와서 리소스 식별자로 사용하여 요청된 범위에서 원하는 대상 그룹을 구문 분석합니다. 따라서 `https://database.windows.net`에서 `https://database.windows.net`이라는 대상 그룹이 필요한 경우 `https://database.windows.net//.default`라는 범위를 요청해야 합니다. GitHub 문제 [#747 참조: `Resource url's trailing slash is omitted, which caused sql auth failure`](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>v1.0 애플리케이션의 모든 권한에 대한 액세스를 요청하는 범위

v1.0 애플리케이션의 모든 정적 범위에 대한 토큰을 획득하려면 “default”를 API의 앱 ID URI에 추가합니다.

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>클라이언트 자격 증명 흐름/디먼 앱의 경우 요청하는 범위

표준 클라이언트 자격 증명 흐름의 경우 `/.default`를 사용합니다. 예들 들어 `https://graph.microsoft.com/.default`입니다.

Azure AD는 클라이언트 자격 증명 흐름에 대한 액세스 토큰에 관리자가 동의한 모든 앱 수준 권한을 자동으로 포함합니다.
