---
title: Azure Video Analyzer 플레이어 위젯 사용
description: 이 참조 문서에서는 애플리케이션에 Video Analyzer 플레이어 위젯을 추가하는 방법을 설명합니다.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: ffc17e756a303723fe1d21c6ba221fed31147eaa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620576"
---
# <a name="use-the-azure-video-analyzer-player-widget"></a>Azure Video Analyzer 플레이어 위젯 사용

이 자습서에서는 애플리케이션 내에서 플레이어 위젯을 사용하는 방법을 배웁니다. 이 코드는 사용자가 동영상을 재생하고 분할된 동영상 파일의 일부를 탐색할 수 있도록 포함하기 쉬운 위젯입니다. 이렇게 하려면 위젯이 포함된 정적 HTML 페이지와 이를 작동하도록 하는 모든 부분을 생성합니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 토큰 만들기
> * 비디오 나열
> * [비디오 애플리케이션 리소스](./terminology.md#video) 재생을 위한 기본 URL 가져오기
> * 플레이어를 사용하여 페이지 만들기
> * 스트리밍 엔드포인트와 토큰을 플레이어에게 전달

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에는 다음이 필요합니다.

* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.
* [VISUAL STUDIO CODE](https://code.visualstudio.com/) 또는 HTML 파일에 대한 다른 편집기입니다.
* [지속적인 비디오 녹화 및 재생](./use-continuous-video-recording.md) 또는 [에지 디바이스에서 동작 감지 및 비디오 녹화](./detect-motion-record-video-clips-cloud.md)
* [토큰](./access-policies.md#creating-a-token) 만들기
* 액세스 [정책](./access-policies.md#creating-an-access-policy) 만들기

## <a name="list-video-resources"></a>동영상 리소스 나열

다음으로 동영상 리소스 목록을 생성합니다. 이전에 사용한 계정 엔드포인트에 대해 REST 호출을 수행하고 생성한 토큰으로 인증합니다.

여러 가지 방법으로 REST API에 GET 요청을 보낼 수 있지만, 이를 위해 JavaScript 함수를 사용할 예정입니다. 다음 코드는 동기 `GET` 요청을 보내기 위해 페이지의 `clientApiEndpointUrl` 및 `token` 필드에 저장하는 값과 결합된 [XMLHttpRequest](https://www.w3schools.com/xml/ajax_xmlhttprequest_create.asp)를 사용합니다. 그런 다음 결과 비디오 목록을 가져와서 페이지에서 설정한 `videoList` 텍스트 영역에 저장합니다.

```javascript
function getVideos()
{
    var xhttp = new XMLHttpRequest();
    var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
    xhttp.open("GET", getUrl, false);
    xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
    xhttp.send();
    document.getElementById("videoList").value = xhttp.responseText.toString();
}
```
   > [!NOTE]
   >`clientApiEndPoint`및 토큰은 토큰을 [만들어](./access-policies.md#creating-a-token) 수집됩니다.

## <a name="add-the-video-analyzer-player-component"></a>Video Analyzer 플레이어 구성 요소 추가

이제 클라이언트 API 엔드포인트 URL, 토큰 및 비디오 이름이 있으므로 플레이어를 페이지에 추가할 수 있습니다.

1. 페이지에 직접 패키지를 추가하여 플레이어 모듈 자체를 포함합니다. 애플리케이션에 NPM 패키지 방향을 포함하거나 다음과 같이 런타임에 동적으로 포함되도록 할 수 있습니다.
   ```html
   <script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
   ```
1. 문서에 `AVA-Player` 요소를 추가합니다.
   ```html
   <ava-player width="720px" id="avaPlayer"></ava-player>
   ```
1. 페이지에 있는 Video Analyzer 플레이어 위젯에 대한 링크를 가져옵니다.
   ```javascript
   const avaPlayer = document.getElementById("avaPlayer");
   ```
1. 플레이어를 보유한 값으로 구성하려면 다음과 같이 개체로 설정해야 합니다.
   ```javascript
   avaPlayer.configure( {
      token: document.getElementById("token").value,
      clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
      videoName: document.getElementById("videoName").value
   } );
   ```
1. 플레이어에 비디오를 로드하여 시작합니다.
   ```javascript
   avaPlayer.load();
   ```
   
## <a name="add-the-zone-drawer-component"></a>영역 서랍 구성 요소 추가

1. 문서에 AVA-Zone-Drawer 요소를 추가합니다.
   ```html
   <ava-zone-drawer width="720px" id="zoneDrawer"></ava-zone-drawer>
   ```
1. 페이지에 있는 Video Analyzer 영역 서랍에 대한 링크를 가져옵니다.
   ```javascript
   const zoneDrawer = document.getElementById("zoneDrawer");
   ```
1. 플레이어에 영역 서랍을 로드합니다.
   ```javascript
   zoneDrawer.load();
   ```
1. 영역을 만들고 저장하려면 여기에 이벤트 수신기를 추가해야 합니다.
   ```javascript
   zoneDrawer.addEventListener('ZONE_DRAWER_ADDED_ZONE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });

        zoneDrawer.addEventListener('ZONE_DRAWER_SAVE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });
   ```

## <a name="put-it-all-together"></a>모든 요소 결합

앞의 웹 요소를 결합하면 다음과 같은 정적 HTML 페이지가 생성됩니다. 이 페이지에서는 계정 엔드포인트와 토큰을 사용하여 동영상을 볼 수 있습니다.

```html
<html>
<head>
<title>Video Analyzer Player Widget Demo</title>
</head>
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
<body>
<script>
    function getVideos()
    {
        var xhttp = new XMLHttpRequest();
        var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
        xhttp.open("GET", getUrl, false);
        xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
        xhttp.send();
        document.getElementById("videoList").value = xhttp.responseText.toString();
    }
    function playVideo() {
        const avaPlayer = document.getElementById("avaPlayer");
        avaPlayer.configure( {
            token: document.getElementById("token").value,
            clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
            videoName: document.getElementById("videoName").value
        } );
        avaPlayer.load();
    
        const zoneDrawer = document.getElementById("zoneDrawer");
        zoneDrawer.load();

        zoneDrawer.addEventListener('ZONE_DRAWER_ADDED_ZONE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });

        zoneDrawer.addEventListener('ZONE_DRAWER_SAVE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });
    }
</script>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
Token: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
<textarea rows="5" cols="100" id="zoneList"></textarea><br><br>
<ava-zone-drawer width="720px" id="zoneDrawer">
    <ava-player id="avaPlayer"></ava-player>
</ava-zone-drawer>
</body>
</html>
```

## <a name="host-the-page"></a>페이지 호스팅

이 페이지를 로컬로 테스트할 수 있지만 호스트된 버전을 테스트할 수 있습니다. 페이지를 빠르게 호스트하는 방법이 없는 경우 Azure Storage에서 [정적 웹 사이트](../../storage/blobs/storage-blob-static-website.md)를 사용하는 방법에 대한 지침은 다음과 같습니다. 다음 단계는 [보다 완전한 지침](../../storage/blobs/storage-blob-static-website-how-to.md)을 요약한 버전입니다. 이 자습서에서 사용 중인 파일에 대한 단계가 업데이트됩니다.

1. 스토리지 계정을 만듭니다.
1. **데이터 관리** 에서 **정적 웹 사이트** 를 선택합니다.
1. 스토리지 계정에서 정적 웹 사이트를 사용하도록 설정합니다.
1. **인덱스 문서 이름** 에 **index.html** 을 입력합니다.
1. **오류 문서 경로** 에 **404.html** 을 입력합니다.
1. **저장** 을 선택합니다.
1. 표시되는 **기본 엔드포인트** 를 확인하세요. 이는 사용자 웹 사이트가 됩니다.
1. **기본 엔드포인트** 위에서 **$web** 을 선택합니다.
1. 상단의 **업로드** 단추를 사용하여 정적 HTML 페이지를 **index.html** 로 업로드합니다.`

## <a name="play-a-video"></a>비디오 재생

이제 페이지를 호스팅했으므로 해당 페이지로 이동하여 동영상을 재생하는 단계를 수행합니다.

1. **클라이언트 API 엔드포인트 URL** 및 **토큰** 값을 입력합니다.
1. **동영상 가져오기** 를 선택합니다.
1. 동영상 목록에서 동영상 이름을 선택하고 **동영상 이름** 필드에 입력합니다.
1. **동영상 재생** 을 선택합니다.

## <a name="additional-details"></a>추가 정보

다음 섹션에는 알아야 할 몇 가지 중요한 추가 세부 정보가 포함되어 있습니다.

### <a name="refresh-the-access-token"></a>액세스 토큰 새로 고침

플레이어는 이전에 생성한 액세스 토큰을 사용하여 재생 권한 부여 토큰을 가져옵니다. 토큰은 정기적으로 만료되며 새로 고쳐야 합니다. 새 액세스 토큰을 생성한 후 플레이어의 액세스 토큰을 새로 고치는 방법에는 두 가지가 있습니다.

* 위젯 메서드 `setAccessToken`을 적극적으로 호출
    ```typescript
    avaPlayer.setAccessToken('<NEW-ACCESS-TOKEN>');
    ```
* 이 이벤트를 수신하여 `TOKEN_EXPIRED` 이벤트에 대한 조치 수행
    ```typescript
    avaPlayer.addEventListener(PlayerEvents.TOKEN_EXPIRED, () => {
        avaPlayer.setAccessToken('<YOUR-NEW-TOKEN>');
    });
    ```

`TOKEN_EXPIRED` 이벤트는 토큰이 만료되기 5초 전에 발생합니다. 이벤트 수신기를 설정하는 경우 플레이어 위젯에서 `load` 함수를 호출하기 전에 수행해야 합니다.

### <a name="configuration-details"></a>구성 세부 정보

앞의 플레이어는 구성이 간단하지만 구성 값에 대해 더 넓은 범위의 옵션을 사용할 수 있습니다. 다음은 지원되는 필드입니다.

| Name   | 유형             | Description                         |
| ------ | ---------------- | ----------------------------------- |
| `token`  | 문자열 | 위젯의 JWT 토큰 |
| `videoName` | 문자열 | 비디오 리소스의 이름  |
| `clientApiEndpointUrl` | 문자열 | 클라이언트 API의 엔드포인트 URL |

### <a name="alternate-ways-to-load-the-code-into-your-application"></a>애플리케이션에 코드를 로드하는 대체 방법

애플리케이션에 코드를 가져오는 데 사용되는 패키지는 [NPM 패키지](https://www.npmjs.com/package/@azure/video-analyzer-widgets)입니다. 앞의 예에서 최신 버전은 런타임 시 리포지토리에서 직접 로드되었습니다. 그러나 다음을 사용하여 로컬로 패키지를 다운로드하고 설치할 수도 있습니다.

```bash
npm install @azure/video-analyzer/widgets
```

또는 TypeScript에 다음을 사용하여 애플리케이션 코드 내에서 가져올 수 있습니다.

```typescript
import { Player } from '@azure/video-analyzer-widgets';
import { ZoneDrawer } from '@azure/video-analyzer-widgets';
```

플레이어 위젯을 동적으로 만들려는 경우 Javascript에 다음을 사용할 수 있습니다.
```javascript
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets@latest/dist/global.min.js"></script>
```


이 메서드를 사용하여 가져오는 경우 가져오기가 완료된 후 영역 서랍 및 플레이어 개체를 프로그래밍 방식으로 만들어야 합니다.  앞의 예제에서는 HTML 태그를 사용하여 모듈을 페이지에 `ava-player` 추가했습니다. 코드를 통해 영역 서랍 개체와 플레이어 개체를 만들려면 JavaScript에서 다음을 수행할 수 있습니다.


```javascript
const zoneDrawer = new window.ava.widgets.zoneDrawer();
document.firstElementChild.appendChild(zoneDrawer);
const playerWidget = new window.ava.widgets.player();
zoneDrawer.appendChild(playerWidget);
```

또는 Typescript에서 다음을 수행할 수 있습니다.

```typescript
const avaPlayer = new Player();
const zoneDrawer = new ZoneDrawer();
```

그런 다음 HTML에 추가해야 합니다.

```javascript
document.firstElementChild.appendChild(zoneDrawer);
zoneDrawer.appendChild(playerWidget);
```

## <a name="next-steps"></a>다음 단계

* [위젯 API](https://github.com/Azure/video-analyzer/tree/main/widgets)에 대해 자세히 알아봅니다.
