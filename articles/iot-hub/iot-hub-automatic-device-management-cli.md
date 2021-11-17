---
title: Azure IoT Hub(CLI)를 사용하여 대규모로 자동 디바이스 관리 | Microsoft Docs
description: Azure IoT Hub 자동 구성을 사용하여 여러 IoT 디바이스 또는 모듈 관리
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/08/2021
ms.author: lizross
ms.openlocfilehash: f8cd268aa2a064397b8f87042817ad0f3d2abb46
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548903"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>Azure CLI를 사용한 자동 IoT 디바이스 및 모듈 관리

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT Hub에서 자동 디바이스 관리는 대규모 디바이스를 관리하는 반복적이고 복잡한 작업을 자동화합니다. 자동 디바이스 관리를 사용하여 해당 속성을 기반으로 디바이스 집합을 대상으로 지정하고, 원하는 구성을 정의하고, 범위에 나올 때 IoT Hub에서 디바이스를 업데이트하도록 할 수 있습니다. 이 업데이트는 완료 및 규정 준수를 요약하고, 병합 및 충돌을 처리하고, 단계별 방식으로 구성을 롤아웃할 수 있는 *자동 디바이스 구성* 또는 *자동 모듈 구성* 을 사용하여 수행됩니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

자동 디바이스 관리는 원하는 속성이 지정된 디바이스 쌍 세트 또는 모듈 쌍 세트를 업데이트하고 보고된 쌍 속성을 기준으로 요약을 보고하여 작동합니다.  새 클래스 및 다음과 같이 세 부분으로 이루어진 *구성* 이라고 하는 JSON 문서를 도입했습니다.

* **대상 조건** 은 업데이트할 디바이스 쌍 또는 모듈 쌍의 범위를 정의합니다. 대상 조건은 디바이스 쌍 태그 및/또는 보고된 속성에서 쿼리로 지정됩니다.

* **대상 콘텐츠** 는 대상으로 지정된 디바이스 쌍 또는 모듈 쌍에 추가하거나 업데이트할 원하는 속성을 정의합니다. 콘텐츠에는 변경할 원하는 속성의 섹션에 대한 경로가 포함됩니다.

* **메트릭** 은 **성공**, **진행 중** 및 **오류** 와 같은 다양한 구성 상태의 요약 횟수를 정의합니다. 사용자 지정 메트릭은 보고된 쌍 속성에서 쿼리로 지정됩니다. 시스템 메트릭은 대상으로 지정된 쌍의 수 및 성공적으로 업데이트된 쌍의 수와 같은 쌍 업데이트 상태를 측정하는 기본 메트릭입니다.

자동 구성은 구성이 생성된 후 즉시 실행되며 그 이후에는 5분 간격으로 실행됩니다. 메트릭 쿼리는 자동 구성이 실행될 때마다 실행됩니다.

## <a name="cli-prerequisites"></a>CLI 필수 조건

* Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-using-cli.md) 

* 사용자 환경의 [Azure CLI](/cli/azure/install-azure-cli). Azure CLI 버전이 2.0.70 이상이어야 합니다. `az –-version` 명령을 사용하여 유효성을 검사합니다. 이 버전은 az extension 명령을 지원하며 Knack 명령 프레임워크를 도입했습니다. 

* [Azure CLI용 IoT 확장](https://github.com/Azure/azure-cli).

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="implement-twins"></a>쌍 구현

자동 디바이스 구성은 클라우드와 디바이스 간의 상태를 동기화하는 디바이스 쌍을 사용해야 합니다.  자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](iot-hub-devguide-device-twins.md)을 참조하세요.

자동 모듈 구성은 클라우드와 모듈 간의 상태를 동기화하는 모듈 쌍을 사용해야 합니다. 자세한 내용은 [IoT Hub의 모듈 쌍 이해 및 사용](iot-hub-devguide-module-twins.md)을 참조하세요.

## <a name="use-tags-to-target-twins"></a>태그를 사용하여 쌍을 대상으로 지정

배포를 만들려면 먼저 적용할 디바이스 또는 모듈을 지정해야 합니다. Azure IoT Hub는 디바이스를 식별하고 디바이스 쌍의 태그를 사용하며 모듈 쌍의 태그를 사용하여 모듈을 식별합니다. 각 디바이스 또는 모듈은 여러 개의 태그를 갖출 수 있으며 솔루션에 적합한 방식으로 이러한 태그를 정의할 수 있습니다. 예를 들어 다른 위치에 있는 디바이스를 관리하는 경우 디바이스 쌍에 다음 태그를 추가합니다.

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>대상 콘텐츠 및 메트릭 정의

대상 콘텐츠 및 메트릭 쿼리는 설정할 원하는 디바이스 쌍 또는 모듈 쌍 속성과 측정할 보고된 속성을 설명하는 JSON 문서로 지정됩니다. Azure CLI를 사용하여 자동 구성을 만들려면 대상 콘텐츠와 메트릭을 로컬에 .txt 파일로 저장합니다. 이후 섹션에서 명령을 실행하여 디바이스에 구성을 적용할 때 파일 경로를 사용합니다.

자동 디바이스 구성에 대한 기본 대상 콘텐츠 샘플은 다음과 같습니다.

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

자동 모듈 구성은 매우 유사하게 작동하지만 `deviceContent` 대신 `moduleContent`를 대상으로 합니다.

```json
{
  "content": {
    "moduleContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

다음은 메트릭 쿼리 예입니다.

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

모듈에 대한 메트릭 쿼리는 디바이스에 대한 쿼리와도 유사하지만 `devices.modules`에서 `moduleId`를 선택합니다. 예를 들면 다음과 같습니다. 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>구성 만들기

대상 콘텐츠 및 메트릭으로 구성된 구성을 만들어 대상 디바이스를 구성합니다. 

다음 명령을 사용하여 구성을 만듭니다.

```azurecli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --**config-id** - IoT Hub에 만들 구성의 이름입니다. 구성에 최대 128자의 소문자로 된 고유한 이름을 지정합니다. 공백과 잘못된 문자(`& ^ [ ] { } \ | " < > /`)는 사용하지 않도록 합니다.

* --**labels** - 구성을 추적하는 데 도움이 되는 레이블을 추가합니다. 레이블은 배포를 설명하는 이름, 값 쌍입니다. 예를 들어 `HostPlatform, Linux` 또는 `Version, 3.0.1`와 같습니다.

* --**content** - 원하는 쌍 속성으로 설정할 대상 콘텐츠의 파일 경로 또는 인라인 JSON입니다. 

* --**hub-name** - 구성을 만들 IoT Hub의 이름입니다. 허브가 현재 구독에 있어야 합니다. `az account set -s [subscription name]` 명령을 사용하여 원하는 구독으로 전환합니다.

* --**target-condition** - 대상 조건을 입력하여 이 구성의 대상으로 지정할 디바이스 또는 모듈을 결정합니다. 자동 디바이스 구성의 경우 조건은 디바이스 쌍 태그 또는 원하는 디바이스 쌍 속성을 기반으로 하며, 식 형식과 일치해야 합니다. 예를 들어 `tags.environment='test'` 또는 `properties.desired.devicemodel='4000x'`입니다. 자동 모듈 구성의 경우 조건은 모듈 쌍 태그 또는 원하는 모듈 쌍 속성을 기반으로 합니다. 예를 들어 `from devices.modules where tags.environment='test'` 또는 `from devices.modules where properties.reported.chillerProperties.model='4000x'`입니다.

* --**priority** - 양의 정수입니다. 둘 이상의 구성이 동일한 디바이스 또한 모듈을 대상으로 하는 경우, Priority의 숫자 값이 가장 큰 구성이 적용됩니다.

* --**metrics** - 메트릭 쿼리의 파일 경로입니다. 메트릭은 디바이스 또는 모듈이 구성 콘텐츠를 적용한 후 다시 보고할 수 있는 다양한 상태의 요약 수를 제공합니다. 예를 들어 보류 중인 설정 변경에 대한 메트릭, 오류에 대한 메트릭 및 성공적인 설정 변경에 대한 메트릭을 만들 수 있습니다. 

## <a name="monitor-a-configuration"></a>구성 모니터링

다음 명령을 사용하여 구성 내용을 표시합니다.

```azurecli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --**config-id** - IoT Hub에 있는 구성의 이름입니다.

* --**hub-name** - 구성이 있는 IoT Hub의 이름입니다. 허브가 현재 구독에 있어야 합니다. `az account set -s [subscription name]` 명령을 사용하여 원하는 구독으로 전환합니다.

명령 창에서 구성을 검사합니다.  **metrics** 속성은 각 허브에서 평가되는 각 메트릭의 개수를 나열합니다.

* **targetedCount** - 대상 지정 조건과 일치하는 IoT Hub의 디바이스 쌍 또는 모듈 쌍의 수를 지정하는 시스템 메트릭입니다.

* **appliedCount** - 시스템 메트릭은 대상 콘텐츠가 적용된 디바이스 또는 모듈 수를 지정합니다.

* **사용자 지정 메트릭** - 정의한 메트릭이 사용자 메트릭입니다.

다음 명령을 사용하여 각 메트릭에 대한 디바이스 ID, 모듈 ID 또는 개체 목록을 표시할 수 있습니다.

```azurecli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --**config-id** - IoT Hub에 있는 배포의 이름입니다.

* --**metric-id** - 디바이스 ID 또는 모듈 ID 목록을 보려는 메트릭의 이름(예: `appliedCount`)입니다.

* --**hub-name** - 배포가 있는 IoT Hub의 이름입니다. 허브가 현재 구독에 있어야 합니다. `az account set -s [subscription name]` 명령을 사용하여 원하는 구독으로 전환합니다.

* --**metric-type** - 메트릭 유형은 `system` 또는 `user`일 수 있습니다.  시스템 메트릭은 `targetedCount` 및 `appliedCount`입니다. 다른 모든 메트릭은 사용자 메트릭입니다.

## <a name="modify-a-configuration"></a>구성 수정

구성을 수정하면 변경 내용이 즉시 모든 대상 디바이스에 복제됩니다. 

대상 조건을 업데이트하면 다음과 같은 업데이트가 발생합니다.

* 쌍에서 이전 대상 조건을 충족하지 않았지만 새 대상 조건은 충족하고 이 구성이 해당 쌍에 대해 가장 높은 순위이면 이 구성이 적용됩니다. 

* 이 구성에서 현재 실행 중인 쌍이 더 이상 대상 조건을 충족하지 않는 경우 구성에서 설정이 제거되고, 다음으로 가장 높은 우선 순위 구성에 의해 쌍이 수정됩니다. 

* 현재 이 구성에서 실행되는 쌍이 더 이상 대상 조건을 충족하지 않으며 다른 어떤 구성의 대상 조건도 충족하지 않는 경우에는 구성에서 설정이 제거되며 쌍에 다른 변경이 발생하지 않습니다. 

다음 명령을 사용하여 구성을 업데이트합니다.

```azurecli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --**config-id** - IoT Hub에 있는 구성의 이름입니다.

* --**hub-name** - 구성이 있는 IoT Hub의 이름입니다. 허브가 현재 구독에 있어야 합니다. `az account set -s [subscription name]` 명령을 사용하여 원하는 구독으로 전환합니다.

* --**set** - 구성에서 속성을 업데이트합니다. 다음 속성을 업데이트할 수 있습니다.

    * targetCondition - 예: `targetCondition=tags.location.state='Oregon'`

    * 레이블 

    * priority

## <a name="delete-a-configuration"></a>구성 삭제

구성을 삭제하면 모든 디바이스 쌍 또는 모듈 쌍은 다음으로 가장 높은 우선 순위 구성을 적용합니다. 쌍이 다른 구성의 대상 조건을 충족하지 않는 경우에는 다른 설정이 적용됩니다. 

다음 명령을 사용하여 구성을 삭제합니다.

```azurecli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```

* --**config-id** - IoT Hub에 있는 구성의 이름입니다.

* --**hub-name** - 구성이 있는 IoT Hub의 이름입니다. 허브가 현재 구독에 있어야 합니다. `az account set -s [subscription name]` 명령을 사용하여 원하는 구독으로 전환합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 IoT 디바이스를 크기 조정 시 구성 및 모니터링하는 방법에 대해 알아보았습니다. Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 알아보려면 다음 링크를 따라가세요.

* [대량으로 IoT Hub 디바이스 ID 관리](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub 모니터링](monitor-iot-hub.md)

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드](iot-hub-devguide.md)
* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포](../iot-edge/quickstart-linux.md)

IoT Hub Device Provisioning Service를 사용하여 무인 Just-In-Time 프로비저닝을 수행하는 방법을 알아보려면 다음을 참조하세요. 

* [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml)