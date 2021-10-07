---
title: Azure Cosmos DB 사용할 수 없음 예외 문제 해결
description: 사용할 수 없음 예외를 진단하고 해결하는 방법을 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: dcef0eccc6ca314c51b436025fb10c25dbbe04fa
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658237"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Azure Cosmos DB 사용할 수 없음 예외 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

HTTP 상태 코드 403은 요청을 완료할 수 없음을 나타냅니다.

## <a name="firewall-blocking-requests"></a>방화벽 차단 요청
이 시나리오에서는 아래와 같은 오류가 발생하는 것이 일반적입니다.

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>솔루션
현재 [방화벽 설정](../how-to-configure-firewall.md)이 올바른지 확인하고 연결하려는 IP 또는 네트워크를 포함합니다.
최근에 업데이트한 경우 변경 내용을 **적용하는 데 최대 15분** 이 걸릴 수 있다는 점에 유의하세요.

## <a name="partition-key-exceeding-storage"></a>스토리지를 초과하는 파티션 키
이 시나리오에서는 아래와 같은 오류가 발생하는 것이 일반적입니다.

```
Response status code does not indicate success: Forbidden (403); Substatus: 1014
```

```
Partition key reached maximum size of {...} GB
```

### <a name="solution"></a>솔루션
이 오류는 현재 [분할 디자인](../partitioning-overview.md#logical-partitions) 및 워크로드가 지정된 파티션 키 값에 대해 허용되는 데이터 양을 초과하여 저장하려고 시도한다는 것을 의미합니다. 컨테이너의 논리 파티션 수에는 제한이 없지만 각 논리 파티션이 저장할 수 있는 데이터의 크기는 제한됩니다. 에 도달하여 설명을 지원할 수 있습니다.

## <a name="non-data-operations-are-not-allowed"></a>비데이터 작업은 허용되지 않습니다.
이 시나리오는 비데이터 [작업이 계정에서 허용되지 않는](../how-to-restrict-user-data.md#disallow-the-execution-of-non-data-operations) 경우에 발생합니다. 이 시나리오에서는 아래와 같은 오류가 발생하는 것이 일반적입니다.

```
Operation 'POST' on resource 'calls' is not allowed through Azure Cosmos DB endpoint
```

### <a name="solution"></a>솔루션
Azure Resource Manager, Azure Portal, Azure CLI 또는 Azure PowerShell을 통해 작업을 수행합니다. 또는 비데이터 작업 실행을 다시 허용합니다.

## <a name="next-steps"></a>다음 단계
* [IP 방화벽](../how-to-configure-firewall.md) 구성
* [가상 네트워크](../how-to-configure-vnet-service-endpoint.md)에서 액세스 구성
* [프라이빗 엔드포인트](../how-to-configure-private-endpoints.md)에서 액세스 구성
