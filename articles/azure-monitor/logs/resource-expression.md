---
title: Azure Monitor 로그 쿼리의 resource() 식 | Microsoft Docs
description: 리소스 식은 여러 리소스에서 데이터를 검색하기 위해 리소스 중심 Azure Monitor 로그 쿼리에서 사용됩니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2021
ms.openlocfilehash: cf49ee6f0913721d3c71c0d42e5f11a2aeeb2025
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122531235"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>Azure Monitor 로그 쿼리의 resource() 식

`resource` 식은 다른 리소스에서 데이터를 검색하기 위해 [리소스로 범위](scope.md#query-scope)가 지정된 Azure Monitor 쿼리에 사용됩니다. 


## <a name="syntax"></a>구문

`resource(`*Identifier*`)`

## <a name="arguments"></a>인수

- *식별자*: 리소스의 리소스 ID입니다.

| ID | Description | 예
|:---|:---|:---|
| 리소스 | 리소스에 대한 데이터를 포함합니다. | resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm") |
| 리소스 그룹 또는 구독 | 모든 리소스에 포함된 데이터를 포함합니다.  | resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>참고

* 리소스에 대한 읽기 권한이 있어야 합니다.


## <a name="examples"></a>예제

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>다음 단계

- 쿼리 범위에 대한 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](scope.md)를 참조하세요.
- [ 쿼리 언어](/azure/kusto/query/)에 대한 전체 문서에 액세스합니다.
