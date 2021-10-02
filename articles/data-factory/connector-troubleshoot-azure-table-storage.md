---
title: Azure Table Storage 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics Azure Table Storage 커넥터와 관련된 문제를 해결하는 방법을 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: e99a31f4ca0380d8bbc12f941248d745e0c7f74c
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129391097"
---
# <a name="troubleshoot-the-azure-table-storage-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse Azure Table Storage 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse Azure Table Storage 커넥터의 일반적인 문제를 해결하기 위한 제안을 제공합니다.

## <a name="error-code-azuretableduplicatecolumnsfromsource"></a>오류 코드: AzureTableDuplicateColumnsFromSource

- **메시지**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **원인**: 중복된 원본 열이 발생하는 이유는 다음 중 하나일 수 있습니다.
   * 데이터베이스를 원본으로 사용하고 있고 테이블 조인을 적용했습니다.
   * 구조화되지 않은 CSV 파일로 인해 헤더 행에서 열 이름이 중복되었습니다.

- **권장 사항**: 열 이름을 재차 확인하고 결과에 따라 수정합니다.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
