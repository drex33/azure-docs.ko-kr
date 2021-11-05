---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 09/16/2021
ms.author: danlep
ms.openlocfilehash: a5bfee2b0348a68476855edc2b113338b779f20d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029701"
---
## <a name="import-images-to-your-cloud-registry"></a>클라우드 레지스트리로 이미지 가져오기

[az acr import](/cli/azure/acr#az_acr_import) 명령을 사용하여 클라우드 레지스트리로 다음 컨테이너 이미지를 가져옵니다. 이러한 이미지를 이미 가져온 경우에는 이 단계를 건너뜁니다.

### <a name="connected-registry-image"></a>연결된 레지스트리 이미지

중첩된 IoT Edge 시나리오를 지원하려면 연결된 레지스트리 런타임에 대한 컨테이너 이미지를 프라이빗 Azure 컨테이너 레지스트리에서 사용할 수 있어야 합니다. [az acr import](/cli/azure/acr#az_acr_import) 명령을 사용하여 연결된 레지스트리 이미지를 프라이빗 레지스트리로 가져옵니다. 

```azurecli
# Use the REGISTRY_NAME variable in the following Azure CLI commands to identify the registry
REGISTRY_NAME=<container-registry-name>

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/acr/connected-registry:0.5.0
```

### <a name="iot-edge-and-api-proxy-images"></a>IoT Edge 및 API 프록시 이미지

중첩된 IoT Edge에서 연결된 레지스트리를 지원하려면 IoT Edge 및 API 프록시용 모듈을 배포해야 합니다. 이러한 이미지를 프라이빗 레지스트리로 가져옵니다.

[IoT Edge API 프록시 모듈](../articles/iot-edge/how-to-configure-api-proxy-module.md)을 사용하면 IoT Edge 디바이스에서 443과 같은 동일한 포트에서 HTTPS 프로토콜을 사용하여 여러 서비스를 노출할 수 있습니다.

```azurecli
az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-agent:1.2.4

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-hub:1.2.4

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-api-proxy:1.1.2

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-diagnostics:1.2.4
```

### <a name="hello-world-image"></a>Hello-world 이미지

연결된 레지스트리를 테스트하려면 `hello-world` 이미지를 가져옵니다. 이 리포지토리는 연결된 레지스트리에 동기화되며 연결된 레지스트리 클라이언트에서 가져옵니다.

```azurecli
az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/hello-world:1.1.2
```