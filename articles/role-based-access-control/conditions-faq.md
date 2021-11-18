---
title: Azure 역할 할당 조건에 대한 FAQ(미리 보기)
description: Azure 역할 할당 조건에 대한 질문과 대답(미리 보기)
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 11/16/2021
ms.author: rolyon
ms.openlocfilehash: ce7533467e039165a38ecf618043a312e40f4f21
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132754465"
---
# <a name="faq-for-azure-role-assignment-conditions-preview"></a>Azure 역할 할당 조건에 대한 FAQ(미리 보기)

> [!IMPORTANT]
> Azure ABAC 및 Azure 역할 할당 조건은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

**Azure Portal의 조건 작성기에서 스토리지 컨테이너 이름 또는 Blob 경로를 선택할 수 있나요?**

조건에서 스토리지 컨테이너 이름, Blob 경로, 태그 이름 또는 값을 작성해야 합니다. 특성 값에 대한 선택 환경은 없습니다.

**식을 그룹화할 수 있나요?**

대상 동작에 대해 식을 세 개 이상 추가하는 경우 코드 편집기, Azure PowerShell 또는 Azure CLI에 있는 식의 논리적 그룹화를 정의해야 합니다. `a AND b OR c`의 논리적 그룹화는 `(a AND b) OR c` 또는 `a AND (b OR c )`일 수 있습니다.

**미리 보기에서 Azure 리소스에 대한 PIM(Privileged Identity Management)을 통해 조건이 지원되나요?**

예. 자세한 내용은 [Privileged Identity Management에서 Azure 리소스 역할 할당](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)을 참조하세요.

**기존 관리자에게 조건이 지원되나요?**

아니요. 

**사용자 지정 역할 할당에 조건을 추가할 수 있나요?**

예, 사용자 지정 역할에 [조건을 지원하는 작업](conditions-format.md#actions)이 포함되어 있으면 가능합니다.
 
**조건으로 인해 Storage Blob에 대한 액세스 대기 시간이 증가하나요?**

아니요, 벤치마크 테스트에 따르면 조건으로 인해 사용자가 인지할 수 있는 대기 시간이 추가되지 않을 것으로 예상됩니다.

**조건을 지원하기 위해 역할 할당 스키마에 도입된 새로운 속성은 무엇인가요?**

새 조건 속성은 다음과 같습니다.

- `condition`: 역할 정의 및 속성에서 하나 이상의 작업을 사용하여 빌드된 조건문.
- `conditionVersion`: 조건 버전 번호. 기본적으로 2.0이며 공개적으로 지원되는 유일한 버전입니다.

역할 할당에 대한 새 설명 속성도 있습니다.

- `description`: 조건을 설명하는 데 사용할 수 있는 역할 할당에 대한 설명입니다.

**전체 역할 할당 또는 특정 작업에 조건이 적용되나요?**

조건은 특정 대상 작업에만 적용됩니다.

**조건에 대한 제한은 무엇인가요?**

조건은 최대 8KB까지 가능합니다.

**설명에 대한 제한은 무엇인가요?**

설명의 길이는 최대 2KB가 될 수 있습니다.

**조건이 있거나 없는 역할 할당을 만들 수 있지만 동일한 보안 주체, 역할 정의 및 범위 튜플을 사용할 수 있나요?**

아니요, 이 역할 할당을 만들려고 하면 오류가 표시됩니다.

**역할 할당의 조건이 명시적인 거부 효과를 제공하나요?**

아니요, 역할 할당의 조건에는 명시적인 거부 효과가 없습니다. 역할 할당의 조건은 역할 할당에 부여된 액세스를 필터링하여 액세스가 허용되지 않을 수 있습니다. 명시적 거부 효과는 거부 할당의 일부입니다.

## <a name="next-steps"></a>다음 단계

- [Azure 역할 할당 조건 형식 및 구문(미리 보기)](conditions-format.md)
- [Azure 역할 할당 조건 문제 해결(미리 보기)](conditions-troubleshoot.md)
