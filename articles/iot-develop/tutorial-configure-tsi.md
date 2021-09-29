---
title: 자습서 - Azure Time Series Insights를 사용하여 Azure IoT 플러그 앤 플레이 디바이스 원격 분석 저장 및 분석
description: 자습서 - Time Series Insights 환경을 설정하고 IoT 허브를 연결하여 IoT 플러그 앤 플레이 디바이스에서 원격 분석을 살펴보고 분석합니다.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: 1258255ddc12dc4d718998e2320aa40951916400
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642741"
---
# <a name="tutorial-create-and-configure-a-time-series-insights-gen2-environment"></a>자습서: Time Series Insights Gen2 환경 만들기 및 구성

이 자습서에서는 IoT 플러그 앤 플레이 솔루션과 통합하기 위해 [Azure Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md) 환경을 만들고 구성하는 방법을 알아봅니다. Time Series Insights를 사용하여 IoT(사물 인터넷) 규모에서 시계열 데이터를 수집, 처리, 저장, 쿼리 및 시각화합니다.

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Time Series Insights 환경을 프로비저닝하고 IoT 허브를 스트리밍 이벤트 원본으로 연결합니다.
> * 모델 동기화를 통해 [시계열 모델](../time-series-insights/concepts-model-overview.md)을 작성합니다.
> * 온도 조절기 및 자동 온도 조절기 디바이스에 사용한 [DTDL(Digital Twins 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl) 샘플 모델 파일을 사용합니다.

> [!NOTE]
> Time Series Insights와 IoT 플러그 앤 플레이 간의 이러한 통합은 미리 보기에 있습니다. DTDL 디바이스 모델이 Time Series Insights 시계열 모델에 매핑되는 방식은 변경될 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

현재 다음과 같은 요구 사항을 충족해야 합니다.

* Azure IoT Hub -
* IoT 허브에 연결된 DPS(Device Provisioning Service) 인스턴스. DPS 인스턴스에는 IoT 플러그 앤 플레이 디바이스에 대한 개별 디바이스 등록이 있어야 합니다.
* 시뮬레이션된 데이터를 스트림하는 단일 구성 요소 디바이스 또는 다중 구성 요소 디바이스에서 IoT 허브에 연결

## <a name="prepare-your-event-source"></a>이벤트 원본 준비

이전에 만든 IoT 허브는 Time Series Insights 환경의 [이벤트 원본](../time-series-insights/concepts-streaming-ingestion-event-sources.md)이 됩니다.

> [!IMPORTANT]
> 기존 IoT Hub 경로를 사용하지 않도록 설정하세요. [라우팅](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints)이 구성된 IoT 허브를 사용하는 것과 관련된 알려진 문제가 있습니다. 라우팅 엔드포인트를 일시적으로 사용하지 않도록 설정하세요. IoT 허브가 Time Series Insights에 연결되면 라우팅 엔드포인트를 다시 사용하도록 설정할 수 있습니다.

IoT 허브에서 Time Series Insights에서 사용할 고유한 소비자 그룹을 만듭니다. 다음 예제에서는 `my-pnp-hub`를 이전에 사용한 IoT 허브 이름으로 바꿉니다.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-a-time-series-id"></a>시계열 ID 선택

Time Series Insights 환경을 프로비저닝하는 경우 *시계열 ID* 를 선택해야 합니다. 적절한 시계열 ID를 선택해야 합니다. 이 속성은 변경할 수 없으므로 설정된 후에는 변경할 수 없습니다. 시계열 ID는 데이터베이스 파티션 키와 비슷합니다. 시계열 ID는 시계열 모델의 기본 키 역할을 합니다. 자세한 내용은 [시계열 ID 선택 모범 사례](../time-series-insights/how-to-select-tsid.md)를 참조하세요.

IoT 플러그 앤 플레이 사용자는 시계열 ID에 대해 `iothub-connection-device-id` 및 `dt-subject`로 구성된 _복합 키_ 를 지정합니다. IoT 허브는 IoT 플러그 앤 플레이 디바이스 ID 및 디바이스 구성 요소 이름이 각각 포함된 이러한 시스템 속성을 추가합니다.

IoT 플러그 앤 플레이 디바이스 모델에서 현재 구성 요소를 사용하지 않더라도 나중에 해당 구성 요소를 사용할 수 있도록 `dt-subject`를 복합 키의 일부로 포함해야 합니다. 시계열 ID는 변경할 수 없으므로, 나중에 필요할 때를 대비하여 이 옵션을 사용하도록 설정하는 것이 좋습니다.

> [!NOTE]
> 이 문서의 예제는 다중 구성 요소 `TemperatureController` 디바이스에 대한 것입니다. 그러나 구성 요소가 없는 `Thermostat` 디바이스에 대한 개념도 동일합니다.

## <a name="provision-your-time-series-insights-environment"></a>Time Series Insights 환경 프로비저닝

이 섹션에서는 Azure Time Series Insights Gen2 환경을 프로비저닝하는 방법을 설명합니다.

뒤에 나오는 명령을 실행하여 다음을 수행합니다.

* 환경의 [콜드 저장소](../time-series-insights/concepts-storage.md#cold-store)에 대한 Azure 스토리지 계정을 만듭니다. 이 계정은 기록 데이터의 장기 보존 및 분석을 위해 설계되었습니다.
  * 코드에서 `mytsicoldstore`를 콜드 스토리지 계정의 고유한 이름으로 바꿉니다.
* Azure Time Series Insights Gen2 환경을 만듭니다. 보존 기간이 7일인 웜 스토리지가 포함된 환경이 만들어집니다. 무한 보존을 위해 콜드 스토리지 계정이 연결됩니다.
  * 코드에서 `my-tsi-env`를 Time Series Insights 환경에 대한 고유한 이름으로 바꿉니다.
  * 코드에서 `my-pnp-resourcegroup`을 설정 중에 사용한 리소스 그룹 이름으로 바꿉니다.
  * 시계열 ID 속성은 `iothub-connection-device-id, dt-subject`입니다.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az tsi environment gen2 create --name "my-tsi-env" --location eastus2 --resource-group $rg --sku name="L1" capacity=1 --time-series-id-properties name=iothub-connection-device-id type=String --time-series-id-properties name=dt-subject type=String --warm-store-configuration data-retention=P7D --storage-configuration account-name=$storage management-key=$key
```

IoT Hub 이벤트 원본을 연결합니다. `my-pnp-resourcegroup`, `my-pnp-hub` 및 `my-tsi-env`를 개발자가 선택한 값으로 바꿉니다. 다음 명령은 이전에 만든 Time Series Insights에 대한 소비자 그룹을 참조합니다.

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az tsi event-source iothub create --event-source-name iot-hub-event-source --environment-name $env --resource-group $rg --location eastus2 --consumer-group-name tsi-consumer-group --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id --iot-hub-name $iothub
```

[Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동한 다음, 새 Time Series Insights 환경을 선택합니다. 인스턴스 개요에 표시된 **Time Series Insights 탐색기 URL** 로 이동합니다.

![포털 개요 페이지의 스크린샷](./media/tutorial-configure-tsi/view-environment.png)

탐색기에 다음과 같은 세 개의 인스턴스가 표시됩니다.

* &lt;pnp 디바이스 ID&gt;, thermostat1
* &lt;pnp 디바이스 ID&gt;, thermostat2
* &lt;pnp 디바이스 ID&gt;, `null`

> [!NOTE]
> 세 번째 태그는 `TemperatureController` 자체의 원격 분석 데이터(예: 디바이스 메모리의 작업 세트)를 나타냅니다. 이는 최상위 속성이므로 구성 요소 이름의 값은 null입니다. 이 이름은 이후 단계에서 더 친숙하게 만들 수 있습니다.

![탐색기에서 세 개의 인스턴스를 보여 주는 스크린샷](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>모델 변환 구성

다음으로, DTDL 디바이스 모델을 Azure Time Series Insights의 자산 모델로 변환합니다. Time Series Insights에서 시계열 모델은 데이터 컨텍스트화를 위한 의미 체계 모델링 도구입니다. 모델에는 다음과 같은 세 가지 핵심 구성 요소가 있습니다.

* [시계열 모델 인스턴스](../time-series-insights/concepts-model-overview.md#time-series-model-instances)는 시계열 자체에 대한 가상 표현입니다. 인스턴스는 시계열 ID를 통해 고유하게 식별됩니다.
* [시계열 모델 계층 구조](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies)는 속성 이름 및 해당 관계를 지정하여 인스턴스를 구성합니다.
* [시계열 모델 형식](../time-series-insights/concepts-model-overview.md#time-series-model-types)은 계산을 수행하는 [변수](../time-series-insights/concepts-variables.md) 또는 수식을 정의하는 데 도움이 됩니다. 형식은 특정 인스턴스와 연결됩니다.

### <a name="define-your-types"></a>형식 정의

모델을 미리 정의하지 않고도 Azure Time Series Insights Gen2로 데이터 수집을 시작할 수 있습니다. 원격 분석을 받으면 Time Series Insights에서 시계열 ID 속성 값에 따라 시계열 인스턴스를 자동으로 확인하려고 시도합니다. 모든 인스턴스에는 *기본 형식* 이 할당됩니다. 인스턴스를 올바르게 범주화하려면 새 형식을 수동으로 만들어야 합니다.

다음 세부 정보는 디바이스 DTDL 모델을 시계열 모델 형식과 동기화하는 가장 간단한 방법에 대해 간략히 설명합니다.

* 디지털 트윈 모델 식별자는 형식 ID가 됩니다.
* 형식 이름은 모델 이름 또는 표시 이름입니다.
* 모델 설명은 형식에 대한 설명이 됩니다.
* 숫자 스키마가 있는 각 원격 분석에 대해 하나 이상의 형식 변수가 만들어집니다.
  * 변수에는 숫자 데이터 형식만 사용할 수 있지만, 값이 변환 가능한 또 다른 형식(예: `"0"`)으로 전송되면 `toDouble` 같은 [변환](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) 함수를 사용할 수 있습니다.
* 변수 이름은 원격 분석 이름 또는 표시 이름입니다.
* 시계열 식 변수를 정의하는 경우 연결의 원격 분석 이름 및 해당 데이터 형식을 참조합니다.

| DTDL JSON | 시계열 모델 형식 JSON | 예제 값 |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |
|`contents`(배열)| `variables`(개체)  | 다음 예제를 참조하세요.

![시계열 모델 형식에 대한 DTDL을 보여 주는 스크린샷](./media/tutorial-configure-tsi/dtdl-to-tsm-type-update.png)

> [!NOTE]
> 이 예제에서는 세 개의 변수를 보여 주지만, 각 형식에는 최대 100개의 변수가 있을 수 있습니다. 다른 변수는 동일한 원격 분석 값을 참조하여 필요에 따라 다른 계산을 수행할 수 있습니다. 필터링, 집계 및 스칼라 함수의 전체 목록은 [Time Series Insights Gen2 시계열 식 구문](/rest/api/time-series-insights/reference-time-series-expression-syntax)을 참조하세요.

텍스트 편집기를 열고, 다음 JSON을 로컬 드라이브에 저장합니다.

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

Time Series Insights 탐색기에서 왼쪽에 있는 모델 아이콘을 선택하여 **모델** 탭을 엽니다. **형식** 을 선택한 다음, **JSON 업로드** 를 선택합니다.

![JSON을 업로드하는 방법을 보여 주는 스크린샷](./media/tutorial-configure-tsi/upload-type.png)

**파일 선택** 을 선택하고, 이전에 저장한 JSON을 선택한 다음, **업로드** 를 선택합니다.

새로 정의된 **온도 컨트롤러** 형식이 표시됩니다.

### <a name="create-a-hierarchy"></a>계층 만들기

계층 구조를 만들어 `TemperatureController` 부모 아래에 태그를 구성합니다. 다음 예제에서는 간단하게 단일 수준을 사용하지만, IoT 솔루션은 일반적으로 조직의 실제 및 의미 체계 위치 내에서 태그를 컨텍스트화하는 여러 중첩 수준을 사용합니다.

**계층 구조** 를 선택한 다음, **계층 구조 추가** 를 선택합니다. 이름에 대해 *디바이스 집합* 을 입력합니다. *디바이스 이름* 이라는 하나의 수준을 만듭니다. 그런 다음 **저장** 을 선택합니다.

![계층 구조를 추가하는 방법을 보여 주는 스크린샷](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>올바른 형식에 인스턴스 할당

다음으로 인스턴스 형식을 변경하고 계층 구조에 배치합니다.

**인스턴스** 탭을 선택합니다. 디바이스의 작업 집합을 나타내는 인스턴스를 찾은 다음, 오른쪽 끝에 있는 **편집** 아이콘을 선택합니다.

![인스턴스를 편집하는 방법을 보여 주는 스크린샷](./media/tutorial-configure-tsi/edit-instance.png)

**형식** 드롭다운 메뉴를 연 다음, **온도 조절기** 를 선택합니다. *defaultComponent \<your device name\>* 를 입력하여 디바이스와 연결된 모든 최상위 태그를 나타내는 인스턴스의 이름을 업데이트합니다.

![인스턴스 유형을 변경하는 방법을 보여 주는 스크린샷](./media/tutorial-configure-tsi/change-type.png)

**저장** 을 선택하기 전에 먼저 **인스턴스 필드** 탭을 선택한 다음, **디바이스 집합** 을 선택합니다. 원격 분석을 그룹화하려면 *\<your device name\> - 온도 조절기* 를 입력합니다. 그런 다음 **저장** 을 선택합니다.

![인스턴스를 계층 구조에 할당하는 방법을 보여 주는 스크린샷](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

이전 단계를 반복하여 자동 온도 조절기 태그에 올바른 형식과 계층 구조를 할당합니다.

## <a name="view-your-data"></a>데이터 보기

차트 창으로 돌아가서 **디바이스 집합 > 사용자 디바이스** 를 차례로 펼칩니다. **thermostat1** 을 선택하고 **Temperature** 변수를 선택한 다음, **추가** 를 선택하여 값을 차트로 표시합니다. **thermostat2** 및 **defaultComponent** **workingSet** 값에 대해서도 동일한 작업을 수행합니다.

![thermostat2에 대한 인스턴스 유형을 변경하는 방법을 보여 주는 스크린샷](./media/tutorial-configure-tsi/charting-values.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> 간격 크기 조정 및 y축 컨트롤을 포함한 다양한 차트 옵션에 대한 자세한 내용은 [Azure Time Series Insights 탐색기](../time-series-insights/concepts-ux-panels.md)를 참조하세요.
