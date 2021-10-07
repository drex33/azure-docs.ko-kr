---
title: '자습서: 하이브리드 연결에 사용자 지정 이벤트 보내기 - Event Grid'
description: '자습서: Azure Event Grid 및 Azure CLI를 사용하여 토픽을 게시하고 해당 이벤트를 구독합니다. 하이브리드 연결은 엔드포인트에 사용됩니다.'
ms.date: 09/29/2021
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: b4f4b82e590095c866d4c7a8ee016910d2e11383
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129277769"
---
# <a name="tutorial-route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>자습서: Azure CLI 및 Event Grid를 사용하여 Azure Relay 하이브리드 연결로 사용자 지정 이벤트 라우팅

Azure Event Grid는 클라우드에 대한 이벤트 서비스입니다. Azure Relay 하이브리드 연결은 지원되는 이벤트 처리기 중 하나입니다. 공용 엔드포인트가 없는 애플리케이션에서 이벤트를 처리해야 할 때 하이브리드 연결을 이벤트 처리기로 사용합니다. 이러한 애플리케이션은 회사 엔터프라이즈 네트워크 내에서 작성될 수 있습니다. 이 문서에서는 Azure CLI를 사용하여 사용자 지정 항목을 만들고 사용자 지정 항목을 구독하며 이벤트를 트리거하여 결과를 확인합니다. 이벤트를 하이브리드 연결로 보냅니다.

## <a name="prerequisites"></a>사전 요구 사항

- 이 문서에서는 이미 하이브리드 연결 및 수신기 애플리케이션이 있는 것으로 가정합니다. 하이브리드 연결을 시작하려면 [Relay 하이브리드 연결 시작 - .NET](../azure-relay/relay-hybrid-connections-dotnet-get-started.md) 또는 [Relay 하이브리드 연결 시작 - 노드](../azure-relay/relay-hybrid-connections-node-get-started.md)를 참조하세요.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 이 문서에는 Azure CLI 버전 2.0.56 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Event Grid 토픽은 Azure 리소스이며 Azure 리소스 그룹에 배치해야 합니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. 

다음 예제에서는 *westus2* 위치에 *gridResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>사용자 지정 토픽 만들기

Event Grid 항목은 이벤트를 게시하는 사용자 정의 엔드포인트를 제공합니다. 다음 예제에서는 리소스 그룹에 사용자 지정 토픽을 만듭니다. `<topic_name>`을 사용자 지정 항목의 고유한 이름으로 바꿉니다. Event Grid 항목 이름은 DNS 항목으로 표시되기 때문에 고유해야 합니다.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-custom-topic"></a>사용자 지정 항목 구독

Event Grid 항목을 구독하여 Event Grid에 추적하려는 이벤트를 알립니다. 다음 예제에서는 직접 만든 사용자 지정 항목을 구독하고, 엔드포인트에 대한 하이브리드 연결의 리소스 ID를 전달합니다. 하이브리드 연결 ID는 다음과 같은 형식입니다.

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

다음 스크립트는 릴레이 네임스페이스의 리소스 ID를 가져옵니다. 하이브리드 연결의 ID를 생성하고 Event Grid 토픽을 구독합니다. 스크립트는 엔드포인트 형식을 `hybridconnection`으로 설정하고 엔드포인트에 대한 하이브리드 연결 ID를 사용합니다.

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid \
  --expiration-date "<yyyy-mm-dd>"
```

구독의 [만료 날짜](concepts.md#event-subscription-expiration)가 설정되었습니다.

## <a name="create-application-to-process-events"></a>이벤트를 처리하는 애플리케이션 만들기

하이브리드 연결에서 이벤트를 검색할 수 있는 애플리케이션이 필요합니다. [C#용 Microsoft Azure Event Grid 하이브리드 연결 소비자 샘플](https://github.com/Azure-Samples/event-grid-dotnet-hybridconnection-destination)은 해당 작업을 수행합니다. 필수 구성 요소 단계를 이미 완료했습니다.

1. Visual Studio 2019 이상이 설치되어 있는지 확인하세요.

1. 로컬 컴퓨터에 리포지토리를 복제합니다.

1. Visual Studio에서 HybridConnectionConsumer 프로젝트를 로드합니다.

1. Program.cs에서 `<relayConnectionString>` 및 `<hybridConnectionName>`을 만든 릴레이 연결 문자열 및 하이브리드 연결 이름으로 바꿉니다.

1. Visual Studio에서 애플리케이션을 컴파일하고 실행합니다.

## <a name="send-an-event-to-your-topic"></a>토픽에 이벤트 보내기

이벤트를 트리거하여 Event Grid가 메시지를 사용자 엔드포인트에 어떻게 배포하는지 살펴보겠습니다. 이 아티클에서는 Azure CLI를 사용하여 이벤트를 트리거하는 방법을 보여줍니다. 또한 [Event Grid 게시자 애플리케이션](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridPublisher)을 사용할 수 있습니다.

먼저, 사용자 지정 토픽에 대한 URL 및 키를 가져오겠습니다. 다시, `<topic_name>`의 사용자 지정 항목 이름을 사용합니다.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

이 문서를 간소화하기 위해 사용자 지정 항목에 보낼 샘플 이벤트 데이터를 사용합니다. 일반적으로 애플리케이션 또는 Azure 서비스는 이벤트 데이터를 보냅니다. CURL은 HTTP 요청을 보내는 유틸리티입니다. 이 문서에서는 CURL을 사용하여 사용자 지정 항목에 이벤트를 보냅니다.

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

수신기 애플리케이션이 이벤트 메시지를 수신해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리
이 이벤트로 작업을 계속하려는 경우 이 문서에서 만든 리소스를 정리하지 마세요. 그렇지 않으면 다음 명령을 사용하여 이 문서에서 만든 리소스를 삭제합니다.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>다음 단계

토픽 및 이벤트 구독을 만드는 방법을 배웠으므로 어떤 Event Grid가 도움이 되는지 자세히 알아보세요.

- [Event Grid 정보](overview.md)
- [Blob Storage 이벤트를 사용자 지정 웹 엔드포인트로 라우팅](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid 및 Logic Apps를 사용하여 가상 머신 변경 모니터링](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [데이터 웨어하우스로 빅 데이터 스트림](event-grid-event-hubs-integration.md)