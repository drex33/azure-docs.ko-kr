---
title: Azure Static Web Apps의 사용자 정보 액세스
description: 권한 부여 공급자가 반환하는 사용자 데이터를 읽는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 10b622a707747e69beba1e2be5f989ee2bcd4804
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124804545"
---
# <a name="accessing-user-information-in-azure-static-web-apps"></a>Azure Static Web Apps의 사용자 정보 액세스

Azure 정적 Web Apps는 [직접 액세스 엔드포인트](#direct-access-endpoint) 및 [API 함수](#api-functions)를 통해 인증 관련 사용자 정보를 제공합니다.

많은 사용자 인터페이스가 사용자 인증 데이터에 크게 의존합니다. 직접 액세스 엔드포인트는 사용자 지정 함수를 구현하지 않고도 사용자 정보를 노출하는 유틸리티 API입니다. 편의성 외에도 직접 액세스 엔드포인트는 서버리스 아키텍처와 관련된 콜드 시작 지연의 영향을 받지 않습니다.

## <a name="client-principal-data"></a>클라이언트 보안 주체 데이터

클라이언트 보안 주체 데이터 개체는 사용자가 식별할 수 있는 정보를 앱에 노출합니다. 클라이언트 보안 주체 개체에는 다음과 같은 속성이 있습니다.

| 속성           | Description                                                                                                                                                                                                                                                                                                                                                        |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `identityProvider` | [ID 공급자](authentication-authorization.md)의 이름입니다.                                                                                                                                                                                                                                                                                              |
| `userId`           | 사용자에 대한 Azure Static Web Apps 관련 고유 식별자입니다. <ul><li>이 값은 앱별로 고유합니다. 예를 들어 동일한 사용자가 다른 Static Web Apps 리소스에서 다른 `userId` 값을 반환합니다.<li>이 값은 사용자의 수명 동안 지속됩니다. 동일한 사용자를 삭제하고 앱에 다시 추가하면 새 `userId`가 생성됩니다.</ul> |
| `userDetails`      | 사용자의 사용자 이름 또는 이메일 주소입니다. 일부 공급자는 [사용자의 이메일 주소](authentication-authorization.md)를 반환하는 반면 다른 공급자는 [사용자 핸들](authentication-authorization.md)을 보냅니다.                                                                                                                                                                    |
| `userRoles`        | [사용자의 할당된 역할](authentication-authorization.md)의 배열입니다.                                                                                                                                                                                                                                                                                          |

다음 예제는 샘플 클라이언트 보안 주체 개체입니다.

```json
{
  "identityProvider": "github",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "username",
  "userRoles": ["anonymous", "authenticated"]
}
```

## <a name="direct-access-endpoint"></a>직접 액세스 엔드포인트

`GET` 요청을 `/.auth/me` 경로로 보내고 클라이언트 보안 주체 데이터에 직접 액세스할 수 있습니다. 보기 상태가 권한 부여 데이터를 사용하는 경우 최상의 성능을 위해 이 방법을 사용합니다.

로그인한 사용자의 경우 응답에는 클라이언트 보안 주체 JSON 개체가 포함되어 있습니다. 인증되지 않은 사용자의 요청은 `null`을 반환합니다.

[fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> API를 통해 다음 구문을 사용하여 클라이언트 보안 주체 데이터에 액세스할 수 있습니다.

```javascript
async function getUserInfo() {
  const response = await fetch('/.auth/me');
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>API 함수

Azure Functions 백 엔드를 통해 Static Web Apps에서 사용 가능한 API 함수는 클라이언트 애플리케이션과 동일한 사용자 정보에 액세스할 수 있습니다. API는 사용자 식별 가능한 정보를 수신하는 반면 사용자가 인증되는지 또는 요구되는 역할과 일치하는지 자체적인 확인을 수행하지는 않습니다. 액세스 제어 규칙은 [`staticwebapp.config.json`](configuration.md#routes) 파일에 정의되어 있습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

클라이언트 보안 주체 데이터는 `x-ms-client-principal` 요청 헤더의 API 함수에 전달됩니다. 클라이언트 보안 주체 데이터는 직렬화된 JSON 개체를 포함하는 [Base64](https://www.wikipedia.org/wiki/Base64) 인코딩 문자열로 전송됩니다.

다음 예제 함수는 사용자 정보를 읽고 반환하는 방법을 보여줍니다.

```javascript
module.exports = async function (context, req) {
  const header = req.headers['x-ms-client-principal'];
  const encoded = Buffer.from(header, 'base64');
  const decoded = encoded.toString('ascii');

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded),
    },
  };
};
```

위의 함수 이름이 `user`라고 가정할 경우 [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> 브라우저 API를 통해 다음 구문을 사용하여 API의 응답에 액세스할 수 있습니다.

```javascript
async function getUser() {
  const response = await fetch('/api/user');
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(await getUser());
```

# <a name="c"></a>[C#](#tab/csharp)

C# 함수에서 사용자 정보는 `ClaimsPrincipal` 개체 또는 사용자 자체 지정 유형으로 역직렬화될 수 있는 `x-ms-client-principal` 헤더에서 사용할 수 있습니다. 다음 코드에서는 `ClaimsPrincipal` 인스턴스로 바뀌기 전의 중간 유형인 `ClientPrincipal`로 헤더 압축을 푸는 방법을 보여 줍니다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Security.Claims;
using System.Text;
using System.Text.Json;
using Microsoft.AspNetCore.Http;

public static class StaticWebAppsAuth
{
  private class ClientPrincipal
  {
      public string IdentityProvider { get; set; }
      public string UserId { get; set; }
      public string UserDetails { get; set; }
      public IEnumerable<string> UserRoles { get; set; }
  }

  public static ClaimsPrincipal Parse(HttpRequest req)
  {
      var principal = new ClientPrincipal();

      if (req.Headers.TryGetValue("x-ms-client-principal", out var header))
      {
          var data = header[0];
          var decoded = Convert.FromBase64String(data);
          var json = Encoding.UTF8.GetString(decoded);
          principal = JsonSerializer.Deserialize<ClientPrincipal>(json, new JsonSerializerOptions { PropertyNameCaseInsensitive = true });
      }

      principal.UserRoles = principal.UserRoles?.Except(new string[] { "anonymous" }, StringComparer.CurrentCultureIgnoreCase);

      if (!principal.UserRoles?.Any() ?? true)
      {
          return new ClaimsPrincipal();
      }

      var identity = new ClaimsIdentity(principal.IdentityProvider);
      identity.AddClaim(new Claim(ClaimTypes.NameIdentifier, principal.UserId));
      identity.AddClaim(new Claim(ClaimTypes.Name, principal.UserDetails));
      identity.AddClaims(principal.UserRoles.Select(r => new Claim(ClaimTypes.Role, r)));

      return new ClaimsPrincipal(identity);
  }
}
```

---

사용자가 로그인하면 `x-ms-client-principal` Static Web Apps 에지 노드를 통해 사용자 정보에 대한 요청에 헤더가 추가됩니다.

<sup>1</sup> Internet Explorer에서는 [fetch](https://caniuse.com/#feat=fetch) API 및 [await](https://caniuse.com/#feat=mdn-javascript_operators_await) 연산자가 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 설정 구성](application-settings.md)
