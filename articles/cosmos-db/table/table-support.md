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
ms.openlocfilehash: f9f84e6e023fab37ec806cd498356fe2ccd0665a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509065"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Azure Cosmos DB Table API 및 Azure Table storage로 개발하기
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Azure Cosmos DB 테이블 API와 Azure Table storage는 동일한 테이블 데이터 모델을 공유하고, 해당 SDK를 통해 동일한 만들기, 삭제, 업데이트 및 쿼리 작업을 노출합니다.

> [!NOTE]
> 이제 Azure Cosmos DB의 Table API에서 [서버리스 용량 모드](../serverless.md)를 사용할 수 있습니다.

[!INCLUDE [storage-table-cosmos-comparison](../../../includes/storage-table-cosmos-comparison.md)]

## <a name="azure-sdks"></a>Azure SDK

### <a name="current-release"></a>현재 릴리스

다음 SDK 패키지는 azure Cosmos Table API와 azure Table storage 모두에서 작동 합니다.

* **.Net** -NuGet에서 사용할 수 있는 [Azure. Tables](https://www.nuget.org/packages/Azure.Data.Tables/) 를 사용 합니다.

* **Python** -PyPi에서 사용할 수 있는 [azure 데이터 테이블](https://pypi.org/project/azure-data-tables/) 을 사용 합니다.

* **JavaScript/TypeScript** - [@azure/data-tables](https://www.npmjs.com/package/@azure/data-tables) npm.js에서 사용할 수 있는 패키지를 사용 합니다.  

* **Java** -Maven에서 사용할 수 있는 [azure 데이터 테이블](https://mvnrepository.com/artifact/com.azure/azure-data-tables/12.0.0) 패키지를 사용 합니다.

### <a name="prior-releases"></a>이전 릴리스

다음 SDK 패키지는 Azure Cosmos DB Table API 에서만 작동 합니다.

* **.Net**  -  NuGet에서 사용할 수 있는 [Azure Data Tables.](https://www.nuget.org/packages/Azure.Data.Tables/)  azure Tables 클라이언트 라이브러리는 코드를 변경 하지 않고 azure table storage 또는 Azure Cosmos DB Table service 끝점을 원활 하 게 대상으로 지정할 수 있습니다.

* **Python**  -  [cosmosdb-](https://pypi.org/project/azure-cosmosdb-table/) PyPi에서 사용할 수 있는 테이블입니다. 이 SDK는 Azure Table Storage 및 Azure Cosmos DB Table API 모두와 연결됩니다.

* **JavaScript/TypeScript**  -  [azure-](https://www.npmjs.com/package/azure-storage) npm.js에서 사용할 수 있는 저장소 패키지입니다. 이 Azure Storage SDK에는 Table API를 사용하여 Azure Cosmos DB 계정에 연결할 수 있는 기능이 있습니다.

* **Java**  -  Maven에서 [Java 용 클라이언트 SDK를 Microsoft Azure Storage](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) 합니다. 이 Azure Storage SDK에는 Table API를 사용하여 Azure Cosmos DB 계정에 연결할 수 있는 기능이 있습니다.

* **C + +**   -  [c + + 용 클라이언트 라이브러리를 Azure Storage](https://github.com/Azure/azure-storage-cpp/)합니다. 이 라이브러리를 사용하면 Azure Storage에 애플리케이션을 빌드할 수 있습니다.

* **Ruby**  -  [Ruby 용 테이블 클라이언트 라이브러리를 Azure Storage](https://github.com/azure/azure-storage-ruby/tree/master/table)합니다. 이 프로젝트는 Azure Storage Table 서비스에 쉽게 액세스할 수 있게 해주는 Ruby 패키지를 제공합니다.

* **PHP**  -  [표 PHP 클라이언트 라이브러리를 Azure Storage](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table)합니다. 이 프로젝트는 Azure Storage Table 서비스에 쉽게 액세스할 수 있게 해주는 PHP 클라이언트 라이브러리를 제공합니다.

* **PowerShell**  -  [AzureRmStorageTable PowerShell 모듈](https://www.powershellgallery.com/packages/AzureRmStorageTable)입니다. 이 PowerShell 모듈에는 스토리지 테이블과 함께 작동하는 cmdlet이 있습니다.
