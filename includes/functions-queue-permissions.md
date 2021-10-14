---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: e9897a9fb28f3ea9489e80b0dd4bb814c836b23b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992739"
---
런타임에 큐에 대 한 액세스를 제공 하는 역할 할당을 만들어야 합니다. [Owner](../articles/role-based-access-control/built-in-roles.md#owner) 와 같은 관리 역할은 충분 하지 않습니다. 다음 표에서는 정상적인 작업에서 Queue Storage 확장을 사용할 때 권장 되는 기본 제공 역할을 보여 줍니다. 응용 프로그램에는 사용자가 작성 하는 코드에 따라 추가 사용 권한이 필요할 수 있습니다.

| 바인딩 유형   | 기본 제공 역할 예                                                |
|----------------|-----------------------------------------------------------------------|
| 트리거        | 큐 데이터 [판독기 Storage] [Storage 큐 데이터 메시지 처리기]   |
| 출력 바인딩 | 큐 데이터 [참가자 Storage] [Storage 큐 데이터 메시지 보낸 사람] |

[Storage 큐 데이터 읽기 권한자]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader
[Storage 큐 데이터 메시지 처리자]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor
[Storage 큐 데이터 메시지 보내는 사람]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender
[Storage 큐 데이터 기여자]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor
