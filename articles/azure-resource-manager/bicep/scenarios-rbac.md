---
title: Bicep를 사용 하 여 Azure RBAC 리소스 만들기
description: Bicep를 사용 하 여 역할 할당 및 역할 정의를 만드는 방법을 설명 합니다.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 12/01/2021
ms.openlocfilehash: c7fbc63ba0891051d5b77f9591dd637d4d97b679
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386922"
---
# <a name="create-azure-rbac-resources-by-using-bicep"></a>Bicep를 사용 하 여 Azure RBAC 리소스 만들기

Azure에는 강력한 RBAC (역할 기반 액세스 제어) 시스템이 있습니다. Bicep를 사용 하 여 RBAC 역할 할당 및 역할 정의를 프로그래밍 방식으로 정의할 수 있습니다.

## <a name="role-assignments"></a>역할 할당

역할 할당을 정의 하려면 유형을 사용 하 여 리소스를 만듭니다 [`Microsoft.Authorization/roleAssignments`](/azure/templates/microsoft.authorization/roleassignments?tabs=bicep) . 역할 정의에는 범위, 이름, 역할 정의 ID, 보안 주체 ID 및 보안 주체 유형을 비롯 한 여러 속성이 있습니다.

### <a name="scope"></a>Scope

역할 할당은 다른 리소스에 적용 됨을 의미 하는 [확장 리소스](scope-extension-resources.md)입니다. 다음 예제에서는 저장소 계정 및 해당 저장소 계정으로 범위가 지정 된 역할 할당을 만드는 방법을 보여 줍니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/scenarios-rbac/scope.bicep" highlight="17" :::

범위를 명시적으로 지정 하지 않으면 Bicep는 파일의를 사용 `targetScope` 합니다. 다음 예에서는 `scope` 속성을 지정 하지 않으므로 역할 할당이 구독에 적용 됩니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/scenarios-rbac/scope-default.bicep" highlight="4" :::

> [!TIP]
> 요구 사항에 필요한 가장 작은 범위를 사용 하는지 확인 합니다.
>
> 예를 들어 관리 id에 단일 저장소 계정에 대 한 액세스 권한을 부여 해야 하는 경우 리소스 그룹이 나 구독 범위가 아닌 저장소 계정의 범위에서 역할 할당을 만드는 것이 좋습니다.

### <a name="name"></a>이름

역할 할당의 리소스 이름은 GUID (globally unique identifier) 여야 합니다. 범위, 보안 주체 ID 및 역할 ID를 함께 사용 하는 GUID를 만드는 것이 좋습니다.

> [!TIP]
> 함수를 사용 `guid()` 하 여 다음 예제와 같이 역할 할당 이름에 대 한 결정적 GUID를 만들 수 있습니다.
>
> ```bicep
> name: guid(subscription().id, principalId, roleDefinitionResourceId)
> ```

### <a name="role-definition-id"></a>역할 정의 ID

할당 하는 역할은 기본 제공 역할 정의 또는 [사용자 지정 역할 정의](#custom-role-definitions)일 수 있습니다. 기본 제공 역할 정의를 사용 하려면 [적절 한 역할 정의 ID를 찾습니다](/azure/role-based-access-control/built-in-roles). 예를 들어 *참가자* 역할의 역할 정의 ID는 `b24988ac-6180-42a0-ab88-20f7382dd24c` 입니다.

역할 할당 리소스를 만들 때 정규화 된 리소스 ID를 지정 해야 합니다. 기본 제공 역할 정의 Id는 구독 범위 리소스입니다. 리소스를 사용 하 여 `existing` 기본 제공 역할을 참조 하 고 속성을 사용 하 여 정규화 된 리소스 ID에 액세스 하는 것이 좋습니다 `.id` .

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/scenarios-rbac/built-in-role.bicep" highlight="3-7, 12" :::

### <a name="principal"></a>주 서버

`principalId`속성은 보안 주체의 Azure Active Directory (Azure AD) 식별자를 나타내는 GUID로 설정 되어야 합니다. Azure AD에서이를 *개체 ID* 라고도 합니다.

`principalType`속성은 보안 주체가 사용자, 그룹 또는 서비스 사용자 인지 여부를 지정 합니다. 관리 id는 서비스 사용자의 형식입니다.

> [!TIP]
> `principalType`Bicep에서 역할 할당을 만들 때 속성을 설정 하는 것이 중요 합니다. 그렇지 않으면 특히 서비스 사용자와 관리 되는 id로 작업 하는 경우 일시적인 배포 오류가 발생할 수 있습니다.

다음 예에서는 사용자 할당 관리 id 및 역할 할당을 만드는 방법을 보여 줍니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/scenarios-rbac/managed-identity.bicep" highlight="15-16" :::

## <a name="custom-role-definitions"></a>사용자 지정 역할 정의

사용자 지정 역할 정의를 만들려면 형식의 리소스를 정의 `Microsoft.Authorization/roleDefinitions` 합니다. 예는 [구독 수준 배포를 통해 새 역할 정의 만들기](https://azure.microsoft.com/resources/templates/create-role-def/) 빠른 시작을 참조 하세요.

> [!NOTE]
> 일부 서비스는 자체 역할 정의 및 할당을 관리 합니다. 예를 들어 Azure Cosmos DB는 자체 및 리소스를 유지 관리 [`Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments`](/azure/templates/microsoft.documentdb/databaseaccounts/sqlroleassignments?tabs=bicep) [`Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions`](/azure/templates/microsoft.documentdb/databaseaccounts/sqlroledefinitions?tabs=bicep) 합니다. 자세한 내용은 특정 서비스의 설명서를 참조 하세요.

## <a name="related-resources"></a>관련 참고 자료

- 리소스 설명서
  - [`Microsoft.Authorization/roleAssignments`](/azure/templates/microsoft.authorization/roleassignments?tabs=bicep)
  - [`Microsoft.Authorization/roleDefinitions`](/azure/templates/microsoft.authorization/roledefinitions?tabs=bicep)
- [확장 리소스](scope-extension-resources.md)
- 범위
  - [리소스 그룹](deploy-to-resource-group.md)
  - [구독](deploy-to-subscription.md)
  - [관리 그룹](deploy-to-management-group.md)
  - [테넌트](deploy-to-tenant.md)
- 빠른 시작 템플릿
  - [구독 수준 배포를 통해 새 역할 정의 만들기](https://azure.microsoft.com/resources/templates/create-role-def/)
  - [구독 범위에서 역할 할당](https://azure.microsoft.com/resources/templates/subscription-role-assignment/)
  - [테 넌 트 범위에서 역할 할당](https://azure.microsoft.com/resources/templates/tenant-role-assignment/)
  - [ResourceGroup 만들기, 잠금 및 RBAC 적용](https://azure.microsoft.com/resources/templates/create-rg-lock-role-assignment/)
