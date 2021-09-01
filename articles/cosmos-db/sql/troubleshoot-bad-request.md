---
title: Azure Cosmos DB 잘못된 요청 예외 문제 해결
description: 입력 콘텐츠 또는 파티션 키가 유효하지 않음, 파티션 키가 Azure Cosmos DB와 일치하지 않는 등 잘못된 요청 예외를 진단하고 해결하는 방법을 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 04/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: ea7c05bc2361c266103908342e4ef9fb3a6160a6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117061"
---
# <a name="diagnose-and-troubleshoot-bad-request-exceptions-in-azure-cosmos-db"></a>Azure Cosmos DB에서 잘못된 요청 예외 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

HTTP 상태 코드 400은 요청에 잘못된 데이터가 포함되어 있거나 필요한 매개 변수가 누락되었음을 나타냅니다.

## <a name="missing-the-id-property"></a><a name="missing-id-property"></a>ID 속성 누락
이 시나리오에서는 일반적으로 다음과 같은 오류가 표시됩니다.

*필수 속성 'id;'가 누락되어 입력 콘텐츠가 잘못되었습니다.*

이 오류가 있는 응답은 서비스로 보내는 JSON 문서에 필수 ID 속성이 없음을 의미합니다.

### <a name="solution"></a>해결 방법
문서의 일부로 [REST 사양](/rest/api/cosmos-db/documents)에 따라 문자열 값이 있는 `id` 속성을 지정합니다. SDK는 이 속성에 대한 값을 자동으로 생성하지 않습니다.

## <a name="invalid-partition-key-type"></a><a name="invalid-partition-key-type"></a> 잘못된 파티션 키 형식
이 시나리오에서는 일반적으로 다음과 같은 오류가 표시됩니다.

*파티션 키 ...가 잘못되었습니다.*

이 오류가 있는 응답은 파티션 키 값의 형식이 잘못되었음을 의미합니다.

### <a name="solution"></a>해결 방법
파티션 키의 값은 문자열 또는 숫자여야 합니다. 값이 필요한 유형인지 확인합니다.

## <a name="wrong-partition-key-value"></a><a name="wrong-partition-key-value"></a>잘못된 파티션 키 값
이 시나리오에서는 일반적으로 다음과 같은 오류가 표시됩니다.

*문서에서 추출된 PartitionKey가 헤더에 지정된 것과 일치하지 않습니다.*

이 오류가 있는 응답은 작업을 실행하고 예상 속성에 대한 문서의 본문 값과 일치하지 않는 파티션 키 값을 전달하고 있음을 의미합니다. 컬렉션의 파티션 키 경로가 `/myPartitionKey`인 경우 문서에는 SDK 메서드를 호출할 때 파티션 키 값으로 제공된 값과 일치하지 않는 값을 가진 `myPartitionKey`라는 속성이 있습니다.

### <a name="solution"></a>해결 방법
문서 속성 값과 일치하는 파티션 키 값 매개 변수를 보냅니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용하고 있는 경우 문제를 [진단 및 해결](troubleshoot-dot-net-sdk.md)합니다.
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.NET v2](performance-tips.md)의 성능 지침에 대해 알아봅니다.
* Azure Cosmos DB Java v4 SDK를 사용할 때 발생하는 문제를 [진단하고 해결](troubleshoot-java-sdk-v4-sql.md)합니다.
* [Java v4 SDK](performance-tips-java-sdk-v4-sql.md)의 성능 지침에 대해 알아봅니다.
