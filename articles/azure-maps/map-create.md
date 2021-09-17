---
title: Azure Maps로 맵 만들기 | Microsoft Azure Maps
description: Azure Maps 웹 SDK를 사용하여 웹 페이지에 맵을 추가하는 방법을 알아봅니다. 애니메이션, 스타일, 카메라, 서비스 및 사용자 상호 작용에 대한 옵션을 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 137381e3e08f9f2096467171e42033d2ffbd5b1b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432723"
---
# <a name="create-a-map"></a>맵 만들기

이 문서에서는 맵을 만들고 맵을 애니메이션하는 방법을 보여줍니다.  

## <a name="loading-a-map"></a>맵 로드

맵을 로드하려면 [맵 클래스](/javascript/api/azure-maps-control/atlas.map)의 새 인스턴스를 만듭니다. 맵을 시작할 때 DIV 요소 ID를 전달하여 맵을 렌더링하고 맵을 로드할 때 사용할 옵션 집합을 전달합니다. `atlas` 네임스페이스에 기본 인증 정보가 지정되지 않은 경우 맵을 로드할 때 맵 옵션에서 이 정보를 지정해야 합니다. 맵은 성능을 위해 여러 리소스를 비동기적으로 로드합니다. 따라서 맵 인스턴스를 만든 후에 `ready` 또는 `load` 이벤트를 맵에 연결한 다음, 맵과 상호 작용하는 추가 코드를 이벤트 처리기에 추가합니다. `ready` 이벤트는 맵에서 프로그래밍 방식으로 상호 작용할 수 있는 충분한 리소스를 로드하는 즉시 발생합니다. `load` 이벤트는 초기 맵 뷰의 로드가 완전히 완료된 후에 발생합니다. 

<br/>

<iframe height="500" scrolling="no" title="기본 맵 로드" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 Pen <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>기본 맵 로드</a>를 참조하세요.
</iframe>

> [!TIP]
> 여러 맵을 동일한 페이지에 로드할 수 있습니다. 동일한 페이지의 여러 맵에서 동일하거나 다른 인증 및 언어 설정을 사용할 수 있습니다.

## <a name="show-a-single-copy-of-the-world"></a>전 세계의 단일 복사본 표시

넓은 화면에서 맵을 축소하면 여러 사본으로 구성된 전 세계가 가로로 나타납니다. 이 옵션은 일부 시나리오에는 유용하지만 다른 애플리케이션의 경우에는 전 세계의 단일 복사본을 확인하는 것이 좋습니다. 이 동작은 맵 `renderWorldCopies` 옵션을 `false`로 설정하여 구현 합니다.

<br/>

<iframe height="500" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a>를 참조하세요.
</iframe>


## <a name="map-options"></a>맵 옵션

맵을 만들 때 아래 나열된 대로 맵이 작동하는 방식을 사용자 지정하기 위해 전달할 수 있는 몇 가지 옵션이 있습니다.

- [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) 및 [CameraBoundOptions](/javascript/api/azure-maps-control/atlas.cameraboundsoptions)를 사용하여 맵에서 표시해야 하는 영역을 지정합니다.
- [Serviceoptions](/javascript/api/azure-maps-control/atlas.serviceoptions)는 맵이 맵을 구동하는 서비스와 상호 작용하는 방법을 지정하는 데 사용됩니다.
- [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)는 맵의 스타일과 렌더링을 지정하는 데 사용됩니다.
- [UserInteractionOptions](/javascript/api/azure-maps-control/atlas.userinteractionoptions)는 사용자가 맵과 상호 작용할 때 맵에 도달하는 방법을 지정하는 데 사용됩니다. 

이러한 옵션은 `setCamera`, `setServiceOptions`, `setStyle` 및 `setUserInteraction` 함수를 사용하여 맵을 로드한 후에도 업데이트할 수 있습니다. 

## <a name="controlling-the-map-camera"></a>맵 카메라 제어

맵의 카메라를 사용하여 맵의 표시된 영역을 설정하는 방법에는 두 가지가 있습니다. 맵을 로드할 때 카메라 옵션을 설정할 수 있습니다. 또는 맵이 로드된 후 언제든지 `setCamera` 옵션을 호출하여 맵 뷰를 프로그래밍 방식으로 업데이트할 수 있습니다.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>카메라 설정

맵 카메라는 맵 캔버스의 뷰포트에 표시되는 항목을 제어합니다. 카메라 옵션은 초기화되거나 맵 `setCamera` 함수에 전달될 때 맵 옵션으로 전달될 수 있습니다.

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

다음 코드에서 [맵 개체](/javascript/api/azure-maps-control/atlas.map)가 만들어지고 중심 및 확대/축소 옵션이 설정됩니다. 중심 및 확대/축소 수준과 같은 맵 속성은 [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)의 일부입니다.

<br/>

<iframe height='500' scrolling='no' title='CameraOptions를 통해 맵 만들기' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>의 Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Azure Location Based Services에서 제공되는 `CameraOptions` </a>를 통해 맵 만들기(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조하세요.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>카메라 경계 설정

경계 상자는 맵 카메라를 업데이트하는 데 사용할 수 있습니다. 경계 상자가 포인트 데이터에서 계산된 경우 아이콘 크기를 고려하기 위해 카메라 옵션에서 픽셀 패딩 값도 지정하는 것이 유용한 경우가 많습니다. 그러면 요소가 맵 뷰포트의 가장자리를 벗어나지 않습니다.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

다음 코드에서 [맵 개체](/javascript/api/azure-maps-control/atlas.map)는 `new atlas.Map()`를 통해 생성됩니다. `CameraBoundsOptions`와 같은 맵 속성은 맵 클래스의 [setCamera](/javascript/api/azure-maps-control/atlas.map) 함수를 통해 정의될 수 있습니다. 경계 및 안쪽 여백 속성은 `setCamera`를 사용하여 설정됩니다.

<br/>

<iframe height='500' scrolling='no' title='CameraBoundsOptions를 통해 맵 만들기' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>의 Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Azure Maps에서 제공되는 `CameraBoundsOptions` </a>를 통해 맵 만들기(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조하세요.
</iframe>

### <a name="animate-map-view"></a>맵 보기 애니메이션

맵의 카메라 옵션을 설정할 때 [애니메이션 옵션](/javascript/api/azure-maps-control/atlas.animationoptions)도 설정할 수 있습니다. 이 옵션은 애니메이션 유형과 카메라를 이동하는 데 걸리는 시간을 지정합니다.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

다음 코드에서 첫 번째 코드 블록은 맵을 만들고 입력 및 확대/축소 맵 스타일을 설정합니다. 두 번째 코드 블록에서는 애니메이션 단추에 대한 클릭 이벤트 처리기가 생성됩니다. 이 단추를 클릭하면 [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) 및 [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)에 대한 일부 임의 값과 함께 `setCamera` 함수가 호출됩니다.

<br/>

<iframe height='500' scrolling='no' title='맵 보기 애니메이션' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>의 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 제공되는 Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>맵 보기 애니메이션</a>을 참조하세요.
</iframe>

## <a name="request-transforms"></a>변환 요청

때로는 맵 컨트롤에서 만든 HTTP 요청을 수정하는 기능이 유용할 수 있습니다. 예를 들면 다음과 같습니다.

- 타일 요청에 헤더를 더 추가합니다. 이 작업은 암호로 보호된 서비스에 대해 수행되는 경우가 많습니다.
- 프록시 서비스를 통해 요청을 실행하는 URL을 수정합니다.

맵의 [서비스 옵션](/javascript/api/azure-maps-control/atlas.serviceoptions)에는 맵의 모든 요청을 생성하기 전에 수정하는 데 사용할 수 있는 `transformRequest`가 있습니다. `transformRequest` 옵션은 두 개의 매개 변수, 즉 문자열 URL과 요청이 사용된 항목을 나타내는 리소스 종류 문자열을 사용하는 함수입니다. 이 함수는 [Requestparameters](/javascript/api/azure-maps-control/atlas.requestparameters) 결과를 반환해야 합니다.

```JavaScript
transformRequest: (url: string, resourceType: string) => RequestParameters
```

다음 예제에서는 이를 사용하여 요청에 사용자 이름 및 암호를 헤더로 추가하여 모든 요청을 `https://example.com` 크기에 맞게 수정하는 방법을 보여줍니다.

```JavaScript
var map = new atlas.Map('myMap', {
    transformRequest: function (url, resourceType) {
        //Check to see if the request is to the specified endpoint.
        if (url.indexOf('https://examples.com') > -1) {
            //Add custom headers to the request.
            return {
                url: url,
                header: {
                    username: 'myUsername',
                    password: 'myPassword'
                }
            };
        }

        //Return the URL unchanged by default.
        return { url: url };
    },

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

## <a name="try-out-the-code"></a>코드 사용해 보기

코드 샘플을 확인합니다. **JS 탭** 내에서 JavaScript 코드를 편집하고 **결과 탭** 에서 맵 뷰 변경 내용을 볼 수 있습니다. 오른쪽 상단의 **CodePen에서 편집** 을 클릭하고 CodePen에서 코드를 수정할 수도 있습니다.

<a id="relatedReference"></a>

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

앱에 기능을 추가하는 코드 예제를 참조하세요.

> [!div class="nextstepaction"]
> [맵 스타일 변경](choose-map-style.md)

> [!div class="nextstepaction"]
> [맵에 컨트롤 추가](map-add-controls.md)

> [!div class="nextstepaction"]
> [코드 샘플](/samples/browse/?products=azure-maps)