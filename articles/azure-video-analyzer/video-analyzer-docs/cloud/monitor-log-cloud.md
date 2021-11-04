---
title: 모니터링 및 로깅-Azure Video Analyzer 서비스
description: 이 문서에서는 Azure Video Analyzer 서비스의 모니터링 및 로깅에 대 한 개요를 제공 합니다.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 370904873a3f86ebee1cd530f349c55306588d08
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564008"
---
# <a name="monitor-and-log"></a>모니터링 및 로깅

[!INCLUDE [header](includes/cloud-env.md)]

이 문서에서는 Azure Video Analyzer 서비스에서 생성 되는 이벤트 및 로그에 대해 알아봅니다. 또한 서비스에서 생성 하는 로그를 사용 하는 방법 및 서비스 이벤트를 모니터링 하는 방법을 알아봅니다.

## <a name="taxonomy-of-events"></a>이벤트 분류

아래 다이어그램은 Video Analyzer 서비스에서 내보내는 이벤트 또는 원격 분석 데이터에 사용 되는 일반적인 분류를 나타냅니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-taxonomy-cloud.png" alt-text="이벤트의 분류를 보여 주는 다이어그램.":::

## <a name="event-schema"></a>이벤트 스키마

 Video Analyzer 서비스에서 생성 되는 이벤트는 시스템 속성, 응용 프로그램 속성 및 본문으로 구성 됩니다.

### <a name="common-properties"></a>공용 속성

모든 이벤트에는 다음과 같은 공용 속성 집합이 있습니다.

| 속성      | 속성 형식       | 데이터 형식 | Description                                                  |
| ------------- | ------------------- | --------- | ------------------------------------------------------------ |
| `trace-id`    | 시스템              | guid      | 고유한 이벤트 ID입니다.                                             |
| `resourceId`  | applicationProperty | 문자열    | Azure Video Analyzer 계정의 ARM 경로입니다.               |
| `subject`     | applicationProperty | 문자열    | 이벤트를 내보내는 엔터티의 하위 경로입니다.                    |
| `time`        | applicationProperty | 문자열    | 이벤트가 생성된 시간입니다.                                |
| `category`    | 시스템              | 문자열    | 감사, 운영, 진단입니다.                              |
| `operationName`| applicationProperty| 문자열    | 이벤트 유형 식별자 (다음 섹션 참조)          |
| `level`        | 시스템             | 문자열    | 이벤트 수준 (정보, 경고, 오류, 중요)입니다.           |
| `body`        | 본문                | object    | 특정 이벤트 데이터입니다.                                       |
| `operationVersion` | 시스템         | 문자열    | 이벤트 데이터 버전 {Major}. 주                           |

## <a name="event-types"></a>일정 유형
Video Analyzer 서비스는 다음과 같은 유형의 이벤트 데이터를 내보냅니다.

**작동:** [파이프라인](../pipeline.md)을 실행 하는 동안 또는 사용자 작업에 의해 생성 된 이벤트입니다.
* 볼륨: 낮음 (몇 분 a 분 또는 그 미만)으로 예상 됩니다. 예제:

| 이벤트        | Level               | 간단한 설명                                              |
| ------------- | ------------------- |  ------------------------------------------------------------ |
|RecordingStarted   |정보 제공| 미디어 기록이 시작 됨|
|RecordingAvailable |정보 제공| 미디어 기록 사용 가능|
|RecordingStopped 됨   |정보 제공| 미디어 기록이 중지 됨|
|PipelineStateChanged   |정보 제공| 파이프라인 상태가 변경 됨|

   *샘플 작업 이벤트*
      
```json
{
  "time": "2021-10-06T21:19:36.0988630Z",
  "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
  "region": "westcentralus",
  "category": "Operational",
  "operationName": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
  "operationVersion": "1.0",
  "level": "Informational",
  "correlationId": "c7887efd-0043-4ada-aa3d-9a411e612497",
  "traceContext": "{\n  \"traceId\": \"e74a9d4c-c4b9-4024-9acf-06be76d978ad\"\n}",
  "properties": {
    "subject": "/livePipelines/livepipeline/sinks/videoSink",
    "body": {
      "type": "video",
      "location": "/videos/livetest1",
      "startTime": "2021-10-06T21:19:32.520Z"
    }
  }
}
```

**진단:** 문제 및/또는 성능을 진단 하는 데 도움이 되는 이벤트입니다.
   * 볼륨: 높을 수 있습니다 (1 분에 몇 번). 저장소 비용에도 영향을 줄 수 있습니다. 진단을 필요로 하거나 문제를 해결 하기 위해 이러한 이벤트만 사용 하도록 권장 합니다. 예제:
    
| 이벤트             | Level           | 간단한 설명                                            |
| ----------------- | ----------------| ------------------------------------------------------------ |
|AuthenticationError|오류|서버/클라이언트 인증 오류|
|AuthorizationError |   오류|  서버/클라이언트 권한 부여 오류|
|FormatError        |   오류|  패키징, 형식 지정 또는 인코딩 문제|
|MediaSessionEstablished|   정보 제공|  SDP 또는 기타 세션 정보|
|NetworkError        |  오류|  DNS, 네트워크 오류|
|ProtocolError        | 오류|  RTSP 또는 기타 프로토콜 오류|
|StorageError        |  오류|  읽기/쓰기 오류 Storage|
|RtspPlaybackSessionEstablished|    정보 제공| RTSP 재생 세션이 설정 되었습니다.|
|RtspPlaybackSessionClosed| 정보 제공|  RTSP 재생 세션이 닫혔습니다.|

*샘플 진단 이벤트*
  
```json
{
  "time": "2021-10-06T21:19:34.1290000Z",
  "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
  "region": "westcentralus",
  "category": "Diagnostics",
  "operationName": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
  "operationVersion": "1.0",
  "level": "Informational",
  "traceContext": "{\n  \"traceId\": \"b03cabe2-b9d1-4be7-9770-4ac9e4fdc012\"\n}",
  "properties": {
    "subject": "/livePipelines/livepipesample/sources/rtspSource",
    "body": {
        "sdp": "SDP:\nv=0\r\no=- 0 0 IN IP4 127.0.0.1\r\ns=No Name\r\nt=0 0\r\na=tool:libavformat 58.76.100\r\nm=video 0 RTP/AVP 96\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1; sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX; profile-level-id=4D0029\r\na=control:streamid=0\r\n"
    }
  }
}

```
**감사:** 이벤트는 API 액세스를 기록 하는 데 사용 됩니다. 
  * 볼륨: 낮음, 감사가 필요한 경우에만 이러한 이벤트를 사용 하도록 권장 합니다.

*샘플 감사 이벤트*
  
```json
{
  "time": "2021-10-07T23:53:31.6792370Z",
  "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
  "region": "westcentralus",
  "category": "Audit",
  "operationName": "Microsoft.VideoAnalyzer.Audit.ResourceGet",
  "level": "Warning",
  "uri": "https://{GUID}.api.{region}.videoanalyzer.azure.net/videos/batchjobsinknode",
  "resultType": "Failed",
  "resultSignature": "403",
  "identity": [
    {
      "alg": "RS256",
      "kid": "{KID}",
      "typ": "JWT"
    },
    {
      "sub": "livetest1",
      "aud": [ "https://{GUID}.streaming.{region}.videoanalyzer.azure.net/{GUID}", "wss://{GUID}.rtsp-tunnel.{region}.videoanalyzer.azure.net/{GUID}" ],
      "exp": 1633410145,
      "iss": "https://{region}.videoanalyzer.azure.net/"
    }
  ],
  "traceContext": "{\n  \"traceId\": \"4bb0dcf5-5c6d-4aa3-8c03-3f3d7e2c6210\"\n}",
  "properties": { "subject": "/videos/batchjobsinknodesample" }
}

```
## <a name="metrics"></a>메트릭

Video Analyzer 서비스는 [파이프라인](../pipeline.md)에 대 한 메트릭을 생성 합니다. 비디오 분석기 계정의 관리 창에서 모니터링 섹션으로 이동 하 여 Azure Portal를 사용 하 여 메트릭에 액세스할 수 있습니다.

| 메트릭 이름                           | 유형    | 차원                                                                              | Description                                                  |
| ------------------------------------- | ------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| 수신 바이트              | 카운터   | 파이프라인 종류, 상태, 토폴로지 이름                                 | 파이프라인이 받은 총 바이트 수입니다. RTSP 원본에 대해서만 지원 됩니다.           |
| 파이프라인                  | 카운터   | 파이프라인 종류, 상태, 토폴로지 이름                        | 여러 상태의 파이프라인을 제공 합니다. 일정 한 간격으로 내보내집니다. |

## <a name="monitoring-of-events"></a>이벤트 모니터링

Video Analyzer 서비스에서 생성 된 이벤트를 저장소 계정에 저장 하 고 Azure monitor를 사용 하 여 사용할 수 있습니다.

**Azure Monitor events 컬렉션**

현재 Video Analyzer 서비스는 저장소 계정에 원격 분석 이벤트를 쓰고 이러한 이벤트를 사용 하 Azure Monitor를 사용 하는 것을 지원 합니다. [Azure Monitor](../../../azure-monitor/overview.md) 를 사용 하 여 서비스에 의해 생성 된 이벤트를 사용 하 고 고객은 Azure Monitor를 통해 기본 제공 되는 모니터링 환경을 제공 합니다.

Azure Monitor를 사용 하 여 모니터링 및 이벤트 수집을 사용 하도록 설정 하는 단계를 수행 합니다.
* Azure Portal에서 Video Analyzer 계정의 **모니터링** 섹션으로 이동한 다음 **진단 설정** 을 선택 합니다.
* **진단 설정 추가** 를 클릭 하 여 다음 로그의 컬렉션을 사용 하도록 설정 합니다.
    *   작동
    *   진단
    *   감사
* 사용 하도록 설정할 로그 범주와 해당 보존 기간을 선택 합니다.
* 대상 세부 정보에 대해 **저장소 계정에 보관** 을 선택 하 고 이러한 이벤트 로그를 저장할 저장소 계정을 지정 합니다.
    > [!IMPORTANT]
    > 현재 Video Analyzer 서비스는 Azure storage 이외의 대상으로 진단을 보내는 기능을 지원 하지 않습니다.

* 진단 설정을 구성한 후 **저장** 을 클릭 합니다.
* 진단 로그에 액세스 하려면 저장소 탐색기로 이동 하 여 저장소 계정을 확장 합니다. 그러면 Blob 컨테이너가 표시 됩니다. ' Insights-로그-범주 ' 컨테이너는 JSON 파일 형식의 로그를 포함 합니다. 
* 원하는 로그 파일을 다운로드 하면 다운로드 한 로그 파일의 내용이 아래 샘플과 같이 표시 됩니다.

```json
{
    "time": "2021-10-06T21:19:36.0988630Z",
    "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
    "region": "westcentralus",
    "category": "Operational",
    "operationName": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
    "operationVersion": "1.0",
    "level": "Informational",
    "correlationId": "c7887efd-0043-4ada-aa3d-9a411e612497",
    "traceContext": "{\n  \"traceId\": \"e74a9d4c-c4b9-4024-9acf-06be76d978ad\"\n}",
    "properties": {
        "subject": "/livePipelines/livepipesample/sinks/videoSink",
        "body": {
            "type": "video",
            "location": "/videos/livetest1",
            "startTime": "2021-10-06T21:19:32.520Z"
        }
    }
}
{
    "time": "2021-10-06T21:19:34.1290000Z",
    "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
    "region": "westcentralus",
    "category": "Diagnostics",
    "operationName": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "operationVersion": "1.0",
    "level": "Informational",
    "traceContext": "{\n  \"traceId\": \"b03cabe2-b9d1-4be7-9770-4ac9e4fdc012\"\n}",
    "properties": {
        "subject": "/livePipelines/livepipe/sources/rtspSource",
        "body": {
            "sdp": "SDP:\nv=0\r\no=- 0 0 IN IP4 127.0.0.1\r\ns=No Name\r\nt=0 0\r\na=tool:libavformat 58.76.100\r\nm=video 0 RTP/AVP 96\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1; sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX; profile-level-id=4D0029\r\na=control:streamid=0\r\n" 
        }
    }
```

**활동 로그** 는 파이프라인 작업에 대해 자동으로 생성 되며, Azure Portal에서 Video Analyzer 계정의 ' 활동 로그 ' 섹션으로 이동 하 여 액세스할 수 있습니다. 계정에 대 한 ARM API 호출 기록과 관련 세부 정보를 볼 수 있습니다.

![활동 로그 샘플](./media/activity-log.png)


## <a name="next-steps"></a>다음 단계

[Azure Video Analyzer 서비스 문제 해결](./troubleshoot.md)
