---
title: SharePoint Online 목록 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics SharePoint Online 목록 커넥터와 관련된 문제를 해결하는 방법을 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 2407521ef6abcea5a27f7810af527e47a2739410
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129391105"
---
# <a name="troubleshoot-the-sharepoint-online-list-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse SharePoint Online 목록 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse SharePoint Online 목록 커넥터의 일반적인 문제를 해결하기 위한 제안을 제공합니다.

## <a name="error-code-sharepointonlineauthfailed"></a>오류 코드: SharePointOnlineAuthFailed

- **메시지**: `The access token generated failed, status code: %code;, error message: %message;.`

- **원인**: 서비스 주체 ID와 키가 올바르게 설정되지 않았을 수 있습니다.

- **권장 사항**: 등록된 애플리케이션(서비스 주체 ID)과 키가 올바르게 설정되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
