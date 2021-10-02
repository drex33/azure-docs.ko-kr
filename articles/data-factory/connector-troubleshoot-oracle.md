---
title: Oracle 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics에서 Oracle 커넥터 문제를 해결 하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 0450faa94af3c01fccebc618c5f91094447116f5
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129391104"
---
# <a name="troubleshoot-the-oracle-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse에서 Oracle 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse의 Oracle 커넥터와 관련 된 일반적인 문제를 해결 하기 위한 제안 사항을 제공 합니다.

## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>오류 코드: ArgumentOutOfRangeException

- **메시지**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **원인**: Azure Data Factory 및 Synapse 파이프라인에서 DateTime 값은 0001-01-01 00:00:00~9999-12-31 23:59:59 범위에서 지원됩니다. 그러나 Oracle은 이보다 큰 범위의 DateTime 값을 지원하므로(예: 기원전 세기, min/sec>59 등), 이로 인해 오류가 발생하게 됩니다.

- **권장 사항**: 

    Oracle의 값이 지원되는 날짜 범위 안에 있는지 확인하려면 `select dump(<column name>)`을 실행합니다. 

    결과에서 바이트 시퀀스를 확인하려면 [How are dates stored in Oracle?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle)(Oracle에서 날짜가 저장되는 방식)을 참조하세요.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
