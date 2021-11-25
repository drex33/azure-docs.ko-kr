---
title: 빠른 시작 - Azure CLI를 사용하여 Azure Stream Analytics 작업 만들기
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Azure Stream Analytics 작업을 만드는 방법을 보여줍니다.
services: stream-analytics
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, mode-api
ms.date: 07/01/2020
ms.openlocfilehash: 590e59e250845811c9bd423aa37f81a5751450a2
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133047547"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Stream Analytics 작업 만들기

이 빠른 시작에서는 Azure CLI를 사용하여 온도 판독값이 27보다 큰 실시간 스트리밍 센서 메시지를 필터링하는 Stream Analytics 작업을 정의합니다. Stream Analytics 작업은 IoT Hub에서 데이터를 읽고, 데이터를 변환하고, 데이터를 BLOB 스토리지의 컨테이너에 다시 씁니다. 이 빠른 시작에서 사용된 입력 데이터는 Raspberry Pi 온라인 시뮬레이터로 생성됩니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 리소스 그룹을 만듭니다. 모든 Azure 리소스는 리소스 그룹에 배포되어야 합니다. 리소스 그룹을 사용하면 관련 Azure 리소스를 구성하고 관리할 수 있습니다.

   이 빠른 시작에서는 다음과 같이 [az group create](/cli/azure/group#az_group_create) 명령을 사용하여 *eastus* 위치에 *streamanalyticsrg* 라는 리소스 그룹을 만듭니다.

   ```azurecli
   az group create --name streamanalyticsrg --location eastus
   ```

## <a name="prepare-the-input-data"></a>입력 데이터 준비

Stream Analytics 작업을 정의하기 전에, 작업의 입력에 사용할 데이터를 준비해야 합니다.

다음 Azure CLI 코드 블록은 작업에 필요한 입력 데이터를 준비합니다. 코드를 이해하려면 섹션을 검토합니다.

1. [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) 명령을 사용하여 IoT Hub를 만듭니다. 이 예제에서는 **MyASAIoTHub** 라는 IoT Hub를 만듭니다. IoT Hub 이름은 고유하므로 사용자 고유의 IoT Hub 이름으로 표시해야 합니다. 구독에 사용 가능한 경우 체험 계층을 사용하도록 SKU를 F1로 설정합니다. 그러지 않으면 다음으로 가장 낮은 계층을 선택합니다.

    ```azurecli
    az iot hub create --name "MyASAIoTHub" --resource-group streamanalyticsrg --sku S1
    ```

    IoT Hub가 만들어지면 [az iot hub show-connection-string](/cli/azure/iot/hub) 명령을 사용하여 IoT Hub 연결 문자열을 가져옵니다. 전체 연결 문자열을 복사하여 IoT Hub를 Stream Analytics 작업에 대한 입력으로 추가할 때를 위해 저장해 둡니다.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

2. [az iothub device-identity create](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) 명령을 사용하여 IoT Hub에 디바이스를 추가합니다. 이 예제에서는 **MyASAIoTDevice** 라는 디바이스를 만듭니다.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

3. [az iot hub device-identity show-connection-string](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_show_connection_string) 명령을 사용하여 디바이스 연결 문자열을 가져옵니다. 전체 연결 문자열을 복사하여 Raspberry Pi 시뮬레이터를 만들 때를 위해 저장해 둡니다.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **출력 예제:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-a-blob-storage-account"></a>Blob Storage 계정 만들기

다음 Azure CLI 코드 블록은 작업 출력에 사용되는 BLOB 스토리지 계정을 만듭니다. 코드를 이해하려면 섹션을 검토합니다.

1. [az storage account create](/cli/azure/storage/account) 명령을 사용하여 범용 스토리지 계정을 만듭니다. 범용 스토리지 계정은 4개의 모든 서비스(Blob, 파일, 테이블 및 큐)에 사용할 수 있습니다.

   꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

   ```azurecli
   az storage account create \
       --name <storage-account> \
       --resource-group streamanalyticsrg \
       --location eastus \
       --sku Standard_ZRS \
       --encryption-services blob
   ```

2. [az storage account keys list](/cli/azure/storage/account/keys) 명령을 실행하여 스토리지 계정의 키를 가져옵니다. 다음 단계에서 사용할 수 있도록 이 키를 저장합니다.

   ```azurecli
   az storage account keys list -g streamanalyticsrg -n <storage-account>
   ```

3. Blob 저장을 위한 컨테이너는 [az storage container create](/cli/azure/storage/container) 명령을 사용하여 만듭니다. 스토리지 계정 키를 사용하여 컨테이너를 만들 수 있는 권한을 작업에 부여합니다. Azure CLI 사용하여 데이터 작업에 권한을 부여하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 blob 또는 큐 데이터에 대한 액세스 권한 부여](../storage/blobs/authorize-data-operations-cli.md)를 참조하세요.

   ```azurecli
   az storage container create \
       --account-name <storage-account> \
       --name sample-container \
       --account-key <key>
       --auth-mode key
   ```

## <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

다음 Azure CLI 코드 블록은 Stream Analytics 작업을 만듭니다. 섹션을 검토하여 코드 이해하기

1. [az stream-analytics job create](/cli/azure/stream-analytics/job#az_stream_analytics_job_create) 명령을 사용하여 Stream Analytics 작업을 만듭니다.

```azurecli
az stream-analytics job create \
    --resource-group streamanalyticsrg 
    --name streamanalyticsjob \
    --location eastus \
    --output-error-policy "Drop" \
    --events-outoforder-policy "Drop" \
    --events-outoforder-max-delay 5 \
    --events-late-arrival-max-delay 16 \     
    --data-locale "en-US"
```

## <a name="configure-input-to-the-job"></a>작업에 대한 입력 구성

[az stream-analytics input](/cli/azure/stream-analytics/input#az_stream_analytics_input_create) cmdlet을 사용하여 작업에 입력을 추가합니다. 이 cmdlet은 작업 이름, 작업 입력 이름, 리소스 그룹 이름 및 작업 입력 정의를 매개 변수로 사용합니다. 작업 입력 정의는 작업의 입력을 구성하는 데 필요한 속성을 포함하는 JSON 파일입니다. 이 예제에서는 입력으로 IoT Hub를 만듭니다.

로컬 컴퓨터에서 `datasource.json`이라는 파일을 만들고 여기에 다음 JSON 데이터를 추가합니다. `sharedAccessPolicyKey`의 값을 이전 섹션에서 저장한 IoT Hub 연결 문자열의 `SharedAccessKey` 부분으로 바꾸어야 합니다.

```json
{
    "type": "Microsoft.Devices/IotHubs",
    "properties": {
        "iotHubNamespace": "iothub",
        "sharedAccessPolicyName": "iothubowner",
        "sharedAccessPolicyKey": "sharedAccessPolicyKey=",
        "consumerGroupName": "sdkconsumergroup",
        "endpoint": "messages/events"
    }
}
```

로컬 컴퓨터에서 `serialization.json`이라는 파일을 만들고 여기에 다음 JSON 데이터를 추가합니다.

```json
{
     "type": "Json",
     "properties": {
         "encoding": "UTF8"
     }
}
```

그런 다음, `az stream-analytics input create` cmdlet을 실행합니다. `datasource` 변수 값을 작업 입력 정의 JSON 파일을 저장한 경로로 바꾸고, `serialization` 변수 값을 직렬화 JSON 파일을 저장한 경로로 바꿉니다.

```azurecli
az stream-analytics input create \
    --resource-group streamanalyticsrg 
    --job-name streamanalyticsjob \
    --name asaiotinput \
    --type Stream \
    --datasource datasource.json \
    --serialization serialization.json
```

## <a name="configure-output-to-the-job"></a>작업에 대한 출력 구성

[az stream-analytics output create](/cli/azure/stream-analytics/output#az_stream_analytics_output_create) cmdlet을 사용하여 작업에 출력을 추가합니다. 이 cmdlet은 작업 이름, 작업 출력 이름, 리소스 그룹 이름 및 작업 출력 정의를 매개 변수로 사용합니다. 작업 출력 정의는 작업의 출력을 구성하는 데 필요한 속성을 포함하는 JSON 파일입니다. 이 예제에서는 출력으로 Blob Storage를 사용합니다.

로컬 컴퓨터에서 `datasink.json`이라는 파일을 만들고 여기에 다음 JSON 데이터를 추가합니다. `accountKey`의 값을 $storageAccountKey 값에 저장된 값인 스토리지 계정의 액세스 키로 바꾸어야 합니다.

```json
{
    "type": "Microsoft.Storage/Blob",
    "properties": {
        "storageAccounts": [
            {
                "accountName": "<storage-account>",
                "accountKey": "accountKey=="
            }
        ],
        "container": "state",
        "pathPattern": "{date}/{time}",
        "dateFormat": "yyyy/MM/dd",
        "timeFormat": "HH"
    }
}
```

그런 다음, `az stream-analytics output` cmdlet을 실행합니다. `datasource` 변수 값을 작업 출력 정의 JSON 파일을 저장한 경로로 바꾸고, `serialization` 변수 값을 직렬화 JSON 파일을 저장한 경로로 바꿉니다.

```azurecli
az stream-analytics output create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name asabloboutput \
    --datasource datasink.json \
    --serialization serialization.json
```

## <a name="define-the-transformation-query"></a>변환 쿼리 정의

[az stream-analytics transformation create](/cli/azure/stream-analytics/transformation#az_stream_analytics_transformation_create) cmdlet을 사용하여 작업에 변환을 추가합니다. 이 cmdlet은 작업 이름, 작업 변환 이름, 리소스 그룹 이름 및 작업 변환 정의를 매개 변수로 사용합니다. 

`az stream-analytics transformation create` cmdlet을 실행합니다.

```azurecli
az stream-analytics transformation create \
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name Transformation \
    --streaming-units "6" \
    --transformation-query "SELECT * INTO asabloboutput FROM asaiotinput HAVING Temperature > 27"
```
## <a name="run-the-iot-simulator"></a>IoT 시뮬레이터 실행

1. [Raspberry Pi Azure IoT 온라인 시뮬레이터](https://azure-samples.github.io/raspberry-pi-web-simulator/)를 엽니다.

2. 15행의 자리 표시자를 이전 섹션에서 저장한 Azure IoT Hub 디바이스 전체 연결 문자열로 바꿉니다.

3. **실행** 을 클릭합니다. IoT Hub로 전송 중인 센서 데이터와 메시지가 출력에 표시되어야 합니다.

    ![Raspberry Pi Azure IoT 온라인 시뮬레이터](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Stream Analytics 작업을 시작하고 출력을 확인합니다.

[az stream-analytics job start](/cli/azure/stream-analytics/job#az_stream_analytics_job_start) cmdlet을 사용하여 작업을 시작합니다. 이 cmdlet은 작업 이름, 리소스 그룹 이름, 출력 시작 모드 및 시작 시간을 매개 변수로 사용합니다. `OutputStartMode`는 `JobStartTime`, `CustomTime` 또는 `LastOutputEventTime`의 값을 허용합니다.

다음 cmdlet을 실행한 후 작업이 시작되면 `True`가 출력으로 반환됩니다. 스토리지 컨테이너에서 변환된 데이터를 사용하여 출력 폴더가 생성됩니다.

```azurecli
az stream-analytics job start \
    --resource-group streamanalyticsrg \
    --name streamanalyticsjob \
    --output-start-mode JobStartTime
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 리소스 그룹, 스트리밍 작업 및 모든 관련 리소스를 삭제합니다. 작업을 삭제하면 작업에서 사용된 스트리밍 단위에 대한 청구를 방지합니다. 작업을 나중에 사용하려는 경우 삭제를 건너뛰고 지금은 작업을 중지할 수 있습니다. 이 작업을 계속 사용하지 않으려면 다음과 같은 cmdlet을 실행하여 빠른 시작에서 만든 리소스를 모두 삭제합니다.

```azurecli
az group delete \
    --name streamanalyticsrg \
    --no-wait
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI를 사용하여 간단한 Stream Analytics 작업을 배포했습니다. [Azure Portal](stream-analytics-quick-create-portal.md)과 [Visual Studio](stream-analytics-quick-create-vs.md)를 통해서도 Stream Analytics 작업을 배포할 수 있습니다.

다른 입력 원본을 구성하고 실시간 검색을 수행하는 방법을 알아보려면 다음 문서로 이동하세요.

> [!div class="nextstepaction"]
> [Azure Stream Analytics를 사용하여 실시간 부정 행위 감지](stream-analytics-real-time-fraud-detection.md)
