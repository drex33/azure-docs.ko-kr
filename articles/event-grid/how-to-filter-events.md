---
title: Azure Event Grid에 대한 이벤트를 필터링하는 방법
description: 이 문서에서는 Event Grid 구독을 만들 때 이벤트 유형(주체, 운영자 및 데이터 등)을 기준으로 이벤트를 필터링하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 08/11/2021
ms.openlocfilehash: 666690fc7c0d21c7ae257a20b49949961d89cb60
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529073"
---
# <a name="filter-events-for-event-grid"></a>Event Grid에 대한 이벤트 필터링

이 문서에서는 Event Grid 구독을 만들 때 이벤트를 필터링하는 방법을 보여줍니다. 이벤트 필터링에 대한 옵션을 알아보려면 [Event Grid 구독에 대한 이벤트 필터링 이해](event-filtering.md)를 참조하세요.

## <a name="filter-by-event-type"></a>이벤트 유형별 필터링

Event Grid 구독을 만들 때 엔드포인트에 보낼 [이벤트 유형](event-schema.md)을 지정할 수 있습니다. 이 섹션의 예제에서는 리소스 그룹에 대한 이벤트 구독을 만들지만 `Microsoft.Resources.ResourceWriteFailure` 및 `Microsoft.Resources.ResourceWriteSuccess`에 전송되는 이벤트를 제한합니다. 이벤트 유형별로 이벤트를 필터링할 때 더 많은 유연성이 필요한 경우 고급 연산자 및 데이터 필드별 필터링을 참조하세요.

### <a name="azure-powershell"></a>Azure PowerShell
PowerShell의 경우 구독을 만들 때 `-IncludedEventType` 매개 변수를 사용합니다.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

### <a name="azure-cli"></a>Azure CLI
Azure CLI의 경우 `--included-event-types` 매개 변수를 사용합니다. 다음 예제는 Bash 셸에서 Azure CLI를 사용합니다.

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

### <a name="azure-portal"></a>Azure portal

1. **이벤트 구독** 페이지에서 **필터** 탭으로 전환합니다. 
1. **이벤트 유형으로 필터링** 옆에 있는 **이벤트 유형 추가** 를 선택합니다. 

    :::image type="content" source="./media/how-to-filter-events/add-event-type-button.png" alt-text="이벤트 유형 추가 단추가 선택된 이벤트 구독 페이지의 스크린샷.":::    
1. 이벤트 유형을 입력하고 Enter 키를 누릅니다. 다음 예에서 이벤트 유형은 `Microsoft.Resources.ResourceWriteSuccess`입니다. 

    :::image type="content" source="./media/how-to-filter-events/sample-event-type.png" alt-text="샘플 이벤트 유형이 있는 이벤트 구독 페이지 스크린샷.":::    
      
### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿
Resource Manager 템플릿의 경우 `includedEventTypes` 속성을 사용합니다.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

> [!NOTE]
> 이러한 필터(이벤트 유형, 제목 및 고급)에 대한 자세한 내용은 [Event Grid 구독에 대한 이벤트 필터링 이해](event-filtering.md)를 참조하세요. 

## <a name="filter-by-subject"></a>제목별 필터링

이벤트 데이터의 제목별로 이벤트를 필터링할 수 있습니다. 제목의 시작 또는 끝에 대해 일치하는 값을 지정할 수 있습니다. 제목별로 이벤트를 필터링할 때 더 많은 유연성이 필요한 경우 고급 연산자 및 데이터 필드별 필터링을 참조하세요.

다음 PowerShell 예제에서는 제목의 시작별로 필터링하는 이벤트 구독을 만듭니다. `-SubjectBeginsWith` 매개 변수를 사용하여 특정 리소스에 대한 것으로 이벤트를 제한합니다. 네트워크 보안 그룹의 리소스 ID를 전달합니다.

### <a name="azure-powershell"></a>Azure PowerShell
```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

다음 PowerShell 예제에서는 Blob Storage에 대한 구독을 만듭니다. 이벤트를 `.jpg`로 끝나는 제목이 있는 것으로 제한합니다.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

### <a name="azure-cli"></a>Azure CLI
다음 Azure CLI 예제에서는 제목의 시작별로 필터링하는 이벤트 구독을 만듭니다. `--subject-begins-with` 매개 변수를 사용하여 특정 리소스에 대한 것으로 이벤트를 제한합니다. 네트워크 보안 그룹의 리소스 ID를 전달합니다.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

다음 Azure CLI 예제에서는 Blob Storage에 대한 구독을 만듭니다. 이벤트를 `.jpg`로 끝나는 제목이 있는 것으로 제한합니다.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

### <a name="azure-portal"></a>Azure portal

1. **이벤트 구독** 페이지에서 **제목 필터링 사용** 을 선택합니다. 
1. **제목 시작 문자** 및 **제목 종료 문자** 필드 중 하나 이상에 값을 입력합니다. 다음 옵션에서는 두 옵션을 모두 선택했습니다. 

    :::image type="content" source="./media/how-to-filter-events/subject-filter-example.png" alt-text="제목 필터링 예가 있는 이벤트 구독 페이지 스크린샷.":::
1. 이벤트 제목이 지정된 필터의 대/소문자와 일치하도록 하려면 **대/소문자 구분 제목 일치** 옵션을 선택합니다. 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿
다음 Resource Manager 템플릿 예제에서는 제목의 시작별로 필터링하는 이벤트 구독을 만듭니다. `subjectBeginsWith` 속성을 사용하여 특정 리소스에 대한 것으로 이벤트를 제한합니다. 네트워크 보안 그룹의 리소스 ID를 전달합니다.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

다음 Resource Manager 템플릿 예제에서는 Blob Storage에 대한 구독을 만듭니다. 이벤트를 `.jpg`로 끝나는 제목이 있는 것으로 제한합니다.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

> [!NOTE]
> 이러한 필터(이벤트 유형, 제목 및 고급)에 대한 자세한 내용은 [Event Grid 구독에 대한 이벤트 필터링 이해](event-filtering.md)를 참조하세요. 

## <a name="filter-by-operators-and-data"></a>연산자 및 데이터별 필터링

보다 유연한 필터링을 원한다면 연산자 및 데이터 속성을 사용하여 이벤트를 필터링하면 됩니다.

### <a name="subscribe-with-advanced-filters"></a>고급 필터가 포함된 구독

고급 필터링에 사용할 수 있는 연산자 및 키에 대해 알아보려면 [고급 필터링](event-filtering.md#advanced-filtering)을 참조하세요.

다음은 사용자 지정 토픽을 만드는 예제입니다. 사용자 지정 토픽을 구독하고 데이터 개체의 값으로 필터링합니다. 색 속성이 파란색, 빨간색 또는 녹색으로 설정된 이벤트가 구독에 전송됩니다.

### <a name="azure-powershell"></a>Azure PowerShell

PowerShell의 경우 다음을 사용합니다.

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="azure-cli"></a>Azure CLI

Azure CLI의 경우

```azurecli
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

구독의 [만료 날짜](concepts.md#event-subscription-expiration)가 설정되었습니다.


### <a name="azure-portal"></a>Azure portal

1. **이벤트 구독** 페이지의 **고급 필터** 섹션에서 **새 필터 추가** 를 선택합니다. 

    :::image type="content" source="./media/how-to-filter-events/add-new-filter-button.png" alt-text="새 필터 추가 링크가 강조 표시된 이벤트 구독 페이지를 보여 주는 스크린샷.":::    
2. 키, 연산자 및 비교할 값을 지정합니다. 다음 예에서는 **data.color** 가 키로 사용되고, **문자열이 다음에 있음** 이 연산자로 사용되고, **blue**, **red**, **green** 값이 값에 대해 지정됩니다. 

    :::image type="content" source="./media/how-to-filter-events/advanced-filter-example.png" alt-text="고급 필터의 예를 보여 주는 스크린샷."::: 

    > [!NOTE]
    > 고급 필터에 대한 자세한 내용은 [Event Grid 구독에 대한 이벤트 필터링 이해](event-filtering.md)를 참조하세요. 


### <a name="test-the-filter"></a>필터 테스트
필터를 테스트하려면 색 필드가 녹색으로 설정된 이벤트를 보냅니다. 녹색은 필터의 값 중 하나이므로 이벤트가 엔드포인트로 전달됩니다.

### <a name="azure-powershell"></a>Azure PowerShell
PowerShell의 경우 다음을 사용합니다.

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

이벤트가 전송되지 않은 시나리오를 테스트하려면 색 필드가 노란색으로 설정된 이벤트를 보냅니다. 노란색은 구독에서 지정된 값 중 하나가 아니므로 이벤트는 구독에 배달되지 않습니다.

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```


### <a name="azure-cli"></a>Azure CLI
Azure CLI의 경우

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

이벤트가 전송되지 않은 시나리오를 테스트하려면 색 필드가 노란색으로 설정된 이벤트를 보냅니다. 노란색은 구독에서 지정된 값 중 하나가 아니므로 이벤트는 구독에 배달되지 않습니다.

Azure CLI의 경우

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

## <a name="next-steps"></a>다음 단계
필터(이벤트 유형, 제목 및 고급)에 대한 자세한 내용은 [Event Grid 구독에 대한 이벤트 필터링 이해](event-filtering.md)를 참조하세요. 

