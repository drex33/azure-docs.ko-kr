---
title: Live Video Analytics를 사용하여 Windows의 Linux용 IoT Edge에 배포 - Azure
description: 이 문서에서는 Live Video Analytics를 사용하여 Windows의 Linux용 IoT Edge 디바이스에 배포하는 방법에 대한 지침을 제공합니다.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 315501540e38340285e113dace2b68a46c3f6493
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225721"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device-with-live-video-analytics"></a>Live Video Analytics를 사용하여 IoT EFLOW(Edge for Linux on Windows) 디바이스에 배포

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

이 문서에서는 [IoT EFLOW(Edge for Linux on Windows)](../../iot-edge/iot-edge-for-linux-on-windows.md)가 있는 에지 디바이스에 Live Video Analytics를 배포하는 방법에 대해 알아봅니다. 이 문서의 단계를 완료하면 비디오에서 동작을 감지하고 해당 이벤트를 클라우드의 IoT Hub로 내보내는 [미디어 그래프](media-graph-concept.md)를 실행할 수 있습니다. 그런 다음 고급 시나리오에 대한 미디어 그래프를 전환하고 Live Video Analytics 기능을 Windows 기반 IoT Edge 디바이스로 가져올 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소 

* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    > [!NOTE]
    > 서비스 주체를 만들 수 있는 권한이 있는 Azure 구독이 필요합니다(**소유자 역할** 이 이를 제공함). 적절한 권한이 없는 경우 계정 관리자에게 문의하여 적절한 권한을 부여합니다.
* 개발 컴퓨터의 [Visual Studio Code](https://code.visualstudio.com/). [Azure IoT Tools 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)이 있는지 확인합니다.
* [EFLOW 정의](../../iot-edge/iot-edge-for-linux-on-windows.md)를 읽습니다.

## <a name="deployment-steps"></a>배포 단계

다음은 문서의 전반적인 흐름을 보여 주고 5개의 간단한 단계에서는 EFLOW가 있는 Windows 디바이스에서 Live Video Analytics를 실행하도록 모두 설정해야 합니다.

:::image type="content" source="./media/deploy-iot-edge-linux-on-windows/eflow.png" alt-text="IoT EFLOW(Edge for Linux on Windows) 다이어그램":::

1. Windows 디바이스에 [EFLOW를 설치](../../iot-edge/how-to-provision-single-device-linux-on-windows-symmetric.md)합니다. 

    1. Windows PC를 사용하는 경우 [Windows Admin Center](/windows-server/manage/windows-admin-center/overview) 시작 페이지의 연결 목록 아래에 Windows Admin Center를 실행하는 PC를 나타내는 로컬 호스트 연결이 표시됩니다. 
    1. 관리하는 모든 추가 서버, PC 또는 클러스터도 여기에 표시됩니다.
    1. Windows Admin Center를 통해 로컬 디바이스나 원격 관리 디바이스에서 Azure EFLOW를 설치하고 관리할 수 있습니다. 이 안내선에서 로컬 호스트 연결은 Azure IoT Edge for Linux on Windows를 배포하기 위한 대상 디바이스로 역할을 했습니다. 따라서 localhost가 IoT Edge 디바이스로도 나열됩니다.

    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png" alt-text="배포 단계 - Windows Admin Center":::
1. IoT Edge 디바이스를 클릭하여 연결하면 개요 및 명령 셸 탭이 표시됩니다. 명령 셸 탭에서는 에지 디바이스에 대한 명령을 실행할 수 있습니다.
 
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png" alt-text="배포 단계 - Azure IoT Edge Manager":::
1. 명령 셸로 이동하고 다음 명령을 입력합니다.
    
    ```bash
    bash -c "$(curl -sL https://aka.ms/lva-edge/prep_device)"
    ```

    Live Video Analytics 모듈은 권한이 없는 [로컬 사용자 계정](deploy-iot-edge-device.md#create-and-use-local-user-account-for-deployment)을 사용하여 에지 디바이스에서 실행됩니다. 또한 애플리케이션 구성 데이터를 저장하기 위해 [특정 로컬 폴더가 필요](deploy-iot-edge-device.md#granting-permissions-to-device-storage)합니다. 마지막으로, 이 방법 가이드에서는 분석을 위해 실시간으로 비디오 피드를 LVA 모듈에 릴레이하는 [RTSP 시뮬레이터](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)를 활용하고 있습니다. 이 시뮬레이터는 입력 디렉터리에서 입력으로 사용합니다. 
    
    위에서 사용된 준비 디바이스 스크립트는 이러한 작업을 자동화하므로 하나의 명령을 실행하고, 관련 입력과 구성 폴더, 비디오 입력 파일 및 권한을 가진 사용자 계정을 모두 원활하게 만들 수 있습니다. 명령이 성공적으로 완료되면 에지 디바이스에 생성된 다음 폴더가 표시됩니다. 
    
    * `/home/lvaedgeuser/samples`
    * `/home/lvaedgeuser/samples/input`
    * `/var/lib/azuremediaservices`
    * `/var/media`
    
    분석할 입력 파일 역할을 하는 /home/lvaedgeuser/samples/input 폴더에 있는 비디오 파일(*.mkv)을 확인합니다. 
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager-analysis.png" alt-text="분석":::
1. 에지 디바이스를 설정하고, 허브에 등록하고, 생성된 올바른 폴더 구조를 사용하여 성공적으로 실행했으므로 다음 단계는 다음과 같은 추가 Azure 리소스를 설정하고 LVA 모듈을 배포하는 것입니다. 

    * 스토리지 계정
    * Azure Media Services 계정

    이를 위해 [Live Video Analytics 리소스 설치 스크립트](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)를 사용하여 Azure 구독에 필요한 리소스를 배포하는 것이 좋습니다. 이렇게 하려면 다음 단계를 따르십시오.

    1. [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/)을 엽니다.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-resources-cloud-shell.png" alt-text="Cloud shell":::
    1. Cloud Shell을 처음 사용하는 경우 스토리지 계정 및 Microsoft Azure Files 공유를 만들 구독을 선택하라는 메시지가 표시됩니다. **스토리지 만들기** 를 선택하여 Cloud Shell 세션 정보에 대한 스토리지 계정을 만듭니다. 이 스토리지 계정은 Azure Media Services 계정에서 사용하기 위해 스크립트에서 만드는 계정과는 다릅니다.
    1. Cloud Shell 창 왼쪽의 드롭다운 메뉴에서 사용자 환경으로 Bash를 선택합니다.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/bash.png" alt-text="Bash 환경":::
    1. 다음 명령을 실행합니다.

        ```bash
        bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
        ```
        
        이전에 디바이스와 IoT Hub를 모두 만들었기 때문에 자체 에지 디바이스를 IoT Edge 디바이스로 선택하라는 메시지가 표시되면 **Y** 를 선택해야 합니다. IoT Hub 이름과 IoT Edge 디바이스 ID를 입력하라는 메시지도 표시됩니다. Azure Portal에 로그인하고 IoT Hub를 클릭한 다음 IoT Hub 포털 블레이드의 IoT Edge 옵션으로 이동하여 둘 다 가져올 수 있습니다.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/iot-edge-devices.png" alt-text="IoT Edge 디바이스 참조":::

    완료되면 IoT Edge 디바이스 명령 셸에 다시 로그인하여 다음 명령을 실행할 수 있습니다.
    
    `sudo iotedge list`
    
    에지 디바이스에서 배포되고 실행되는 다음 4개의 모듈이 표시됩니다. 리소스 생성 스크립트는 IoT Edge 모듈(edgeAgent 및 edgeHub) 및 RTSP 시뮬레이션 모듈과 함께 LVA 모듈을 배포하여 시뮬레이션된 RTSP 비디오 피드를 제공합니다.
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/running.png" alt-text="상태 참조":::
1. 모듈을 배포하고 설정하면 EFLOW에서 첫 번째 LVA 미디어 그래프를 실행할 준비가 된 것입니다. 아래와 같이 간단한 동작 감지 그래프를 실행하고 다음 단계를 실행하여 결과를 시각화할 수 있습니다.

    :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="동작 감지 그래프":::

    1. Azure IoT Tools 확장을 [구성](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension)합니다.
    
        > [!Note]
        > `~/clouddrive/lva_byod/appsettings.json. - instead of ~/clouddrive/lva-sample/appsettings.json` 경로를 사용합니다.
    1. 토폴로지를 설정하고, 그래프를 인스턴스화하고, 이러한 [직접 메서드 호출](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls)을 통해 활성화합니다.
    1. 허브에 대한 [결과를 관찰](get-started-detect-motion-emit-events-quickstart.md#observe-results)합니다.
    1. [메서드 정리](get-started-detect-motion-emit-events-quickstart.md#invoke-graphinstancedeactivate)를 호출합니다.
    1. 필요하지 않은 경우 리소스를 삭제합니다.

        > [!IMPORTANT]
        > 삭제되지 않은 리소스는 여전히 활성 상태이며 Azure 비용이 발생합니다.
    
## <a name="next-steps"></a>다음 단계

* 클라우드에서 관련 비디오를 기록하는 것과 함께 동작 감지를 시도합니다. [동작 감지, Media Services에 비디오 클립 기록](detect-motion-record-video-clips-media-services-quickstart.md#review-the-sample-video) 빠른 시작의 단계를 따릅니다.
* [Live Video에서 AI](use-your-model-quickstart.md#overview) 실행(위에서 이미 수행된 필수 요소 설치를 건너뛸 수 있음)
* [VS Code 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge)을 사용하여 추가 미디어 그래프를 봅니다.
* RTSP 시뮬레이터를 사용하는 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격 제품](https://www.onvif.org/conformant-products/) 페이지에서 RTSP를 지원하는 IP 카메라를 찾을 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다.
