---
title: Azure Files 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics Azure Files 커넥터와 관련된 문제를 해결하는 방법을 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 809bf30f1e11eaa7741ac0581881479d854ed4cb
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129391112"
---
# <a name="troubleshoot-the-azure-files-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse Azure Files 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse Azure Files 커넥터의 일반적인 문제를 해결하기 위한 제안을 제공합니다.

## <a name="error-code-azurefileoperationfailed"></a>오류 코드: AzureFileOperationFailed

- **메시지**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **원인**: Azure Files 스토리지 작업에서 문제가 발생했습니다.

- **권장 사항**: 오류 세부 정보를 확인하려면 [Azure Files 도움말](/rest/api/storageservices/file-service-error-codes)을 참조하세요. 추가 도움이 필요한 경우 Azure Files 팀으로 문의하세요.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
