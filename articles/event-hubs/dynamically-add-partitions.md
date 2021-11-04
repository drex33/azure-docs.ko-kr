---
title: Azure Event Hubs에서 이벤트 허브에 동적으로 파티션 추가
description: 이 문서에서는 Azure Event Hubs에서 이벤트 허브에 파티션을 동적으로 추가하는 방법을 보여줍니다.
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fdd87e281bb4df98d118f7964eaa98925cef0947
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070682"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Azure Event Hubs에서 이벤트 허브(Apache Kafka 토픽)에 동적으로 파티션 추가
Event Hubs는 각 소비자만이 특정 하위 집합, 파티션 또는 메시지 스트림을 읽는 파티션된 소비자 패턴을 통해 메시지 스트리밍을 제공합니다. 이 패턴은 이벤트 처리를 위한 가로 눈금을 사용하며 큐 및 항목에 사용할 수 없는 기타 스트림 중심 기능을 제공합니다. 파티션은 Event Hub에서 보유하는 순서가 지정된 이벤트 시퀀스입니다. 최신 이벤트가 도착하면 이 시퀀스의 끝에 추가됩니다. 파티션에 대한 자세한 내용은 [파티션](event-hubs-scalability.md#partitions)을 참조하세요.

이벤트 허브를 만들 때 파티션 수를 지정할 수 있습니다. 일부 시나리오에서는 이벤트 허브가 생성된 후 파티션을 추가해야 할 수 있습니다. 이 문서에서는 기존 이벤트 허브에 파티션을 동적으로 추가하는 방법을 설명합니다. 

> [!IMPORTANT]
> 파티션의 동적 추가는 Event Hubs의 **프리미엄** 및 **전용** 계층에서만 사용할 수 있습니다. 

> [!NOTE]
> Apache Kafka 클라이언트의 경우 **이벤트 허브** 가 **Kafka 토픽** 에 매핑됩니다. Azure Event Hubs와 Apache Kafka 간의 추가 매핑은 [Kafka 및 Azure Event Hubs 개념 매핑](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)을 참조하세요.


## <a name="update-the-partition-count"></a>파티션 수 업데이트
이 섹션에서는 여러 가지 방법(PowerShell, CLI 등)으로 이벤트 허브의 파티션 수를 업데이트하는 방법을 보여줍니다.

### <a name="powershell"></a>PowerShell
[Set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub) PowerShell 명령을 사용하여 이벤트 허브에서 파티션을 업데이트합니다. 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>CLI
[`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub#az_eventhubs_eventhub_update) CLI 명령을 사용하여 이벤트 허브에서 파티션을 업데이트합니다. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Resource Manager 템플릿
Resource Manager 템플릿의 `partitionCount` 속성 값을 업데이트하고 템플릿을 다시 배포하여 리소스를 업데이트합니다. 

```json
    {
        "apiVersion": "2017-04-01",
        "type": "Microsoft.EventHub/namespaces/eventhubs",
        "name": "[concat(parameters('namespaceName'), '/', parameters('eventHubName'))]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[resourceId('Microsoft.EventHub/namespaces', parameters('namespaceName'))]"
        ],
        "properties": {
            "messageRetentionInDays": 7,
            "partitionCount": 12
        }
    }
```

### <a name="apache-kafka"></a>Apache Kafka
`AlterTopics` API(예: **kafka-topics** CLI 도구를 통해)를 사용하여 파티션 수를 늘립니다. 자세한 내용은 [Kafka 토픽 수정](http://kafka.apache.org/documentation/#basic_ops_modify_topic)을 참조하세요. 

## <a name="event-hubs-clients"></a>Azure Event Hubs 클라이언트
파티션 수가 이벤트 허브에서 업데이트될 때 Azure Event Hubs 클라이언트가 어떻게 동작하는지 살펴보겠습니다. 

기존 짝수 허브에 파티션을 추가하면 이벤트 허브 클라이언트는 서비스로부터 엔터티 메타데이터(엔터티는 이벤트 허브이고 메타 데이터는 파티션 정보임)가 변경되었음을 알리는 `MessagingException`을 받습니다. 클라이언트는 AMQP 링크를 자동으로 다시 연 다음, 변경된 메타데이터 정보를 선택합니다. 그러면 클라이언트가 정상적으로 작동합니다.

### <a name="senderproducer-clients"></a>발신자/생산자 클라이언트
Azure Event Hubs는 세 가지 발신자 옵션을 제공합니다.

- **파티션 발신자** – 이 시나리오에서 클라이언트는 파티션에 이벤트를 직접 보냅니다. 파티션은 식별 가능하고 이벤트를 파티션에 직접 보낼 수 있지만 이 패턴은 권장하지 않습니다. 파티션을 추가해도 이 시나리오에는 영향이 없습니다. 새로 추가된 파티션을 검색할 수 있도록 애플리케이션을 다시 시작하는 것이 좋습니다. 
- **파티션 키 발신자** – 이 시나리오에서는 클라이언트가 키와 함께 이벤트를 보내서 해당 키에 속하는 모든 이벤트가 동일한 파티션에 있도록 합니다. 이 경우 서비스는 키를 해시하고 해당 파티션으로 라우팅합니다. 파티션 수 업데이트를 수행하면 해싱 변경으로 인해 잘못된 순서 문제가 발생할 수 있습니다. 따라서 순서가 중요한 경우에는 파티션 수를 늘리기 전에 애플리케이션에서 기존 파티션의 모든 이벤트를 사용하는지 확인합니다.
- **라운드 로빈 발신자(기본값)** – 이 시나리오에서는 Azure Event Hubs 서비스가 파티션 간에 이벤트를 라운드 로빈하고 부하 분산 알고리즘도 사용합니다. Azure Event Hubs 서비스는 파티션 수 변경 내용을 인식하고 파티션 수를 변경한 후 몇 초 내에 새 파티션으로 보냅니다.

### <a name="receiverconsumer-clients"></a>수신자/소비자 클라이언트
Azure Event Hubs는 직접 수신자와 [이벤트 프로세서 호스트(이전 SDK)](event-hubs-event-processor-host.md) 또는 [이벤트 프로세서(새 SDK)](event-processor-balance-partition-load.md)라는 편리한 소비자 라이브러리를 제공합니다.

- **직접 수신자** – 직접 수신자는 특정 파티션을 수신 대기합니다. 파티션이 이벤트 허브에 대해 스케일 아웃될 때 직접 수신자의 런타임 동작은 영향을 받지 않습니다. 직접 수신자를 사용하는 애플리케이션이 새 파티션을 선택하고 그에 따라 수신자를 할당해야 합니다.
- **이벤트 프로세서 호스트** – 이 클라이언트는 엔터티 메타데이터를 자동으로 새로 고치지 않습니다. 따라서 파티션 수 증가를 선택하지 않습니다. 이벤트 프로세서 인스턴스를 다시 만들면 엔터티 메타데이터가 페치되고 새로 추가된 파티션의 새 BLOB이 만들어집니다. 기존 BLOB은 영향을 받지 않습니다. 모든 이벤트 프로세서 인스턴스를 다시 시작하여 모든 인스턴스가 새로 추가된 파티션을 인식하고 소비자 간에 부하 분산이 올바르게 처리되도록 하는 것이 좋습니다.

    이전 버전의 .NET SDK([WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/))를 사용하는 경우 검사점의 파티션 수가 서비스에서 페치된 파티션 수와 일치하지 않으면 재시작 시 이벤트 프로세서 호스트가 기존 검사점을 제거합니다. 이 동작은 애플리케이션에 영향을 줄 수 있습니다. 

## <a name="apache-kafka-clients"></a>Apache Kafka 클라이언트
이 섹션에서는 Azure Event Hubs의 Kafka 엔드포인트를 사용하는 Apache Kafka 클라이언트가 이벤트 허브에 대한 파티션 수가 업데이트될 때 어떻게 동작하는지 설명합니다. 

Apache Kafka 프로토콜과 함께 Azure Event Hubs를 사용하는 Kafka 클라이언트는 AMQP 프로토콜을 사용하는 이벤트 허브 클라이언트와 다르게 동작합니다. Kafka 클라이언트는 `metadata.max.age.ms`밀리초마다 한 번씩 메타데이터를 업데이트합니다. 클라이언트 구성에서 이 값을 지정합니다. `librdkafka` 라이브러리도 동일한 구성을 사용합니다. 메타데이터 업데이트는 파티션 수 증가 등의 서비스 변경 내용을 클라이언트에 알립니다. 구성 목록은 [Azure Event Hubs에 대한 Apache Kafka 구성](apache-kafka-configurations.md)을 참조하세요.

### <a name="senderproducer-clients"></a>발신자/생산자 클라이언트
생산자는 항상 보내기 요청에 생산된 각 레코드 집합에 대한 파티션 대상이 포함되도록 지시합니다. 따라서 모든 생산 분할은 생산자의 broker 메타데이터 보기를 사용하여 클라이언트 쪽에서 수행됩니다. 새 파티션이 생산자의 메타데이터 보기에 추가되면 생산자 요청에 사용할 수 있습니다.

### <a name="consumerreceiver-clients"></a>소비자/수신자 클라이언트
소비자 그룹 구성원이 메타데이터 새로 고침을 수행하고 새로 만든 파티션을 선택하면 해당 구성원이 그룹 균형 재조정을 시작합니다. 그런 다음, 모든 그룹 구성원에 대해 소비자 메타데이터가 새로 고쳐지고 할당된 균형 재조정 리더에 의해 새 파티션이 할당됩니다.

## <a name="recommendations"></a>권장 사항

- 생산자 응용 애플리케이션과 함께 파티션 키를 사용하고 키 해시를 사용하여 파티션에 순서를 지정하는 경우 동적으로 파티션을 추가하지 않는 것이 좋습니다. 

    > [!IMPORTANT]
    > 기존 데이터가 순서를 유지하지만 파티션 추가로 인해 파티션 수가 변경되면 해시된 메시지에 대해 파티션 해싱이 중단됩니다.
- 다음과 같은 경우에 기존 토픽 또는 이벤트 허브 인스턴스에 파티션을 추가하는 것이 좋습니다.
    - 기본 방법으로 이벤트를 보내는 경우
     - Kafka 기본 분할 전략(예 – Sticky Assignor 전략)


## <a name="next-steps"></a>다음 단계
파티션에 대한 자세한 내용은 [파티션](event-hubs-scalability.md#partitions)을 참조하세요.
