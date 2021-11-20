---
title: 공간 분석 컨테이너에 대한 원격 분석 및 로깅
titleSuffix: Azure Cognitive Services
description: 공간 분석은 일반적인 구성 프레임워크 인사이트, 로깅 및 보안 설정을 각 컨테이너에 제공합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: pafarley
ms.openlocfilehash: 9a99f6dfb8e76f6e90e655f2ff45b644e35ff1c6
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132870524"
---
# <a name="telemetry-and-troubleshooting"></a>원격 분석 및 문제 해결

공간 분석에는 시스템 상태를 모니터링하고 문제를 진단하는 데 도움이 되는 기능 세트가 포함되어 있습니다.

## <a name="enable-visualizations"></a>시각화 사용

비디오 프레임에서 AI Insight 이벤트의 시각화를 사용하도록 설정하려면 `.debug` 데스크톱 머신 또는 Azure VM에서 [공간 분석 작업의](spatial-analysis-operations.md) 버전을 사용해야 합니다. Azure Stack Edge 디바이스에서는 시각화가 가능하지 않습니다. 대신 네 가지 디버그 작업을 사용할 수 있습니다.

디바이스가 로컬 데스크톱 머신 또는 Azure GPU VM(원격 데스크톱 사용)인 경우 `.debug` 모든 작업의 버전으로 전환하고 출력을 시각화할 수 있습니다.

1.  로컬로 또는 공간 분석을 실행하는 호스트 컴퓨터에서 원격 데스크톱 클라이언트를 사용하여 데스크톱을 엽니다. 
2.  터미널에서 를 실행합니다. `xhost +`
3.  모듈의 [배포 매니페스트를](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) `spaceanalytics` 환경 변수 값으로 업데이트합니다. `DISPLAY` 호스트 컴퓨터의 터미널에서 를 실행하여 해당 값을 찾을 수 `echo $DISPLAY` 있습니다.
    ```
    "env": {        
        "DISPLAY": {
            "value": ":11"
            }
    }
    ```
4. 디버그 모드에서 실행하려는 배포 매니페스트에서 그래프를 업데이트합니다. 아래 예제에서는 operationId를 cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug로 업데이트합니다. 시각화 도우미 창을 사용하려면 새 매개 `VISUALIZER_NODE_CONFIG` 변수가 필요합니다. 모든 작업은 디버그에서 사용할 수 있습니다. 공유 노드를 사용하는 경우 cognitiveservices.vision.spatialanalysis.debug 작업을 사용하고 인스턴스 매개 변수에 를 `VISUALIZER_NODE_CONFIG` 추가합니다. 

    ```
    "zonecrossing": {
        "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug",
        "version": 1,
        "enabled": true,
        "parameters": {
            "VIDEO_URL": "Replace http url here",
            "VIDEO_SOURCE_ID": "zonecrossingcamera",
            "VIDEO_IS_LIVE": false,
            "VIDEO_DECODE_GPU_INDEX": 0,
            "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
            "CAMERACALIBRATOR_NODE_CONFIG": "{ \"gpu_index\": 0}",
            "VISUALIZER_NODE_CONFIG": "{ \"show_debug_video\": true }",
            "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"threshold\":35.0}]}"
        }
    }
    ```
    
5. 다시 배포하면 호스트 컴퓨터에 시각화 도우미 창이 표시됩니다.
6. 배포가 완료되면 `.Xauthority` 호스트 컴퓨터에서 컨테이너로 파일을 복사하고 다시 시작해야 할 수 있습니다. 아래 예제에서 `peopleanalytics`는 호스트 컴퓨터의 컨테이너 이름입니다.

    ```bash
    sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
    sudo docker stop peopleanalytics
    sudo docker start peopleanalytics
    xhost +
    ```

## <a name="collect-system-health-telemetry"></a>시스템 상태 원격 분석 수집

telegraf는 공간 분석에서 작동하는 오픈 소스 이미지이며, Microsoft Container Registry에서 사용할 수 있습니다. 다음 입력을 받아 Azure Monitor에 보냅니다. telegraf 모듈은 원하는 사용자 지정 입력 및 출력을 사용하여 빌드할 수 있습니다. 공간 분석의 telegraf 모듈 구성은 배포 매니페스트(위에서 연결됨)의 일부입니다. 이 모듈은 선택 사항이며, 필요하지 않은 경우 매니페스트에서 제거할 수 있습니다. 

입력: 
1. 공간 분석 메트릭
2. 디스크 메트릭
3. CPU 메트릭
4. Docker 메트릭
5. GPU 메트릭

출력:
1. Azure Monitor

제공된 공간 분석 telegraf 모듈은 공간 분석 컨테이너에서 내보낸 모든 원격 분석 데이터를 Azure Monitor에 게시합니다. Azure Monitor를 구독에 추가하는 방법에 대한 자세한 내용은 [Azure Monitor](../../azure-monitor/overview.md)를 참조하세요.

Azure Monitor가 설정되면 모듈에서 원격 분석을 보낼 수 있도록 하는 자격 증명을 만들어야 합니다. Azure Portal을 사용하여 새 서비스 주체를 만들거나 아래의 Azure CLI 명령을 사용하여 만들 수 있습니다.

> [!NOTE] 
> 이 명령을 사용하려면 구독에 대한 소유자 권한이 있어야 합니다. 

```azurecli
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/..."
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

[Azure Stack Edge 디바이스](https://go.microsoft.com/fwlink/?linkid=2142179), [데스크톱 컴퓨터](https://go.microsoft.com/fwlink/?linkid=2152270) 또는 [GPU가 있는 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189)에 대한 배포 매니페스트에서 *telegraf* 모듈을 찾고, 다음 값을 이전 단계의 서비스 주체 정보로 바꾸고, 다시 배포합니다.

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

telegraf 모듈이 배포되면 Azure Monitor 서비스를 통해 또는 Azure Portal의 IoT Hub에서 **모니터링** 을 선택하여 보고된 메트릭에 액세스할 수 있습니다.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Azure Monitor 원격 분석 보고서":::

### <a name="system-health-events"></a>시스템 상태 이벤트

| 이벤트 이름                  | 설명    |
|-----------------------------|-------------------------------------------------------------------------------------------|
| archon_exit                 | 사용자가 공간 분석 모듈 상태를 *실행 중* 에서 *중지됨* 으로 변경하면 전송됩니다.  |
| archon_error                | 컨테이너 내부의 프로세스가 중단되면 전송됩니다. 심각한 오류입니다.      |
| InputRate                   | 그래프에서 비디오 입력을 처리하는 속도입니다. 5분마다 보고됩니다.              |
| OutputRate                  | 그래프에서 AI 인사이트를 출력하는 속도입니다. 5분마다 보고됩니다.                |
| archon_allGraphsStarted     | 모든 그래프의 시작이 완료되면 전송됩니다.                                           |
| archon_configchange         | 그래프 구성이 변경되면 전송됩니다.                                              |
| archon_graphCreationFailed  | 보고된 `graphId`가 있는 그래프가 시작되지 않으면 전송됩니다.                           |
| archon_graphCreationSuccess | 보고된 `graphId`가 있는 그래프가 성공적으로 시작되면 전송됩니다.                      |
| archon_graphCleanup         | 보고된 `graphId`가 있는 그래프가 정리되고 종료되면 전송됩니다.                      |
| archon_graphHeartbeat       | 기술의 모든 그래프에 대해 1분마다 전송됩니다.                                   |
| archon_apiKeyAuthFail       | 할당량 부족, 잘못됨, 오프라인과 같은 이유로 인해 Computer Vision 리소스 키가 컨테이너를 24시간을 초과할 때까지 인증하지 못하면 전송됩니다. |
| VideoIngesterHeartbeat      | 비디오가 비디오 원본에서 스트림되었음을 나타내기 위해 1시간마다 해당 시간의 오류 수와 함께 전송됩니다. 각 그래프에 대해 보고됩니다. |
| VideoIngesterState          | 비디오 스트리밍에 대해 *중지됨* 또는 *시작됨* 상태를 보고합니다. 각 그래프에 대해 보고됩니다.              |

##  <a name="troubleshooting-an-iot-edge-device"></a>IoT Edge 디바이스 문제 해결

`iotedge` 명령줄 도구를 사용하여 실행 중인 모듈의 상태 및 로그를 확인할 수 있습니다. 예를 들면 다음과 같습니다.
* `iotedge list`: 실행 중인 모듈의 목록을 보고합니다. 
  `iotedge logs edgeAgent`를 사용하여 오류를 추가로 확인할 수 있습니다. `iotedge`가 중단되면 `iotedge restart edgeAgent`를 사용하여 다시 시작해 볼 수 있습니다.
* `iotedge logs <module-name>`
* `iotedge restart <module-name>`: 특정 모듈을 다시 시작합니다. 

## <a name="collect-log-files-with-the-diagnostics-container"></a>진단 컨테이너를 사용하여 로그 파일 수집

공간 분석은 런타임 문제를 진단하거나 지원 티켓에 포함하는 데 사용할 수 있는 Docker 디버깅 로그를 생성합니다. 공간 분석 진단 모듈은 Microsoft Container Registry에서 다운로드할 수 있습니다. [Azure Stack Edge 디바이스](https://go.microsoft.com/fwlink/?linkid=2142179), [데스크톱 컴퓨터](https://go.microsoft.com/fwlink/?linkid=2152270) 또는 [GPU가 있는 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189)에 대한 매니페스트 배포 파일에서 *diagnostics* 모듈을 찾습니다.

"env" 섹션에서 다음 구성을 추가합니다.

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Azure Blob Storage와 같은 원격 엔드포인트에 업로드된 로그를 최적화하려면 작은 파일 크기를 유지하는 것이 좋습니다. 권장되는 Docker 로그 구성은 아래 예제를 참조하세요.

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>로그 수준 구성

로그 수준 구성을 사용하면 생성된 로그의 자세한 정보 표시 수준을 제어할 수 있습니다. 지원되는 로그 수준은 `none`, `verbose`, `info`, `warning` 및 `error`입니다. 노드와 플랫폼 모두에 대한 기본 로그의 자세한 정보 표시 수준은 `info`입니다. 

`ARCHON_LOG_LEVEL` 환경 변수를 허용된 값 중 하나로 설정하여 로그 수준을 전체적으로 수정할 수 있습니다.
또한 아래와 같이 `platformLogLevel` 및 `nodesLogLevel`의 값을 설정하여 IoT Edge 모듈 쌍 문서를 통해 모든 배포된 모듈에 대해 전체적으로 설정하거나 모든 특정 기술에 대해 설정할 수 있습니다.

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodesLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>로그 수집

> [!NOTE]
> `diagnostics` 모듈은 로깅 콘텐츠에 영향을 주지 않으며, 기존 로그를 수집, 필터링 및 업로드하는 데 도움이 됩니다.
> 이 모듈을 사용하려면 Docker API 버전 1.40 이상이 있어야 합니다.

[Azure Stack Edge 디바이스](https://go.microsoft.com/fwlink/?linkid=2142179), [데스크톱 컴퓨터](https://go.microsoft.com/fwlink/?linkid=2152270) 또는 [GPU가 있는 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189)에 대한 샘플 배포 매니페스트 파일에는 로그를 수집하고 업로드하는 `diagnostics`라는 모듈이 포함되어 있습니다. 이 모듈은 기본적으로 사용하지 않도록 설정되어 있으며, 로그에 액세스해야 하는 경우 IoT Edge 모듈 구성을 통해 사용하도록 설정해야 합니다. 

`diagnostics` 수집은 주문형 및 IoT Edge 직접 메서드를 통해 제어되며, 로그를 Azure Blob Storage에 보낼 수 있습니다.

### <a name="configure-diagnostics-upload-targets"></a>진단 업로드 대상 구성

IoT Edge 포털에서 디바이스를 선택한 다음, **diagnostics** 모듈을 선택합니다. [Azure Stack Edge 디바이스](https://go.microsoft.com/fwlink/?linkid=2142179), [데스크톱 컴퓨터](https://go.microsoft.com/fwlink/?linkid=2152270) 또는 [GPU가 있는 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189)에 대한 샘플 배포 매니페스트 파일에서 진단에 대한 `env`라는 **환경 변수** 섹션을 찾고, 다음 정보를 추가합니다.

**Azure Blob Storage에 업로드 구성**

1. 아직 없는 경우 사용자 고유의 Azure Blob Storage 계정을 만듭니다.
2. Azure Portal에서 스토리지 계정에 대한 **연결 문자열** 을 가져옵니다. 이는 **액세스 키** 에 있습니다.
3. 공간 분석 로그는 `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` 파일 이름 형식으로 *rtcvlogs* 라는 Blob Storage 컨테이너에 자동으로 업로드됩니다.

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>공간 분석 로그 업로드

로그는 `diagnostics` 모듈에서 `getRTCVLogs` IoT Edge 메서드를 사용하여 주문형 방식으로 업로드됩니다. 


1. IoT Hub 포털 페이지로 이동하여 **Edge 디바이스** 를 선택하고, 디바이스 및 진단 모듈을 선택합니다. 
2. 모듈의 세부 정보 페이지로 이동하여 ***직접 메서드*** 탭을 클릭합니다.
3. [메서드 이름]에서 `getRTCVLogs`를 입력하고, [페이로드]에서 json 형식 문자열을 입력합니다. 빈 페이로드(`{}`)를 입력할 수 있습니다. 
4. 연결 및 메서드 시간 제한을 설정하고, **메서드 호출** 을 클릭합니다.
5. 대상 컨테이너를 선택하고, **로깅 구문** 섹션에서 설명하는 매개 변수를 사용하여 페이로드 json 문자열을 작성합니다. **메서드 호출** 을 클릭하여 요청을 수행합니다.

>[!NOTE]
> 빈 페이로드를 사용하여 `getRTCVLogs` 메서드를 호출하면 디바이스에 배포된 모든 컨테이너 목록이 반환됩니다. 메서드 이름은 대/소문자를 구분합니다. 잘못된 메서드 이름이 지정되면 501 오류가 발생합니다.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="getRTCVLogs 메서드 호출 ":::
![getRTCVLogs 직접 메서드 페이지](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>로깅 구문

아래 표에는 로그를 쿼리할 때 사용할 수 있는 매개 변수가 나와 있습니다.

| 키워드 | 설명 | 기본값 |
|--|--|--|
| StartTime | 원하는 로그 시작 시간(밀리초 UTC)입니다. | `-1`, 컨테이너 런타임의 시작입니다. `[-1.-1]`이 시간 범위로 사용되면 API에서 지난 1시간 동안의 로그를 반환합니다.|
| EndTime | 원하는 로그 종료 시간(밀리초 UTC)입니다. | `-1`, 현재 시간입니다. `[-1.-1]` 시간 범위가 사용되면 API에서 지난 1시간 동안의 로그를 반환합니다. |
| ContainerId | 로그를 가져오는 대상 컨테이너입니다.| `null`, 컨테이너 ID가 없는 경우입니다. API에서 ID와 함께 사용 가능한 모든 컨테이너 정보를 반환합니다.|
| DoPost | 업로드 작업을 수행합니다. `false`로 설정되면 요청된 작업을 수행하고, 업로드를 수행하지 않고 업로드 크기를 반환합니다. `true`로 설정되면 선택한 로그의 비동기 업로드를 시작합니다. | `false`, 업로드하지 않습니다.|
| 제한 | 일괄 처리당 업로드할 로그의 줄 수를 표시합니다. | `1000`, 이 매개 변수를 사용하여 POST 속도를 조정합니다. |
| 필터 | 업로드할 로그를 필터링합니다. | `null`, 필터는 공간 분석 로그 구조체(`[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]`)에 따라 키 값 쌍으로 지정할 수 있습니다. `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

다음 표에는 쿼리 응답의 특성이 나와 있습니다.

| 키워드 | 설명|
|--|--|
|DoPost| *true* 또는 *false* 입니다. 로그가 업로드되었는지 여부를 나타냅니다. 로그를 업로드하지 않도록 선택하면 API에서 정보를 ***동기적** _으로 반환합니다. 로그를 업로드하도록 선택하면 요청이 유효한 경우 API에서 200을 반환하고 로그 업로드를 _*_비동기적으로_** 시작합니다.|
|TimeFilter| 로그에 적용되는 시간 필터입니다.|
|ValueFilters| 로그에 적용되는 키워드 필터입니다. |
|타임스탬프| 메서드 실행 시작 시간입니다. |
|ContainerId| 대상 컨테이너 ID입니다. |
|FetchCounter| 총 로그 줄 수입니다. |
|FetchSizeInByte| 총 로그 데이터 양(바이트)입니다. |
|MatchCounter| 유효한 로그 줄 수입니다. |
|MatchSizeInByte| 유효한 로그 데이터 양(바이트)입니다. |
|FilterCount| 필터를 적용한 후의 총 로그 줄 수입니다. |
|FilterSizeInByte| 필터를 적용한 후의 총 로그 데이터 양(바이트)입니다. |
|FetchLogsDurationInMiliSec| 가져오기 작업 기간입니다. |
|PaseLogsDurationInMiliSec| 필터 작업 기간입니다. |
|PostLogsDurationInMiliSec| POST 작업 기간입니다. |

#### <a name="example-request"></a>요청 예 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>예제 응답 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

가져오기 로그의 줄, 시간 및 크기를 확인합니다. 해당 설정이 올바르게 지정되었으면 ***DoPost*** 를 `true`로 바꿉니다. 그러면 동일한 필터가 있는 로그가 대상으로 푸시됩니다. 

문제를 해결할 때 Azure Blob Storage에서 로그를 내보낼 수 있습니다. 

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Azure Stack Edge 디바이스 문제 해결

다음 섹션은 Azure Stack Edge 디바이스의 상태를 디버그하고 확인하는 데 도움이 됩니다.

### <a name="access-the-kubernetes-api-endpoint"></a>Kubernetes API 엔드포인트에 액세스 

1. 디바이스의 로컬 UI에서 **디바이스** 페이지로 이동합니다. 
2. **디바이스 엔드포인트** 아래에서 Kubernetes API 서비스 엔드포인트를 복사합니다. 이 엔드포인트는 `https://compute..[device-IP-address]` 같은 형식의 문자열입니다.
3. 엔드포인트 문자열을 저장합니다. 이는 나중에 Kubernetes 클러스터에 액세스하도록 `kubectl`을 구성할 때 사용합니다.

### <a name="connect-to-powershell-interface"></a>PowerShell 인터페이스에 연결

원격으로 Windows 클라이언트에서 연결합니다. Kubernetes 클러스터가 만들어지면 이 클러스터를 통해 애플리케이션을 관리할 수 있습니다. 디바이스의 PowerShell 인터페이스에 연결해야 합니다. 클라이언트의 운영 체제에 따라 원격으로 디바이스에 연결하는 절차가 다를 수 있습니다. PowerShell을 실행하는 Windows 클라이언트에 대한 단계는 다음과 같습니다.

> [!TIP]
> * 시작하기 전에 Windows 클라이언트에서 Windows PowerShell 5.0 이상을 실행하고 있는지 확인합니다.
> * PowerShell은 [Linux에서도 사용](/powershell/scripting/install/installing-powershell-core-on-linux)할 수 있습니다.

1. 관리자 권한으로 Windows PowerShell 세션을 실행합니다. 
    1. Windows 원격 관리 서비스가 클라이언트에서 실행되고 있는지 확인합니다. 명령 프롬프트에서 `winrm quickconfig`를 입력합니다.

2. 디바이스 IP 주소에 대한 변수를 할당합니다. 예: `$ip = "<device-ip-address>"`.

3. 다음 명령을 사용하여 디바이스의 IP 주소를 클라이언트의 신뢰할 수 있는 호스트 목록에 추가합니다. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. 디바이스에서 Windows PowerShell 세션을 시작합니다. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. 메시지가 표시되면 암호를 제공합니다. 로컬 웹 인터페이스에 로그인하는 데 사용되는 것과 동일한 암호를 사용합니다. 기본 로컬 웹 인터페이스 암호는 `Password1`입니다. 

### <a name="access-the-kubernetes-cluster"></a>Kubernetes 클러스터에 액세스

Kubernetes 클러스터가 만들어지면 `kubectl` 명령줄 도구를 사용하여 클러스터에 액세스할 수 있습니다.

1. 새 네임스페이스를 만듭니다. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. 사용자를 만들고 config 파일을 가져옵니다. 이 명령은 Kubernetes 클러스터에 대한 구성 정보를 출력합니다. 이 정보를 복사하여 *config* 라는 파일에 저장합니다. 파일 확장명 없이 파일을 저장하세요.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. *config* 파일을 로컬 컴퓨터의 사용자 프로필에 있는 *.kube* 폴더에 추가합니다.    

4. 네임스페이스를 만든 사용자와 연결합니다.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. 다음 명령을 사용하여 `kubectl`을 Windows 클라이언트에 설치합니다.
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. DNS 항목을 시스템의 호스트 파일에 추가합니다. 
    1. 관리자 권한으로 메모장을 실행하고, `C:\windows\system32\drivers\etc\hosts`에 있는 *hosts* 파일을 엽니다. 
    2. 로컬 UI의 **디바이스** 페이지에서 가져온 디바이스 IP 주소 및 DNS 도메인을 사용하여 항목을 hosts 파일에 만듭니다. 사용해야 하는 엔드포인트는 `https://compute.asedevice.microsoftdatabox.com/10.100.10.10`과 비슷합니다.

7. Kubernetes Pod에 연결할 수 있는지 확인합니다.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

컨테이너 로그를 가져오려면 다음 명령을 실행합니다.

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>유용한 명령

|명령  |설명  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Kubernetes 구성 파일을 생성합니다. 명령을 사용할 때 정보를 *config* 라는 파일에 복사합니다. 파일 확장명 없이 파일을 저장하세요.        |
| `Get-HcsApplianceInfo` | 디바이스에 대한 정보를 반환합니다. |
| `Enable-HcsSupportAccess` | 지원 세션을 시작하기 위한 액세스 자격 증명을 생성합니다. |


## <a name="how-to-file-a-support-ticket-for-spatial-analysis"></a>공간 분석에 대한 지원 티켓을 제출하는 방법 

공간 분석 컨테이너와 관련된 문제에 대한 해결 방법을 찾는 데 더 많은 지원이 필요한 경우 다음 단계에 따라 지원 티켓을 작성하여 제출하세요. Microsoft 팀에서 추가 지침과 함께 연락드리겠습니다. 

### <a name="fill-out-the-basics"></a>기본 사항 작성 
[새 지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 페이지에서 새 지원 티켓을 만듭니다. 화면의 지시에 따라 다음 매개 변수를 입력합니다.

![지원 기본 사항](./media/support-ticket-page-1-final.png)

1. **문제 유형** 을 `Technical`로 설정합니다.
2. 공간 분석 컨테이너를 배포하는 데 활용하는 구독을 선택합니다.
3. 을 선택 `My services` 하 고 `Cognitive Services` 서비스로 선택 합니다.
4. 공간 분석 컨테이너를 배포하는 데 활용하는 리소스를 선택합니다.
5. 직면하고 있는 문제를 자세히 설명하는 간단한 설명을 작성합니다. 
6. 문제 유형으로 `Spatial Analysis`를 선택합니다.
7. 드롭다운에서 적절한 하위 유형을 선택합니다.
8. **다음: 문제 해결** 을 선택하여 다음 페이지로 이동합니다.

### <a name="recommended-solutions"></a>권장 솔루션
다음 단계에서는 선택한 문제 유형에 권장되는 해결 방법을 제공합니다. 이러한 해결 방법은 가장 일반적인 문제를 해결하지만, 해결 방법으로 유용하지 않은 경우 **다음: 세부 정보** 를 선택하여 다음 단계로 이동합니다.

### <a name="details"></a>세부 정보
이 페이지에서 직면하고 있는 문제에 대한 몇 가지 추가 세부 정보를 추가합니다. 엔지니어가 문제를 더 효율적으로 좁히는 데 도움이 될 수 있으므로 최대한 많은 세부 정보를 포함해야 합니다. 적절하게 연락할 수 있도록 선호하는 연락 방법 및 문제 심각도를 포함하고, **다음: 검토 + 만들기** 를 선택하여 다음 단계로 이동합니다. 

### <a name="review-and-create"></a>검토 후 만들기 
지원 요청의 세부 정보를 검토하여 모든 항목이 정확하고 문제를 효과적으로 나타내는지 확인합니다. 준비가 되면 **만들기** 를 선택하여 티켓을 Microsoft 팀에 보냅니다! 티켓이 접수되면 이메일 확인 메시지를 받게 되며, Microsoft 팀에서 최대한 빨리 연락할 것입니다. Azure Portal에서 티켓 상태를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [인원 수를 세는 웹 애플리케이션 배포](spatial-analysis-web-app.md)
* [공간 분석 작업 구성](./spatial-analysis-operations.md)
* [카메라 배치 가이드](spatial-analysis-camera-placement.md)
* [영역 및 선 배치 가이드](spatial-analysis-zone-line-placement.md)
