---
title: 에지에서 연속 비디오 녹화
description: CVR(연속 비디오 녹화)은 라이브 비디오 원본에서 지속적으로 녹화하는 프로세스를 다룹니다. 이 항목에서는 CVR의 내용과 Azure Video Analyzer에서 CVR을 사용하는 방법에 대해 설명합니다.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 89ea5905639c5d2ff24968072ed0eea13041e122
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133051465"
---
# <a name="continuous-video-recording"></a>연속 비디오 녹화    

연속 비디오 녹화(CVR)는 비디오 원본에서 비디오를 지속적으로 녹화하는 프로세스를 다룹니다. Azure Video Analyzer는 RTSP 원본 노드와 비디오 싱크 노드로 구성된 비디오 처리 [파이프라인 토폴로지](pipeline.md)를 통해 CCTV 카메라에서 연중무휴 연속 비디오 녹화를 지원합니다. 아래 다이어그램은 해당 파이프라인의 그래픽 표현을 보여 줍니다. 토폴로지의 JSON 표현은 이 [문서](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json)에서 찾을 수 있습니다. 이러한 토폴로지는 임의로 긴 녹음(몇 년 분량의 콘텐츠)을 만들 수 있습니다. 녹음에 대한 타임스탬프는 UTC로 저장됩니다.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording/continuous-video-recording-overview.svg" alt-text="연속 비디오 녹화":::

위에 나타낸 파이프라인 토폴로지의 인스턴스는 비디오 [싱크가 비디오 리소스](terminology.md#video)에지 로 녹화된 Video Analyzer 서비스의 에지 디바이스에서 실행할 수 있습니다. 파이프라인이 활성화된 상태로 유지되는 동안에는 비디오가 녹화됩니다. Video Analyzer의 스트리밍 기능을 사용하여 녹화된 비디오를 재생할 수 있습니다. 자세한 내용은 [비디오 녹화 재생](playback-recordings-how-to.md)을 참조하세요.

## <a name="suggested-pre-reading"></a>추천 참고 자료  

진행하기 전에 다음 문서를 읽는 것이 좋습니다.

* [파이프라인 토폴로지 개념](pipeline.md)
* [비디오 녹화 개념](video-recording.md) 
 
## <a name="resilient-recording"></a>복원력 있는 녹화

Video Analyzer 에지 모듈은 에지 디바이스가 클라우드와의 연결이 끊어지거나 사용 가능한 대역폭이 저하될 수 있는 조건에서 작동을 지원합니다. 이를 설명하기 위해 원본의 비디오는 캐시에 로컬로 녹화되고 주기적으로 비디오 리소스와 자동으로 동기화됩니다. [파이프라인 토폴로지](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-video-sink/topology.json)를 살펴보면 다음과 같은 속성이 정의되어 있는 것을 알 수 있습니다.

```
"segmentLength": "PT30S",
"localMediaCacheMaximumSizeMiB": "2048",
"localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
```

후자의 두 속성은 복원력 있는 녹화와 관련이 있습니다(둘 다 비디오 싱크 노드에 대한 필수 속성이기도 함). `localMediaCachePath` 속성은 클라우드에 업로드하기 전에 해당 폴더 경로를 사용하여 미디어 데이터를 캐시하도록 비디오 싱크에 지시합니다. [이](../../iot-edge/how-to-access-host-storage-from-module.md) 문서를 참조하여 에지 모듈이 디바이스의 로컬 스토리지를 어떻게 활용할 수 있는지 파악할 수 있습니다. `localMediaCacheMaximumSizeMiB` 속성은 비디오 싱크가 캐시로 사용할 수 있는 디스크 공간의 크기(1MiB = 1024 * 1024바이트)를 정의합니다. 

에지 모듈이 긴 시간 동안 연결이 끊어지고 캐시 폴더에 저장된 콘텐츠가 `localMediaCacheMaximumSizeMiB` 값에 도달하는 경우 비디오 싱크는 가장 오래된 데이터부터 캐시에서 데이터를 삭제하기 시작합니다. 예를 들어 디바이스에서 오전 10시에 연결이 끊어진 경우 캐시가 오후 6시의 최대 한도에 도달하면 비디오 싱크는 오전 10시에 기록된 데이터를 삭제하기 시작합니다. 

네트워크 연결이 복원되면 비디오 싱크는 가장 오래된 데이터부터 다시 캐시에서 업로드를 시작합니다. 위의 예제에서는 연결이 복원된 시간(6:02PM)까지 캐시에서 5분 분량의 비디오를 삭제해야 한다고 가정합니다. 그러면 비디오 싱크가 10:05AM부터 업로드를 시작합니다.

녹화의 간격도 발생할 수 있습니다. 예를 들어 어떤 이유로든 파이프라인을 다시 시작하는 경우에도 발생할 수 있습니다. 파이프라인을 중지하고 나중에 다시 시작할 수도 있습니다. 카메라 설정이 변경되지 않는 한 동일한 Video Analyzer 비디오 리소스에 계속 녹화할 수 있습니다.

## <a name="segmented-recording"></a>분할된 녹화  

위에 표시된 `segmentLength` 속성은 비디오 리소스를 녹화하는 스토리지 계정에 데이터를 쓰는 작업과 관련된 쓰기 트랜잭션 비용을 제어하는 데 도움이 됩니다. 예를 들어 값을 30초에서 5분으로 늘리면 스토리지 트랜잭션 수가 10(5*60/30) 단위로 감소합니다.

`segmentLength`속성은 비디오가 스토리지 계정에 최대 초당 한 번 기록되도록 `segmentLength` 합니다. 이 속성의 최솟값은 30초(기본값)이며, 최대 5분까지 30초 단위로 늘릴 수 있습니다.

이 속성은 Video Analyzer 에지 모듈과 Video Analyzer 서비스 모두에 적용됩니다. `segmentLength`가 재생에 미치는 영향에 대해서는 [비디오 녹화 재생](playback-recordings-how-to.md) 문서를 참조하세요.

## <a name="see-also"></a>참조

* [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md) 
* [비디오 녹화 재생](playback-recordings-how-to.md) 

## <a name="next-steps"></a>다음 단계

[자습서: 연속 비디오 녹화](edge/use-continuous-video-recording.md) 
