---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: 32184e2b836aa881dc3175bb0790fc9cbbca1df7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996443"
---
런타임에 Blob 컨테이너에 대한 액세스를 제공하는 역할 할당을 만들어야 합니다. [소유자와](../articles/role-based-access-control/built-in-roles.md#owner) 같은 관리 역할로는 충분하지 않습니다. 다음 표에서는 일반 작업에서 Blob Storage 확장을 사용할 때 권장되는 기본 제공 역할을 보여 드립니다. 애플리케이션에 작성하는 코드에 따라 추가 권한이 필요할 수 있습니다.

| 바인딩 유형   | 기본 제공 역할 예                |
|----------------|---------------------------------------|
| 트리거        | [Storage Blob 데이터 소유자]<sup>1</sup> |
| 입력 바인딩  | [Storage Blob 데이터 읽기 권한자]            |
| 출력 바인딩 | [Storage Blob 데이터 소유자]             |

<sup>1</sup> 일부 구성에서는 Blob 트리거에 [Storage 큐 데이터 기여자가](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor)추가로 필요할 수 있습니다.

[Storage Blob 데이터 읽기 권한자]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader
[Storage Blob 데이터 소유자]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner
