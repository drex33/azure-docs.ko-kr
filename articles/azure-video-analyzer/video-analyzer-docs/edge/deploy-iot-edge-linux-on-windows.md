---
title: eflow를 사용 하 여 Windows 장치에 배포
description: 이 문서에서는 IoT Edge for Linux on Windows 디바이스에 배포하는 방법에 대한 지침을 제공합니다.
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3e2e045a72700b7e2c2e96cf63f344a84173b0f7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102846"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>IoT EFLOW(Edge for Linux on Windows) 디바이스에 배포

[!INCLUDE [header](includes/edge-env.md)]

이 문서에서는 [IoT EFLOW(Edge for Linux on Windows)](../../../iot-edge/iot-edge-for-linux-on-windows.md)가 있는 에지 디바이스에 Azure Video Analyzer를 배포하는 방법에 대해 알아봅니다. 이 문서의 단계를 완료 하면 비디오에서 동작을 검색 하는 [파이프라인](../pipeline.md) 을 실행 하 고 해당 이벤트를 IoT Hub으로 내보낼 수 있습니다. 그런 다음, 고급 시나리오에 대한 파이프라인을 전환하고 Azure Video Analyzer 기능을 Windows 기반 IoT Edge 디바이스로 가져올 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소 

* 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

* 개발 컴퓨터의 [Visual Studio Code](https://code.visualstudio.com/). [Azure IoT Tools 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)이 있는지 확인합니다.
* [EFLOW 정의](../../../iot-edge/iot-edge-for-linux-on-windows.md)를 읽습니다.

## <a name="deployment-steps"></a>배포 단계

다음은 문서의 전반적인 흐름을 보여주고 5개의 간단한 단계에서는 EFLOW가 있는 Windows 디바이스에서 Azure Video Analyzer를 실행하도록 모두 설정해야 합니다.

![IoT Edge for Linux on Windows(E FLOW) 다이어그램.](./media/deploy-iot-edge-linux-on-windows/eflow.png)

1. PowerShell을 사용하여 Windows 디바이스에 [EFLOW를 설치](../../../iot-edge/how-to-install-iot-edge-on-windows.md)합니다.

    > [!NOTE]
    > EFLOW를 배포하는 두 가지 방법(PowerShell 및 Windows Admin Center)과 가상 머신을 프로비전하는 두 가지 방법(연결 문자열을 사용한 수동 프로비전 및 X.509 인증서를 사용한 수동 프로비저닝)이 있습니다. [PowerShell 배포](../../../iot-edge/how-to-install-iot-edge-on-windows.md#create-a-new-deployment) 및 [IoT Hub에서 연결 문자열을 사용하여 머신 프로비전](../../../iot-edge/how-to-install-iot-edge-on-windows.md#manual-provisioning-using-the-connection-string)을 따르세요.

1. EFLOW가 설치되면 PowerShell에 `Connect-EflowVm` 명령을 입력하여(관리자 권한으로) 연결합니다. 그러면 EFLOW VM을 제어하기 위해 PowerShell 내에 bash 터미널이 표시되며, 여기서 Top 및 Nano와 같은 유틸리티를 포함한 Linux 명령을 실행할 수 있습니다. 

    > [!TIP] 
    > EFLOW VM을 종료하려면 터미널 내에 `exit`를 입력합니다.

1. PowerShell을 통해 EFLOW VM에 로그인 하 고 다음 명령을 입력 합니다.

    `bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`
    
    `sudo iptables -I INPUT -p udp -j ACCEPT`

    비디오 분석기에는 응용 프로그램 구성 데이터를 저장 하기 위한 특정 로컬 폴더가 필요 합니다. 이 방법 가이드에서는 분석을 위해 비디오 피드를 실시간으로 비디오 분석기 모듈에 릴레이 하는 [RTSP 시뮬레이터](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) 를 활용 하 고 있습니다. 이 시뮬레이터는 입력 디렉터리에서 입력으로 사용합니다. 

    위에서 사용된 준비 디바이스 스크립트는 이러한 작업을 자동화하므로 하나의 명령을 실행하고, 관련 입력과 구성 폴더, 비디오 입력 파일 및 권한을 가진 사용자 계정을 모두 원활하게 만들 수 있습니다. 명령이 성공적으로 완료되면 에지 디바이스에 생성된 다음 폴더가 표시됩니다. 

    * `/home/localedgeuser/samples`
    * `/home/localedgeuser/samples/input`
    * `/var/lib/videoanalyzer`
    * `/var/media`

    분석할 입력 파일 역할을 하는 /home/localedgeuser/samples/input 폴더에 있는 비디오 파일(*.mkv)을 확인합니다. 
    
1. Edge 장치를 설정 하 고, 허브에 등록 하 고, 생성 된 올바른 폴더 구조를 사용 하 여 성공적으로 실행 했으므로 다음 단계는 다음과 같은 추가 Azure 리소스를 설정 하 고 Video Analyzer 모듈을 배포 하는 것입니다. 다음 배포 템플릿은 리소스 만들기를 처리합니다.

    [![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
    
    배포 프로세스는 약 20분이 걸립니다. 배포 프로세스가 완료되면 Azure 구독에 다음을 비롯한 특정 Azure 리소스가 배포됩니다.

    * Video Analyzer 계정 - 이 클라우드 서비스는 Video Analyzer 에지 모듈을 등록하고 녹화된 비디오 및 비디오 분석을 재생하는 데 사용됩니다.
    * 스토리지 계정 - 녹화된 비디오 및 비디오 분석을 저장하는 데 사용됩니다.
    * 관리 ID - 위의 스토리지 계정에 대한 액세스를 관리하는 데 사용되는 사용자 할당 관리 ID입니다.
    * IoT Hub - IoT 애플리케이션, IoT Edge 및 관리하는 디바이스 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다.

    템플릿에서 에지 디바이스가 필요한지 묻는 메시지가 표시되면 이전에 디바이스와 IoT Hub를 모두 만들었으므로 "사용 및 기존 에지 디바이스" 옵션을 선택합니다. 또한 후속 단계에서 IoT Hub 이름 및 IoT Edge 디바이스 ID를 입력하라는 메시지가 표시됩니다.  
    
    ![기존 디바이스 사용](./media/deploy-iot-edge-linux-on-windows/use-existing-device.png) 

    완료되면 EFLOW VM에 다시 로그인하고 다음 명령을 실행할 수 있습니다.

    **`sudo iotedge list`**

    에지 디바이스에서 배포되고 실행되는 다음 4개의 모듈이 표시됩니다. 리소스 생성 스크립트는 IoT Edge 모듈(edgeAgent 및 edgeHub) 및 RTSP 시뮬레이션 모듈과 함께 AVA 모듈을 배포하여 시뮬레이션된 RTSP 비디오 피드를 제공합니다.
    
    ![배포된 모듈](./media/vscode-common-screenshots/avaedge-module.png)
    
1. 모듈을 배포하고 설정하면 EFLOW에서 첫 번째 AVA 파이프라인을 실행할 준비가 된 것입니다. 아래와 같이 간단한 동작 감지 파이프라인을 실행하고 다음 단계를 실행하여 결과를 시각화할 수 있습니다.

    ![동작 감지를 기반으로 하는 Video Analyzer](./media/get-started-detect-motion-emit-events/motion-detection.svg)

    1. Azure IoT Tools 확장을 [구성](get-started-detect-motion-emit-events.md#prepare-to-monitor-the-modules)합니다.
    1. pipelineTopology를 설정하고, livePipeline을 인스턴스화하고, 이러한 [직접 메서드 호출](get-started-detect-motion-emit-events.md#use-direct-method-calls)을 통해 활성화합니다.
    1. 허브에 대한 [결과를 관찰](get-started-detect-motion-emit-events.md#observe-results)합니다.
    1. [메서드 정리](get-started-detect-motion-emit-events.md#deactivate-the-live-pipeline)를 호출합니다.
    1. 필요하지 않은 경우 리소스를 삭제합니다.

        > [!IMPORTANT]
        > 삭제되지 않은 리소스는 여전히 활성 상태이며 Azure 비용이 발생합니다. 사용하지 않으려는 리소스를 삭제했는지 확인하세요.
   
## <a name="next-steps"></a>다음 단계

* 클라우드에서 관련 비디오를 기록하는 것과 함께 동작 감지를 시도합니다. [동작 감지 및 비디오 클립 기록](detect-motion-record-video-edge-devices.md) 빠른 시작의 단계를 따릅니다.
* [VS Code 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge)을 사용하여 추가 파이프라인을 봅니다.
* RTSP 시뮬레이터를 사용하는 대신 RTSP를 지원하는 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera)를 사용합니다. [ONVIF 규격 제품](https://www.onvif.org/conformant-products/) 페이지에서 RTSP를 지원하는 IP 카메라를 찾을 수 있습니다. G, S 또는 T 프로필을 준수하는 디바이스를 찾습니다.
* [Live Video에서 AI](analyze-live-video-use-your-model-http.md#overview)를 실행합니다(위에서 이미 수행된 필수 조건 설정을 건너뛸 수 있음).

    > [!WARNING] 
    > YOLO와 같은 메모리 집약적 AI 모델을 실행하려는 고급 사용자의 경우 EFLOW VM에 할당된 리소스를 늘려야 할 수 있습니다. 먼저 `exit`를 입력하여 EFLOW VM을 종료하고 Windows PowerShell 터미널로 돌아갑니다. 그런 다음, PowerShell에서 상승된 권한으로 `Set-EflowVM` 명령을 실행합니다. 명령을 실행한 후 PowerShell의 프롬프트에 따라 원하는 [매개 변수](../../../iot-edge/reference-iot-edge-for-linux-on-windows-functions.md#set-eflowvm)(예: `cpuCount: 2`, `memoryInMB: 2048`)를 입력합니다. 몇 분 후에 Edge 모듈을 다시 배포하고 라이브 파이프라인을 다시 활성화하여 추론을 확인합니다. 연결 문제가 발생하는 경우(예: IoT Hub에 오류 137 또는 255가 나열됨) 이 단계를 다시 실행해야 할 수 있습니다. 
