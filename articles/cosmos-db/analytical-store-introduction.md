---
title: Azure Cosmos DB 분석 저장소란?
description: Microsoft Azure Cosmos DB 트랜잭션(행 기반) 및 분석(열 기반) 저장소에 대해 알아봅니다. 분석 저장소의 이점, 대규모 워크로드 성능에 미치는 영향 및 트랜잭션 저장소에서 분석 저장소로 데이터 자동 동기화
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rosouz
ms.custom: seo-nov-2020
ms.openlocfilehash: 3ca9f06158fdcb13107c8b27a280dbdf037ea200
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133543335"
---
# <a name="what-is-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 분석 저장소란?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Microsoft Azure Cosmos DB 분석 저장소는 트랜잭션 워크로드에 영향을 주지 않고 Microsoft Azure Cosmos DB의 작동 데이터에 대한 대규모 분석을 사용할 수 있게 하는 완전히 격리된 열 저장소입니다. 

Microsoft Azure Cosmos DB 트랜잭션 저장소는 스키마에 구애받지 않으며 스키마나 인덱스 관리를 처리할 필요 없이 트랜잭션 애플리케이션을 반복할 수 있습니다. 이와 달리 Microsoft Azure Cosmos DB 분석 저장소는 분석 쿼리 성능을 최적화하도록 스키마화됩니다. 이 문서에서는 분석 스토리지에 대해 자세히 설명합니다.

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>작동 데이터의 대규모 분석 문제

Microsoft Azure Cosmos DB 컨테이너의 다중 모델 작동 데이터는 인덱싱된 행 기반 "트랜잭션 저장소"에 내부적으로 저장됩니다. 행 저장소 형식은 밀리초 단위의 응답 시간과 운영 쿼리를 통해 빠른 트랜잭션 읽기 및 쓰기를 지원하도록 설계되었습니다. 데이터 세트가 커지면 이 형식으로 저장된 데이터에 대한 프로비저닝된 처리량 측면에서 복잡한 분석 쿼리에 많은 비용이 들 수 있습니다. 프로비저닝된 처리량의 소비량이 많으면 실시간 애플리케이션과 서비스에서 사용하는 트랜잭션 워크로드의 성능에 영향이 있습니다.

일반적으로 많은 양의 데이터를 분석하기 위해 Microsoft Azure Cosmos DB의 트랜잭션 저장소에서 작동 데이터가 추출되어 별도의 데이터 계층에 저장됩니다. 예를 들어, 데이터는 데이터 웨어하우스나 데이터 레이크에 적절한 형식으로 저장됩니다. 이 데이터는 나중에 대규모 분석에 사용되며 Apache Spark 클러스터와 같은 컴퓨팅 엔진을 사용하여 분석됩니다. 트랜잭션 워크로드에 미치는 잠재적 영향을 최소화하기 위해 ETL(추출, 변환 및 로드) 파이프라인이 덜 자주 실행되기 때문에 작동 데이터에서 분석 스토리지와 컴퓨팅 레이어를 이렇게 분리하면 대기 시간이 증가합니다.

또한 새로 수집된 작동 데이터만 처리할 때보다 작동 데이터의 업데이트를 처리할 때 ETL 파이프라인이 복잡해집니다. 

## <a name="column-oriented-analytical-store"></a>열 기반 분석 저장소

Microsoft Azure Cosmos DB 분석 저장소는 기존의 ETL 파이프라인에서 발생하는 복잡성과 대기 시간 문제를 해결합니다. Microsoft Azure Cosmos DB 분석 저장소는 작동 데이터를 별도의 열 저장소로 자동 동기화할 수 있습니다. 열 저장소 형식은 대규모 분석 쿼리를 최적화된 방식으로 수행하는 데 적합합니다. 따라서 열 저장소 형식을 사용하면 대규모 분석 쿼리의 대기 시간이 향상됩니다.

이제 Azure Synapse Link로 Azure Synapse Analytics에서 Microsoft Azure Cosmos DB 분석 저장소에 직접 연결하여 비 ETL HTAP 솔루션을 구축할 수 있습니다. 이를 통해 작동 데이터에 대해 거의 실시간으로 대규모 분석을 실행할 수 있습니다.

## <a name="features-of-analytical-store"></a>분석 저장소의 기능 

Microsoft Azure Cosmos DB 컨테이너에서 분석 저장소를 사용하도록 설정하면 컨테이너의 작동 데이터를 기반으로 새 열 저장소가 내부적으로 만들어집니다. 이 열 저장소는 해당 컨테이너의 행 기반 트랜잭션 저장소와 별도로 유지됩니다. 작동 데이터의 삽입, 업데이트 및 삭제는 분석 저장소에 자동으로 동기화됩니다. 데이터를 동기화하는 데 변경 피드나 ETL이 필요하지 않습니다.

## <a name="column-store-for-analytical-workloads-on-operational-data"></a>작동 데이터에 대한 분석 워크로드의 열 저장소

분석 워크로드에는 일반적으로 선택된 필드의 집계와 순차 검사가 포함됩니다. 데이터를 열 우선 순서로 저장하면 분석 저장소에서 각 필드의 값 그룹을 함께 직렬화할 수 있습니다. 이 형식은 특정 필드에 대한 통계를 검사하거나 계산하는 데 필요한 IOPS를 줄입니다. 이를 통해 큰 데이터 세트 검사를 위한 쿼리 응답 시간이 크게 향상됩니다. 

예를 들어, 운영 테이블이 다음과 같은 형식입니다.

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="운영 테이블 예" border="false":::

행 저장소는 디스크에 행별로 직렬화된 형식으로 위의 데이터를 저장합니다. 이 형식을 사용하면 트랜잭션 읽기와 쓰기 속도는 물론 "Product1에 대한 정보 반환"과 같은 운영 쿼리 속도를 높일 수 있습니다. 그러나 데이터 세트가 커지고 데이터에 대해 복잡한 분석 쿼리를 실행하려는 경우 비용이 많이 들 수 있습니다. 예를 들어, "여러 사업부와 월에 걸쳐 '장비'라는 범주에 속하는 제품의 판매 추세"를 알아보려면 복잡한 쿼리를 실행해야 합니다. 이 데이터 세트에 대한 대규모 검사는 프로비저닝된 처리량 측면에서 비용이 많이 들 수 있으며 실시간 애플리케이션 및 서비스를 지원하는 트랜잭션 워크로드의 성능에 영향을 줄 수도 있습니다.

열 저장소인 분석 저장소가 유사한 데이터 필드를 함께 직렬화하고 디스크 IOPS를 줄이므로 이러한 쿼리에 더 적합합니다.

다음 이미지에서는 Microsoft Azure Cosmos DB의 트랜잭션 행 저장소와 분석 열 저장소를 비교하여 보여줍니다.

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Microsoft Azure Cosmos DB의 트랜잭션 행 저장소와 분석 열 저장소" border="false":::

## <a name="decoupled-performance-for-analytical-workloads"></a>분석 워크로드를 위한 분리된 성능

분석 저장소는 트랜잭션 저장소와 별개이므로 분석 쿼리가 트랜잭션 워크로드의 성능에 영향을 미치지 않습니다.  분석 저장소에 별도의 RU(요청 단위)를 할당할 필요가 없습니다.

## <a name="auto-sync"></a>자동 동기화

자동 동기화는 작동 데이터의 삽입, 업데이트 및 삭제가 거의 실시간으로 트랜잭션 저장소에서 분석 저장소로 자동 동기화되는 Microsoft Azure Cosmos DB의 완전 관리형 기능입니다. 자동 동기화 대기 시간은 일반적으로 2분 이내입니다. 컨테이너가 많은 공유 처리량 데이터베이스의 경우 개별 컨테이너의 자동 동기화 대기 시간이 더 길어질 수 있으며 최대 5분이 걸릴 수 있습니다. 이 대기 시간이 시나리오에 얼마나 적합한지 자세히 알아보세요. 이에 대해서는 [Azure Cosmos DB 팀](mailto:cosmosdbsynapselink@microsoft.com)에 문의하세요.

자동 동기화 프로세스 실행이 끝날 때마다 Azure Synapse Analytics 런타임에서 트랜잭션 데이터를 즉시 사용할 수 있습니다.

* Azure Synapse Analytics Spark 풀은 항상 데이터의 마지막 상태를 읽는 `spark.read` 명령을 통해 또는 자동으로 업데이트되는 Spark 테이블을 사용하여 최근 업데이트를 비롯한 모든 데이터를 읽을 수 있습니다.

*  Azure Synapse Analytics SQL 서버리스 풀은 항상 데이터의 최신 상태를 읽는 `SELECT` 및 ` OPENROWSET` 명령을 통해 또는 자동으로 업데이트되는 뷰를 사용하여 최근 업데이트를 비롯한 모든 데이터를 읽을 수 있습니다.

> [!NOTE]
> 트랜잭션 TTL이 2분 미만인 경우에도 트랜잭션 데이터가 분석 저장소에 동기화됩니다. 

## <a name="scalability--elasticity"></a>확장성 및 탄력성

Microsoft Azure Cosmos DB 트랜잭션 저장소는 행 분할을 사용하여 가동 중지 시간 없이 스토리지와 처리량을 탄력적으로 스케일링할 수 있습니다. 트랜잭션 저장소의 행 분할은 자동 동기화의 확장성과 탄력성을 제공하여 데이터가 분석 저장소에 거의 실시간으로 동기화되도록 합니다. 데이터 동기화는 트랜잭션 트래픽 처리량이 초당 작업 1,000개인지 아니면 초당 작업 100만개인지와 무관하게 수행되며 트랜잭션 저장소의 프로비저닝된 처리량에 영향을 주지 않습니다. 

## <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>스키마 업데이트 자동 처리

Microsoft Azure Cosmos DB 트랜잭션 저장소는 스키마에 구애받지 않으며 스키마나 인덱스 관리를 처리할 필요 없이 트랜잭션 애플리케이션을 반복할 수 있습니다. 이와 달리 Microsoft Azure Cosmos DB 분석 저장소는 분석 쿼리 성능을 최적화하도록 스키마화됩니다. 자동 동기화 기능을 통해 Azure Cosmos DB는 트랜잭션 저장소의 최신 업데이트에 대한 스키마 유추를 관리합니다. 또한 중첩된 데이터 형식 처리를 포함하여 기본적으로 분석 저장소에서 스키마 표현을 관리합니다.

스키마가 진화하고 시간이 지남에 따라 새 속성이 추가되면 분석 저장소는 트랜잭션 저장소의 모든 기록 스키마에서 통합된 스키마를 자동으로 표시합니다.

> [!NOTE]
> 분석 저장소 컨텍스트에서는 다음 구조를 속성으로 간주합니다.
> * JSON “요소” 또는 “`:`으로 구분된 문자열-값 쌍”
> * `{` 및 `}`로 구분된 JSON 개체
> * `[` 및 `]`로 구분된 JSON 배열


### <a name="schema-constraints"></a>스키마 제약 조건

다음 제약 조건은 분석 저장소가 스키마를 자동으로 유추하고 올바르게 표현하도록 설정할 때 Azure Cosmos DB의 작동 데이터에 적용됩니다.

* 스키마의 모든 중첩 수준에는 최대 1000개의 속성을 사용할 수 있으며 최대 중첩 깊이는 127입니다.
  * 처음 1000개의 속성만 분석 저장소에 표시됩니다.
  * 처음 127개의 중첩된 수준만 분석 저장소에 표시됩니다.
  * JSON 문서의 첫 번째 수준은 `/` 루트 수준입니다.
  * 문서의 첫 번째 수준에 있는 속성은 열로 표시됩니다.


* 샘플 시나리오:
  * 문서의 첫 번째 수준에 2,000개의 속성이 포함된 경우 처음 1,000개만 표시됩니다.
  * 문서에 각각 200개의 속성이 포함된 5개 수준이 있는 경우 모든 속성이 표시됩니다.
  * 문서에 각각 400개의 속성이 포함된 10개 수준이 있는 경우 처음 2개 수준만 분석 저장소에 완전히 표시됩니다. 세 번째 수준도 절반이 표시됩니다.

* 아래 가상 문서에는 속성 4개와 수준 3개가 포함되어 있습니다.
  * 수준은 `root`, `myArray`, `myArray` 내의 중첩 구조입니다.
  * 속성은 `id`, `myArray`, `myArray.nested1`, `myArray.nested2`입니다.
  * 분석 저장소 표시에는 `id`와 `myArray`라는 2개의 열이 있습니다. Spark 또는 T-SQL 함수를 사용하여 중첩 구조를 열로 노출할 수도 있습니다.


```json
{
  "id": "1",
  "myArray": [
    "string1",
    "string2",
    {
      "nested1": "abc",
      "nested2": "cde"
    }
  ]
}
```

* JSON 문서(및 Cosmos DB 컬렉션/컨테이너)는 고유성 관점에서 대/소문자를 구분하지만 분석 저장소는 그렇지 않습니다.

  * **동일한 문서에서:** 동일한 수준의 속성 이름은 대/소문자를 구분하지 않고 비교할 때 고유해야 합니다. 예를 들어 다음 JSON 문서는 동일한 수준에 "Name"과 "name"이 있습니다. 유효한 JSON 문서이지만 고유성 제약 조건을 충족하지 않으므로 분석 저장소에 완전히 표시되지 않습니다. 이 예에서 "Name"과 "name"은 대/소문자를 구분하지 않고 비교할 때 동일합니다. `"Name": "fred"`가 첫 번째 발생 항목이므로 이 항목만 분석 저장소에 표시됩니다. 그리고 `"name": "john"`은 전혀 표시되지 않습니다.
  
  
  ```json
  {"id": 1, "Name": "fred", "name": "john"}
  ```
  
  * **다른 문서에서:** 동일한 수준에 있고 이름은 같지만 대/소문자가 다른 속성은 첫 번째 발생 항목의 이름 형식을 사용하여 동일한 열에 표시됩니다. 예를 들어 다음 JSON 문서는 동일한 수준에 `"Name"` 및 `"name"`이 있습니다. 첫 번째 문서 형식은 `"Name"`이므로 분석 저장소에서 이 속성 이름을 나타내는 데 사용됩니다. 즉, 분석 저장소의 열 이름은 `"Name"`입니다. `"fred"` 및 `"john"` 모두 `"Name"` 열에 표시됩니다.


  ```json
  {"id": 1, "Name": "fred"}
  {"id": 2, "name": "john"}
  ```


* 컬렉션의 첫 번째 문서는 초기 분석 저장소 스키마를 정의합니다.
  * 초기 스키마보다 속성이 더 많은 문서는 분석 저장소에 새 열을 생성합니다.
  * 열은 제거할 수 없습니다.
  * 컬렉션의 모든 문서를 삭제해도 분석 저장소 스키마가 다시 설정되지는 않습니다.
  * 스키마의 버전은 관리되지 않습니다. 트랜잭션 저장소에서 유추된 마지막 버전을 분석 저장소에서 볼 수 있습니다.

* 현재 Azure Synapse Spark는 아래에 나열된 이름에 특수 문자가 포함된 속성을 읽을 수 없습니다. Azure Synapse SQL 서버리스는 영향을 받지 않습니다.
  * :(콜론)
  * `(억음 악센트 기호)
  * ,(쉼표)
  * ;(세미콜론)
  * {}
  * ()
  * \n
  * \t
  * =(등호)
  * "(따옴표)

> [!NOTE]
> 이 제한에 도달하면 반환되는 Spark 오류 메시지에도 공백이 나열됩니다. 그러나 공백에 대한 특별한 처리를 추가했습니다. 아래 항목에서 자세한 내용을 확인하세요.
 
* 위에 나열된 문자를 사용하는 속성 이름이 있는 경우 대안은 다음과 같습니다.
   * 이러한 문자를 방지하려면 데이터 모델을 미리 변경합니다.
   * 현재 스키마 재설정을 지원하지 않기 때문에 애플리케이션을 변경하여 유사한 이름의 중복 속성을 추가하여 이러한 문자를 방지할 수 있습니다.
   * 변경 피드를 사용하여 속성 이름에 이러한 문자 없이 컨테이너의 구체화된 뷰를 만듭니다.
   * Spark 옵션을 사용하여 `dropColumn` 영향을 받는 열을 무시하고 다른 모든 열을 DataFrame에 로드합니다. 구문은 다음과 같습니다.

```Python
# Removing one column:
df = spark.read\
     .format("cosmos.olap")\
     .option("spark.synapse.linkedService","<your-linked-service-name>")\
     .option("spark.synapse.container","<your-container-name>")\
     .option("spark.synapse.dropColumn","FirstName,LastName")\
     .load()
     
# Removing multiple columns:
df = spark.read\
     .format("cosmos.olap")\
     .option("spark.synapse.linkedService","<your-linked-service-name>")\
     .option("spark.synapse.container","<your-container-name>")\
     .option("spark.synapse.dropColumn","FirstName,LastName;StreetName,StreetNumber")\
     .option("spark.cosmos.dropMultiColumnSeparator", ";")\
     .load()  
```

* Azure Synapse Spark는 이제 이름에 공백이 있는 속성을 지원합니다. 이를 위해 Spark 옵션을 사용하여 `allowWhiteSpaceInFieldNames` 영향을 받는 열을 DataFrame에 로드하고 원래 이름을 유지해야 합니다. 구문은 다음과 같습니다.

```Python
df = spark.read\
     .format("cosmos.olap")\
     .option("spark.synapse.linkedService","<your-linked-service-name>")\
     .option("spark.synapse.container","<your-container-name>")\
     .option("spark.cosmos.allowWhiteSpaceInFieldNames", "true")\
    .load()
```

* 다음 BSON 데이터 형식은 지원되지 않으며 분석 저장소에 표시되지 않습니다.
  * Decimal128
  * 정규식
  * DB 포인터
  * JavaScript
  * 기호
  * MinKey / MaxKey 

* ISO 8601 UTC 표준을 따르는 DateTime 문자열을 사용하는 경우 다음과 같은 동작이 예상됩니다.
  * Azure Synapse의 Spark 풀은 이러한 열을 `string`로 나타냅니다.
  * Azure Synapse의 SQL 서버리스 풀은 이러한 열을 `varchar(8000)`로 나타냅니다.

* Azure Synapse SQL 서버리스 풀은 최대 1000개의 열이 있는 결과 집합을 지원하며 중첩 열을 노출하는 것도 해당 제한에 포함됩니다. 데이터 아키텍처를 디자인하고 트랜잭션 데이터를 모델링할 때 이 정보를 고려하세요.


### <a name="schema-representation"></a>스키마 표현

분석 저장소에는 두 가지 유형의 스키마 표현이 있습니다. 이러한 형식은 데이터베이스 계정의 모든 컨테이너에 대한 스키마 표현 메서드를 정의하며, 쿼리 환경의 단순성과 다형 스키마에 대한 보다 포괄적인 열 형식 표현의 편의성 간에 장단점이 있습니다.

* 잘 정의된 스키마 표현(SQL(CORE) API 계정에 대한 기본 옵션) 
* 전체 충실도 스키마 표현(Azure Cosmos DB API for MongoDB 계정의 기본 옵션)

#### <a name="full-fidelity-schema-for-sql-api-accounts"></a>SQL API 계정에 대한 전체 충실도 스키마

Cosmos DB 계정에서 처음으로 Synapse Link 사용하도록 설정할 때 스키마 유형을 설정하여 기본 옵션 대신 SQL(Core) API 계정에 대해 전체 충실도 스키마를 사용할 수 있습니다. 기본 스키마 표현 형식 변경에 대한 고려 사항은 다음과 같습니다.

 * 이 옵션은 Synapse Link 사용하도록 **설정되지 않은** 계정에만 유효합니다.
 * 스키마 표현 형식을 잘 정의된 형식에서 전체 충실도로 또는 그 반대로 다시 설정할 수 없습니다.
 * 현재 Azure Cosmos DB API for MongoDB 계정은 이 스키마 표현 변경 가능성과 호환되지 않습니다. 모든 MongoDB 계정에는 항상 전체 충실도 스키마 표현 유형이 있습니다.
 * 현재 이 변경은 Azure Portal 통해 만들 수 없습니다. Azure Portal Synapse LinK를 사용하도록 설정한 모든 데이터베이스 계정에는 기본 스키마 표현 유형과 잘 정의된 스키마가 있습니다.
 
Azure CLI 또는 PowerShell을 사용하여 계정에서 Synapse Link 사용하도록 설정하는 동시에 스키마 표현 유형을 결정해야 합니다.
 
 Azure CLI 사용:
 ```cli
 az cosmosdb create --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --subscription MySubscription --analytical-storage-schema-type "FullFidelity" --enable-analytical-storage true
 ```
 
> [!NOTE]
> 위의 명령에서 기존 계정에 대해 `create`를 `update`로 바꿉니다.
 
  PowerShell 사용:
  ```
   New-AzCosmosDBAccount -ResourceGroupName MyResourceGroup -Name MyCosmosDBDatabaseAccount  -EnableAnalyticalStorage true -AnalyticalStorageSchemaType "FullFidelity"
   ```
 
> [!NOTE]
> 위의 명령에서 `New-AzCosmosDBAccount`를 기존 계정에 대한 `Update-AzCosmosDBAccount`로 바꿉니다.
 


#### <a name="well-defined-schema-representation"></a>올바르게 정의된 스키마 표시

잘 정의된 스키마 표시는 트랜잭션 저장소에서 스키마와 관계없는 데이터의 간단한 테이블 형식 표시를 만듭니다. 잘 정의된 스키마 표시의 고려 사항은 다음과 같습니다.

* 첫 번째 문서는 기본 스키마를 정의하고 속성은 항상 모든 문서에서 동일한 형식이어야 합니다. 유일한 예외는 다음과 같습니다.
  * Null에서 다른 데이터 형식 변경. Null이 아닌 첫 번째 발생이 열 데이터 형식을 정의합니다. 첫 번째 null이 아닌 데이터 형식을 따르지 않는 문서는 분석 저장소에 표시되지 않습니다.
  * `float`에서 `integer`로 모든 문서는 분석 저장소에 표시됩니다.
  * `integer`에서 `float`로 모든 문서는 분석 저장소에 표시됩니다. 그러나 Azure Synapse SQL 서버리스 풀에서 이 데이터를 읽으려면 WITH 절을 사용하여 열을 `varchar`로 변환해야 합니다. 또한 이 초기 변환 후에는 다시 숫자로 변환할 수 있습니다. **num** 초기 값이 정수이고 두 번째 값이 float인 아래 예제를 확인하세요.

```SQL
SELECT CAST (num as float) as num
FROM OPENROWSET(PROVIDER = 'CosmosDB',
                CONNECTION = '<your-connection',
                OBJECT = 'IntToFloat',
                SERVER_CREDENTIAL = 'your-credential'
) 
WITH (num varchar(100)) AS [IntToFloat]
```

  * 기본 스키마 데이터 형식을 따르지 않는 속성은 분석 저장소에 표시되지 않습니다. 예를 들어 아래의 두 문서를 고려해보세요. 첫 번째 문서는 분석 저장소 기본 스키마를 정의합니다. 두 번째 문서(`id`가 `2`)는 속성 `"a"`가 문자열이고 첫 번째 문서에 `"a"`가 숫자로 지정되어 있으므로 잘 정의된 스키마가 없습니다. 이 경우 분석 저장소는 컨테이너 수명 동안 `"a"`의 데이터 형식을 `integer`로 등록합니다. 두 번째 문서는 분석 저장소에 계속 포함되지만 해당 `"a"` 속성은 포함되지 않습니다.
  
    * `{"id": "1", "a":123}` 
    * `{"id": "2", "a": "str"}`
     
 > [!NOTE]
 > null 속성에는 이 조건이 적용되지 않습니다. 예를 들어 `{"a":123} and {"a":null}`은 여전히 잘 정의되어 있습니다.

* 배열 형식에는 단일 형식이 반복되어 포함되어야 합니다. 예를 들어 배열에 정수 형식과 문자열 형식이 혼합되어 있기 때문에 `{"a": ["str",12]}`는 잘 정의된 스키마가 아닙니다.

> [!NOTE]
> Azure Cosmos DB 분석 저장소가 잘 정의된 스키마 표현을 따르고 특정 항목이 위의 사양을 위반하는 경우 해당 항목은 분석 저장소에 포함되지 않습니다.

* 잘 정의된 스키마에서 다른 유형과 관련하여 다른 동작이 발생합니다.
  * Azure Synapse의 Spark 풀은 이러한 값을 `undefined`로 나타냅니다.
  * Azure Synapse의 SQL 서버리스 풀은 이러한 값을 `NULL`로 나타냅니다.

* 명시적 `null` 값과 관련하여 다른 동작이 발생합니다.
  * Azure Synapse의 Spark 풀은 이러한 값을 `0`(영)으로 읽습니다. 열에 Null이 아닌 값이 포함되는 즉시 `undefined`로 변경됩니다.
  * Azure Synapse의 SQL 서버리스 풀은 해당 값을 `NULL`로 읽습니다.
    
* 누락된 열과 관련하여 다른 동작이 발생합니다.
  * Azure Synapse의 Spark 풀은 이러한 열을 `undefined`로 나타냅니다.
  * Azure Synapse의 SQL 서버리스 풀은 이러한 열을 `NULL`로 나타냅니다.


#### <a name="full-fidelity-schema-representation"></a>전체 충실도 스키마 표시

전체 충실도 스키마 표시는 스키마와 관계없는 작동 데이터에서 전체 다형성 스키마를 처리하도록 설계되었습니다. 이 스키마 표시에서는 잘 정의된 스키마 제약 조건(혼합 데이터 형식 필드도 혼합 데이터 형식 배열도 아님)을 위반하더라도 분석 저장소에서 항목이 삭제되지 않습니다.

이는 작동 데이터의 리프 속성을 속성의 값 데이터 형식에 따라 고유한 열이 있는 분석 저장소로 변환하여 수행됩니다. 리프 속성 이름은 모호성 없이 쿼리할 수 있도록 분석 저장소 스키마에서 데이터 형식을 접미사로 사용하여 확장됩니다.

전체 충실도 스키마 표시에서 각 속성의 각 데이터 형식은 해당 데이터 형식의 열을 생성합니다. 각 속성은 최대 1,000개 속성 중 하나로 계산됩니다.

예를 들어 트랜잭션 저장소에서 다음 예제 문서를 사용해 보겠습니다.

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

중첩된 오브젝트 `address` 내의 리프 속성 `streetNo`는 분석 저장소 스키마에서 `address.object.streetNo.int32` 열로 표시됩니다. 데이터 형식이 열에 접미사로 추가됩니다. 이러한 방식으로 리프 속성 `streetNo`의 값이 "123"(문자열임)인 트랜잭션 저장소에 다른 문서가 추가되는 경우 분석 저장소의 스키마가 이전에 작성된 열의 형식을 변경하지 않아도 자동으로 전개됩니다. 이 값 "123"이 저장되는 새 열이 `address.object.streetNo.string`으로 분석 저장소에 추가되었습니다.

**데이터 형식과 접미사 맵**

다음은 분석 저장소에 있는 모든 속성 데이터 형식 및 해당 접미사 표시에 대한 맵입니다.

|원래 데이터 형식  |접미사  |예제  |
|---------|---------|---------|
| Double |  ".float64" |    24.99|
| Array | ".array" |    ["a", "b"]|
|이진 | ".binary" |0|
|부울    | ".bool"   |True|
|Int32  | ".int32"  |123|
|Int64  | ".int64"  |255486129307|
|Null   | ".null"   | null|
|String|    ".string" | "ABC"|
|타임스탬프 |    ".timestamp" |  Timestamp(0, 0)|
|DateTime   |".date"    | ISODate("2020-08-21T07:43:07.375Z")|
|ObjectId   |".objectId"    | ObjectId("5f3f7b59330ec25c132623a2")|
|문서   |".object" |    {"a": "a"}|

* 명시적 `null` 값과 관련하여 다른 동작이 발생합니다.
  * Azure Synapse의 Spark 풀은 이러한 값을 `0`(영)으로 읽습니다.
  * Azure Synapse의 SQL 서버리스 풀은 해당 값을 `NULL`로 읽습니다.
  
* 누락된 열과 관련하여 다른 동작이 발생합니다.
  * Azure Synapse의 Spark 풀은 이러한 열을 `undefined`로 나타냅니다.
  * Azure Synapse의 SQL 서버리스 풀은 이러한 열을 `NULL`로 나타냅니다.

## <a name="cost-effective-archival-of-historical-data"></a>비용 효율적으로 기록 데이터 보관

데이터 계층화는 서로 다른 시나리오에 최적화된 스토리지 인프라 간에 데이터를 분리하는 것을 말합니다. 그러면 엔드투엔드 데이터 스택의 전반적인 성능과 비용 효율성이 향상됩니다. Microsoft Azure Cosmos DB는 이제 분석 저장소를 사용하여 다른 데이터 레이아웃을 가진 트랜잭션 저장소에서 분석 저장소로의 데이터 자동 계층화를 지원합니다. 트랜잭션 저장소와 비교하여 스토리지 비용 측면에서 최적화된 분석 저장소를 사용하면 기록 분석을 위해 훨씬 더 긴 기간의 작동 데이터를 보존할 수 있습니다.

트랜잭션 저장소의 데이터 보존 요구 사항에 따라 분석 저장소를 사용하도록 설정한 후 특정 기간이 지나면 트랜잭션 저장소에서 레코드가 자동으로 삭제되도록 '트랜잭션 저장소 TTL(트랜잭션 TTL(Time to Live))' 속성을 구성할 수 있습니다. 마찬가지로 '분석 저장소 TTL(분석 TTL)'을 사용하면 트랜잭션 저장소와는 독립적으로 분석 저장소에 보존되는 데이터의 수명 주기를 관리할 수 있습니다. 분석 저장소를 사용하도록 설정하고 TTL 속성을 구성하여 두 저장소의 데이터 보존 기간을 원활하게 계층화하고 정의할 수 있습니다.

> [!NOTE]
>현재 분석 저장소는 백업 및 복원을 지원하지 않습니다. 분석 저장소를 사용하여 백업 정책을 계획할 수 없습니다. 자세한 내용은 [이](synapse-link.md#limitations) 문서의 제한 사항 섹션을 참조하세요. 분석 저장소의 데이터는 트랜잭션 저장소에 있는 데이터와 다른 스키마를 사용한다는 것에 유의하세요. RU 비용 없이 분석 저장소 데이터의 스냅샷을 생성할 수는 있지만, 이 스냅샷을 사용하여 트랜잭션 저장소를 백피드하지 못할 수도 있습니다. 이 프로세스는 지원되지 않습니다.

## <a name="global-distribution"></a>글로벌 배포

전역적으로 분산된 Microsoft Azure Cosmos DB 계정이 있는 경우 컨테이너에 분석 저장소를 사용하도록 설정하면 해당 계정의 모든 지역에서 이 계정을 사용할 수 있습니다.  작동 데이터의 변경 내용은 모든 지역에서 전역적으로 복제됩니다. Microsoft Azure Cosmos DB에서 데이터의 가장 가까운 지역 복사본을 대상으로 분석 쿼리를 효과적으로 실행할 수 있습니다.

## <a name="partitioning"></a>분할

분석 저장소 분할은 트랜잭션 저장소의 분할과 완전히 독립적입니다. 기본적으로 분석 저장소의 데이터는 분할되지 않습니다. 분석 쿼리에 자주 사용되는 필터가 있는 경우 더 나은 쿼리 성능을 위해 이러한 필드를 기반으로 분할하는 옵션이 있습니다. 자세한 내용은 [사용자 지정 분할 소개](custom-partitioning-analytical-store.md) 및 [사용자 지정 분할 구성 방법](configure-custom-partitioning.md) 문서를 참조하세요.  

## <a name="security"></a>보안

* **분석 저장소를 사용한 인증** 은 지정된 데이터베이스의 트랜잭션 저장소와 동일합니다. 인증에 기본, 보조 또는 읽기 전용 키를 사용할 수 있습니다. Synapse Studio에서 연결된 서비스를 활용하여 Spark 노트북에 Microsoft Azure Cosmos DB 키 붙여넣기를 방지할 수 있습니다. Azure Synapse SQL 서버리스의 경우 SQL 자격 증명을 사용하여 Azure Cosmos DB 키를 SQL Notebook에 붙여넣지 않도록 방지할 수도 있습니다. 이러한 연결된 서비스 또는 이러한 SQL 자격 증명에 대한 액세스는 작업 영역에 대한 액세스 권한이 있는 모든 사용자가 사용할 수 있습니다.

* **프라이빗 엔드포인트를 사용한 네트워크 격리** - 트랜잭션 및 분석 저장소에 있는 데이터에 대한 네트워크 액세스를 독립적으로 제어할 수 있습니다. 네트워크 격리는 Azure Synapse 작업 영역의 관리형 가상 네트워크 내에서 각 저장소마다 별도의 관리형 프라이빗 엔드포인트를 사용하여 수행됩니다. 자세히 알아보려면 [분석 저장소에 대한 프라이빗 엔드포인트 구성](analytical-store-private-endpoints.md) 방법에 대한 문서를 참조하세요.

* **고객 관리형 키를 통한 데이터 암호화** - 자동화되고 투명한 방식으로 동일한 고객 관리형 키를 사용하여 트랜잭션 및 분석 저장소에서 데이터를 원활하게 암호화할 수 있습니다. Azure Synapse Link는 Azure Cosmos DB 계정의 관리 ID를 사용하여 고객 관리형 키 구성만 지원합니다. 계정에서 [Azure Synapse Link를 사용하도록 설정](configure-synapse-link.md#enable-synapse-link)하기 전에 Azure Key Vault 액세스 정책에서 계정의 관리 ID를 구성해야 합니다. 자세한 내용은 [Azure Cosmos DB 계정의 관리 ID를 사용하여 고객 관리형 키 구성](how-to-setup-cmk.md#using-managed-identity) 문서를 참조하세요.

## <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>여러 Azure Synapse Analytics 런타임 지원

분석 저장소는 컴퓨팅 실행 시간에 종속되지 않고 분석 워크로드에 확장성, 탄력성 및 성능을 제공하도록 최적화되었습니다. 스토리지 기술은 수동 작업 없이 분석 워크로드를 최적화하기 위해 자체 관리됩니다.

분석 컴퓨팅 시스템에서 분석 스토리지 시스템을 분리하면 Azure Synapse Analytics에서 지원하는 여러 분석 런타임에서 Microsoft Azure Cosmos DB 분석 저장소의 데이터를 동시에 쿼리할 수 ​​있습니다. 현재 Azure Synapse Analytics는 Azure Cosmos DB 분석 저장소를 통해 Apache Spark와 서버리스 SQL 풀을 지원합니다.

> [!NOTE]
> Azure Synapse Analytics 런타임을 사용하여 분석 저장소에서 읽기만 가능합니다. 반대의 경우도 마찬가지입니다. Azure Synapse Analytics 런타임은 분석 저장소에서 읽기만 가능합니다. 자동 동기화 프로세스만 분석 저장소의 데이터를 변경할 수 있습니다. 기본 제공 Azure Cosmos DB OLTP SDK를 통해 Azure Synapse Analytics Spark 풀을 사용하여 Cosmos DB 트랜잭션 저장소에 데이터를 쓰기 저장할 수 있습니다.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> 가격 책정

분석 저장소는 다음에 대해 청구되는 소비 기반 가격 책정 모델을 따릅니다.

* 스토리지: 분석 TTL에서 정의한 기록 데이터를 포함하여 매달 분석 저장소에 보존되는 데이터의 양입니다.

* 분석 쓰기 작업: 트랜잭션 저장소에서 분석 저장소로 작동 데이터 업데이트의 완전 관리형 동기화(자동 동기화)

* 분석 읽기 작업: Azure Synapse Analytics Spark 풀 및 서버리스 SQL 풀 런타임에서 분석 저장소에 대해 수행되는 읽기 작업입니다.

분석 저장소 가격은 트랜잭션 저장소 가격 책정 모델과는 별개입니다. 분석 저장소에는 프로비저닝된 RU의 개념이 없습니다. 분석 저장소의 가격 책정 모델에 대한 자세한 내용은 [Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) 가격 책정 페이지를 참조하세요.

분석 저장소의 데이터는 Azure Synapse Analytics 런타임(Azure Synapse Apache Spark 풀 및 Azure Synapse 서버리스 SQL 풀)에서 수행되는 Azure Synapse Link를 통해서만 액세스할 수 있습니다. 분석 저장소의 데이터에 액세스하는 [가격 책정](https://azure.microsoft.com/pricing/details/synapse-analytics/) 모델에 대한 자세한 내용은 Azure Synapse Analytics 가격 책정 페이지를 참조하세요.

분석 저장소 관점에서 Azure Cosmos DB 컨테이너에서 분석 저장소를 사용하도록 설정하는 높은 수준의 예상 비용을 얻으려면 Azure [Cosmos DB Capacity Planner를](https://cosmos.azure.com/capacitycalculator/) 사용하여 분석 스토리지 및 쓰기 작업 비용을 예상할 수 있습니다. 분석 읽기 작업 비용은 분석 워크로드 특성에 따라 달라지지만 대략적으로 어림하여 분석 저장소에서 1TB의 데이터를 검사할 경우 대개 13만개의 분석 읽기 작업이 수행되고 결과적으로 $0.065의 비용이 발생합니다.

> [!NOTE]
> 분석 저장소 읽기 작업 추정치는 분석 워크로드의 기능이므로 Cosmos DB 비용 계산기에 포함되지 않습니다. 위의 추정치는 분석 저장소에서 1TB의 데이터를 검사하는 경우에 대한 것이지만 필터를 적용하면 검사되는 데이터 볼륨이 감소합니다. 이 값은 사용량에 따른 가격 책정 모델에서 정확한 분석 읽기 작업 수를 결정합니다. 분석 워크로드의 개념 증명은 분석 읽기 작업에 대한 보다 정밀한 추정치를 제공합니다. 이 예상치에는 Azure Synapse Analytics 비용이 포함되지 않습니다.


## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> 분석 TTL(Time-to-Live)

분석 TTL은 컨테이너에 대해 분석 저장소에 데이터를 보존해야 하는 기간을 나타냅니다. 

분석 저장소가 사용되는 경우 작동 데이터의 삽입, 업데이트, 삭제는 트랜잭션 TTL 구성과 관계없이 트랜잭션 저장소에서 분석 저장소로 자동 동기화됩니다. 분석 저장소에서 이 작동 데이터의 보존은 아래에 지정된 대로 컨테이너 수준에서 분석 TTL 값으로 제어할 수 있습니다.

컨테이너의 분석 TTL은 `AnalyticalStoreTimeToLiveInSeconds` 속성을 사용하여 설정됩니다.

* 값이 "0"으로 설정되거나 누락되거나 null로 설정되면 분석 저장소를 사용할 수 없으며 트랜잭션 저장소에서 분석 저장소로 데이터가 복제되지 않습니다.

* 존재하고 값이 "-1"로 설정되면 트랜잭션 저장소의 데이터 보존과 관계없이 분석 저장소에 모든 기록 데이터가 보존됩니다. 이 설정은 분석 저장소에 작동 데이터가 무기한으로 보존됨을 나타냅니다.

* 존재하고 값이 양수 "n"으로 설정되면 항목이 트랜잭션 저장소에서 마지막으로 수정되고 "n"초 후에 분석 저장소에서 만료됩니다. 트랜잭션 저장소의 데이터 보존과 관계없이 제한된 기간 동안 분석 저장소에 작동 데이터를 보존하려는 경우에 이 설정을 활용할 수 있습니다.

몇 가지 고려할 점은 다음과 같습니다.

*   분석 TTL 값으로 분석 저장소를 사용하도록 설정한 후 나중에 다른 유효한 값으로 분석 저장소를 업데이트할 수 있습니다. 
*   컨테이너 또는 항목 수준에서 트랜잭션 TTL을 설정할 수 있지만 현재 분석 TTL은 컨테이너 수준에서만 설정할 수 있습니다.
*   컨테이너 수준에서 분석 TTL > = 트랜잭션 TTL을 설정하여 분석 저장소에 작동 데이터를 더 길게 보존할 수 있습니다.
*   분석 TTL = 트랜잭션 TTL로 설정하여 트랜잭션 저장소를 미러링하도록 분석 저장소를 만들 수 있습니다.

컨테이너에서 분석 저장소를 사용하도록 설정하는 방법은 다음과 같습니다.

* Azure Portal에서 분석 TTL 옵션을 켜면 기본값인 -1로 설정됩니다. Data Explorer 아래의 컨테이너 설정으로 이동하여 이 값을 'n'초로 변경할 수 있습니다. 
 
* Azure Management SDK, Azure Cosmos DB SDK, PowerShell 또는 CLI에서 분석 TTL 옵션을 -1 또는 ‘n’초로 설정하면 사용하도록 설정할 수 있습니다. 

자세한 내용은 [컨테이너에 분석 TTL을 구성하는 방법](configure-synapse-link.md#create-analytical-ttl)을 참조하세요

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음 문서를 참조하세요

* [Microsoft Azure Cosmos DB용 Azure Synapse Link](synapse-link.md)

* [Azure Synapse Analytics를 사용하여 하이브리드 트랜잭션 및 분석 처리를 디자인](/learn/modules/design-hybrid-transactional-analytical-processing-using-azure-synapse-analytics/)하는 방법에 대한 학습 모듈을 확인하세요.

* [Microsoft Azure Cosmos DB용 Azure Synapse Link 시작하기](configure-synapse-link.md)

* [Microsoft Azure Cosmos DB용 Azure Synapse Link에 대한 질문과 대답](synapse-link-frequently-asked-questions.yml)

* [Microsoft Azure Cosmos DB용 Azure Synapse Link 사용 사례](synapse-link-use-cases.md)
