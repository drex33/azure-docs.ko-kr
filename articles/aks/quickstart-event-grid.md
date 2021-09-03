---
title: Azure Event Grid를 사용하여 Azure Kubernetes Service 이벤트 구독(미리 보기)
description: Azure Event Grid를 사용하여 Azure Kubernetes Service 이벤트를 구독할 수 있습니다.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 07/12/2021
ms.author: zarhoads
ms.openlocfilehash: cc4801dc588216cbcfb2c6c2742643b2e0546e63
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113734239"
---
# <a name="quickstart-subscribe-to-azure-kubernetes-service-aks-events-with-azure-event-grid-preview"></a>빠른 시작: Azure Event Grid를 사용하여 AKS(Azure Kubernetes Service) 이벤트 구독(미리 보기)

Azure Event Grid는 게시-구독 모델을 사용하여 균일한 이벤트 소비를 제공하는 완전히 관리되는 이벤트 라우팅 서비스입니다.

이 빠른 시작에서는 AKS 클러스터를 만들고 AKS 이벤트를 구독합니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI 설치](/cli/azure/install-azure-cli)

### <a name="register-the-eventgridpreview-preview-feature"></a>`EventgridPreview` 미리 보기 기능 등록

기능을 사용하려면 구독에서도 `EventgridPreview` 기능 플래그를 사용하도록 설정해야 합니다.

`EventgridPreview`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EventgridPreview"
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EventgridPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

[az aks create][az-aks-create] 명령을 사용하여 AKS 클러스터를 만듭니다. 다음 예에서는 *MyResourceGroup* 리소스 그룹에 하나의 노드가 있는 *MyResourceGroup* 리소스 그룹과 *MyAKS* 라는 클러스터를 만듭니다.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus  --node-count 1 --generate-ssh-keys
```

## <a name="subscribe-to-aks-events"></a>AKS 이벤트 구독

[az eventhubs namespace create][az-eventhubs-namespace-create] 및 [az eventhubs eventhub create][az-eventhubs-eventhub-create]를 사용하여 네임스페이스와 이벤트 허브를 만듭니다. 다음 예에서는 *MyResourceGroup* 리소스 그룹의 *MyNamespace* 에 네임스페이스 *MyNamespace* 와 이벤트 허브 *MyEventGridHub* 를 만듭니다.

```azurecli
az eventhubs namespace create --location eastus --name MyNamespace -g MyResourceGroup
az eventhubs eventhub create --name MyEventGridHub --namespace-name MyNamespace -g MyResourceGroup
```

> [!NOTE]
> 네임스페이스의 *이름* 은 고유해야 합니다.

[az eventgrid event-subscription create][az-eventgrid-event-subscription-create]를 사용하여 AKS 이벤트를 구독합니다.

```azurecli
SOURCE_RESOURCE_ID=$(az aks show -g MyResourceGroup -n MyAKS --query id --output tsv)
ENDPOINT=$(az eventhubs eventhub show -g MyResourceGroup -n MyEventGridHub --namespace-name MyNamespace --query id --output tsv)
az eventgrid event-subscription create --name MyEventGridSubscription \
--source-resource-id $SOURCE_RESOURCE_ID \
--endpoint-type eventhub \
--endpoint $ENDPOINT
```

`az eventgrid event-subscription list`을 사용하여 AKS 이벤트 구독을 확인합니다.

```azurecli
az eventgrid event-subscription list --source-resource-id $SOURCE_RESOURCE_ID
```

다음 예시 출력은 *MyAKS* 클러스터의 이벤트를 구독하고 해당 이벤트가 *MyEventGridHub* 이벤트 허브로 전달됨을 보여 줍니다.

```output
$ az eventgrid event-subscription list --source-resource-id $SOURCE_RESOURCE_ID
[
  {
    "deadLetterDestination": null,
    "deadLetterWithResourceIdentity": null,
    "deliveryWithResourceIdentity": null,
    "destination": {
      "deliveryAttributeMappings": null,
      "endpointType": "EventHub",
      "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNamespace/eventhubs/MyEventGridHub"
    },
    "eventDeliverySchema": "EventGridSchema",
    "expirationTimeUtc": null,
    "filter": {
      "advancedFilters": null,
      "enableAdvancedFilteringOnArrays": null,
      "includedEventTypes": [
        "Microsoft.ContainerService.NewKubernetesVersionAvailable"
      ],
      "isSubjectCaseSensitive": null,
      "subjectBeginsWith": "",
      "subjectEndsWith": ""
    },
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/MyAKS/providers/Microsoft.EventGrid/eventSubscriptions/MyEventGridSubscription",
    "labels": null,
    "name": "MyEventGridSubscription",
    "provisioningState": "Succeeded",
    "resourceGroup": "MyResourceGroup",
    "retryPolicy": {
      "eventTimeToLiveInMinutes": 1440,
      "maxDeliveryAttempts": 30
    },
    "systemData": null,
    "topic": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/MyResourceGroup/providers/microsoft.containerservice/managedclusters/MyAKS",
    "type": "Microsoft.EventGrid/eventSubscriptions"
  }
]
```

AKS 이벤트가 발생하면 이벤트 허브에 해당 이벤트가 표시됩니다. 예를 들어 클러스터에 사용 가능한 Kubernetes 버전 목록이 변경되면 `Microsoft.ContainerService.NewKubernetesVersionAvailable` 이벤트가 표시됩니다. AKS가 내보내는 이벤트에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)를 Event Grid 원본으로 사용][aks-events]을 참조하세요.

## <a name="delete-the-cluster-and-subscriptions"></a>클러스터 및 구독 삭제

[az group delete][az-group-delete] 명령을 사용하여 리소스 그룹, AKS 클러스터, 네임스페이스, 이벤트 허브 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제][sp-delete]를 참조하세요.
> 
> 관리 ID를 사용하는 경우 ID는 플랫폼에 의해 관리되며 제거할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Kubernetes 클러스터를 배포한 다음 Azure Event Hub에서 AKS 이벤트를 구독했습니다.

AKS에 대해 자세히 알아보고 배포 예제에 대한 전체 코드를 연습해 보려면 Kubernetes 클러스터 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [AKS 자습서][aks-tutorial]

[aks-events]: ../event-grid/event-schema-aks.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-eventhubs-namespace-create]: /cli/azure/eventhubs/namespace?view=azure-cli-latest&preserve-view=true#az-eventhubs-namespace-create
[az-eventhubs-eventhub-create]: /cli/azure/eventhubs/eventhub?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-create
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription?view=azure-cli-latest&preserve-view=true#az-eventgrid-event-subscription-create
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-group-delete]: /cli/azure/group#az_group_delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
