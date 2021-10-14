---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: d95d50d6e5f69922856e6a3045dfccdf91ddbf1a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994334"
---
런타임에 데이터베이스 계정에 대 한 액세스를 제공 하는 역할 할당을 만들어야 합니다. [Owner](../articles/role-based-access-control/built-in-roles.md#owner) 와 같은 관리 역할은 충분 하지 않습니다. 다음 표에서는 정상적인 작업에서 Cosmos DB 확장을 사용할 때 권장 되는 기본 제공 역할을 보여 줍니다. 응용 프로그램에는 사용자가 작성 하는 코드에 따라 추가 사용 권한이 필요할 수 있습니다.

| 바인딩 유형   | 기본 제공 역할 예                |
|----------------|---------------------------------------|
| 트리거        | [Cosmos DB 기본 제공 데이터 판독기]      |
| 출력 바인딩 | [Cosmos DB 기본 제공 데이터 기여자] |


[Cosmos DB 기본 제공 데이터 판독기]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions
[Cosmos DB 기본 제공 데이터 기여자]: ../articles/cosmos-db/how-to-setup-rbac.md#built-in-role-definitions