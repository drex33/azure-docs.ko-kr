---
title: Azure Cosmos DB 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics Azure Cosmos DB 및 Azure Cosmos DB(SQL API) 커넥터와 관련된 문제를 해결하는 방법을 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: cd5f10c05ab1ca2524f384a4d085a913193e7293
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129391116"
---
# <a name="troubleshoot-the-azure-cosmos-db-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse Azure Cosmos DB 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse Azure Cosmos DB 및 Azure Cosmos DB(SQL API) 커넥터의 일반적인 문제를 해결하기 위한 제안을 제공합니다.

## <a name="error-message-request-size-is-too-large"></a>오류 메시지: 요청 크기가 너무 큽니다.

- **증상**: 기본 쓰기 일괄 처리 크기를 사용하여 Azure Cosmos DB로 데이터를 복사할 때 다음과 같은 오류가 표시됩니다. `Request size is too large.`

- **원인**: Azure Cosmos DB에서는 단일 요청의 크기가 2MB로 제한됩니다. 수식은 ‘요청 크기 = 단일 문서 크기 \* 쓰기 일괄 처리 크기’입니다. 문서 크기가 큰 경우 기본 동작으로 인해 요청 크기가 너무 커집니다. 쓰기 일괄 처리 크기를 조정할 수 있습니다.

- **해결 방법**: 복사 작업 싱크에서 ‘쓰기 일괄 처리 크기’ 값을 줄입니다(기본값은 10000입니다).

## <a name="error-message-unique-index-constraint-violation"></a>오류 메시지: 고유한 인덱스 제약 조건 위반

- **증상**: Azure Cosmos DB로 데이터를 복사할 때 다음과 같은 오류가 표시됩니다.

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **원인**: 다음 두 가지 가능한 원인이 있습니다.

    - 원인 1: 쓰기 동작으로 **Insert** 를 사용한 경우, 이 오류는 원본 데이터에 ID가 동일한 행 또는 개체가 있음을 의미합니다.
    - 원인 2: 쓰기 동작으로 **Upsert** 를 사용했고 컨테이너에 대해 다른 고유 키를 설정한 경우, 이 오류는 원본 데이터에 ID는 서로 다르지만 정의된 고유 키에 대해 값이 같은 행 또는 개체가 있음을 의미합니다.

- **해결 방법**: 

    - 원인 1의 경우 쓰기 동작으로 **Upsert** 를 설정합니다.
    - 원인 2의 경우 각 문서의 정의된 고유 키 값이 서로 다른지 확인합니다.

## <a name="error-message-request-rate-is-large"></a>오류 메시지: 요청 빈도가 높습니다.

- **증상**: Azure Cosmos DB로 데이터를 복사할 때 다음과 같은 오류가 표시됩니다.

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **원인**: 사용된 RU(요청 단위)의 개수가 Azure Cosmos DB에 구성된 사용 가능한 RU의 개수보다 큽니다. Azure Cosmos DB가 RU를 계산하는 방법을 알아보려면 [Azure Cosmos DB의 요청 단위](../cosmos-db/request-units.md#request-unit-considerations)를 참조하세요.

- **해결 방법**: 다음 두 가지 방법 중 하나를 시도해 봅니다.

    - Azure Cosmos DB에서 ‘컨테이너 RU’의 개수를 늘립니다. 이 솔루션을 사용하면 복사 작업 성능이 개선되지만 Azure Cosmos DB에서 더 많은 비용이 발생합니다. 
    - *writeBatchSize* 를 더 작은 값으로 줄이고(예: 1000) *parallelCopies* 를 더 작은 값으로 줄입니다(예: 1). 이 솔루션을 사용하면 복사 실행 성능이 줄어들지만 Azure Cosmos DB에서 더 많은 비용이 발생하지 않습니다.

## <a name="columns-missing-in-column-mapping"></a>열 매핑에서 열이 누락됨

- **증상**: Azure Cosmos DB에 대해 열 매핑을 위한 스키마를 가져올 때 일부 열이 누락됩니다. 

- **원인**: Azure Data Factory 및 Synapse 파이프라인은 처음 10개의 Azure Cosmos DB 문서에서 스키마를 유추합니다. 일부 문서 열 또는 속성이 값을 포함하지 않을 경우 스키마를 감지하지 못하여 스키마가 표시되지 않습니다.

- **해결 방법**: 다음 코드에 나와 있는 것처럼 쿼리를 수정하여 결과 세트에 열 값이 빈 값으로 표시되도록 할 수 있습니다. (처음 10개의 문서에서 *impossible* 열이 누락되었다고 가정합니다.) 또는 매핑을 위한 열을 수동으로 추가할 수 있습니다.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

## <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>오류 메시지: 판독기에 대한 GuidRepresentation이 CSharpLegacy입니다.

- **증상**: Azure Cosmos DB MongoAPI 또는 MongoDB에서 UUID(Universally Unique Identifier) 필드가 있는 데이터를 복사할 때 다음과 같은 오류가 표시됩니다.

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **원인**: BSON(이진 JSON)에서 UUID를 표현하는 방법에는 UuidStardard와 UuidLegacy, 이렇게 두 가지가 있습니다. 기본적으로 UuidLegacy는 데이터를 읽는 데 사용됩니다. MongoDB의 UUID 데이터가 UuidStandard인 경우 오류가 발생합니다.

- **해결 방법**: MongoDB 연결 문자열에서 *uuidRepresentation=standard* 옵션을 추가합니다. 자세한 내용은 [MongoDB 연결 문자열](connector-mongodb.md#linked-service-properties)을 참조하세요.

## <a name="error-code-cosmosdbsqlapioperationfailed"></a>오류 코드: CosmosDbSqlApiOperationFailed

- **메시지**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **원인**: CosmosDbSqlApi 작업에서 문제가 발생했습니다.  이는 특히 Cosmos DB(SQL API) 커넥터에 적용됩니다.

- **권장 사항**: 오류 세부 정보를 확인하려면 [Azure Cosmos DB 도움말 문서](../cosmos-db/troubleshoot-dot-net-sdk.md)를 참조하세요. 추가 도움이 필요한 경우 Azure Cosmos DB 팀으로 문의하세요.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
