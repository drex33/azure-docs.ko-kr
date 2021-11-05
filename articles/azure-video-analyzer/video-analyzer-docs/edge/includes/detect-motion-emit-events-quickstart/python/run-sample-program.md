---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: faneerde
ms.custom: ignite-fall-2021
ms.openlocfilehash: d4964c543ae38234bdc119397154c1f2cd818957
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860749"
---
샘플 코드를 실행하려면 다음 단계를 수행합니다.

1. Visual Studio Code에서 **확장** 탭을 열고(또는 Ctrl+Shift+X를 누름) Azure IoT Hub를 검색합니다.
1. 마우스 오른쪽 단추를 클릭하고 **확장 설정** 을 선택합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="../../../media/vscode-common-screenshots/extension-settings.png" alt-text="확장 설정":::

1. "자세한 정보 메시지 표시"를 검색하고 활성화합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="../../../media/vscode-common-screenshots/verbose-message.png" alt-text="자세한 정보 메시지 표시":::

1. Visual Studio Code에서 _src/cloud-to-device-console-app/operations.json_ 으로 이동합니다.
1. `pipelineTopologySet` 노드에서 다음 값이 표시되는지 확인합니다.

   ```
   "pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/motion-detection/topology.json"
   ```

1. `livePipelineSet` 및 `livePipelineDelete` 노드에서 **topologyName** 값이 파이프라인 토폴로지의 **name** 속성 값과 일치하는지 확인합니다.

   `"topologyName" : "MotionDetection"`

1. * VS Code에서 `TERMINAL` 창으로 이동
   * cd 명령을 사용하여 /video-analyzer-iot-edge-python-main/src/cloud-to-device-console-app 디렉터리로 이동
   * “python main.py”를 실행하면 `TERMINAL` 창에 출력된 메시지가 표시됨
1. _operations.json_ 파일은 `pipelineTopologyList` 및 `livePipelineList`에 대한 호출로 시작됩니다. 이전 빠른 시작을 완료한 후에 리소스를 정리한 경우 이 프로세스에서 빈 목록이 반환됩니다.

   ```
   -----------------------  Request: pipelineTopologyList  --------------------------------------------------

   {
   "@apiVersion": "1.1"
   }

   ---------------  Response: pipelineTopologyList - Status: 200  ---------------

   {
     "value": []
   }

   --------------------------------------------------------------------------

   ```

   **터미널** 창에 직접 메서드 호출의 다음 세트가 표시됩니다.

   - 이전 pipelineTopologyUrl을 사용하는 `pipelineTopologySet`에 대한 호출
   - 다음 본문을 사용하는 `livePipelineSet`에 대한 호출

   ```json
   {
     "@apiVersion": "1.1",
     "name": "Sample-Pipeline-1",
     "properties": {
       "topologyName": "MotionDetection",
       "description": "Sample pipeline description",
       "parameters": [
         {
           "name": "rtspUrl",
           "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
         },
         {
           "name": "rtspUserName",
           "value": "testuser"
         },
         {
           "name": "rtspPassword",
           "value": "testpassword"
         }
       ]
     }
   }
   ```

   - 라이브 파이프라인 및 비디오 흐름을 시작하는 `livePipelineActivate`에 대한 호출입니다.
   - 라이브 파이프라인이 실행 중 상태임을 나타내는 `livePipelineList`에 대한 두 번째 호출입니다.

1. **터미널** 창의 출력이 `Press Enter to continue`에서 일시 중지됩니다. 아직 Enter 키를 선택하지 마세요. 위로 스크롤하여 호출한 직접 메서드에 대한 JSON 응답 페이로드를 확인합니다.
1. Visual Studio Code에서 **출력** 창으로 전환합니다. Azure Video Analyzer 모듈에서 IoT 허브에 보내는 메시지가 표시됩니다. 이 빠른 시작의 다음 섹션에서는 이러한 메시지를 설명합니다.
1. 라이브 파이프라인은 계속 실행되고 결과를 출력합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 라이브 파이프라인을 중지하려면 **TERMINAL** 창으로 돌아가서 Enter를 선택합니다.

   일련의 다음 호출은 리소스를 정리합니다.

   - `livePipelineDeactivate`를 호출하면 파이프라인이 비활성화됩니다.
   - `livePipelineDelete`를 호출하면 파이프라인이 삭제됩니다.
   - `pipelineTopologyDelete`에 대한 호출은 토폴로지를 삭제합니다.
   - `pipelineTopologyList`에 대한 최종 호출은 목록이 비어 있음을 보여 줍니다.
