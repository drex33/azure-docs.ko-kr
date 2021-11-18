---
title: '자습서: 중첩된 IoT Edge 디바이스에 연결된 레지스트리 배포'
description: Azure CLI 명령을 사용하여 연결된 Azure 컨테이너 레지스트리를 중첩된 Azure IoT Edge 계층 구조에 배포합니다.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 38f125d6f839e1dbe5ff2859eecebbeb04dbbb4d
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132755555"
---
# <a name="tutorial-deploy-a-connected-registry-to-a-nested-iot-edge-hierarchy"></a>자습서: 중첩된 IoT Edge 계층 구조에 연결된 레지스트리 배포

이 자습서에서는 Azure CLI 명령을 사용하여 Azure IoT Edge 디바이스의 2계층 구조를 만들고, [연결된 레지스트리](intro-connected-registry.md)를 모듈로 각 계층에 배포합니다.

IoT Edge에서 연결된 레지스트리를 사용하는 방법에 대한 개요는 [Azure IoT Edge에서 연결된 레지스트리 사용](overview-connected-registry-and-iot-edge.md)을 참조하세요. 이 시나리오에서 [상위 계층](overview-connected-registry-and-iot-edge.md#top-layer)의 디바이스는 클라우드 레지스트리와 통신하고, [하위 계층](overview-connected-registry-and-iot-edge.md#top-layer)의 디바이스는 상위 계층에서 연결된 레지스트리 부모와 통신합니다. 

[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]
* Azure IoT Hub입니다. 배포 단계는 [Azure Portal을 사용하여 IoT 허브 만들기](../iot-hub/iot-hub-create-through-portal.md)를 참조하세요.
* Azure에서 연결된 두 개의 레지스트리 리소스. 배포 단계는 [Azure CLI][quickstart-connected-registry-cli] 또는 [Azure Portal][quickstart-connected-registry-portal]을 사용한 빠른 시작을 참조하세요. 

    * 상위 계층의 경우 연결된 레지스트리는 ReadWrite 또는 ReadOnly 모드일 수 있습니다. 이 문서에서는 ReadWrite 모드를 가정하고, 연결된 레지스트리 이름이 *$CONNECTED_REGISTRY_RW* 환경 변수에 저장됩니다.
    * 하위 계층의 경우 연결된 레지스트리는 ReadOnly 모드여야 합니다. 이 문서에서는 연결된 레지스트리 이름이 *$CONNECTED_REGISTRY_RO* 환경 변수에 저장되어 있다고 가정합니다.

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="retrieve-connected-registry-configuration"></a>연결된 레지스트리 구성 검색 

연결된 각 레지스트리를 계층 구조의 IoT Edge 디바이스에 배포하려면 Azure의 연결된 레지스트리 리소스에서 구성 설정을 검색해야 합니다. 필요한 경우 연결된 각 레지스트리에 대해 [az acr connected-registry get-settings][az-acr-connected-registry-get-settings] 명령을 실행하여 구성을 검색합니다. 

기본적으로 설정 정보에는 연결된 레지스트리를 배포하는 데 필요한 [동기화 토큰](overview-connected-registry-access.md#sync-token) 암호가 포함되어 있지 않습니다. 필요에 따라 `--generate-password 1` 또는 `generate-password 2` 매개 변수를 전달하여 암호 중 하나를 생성합니다. 생성된 암호를 안전한 위치에 저장합니다. 암호는 다시 검색할 수 없습니다.

> [!WARNING]
> 암호를 다시 생성하면 동기화 토큰 자격 증명이 회전됩니다. 이전 암호를 사용하여 디바이스를 구성한 경우 해당 구성을 업데이트해야 합니다.

```azurecli
# Use the REGISTRY_NAME variable in the following Azure CLI commands to identify the registry
REGISTRY_NAME=<container-registry-name>

# Run the command for each registry resource in the hierarchy

az acr connected-registry get-settings \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --parent-protocol https

az acr connected-registry get-settings \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RO \
  --parent-protocol https
```

[!INCLUDE [container-registry-connected-connection-configuration](../../includes/container-registry-connected-connection-configuration.md)]

## <a name="configure-deployment-manifests-for-the-nested-iot-edge-devices"></a>중첩된 IoT Edge 디바이스에 대한 배포 매니페스트 구성

배포 매니페스트는 IoT Edge 디바이스에 배포할 모듈을 설명하는 JSON 문서입니다. 자세한 내용은 [IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법 이해](../iot-edge/module-composition.md)를 참조하세요.

Azure CLI를 사용하여 연결된 레지스트리 모듈을 각 IoT Edge 디바이스에 배포하려면 다음 배포 매니페스트를 JSON 파일로 로컬로 저장합니다. 이전 섹션의 정보를 사용하여 각 매니페스트에서 관련 JSON 값을 업데이트합니다. 명령을 실행하여 구성을 디바이스에 적용하는 경우 다음 섹션의 파일 경로를 사용합니다.

### <a name="deployment-manifest-for-the-top-layer"></a>상위 계층에 대한 배포 매니페스트

상위 계층의 디바이스에 대해 다음 콘텐츠를 사용하여 `deploymentTopLayer.json` 배포 매니페스트 파일을 만듭니다. 이 매니페스트는 [빠른 시작: IoT Edge 디바이스에 연결된 레지스트리 배포](quickstart-deploy-connected-registry-iot-edge-cli.md)에서 사용하는 것과 비슷합니다. 

> [!NOTE]
> [빠른 시작](quickstart-deploy-connected-registry-iot-edge-cli.md)을 사용하여 연결된 레지스트리를 상위 계층 IoT Edge 디바이스에 이미 배포한 경우 중첩된 계층 구조의 상위 계층에서 사용할 수 있습니다. 계층 구조(표시되지 않음)에서 구성하려면 이 자습서의 배포 단계를 수정해야 합니다.

[!INCLUDE [container-registry-connected-iot-edge-manifest](../../includes/container-registry-connected-iot-edge-manifest.md)]

### <a name="deployment-manifest-for-the-lower-layer"></a>하위 계층에 대한 배포 매니페스트

하위 계층의 디바이스에 대해 다음 콘텐츠를 사용하여 `deploymentLowerLayer.json` 배포 매니페스트 파일을 만듭니다.

전반적으로 하위 계층 배포 파일은 상위 계층 배포 파일과 비슷합니다. 차이점은 다음과 같습니다. 

- 클라우드 레지스트리 대신 상위 계층 연결된 레지스트리에서 필요한 이미지를 끌어옵니다. 
    
    상위 계층 연결된 레지스트리를 설정하는 경우 필요한 모든 이미지(`azureiotedge-agent`, `azureiotedge-hub`, `azureiotedge-api-proxy`, `acr/connected-registry`)를 로컬로 동기화해야 확인합니다. 하위 계층 IoT 디바이스는 상위 계층 연결된 레지스트리에서 이러한 이미지를 끌어와야 합니다.
- 하위 계층에 구성된 동기화 토큰을 사용하여 상위 계층 연결된 레지스트리를 인증합니다.
- 클라우드 레지스트리의 FQDN 대신 상위 계층 연결된 레지스트리의 IP 주소 또는 FQDN을 사용하여 부모 게이트웨이 엔드포인트를 구성합니다. 

> [!IMPORTANT]
> 다음 배포 매니페스트에서 `$upstream`은 연결된 부모 레지스트리를 호스트하는 디바이스의 IP 주소 또는 FQDN으로 사용됩니다. 그러나 `$upstream`은 환경 변수에서 지원되지 않습니다. 부모 게이트웨이 엔드포인트를 가져오려면 연결된 레지스트리에서 `ACR_PARENT_GATEWAY_ENDPOINT` 환경 변수를 읽어야 합니다. 연결된 레지스트리는 `$upstream`을 사용하는 대신 다른 환경 변수에서 IP 주소 또는 FQDN을 동적으로 확인하는 것을 지원합니다. 중첩된 IoT Edge의 하위 계층에는 부모 디바이스의 IP 주소 또는 FQDN과 동일한 `$IOTEDGE_PARENTHOSTNAME` 환경 변수가 있습니다. 부모 IP 주소 또는 FQDN이 하드 코딩되지 않도록 환경 변수를 연결 문자열의 `ParentGatewayEndpoint` 값으로 수동으로 바꿉니다. 다음 예제의 부모 디바이스는 8000 포트에서 nginx를 실행하므로 `$IOTEDGE_PARENTHOSTNAME:8000`을 전달합니다. 또한 `ParentEndpointProtocol`에서 적절한 프로토콜을 선택해야 합니다.

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                "modules": {
                    "connected-registry": {
                        "settings": {
                            "image": "$upstream:8000/acr/connected-registry:0.5.0",
                            "createOptions": "{\"HostConfig\":{\"Binds\":[\"/home/azureuser/connected-registry:/var/acr/data\"]}}"
                        },
                        "type": "docker",
                        "env": {
                            "ACR_REGISTRY_CONNECTION_STRING": {
                                "value": "ConnectedRegistryName=<REPLACE_WITH_CONNECTED_REGISTRY_NAME>;SyncTokenName=<REPLACE_WITH_SYNC_TOKEN_NAME>;SyncTokenPassword=<REPLACE_WITH_SYNC_TOKEN_PASSWORD>;ParentGatewayEndpoint=$IOTEDGE_PARENTHOSTNAME:8000;ParentEndpointProtocol=https"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "version": "1.0"
                    },
                    "IoTEdgeApiProxy": {
                        "settings": {
                            "image": "$upstream:8000/azureiotedge-api-proxy:1.1.2",
                            "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\": \"8000\"}]}}}"
                        },
                        "type": "docker",
                        "version": "1.0",
                        "env": {
                            "NGINX_DEFAULT_PORT": {
                                "value": "8000"
                            },
                            "CONNECTED_ACR_ROUTE_ADDRESS": {
                                "value": "connected-registry:8080"
                            },
                            "NGINX_CONFIG_ENV_VAR_LIST": {
                                    "value": "NGINX_DEFAULT_PORT,BLOB_UPLOAD_ROUTE_ADDRESS,CONNECTED_ACR_ROUTE_ADDRESS,IOTEDGE_PARENTHOSTNAME,DOCKER_REQUEST_ROUTE_ADDRESS"
                            },
                            "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                "value": "AzureBlobStorageonIoTEdge:11002"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "startupOrder": 3
                    }
                },
                "runtime": {
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "registryCredentials": {
                            "connectedregistry": {
                                "address": "$upstream:8000",
                                "password": "<REPLACE_WITH_SYNC_TOKEN_PASSWORD>",
                                "username": "<REPLACE_WITH_SYNC_TOKEN_NAME>"
                            }
                        }
                    },
                    "type": "docker"
                },
                "schemaVersion": "1.1",
                "systemModules": {
                    "edgeAgent": {
                        "settings": {
                            "image": "$upstream:8000/azureiotedge-agent:1.2.4",
                            "createOptions": ""
                        },
                        "type": "docker",
                        "env": {
                            "SendRuntimeQualityTelemetry": {
                                "value": "false"
                            }
                        }
                    },
                    "edgeHub": {
                        "settings": {
                            "image": "$upstream:8000/azureiotedge-hub:1.2.4",
                            "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                        },
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always"
                    }
                }
            }
        },
        "$edgeHub": {
            "properties.desired": {
                "routes": {
                    "route": "FROM /messages/* INTO $upstream"
                },
                "schemaVersion": "1.1",
                "storeAndForwardConfiguration": {
                    "timeToLiveSecs": 7200
                }
            }
        }
    }
}
```

## <a name="set-up-and-deploy-connected-registry-modules-on-nested-iot-edge-devices"></a>중첩된 IoT Edge 디바이스에서 연결된 레지스트리 모듈 설정 및 배포

다음 단계는 [자습서: IoT Edge 디바이스의 계층 구조 만들기](../iot-edge/tutorial-nested-iot-edge.md)에서 조정되었으며, 연결된 레지스트리 모듈을 IoT Edge 계층 구조에 배포하는 것과 관련이 있습니다. 개별 단계에 대한 자세한 내용은 해당 자습서를 참조하세요.

### <a name="create-top-layer-and-lower-layer-devices"></a>상위 계층 및 하위 계층 디바이스 만들기

기존 [ARM 템플릿](https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json)을 사용하여 상위 계층 및 하위 계층 VM을 만듭니다. 또한 템플릿은 IoT Edge 에이전트를 설치합니다. 사용자 고유의 디바이스에서 대신 배포하려면 [자습서: Linux용 Azure IoT Edge 설치 또는 제거](../iot-edge/how-to-install-iot-edge.md)를 참조하여 디바이스를 수동으로 설정하는 방법을 알아보세요.

> [!IMPORTANT]
> 나중에 상위 계층 디바이스에 배포된 모듈에 액세스하려면 8000, 443, 5671, 8883 인바운드 포트를 열어야 합니다. 구성 단계는 [Azure Portal을 사용하여 가상 머신에 대한 포트를 여는 방법](../virtual-machines/windows/nsg-quickstart-portal.md)을 참조하세요.

### <a name="create-and-configure-the-hierarchy"></a>계층 구조 만들기 및 구성

`iotedge-config` 도구를 사용하여 Azure CLI 또는 Azure Cloud Shell에서 다음 단계에 따라 계층 구조를 만들고 구성합니다.

1. 구성 도구를 다운로드합니다.
    ```bash
    mkdir nestedIotEdgeTutorial
    cd ~/nestedIotEdgeTutorial
    wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
    tar -xvf iotedge_config.tar
    ```

    이 단계에서는 `iotedge_config_cli_release` 폴더를 자습서 디렉터리에 만듭니다. 디바이스 계층 구조를 만드는 데 사용되는 템플릿 파일은 `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial`에 있는 `iotedge_config.yaml` 파일입니다. 동일한 디렉터리에는 상위 계층 및 하위 계층에 대한 두 개의 배포 매니페스트(`deploymentTopLayer.json` 및 `deploymentLowerLayer.json` 파일)가 있습니다. 

1. 사용자의 정보를 사용하여 `iotedge_config.yaml`을 편집합니다. 여기에는 `iothub_hostname`, `iot_name`, 상위 계층 및 하위 계층에 대한 배포 매니페스트 파일 이름 및 각 계층의 업스트림에서 이미지를 끌어오기 위해 만든 클라이언트 토큰 자격 증명이 포함됩니다. 샘플 구성은 다음과 같습니다.

    ```yaml
    config_version: "1.0"

    iothub:
        iothub_hostname: <REPLACE_WITH_HUB_NAME>.azure-devices.net
        iothub_name: <REPLACE_WITH_HUB_NAME>
        ## Authentication method used by IoT Edge devices: symmetric_key or x509_certificate
        authentication_method: symmetric_key 

        ## Root certificate used to generate device CA certificates. Optional. If not provided a self-signed CA will be generated
        # certificates:
        #   root_ca_cert_path: ""
        #   root_ca_cert_key_path: ""

        ## IoT Edge configuration template to use
    configuration:
        template_config_path: "./templates/tutorial/device_config.toml"
        default_edge_agent: "$upstream:8000/azureiotedge-agent:1.2.4"

        ## Hierarchy of IoT Edge devices to create
    edgedevices:
        device_id: top-layer
        edge_agent: "<REPLACE_WITH_REGISTRY_NAME>.azurecr.io/azureiotedge-agent:1.2.4" ## Optional. If not provided, default_edge_agent will be used
        deployment: "./templates/tutorial/deploymentTopLayer.json" ## Optional. If provided, the given deployment file will be applied to the newly created device
            # hostname: "FQDN or IP" ## Optional. If provided, install.sh will not prompt user for this value nor the parent_hostname value
        container_auth: ## The token used to pull the image from cloud registry
            serveraddress: "<REPLACE_WITH_REGISTRY_NAME>.azurecr.io"
            username: "<REPLACE_WITH_SYNC_TOKEN_NAME_FOR_TOP_LAYER>"
            password: "<REPLACE_WITH_SYNC_TOKEN_PASSWORD_FOR_TOP_LAYER>"
        child:
            - device_id: lower-layer
              deployment: "./templates/tutorial/deploymentLowerLayer.json" ## Optional. If provided, the given deployment file will be applied to the newly created device
               # hostname: "FQDN or IP" ## Optional. If provided, install.sh will not prompt user for this value nor the parent_hostname value
              container_auth: ## The token used to pull the image from parent connected registry
                serveraddress: "$upstream:8000"
                username: "<REPLACE_WITH_SYNC_TOKEN_NAME_FOR_LOWER_LAYER>"
                password: "<REPLACE_WITH_SYNC_TOKEN_PASSWORD_FOR_LOWER_LAYER>"
    ```


1. 상위 계층 및 하위 계층 배포 파일(deploymentTopLayer.json 및 deploymentLowerLayer.json)을 준비합니다. 이 문서의 앞부분에서 만든 [배포 매니페스트 파일](#configure-deployment-manifests-for-the-nested-iot-edge-devices)을 `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` 폴더에 복사합니다.

1. `iotedge_config_cli_release` 디렉터리로 이동하고, 도구를 실행하여 IoT Edge 디바이스의 계층 구조를 만듭니다.

    ```bash
    cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
    ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
    ```

    `--output` 플래그를 사용하면 이 도구가 선택된 디렉터리에 디바이스 인증서, 인증서 번들 및 로그 파일을 만듭니다. `-f` 플래그를 설정하면 이 도구가 IoT Hub에서 기존 IoT Edge 디바이스를 자동으로 찾아서 제거하여 오류를 방지하고 허브를 깔끔하게 유지합니다.

    이 도구는 몇 분 동안 실행될 수 있습니다.

1. `scp`를 사용하여 이전 단계에서 생성된 `top-layer.zip` 및 `lower-layer.zip` 파일을 해당하는 상위 계층 및 하위 계층 VM에 복사합니다.

    ```bash
    scp <PATH_TO_CONFIGURATION_BUNDLE>   <USER>@<VM_IP_OR_FQDN>:~
    ```

1. 상위 계층 디바이스에 연결하여 구성 번들을 설치합니다.
    1. 구성 번들의 압축을 풉니다. 먼저 zip을 설치해야 합니다.

        ```bash
        sudo apt install zip
        unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip #unzip top-layer.zip
        ```
    1. `sudo ./install.sh`를 실행하고, IP 주소 또는 호스트 이름을 입력합니다. IP 주소를 사용하는 것이 좋습니다.
    1. `sudo iotedge list`를 실행하여 모든 모듈이 실행 중인지 확인합니다.

1. 하위 계층 디바이스에 연결하여 구성 번들을 설치합니다.
    1. 구성 번들의 압축을 풉니다. 먼저 zip을 설치해야 합니다.

        ```bash
        sudo apt install zip
        unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip #unzip lower-layer.zip
        ```
    1. `sudo ./install.sh`를 실행하고, 디바이스와 부모 IP 주소 또는 호스트 이름을 입력합니다. IP 주소를 사용하는 것이 좋습니다.
    1. `sudo iotedge list`를 실행하여 모든 모듈이 실행 중인지 확인합니다.
 
    
디바이스 구성에 대한 배포 파일을 지정하지 않았거나 상위 계층 또는 하위 계층 디바이스에서 잘못된 배포 매니페스트와 같은 배포 문제가 발생하는 경우 모듈을 수동으로 배포합니다. 다음 섹션을 참조하세요.

## <a name="manually-deploy-the-connected-registry-module"></a>연결된 레지스트리 모듈을 수동으로 배포

다음 명령을 사용하여 연결된 레지스트리 모듈을 IoT Edge 디바이스에 수동으로 배포합니다.

```azurecli
az iot edge set-modules \
  --device-id <device-id> \
  --hub-name <hub-name> \
  --content <deployment-manifest-filename>
```

자세한 내용은 [Azure CLI를 사용하여 Azure IoT Edge 모듈 배포](../iot-edge/how-to-deploy-modules-cli.md)를 참조하세요.

성공적으로 배포되면 연결된 레지스트리에서 `Online` 상태를 표시합니다.

연결된 레지스트리의 상태를 확인하려면 다음과 같은 [az acr connected-registry show][az-acr-connected-registry-show] 명령을 사용합니다.

```azurecli
az acr connected-registry show \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RO \
  --output table
```

연결된 레지스트리 배포가 완료될 때까지 몇 분 정도 기다려야 할 수 있습니다.

성공적으로 배포되면 연결된 레지스트리에서 `Online` 상태를 표시합니다.

배포 문제를 해결하려면 영향을 받는 디바이스에서 `iotedge check`를 실행합니다. 자세한 내용은 [문제 해결](../iot-edge/tutorial-nested-iot-edge.md#troubleshooting)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 연결된 레지스트리를 중첩된 IoT Edge 디바이스에 배포하는 방법을 알아보았습니다. 새로 배포된 연결된 레지스트리에서 이미지를 끌어오는 방법을 알아보려면 다음 가이드로 계속 진행하세요.

> [!div class="nextstepaction"]
> [연결된 레지스트리에서 이미지 끌어오기][pull-images-from-connected-registry]

<!-- LINKS - internal -->
[az-acr-connected-registry-get-settings]: /cli/azure/acr/connected-registry/install#az_acr_connected_registry_get_settings
[az-acr-connected-registry-show]: /cli/azure/acr/connected-registry#az_acr_connected_registry_show
[az-acr-import]: /cli/azure/acr#az-acr-import
[az-acr-token-credential-generate]: /cli/azure/acr/credential#az-acr-token-credential-generate
[container-registry-intro]: container-registry-intro.md
[pull-images-from-connected-registry]: pull-images-from-connected-registry.md
[quickstart-connected-registry-cli]: quickstart-connected-registry-cli.md
[quickstart-connected-registry-portal]: quickstart-connected-registry-portal.md
