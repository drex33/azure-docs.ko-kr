---
title: 미리 보기 이미지 사용
description: 이 문서에서는 Azure Video Analyzer를 사용 하 여 비디오를 녹화할 때 미리 보기 이미지를 사용 하 고 액세스 하는 방법을 설명 합니다
ms.topic: how-to
ms.date: 11/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ed3ebeffb70b592c1a81e8ec7871a662f7b856bd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053038"
---
# <a name="enable-preview-images-when-recording-video"></a>비디오를 녹화할 때 미리 보기 이미지 사용

Azure Video Analyzer를 사용 하 여 RTSP 카메라에서 [비디오를 캡처 및 기록할](../video-recording.md) 수 있습니다. 이 [빠른](detect-motion-record-video-clips-cloud.md) 시작 또는이 [자습서](use-continuous-video-recording.md)에 표시 된 것 처럼 비디오 싱크 노드를 포함 하는 파이프라인 토폴로지를 만듭니다. 

Video Analyzer edge 모듈을 사용 하 여 비디오를 기록 하는 경우 비디오 싱크 노드를 사용 하도록 설정 하 여 다양 한 크기의 미리 보기 이미지 집합을 정기적으로 생성할 수 있습니다. 이러한 이미지는 비디오 분석기 계정의 [비디오 리소스](../terminology.md#video) 에서 검색할 수 있습니다. 예를 들어 카메라에서 해상도가 1920 x 1080 인 비디오를 생성 하는 경우 미리 보기 이미지의 크기는 다음과 같습니다.

  * 320 x 180: 작음
  * 640 x 360: 중형
  * 1280 x 720: 큼

> [!NOTE]
> 미리 보기 이미지는 카메라의 비디오 가로 세로 비율을 유지 합니다.

미리 보기 이미지는에 의해 결정 되는 빈도에 따라 주기적으로 생성 됩니다 [`segmentLength`](../playback-recordings-how-to.md#recording-and-playback-latencies) . [이벤트 기반 기록을](record-event-based-live-video.md)사용 하는 경우 라이브 파이프라인이 활성 상태이 고 비디오가 기록 될 때만 이미지가 생성 되는지 확인 해야 합니다. 미리 보기 이미지 집합이 생성 될 때마다 이전 집합을 덮어씁니다.

> [!NOTE]
> 이 기능은 현재 Video Analyzer Edge 모듈 에서만 사용할 수 있습니다. 또한이 기능을 사용 하도록 설정 하면 이미지를 쓰고 이미지 크기를 확인 하는 데 자주 사용 되는 Azure storage 비용에 영향을 줍니다.

## <a name="enable-preview-images-in-the-video-sink-node"></a>비디오 싱크 노드에서 미리 보기 이미지 사용
미리 보기 이미지를 사용 하도록 설정 하려면 파이프라인 토폴로지의 비디오 싱크 노드에 적절 한 플래그를 설정 해야 합니다. **VideoPublishingOptions** 에서 **enableVideoPreviewImage** 를 **true** 로 설정 합니다.  

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

사용 가능한 미리 보기 이미지에 대 한 정적 Url을 얻으려면 [권한 있는 전달자 토큰](../playback-recordings-how-to.md#accessing-videos)을 사용 하 여 비디오 리소스에서 GET 요청을 호출 해야 합니다. 아래와 같이 응답의 **contenturls** 아래에 나열 된 url이 표시 됩니다.

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

이 [빠른](detect-motion-record-video-clips-cloud.md) 시작 또는이 [자습서](use-continuous-video-recording.md)의 토폴로지에서 비디오 미리 보기 이미지를 사용 하도록 설정 해 보세요. 
