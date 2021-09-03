---
title: Azure Video Analyzer 문제 해결 - Azure
description: 이 문서에서는 Azure Video Analyzer에 대한 문제 해결 단계를 다룹니다.
ms.topic: troubleshooting
ms.date: 07/15/2021
ms.openlocfilehash: c3b95936eabfcaefa12b9271b152d196790841c4
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362649"
---
# <a name="troubleshoot-azure-video-analyzer"></a>Azure Video Analyzer 문제 해결

이 문서에서는 AVA(Azure Video Analyzer)의 문제 해결 단계를 다룹니다.

## <a name="troubleshoot-deployment-issues"></a>배포 문제 해결

### <a name="diagnostics"></a>진단

Video Analyzer 배포의 일부로 IoT Hub 및 IoT Edge 디바이스와 같은 Azure 리소스를 설정합니다. 문제 진단의 첫 번째 단계는 항상 다음 지침에 따라 Edge 디바이스가 올바르게 설정되었는지 확인하는 것입니다.

1. [`check` 명령을 실행합니다](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [IoT Edge 버전을 확인합니다](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [IoT Edge 보안 관리자의 상태 및 해당 로그를 확인합니다](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [IoT Edge 허브를 통과하는 메시지를 확인합니다](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [컨테이너를 다시 시작합니다](../../iot-edge/troubleshoot.md#restart-containers).
1. [방화벽 및 포트 구성 규칙을 확인합니다](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="issues-when-using-arm-template"></a>ARM 템플릿 사용 시 발생하는 문제

ARM 템플릿(Azure에 배포 단추)을 사용할 때 문제가 발생하는 경우 이 [빠른 시작](get-started-detect-motion-emit-events-portal.md)의 단계를 따라 필요한 리소스를 수동으로 배포합니다. 

### <a name="pre-deployment-issues"></a>배포 전 문제

에지 인프라가 정상이면 배포 매니페스트 파일을 통해 문제를 찾을 수 있습니다. 다른 IoT 모듈과 함께 IoT Edge 디바이스에 Video Analyzer 모듈을 배포하려면 IoT Edge 허브, IoT Edge 에이전트 및 기타 모듈과 속성이 포함된 배포 매니페스트를 사용합니다. 매니페스트 파일은 다음 명령을 사용하여 배포할 수 있습니다.

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id avasample-iot-edge-device --content <path-to-deployment_manifest.json>
```

JSON 코드가 제대로 구성되지 않은 경우 다음 오류가 표시될 수 있습니다. &nbsp;&nbsp;&nbsp;**Failed to parse JSON from file: '<deployment manifest.json>' for argument 'content' with exception: "Extra data: line 101 column 1 (char 5325)"**

이 오류가 발생하면 JSON에 누락된 괄호가 있는지 또는 파일 구조와 관련된 기타 문제가 있는지 확인하는 것이 좋습니다. 파일 구조의 유효성을 검사하려면 [Notepad++ with JSON Viewer plug-in](https://riptutorial.com/notepadplusplus/example/18201/json-viewer)과 같은 클라이언트 또는 [JSON Formatter & Validator](https://jsonformatter.curiousconcept.com/)와 같은 온라인 도구를 사용하면 됩니다.

### <a name="during-deployment-diagnose-with-live-pipeline-direct-methods"></a>배포 중: 라이브 파이프라인 직접 메서드를 사용하여 진단

Video Analyzer 모듈이 IoT Edge 디바이스에 올바르게 배포된 후에는 [직접 메서드](direct-methods.md)를 호출하여 라이브 파이프라인을 만들고 실행할 수 있습니다.

> [!NOTE]
> 직접 메서드 호출은 **`avaedge`** 모듈에 대해서만 수행해야 합니다.

Azure Portal을 통해 직접 메서드를 사용하여 라이브 파이프라인에 대한 진단을 실행할 수 있습니다.

1. Azure Portal에서 IoT Edge 디바이스에 연결된 IoT 허브로 이동합니다.
1. **자동 디바이스 관리** 를 찾은 다음, **IoT Edge** 를 선택합니다.
1. 에지 디바이스 목록에서 진단할 디바이스를 선택합니다.

   ![Edge 디바이스 목록을 보여주는 Azure Portal 스크린샷](./media/troubleshoot/ava-sample-device.png)

1. 응답 코드가 _200-OK_ 인지 확인합니다. [IoT Edge 런타임에](../../iot-edge/iot-edge-runtime.md)에 대한 다른 응답 코드는 다음과 같습니다.

   - 400 - 배포 구성이 잘못되었거나 유효하지 않습니다.
   - 417 - 디바이스에 배포 구성 집합이 없습니다.
   - 412 - 배포 구성의 스키마 버전이 잘못되었습니다.
   - 406 - IoT Edge 디바이스가 오프라인 상태이거나 상태 보고서를 전송하지 않습니다.
   - 500 - IoT Edge 런타임에서 오류가 발생했습니다.

   > [!TIP]
   > 환경에서 Azure IoT Edge 모듈을 실행하는 데 문제가 있는 경우 **[Azure IoT Edge 표준 진단 단계](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** 를 문제 해결 및 진단 지침으로 사용하세요.

### <a name="post-deployment-direct-method-error-code"></a>배포 후: 직접 메서드 오류 코드

1. 상태 `501 code`가 표시되면 직접 메서드 이름이 정확한지 확인합니다. 메서드 이름과 요청 페이로드가 정확하면 성공 코드 = 200과 함께 결과가 표시되어야 합니다.
1. 요청 페이로드가 정확하지 않으면 상태 `400 code`와 오류 코드 및 메시지를 나타내는 응답 페이로드를 받게 되고, 이 정보는 직접 메서드 호출 관련 문제를 진단하는 데 도움이 됩니다.

   - reported 속성과 desired 속성을 확인하면 모듈 속성이 배포와 동기화되었는지 여부를 파악할 수 있습니다. 그렇지 않은 경우 IoT Edge 디바이스를 다시 시작할 수 있습니다.
   - [직접 메서드](direct-methods.md) 가이드를 사용하여 몇 가지 메서드, 특히 pipelineTopologyList와 같은 간단한 메서드를 호출합니다. 이 가이드에도 예상되는 요청 및 응답 페이로드와 오류 코드가 설명되어 있습니다. 간단한 직접 메서드가 성공하면 Video Analyzer IoT Edge 모듈이 기능적으로 정상임을 확신할 수 있습니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/troubleshoot/direct-method.png" alt-text="IoT Edge 모듈에 대한 직접 메서드 창 스크린샷" lightbox="./media/troubleshoot/direct-method.png":::

1. **배포에 지정됨** 및 **디바이스에서 보고됨** 열이 _예_ 를 나타내면 Video Analyzer 모듈에서 직접 메서드를 호출할 수 있습니다. 모듈을 선택하면 desired 및 reported 속성을 확인하고 직접 메서드를 호출할 수 있는 페이지로 이동됩니다. 고려할 사항은 다음과 같습니다.

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>배포 후: 실행 중 문제에 대한 로그 진단

IoT Edge 모듈의 컨테이너 로그에는 모듈 런타임 중에 문제를 디버그하는 데 도움이 되는 진단 정보가 포함되어야 합니다. [컨테이너 로그에서 문제가 있는지 확인](../../iot-edge/troubleshoot.md#check-container-logs-for-issues)하고 문제를 직접 진단할 수 있습니다.

앞의 모든 검사를 실행해도 여전히 문제가 발생하면 Azure 팀의 추가 분석을 위해 [`support bundle` 명령을 사용하여](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) IoT Edge 디바이스에서 로그를 수집합니다. 지원에 대해 [문의](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)하고 수집된 로그를 제출할 수 있습니다.

## <a name="common-error-resolutions"></a>일반적인 오류 해결

Video Analyzer는 IoT Edge 디바이스의 IoT Edge 모듈로 배포되며 IoT Edge 에이전트 및 허브 모듈과 긴밀하게 연동됩니다. Video Analyzer 배포와 관련하여 발생하는 일반적인 오류 중 일부는 기본 IoT 인프라 문제로 인해 발생합니다. 이러한 오류는 다음과 같습니다.

- [IoT Edge 에이전트가 약 1분 후 중지됩니다](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
- [IoT Edge 에이전트가 모듈의 이미지에 액세스할 수 없습니다(403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
- [IoT Edge 에이전트 모듈이 '빈 구성 파일'을 보고하고 모듈이 디바이스에서 시작되지 않습니다](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
- [IoT Edge 허브가 시작되지 않습니다](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
- [IoT Edge 보안 디먼이 유효하지 않은 호스트 이름으로 인해 실패합니다](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
- [Video Analyzer 또는 기타 사용자 지정 IoT Edge 모듈이 404 오류로 인해 에지 허브에 메시지를 보내지 못합니다](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
- [IoT Edge 모듈이 성공적으로 배포된 다음, 디바이스에서 사라집니다](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

  > [!TIP]
  > 환경에서 Azure IoT Edge 모듈을 실행하는 데 문제가 있는 경우 **[Azure IoT Edge 표준 진단 단계](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** 를 문제 해결 및 진단 지침으로 사용하세요.

도움이 필요할 수 있는 추가 문제가 있으면 **[로그를 수집하여 지원 티켓을 제출](#collect-logs-for-submitting-a-support-ticket)** 하세요. **[amshelp@microsoft.com](mailto:amshelp@microsoft.com)** 으로 이메일을 보내서 연락하셔도 됩니다.

### <a name="video-analyzer-working-with-external-modules"></a>Video Analyzer 외부 모듈 사용

Video Analyzer는 파이프라인 확장 프로세서를 통해 파이프라인을 확장하고 HTTP 또는 gRPC 프로토콜을 사용하여 다른 IoT Edge 모듈에서 데이터를 송수신할 수 있습니다. [구체적인 예](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/httpExtension)로, 이 라이브 파이프라인은 비디오 프레임을 외부 유추 모듈(예: Yolo v3)에 이미지로 보내고 HTTP 프로토콜을 사용하여 JSON 기반 분석 결과를 받을 수 있습니다. 이러한 토폴로지에서 이벤트의 대상은 대부분 IoT 허브입니다. 허브에 유추 이벤트가 표시되지 않는 경우 다음을 확인합니다.

- 라이브 파이프라인이 게시되는 허브와 검사 중인 허브가 동일한지 확인합니다. 여러 배포를 만드는 경우 여러 허브가 생성되어 잘못된 허브에서 이벤트를 잘못 확인할 수 있습니다.
- Azure Portal에서 외부 모듈이 배포되어 실행되고 있는지 확인합니다. 여기 예시 이미지에서 rtspsim, yolov3, tinyyolov3, logAnalyticsAgent는 avaedge 모듈 외부에서 실행되는 IoT Edge 모듈입니다.

  [ ![Azure IoT Hub에서 모듈의 실행 상태를 보여주는 스크린샷](./media/troubleshoot/iot-hub-azure.png) ](./media/troubleshoot/iot-hub-azure.png#lightbox)

- 이벤트를 올바른 URL 엔드포인트에 보내고 있는지 확인합니다. 외부 AI 컨테이너는 URL과 포트를 노출합니다. 이를 통해 POST 요청에서 데이터를 수신하고 반환합니다. 이 URL은 HTTP 확장 프로세서에 대한 `endpoint: url` 속성으로 지정됩니다. [토폴로지 URL](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json)에서 볼 수 있듯이 엔드포인트는 유추 URL 매개 변수로 설정됩니다. 매개 변수의 기본값 또는 전달된 값이 정확한지 확인합니다. 클라이언트 URL(cURL)을 사용하여 작동하는지 테스트할 수 있습니다.

  예를 들어, 다음은 IP 주소가 172.17.0.3인 로컬 컴퓨터에서 실행되는 Yolo v3 컨테이너입니다.

  ```
  curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
  ```

  반환된 결과:

  ```
  {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
  ```

  > [!TIP]
  > **[Docker inspect 명령을](https://docs.docker.com/engine/reference/commandline/inspect/)** 사용하여 컴퓨터의 IP 주소를 찾을 수 있습니다.

- 파이프라인 확장 프로세서를 사용하는 라이브 파이프라인을 하나 이상 실행하는 경우에는 `samplingOptions` 필드를 사용하여 비디오 피드의 초당 프레임(fps) 속도를 관리해야 합니다.

  - 에지 컴퓨터의 CPU 또는 메모리가 많이 사용되는 특정 상황에서는 특정 유추 이벤트가 손실될 수 있습니다. 이 문제를 해결하려면 `samplingOptions` 필드의 `maximumSamplesPerSecond` 속성에 낮은 값을 설정합니다. 파이프라인의 각 인스턴스에서 0.5("maximumSamplesPerSecond": "0.5")로 설정한 다음, 인스턴스를 다시 실행하여 허브에서 유추 이벤트를 확인할 수 있습니다.

### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>병렬의 여러 직접 메서드 - 시간 초과 실패

Video Analyzer는 여러 토폴로지와 여러 파이프라인을 설정하는 데 사용할 수 있는 직접 메서드 기반 프로그래밍 모델을 제공합니다. 토폴로지 및 파이프라인 설정의 일부로 IoT Edge 모듈에서 직접 메서드 호출을 여러 개 호출합니다. 이러한 메서드 호출(특히 파이프라인을 시작 및 중지하는) 여러 개를 병렬로 호출하면 다음과 같은 시간 초과 오류가 발생할 수 있습니다.

어셈블리 초기화 메서드 Microsoft.Media.VideoAnalyzer.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync에서 예외가 발생했습니다. Microsoft.Azure.Devices.Common.Exceptions.IotHubException: <br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

직접 메서드를 병렬로 호출하지 않는 것이 좋습니다. 순차적으로 호출하십시오. (즉, 이전 직접 메서드가 완료된 후에만 직접 메서드를 호출합니다.)

### <a name="collect-logs-for-submitting-a-support-ticket"></a>지원 티켓 제출용 로그 수집

셀프 가이드 문제 해결 단계로 문제가 해결되지 않으면 Azure Portal로 이동하여 [지원 티켓을 엽니다](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> 로그에는 IP 주소와 같은 PII(개인 식별 정보)가 포함될 수 있습니다. 로그의 모든 로컬 복사본은 검토를 완료하고 지원 티켓을 닫는 즉시 삭제됩니다.

티켓에 추가해야 하는 관련 로그를 수집하려면 아래 지침을 순서대로 따르고 지원 요청의 **세부 정보** 창에 로그 파일을 업로드합니다.

1. [자세한 로그를 수집하도록 Video Analyzer 모듈 구성](#configure-video-analyzer-module-to-collect-verbose-logs)
1. [디버그 로그 켜기](#video-analyzer-debug-logs)
1. 문제 재현
1. 포털의 **IoT Hub** 페이지에서 가상 머신에 연결

   1. _debugLogs_ 폴더의 모든 파일을 압축합니다.

      > [!NOTE]
      > 이러한 로그 파일은 자체 진단용이 아닙니다. Azure 엔지니어링 팀이 문제를 분석하기 위한 것입니다.

      - 다음 명령에서 **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** 를 앞서 **2단계** 에서 설정한 에지 디바이스에 있는 디버그 로그의 위치로 바꾸어야 합니다.

        ```
        sudo apt install zip unzip
        zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE
        ```

   1. _debugLogs.zip_ 파일을 지원 티켓에 첨부합니다.

1. [support bundle 명령](#use-the-support-bundle-command)을 실행하고 로그를 수집하여 지원 티켓에 첨부합니다.

### <a name="configure-video-analyzer-module-to-collect-verbose-logs"></a>자세한 로그를 수집하도록 Video Analyzer 모듈 구성

`logLevel` 및 `logCategories`를 다음과 같이 설정하여 자세한 로그를 수집하도록 Video Analyzer 모듈을 구성합니다.

```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

다음 중 한 가지 방법으로 수행할 수 있습니다.

- **Azure Portal** 에서 Video Analyzer 모듈의 모듈 ID 쌍 속성을 업데이트하여 [ ![모듈 ID 쌍 속성](media/troubleshoot/module-twin.png)](media/troubleshoot/module-twin.png#lightbox)
- 또는 **배포 매니페스트** 파일에서 Video Analyzer 모듈의 속성 노드에 해당 항목을 추가할 수 있습니다.

### <a name="use-the-support-bundle-command"></a>support-bundle 명령 사용

IoT Edge 디바이스에서 로그를 수집해야 하는 경우 가장 쉬운 방법은 `support-bundle` 명령을 사용하는 것입니다. 이 명령은 다음을 수집합니다.

- 모듈 로그
- IoT Edge 보안 관리자 및 컨테이너 엔진 로그
- IoT Edge 확인 JSON 출력
- 유용한 디버그 정보

1. `support-bundle` 명령을 _--since_ 플래그와 함께 실행하여 로그에 포함할 시간을 지정합니다. 예를 들어 2h는 지난 2시간 동안의 로그를 가져옵니다. 다른 기간에 대한 로그를 포함하도록 이 플래그의 값을 변경할 수 있습니다.

   ```
   sudo iotedge support-bundle --since 2h
   ```

   이 명령은 명령을 실행한 디렉터리에 _support_bundle.zip_ 라는 파일을 만듭니다.

1. _support_bundle.zip_ 파일을 지원 티켓에 첨부합니다.

### <a name="video-analyzer-debug-logs"></a>Video Analyzer 디버그 로그

디버그 로그를 생성하도록 VVideo Analyzer 모듈을 구성하려면 다음을 수행합니다.

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

1. **모듈 ID 쌍** 의 값을 _null_ 로 설정하여 로그 수집을 중지할 수 있습니다. **모듈 ID 쌍** 페이지로 돌아가서 다음 매개 변수를 다음과 같이 업데이트합니다.

   `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>로깅에 대한 모범 사례

[모니터링 및 로깅](monitor-log-edge.md)은 분류를 이해하고 Video Analyzer 문제를 디버깅하는 데 도움이 되는 로그를 생성하는 방법을 이해하는 데 도움이 됩니다.

gRPC 서버 구현은 언어에 따라 다르므로 서버 내부에 로깅을 추가하는 표준 방법은 없습니다.

예를 들어 .NET core를 사용하여 gRPC 서버를 빌드하는 경우 gRPC 서비스는 **Grpc** 범주에 로그를 추가 합니다. gRPC의 자세한 로그를 사용하려면 Logging의 LogLevel 하위 섹션에 다음 항목을 추가하여 appsettings.json 파일의 Debug 수준에 Grpc 접두사를 구성합니다.

```
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information",
      "Grpc": "Debug"
       }
  }
}
```

이 내용은 Startup.cs 파일에서 ConfigureLogging을 사용하여 구성할 수도 있습니다.

```
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {

           logging.AddFilter("Grpc", LogLevel.Debug);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });

```

[.NET의 gRPC에서 로깅 및 진단 ](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1)에는 gRPC 서버에서 일부 진단 로그를 수집하기 위한 몇 가지 지침이 제공됩니다.

### <a name="a-failed-grpc-connection"></a>gRPC 연결 실패

파이프라인이 활성 상태이고 카메라에서 스트리밍되는 경우 Video Analyzer에 의해 연결이 유지됩니다.

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>CPU 및 GPU 리소스의 부하 분산 및 모니터링(해당 리소스가 병목 상태가 되는 경우)

Video Analyzer는 하드웨어 리소스 모니터링을 수행하거나 제공하지 않습니다. 개발자는 하드웨어 제조업체의 모니터링 솔루션을 사용해야 합니다. 단, Kubernetes 컨테이너를 사용하면 [Kubernetes 대시보드](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)를 사용하여 디바이스를 모니터링할 수 있습니다.

.NET Core 문서의 gRPC는 [성능 모범 사례](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1) 및 [부하 분산](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1#load-balancing)에 대한 몇 가지 중요한 정보를 공유합니다.

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>유추 서버가 프레임을 받지 못하고 "알 수 없는" 프로토콜 오류가 발생하는 경우 문제 해결

문제에 대한 자세한 정보를 얻기 위해 수행할 수 있는 몇 가지 작업이 있습니다.

- Video Analyzer 모듈의 desired 속성에 "**mediaPipeline** 로그 범주를 포함하고 로그 수준이 `Information`로 설정되어 있는지 확인합니다.
- 네트워크 연결을 테스트하려면 에지 디바이스에서 다음 명령을 실행하면 됩니다.

  ```
  sudo docker exec avaedge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>”
  ```

  명령이 무질서한 짧은 텍스트 문자열을 출력하면 텔넷이 유추 서버에 대한 연결을 여는 데 성공하고 바이너리 gRPC 채널을 열 수 있었다는 의미입니다. 이것이 보이지 않으면 텔넷은 네트워크 오류를 보고합니다.

- 유추 서버에서 gRPC 라이브러리에 추가 로깅을 사용하도록 설정할 수 있습니다. 이렇게 하면 gRPC 채널 자체에 대한 추가 정보를 제공할 수 있습니다. 이 작업은 언어에 따라 다르며 [C#](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1)에 대한 지침은 있습니다.

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>첫 번째 버퍼에 대한 결과를 보내지 않고 gRPC의 버퍼에서 더 많은 이미지를 선택하는 경우

gRPC 데이터 전송 계약의 일환으로, Video Analyzer가 gRPC 유추 서버로 보내는 모든 메시지는 승인(Acknowledged)되어야 합니다. 이미지 프레임 수신을 승인하지 않으면 데이터 계약이 중단되고 원치 않는 상황이 발생할 수 있습니다.

Video Analyzer와 함께 gRPC 서버를 사용하려면 최상의 성능을 위해 공유 메모리를 사용할 수 있습니다. 이렇게 하려면 프로그래밍 언어/환경에서 노출하는 Linux 공유 메모리 기능을 사용해야 합니다.

1. Linux 공유 메모리 핸들을 엽니다.
1. 프레임이 수신되면 공유 메모리 내의 주소 오프셋에 액세스합니다.
1. 프레임 처리 완료를 승인합니다. 그래야 Video Analyzer에서 메모리를 회수할 수 있습니다.

   > [!NOTE]
   > Video Analyzer에 대한 프레임 수신 승인이 오랫동안 지연되면 공유 메모리가 가득 차서 데이터가 삭제될 수 있습니다.

1. 유추 서버에서 각 프레임을 원하는 데이터 구조(목록, 배열 등)에 저장합니다.
1. 그런 다음, 원하는 수의 이미지 프레임이 있을 때 처리 논리를 실행할 수 있습니다.
1. 준비가 되면 유추 결과를 Video Analyzer에 반환합니다.

## <a name="next-steps"></a>다음 단계

[자습서: 이벤트 기반 비디오를 클라우드에 녹화하고 클라우드에서 재생](record-event-based-live-video.md)
