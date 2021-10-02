---
title: DB2 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics에서 DB2 커넥터를 사용 하 여 문제를 해결 하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: f2a9e79fb3c01938eae52b6c34687943a9472024
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129391096"
---
# <a name="troubleshoot-the-db2-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse에서 DB2 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse의 Azure Database for PostgreSQL 커넥터와 관련 된 일반적인 문제를 해결 하기 위한 제안 사항을 제공 합니다.

## <a name="error-code-db2driverrunfailed"></a>오류 코드: DB2DriverRunFailed

- **메시지**: `Error thrown from driver. Sql code: '%code;'`

- **원인**: 오류 메시지에 “SQLSTATE=51002 SQLCODE=-805”이라는 문자열이 포함된 경우 [DB2에서 데이터 복사](./connector-db2.md#linked-service-properties)의 “팁”을 따릅니다.

- **권장 사항**: `packageCollection` 속성에서 “NULLID”를 설정해 봅니다.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
