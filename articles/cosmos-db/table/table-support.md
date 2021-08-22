---
title: Azure Cosmos DB에서 Azure Table Storage 지원
description: 동일한 테이블 데이터 모델 작업을 공유하여 Azure Cosmos DB Table API와 Azure Storage Tables를 함께 작동하는 방법 알아보기
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 01/08/2021
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: c25a18516fb654dc211f532d7f9f735d0b739bc8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528672"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Azure Cosmos DB Table API 및 Azure Table storage로 개발하기
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

Azure Cosmos DB 테이블 API와 Azure Table storage는 동일한 테이블 데이터 모델을 공유하고, 해당 SDK를 통해 동일한 만들기, 삭제, 업데이트 및 쿼리 작업을 노출합니다.

> [!NOTE]
> 이제 Azure Cosmos DB의 Table API에서 [서버리스 용량 모드](../serverless.md)를 사용할 수 있습니다.

[!INCLUDE [storage-table-cosmos-comparison](../../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Azure Cosmos DB Table API를 사용하여 개발

현재 [Azure Cosmos DB Table API](introduction.md)에는 개발에 사용할 수 있는 네 개의 SDK가 있습니다. 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. 이 라이브러리는 .NET 표준을 대상으로 하며 공용 [Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage)와 동일한 클래스 및 메서드 서명을 갖추고 있지만 Table API를 사용하여 Azure Cosmos DB 계정에 연결할 수 있습니다. .NET Framework 라이브러리 [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/)의 사용자는 유지 관리 모드에 있으며 곧 사용 중지될 예정이므로 [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)로 업그레이드하는 것이 좋습니다.

* [Python SDK](python-sdk.md): 새 Azure Cosmos DB Python SDK는 Python에서 Azure Table Storage를 지원하는 유일한 SDK입니다. 이 SDK는 Azure Table Storage 및 Azure Cosmos DB Table API 모두와 연결됩니다.

* [Java SDK](java-sdk.md): 이 Azure Storage SDK에는 Table API를 사용하여 Azure Cosmos DB 계정에 연결할 수 있는 기능이 있습니다.

* [Node.js SDK](nodejs-sdk.md): 이 Azure Storage SDK에는 Table API를 사용하여 Azure Cosmos DB 계정에 연결할 수 있는 기능이 있습니다.


Table API를 사용하여 작업하는 방법에 관한 추가 정보는 [FAQ: Table API를 사용하여 개발](table-api-faq.yml) 문서에서 제공됩니다.

## <a name="developing-with-azure-table-storage"></a>Azure Table 스토리지를 사용하여 개발

Azure Table Storage에는 개발에 사용할 수 있는 다음과 같은 SDK가 있습니다.

- [Microsoft.Azure.Storage.Blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/), [Microsoft.Azure.Storage.File](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/), [Microsoft.Azure.Storage.Queue](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/) 및 [Microsoft.Azure.Storage.Common](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) 라이브러리를 통해 Azure Table 스토리지 서비스를 사용할 수 있습니다. Azure Cosmos DB에서 Table API를 사용하는 경우 [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) 라이브러리를 대신 사용할 수 있습니다.
- [Python SDK](https://github.com/Azure/azure-cosmos-table-python). Python용 Azure Cosmos DB Table SDK는 Table Storage 서비스를 지원합니다(Azure Table Storage 및 Cosmos DB의 Table API는 동일한 기능과 함수를 공유하므로 SDK 개발 노력을 고려하기 위해 이 SDK를 사용하는 것이 좋습니다).
- [Java용 Azure Storage SDK](https://github.com/azure/azure-storage-java). 이 Azure Storage SDK는 Java에서 Azure Table 스토리지를 사용하기 위한 클라이언트 라이브러리를 제공합니다.
- [Node.js SDK](https://github.com/Azure/azure-storage-node). 이 SDK는 스토리지 Table 서비스를 사용하기 위한 Node.js 패키지 및 브라우저 호환 JavaScript 클라이언트 라이브러리를 제공합니다.
- [AzureRmStorageTable PowerShell 모듈](https://www.powershellgallery.com/packages/AzureRmStorageTable). 이 PowerShell 모듈에는 스토리지 테이블과 함께 작동하는 cmdlet이 있습니다.
- [C++용 Azure Storage 클라이언트 라이브러리](https://github.com/Azure/azure-storage-cpp/). 이 라이브러리를 사용하면 Azure Storage에 애플리케이션을 빌드할 수 있습니다.
- [Ruby용 Azure Storage Table 클라이언트 라이브러리](https://github.com/azure/azure-storage-ruby/tree/master/table). 이 프로젝트는 Azure Storage Table 서비스에 쉽게 액세스할 수 있게 해주는 Ruby 패키지를 제공합니다.
- [Azure Storage Table PHP 클라이언트 라이브러리](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). 이 프로젝트는 Azure Storage Table 서비스에 쉽게 액세스할 수 있게 해주는 PHP 클라이언트 라이브러리를 제공합니다.


   





