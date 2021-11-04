---
title: 모니터링 및 로깅-Azure Video Analyzer 서비스
description: 이 문서에서는 Azure Video Analyzer 서비스의 모니터링 및 로깅에 대 한 개요를 제공 합니다.
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ee7e0367dadcc328c90fc3005688992831716c7a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053170"
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
  * 볼륨: 낮음, 감사가 필요한 경우에만 이러한 이벤트를 사용하도록 설정하는 것이 좋습니다.

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

Video Analyzer 서비스는 파이프라인에 대한 메트릭을 [생성합니다.](../pipeline.md) Video Analyzer 계정의 관리 창에서 모니터링 섹션으로 이동하여 Azure Portal 사용하여 메트릭에 액세스할 수 있습니다.

| 메트릭 이름                           | 유형    | 차원                                                                              | Description                                                  |
| ------------------------------------- | ------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| 수신 바이트              | 카운터   | 파이프라인 종류, 상태, 토폴로지 이름                                 | 파이프라인에서 받은 총 바이트 수입니다. RTSP 원본에 대해서만 지원됩니다.           |
| Pipelines                  | 카운터   | 파이프라인 종류, 상태, 토폴로지 이름                        | 파이프라인을 다양한 상태로 제공합니다. 정기적인 간격으로 내전됩니다. |

## <a name="monitoring-of-events"></a>이벤트 모니터링

Video Analyzer 서비스에서 생성된 이벤트를 스토리지 계정에 저장하고 Azure Monitor를 사용하여 사용할 수 있습니다.

**Azure Monitor 이벤트 수집**

Video Analyzer 서비스는 현재 스토리지 계정에 원격 분석 이벤트를 작성하고 Azure Monitor 사용하여 해당 이벤트를 사용하도록 지원합니다. [Azure Monitor](../../../azure-monitor/overview.md) 사용하여 서비스에서 생성된 이벤트를 사용하며, 고객은 Azure Monitor 통해 기본 제공 모니터링 환경을 사용할 수 있습니다.

다음 단계에 따라 Azure Monitor 모니터링 및 이벤트 수집을 사용하도록 설정합니다.
* Azure Portal Video Analyzer 계정의 **모니터링** 섹션으로 이동한 **다음, 진단 설정을** 선택합니다.
* 진단 **설정 추가를** 클릭하여 다음 로그의 컬렉션을 사용하도록 설정합니다.
    *   작동
    *   진단
    *   감사
* 사용하도록 설정할 로그 범주와 해당 보존 기간을 선택합니다.
* 대상 세부 정보는 **스토리지 계정에 보관을** 선택하고 이러한 이벤트 로그를 저장할 스토리지 계정을 지정합니다.
    > [!IMPORTANT]
    > Video Analyzer 서비스는 현재 Azure Storage 이외의 대상으로 진단 전송을 지원하지 않습니다.

* 진단 설정을 구성한 후 **저장을** 클릭합니다.
* 진단 로그에 액세스하려면 Storage Explorer로 이동한 다음, 스토리지 계정을 확장하면 Blob 컨테이너가 표시됩니다. 'insights-logs-category' 컨테이너에는 JSON 파일 형식의 로그가 있습니다. 
* 원하는 로그 파일 다운로드 및 다운로드한 로그 파일의 콘텐츠는 아래 샘플과 유사합니다.

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

또한 **활동 로그는** 파이프라인 작업에 대해 자동으로 생성되며, Azure Portal Video Analyzer 계정의 '활동 로그' 섹션으로 이동하여 액세스할 수 있습니다. 계정에 대한 ARM API 호출의 기록 및 관련 세부 정보를 볼 수 있습니다.

![활동 로그 샘플](./media/activity-log.png)


## <a name="next-steps"></a>다음 단계

[Azure Video Analyzer 서비스 문제 해결](./troubleshoot.md)
