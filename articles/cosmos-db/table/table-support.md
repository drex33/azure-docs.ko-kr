---
title: Azure Cosmos DB에서 Azure Table Storage 지원
description: 동일한 테이블 데이터 모델 작업을 공유하여 Azure Cosmos DB Table API와 Azure Storage Tables를 함께 작동하는 방법 알아보기
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 08/25/2021
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: f2d337f7154e2ae9412930aab9720a47bde4768e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124762040"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Azure Cosmos DB Table API 및 Azure Table storage로 개발하기
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Azure Cosmos DB 테이블 API와 Azure Table storage는 동일한 테이블 데이터 모델을 공유하고, 해당 SDK를 통해 동일한 만들기, 삭제, 업데이트 및 쿼리 작업을 노출합니다.

> [!NOTE]
> 이제 Azure Cosmos DB의 Table API에서 [서버리스 용량 모드](../serverless.md)를 사용할 수 있습니다.

[!INCLUDE [storage-table-cosmos-comparison](../../../includes/storage-table-cosmos-comparison.md)]

## <a name="azure-sdks"></a>Azure SDK

### <a name="current-release"></a>현재 릴리스

다음 SDK 패키지는 Azure Cosmos Table API 및 Azure Table Storage 둘 다에서 작동합니다.

* **.NET** - NuGet 사용할 수 있는 [Azure.Data.Tables를](https://www.nuget.org/packages/Azure.Data.Tables/) 사용합니다.

* **Python** - PyPi에서 사용할 수 있는 [azure-data-tables를](https://pypi.org/project/azure-data-tables/) 사용합니다.

* **JavaScript/TypeScript** - [@azure/data-tables](https://www.npmjs.com/package/@azure/data-tables) npm.js 사용할 수 있는 패키지를 사용합니다.  

* **Java** - Maven에서 사용할 수 있는 [azure-data-tables](https://mvnrepository.com/artifact/com.azure/azure-data-tables/12.0.0) 패키지를 사용합니다.

### <a name="prior-releases"></a>이전 릴리스

다음 SDK 패키지는 Azure Cosmos DB Table API 작동합니다.

* **.NET**  -  [Microsoft.Azure. Cosmos.](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)NuGet 사용할 수 있는 테이블입니다.  이 라이브러리는 Cosmos DB Table API만 작동합니다.

* **Python**  -  [azure-cosmosdb-table은](https://pypi.org/project/azure-cosmosdb-table/) PyPi에서 사용할 수 있습니다. 이 SDK는 Azure Table Storage 및 Azure Cosmos DB Table API 모두와 연결됩니다.

* **JavaScript/TypeScript**  -  [azure-storage](https://www.npmjs.com/package/azure-storage) 패키지는 npm.js 사용할 수 있습니다. 이 Azure Storage SDK에는 Table API를 사용하여 Azure Cosmos DB 계정에 연결할 수 있는 기능이 있습니다.

* **Java**  -  [maven에서 Java용 클라이언트 SDK를 Microsoft Azure Storage.](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) 이 Azure Storage SDK에는 Table API를 사용하여 Azure Cosmos DB 계정에 연결할 수 있는 기능이 있습니다.

* **C++**   -  [C++용 클라이언트 라이브러리를 Azure Storage.](https://github.com/Azure/azure-storage-cpp/) 이 라이브러리를 사용하면 Azure Storage에 애플리케이션을 빌드할 수 있습니다.

* **Ruby**  -  [Azure Storage Table Client Library for Ruby.](https://github.com/azure/azure-storage-ruby/tree/master/table) 이 프로젝트는 Azure Storage Table 서비스에 쉽게 액세스할 수 있게 해주는 Ruby 패키지를 제공합니다.

* **PHP**  -  [테이블 PHP 클라이언트 라이브러리 를 Azure Storage.](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) 이 프로젝트는 Azure Storage Table 서비스에 쉽게 액세스할 수 있게 해주는 PHP 클라이언트 라이브러리를 제공합니다.

* **PowerShell**  -  [AzureRmStorageTable PowerShell 모듈.](https://www.powershellgallery.com/packages/AzureRmStorageTable) 이 PowerShell 모듈에는 스토리지 테이블과 함께 작동하는 cmdlet이 있습니다.
