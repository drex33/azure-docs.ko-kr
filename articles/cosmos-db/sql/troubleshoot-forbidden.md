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
ms.openlocfilehash: 9a5f1590e73388429f613a1c6b078f5c315397d6
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130238947"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Azure Cosmos DB 사용할 수 없음 예외 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

HTTP 상태 코드 403은 요청을 완료할 수 없음을 나타냅니다.

## <a name="firewall-blocking-requests"></a>방화벽 차단 요청

데이터 평면 요청은 다음 3개의 경로를 통해 Cosmos DB에 올 수 있습니다.

- 공용 인터넷(IPv4)
- 서비스 엔드포인트
- 프라이빗 엔드포인트

403 사용할 수 없음으로 데이터 평면 요청이 차단되면 위의 3개 경로 중 요청이 Cosmos DB에 전달된 경로를 통해 오류 메시지가 지정됩니다.

- `Request originated from client IP {...} through public internet.`
- `Request originated from client VNET through service endpoint.`
- `Request originated from client VNET through private endpoint.`

### <a name="solution"></a>솔루션

Cosmos DB에 올 **것으로 예상되는** 경로를 이해합니다.
   - 예상 경로를 통해 요청이 Cosmos DB에 들어오지 않았다는 오류 메시지가 표시되면 클라이언트 쪽 설정에서 문제가 발생할 수 있습니다. 다음 설명서에 따라 클라이언트 쪽 설정을 다시 확인하세요.
      - 공용 인터넷: [Azure Cosmos DB에서 IP 방화벽을 구성합니다.](../how-to-configure-firewall.md)
      - 서비스 엔드포인트: [VNet(가상 네트워크)에서 Azure Cosmos DB에 대한 액세스를 구성합니다.](../how-to-configure-vnet-service-endpoint.md) 예를 들어 서비스 엔드포인트를 사용할 것으로 예상하지만 공용 인터넷을 통해 요청이 DB에 Cosmos 경우 클라이언트가 실행 중인 서브넷이 서비스 엔드포인트를 Cosmos DB를 사용하도록 설정하지 않았을 수 있습니다.
      - 프라이빗 엔드포인트: [Azure Cosmos 계정에 대한 Azure Private Link 구성합니다.](../how-to-configure-private-endpoints.md) 예를 들어 프라이빗 엔드포인트를 사용할 것으로 예상하지만 공용 인터넷을 통해 요청이 DB에 Cosmos 경우 VM의 DNS가 계정 엔드포인트를 개인 IP로 확인하도록 구성되지 않아 계정의 공용 IP를 대신 통과했을 수 있습니다.
   - 요청이 예상 경로를 통해 DB에 Cosmos 경우 원본 네트워크 ID가 계정에 대해 허용되도록 구성되지 않았기 때문에 요청이 차단되었습니다. 요청이 Cosmos DB에 온 경로에 따라 계정 설정을 확인합니다.
      - 공용 인터넷: 계정의 [공용 네트워크 액세스](../how-to-configure-private-endpoints.md#blocking-public-network-access-during-account-creation) 및 IP 범위 필터 구성을 확인합니다.
      - 서비스 엔드포인트: 계정의 [공용 네트워크 액세스](../how-to-configure-private-endpoints.md#blocking-public-network-access-during-account-creation) 및 VNET 필터 구성을 확인합니다.
      - 프라이빗 엔드포인트: 계정의 프라이빗 엔드포인트 구성 및 클라이언트의 프라이빗 DNS 구성을 확인합니다. 이는 다른 계정에 대해 설정된 프라이빗 엔드포인트에서 계정에 액세스하기 때문일 수 있습니다.

최근에 계정의 방화벽 구성을 업데이트한 경우 변경 내용을 **적용하는 데 최대 15분이** 걸릴 수 있습니다.

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
