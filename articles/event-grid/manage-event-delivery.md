---
title: 배달 못한 편지 및 다시 시도 정책 - Azure Event Grid
description: Event Grid에 대한 이벤트 전송 옵션을 사용자 지정하는 방법을 설명합니다. 배달 못한 편지 대상을 설정하고 배달을 다시 시도하기 위한 기간을 지정합니다.
ms.topic: conceptual
ms.date: 07/27/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 48fa27a1fe69481dda294f32bfc88af0e1718f25
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114731962"
---
# <a name="set-dead-letter-location-and-retry-policy"></a>배달 못한 편지 위치 설정 및 정책 다시 시도

이벤트 구독을 만들 때 이벤트 전송 설정을 사용자 지정할 수 있습니다. 이 문서에서는 배달 못한 편지 위치를 설정하고 재시도 설정을 사용자 지정하는 방법을 보여 줍니다. 이 기능에 대한 자세한 내용은 [Event Grid 메시지 전송 및 재시도](delivery-and-retry.md)를 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> 메시지 배달, 다시 시도 및 배달 못한 편지에 대해 알아보려면 [Event Grid 메시지 배달 및 다시 시도](delivery-and-retry.md) 개념 문서를 참조하세요.

## <a name="set-dead-letter-location"></a>배달 못한 편지 위치 설정

배달 못한 편지 위치를 설정하려면 엔드포인트로 전달할 수 없는 이벤트를 보유할 스토리지 계정이 필요합니다. 이 예제는 기존 스토리지 계정의 리소스 ID를 가져옵니다. 배달 못한 편지 엔드포인트의 스토리지 계정에 있는 컨테이너를 사용하는 이벤트 구독을 만듭니다.

> [!NOTE]
> - 이 문서에서 명령을 실행하기 전에 스토리지에서 스토리지 계정 및 Blob 컨테이너를 만듭니다.
> - Event Grid 서비스는 이 컨테이너에 Blob을 만듭니다. Blob 이름에는 모든 문자가 대문자인 Event Grid 구독의 이름이 포함됩니다. 예를 들어 구독 이름이 My-Blob-Subscription인 경우 배달 못한 편지 Blob 이름에 MY-BLOB-SUBSCRIPTION(myblobcontainer/MY-BLOB-SUBSCRIPTION/2019/8/8/5/111111111-1111-1111-1111-111111111111.json)이 포함됩니다. 이 동작은 Azure 서비스 간에 처리하는 경우 차이가 발생하는 것을 방지합니다.


### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

배달 못한 편지를 해제하려면 이벤트 구독을 만드는 명령을 다시 실행하지만 `deadletter-endpoint`에 대한 값을 제공하지는 않습니다. 이벤트 구독을 삭제할 필요가 없습니다.

> [!NOTE]
> 로컬 머신에서 Azure CLI를 사용 중인 경우 Azure CLI 버전 2.0.56 이상을 사용하세요. Azure CLI의 최신 버전을 설치하는 방법에 대한 지침은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

배달 못한 편지를 해제하려면 이벤트 구독을 만드는 명령을 다시 실행하지만 `DeadLetterEndpoint`에 대한 값을 제공하지는 않습니다. 이벤트 구독을 삭제할 필요가 없습니다.

> [!NOTE]
> 로컬 머신에서 Azure Poweshell을 사용하는 경우 Azure PowerShell 버전 1.1.0 이상을 사용하세요. [Azure 다운로드](https://azure.microsoft.com/downloads/)에서 최신 Azure PowerShell을 다운로드하여 설치합니다.

## <a name="set-retry-policy"></a>재시도 정책 설정

Event Grid 구독을 만들 때 Event Grid에서 이벤트 전송을 시도할 기간의 값을 설정할 수 있습니다. 기본적으로 Event Grid는 24시간(1440분) 동안 또는 30회 시도합니다. Event Grid 구독에 대해 이러한 값 중 하나를 설정할 수 있습니다. 이벤트 TTL(Time to Live)의 값은 1과 1440 사이의 정수여야 합니다. 최대 재시도 값은 1과 30 사이의 정수여야 합니다.

[재시도 일정](delivery-and-retry.md#retry-schedule)을 구성할 수 없습니다.

### <a name="azure-cli"></a>Azure CLI

이벤트 TTL(Time to Live)을 1440분 이외의 값으로 설정하려면 다음을 사용합니다.

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

최대 재시도 값을 30 이외의 값으로 설정하려면 다음을 사용합니다.

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

> [!NOTE]
> `event-ttl` 및 `max-deliver-attempts`를 모두 설정하면 Event Grid는 먼저 만료되는 것을 사용하여 이벤트 전송을 중지할 시기를 결정합니다. 예를 들어 TTL(Time-to-Live)로 30분을 설정하고 최대 배달 시도를 10회로 설정한 경우입니다. 30분 후 이벤트가 전달되지 않거나 10회 시도 후에도 전달되지 않으면 발생 순서에 관계없이 이벤트가 배달되지 않습니다.  

### <a name="powershell"></a>PowerShell

이벤트 TTL(Time to Live)을 1440분 이외의 값으로 설정하려면 다음을 사용합니다.

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

최대 재시도 값을 30 이외의 값으로 설정하려면 다음을 사용합니다.

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

> [!NOTE]
> `event-ttl` 및 `max-deliver-attempts`를 모두 설정하면 Event Grid는 먼저 만료되는 것을 사용하여 이벤트 전송을 중지할 시기를 결정합니다. 예를 들어 TTL(Time-to-Live)로 30분을 설정하고 최대 배달 시도를 10회로 설정한 경우입니다. 30분 후 이벤트가 전달되지 않거나 10회 시도 후에도 전달되지 않으면 발생 순서에 관계없이 이벤트가 배달되지 않습니다.  

## <a name="next-steps"></a>다음 단계

* 배달 못한 편지 이벤트를 처리하기 위해 Azure 함수 앱을 사용하는 샘플 애플리케이션은 [.NET용 Azure Event Grid 배달 못한 편지 샘플](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/)을 참조하세요.
* 이벤트 배달 및 다시 시도에 대한 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](delivery-and-retry.md)를 참조하세요.
* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
