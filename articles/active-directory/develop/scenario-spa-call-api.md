---
title: 웹 API를 호출하는 단일 페이지 앱 빌드
titleSuffix: Microsoft identity platform
description: 웹 API를 호출하는 단일 페이지 애플리케이션을 빌드하는 방법을 알아봅니다
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2021
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: ac71d20088a3697315ee24bce7be544e3c0763de
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091885"
---
# <a name="single-page-application-call-a-web-api"></a>단일 페이지 애플리케이션: web API 호출

웹 API를 호출하기 전에 `acquireTokenSilent` 메서드를 호출하여 액세스 토큰을 얻거나 갱신하는 것이 좋습니다. 토큰이 있으면 보호된 웹 API를 호출할 수 있습니다.

## <a name="call-a-web-api"></a>웹 API 호출

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Microsoft Graph API와 같은 웹 API를 호출하는 HTTP 요청에서 전달자로 획득된 액세스 토큰을 사용합니다. 예를 들면 다음과 같습니다.

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL 각도 래퍼는 HTTP 인터셉터를 활용하여 자동으로 액세스 토큰을 획득하고 API에 대한 HTTP 요청에 연결합니다. 자세한 내용은 [API를 호출하기 위한 토큰 가져오기](scenario-spa-acquire-token.md)를 참조하세요.

---

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서인 [프로덕션으로 이동](scenario-spa-production.md)으로 이동합니다.
