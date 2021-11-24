---
title: 빠른 시작 - IoT Edge 디바이스에 연결된 레지스트리 배포
description: Azure CLI 명령 및 Azure Portal을 사용하여 연결된 Azure Container Registry를 Azure IoT Edge 디바이스에 배포합니다.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021, mode-other
ms.openlocfilehash: 0fee04cddfcde16ec702c8887dc5285ee0b6e983
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133062020"
---
# <a name="quickstart-deploy-a-connected-registry-to-an-iot-edge-device"></a>빠른 시작: IoT Edge 디바이스에 연결된 레지스트리 배포

이 빠른 시작에서는 Azure CLI를 사용하여 [연결된 레지스트리](intro-connected-registry.md)를 Azure IoT Edge 디바이스에 모듈로 배포합니다. IoT Edge 디바이스는 클라우드의 부모 Azure Container Registry에 액세스할 수 있습니다.

IoT Edge에서 연결된 레지스트리를 사용하는 방법에 대한 개요는 [Azure IoT Edge에서 연결된 레지스트리 사용](overview-connected-registry-and-iot-edge.md)을 참조하세요. 이 시나리오는 IoT Edge 계층 구조의 [최상위 계층](overview-connected-registry-and-iot-edge.md#top-layer)에 있는 디바이스에 해당합니다. 


[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]
* Azure IoT Hub 및 IoT Edge 디바이스 배포 단계는 [빠른 시작: 가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](../iot-edge/quickstart-linux.md)를 참조하세요.
  > [!IMPORTANT]
  > IoT Edge 디바이스에 배포된 모듈에 나중에 액세스하려면 디바이스에서 포트 8000, 5671 및 8883을 열어야 합니다. 구성 단계는 [Azure Portal을 사용하여 가상 머신에 대한 포트를 여는 방법](../virtual-machines/windows/nsg-quickstart-portal.md)을 참조하세요. 

* Azure에서 연결된 레지스트리 리소스입니다. 배포 단계는 [Azure CLI][quickstart-connected-registry-cli] 또는 [Azure Portal][quickstart-connected-registry-portal]을 사용한 빠른 시작을 참조하세요. 

    * 이 시나리오에서는 `ReadWrite` 또는 `ReadOnly` 모드의 연결된 레지스트리를 사용할 수 있습니다. 
    * 이 문서의 명령에서는 연결된 레지스트리의 이름이 환경 변수 *CONNECTED_REGISTRY_RW* 에 저장됩니다.

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="retrieve-connected-registry-configuration"></a>연결된 레지스트리 구성 검색

연결된 레지스트리를 IoT Edge 디바이스에 배포하기 전에 Azure의 연결된 레지스트리 리소스에서 구성 설정을 검색해야 합니다.

[az acr connected-registry get-settings][az-acr-connected-registry-get-settings] 명령을 사용하여 연결된 레지스트리를 설치하는 데 필요한 설정 정보를 얻습니다. 다음 예제에서는 HTTPS를 부모 프로토콜로 지정합니다. 부모 레지스트리가 클라우드 레지스트리인 경우 이 프로토콜이 필요합니다.

```azurecli
az acr connected-registry get-settings \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --parent-protocol https
```

기본적으로 설정 정보에는 연결된 레지스트리를 배포하는 데 필요한 [동기화 토큰](overview-connected-registry-access.md#sync-token) 암호가 포함되어 있지 않습니다. 필요에 따라 `--generate-password 1` 또는 `generate-password 2` 매개 변수를 전달하여 암호 중 하나를 생성합니다. 생성된 암호를 안전한 위치에 저장합니다. 암호는 다시 검색할 수 없습니다.

> [!WARNING]
> 암호를 다시 생성하면 동기화 토큰 자격 증명이 회전됩니다. 이전 암호를 사용하여 디바이스를 구성한 경우 해당 구성을 업데이트해야 합니다.

[!INCLUDE [container-registry-connected-connection-configuration](../../includes/container-registry-connected-connection-configuration.md)]

## <a name="configure-a-deployment-manifest-for-iot-edge"></a>IoT Edge에 대한 배포 매니페스트 구성

배포 매니페스트는 어떤 모듈을 IoT Edge 디바이스에 배포할지 설명하는 JSON 문서입니다. 자세한 내용은 [IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법 이해](../iot-edge/module-composition.md)를 참조하세요.

Azure CLI를 사용하여 연결된 레지스트리 및 API 프록시 모듈을 배포하려면 다음 배포 매니페스트를 로컬에 `manifest.json` 파일로 저장합니다. 명령을 실행하여 디바이스에 구성을 적용할 때 다음 섹션에서 파일 경로를 사용합니다.

[!INCLUDE [container-registry-connected-iot-edge-manifest](../../includes/container-registry-connected-iot-edge-manifest.md)]

## <a name="deploy-the-connected-registry-and-api-proxy-modules-on-iot-edge"></a>IoT Edge에 연결된 레지스트리 및 API 프록시 모듈 배포

다음 명령을 사용하여 이전 섹션에서 만든 배포 매니페스트를 사용하여 IoT Edge 디바이스에 연결된 레지스트리 및 API 프록시 모듈을 배포합니다. IoT Edge 최상위 계층 디바이스의 ID와 표시된 IoT Hub의 이름을 제공합니다.

```azurecli
# Set the IOT_EDGE_TOP_LAYER_DEVICE_ID and IOT_HUB_NAME environment variables for use in the following Azure CLI command
IOT_EDGE_TOP_LAYER_DEVICE_ID=<device-id>
IOT_HUB_NAME=<hub-name>

az iot edge set-modules \
  --device-id $IOT_EDGE_TOP_LAYER_DEVICE_ID \
  --hub-name $IOT_HUB_NAME \
  --content manifest.json
```

자세한 내용은 [Azure CLI를 사용하여 Azure IoT Edge 모듈 배포](../iot-edge/how-to-deploy-modules-cli.md)를 참조하세요.

연결된 레지스트리의 상태를 확인하려면 다음 [az acr connected-registry show][az-acr-connected-registry-show] 명령을 사용합니다. 연결된 레지스트리의 이름은 *$CONNECTED_REGISTRY_RW* 값입니다.

```azurecli
az acr connected-registry show \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --output table
```

성공적으로 배포되면 연결된 레지스트리에서 `Online` 상태를 표시합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 연결된 레지스트리를 IoT Edge 디바이스에 배포하는 방법을 배웠습니다. 다음 가이드를 계속 진행하여 새로 배포된 연결된 레지스트리에서 이미지를 가져오거나 중첩된 IoT Edge 디바이스에 연결된 레지스트리를 배포하는 방법을 알아봅니다.


> [!div class="nextstepaction"]
> [연결된 레지스트리에서 이미지 끌어오기][pull-images-from-connected-registry]

> [!div class="nextstepaction"]
> [자습서: 중첩된 IoT Edge 디바이스에 연결된 레지스트리 배포][tutorial-connected-registry-nested]

<!-- LINKS - internal -->
[az-acr-connected-registry-get-settings]: /cli/azure/acr/connected-registry/install#az_acr_connected_registry_get_settings
[az-acr-connected-registry-show]: /cli/azure/acr/connected-registry#az_acr_connected_registry_show
[az-acr-import]:/cli/azure/acr#az_acr_import
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential?#az_acr_token_credential_generate
[container-registry-intro]: container-registry-intro.md
[pull-images-from-connected-registry]: pull-images-from-connected-registry.md
[quickstart-connected-registry-cli]: quickstart-connected-registry-cli.md
[quickstart-connected-registry-portal]: quickstart-connected-registry-portal.md
[tutorial-connected-registry-nested]: tutorial-deploy-connected-registry-nested-iot-edge-cli.md
