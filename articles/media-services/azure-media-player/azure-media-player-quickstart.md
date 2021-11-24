---
title: Azure Media Player 빠른 시작
description: Azure Media Player를 설정하는 기본 단계에 대해 알아봅니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/05/2021
ms.custom: mode-other
ms.openlocfilehash: ff5eccb7884061ed5b788aeeb89300b3a94cdc36
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133074126"
---
# <a name="azure-media-player-quickstart"></a>Azure Media Player 빠른 시작
Azure Media Player는 설정하기 쉽습니다. Azure Media Services 계정에서 미디어 콘텐츠의 기본 재생을 가져오는 데 몇 분 밖에 걸리지 않습니다. 이 섹션에서는 세부 정보를 설명하지 않고 기본 단계를 보여줍니다. 다음 섹션에서는 Azure Media Player를 설정하고 구성하는 방법에 대해 자세히 설명합니다.  문서의 `<head>`에 다음과 같은 내용을 추가하기만 하면 됩니다.

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> 주문형으로 변경될 수 있으므로 프로덕션에 `latest` 버전을 사용하지 **않아야** 합니다. `latest`를 Azure Media Player 버전으로 대체합니다. 예를 들어 `latest`를 `1.0.0`으로 대체합니다. [여기에서](https://amp.azure.net/libs/amp/latest/docs/changelog.html) Azure Media Player 버전을 쿼리할 수 있습니다.

## <a name="use-the-video-element"></a>비디오 요소 사용

그런 다음, 일반적인 방법으로 `<video>` 요소를 사용하지만 모든 옵션이 포함된 추가 `data-setup` 특성을 사용하면 됩니다. 이러한 옵션에는 유효한 JSON 개체의 Azure Media Services 옵션이 포함될 수 있습니다.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

자동 설정을 사용하지 않으려면 `data-setup` 특성을 생략하고 비디오 요소를 수동으로 초기화할 수 있습니다.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            });
          }
    );
    myPlayer.src([{
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 전체 설정](./azure-media-player-full-setup.md)
