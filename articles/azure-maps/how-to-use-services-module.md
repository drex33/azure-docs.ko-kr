---
title: Azure Maps Services 모듈 사용 | Microsoft Azure 맵
description: Azure Maps 서비스 모듈에 대해 알아봅니다. 이 도우미 라이브러리를 로드하고 사용하여 웹 또는 Node.js 애플리케이션에서 Azure Maps REST 서비스에 액세스하는 방법을 참조하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/25/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: devx-track-js
ms.openlocfilehash: 512eff61d6195ad0ff21a4d22bc4a9e633f7d109
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006718"
---
# <a name="use-the-azure-maps-services-module"></a>Azure Maps 서비스 모듈 사용

Azure Maps 웹 SDK는 *서비스 모듈* 을 제공합니다. 이 모듈은 JavaScript 또는 TypeScript를 사용하여 웹 또는 Node.js 애플리케이션에서 Azure Maps REST 서비스를 쉽게 사용할 수 있도록 지원하는 도우미 라이브러리입니다.

## <a name="use-the-services-module-in-a-webpage"></a>웹 페이지에서 서비스 모듈 사용

1. 새 HTML 파일 만들기
1. Azure Maps 서비스 모듈을 로드합니다. 다음 두 가지 방법 중 한 가지로 로드할 수 있습니다.
    - Azure Maps services 모듈의 전역적으로 호스트된 Azure Content Delivery Network 버전을 사용합니다. 파일의 `<head>` 요소에 스크립트 참조를 추가합니다.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

    - 또는 [azure-maps-rest](https://www.npmjs.com/package/azure-maps-rest) npm 패키지를 사용하여 Azure Maps 웹 SDK 소스 코드에 대한 서비스 모듈을 로컬로 로드한 다음, 앱으로 호스트합니다. 이 패키지에는 TypeScript 정의도 포함됩니다. 다음 명령을 실행합니다.

      `npm install azure-maps-rest`

      그런 다음, 파일의 `<head>` 요소에 스크립트 참조를 추가합니다.

      ```html
      <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
      ```

1. 인증 파이프라인을 만듭니다. 서비스 URL 클라이언트 엔드포인트를 초기화하려면 먼저 파이프라인을 만들어야 합니다. 사용자 고유의 Azure Maps 계정 키 또는 Azure AD(Azure Active Directory) 자격 증명을 사용하여 Azure Maps Search 서비스 클라이언트를 인증합니다. 이 예제에서는 검색 서비스 URL 클라이언트가 생성됩니다. 

    인증에 구독 키를 사용하는 경우:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    인증에 Azure AD를 사용하는 경우:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    자세한 내용은 [Azure Maps로 인증](azure-maps-authentication.md)을 참조하세요.

1. 다음 코드는 새로 만든 Azure Maps Search 서비스 URL 클라이언트를 사용하여 "1 Microsoft 방식, Redmond, WA"와 같은 주소로 지역 코드를 사용합니다. 이 코드는 `searchAddress` 함수를 사용하고 결과를 페이지 본문에 테이블로 표시합니다.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    실행 중인 전체 코드 샘플은 다음과 같습니다.

<br/>

<iframe height="500" scrolling="no" title="서비스 모듈 사용" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>서비스 모듈 사용</a> 펜을 참조하세요.
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Azure Government 클라우드 지원

Azure Maps 웹 SDK는 Azure Government 클라우드를 지원합니다. Azure Maps 웹 SDK에 액세스하는 데 사용되는 모든 JavaScript 및 CSS URL은 동일하게 유지되지만 Azure Maps 플랫폼의 Azure Government 클라우드 버전에 연결하려면 다음 작업을 수행해야 합니다.

대화형 맵 컨트롤을 사용하는 경우 `Map` 클래스의 인스턴스를 만들기 전에 다음 코드 줄을 추가합니다. 

```javascript
atlas.setDomain('atlas.azure.us');
```

맵과 서비스를 인증하는 경우 Azure Government 클라우드 플랫폼에서 Azure Maps 인증 세부 정보를 사용해야 합니다.

서비스 모듈을 사용하는 경우 API URL 엔드포인트의 인스턴스를 만들 때 서비스의 도메인을 설정해야 합니다. 예를 들어 다음 코드는 `SearchURL` 클래스의 인스턴스를 만들고 도메인을 Azure Government 클라우드로 가리킵니다.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Azure Maps REST 서비스에 직접 액세스하는 경우 URL 도메인을 `atlas.azure.us`로 변경합니다. 예를 들어 검색 API 서비스를 사용하는 경우 URL 도메인을 `https://atlas.microsoft.com/search/`에서 `https://atlas.azure.us/search/`로 변경합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [MapsURL](/javascript/api/azure-maps-rest/atlas.service.mapsurl)

> [!div class="nextstepaction"]
> [SearchURL](/javascript/api/azure-maps-rest/atlas.service.searchurl)

> [!div class="nextstepaction"]
> [RouteURL](/javascript/api/azure-maps-rest/atlas.service.routeurl)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential)

> [!div class="nextstepaction"]
> [TokenCredential](/javascript/api/azure-maps-rest/atlas.service.tokencredential)

서비스 모듈을 사용하는 코드 샘플을 더 알아보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [지도에 검색 결과 표시](./map-search-location.md)

> [!div class="nextstepaction"]
> [좌표에서 정보 가져오기](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [A에서 B로의 방향 표시](./map-route.md)