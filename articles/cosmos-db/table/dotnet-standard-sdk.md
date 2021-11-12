---
title: Azure Cosmos DB Table API .NET Standard SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜, 각 버전 간의 변경 내용을 포함하여 Azure Cosmos DB Table API 및 .NET Standard SDK에 대한 모든 것을 알아봅니다.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/03/2021
ms.custom: devx-track-dotnet
ms.openlocfilehash: fdf88bd530eedb37ff2637c7daf43433d9a0e427
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284420"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET Standard API: 다운로드 및 릴리스 정보
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]
> [!div class="op_single_selector"]
> 
> * [.NET](dotnet-sdk.md)
> * [.NET Standard](dotnet-standard-sdk.md)
> * [Java](java-sdk.md)
> * [Node.JS](nodejs-sdk.md)
> * [Python](python-sdk.md)

|   | 링크  |
|---|---|
|**SDK 다운로드**|[NuGet](https://www.nuget.org/packages/Azure.Data.Tables/)|
|**샘플**|[Cosmos DB Table API .NET 샘플](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**빠른 시작**|[빠른 시작](create-table-dotnet.md)|
|**자습서**|[자습서](tutorial-develop-table-dotnet.md)|
|**현재 지원되는 프레임워크**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**보고서 문제**|[보고서 문제](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>2\.0.0 시리즈에 대한 릴리스 정보
2.0.0 시리즈는 [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)에 대한 종속성을 사용하여 성능 향상 및 네임스페이스 통합 기능을 Cosmos DB 엔드포인트에 제공합니다.

### <a name="200-preview"></a><a name="2.0.0-preview"></a>2.0.0-preview
* [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)에 대한 종속성을 사용하는 2.0.0 Table SDK의 초기 미리 보기는 성능 향상 및 네임스페이스 통합 기능을 Cosmos DB 엔드포인트에 제공합니다. 공용 API는 동일하게 유지됩니다.

## <a name="release-notes-for-100-series"></a>1\.0.0 시리즈에 대한 릴리스 정보
1.0.0 시리즈는 [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)에 대한 종속성을 사용합니다.

### <a name="108"></a><a name="1.0.8"></a>1.0.8
* cosmosdb 엔드포인트인 경우 TTL 속성 설정 지원 추가 
* 제한 시간 및 작업 취소 예외 시 다시 시도 정책 준수
* asp .net 애플리케이션에 표시되는 간헐적 작업 취소 예외 수정
* 보조 엔드포인트 전용 위치 모드에서 Azure 테이블 스토리지 검색 수정
* `Microsoft.Azure.DocumentDB.Core` 종속성 버전을 간헐적 null 참조 예외를 해결하는 2.11.2로 업데이트합니다.
* `Odata.Core` 종속성 버전을 Azure Shell과의 호환성 충돌을 해결하는 7.6.4로 업데이트합니다.

### <a name="107"></a><a name="1.0.7"></a>1.0.7
* Table SDK 기본 추적 수준을 app.config를 통해 옵트인할 수 있는 SourceLevels.Off로 설정하여 성능 향상

### <a name="105"></a><a name="1.0.5"></a>1.0.5
* Rest Executor를 사용하여 Cosmos DB Table API와 통신하도록 TableClientConfiguration 아래에 새 구성 소개

### <a name="105-preview"></a><a name="1.0.5-preview"></a>1.0.5-preview
* 버그 수정

### <a name="104"></a><a name="1.0.4"></a>1.0.4
* 버그 수정
* RestExecutorConfiguration에 대한 HttpClientTimeout 옵션을 제공합니다.

### <a name="104-preview"></a><a name="1.0.4-preview"></a>1.0.4-preview
* 버그 수정
* RestExecutorConfiguration에 대한 HttpClientTimeout 옵션을 제공합니다.

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* 버그 수정

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* 일반 공급 릴리스

### <a name="0110-preview"></a><a name="0.11.0-preview"></a>0.11.0-preview

* CloudTableClient를 구성할 수 있는 방법이 변경되었습니다. 이제 구성 중에 TableClientConfiguration 개체를 가져옵니다. TableClientConfiguration은 대상 엔드포인트가 Cosmos DB Table API 또는 Azure Storage Table API에 따라 클라이언트 동작을 구성하는 다양한 속성을 제공합니다.
* 사용자 지정 열에 정렬된 순서로 결과를 반환하도록 TableQuery에 대한 지원이 추가되었습니다. 이 기능은 Cosmos DB Table 엔드포인트에서만 지원됩니다.
* 다양한 결과 형식에 대한 RequestCharges를 노출하는 지원이 추가되었습니다. 이 기능은 Cosmos DB Table 엔드포인트에서만 지원됩니다.

### <a name="0101-preview"></a><a name="0.10.1-preview"></a>0.10.1-preview
* Azure Storage Table 엔드포인트에 대한 TablePermissions, ServiceProperties 및 ServiceStats 작업, SAS 토큰 지원을 추가합니다. 
   > [!NOTE]
   > 클라이언트 쪽 암호화와 같은 이전 Azure Storage Table SDK의 일부 기능은 아직 지원되지 않습니다.

### <a name="0100-preview"></a><a name="0.10.0-preview"></a>0.10.0-preview
* Azure Storage Table 엔드포인트에 대한 핵심 CRUD, 배치 및 쿼리 작업 지원을 추가합니다. 
   > [!NOTE]
   > 클라이언트 쪽 암호화와 같은 이전 Azure Storage Table SDK의 일부 기능은 아직 지원되지 않습니다.

### <a name="091-preview"></a><a name="0.9.1-preview"></a>0.9.1-preview
* Azure Cosmos DB Table.NET Standard SDK는 Cosmos DB의 테이블 데이터 모델에 효율적으로 액세스할 수 있는 플랫폼 간 .NET 라이브러리입니다. 이 초기 릴리스는 [Cosmos DB Table SDK For .NET Framework](dotnet-sdk.md)와 비슷한 API를 사용하여 테이블 및 엔터티 CRUD + 쿼리 기능 전체 집합을 지원합니다. 
   > [!NOTE]
   >  Azure Storage Table 엔드포인트는 아직 0.9.1-preview 버전에서 지원되지 않습니다.

## <a name="release-and-retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 최신/지원 버전으로 원활히 전환할 수 있도록 SDK 사용 중지 최소 **12개월** 전에 알림을 제공합니다.

이 플랫폼 간 .NET Standard 라이브러리 [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)은 .NET Framework 라이브러리 [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)을 대체합니다.

### <a name="200-series"></a>2.0.0 시리즈
| 버전 | 출시 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [2.0.0-preview](#2.0.0-preview) |2019년 8월 22일 |--- |

### <a name="100-series"></a>1.0.0 시리즈
| 버전 | 출시 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [1.0.5](#1.0.5) |2019년 9월 13일 |--- |
| [1.0.5-preview](#1.0.5-preview) |2019년 8월 20일 |--- |
| [1.0.4](#1.0.4) |2019년 8월 12일 |--- |
| [1.0.4-preview](#1.0.4-preview) |2019년 7월 26일 |--- |
| 1.0.2-preview |2019년 5월 2일 |--- |
| [1.0.1](#1.0.1) |2019년 4월 19일 |--- |
| [1.0.0](#1.0.0) |2019년 3월 13일 |--- |
| [0.11.0-preview](#0.11.0-preview) |2019년 3월 5일 |--- |
| [0.10.1-preview](#0.10.1-preview) |2019년 1월 22일 |--- |
| [0.10.0-preview](#0.10.0-preview) |2018년 12월 18일 |--- |
| [0.9.1-preview](#0.9.1-preview) |2018년 10월 18일 |--- |


## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Azure Cosmos DB Table API에 대한 자세한 내용은 [Azure Cosmos DB Table API 소개](introduction.md)를 참조하세요.
