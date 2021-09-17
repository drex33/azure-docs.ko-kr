---
title: Live Video Analytics에서 Azure Video Analyzer로 마이그레이션
description: Live Video Analytics에서 Azure Video Analyzer로 마이그레이션하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 08/16/2021
ms.openlocfilehash: 452b3430dd1e0eafbfe35166339e42bcbfe1f47b
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123471699"
---
# <a name="how-to-migrate-from-live-video-analytics-to-azure-video-analyzer"></a>Live Video Analytics에서 Azure Video Analyzer로 마이그레이션하는 방법

이 문서는 IoT Edge 디바이스에 Live Video Analytics Edge 모듈을 배포했고 현재 RTSP(Real Time Streaming Protocol) 카메라의 피드를 처리하기 위해 미디어 그래프를 실행하고 있는 경우에 사용해야 합니다.

RTSP 카메라를 사용하지 않고 [Live Video Analytics에 관한 빠른 시작이나 자습서](../live-video-analytics-edge/overview.md) 중 하나를 사용하는 경우에는 [Azure Video Analyzer를 사용하여 해당 빠른 시작이나 자습서](../../azure-video-analyzer/video-analyzer-docs/overview.md)로 전환하면 됩니다.

다음 지침은 단일 IoT Edge 디바이스에 대해 Live Video Analytics를 Azure Video Analyzer로 마이그레이션하는 작업에 적용됩니다. 각 개별 IoT Edge 디바이스에 대해 단계를 반복해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 활성화된 Azure 구독.

   > [!NOTE]
   >  [기여자](../../role-based-access-control/built-in-roles.md) 역할과 새 리소스(사용자가 할당한 관리 ID, 스토리지 계정, Video Analyzer 계정)를 만드는 리소스 그룹에 대한  [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md) 역할 모두에 액세스할 수 있는 Azure 구독이 필요합니다. 적절한 권한이 없는 경우 계정 관리자에게 권한을 부여해 달라고 요청하세요. 

* Live Video Analytics를 설치한 IoT Edge 디바이스에 대한 관리 권한이 필요합니다.

## <a name="create-a-backup-of-current-settings"></a>현재 설정의 백업 만들기

마이그레이션 작업을 시작하기 전에 에지 배포 매니페스트와 Live Video Analytics 미디어 그래프 구성을 저장하는 것이 좋습니다.

> [!NOTE]
> 카메라 RTSP 자격 증명과 스트림 URL 세부 정보 아래에 나열된 단계는 검색할 수 없습니다.  해당 정보는 카메라의 관리 애플리케이션에서 직접 검색해야 합니다.

### <a name="save-the-current-deployment-manifest"></a>현재 배포 매니페스트 저장

1. Azure Portal에 로그인합니다.

1. **IoT Hub** 페이지로 이동하고 **자동 디바이스 관리** 에서 **IoT Edge** 를 선택합니다.

1. **IoT Edge** 에서 to Azure Video Analyzer로 마이그레이션할 IoT Edge 디바이스를 선택합니다.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/iot-edge.png" alt-text="IoT Edge 디바이스를 보여 주는 스크린샷":::

1. **모듈 설정** 을 선택한 다음, **검토 + 만들기** 를 선택합니다.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/set-modules.png" alt-text="배포 매니페스트를 보여 주는 스크린샷":::

1. 창에서 **배포** 섹션을 복사하고 안전한 위치에 저장합니다.

1. 포털의 오른쪽 위에 있는 **X** 를 선택합니다.  

    > [!NOTE]
    > **만들기** 를 선택하지 ‘마세요’.  배포 매니페스트를 검토하고 `modules`에서 `lvaEdge` 노드를 찾습니다.  `lvaEdge` 노드 아래에 `env` 노드가 있는 경우 LOCAL_EDGE_USER 및 lOCAL_GROUP_ID 값을 기록해 둡니다. 이 값은 문서의 뒷부분에서 필요합니다.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/deployment-manifest-env-node.png" alt-text="로컬 에지 사용자 및 그룹의 배포 매니페스트와 env 섹션을 보여 주는 스크린샷":::

### <a name="save-the-live-video-analytics-topologies"></a>Live Video Analytics 토폴로지 저장

1. Live Video Analytics 미디어 그래프를 저장하려면 IoT Edge 디바이스에서 Live Video Analytics 모듈을 선택합니다.  다음 예제에서 컨테이너 이름은 *lvaEdge* 입니다.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/live-video-analytics-edge.png" alt-text="Live Video Analytics IoT Edge 모듈 컨테이너 이름을 보여 주는 스크린샷":::

1. **직접 메서드** 를 선택합니다.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/direct-method.png" alt-text="Live Video Analytics의 직접 메서드 호출을 보여 주는 스크린샷":::

1. 메서드 이름 필드에 **GraphTopologyList** 를 입력하고 페이로드에 다음 코드를 입력합니다.

    ```JSON
    {
        "@apiVersion" : "2.0"
    }
    ```

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/method-name.png" alt-text="IoT Edge 직접 메서드 호출을 보여 주는 스크린샷":::

1. **메서드 호출** 을 선택합니다.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/invoke-method.png" alt-text="invoke 메서드를 보여 주는 스크린샷":::

1. 반환은 결과 섹션에 표시됩니다.  Live Video Analytics 그래프 토폴로지입니다.  JSON을 복사하고 안전한 위치에 저장합니다.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/topology-result.png" alt-text="GraphTopologyList 메서드의 결과를 보여 주는 스크린샷":::

1. 프로세스를 반복하지만, `GraphTopologyList`를 `GraphInstanceList`로 바꾸고, 결과 섹션을 저장합니다.  그래프 인스턴스의 목록입니다. 모든 활성 인스턴스의 이름을 기록해 둡니다.

1.  Live Video Analytics 설정의 백업 복사본을 만든 후에는 **메서드 이름** 에 **GraphInstanceDeactivate** 를 입력한 다음, 다음 코드를 입력하여 ‘모든 활성 미디어 그래프를 비활성화’할 수 있습니다.

    ```json
    {
        "@apiVersion" : "2.0",
        "name" : "<Graph_Name>"
    }
    ```

    > [!NOTE]
    > Graph_Name을 새 결과의 활성 인스턴스 이름으로 바꿉니다.  모든 활성 그래프가 비활성화될 때까지 이 프로세스를 반복합니다. 

## <a name="azure-video-analyzer-device-preparation"></a>Azure Video Analyzer: 디바이스 준비 

마이그레이션을 시작하려면 Azure Video Analyzer 모듈을 실행하도록 IoT Edge 디바이스를 구성해야 합니다. Azure Video Analyzer 모듈은 권한 없는 로컬 사용자 계정으로 IoT Edge 디바이스에서 실행해야 합니다. 이 모듈은 애플리케이션 구성 데이터를 저장하기 위해 특정 로컬 폴더가 필요합니다. RTSP 카메라 시뮬레이터 모듈에는 라이브 비디오 피드를 합성할 수 있는 비디오 파일이 필요합니다. 

1. SSH를 통해 IoT Edge 디바이스에 로그인합니다.

    > [!Note]
    > IoT Edge 디바이스 매니페스트 파일의 검토(“현재 설정 백업 만들기” 섹션에 있는 6단계)에 따라 IoT Edge 디바이스에 새 사용자/그룹을 만들거나 Live Video Analytics에서 만든 사용자/그룹을 다시 사용해야 합니다.

    IoT Edge 디바이스가 Live Video Analytics의 로컬 사용자 및 그룹 ID(배포 매니페스트에 있음)를 사용하여 구성된 경우 1단계를 건너뛸 수 있지만 사용자 및 그룹 이름과 값을 기록해 둡니다. 해당 이름과 값은 이 문서의 뒷부분에서 사용됩니다.  또한 Live Video Analytics 배포에 고유한 폴더 구조(배포 매니페스트에 있음)를 사용하는 경우 *videoanalyzer* 를 해당 값으로 바꾸고 2단계에서 처음 두 줄을 생략합니다.  

    다음 예제에서는 Live Video Analytics 배포에 고유한 폴더 구조를 보여 줍니다.  *applicationDataDirectory* 는 /var/lib/videofiles입니다. 이 예제에서 Live Video Analytics의 *localedgeuser*, *localedgegroup* 은 IoT Edge 디바이스에 만들어져 있습니다.

    ```
    "lvaEdge": {
                "properties.desired": {
                    "applicationDataDirectory": "/var/lib/videofiles",
                    "azureMediaServicesArmId": "/subscriptions/resourceGroups/lvamigration/providers/microsoft.media/mediaservices/lvasamplejbbvaomtycjas",
                    "aadTenantId": "<guid>",
                    "aadServicePrincipalAppId": "<guid>",
                    "aadServicePrincipalSecret": "<guid>",
                    "aadEndpoint": "https://login.microsoftonline.com",
                    "aadResourceId": "https://management.core.windows.net/",
                    "armEndpoint": "https://management.azure.com/",
                    "diagnosticsEventsOutputName": "AmsDiagnostics",
                    "operationalEventsOutputName": "AmsOperational",
                    "logLevel": "Information",
                    "logCategories": "Application,Events",
                    "allowUnsecuredEndpoints": true,
                    "telemetryOptOut": false
                }
            },
    ```

    다음 코드를 실행합니다.

     ```
       sudo mkdir -p /var/lib/videofiles/tmp/ 
       sudo chown -R localedgeuser:localedgegroup /var/lib/videofiles/tmp/
       sudo mkdir -p /var/lib/videofiles/logs
       sudo chown -R localedgeuser:localedgegroup /var/lib/videofiles/logs
     ```

1. 다음 코드를 실행하여 IoT Edge 디바이스에서 로컬 사용자 계정을 만듭니다.

   ```bash
   sudo groupadd -g 1010 localedgegroup
   sudo useradd --home-dir /home/localedgeuser --uid 1010 --gid 1010 localedgeuser
   sudo mkdir -p /home/localedgeuser
   sudo chown -R localedgeuser:localedgegroup /home/localedgeuser/
   ```

1. 다음 코드를 사용하여 *avaedge* 폴더 구조를 만듭니다.

   ```bash
   sudo mkdir -p /var/lib/videoanalyzer 
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/
   sudo mkdir -p /var/lib/videoanalyzer/tmp/ 
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/tmp/
   sudo mkdir -p /var/lib/videoanalyzer/logs
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/logs
   ```

## <a name="create-azure-resources"></a>Azure 리소스 만들기

다음 단계는 필요한 Azure 리소스를 만드는 것입니다. 다음 지침은 [Azure 리소스 만들기 섹션](../../azure-video-analyzer/video-analyzer-docs/get-started-detect-motion-emit-events-portal.md)에 문서화되어 있으며 여기서는 편의상 반복됩니다. 

이제 필요한 Azure 리소스(Video Analyzer 계정, 스토리지 계정, 사용자가 할당한 관리 ID)를 만듭니다. 

Azure Video Analyzer 계정을 만들 때 Azure Storage 계정을 연결해야 합니다. Video Analyzer를 사용하여 카메라에서 라이브 비디오를 녹화하는 경우 해당 데이터는 스토리지 계정의 컨테이너에 BLOB으로 저장됩니다. 관리 ID를 사용하여 Video Analyzer 계정에 스토리지 계정에 대한 적절한 액세스 권한을 부여해야 합니다. 

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Azure Portal에서 Video Analyzer 계정 만들기

1.  [Azure Portal](https://portal.azure.com/)에 로그인합니다. 

1. 검색 상자에 **Video Analyzer** 를 입력합니다. 

1.  **서비스** 에서 **Video Analyzer** 를 선택합니다. 

1.  **추가** 를 선택합니다. 

1.  **Video Analyzer 계정** 섹션에서 다음 필수 값을 입력합니다. 

   - **구독**: Video Analyzer 계정을 만드는 데 사용 중인 구독을 선택합니다. 

   - **리소스 그룹**: Video Analyzer 계정을 만들 리소스 그룹을 선택하거나  **새로 만들기** 를 선택하여 리소스 그룹을 만듭니다. 

   - **Video Analyzer 계정 이름**: Video Analyzer 계정의 이름을 입력합니다. 이름은 공백 없이 모두 소문자거나 숫자여야 하고 길이는 3~24자여야 합니다. 

   - **위치**: Video Analyzer 계정을 배포할 위치를 선택합니다(예:  **미국 서부 2**). 

   - **스토리지 계정**: 스토리지 계정을 만듭니다.  [표준 범용 v2](../../storage/common/storage-account-overview.md#types-of-storage-accounts) 스토리지 계정을 선택하는 것이 좋습니다. 

      > [!NOTE]
      > 스토리지 계정이 Azure Video Analyzer 계정을 배포할 지역에 있는 경우 새 스토리지 계정을 만드는 대신 해당 스토리지 계정을 사용하도록 선택할 수 있습니다. 

   - **사용자 ID**: 사용자가 할당한 관리 ID를 새로 만들고 이름을 지정합니다. 

1. 양식 아래쪽에서 “미디어 콘텐츠를 처리하기 위한 관련 법률에 따라 필요한 모든 권리와 동의를 획득했음을 인정하며 모든 관련 법률을 준수하는 데 동의합니다.” 옆에 있는 상자를 **선택** 합니다. 

1. 양식 아래쪽에서 **검토 + 만들기** 를 선택합니다. 

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/video-analyzer-account.png" alt-text="Azure Portal에 Video Analyzer 계정을 만드는 방법을 보여 주는 스크린샷":::

   > [!NOTE]
   > 이 Azure 리소스를 리소스 그룹에 추가하게 됩니다.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/resources.png" alt-text="Azure 리소스 그룹에도 추가될 리소스를 보여 주는 스크린샷":::

## <a name="deploy-the-azure-video-analyzer-edge-module"></a>Azure Video Analyzer 에지 모듈 배포

### <a name="generate-azure-video-analyzer-edge-module-token"></a>Azure Video Analyzer 에지 모듈 토큰 생성

1. Video Analyzer 계정으로 이동합니다.

1. **에지** 창에서 **에지 모듈** 을 선택합니다.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge-module.png" alt-text="Azure Video Analyzer 계정 Edge 모듈 만들기를 보여 주는 스크린샷":::

1. **에지 모듈 추가** 를 선택하고, 새 에지 모듈의 이름으로 **avaedge** 를 입력한 다음, **추가** 를 선택합니다.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/add-edge-modules.png" alt-text="에지 모듈을 추가하는 방법을 보여 주는 스크린샷":::

1. **프로비저닝 토큰 복사** 창이 오른쪽에 표시됩니다. **IoT 모듈 배포를 위해 권장되는 속성** 에서 코드 조각을 복사합니다.  이 코드 조각은 이후 단계에서 필요합니다.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/provisioning-token.png" alt-text="프로비저닝 토큰 창을 보여 주는 스크린샷":::

### <a name="deploy-the-azure-video-analyzer-edge-module"></a>Azure Video Analyzer 에지 모듈 배포

1.  Azure IoT Hub로 이동합니다.

1. **자동 디바이스 관리** 에서 **IoT Edge 선택** 을 선택합니다.

1. IoT Edge 디바이스의 디바이스 ID 값을 선택합니다.

1. 모듈 설정을 선택합니다.

1. **추가** 를 선택한 다음, 드롭다운 메뉴에서 **IoT Edge 모듈 마켓플레이스** 를 선택합니다.

1. 검색 필드에 Azure Video Analyzer를 입력합니다.

1. Azure Video Analyzer를 선택합니다.

1. **모듈 설정 창** 에서 **AzureVideoAnalyzerEdge** 를 선택합니다.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge.png" alt-text="IoT Edge 추가 모듈 Azure Video Analyzer를 보여 주는 스크린샷":::

1. IoT Edge 모듈 이름 필드에 **avaedge** 를 입력합니다.
1. **환경 변수** 를 선택합니다. 

   > [!NOTE]
   > Live Video Analytics 배포가 “현재 설정의 백업 만들기” 섹션의 6단계에 있는 로컬 사용자와 그룹을 사용한 경우 다음 단계에서 LOCAL_GROUP_ID, LOCAL_USER_ID 대신 해당 값을 사용합니다.

1. 이름 필드에 **LOCAL_USER_ID** 를 입력하고 값 필드에 **1010** 을 입력합니다.  다음 이름 필드에 **LOCAL_GROUP_ID** 를 입력하고 값 필드에 **1010** 을 입력합니다.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/environment-variables.png" alt-text="Azure Video Analyzer 환경 변수를 보여 주는 스크린샷":::

1. **컨테이너 만들기 옵션** 을 선택하고 다음을 붙여넣습니다.

      ```json
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
                  "/var/media/:/var/media/",
                  "/var/lib/videoanalyzer/:/var/lib/videoanalyzer"
            ],
            "IpcMode": "host",
            "ShmSize": 1536870912
         }
      }
      ```

1.  **모듈 쌍 설정** 을 선택하고 Video Analyzer 계정의  **프로비저닝 토큰 복사** 페이지에서 이전에 복사한 코드 조각을 붙여넣습니다. 

      ```json
      {
         "applicationDataDirectory": "/var/lib/videoanalyzer",
         "ProvisioningToken": "<Provisioning Token Value>",
         "diagnosticsEventsOutputName": "diagnostics",
         "operationalEventsOutputName": "operational",
         "logLevel": "information",
         "LogCategories": "Application,Events",
         "allowUnsecuredEndpoints": true,
         "telemetryOptOut": false
      }
      ```

      > [!NOTE]
      > `<Provisioning Token Value>`는 Azure Video Analyzer 에지 모듈 토큰 생성 섹션 4단계에서 이전에 캡처한 프로비저닝 토큰의 자리 표시자입니다.

1. **업데이트** 를 선택합니다.

1.  **경로** 를 선택합니다.
1.  **이름** 에 **AVAToHub** 를 입력합니다.  **값** 에 __FROM /messages/modules/avaedge/outputs/* INTO $upstream__ 을 입력합니다. 
1. 경로 오른쪽에 있는 휴지통 아이콘을 선택하여 경로 **LVAToHub** 를 제거합니다.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/delete-route.png" alt-text="Live Video Analytics IoT Edge 경로를 삭제하는 방법을 보여 주는 스크린샷":::

1. **모듈** 에서 *lvaEdge* 모듈을 찾고 오른쪽에서 휴지통 아이콘을 선택하여 해당 모듈을 제거합니다.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/delete-live-video-analytics-edge.png" alt-text="Live Video Analytics IoT Edge 모듈을 삭제하는 방법을 보여 주는 스크린샷":::

1.  **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택하여 **avaedge** 에지 모듈을 배포합니다. 

### <a name="verify-your-deployment"></a>배포 확인

디바이스 세부 정보 페이지에서 **avaedge**  모듈이 **배포에 지정됨** 및 **디바이스에서 보고됨** 으로 나열되는지 확인합니다. 

디바이스에서 모듈이 시작된 후 IoT Hub에 다시 보고되는 데 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다. 상태 코드 **200 -- OK** 는  [IoT Edge 런타임](../../iot-edge/iot-edge-runtime.md)이 정상이며 제대로 작동하고 있음을 의미합니다. 

:::image type="content" source="./media/migrate-to-azure-video-analyzer/status-200.png" alt-text="IoT Edge 런타임이 200 상태를 보고하고 있음을 보여 주는 스크린샷":::

:::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge-running.png" alt-text="IoT Edge 런타임이 모든 모듈이 실행 중이라고 보고하고 있음을 보여 주는 스크린샷":::

## <a name="convert-media-graph-topologies-to-azure-video-analyzer-pipelines"></a>미디어 그래프 토폴로지를 Azure Video Analyzer 파이프라인으로 변환

Live Video Analytics가 사용하는 미디어 그래프 토폴로지와 Azure Video Analyzer가 사용하는 파이프라인 토폴로지 간에는 몇 가지 차이점이 있습니다.  다음 표를 사용하여 Live Video Analytics 및 Azure Video Analyzer 토폴로지를 비교합니다.

| 시나리오                                                     | 미디어 그래프(Live Video Analytics)                                            | 파이프라인(Azure Video Analyzer)                                               |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 여러 AI 모델을 사용하여 관심 있는 개체 기록         | [ai-composition](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/ai-composition/2.0/topology.json) | [ai-composition](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/ai-composition/topology.json) |
| 비디오 및 오디오                                              | [audio-video]()                                               | [audio-video]()                                               |
| 연속 비디오 녹화                                   | [cvr-asset](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset) | [cvr-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-video-sink) |
| gRPC 확장을 사용하여 연속 비디오 녹화 및 유추 | [cvr-with-grpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json) | [cvr-with-grpcExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-grpcExtension/topology.json) |
| HTTP 확장을 사용하여 연속 비디오 녹화 및 유추 | [cvr-with-httpExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-httpExtension/2.0/topology.json) | [cvr-with-httpExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-httpExtension/topology.json) |
| 동작 감지를 사용하여 연속 비디오 녹화             | [cvr-with-motion](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-motion/2.0/topology.json) | [cvr-with-motion](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-motion/topology.json) |
| 외부 AI의 이벤트를 기반으로 [Azure Video Analyzer 비디오/자산]에 대해 이벤트 기반 비디오 녹화 | [evr-grpcExtension-assets](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-grpcExtension-assets) | [evr-grpcExtension-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sink) |
| 외부 AI의 이벤트를 기반으로 [Azure Video Analyzer 비디오/자산]에 대해 이벤트 기반 비디오 녹화 | [evr-httpExtension-assets](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-httpExtension-assets) | [evr-httpExtension-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-httpExtension-video-sink) |
| 외부 유추 엔진에서 검색되는 특정 개체를 기반으로 [자산/비디오 싱크]에 대해 이벤트 기반 비디오 녹화 | [evr-hubMessages-assets](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/2.0/topology.json) | [evr-hubMessages-video-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json) |
| IoT Edge Hub를 통해 전송된 메시지를 기반으로 파일에 대해 이벤트 기반 비디오 녹화 | [evr-hubMessage-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/2.0/topology.json) | [evr-hubMessage-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-hubMessage-file-sink/topology.json) |
| 동작 이벤트를 기반으로 로컬 파일에 대해 이벤트 기반 비디오 녹화 | [evr-motion-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json) | [evr-motion-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-motion-file-sink/topology.json) |
| 동작 이벤트를 기반으로 [자산/비디오 싱크] 및 로컬 파일에 대해 이벤트 기반 비디오 녹화 | [evr-motion-assets-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets-files/2.0/topology.json) | [evr-motion-video-sink-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-motion-video-sink-file-sink/topology.json) |
| gRPC 확장을 통해 라이브 비디오를 분석하여 Intel의 OpenVINO(TM) DL Streamer - Edge AI 확장 모듈에 이미지 보내기 | [grpcExtensionOpenVINO](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) | [grpcExtensionOpenVINO](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json) |
| HTTP 확장을 통해 라이브 비디오를 분석하여 외부 유추 엔진에 이미지 보내기 | [httpExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json) | [httpExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json) |
| HTTP 확장을 통해 라이브 비디오를 분석하여 Intel의 OpenVINO™ Model Server – AI 확장 모듈에 이미지 보내기 | [httpExtensionOpenVINO](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtensionOpenVINO/2.0/topology.json) | [httpExtensionOpenVINO](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtensionOpenVINO/topology.json) |
| 공간 분석을 위해 Computer Vision을 사용하여 라이브 비디오 분석 | [lva-spatial-analysis](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/lva-spatial-analysis/2.0/topology.json) | [spatial-analysis](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/spatial-analysis) |

> [!NOTE]
> GitHub의 샘플 섹션에서 [Live Video Analytics 미디어 그래프 토폴로지](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies) 및 [Azure Video Analyzer 파이프라인 토폴로지](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies)를 둘 다 찾고 비교할 수 있습니다. 이러한 샘플을 검토하면 기존 미디어 그래프 토폴로지를 Azure Video Analyzer 파이프라인 토폴로지로 업데이트할 수 있습니다.

### <a name="set-and-activate-the-pipeline-topology"></a>파이프라인 토폴로지 설정 및 활성화

미디어 그래프 토폴로지(Live Video Analytics)를 파이프라인 토폴로지(Azure Video Analyzer)로 변환한 후에는 *avaedge* 모듈에서 파이프라인을 설정해야 합니다.

1. 토폴로지를 설정하려면 IoT Edge 디바이스에서 Azure Video Analyzer 에지 모듈을 선택합니다.  아래 예제에서 컨테이너 이름은 *avaedge* 입니다.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-container.png" alt-text="Live Video Analytics IoT Edge 모듈을 보여 주는 스크린샷":::

1. **직접 메서드** 를 선택합니다.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/direct-method.png" alt-text="Live Video Analytics의 직접 메서드 호출을 보여 주는 스크린샷":::

1. **메서드 이름** 에 **pipelineTopologySet** 를 입력하고 페이로드에 변환된 파이프라인 JSON을 입력합니다.

1. **메서드 호출** 을 선택합니다.

      반환 필드에 “status”: 201, 해당 토폴로지가 차례로 표시됩니다.  이 상태는 새 토폴로지가 만들어졌음을 나타냅니다.

1. 다음으로, 토폴로지를 사용하여 라이브 파이프라인을 만들어야 합니다.  하지만 1~3단계를 사용하여 메서드 이름 필드에 **livePipelineSet** 를 입력하고 페이로드 필드에 다음을 입력합니다.

   ```JSON
         {
   "@apiVersion": "1.0",
   "name": "<live pipeline name>",
   "properties": {
      "topologyName": "<Name of the set topology>",
      "description": "Sample pipeline description",
      "parameters": [
         {
         "name": "rtspUrl",
         "value": "<RTSP camera string>"
         },
         {
         "name": "rtspUserName",
         "value": "<Camera User>"
         },
         {
         "name": "rtspPassword",
         "value": "Camera User Password"
         }
      ]
   }
   }
   ```

   > [!NOTE]
   > `<live pipeline name>`, `<Name of the set topology>`, `<RTSP camera string>`, `<Camera User Password>` 값을 해당 환경의 값으로 바꿉니다. 

1. **메서드 호출** 을 선택합니다.
   
      반환 필드에 “status”: 201, 해당 라이브 파이프라인이 차례로 표시됩니다.  이 상태는 새 파이프라인이 만들어졌음을 나타냅니다.

2. 마지막으로, 1~3단계에 따라 라이브 파이프라인을 활성화하지만, **메서드 이름** 에 **livePipelineActivate** 를 입력하고, 페이로드 필드에 다음을 입력합니다.

      ```JSON
         {
            "@apiVersion": "1.0",
            "name": "<live pipeline name>"
         }
      ```

    > [!NOTE]
    > `<live pipeline name>`을 위에서 설정한 라이브 파이프라인 이름으로 바꿉니다.

4. **메서드 호출** 을 선택합니다.
   
   반환 필드에 “status”: 200이 표시됩니다.  이 상태는 새 파이프라인이 활성화되었음을 나타냅니다.

## <a name="direct-methods"></a>직접 메서드

Live Video Analytics 및 Azure Video Analyzer는 둘 다 직접 메서드 호출을 사용합니다.  마이그레이션의 일부로 직접 메서드가 업데이트되었으며 Live Video Analytics 솔루션에서 사용할 수 있는 워크플로에 대한 검토가 필요합니다.  다음 표에서는 [Live Video Analytics 직접 메서드 호출](direct-methods.md)과 [Azure Video Analyzer 직접 메서드 호출](../../azure-video-analyzer/video-analyzer-docs/direct-methods.md) 간 차이점을 보여 줍니다.

| 라이브 비디오 분석 | Azure Video Analyzer |
| ------------------------ | ------------------------ |
| GraphTopologyList        | pipelineTopologyList     |
| GraphTopologySet         | pipelineTopologySet      |
| GraphTopologyDelete      | pipelineTopologyDelete   |
| GraphInstanceList        | livePipelineList         |
| GraphInstanceSet         | livePipelineSet          |
| GraphInstanceActivate    | livePipelineActivate     |
| GraphInstanceDeactivate  | livePipelineDeactivate   |
| GraphInstanceDelete      | livePipelineDelete       |

## <a name="miscellaneous-changes"></a>기타 변경 내용 

- Appsettings.json: moduleId가 모듈을 참조하도록 appsettings.json 파일을 업데이트합니다. 즉, *avaeEdge* 로 설정합니다. 

- [gRPC 계약](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc)이 약간 변경되었습니다. 기존 gRPC 확장은 계속 작동하지만 프로토타입 파일을 업데이트하는 것이 좋습니다. 변경은 inferencing.proto : string sequence_id=14의 새 줄입니다.

## <a name="next-steps"></a>다음 단계

-  [빠른 시작: 동작이 검색되면 클라우드에 비디오 녹화](../../azure-video-analyzer/video-analyzer-docs/detect-motion-record-video-clips-cloud.md)를 사용해 봅니다. 
-  [빠른 시작: 라이브 비디오 분석](../../azure-video-analyzer/video-analyzer-docs/analyze-live-video-use-your-model-http.md)을 사용해 봅니다. 
- [Azure Video Analyzer 용어](../../azure-video-analyzer/video-analyzer-docs/terminology.md)를 검토합니다. 
- [Azure Video Analyzer 파이프라인](../../azure-video-analyzer/video-analyzer-docs/pipeline.md)을 검토합니다. 
