---
title: Azure CLI를 사용하여 이벤트 허브 만들기 - Azure Event Hubs | Microsoft Docs
description: 이 빠른 시작에서는 Azure CLI를 사용하여 이벤트 허브를 만든 다음, Java를 사용하여 이벤트를 수신하는 방법을 설명합니다.
ms.topic: quickstart
ms.date: 06/18/2021
ms.author: spelluru
ms.custom: devx-track-azurecli, mode-api
ms.openlocfilehash: 6bc24e768210e28a878f6dced7e65f23688e2e0d
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133075108"
---
# <a name="quickstart-create-an-event-hub-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 이벤트 허브 만들기

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 디바이스에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/스토리지 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

빠른 시작에서 Azure CLI를 사용하여 이벤트 허브를 만듭니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="set-the-subscription-context"></a>구독 컨텍스트 설정

Cloud Shell에서 명령을 실행하는 경우에는 다음 단계가 필요하지 않습니다. CLI를 로컬로 실행하는 경우 다음 단계를 수행하여 Azure에 로그인하고 현재 구독을 설정합니다.

현재 구독 컨텍스트를 설정합니다. `MyAzureSub`를 사용할 Azure 구독의 이름으로 바꿉니다.

```azurecli-interactive
az account set --subscription MyAzureSub
``` 

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
리소스 그룹은 Azure 리소스에 대한 논리적 컬렉션입니다. 모든 리소스는 리소스 그룹에서 배포 및 관리됩니다. 다음 명령을 실행하여 리소스 그룹을 만듭니다.

```azurecli-interactive
# Create a resource group. Specify a name for the resource group.
az group create --name <resource group name> --location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기
Event Hubs 네임스페이스는 정규화된 도메인 이름으로 참조되는 고유한 범위 지정 컨테이너를 제공하며, 하나 이상의 이벤트 허브를 만듭니다. 리소스 그룹에서 네임스페이스를 만들려면 다음 명령을 실행하세요.

```azurecli-interactive
# Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
```

## <a name="create-an-event-hub"></a>이벤트 허브 만들기
다음 명령을 실행하여 이벤트 허브를 만듭니다.

```azurecli-interactive
# Create an event hub. Specify a name for the event hub. 
az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
```

축하합니다! Azure CLI를 사용하여 Event Hubs 네임스페이스와, 그 네임스페이스 안에 이벤트 허브를 만들었습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 리소스 그룹, Event Hubs 네임스페이스 및 이벤트 허브를 만들었습니다. 이벤트 허브에서 이벤트를 보내거나 받기 위한 단계별 지침은 **이벤트 보내기 및 받기** 자습서를 참조하세요. 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C(보내기 전용)](event-hubs-c-getstarted-send.md)
- [Apache Storm(받기 전용)](event-hubs-storm-getstarted-receive.md)

[create a free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
