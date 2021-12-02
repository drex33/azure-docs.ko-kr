---
title: Azure 지도 웹 맵 컨트롤을 사용하는 방법
titleSuffix: Microsoft Azure Maps
description: Azure 지도 맵 컨트롤 클라이언트 쪽 JavaScript 라이브러리를 사용하여 웹 및 모바일 애플리케이션에 맵을 추가하고 지역화하는 방법을 알아봅니다.
author: stevemunk
ms.author: v-munksteve
manager: jokebeck
ms.date: 11/29/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: 2a6c2101bdfc59dcd32a6b6dc4a751dc0d2589b2
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133486602"
---
# <a name="use-the-azure-maps-map-control"></a>Azure Maps 맵 컨트롤 사용

맵 컨트롤 클라이언트 측 JavaScript 라이브러리를 사용하면 맵 및 포함된 Azure Maps 기능을 웹 또는 모바일 애플리케이션에 렌더링할 수 있습니다.

본 문서에서는 Azure Maps Web SDK를 사용하지만 Azure Maps 서비스는 모든 맵 컨트롤에서 사용할 수 있습니다. Azure Maps 팀에서 플러그 인을 만든 몇 가지 인기 있는 오픈 소스 맵 컨트롤은 [다음](open-source-projects.md#third-part-map-control-plugins)과 같습니다.

## <a name="prerequisites"></a>사전 요구 사항

웹 페이지에서 맵 컨트롤을 사용하려면 다음 필수 조건 중 하나가 있어야 합니다.

* [Azure Maps 계정을 만들고](quick-demo-map-app.md#create-an-azure-maps-account) 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).

* [인증 옵션](/javascript/api/azure-maps-control/atlas.authenticationoptions)을 사용하여 AAD(Azure Active Directory) 자격 증명을 가져옵니다.

## <a name="create-a-new-map-in-a-web-page"></a>웹 페이지에 새 맵 만들기

맵 컨트롤 클라이언트 쪽 JavaScript 라이브러리를 사용하여 웹 페이지에 맵을 포함할 수 있습니다.

1. 새 HTML 파일 만들기

2. Azure Maps 웹 SDK에서 로드합니다. 다음 두 가지 옵션 중 하나를 선택하세요.

    * HTML 파일의 `<head>` 요소에서 JavaScript 및 스타일 시트에 대한 참조를 추가하여 Azure Maps 웹 SDK의 전역적으로 호스트된 CDN 버전을 사용합니다.

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      ```

    * [azure-maps-control](https://www.npmjs.com/package/azure-maps-control) NPM 패키지를 사용하여 로컬에서 Azure Maps 웹 SDK 소스 코드를 로드하여 앱으로 호스트합니다. 이 패키지에는 TypeScript 정의도 포함됩니다.

      > **npm install azure-maps-control**

    그런 다음 파일의 `<head>` 요소에 Azure Maps 스타일 시트에 대한 참조를 추가합니다.

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
     ```

    > [!NOTE]
    > 다음 코드를 추가하여 Typescript 정의를 애플리케이션으로 가져올 수 있습니다.
    >
    > ```javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. 맵이 페이지 본문 전체를 채우도록 렌더링하려면 `<head>` 요소에 다음 `<style>` 요소를 추가합니다.

   ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
   ```

4. 페이지 본문에 `<div>` 요소를 추가하고 **myMap** 이라는 `id`를 부여합니다.

   ```HTML
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
   ```

5. 이제 맵 컨트롤을 초기화합니다. 컨트롤을 인증하려면 Azure Maps 구독 키를 소유하거나 [인증 옵션](/javascript/api/azure-maps-control/atlas.authenticationoptions)과 함께 AAD(Azure Active Directory) 자격 증명을 사용해야 합니다.

    인증을 위해 구독 키를 사용하는 경우에는 다음 스크립트 요소를 복사하여 `<head>` 요소 내, 그리고 첫 번째 `<script>` 요소 아래에 붙여넣습니다. `<Your Azure Maps Key>`를 Azure Maps 기본 구독 키로 바꿉니다.

    ```html
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
       }
    </script>
    ```

    인증을 위해 AAD(Azure Active Directory)를 사용하는 경우에는 다음 스크립트 요소를 복사하여 `<head>` 요소 내, 그리고 첫 번째 `<script>` 요소 아래에 붙여넣습니다.

      ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'aad',
                    clientId: '<Your AAD Client Id>',
                    aadAppId: '<Your AAD App Id>',
                    aadTenant: '<Your AAD Tenant Id>'
                }
            });
        }
    </script>
   ```

    Azure Maps 인증에 대한 자세한 내용은 [Azure Maps 인증](azure-maps-authentication.md) 문서를 참조하세요. 또한 Azure Maps에 AAD(Azure Active Directory)를 통합하는 방법을 보여 주는 샘플 목록은 [여기](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)에서 찾을 수 있습니다.

    >[!TIP]
    >이 예제에서는 맵 `<div>`의 `id`에서 전달했습니다. 이 작업을 수행하는 또 다른 방법은 `document.getElementById('myMap')`를 첫 번째 매개 변수로 전달하여 `HTMLElement` 개체에서 전달하는 것입니다.

6. 필요에 따라 페이지의 `head` 요소에 다음 `meta` 요소를 추가하는 것이 유용할 수 있습니다.

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. HTML 파일을 모두 모으면 다음 표시와 같은 모양입니다.

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>


        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            function InitMap()
            {
                var map = new atlas.Map('myMap', {
                    center: [-122.33, 47.6],
                    zoom: 12,
                    language: 'en-US',
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });
            }
        </script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

8. 웹 브라우저에서 파일을 열고 렌더링된 맵을 봅니다. 다음 이미지와 같아야 합니다.

   ![렌더링된 결과를 표시하는 맵 이미지](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>맵 지역화

Azure Maps는 렌더링된 맵의 언어 및 지역 보기를 설정하는 두 가지 방법을 제공합니다. 첫 번째 옵션은 이 정보를 전체 `atlas` 네임스페이스에 추가하는 것입니다. 그러면 앱의 모든 맵 컨트롤 인스턴스가 기본적으로 이 설정으로 지정됩니다. 다음은 언어를 프랑스어("fr-FR")로 설정하고 지역 보기를 "Auto"로 설정합니다.

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

두 번째 옵션은 다음과 같이 맵을 로드할 때 맵 옵션에 이 정보를 전달하는 것입니다.

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!NOTE]
> 다른 언어 및 지역 설정을 사용하여 동일한 페이지에 여러 개의 맵 인스턴스를 로드할 수 있습니다. 또한 맵의 `setStyle` 함수를 사용하여 맵이 로드된 후 해당 설정을 업데이트할 수 있습니다.

다음은 언어가 “fr-FR”로 설정되고 지역 보기가 “Auto”로 설정된 Azure Maps의 예입니다.

![프랑스어로 레이블을 표시하는 맵 이미지](./media/how-to-use-map-control/websdk-localization.png)

지원되는 언어 및 지역 보기의 전체 목록은 [여기](supported-languages.md)에 설명되어 있습니다.

## <a name="azure-government-cloud-support"></a>Azure Government 클라우드 지원

Azure Maps 웹 SDK는 Azure Government 클라우드를 지원합니다. Azure Maps 웹 SDK에 액세스하는 데 사용되는 모든 JavaScript 및 CSS URL은 동일하게 유지됩니다. Azure Maps 플랫폼의 Azure Government 클라우드 버전에 연결하려면 다음 작업을 수행해야 합니다.

대화형 맵 컨트롤을 사용하는 경우 `Map` 클래스의 인스턴스를 만들기 전에 다음 코드 줄을 추가합니다.

```javascript
atlas.setDomain('atlas.azure.us');
```

맵 및 서비스 인증 시 Azure Government 클라우드 플랫폼에서 Azure Maps 인증 세부 정보를 사용해야 합니다.

서비스 모듈을 사용하는 경우에는 API URL 엔드포인트의 인스턴스를 만들 때 서비스에 대한 도메인을 설정해야 합니다. 예를 들어 다음 코드는 `SearchURL` 클래스의 인스턴스를 만들고 도메인을 Azure Government 클라우드로 가리킵니다.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Azure Maps REST 서비스에 직접 액세스하는 경우 URL 도메인을 `atlas.azure.us`로 변경합니다. 예를 들어 검색 API 서비스를 사용하는 경우 URL 도메인을 `https://atlas.microsoft.com/search/`에서 `https://atlas.azure.us/search/`로 변경합니다.

## <a name="javascript-frameworks"></a>JavaScript 프레임워크

JavaScript 프레임워크를 사용하여 개발하는 경우 다음 오픈 소스 프로젝트 중 하나가 유용할 수 있습니다.

* [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps) - Azure 맵 주변의 Angular 10 래퍼.
* [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) - Azure Maps Blazor 구성 요소.
* [Azure Maps React 구성 요소](https://github.com/WiredSolutions/react-azure-maps) - Azure Maps 컨트롤의 반응 래퍼.
* [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps) - Vue 애플리케이션용 Azure Maps 구성 요소.

## <a name="next-steps"></a>다음 단계

맵을 만들고 상호 작용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [맵 만들기](map-create.md)

맵 스타일을 지정하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [지도 스타일 선택](choose-map-style.md)

모범 사례를 알아보고 샘플을 참조하세요.

> [!div class="nextstepaction"]
> [모범 사례](web-sdk-best-practices.md)

> [!div class="nextstepaction"]
> [코드 샘플](/samples/browse/?products=azure-maps)

Azure Maps에 AAD(Azure Active Directory)를 통합하는 방법을 보여 주는 샘플 목록은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure AD 인증 샘플](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
