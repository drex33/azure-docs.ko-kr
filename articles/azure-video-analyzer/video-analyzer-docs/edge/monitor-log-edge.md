---
title: 모니터링 및 로깅 - Azure
description: 이 문서에서는 Azure Video Analyzer의 모니터링 및 로깅에 대한 개요를 제공합니다.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ed86a5de4fd5d65ec3e5a3b8112cb96c95f01811
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399942"
---
# <a name="monitor-and-log-on-iot-edge"></a>IoT Edge 모니터링 및 로그온

![에지 아이콘](media/env-icon/edge.png)  
또는 모니터를 체크 [아웃하고 서비스에 로그인합니다.](../cloud/monitor-log-cloud.md)

---

이 문서에서는 Azure Video Analyzer IoT Edge 모듈에서 원격 모니터링에 대한 이벤트를 수신하는 방법을 알아봅니다. 

또한 모듈에서 생성하는 로그를 제어하는 방법에 대해 알아봅니다.

## <a name="taxonomy-of-events"></a>이벤트 분류

IoT Edge에 기반을 둔 Video Analyzer는 다음 분류에 따라 이벤트 또는 원격 분석 데이터를 내보냅니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="이벤트의 분류를 보여 주는 다이어그램.":::

* 작업: 사용자의 작업 또는 [파이프라인](../pipeline.md) 실행 중에 생성된 이벤트
  
   * 볼륨: 낮을 것으로 예상됨(1분에 몇 번 또는 그 미만)
   * 예:

      - 라이브 파이프라인 활성화
      
      - 라이브 파이프라인 비활성화
      
      *샘플 작업 이벤트*
      
      ```json
      {
         "body": {
             "outputType": "filePath",
             "outputLocation": "/var/media/Sample-1-fileSink/sampleFilesFromEVR-motion-fileSinkOutput-20210426T181911Z.mp4"
           },
           "properties": {
             "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/Microsoft.Media/videoAnalyzers/<account-name>",
             "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/fileSink",
             "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
             "eventTime": "2021-04-26T18:19:13.298Z",
             "dataVersion": "1.0"
          },
      }
      ```
* 진단: 성능 문제를 진단하는 데 도움이 되는 이벤트

   * 볼륨: 높을 수 있음(1분에 여러 번)
   * 예:
   
      - RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 정보(다음 예제에 표시됨)       
      - 카메라 또는 AI 확장 등에 대한 연결에 실패하는 경우의 오류
      
    *샘플 진단 이벤트*
  
    ```json
    {
      "body": {
        "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
      },
      "applicationProperties": {
        "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/Microsoft.Media/videoAnalyzers/<account-name>",
        "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
        "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
        "eventTime": "2021-04-26T18:15:13.298Z",
        "dataVersion": "1.0"
      }
    }
    ```
* 분석: 비디오 분석의 결과로 생성된 이벤트

   * 볼륨: 높을 수 있음(1분에 여러 번 또는 그 이상)
   * 예:
     
      - 동작이 감지됨(다음 예제에 표시됨) 
      
      - 유추 결과
     
      *샘플 분석 이벤트*
        
      ```json
      {
        "body": {
          "timestamp": 143039375044290,
          "inferences": [
            {
              "type": "motion",
              "motion": {
                "box": {
                  "l": 0.48954,
                  "t": 0.140741,
                  "w": 0.075,
                  "h": 0.058824
                }
              }
            }
          ]
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/Microsoft.Media/videoAnalyzers/<account-name>",
          "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/processors/md",
          "eventType": "Microsoft.VideoAnalyzer.Analytics.Inference",
          "eventTime": "2021-04-26T18:15:13.298Z",
          "dataVersion": "1.0"
        }
      }
      ```
        
모듈에서 내보낸 이벤트는 [IoT Edge 허브](../../../iot-edge/iot-edge-runtime.md#iot-edge-hub)로 전송됩니다. 여기서 다른 대상으로 라우팅할 수 있습니다. 

### <a name="events-and-video-playback"></a>이벤트 및 비디오 재생

위에서 설명한 것처럼 비디오 분석의 일부로 생성된 이벤트에는 `eventTime`이 연결되어 있습니다. 파이프라인 토폴로지의 일부로 [라이브 비디오를 녹화](../video-recording.md)한 경우 이를 통해 녹화된 비디오에서 특정 이벤트가 발생한 위치를 찾을 수 있습니다. [Video Analyzer 플레이어 위젯](../player-widget.md)에서 비디오 녹화를 로드하고 해당 컨트롤을 사용하여 관심 있는 날짜와 시간을 찾을 수 있습니다. 파이프라인이 AI 모델을 사용하여 유추 결과를 생성하는 데 관여한 경우 비디오와 함께 유추 데이터를 기록해야 합니다. 이렇게 하면 이 [자습서](record-stream-inference-data-with-video.md)에 표시된 대로 비디오와 함께 유추 메타데이터를 재생할 수 있습니다.


## <a name="routing-events"></a>이벤트 라우팅

Video Analyzer 모듈에서 게시한 운영 및 진단 이벤트를 라우팅하는 데 다음 모듈 쌍 속성을 사용할 수 있습니다. 다음 모듈 쌍속성은 [모듈 쌍 JSON 스키마](module-twin-configuration-schema.md)에 설명되어 있습니다.

- `diagnosticsEventsOutputName`: 모듈에서 진단 이벤트를 가져오려면 이 속성을 포함하고 속성에 대한 값을 제공합니다. 모듈이 진단 이벤트를 게시하지 않도록 하려면 이 속성을 생략하거나 비워 둡니다.
  
- `operationalEventsOutputName`: 모듈에서 작업 이벤트를 가져오려면 이 속성을 포함하고 속성에 대한 값을 제공합니다. 모듈이 작업 이벤트를 게시하지 않도록 하려면 이 속성을 생략하거나 비워 둡니다.

진단 및 운영 이벤트는 동작 감지 프로세서 또는 확장 프로세서와 같은 노드에서 자동으로 내보냅니다. IoT Edge 허브로 전송하려면 파이프라인 내에서 IoT 허브 메시지 싱크로 분석 이벤트를 라우팅해야 합니다. 

배포 매니페스트에서 `$edgeHub` 모듈 쌍의 `desired` 속성을 사용하여 [모든 이전 이벤트의 라우팅](../../../iot-edge/module-composition.md#declare-routes)을 제어할 수 있습니다.

```json
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.1",
     "routes": {
       "moduleToHub": "FROM /messages/modules/avaedge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

위의 JSON에서 `avaedge`는 Video Analyzer 모듈의 이름입니다. 회람 규칙은 [Declare 경로](../../../iot-edge/module-composition.md#declare-routes)에 정의된 스키마를 따릅니다.

> [!NOTE]
> 분석 이벤트가 IoT Edge 허브에 도달하게 하려면 모든 동작 감지 프로세서 노드 및/또는 모든 확장 프로세서 노드의 IoT 허브 메시지 싱크 노드 다운스트림이 있어야 합니다.

## <a name="event-schema"></a>이벤트 스키마

에지 디바이스에서 시작되는 이벤트는 에지 또는 클라우드에서 사용할 수 있습니다. Video Analyzer에서 생성한 이벤트는 Azure IoT Hub에 의해 설정된 [스트리밍 메시징 패턴](../../../iot-hub/iot-hub-devguide-messages-construct.md)을 따릅니다. 패턴은 시스템 속성, 애플리케이션 속성 및 본문으로 구성됩니다.

### <a name="summary"></a>요약

IoT Hub를 통해 관찰되는 모든 이벤트에는 공용 속성 세트가 있습니다.

| 속성      | 속성 형식       | 데이터 형식 | Description                                                  |
| ------------- | ------------------- | --------- | ------------------------------------------------------------ |
| `message-id`  | 시스템              | guid      | 고유한 이벤트 ID입니다.                                             |
| `topic`       | applicationProperty | 문자열    | Azure Video Analyzer 계정의 Azure Resource Manager 경로입니다. |
| `subject`     | applicationProperty | 문자열    | 이벤트를 내보내는 엔터티의 하위 경로입니다.                    |
| `eventTime`   | applicationProperty | 문자열    | 이벤트가 생성된 시간입니다.                                |
| `eventType`   | applicationProperty | 문자열    | 이벤트 유형 식별자입니다. (다음 섹션을 참조하세요.)          |
| `body`        | 본문                | object    | 특정 이벤트 데이터입니다.                                       |
| `dataVersion` | applicationProperty | 문자열    | {Major}.{Minor}                                              |

### <a name="properties"></a>속성

#### <a name="message-id"></a>message-id

이벤트의 GUID(Globally Unique Identifier)입니다.

#### <a name="topic"></a>토픽

이벤트를 생성한 Video Analyzer 에지 모듈을 나타냅니다. 예:

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/videoAnalyzers/{accountName}`

#### <a name="subject"></a>subject

이벤트를 내보내는 엔터티입니다. 예:

`/edgeModules/avaedge/livePipelines/{livePipelineName}`<br/>
`/edgeModules/avaedge/livePipelines/{livePipelineName}/sources/{nodeName}`<br/>
`/edgeModules/avaedge/livePipelines/{livePipelineName}/processors/{nodeName}`<br/>
`/edgeModules/avaedge/livePipelines/{livePipelineName}/sinks/{nodeName}`

`subject` 속성을 사용하면 제네릭 이벤트를 내보낸 라이브 파이프라인의 노드에 해당 이벤트를 매핑할 수 있습니다. 예를 들어 RTSP 사용자 이름 또는 암호가 잘못된 경우 생성된 이벤트는 `/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource` 노드의 `Microsoft.VideoAnalyzer.Diagnostics.ProtocolError`입니다.

#### <a name="eventtype"></a>eventType

이벤트 유형은 다음 스키마에 따라 네임스페이스에 할당됩니다.

`Microsoft.VideoAnalyzer.{EventClass}.{EventType}`

이벤트 클래스는 다음 중 하나일 수 있습니다.

| 클래스 이름  | 설명                                                  |
| ----------- | ------------------------------------------------------------ |
| 분석   | 콘텐츠 분석의 일부로 생성되는 이벤트입니다.                |
| 진단 | 문제 및 성능 진단에 도움이 되는 이벤트입니다. |
| 작동 | 리소스 작업의 일부로 생성되는 이벤트입니다.              |

예:

* `Microsoft.VideoAnalyzer.Analytics.Inference`
* `Microsoft.VideoAnalyzer.Diagnostics.AuthorizationError`
* `Microsoft.VideoAnalyzer.Operational.RecordingStarted`

#### <a name="eventtime"></a>eventTime

이벤트 시간은 ISO 8601 문자열로 형식이 지정됩니다. 이벤트가 발생한 시간을 나타냅니다.

예:

`2021-04-26T18:15:13.298Z`

## <a name="metrics"></a>메트릭

이러한 메트릭은 모듈의 포트 9600에서 실행되는 [Prometheus 엔드포인트](https://prometheus.io/docs/instrumenting/exposition_formats/#text-based-format)를 통해 Video Analyzer 모듈에서 보고됩니다.

| 메트릭 이름                           | 유형    | 레이블                                                                              | 설명                                                  |
| ------------------------------------- | ------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| va_active_live_pipelines              | 계기   | iothub, edge_device, module_name, pipeline_topology                                 | 토폴로지당 총 활성 라이브 파이프라인 수입니다.          |
| va_cumulative_latency_seconds_average | 계기   | iothub, edge_device, module_name, pipeline_topology, live_pipeline, node            | 지난 1분간 원본에서 비디오 프레임을 수신한 시간부터 노드를 떠날 때까지의 평균 대기 시간입니다. 프레임이 없는 경우에는 보고되지 않습니다. |
| va_cumulative_latency_seconds_max     | 계기   | iothub, edge_device, module_name, pipeline_topology, live_pipeline, node            | 지난 1분간 원본에서 비디오 프레임을 수신한 시간부터 노드를 떠날 때까지의 최대 대기 시간입니다. 프레임이 없는 경우에는 보고되지 않습니다. |
| va_data_dropped_total                 | 카운터 | iothub, edge_device, module_name, pipeline_topology, live_pipeline, node, data_kind | 삭제된 데이터(이벤트, 미디어 등)의 카운터입니다.      |
| va_received_bytes_total               | 카운터 | iothub, edge_device, module_name, pipeline_topology, live_pipeline, node            | 노드에서 받은 총 바이트 수입니다. RTSP 소스에 대해서만 지원됩니다. |


> [!NOTE]
> Video Analyzer 모듈에 `avaedge`라는 이름을 지정할 경우 `http://avaedge:9600/metrics`에 GET 요청을 보내 메트릭에 액세스할 수 있습니다. 배포에 따라 디바이스에 연결하기 위해 이 포트를 디바이스에 매핑해야 할 수 있습니다.

### <a name="azure-monitor-collection-via-telegraf"></a>Telegraf를 통한 Azure Monitor 컬렉션 

Video Analyzer 모듈에서 메트릭 수집을 사용하도록 설정하려면 다음 단계를 수행합니다. 메트릭은 [Telegraf](https://github.com/influxdata/telegraf)에 의해 수집된 다음, [Azure Monitor](../../../azure-monitor/overview.md)에 [사용자 지정 메트릭](../../../azure-monitor/essentials/metrics-custom-overview.md)으로 업로드됩니다. Telegraf에서 로그는 [Prometheus](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/prometheus) 플러그 인에 의해 스크랩되고, [azure_monitor](https://github.com/influxdata/telegraf/tree/master/plugins/outputs/azure_monitor) 플러그 인에 의해 업로드됩니다.

이 에이전트를 통해 Prometheus 엔드포인트를 노출하는 모든 모듈에서 메트릭을 수집할 수 있습니다.

1. 개발 컴퓨터에 폴더를 만들고 해당 폴더로 이동합니다.
1. 해당 폴더에서 다음 구성이 포함된 `telegraf.toml` 파일을 만듭니다.
    ```toml
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{AVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "{AVA_EDGE_MODULE_NAME}"
      region = "westus2"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > .toml 파일에서 변수를 바꿔야 합니다. 변수는 중괄호(`{}`)로 표시됩니다. 또한 `region`의 값을 업데이트합니다.
1. 동일한 폴더에서 다음 명령이 포함된 Dockerfile을 만듭니다.
    ```docker
    FROM telegraf:1.15.3-alpine
    COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```
1. Docker CLI 명령을 사용하여 Docker 파일을 빌드하고 Azure Container Registry에 이미지를 게시합니다.
   
   Docker CLI를 사용하여 컨테이너 레지스트리에 푸시하는 방법에 대한 자세한 내용은 [Docker 이미지 푸시 및 풀](../../../container-registry/container-registry-get-started-docker-cli.md)을 참조하세요. Azure Container Registry에 대한 자세한 내용은 [설명서](../../../container-registry/index.yml)를 참조하세요.

   ```bash
   # Insert your own container image URL
   docker build . -t myregistry.azurecr.io/telegraf_agent
   docker push myregistry.azurecr.io/telegraf_agent
   ```

1. Azure Container Registry로 푸시가 완료되면 배포 매니페스트 파일에 다음 노드를 추가합니다.
    ```json
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_URL_OF_YOUR_TELEGRAF_IMAGE}"
        },
        "type": "docker",
        "version": "1.0",
        "status": "running",
        "restartPolicy": "always",
        "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    }
    ```
    > [!IMPORTANT]
    > 매니페스트 파일에서 변수를 바꿔야 합니다. 변수는 중괄호(`{}`)로 표시됩니다.


   Azure Monitor는 [서비스 주체를 통해 인증](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)할 수 있습니다.
        
   Azure Monitor Telegraf 플러그 인은 [몇 가지 인증 방법](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication)을 제공합니다. 
  1. 서비스 주체 인증을 사용하려면 다음 환경 변수를 설정합니다.  
     `AZURE_TENANT_ID`: 인증할 테넌트를 지정합니다.  
     `AZURE_CLIENT_ID`: 사용할 앱 클라이언트 ID를 지정합니다.  
     `AZURE_CLIENT_SECRET`: 사용할 앱 비밀을 지정합니다.  
     
     >[!TIP]
     >서비스 주체에게 **모니터링 메트릭 게시자** 역할을 제공할 수 있습니다. **[서비스 주체 만들기](../../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)** 의 단계에 따라 서비스 주체를 만들고 역할을 할당합니다.
1. 모듈을 배포하면 메트릭이 단일 네임스페이스의 Azure Monitor에 표시됩니다. 메트릭 이름은 Prometheus에서 내보낸 것과 일치합니다. 

   이 경우 Azure Portal에서 IoT 허브로 이동하고 왼쪽 창에서 **메트릭** 을 선택합니다. 여기에 메트릭이 표시됩니다. 

### <a name="log-analytics-metrics-collection"></a>Log Analytics 메트릭 수집

[Log Analytics](../../../azure-monitor/logs/log-analytics-tutorial.md)와 함께 [Prometheus 엔드포인트](https://prometheus.io/docs/practices/naming/)를 사용하여 사용된 CPUPercent, MemoryUsedPercent 등의 메트릭을 생성하고 [모니터링](../../../azure-monitor/essentials/metrics-supported.md)할 수 있습니다.   

> [!NOTE]
> 아래 구성은 로그를 수집하지 않고 **메트릭만** 수집합니다. 수집기 모듈을 확장하여 로그를 수집하고 업로드할 수도 있습니다.

[![Log Analytics를 사용한 메트릭 수집을 보여 주는 다이어그램.](./media/telemetry-schema/log-analytics.svg)](./media/telemetry-schema/log-analytics.svg#lightbox)

1. [메트릭 수집](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector) 방법을 알아봅니다.
1. Docker CLI 명령을 사용하여 [Docker 파일](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux)을 빌드하고 Azure Container Registry에 이미지를 게시합니다.
   
   Docker CLI를 사용하여 컨테이너 레지스트리에 푸시하는 방법에 대한 자세한 내용은 [Docker 이미지 푸시 및 풀](../../../container-registry/container-registry-get-started-docker-cli.md)을 참조하세요. Azure Container Registry에 대한 자세한 내용은 [설명서](../../../container-registry/index.yml)를 참조하세요.
1. Azure Container Registry로 푸시가 완료되면 배포 매니페스트에 다음을 삽입합니다.
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_URL_OF_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://avaedge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > `edgeHub`, `edgeAgent`, `avaedge` 모듈은 배포 매니페스트 파일에 정의된 모듈의 이름입니다. 모듈 이름이 일치하는지 확인하세요.   

    다음 단계에 따라 `LogAnalyticsWorkspaceId` 및 `LogAnalyticsSharedKey` 값을 가져올 수 있습니다.
    1. Azure Portal로 이동
    1. Log Analytics 작업 영역 찾기
    1. Log Analytics 작업 영역을 찾았으면 왼쪽 탐색 창에서 `Agents management` 옵션으로 이동합니다.
    1. 사용할 수 있는 작업 영역 ID 및 비밀 키를 확인합니다.

1. 그런 다음 왼쪽 탐색 창에서 `Workbooks` 탭을 클릭하여 통합 문서를 만듭니다.
1. Kusto 쿼리 언어를 사용하여 아래와 같이 쿼리를 작성하고 IoT Edge 모듈에 사용된 CPU 백분율을 가져올 수 있습니다.
    ```kusto
    let cpu_metrics = IoTEdgeMetrics_CL
    | where Name_s == "edgeAgent_used_cpu_percent"
    | extend dimensions = parse_json(Tags_s)
    | extend module_name = tostring(dimensions.module_name)
    | where module_name in ("avaedge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [ ![Kusto 쿼리를 사용한 메트릭을 보여 주는 다이어그램.](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>로깅

다른 IoT Edge 모듈과 마찬가지로 Edge 디바이스에서 [컨테이너 로그를 검사](../../../iot-edge/troubleshoot.md#check-container-logs-for-issues)할 수도 있습니다. [다음 모듈 쌍](module-twin-configuration-schema.md) 속성을 사용하여 로그에 기록되는 정보를 구성할 수 있습니다.

* `logLevel`

   * 허용되는 값은 `Verbose`, `Information`, `Warning`, `Error`, `None`입니다.
   * 기본값은 `Information`입니다. 로그에는 오류, 경고, 정보 메시지가 포함됩니다.
   * 값을 `Warning`으로 설정하면 로그에 오류 및 경고 메시지가 포함됩니다.
   * 값을 `Error`로 설정하면 로그에 오류 메시지만 포함됩니다.
   * 값을 `None`으로 설정하면 로그가 생성되지 않습니다. 이 방법은 권장되지 않습니다.
   * 문제를 진단하기 위해 Azure 지원과 로그를 공유해야 하는 경우에만 `Verbose`를 사용합니다.

* `logCategories`

   * 쉼표로 구분된 하나 이상의 `Application`, `Events`, `MediaPipeline` 값 목록입니다.
   * 기본값은 `Application, Events`입니다.
   * `Application`: 모듈 시작 메시지, 환경 오류, 직접 메서드 호출과 같은 모듈의 개략적인 정보입니다.
   * `Events`:이 문서 앞부분에서 설명한 모든 이벤트입니다.
   * `MediaPipeline`: RTSP 지원 카메라와의 연결 설정 문제와 같은 문제를 해결할 때 인사이트를 제공할 수 있는 하위 수준 로그입니다.
   
### <a name="generating-debug-logs"></a>디버그 로그 생성
경우에 따라 Azure 지원에서 문제를 해결하도록 지원하기 위해 앞에서 설명한 것보다 더 자세한 로그를 생성해야 할 수도 있습니다. 로그를 생성하려면 다음을 수행하세요.  

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT 허브로 이동합니다.
1. 왼쪽 창에서 **IoT Edge** 를 선택합니다.
1. 디바이스 목록에서 대상 디바이스의 ID를 선택합니다.
1. 창의 위쪽에서 **모듈 설정** 을 선택합니다.

   ![Azure Portal의 "모듈 설정" 단추 스크린샷](media/troubleshoot/set-modules.png)

1. **IoT Edge 모듈** 섹션에서 **avaedge** 를 찾아서 선택합니다.
1. **모듈 ID 트윈** 을 선택합니다. 편집 가능한 창이 열립니다.
1. **desired key** 아래에 다음 키/값 쌍을 추가합니다.

   `"DebugLogsDirectory": "/var/lib/videoanalyzer/logs"`

   > [!NOTE]
   > 이 명령은 에지 디바이스와 컨테이너 사이의 로그 폴더를 바인딩합니다. 디바이스의 다른 위치에서 로그를 수집하려면:
   >
   > 1. **Binds** 섹션에서 **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** 및 **$DEBUG_LOG_LOCATION** 을 원하는 위치로 바꿔서 디버그 로그 위치에 대한 바인딩을 만듭니다. `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
   > 2. 다음 명령에서 **$DEBUG _LOG_LOCATION** 을 이전 단계에서 사용한 위치로 바꿔서 사용합니다.`"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`

1. **저장** 을 선택합니다.

이제 모듈에서 디바이스 스토리지 경로 `/var/local/videoAnalyzer/debuglogs/`에 디버그 로그를 이진 형식으로 씁니다. 로그를 Azure 지원과 공유할 수 있습니다.  

**모듈 ID 쌍** 의 값을 _null_ 로 설정하여 로그 수집을 중지할 수 있습니다. **모듈 ID 쌍** 페이지로 돌아가서 다음 매개 변수를 다음과 같이 업데이트합니다.

   `"DebugLogsDirectory": ""`

## <a name="faq"></a>FAQ

질문이 있는 경우 [모니터링 및 메트릭 FAQ](faq.yml#monitoring-and-metrics)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Azure Video Analyzer 문제 해결](troubleshoot.md)
