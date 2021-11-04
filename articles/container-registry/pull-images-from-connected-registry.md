---
title: 연결된 레지스트리에서 이미지 끌어오기
description: Azure Container Registry CLI 명령을 사용해 클라이언트 토큰을 구성하고, IoT Edge 디바이스의 연결된 레지스트리에서 이미지를 끌어올 수 있습니다.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5409afea3ab1ab1ebedaed87adbafd2b15fe9418
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029451"
---
# <a name="pull-images-from-a-connected-registry-on-iot-edge-device"></a>IoT Edge 디바이스의 연결된 레지스트리에서 이미지 끌어오기

[연결된 레지스트리](intro-connected-registry.md)에서 이미지를 끌어오려면 [클라이언트 토큰](overview-connected-registry-access.md#client-tokens)을 구성하고 토큰 자격 증명을 액세스 레지스트리 콘텐츠에 전달합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
* Azure에서 연결된 레지스트리 리소스입니다. 배포 단계는 [빠른 시작: Azure CLI를 사용하여 연결된 레지스트리 만들기][quickstart-connected-registry-cli]를 참조하세요.
* IoT Edge 디바이스에 배포된 연결된 레지스트리 인스턴스입니다. 배포 단계는 [빠른 시작: IoT Edge 디바이스에 연결된 레지스트리 배포](quickstart-deploy-connected-registry-iot-edge-cli.md) 또는 [자습서: 중첩된 IoT Edge 디바이스에 연결된 레지스트리 배포](tutorial-deploy-connected-registry-nested-iot-edge-cli.md)를 참조하세요. 이 문서의 명령에서는 연결된 레지스트리의 이름이 환경 변수 *$CONNECTED_REGISTRY_RW* 에 저장됩니다.

## <a name="create-a-scope-map"></a>범위 맵 만들기

[az acr scope-map create][az-acr-scope-map-create] 명령을 사용하여 `hello-world` 리포지토리에 대한 읽기 권한용 범위 맵을 만듭니다.

```azurecli
# Use the REGISTRY_NAME variable in the following Azure CLI commands to identify the registry
REGISTRY_NAME=<container-registry-name>

az acr scope-map create \
  --name hello-world-scopemap \
  --registry $REGISTRY_NAME \
  --repository hello-world content/read \
  --description "Scope map for the connected registry."
```

## <a name="create-a-client-token"></a>클라이언트 토큰 만들기

[az acr token create][az-acr-token-create] 명령을 사용하여 클라이언트 토큰을 만들고 새로 만든 범위 맵과 연결합니다.

```azurecli
az acr token create \
  --name myconnectedregistry-client-token \
  --registry $REGISTRY_NAME \
  --scope-map hello-world-scopemap
```

이 명령은 암호를 포함해 새로 만든 토큰에 대한 세부 정보를 반환합니다.

  > [!IMPORTANT]
  > 생성된 암호는 잘 적어 둬야 합니다. 이러한 암호는 일회성으로, 검색할 수 없습니다. [az acr token credential generate][az-acr-token-credential-generate] 명령을 사용하여 새 암호를 생성할 수 있습니다.

## <a name="update-the-connected-registry-with-the-client-token"></a>클라이언트 토큰으로 연결된 레지스트리 업데이트

[az acr connected-registry update][az-acr-connected-registry-update] 명령을 사용하여 새로 만든 클라이언트 토큰으로 연결된 레지스트리를 업데이트합니다. 

```azurecli
az acr connected-registry update \
  --name $CONNECTED_REGISTRY_RW \
  --registry $REGISTRY_NAME \
  --add-client-token myconnectedregistry-client-token
```

## <a name="pull-an-image-from-the-connected-registry"></a>연결된 레지스트리에서 이미지 끌어오기

IoT Edge 디바이스에 액세스할 수 있는 컴퓨터에서 다음 예제 명령으로 클라이언트 토큰 자격 증명을 사용해 연결된 레지스트리에 로그인합니다. 로그인 자격 증명 관리 모범 사례는 [Docker 로그인](https://docs.docker.com/engine/reference/commandline/login/) 명령 참조를 참조하세요.

> [!CAUTION]
> 연결된 레지스트리를 안전하지 않은 것으로 설정할 경우, Docker 디먼 구성에서 안전하지 않은 레지스트리를 업데이트해 IP 주소(또는 FQDN) 및 IoT Edge 디바이스의 연결된 레지스트리 포트를 포함시켜야 합니다. 이 구성은 테스트 목적으로만 사용해야 합니다. 자세한 내용은 [안전하지 않은 레지스트리 테스트](https://docs.docker.com/registry/insecure/)를 참조하세요.

```
docker login --username myconnectedregistry-client-token \
  --password <token_password> <IP_address_or_FQDN_of_connected_registry>:<port>
```

IoT Edge 시나리오의 경우, 디바이스에서 연결된 레지스트리에 도달하는 데 사용되는 포트를 포함시켜야 합니다. 예제:

```
docker login --username myconnectedregistry-client-token \
  --password xxxxxxxxxxx 192.0.2.13:8000
```

이후 다음 명령을 사용해 `hello-world` 이미지를 끌어옵니다.

```
docker pull <IP_address_or_FQDN_of_connected_registry>:<port>/hello-world
```

## <a name="next-steps"></a>다음 단계

* [리포지토리 범위 토큰](container-registry-repository-scoped-permissions.md)에 대해 자세히 알아보세요.
* [연결된 레지스트리 액세스](overview-connected-registry-access.md)에 대해 자세히 알아보세요.

<!-- LINKS - internal -->
[az-acr-scope-map-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential#az_acr_token_credential_generate
[az-acr-connected-registry-update]: /cli/azure/acr/connect-registry#az_acr_connected_registry_update] 
[container-registry-intro]: container-registry-intro.md
[quickstart-connected-registry-cli]: quickstart-connected-registry-cli.md
