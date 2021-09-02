---
title: Azure Cosmos DB에서 날짜 사용
description: Azure Cosmos DB에서 DataTime 개체를 저장, 인덱싱, 쿼리하는 방법 알아보기
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 04/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 3dcbe17a846d2751e919d725bc19dccbed8346c1
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117192"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Azure Cosmos DB에서 날짜 사용
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB는 네이티브 [JSON](https://www.json.org) 데이터 모델을 통해 스키마 유연성과 풍부한 인덱싱을 제공합니다. 데이터베이스, 컨테이너, 문서 및 저장 프로시저를 포함한 모든 Azure Cosmos DB 리소스는 모델링되어 JSON 문서로 저장됩니다. 이식 가능성을 위한 요구 사항인 JSON(및 Azure Cosmos DB)은 String, Number, Boolean, Array, Object 및 Null과 같은 기본 형식만 지원합니다. 그러나 JSON은 유연하므로 개발자와 프레임워크는 이러한 기본형을 사용하여 개체 또는 배열로 구성하여 보다 복잡한 형식을 나타낼 수 있습니다.

기본형 외에도 많은 애플리케이션에는 날짜 및 타임스탬프를 나타내기 위해 DateTime 형식이 필요합니다. 이 문서에서는 개발자가 .NET SDK를 사용하여 Azure Cosmos DB에서 날짜를 저장, 검색 및 쿼리하는 방법에 대해 설명합니다.

## <a name="storing-datetimes"></a>날짜/시간 저장

Azure Cosmos DB는 문자열, 숫자, 부울, null, 배열, 개체와 같은 JSON 형식을 지원합니다. DateTime 형식은 직접 지원하지 않습니다. 현재 Azure Cosmos DB는 날짜 지역화를 지원하지 않습니다. 따라서 DateTime을 문자열로 저장해야 합니다. Azure Cosmos DB DateTime 문자열에 권장되는 형식은 ISO 8601 UTC 표준에 따른 `yyyy-MM-ddTHH:mm:ss.fffffffZ`입니다. Azure Cosmos DB에는 모든 날짜를 UTC 기준으로 저장하는 것이 좋습니다. 날짜 문자열을 이 형식으로 변환하면 날짜를 사전순으로 정렬할 수 있습니다. UTC 기준이 아닌 날짜가 저장되면 클라이언트 측에서 논리를 처리해야 합니다. 현지 DateTime을 UTC 기준으로 변환하려면 오프셋을 JSON의 속성으로 파악하고 저장해야 하며 클라이언트는 오프셋을 사용하여 UTC DateTime 값을 계산할 수 있습니다.

DateTime 문자열이 모두 UTC 기준이고 길이가 동일한 경우에만 DateTime 문자열을 필터로 사용하는 범위 쿼리가 지원됩니다. Azure Cosmos DB에서 [GetCurrentDateTime](sql-query-getcurrentdatetime.md) 시스템 함수는 현재 UTC 날짜 및 시간 ISO 8601 문자열 값을 `yyyy-MM-ddTHH:mm:ss.fffffffZ` 형식으로 반환합니다.

대부분의 애플리케이션에서는 다음과 같은 이유로 DateTime의 기본 문자열 표현을 사용할 수 있습니다.

* 문자열을 비교할 수 있으며, 문자열로 변환할 때 DateTime 값의 상대적인 순서는 유지됩니다.
* 이 접근 방식에는 JSON 변환에 대해 사용자 지정 코드 또는 속성이 필요하지 않습니다.
* JSON에 저장된 날짜는 사람이 읽을 수 있습니다.
* 이 접근 방식은 빠른 쿼리 성능을 위해 Azure Cosmos DB의 인덱스를 활용할 수 있습니다.

예를 들어 다음 코드 조각에서는 .NET SDK를 사용하여 두 가지 DateTime 속성(`ShipDate` 및 `OrderDate`)을 문서로 포함하는 `Order` 개체를 저장합니다.

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14),
            Total = 113.39
        });
```

이 문서는 다음과 같이 Azure Cosmos DB에 저장됩니다.

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

또는 DateTime을 Unix 타임스탬프, 즉 1970년 1월 1일 이후로 경과된 초 수를 나타내는 숫자로 저장할 수 있습니다. Azure Cosmos DB의 내부 타임스탬프(`_ts`) 속성은 이 접근 방식을 따릅니다. [UnixDateTimeConverter](/dotnet/api/microsoft.azure.documents.unixdatetimeconverter) 클래스를 사용하여 DateTime을 숫자로 직렬화할 수 있습니다.

## <a name="querying-datetimes-in-linq"></a>LINQ에서 날짜/시간 쿼리

SQL .NET SDK는 LINQ를 통해 Azure Cosmos DB에 저장된 데이터의 쿼리를 자동으로 지원합니다. 예를 들어 다음 코드 조각에서는 지난 3일 동안 배송된 주문을 필터링하는 LINQ 쿼리를 보여 줍니다.

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

다음 SQL 문으로 변환되고 Azure Cosmos DB에서 실행됩니다.

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2014-09-30T23:14:25.7251173Z")
```

[LINQ에서 Cosmos DB 쿼리](sql-query-linq-to-sql.md)에서 Azure Cosmos DB의 SQL 쿼리 언어와 LINQ 공급자에 대해 자세히 알아볼 수 있습니다.

## <a name="indexing-datetimes-for-range-queries"></a>범위 쿼리의 날짜/시간 인덱싱

쿼리는 일반적으로 DateTime 값입니다. 쿼리를 효율적으로 실행하려면 쿼리 필터의 속성에 인덱스가 정의되어야 합니다.

[Azure Cosmos DB 인덱싱 정책](../index-policy.md)에서 인덱싱 정책을 구성하는 방법에 대해 자세히 알아볼 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [GitHub 샘플 코드](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples)(영문)에서 코드 샘플을 다운로드하여 실행합니다.
* [SQL 쿼리](sql-query-getting-started.md)에 대한 자세한 정보
* [Azure Cosmos DB 인덱싱 정책에 대해 알아보기](../index-policy.md)