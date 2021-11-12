---
title: Azure CLI를 사용하여 대규모 모듈 배포 - Azure IoT Edge
description: Azure CLI에 대 한 IoT 확장을 사용 하 여 IoT Edge 장치 그룹에 대 한 자동 배포를 만듭니다.
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 504ae03ecff532fff5a8343d02fd8bba21524cfd
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397322"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-by-using-the-azure-cli"></a>Azure CLI를 사용 하 여 규모에 IoT Edge 모듈을 배포 하 고 모니터링 합니다.

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure 명령줄 인터페이스를 사용 하 여 여러 장치에 대 한 지속적인 배포를 한 번에 관리 하 여 [Azure IoT Edge 자동 배포](module-deployment-monitoring.md) 를 만듭니다. IoT Edge에 대 한 자동 배포는 Azure IoT 허브의 [자동 장치 관리](../iot-hub/iot-hub-automatic-device-management.md) 기능의 일부입니다. 배포는 여러 모듈을 여러 디바이스에 배포하고, 모듈의 상태를 추적하며, 필요한 경우 변경할 수 있게 하는 동적 프로세스입니다.

이 문서에서는 Azure CLI 및 IoT 확장을 설정 합니다. 그런 다음 IoT Edge 장치 집합에 모듈을 배포 하 고 사용 가능한 CLI 명령을 사용 하 여 진행률을 모니터링 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-using-cli.md)
* 하나 이상의 IoT Edge 디바이스.

  IoT Edge 디바이스를 설정하지 않은 경우 Azure 가상 머신에서 만들 수 있습니다. 다음 빠른 시작 문서 중 하나의 단계를 따르세요. [가상 Linux 장치 만들기](quickstart-linux.md) 또는 [가상 Windows 장치 만들기](quickstart.md)를 참조 하세요.

* 사용자 환경에 [Azure CLI](/cli/azure/install-azure-cli) 입니다. Azure CLI 버전은 2.0.70 이상 이어야 합니다. `az --version` 명령을 사용하여 유효성을 검사합니다. 이 버전은 az extension 명령을 지원하며 Knack 명령 프레임워크를 도입했습니다.
* [Azure CLI용 IoT 확장](https://github.com/Azure/azure-iot-cli-extension).

## <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. 자세한 내용은 [IoT Edge에서 모듈 배포 및 경로를 설정하는 방법 알아보기](module-composition.md)을 참조하세요.

Azure CLI를 사용 하 여 모듈을 배포 하려면 배포 매니페스트를 .txt 파일로 로컬로 저장 합니다. 명령을 실행하여 디바이스에 구성을 적용할 때 다음 섹션에서 파일 경로를 사용합니다.

예를 들어 한 개의 모듈이 있는 기본 배포 매니페스트의 예제는 다음과 같습니다.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.1",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

>[!NOTE]
>이 샘플 배포 매니페스트는 IoT Edge 에이전트 및 허브용 스키마 버전 1.1을 사용합니다. 스키마 버전 1.1은 IoT Edge 버전 1.0.10 함께 출시 되었습니다. 모듈 시작 순서 및 경로 우선 순위와 같은 기능을 사용할 수 있습니다.

## <a name="layered-deployment"></a>계층화된 배포

계층화된 배포는 차례대로 누적될 수 있는 자동 배포의 한 유형입니다. 계층화된 배포에 대한 자세한 내용은 [단일 디바이스 또는 대규모 IoT Edge 자동 배포에 대한 이해](module-deployment-monitoring.md)를 참조하세요.

계층화된 배포는 몇 가지 차이점이 있지만 자동 배포와 같이 Azure CLI를 사용하여 만들고 관리할 수 있습니다. 계층화 된 배포를 만든 후에는 배포와 동일 하 게 계층화 된 배포에 대해 Azure CLI 작동 합니다. 계층화된 배포를 만들려면 create 명령에 `--layered` 플래그를 추가합니다.

두 번째 차이점은 배포 매니페스트의 생성입니다. 표준 자동 배포에는 사용자 모듈 외에 시스템 런타임 모듈이 포함 되어야 하지만 계층화 된 배포에는 사용자 모듈만 포함할 수 있습니다. 계층화 된 배포에는 시스템 런타임 모듈과 같은 모든 IoT Edge 장치에 필요한 구성 요소를 제공 하는 장치에 대 한 표준 자동 배포도 필요 합니다.

예를 들어 1개의 모듈이 있는 기본 계층화된 배포 매니페스트의 예제는 다음과 같습니다.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```
>[!NOTE]
> 이 계층화 된 배포 매니페스트에는 표준 배포 매니페스트와 약간 다른 형식이 있습니다. 런타임 모듈의 desired 속성은 축소 되 고 점 표기법을 사용 합니다. 이 형식은 Azure Portal 계층화 된 배포를 인식 하는 데 필요 합니다. 예를 들면 다음과 같습니다.
>
> - `properties.desired.modules.<module_name>`
> - `properties.desired.routes.<route_name>`

이전 예제에서는 `properties.desired` 모듈에 대 한 계층화 된 배포 설정을 보여 주었습니다. 이 계층화된 배포가 동일한 모듈이 이미 적용된 디바이스를 대상으로 하는 경우 기존 desired 속성을 덮어씁니다. 원하는 속성을 덮어쓰지 않고 업데이트 하려면 새 하위 섹션을 정의할 수 있습니다. 예를 들면 다음과 같습니다.

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

다음을 사용 하 여 동일한를 표현할 수도 있습니다.

```json
"SimulatedTemperatureSensor": {
  "properties.desired.layeredProperties.SendData" : true,
  "properties.desired.layeredProperties.SendInterval": 5
}
```

>[!NOTE]
>현재 모든 계층화 된 배포에는 유효한 것으로 간주 되는 개체를 포함 해야 합니다 `edgeAgent` . 계층화 된 배포에서 모듈 속성만 업데이트 하는 경우에도 빈 개체를 포함 합니다. 예를 들어 `"$edgeAgent":{}`을 참조하십시오. 빈 개체가 있는 계층화 된 배포는 `edgeAgent` 모듈 쌍에서 **대상** 으로 표시 되 `edgeAgent` 고 **적용** 되지 않습니다.

요약 하자면, 계층화 된 배포를 만들려면 다음을 수행 합니다.

- `--layered`Azure CLI 만들기 명령에 플래그를 추가 합니다.
- 시스템 모듈을 포함 하지 않습니다.
- 및에서 전체 점 표기법 `$edgeAgent` 을 사용 `$edgeHub` 합니다.

계층화 된 배포에서 모듈 쌍을 구성 하는 방법에 대 한 자세한 내용은 [계층화 된 배포](module-deployment-monitoring.md#layered-deployment)를 참조 하세요.

## <a name="identify-devices-by-using-tags"></a>태그를 사용 하 여 장치 식별

배포를 만들려면 먼저 적용할 디바이스를 지정할 수 있어야 합니다. Azure IoT Edge는 장치 쌍의 *태그* 를 사용 하 여 장치를 식별 합니다. 

각 디바이스는 솔루션에 적합한 방식으로 정의하는 여러 개의 태그를 포함할 수 있습니다. 예를 들어 스마트 건물의 캠퍼스를 관리하는 경우 디바이스에 다음 태그를 추가할 수 있습니다.

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

디바이스 쌍 및 태그에 대한 자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-device-twins.md)을 참조하세요.

## <a name="create-a-deployment"></a>배포 만들기

배포 매니페스트 및 기타 매개 변수로 구성 된 배포를 만들어 대상 장치에 모듈을 배포 합니다.

[az iot edge deployment create](/cli/azure/iot/edge/deployment) 명령을 사용하여 배포를 만듭니다.

```azurecli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

`--layered` 플래그와 함께 동일한 명령을 사용하여 계층화된 배포를 만듭니다.

배포용 만들기 명령에는 다음 매개 변수가 사용 됩니다.

* **--계층화**. 배포를 계층화 된 배포로 식별 하는 선택적 플래그입니다.
* **--배포 id** 입니다. IoT hub에서 생성 될 배포의 이름입니다. 배포에 최대 128 소문자를 지정 하는 고유한 이름을 지정 합니다. 공백과 잘못된 문자(`& ^ [ ] { } \ | " < > /`)는 사용하지 않도록 합니다. 이 매개 변수는 필수입니다.
* **--콘텐츠**. 배포 매니페스트 JSON에 대 한 파일 경로입니다. 이 매개 변수는 필수입니다.
* **--hub-name**. 배포를 만들 IoT hub의 이름입니다. 허브가 현재 구독에 있어야 합니다. 명령을 사용 하 여 현재 구독을 변경 `az account set -s [subscription name]` 합니다.
* **--레이블**. 배포를 추적 하는 데 도움이 되는 이름/값 쌍입니다. 레이블은 이름 및 값에 대해 JSON 서식을 적용합니다. 예를 들어 `{"HostPlatform":"Linux", "Version:"3.0.1"}`을 참조하십시오.
* **--target 조건** 입니다. 이 배포의 대상으로 지정할 장치를 결정 하는 조건입니다. 조건은 장치 쌍 태그 또는 보고 된 장치 쌍 속성을 기반으로 하며 식 형식과 일치 해야 합니다. 예를 들어 `tags.environment='test' and properties.reported.devicemodel='4000x'`을 참조하십시오.
* **--priority** 양의 정수 둘 이상의 배포가 동일한 장치를 대상으로 하는 경우 우선 순위 값이 가장 높은 배포가 적용 됩니다.
* **--메트릭**. 보고 된 속성을 쿼리하여 배포 상태를 추적 하는 메트릭입니다 `edgeHub` . 메트릭은 JSON 입력 또는 파일 경로를 사용 합니다. 예를 들어 `'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`을 참조하십시오.

Azure CLI를 사용 하 여 배포를 모니터링 하려면 [IoT Edge 배포 모니터링](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli)을 참조 하세요.

## <a name="modify-a-deployment"></a>배포 수정

배포를 수정하면 변경 내용이 모든 대상 디바이스에 즉시 복제됩니다.

대상 조건을 업데이트하면 다음과 같은 업데이트가 발생합니다.

* 장치가 이전 대상 조건을 충족 하지 않았지만 새 대상 조건을 충족 하는 경우이 배포가 해당 장치에 대해 가장 높은 우선 순위 이면이 배포가 장치에 적용 됩니다.
* 현재이 배포를 실행 하는 장치가 대상 조건을 더 이상 충족 하지 않는 경우이 배포를 제거 하 고 우선 순위가 가장 높은 다음 배포를 수행 합니다.
* 이 배포를 현재 실행 중인 디바이스에서 더 이상 대상 조건을 충족하지 않고 다른 배포의 대상 조건도 충족하지 않으면 디바이스에서 변경되지 않습니다. 장치는 현재 상태에서 현재 모듈을 계속 실행 하지만이 배포의 일부로 더 이상 관리 되지 않습니다. 장치가 다른 배포의 대상 조건을 충족 하면이 배포를 제거 하 고 새 배포를 사용 합니다.

배포 매니페스트에 정의 된 모듈과 경로를 포함 하는 배포 내용은 업데이트할 수 없습니다. 배포 콘텐츠를 업데이트 하려면 우선 순위가 더 높은 동일한 장치를 대상으로 하는 새 배포를 만듭니다. 대상 조건, 레이블, 메트릭 및 우선 순위를 포함하여 기존 모듈의 특정 속성을 수정할 수 있습니다.

[az iot edge deployment update](/cli/azure/iot/edge/deployment) 명령을 사용하여 배포를 업데이트합니다.

```azurecli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

deployment update 명령은 다음 매개 변수를 사용합니다.

* **--배포 id** 입니다. IoT hub에 존재 하는 배포의 이름입니다.
* **--hub-name**. 배포가 있는 IoT hub의 이름입니다. 허브가 현재 구독에 있어야 합니다. 명령을 사용 하 여 원하는 구독으로 전환 `az account set -s [subscription name]` 합니다.
* **--설정** 합니다. 배포의 속성을 업데이트 합니다. 다음 속성을 업데이트할 수 있습니다.
  * `targetCondition`(예: `targetCondition=tags.location.state='Oregon'`)
  * `labels`
  * `priority`
* **--를 추가** 합니다. 대상 조건 또는 레이블을 포함 하 여 배포에 새 속성을 추가 합니다.
* **--를 제거** 합니다. 대상 조건 또는 레이블을 포함 하 여 기존 속성을 제거 합니다.

## <a name="delete-a-deployment"></a>배포 삭제

배포를 삭제 하는 경우 모든 장치는 우선 순위가 가장 높은 다음 배포를 수행 합니다. 장치가 다른 배포의 대상 조건을 충족 하지 않는 경우에는 배포를 삭제할 때 모듈이 제거 되지 않습니다.

[az iot edge deployment delete](/cli/azure/iot/edge/deployment) 명령을 사용하여 배포를 삭제합니다.

```azurecli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

`deployment delete` 명령은 다음 매개 변수를 사용합니다.

* **--배포 id** 입니다. IoT hub에 존재 하는 배포의 이름입니다.
* **--hub-name**. 배포가 있는 IoT hub의 이름입니다. 허브가 현재 구독에 있어야 합니다. 명령을 사용 하 여 원하는 구독으로 전환 `az account set -s [subscription name]` 합니다.

## <a name="next-steps"></a>다음 단계

[IoT Edge 장치에 모듈을 배포 하](module-deployment-monitoring.md)는 방법에 대해 자세히 알아보세요.
