---
title: API 프록시 모듈 구성 - Azure IoT Edge | Microsoft Docs
description: IoT Edge 게이트웨이 계층 구조에 대한 API 프록시 모듈을 사용자 지정하는 방법을 알아봅니다.
author: kgremban
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 100d67f30066b74fdcd6e70cfc714be7f7ee5ccd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537039"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>게이트웨이 계층 구조 시나리오에 대한 API 프록시 모듈 구성(미리 보기)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

이 문서에서는 게이트웨이 계층 구조 요구 사항을 지원하도록 모듈을 사용자 지정할 수 있는 API 프록시 모듈에 대한 구성 옵션을 안내합니다.

API 프록시 모듈은 모두 HTTPS 프로토콜을 지원하고 포트 443에 바인딩하는 여러 서비스가 배포될 때 IoT Edge 디바이스에 대한 통신을 단순화합니다. 이는 자식 디바이스의 클라이언트가 클라우드에 직접 연결할 수 없기 때문에 [네트워크 격리 다운스트리밍 디바이스](how-to-connect-downstream-iot-edge-device.md#network-isolate-downstream-devices)에 설명된 것과 같은 ISA-95 기반 네트워크 격리 아키텍처에서 IoT Edge 디바이스의 계층적 배포와 특히 관련이 있습니다.

예를 들어 자식 IoT Edge 디바이스가 Docker 이미지를 가져오도록 허용하려면 Docker 레지스트리 모듈을 배포해야 합니다. Blob 업로드를 허용하려면 동일한 IoT Edge 디바이스에 Azure Blob Storage 모듈을 배포해야 합니다. 두 서비스 모두 통신에 HTTPS를 사용합니다. API 프록시는 IoT Edge 디바이스에서 이러한 배포를 가능하게 합니다. API 프록시 모듈은 각 서비스 대신 호스트 디바이스의 포트 443에 바인딩하고 사용자 구성 규칙에 따라 해당 디바이스에서 실행 중인 올바른 서비스 모듈로 요청을 라우팅합니다. 개별 서비스는 여전히 클라이언트 인증 및 권한 부여를 포함하여 요청을 처리할 책임이 있습니다.

API 프록시가 없으면 각 서비스 모듈은 호스트 디바이스의 별도 포트에 바인딩해야 하므로 부모 IoT Edge 디바이스에 연결하는 각 자식 디바이스에서 수행하기 힘들고 오류가 발생하기 쉬운 구성 변경이 필요합니다.

## <a name="deploy-the-proxy-module"></a>프록시 모듈 배포

API 프록시 모듈은 MCR(Microsoft Container Registry)에서 사용할 수 있습니다(`mcr.microsoft.com/azureiotedge-api-proxy:1.0`).

Azure Marketplace: [IoT Edge API 프록시](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview)에서 직접 API 프록시 모듈을 배포할 수도 있습니다.

## <a name="understand-the-proxy-module"></a>프록시 모듈 이해

API 프록시 모듈은 nginx 역방향 프록시를 활용하여 네트워크 계층을 통해 데이터의 경로를 지정합니다. 프록시는 모듈에 포함됩니다. 즉, 모듈 이미지가 프록시 구성을 지원해야 합니다. 예를 들어 프록시가 특정 포트에서 수신 대기하는 경우 모듈에서 해당 포트가 열려 있어야 합니다.

프록시는 모듈에 포함된 기본 구성 파일로 시작합니다. [모듈 쌍](../iot-hub/iot-hub-devguide-module-twins.md)을 사용하여 클라우드에서 모듈로 새 구성을 전달할 수 있습니다. 또한 환경 변수를 사용하여 배포 시 구성 설정을 실행하거나 해제할 수 있습니다.

이 문서에서는 먼저 기본 구성 파일과, 환경 변수를 사용한 설정 사용 방법에 초점을 맞춥니다. 그런 다음, 마지막에 구성 파일을 사용자 지정하는 방법에 대해 설명합니다.

### <a name="default-configuration"></a>기본 구성

API 프록시 모듈에는 일반적인 시나리오를 지원하고 사용자 지정할 수 있는 기본 구성이 제공됩니다. 모듈의 환경 변수를 통해 기본 구성을 제어할 수 있습니다.

현재 기본 환경 변수는 다음과 같습니다.

| 환경 변수 | Description |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | 업데이트하려는 모든 변수를 쉼표로 구분된 목록으로 나열합니다. 이 단계는 실수로 잘못된 구성 설정을 수정하는 것을 방지합니다.
| `NGINX_DEFAULT_PORT` | nginx 프록시가 수신 대기하는 포트를 변경합니다. 이 환경 변수를 업데이트할 경우 선택한 포트가 dockerfile 모듈에도 노출되고 배포 매니페스트에서 포트 바인딩으로 선언되었는지 확인합니다.<br><br>기본값은 443입니다.<br><br>Azure Marketplace에서 배포할 경우 edgeHub 모듈과의 충돌을 방지하기 위해 기본 포트가 8000로 업데이트됩니다. 자세한 내용은 [열린 포트 최소화](#minimize-open-ports)를 참조하세요. |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | Docker 요청의 경로를 지정하는 주소입니다. 최상위 계층 디바이스에서 이 변수를 레지스트리 모듈을 가리키게 수정합니다.<br><br>기본값은 부모 호스트 이름입니다. |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | Blob 레지스트리 요청의 경로를 지정하는 주소입니다. 최상위 계층 디바이스에서 이 변수를 Blob 스토리지 모듈을 가리키게 수정합니다.<br><br>기본값은 부모 호스트 이름입니다. |

## <a name="minimize-open-ports"></a>열린 포트 최소화

열린 포트 수를 최소화하려면 API 프록시 모듈이 edgeHub 모듈을 대상으로 하는 트래픽을 포함하여 모든 HTTPS 트래픽(포트 443)을 릴레이해야 합니다. API 프록시 모듈은 기본적으로 포트 443에서 모든 edgeHub 트래픽의 경로를 다시 지정하도록 구성됩니다.

다음 단계를 사용하여 열린 포트를 최소화하는 배포를 구성합니다.

1. 포트 443에 바인딩하지 않도록 edgeHub 모듈 설정을 업데이트합니다. 그렇지 않으면 포트 바인딩 충돌이 발생합니다. 기본적으로 edgeHub 모듈은 포트 443, 5671 및 8883에 바인딩합니다. 포트 443 바인딩을 삭제하고 나머지 둘은 그대로 둡니다.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. 포트 443에서 바인딩할 API 프록시 모듈을 구성합니다.

   1. **NGINX_DEFAULT_PORT** 환경 변수의 값을 `443`으로 설정합니다.
   1. 포트 443에서 바인딩하도록 컨테이너 만들기 옵션을 업데이트합니다.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

열려 있는 포트를 최소화할 필요가 없다면 edgeHub 모듈이 포트 443을 사용하게 하고 API 프록시 모듈이 다른 포트에서 수신 대기하게 구성할 수 있습니다. 예를 들어 **NGINX_DEFAULT_PORT** 환경 변수의 값을 `8000`으로 설정하고 포트 8000에 대한 포트 바인딩을 만들면 API 프록시가 포트 8000에서 수신 대기할 수 있습니다.

## <a name="enable-container-image-download"></a>컨테이너 이미지 다운로드 사용

API 프록시 모듈의 일반적인 사용 사례는 컨테이너 이미지를 풀하는 데 더 낮은 계층의 IoT Edge 디바이스를 사용하도록 설정하는 것입니다. 이 시나리오에서는 [Docker 레지스트리 모듈](https://hub.docker.com/_/registry)을 사용하여 클라우드에서 컨테이너 이미지를 검색하고 최상위 계층에 캐시합니다. API 프록시는 최상위 계층의 레지스트리 모듈에서 서비스하도록 모든 HTTPS 요청을 릴레이하여 더 낮은 계층에서 컨테이너 이미지를 다운로드합니다.

이 시나리오에서는 IoT Edge 디바이스가 이미지의 컨테이너 레지스트리 대신, 도메인 이름 `$upstream`과 이어 API 프록시 모듈 포트 번호를 가리켜야 합니다. 예: `$upstream:8000/azureiotedge-api-proxy:1.0`

이 사용 사례는 [게이트웨이를 사용하여 IoT Edge 디바이스의 계층 구조 만들기](tutorial-nested-iot-edge.md) 자습서에서 설명합니다.

**최상위 계층** 에서 다음 모듈을 구성합니다.

* Docker 레지스트리 모듈
  * *레지스트리* 같이 기억하기 쉬운 이름으로 모듈을 구성하고, 요청을 수신하도록 모듈에서 포트를 노출합니다.
  * 모듈을 구성하여 컨테이너 레지스트리에 매핑합니다.
* API 프록시 모듈
  * 다음 환경 변수를 구성합니다.

    | Name | 값 |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | 레지스트리 모듈 이름 및 열린 포트입니다. 예들 들어 `registry:5000`입니다. |
    | `NGINX_DEFAULT_PORT` | nginx 프록시가 다운스트림 디바이스로부터의 요청을 수신 대기하는 포트입니다. 예들 들어 `8000`입니다. |

  * 다음 createOption을 구성합니다.

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

이 시나리오에서는 **더 낮은 계층** 에서 다음 모듈을 구성합니다.

* API 프록시 모듈
  * 다음 환경 변수를 구성합니다.

    | Name | 값 |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | nginx 프록시가 다운스트림 디바이스로부터의 요청을 수신 대기하는 포트입니다. 예들 들어 `8000`입니다. |

  * 다음 createOption을 구성합니다.

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>Blob 업로드 사용

API 프록시 모듈의 또 다른 사용 사례는 Blob 업로드에 더 낮은 계층의 IoT Edge 디바이스를 사용하도록 설정하는 것입니다. 이 사용 사례를 통해 모듈 로그 업로드 또는 지원 번들 업로드 등 하위 계층 디바이스에서의 문제 해결 기능을 사용할 수 있습니다.

이 시나리오에서는 최상위 계층의 [IoT Edge 모듈에서 Azure Blob Storage](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage)를 사용하여 Blob 생성 및 업로드를 처리합니다.

**최상위 계층** 에서 다음 모듈을 구성합니다.

* IoT Edge 모듈의 Azure Blob Storage
* API 프록시 모듈
  * 다음 환경 변수를 구성합니다.

    | Name | 값 |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | Blob 스토리지 모듈 이름 및 열린 포트 예들 들어 `azureblobstorageoniotedge:1102`입니다. |
    | `NGINX_DEFAULT_PORT` | nginx 프록시가 다운스트림 디바이스로부터의 요청을 수신 대기하는 포트입니다. 예들 들어 `8000`입니다. |

  * 다음 createOption을 구성합니다.

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

이 시나리오에서는 **더 낮은 계층** 에서 다음 모듈을 구성합니다.

* API 프록시 모듈
  * 다음 환경 변수를 구성합니다.

    | Name | 값 |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | nginx 프록시가 다운스트림 디바이스로부터의 요청을 수신 대기하는 포트입니다. 예들 들어 `8000`입니다. |

  * 다음 createOption을 구성합니다.

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

다음 단계를 사용하여 지원 번들이나 로그 파일을 최상위 계층에 있는 Blob 스토리지 모듈에 업로드합니다.

1. Azure Storage Explorer 또는 REST API를 사용하여 Blob 컨테이너를 만듭니다. 자세한 내용은 [IoT Edge에서 Azure Blob Storage를 사용하여 에지에 데이터 저장](how-to-store-data-blob.md)을 참조하세요.
1. [IoT Edge 배포에서 로그 검색](how-to-retrieve-iot-edge-logs.md)의 단계에 따라 로그 또는 지원 번들 업로드를 요청하지만, Blob 스토리지 모듈 주소 대신 도메인 이름 `$upstream`과 열린 프록시 포트를 사용합니다. 예를 들면 다음과 같습니다.

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>프록시 구성 편집

API 프록시 모듈에 기본 구성 파일이 포함되지만 모듈 쌍을 사용하여 클라우드를 통해 모듈에 새 구성을 전달할 수 있습니다.

고유의 구성을 작성하는 경우에도 환경을 사용하여 배포에 따라 설정을 조정할 수 있습니다. 다음 구문을 사용합니다.

* `${MY_ENVIRONMENT_VARIABLE}`을 사용하여 환경 변수 값을 검색합니다.
* 조건문을 사용하여 환경 변수 값에 따라 설정을 실행하거나 해제합니다.

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

API 프록시 모듈은 프록시 구성을 구문 분석할 때 먼저 대체를 사용하여 `PROXY_CONFIG_ENV_VAR_LIST`에 나열된 모든 환경 변수를 제공 된 값으로 바꿉니다. 그런 다음, `#if_tag` 및 `#endif_tag` 쌍 사이의 모든 항목을 바꿉니다. 그리고 구문 분석된 구성을 nginx 역방향 프록시에 제공합니다.

프록시 구성을 동적으로 업데이트하려면 다음 단계를 사용합니다.

1. 구성 파일을 작성합니다. [nginx_default_config.conf](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf) 기본 템플릿을 참조로 사용할 수 있습니다.
1. 구성 파일의 텍스트를 복사하여 Base64로 변환합니다.
1. 인코딩된 구성 파일을 `proxy_config` 모듈 쌍의 원하는 속성 값으로 붙여넣습니다.

   ![인코딩된 구성 파일을 proxy_config 속성 값으로 붙여넣기](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>다음 단계

API 프록시 모듈을 사용하여 [다운스트림 IoT Edge 디바이스를 Azure IoT Edge 게이트웨이에 연결](how-to-connect-downstream-iot-edge-device.md)합니다.
