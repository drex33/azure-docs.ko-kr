---
title: Azure API Management에서 역할 기반 액세스 제어를 사용하는 방법 | Microsoft Docs
description: Azure API Management에서 기본 제공 역할을 사용하고 사용자 지정 역할을 만드는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/18/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 90278abd972b0a6f56e820090435f4cfb8ebbeb6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643292"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Azure API Management에서 역할 기반 액세스 제어를 사용하는 방법

Azure API Management는 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 API Management 서비스 및 엔터티(예: API 및 정책)에 대한 세분화된 액세스 관리를 가능하게 합니다. 이 문서에서는 API Management에서 기본 제공 및 사용자 지정 역할에 대한 개요를 제공합니다. Azure Portal에서 액세스 관리에 대한 자세한 내용은 [Azure Portal에서 액세스 관리 시작](../role-based-access-control/overview.md)을 참조하세요.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>기본 제공 역할

API Management는 현재 3가지 기본 제공 역할을 제공하며 곧 2가지 역할을 추가할 예정입니다. 이러한 역할은 구독, 리소스 그룹 및 개별 API Management 인스턴스를 포함하여 서로 다른 범위에서 할당될 수 있습니다. 예를 들어 'API Management 서비스 읽기 권한자' 역할을 리소스 그룹 수준의 사용자에게 할당한 경우 사용자는 리소스 그룹 내의 모든 API Management 인스턴스에 대한 읽기 권한을 가지게 됩니다. 

다음 테이블은 기본 제공 역할을 간략하게 설명합니다. Azure Portal 또는 Azure [PowerShell](../role-based-access-control/role-assignments-powershell.md), [Azure CLI](../role-based-access-control/role-assignments-cli.md) 및 [REST API](../role-based-access-control/role-assignments-rest.md)를 비롯한 다른 도구를 사용하여 이러한 역할을 할당할 수 있습니다. 기본 제공 역할을 할당하는 방법에 대한 자세한 내용은 [Azure 역할을 할당하여 Azure 구독 리소스에 대한 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.

| 역할          | 읽기 액세스<sup>[1]</sup> | 쓰기 액세스<sup>[2]</sup> | 서비스 만들기, 삭제, 크기 조정, VPN 및 사용자 지정 도메인 구성 | 기존 게시자 포털에 대한 액세스 권한 | Description
| ------------- | ---- | ---- | ---- | ---- | ---- 
| API Management 서비스 참가자 | ✓ | ✓ | ✓ | ✓ | 슈퍼 사용자입니다. API Management 서비스 및 엔터티(예: API 및 정책)에 대한 전체 CRUD 액세스 권한을 보유합니다. 기존 게시자 포털에 대한 액세스 권한을 보유합니다. |
| API Management 읽기 권한자 | ✓ | | || API Management 서비스 및 엔터티에 대한 읽기 전용 액세스 권한을 보유합니다. |
| API Management 서비스 운영자 | ✓ | | ✓ | | 엔터티가 아닌 API Management 서비스를 관리할 수 있습니다.|

<sup>[1] API Management 서비스 및 엔터티(예: API 및 정책)에 대한 읽기 권한을 보유합니다.</sup>

<sup>[2] 다음 작업을 제외한 API Management 서비스 및 엔터티에 대한 쓰기 권한: 인스턴스 생성, 삭제 및 확장, VPN 구성 및 사용자 지정 도메인 설정.</sup>

## <a name="custom-roles"></a>사용자 지정 역할

기본 제공 역할이 사용자의 특정 요구 사항을 충족시키지 못하면 API Management 엔터티에 대한 보다 세분화된 액세스 관리를 제공하는 사용자 지정 역할을 작성할 수 있습니다. 예를 들어 API Management 서비스에 대해서는 읽기 전용 액세스 권한을 보유하지만 하나의 특정 API에 대해서는 쓰기 권한만 보유하는 사용자 지정 역할을 만들 수 있습니다. 사용자 지정 역할에 대한 자세한 내용은 [Azure RBAC에서 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요. 

> [!NOTE]
> Azure Portal에서 API Management 인스턴스를 보려면 사용자 지정 역할이 ```Microsoft.ApiManagement/service/read``` 작업을 포함해야 합니다.

사용자 지정 역할을 만들 때는 기본 제공 역할 중 하나로 시작하는 것이 쉽습니다. 속성을 편집하여 **Actions**, **NotActions** 또는 **AssignableScopes** 를 추가한 다음 변경 사항을 새 역할로 저장합니다. 다음 예제는 'API Management 서비스 읽기 권한자' 역할로 시작하고 '계산기 API 편집자'라는 사용자 지정 역할을 만듭니다. 사용자 지정 역할을 특정 API에 할당할 수 있습니다. 따라서 이 역할만 해당 API에 대한 액세스 권한을 보유합니다. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

[Azure Resource Manager 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) 문서에는 API Management 수준에서 부여할 수 있는 사용 권한 목록이 들어 있습니다.

## <a name="video"></a>비디오


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>다음 단계

Azure의 역할 기반 액세스 제어에 대한 자세한 정보는 다음 문서를 참조하세요.
  * [Azure 포털에서 액세스 관리 시작](../role-based-access-control/overview.md)
  * [Azure 역할을 할당하여 Azure 구독 리소스에 대한 액세스 관리](../role-based-access-control/role-assignments-portal.md)
  * [Azure RBAC에서 사용자 지정 역할](../role-based-access-control/custom-roles.md)
  * [Azure Resource Manager 리소스 공급자 작업](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
