---
title: 미리 보기 이미지 사용
description: 이 문서에서는 Azure Video Analyzer를 사용하여 비디오를 녹화할 때 미리 보기 이미지를 사용하도록 설정하고 액세스하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: dd78a3753f61fe5615f15a41d1b1493556d0dafc
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559431"
---
# <a name="enable-preview-images-when-recording-video"></a>비디오를 녹화할 때 미리 보기 이미지 사용

Azure Video Analyzer를 사용하여 RTSP 카메라에서 [비디오를 캡처하고 녹화할](../video-recording.md) 수 있습니다. 이 [빠른 시작](detect-motion-record-video-clips-cloud.md) 또는 이 [자습서에](use-continuous-video-recording.md)표시된 대로 비디오 싱크 노드를 포함하는 파이프라인 토폴로지 를 만듭니다. 

Video Analyzer 에지 모듈을 사용하여 비디오를 녹화하는 경우 비디오 싱크 노드가 다양한 크기의 미리 보기 이미지 집합을 주기적으로 생성하도록 설정할 수 있습니다. 그런 다음 Video Analyzer 계정의 [비디오 리소스에서](../terminology.md#video) 이러한 이미지를 검색할 수 있습니다. 예를 들어 카메라에서 해상도가 1920x1080인 비디오를 생성하는 경우 미리 보기 이미지의 크기는 다음과 같습니다.

  * 320 x 180: small
  * 640 x 360: 중간
  * 1280 x 720: large

> [!NOTE]
> 미리 보기 이미지는 카메라에서 비디오의 가로 세로 비율을 유지합니다.

미리 보기 이미지는 주기적으로 생성되며, 빈도는 에 의해 [`segmentLength`](../playback-recordings-how-to.md#recording-and-playback-latencies) 결정됩니다. 이벤트 기반 [녹화를](record-event-based-live-video.md)사용하는 경우 라이브 파이프라인이 활성화되고 비디오가 녹화되는 경우에만 이미지가 생성됩니다. 미리 보기 이미지 집합이 생성될 때마다 이전 집합을 덮어쓰게 됩니다.

> [!NOTE]
> 이 기능은 현재 Video Analyzer Edge 모듈에서만 사용할 수 있습니다. 또한 이를 사용하도록 설정하면 이미지를 작성하거나 보는 잦은 트랜잭션과 이미지 크기에 따라 Azure Storage 비용에 영향을 줍니다.

## <a name="enable-preview-images-in-the-video-sink-node"></a>비디오 싱크 노드에서 미리 보기 이미지 사용
미리 보기 이미지를 사용하려면 파이프라인 토폴로지의 비디오 싱크 노드에서 적절한 플래그를 설정해야 합니다. **videoPublishingOptions에서** **enableVideoPreviewImage를** **true로** 설정합니다.  

예제:
```
        "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.VideoSink",
          "name": "videoSink",
          "videoName": "{$parameter-for-specifying-unique-videoName-for-each-pipeline}",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value": "video"
                }
              ]
            }
          ],
          "videoPublishingOptions": {
            "enableVideoPreviewImage": true
          },
          "videoCreationProperties": {
            "title": "{$parameter-for-specifying-unique-title-for-each-pipeline}",
            "description": "{$parameter-for-specifying-unique-description-for-each-pipeline}k",
            "segmentLength": "PT30S"
          },
          "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048"
        }
      ]
``` 

## <a name="accessing-preview-images"></a>미리 보기 이미지 액세스

사용 가능한 미리 보기 이미지에 대한 정적 URL을 얻으려면 권한 있는 [bearer 토큰](../playback-recordings-how-to.md#accessing-videos)를 사용하여 비디오 리소스에서 GET 요청을 호출해야 합니다. 아래와 같이 응답의 **contentUrls** 아래에 나열된 URL이 표시됩니다.

```
      "contentUrls": {
        ...
        "previewImageUrls": {
          "small": "XXXX",
          "medium": "XXXX",
          "large": "XXXX"
         }
       },
    
```

## <a name="next-steps"></a>다음 단계

이 [빠른 시작](detect-motion-record-video-clips-cloud.md) 또는 이 [자습서의](use-continuous-video-recording.md)토폴로지에서 비디오 미리 보기 이미지를 사용하도록 설정해 보세요. 
