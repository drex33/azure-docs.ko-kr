---
title: Azure Stack Edge에 Azure Video Analyzer 배포
description: 이 문서에서는 Azure Stack Edge에 Azure Video Analyzer를 배포 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 2834828eb666bd80ca35284884e7745d8dbff350
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178109"
---
# <a name="deploy-azure-video-analyzer-on-azure-stack-edge"></a>Azure Stack Edge에 Azure Video Analyzer 배포

이 문서에서는 Azure Stack Edge 장치에 Azure Video Analyzer를 배포 하기 위한 전체 지침을 제공 합니다. 장치를 설정 하 고 활성화 한 후에는 Video Analyzer를 배포할 준비가 된 것입니다. 

이 문서에서는 Azure IoT 허브를 사용 하 여 비디오 분석기를 배포 하지만 Azure Stack Edge 리소스는 Kubernetes API를 노출 하며,이 API를 사용 하 여 비디오 분석기와 인터페이스 할 수 있는 IoT Hub 인식 이외의 추가 솔루션을 배포할 수 있습니다. 

> [!TIP]
> 사용자 지정 배포에 Kubernetes API를 사용 하는 것은 고급 사례입니다. Edge 모듈을 만들고 Kubernetes API를 사용 하는 대신 각 Azure Stack에 지 리소스에 IoT Hub를 통해 배포 하는 것이 좋습니다. 이 문서에서는 IoT Hub를 사용 하 여 Video Analyzer 모듈을 배포 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* Azure 비디오 분석기 계정

    이 [클라우드 서비스](./overview.md) 는 비디오 분석기 edge 모듈을 등록 하 고 녹화 된 비디오 및 비디오 분석을 재생 하는 데 사용 됩니다.

* 관리 id

    저장소 계정에 대 한 액세스를 관리 하는 데 사용 하는 사용자 할당 [관리 id](../../active-directory/managed-identities-azure-resources/overview.md) 입니다.

* [Azure Stack Edge 리소스](../../databox-online/azure-stack-edge-gpu-deploy-prep.md)

* [IoT hub](../../iot-hub/iot-hub-create-through-portal.md)

* 스토리지 계정

    범용 [v2 저장소 계정을](../../storage/common/storage-account-upgrade.md?tabs=azure-portal)사용 하는 것이 좋습니다.  
    
* 개발 컴퓨터에 설치 된 [Visual Studio Code](https://code.visualstudio.com/)

*  Visual Studio Code에 설치 된 [Azure IoT Tools 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

* 개발 컴퓨터가 연결된 네트워크에서 5671 포트를 통해 고급 메시지 큐 프로토콜을 허용하는지 확인합니다. 이렇게 설정 하면 Azure IoT Tools Azure IoT 허브와 통신할 수 있습니다.

## <a name="configure-azure-stack-edge-to-use-video-analyzer"></a>비디오 분석기를 사용 하도록 Azure Stack Edge 구성

Azure Stack Edge는 네트워크 데이터 전송 기능을 사용 하는 서비스 제공 서비스 솔루션 및 AI 지원에 지 컴퓨팅 장치입니다. 자세한 내용은 [Azure Stack Edge 및 자세한 설치 지침](../../databox-online/azure-stack-edge-gpu-deploy-prep.md)을 참조 하세요. 

시작하려면 다음을 수행합니다.

1. [Azure Stack Edge 또는 Azure Data Box Gateway 리소스를 만듭니다](../../databox-online/azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).  
1. [GPU를 사용 하 여 Azure Stack Edge Pro를 설치 하 고 설정](../../databox-online/azure-stack-edge-gpu-deploy-install.md)합니다.  
1. 다음을 수행 하 여 리소스를 커넥트 하 고 활성화 합니다.

    a. [로컬 웹 UI 설정에 커넥트](../../databox-online/azure-stack-edge-gpu-deploy-connect.md)합니다.  
    b. [네트워크를 구성](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)합니다.  
    c. [장치를 구성](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)합니다.  
    d. [인증서를 구성](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)합니다.  
    e. [디바이스 활성화](../../databox-online/azure-stack-edge-gpu-deploy-activate.md).  

1. [IoT hub를 Azure Stack Edge에 연결](../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute)합니다.

### <a name="meet-the-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Azure Stack Edge 로컬 UI에서 계산 전제 조건을 충족 합니다.

계속 하기 전에 다음을 완료 했는지 확인 합니다.

* Azure Stack Edge 리소스를 활성화 했습니다.
* Azure Stack에 지 리소스에 액세스 하기 위해 PowerShell 5.0 이상을 실행 하는 Windows 클라이언트 시스템에 액세스할 수 있습니다.
* Kubernetes 클러스터를 배포 하려면 [로컬 웹 UI](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup)에 Azure Stack Edge 리소스를 구성 했습니다. 

    1. 다음을 수행 하 여 리소스를 커넥트 하 고 구성 합니다.
    
        a. [로컬 웹 UI 설정에 커넥트](../../databox-online/azure-stack-edge-gpu-deploy-connect.md)합니다.  
        b. [네트워크를 구성](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)합니다.  
        c. [디바이스 구성](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)  
        d. [인증서를 구성](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)합니다.  
        e. [디바이스 활성화](../../databox-online/azure-stack-edge-gpu-deploy-activate.md).

    1. 계산을 사용 하도록 설정 하려면 장치의 로컬 웹 UI에서 **계산** 페이지로 이동 합니다.
    
        a. 계산을 사용 하도록 설정 하려는 네트워크 인터페이스를 선택한 다음 **사용** 을 선택 합니다. Compute를 사용 하도록 설정 하면 해당 네트워크 인터페이스에서 장치에 가상 스위치가 생성 됩니다.  
        b. Kubernetes 테스트 노드 IP와 Kubernetes 외부 서비스 IP를 비워 둡니다.  
        다. **적용** 을 선택합니다. 작업에 약 2 분이 소요 됩니다.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="../../databox-online/media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png" alt-text="Azure Stack Edge 로컬 UI의 계산 전제 조건 스크린샷":::

        Azure DNS Kubernetes API 및 Azure Stack에 지 리소스에 대해 구성 되지 않은 경우 다음을 수행 하 여 Windows 호스트 파일을 업데이트할 수 있습니다.
        
        a. 관리자 권한으로 텍스트 편집기를 엽니다.  
        b. *C:\ Windows \System32\drivers\etc \\* 에서 *hosts* 파일을 엽니다.  
        c. Kubernetes API 장치 이름의 IPv4 (인터넷 프로토콜 버전 4) 및 호스트 이름을 파일에 추가 합니다. 이 정보는 Azure Stack Edge 포털의 **장치** 아래에서 찾을 수 있습니다.  
        d. 파일을 저장하고 닫습니다.

### <a name="deploy-video-analyzer-edge-modules-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 비디오 분석기에 지 모듈 배포

Azure Portal 배포 매니페스트를 만들고 배포를 IoT Edge 장치로 푸시할 수 있습니다.  

#### <a name="select-your-device-and-set-modules"></a>디바이스를 선택하고 모듈 설정

1. [Azure Portal](https://ms.portal.azure.com/)에 로그인 하 고 IoT hub로 이동 합니다.
1. 왼쪽 창에서 **IoT Edge** 를 선택합니다.
1. 디바이스 목록에서 대상 디바이스의 ID를 선택합니다.
1. **모듈 설정** 을 선택합니다.

#### <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간 데이터 흐름 방법 및 모듈 쌍의 desired 속성을 설명 하는 JSON 문서입니다. Azure Portal에는 배포 매니페스트를 만들 수 있도록 안내하는 마법사가 있습니다. 세 단계는 **모듈**, **경로** 및 **검토 + 만들기** 탭으로 구성 됩니다.

#### <a name="add-modules"></a>모듈 추가

1. 모듈 **IoT Edge** 섹션의 **추가** 드롭다운 목록에서 **IoT Edge 모듈** 을 선택 하 여 **IoT Edge 모듈 추가** 페이지를 표시 합니다.
1. **모듈 설정** 탭을 선택 하 고 모듈 이름을 제공한 다음 컨테이너 이미지 URI를 지정 합니다. 다음 그림에서는 예제 값을 보여 줍니다.     
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-module.png" alt-text="IoT Edge 모듈 추가 페이지에서 모듈 설정 창의 스크린샷":::
    
    > [!TIP]
    > 이 절차에 설명 된 대로 **모듈 설정**, **컨테이너 만들기 옵션** 및 **모듈 쌍 설정** 탭에서 값을 지정할 때까지 **추가** 를 선택 하지 마십시오.
    
    > [!IMPORTANT]
    > Azure IoT Edge 값은 모듈에 대 한 호출을 수행할 때 대/소문자를 구분 합니다. 모듈 이름으로 사용 하는 정확한 문자열을 기록해 둡니다.

1. **환경 변수** 탭을 선택 하 고 다음 이미지와 같이 값을 입력 합니다.
   
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/environment-variables.png" alt-text="' IoT Edge 모듈 추가 ' 페이지의 ' 환경 변수 ' 창 스크린샷":::

1. **컨테이너 만들기 옵션** 탭을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/container-create-options.png" alt-text="IoT Edge 모듈 추가 페이지의 컨테이너 만들기 옵션 창의 스크린샷":::
 
    **컨테이너 만들기 옵션** 창의 상자에 다음 JSON 코드를 붙여넣습니다. 이 동작은 모듈에서 생성 되는 로그 파일의 크기를 제한 합니다.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/videoanalyzer/:/var/lib/videoanalyzer",
               "/var/media:/var/media"
            ],
            "IpcMode": "host",
            "ShmSize": 1536870912
        }
    }
    ````
   
   JSON의 "바인드" 섹션에는 두 가지 항목이 있습니다.
   * **"/var/lib/videoanalyzer:/var/cer/ps"** 는 컨테이너에서 영구 응용 프로그램 구성 데이터를 바인딩하고에 지 장치에 저장 하는 데 사용 됩니다.
   * **"/var/sermedia:/var/cermedia:** "는 미디어 폴더를에 지 장치와 컨테이너에 바인딩합니다. Edge 장치에 비디오 클립을 저장 하는 기능을 지 원하는 pipelineTopology를 실행할 때 비디오 기록을 저장 하는 데 사용 됩니다.
   
1. 모듈 쌍 **설정** 탭을 선택 합니다.
 
   Azure Video Analyzer를 실행 하려면 [모듈 쌍 구성 스키마](module-twin-configuration-schema.md)에 나열 된 필수 쌍 속성 집합이 필요 합니다.  

1. **모듈 쌍 설정** 창에 있는 상자에 다음 JSON 코드를 붙여넣습니다.    

    ```
    {
        "applicationDataDirectory": "/var/lib/videoanalyzer",
        "ProvisioningToken": "{provisioning-token}",
    }
    ```
   
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/twin-settings.png" alt-text="' IoT Edge 모듈 추가 ' 페이지의 ' 모듈 쌍 설정 ' 창 스크린샷":::   

    모듈을 모니터링 하는 데 도움이 되도록 다음과 같은 *권장* 속성을 JSON 코드에 추가할 수 있습니다. 자세한 내용은 [모니터링 및 로깅](monitor-log-edge.md)을 참조 하세요.
    
    ```
    "diagnosticsEventsOutputName": "diagnostics",
    "OperationalEventsOutputName": "operational",
    "logLevel": "Information",
    "logCategories": "Application,Events",
    "allowUnsecuredEndpoints": true,
    "telemetryOptOut": false
    ```
1. **추가** 를 선택합니다.  

#### <a name="add-the-real-time-streaming-protocol-rtsp-simulator-edge-module"></a>RTSP (Real-Time 스트리밍 프로토콜) 시뮬레이터 edge 모듈 추가

1. 모듈 **IoT Edge** 섹션의 **추가** 드롭다운 목록에서 **IoT Edge 모듈** 을 선택 하 여 **IoT Edge 모듈 추가** 페이지를 표시 합니다.
1. **모듈 설정** 탭을 선택 하 고 모듈 이름을 제공한 다음 컨테이너 이미지 URI를 지정 합니다. 예를 들어:   
    
    * **IoT Edge 모듈 이름**: rtspsim  
    * **이미지 URI**: mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2 

1. **컨테이너 만들기 옵션** 탭을 선택 하 고 상자에 다음 JSON 코드를 붙여넣습니다.
    
    ```
    {
        "HostConfig": {
            "Binds": [
               "/home/localedgeuser/samples/input/:/live/mediaServer/media/"
            ],
            "PortBindings": {
                    "554/tcp": [
                        {
                        "HostPort": "554"
                        }
                    ]
            }
        }
    }
    ```
1. **추가** 를 선택합니다.  

1. **다음: 경로** 를 선택하여 경로 섹션으로 진행합니다. 

1. 경로를 지정 하려면 **이름** 에 **AVAToHub** 를 입력 하 고 **값** 아래에 **/messages/modules/avaedge/outputs/에서 $upstream** 를 입력 합니다.

1. **다음: 검토 + 만들기** 를 선택 하 여 검토 섹션으로 이동 합니다.

1. 배포 정보를 검토 한 다음 **만들기** 를 선택 하 여 모듈을 배포 합니다.

#### <a name="generate-the-provisioning-token"></a>프로 비전 토큰 생성

1. Azure Portal에서 Video Analyzer로 이동 합니다.
1. 왼쪽 창에서에 **지 모듈** 을 선택 합니다.
1. Edge 모듈을 선택한 다음 **토큰 생성** 을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/generate-provisioning-token.png" alt-text="토큰을 생성 하기 위한 ' edge 모듈 추가 ' 창의 스크린샷" lightbox="./media/deploy-on-stack-edge/generate-provisioning-token.png":::

1. 다음 그림에 표시 된 것 처럼 프로 비전 토큰을 복사 합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/copy-provisioning-token.png" alt-text="' 프로 비전 토큰 복사 ' 페이지의 스크린샷":::



#### <a name="optional-set-up-docker-volume-mounts"></a>필드 Docker 볼륨 탑재 설정

작업 디렉터리의 데이터를 확인 하려면 배포 전에 Docker 볼륨 탑재를 설정 합니다. 

이 섹션에서는 게이트웨이 사용자를 만들고 파일 공유를 설정 하 여 Video Analyzer 작업 디렉터리 및 Video Analyzer 미디어 폴더의 콘텐츠를 확인 하는 방법을 설명 합니다.

> [!NOTE]
> Bind 탑재는 지원 되지만 볼륨 탑재를 통해 데이터를 볼 수 있으며, 선택 하는 경우 원격으로 복사할 수 있습니다. 바인딩 및 볼륨 탑재를 모두 사용할 수 있지만 동일한 컨테이너 경로를 가리킬 수 없습니다.

1. Azure Portal에서 Azure Stack Edge 리소스로 이동 합니다.
1. 다음을 수행 하 여 공유에 액세스할 수 있는 게이트웨이 사용자를 만듭니다.
    
    a. 왼쪽 창에서 **클라우드 저장소 게이트웨이** 를 선택 합니다.  
    b. 왼쪽 창에서 **사용자** 를 선택 합니다.  
    c. **사용자 추가** 를 선택 하 여 사용자 이름 (예: *avauser* 권장) 및 암호를 설정 합니다.  
    d. **추가** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-user.png" alt-text="Azure Stack Edge 리소스 ' 사용자 추가 ' 페이지의 스크린샷":::

1. 다음을 수행 하 여 비디오 분석기 지 속성에 대 한 *로컬 공유* 를 만듭니다.

    a. **클라우드 저장소 게이트웨이**  >  **공유** 를 선택 합니다.  
    b. **공유 추가** 를 선택 합니다.  
    c. 공유 이름을 설정 합니다. 예를 들어 *ava* 를 권장 합니다.  
    d. 공유 유형을 **SMB** 로 유지 합니다.  
    e. **Edge 계산에서 공유 사용** 확인란을 선택 했는지 확인 합니다.  
    f. **Edge 로컬 공유로 구성** 확인란을 선택 했는지 확인 합니다.  
    g. **사용자 세부 정보** 에서 **기존 항목 사용** 을 선택 하 여 최근에 만든 사용자에 게 공유에 대 한 액세스 권한을 부여 합니다.  
    h. **만들기** 를 선택합니다.
            
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/local-share.png" alt-text="로컬 공유를 만들기 위한 ' 공유 추가 ' 페이지의 스크린샷":::  
    
    > [!TIP]
    > Windows 클라이언트를 Azure Stack에 지 장치에 연결한 상태에서 [Azure Stack Edge Pro FPGA를 사용 하 여 데이터 전송](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share)의 "SMB 공유에 대 한 커넥트" 섹션의 지침을 따르세요.    

1. 다음을 수행 하 여 파일 동기화 저장소에 대 한 *원격 공유* 를 만듭니다.

    a. **클라우드 저장소 게이트웨이**  >  **Storage 계정** 을 선택 하 여 동일한 지역에 Azure Blob Storage 계정을 만듭니다.  
    b. **클라우드 저장소 게이트웨이**  >  **공유** 를 선택 합니다.  
    c. **공유 추가** 를 선택 합니다.  
    d. **이름** 상자에 공유 이름 (예: *미디어* 권장)을 입력 합니다.  
    e. **유형의** 경우 공유 유형을 **SMB** 로 유지 합니다.  
    f. **Edge 계산에서 공유 사용** 확인란을 선택 했는지 확인 합니다.  
    g. **Edge 로컬 공유로 구성** 확인란의 선택이 취소 되어 있는지 확인 합니다.  
    h. **Storage 계정** 드롭다운 목록에서 최근에 만든 저장소 계정을 선택 합니다.  
    i. **Storage 서비스** 드롭다운 목록에서 **블록 Blob** 을 선택 합니다.  
    j. **Blob 컨테이너 선택** 상자에 컨테이너 이름을 입력 합니다.  
    k. **사용자 세부 정보** 에서 **기존 항목 사용** 을 선택 하 여 최근에 만든 사용자에 게 공유에 대 한 액세스 권한을 부여 합니다.  
    l. **만들기** 를 선택합니다.    
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/remote-share.png" alt-text="원격 공유를 만들기 위한 ' 공유 추가 ' 페이지의 스크린샷":::

1. 볼륨 탑재를 사용 하려면 다음을 수행 하 여 컨테이너의 RTSP 시뮬레이터 모듈 **만들기 옵션** 창에서 설정을 업데이트 합니다.

    a. **모듈 설정** 단추를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/set-modules.png" alt-text="Edge 장치 설정 창의 ' 모듈 설정 ' 단추를 보여 주는 스크린샷" lightbox="./media/deploy-on-stack-edge/set-modules.png":::  

    b. **이름** 목록에서 **rtspsim** 모듈을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/select-module.png" alt-text="Edge 장치 설정 창의 ' IoT Edge 모듈 '에 있는 ' rtspsim ' 모듈의 스크린샷":::  
    
    c. **업데이트 IoT Edge 모듈** 창에서 **컨테이너 만들기 옵션** 탭을 선택한 후 다음 JSON 코드와 같이 탑재를 추가 합니다.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/update-module.png" alt-text="' 컨테이너 만들기 옵션 ' 창에서 JSON 탑재 코드의 스크린샷":::

    ```json
        "createOptions": 
        {
            "HostConfig": 
            {
                "Mounts": 
                [
                    {
                        "Target": "/live/mediaServer/media",
                        "Source": "media",
                        "Type": "volume"
                    }
                ],
                "PortBindings": {
                    "554/tcp": [
                        {
                        "HostPort": "554"
                        }
                    ]
                }
            }
        }
    ```  
    d. **업데이트** 를 선택합니다.  
    e. 모듈을 업데이트 하려면 **검토 및 만들기** 를 선택 하 고 **만들기** 를 선택 합니다.
    
### <a name="verify-that-the-module-is-running"></a>모듈이 실행 중인지 확인합니다.

마지막으로 IoT Edge 장치 모듈이 정상적으로 연결 되어 실행 되 고 있는지 확인 합니다. 모듈의 런타임 상태를 확인 하려면 다음을 수행 합니다.

1. Azure Portal에서 Azure Stack에 지 리소스로 돌아갑니다.
1. 왼쪽 창에서 **모듈** 을 선택 합니다. 
1. **모듈** 창의 **이름** 목록에서 배포 된 모듈을 선택 합니다. **런타임 상태** 열에서 모듈의 상태가 *실행 중* 이어야 합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/running-module.png" alt-text="선택한 모듈의 런타임 상태를 ' 실행 중 '으로 표시 하는 ' 모듈 ' 창의 스크린샷" lightbox="./media/deploy-on-stack-edge/running-module.png":::

### <a name="configure-the-azure-iot-tools-extension"></a>Azure IoT Tools 확장 구성

Azure IoT Tools 확장을 사용 하 여 IoT hub에 연결 하려면 다음을 수행 합니다.

1. Visual Studio Code에서 **보기** > **탐색기** 를 차례로 선택합니다.
1. **탐색기** 창에서 왼쪽 아래에 있는 **Azure IoT 허브** 를 선택 합니다.
1. **기타 옵션** 아이콘을 선택 하 여 상황에 맞는 메뉴를 표시 한 다음 **연결 문자열 IoT Hub 설정** 을 선택 합니다.

   IoT hub 연결 문자열을 입력 하 라는 입력 상자가 표시 됩니다. 연결 문자열을 가져오려면 다음을 수행 합니다. 

   a. Azure Portal에서 IoT Hub로 이동합니다.  
   b. 왼쪽 창에서 **공유 액세스 정책** 을 선택 합니다.  
   c. **Iothubowner을 선택 하 여 공유 액세스 키를 가져옵니다**.  
   d. 연결 문자열 기본 키를 복사한 다음 입력 상자에 붙여넣습니다.

   > [!NOTE]
   > 연결 문자열은 다음 형식으로 작성 됩니다.
   >
   > `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   연결에 성공 하면 Azure Stack Edge 장치를 포함 하 여에 지 장치 목록이 표시 됩니다. 이제 IoT Edge 장치를 관리 하 고 상황에 맞는 메뉴를 통해 Azure IoT 허브와 상호 작용할 수 있습니다. 
   
   에 지 장치에 배포 된 모듈을 보려면 Azure Stack 장치에서 **모듈** 노드를 확장 합니다.
    
## <a name="troubleshooting"></a>문제 해결

* **Kubectl (Kubernetes API access)**

    * [Azure Stack Edge Pro GPU 장치에서 Kubernetes 클러스터 만들기 및 관리](../../databox-online/azure-stack-edge-gpu-create-kubernetes-cluster.md)의 지침에 따라 Kubernetes 클러스터에 대 한 액세스를 위해 컴퓨터를 구성 합니다.
    * 배포 된 모든 IoT Edge 모듈은 *iotedge* 네임 스페이스를 사용 합니다. Kubectl를 사용 하는 경우 해당 이름을 포함 해야 합니다. 

* **모듈 로그**

    로그를 가져오기 위해 *iotedge* 도구에 액세스할 수 없는 경우 [kubectl 로그](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) 를 사용 하 여 파일에 대 한 로그 또는 파이프를 확인 합니다. 예를 들어: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`  

* **Pod 및 노드 메트릭**

    Pod 및 노드 메트릭을 보려면 [kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)을 사용 합니다. 예:   <br/>`kubectl top pods -n iotedge` 

* **모듈 네트워킹**   

    Azure Stack Edge에서 모듈 검색의 경우 모듈에 createOptions의 호스트 포트 바인딩이 있어야 합니다. 그러면 모듈이 `moduleName:hostport`에 대해 주소를 지정할 수 있습니다.
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* **볼륨 탑재**

    컨테이너가 기존 디렉터리와 비어 있지 않은 디렉터리에 볼륨 탑재를 시도하면 모듈이 시작되지 않습니다.

* **GRPC를 사용 하는 경우 공유 메모리**

    호스트 IPC를 사용 하는 경우 Azure Stack Edge 리소스의 공유 메모리가 모든 네임 스페이스의 pod에서 지원 됩니다.
    
    다음 코드를 사용 하 여 IoT Hub를 통해 배포할에 지 모듈에서 공유 메모리를 구성 합니다.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    //(Advanced) Configuring shared memory on a Kubernetes pod or deployment manifest for deployment via the Kubernetes API spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
* **고급 Pod 공동 배치**

    Kubernetes를 사용 하 여 gRPC를 통해 비디오 분석기와 통신 하는 사용자 지정 유추 솔루션을 배포 하는 경우 pod가 Video Analyzer 모듈과 동일한 노드에 배포 되었는지 확인 합니다.

    * **옵션 1**: 공동 배치를 위해 *노드 선호도* 및 기본 제공 노드 레이블을 사용 합니다.  

    사용자가 노드에 레이블을 설정할 수 있는 권한이 없기 때문에 현재 NodeSelector 사용자 지정 구성은 옵션으로 표시 되지 않습니다. 그러나 사용자의 토폴로지와 명명 규칙에 따라 [기본 제공 노드 레이블을](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels)사용할 수 있습니다. 공동 배치를 위해 Video Analyzer를 사용 하 여 Azure Stack에 지 리소스를 참조 하는 nodeAffinity 섹션을 유추 pod 매니페스트에 추가할 수 있습니다.
    
    * **옵션 2**: (권장) 공동 배치에 *Pod 선호도* 를 사용 합니다.  

        Kubernetes는 동일한 노드에서 pod을 예약할 수 있는 [pod 선호도](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)를 지원 합니다. 공동 배치를 위해 Video Analyzer 모듈을 참조 하는 podAffinity 섹션인 유추 pod 매니페스트에 추가할 수 있습니다.

         ```json   
        // Example Video Analyzer module deployment match labels
        selector:
          matchLabels:
            net.azure-devices.edge.deviceid: dev-ase-1-edge
            net.azure-devices.edge.module: mediaedge
        
        // Example inference deployment manifest pod affinity
        spec:
          affinity:
            podAntiAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
              - labelSelector:
                  matchExpressions:
                  - key: net.azure-devices.edge.module
                    operator: In
                    values:
                    - mediaedge
                topologyKey: "kubernetes.io/hostname"
        ```

* ***Rtspsim* 모듈을 사용할 때 404 오류 코드가 표시 됩니다.**  
    
    컨테이너는 컨테이너 내에서 정확히 한 폴더의 비디오를 읽습니다. 컨테이너 이미지에 이미 있는 폴더에 외부 폴더를 매핑하거나 바인딩하는 경우 Docker는 컨테이너 이미지에 있는 파일을 숨깁니다.
 
    예를 들어 바인딩이 없는 경우 컨테이너에는 다음 파일이 있을 수 있습니다.  

    ```
    root@rtspsim# ls /live/mediaServer/media  
    /live/mediaServer/media/camera-300s.mkv  
    /live/mediaServer/media/win10.mkv  
    ```
     
    그리고 호스트에는 다음 파일이 있을 수 있습니다.

    ```    
    C:\MyTestVideos> dir
    Test1.mkv
    Test2.mkv
    ```
     
    그러나 다음 바인딩이 배포 매니페스트 파일에 추가 되 면 Docker는 호스트에 있는 것과 일치 하도록/live/mediaServer/media의 콘텐츠를 덮어씁니다.

    `C:\MyTestVideos:/live/mediaServer/media`
    
    ```
    root@rtspsim# ls /live/mediaServer/media
    /live/mediaServer/media/Test1.mkv
    /live/mediaServer/media/Test2.mkv
    ```

## <a name="next-steps"></a>다음 단계

[동작 감지 및 이벤트 내보내기](detect-motion-emit-events-quickstart.md)
