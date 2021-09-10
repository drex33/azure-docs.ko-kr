---
title: Azure Cosmos DB에서 Cassandra API를 사용하여 탄력적으로 크기 조정
description: Azure Cosmos DB Cassandra API 계정의 크기를 조정하는 데 사용할 수 있는 옵션과 해당 장점/단점에 대해 알아봅니다.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: thvankra
ms.openlocfilehash: 9c68a12fe64120163170f1687194f54204c99c39
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566403"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Azure Cosmos DB Cassandra API 계정에서 탄력적으로 크기 조정
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Cassandra용 Azure Cosmos DB API의 탄력적 특성을 탐색하는 다양한 옵션이 있습니다. Azure Cosmos DB에서 효과적으로 크기를 조정하는 방법을 이해하려면 시스템의 성능 요구 사항을 고려하여 적절한 양의 요청 단위(RU/초)를 프로비저닝하는 방법을 이해하는 것이 중요합니다. 요청 단위에 대해 알아보려면 [요청 단위](../request-units.md) 문서를 참조하세요. 

Cassandra API의 경우 [.NET 및 Java SDK](./find-request-unit-charge-cassandra.md)를 사용하여 개별 쿼리에 대한 요청 단위 요금을 검색할 수 있습니다. 이는 서비스에서 프로비저닝해야 하는 RU/초의 양을 결정하는 데 유용합니다.

:::image type="content" source="../media/request-units/request-units.png" alt-text="데이터베이스 작업은 요청 단위를 사용함" border="false":::

## <a name="handling-rate-limiting-429-errors"></a>속도 제한 처리(429 오류)

클라이언트에서 프로비저닝한 용량보다 많은 리소스(RU/초)를 사용하는 경우 Azure Cosmos DB에서 속도 제한(429) 오류를 반환합니다. Azure Cosmos DB의 Cassandra API는 Cassandra 네이티브 프로토콜에서 이러한 예외를 오버로드된 오류로 변환합니다. 

시스템이 대기 시간을 인식하지 못하는 경우 재시도를 사용하여 처리량 속도 제한을 처리하는 것이 충분할 수 있습니다. 속도 제한을 투명하게 처리하는 방법은 Apache Cassandra Java 드라이버 [버전 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample) 및 [버전 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample-v4)에 대한 Java 코드 샘플을 참조하세요. 이러한 샘플은 Java에서 기본 [Cassandra 재시도 정책](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/)의 사용자 지정 버전을 구현합니다. [Spark 확장](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper)을 사용하여 속도 제한을 처리할 수도 있습니다. Spark를 사용하는 경우 [Spark 커넥터 처리량 구성 최적화](connect-spark-configuration.md#optimizing-spark-connector-throughput-configuration)에 대한 지침을 따라야 합니다.

## <a name="manage-scaling"></a>크기 조정 관리

대기 시간을 최소화해야 하는 경우 Cassandra API에는 크기 조정 관리 및 처리량 프로비저닝(RU)을 위한 다양한 옵션이 있습니다.

* [Azure Portal을 사용하여 수동으로](#use-azure-portal)
* [컨트롤 플레인 기능을 사용하여 프로그래밍 방식으로](#use-control-plane)
* [특정 SDK로 CQL 명령을 사용하여 프로그래밍 방식으로](#use-cql-queries)
* [자동 크기 조정을 사용하여 동적으로](#use-autoscale)

다음 섹션에서는 각 방법의 장점 및 단점을 설명합니다. 그런 다음, 시스템의 크기 조정 요구 사항, 전반적인 비용 및 솔루션에 대한 효율성 요구 사항을 균형 있게 조정하기 위한 최상의 전략을 결정할 수 있습니다.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>Azure Portal 사용

Azure Portal을 사용하여 Azure Cosmos DB Cassandra API 계정에서 리소스의 크기를 조정할 수 있습니다. 자세한 내용은 [컨테이너 및 데이터베이스의 처리량 프로비전](../set-throughput.md) 문서를 참조하세요. 이 문서에서는 Azure Portal의 [데이터베이스](../set-throughput.md#set-throughput-on-a-database) 또는 [컨테이너](../set-throughput.md#set-throughput-on-a-container) 수준에서 처리량을 설정하는 상대적인 이점에 대해 설명합니다. 이 문서에 언급된 "데이터베이스" 및 "컨테이너"라는 용어는 Cassandra API에 대해 각각 "keyspace" 및 "table"에 매핑됩니다.

이 방법의 장점은 데이터베이스에서 처리량 용량을 관리하는 간단한 턴키 방법이라는 것입니다. 그러나 대부분의 경우에서 크기 조정에 대한 접근 방식은 비용 효율적이면서도 성능이 뛰어난 특정 수준의 자동화가 필요할 수 있다는 것이 단점입니다. 다음 섹션에서는 관련 시나리오와 방법을 설명합니다.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>컨트롤 플레인

Cassandra용 Azure Cosmos DB의 API는 다양한 컨트롤 플레인 기능을 사용하여 프로그래밍 방식으로 처리량을 조정하는 기능을 제공합니다. 지침 및 샘플은 [Azure Resource Manager](templates-samples.md), [PowerShell](powershell-samples.md) 및 [Azure CLI](cli-samples.md) 문서를 참조하세요.

이 방법의 장점은 최대 작업 또는 낮은 작업 기간을 고려하여 타이머를 기반으로 리소스의 확장 또는 축소를 자동화할 수 있다는 것입니다. Azure Functions 및 PowerShell을 사용하여 이를 수행하는 방법은 [여기](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler)에서 샘플을 살펴보세요.

이 접근 방식의 단점은 예기치 않은 변경 크기 조정 요구 사항에 실시간으로 응답할 수 없다는 것입니다. 대신, 클라이언트/SDK 수준에서 또는 [자동 크기 조정](../provision-throughput-autoscale.md)을 사용하여 시스템에서 애플리케이션 컨텍스트를 활용해야 할 수 있습니다.

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>특정 SDK를 사용하여 CQL 쿼리 사용

지정된 데이터베이스 또는 컨테이너에 대해 [CQL ALTER 명령](cassandra-support.md#keyspace-and-table-options)을 실행하여 코드를 사용하여 시스템을 동적으로 크기 조정할 수 있습니다.

이 접근 방식의 장점은 애플리케이션에 적합한 사용자 지정 방식으로 크기 조정 요구 사항에 대응할 수 있다는 점입니다. 이 접근 방식에서는 표준 RU/초 요금 및 속도를 계속 활용할 수 있습니다. 시스템의 크기 조정 요구 사항이 대부분 예측 가능(70% 이상)한 경우 CQL과 함께 SDK를 사용하면 자동 크기 조정을 사용하는 것보다 비용 효율적인 자동 크기 조정 방법이 될 수 있습니다. 이 접근 방식의 단점은 재시도를 구현하는 것이 매우 복잡할 수 있으며 속도 제한으로 대기 시간이 길어질 수 있다는 것입니다.

## <a name="use-autoscale-provisioned-throughput"></a><a id="use-autoscale"></a>자동 크기 조정 프로비저닝된 처리량 사용

표준(수동) 또는 프로그래밍 방식으로 처리량을 프로비저닝하는 것 외에도 자동 크기 조정 프로비저닝된 처리량에서 Azure cosmos 컨테이너를 구성할 수도 있습니다. 자동 크기 조정은 SLA를 손상시키지 않고 지정된 RU 범위 내에서 사용 요구 사항에 맞게 자동으로 즉시 크기 조정됩니다. 자세히 알아보려면 [자동 크기 조정에서 Azure Cosmos 컨테이너 및 데이터베이스 만들기](../provision-throughput-autoscale.md) 문서를 참조하세요.

이 접근 방식의 장점은 시스템에서 크기 조정 요구 사항을 관리하는 가장 쉬운 방법이라는 점입니다. 이는 **구성된 RU 범위 내에서** 속도 제한을 적용하지 않습니다. 단점은 시스템에서 크기 조정 요구 사항을 예측할 수 있는 경우 자동 크기 조정이 위에서 언급한 맞춤식 컨트롤 플레인이나 SDK 수준 접근 방식을 사용하는 것보다 크기 조정 요구 사항을 처리하는 데 비용 효율성이 떨어지는 방법이 될 수 있다는 점입니다.

CQL을 사용하여 자동 크기 조정에 대한 최대 처리량(RU)을 설정하거나 변경하려면 다음을 사용합니다(keyspace/table 이름을 적절하게 바꾸기).

```Bash
# to set max throughput (RUs) for autoscale at keyspace level:
create keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at keyspace level:
alter keyspace <keyspace name> WITH cosmosdb_autoscale_max_throughput=4000;

# to set max throughput (RUs) for autoscale at table level:
create table <keyspace name>.<table name> (pk int PRIMARY KEY, ck int) WITH cosmosdb_autoscale_max_throughput=5000;

# to alter max throughput (RUs) for autoscale at table level:
alter table <keyspace name>.<table name> WITH cosmosdb_autoscale_max_throughput=4000;
```

## <a name="next-steps"></a>다음 단계

- Java 애플리케이션을 사용하여 [Cassandra API 계정, 데이터베이스 및 테이블 만들기](create-account-java.md)를 시작합니다.