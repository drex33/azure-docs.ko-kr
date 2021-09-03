---
title: Azure Stack Edge에 Azure Video Analyzer 배포
description: 이 문서에서는 Azure Stack Edge에 Azure Video Analyzer를 배포하는 데 도움이 되는 단계를 나열합니다.
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 1cfcd7956cd14d0c687c8619732523a5d7bba4c0
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114605210"
---
# <a name="deploy-azure-video-analyzer-on-azure-stack-edge"></a>Azure Stack Edge에 Azure Video Analyzer 배포

이 문서에서는 Azure Stack Edge에 Video Analyzer를 배포하는 데 도움이 되는 단계를 나열합니다. 디바이스가 설정되고 활성화되면 Video Analyzer를 배포할 준비가 된 것입니다. 

Video Analyzer의 경우, IoT Hub를 통해 배포되지만, Azure Stack Edge 리소스는 Kubernetes API를 노출합니다. 이를 통해 고객이 Video Analyzer와 인터페이스할 수 있는 추가 비 IoT Hub 인식 솔루션을 배포할 수 있습니다. 

> [!TIP]
> 사용자 지정 배포에 Kubernetes (K8s) API를 사용하는 것은 고급 사례입니다. 고객은 Kubernetes API를 사용하는 대신 에지 모듈을 만들고, 이를 IoT Hub를 통해 각 Azure Stack Edge 리소스에 배포하는 것이 좋습니다. 이 문서에서는 IoT Hub를 사용하여 Video Analyzer 모듈을 배포하는 단계를 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

* Video Analyzer 계정

    이 [클라우드 서비스](./overview.md)는 Video Analyzer 에지 모듈을 등록하고 녹화된 비디오 및 비디오 분석을 재생하는 데 사용됩니다.
* 관리 ID

    위의 스토리지 계정에 대한 액세스를 관리하는 데 사용되는 사용자 할당 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)입니다.
* [Azure Stack Edge 리소스](../../databox-online/azure-stack-edge-gpu-deploy-prep.md)
* [IoT Hub](../../iot-hub/iot-hub-create-through-portal.md)
* 스토리지 계정

    범용 v2 (GPv2) 스토리지 계정을 사용하는 것이 좋습니다.  
    [범용 v2 스토리지 계정](../../storage/common/storage-account-upgrade.md?tabs=azure-portal)에 대해 자세히 알아봅니다.
* 개발 컴퓨터의 [Visual Studio Code](https://code.visualstudio.com/). [Azure IoT Tools 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)이 있는지 확인합니다.
* 개발 컴퓨터가 연결된 네트워크에서 5671 포트를 통해 고급 메시지 큐 프로토콜을 허용하는지 확인합니다. 이렇게 설정하면 Azure IoT Tools에서 Azure IoT Hub와 통신할 수 있습니다.

## <a name="configuring-azure-stack-edge-for-using-video-analyzer"></a>Video Analyzer 사용을 위한 Azure Stack Edge 구성

Azure Stack Edge는 HaaS(Hardware-as-a-Service) 솔루션이며, 네트워크 데이터 전송 기능이 있는 AI 지원 에지 컴퓨팅 디바이스입니다. [Azure Stack Edge 및 자세한 설정 지침](../../databox-online/azure-stack-edge-gpu-deploy-prep.md)을 참조하세요. 시작하기 전에 아래 링크의 지침을 따르세요.

* [Azure Stack Edge / Data Box Gateway 리소스 만들기](../../databox-online/azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)
* [설치 및 설정](../../databox-online/azure-stack-edge-gpu-deploy-install.md)
* 연결 및 활성화

    1. [연결](../../databox-online/azure-stack-edge-gpu-deploy-connect.md)
    2. [네트워크 구성](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    3. [디바이스 구성](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
    4. [인증서 구성](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)
    5. [활성화](../../databox-online/azure-stack-edge-gpu-deploy-activate.md)
* [Azure Stack Edge에 IoT Hub 연결](../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute)
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Azure Stack Edge 로컬 UI에서컴퓨팅 사전 요구 사항 사용

계속하기 전에 다음을 확인합니다.

* Azure Stack Edge 리소스를 활성화 했습니다.
* Azure Stack Edge 리소스에 액세스하기 위한 PowerShell 5.0 이상을 실행하는 Windows 클라이언트 시스템에 대한 액세스 권한이 있습니다.
* Kubernetes 클러스터를 배포하려면, [로컬 웹 UI](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup)를 통해 Azure Stack Edge 리소스를 구성해야 합니다. 

    * 연결 및 구성:
    
        1. [연결](../../databox-online/azure-stack-edge-gpu-deploy-connect.md)
        2. [네트워크 구성](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
        3. [디바이스 구성](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
        4. [인증서 구성](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)
        5. [활성화](../../databox-online/azure-stack-edge-gpu-deploy-activate.md)
    * 컴퓨팅을 사용하도록 설정하려면, 디바이스의 로컬 웹 UI에서 컴퓨팅 페이지로 이동합니다.
    
        * 컴퓨팅을 사용하도록 설정하려는 네트워크 인터페이스를 선택합니다. 사용을 선택합니다. 컴퓨팅을 사용하도록 설정하면 해당 네트워크 인터페이스에서 디바이스에 가상 스위치가 생성됩니다.
        * Kubernetes 테스트 노드 IP와 Kubernetes 외부 서비스 IP를 비워 둡니다.
        * 적용 선택 - 이 작업에는 약 2분이 소요됩니다.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="../../databox-online/media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png" alt-text="Azure Stack Edge 로컬 UI의 컴퓨팅 사전 요구 사항":::

        * Kubernetes API 및 Azure Stack Edge 리소스에 대해 DNS가 구성되지 않은 경우, 창의 호스트 파일을 업데이트할 수 있습니다.
        
            * 관리자로서 텍스트 편집기를 엽니다.
            * 'C:\Windows\System32\drivers\etc\hosts'에서 파일을 엽니다.
            * Kubernetes API 디바이스 이름의 IPv4 및 호스트 이름을 파일에 추가합니다. (이 정보는 디바이스 섹션의 Azure Stack Edge Portal에서 찾을 수 있습니다.)
            * 저장 후 닫기

### <a name="deploy-video-analyzer-edge-modules-using-azure-portal"></a>Azure Portal을 사용하여 Video Analyzer Edge 모듈 배포

Azure Portal에서는 배포 매니페스트를 만들고 IoT Edge 디바이스에 배포를 푸시하는 방법을 안내합니다.  
#### <a name="select-your-device-and-set-modules"></a>디바이스를 선택하고 모듈 설정

1. [Azure Portal](https://ms.portal.azure.com/)에 로그인하고 IoT Hub로 이동합니다.
1. 메뉴에서 **IoT Edge** 를 선택합니다.
1. 디바이스 목록에서 대상 디바이스의 ID를 클릭합니다.
1. **모듈 설정** 을 선택합니다.

#### <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. Azure Portal에는 배포 매니페스트를 만들 수 있도록 안내하는 마법사가 있습니다. 마법사는 **모듈**, **경로** 및 **검토 + 만들기** 탭으로 구성된 세 단계를 제공합니다.

#### <a name="add-modules"></a>모듈 추가

1. 페이지의 **IoT Edge 모듈** 섹션에서 **추가** 드롭다운을 클릭하고 **IoT Edge 모듈** 을 선택하면 **IoT Edge 모듈 추가** 페이지가 표시됩니다.
1. **모듈 설정** 탭에서 모듈의 이름을 입력한 다음, 컨테이너 이미지 URI를 지정합니다.   
    예:
    
    * **IoT Edge 모듈 이름**: avaedge
    * **이미지 URI**: mcr.microsoft.com/media/Video-analyzer:1    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-module.png" alt-text="모듈 설정 탭을 보여 주는 스크린샷":::
    
    > [!TIP]
    > 이 절차에 설명된 대로 **모듈 설정**, **컨테이너 만들기 옵션** 및 **모듈 쌍 설정** 탭에서 값을 지정할 때까지 **추가** 를 선택하지 마세요.
    
    > [!WARNING]
    > 사용자가 모듈에 대한 호출을 수행할 때 Azure IoT Edge는 대/소문자를 구분합니다. 모듈 이름으로 사용할 정확한 문자열을 기록해 둡니다.

1. **환경 변수** 탭을 엽니다.
   
   표시되는 입력 상자에 다음 값을 추가합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/environment-variables.png" alt-text="환경 변수":::

1. **컨테이너 만들기 옵션** 탭을 엽니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/container-create-options.png" alt-text="컨테이너 만들기 옵션":::
 
    다음 JSON을 복사하고 상자에 붙여넣어, 모듈에서 생성되는 로그 파일의 크기를 제한합니다.
    
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
   
   JSON의 "Binds" 섹션에는 2개의 항목이 있습니다.
   1. "/var/lib/Videoanalyzer:/var/lib/Videoanalyzer": 컨테이너의 영구 애플리케이션 구성 데이터를 바인딩하고 에지 디바이스에 저장하는 데 사용됩니다.
   1. "/var/media:/var/media": 에지 디바이스와 컨테이너 사이에 미디어 폴더를 바인딩합니다. 이것은 에지 디바이스에 비디오 클립을 저장할 수 있도록 지원하는 pipelineTopology를 실행할 때 비디오 기록을 저장하는 데 사용됩니다.
   
1. **모듈 쌍 설정** 탭에서 다음 JSON을 복사하여 입력란에 붙여넣습니다.
 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/twin-settings.png" alt-text="쌍 설정":::

    Azure Video Analyzer를 실행하려면 [모듈 쌍 구성 스키마](module-twin-configuration-schema.md)에 나열된 필수 쌍 속성 집합이 필요합니다.  

    모듈 쌍 설정 편집 상자에 입력해야 하는 JSON은 다음과 같습니다.    
    ```
    {
        "applicationDataDirectory": "/var/lib/videoanalyzer",
        "ProvisioningToken": "{provisioning-token}",
    }
    ```
    다음은 JSON에 추가할 수 있으며 모듈을 모니터링하는 데 도움이 되는 몇 가지 추가 **권장** 속성입니다. 자세한 내용은 [모니터링 및 로깅](monitor-log-edge.md)을 참조하세요.
    
    ```
    "diagnosticsEventsOutputName": "diagnostics",
    "OperationalEventsOutputName": "operational",
    "logLevel": "Information",
    "logCategories": "Application,Events",
    "allowUnsecuredEndpoints": true,
    "telemetryOptOut": false
    ```
1. **다음: 경로** 를 선택하여 경로 섹션으로 이동합니다. 경로를 지정합니다.

    NAME에 **AVAToHub** 를 입력하고, VALUE에 **FROM /messages/modules/avaedge/outputs/ INTO $upstream** 을 입력합니다.
1. 다음: **검토 + 만들기** 를 선택하여 검토 섹션으로 이동합니다.
1. 배포 정보를 검토한 다음 **만들기** 를 선택하여 모듈을 배포합니다.

    > [!TIP]
    > 프로비저닝 토큰을 생성하려면 다음 단계를 따르세요.
1. Azure Portal을 열고 Video Analyzer로 이동합니다.
1. 왼쪽 탐색 창에서 **Edge 모듈** 을 클릭합니다.
1. Edge 모듈을 선택하고 **토큰 생성** 단추를 클릭합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/generate-provisioning-token.png" alt-text="토큰 생성" lightbox="./media/deploy-on-stack-edge/generate-provisioning-token.png":::
1. 프로비저닝 토큰을 복사합니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/copy-provisioning-token.png" alt-text="토큰 복사":::

#### <a name="optional-setup-docker-volume-mounts"></a>(선택 사항)Docker 볼륨 탑재 설정

작업 디렉터리의 데이터를 보려면, 다음 단계를 수행하여 배포 전에 Docker 볼륨 탑재를 설정합니다. 

이 단계에서는 게이트웨이 사용자를 만들고 파일 공유를 설정하여 Video Analyzer 작업 디렉터리와 Video Analyzer 미디어 폴더의 콘텐츠를 확인하는 과정을 다룹니다.

> [!NOTE]
> 바인딩 탑재는 지원되지만, 볼륨 탑재를 통해 데이터를 볼 수 있으며, 원하는 경우 원격으로 복사할 수 있습니다. 바인딩 및 볼륨 탑재를 모두 사용할 수 있지만, 동일한 컨테이너 경로를 가리킬 수 없습니다.

1. Azure Portal를 열고 Azure Stack Edge로 이동합니다.
1. 공유에 액세스할 수 있는 **게이트웨이 사용자** 를 만듭니다.
    
    1. 왼쪽 탐색 창에서 **클라우드 스토리지 게이트웨이** 를 클릭합니다.
    1. 왼쪽 탐색 창에서 **사용자** 를 클릭합니다.
    1. 사용자 이름 및 암호를 설정하려면 **+ 사용자 추가** 를 클릭합니다. (권장:`avauser`).
    1. **추가** 를 클릭합니다.

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/add-user.png" alt-text="사용자 추가":::
1. Video Analyzer 지속성을 위해 **로컬 공유** 를 만듭니다.

    1. **클라우드 스토리지 게이트웨이->공유** 를 클릭합니다.
    1. **+ 공유 추가** 를 클릭합니다.
    1. 공유 이름을 설정하세요. (권장:`ava`).
    1. 공유 유형을 SMB로 유지합니다.
    1. **Edge 컴퓨팅에서 공유 사용** 이 선택되어 있는지 확인합니다.
    1. **Edge 로컬 공유로 구성** 이 선택되어 있는지 확인합니다.
    1. 사용자 세부 정보에서 최근에 만든 사용자에게 공유에 대한 액세스 권한을 부여합니다.
    1. **만들기** 를 클릭합니다.
            
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/local-share.png" alt-text="로컬 공유":::  
    
        > [!TIP]
        > Azure Stack Edge에 연결된 Windows 클라이언트를 사용하여 [이 문서에 설명된](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share) 단계를 수행하여 SMB 공유에 연결합니다.    
1. 파일 동기화 스토리지에 대한 원격 공유를 만듭니다.

    1. 먼저 **클라우드 스토리지 게이트웨이->스토리지 계정** 을 클릭하여 동일한 지역에 Blob storage 계정을 만듭니다.
    1. **클라우드 스토리지 게이트웨이->공유** 를 클릭합니다.
    1. **+ 공유 추가** 를 클릭합니다.
    1. 공유 이름을 설정하세요. (권장:미디어).
    1. 공유 유형을 SMB로 유지합니다.
    1. **Edge 컴퓨팅에서 공유 사용** 이 선택되어 있는지 확인합니다.
    1. **Edge 로컬 공유로 구성** 이 선택 해제되어 있는지 확인합니다.
    1. 최근 만든 스토리지 계정을 선택합니다.
    1. 스토리지 유형을 블록 Blob으로 설정합니다.
    1. 컨테이너 이름을 설정합니다.
    1. 사용자 세부 정보에서 최근에 만든 사용자에게 공유에 대한 액세스 권한을 부여합니다.
    1. **만들기** 를 클릭합니다.    
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/remote-share.png" alt-text="원격 공유":::
1. 볼륨 탑재를 사용하도록 RTSP 시뮬레이터 모듈의 컨테이너 만들기 옵션 업데이트:
    1. **모듈 설정** 단추를 클릭합니다.

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/set-modules.png" alt-text="모듈 설정" lightbox="./media/deploy-on-stack-edge/set-modules.png":::
    1. **rtspsim** 모듈을 클릭합니다.

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/select-module.png" alt-text="모듈 선택":::
    1. **컨테이너 만들기 옵션** 탭을 선택하고 아래와 같이 탑재를 추가합니다.
    
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/update-module.png" alt-text="모듈 업데이트":::

        ```json
            "createOptions": 
            {
                "HostConfig": 
                {
                    "Mounts": 
                    [
                        {
                            "Target": "/var/media",
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
    1. **업데이트** 단추를 클릭합니다.
    1. **검토 및 만들기** 단추를 클릭하고 마지막으로 **만들기** 단추를 클릭하여 모듈을 업데이트합니다.
    
### <a name="verify-that-the-module-is-running"></a>모듈이 실행 중인지 확인합니다.

마지막 단계는 모듈이 예상대로 연결되어 실행되는지 확인하는 것입니다. IoT Hub 리소스에서 IoT Edge 디바이스에 대한 모듈의 런타임 상태가 실행 중이어야 합니다.

모듈이 실행되는지 확인하려면 다음을 수행합니다.

1. Azure Portal에서 Azure Stack Edge 리소스로 돌아갑니다.
1. 모듈 타일을 선택합니다. 그러면 모듈 블레이드로 이동됩니다. 모듈 목록에서, 배포한 모듈을 찾습니다. 추가한 모듈의 런타임 상태가 실행 중일 것입니다.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/running-module.png" alt-text="사용자 지정 모듈" lightbox="./media/deploy-on-stack-edge/running-module.png":::

### <a name="configure-the-azure-iot-tools-extension"></a>Azure IoT Tools 확장 구성

다음 지침에 따라 Azure IoT Tools 확장을 사용하여 IoT 허브에 연결합니다.

1. Visual Studio Code에서 보기>탐색기를 차례로 선택합니다. 또는 Ctrl+Shift+E를 선택합니다.
1. 탐색기 탭의 왼쪽 아래 모서리에서 Azure IoT Hub를 선택합니다.
1. 기타 옵션 아이콘을 선택하여 상황에 맞는 메뉴를 표시합니다. 그런 다음, IoT Hub 연결 문자열 설정을 선택합니다.
1. 입력 상자가 표시되면 IoT Hub 연결 문자열을 입력합니다. 

   * 연결 문자열을 가져오려면, Azure Portal에서 IoT Hub로 이동하여 왼쪽 탐색 창에서 공유 액세스 정책을 클릭합니다.
   * iothubowner를 클릭하여 공유 액세스 키를 가져옵니다.
   * 연결 문자열 – 기본 키를 복사하여 VSCode의 입력 상자에 붙여넣습니다.

   연결 문자열은 다음과 같습니다.<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   연결에 성공하면 에지 디바이스의 목록이 표시됩니다. Azure Stack Edge가 표시되어야 합니다. 이제 상황에 맞는 메뉴를 통해 IoT Edge 디바이스를 관리하고 Azure IoT Hub와 상호 작용할 수 있습니다. 에지 디바이스에 배포된 모듈을 보려면, Azure Stack 디바이스 아래에서 모듈 노드를 펼칩니다.
    
## <a name="troubleshooting"></a>문제 해결

* **Kubernetes API Access (Kubectl)**

    * 설명서에 따라 [Kubernetes 클러스터에 대한 액세스](../../databox-online/azure-stack-edge-gpu-create-kubernetes-cluster.md)를 위해 컴퓨터를 구성합니다.
    * 배포된 모든 IoT Edge 모듈은 `iotedge` 네임스페이스를 사용합니다. Kubectl를 사용할 때 이를 포함해야 합니다.  
* **모듈 로그**

    로그를 가져오기 위해 `iotedge` 도구에 액세스할 수 없습니다. [Kubectl 로그](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs)를 사용하여 파일에 대한 로그 또는 파이프를 확인해야 합니다. 예: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`  
* **Pod 및 노드 메트릭**

    Pod 및 노드 메트릭을 보려면, [kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)을 사용합니다.
    <br/>`kubectl top pods -n iotedge` 
* **모듈 네트워킹**   

    Azure Stack Edge에서 모듈 검색의 경우, 모듈의 createOptions에 호스트 포트 바인딩이 있어야 합니다. 그러면 모듈이 `moduleName:hostport`에 대해 주소를 지정할 수 있습니다.
    
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
* **gRPC 사용 시 공유 메모리**

    호스트 IPC를 사용하여 네임스페이스의 pod에서 Azure Stack Edge 리소스의 공유 메모리를 지원합니다.
    IoT Hub를 통해 배포용 에지 모듈에서 공유 메모리를 구성합니다.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    //(Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
* **(고급) Pod 공동 배치**

    K8s를 사용하여 gRPC를 통해 Video Analyzer와 통신하는 사용자 지정 유추 솔루션을 배포하는 경우, Pod가 Video Analyzer 모듈과 동일한 노드에 배포 되었는지 확인해야 합니다.

    * **옵션 1** - 공동 배치를 위해 노드 선호도 및 기본 제공 노드 레이블을 사용합니다.

    사용자가 노드에 레이블을 설정할 수 있는 액세스 권한이 없기 때문에 현재 NodeSelector 사용자 지정 구성은 옵션으로 표시되지 않습니다. 그러나 고객의 토폴로지 및 명명 규칙에 따라 [기본 제공 노드 레이블](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels)을 사용할 수 있습니다. Video Analyzer를 사용하여 Azure Stack Edge 리소스를 참조하는 nodeAffinity 섹션을 유추 Pod 매니페스트에 추가하여 공동 배치할 수 있습니다.
    * **옵션 2** -공동 배치에 Pod 선호도 사용(권장).

        Kubernetes는 동일한 노드에서 Pod를 예약할 수 있는 [Pod 선호도](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)를 지원합니다. Video Analyzer 모듈을 참조하는 nodeAffinity 섹션을 유추 Pod 매니페스트에 추가하여 공동 배치할 수 있습니다.

         ```json   
        // Example Video Analyzer module deployment match labels
        selector:
          matchLabels:
            net.azure-devices.edge.deviceid: dev-ase-1-edge
            net.azure-devices.edge.module: mediaedge
        
        // Example Inference deployment manifest pod affinity
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
* **`rtspsim` 모듈 사용 시 404 오류 코드**  
    
    컨테이너는 컨테이너 내에서 정확히 한 폴더의 비디오를 읽습니다. 컨테이너 이미지에 이미 존재하는 폴더에 외부 폴더를 매핑하거나 바인딩하는 경우, Docker는 컨테이너 이미지에 있는 파일을 숨깁니다.  
 
    예를 들어, 바인딩이 없는 경우, 컨테이너에는 다음 파일이 있을 수 있습니다.  
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
     
    그러나 다음 바인딩이 배포 매니페스트 파일에 추가되면, Docker는 호스트에 있는 것과 일치 하도록 /live/mediaServer/media의 콘텐츠를 덮어씁니다.
    `C:\MyTestVideos:/live/mediaServer/media`
    
    ```
    root@rtspsim# ls /live/mediaServer/media
    /live/mediaServer/media/Test1.mkv
    /live/mediaServer/media/Test2.mkv
    ```

## <a name="next-steps"></a>다음 단계

[동작 감지 및 이벤트 내보내기](detect-motion-emit-events-quickstart.md)
