---
title: Azure Communication Services 미디어 품질 메트릭
titleSuffix: An Azure Communication Services concept document
description: ACS 미디어 품질 정적 SDK의 개요를 제공 합니다.
author: sloanster
ms.author: micahvivion
manager: nmurav
services: azure-communication-services
ms.date: 11/22/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: caada18a523b3026b5bf2c0297300bba43b8f6e2
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133289630"
---
# <a name="media-quality-statistics"></a>미디어 품질 통계 
Azure 통신 서비스에서 호출을 사용할 때 ACS 호출 내에서 생성 되는 미디어 품질 통계를 알아야 하는 경우가 있습니다. 이러한 세부 정보를 이해 하기 위해 하위 수준 오디오, 비디오 및 화면 공유 품질 메트릭을 검사 하는 데 사용할 수 있는 "미디어 품질 통계" 라는 기능을 제공 합니다.

### <a name="media-quality-statistics-for-ongoing-call"></a>진행 중인 통화에 대 한 미디어 품질 통계
> **참고** 이 API는 개발자를 위해 미리 보기 (' 베타 ')로 제공 되며 받은 피드백에 따라 변경 될 수 있습니다. 프로덕션 환경에서 이 API를 사용하지 마세요.

미디어 품질 통계는 핵심 API의 확장 된 기능입니다 `Call` . 먼저 MediaStats 기능 API 개체를 가져와야 합니다.

```js
const mediaStatsFeature = call.feature(Features.MediaStats);
```

그런 다음 `mediaStatsCollectorOptions` `MediaStatsCollectorOptions` 간격을 제어 하려면 형식을 정의 합니다. 그렇지 않으면 SDK에서 기본값을 사용 합니다.

```js
const mediaStatsCollectorOptions: SDK.MediaStatsCollectorOptions = {
    aggregationInterval: 2,
    dataPointsPerAggregation: 20
};
```

Where
- `aggregationInterval` 통계가 집계 되는 간격 (초)입니다. 
- `dataPointsPerAggregation` 각 집계에 필요한 데이터 요소 수를 정의 합니다.

미디어 통계 수집기를 가져온 후에는 `mediaStatsEmitted` 매 초 마다 통계를 포함 한 이벤트를 받게 됩니다 `aggregationInterval * dataPointsPerAggregation` .

예:
- = = 1로 설정 하는 경우 `aggregatinInterval`
- `dataPointsPerAggregation` = = 60

미디어 통계는 `mediaStatsEmmitted` 60 초 마다 발생 하며 기록 된 각 stat에 대해 60 고유 단위를 포함 합니다.

- = = 60을 설정 하는 경우 `aggregatinInterval`
- `dataPointsPerAggregation` = = 1- `mediaStatsEmmitted` 60 초 마다 미디어 통계가 발생 하 고 기록 된 각 stat에 대해 1 개의 고유한 단위를 포함 합니다.

개발자는 `startCollector` 선택적으로의 메서드를 호출할 수 있습니다 `mediaStatsApi` `mediaStatsSubscriptionOptions` .

```js
const mediaStatsCollector = mediaStatsFeature.startCollector(mediaStatsSubscriptionOptions);
```



```js
mediaStatsCollector.on('mediaStatsEmitted', (mediaStats) => {
    console.log('media stats:', mediaStats.stats);
    console.log('media stats collectionInterval:', mediaStats.collectionInterval);
    console.log('media stats aggregationInterval:', mediaStats.aggregationInterval);
});
```

미디어 통계 수집기를 삭제 하려면 `dispose` 의 메서드를 호출 `mediaStatsCollector` 합니다.

```js
mediaStatsCollector.dispose();
```

모든 수집기를 삭제 하려면 `disposeAllCollectors` 의 메서드를 호출 `mediaStatsApi` 합니다.

```js
mediaStatsFeature.disposeAllCollectors();
```
## <a name="best-practices"></a>모범 사례
호출이 종료 된 후 오프 라인 검사에 대해이 데이터를 수집 하려면이 데이터를 수집 하 고 호출이 종료 된 후 파이프라인 수집으로 전송 하는 것이 좋습니다. 현재 데이터를 전송 하는 동안이 데이터를 전송 하는 경우 ACS 호출을 계속 하는 데 필요한 인터넷 대역폭을 사용할 수 있습니다 (사용 가능한 대역폭이 낮은 경우).

### <a name="bandwidth-metrics"></a>대역폭 메트릭
| 메트릭 이름    | 용도              | 자세한 설명                                                    | 의견                                                                      |
| -------------- | -------------------- | ----------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| SentBWEstimate | 대역폭 추정 | 채널 bps에 할당 된 평균 비디오 대역폭 (초당 비트) | 업로드/다운로드에 고품질 비디오를 사용할 경우 1.5 MBps 이상이 권장 됩니다. |


### <a name="audio-quality-metrics"></a>오디오 품질 메트릭
| 메트릭 이름               | 용도                      | 세부 정보                                                                                                                                                                               | 의견                                                     |
| ------------------------- | ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| 오디오 Send비트 전송률          | 전송 비트 전송률                 | 오디오의 전송 비트 전송률 (초당 비트)                                                                                                                                               | 일반 값은 24 kbps 범위 (일반적으로 36-128kbps)입니다. |
| audioRecvBitrate          | 수신 비트 전송률             | 받은 오디오의 비트 전송률 (초당 비트)                                                                                                                                  |                                                              |
| 오디오 Sendpackets          | 보낸 패킷                 | 마지막 1 초 (초당 패킷)에 전송 된 오디오 패킷 수                                                                                                                  |                                                              |
| audioRecvJitterBufferMs   | 받은 지터              | 지터는 패킷 지연의 차이 (밀리초 (밀리초))입니다.                                                                                                             | 일반적으로 30 밀리초 이하의 오디오 지터를 권장 합니다.  |
| audioRecvPacketsLost      | 패킷 손실 수신         | 수신 되었지만 손실 된 오디오 패킷의 수입니다. 결과는 초당 패킷 수입니다 (마지막 1 초 동안).                                                            | 낮을수록 좋습니다.                                             |
| audioSendPacketsLost      | 전송 패킷 손실             | 마지막 1 초 동안 손실 (수신 되지 않음) 된 전송 된 오디오 패킷 수입니다.  결과는 초당 패킷 수입니다 (마지막 1 초 동안).                                            | 낮을수록 좋습니다.                                             |
| audioRecvPackets          | 받은 패킷             | 마지막 1 초 동안 받은 오디오 패킷 수입니다. 결과는 초당 패킷 수입니다 (마지막 1 초 동안).                                                                       | 정보만 해당 됩니다.                                            |
| audioSendCodecName        | 전송 코덱                   | 사용 되는 오디오 코덱입니다.                                                                                                                                                                     | 정보만 해당 됩니다.                                            |
| 오디오 Sendrtt              | 전송 Round-Trip 시간         | 시스템과 ACS 서버 간의 왕복 시간입니다. 결과는 밀리초 (밀리초)입니다.                                                                                                   | 200 밀리초 이하의 왕복 시간을 권장 합니다.          |
| audioSendPairRtt          | 송신 쌍 Round-Trip 시간    | 전체 전송에 대 한 왕복 시간입니다. 결과는 밀리초 (밀리초)입니다.                                                                                                                           | 200 밀리초 이하의 왕복 시간을 권장 합니다.          |
| audioRecvPairRtt          | 수신 쌍 Round-Trip 시간 | 전체 전송 결과의 라운드트립 시간은 밀리초 (밀리초)입니다.                                                                                                                            | 200 밀리초 이하의 왕복 시간을 권장 합니다.          |
| 오디오 Send오디오 Inputlevel  | 마이크의 입력 수준   | 오디오 송출 수준을 보냈습니다. 원본 데이터가 0-1 사이인 경우 미디어 스택은이를 0xFFFF와 곱합니다. 마이크에 따라 다릅니다. 마이크가 자동 (들어오는 에너지 없음) 인지 확인 하는 데 사용 됩니다. | 마이크 입력 수준입니다.                                      |
| audioRecvAudioOutputLevel | 스피커 출력 수준입니다.        | 오디오 송출 수준을 받았습니다.  원본 데이터가 0-1 사이인 경우 미디어 스택은이를 0xFFFF와 곱합니다.                                                                                 | 화자 출력 수준입니다.                                        |


### <a name="video-quality-metrics"></a>비디오 품질 메트릭
| 메트릭 이름                    | 용도                          | 세부 정보                                                                                                                                  | 의견                                                                                         |
| ------------------------------ | -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| videoSendFrameRateSent         | 전송된 프레임 속도                  | 전송된 비디오 프레임 수입니다. 결과는 초당 프레임 수입니다.                                                                               | 더 높아도 좋습니다.<br>25-30fps(360p 이상)<br>8-15fps(270p 이하)<br>프레임/초<br> |
| videoSendFrameWidthSent        | 전송된 너비                       | 비디오 너비 해상도가 전송되었습니다.                                                                                                             | 높을수록 좋습니다. 가능한 값은 다음과 같습니다.<br>1920, 1280, 960, 640, 480, 320                             |
| videoSendFrameHeightSent       | 보낸 높이                      | 비디오 높이가 전송되었습니다. 높을수록 좋습니다.                                                                                                      | 높을수록 좋습니다. 가능한 값은 다음과 같습니다.<br>1080, 720, 540, 360, 270, 240                              |
| videoSendBitrate               | 전송 비트 전송률                     | 전송되는 비디오 비트 전송률의 양입니다. 결과는 bps(초당 비트)입니다.                                                                    |                                                                                                  |
| videoSendPackets               | 보낸 패킷                     | 전송된 비디오 패킷의 수입니다. 결과는 초당 패킷입니다(마지막 1초 동안).                                                 | 정보만                                                                                 |
| VideoSendCodecName             | 보낸 코덱                       | 비디오 인코딩에 사용되는 비디오 CODEC                                                                                                      | VP8(1:1 호출) 및 H264                                                                         |
| videoRecvJitterBufferMs        | 받은 지터                  | 지터는 패킷 지연의 차이(밀리초)입니다.                                                                | 낮을수록 좋습니다.                                                                                 |
| videoSendRtt                   | Round-Trip 시간 보내기             | 시스템과 ACS 서버 간의 응답 시간입니다. 더 낮을수록 좋습니다.                                                                        | 200ms 이하의 왕복 시간을 권장합니다.                                              |
| videoSendPairRtt               | 쌍 Round-Trip 시간 보내기        | 시스템과 ACS 서버 간의 응답 시간입니다. 결과는 밀리초(밀리초)입니다.                                                      | 200ms 이하의 왕복 시간을 권장합니다.                                              |
| videoRecvPairRtt               | 수신 쌍 Round-Trip 시간     | 전체 전송에 대한 왕복 시간입니다. 결과는 밀리초(밀리초)입니다.                                                                              | 200ms 이하의 왕복 시간을 권장합니다.                                              |
| videoRecvFrameRateReceived     | 수신된 프레임 속도              | 현재 받은 비디오의 프레임 속도                                                                                                   | 25-30fps(360p 이상)<br>8-15fps(270p 이하)                                           |
| videoRecvFrameWidthReceived    | 받은 너비                   | 현재 받은 비디오의 너비                                                                                                        | 1920, 1280, 960, 640, 480, 320                                                                   |
| videoRecvFrameHeightReceived   | 수신된 높이                  | 현재 받은 비디오의 높이                                                                                                       | 1080, 720, 540, 360, 270, 240                                                                    |
| videoRecvBitrate               | 받은 비트 전송률                 | 현재 받은 비디오의 비트 전송률(초당 비트)                                                                                    | 정보만 해당,                                                                                |
| videoRecvPackets               | 받은 패킷                 | 마지막 1초 동안 받은 패킷 수                                                                                            | 정보만                                                                                 |
| VideoRecvPacketsLost           | 수신된 패킷 손실             | 수신되었지만 손실된 비디오 패킷의 수입니다. 결과는 초당 패킷입니다(마지막 1초 동안).                                                                                                             | 더 낮을수록 좋습니다.                                                                                  |
| videoSendPacketsLost           | 전송된 패킷 손실                 | 전송되었지만 손실된 오디오 패킷의 수입니다. 결과는 초당 패킷입니다(마지막 1초 동안).                                                                                                             | 더 낮을수록 좋습니다.                                                                                  |
| videoSendFrameRateInput        | 전송된 프레임 속도 입력             | 스트림 입력에서 peerConnection으로의 프레임 속도 측정                                                                         | 정보만                                                                                 |
| videoRecvFrameRateDecoded      | 수신된 디코딩된 프레임 속도       | 디코더 출력의 프레임 속도입니다. 이렇게 하면 videoSendFrameRateInput이 입력으로 사용되며 디코딩에 약간의 손실이 있을 수 있습니다.                           | 정보만                                                                                 |
| videoSendFrameWidthInput       | 전송된 프레임 너비 입력           | peerConnection에 대한 스트림 입력의 프레임 너비입니다. 이 경우 비디오RecvFrameRateDecoded가 입력으로 사용되며 렌더링에 약간의 손실이 있을 수 있습니다. | 1920, 1280, 960, 640, 480, 320                                                                   |
| videoSendFrameHeightInput      | 보낸 프레임 높이 입력          | peerConnection에 대한 스트림 입력의 프레임 높이                                                                                     | 1080, 720, 540, 360, 270, 240                                                                    |
| videoRecvLongestFreezeDuration | 받은 가장 긴 중지 기간 | 가장 긴 동결 기간                                                                                                          | 더 낮을수록 좋습니다.                                                                                  |
| videoRecvTotalFreezeDuration   | 받은 총 동결 기간   | 총 중지 기간(초)                                                                                                         | 더 낮을수록 좋습니다.                                                                                  |

### <a name="screen-share-quality-metrics"></a>화면 공유 품질 메트릭
| 메트릭 이름                            | 용도                          | 세부 정보                                                          | 의견                              |
| -------------------------------------- | -------------------------------- | ---------------------------------------------------------------- | ------------------------------------- |
| screenSharingSendFrameRateSent         | 전송된 프레임 속도                  | 전송된 비디오 프레임 수입니다. 높을수록 좋습니다.                    | 1-30 FPS(콘텐츠 인식, 변수)    |
| screenSharingSendFrameWidthSent        | 전송된 너비                       | 비디오 해상도가 전송되었습니다. 높을수록 좋습니다.                          | 1920픽셀(콘텐츠 인식, 변수) |
| screenSharingSendFrameHeightSent       | 보낸 높이                      | 비디오 해상도가 전송되었습니다. 높을수록 좋습니다.                          | 1080픽셀(콘텐츠 인식, 변수) |
| screenSharingSendCodecName             | 보낸 코덱                       | 화면 공유 인코딩에 사용되는 코덱                             | 정보만                      |
| screenSharingRecvFrameRateReceived     | 수신된 프레임 속도              | 받은 비디오 프레임 수입니다. 낮을수록 좋습니다.                | 1-30 FPS                              |
| screenSharingRecvFrameWidthReceived    | 받은 너비                   | 비디오 해상도가 수신되었습니다. 높을수록 좋습니다.                      | 1920픽셀(콘텐츠 인식, 변수) |
| screenSharingRecvFrameHeightReceived   | 수신된 높이                  | 비디오 해상도가 전송되었습니다. 높을수록 좋습니다.                          | 1080픽셀(콘텐츠 인식, 변수) |
| screenSharingRecvCodecName             | 받은 코덱                   | 비디오 스트림 디코딩에 사용되는 코덱                             | 정보만                      |
| screenSharingRecvJitterBufferMs        | 받은 지터                  | 지터는 패킷 지연의 차이(밀리초)입니다.                                                                |                                 |
| screenSharingRecvPacketsLost           | 수신된 패킷 손실             | 수신되었지만 손실된 화면 공유 패킷의 수입니다. 결과는 초당 패킷입니다(마지막 1초 동안).                                     | 더 낮을수록 좋습니다.                       |
| screenSharingSendPacketsLost           | 수신된 패킷 손실             | 전송된 화면 공유 패킷 수가 손실되었습니다. 결과는 초당 패킷입니다(마지막 1초 동안).                                     | 더 낮을수록 좋습니다.                       |
| screenSharingSendFrameRateInput        | 전송된 프레임 속도 입력             | 스트림 입력에서 peerConnection으로의 프레임 속도 측정 | 정보만                      |
| screenSharingRecvFrameRateDecoded      | 수신된 디코딩된 프레임 속도       | 디코더 출력의 프레임 속도                                    | 정보만                      |
| screenSharingRecvFrameRateOutput       | 수신된 프레임 속도 출력        | 렌더러로 전송된 스트림의 프레임 속도                | 정보만                      |
| screenSharingSendFrameWidthInput       | 전송된 프레임 너비 입력           | peerConnection에 대한 스트림 입력의 프레임 너비              | 정보만                      |
| screenSharingSendFrameHeightInput      | 보낸 프레임 높이 입력          | peerConnection에 대한 스트림 입력의 프레임 높이             | 정보만                      |
| screenSharingRecvLongestFreezeDuration | 받은 가장 긴 중지 기간 | 가장 긴 동결 기간                                  | 더 낮을수록 좋습니다.                       |
| screenSharingRecvTotalFreezeDuration   | 받은 총 동결 기간   | 총 중지 기간(초)                                 | 더 낮을수록 좋습니다.                       |
