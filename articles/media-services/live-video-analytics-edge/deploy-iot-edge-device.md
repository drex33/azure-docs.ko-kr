---
title: IoT Edge 디바이스에 Azure Live Video Analytics 배포
description: 이 문서에서는 IoT Edge 디바이스에 Azure Live Video Analytics를 배포하는 데 도움이 되는 단계를 나열합니다. 예를 들어 로컬 Linux 컴퓨터에 대한 액세스 권한이 있거나 이전에 Azure Media Services 계정을 만든 경우 이 작업을 수행할 수 있습니다.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: cf4dbd55314bae73d9082c8b45bfe728cb4c235a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225740"
---
# <a name="deploy-azure-live-video-analytics-on-an-iot-edge-device"></a>IoT Edge 디바이스에 Azure Live Video Analytics 배포

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

이 문서에서는 IoT Edge 디바이스에 Live Video Analytics를 배포하는 데 도움이 되는 단계를 나열합니다. 예를 들어 로컬 Linux 컴퓨터에 대한 액세스 권한이 있거나 이전에 Azure Media Services 계정을 만든 경우 이 작업을 수행할 수 있습니다.

> [!NOTE]
> IoT Edge 빌드 `1.0.4` 이상의 Live Video Analytics에서 ARM64 디바이스에 대한 지원이 제공됩니다.
> ARM64 디바이스에서 Azure IoT Edge 런타임을 실행하는 것에 대한 지원은 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [지원되는 Linux 운영 체제](../../iot-edge/support.md#operating-systems) 중 하나를 실행하는 x86-64 또는 ARM64 디바이스
* [소유자 권한](../../role-based-access-control/built-in-roles.md#owner)이 있는 Azure 구독
* [IoT Hub 만들기 및 설정](../../iot-hub/iot-hub-create-through-portal.md)
* [IoT Edge 디바이스 등록](../../iot-edge/how-to-provision-single-device-linux-symmetric.md#register-your-device)
* [Debian 기반 Linux 시스템에서 Azure IoT Edge 런타임 설치](../../iot-edge/how-to-provision-single-device-linux-symmetric.md)
* [Azure Media Services 계정 만들기](../latest/account-create-how-to.md)

    * 다음 지역 중 하나를 사용합니다(해당 지역: 미국 동부 2, 미국 동부, 미국 중부, 미국 북중부, 일본 동부, 미국 서부, 미국 서부 2, 미국 중서부, 캐나다 동부, 영국 남부, 프랑스 중부, 프랑스 남부, 스위스 북부, 스위스 서부, 일본 서부).
    * 범용 v2(GPv2) 스토리지 계정을 사용하는 것이 좋습니다.

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>Live Video Analytics 사용을 위한 Azure 리소스 구성

### <a name="create-custom-azure-resource-manager-role"></a>사용자 지정 Azure Resource Manager 역할 만들기

[사용자 지정 Azure Resource Manager 역할 만들기](create-custom-azure-resource-manager-role-how-to.md)를 참조하고 서비스 주체에 이를 할당하여 Live Video Analytics를 사용할 수 있도록 합니다.

### <a name="set-up-a-premium-streaming-endpoint"></a>프리미엄 스트리밍 엔드포인트 설정

Live Video Analytics를 사용하여 계속해서 클라우드에 동영상을 기록하고 나중에 재생하기 전에 [쿼리 API](playback-recordings-how-to.md#query-api)를 사용하려는 경우에는 [프리미엄 스트리밍 엔드포인트](../latest/stream-streaming-endpoint-concept.md#types)를 사용하도록 미디어 서비스를 업데이트하는 것이 좋습니다.  

선택적 단계입니다. 다음 Azure CLI 명령을 사용하여 이 작업을 수행할 수 있습니다.

```azurecli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

이 명령을 사용하여 스트리밍 엔드포인트를 시작할 수 있습니다. 

> [!IMPORTANT]
> 이때 구독에 요금이 청구되기 시작합니다.

```azurecli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

[미디어 서비스 API에 액세스](../latest/access-api-howto.md?tabs=portal) 문서의 단계를 따라 미디어 서비스 API에 액세스하는 데 필요한 자격 증명을 가져오고 포털 탭을 선택합니다.

## <a name="create-and-use-local-user-account-for-deployment"></a>배포를 위한 로컬 사용자 계정 만들기 및 사용
IoT Edge 모듈에서 Live Video Analytics를 실행하려면 최대한 적은 수의 권한을 가진 로컬 사용자 계정을 만듭니다. 예를 들어 Linux 컴퓨터에서 다음 명령을 실행합니다.

```
sudo groupadd -g 1010 localusergroup
sudo useradd --home-dir /home/edgeuser --uid 1010 --gid 1010 lvaedgeuser
```

## <a name="granting-permissions-to-device-storage"></a>디바이스 스토리지에 대한 사용 권한 부여

이제 로컬 사용자 계정을 만들었으므로 

* 애플리케이션 구성 데이터를 저장할 로컬 폴더가 필요합니다. 폴더를 만들고 다음 명령을 사용하여 localuser 계정에 해당 폴더에 대한 쓰기 권한을 부여합니다.

```
sudo mkdir -p /var/lib/azuremediaservices
sudo chown -R lvaedgeuser /var/lib/azuremediaservices
```

* 또한 [로컬 파일에 동영상을 기록](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)하는 폴더가 필요합니다. 다음 명령을 사용하여 동일한 항목에 대한 로컬 폴더를 만듭니다.

```
sudo mkdir -p /var/media
sudo chown -R lvaedgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>Live Video Analytics 모듈 배포

IoT Edge의 Live Video Analytics는 [모듈 쌍 구성 스키마](module-twin-configuration-schema.md)에 설명된 모듈 쌍 속성을 노출합니다. 

### <a name="deploy-using-the-azure-portal"></a>Azure Portal을 사용하여 배포

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
    
    * **IoT Edge 모듈 이름**: lvaEdge
    * **이미지 URI**: mcr.microsoft.com/media/live-video-analytics:2.0    
    
    ![모듈 설정 탭을 보여 주는 스크린샷](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > 이 절차에 설명된 대로 **모듈 설정**, **컨테이너 만들기 옵션** 및 **모듈 쌍 설정** 탭에서 값을 지정할 때까지 **추가** 를 선택하지 마세요.
    
    > [!WARNING]
    > 사용자가 모듈에 대한 호출을 수행할 때 Azure IoT Edge는 대/소문자를 구분합니다. 모듈 이름으로 사용할 정확한 문자열을 기록해 둡니다.

1. **환경 변수** 탭을 엽니다.
   
   ![환경 변수](./media/deploy-iot-edge-device/environment-variables.png)가 표시되는 입력 상자에 다음 값을 추가합니다. 

1. **컨테이너 만들기 옵션** 탭을 엽니다.

    ![컨테이너 만들기 옵션](./media/deploy-iot-edge-device/container-create-options.png)
 
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
               "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
               "/var/media:/var/media"
            ]
        }
    }
    ````
   
   JSON의 "Binds" 섹션에는 2개의 항목이 있습니다.
   1. "/var/lib/azuremediaservices:/var/lib/azuremediaservices": 컨테이너에서 영구 애플리케이션 구성 데이터를 바인딩하고, 이를 에지 디바이스에 저장하는 데 사용됩니다.
   1. "/var/media:/var/media": 에지 디바이스와 컨테이너 사이에 미디어 폴더를 바인딩합니다. 에지 디바이스에 동영상 클립을 저장할 수 있도록 지원하는 미디어 그래프 토폴로지를 실행할 때 동영상 레코딩을 저장하는 데 사용됩니다.
   
1. **모듈 쌍 설정** 탭에서 다음 JSON을 복사하여 입력란에 붙여넣습니다.
 
    ![쌍 설정](./media/deploy-iot-edge-device/twin-settings.png)

    IoT Edge의 Live Video Analytics를 실행하려면 [모듈 쌍 구성 스키마](module-twin-configuration-schema.md)에 나열된 필수 쌍 속성 집합이 필요합니다.  

    모듈 쌍 설정 편집 상자에 입력해야 하는 JSON은 다음과 같습니다.    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    위의 JSON에 대한 **필수** 속성은 다음과 같습니다.  
    * {subscriptionID} - Azure 구독 ID
    * {resourceGroupName} - Media Service 계정이 속한 리소스 그룹
    * {AMS-account-name} - Media Services 계정의 이름
    
    다른 값을 얻으려면 [Azure Media Services API 액세스](../latest/access-api-howto.md?tabs=portal)를 참조하고 포털 탭을 선택합니다.  
    * aadTenantId - 테넌트의 ID이며 위 링크의 "AadTenantId"와 동일합니다.
    * aadServicePrincipalAppId - Media Service 계정에 대한 서비스 주체의 앱 ID이며 위 링크의 "AadClientId"와 동일합니다.
    * aadServicePrincipalSecret - 서비스 주체의 비밀이며 위 링크의 "AadSecret"와 동일합니다.

    다음은 JSON에 추가할 수 있으며 모듈을 모니터링하는 데 도움이 되는 몇 가지 추가 **권장** 속성입니다. 자세한 내용은 [모니터링 및 로깅](monitoring-logging.md)을 참조하세요.
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    다음은 JSON에 추가할 수 있는 몇 가지 **선택적** 속성입니다.
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```

   > [!Note]
   > 쌍 속성 **allowUnsecuredEndpoints** 는 자습서 및 빠른 시작의 목적에 맞게 true로 설정됩니다.   
   프로덕션 환경에서 실행하는 경우 이 속성을 **false** 로 설정해야 합니다. 이렇게 하면 애플리케이션이 보호되지 않은 모든 엔드포인트를 차단하며, 그래프 토폴로지를 실행하기 위해 유효한 연결 자격 증명이 필요합니다.  
   
    추가를 선택하여 모듈 쌍 속성을 추가합니다.
1. **다음: 경로** 를 선택하여 경로 섹션으로 이동합니다.
    경로를 지정합니다.

기본 경로를 유지하고 **다음: 검토 + 만들기** 를 선택하여 검토 섹션으로 이동합니다.

#### <a name="review-deployment"></a>배포 검토

검토 섹션에서는 이전 두 개의 섹션에서 선택한 항목에 따라 생성된 JSON 배포 매니페스트를 보여줍니다. 추가하지 않고 선언된 $edgeAgent 및 $edgeHub라는 두 개의 모듈도 있습니다. 이 두 개의 모듈은 IoT Edge 런타임을 구성하며 모든 배포에서 필수 기본값입니다.

배포 정보를 검토한 다음, 만들기를 선택합니다.

### <a name="verify-your-deployment"></a>배포 확인

배포를 만든 후 IoT 허브의 IoT Edge 페이지로 돌아갑니다.

1. 해당 세부 정보를 열려면 배포할 대상으로 지정한 IoT Edge 디바이스를 선택합니다.
2. 디바이스 세부 정보에서 Blob Storage 모듈이 **배포에 지정됨 및 디바이스에서 보고됨** 모두로 나열되어 있는지를 확인합니다.

모듈을 디바이스에서 시작한 다음, IoT Hub에 다시 보고하려면 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다.
상태 코드: 200 – OK는 [IoT Edge 런타임](../../iot-edge/iot-edge-runtime.md)이 정상이며 제대로 작동하고 있음을 의미합니다.

![IoT Edge 런타임의 상태 값을 보여 주는 스크린샷.](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>직접 메서드 호출

다음으로, 직접 메서드를 호출하여 샘플을 테스트해 보겠습니다. lvaEdge 모듈에서 제공하는 모든 직접 메서드를 이해하려면 [IoT Edge의 Live Video Analytics에 대한 직접 메서드](direct-methods.md)를 읽어 보세요.

1. 만든 에지 모듈을 클릭하면 해당 구성 페이지로 이동합니다.  

    ![에지 모듈의 구성 페이지를 보여 주는 스크린샷.](./media/deploy-iot-edge-device/modules.png)
1. 직접 메서드 메뉴 옵션을 클릭합니다.

    > [!NOTE] 
    > 현재 페이지에서 볼 수 있듯이 연결 문자열 섹션에 값을 추가해야 할 수도 있습니다. **설정 이름** 섹션에서 아무것도 숨기거나 변경할 필요가 없습니다. 공개하는 것도 괜찮습니다.

    ![직접 메서드](./media/deploy-iot-edge-device/module-details.png)
1. 다음으로, 메서드 이름 상자에 "GraphTopologyList"를 입력합니다.
1. 그런 다음, 페이로드 상자에 아래 JSON 페이로드를 복사하여 붙여넣습니다.
    
    ```
    {
        "@apiVersion" : "2.0"
    }
    ```
1. 페이지 상단의 "메서드 호출" 옵션을 클릭합니다.

    ![직접 메서드](./media/deploy-iot-edge-device/direct-method.png)
1. 결과 상자에 상태 200 메시지가 표시됩니다.

    ![상태 200 메시지](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>다음 단계

[빠른 시작: 시작 - IoT Edge의 Live Video Analytics](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device) 사용해 보기

> [!TIP]
> 위의 빠른 시작을 진행하는 경우, Visual Studio Code를 사용하여 직접 메서드를 호출할 때 `lva-sample-device` 대신 이 문서를 통해 IoT Hub에 추가된 디바이스를 사용합니다.
