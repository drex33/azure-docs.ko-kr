---
title: Azure Cosmos DB는 SQL .NET Core API, SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB .NET Core SDK의 각 버전 간 변경 내용을 포함하여 SQL .NET Core API 및 SDK에 대한 모든 것을 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/11/2021
ms.author: anfeldma
ms.custom: devx-track-dotnet
ms.openlocfilehash: cbb743c9bf1ca731e2cefb71c1aa8ccf18e1c960
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132750799"
---
# <a name="azure-cosmos-db-net-core-sdk-v2-for-sql-api-release-notes-and-resources-legacy"></a>SQL API용 Azure Cosmos DB .NET Core SDK v2: 릴리스 정보 및 리소스(레거시)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 변경 피드 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP 커넥터](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP 커넥터](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api
> * [REST 리소스 공급자](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-query-getting-started.md)
> * [대량 실행기 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [대량 실행기 - Java](sql-api-sdk-bulk-executor-java.md)

| | 링크 |
|---|---|
|**SDK 다운로드**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**API 설명서**|[.NET API 참조 설명서](/dotnet/api/overview/azure/cosmosdb)|
|**샘플**|[.NET 코드 샘플](sql-api-dotnet-samples.md)|
|**시작**|[Azure Cosmos DB .NET 시작](sql-api-sdk-dotnet.md)|
|**웹앱 자습서**|[Azure Cosmos DB를 사용한 웹 애플리케이션 개발](sql-api-dotnet-application.md)|
|**현재 지원되는 프레임워크**|[.NET Standard 1.6 및 .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)|

> [!WARNING]
> 2024년 8월 31일에 Azure Cosmos DB .NET SDK v2.x가 사용 중지됩니다. SDK 및 SDK를 사용하는 모든 애플리케이션은 계속 작동합니다. Azure Cosmos DB는 이 SDK에 대한 추가 유지 관리 및 지원을 제공하기 위해 중단됩니다. 최신 버전의 .NET SDK v3 SDK로 [마이그레이션하는](migrate-dotnet-v3.md) 것이 좋습니다.
>

> [!NOTE]
> .NET Core를 사용하는 경우 .NET Standard를 대상으로 하는 [.NET SDK](sql-api-sdk-dotnet-standard.md)의 최신 버전 3.x를 참조하세요.

## <a name="release-history"></a><a name="release-history"></a> 릴리스 기록

릴리스 기록은 Azure Cosmos DB .NET SDK 원본 리포지토리에서 유지 관리됩니다. 각 릴리스에서 수정된 기능 릴리스 및 버그의 자세한 목록은 [SDK changelog 설명서를 참조하세요.](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)

Azure Cosmos DB .NET SDK 버전 3에는 업데이트된 기능과 향상된 성능이 포함되어 있으므로 이 SDK의 2.x는 2024년 8월 31일에 사용 중지됩니다.해당 날짜까지 SDK를 버전 3으로 업데이트해야 합니다. 지침에 따라 Azure Cosmos DB .NET SDK 버전 3으로 마이그레이션하는 것이 좋습니다.

## <a name="recommended-version"></a><a name="recommended-version"></a> 권장 버전

.NET SDK의 다른 하위 버전은 2.x.x 버전에서 사용할 수 있습니다. **최소 권장 버전은 2.16.2입니다.**

## <a name="known-issues"></a><a name="known-issues"></a> 알려진 문제

다음은 [권장되는 최소 버전](#recommended-version)에 영향을 주는 모든 알고 있는 문제 목록입니다.

| 문제 | 영향 | 완화 방법 | 추적 링크 |
| --- | --- | --- | --- |
| 쓰기 위치가 여러 개 있는 계정으로 직접 모드를 사용하는 경우 SDK는 지역이 계정에 추가되는 시기를 검색하지 못할 수 있습니다. 계정 정보를 [새로 고치는](troubleshoot-sdk-availability.md#adding-a-region-to-an-account) 백그라운드 프로세스가 시작되지 않습니다. |현재 지역보다 높은 순서로 PreferredLocations의 일부인 계정에 새 지역이 추가되면 SDK는 사용 가능한 새 지역을 검색하지 않습니다. |애플리케이션을 다시 시작합니다. |https://github.com/Azure/azure-cosmos-dotnet-v2/issues/852 |

## <a name="see-also"></a>참고 항목

Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.

