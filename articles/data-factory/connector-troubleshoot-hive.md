---
title: Hive 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics Hive 커넥터 문제를 해결하는 방법을 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 8412d5d49a0e457b42fce408440693fa5ffc6d4c
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067897"
---
# <a name="troubleshoot-the-hive-connector-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse Hive 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse Hive 커넥터의 일반적인 문제를 해결하기 위한 제안을 제공합니다.

## <a name="the-performance-difference-between-the-odbc-connector-and-the-hive-connector"></a>ODBC 커넥터와 Hive 커넥터 간의 성능 차이

- **증상:** ODBC 커넥터와 Hive 커넥터 간의 성능 차이에 대한 우려입니다. 

- **원인:** ODBC 커넥터에는 타사 드라이버 품질로 인해 성능 차이가 발생할 수 있는 자체 드라이버가 필요합니다.

- **권장 사항:** Hive 커넥터를 먼저 사용합니다. 


## <a name="unexpected-response-received-from-the-server-when-connecting-to-the-hive-server-via-odbc"></a>ODBC를 통해 Hive 서버에 연결할 때 서버에서 예기치 않은 응답이 수신되었습니다.

- **증상:** ODBC 연결된 서비스를 사용하여 Hive 서버에 연결할 때 다음 오류 메시지가 표시됩니다. `ERROR [HY000] [Cloudera][DriverSupport] (1110) Unexpected response received from server. Please ensure the server host and port specified for the connection are correct and confirm if SSL should be enabled for the connection.`

- **원인:** Azure Data Factory 지원되지 않는 Kerberos 인증을 사용합니다.

- **권장 사항:** 다음 단계를 시도합니다. 작동하지 않는 경우 제공된 드라이버를 확인하여 이 문제를 해결합니다.
    1. **kr5.ini** 파일은 **C:\Program Files\MIT\Kerberos\bin 폴더에** 있습니다.
    2. 시스템 `KRB5_CONFIG` 변수에도 및 `KRB5CCNAME` 를 추가합니다.
    3. **krb.ini** 파일을 편집합니다.
    4. 컴퓨터에서 VM 및 SHIR을 종료하고 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)