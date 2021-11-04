---
author: russell-cooks
ms.topic: include
ms.service: azure-video-analyzer
ms.date: 05/03/2021
ms.author: juliako
ms.openlocfilehash: 341730ee0ed64809cec3f60a0e28cb6367fa863f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030806"
---
### <a name="get-the-sample-code"></a>샘플 코드 가져오기

1. [AVA C# 샘플 리포지토리](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)를 복제합니다.
1. Visual Studio Code를 시작하고 리포지토리가 다운로드된 폴더를 엽니다.
1. Visual Studio Code에서 src/cloud-to-device-console-app 폴더로 이동하고, **appsettings.json** 이라는 파일을 만듭니다. 이 파일에는 프로그램을 실행하는 데 필요한 설정이 포함되어 있습니다.
1. 위의 설정 단계에서 만든 스토리지 계정의 파일 공유를 찾아 "deployment-output" 파일 공유 아래에서 **appsettings.json** 파일을 찾습니다. 파일을 클릭한 다음, "다운로드" 단추를 누릅니다. 콘텐츠는 다음과 같은 새 브라우저 탭에서 열립니다.

   ```
   {
       "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",
       "deviceId" : "avasample-iot-edge-device",
       "moduleId" : "avaedge"
   }
   ```

   IoT Hub 연결 문자열을 사용하면 Visual Studio Code를 사용하여 Azure IoT Hub를 통해 명령을 에지 모듈에 보낼 수 있습니다. 위의 JSON을 **src/cloud-to-device-console-app/appsettings.json** 파일에 복사합니다.
1. 다음으로, src/edge 폴더로 이동하고, **.env** 라는 파일을 만듭니다. 이 파일에는 Visual Studio Code에서 모듈을 에지 디바이스에 배포하는 데 사용하는 속성이 포함되어 있습니다.
1. 위의 설정 단계에서 만든 스토리지 계정의 파일 공유를 찾아 "deployment-output" 파일 공유 아래에서 **env.txt** 파일을 찾습니다. 파일을 클릭한 다음, "다운로드" 단추를 누릅니다. 콘텐츠는 다음과 같은 새 브라우저 탭에서 열립니다.

   ```
        SUBSCRIPTION_ID="<Subscription ID>"
        RESOURCE_GROUP="<Resource Group>"
        AVA_PROVISIONING_TOKEN="<Provisioning token>"
        VIDEO_INPUT_FOLDER_ON_DEVICE="/home/localedgeuser/samples/input"
        VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
        APPDATA_FOLDER_ON_DEVICE="/var/lib/videoAnalyzer"
        CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"
        CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"
   ```

   **env.txt** 의 JSON을 **src/edge/.env** 파일로 복사합니다.

### <a name="connect-to-the-iot-hub"></a>IoT Hub에 연결

1. Visual Studio Code에서 왼쪽 아래 모서리의 **AZURE IOT HUB** 창 옆에 있는 **추가 작업** 아이콘을 선택하여 IoT Hub 연결 문자열을 설정합니다. src/cloud-to-device-console-app/appsettings.json 파일의 문자열을 복사합니다.

    <!-- commenting out the image for now ![Set IoT Hub connection string]()./media/quickstarts/set-iotconnection-string.png-->
    [!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]
1. 약 30초 후에 왼쪽 아래 섹션에서 Azure IoT Hub가 새로 고침됩니다. 에지 디바이스 `avasample-iot-edge-device`에 다음 모듈이 배포되었을 것입니다.
    - Edge Hub(모듈 이름 **edgeHub**)
    - Edge 에이전트(모듈 이름 **edgeAgent**)
    - Video Analyzer(모듈 이름 **avaedge**)
    - RTSP 시뮬레이터(모듈 이름 **rtspsim**)

### <a name="prepare-to-monitor-the-modules"></a>모듈 모니터링 준비

이 빠른 시작 또는 자습서 실행을 사용하면 이벤트가 IoT Hub로 전송됩니다. 이러한 이벤트를 보려면 다음 단계를 수행합니다.

1. Visual Studio Code에서 탐색기 창을 열고, 왼쪽 아래 모서리에서 **Azure IoT Hub** 를 찾습니다.
1. **디바이스** 노드를 펼칩니다.
1. `avasample-iot-edge-device`를 마우스 오른쪽 단추로 클릭하고, **기본 제공 이벤트 엔드포인트 모니터링 시작** 을 선택합니다.

    [!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]
