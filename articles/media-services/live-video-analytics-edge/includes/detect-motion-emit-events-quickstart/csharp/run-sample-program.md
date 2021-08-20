---
ms.openlocfilehash: 7b173a5fe639ff21cc8a475edd16d0e3885dacef
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113279858"
---
샘플 코드를 실행하려면 다음 단계를 수행합니다.

1. Visual Studio Code에서 **확장** 탭을 열고(또는 Ctrl+Shift+X를 누름) Azure IoT Hub를 검색합니다.
1. 마우스 오른쪽 단추를 클릭하고 **확장 설정** 을 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="확장 설정":::
1. "자세한 정보 메시지 표시"를 검색하고 활성화합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="자세한 정보 메시지 표시":::
1. Visual Studio Code에서 *src/cloud-to-device-console-app/operations.json* 으로 이동합니다.
1. **GraphTopologySet** 노드에서 다음 값이 표시되는지 확인합니다.

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/2.0/topology.json"`
1. **GraphInstanceSet** 및 **GraphTopologyDelete** 노드에서 `topologyName` 값이 그래프 토폴로지의 `name` 속성 값과 일치하는지 확인합니다.

    `"topologyName" : "MotionDetection"`
    
1. F5 키를 선택하여 디버깅 세션을 시작합니다. **터미널** 창에 몇 가지 메시지가 표시됩니다.

    F5를 사용하여 디버깅 세션을 시작하면 환경 유형 및 프로젝트를 묻는 메시지가 처음 표시됩니다. 이는 폴더에 launch.json 파일을 만들고 구성하는 것입니다. 이 데모의 목적을 위해 다음을 사용합니다.
    * 환경 - .Net Core
    * 프로젝트 - c2d-console-app
    
    launch.json 파일이 생성된 후 편집합니다. 콘솔 구성을 'integratedTeminal'로 변경합니다.
    
    `"console": "integratedTerminal"`
1. *operations.json* 파일은 `GraphTopologyList` 및 `GraphInstanceList`에 대한 호출로 시작됩니다. 이전 빠른 시작을 완료한 후에 리소스를 정리한 경우 이 프로세스에서 빈 목록을 반환한 다음, 일시 중지합니다. 계속하려면 Enter 키를 선택합니다.

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
        "@apiVersion": "2.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
        "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
    
    **터미널** 창에 직접 메서드 호출의 다음 세트가 표시됩니다.
     * 이전 `topologyUrl`을 사용하는 `GraphTopologySet`에 대한 호출
     * 다음 본문을 사용하는 `GraphInstanceSet`에 대한 호출
         
    ```
    {
      "@apiVersion": "2.0",
      "name": "Sample-Graph",
      "properties": {
        "topologyName": "MotionDetection",
        "description": "Sample graph description",
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
     
    * 그래프 인스턴스 및 비디오 흐름을 시작하는 `GraphInstanceActivate`에 대한 호출입니다.
    * 그래프 인스턴스가 실행 중 상태임을 보여주는 `GraphInstanceList`에 대한 두 번째 호출입니다.
1. **터미널** 창의 출력이 `Press Enter to continue`에서 일시 중지됩니다. 아직 Enter 키를 선택하지 마세요. 위로 스크롤하여 호출한 직접 메서드에 대한 JSON 응답 페이로드를 확인합니다.
1. Visual Studio Code에서 **출력** 창으로 전환합니다. Live Video Analytics on IoT Edge 모듈에서 IoT 허브에 보내는 메시지가 표시됩니다. 이 빠른 시작의 다음 섹션에서는 이러한 메시지를 설명합니다.
1. 미디어 그래프가 계속 실행되어 결과를 출력합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 미디어 그래프를 중지하려면 **터미널** 창으로 돌아가서 Enter 키를 선택합니다. 

    일련의 다음 호출은 리소스를 정리합니다.

    * `GraphInstanceDeactivate`에 대한 호출은 그래프 인스턴스를 비활성화합니다.
    * `GraphInstanceDelete`에 대한 호출은 인스턴스를 삭제합니다.
    * `GraphTopologyDelete`에 대한 호출은 토폴로지를 삭제합니다.
    * `GraphTopologyList`에 대한 최종 호출은 목록이 비어 있음을 보여 줍니다.
