---
title: Azure Event Grid의 시스템 토픽
description: Azure Event Grid의 시스템 토픽을 설명합니다.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: 2c0bf2879ce2b137faf33f1ec00d456ab884ccf8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130035109"
---
# <a name="system-topics-in-azure-event-grid"></a>Azure Event Grid의 시스템 토픽
Event Grid의 시스템 토픽은 Azure Storage 및 Azure Event Hubs와 같은 Azure 서비스에서 게시한 하나 이상의 이벤트를 나타냅니다. 예를 들어 시스템 토픽은 **모든 Blob 이벤트** 또는 **특정 스토리지 계정** 에 대해 게시된 **Blob 만들기** 및 **Blob 삭제** 이벤트만 나타낼 수 있습니다. 이 예에서 Blob이 스토리지 계정에 업로드되면 Azure Storage 서비스는 Event Grid의 시스템 토픽에 **blob 만들기** 이벤트를 게시한 다음, 이벤트를 받고 처리하는 토픽의 [구독자](event-handlers.md)에 전달합니다. 

> [!NOTE] 
> Azure 서비스만 시스템 항목에 이벤트를 게시할 수 있습니다. 따라서 사용자 지정 토픽이나 도메인에 사용할 때와 마찬가지로 이벤트를 게시하는 데 사용할 수 있는 엔드포인트 또는 액세스 키를 얻지 못합니다.

## <a name="azure-services-that-support-system-topics"></a>시스템 토픽을 지원하는 Azure 서비스
다음은 시스템 토픽 만들기를 지원하는 Azure 서비스의 현재 목록입니다.

- [Azure API Management](event-schema-api-management.md)
- [Azure App Configuration](event-schema-app-configuration.md)
- [Azure App Service](event-schema-app-service.md)
- [Azure Blob Storage](event-schema-blob-storage.md)
- [Azure Communication Services](event-schema-communication-services.md) 
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Azure FarmBeats](event-schema-farmbeats.md)
- [Azure IoT Hub](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Azure Media Services](event-schema-media-services.md)
- [Azure Policy](./event-schema-policy.md)
- [Azure 리소스 그룹](event-schema-resource-groups.md)
- [Azure Service Bus](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Azure 구독](event-schema-subscriptions.md)
- [Azure Cache for Redis](event-schema-azure-cache.md)

## <a name="system-topics-as-azure-resources"></a>Azure 리소스로서의 시스템 토픽
이전에는 시스템 토픽이 암시적이었으며 단순성을 위해 노출되지 않았습니다. 이제 시스템 토픽이 Azure 리소스로 표시되고 다음과 같은 기능을 제공합니다.

- [Azure Portal에서 시스템 토픽 보기](create-view-manage-system-topics.md#view-all-system-topics)
- Azure Portal에서 시스템 토픽과 이벤트 구독의 Resource Manager 템플릿 내보내기
- [시스템 토픽의 진단 로그 설정](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-system-topic)
- 게시 및 제공 실패에 관한 경고 설정 

## <a name="lifecycle-of-system-topics"></a>시스템 토픽 수명 주기
다음 두 방법으로 시스템 토픽을 만들 수 있습니다. 

- [Azure 리소스의 이벤트 구독을 확장 리소스](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)로 만듭니다. 그러면 `<Azure resource name>-<GUID>` 형식의 이름을 사용하여 자동으로 시스템 토픽을 만듭니다. 해당 방식으로 만든 시스템 토픽은 토픽의 마지막 이벤트 구독이 삭제될 때 자동으로 삭제됩니다. 
- Azure 리소스의 시스템 토픽을 만든 다음, 해당 시스템 토픽의 이벤트 구독을 만듭니다. 이 방법을 사용하는 경우 시스템 토픽의 이름을 지정할 수 있습니다. 시스템 토픽은 마지막 이벤트 구독이 삭제될 때 자동으로 삭제되지 않습니다. 수동으로 삭제해야 합니다. 

    Azure Portal을 사용하는 경우 항상 이 방법을 사용합니다. [Azure 리소스의 **이벤트** 페이지](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage)를 사용하여 이벤트 구독을 만들 때 먼저 시스템 토픽을 만든 다음, 항목의 구독을 만듭니다. 먼저 [**Event Grid 시스템 토픽** 페이지](create-view-manage-system-topics.md#create-a-system-topic)를 사용하여 시스템 토픽을 명시적으로 만든 다음, 해당 토픽의 구독을 만들 수 있습니다. 

[CLI](create-view-manage-system-topics-cli.md), [REST](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate), [Azure Resource Manager 템플릿](create-view-manage-system-topics-arm.md)을 사용할 때 위의 방법 중 하나를 선택할 수 있습니다. 시스템 토픽을 만드는 최신 방법이므로 먼저 시스템 토픽을 생성한 다음, 토픽에 대한 구독을 만드는 것이 좋습니다.

### <a name="failure-to-create-system-topics"></a>시스템 항목을 만들지 못했습니다.
Event Grid 서비스에서 만들 수 없는 방식으로 Azure 정책을 설정한 경우 시스템 토픽 만들기에 실패합니다. 예를 들어, 구독에서 특정 종류의 리소스(예: Azure Storage, Azure Event Hubs 등)만 만들 수 있는 정책이 있을 수 있습니다. 

이러한 경우에는 이벤트 흐름 기능이 유지 됩니다. 그러나 시스템 항목의 메트릭 및 진단 기능을 사용할 수 없습니다.

이 기능이 필요한 경우 시스템 항목 유형의 리소스 만들기를 허용 하 고 [시스템 항목의 수명 주기](#lifecycle-of-system-topics) 섹션에 설명 된 대로 누락 된 시스템 항목을 만듭니다.

## <a name="location-and-resource-group-for-a-system-topic"></a>시스템 토픽의 위치 및 리소스 그룹
특정 지역/위치에 있는 Azure 이벤트 원본의 경우 시스템 토픽은 Azure 이벤트 원본과 같은 위치에 만듭니다. 예를 들어 미국 동부에서 Azure Blob Storage의 이벤트 구독을 만드는 경우 시스템 토픽은 미국 동부에서 만듭니다. Azure 구독, 리소스 그룹, Azure Maps 등의 글로벌 Azure 이벤트 원본의 경우, Event Grid는 **글로벌** 위치에 시스템 토픽을 만듭니다. 

일반적으로 시스템 토픽은 Azure 이벤트 원본이 있는 것과 같은 리소스 그룹에 만듭니다. Azure 구독 범위에서 만든 이벤트 구독의 경우, **미국 서부 2** 지역의 **Default-EventGrid** 리소스 그룹에 시스템 토픽을 만듭니다. 리소스 그룹이 없으면 시스템 토픽을 만들기 전에 Azure Event Grid에서 만듭니다. 

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- [Azure Portal을 사용하여 시스템 토픽을 만들고 보고 관리합니다](create-view-manage-system-topics.md).
- [Azure CLI를 사용하여 Event Grid 시스템 토픽 만들기, 보기 및 관리](create-view-manage-system-topics-cli.md)
- [Azure Resource Manager 템플릿을 사용하여 Event Grid 시스템 토픽 만들기](create-view-manage-system-topics-arm.md)
