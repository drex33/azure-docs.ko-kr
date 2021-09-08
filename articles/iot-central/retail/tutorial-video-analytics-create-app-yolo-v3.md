---
title: 자습서 - Azure IoT Central(YOLO v3)에서 비디오 분석 - 개체 및 동작 감지 애플리케이션 만들기
description: 이 자습서에서는 IoT Central에서 비디오 분석 애플리케이션을 만드는 방법을 보여줍니다. 이 애플리케이션을 만들어서 사용자 지정하고, 다른 Azure 서비스에 연결합니다. 이 자습서에서는 YOLO v3 실시간 개체 검색 시스템을 사용합니다.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 09/01/2021
ms.openlocfilehash: 1517a2312751574e3d590c0fc27636c994e8c12c
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479925"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-yolo-v3"></a>자습서: Azure IoT Central(YOLO v3)에서 비디오 분석 - 개체 및 동작 감지 애플리케이션 만들기

IoT Central ‘비디오 분석 - 개체 및 동작 감지’ 애플리케이션 템플릿, Azure IoT Edge 디바이스, Azure Media Services, YOLO v3 실시간 개체 및 동작 감지 시스템을 사용하여 비디오 분석 애플리케이션을 만드는 방법을 알아봅니다. 이 솔루션에서는 소매점을 사용하여 보안 카메라 모니터링이라는 일반적인 비즈니스 요구 사항을 충족하는 방법을 보여줍니다. 이 솔루션에서는 비디오 피드의 자동 개체 감지를 사용하여 관심 있는 이벤트를 신속하게 식별하고 찾습니다.

> [!TIP]
> 동작 감지 개체에 대해 YOLO v3 대신 OpenVINO&trade;를 사용하려면 [자습서: Azure IoT Central(OpenVINO&trade;)에서 비디오 분석 - 개체 및 동작 감지 애플리케이션 만들기](tutorial-video-analytics-create-app-openvino.md)를 참조하세요.

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) - 이 파일은 이러한 자습서를 수행할 때 필요한 다양한 구성 옵션을 기록하는 데 도움이 됩니다.
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deploymentManifests/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json) - 두 번째 자습서에서 Intel NUC 디바이스를 사용하려는 경우에만 이 파일을 다운로드해야 합니다.

> [!NOTE]
> GitHub 리포지토리에는 **LvaEdgeGatewayModule** 및 **lvaYolov3** IoT Edge 모듈의 소스 코드도 포함되어 있습니다. 소스 코드를 사용하는 방법에 대한 자세한 내용은 [LVA 게이트웨이 모듈 빌드](tutorial-video-analytics-build-module.md)를 참조하세요.

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>배포 매니페스트 편집

배포 매니페스트를 사용하여 IoT Edge 모듈을 배포합니다. IoT Central에서 매니페스트를 디바이스 템플릿으로 가져온 다음, IoT Central에서 모듈 배포를 관리하게 할 수 있습니다.

배포 매니페스트를 준비하는 방법은 다음과 같습니다.

1. 텍스트 편집기를 사용하여 *lva-configuration* 폴더에 저장한 *deployment.amd64.json* 파일을 엽니다.

1. `LvaEdgeGatewayModule` 설정을 찾아 이미지 이름이 다음 코드 조각에 표시된 것과 같은지 확인합니다.

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. `LvaEdgeGatewayModule` 섹션의 `env` 노드에 Media Services 계정 이름을 추가합니다. *scratchpad.txt* 파일에 Media Services 계정 이름을 기록했습니다.

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_SERVICES_ACCOUNT_NAME>"
        }
    }
    ```

1. 이 템플릿은 IoT Central에서 이러한 속성을 공개하지 않으므로, 배포 매니페스트에 Media Services 구성 값을 추가해야 합니다. `lvaEdge` 모듈을 찾은 다음, 해당 자리 표시자를 Media Services 계정을 만들 때 *scratchpad.txt* 파일에 기록해 둔 값으로 바꿉니다.

    `azureMediaServicesArmId`는 앞에서 Media Services 계정을 만들 때 *scratchpad.txt* 파일에 기록해 둔 **리소스 ID** 입니다.

    다음 표에서는 배포 매니페스트에서 사용해야 하는 *scratchpad.txt* 파일에 있는 **Media Services API에 연결(JSON)** 의 값을 보여줍니다.

    | 배포 매니페스트       | Scratchpad  |
    | ------------------------- | ----------- |
    | aadTenantId               | AadTenantId |
    | aadServicePrincipalAppId  | AadClientId |
    | aadServicePrincipalSecret | AadSecret   |

    > [!CAUTION]
    > 이전 표를 사용하여 배포 매니페스트에 올바른 값을 추가했는지 확인합니다. 그렇지 않으면 디바이스가 작동하지 않습니다.

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. 변경 내용을 저장합니다.

이 자습서에서는 개체 및 동작 감지에 YOLO v3 모듈을 사용하도록 솔루션을 구성합니다. 다음 코드 조각은 모듈의 구성을 보여줍니다.

```json
"lvaYolov3": {
    "settings": {
        "image": "mcr.microsoft.com/lva-utilities/yolov3-onnx:1.0"
    },
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "version": "1.0"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>매니페스트 바꾸기

**LVA Edge 게이트웨이 v2** 페이지에서 **+ 매니페스트 바꾸기** 를 선택합니다.

:::image type="content" source="./media/tutorial-video-analytics-create-app-yolo-v3/replace-manifest.png" alt-text="매니페스트 바꾸기":::

*lva-configuration* 폴더로 이동한 다음, 앞에서 편집한 *deployment.amd64.json* 매니페스트 파일을 선택합니다. **업로드** 를 선택합니다. 유효성 검사가 완료되면 **바꾸기** 를 선택합니다.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션 사용을 마친 후에는 앞에서 만든 모든 리소스를 다음과 같이 제거할 수 있습니다.

1. IoT Central 애플리케이션의 **관리** 섹션에서 **사용자의 애플리케이션** 페이지로 이동합니다. 그런 다음, **삭제** 를 선택합니다.
1. Azure Portal에서 **lva-rg** 리소스 그룹을 삭제합니다.
1. 로컬 머신에서 **amp-viewer** Docker 컨테이너를 중지합니다.

## <a name="next-steps"></a>다음 단계

**비디오 분석 - 개체 및 동작 감지** 애플리케이션 템플릿을 사용하여 IoT Central 애플리케이션을 만들고, 게이트웨이 디바이스에 대한 디바이스 템플릿을 만들고, 게이트웨이 디바이스를 애플리케이션에 추가했습니다.

시뮬레이션된 비디오 스트림을 사용하는 클라우드 VM을 실행하는 IoT Edge 모듈에서 비디오 분석 - 개체 및 동작 감지 애플리케이션을 사용해 보려면 다음을 수행합니다.

> [!div class="nextstepaction"]
> [비디오 분석용 IoT Edge 인스턴스 만들기(Linux VM)](tutorial-video-analytics-iot-edge-vm.md)

실제 **ONVIF** 카메라를 사용하는 실제 디바이스를 실행하는 IoT Edge 모듈에서 비디오 분석 - 개체 및 동작 감지 애플리케이션을 사용해 보려면 다음을 수행합니다.

> [!div class="nextstepaction"]
> [비디오 분석용 IoT Edge 인스턴스 만들기(Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md)
