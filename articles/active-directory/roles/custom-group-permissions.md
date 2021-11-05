---
title: Azure AD 사용자 지정 역할에 대한 그룹 관리 권한(미리 보기) - Azure Active Directory
description: Azure Portal, PowerShell 또는 Microsoft Graph API의 Azure AD 사용자 지정 역할(미리 보기)에 대한 그룹 관리 권한입니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 10/26/2021
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.openlocfilehash: 5e768d9661a7030fb75d8089a6a49e9d5022d612
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057213"
---
# <a name="group-management-permissions-for-azure-ad-custom-roles-preview"></a>Azure AD 사용자 지정 역할에 대한 그룹 관리 권한(미리 보기)

> [!IMPORTANT]
> Azure AD 사용자 지정 역할에 대한 그룹 관리 권한은 현재 미리 보기 상태입니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure AD(Azure Active Directory)의 사용자 지정 역할 정의에서 그룹 관리 권한을 사용하여 다음과 같은 세분화된 액세스 권한을 부여할 수 있습니다.

- 이름 및 설명과 같은 그룹 속성 관리
- 구성원 및 소유자 관리
- 그룹 만들기 또는 삭제
- 감사 로그 읽기
- 특정 유형의 그룹 관리

이 문서에는 다양한 그룹 관리 시나리오에 대한 사용자 지정 역할에서 사용할 수 있는 권한이 나열되어 있습니다. 사용자 지정 역할을 만드는 방법에 대한 자세한 내용은 [사용자 지정 역할 만들기 및 할당](custom-create.md)을 참조하세요.

> [!NOTE]
> Azure Portal을 사용하여 그룹 범위에서 사용자 지정 역할을 할당하는 것은 현재 Azure AD Premium P1에 대해 **만** 사용할 수 있습니다.

## <a name="how-to-interpret-group-management-permissions"></a>그룹 관리 권한을 해석하는 방법

그룹 관리 권한을 해석하려면 다양한 권한 하위 유형이 의미하는 바를 이해하는 것이 도움이 됩니다.

> [!div class="mx-tableFixed"]
> | 권한 하위 유형 | 권한 하위 유형 설명 |
> | --- | --- |
> | groups | 역할 할당 가능한 그룹을 제외한 보안 그룹 및 Microsoft 365 그룹 관리 |
> | 그룹.통합 | 역할 할당 가능한 그룹을 제외한 동적 및 할당된 멤버 자격 유형의 Microsoft 365 그룹 관리 |
> | groups.unified.assignedMembership | 역할 할당 가능한 그룹을 제외하고 할당된 멤버 자격 유형의 Microsoft 365 그룹만 관리합니다. |
> | groups.security | 역할 할당 가능한 그룹을 제외한 동적 및 할당된 멤버 자격 유형의 보안 그룹 관리 |
> | groups.security.assignedMembership | 역할 할당 가능한 그룹을 제외하고 할당된 멤버 자격 유형의 보안 그룹만 관리 |

다음 표에는 다른 하위 유형의 그룹 구성원을 업데이트하기 위한 권한의 예가 나와 있습니다. 

> [!div class="mx-tableFixed"]
> | 권한 예시 | 사용 권한 설명 |
> | --- | --- |
> | microsoft.directory/**groups**/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/**groups.unified**/members/update | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/**groups.unified.assignedMembership**/members/update | 역할 할당 가능한 그룹을 제외한 할당된 멤버 자격 유형의 Microsoft 365 그룹 구성원 업데이트 |
> | microsoft.directory/**groups.security**/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 구성원 업데이트 |
> | microsoft.directory/**groups.security.assignedMembership**/members/update | 역할 할당 가능한 그룹을 제외한 할당된 멤버 자격 유형의 보안 그룹 구성원 업데이트 |

## <a name="read-group-information"></a>그룹 정보 읽기

그룹의 속성, 구성원 및 소유자를 읽을 수 있는 권한은 다음과 같습니다.

> [!div class="mx-tableFixed"]
> | 사용 권한 | 설명 |
> | ---------- | ----------- |
> | microsoft.directory/groups/allProperties/read | 역할 할당 가능한 그룹을 포함한 보안 그룹과 Microsoft 365 그룹의 모든 속성(권한 있는 속성 포함) 읽기 |
> | microsoft.directory/groups/standard/read | 역할 할당 가능 그룹을 포함한 보안 그룹과 Microsoft 365 그룹의 표준 속성 읽기 |
> | microsoft.directory/groups/members/read | 역할 할당 가능 그룹을 포함한 보안 그룹과 Microsoft 365 그룹의 구성원 읽기 |
> | microsoft.directory/groups/memberOf/read | 역할 할당 가능 그룹을 포함한 보안 그룹과 Microsoft 365 그룹의 memberOf 속성 읽기 |
> | microsoft.directory/groups/owners/read | 역할 할당 가능 그룹을 포함한 보안 그룹과 Microsoft 365 그룹의 소유자 읽기 |

## <a name="create-groups"></a>그룹 만들기

다음 권한은 다양한 유형의 그룹을 만드는 데 사용할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 사용 권한 | 설명 |
> | ---------- | ----------- |
> | microsoft.directory/groups/create | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹 만들기 |
> | microsoft.directory/groups.unified/create | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹 만들기 |
> | microsoft.directory/groups.unified.assignedMembership/create | 역할 할당 가능한 그룹을 제외한 할당된 멤버 자격 유형의 Microsoft 365 그룹 만들기 |
> | microsoft.directory/groups.security/create | 역할 할당 가능 그룹을 제외한 보안 그룹 만들기 |
> | microsoft.directory/groups.security.assignedMembership/create | 역할 할당 가능한 그룹을 제외한 할당된 멤버 자격 유형의 보안 그룹 만들기 |
> | microsoft.directory/groups/createAsOwner | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹 만들기. 작성자가 첫 번째 소유자로 추가됩니다. |
> | microsoft.directory/groups.unified/createAsOwner | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹 만들기. 작성자가 첫 번째 소유자로 추가됩니다. |
> | microsoft.directory/groups.unified.assignedMembership/createAsOwner | 역할 할당 가능한 그룹을 제외하고 할당된 멤버 자격 유형의 Microsoft 365 그룹을 만듭니다. 작성자가 첫 번째 소유자로 추가됩니다. |
> | microsoft.directory/groups.security/createAsOwner | 역할 할당 가능 그룹을 제외한 보안 그룹 만들기 작성자가 첫 번째 소유자로 추가됩니다. |
> | microsoft.directory/groups.security.assignedMembership/createAsOwner | 역할 할당 가능한 그룹을 제외한 할당된 멤버 자격 유형의 보안 그룹을 만듭니다. 작성자가 첫 번째 소유자로 추가됩니다. |

## <a name="update-group-information"></a>그룹 정보 업데이트

다음 권한은 그룹의 속성 및 구성원을 업데이트하는 데 사용할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 사용 권한 | 설명 |
> | ---------- | ----------- |
> | microsoft.directory/groups/allProperties/update | 역할 할당 가능 그룹을 제외한 보안 그룹 및 Microsoft 365 그룹의 모든 속성(권한 있는 속성 포함) 업데이트 |
> | microsoft.directory/groups.unified/allProperties/update | 역할 할당 가능한 그룹을 제외한 Microsoft 365 그룹의 모든 속성(권한 있는 속성 포함) 업데이트 |
> | microsoft.directory/groups.unified.assignedMembership/allProperties/update | 역할 할당 가능한 그룹을 제외한 할당된 멤버 자격 유형의 Microsoft 365 그룹에 대한 모든 속성(권한 있는 속성 포함) 업데이트 |
> | microsoft.directory/groups.security/allProperties/update | 역할 할당 가능한 그룹을 제외한 보안 그룹의 모든 속성(권한 있는 속성 포함) 업데이트 |
> | microsoft.directory/groups.security.assignedMembership/allProperties/update | 역할 할당 가능 그룹을 제외한 할당된 멤버 자격 유형의 보안 그룹에 대한 모든 속성(특권 속성 포함) 업데이트 |
> | microsoft.directory/groups/basic/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 기본 속성 업데이트 |
> | microsoft.directory/groups.unified/basic/update | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹의 기본 속성 업데이트 |
> | microsoft.directory/groups.unified.assignedMembership/basic/update | 역할 할당 가능한 그룹을 제외한 할당된 멤버 자격 유형의 Microsoft 365 그룹에 대한 기본 속성 업데이트 |
> | microsoft.directory/groups.security/basic/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 기본 속성 업데이트 |
> | microsoft.directory/groups.security.assignedMembership/basic/update | 역할 할당 가능한 그룹을 제외한 할당된 멤버 자격 유형의 보안 그룹에 대한 기본 속성 업데이트 |
> | microsoft.directory/groups/classification/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 분류 속성 업데이트 |
> | microsoft.directory/groups.unified/classification/update | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹의 분류 속성 업데이트 |
> | microsoft.directory/groups.unified.assignedMembership/classification/update | 역할 할당 가능 그룹을 제외한 할당된 멤버 자격 유형의 Microsoft 365 그룹에 대한 분류 속성 업데이트 |
> | microsoft.directory/groups.security/classification/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 분류 속성 업데이트 |
> | microsoft.directory/groups.security.assignedMembership/classification/update | 역할 할당 가능한 그룹을 제외한 할당된 멤버 자격 유형의 보안 그룹에 대한 분류 속성 업데이트 |
> | microsoft.directory/groups/dynamicMembershipRule/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 동적 멤버 관리 규칙 업데이트 |
> | microsoft.directory/groups.unified/dynamicMembershipRule/update | 역할 할당 가능한 그룹을 제외한 Microsoft 365 그룹의 동적 멤버십 규칙 업데이트 |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | 역할 할당 가능한 그룹을 제외한 보안 그룹에 대한 동적 멤버십 규칙 업데이트 |
> | microsoft.directory/groups/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/groups.unified/members/update | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/groups.unified.assignedMembership/members/update | 역할 할당 가능한 그룹을 제외한 할당된 멤버 자격 유형의 Microsoft 365 그룹 구성원 업데이트 |
> | microsoft.directory/groups.security/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 구성원 업데이트 |
> | microsoft.directory/groups.security.assignedMembership/members/update | 역할 할당 가능한 그룹을 제외한 할당된 멤버 자격 유형의 보안 그룹 구성원 업데이트 |

## <a name="update-members-of-different-group-types"></a>다른 그룹 유형의 구성원 업데이트

다음 권한은 다양한 그룹 유형의 구성원을 업데이트하는 데 사용할 수 있습니다.

> [!div class="mx-tableFixed"]
> | 사용 권한 | 설명 |
> | ---------- | ----------- |
> | microsoft.directory/groups/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/groups.unified/members/update | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/groups.unified.assignedMembership/members/update | 역할 할당 가능한 그룹을 제외한 할당된 멤버 자격 유형의 Microsoft 365 그룹 구성원 업데이트 |
> | microsoft.directory/groups.security/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 구성원 업데이트 |
> | microsoft.directory/groups.security.assignedMembership/members/update | 역할 할당 가능한 그룹을 제외한 할당된 멤버 자격 유형의 보안 그룹 구성원 업데이트 |
 
## <a name="delete-groups"></a>그룹 삭제

그룹을 삭제할 수 있는 권한은 다음과 같습니다.

> [!div class="mx-tableFixed"]
> | 사용 권한 | 설명 |
> | ---------- | ----------- |
> | microsoft.directory/groups/delete | 역할 할당 가능 그룹을 제외한 보안 그룹과 Microsoft 365 그룹 삭제 |
> | microsoft.directory/groups.unified/members/update | 역할 할당 가능 그룹을 제외한 Microsoft 365 그룹의 구성원 업데이트 |
> | microsoft.directory/groups.unified.assignedMembership/members/update | 역할 할당 가능한 그룹을 제외한 할당된 멤버 자격 유형의 Microsoft 365 그룹 구성원 업데이트 |
> | microsoft.directory/groups.security/members/update | 역할 할당 가능 그룹을 제외한 보안 그룹의 구성원 업데이트 |
> | microsoft.directory/groups.security.assignedMembership/members/update | 역할 할당 가능한 그룹을 제외한 할당된 멤버 자격 유형의 보안 그룹 구성원 업데이트 |

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory에서 사용자 지정 역할 만들기 및 할당](custom-create.md)
- [Azure AD 역할 할당 나열](view-assignments.md)
