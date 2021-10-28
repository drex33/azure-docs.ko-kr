---
title: Privileged Identity Management의 API 개념 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)의 API를 이해하기 위한 정보입니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: how-to
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac89554135277e0656392a7035b34aac307008b7
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668545"
---
# <a name="understand-the-privileged-identity-management-apis"></a>Privileged Identity Management API 이해

Azure AD(Azure Active Directory) 역할에 대한 Microsoft Graph API 및 Azure 리소스 역할(Azure RBAC 역할이라고도 함)에 대한 Azure Resource Manager API를 사용하여 PIM(Privileged Identity Management) 작업을 수행할 수 있습니다. 이 문서에서는 Privileged Identity Management API를 사용하기 위한 중요한 개념을 설명합니다.

PIM API에 대한 요청 및 기타 세부 정보는 다음을 확인하세요.

- [Azure AD 역할에 대한 PIM API 참조](/graph/api/resources/unifiedroleeligibilityschedulerequest?view=graph-rest-beta&preserve-view=true)
- [Azure 리소스 역할에 대한 PIM API 참조](/rest/api/authorization/roleeligibilityschedulerequests)

> [!IMPORTANT]
> PIM API [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="pim-api-history"></a>PIM API 기록

지난 몇 년 동안 PIM API가 여러 차례 반복되었습니다. 기능이 일부 겹치지만 버전의 선형 진행을 나타내지는 않습니다.

### <a name="iteration-1--only-supports-azure-ad-roles-deprecating"></a>반복 1 – Azure AD 역할만 지원, 더 이상 사용되지 않음

/beta/privilegedRoles 엔드포인트에서 Microsoft는 대부분의 테넌트에서 더 이상 지원되지 않는 클래식 버전의 PIM API를 가지고 있습니다. 05/31에 이 API에 대한 남은 액세스를 더 이상 사용되지 않도록 하고 있습니다.

### <a name="iteration-2--supports-azure-ad-roles-and-azure-resource-roles"></a>반복 2 – Azure AD 역할 및 Azure 리소스 역할 지원

/beta/privilegedAccess 엔드포인트에서 Microsoft는 /aadRoles 및 /azureResources를 모두 지원했습니다. 이 엔드포인트는 테넌트에서 계속 사용할 수 있지만, 이 API를 사용하여 새 개발을 시작하지 않는 것이 좋습니다. 이 베타 API는 일반 공급으로 릴리스되지 않으므로 결국 사용되지 않습니다.

### <a name="current-iteration--azure-ad-roles-in-microsoft-graph-and-azure-resource-roles-in-azure-resource-manager"></a>현재 반복 – Microsoft Graph의 Azure AD 역할 및 Azure Resource Manager의 Azure 리소스 역할

이제 베타 버전에서 Microsoft는 일반 공급으로 API를 릴리스하기 전에 PIM API를 마지막으로 반복합니다. 사용자 피드백에 따라 Azure AD PIM API는 이제 unifiedRoleManagement API 집합 아래에 있으며 Azure Resource PIM API는 이제 Azure Resource Manager 역할 할당 API 아래에 있습니다. 이러한 위치에는 다음을 비롯한 몇 가지 추가 이점이 있습니다.

- Azure AD 역할 및 Azure 리소스 역할에 대한 일반 역할 할당 API에 PIM API를 맞춥니다.
- 추가 PIM API를 호출하여 리소스를 온보딩하거나, 리소스를 가져오거나, 역할 정의를 가져올 필요가 줄어듭니다.
- 앱 전용 권한 지원.
- 승인 및 이메일 알림 구성과 같은 새로운 기능.

현재 반복에서는 PIM 경고 및 권한 있는 액세스 그룹에 대한 API가 지원되지 않습니다.

## <a name="current-permissions-required"></a>필요한 현재 권한

### <a name="azure-ad-roles"></a>Azure AD 역할

  Azure AD 역할에 대한 PIM Graph API를 호출하려면 다음 권한 중 하나 이상 필요합니다.

- RoleManagement.ReadWrite.Directory
- RoleManagement.Read.Directory

  필요한 권한을 지정하는 가장 쉬운 방법은 Azure AD 동의 프레임워크를 사용하는 것입니다.

### <a name="azure-resource-roles"></a>Azure 리소스 역할

  Azure 리소스 역할에 대한 PIM API는 Azure Resource Manager 프레임워크를 기반으로 개발됩니다. Azure Resource Management에 동의해야 하지만 Graph API 권한은 필요하지 않습니다. 또한 API를 호출하는 사용자 또는 서비스 주체가 관리하려는 리소스에 대해 최소한 소유자 또는 사용자 액세스 관리자 역할을 가지고 있는지 확인해야 합니다.

## <a name="calling-pim-api-with-an-app-only-token"></a>앱 전용 토큰을 사용하여 PIM API 호출

### <a name="azure-ad-roles"></a>Azure AD 역할

  PIM API는 이제 위임된 권한 위에 앱 전용 권한을 지원합니다.

- 앱 전용 권한의 경우 필요한 Azure AD 또는 Azure 역할 권한으로 이미 동의한 애플리케이션을 사용하여 API를 호출해야 합니다.
- 위임된 권한의 경우 사용자 및 애플리케이션 토큰을 모두 사용하여 PIM API를 호출해야 합니다. 사용자는 전역 관리자 역할 또는 권한 있는 역할 관리자 역할에 할당되어야 하며, API를 호출하는 서비스 주체에게 관리하려는 리소스에 대한 최소한 소유자 또는 사용자 액세스 관리자 역할이 있어야 합니다.

### <a name="azure-resource-roles"></a>Azure 리소스 역할

  Azure 리소스에 대한 PIM API는 사용자 전용 및 애플리케이션 전용 호출을 모두 지원합니다. 서비스 주체에게 리소스에 대한 소유자 또는 사용자 액세스 관리자 역할이 있는지 확인하기만 하면 됩니다.

## <a name="design-of-current-api-iteration"></a>현재 API 반복 디자인

PIM API는 Azure AD 역할에 대한 API 및 Azure 리소스 역할(할당 및 활성화 API 요청 및 정책 설정)에 대해 일치하는 두 가지 범주로 구성됩니다.

### <a name="assignment-and-activation-api"></a>할당 및 활성화 API

적절한 할당, 시간 범위 적격/활성 할당을 만들고 할당을 활성화하기 위해 PIM은 다음과 같은 엔터티를 제공합니다.

- RoleAssignmentSchedule
- RoleEligibilitySchedule
- RoleAssignmentScheduleInstance
- RoleEligibilityScheduleInstance
- RoleAssignmentScheduleRequest
- RoleEligibilityScheduleRequest

이러한 엔터티는 Azure AD 역할 및 Azure 역할 모두에 대해 기존 roleDefinition 및 roleAssignment 엔터티와 함께 작동하여 엔드투엔드 시나리오를 만들 수 있도록 합니다.

- 일정(시작 또는 종료 시간)이 없는 영구(활성) 역할 할당을 만들거나 검색하려는 경우 이러한 PIM 엔터티를 피하고 roleAssignment 엔터티의 읽기/쓰기 작업에 집중해야 합니다.

- 만료 시간을 사용하거나 사용하지 않고 적격 할당을 만들기 위해 roleEligibilityScheduleRequest에서 쓰기 작업을 사용할 수 있습니다.

- 일정(시작 또는 종료 시간)을 사용하여 영구(활성) 할당을 만들기 위해 roleAssignmentScheduleRequest에서 쓰기 작업을 사용할 수 있습니다.  

- 또한 적격 할당을 활성화하려면 selfActivate라는 수정된 작업 매개 변수를 사용하여 roleAssignmentScheduleRequest에서 쓰기 작업을 사용해야 합니다.

각 요청 개체는 roleAssignmentSchedule 또는 roleEligibilitySchedule 개체를 만듭니다. 이러한 개체는 읽기 전용이며 현재 및 미래의 모든 할당에 대한 일정을 표시합니다.

적격 할당을 활성화하는 경우 roleEligibilityScheduleInstance는 계속 존재합니다. 활성화에 대한 roleAssignmentScheduleRequest는 활성화된 기간 동안 별도의 roleAssignmentSchedule 및 roleAssignmentScheduleInstance를 만듭니다.

인스턴스 개체는 적격 할당이든 활성 할당이든 현재 존재하는 실제 할당입니다. 인스턴스 엔터티에 대한 GET 작업을 사용하여 역할/사용자에 대한 적격 할당/활성 할당 목록을 검색해야 합니다.

### <a name="policy-setting-api"></a>정책 설정 API

설정을 관리하기 위해 다음 엔터티를 제공합니다.

- roleManagementPolicy
- roleManagementPolicyAssignment

*역할 관리 정책* 은 규칙 설정을 정의합니다. 예를 들어 MFA/승인이 필요한지 여부, 이메일 알림을 보낼지 여부 및 대상 또는 영구 할당이 허용되는지 여부 등이 있습니다. *정책 할당* 은 정책을 특정 역할에 연결합니다.

이 API를 사용하여 모든 roleManagementPolicyAssignments 목록을 가져오고, 수정할 roleDefinitionID를 기준으로 필터링한 다음, policyAssignment와 관련된 정책을 업데이트합니다.

## <a name="relationship-between-pim-entities-and-role-assignment-entities"></a>PIM 엔터티와 역할 할당 엔터티 간의 관계

Azure AD 역할 또는 Azure 역할의 영구(활성) 할당에 대한 PIM 엔터티와 역할 할당 엔터티 간의 유일한 링크는 roleAssignmentScheduleInstance입니다. 두 엔터티 간에 일대일 매핑이 있습니다. 이 매핑은 roleAssignment 및 roleAssignmentScheduleInstance가 다음을 모두 포함한다는 것을 의미합니다.  

- PIM 외부에서 수행된 영구(활성) 할당
- PIM 내에 지정된 일정으로 영구(활성) 할당
- 활성화된 적격 할당

## <a name="next-steps"></a>다음 단계

- [Azure AD Privileged Identity Management API 참고 자료](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta&preserve-view=true)