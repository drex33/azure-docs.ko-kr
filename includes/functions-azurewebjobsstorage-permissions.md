---
author: mattchenderson
ms.service: azure-functions
ms.topic: include
ms.date: 10/08/2021
ms.author: mahender
ms.openlocfilehash: e8b274cd3d9398694661b06a5a1c9e13e42c6c89
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130137384"
---
런타임에 "AzureWebJobsStorage"에 대 한 저장소 계정에 대 한 액세스를 제공 하는 역할 할당을 만들어야 합니다. [Owner](../articles/role-based-access-control/built-in-roles.md#owner) 와 같은 관리 역할은 충분 하지 않습니다. [Storage blob 데이터 소유자] 역할은 기능 호스트 저장소의 기본 요구 사항을 다룹니다. 런타임은 blob에 대 한 읽기 및 쓰기 액세스와 컨테이너를 만드는 기능을 모두 필요로 합니다. blob 트리거를 사용 하는 경우와 마찬가지로 [Storage Queue 데이터 참가자] 도 필요할 수 있는 경우가 있습니다. 다른 용도로 "AzureWebJobsStorage"를 사용하는 경우 추가 권한이 필요할 수 있습니다.

[Storage Blob 데이터 소유자]: ../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner
[Storage 큐 데이터 기여자]: ../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor