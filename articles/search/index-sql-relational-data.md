---
title: 가져오기 및 인덱싱을 위해 SQL 관계형 데이터 모델링
titleSuffix: Azure Cognitive Search
description: Azure Search에서 인덱싱 및 전체 텍스트 검색을 위해 관계형 데이터를 모델링하고 플랫 결과 집합으로 비정규화하는 방법을 알아봅니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: how-to
ms.date: 11/12/2021
ms.openlocfilehash: cd55d0dd76a35135bdef25b4a8c5bae62183e337
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132752375"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Azure Cognitive Search에서 가져오기 및 인덱싱을 위해 관계형 SQL 데이터를 모델링하는 방법

Azure Cognitive Search는 플랫 행 집합을 [인덱싱 파이프라인](search-what-is-an-index.md)의 입력으로 수락합니다. 원본 데이터가 SQL Server 관계형 데이터베이스의 조인된 테이블에서 시작되는 경우 이 문서에서는 결과 집합을 생성하는 방법과 Azure Cognitive Search 인덱스에서 부모-자식 관계를 모델링하는 방법을 설명합니다.

실례로, [데모 데이터](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels)를 기반으로 하는 가상 호텔 데이터베이스를 참조하겠습니다. 데이터베이스가 50개 호텔이 포함된 Hotels$ 테이블과 다양한 유형, 요금, 편의 시설의 총 750개 객실이 포함된 Rooms$ 테이블로 구성되어 있다고 가정합니다. 테이블 간에는 일 대 다 관계가 있습니다. 이 방법에서 뷰는 호텔당 한 행씩 50개 행을 반환하는 쿼리를 제공합니다. 각 행에는 관련 객실의 세부 정보가 포함되어 있습니다.

   ![Hotels 데이터베이스의 테이블 및 뷰](media/index-sql-relational-data/hotels-database-tables-view.png "Hotels 데이터베이스의 테이블 및 뷰")

## <a name="the-problem-of-denormalized-data"></a>비정규화된 데이터 문제

일 대 다 관계로 작업하는 경우의 문제 중 하나는 조인된 테이블을 기반으로 하는 표준 쿼리에서 Azure Cognitive Search 시나리오에 적합하지 않은 비정규화된 데이터가 반환된다는 것입니다. 호텔과 객실을 조인하는 다음 예제를 살펴보세요.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
이 쿼리의 결과는 모든 호텔 필드와 모든 객실 필드를 차례로 반환하며 객실 값마다 예비 호텔 정보가 반복됩니다.

   ![비정규화된 데이터, 객실 필드 추가 시 호텔 데이터가 중복됨](media/index-sql-relational-data/denormalize-data-query.png "비정규화된 데이터, 객실 필드 추가 시 호텔 데이터가 중복됨")


외견상으로는 쿼리에 성공하고 모든 데이터가 플랫 행 집합에 제공되지만 예상 검색 경험에 적합한 문서 구조를 제공하지는 못합니다. 인덱싱 중에 Azure Cognitive Search는 수집된 행당 하나의 검색 문서를 만듭니다. 검색 문서가 위의 결과와 같이 표시되면 Twin Dome 호텔의 개별 문서만 해도 7개가 중복된 것을 확인할 수 있습니다. “플로리다의 호텔” 쿼리는 Twin Dome 호텔 하나에 대해 7개 결과를 반환하므로 검색 결과에서 다른 관련 호텔을 확인하기 어렵습니다.

호텔당 하나의 문서라는 예상 경험을 얻으려면 올바른 세분성으로 행 집합을 제공하되 전체 정보를 제공해야 합니다. 이 문서에서는 등록 방법을 설명합니다.

## <a name="define-a-query-that-returns-embedded-json"></a>포함된 JSON을 반환하는 쿼리 정의

예상 검색 경험을 제공하려면 데이터 세트가 Azure Cognitive Search의 검색 문서당 하나의 행으로 구성되어야 합니다. 예제에서는 호텔당 하나의 행을 만들되 사용자가 야간 요금, 침대 크기 및 개수, 바다 전망 등 객실 세부 정보에 모두 포함된 관심 있는 다른 객실 관련 필드를 검색할 수 있도록 하려고 합니다.

솔루션은 두 번째 단계와 같이 객실 세부 정보를 중첩된 JSON으로 캡처한 다음 JSON 구조체를 뷰의 필드에 삽입하는 것입니다. 

1. 각각 50개 호텔과 750개 객실의 세부 정보를 포함하고 HotelID 필드에서 조인된 Hotels$ 및 Rooms$라는 두 개의 조인된 테이블이 있다고 가정합니다. 두 테이블에는 각각 50개 호텔과 750개 관련 객실이 포함되어 있습니다.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

1. 부모 테이블의 모든 필드(`SELECT * from dbo.Hotels$`)로 구성되고 중첩된 쿼리의 출력을 포함하는 새 *Rooms* 필드가 추가된 뷰를 만듭니다. `SELECT * from dbo.Rooms$`의 **FOR JSON AUTO** 절은 출력을 JSON으로 구성합니다. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   다음 스크린샷은 맨 아래에 *Rooms* nvarchar 필드가 있는 결과 뷰를 보여 줍니다. *Rooms* 필드는 HotelRooms 뷰에만 있습니다.

   ![HotelRooms 뷰](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms 뷰")

1. `SELECT * FROM dbo.HotelRooms`를 실행하여 행 집합을 검색합니다. 이 쿼리는 호텔당 하나씩 50개 행을 반환하며 관련 객실 정보가 JSON 컬렉션으로 포함됩니다. 

   ![HotelRooms 뷰의 행 집합](media/index-sql-relational-data/hotelrooms-rowset.png "HotelRooms 뷰의 행 집합")

이제 행 집합을 Azure Cognitive Search로 가져올 준비가 되었습니다.

> [!NOTE]
> 이 방법은 포함된 JSON이 [SQL Server의 최대 열 크기 한도](/sql/sql-server/maximum-capacity-specifications-for-sql-server) 이내라고 가정합니다. 

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>일 대 다 관계의 “다” 쪽에 복합 컬렉션 사용

Azure Cognitive Search 쪽에서 중첩된 JSON을 사용하여 일 대 다 관계를 모델링하는 인덱스 스키마를 만듭니다. 이전 섹션에서 만든 결과 집합은 일반적으로 아래에 제공된 인덱스 스키마에 해당합니다(간단히 하기 위해 일부 필드를 잘라냄).

다음 예제는 [복합 데이터 형식을 모델링하는 방법](search-howto-complex-data-types.md#create-complex-fields)의 예제와 유사합니다. 이 문서에서 중점적으로 설명한 *Rooms* 구조체는 *hotels* 라는 인덱스의 필드 컬렉션에 있습니다. 또한 이 예제에서는 컬렉션에서 임의 개수의 여러 항목이 허용되는 대신 고정된 항목 집합으로 구성된다는 점에서 *Rooms* 와는 다른 *Address* 의 복합 형식을 보여 줍니다.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

이전 결과 집합과 위의 인덱스 스키마가 있으면 성공적인 인덱싱 작업에 필요한 모든 구성 요소가 준비된 것입니다. 평면화된 데이터 세트는 인덱싱 요구 사항을 충족하지만 세부 정보를 유지합니다. Azure Cognitive Search 인덱스에서는 검색 결과가 개별 객실 및 해당 특성의 컨텍스트를 유지하면서 호텔 기반 엔터티로 쉽게 이동됩니다.

## <a name="next-steps"></a>다음 단계

사용자 고유의 데이터 세트로 [데이터 가져오기 마법사](search-import-data-portal.md)를 사용하여 인덱스를 만들고 로드할 수 있습니다. 마법사는 *Rooms* 에 포함된 것과 같은 포함된 JSON 컬렉션을 검색하고 복합 형식 컬렉션을 포함하는 인덱스 스키마를 유추합니다. 

  ![데이터 가져오기 마법사에서 유추된 인덱스](media/index-sql-relational-data/search-index-rooms-complex-collection.png "데이터 가져오기 마법사에서 유추된 인덱스")

다음 빠른 시작을 통해 데이터 가져오기 마법사의 기본 단계를 알아보세요.

> [!div class="nextstepaction"]
> [빠른 시작: Azure Portal을 사용하여 검색 인덱스 만들기](search-get-started-portal.md)