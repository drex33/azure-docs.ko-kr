---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: b6c9f5561edb35a85efb95be2114d2db44e5b3d1
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132875684"
---
런타임에 blob 컨테이너에 대 한 액세스를 제공 하는 역할 할당을 만들어야 합니다. [Owner](../articles/role-based-access-control/built-in-roles.md#owner) 와 같은 관리 역할은 충분 하지 않습니다. 다음 표에서는 정상적인 작업에서 Blob Storage 확장을 사용할 때 권장 되는 기본 제공 역할을 보여 줍니다. 응용 프로그램에는 사용자가 작성 하는 코드에 따라 추가 사용 권한이 필요할 수 있습니다.

| 바인딩 유형   | 기본 제공 역할 예                |
|----------------|---------------------------------------|
| 트리거        | [Storage Blob 데이터 소유자] **및** [Storage 큐 데이터 참가자]<sup>1</sup> |
| 입력 바인딩  | [Storage Blob 데이터 읽기 권한자]            |
| 출력 바인딩 | [Storage Blob 데이터 소유자]             |

<sup>1</sup> 기본적으로 blob 트리거는 내부적으로 Azure 큐를 사용 합니다. 따라서 메시지를 만들고 받으려면 [큐 데이터 참가자] 권한이 필요 Storage.

[Storage Blob 데이터 읽기 권한자]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader
[Storage Blob 데이터 소유자]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner
[Storage 큐 데이터 기여자]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor
