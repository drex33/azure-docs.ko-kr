---
title: 다중 모델 기능
description: Microsoft Azure SQL을 사용하면 동일한 데이터베이스에서 여러 데이터 모델을 사용할 수 있습니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: mathoma, urmilano
ms.date: 12/17/2018
ms.openlocfilehash: 93005f665f816f0f6dcde3a1c06748622f4bf271
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113585828"
---
# <a name="multi-model-capabilities-of-azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database 및 SQL Managed Instance의 다중 모델 기능
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

다중 모델 데이터베이스를 사용하면 관계형 데이터, 그래프, JSON 또는 XML 문서, 공간 데이터, 키-값 쌍 등의 여러 형식으로 데이터를 저장하고 사용할 수 있습니다.

[Azure SQL 제품군](azure-sql-iaas-vs-paas-what-is-overview.md)은 다양한 범용 애플리케이션에 최상의 성능을 제공하는 관계형 모델을 사용합니다. 그러나 Azure SQL Database 및 SQL Managed Instance 같은 Azure SQL 제품은 관계형 데이터로 제한되지 않습니다. 관계형 모델과 긴밀하게 통합된 비관계형 형식을 사용할 수 있습니다.

다음과 같은 경우 Azure SQL의 다중 모델 기능을 사용하는 것이 좋습니다.

- NoSQL 모델에 더 적합한 정보 또는 구조를 갖고 있으며, 별도의 NoSQL 데이터베이스를 사용하고 싶지 않습니다.
- 대부분의 데이터가 관계형 모델에 적합하며, 데이터의 일부를 NoSQL 스타일로 모델링해야 합니다.
- Transact-SQL 언어를 사용하여 관계형 데이터와 NoSQL 데이터를 쿼리하고 분석한 다음, 해당 데이터를 SQL 언어를 사용할 수 있는 도구 및 애플리케이션과 통합하려고 합니다.
- [메모리 내 기술](in-memory-oltp-overview.md)과 같은 데이터베이스 기능을 적용하여 NoSQL 데이터 구조의 분석 또는 처리 성능을 개선하려고 합니다. [트랜잭션 복제](managed-instance/replication-transactional-overview.md) 또는 [읽기 가능한 복제본](database/read-scale-out.md)을 사용하여 데이터 복사본을 만들고 주 데이터베이스에서 일부 분석 워크로드를 오프로드할 수 있습니다.

다음 섹션에서는 Azure SQL의 가장 중요한 다중 모델 기능에 대해 설명합니다.

> [!Note]
> 동일한 Transact-SQL 쿼리에서 JSONPath 식, XQuery/XPath 식, 공간 함수 및 그래프-쿼리 식을 사용하여 데이터베이스에 저장된 데이터에 액세스할 수 있습니다. Transact-SQL 쿼리를 실행할 수 있는 도구나 프로그래밍 언어도 해당 쿼리 인터페이스를 사용하여 다중 모델 데이터에 액세스할 수 있습니다. 이는 다양한 데이터 모델에 대한 특수 API를 제공하는 [Azure Cosmos DB](../cosmos-db/index.yml)와 같은 다중 모델 데이터베이스와 비교되는 주요 차이점입니다.

## <a name="graph-features"></a>그래프 기능

Azure SQL 제품은 데이터베이스에서 다대다 관계를 모델링하는 그래프 데이터베이스 기능을 제공합니다. 그래프는 노드(또는 정점) 및 에지(또는 관계)의 컬렉션입니다. 노드는 엔터티(예: 사람 또는 조직)를 나타냅니다. 에지는 연결하는 두 노드(예: 좋아요 또는 친구) 간의 관계를 나타냅니다. 

그래프 데이터베이스를 고유하게 만드는 몇 가지 기능은 다음과 같습니다.

- 에지는 그래프 데이터베이스의 첫 번째 클래스 엔터티입니다. 에지에 특성 또는 속성이 연결될 수 있습니다.
- 단일 에지는 유연하게 그래프 데이터베이스의 여러 노드를 연결할 수 있습니다.
- 패턴 일치 및 멀티 홉 탐색 쿼리를 쉽게 표현할 수 있습니다.
- 이행적 폐쇄 및 다형적 쿼리를 쉽게 표현할 수 있습니다.

[그래프 관계 및 그래프 쿼리 기능](/sql/relational-databases/graphs/sql-graph-overview)이 Transact-SQL에 통합되어 SQL Server 데이터베이스 엔진을 기본 데이터베이스 관리 시스템으로 사용하는 이점이 있습니다. 그래프 기능은 그래프 `MATCH` 연산자로 강화된 표준 Transact-SQL 쿼리를 사용하여 그래프 데이터를 쿼리합니다.

관계형 데이터베이스는 그래프 데이터베이스가 할 수 있는 모든 것을 달성할 수 있습니다. 그러나 그래프 데이터베이스를 사용하면 특정 쿼리를 더 쉽게 표현할 수 있습니다. 둘 중 하나를 선택할 때 다음과 같은 요소를 기준으로 선택할 수 있습니다.

- [hierarchyId 데이터 형식](/sql/t-sql/data-types/hierarchyid-data-type-method-reference)을 사용할 수 없도록 한 노드에 여러 부모가 존재할 수 있는 계층적 데이터를 모델링해야 합니다.
- 애플리케이션에 복잡한 다대다 관계가 있습니다. 애플리케이션이 발전함에 따라 새 관계가 추가됩니다.
- 상호 연결된 데이터 및 관계를 분석해야 합니다.
- [SHORTEST_PATH](/sql/relational-databases/graphs/sql-graph-shortest-path)와 같은 그래프 관련 T-SQL 검색 조건을 사용하려고 합니다.

## <a name="json-features"></a>JSON 기능

Azure SQL 제품에서는 [JSON(JavaScript Object Notation)](https://www.json.org/) 형식으로 표현된 데이터를 구문 분석 및 쿼리하고 관계형 데이터를 JSON 텍스트로 내보낼 수 있습니다. [JSON](/sql/relational-databases/json/json-data-sql-server)은 SQL Server 데이터베이스 엔진의 핵심 기능입니다.

JSON 기능을 사용하면 JSON 문서를 테이블에 배치하고, 관계형 데이터를 JSON 문서로 변환하고, JSON 문서를 관계형 데이터로 변환할 수 있습니다. JSON 함수로 향상된 표준 Transact-SQL 언어를 사용하여 문서를 구문 분석할 수 있습니다. 비클러스터형 인덱스, columnstore 인덱스 또는 메모리 최적화 테이블을 사용하여 쿼리를 최적화할 수도 있습니다.

JSON은 최신 웹 및 모바일 애플리케이션에서 데이터를 교환하는 데 사용되는 인기 있는 데이터 형식입니다. 또한 JSON은 로그 파일 또는 NoSQL 데이터베이스에 반구조화된 데이터를 저장하는 데도 사용됩니다. 많은 REST 웹 서비스에서 JSON 텍스트로 형식이 지정된 결과를 반환하거나 JSON으로 형식이 지정된 데이터를 허용합니다. 

대부분의 Azure 서비스에는 JSON을 반환하거나 사용하는 REST 엔드포인트가 있습니다. 이러한 서비스에는 [Azure Cognitive Search](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/) 및 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)가 포함됩니다.

JSON 텍스트가 있는 경우 JSON에서 데이터를 추출하거나 기본 제공 함수 [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql), [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql) 및 [ISJSON](/sql/t-sql/functions/isjson-transact-sql)을 사용하여 JSON 형식이 제대로 지정되었는지 확인할 수 있습니다. 다른 함수는 다음과 같습니다.

- [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql): JSON 텍스트 내부의 값을 업데이트할 수 있습니다. 
- [OPENJSON](/sql/t-sql/functions/openjson-transact-sql): JSON 개체 배열을 행 세트로 변환하여 고급 쿼리 및 분석을 수행할 수 있습니다. 모든 SQL 쿼리는 반환된 결과 집합에서 실행할 수 있습니다. 
- [FOR JSON](/sql/relational-databases/json/format-query-results-as-json-with-for-json-sql-server): 관계형 테이블에 저장된 데이터의 형식을 JSON 텍스트로 지정할 수 있습니다.

![JSON 함수를 보여주는 다이어그램](./media/multi-model-features/image_1.png)

자세한 내용은 [JSON 데이터 작업 방법](database/json-features.md)을 참조하세요. 

일부 특정 시나리오에서는 관계형 모델 대신 문서 모델을 사용할 수 있습니다.

- 개체의 모든 필드에 한 번에 액세스하거나 개체의 정규화된 파트를 한 번도 업데이트하지 않으므로 스키마의 정규화 수준이 높아도 큰 이점은 없습니다. 그러나 정규화된 모델의 경우 데이터를 가져오기 위해 많은 테이블을 조인해야 하므로 쿼리의 복잡성이 커집니다.
- 통신 및 데이터 모델에 JSON 문서를 기본적으로 사용하는 애플리케이션 관련 작업을 수행 중이고, 관계형 데이터를 JSON으로 변환하거나 그 반대로 변환하는 추가 계층을 더 이상 도입하고 싶지 않습니다.
- 자식 테이블 또는 엔터티-개체-값 패턴을 비정규화하여 데이터 모델을 간소화해야 합니다.
- 데이터를 구문 분석하는 추가 도구 없이 JSON 형식으로 저장된 데이터를 로드하거나 내보내야 합니다.

## <a name="xml-features"></a>XML 기능

XML 기능을 사용하면 데이터베이스에서 XML 데이터를 저장 및 인덱싱하고 네이티브 XQuery/XPath 작업을 통해 XML 데이터를 사용할 수 있습니다. Azure SQL 제품에는 XML 데이터를 처리하는 특수한 기본 제공 XML 데이터 형식 및 쿼리 함수가 있습니다.

SQL Server 데이터베이스 엔진은 반정형 데이터를 관리하는 애플리케이션을 개발하는 데 사용되는 강력한 플랫폼을 제공합니다. [XML 지원](/sql/relational-databases/xml/xml-data-sql-server)은 데이터베이스 엔진의 모든 구성 요소에 통합되었으며 다음과 같은 기능이 있습니다.

- 기본적으로 XML 값을 XML 스키마의 컬렉션에 따라 형식화하거나 형식화하지 않은 채로 둘 수 있는 XML 데이터 형식 열에 저장하는 기능. XML 열을 인덱싱할 수 있습니다.
- XML 형식의 열 및 변수에 저장된 XML 데이터에 대한 XQuery 쿼리를 지정하는 기능. 데이터베이스에서 사용하는 데이터 모델에 액세스하는 모든 Transact-SQL 쿼리에 XQuery 기능을 사용할 수 있습니다.
- [기본 XML 인덱스](/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index)를 사용하여 XML 문서의 모든 요소를 자동으로 인덱싱. 또는 [보조 XML 인덱스](/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes)를 사용하여 인덱싱해야 하는 정확한 경로를 지정할 수 있습니다.
- XML 데이터를 대량으로 로드할 수 있는 `OPENROWSET`
- 관계형 데이터를 XML 형식으로 변환하는 기능

일부 특정 시나리오에서는 관계형 모델 대신 문서 모델을 사용할 수 있습니다.

- 개체의 모든 필드에 한 번에 액세스하거나 개체의 정규화된 파트를 한 번도 업데이트하지 않으므로 스키마의 정규화 수준이 높아도 큰 이점은 없습니다. 그러나 정규화된 모델의 경우 데이터를 가져오기 위해 많은 테이블을 조인해야 하므로 쿼리의 복잡성이 커집니다.
- 통신 및 데이터 모델에 XML 문서를 기본적으로 사용하는 애플리케이션 관련 작업을 수행 중이고, 관계형 데이터를 JSON으로 변환하거나 그 반대로 변환하는 추가 계층을 더 이상 도입하고 싶지 않습니다.
- 자식 테이블 또는 엔터티-개체-값 패턴을 비정규화하여 데이터 모델을 간소화해야 합니다.
- 데이터를 구문 분석하는 추가 도구 없이 XML 형식으로 저장된 데이터를 로드하거나 내보내야 합니다.

## <a name="spatial-features"></a>공간 기능

공간 데이터는 개체의 물리적 위치 및 모양 정보를 나타냅니다. 이러한 개체는 지점 위치이거나 국가/지역, 도로 또는 호수와 같은 더 복잡한 개체일 수 있습니다.

Azure SQL은 다음과 같은 두 가지 공간 데이터 형식을 지원합니다. 

- 기하 도형 형식은 유클리드(평면) 좌표계의 데이터를 나타냅니다.
- 지리 형식은 둥근 표면 좌표계의 데이터를 나타냅니다.

Azure SQL의 공간 기능을 사용하면 기하학적 데이터 및 지리적 데이터를 저장할 수 있습니다. Azure SQL의 공간 개체를 사용하여 JSON 형식으로 표시된 데이터를 구문 분석 및 쿼리하고, 관계형 데이터를 JSON 텍스트로 내보낼 수 있습니다. 이러한 공간 개체로는 [점](/sql/relational-databases/spatial/point), [LineString](/sql/relational-databases/spatial/linestring) 및 [다각형](/sql/relational-databases/spatial/polygon)이 있습니다. Azure SQL은 공간 쿼리의 성능을 향상하는 데 사용할 수 있는 특수한 [공간 인덱스](/sql/relational-databases/spatial/spatial-indexes-overview)도 제공합니다.

[공간 지원](/sql/relational-databases/spatial/spatial-data-sql-server)은 SQL Server 데이터베이스 엔진의 핵심 기능입니다.

## <a name="key-value-pairs"></a>키-값 쌍

키-값 구조는 기본적으로 표준 관계형 테이블로 표시할 수 있으므로 Azure SQL 제품에는 키-값 쌍을 지원하는 특수한 형식 또는 구조가 없습니다.

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

제약 조건 없이 필요에 맞게 이 키-값 구조를 사용자 지정할 수 있습니다. 예를 들어 값은 `nvarchar(max)` 형식 대신 XML 문서여도 됩니다. 값이 JSON 문서인 경우 JSON 콘텐츠의 유효성을 확인하는 `CHECK` 제약 조건을 사용할 수 있습니다. 한 키와 관련된 값을 원하는 만큼 추가 열에 배치할 수 있습니다. 예를 들면 다음과 같습니다.

- 계산 열과 인덱스를 추가하여 데이터 액세스를 단순화하고 최적화합니다.
- 테이블을 메모리 최적화 스키마 전용 테이블로 정의하여 성능을 높입니다.

관계형 모델을 키-값 쌍 솔루션으로 효과적으로 사용할 수 있는 방법에 대한 예제는 [bwin에서 SQL Server 2016 메모리 내 OLTP을 사용하여 전례 없는 성능 및 확장성을 달성하는 방법](/archive/blogs/sqlcat/how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale)을 참조하세요. 이 사례 연구에서 bwin은 ASP.NET 캐싱 솔루션에 관계형 모델을 사용하여 초당 120만 개의 일괄 처리 속도를 달성했습니다.

## <a name="next-steps"></a>다음 단계

다중 모델 기능은 Azure SQL 제품 간에 공유되는 핵심 SQL Server 데이터베이스 엔진 기능입니다. 이러한 기능에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [SQL Server 및 Azure SQL Database를 사용한 Graph 처리](/sql/relational-databases/graphs/sql-graph-overview)
- [SQL Server의 JSON 데이터](/sql/relational-databases/json/json-data-sql-server)
- [SQL Server의 공간 데이터](/sql/relational-databases/spatial/spatial-data-sql-server)
- [SQL Server의 XML 데이터](/sql/relational-databases/xml/xml-data-sql-server)
- [Azure SQL Database의 키-값 저장소 성능](https://devblogs.microsoft.com/azure-sql/azure-sql-database-as-a-key-value-store/)
