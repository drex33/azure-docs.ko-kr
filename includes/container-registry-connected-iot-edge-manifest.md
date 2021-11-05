---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 09/10/2021
ms.author: danlep
ms.openlocfilehash: 4153c44c60e8da560b5523e5263f0f7e0c81fcec
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053194"
---
### <a name="connected-registry-module-settings"></a>연결된 레지스트리 모듈 설정

* 이전 섹션의 토큰 자격 증명 및 연결 문자열을 사용하여 `env` 노드의 관련 JSON 값을 업데이트합니다. 
* 다음 환경 변수는 `env` 노드에서 선택 사항입니다.

    |변수  |설명  |
    |---------|---------|
    | `ACR_REGISTRY_LOGIN_SERVER` |  고유한 호스트 이름 또는 FQDN을 지정합니다. 사용되는 경우 연결된 레지스트리는 이 로그인 서버 값에 대한 요청만 수락합니다. <br/><br/>값이 제공되지 않으면 모든 로그인 서버 값을 사용하여 연결된 레지스트리를 액세스할 수 있습니다.  |
    |`ACR_REGISTRY_CERTIFICATE_VOLUME`     |   HTTPS를 통해 연결된 레지스트리를 액세스할 수 있는 경우 HTTPS 인증서가 저장된 볼륨을 가리킵니다.<br/><br/>설정되지 않은 경우 기본 위치는 `/var/acr/certs`입니다.      |
    |`ACR_REGISTRY_DATA_VOLUME`     |  연결된 레지스트리에 의해 이미지가 저장될 기본 위치 `/var/acr/data`를 덮어씁니다.<br/><br/>이 위치는 컨테이너의 볼륨 바인딩과 일치해야 합니다.       |

    > [!IMPORTANT]
    > 연결된 레지스트리가 80 및 443과 다른 포트에서 수신 대기하는 경우 `ACR_REGISTRY_LOGIN_SERVER` 값(지정된 경우)에 포트가 포함되어야 합니다. 예: `192.168.0.100:8080`.
* API 프록시 모듈을 사용하지 않는 경우 연결된 레지스트리에 대한 `HostPort` 바인딩을 설정해야 합니다. 예제:

    ```azurecli
     "createOptions": "{\"HostConfig\":{\"Binds\":[\"/home/azureuser/connected-registry:/var/acr/data\"],\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"8080\"}]}}}"
    ```

### <a name="api-proxy-module-settings"></a>API 프록시 모듈 설정

* API 프록시는 `NGINX_DEFAULT_PORT`로 구성된 8000 포트에서 수신 대기합니다. API 프록시 설정에 대한 자세한 내용은 [IoT Edge GitHub 리포지토리](https://github.com/Azure/iotedge/tree/master/edge-modules/api-proxy-module)를 참조하세요. 

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                "modules": {
                    "connected-registry": {
                        "settings": {
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/acr/connected-registry:0.5.0",
                            "createOptions": "{\"HostConfig\":{\"Binds\":[\"/home/azureuser/connected-registry:/var/acr/data\"]}}"
                        },
                        "type": "docker",
                        "env": {
                            "ACR_REGISTRY_CONNECTION_STRING": {
                                "value": "ConnectedRegistryName=<REPLACE_WITH_CONNECTED_REGISTRY_NAME>;SyncTokenName=<REPLACE_WITH_SYNC_TOKEN_NAME>;SyncTokenPassword=REPLACE_WITH_SYNC_TOKEN_PASSWORD;ParentGatewayEndpoint=<REPLACE_WITH_CLOUD_REGISTRY_NAME>.<REPLACE_WITH_CLOUD_REGISTRY_REGION>.data.azurecr.io;ParentEndpointProtocol=https"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "version": "1.0"
                    },
                    "IoTEdgeAPIProxy": {
                        "settings": {
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/azureiotedge-api-proxy:1.1.2",
                            "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8000/tcp\":[{\"HostPort\":\"8000\"}]}}}"
                        },
                        "type": "docker",
                        "env": {
                            "NGINX_DEFAULT_PORT": {
                                "value": "8000"
                            },
                            "CONNECTED_ACR_ROUTE_ADDRESS": {
                                "value": "connected-registry:8080"
                            },
                            "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                "value": "AzureBlobStorageonIoTEdge:11002"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "version": "1.0"
                    }
                },
                "runtime": {
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "registryCredentials": {
                            "cloudregistry": {
                                "address": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io",
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
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/azureiotedge-agent:1.2.4",
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
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/azureiotedge-hub:1.2.4",
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
