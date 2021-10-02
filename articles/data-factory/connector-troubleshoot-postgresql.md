---
title: Azure Database for PostgreSQL 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics에서 Azure Database for PostgreSQL 커넥터를 사용 하 여 문제를 해결 하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 8613c6b17007f51caf437fb2d66d2d8e17965007
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129391094"
---
# <a name="troubleshoot-the-azure-database-for-postgresql-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse의 Azure Database for PostgreSQL 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse의 Azure Database for PostgreSQL 커넥터와 관련 된 일반적인 문제를 해결 하기 위한 제안 사항을 제공 합니다.

## <a name="error-code-azurepostgresqlnpgsqldatatypenotsupported"></a>오류 코드: AzurePostgreSqlNpgsqlDataTypeNotSupported

- **메시지**: `The data type of the chosen Partition Column, '%partitionColumn;', is '%dataType;' and this data type is not supported for partitioning.`

- **권장 사항**: int, bigint, smallint, serial, bigserial, smallserial, 표준 시간대가 있거나 없는 타임스탬프, 표준 시간대 또는 날짜 데이터 형식이 없는 시간을 사용하여 파티션 열을 선택합니다.

## <a name="error-code-azurepostgresqlnpgsqlpartitioncolumnnamenotprovided"></a>오류 코드: AzurePostgreSqlNpgsqlPartitionColumnNameNotProvided

- **메시지**: `Partition column name must be specified.`

- **원인**: 파티션 열 이름이 제공되지 않았으며 자동으로 결정될 수 없습니다.
 
## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
