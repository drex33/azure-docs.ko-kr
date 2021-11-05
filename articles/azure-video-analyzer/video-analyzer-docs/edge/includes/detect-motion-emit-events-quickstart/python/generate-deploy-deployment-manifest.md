---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: faneerde
ms.openlocfilehash: 3fafa99807c9148cbae962fb4eba89814bc979bf
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861405"
---
배포 매니페스트는 에지 디바이스에 배포되는 모듈을 정의합니다. 또한 해당 모듈에 대한 구성 설정을 정의합니다.

다음 단계에 따라 템플릿 파일에서 매니페스트를 생성한 다음, 에지 디바이스에 배포합니다.

1. Visual Studio Code를 엽니다.
1. **AZURE IOT HUB** 창 옆에 있는 **추가 작업** 아이콘을 선택하여 IoT Hub 연결 문자열을 설정합니다. _src/cloud-to-device-console-app/appsettings.json_ 파일에서 문자열을 복사할 수 있습니다.

    ![IOT 연결 문자열 설정](../../../media/vscode-common-screenshots/set-connection-string.png)

    [!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]
1. **src/edge/deployment.template.json** 을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 배포 매니페스트 생성** 을 선택합니다.

    ![IoT Edge 배포 매니페스트 생성](../../../media/quickstarts/generate-iot-edge-deployment-manifest.png)

    이렇게 하면 _src/edge/config_ 폴더에 _deployment.amd64.json_ 이라는 매니페스트 파일이 생성됩니다.
1. **src/edge/config/deployment.amd64.json** 을 마우스 오른쪽 단추로 클릭하고 **단일 디바이스용 배포 만들기** 를 선택하고 에지 디바이스의 이름을 선택합니다.

    ![단일 디바이스에 대한 배포 만들기](../../../media/quickstarts/create-deployment-single-device.png)
1. IoT Hub 디바이스를 선택하라는 메시지가 표시되면 드롭다운 메뉴에서 **avasample-iot-edge-device** 를 선택합니다.
1. 약 30초 후 창의 왼쪽 아래 모서리에서 Azure IoT Hub를 새로 고칩니다. 이제 에지 디바이스에 다음과 같은 배포된 모듈이 표시됩니다.

    - Azure Video Analyzer(모듈 이름 `avaedge`)
    - RTSP(Real-Time Streaming Protocol) 시뮬레이터(`rtspsim` 모듈 이름)
