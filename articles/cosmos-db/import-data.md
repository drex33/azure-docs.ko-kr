---
title: '자습서: Azure Cosmos DB용 데이터베이스 마이그레이션 도구'
description: '자습서: 오픈 소스 Azure Cosmos DB 데이터 마이그레이션 도구를 사용하여 MongoDB, SQL Server, 테이블 스토리지, Amazon DynamoDB, CSV 및 JSON 파일을 비롯한 다양한 원본에서 Azure Cosmos DB로 데이터를 가져오는 방법을 알아봅니다. CSV에서 JSON로 변환합니다.'
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 08/26/2021
ms.author: dech
ms.openlocfilehash: 72843c595c8fe04bbfd82890b8974ae386b065a2
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037349"
---
# <a name="tutorial-use-data-migration-tool-to-migrate-your-data-to-azure-cosmos-db"></a>자습서: 데이터 마이그레이션 도구를 사용하여 Azure Cosmos DB로 데이터 마이그레이션
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

이 자습서에서는 다양한 원본에서 Azure Cosmos 컨테이너 및 테이블로 데이터를 가져올 수 있는 Azure Cosmos DB 데이터 마이그레이션 도구를 사용하는 지침을 제공합니다. JSON 파일, CSV 파일, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB 및 Azure Cosmos DB SQL API 컬렉션에서도 데이터를 가져올 수 있습니다. Azure Cosmos DB에서 사용할 수 있도록 해당 데이터를 컬렉션 및 테이블로 마이그레이션할 수도 있습니다. 또한 데이터 마이그레이션 도구는 단일 파티션 컬렉션에서 SQL API용 다중 파티션 컬렉션으로 마이그레이션하는 경우에도 사용할 수 있습니다.

> [!NOTE]
> Azure Cosmos DB 데이터 마이그레이션 도구는 소규모 마이그레이션을 위해 설계된 오픈 소스 도구입니다. 대규모 마이그레이션의 경우 [데이터 수집 가이드](cosmosdb-migrationchoices.md)를 참조하세요.

* **[SQL API](./introduction.md)** - 데이터 마이그레이션 도구에서 제공되는 원본 옵션 중 하나를 사용하여 소규모로 데이터를 가져올 수 있습니다. [대규모로 데이터를 가져오기 위한 마이그레이션 옵션에 대해 알아봅니다](cosmosdb-migrationchoices.md).
* **[Table API](table/introduction.md)** - 데이터 마이그레이션 도구 또는 [AzCopy](table/table-import.md#migrate-data-by-using-azcopy)를 사용하여 데이터를 가져올 수 있습니다. 자세한 정보는 [Azure Cosmos DB Table API와 함께 사용할 데이터 가져오기](table/table-import.md)를 참조하세요.
* **[Azure Cosmos DB의 MongoDB API](mongodb/mongodb-introduction.md)** - 데이터 마이그레이션 도구는 Azure Cosmos DB의 API for MongoDB를 원본 또는 대상으로 지원하지 않습니다. Azure Cosmos DB의 API 컬렉션에서 데이터를 마이그레이션하려는 경우 지침은 [Azure Cosmos DB의 API for MongoDB를 사용하여 MongoDB 데이터를 Cosmos 데이터베이스로 마이그레이션하는 방법](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)을 참조하세요. 데이터 마이그레이션 도구를 사용하여 SQL API와 함께 사용할 수 있도록 MongoDB에서 Azure Cosmos DB SQL API 컬렉션으로 데이터를 내보낼 수 있습니다.
* **[Gremlin API](graph-introduction.md)** - 데이터 마이그레이션 도구는 Cassandra API 계정에 대해 지원되는 가져오기 도구가 아닙니다. [Cassandra API로 데이터를 가져오기 위한 마이그레이션 옵션에 대해 알아보기](cosmosdb-migrationchoices.md#azure-cosmos-db-cassandra-api)
* **[Gremlin API](graph-introduction.md)** - 데이터 마이그레이션 도구는 이 경우에 Gremlin API 계정에 지원되는 가져오기 도구가 아닙니다. [Gremlin API로 데이터를 가져오기 위한 마이그레이션 옵션에 대해 알아보기](cosmosdb-migrationchoices.md#other-apis) 

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 마이그레이션 도구 설치
> * 다른 데이터 원본에서 데이터 가져오기
> * Azure Cosmos DB에서 JSON으로 내보내기

## <a name="prerequisites"></a><a id="Prerequisites"></a>필수 조건

이 문서의 지침을 따르기 전에, 다음 단계를 수행합니다.

* [Microsoft .NET Framework 4.51](https://www.microsoft.com/download/developer-tools.aspx) 이상을 **설치** 합니다.

* **처리량 증가:** 데이터 마이그레이션 기간은 개별 컬렉션 또는 컬렉션 세트에 대해 설정한 처리량에 따라 다릅니다. 대량 데이터 마이그레이션의 경우 처리량을 늘려야 합니다. 마이그레이션을 완료한 후에는 비용을 절약하기 위해 처리량을 줄이세요. Azure Portal에서 처리량을 늘리는 방법에 대한 자세한 내용은 Azure Cosmos DB의 [성능 수준](performance-levels.md) 및 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cosmos-db/)을 참조하세요.

* **Azure Cosmos DB 리소스 만들기:** 데이터 마이그레이션을 시작하기 전에 Azure Portal에서 모든 컬렉션을 미리 만듭니다. 데이터베이스 수준 처리량이 있는 Azure Cosmos DB 계정으로 마이그레이션하는 경우에는 Azure Cosmos 컨테이너를 만들 때 파티션 키를 제공합니다.

> [!IMPORTANT]
> Azure Cosmos 계정에 연결할 때 데이터 마이그레이션 도구가 TLS(Transport Layer Security) 1.2를 사용하는지 확인하려면 .NET Framework 버전 4.7을 사용하거나 [이 문서](/dotnet/framework/network-programming/tls)에 있는 지침을 따릅니다.

## <a name="overview"></a><a id="Overviewl"></a>개요

데이터 마이그레이션 도구는 다음을 비롯한 다양한 원본에서 Azure Cosmos DB로 데이터를 가져오는 오픈 소스 솔루션입니다.

* JSON 파일
* MongoDB
* SQL Server
* CSV 파일
* Azure Table Storage
* Amazon DynamoDB
* HBase
* Azure Cosmos 컨테이너

가져오기 도구는 그래픽 사용자 인터페이스(dtui.exe)를 포함하지만 명령줄(dt.exe)에서 구동할 수도 있습니다. 실제로 UI를 통해 가져오기를 설정한 후 관련 명령을 출력하는 옵션이 있습니다. 가져오는 동안 계층 관계(하위 문서)를 만들 수 있도록 테이블 형식 원본 데이터(예: SQL Server 또는 CSV 파일)를 변환할 수 있습니다. 원본 옵션, 각 원본에서 가져오는 샘플 명령, 대상 옵션 및 가져오기 결과 보기에 대해 자세히 알아보려면 계속 진행하세요.

> [!NOTE]
> 소규모 마이그레이션에는 Azure Cosmos DB 마이그레이션 도구만 사용해야 합니다. 대규모 마이그레이션의 경우 [데이터 수집 가이드](cosmosdb-migrationchoices.md)를 참조하세요.

## <a name="installation"></a><a id="Install"></a>설치

### <a name="download-executable-package"></a>실행 파일 패키지 다운로드

  * [여기](https://github.com/Azure/azure-documentdb-datamigrationtool/releases/tag/1.8.3)에서 서명된 최신 **dt.exe** 및 **dtui.exe** Windows 이진 파일의 zip을 다운로드합니다.
  * 컴퓨터의 디렉터리에 압축을 풀고 추출된 디렉터리를 열어 이진 파일을 찾습니다.

### <a name="build-from-source"></a>원본에서 빌드

  마이그레이션 도구 소스 코드는 GitHub의 [이 리포지토리](https://github.com/azure/azure-documentdb-datamigrationtool)에서 사용할 수 있습니다. 솔루션을 로컬로 다운로드 및 컴파일하고, 다음 중 하나를 실행할 수 있습니다.

  * **Dtui.exe**: 도구의 그래픽 인터페이스 버전
  * **Dt.exe**: 도구의 명령줄 버전

## <a name="select-data-source"></a>데이터 원본 선택

이 도구를 설치하면 이제 데이터를 가져올 차례입니다. 어떤 종류의 데이터를 가져오시겠어요?

* [JSON 파일](#JSON)
* [MongoDB](#MongoDB)
* [MongoDB 내보내기 파일](#MongoDBExport)
* [SQL Server](#SQL)
* [CSV 파일](#CSV)
* [Azure Table Storage](#AzureTableSource)
* [Amazon DynamoDB](#DynamoDBSource)
* [Blob](#BlobImport)
* [Azure Cosmos 컨테이너](#SQLSource)
* [HBase](#HBaseSource)
* [Azure Cosmos DB 대량 가져오기](#SQLBulkTarget)
* [Azure Cosmos DB 순차 레코드 가져오기](#SQLSeqTarget)

## <a name="import-json-files"></a><a id="JSON"></a>JSON 파일 가져오기

JSON 파일 원본 가져오기 옵션을 사용하면 단일 문서 JSON 파일이나 각각 JSON 문서 배열을 포함하는 JSON 파일을 하나 이상 가져올 수 있습니다. 가져올 JSON 파일을 포함하는 폴더를 추가하면, 하위 폴더에서 재귀적으로 파일을 검색하는 옵션도 있습니다.

:::image type="content" source="./media/import-data/jsonsource.png" alt-text="JSON 파일 원본 옵션의 스크린샷 - 데이터베이스 마이그레이션 도구":::

연결 문자열은 다음 형식입니다.

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>`

* `<CosmosDB Endpoint>`는 엔드포인트 URI입니다. Azure Portal에서 이 값을 가져올 수 있습니다. Azure Cosmos 계정으로 이동합니다. **개요** 창을 열고 **URI** 값을 복사합니다.
* `<AccountKey>`는 "암호" 또는 **기본 키** 입니다. Azure Portal에서 이 값을 가져올 수 있습니다. Azure Cosmos 계정으로 이동합니다. **연결 문자열** 또는 **키** 창을 열고 "암호" 또는 **기본 키** 값을 복사합니다.
* `<CosmosDB Database>`는 CosmosDB 데이터베이스 이름입니다.

예: `AccountEndpoint=https://myCosmosDBName.documents.azure.com:443/;AccountKey=wJmFRYna6ttQ79ATmrTMKql8vPri84QBiHTt6oinFkZRvoe7Vv81x9sn6zlVlBY10bEPMgGM982wfYXpWXWB9w==;Database=myDatabaseName`

> [!NOTE]
> Verify 명령을 사용하여 연결 문자열 필드에 지정된 Cosmos DB 계정에 액세스할 수 있는지 확인합니다.

JSON 파일을 가져오는 몇 가지 명령줄 샘플은 다음과 같습니다.

```console
#Import a single JSON file
dt.exe /s:JsonFile /s.Files:.\Sessions.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory of JSON files
dt.exe /s:JsonFile /s.Files:C:\TESessions\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Sessions /t.CollectionThroughput:2500

#Import a directory (including sub-directories) of JSON files
dt.exe /s:JsonFile /s.Files:C:\LastFMMusic\**\*.json /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Music /t.CollectionThroughput:2500

#Import a directory (single), directory (recursive), and individual JSON files
dt.exe /s:JsonFile /s.Files:C:\Tweets\*.*;C:\LargeDocs\**\*.*;C:\TESessions\Session48172.json;C:\TESessions\Session48173.json;C:\TESessions\Session48174.json;C:\TESessions\Session48175.json;C:\TESessions\Session48177.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:subs /t.CollectionThroughput:2500

#Import a single JSON file and partition the data across 4 collections
dt.exe /s:JsonFile /s.Files:D:\\CompanyData\\Companies.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:comp[1-4] /t.PartitionKey:name /t.CollectionThroughput:2500
```

## <a name="import-from-mongodb"></a><a id="MongoDB"></a>MongoDB에서 가져오기

> [!IMPORTANT]
> Azure Cosmos DB의 MongoDB API로 구성된 Cosmos 계정으로 가져오는 경우 이러한 [지침](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)을 따르세요.

MongoDB 원본 가져오기 옵션을 사용하면 단일 MongoDB 컬렉션에서 가져오고 필요에 따라 쿼리를 사용하여 문서를 필터링하거나 프로젝션을 사용하여 문서 구조를 수정할 수 있습니다.  

:::image type="content" source="./media/import-data/mongodbsource.png" alt-text="MongoDB 원본 옵션의 스크린샷":::

연결 문자열은 표준 MongoDB 형식입니다.

`mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database>`

> [!NOTE]
> Verify 명령을 사용하여 연결 문자열 필드에 지정된 MongoDB 인스턴스를 액세스할 수 있는지 확인합니다.

데이터를 가져올 컬렉션의 이름을 입력합니다. 필요에 따라 쿼리(예: `{pop: {$gt:5000}}`) 및/또는 프로젝션(예: `{loc:0}`)에 대한 파일을 제공하여 가져올 데이터를 필터링하고 형식을 지정할 수 있습니다.

MongoDB에서 가져오는 몇 가지 명령줄 샘플은 다음과 같습니다.

```console
#Import all documents from a MongoDB collection
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZips /t.IdField:_id /t.CollectionThroughput:2500

#Import documents from a MongoDB collection which match the query and exclude the loc field
dt.exe /s:MongoDB /s.ConnectionString:mongodb://<dbuser>:<dbpassword>@<host>:<port>/<database> /s.Collection:zips /s.Query:{pop:{$gt:50000}} /s.Projection:{loc:0} /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:BulkZipsTransform /t.IdField:_id/t.CollectionThroughput:2500
```

## <a name="import-mongodb-export-files"></a><a id="MongoDBExport"></a>MongoDB 내보내기 파일 가져오기

> [!IMPORTANT]
> MongoDB에 대한 지원을 포함한 Azure Cosmos DB 계정을 가져오는 경우 다음 [지침](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)을 수행합니다.

MongoDB 내보내기 JSON 파일 원본 가져오기 옵션을 사용하면 mongoexport 유틸리티에서 생성된 JSON 파일을 하나 이상 가져올 수 있습니다.  

:::image type="content" source="./media/import-data/mongodbexportsource.png" alt-text="MongoDB 내보내기 원본 옵션의 스크린샷":::

가져올 MongoDB 내보내기 JSON 파일이 포함된 폴더를 추가할 때 하위 폴더의 파일을 재귀적으로 검색하는 옵션이 있습니다.

MongoDB 내보내기 JSON 파일에서 가져오는 명령줄 샘플은 다음과 같습니다.

```console
dt.exe /s:MongoDBExport /s.Files:D:\mongoemployees.json /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:employees /t.IdField:_id /t.Dates:Epoch /t.CollectionThroughput:2500
```

## <a name="import-from-sql-server"></a><a id="SQL"></a>SQL Server에서 가져오기

SQL 원본 가져오기 옵션을 사용하면 개별 SQL Server 데이터베이스에서 가져오고 필요에 따라 쿼리를 사용하여 가져올 레코드를 필터링할 수 있습니다. 또한 중첩 구분 기호를 지정하여 문서 구조를 수정할 수 있습니다(추가 정보 제공 예정).  

:::image type="content" source="./media/import-data/sqlexportsource.png" alt-text="SQL 원본 옵션의 스크린샷 - 데이터베이스 마이그레이션 도구":::

연결 문자열의 형식은 표준 SQL 연결 문자열 형식입니다.

> [!NOTE]
> Verify 명령을 사용하여 연결 문자열 필드에 지정된 SQL Server 인스턴스를 액세스할 수 있는지 확인합니다.

중첩 구분 기호 속성을 사용하여 가져오는 동안 계층 관계(하위 문서)를 만듭니다. 다음과 같은 SQL 쿼리를 고려해 보세요.

`select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'`

다음과 같은 (부분) 결과가 반환됩니다.

:::image type="content" source="./media/import-data/sqlqueryresults.png" alt-text="SQL 쿼리 결과의 스크린샷":::

Address.AddressType 및 Address.Location.StateProvinceName과 같은 별칭을 확인합니다. 중첩 구분 기호 '.'을 지정하면 가져오기 도구가 가져오는 동안 Address 및 Address.Location 하위 문서를 만듭니다. Azure Cosmos DB의 결과 문서 예는 다음과 같습니다.

*{ "id": "956", "Name": "Finer Sales and Service", "Address": { "AddressType": "Main Office", "AddressLine1": "#500-75 O'Connor Street", "Location": { "City": "Ottawa", "StateProvinceName": "Ontario" }, "PostalCode": "K4B 1S2", "CountryRegionName": "Canada" } }*

SQL Server에서 가져오는 몇 가지 명령줄 샘플은 다음과 같습니다.

```console
#Import records from SQL which match a query
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, * from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Stores /t.IdField:Id /t.CollectionThroughput:2500

#Import records from sql which match a query and create hierarchical relationships
dt.exe /s:SQL /s.ConnectionString:"Data Source=<server>;Initial Catalog=AdventureWorks;User Id=advworks;Password=<password>;" /s.Query:"select CAST(BusinessEntityID AS varchar) as Id, Name, AddressType as [Address.AddressType], AddressLine1 as [Address.AddressLine1], City as [Address.Location.City], StateProvinceName as [Address.Location.StateProvinceName], PostalCode as [Address.PostalCode], CountryRegionName as [Address.CountryRegionName] from Sales.vStoreWithAddresses WHERE AddressType='Main Office'" /s.NestingSeparator:. /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:StoresSub /t.IdField:Id /t.CollectionThroughput:2500
```

## <a name="import-csv-files-and-convert-csv-to-json"></a><a id="CSV"></a>CSV 파일 가져오기 및 CSV를 JSON으로 변환

CSV 파일 원본 가져오기 옵션을 사용하면 하나 이상의 CSV 파일을 가져올 수 있습니다. 가져올 CSV 파일이 포함된 폴더를 추가하면, 하위 폴더에서 재귀적으로 파일을 검색하는 옵션도 있습니다.

:::image type="content" source="media/import-data/csvsource.png" alt-text="CSV 원본 옵션의 스크린샷 - CSV에서 JSON으로":::

SQL 원본과 마찬가지로, 중첩 구분 기호 속성을 사용하여 가져오는 동안 계층 관계(하위 문서)를 만들 수 있습니다. 다음과 같은 CSV 머리글 행과 데이터 행을 고려하세요.

:::image type="content" source="./media/import-data/csvsample.png" alt-text="CSV 샘플 레코드의 스크린샷 - CSV에서 JSON으로":::

DomainInfo.Domain_Name 및 RedirectInfo.Redirecting과 같은 별칭을 확인하세요. 중첩 구분 기호 '.'을 지정하면 가져오기 도구가 가져오는 동안 DomainInfo 및 RedirectInfo 하위 문서를 만듭니다. Azure Cosmos DB의 결과 문서 예는 다음과 같습니다.

*{ "DomainInfo": { "Domain_Name": “ACUS.GOV”, “Domain_Name_Address”: “https:\//www.ACUS.GOV” }, “Federal Agency”: "Administrative Conference of the United States", "RedirectInfo": { "Redirecting": "0", "Redirect_Destination": "" }, "id": "9cc565c5-ebcd-1c03-ebd3-cc3e2ecd814d" }*

가져오기 도구는 CSV 파일의 따옴표가 없는 값에 대한 형식 정보를 유추하려고 합니다(따옴표로 묶인 값은 항상 문자열로 처리됨).  숫자, 날짜/시간, 부울 순서로 형식이 식별됩니다.  

CSV 가져오기에 대해 다른 두 가지 사항을 기억해야 합니다.

1. 기본적으로 따옴표가 없는 값이 항상 잘리지만, 따옴표로 묶인 값은 그대로 유지됩니다. Trim 따옴표로 묶인 값 확인란을 선택 또는 /s.TrimQuoted 명령줄 옵션으로 이 동작을 재정의할 수 있습니다.
2. 기본적으로는 따옴표가 없는 null은 null 값으로 처리됩니다. 이 동작은 따옴표가 없는 NULL을 문자열 확인란이나 /s.NoUnquotedNulls 명령줄 옵션으로 재정의할 수 있습니다(예: 따옴표가 없는 null을 "null" 문자열로 처리).

CSV 가져오기에 대한 명령줄 샘플은 다음과 같습니다.

```console
dt.exe /s:CsvFile /s.Files:.\Employees.csv /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:Employees /t.IdField:EntityID /t.CollectionThroughput:2500
```

## <a name="import-from-azure-table-storage"></a><a id="AzureTableSource"></a>Azure Table Storage에서 가져오기

Azure Table Storage 원본 가져오기 옵션을 사용하면 개별 Azure Table Storage 테이블에서 가져올 수 있습니다. 필요에 따라 가져올 테이블 엔터티를 필터링할 수 있습니다.

Azure Table Storage에서 가져온 데이터를 Table API에서 사용하기 위해 Azure Cosmos DB 테이블 및 엔터티를 출력할 수 있습니다. 가져온 데이터를 SQL API에서 사용하기 위해 컬렉션 및 문서로 출력할 수도 있습니다. 그러나 Table API는 명령줄 유틸리티의 대상으로만 사용할 수 있습니다. 따라서 데이터 마이그레이션 도구 사용자 인터페이스를 사용하여 Table API로 내보낼 수 없습니다. 자세한 정보는 [Azure Cosmos DB Table API와 함께 사용할 데이터 가져오기](table/table-import.md)를 참조하세요.

:::image type="content" source="./media/import-data/azuretablesource.png" alt-text="Azure Table Storage 원본 옵션의 스크린샷":::

Azure Table Storage 연결 문자열의 형식은 다음과 같습니다.

`DefaultEndpointsProtocol=<protocol>;AccountName=<Account Name>;AccountKey=<Account Key>;`

> [!NOTE]
> Verify 명령을 사용하여 연결 문자열 필드에 지정된 Azure Table Storage 인스턴스를 액세스할 수 있는지 확인합니다.

가져올 Azure 테이블의 이름을 입력합니다. 필요에 따라 [필터](/visualstudio/azure/vs-azure-tools-table-designer-construct-filter-strings)를 지정할 수 있습니다.

Azure Table Storage 원본 가져오기 옵션에는 다음과 같은 추가 옵션이 있습니다.

1. 내부 필드 포함
   1. 모두 - 모든 내부 필드 포함(PartitionKey, RowKey 및 Timestamp)
   2. 없음 - 모든 내부 필드 제외
   3. RowKey - RowKey 필드만 포함
2. 열 선택
   1. Azure Table Storage 필터는 프로젝션을 지원하지 않습니다. 특정 Azure 테이블 엔터티 속성만 가져오려는 경우 열 선택 목록에 추가합니다. 다른 모든 엔터티 속성은 무시됩니다.

Azure Table Storage에서 가져오는 명령줄 샘플은 다음과 같습니다.

```console
dt.exe /s:AzureTable /s.ConnectionString:"DefaultEndpointsProtocol=https;AccountName=<Account Name>;AccountKey=<Account Key>" /s.Table:metrics /s.InternalFields:All /s.Filter:"PartitionKey eq 'Partition1' and RowKey gt '00001'" /s.Projection:ObjectCount;ObjectSize  /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:metrics /t.CollectionThroughput:2500
```

## <a name="import-from-amazon-dynamodb"></a><a id="DynamoDBSource"></a>Amazon DynamoDB에서 가져오기

Amazon DynamoDB 원본 가져오기 옵션을 사용하면 단일 Amazon DynamoDB 테이블에서 가져올 수 있습니다. 필요에 따라 가져올 엔터티를 필터링할 수 있습니다. 여러 템플릿이 제공되므로 가져오기를 최대한 쉽게 설정할 수 있습니다.

:::image type="content" source="./media/import-data/dynamodbsource1.png" alt-text="Amazon DynamoDB 원본 옵션의 스크린샷 - 데이터베이스 마이그레이션 도구.":::

:::image type="content" source="./media/import-data/dynamodbsource2.png" alt-text="템플릿이 있는 Amazon DynamoDB 원본 옵션의 스크린샷 - 데이터베이스 마이그레이션 도구.":::

Amazon DynamoDB 연결 문자열의 형식은 다음과 같습니다.

`ServiceURL=<Service Address>;AccessKey=<Access Key>;SecretKey=<Secret Key>;`

> [!NOTE]
> Verify 명령을 사용하여 연결 문자열 필드에 지정된 Amazon DynamoDB 인스턴스를 액세스할 수 있는지 확인합니다.

Amazon DynamoDB에서 가져오는 명령줄 샘플은 다음과 같습니다.

```console
dt.exe /s:DynamoDB /s.ConnectionString:ServiceURL=https://dynamodb.us-east-1.amazonaws.com;AccessKey=<accessKey>;SecretKey=<secretKey> /s.Request:"{   """TableName""": """ProductCatalog""" }" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<Azure Cosmos DB Endpoint>;AccountKey=<Azure Cosmos DB Key>;Database=<Azure Cosmos database>;" /t.Collection:catalogCollection /t.CollectionThroughput:2500
```

## <a name="import-from-azure-blob-storage"></a><a id="BlobImport"></a>Azure Blob Storage에서 가져오기

JSON 파일, MongoDB 내보내기 파일 및 CSV 파일 원본 가져오기 옵션을 통해 Azure Blob Storage에서 하나 이상의 파일을 가져올 수 있습니다. Blob 컨테이너 URL 및 계정 키를 지정한 후에 가져올 파일을 선택하는 정규식을 제공합니다.

:::image type="content" source="./media/import-data/blobsource.png" alt-text="Blob 파일 원본 옵션의 스크린샷":::

Azure Blob Storage에서 JSON 파일을 가져오려면 명령줄 예제는 다음과 같습니다.

```console
dt.exe /s:JsonFile /s.Files:"blobs://<account key>@account.blob.core.windows.net:443/importcontainer/.*" /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:doctest
```

## <a name="import-from-a-sql-api-collection"></a><a id="SQLSource"></a>SQL API 컬렉션에서 가져오기

Azure Cosmos DB 원본 가져오기 옵션을 사용하면 필요에 따라 쿼리를 사용하여 문서를 필터링하고 하나 이상의 Azure Cosmos 컨테이너에서 데이터를 가져올 수 있습니다.  

:::image type="content" source="./media/import-data/documentdbsource.png" alt-text="Azure Cosmos DB 원본 옵션의 스크린샷":::

Azure Cosmos DB 연결 문자열의 형식은 다음과 같습니다.

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

[Azure Cosmos DB 계정을 관리하는 방법](./how-to-manage-database-account.md)에 설명된 대로 Azure Portal의 키 페이지에서 Azure Cosmos DB 계정 연결 문자열을 검색할 수 있습니다. 그러나 데이터베이스의 이름을 다음 형식으로 연결 문자열에 추가해야 합니다.

`Database=<CosmosDB Database>;`

> [!NOTE]
> Verify 명령을 사용하여 연결 문자열 필드에 지정된 Azure Cosmos DB 인스턴스를 액세스할 수 있는지 확인합니다.

단일 Azure Cosmos 컨테이너에서 가져오려면 데이터를 가져올 컬렉션의 이름을 입력합니다. 둘 이상의 Azure Cosmos 컨테이너에서 가져오려면 하나 이상의 컬렉션 이름과 일치하는 정규식을 제공합니다(예: collection01 | collection02 | collection03). 필요에 따라 쿼리를 지정하거나 쿼리에 대한 파일을 제공하여 가져올 데이터를 필터링하고 형식을 지정할 수 있습니다.

> [!NOTE]
> 컬렉션 필드 이름이 정규식 문자를 포함하므로 이름에 정규식 문자를 포함한 단일 컬렉션에서 가져오는 경우 해당 문자는 이스케이프되어야 합니다.

Azure Cosmos DB 원본 가져오기 옵션에는 다음과 같은 고급 옵션이 있습니다.

1. 내부 필드 포함: 내보내기에 Azure Cosmos DB 문서 시스템 속성(예: _rid, _ts)을 포함할지 여부를 지정합니다.
2. 실패 시 재시도 횟수: 일시적 오류(예: 네트워크 연결 중단)의 경우 Azure Cosmos DB에 대한 연결을 재시도할 횟수를 지정합니다.
3. 재시도 간격: 일시적 오류(예: 네트워크 연결 중단)의 경우 Azure Cosmos DB에 대한 연결을 재시도하는 간격을 지정합니다.
4. 연결 모드: Azure Cosmos DB에 사용할 연결 모드를 지정합니다. 사용 가능한 선택 사항은 DirectTcp, DirectHttps 및 게이트웨이입니다. 직접 연결 모드는 더 빠르고, 게이트웨이 모드는 포트 443만 사용하므로 더 방화벽 친화적입니다.

:::image type="content" source="./media/import-data/documentdbsourceoptions.png" alt-text="Azure Cosmos DB 원본 고급 옵션의 스크린샷":::

> [!TIP]
> 가져오기 도구는 기본적으로 DirectTcp 연결 모드로 설정되어 있습니다. 방화벽 문제가 발생하는 경우 포트 443만 요구하는 게이트웨이 연결 모드로 전환합니다.

Azure Cosmos DB에서 가져오는 몇 가지 명령줄 샘플은 다음과 같습니다.

```console
#Migrate data from one Azure Cosmos container to another Azure Cosmos containers
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:TEColl /t:DocumentDBBulk /t.ConnectionString:" AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:TESessions /t.CollectionThroughput:2500

#Migrate data from more than one Azure Cosmos container to a single Azure Cosmos container
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:comp1|comp2|comp3|comp4 /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:singleCollection /t.CollectionThroughput:2500

#Export an Azure Cosmos container to a JSON file
dt.exe /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /s.Collection:StoresSub /t:JsonFile /t.File:StoresExport.json /t.Overwrite
```

> [!TIP]
> 또한 Azure Cosmos DB 데이터 가져오기 도구는 [Azure Cosmos DB 에뮬레이터](local-emulator.md)에서 데이터를 가져오도록 지원합니다. 로컬 에뮬레이터에서 데이터를 가져올 때 엔드포인트를 `https://localhost:<port>`로 설정합니다.

## <a name="import-from-hbase"></a><a id="HBaseSource"></a>HBase에서 가져오기

HBase 원본 가져오기 옵션을 사용하면 HBase 테이블에서 데이터를 가져오고 필요에 따라 데이터를 필터링할 수 있습니다. 여러 템플릿이 제공되므로 가져오기를 최대한 쉽게 설정할 수 있습니다.

:::image type="content" source="./media/import-data/hbasesource1.png" alt-text="HBase 원본 옵션의 스크린샷.":::

:::image type="content" source="./media/import-data/hbasesource2.png" alt-text="필터 컨텍스트 메뉴가 확장된 HBase 원본 옵션의 스크린샷.":::

HBase Stargate 연결 문자열의 형식은 다음과 같습니다.

`ServiceURL=<server-address>;Username=<username>;Password=<password>`

> [!NOTE]
> Verify 명령을 사용하여 연결 문자열 필드에 지정된 HBase 인스턴스를 액세스할 수 있는지 확인합니다.

HBase에서 가져오는 명령줄 샘플은 다음과 같습니다.

```console
dt.exe /s:HBase /s.ConnectionString:ServiceURL=<server-address>;Username=<username>;Password=<password> /s.Table:Contacts /t:DocumentDBBulk /t.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;" /t.Collection:hbaseimport
```

## <a name="import-to-the-sql-api-bulk-import"></a><a id="SQLBulkTarget"></a>SQL API로 가져오기(대량 가져오기)

Azure Cosmos DB 대량 가져오기를 사용하면 효율성을 위해 Azure Cosmos DB 저장 프로시저를 통해 사용 가능한 모든 원본 옵션에서 가져올 수 있습니다. 도구는 하나의 단일 분할 Azure Cosmos 컨테이너로의 가져오기를 지원합니다. 또한 둘 이상의 단일 분할 Azure Cosmos 컨테이너 간에 데이터를 분할하는 분할된 가져오기를 지원합니다. 데이터를 분할하는 방법에 대한 자세한 내용은 [Azure Cosmos DB에서 분할 및 크기 조정](partitioning-overview.md)을 참조하세요. 이 도구는 대상 컬렉션에서 저장 프로시저를 만들고 실행한 다음 삭제합니다.  

:::image type="content" source="./media/import-data/documentdbbulk.png" alt-text="Azure Cosmos DB 대량 옵션의 스크린샷":::

Azure Cosmos DB 연결 문자열의 형식은 다음과 같습니다.

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

[Azure Cosmos DB 계정을 관리하는 방법](./how-to-manage-database-account.md)에 설명된 대로 Azure Cosmos DB 계정 연결 문자열은 Azure Portal의 키 페이지에서 검색할 수 있지만 데이터베이스의 이름은 다음 형식으로 연결 문자열에 추가되어야 합니다.

`Database=<CosmosDB Database>;`

> [!NOTE]
> Verify 명령을 사용하여 연결 문자열 필드에 지정된 Azure Cosmos DB 인스턴스를 액세스할 수 있는지 확인합니다.

단일 컬렉션으로 가져오려면, 데이터를 가져올 컬렉션의 이름을 입력하고 추가 단추를 클릭합니다. 둘 이상의 컬렉션을 가져오려면 개별적으로 각 컬렉션 이름을 입력하거나 둘 이상의 컬렉션을 지정하려면 다음 구문을 사용합니다. *collection_prefix*[start index - end index]. 앞서 언급한 구문을 사용하여 둘 이상의 컬렉션을 지정할 때 다음 지침에 유의해야 합니다.

1. 정수 범위 이름 패턴만 지원됩니다. 예를 들어, 컬렉션 [0-3]을 지정하면 collection0, collection1, collection2, collection3 컬렉션을 생성합니다.
2. 축약된 구문을 사용할 수 있습니다. [3] 컬렉션은 1단계에서 설명한 동일한 집합을 생성합니다.
3. 둘 이상의 대체를 제공할 수 있습니다. 예를 들어, 컬렉션 [0-1] [0-9]는 0이 이름 앞에 오는 20개의 컬렉션을 생성합니다(collection01, ..02, ..03).

컬렉션 이름이 지정되면 원하는 컬렉션 처리량을 선택합니다(400RU~10,000RU). 가져오기 성능을 최적화하려면 더 높은 처리량을 선택합니다. 성능 수준에 대한 자세한 내용은 [Azure Cosmos DB의 성능 수준](performance-levels.md)을 참조하세요.

> [!NOTE]
> 성능 처리량 설정은 컬렉션 생성에만 적용됩니다. 지정된 컬렉션이 이미 있는 경우 해당 처리량은 수정되지 않습니다.

둘 이상의 컬렉션을 가져올 때 가져오기 도구는 해시 기반 분할을 지원합니다. 이 시나리오에서는 파티션 키로 사용하려는 문서 속성을 지정합니다. (파티션 키를 비워 두면 대상 컬렉션에서 문서가 임의로 분할됩니다.)

필요에 따라 가져오는 동안 Azure Cosmos DB 문서 ID 속성으로 사용할 가져오기 원본의 필드를 지정할 수 있습니다. 문서에 이 속성에 없는 경우 가져오기 도구가 ID 속성 값으로 GUID를 생성합니다.

가져오는 동안 사용할 수 있는 다양한 고급 옵션이 있습니다. 먼저, 도구에 기본 대량 가져오기 저장 프로시저(BulkInsert.js)가 포함되어 있지만 사용자 고유의 가져오기 저장 프로시저를 지정할 수 있습니다.

 :::image type="content" source="./media/import-data/bulkinsertsp.png" alt-text="Azure Cosmos DB 대량 삽입 저장 프로시저 옵션의 스크린샷":::

또한 SQL Server 또는 MongoDB 등에서 날짜 형식을 가져오는 경우 다음 3개의 가져오기 옵션 중에서 선택할 수 있습니다.

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Azure Cosmos DB 날짜/시간 가져오기 옵션의 스크린샷":::

* 문자열: 문자열 값으로 유지
* Epoch: Epoch 숫자 값으로 유지
* 둘 다: 문자열 및 Epoch 숫자 값으로 유지. 이 옵션은 하위 문서를 만듭니다. 예: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Azure Cosmos DB 대량 가져오기에는 다음과 같은 추가 고급 옵션이 있습니다.

1. Batch 크기: 기본적으로 도구의 배치 크기는 50으로 설정되어 있습니다.  가져올 문서가 크면 배치 크기를 줄이는 것이 좋습니다. 반대로, 가져올 문서가 작으면 배치 크기를 늘리는 것이 좋습니다.
2. 최대 스크립트 크기(바이트): 기본적으로 도구의 최대 스크립트 크기는 512KB입니다.
3. 자동 Id 생성 사용 안 함: 가져올 모든 문서에 ID 필드가 포함되어 있는 경우 이 옵션을 선택하면 성능을 향상시킬 수 있습니다. 고유 ID 필드가 누락된 문서는 가져오지 않습니다.
4. 기존 문서 업데이트 이 도구는 기본적으로 기존 문서를 충돌하는 ID로 대체하지 않습니다. 이 옵션을 선택하면 기존 문서를 일치하는 ID로 덮어쓸 수 있습니다. 이 기능은 기존 문서를 업데이트하는 예약된 데이터 마이그레이션에 유용합니다.
5. 실패 시 재시도 횟수: 일시적 오류(예: 네트워크 연결 중단)의 경우 Azure Cosmos DB에 대한 연결을 다시 시도하는 빈도를 지정합니다.
6. 재시도 간격: 일시적 오류(예: 네트워크 연결 중단)의 경우 Azure Cosmos DB에 대한 연결을 재시도하는 간격을 지정합니다.
7. 연결 모드: Azure Cosmos DB에 사용할 연결 모드를 지정합니다. 사용 가능한 선택 사항은 DirectTcp, DirectHttps 및 게이트웨이입니다. 직접 연결 모드는 더 빠르고, 게이트웨이 모드는 포트 443만 사용하므로 더 방화벽 친화적입니다.

:::image type="content" source="./media/import-data/docdbbulkoptions.png" alt-text="Azure Cosmos DB 대량 가져오기 고급 옵션의 스크린샷":::

> [!TIP]
> 가져오기 도구는 기본적으로 DirectTcp 연결 모드로 설정되어 있습니다. 방화벽 문제가 발생하는 경우 포트 443만 요구하는 게이트웨이 연결 모드로 전환합니다.

## <a name="import-to-the-sql-api-sequential-record-import"></a><a id="SQLSeqTarget"></a>SQL API로 가져오기(순차 레코드 가져오기)

Azure Cosmos DB 순차 레코드 가져오기를 사용하면 레코드 단위로 사용 가능한 모든 원본 옵션에서 가져올 수 있습니다. 저장 프로시저의 할당량에 도달한 기존 컬렉션으로 가져오는 경우 이 옵션을 선택할 수 있습니다. 도구는 단일(단일 파티션 및 다중 파티션 모두) Azure Cosmos 컨테이너로의 가져오기를 지원합니다. 또한 둘 이상의 단일 파티션 또는 다중 파티션 Azure Cosmos 컨테이너 간에 데이터를 분할하는 분할된 가져오기를 지원합니다. 데이터를 분할하는 방법에 대한 자세한 내용은 [Azure Cosmos DB에서 분할 및 크기 조정](partitioning-overview.md)을 참조하세요.

:::image type="content" source="./media/import-data/documentdbsequential.png" alt-text="Azure Cosmos DB 순차 레코드 가져오기 옵션의 스크린샷":::

Azure Cosmos DB 연결 문자열의 형식은 다음과 같습니다.

`AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB Database>;`

[Azure Cosmos DB 계정을 관리하는 방법](./how-to-manage-database-account.md)에 설명된 대로 Azure Portal의 키 페이지에서 Azure Cosmos DB 계정에 대한 연결 문자열을 검색할 수 있습니다. 그러나 데이터베이스의 이름을 다음 형식으로 연결 문자열에 추가해야 합니다.

`Database=<Azure Cosmos database>;`

> [!NOTE]
> Verify 명령을 사용하여 연결 문자열 필드에 지정된 Azure Cosmos DB 인스턴스를 액세스할 수 있는지 확인합니다.

단일 컬렉션으로 가져오려면, 데이터를 가져올 컬렉션의 이름을 입력하고 추가 단추를 클릭합니다. 둘 이상의 컬렉션을 가져오려면 각 컬렉션 이름을 따로 입력합니다. 둘 이상의 컬렉션을 지정하려면 다음 구문을 사용할 수도 있습니다. *collection_prefix*[start index - end index]. 앞서 언급한 구문을 사용하여 둘 이상의 컬렉션을 지정할 때 다음 지침에 유의해야 합니다.

1. 정수 범위 이름 패턴만 지원됩니다. 예를 들어, 컬렉션 [0-3]을 지정하면 collection0, collection1, collection2, collection3 컬렉션을 생성합니다.
2. 축약된 구문을 사용할 수 있습니다. [3] 컬렉션은 1단계에서 설명한 동일한 집합을 생성합니다.
3. 둘 이상의 대체를 제공할 수 있습니다. 예를 들어, 컬렉션 [0-1] [0-9]는 0이 이름 앞에 오는 20개의 컬렉션을 생성합니다(collection01, ..02, ..03).

컬렉션 이름이 지정되면 원하는 컬렉션 처리량을 선택합니다(400RU~250,000RU). 가져오기 성능을 최적화하려면 더 높은 처리량을 선택합니다. 성능 수준에 대한 자세한 내용은 [Azure Cosmos DB의 성능 수준](performance-levels.md)을 참조하세요. 처리량 >10,000RU로 컬렉션에 가져오기는 파티션 키가 필요합니다. 250,000RU 이상을 보유하도록 선택한 경우에는 계정을 늘리도록 포털에 요청을 접수해야 합니다.

> [!NOTE]
> 처리량 설정은 컬렉션 또는 데이터베이스 생성에만 적용됩니다. 지정된 컬렉션이 이미 있는 경우 해당 처리량은 수정되지 않습니다.

둘 이상의 컬렉션을 가져올 때 가져오기 도구는 해시 기반 분할을 지원합니다. 이 시나리오에서는 파티션 키로 사용하려는 문서 속성을 지정합니다. (파티션 키를 비워 두면 대상 컬렉션에서 문서가 임의로 분할됩니다.)

필요에 따라 가져오는 동안 Azure Cosmos DB 문서 ID 속성으로 사용할 가져오기 원본의 필드를 지정할 수 있습니다. (문서에 이 속성에 없는 경우 가져오기 도구가 ID 속성 값으로 GUID를 생성합니다.)

가져오는 동안 사용할 수 있는 다양한 고급 옵션이 있습니다. 먼저, SQL Server 또는 MongoDB 등에서 날짜 형식을 가져오는 경우 다음 3개의 가져오기 옵션 중에서 선택할 수 있습니다.

 :::image type="content" source="./media/import-data/datetimeoptions.png" alt-text="Azure Cosmos DB 날짜/시간 가져오기 옵션의 스크린샷":::

* 문자열: 문자열 값으로 유지
* Epoch: Epoch 숫자 값으로 유지
* 둘 다: 문자열 및 Epoch 숫자 값으로 유지. 이 옵션은 하위 문서를 만듭니다. 예: "date_joined": { "Value": "2013-10-21T21:17:25.2410000Z", "Epoch": 1382390245 }

Azure Cosmos DB - 순차 레코드 가져오기에는 다음과 같은 추가 고급 옵션이 있습니다.

1. 병렬 요청 수: 기본적으로 도구의 병렬 요청 수는 2개로 설정됩니다. 가져올 문서가 작으면 병렬 요청 수를 늘리는 것이 좋습니다. 이 개수를 너무 많이 늘리면 가져오기 시 속도 제한이 발생할 수 있습니다.
2. 자동 Id 생성 사용 안 함: 가져올 모든 문서에 ID 필드가 포함되어 있는 경우 이 옵션을 선택하면 성능을 향상시킬 수 있습니다. 고유 ID 필드가 누락된 문서는 가져오지 않습니다.
3. 기존 문서 업데이트 이 도구는 기본적으로 기존 문서를 충돌하는 ID로 대체하지 않습니다. 이 옵션을 선택하면 기존 문서를 일치하는 ID로 덮어쓸 수 있습니다. 이 기능은 기존 문서를 업데이트하는 예약된 데이터 마이그레이션에 유용합니다.
4. 실패 시 재시도 횟수: 일시적 오류(예: 네트워크 연결 중단)의 경우 Azure Cosmos DB에 대한 연결을 다시 시도하는 빈도를 지정합니다.
5. 재시도 간격: 일시적 오류(예: 네트워크 연결 중단)의 경우 Azure Cosmos DB에 대한 연결을 다시 시도하는 간격을 지정합니다.
6. 연결 모드: Azure Cosmos DB에 사용할 연결 모드를 지정합니다. 사용 가능한 선택 사항은 DirectTcp, DirectHttps 및 게이트웨이입니다. 직접 연결 모드는 더 빠르고, 게이트웨이 모드는 포트 443만 사용하므로 더 방화벽 친화적입니다.

:::image type="content" source="./media/import-data/documentdbsequentialoptions.png" alt-text="Azure Cosmos DB 순차 레코드 가져오기 고급 옵션의 스크린샷":::

> [!TIP]
> 가져오기 도구는 기본적으로 DirectTcp 연결 모드로 설정되어 있습니다. 방화벽 문제가 발생하는 경우 포트 443만 요구하는 게이트웨이 연결 모드로 전환합니다.

## <a name="specify-an-indexing-policy"></a><a id="IndexingPolicy"></a>인덱싱 정책 지정

가져오는 동안 마이그레이션 도구가 Azure Cosmos DB SQL API 컬렉션을 만들 수 있도록 하는 경우 컬렉션의 인덱싱 정책을 지정할 수 있습니다. Azure Cosmos DB 대량 가져오기의 고급 옵션 섹션 및 Azure Cosmos DB 순차 레코드 옵션에서 인덱싱 정책 섹션으로 이동합니다.

:::image type="content" source="./media/import-data/indexingpolicy1.png" alt-text="Azure Cosmos DB 인덱싱 정책 고급 옵션의 스크린샷.":::

인덱싱 정책 고급 옵션을 사용하여 인덱싱 정책 파일을 선택하고 인덱싱 정책을 수동으로 입력하거나 인덱싱 정책 텍스트 상자를 마우스 오른쪽 단추로 클릭하여 일련의 기본 템플릿 중에 선택할 수 있습니다.

도구가 제공하는 정책 템플릿은 다음과 같습니다.

* 기본값 이 정책은 문자열에 대해 같음 쿼리를 수행할 때 가장 적합합니다. 숫자에 대해 ORDER BY, 범위 및 같음 쿼리를 사용할 때도 적용됩니다. 이 정책에는 범위보다 더 낮은 인덱스 스토리지 오버헤드가 있습니다.
* 범위입니다. 이 정책은 숫자와 문자열 모두에 ORDER BY, 범위 및 같음 쿼리를 사용할 때 가장 좋습니다. 이 정책에는 기본값 또는 해시보다 더 높은 인덱스 스토리지 오버헤드가 있습니다.

:::image type="content" source="./media/import-data/indexingpolicy2.png" alt-text="대상 정보를 지정하는 Azure Cosmos DB 인덱싱 정책 고급 옵션의 스크린샷.":::

> [!NOTE]
> 인덱싱 정책을 지정하지 않으면 기본 정책이 적용됩니다. 인덱싱 정책에 대한 자세한 내용은 [Azure Cosmos DB 인덱싱 정책](index-policy.md)을 참조하세요.

## <a name="export-to-json-file"></a>JSON 파일로 내보내기

Azure Cosmos DB JSON 내보내기를 사용하면 사용 가능한 모든 원본 옵션을 JSON 문서 배열이 포함된 JSON 파일로 내보낼 수 있습니다. 이 도구는 자동으로 내보내기를 처리합니다. 또는 사용자가 결과 마이그레이션 명령을 보고 직접 명령을 실행할 수 있습니다. 결과 JSON 파일은 로컬로 또는 Azure Blob Storage에 저장될 수도 있습니다.

:::image type="content" source="./media/import-data/jsontarget.png" alt-text="Azure Cosmos DB JSON 로컬 파일 내보내기 옵션의 스크린샷":::

:::image type="content" source="./media/import-data/jsontarget2.png" alt-text="Azure Cosmos DB JSON Azure Blob 스토리지 내보내기 옵션의 스크린샷":::

필요에 따라 결과 JSON을 꾸밀 수 있습니다. 이 경우 콘텐츠의 가독성은 향상되지만 결과 문서의 크기가 증가합니다.

* 표준 JSON 내보내기

  ```JSON
  [{"id":"Sample","Title":"About Paris","Language":{"Name":"English"},"Author":{"Name":"Don","Location":{"City":"Paris","Country":"France"}},"Content":"Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.","PageViews":10000,"Topics":[{"Title":"History of Paris"},{"Title":"Places to see in Paris"}]}]
  ```

* 꾸민 JSON 내보내기

  ```JSON
    [
     {
    "id": "Sample",
    "Title": "About Paris",
    "Language": {
      "Name": "English"
    },
    "Author": {
      "Name": "Don",
      "Location": {
        "City": "Paris",
        "Country": "France"
      }
    },
    "Content": "Don's document in Azure Cosmos DB is a valid JSON document as defined by the JSON spec.",
    "PageViews": 10000,
    "Topics": [
      {
        "Title": "History of Paris"
      },
      {
        "Title": "Places to see in Paris"
      }
    ]
    }]
  ```

다음은 JSON 파일을 Azure Blob Storage로 내보내는 명령줄 샘플입니다.

```console
dt.exe /ErrorDetails:All /s:DocumentDB /s.ConnectionString:"AccountEndpoint=<CosmosDB Endpoint>;AccountKey=<CosmosDB Key>;Database=<CosmosDB database_name>" /s.Collection:<CosmosDB collection_name>
/t:JsonFile /t.File:"blobs://<Storage account key>@<Storage account name>.blob.core.windows.net:443/<Container_name>/<Blob_name>"
/t.Overwrite
```

## <a name="advanced-configuration"></a>고급 구성

고급 구성 화면에서 원하는 모든 오류가 기록된 로그 파일의 위치를 지정합니다. 이 페이지에는 다음 규칙이 적용됩니다.

1. 파일 이름을 제공하지 않으면, 모든 오류는 결과 페이지에 반환됩니다.
2. 파일 이름에서 디렉터리가 없는 경우 파일은 현재 환경 디렉터리에 만들어지거나 덮어씁니다.
3. 기존 파일을 선택하는 경우 파일을 덮어쓰며 추가 옵션은 없습니다.
4. 그런 다음 모든 메시지를 기록할지, 중요한 메시지를 기록할지 또는 오류 없음 메시지를 기록할지 선택합니다. 마지막으로, 화면 전송 메시지를 해당 진행률과 함께 업데이트할 빈도를 결정합니다.

   :::image type="content" source="./media/import-data/AdvancedConfiguration.png" alt-text="고급 구성 화면의 스크린샷":::

## <a name="confirm-import-settings-and-view-command-line"></a>가져오기 설정 확인 및 명령줄 보기

1. 원본 정보, 대상 정보 및 고급 구성을 지정한 후, 마이그레이션 요약을 검토하고, 결과 마이그레이션 명령을 확인/복사합니다. (명령 복사는 가져오기 작업을 자동화하는 데 유용합니다.)

    :::image type="content" source="./media/import-data/summary.png" alt-text="요약 화면의 스크린샷.":::

    :::image type="content" source="./media/import-data/summarycommand.png" alt-text="명령줄 미리 보기가 있는 요약 화면의 스크린샷.":::

2. 원본 및 대상 옵션이 적절하면 **가져오기** 를 클릭합니다. 고급 구성에서 파일 이름을 지정하지 않은 경우 가져오기가 처리되는 동안 경과된 시간, 전송 횟수 및 실패 정보가 업데이트됩니다. 완료되면 결과를 내보낼 수 있습니다(예: 가져오기 오류를 처리하기 위해).

    :::image type="content" source="./media/import-data/viewresults.png" alt-text="Azure Cosmos DB JSON 내보내기 옵션의 스크린샷.":::

3. 모든 값을 다시 설정하거나 기존 설정을 유지하여 새 가져오기를 시작할 수도 있습니다. (예를 들어, 연결 문자열 정보, 원본 및 대상 선택 등을 유지하도록 선택할 수 있습니다.)

    :::image type="content" source="./media/import-data/newimport.png" alt-text="새 가져오기 확인 대화 상자가 있는 Azure Cosmos DB JSON 내보내기 옵션의 스크린샷.":::

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 수행했습니다.

> [!div class="checklist"]
> * 데이터 마이그레이션 도구 설치
> * 여러 데이터 원본에서 데이터 가져오기
> * Azure Cosmos DB에서 JSON으로 내보내기

이제 다음 자습서로 진행하여 Azure Cosmos DB를 사용하여 데이터를 쿼리하는 방법을 알아볼 수 있습니다.

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요?
  * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
  * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 플래너를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.

> [!div class="nextstepaction"]
>[데이터는 어떻게 쿼리하나요?](../cosmos-db/tutorial-query-sql-api.md)
