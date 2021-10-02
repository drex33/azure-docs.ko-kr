---
title: Azure 거부 할당 이해 - Azure RBAC
description: Azure RBAC(Azure 역할 기반 액세스 제어)의 Azure 거부 할당에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 38427b0686e830939025075536ac79c95ebde4cd
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362955"
---
# <a name="understand-azure-deny-assignments"></a>Azure 거부 할당 이해

‘거부 할당’은 역할 할당과 마찬가지로 액세스를 거부하기 위해 특정 범위에서 사용자, 그룹 또는 서비스 주체에게 거부 작업 세트를 연결합니다. 거부 할당은 역할 할당이 사용자에게 액세스 권한을 부여하더라도 특정 Azure 리소스 작업을 사용자가 수행할 수 없도록 차단합니다.

이 문서에서는 거부 할당을 정의하는 방법을 설명합니다.

## <a name="how-deny-assignments-are-created"></a>거부 할당을 만드는 방법

거부 할당은 리소스를 보호하기 위해 Azure에서 만들고 관리합니다. Azure Blueprints 및 Azure 관리형 앱은 거부 할당을 사용하여 시스템 관리되는 리소스를 보호합니다. Azure Blueprints 및 Azure 관리형 앱은 거부 할당을 만들 수 있는 유일한 방법입니다. 사용자 고유의 거부 할당을 직접 만들 수는 없습니다. Blueprints가 거부 할당을 사용하여 리소스를 잠그는 방법에 대한 자세한 내용은 [Azure Blueprints 리소스 잠금 이해](../governance/blueprints/concepts/resource-locking.md)를 참조하세요.

> [!NOTE]
> 사용자 고유의 거부 할당을 직접 만들 수는 없습니다.

## <a name="compare-role-assignments-and-deny-assignments"></a>역할 할당 및 거부 할당 비교

거부 할당은 역할 할당과 유사한 패턴을 따르지만 몇 가지 차이점이 있습니다.

| 기능 | 역할 할당 | 거부 할당 |
| --- | --- | --- |
| 액세스 권한 부여 | :heavy_check_mark: |  |
| 액세스 거부 |  | :heavy_check_mark: |
| 직접 만들 수 있음 | :heavy_check_mark: |  |
| 범위에서 적용 | :heavy_check_mark: | :heavy_check_mark: |
| 보안 주체 제외 |  | :heavy_check_mark: |
| 자식 범위에 대한 상속 방지 |  | :heavy_check_mark: |
| [클래식 구독 관리자](rbac-and-directory-admin-roles.md) 할당에 적용 |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>거부 할당 속성

 거부 할당에 포함된 속성은 다음과 같습니다.

> [!div class="mx-tableFixed"]
> | 속성 | 필수 | Type | Description |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | 예 | String | 거부 할당의 표시 이름입니다. 이름은 지정된 범위에 대해 고유해야 합니다. |
> | `Description` | 예 | String | 거부 할당의 설명입니다. |
> | `Permissions.Actions` | Actions 또는 DataActions 하나 이상 | String[] | 거부 할당이 액세스를 차단 하는 제어 평면 동작을 지정 하는 문자열의 배열입니다. |
> | `Permissions.NotActions` | 예 | String[] | 거부 할당에서 제외할 제어 평면 작업을 지정 하는 문자열의 배열입니다. |
> | `Permissions.DataActions` | Actions 또는 DataActions 하나 이상 | String[] | 거부 할당이 액세스를 차단 하는 데이터 평면 동작을 지정 하는 문자열의 배열입니다. |
> | `Permissions.NotDataActions` | 예 | String[] | 거부 할당에서 제외할 데이터 평면 작업을 지정 하는 문자열의 배열입니다. |
> | `Scope` | 예 | String | 거부 할당이 적용되는 범위를 지정하는 범위입니다. |
> | `DoNotApplyToChildScopes` | 예 | 부울 | 거부 할당이 하위 범위에 적용되는지 여부를 지정합니다. 기본값은 false입니다. |
> | `Principals[i].Id` | 예 | String[] | 거부 할당이 적용되는 Azure AD 보안 주체 개체 ID(사용자, 그룹, 서비스 주체 또는 관리 ID)의 배열입니다. 모든 보안 주체를 나타내려면 빈 GUID `00000000-0000-0000-0000-000000000000`으로 설정합니다. |
> | `Principals[i].Type` | 예 | String[] | Principals[i].Id로 표현되는 개체 형식의 배열입니다. 모든 보안 주체를 나타내려면 `SystemDefined`로 설정합니다. |
> | `ExcludePrincipals[i].Id` | 예 | String[] | 거부 할당이 적용되지 않는 Azure AD 보안 주체 개체 ID(사용자, 그룹, 서비스 주체 또는 관리 ID)의 배열입니다. |
> | `ExcludePrincipals[i].Type` | 예 | String[] | ExcludePrincipals[i].Id로 표시되는 개체 유형의 배열입니다. |
> | `IsSystemProtected` | 예 | 부울 | 이 거부 할당이 Azure에서 생성되었으며 편집하거나 삭제할 수 없는지 여부를 지정합니다. 현재 모든 거부 할당은 시스템에서 보호됩니다. |

## <a name="the-all-principals-principal"></a>모든 보안 주체

거부 할당을 지원하기 위해 모든 보안 주체라는 시스템 정의 *보안 주체* 가 도입되었습니다. 이 보안 주체는 Azure AD 디렉터리의 모든 사용자, 그룹, 서비스 주체 및 관리 ID를 나타냅니다. 보안 주체 ID가 0만 포함하는 GUID `00000000-0000-0000-0000-000000000000`이고 보안 주체 유형이 `SystemDefined`이면 보안 주체는 모든 보안 주체를 나타냅니다. Azure PowerShell 출력에서 모든 보안 주체는 다음과 같습니다.

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

모든 주체를 `ExcludePrincipals`와 결합하면 일부 사용자를 제외한 모든 보안 주체를 거부할 수 있습니다. 모든 보안 주체에는 다음과 같은 제약 조건이 있습니다.

- `Principals`에서만 사용할 수 있으며 `ExcludePrincipals`에서는 사용할 수 없습니다.
- `Principals[i].Type`은 `SystemDefined`로 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

* [자습서: Azure Blueprints 리소스 잠금으로 새 리소스 보호](../governance/blueprints/tutorials/protect-new-resources.md)
* [Azure Portal을 사용한 Azure 거부 할당 나열](deny-assignments-portal.md)
