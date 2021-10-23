---
title: Azure Media Player API 메서드
description: Azure Media Player API를 사용하면 브라우저에서 HTML5 비디오, Flash, Silverlight 또는 지원되는 기타 재생 기술을 통해 비디오를 재생하고 있는지에 관계없이 JavaScript를 통해 비디오와 상호 작용할 수 있습니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 443c2abbd005c3e2517caa98c707132ad0849d3a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234325"
---
# <a name="api"></a>API #

Azure Media Player API를 사용하면 브라우저에서 HTML5 비디오, Flash, Silverlight 또는 지원되는 기타 재생 기술을 통해 비디오를 재생하고 있는지에 관계없이 JavaScript를 통해 비디오와 상호 작용할 수 있습니다.

## <a name="referencing-the-player"></a>플레이어 참조 ##

API 함수를 사용하려면 플레이어 개체에 액세스할 수 있어야 합니다. 다행히도 쉽게 가져올 수 있습니다. 비디오 태그에 ID가 있는지 확인하기만 하면 됩니다. 예제 embed 코드에는 `vid1`의 ID가 있습니다. 한 페이지에 여러 비디오가 있는 경우 모든 비디오 태그에 고유한 ID가 있는지 확인합니다.

`var myPlayer = amp('vid1');`

> [!NOTE]
> 데이터 설정 특성이나 다른 메서드를 통해 플레이어를 아직 초기화하지 않은 경우에는 플레이어도 초기화됩니다.

## <a name="wait-until-the-player-is-ready"></a>플레이어가 준비될 때까지 기다립니다. ##

Azure Media Player가 비디오 및 API를 설정하는 데 걸리는 시간은 사용 중인 재생 기술에 따라 달라집니다. HTML5는 Flash 또는 Silverlight보다 로드 속도가 훨씬 빠른 경우가 많습니다. 이러한 이유로 플레이어의 'ready' 함수를 사용하여 플레이어의 API를 필요로 하는 모든 코드를 트리거해야 합니다.

```javascript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

또는

```javascript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>API 메서드 ##

이제 준비된 플레이어에 액세스할 수 있으므로 비디오를 제어하거나, 값을 가져오거나, 비디오 이벤트에 응답할 수 있습니다. Azure Media Player API 함수 이름은 [HTML5 미디어 API](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html)를 따르려고 합니다. 주요 차이점은 getter/setter 함수가 비디오 속성에 사용된다는 것입니다.

```javascript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>이벤트 등록 ##
처음으로 플레이어를 초기화한 후 이벤트를 직접 등록하여 모든 이벤트가 애플리케이션에 적절하게 보고되는지 확인하고 준비 이벤트 외부에서 수행해야 합니다.

```javascript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>다음 단계 ##

<!---Some context for the following links goes here--->
- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)