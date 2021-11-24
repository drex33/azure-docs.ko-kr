---
title: 빠른 시작 - Azure IoT Central에서 데이터 내보내기
description: 빠른 시작 - IoT Central의 데이터 내보내기 기능을 사용하여 다른 클라우드 서비스와 통합하는 방법에 대해 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 11/10/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc, mode-other
ms.openlocfilehash: b8fe83e339e00c7e2a3fa554abf8c126241bda64
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133063750"
---
# <a name="quickstart-export-data-from-an-iot-central-application"></a>빠른 시작 - IoT Central 애플리케이션에서 데이터 내보내기

이 빠른 시작에서는 Azure IoT Central 애플리케이션의 데이터를 다른 클라우드 서비스로 지속적으로 내보내는 방법에 대해 알아봅니다. 빠르게 설정하기 위해 이 빠른 시작에서는 실시간 분석을 위한 완전 관리형 데이터 분석 서비스인 [Azure Data Explorer](/azure/data-explorer/data-explorer-overview)를 사용합니다. Azure Data Explorer를 사용하면 **IoT 플러그 앤 플레이** 스마트폰 앱과 같은 디바이스에서 원격 분석을 저장, 쿼리 및 처리할 수 있습니다.

이 빠른 시작에서 관련 정보는 다음과 같습니다.

- IoT Central의 데이터 내보내기 기능을 사용하여 스마트폰 앱에서 보낸 원격 분석을 Azure Data Explorer 데이터베이스로 내보냅니다.
- Azure Data Explorer를 사용하여 원격 분석에서 쿼리를 실행합니다.

## <a name="prerequisites"></a>필수 조건

- 시작하기 전에 먼저 빠른 시작 [Azure IoT Central 애플리케이션 만들기](./quick-deploy-iot-central.md)를 완료해야 합니다. 두 번째 빠른 시작인 [디바이스에 대한 규칙 및 동작 구성](quick-configure-rules.md)은 선택 사항입니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="install-azure-services"></a>Azure 서비스 설치

IoT Central 애플리케이션에서 데이터를 내보내려면 먼저 Azure Data Explorer 클러스터 및 데이터베이스가 필요합니다. 이 빠른 시작에서는 [Azure Cloud Shell](https://shell.azure.com)의 bash 환경을 사용하여 만들고 구성합니다.

Azure Cloud Shell에서 다음 스크립트를 실행합니다. 스크립트를 실행하기 전에 `clustername` 값을 고유한 클러스터 이름으로 바꿉니다.

> [!IMPORTANT]
> 스크립트를 실행하는 데 10분 이상 걸립니다.

```azurecli
clustername="<A unique name for your cluster>"
databasename="phonedata"
location="eastus"
resourcegroup="IoTCentralExportData"

az extension add -n kusto

# Create a resource group for the Azure Data Explorer cluster
az group create --location $location \
    --name $resourcegroup

# Create the Azure Data Explorer cluster
# This command takes at least 10 minutes to run
az kusto cluster create --cluster-name $clustername \
    --sku name="Standard_D11_v2"  tier="Standard" \
    --enable-streaming-ingest=true \
    --enable-auto-stop=true \
    --resource-group $resourcegroup --location $location

# Crete a database in the cluster
az kusto database create --cluster-name $clustername \
    --database-name $databasename \
    --read-write-database location=$location soft-delete-period=P365D hot-cache-period=P31D \
    --resource-group $resourcegroup

# Create a service principal to use when authenticating from IoT Central
SP_JSON=$(az ad sp create-for-rbac --skip-assignment --name spforiotcentral)

az kusto database-principal-assignment create --cluster-name $clustername \
                                              --database-name $databasename \
                                              --principal-id $(jq -r .appId <<< $SP_JSON) \
                                              --principal-assignment-name spforiotcentral \
                                              --resource-group $resourcegroup \
                                              --principal-type App \
                                              --role Admin

echo "Azure Data Explorer URL: $(az kusto cluster show --name $clustername --resource-group $resourcegroup --query uri -o tsv)"
echo "Client ID: $(jq -r .appId <<< $SP_JSON)"
echo "Tenant ID: $(jq -r .tenant <<< $SP_JSON)"
echo "Client secret: $(jq -r .password <<< $SP_JSON)" 
```

**Azure Data Explorer URL**, **클라이언트 ID**, **테넌트 ID** 및 **클라이언트 암호** 를 적어 둡니다. 이러한 값은 빠른 시작의 뒷부분에서 사용합니다.

## <a name="configure-the-database"></a>데이터베이스 구성

테이블을 데이터베이스에 추가하여 **IoT 플러그 앤 플레이** 스마트폰 앱의 가속도계 데이터를 저장하려면 다음을 수행합니다.

1. 이전 섹션의 **Azure Data Explorer URL** 을 사용하여 Azure Data Explorer 환경으로 이동합니다.

1. 클러스터 노드를 펼치고, **phonedata** 데이터베이스를 선택합니다. 쿼리 창의 대처가 `Scope:yourclustername.eastus/phonedata`로 변경됩니다.

1. 다음 Kusto 스크립트를 쿼리 편집기에 붙여넣고, **실행** 을 선택합니다.

    ```kusto
    .create table acceleration (
      EnqueuedTime:datetime,
      Device:string,
      X:real,
      Y:real,
      Z:real
    );
    ```

    결과는 다음 스크린샷과 같습니다.

    :::image type="content" source="media/quick-export-data/azure-data-explorer-create-table.png" alt-text="Azure Data Explorer에서 테이블을 만든 결과를 보여 주는 스크린샷":::

1. Azure Data Explorer에서 새 탭을 열고, 다음 Kusto 스크립트를 붙여넣습니다. 스크립트에서 **acceleration** 테이블에 대한 스트리밍 수신을 사용하도록 설정합니다.

    ```kusto
    .alter table acceleration policy streamingingestion enable;
    ```

브라우저에서 Azure Data Explorer 페이지를 열어 둡니다. IoT Central 애플리케이션에서 데이터 내보내기가 구성되면 쿼리를 실행하여 **acceleration** 테이블에 저장된 가속도계 원격 분석을 확인합니다.

## <a name="configure-data-export"></a>데이터 내보내기 구성

IoT Central에서 데이터 내보내기 대상을 구성하려면 다음을 수행합니다.

1. IoT Central 애플리케이션에서 **데이터 내보내기** 페이지로 이동합니다.
1. **대상** 탭을 선택한 다음, **대상 추가** 를 선택합니다.
1. 대상 이름으로 *Azure Data Explorer* 를 입력합니다. 대상 유형으로 **Azure Data Explorer** 를 선택합니다.
1. **클러스터 URL** 에서 이전에 적어 둔 **Azure Data Explorer URL** 을 입력합니다.
1. **데이터베이스 이름** 에서 *phonedata* 를 입력합니다.
1. **테이블 이름** 에서 *acceleration* 을 입력합니다.
1. **클라이언트 ID** 에서 이전에 적어 둔 **클라이언트 ID** 를 입력합니다.
1. **테넌트 ID** 에서 이전에 적어 둔 **테넌트 ID** 를 입력합니다.
1. **클라이언트 암호** 에서 이전에 적어 둔 **클라이언트 암호** 를 입력합니다.
1. **저장** 을 선택합니다.

데이터 내보내기를 구성하려면 다음을 수행합니다.

1. **데이터 내보내기** 페이지에서 **내보내기** 탭을 선택한 다음, **내보내기 추가** 를 선택합니다.
1. 내보내기 이름으로 *전화 가속도계* 를 입력합니다.
1. 내보낼 데이터 유형으로 **원격 분석** 을 선택합니다.
1. 다음 표의 정보를 사용하여 두 개의 필터를 추가합니다.

    | Name | 연산자 | 값 |
    | ---- | -------- | ----- |
    | 디바이스 템플릿 | 같음 | IoT 플러그 앤 플레이 모바일 |
    | 센서/가속/X | Exists | 해당 없음 |

    모든 조건이 true인 경우 데이터를 내보내는 옵션이 설정되어 있는지 확인합니다.

1. 대상으로 **Azure Data Explorer** 를 추가합니다.
1. 데이터 변환을 대상에 추가합니다. 다음 쿼리를 **데이터 변환 페이지** 의 **2. 변환 쿼리 작성** 필드에 추가합니다.

    ```json
    import "iotc" as iotc;
    {
        Device: .device.id,
        EnqueuedTime: .enqueuedTime,
        X: .telemetry | iotc::find(.name == "accelerometer").value.x,
        Y: .telemetry | iotc::find(.name == "accelerometer").value.y,
        Z: .telemetry | iotc::find(.name == "accelerometer").value.z
    }
    ```

    :::image type="content" source="media/quick-export-data/data-transformation-query.png" alt-text="내보내기에 대한 데이터 변환 쿼리를 보여 주는 스크린샷":::

    변환이 작동하는 방식을 확인하고 쿼리를 실험하려면 다음 샘플 원격 분석 메시지를 **1. 입력 메시지 추가** 에 붙여넣습니다.

    ```json
    {
      "messageProperties": {},
      "device": {
        "id": "8hltz8xa7n",
        "properties": {
          "reported": []
        },
        "approved": true,
        "types": [],
        "name": "8hltz8xa7n",
        "simulated": false,
        "provisioned": true,
        "modules": [],
        "templateId": "urn:modelDefinition:vlcd3zvzdm:y425jkkpqzeu",
        "templateName": "IoT Plug and Play mobile",
        "organizations": [],
        "cloudProperties": [],
        "blocked": false
      },
      "component": "sensors",
      "applicationId": "40a97c91-50cc-44f0-9f63-71386613facc",
      "messageSource": "telemetry",
      "telemetry": [
        {
          "id": "dtmi:azureiot:PhoneSensors:__accelerometer;1",
          "name": "accelerometer",
          "value": {
            "x": 0.09960123896598816,
            "y": 0.09541380405426025,
            "z": 9.907781600952148
          }
        }
      ],
      "enqueuedTime": "2021-11-12T10:01:30.588Z",
      "enrichments": {}
    }
    ```

1. 변환을 저장합니다. 그런 다음, 데이터 내보내기 정의를 저장합니다.

내보내기 상태가 **정상** 으로 표시될 때까지 기다립니다.

:::image type="content" source="media/quick-export-data/healthy-export.png" alt-text="정상 상태의 실행 중인 데이터 내보내기를 보여 주는 스크린샷.":::

## <a name="query-exported-data"></a>내보낸 데이터 쿼리

Azure Data Explorer에서 새 탭을 열고, 다음 Kusto 쿼리를 붙여넣은 다음, **실행** 을 선택하여 가속도계 원격 분석을 표시합니다.

```kusto
['acceleration'] 
    | project EnqueuedTime, Device, X, Y, Z
    | render timechart 
```

충분한 데이터를 수집하려면 몇 분 정도 기다려야 할 수도 있습니다. 휴대폰을 다른 방향으로 유지하여 원격 분석 값이 변경되는지 확인합니다.

:::image type="content" source="media/quick-export-data/acceleration-plot.png" alt-text="가속도계 원격 분석에 대한 쿼리 결과의 스크린샷":::

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

구독에서 Azure Data Explorer 인스턴스를 제거하고 불필요하게 청구되지 않도록 하려면 [Azure Portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)에서 **IoTCentralExportData** 리소스 그룹을 삭제하거나 Azure Cloud Shell에서 다음 명령을 실행합니다.

```azurecli
az group delete --name IoTCentralExportData
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT Central에서 다른 Azure 서비스로 데이터를 지속적으로 내보내는 방법을 배웠습니다.

이제 데이터를 내보내는 방법을 파악했으므로 제안하는 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [디바이스 템플릿을 빌드 및 관리](howto-set-up-template.md)합니다.
