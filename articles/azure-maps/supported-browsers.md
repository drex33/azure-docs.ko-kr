---
title: 웹 SDK 지원 브라우저 | Microsoft Azure Maps
description: Azure Maps 웹 SDK가 브라우저를 지원하는지 확인하는 방법을 알아봅니다. 지원되는 브라우저 목록을 봅니다. 레거시 브라우저에서 맵 서비스를 사용하는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
ms.openlocfilehash: a393d6c54f5d9918b3746bfc6c1de1c93cd9319c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435027"
---
# <a name="web-sdk-supported-browsers"></a>웹 SDK 지원 브라우저

Azure Maps 웹 SDK는 [atlas.isSupported](/javascript/api/azure-maps-control/atlas#issupported-boolean-)라는 도우미 함수를 제공합니다. 이 함수는 지도 컨트롤 로드 및 렌더링을 지원하는 데 필요한 최소 WebGL 기능 집합이 웹 브라우저에 있는지 여부를 검색합니다. 함수 사용 방법의 예는 다음과 같습니다.

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>데스크톱

Azure Maps 웹 SDK는 다음 데스크톱 브라우저를 지원합니다.

- Microsoft Edge(현재 및 이전 버전)
- Google Chrome(현재 및 이전 버전)
- Mozilla Firefox(현재 및 이전 버전)
- Apple Safari(macOS X)(현재 및 이전 버전)

이 문서의 뒷부분에 있는 [대상 레거시 브라우저](#Target-Legacy-Browsers)도 참조하세요.

## <a name="mobile"></a>모바일

Azure Maps 웹 SDK는 다음과 같은 모바일 브라우저를 지원합니다.

- Android
  - Android 6.0 및 그 이후 버전의 Chrome 현재 버전
  - Android 6.0 및 그 이후 버전의 Chrome WebView
- iOS
  - iOS 현재 및 이전 주 버전의 모바일 Safari
  - iOS 현재 및 이전 주 버전의 UIWebView와 WKWebView
  - iOS용 Chrome의 현재 버전

> [!TIP]
> WebView 컨트롤을 사용하여 모바일 애플리케이션 내에 맵을 포함하는 경우 Azure Content Delivery Network에서 호스트되는 SDK 버전을 참조하는 대신 [Azure Maps 웹 SDK의 npm 패키지](https://www.npmjs.com/package/azure-maps-control)를 사용하는 것이 좋습니다. 이 방법은 SDK가 이미 사용자의 디바이스에 있고 런타임을 다운로드할 필요가 없기 때문에 로드 시간이 줄어듭니다.

## <a name="nodejs"></a>Node.js

다음 웹 SDK 모듈은 Node.js에서도 지원됩니다.

- 서비스 모듈([설명서](how-to-use-services-module.md) | [npm 모듈](https://www.npmjs.com/package/azure-maps-rest))

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>대상 레거시 브라우저

WebGL을 지원하지 않거나 제한적으로만 지원하는 구형 브라우저를 대상으로 지정할 수 있습니다. 이러한 경우 [Leaflet](https://leafletjs.com/)과 같은 오픈 소스 지도 컨트롤과 함께 Azure Maps 서비스 사용을 권합니다. 다음은 오픈 소스 [Azure Maps Leaflet 플러그 인](https://github.com/azure-samples/azure-maps-leaflet)을 사용하는 예제입니다.

<br/>

<iframe height="500" scrolling="no" title="Azure Maps + Leaflet" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Azure Maps의 <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + Leaflet</a> 펜(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)을 참조하세요.
</iframe>

Leaflet에서 Azure Maps를 사용하는 추가 코드 샘플은 [여기](https://azuremapscodesamples.azurewebsites.net/?search=leaflet)에서 찾을 수 있습니다.

Azure Maps 팀에서 플러그 인을 만든 몇 가지 인기 있는 오픈 소스 맵 컨트롤은 [다음](open-source-projects.md#third-part-map-control-plugins)과 같습니다.

## <a name="next-steps"></a>다음 단계

Azure Maps 웹 SDK에 대한 자세한 정보를 알아봅니다.

[지도 컨트롤](how-to-use-map-control.md)

[서비스 모듈](how-to-use-services-module.md)
