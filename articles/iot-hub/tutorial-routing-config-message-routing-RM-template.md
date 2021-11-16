---
title: 자습서 - Azure Resource Manager 템플릿을 사용하여 Azure IoT Hub에 대한 메시지 라우팅 구성
description: 자습서 - Azure Resource Manager 템플릿을 사용하여 Azure IoT Hub에 대한 메시지 라우팅 구성
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 08/24/2021
ms.author: lizross
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 988f1e48bfc5fdbd4f6830fe52446859cf2bfa64
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552473"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>자습서: Azure Resource Manager 템플릿을 사용하여 IoT Hub 메시지 라우팅 구성

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>메시지 라우팅

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>템플릿 및 매개 변수 파일 다운로드

이 자습서의 두 번째 부분에서는 Visual Studio 애플리케이션을 다운로드하고 실행하여 IoT Hub에 메시지를 보냅니다. 이 다운로드에는 Azure Resource Manager 템플릿과 매개 변수 파일은 물론 Azure CLI와 PowerShell 스크립트가 포함된 폴더가 있습니다.

[Azure IoT C# 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip)을 지금 다운로드합니다. main.zip 파일의 압축을 풉니다. Resource Manager 템플릿과 매개 변수 파일은 /iot-hub/Tutorials/Routing/SimulatedDevice/resources/에 있는 **template_iothub.json** 및 **template_iothub_parameters.json** 파일입니다.

## <a name="create-your-resources"></a>리소스 만들기

Azure RM(Resource Manager) 템플릿을 사용하여 모든 리소스를 만들려고 합니다. Azure CLI 및 PowerShell 스크립트는 한 번에 몇 줄씩 실행할 수 있습니다. RM 템플릿은 한 번에 배포됩니다. 이 문서는 각 섹션을 이해하는 데 도움이 되도록 섹션을 개별적으로 보여줍니다. 그런 다음, 템플릿을 배포하는 방법과 테스트용 가상 디바이스를 만드는 방법을 보여줍니다. 템플릿이 배포되면 되면 포털에서 메시지 라우팅 구성을 볼 수 있습니다.

몇 가지 리소스 이름(예: IoT Hub 이름 및 스토리지 계정 이름)은 전역적으로 고유해야 합니다. 리소스의 이름을 쉽게 지정할 수 있도록, 해당 리소스 이름에는 현재 날짜/시간으로 생성된 임의의 영숫자 값을 추가하도록 설정됩니다. 

템플릿을 살펴보면 전달된 매개 변수를 취하여 매개 변수에 *randomValue* 를 연결하는 해당 리소스에 대해 변수가 설정된 위치를 알 수 있습니다. 

다음 섹션에서는 사용된 매개 변수에 대해 설명합니다.

### <a name="parameters"></a>매개 변수

이러한 매개 변수 중 대부분은 기본값이 있습니다. **_in** 으로 끝나는 매개 변수는 전역적으로 고유하게 만들기 위해 *randomValue* 와 연결됩니다. 

**randomValue**: 이 값은 템플릿 배포 당시 현재 날짜/시간으로 생성됩니다. 이 필드는 템플릿 자체에서 생성되므로 매개 변수 파일에 없습니다.

**subscriptionId**: 이 필드는 템플릿을 배포하는 구독에 대해 설정됩니다. 이 필드는 설정되어 있으므로 매개 변수 파일에 없습니다.

**IoTHubName_in**: 이 필드는 기본 IoT Hub 이름이며, randomValue와 연결되므로 전역적으로 고유합니다.

**location**: 이 필드는 배포할 Azure 영역(예: "westus")입니다.

**consumer_group**: 이 필드는 라우팅 엔드포인트를 통해 들어오는 메시지에 대해 설정된 소비자 그룹입니다. Azure Stream Analytics에서 결과를 필터링하는 데 사용됩니다. 예를 들어, 모든 것을 가져올 수 있는 전체 스트림이 있거나 consumer_group을 **Contoso** 로 설정하여 데이터를 가져오는 경우에는, 해당 항목만 표시하도록 Azure Stream Analytics 스트림(및 Power BI 보고서)를 설정할 수 있습니다. 이 필드는 이 자습서의 2부에서 사용됩니다.

**sku_name**: 이 필드는 IoT Hub에 대한 확장입니다. 이 필드는 S1 이상이어야 합니다. 무료 계층에서는 여러 엔드포인트가 허용되지 않기 때문에 이 자습서에 대해 작동하지 않습니다.

**sku_units**: 이 필드는 **sku_name** 과 함께 사용되며 사용할 수 있는 IoT Hub 장치 수입니다.

**d2c_partitions**: 이 필드는 이벤트 스트림에 사용되는 파티션 수입니다.

**storageAccountName_in**: 이 필드는 만들 스토리지 계정 이름입니다. 메시지는 스토리지 계정의 컨테이너로 라우팅됩니다. 이 필드는 randomValue와 연결되어 전역적으로 고유하게 됩니다.

**storageContainerName**: 이 필드는 스토리지 계정에 라우팅된 메시지가 저장되는 컨테이너의 이름입니다.

**storage_endpoint**: 이 필드는 메시지 라우팅에서 사용되는 스토리지 계정 엔드포인트의 이름입니다.

**service_bus_namespace_in**: 이 필드는 만들 Service Bus 네임스페이스의 이름입니다. 이 값은 randomValue와 연결되어 전역적으로 고유하게 됩니다.

**service_bus_queue_in**: 이 필드는 메시지 라우팅에 사용되는 Service Bus 큐의 이름입니다. 이 값은 randomValue와 연결되어 전역적으로 고유하게 됩니다.

**AuthRules_sb_queue**: 이 필드는 큐에 대한 연결 문자열을 검색하는 데 사용되는 Service Bus 큐에 대한 권한 부여 규칙입니다.

### <a name="variables"></a>variables

이 값은 템플릿에 사용되며 대부분 매개 변수에서 파생됩니다.

**queueAuthorizationRuleResourceId**: 이 필드는 Service Bus 큐에 대한 권한 부여 규칙의 ResourceId입니다. ResourceId는 차례로 큐의 연결 문자열을 검색하는 데 사용됩니다.

**iotHubName**: 이 필드는 randomValue가 연결된 후 IoT Hub의 이름입니다. 

**storageAccountName**: 이 필드는 randomValue가 연결된 후 스토리지 계정의 이름입니다. 

**service_bus_namespace**: 이 필드는 randomValue가 연결된 후 네임스페이스입니다.

**service_bus_queue**: 이 필드는 randomValue가 연결된 Service Bus 큐 이름입니다.

**sbVersion**: 사용할 Service Bus API 버전입니다. 이 경우 "2017-04-01"입니다.

### <a name="resources-storage-account-and-container"></a>리소스: 스토리지 계정 및 컨테이너

첫 번째 생성된 리소스는 스토리지 계정이며, 메시지가 라우팅되는 컨테이너와 함께 생성됩니다. 컨테이너는 스토리지 계정 아래 리소스입니다. 스토리지 계정에 대해 `dependsOn` 절이 있으므로 스토리지 계정을 컨테이너보다 먼저 만들어야 합니다.

이 섹션은 다음과 같은 모양입니다.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>리소스: Service Bus 네임스페이스 및 큐

두 번째 생성된 리소스는 Service Bus 네임스페이스이며, 메시지가 라우팅되는 Service Bus 큐와 함께 생성됩니다. SKU는 표준으로 설정됩니다. API 버전은 변수에서 검색됩니다. 이 섹션을 배포할 때 Service Bus 네임스페이스를 활성화하도록 설정됩니다(status:Active). 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

이 섹션은 Service Bus 큐를 만듭니다. 스크립트의 이 부분에는 네임스페이스가 큐보다 먼저 생성되도록 하는 `dependsOn` 절이 있습니다.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>리소스: IoT Hub 및 메시지 라우팅

스토리지 계정과 Service Bus 큐가 생성되었으면 여기로 메시지를 라우팅하는 IoT Hub를 만듭니다. RM 템플릿은 `dependsOn` 절을 사용하기 때문에 Service Bus 리소스와 스토리지 계정이 생성되기 전에 허브를 생성하려고 하지 않습니다. 

다음은 IoT Hub 섹션의 첫 번째 부분입니다. 템플릿의 이 부분은 종속성을 설정하고 속성으로 시작됩니다.

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

다음 섹션은 IoT Hub에 대한 메시지 라우팅 구성 섹션입니다. 첫 번째 섹션은 엔드포인트에 대한 섹션입니다. 템플릿의 이 부분은 연결 문자열을 비롯한 스토리지 계정과 Service Bus 큐에 대한 라우팅 엔드포인트를 설정합니다.

큐에 대한 연결 문자열을 만들려면 인라인으로 검색되는 queueAuthorizationRulesResourcedId가 필요합니다. 스토리지 계정에 대한 연결 문자열을 만들려면 기본 스토리지 키를 검색한 다음, 연결 문자열 형식으로 사용합니다.

엔드포인트 구성에서는 Blob 형식도 `AVRO` 또는 `JSON`으로 설정합니다.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

다음 섹션에서는 엔드포인트에 대한 메시지 라우팅을 설명합니다. 엔드포인트마다 하나씩 설정되므로 Service Bus 큐에 대해 하나가 있고 스토리지 계정 컨테이너에 대해 하나가 있습니다.

스토리지로 라우팅되는 메시지에 대한 쿼리 조건은 `level="storage"`이며 Service Bus 큐로 라우팅되는 메시지에 대한 쿼리 조건은 `level="critical"`입니다.

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

이 json은 허브에 대한 기본 정보와 SKU가 포함된 나머지 IoT Hub 섹션을 보여줍니다.

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>리소스: Service Bus 큐 권한 부여 규칙

Service Bus 큐 권한 부여 규칙은 Service Bus 큐에 대한 연결 문자열을 검색하는 데 사용됩니다. `dependsOn` 절을 사용하여 Service Bus 네임스페이스와 Service Bus 큐보다 먼저 생성되지 않도록 합니다.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>리소스: 소비자 그룹

이 섹션에서는 자습서 두 번째 부분의 Azure Stream Analytics에서 사용할 IoT Hub 데이터에 대한 소비자 그룹을 만듭니다.

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>리소스: outputs

표시할 배포 스크립트로 값을 다시 보내려면 출력 섹션을 사용합니다. 템플릿의 이 부분은 Service Bus 큐에 대한 연결 문자열을 반환합니다. 값을 반환하는 것은 필수가 아니며, 호출 스크립트에 결과를 반환하는 방법의 예제로 포함되어 있습니다.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>RM 템플릿 배포

Azure에 템플릿을 배포하려면 템플릿과 매개 변수 파일을 Azure Cloud Shell에 업로드한 다음, 스크립트를 실행하여 템플릿을 배포합니다. Azure Cloud Shell을 열고 로그인합니다. 이 예제에서는 PowerShell을 사용합니다.

파일을 업로드하려면 메뉴 모음에서 **파일 업로드/다운로드** 아이콘을 선택한 다음, 업로드를 선택합니다.

![파일 업로드/다운로드 아이콘을 강조 표시하는 스크린샷.](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

열리는 파일 탐색기를 사용하여 로컬 디스크의 파일을 찾아서 선택한 다음, **열기** 를 선택합니다.

파일이 업로드되면 결과 다음 이미지와 같은 대화 상자가 표시됩니다.

![파일 업로드/다운로드 결과가 강조 표시된 Cloud Shell 메뉴 모음](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Cloud Shell 인스턴스에 사용되는 공유에 파일이 업로드됩니다. 

스크립트를 실행하여 배포를 수행합니다. 이 스크립트의 마지막 줄은 반환하도록 설정된 변수를 검색합니다(Service Bus 큐 연결 문자열).

이 스크립트는 다음 변수를 설정하여 사용합니다.

**$RGName** 은 템플릿을 배포할 리소스 그룹 이름입니다. 이 필드는 템플릿을 배포하기 전에 생성됩니다.

**$location** 은 템플릿에 사용될 Azure 위치입니다(예: "westus").

**deploymentname** 은 반환 변수 값을 검색하기 위해 배포에 지정하는 이름입니다.

PowerShell 스크립트는 다음과 같습니다. 이 PowerShell 스크립트를 복사하여 Cloud Shell 창에 붙여넣은 다음, Enter 키를 눌러 실행합니다.

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

스크립트 오류가 있는 경우 스크립트를 로컬에서 편집하고 Cloud Shell에 다시 업로드한 다음, 스크립트를 다시 실행할 수 있습니다. 스크립트가 성공적으로 실행되면 다음 단계를 계속 진행합니다.

## <a name="create-simulated-device"></a>시뮬레이션된 디바이스 만들기

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>포털에서 메시지 라우팅 보기

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>다음 단계

모든 리소스가 설정되고 메시지 경로가 구성되었으면, 다음 자습서에서 라우팅된 메시지에 대한 정보를 처리하고 표시하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [2부 - 메시지 라우팅 결과 보기](tutorial-routing-view-message-routing-results.md)
