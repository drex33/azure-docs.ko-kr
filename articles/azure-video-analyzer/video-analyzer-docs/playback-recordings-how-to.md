---
title: 비디오 보기
description: 연속 비디오 녹화를 위해 Azure Video Analyzer를 사용하여 몇 주 또는 몇 달 동안 클라우드에 비디오를 녹화할 수 있습니다. 이벤트 기반 녹화를 통해 관심 있는 클립으로 녹화를 제한할 수도 있습니다. 또한 Video Analyzer 서비스를 사용 하 여 카메라에서 비디오를 캡처하는 경우 비디오를 캡처할 때 스트리밍할 수 있습니다. 이 문서에서는 이러한 비디오를 보는 방법에 대해 설명 합니다.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d6d0971be383cdbbdc3b07579f6936357193603a
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133040169"
---
# <a name="viewing-of-videos"></a>비디오 보기

## <a name="suggested-pre-reading"></a>추천 참고 자료

* [Video Analyzer 비디오 리소스](terminology.md#video)
* [연속 비디오 녹화](continuous-video-recording.md)
* [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)

## <a name="background"></a>배경  

RTSP 카메라에서 기록 하거나 이러한 기록의 일부를 내보내 Azure Video Analyzer 계정에 [비디오 리소스](terminology.md#video) 를 만들 수 있습니다. 비디오 분석기 Api를 사용 하 여 [vm](terminology.md#vms) 을 빌드하는 경우이 문서를 통해 비디오를 보는 방법을 이해할 수 있습니다. 이 문서를 읽은 후에는 [액세스 정책](access-policies.md) 및 [Video Analyzer 플레이어 위젯의](player-widget.md)문서를 검토 해야 합니다. 

Video Analyzer의 기능을 평가 [하는 경우 빠른 시작 또는](edge/detect-motion-record-video-clips-cloud.md) [자습서](edge/use-continuous-video-recording.md)중 하나를 사용 하 고 Azure Portal를 활용 하 여 비디오를 볼 수 있습니다.
<!-- TODO - add a section here about 1P/3P SaaS and how to use widgets to allow end users to view videos without talking to ARM APIs -->

## <a name="creating-videos"></a>비디오 만들기

Video Analyzer edge 모듈을 사용 하 여 비디오를 만드는 몇 가지 방법은 다음과 같습니다.

* 몇 주 또는 몇 달 이상 RTSP 카메라에서 [지속적](continuous-video-recording.md) 으로 (cvr)를 기록 합니다.
* [이벤트 기반 비디오 기록](event-based-video-recording-concept.md) (evr)을 통해 관심 있는 부분만 기록 합니다. 
 
또한 Video Analyzer 서비스를 사용 하 여 CVR를 통해 비디오를 만들 수 있습니다. 서비스를 사용 하 여 비디오 녹화의 일부를 내보내 비디오를 만들 수도 있습니다. 이러한 비디오는 다운로드 가능한 파일 (MP4 파일 형식)을 포함 합니다.

## <a name="accessing-videos"></a>비디오 액세스

ARM API를 쿼리하여 [`Videos`](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/Videos.json) 비디오 리소스의 속성을 볼 수 있습니다. 또한 Video Analyzer를 사용 하면 최종 사용자가 ARM을 거치지 않고 비디오를 찾아보고 볼 수 있는 응용 프로그램을 빌드할 수 있습니다. [공용 끝점](access-public-endpoints-networking.md)에 대 한 문서에서 볼 수 있듯이 비디오 속성을 쿼리 하는 데 사용할 수 있는 클라이언트 api에 액세스할 수 있습니다. 이러한 Api는 클라이언트 API 끝점을 통해 노출 되며,이 끝점은 Azure Portal 또는 ARM API를 통해 비디오 분석기 블레이드의 개요 섹션에서 찾을 수 있습니다 [`VideoAnalyzers`](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/VideoAnalyzers.json) . [액세스 정책을](access-policies.md)통해이 끝점에 대 한 액세스를 제어할 수 있습니다. 그러면 [Video Analyzer 플레이어 위젯의 문서](player-widget.md) 에 해당 방법이 표시 됩니다.

## <a name="determining-that-a-video-recording-is-ready-for-viewing"></a>비디오 녹화를 볼 준비가 되었는지 확인

비디오 리소스가 RTSP 카메라의 기록을 나타내는 경우 기록이 완료 된 후 또는 기록이 진행 되는 동안 [해당 콘텐츠를 스트리밍할](terminology.md#streaming) 수 있습니다. 이는 `canStream` `true` 비디오 리소스에 대해로 설정 되는 플래그를 통해 표시 됩니다. 이러한 비디오는 `type` 로 설정 되 `archive` 고 재생 또는 스트리밍을 위한 URL은에서 반환 됩니다 `archiveBaseUrl` . 

비디오 기록의 일부를 MP4 파일로 내보내는 경우 결과 비디오 리소스가 `type` 로 설정 되 `file` 고 비디오 내보내기 작업이 완료 되 면 재생 하거나 다운로드할 수 있습니다. 이러한 파일의 재생 또는 다운로드 URL은에서 반환 됩니다 `downloadUrl` .
   > [!NOTE]
   > 위의 Url에는 [전달자 토큰이](./access-policies.md#creating-a-token)필요 합니다. 자세한 내용은 [Video Analyzer 플레이어 위젯](player-widget.md) 설명서를 참조 하세요.

## <a name="recording-and-playback-latencies"></a>녹화 및 재생 대기 시간

비디오 분석기에 지 모듈을 사용 하 여 비디오 리소스에 기록 하는 경우 파이프라인 토폴로지에서 속성을 지정 합니다 .이 [ `segmentLength` 속성](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzer.json) 을 사용 하면 모듈이 클라우드에 기록 되기 전의 최소 비디오 기간 (초)을 집계 합니다. 예를 들어 `segmentLength`가 300으로 설정된 경우 모듈은 5분 분량의 비디오를 누적하여 5분 '청크'를 하나 업로드한 후 다음 5분 동안 누적 모드로 전환한 후 다시 업로드합니다. `segmentLength`를 높이면 Azure Storage 트랜잭션 비용을 낮추는 이점이 있습니다. 읽기 및 쓰기가 `segmentLength`(초)마다 한 번만 발생하기 때문입니다. Video Analyzer 서비스를 사용 하는 경우 파이프라인 토폴로지의 [ `segmentLength` 속성](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/resource-manager/Microsoft.Media/preview/2021-11-01-preview/PipelineTopologies.json)은 동일 합니다.

결과적으로 Video Analyzer 계정으로부터의 비디오 스트리밍은 최소한 그 시간만큼 지연됩니다. 

종단 간 대기 시간을 결정 하는 또 다른 요소는 (카메라 앞에서 이벤트가 발생 하는 시간 간의 지연, 재생 장치에서 표시 되는 시간까지), 그림 그룹 [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) 기간입니다. [3 개의 간단한 기술을 사용 하 여 라이브 스트림의 지연을 줄이면](https://medium.com/vrt-digital-studio/reducing-the-delay-of-live-streams-by-using-3-simple-techniques-e8e028b0a641) GOP 기간이 길수록 대기 시간이 길어집니다. 일반적으로 Gop를 사용 하도록 구성 된 감시 및 보안 시나리오에서 사용 되는 IP 카메라가 30 초 보다 깁니다. 이는 전체 대기 시간에 큰 영향을 미칩니다.

## <a name="low-latency-streaming"></a>짧은 대기 시간 스트리밍

비디오 분석기 서비스를 사용 하 여 RTSP 카메라에서 비디오를 캡처 및 기록 하는 경우 [짧은 대기 시간 스트리밍을](terminology.md#low-latency-streaming)사용 하 여 약 2 초 동안 대기 시간으로 비디오를 볼 수 있습니다. 이 서비스는 [비디오 분석기 플레이어 위젯을](player-widget.md) 사용 하는 rtsp 지원 플레이어가 [rtsp 프로토콜](https://datatracker.ietf.org/doc/html/rfc7826.html)을 사용 하 여 비디오를 받을 수 있도록 하는 websocket 터널을 사용할 수 있도록 합니다. 전반적인 대기 시간은 카메라와 클라우드 간의 네트워크 대역폭 뿐만 아니라 클라우드 및 재생 장치 간의 네트워크 대역폭에 따라 달라 지 며 재생 장치의 처리 능력에 따라 달라 집니다. 대기 시간이 짧은 스트리밍에 대 한 URL은에서 반환 됩니다 `rtspTunnelUrl` .

   > [!NOTE]
   > 위의 URL에는 [전달자 토큰이](./access-policies.md#creating-a-token)필요 합니다. 자세한 내용은 [Video Analyzer 플레이어 위젯](player-widget.md) 설명서를 참조 하세요.

## <a name="video-analyzer-player-widget"></a>Video Analyzer 플레이어 위젯
비디오 분석기는 HLS 또는 MPEG-2 또는 RTSP 프로토콜을 통해 재생 장치 (클라이언트)로 스트림을 전달 하는 데 필요한 기능을 제공 합니다. [비디오 분석기 플레이어 위젯을](player-widget.md) 사용 하 여 관련 url과 콘텐츠 권한 부여 토큰을 가져온 다음 클라이언트 앱에서이를 사용 하 여 비디오 및 유추 메타 데이터를 재생 합니다.

Video Analyzer 플레이어 위젯을 설치 하 여 비디오를 볼 수 있습니다. 위젯은 `npm` 또는 `yarn`을 사용하여 설치할 수 있으며 이를 통해 클라이언트 쪽 애플리케이션에 위젯을 포함할 수 있습니다. 다음 명령 중 하나를 실행하여 자체 애플리케이션에 위젯을 포함합니다.

NPM:
```
npm install –-save @azure/video-analyzer-widgets
```
YARN:
```
yarn add @azure/video-analyzer-widgets 
```
또는 다음 예를 사용하여 사전 빌드 위치를 참조하는 스크립트 요소에 type="module"을 추가하여 기존 사전 빌드 스크립트를 포함할 수 있습니다.

```
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script> 
``` 

## <a name="viewing-video-with-inference-results"></a>유추 결과로 비디오 보기
비디오 분석기 edge 모듈을 사용 하 여 비디오를 기록 하는 경우 [파이프라인이](pipeline.md) AI를 사용 하 여 유추 결과를 생성 하는 경우 비디오와 함께 이러한 결과를 기록할 수 있습니다. 비디오를 볼 때 video Analyzer 플레이어 위젯은 비디오의 결과를 오버레이 할 수 있습니다. 자세한 내용은 [이 자습서](edge/record-stream-inference-data-with-video.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [액세스 정책 이해](access-policies.md)
* [Video Analyzer 플레이어 위젯 사용](player-widget.md)
* [가장자리에 대 한 연속 비디오 녹화](edge/use-continuous-video-recording.md)
* [클라우드에서 연속 비디오 녹화](cloud/get-started-livepipelines-portal.md)
