---
title: Azure Active Directory 인증만
description: 이 문서에서는 Azure SQL Database 및 azure SQL에서 Azure Active Directory (azure AD) 인증 기능에 대 한 정보를 제공 Managed Instance
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 74b02577e6bb59481182afda881216ebff0544cf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044041"
---
# <a name="azure-ad-only-authentication-with-azure-sql"></a>Azure SQL을 사용한 Azure AD 전용 인증

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure AD 전용 인증은 서비스에서 Azure AD 인증만 지원하도록 하며 [Azure SQL Database](sql-database-paas-overview.md) 및 [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)에 대해 지원되는 [Azure SQL](../azure-sql-iaas-vs-paas-what-is-overview.md)의 기능입니다. SQL Server 관리자, 로그인 및 사용자의 연결을 포함하여 Azure SQL 환경에서 Azure AD 전용 인증을 사용하는 경우 SQL 인증을 사용할 수 없습니다. [Azure AD 인증](authentication-aad-overview.md)을 사용하는 사용자에게만 서버 또는 데이터베이스에 연결할 수 있는 권한이 부여됩니다.

Azure Portal, Azure CLI, PowerShell 또는 REST API를 사용하여 Azure AD 전용 인증을 사용하거나 사용하지 않도록 설정할 수 있습니다. ARM 템플릿을 사용하여 서버를 만드는 동안 Azure AD 전용 인증을 구성할 수도 있습니다.

Azure SQL 인증에 대한 자세한 내용은 [인증 및 권한 부여](logins-create-manage.md#authentication-and-authorization)를 참조하세요.

## <a name="feature-description"></a>기능 설명

Azure AD 전용 인증을 사용 하는 경우 [SQL 인증이](logins-create-manage.md#authentication-and-authorization) 서버 또는 관리 되는 인스턴스 수준에서 사용 하지 않도록 설정 되 고 SQL 인증 자격 증명을 기반으로 하는 모든 인증을 방지 합니다. SQL 인증 사용자는 모든 데이터베이스를 포함 하 여 Azure SQL Database 또는 관리 되는 인스턴스의 [논리 서버](logical-servers.md) 에 연결할 수 없습니다. SQL 인증을 사용하지 않도록 설정해도 적절한 권한이 있는 Azure AD 계정으로 새 SQL 인증 로그인 및 사용자를 계속 만들 수 있습니다. 새로 만든 SQL 인증 계정은 서버에 연결할 수 없습니다. Azure AD 전용 인증을 사용하도록 설정해도 기존 SQL 인증 로그인 및 사용자 계정이 제거되지는 않습니다. 이 기능은 이러한 계정으로 서버와 이 서버에 대해 만들어진 모든 데이터베이스에 연결하는 것만 방지합니다.

Azure Policy를 사용 하 여 Azure AD 전용 인증을 사용 하 여 강제로 서버를 만들도록 할 수도 있습니다. 자세한 내용은 [AZURE AD 전용 인증에 대 한 Azure Policy](authentication-azure-ad-only-authentication-policy.md)를 참조 하세요.

## <a name="permissions"></a>사용 권한

Azure AD 전용 인증은 Azure 구독 [소유자](../../role-based-access-control/built-in-roles.md#owner), [기여자](../../role-based-access-control/built-in-roles.md#contributor) 및 [전역 관리자](../../active-directory/roles/permissions-reference.md#global-administrator)와 같은 높은 권한의 [Azure AD 기본 제공 역할](../../role-based-access-control/built-in-roles.md) 멤버인 Azure AD 사용자가 사용하거나 사용하지 않도록 설정할 수 있습니다. 또한 [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 있어도 Azure AD 전용 인증 기능을 사용하거나 사용하지 않도록 설정할 수 있습니다.

[SQL Server 기여자](../../role-based-access-control/built-in-roles.md#sql-server-contributor) 및 [SQL Managed Instance 기여자](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) 역할에는 Azure AD 전용 인증 기능을 사용하거나 사용하지 않도록 설정할 수 있는 권한이 없습니다. 이것은 Azure SQL Server를 만들거나 Azure AD 관리자를 만들 수 있는 사용자가 보안 기능을 사용하거나 사용하지 않도록 설정할 수 없는 [의무 분리](security-best-practice.md#implement-separation-of-duties)에 부합됩니다.

### <a name="actions-required"></a>필요한 작업

다음 작업은 Azure AD 전용 인증 기능을 관리할 수 있도록 하기 위해 [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할에 추가됩니다.

- Microsoft.Sql/servers/azureADOnlyAuthentications/*
- Microsoft .Sql/servers/administrators/read - Azure Portal **Azure Active Directory** 메뉴에 액세스 하는 사용자에게만 필요합니다.
- Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*
- Microsoft.Sql/managedInstances/read

위의 작업을 사용자 지정 역할에 추가하여 Azure AD 전용 인증을 관리할 수도 있습니다. 자세한 내용은 [Azure Active Directory에서 사용자 지정 역할 만들기 및 할당](../../active-directory/roles/custom-create.md)을 참조하세요.

## <a name="managing-azure-ad-only-authentication-using-apis"></a>API를 사용하여 Azure AD 전용 인증 관리

> [!IMPORTANT]
> Azure AD 전용 인증을 사용하도록 설정하려면 먼저 Azure AD 관리자를 설정해야 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 버전이 **2.14.2** 이상이어야 합니다.

`name`은 서버 또는 인스턴스 이름(예: `myserver`)의 접두사에 해당하고 `resource-group`은 서버가 속한 리소스 (예: `myresource`)에 해당합니다.

## <a name="azure-sql-database"></a>Azure SQL Database

자세한 내용은 [az sql server ad-only-auth](/cli/azure/sql/server/ad-only-auth)를 참조하세요.

### <a name="enable-or-disable-in-sql-database"></a>RBS 데이터베이스를 사용하거나 사용하지 않도록 설정

**사용**

```azurecli
az sql server ad-only-auth enable --resource-group myresource --name myserver
```

**사용 안함**

```azurecli
az sql server ad-only-auth disable --resource-group myresource --name myserver
```

### <a name="check-the-status-in-sql-database"></a>SQL Database에서 상태 확인

```azurecli
az sql server ad-only-auth get --resource-group myresource --name myserver
```

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

자세한 내용은 [az sql mi ad-only-auth](/cli/azure/sql/mi/ad-only-auth)를 참조하세요.

**사용**

```azurecli
az sql mi ad-only-auth enable --resource-group myresource --name myserver
```

**사용 안함**

```azurecli
az sql mi ad-only-auth disable --resource-group myresource --name myserver
```

### <a name="check-the-status-in-sql-managed-instance"></a>SQL Managed Instance에서 상태 확인

```azurecli
az sql mi ad-only-auth get --resource-group myresource --name myserver
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0) 모듈 이상이 필요합니다.

`ServerName` 또는 `InstanceName`은 서버 이름의 접두사(예: `myserver` 또는 `myinstance`)에 해당하고 `ResourceGroupName`는 서버가 속한 리소스(예: `myresource`)에 해당합니다.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="enable-or-disable-in-sql-database"></a>RBS 데이터베이스를 사용하거나 사용하지 않도록 설정

**사용**

자세한 내용은 [Enable-AzSqlServerActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlserveractivedirectoryonlyauthentication)을 참조하세요. `get-help Enable-AzSqlServerActiveDirectoryOnlyAuthentication -full`을 실행할 수도 있습니다.

```powershell
Enable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName myserver -ResourceGroupName myresource
```

다음 명령을 사용할 수도 있습니다.

```powershell
Get-AzSqlServer -ServerName myserver | Enable-AzSqlServerActiveDirectoryOnlyAuthentication
```

**사용 안 함**

자세한 내용은 [Disable-AzSqlServerActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/disable-azsqlserveractivedirectoryonlyauthentication)을 참조하세요.

```powershell
Disable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName myserver -ResourceGroupName myresource
```

### <a name="check-the-status-in-sql-database"></a>SQL Database에서 상태 확인

```powershell
Get-AzSqlServerActiveDirectoryOnlyAuthentication  -ServerName myserver -ResourceGroupName myresource
```

다음 명령을 사용할 수도 있습니다.

```powershell
Get-AzSqlServer -ServerName myserver | Get-AzSqlServerActiveDirectoryOnlyAuthentication
```

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

### <a name="enable-or-disable-in-sql-managed-instance"></a>SQL Managed Instance에서 사용하거나 사용하지 않도록 설정

**사용**

자세한 내용은 [Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlinstanceactivedirectoryonlyauthentication)을 참조하세요.

```powershell
Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

다음 명령을 사용할 수도 있습니다.

```powershell
Get-AzSqlInstance -InstanceName myinstance | Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication
```

이러한 PowerShell 명령에 대한 자세한 내용을 보려면 `get-help  Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -full`을 실행합니다.

**사용 안 함**

자세한 내용은 [Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/disable-azsqlinstanceactivedirectoryonlyauthentication)을 참조하세요.

```powershell
Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

### <a name="check-the-status-in-sql-managed-instance"></a>SQL Managed Instance에서 상태 확인

```powershell
Get-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName myinstance -ResourceGroupName myresource
```

다음 명령을 사용할 수도 있습니다.

```powershell
Get-AzSqlInstance -InstanceName myinstance | Get-AzSqlInstanceActiveDirectoryOnlyAuthentication
```

# <a name="rest-api"></a>[REST API](#tab/rest-api)

다음 매개 변수를 정의해야 합니다.

- `<subscriptionId>`는 Azure Portal에서 **구독** 으로 이동하여 찾을 수 있습니다.
- `<myserver>`는 서버 또는 인스턴스 이름의 접두사(예: `myserver`)에 해당합니다.
- `<myresource>`는 서버가 속한 리소스(예: `myresource`)에 해당합니다.

최신 MSAL을 사용하려면 https://www.powershellgallery.com/packages/MSAL.PS 에서 다운로드합니다.

```rest
$subscriptionId = '<subscriptionId>'
$serverName = "<myserver>"
$resourceGroupName = "<myresource>"
```

## <a name="azure-sql-database"></a>Azure SQL Database

자세한 내용은 [서버 Azure ADA 전용 인증](/rest/api/sql/2021-02-01-preview/serverazureadonlyauthentications) REST API 설명서를 참조하세요.

### <a name="enable-or-disable-in-sql-database"></a>RBS 데이터베이스를 사용하거나 사용하지 않도록 설정

**사용**

```rest
$body = @{ properties = @{ azureADOnlyAuthentication = 1 } } | ConvertTo-Json
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

**사용 안함**

```rest
$body = @{ properties = @{ azureADOnlyAuthentication = 0 } } | ConvertTo-Json
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

### <a name="check-the-status-in-sql-database"></a>SQL Database에서 상태 확인

```rest
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method GET -Headers $authHeader  | Format-List
```

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

자세한 내용은 [Managed Instance Azure AD 전용 인증](/rest/api/sql/2021-02-01-preview/managedinstanceazureadonlyauthentications) REST API 설명서를 참조하세요.

### <a name="enable-or-disable-in-sql-managed-instance"></a>SQL Managed Instance에서 사용하거나 사용하지 않도록 설정

**사용**

```rest
$body = @{   properties = @{  azureADOnlyAuthentication = 1 }  } | ConvertTo-Json 
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

**사용 안함**

```rest
$body = @{   properties = @{  azureADOnlyAuthentication = 0 }  } | ConvertTo-Json 
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

### <a name="check-the-status-in-sql-managed-instance"></a>SQL Managed Instance에서 상태 확인

```rest
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$serverName/azureADOnlyAuthentications/default?api-version=2020-02-02-preview" -Method GET -Headers $authHeader  | Format-List
```

# <a name="arm-template"></a>[ARM 템플릿](#tab/arm)

- 배포에 대한 Azure AD 관리자를 입력합니다. [Azure Portal](https://portal.azure.com) > **Azure Active Directory** 리소스로 이동하여 사용자 개체 ID를 찾습니다. **관리** 에서 **사용자** 를 선택합니다. Azure SQL Server에 대한 Azure AD 관리자로 설정하려는 사용자를 검색합니다. 사용자를 선택하면 해당 **프로필** 페이지 아래에 **개체 ID** 가 표시됩니다.
- 테넌트 ID는 **Azure Active Directory** 리소스의 **개요** 페이지에서 찾을 수 있습니다.

## <a name="azure-sql-database"></a>Azure SQL Database

### <a name="enable"></a>사용

아래 ARM 템플릿은 Azure SQL Database에서 Azure AD 전용 인증을 사용하도록 설정합니다. Azure AD 전용 인증을 사용하지 않도록 설정하려면 `azureADOnlyAuthentication` 속성을 `false`로 설정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "sqlServer_name": {
            "type": "String"
        },
        "aad_admin_name": {
            "type": "String"
        },
        "aad_admin_objectid": {
            "type": "String"
        },
        "aad_admin_tenantid": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/administrators",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('sqlServer_name'), '/ActiveDirectory')]",
            "properties": {
                "administratorType": "ActiveDirectory",
                "login": "[parameters('aad_admin_name')]",
                "sid": "[parameters('aad_admin_objectid')]",
                "tenantId": "[parameters('aad_admin_tenantId')]"
            }
        },        
        {
            "type": "Microsoft.Sql/servers/azureADOnlyAuthentications",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('sqlServer_name'), '/Default')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/administrators', parameters('sqlServer_name'), 'ActiveDirectory')]"
            ],
            "properties": {
                "azureADOnlyAuthentication": true
            }
        }
    ]
}
```

자세한 내용은 [Microsoft.Sql servers/azureADOnlyAuthentications](/azure/templates/microsoft.sql/servers/azureadonlyauthentications)를 참조하세요.

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

### <a name="enable"></a>사용

아래 ARM 템플릿은 Azure SQL Managed Instance에서 Azure AD 전용 인증을 사용하도록 설정합니다. Azure AD 전용 인증을 사용하지 않도록 설정하려면 `azureADOnlyAuthentication` 속성을 `false`로 설정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "String"
        },
        "aad_admin_name": {
            "type": "String"
        },
        "aad_admin_objectid": {
            "type": "String"
        },
        "aad_admin_tenantid": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/managedInstances/administrators",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('instance'), '/ActiveDirectory')]",
            "properties": {
                "administratorType": "ActiveDirectory",
                "login": "[parameters('aad_admin_name')]",
                "sid": "[parameters('aad_admin_objectid')]",
                "tenantId": "[parameters('aad_admin_tenantId')]"
            }
        },
        {
            "type": "Microsoft.Sql/managedInstances/azureADOnlyAuthentications",
            "apiVersion": "2020-02-02-preview",
            "name": "[concat(parameters('instance'), '/Default')]",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/managedInstances/administrators', parameters('instance'), 'ActiveDirectory')]"
            ],
            "properties": {
                "azureADOnlyAuthentication": true
            }
        }
    ]
}

```

자세한 내용은 [Microsoft.Sql managedInstances/azureADOnlyAuthentications](/azure/templates/microsoft.sql/managedinstances/azureadonlyauthentications)를 참조하세요.

---

### <a name="checking-azure-ad-only-authentication-using-t-sql"></a>T-SQL을 사용하여 Azure AD 전용 인증 확인

서버 또는 관리되는 인스턴스에 대해 Azure AD 전용 인증을 사용할 수 있는지 확인하기 위해[SEVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql) `IsExternalAuthenticationOnly`가 추가되었습니다. `1`은 해당 기능이 사용하도록 설정되어 있음을 나타내고 `0`은 해당 기능이 사용하지 않도록 설정되어 있음을 나타냅니다.

```sql
SELECT SERVERPROPERTY('IsExternalAuthenticationOnly') 
```

## <a name="remarks"></a>설명

- [SQL Server 기여자](../../role-based-access-control/built-in-roles.md#sql-server-contributor)는 Azure AD 관리자를 설정하거나 제거할 수 있지만 **Azure Active Directory 인증 전용** 설정은 지정할 수 없습니다. [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager)는 Azure AD 관리자를 설정하거나 제거할 수 없지만 **Azure Active Directory 인증 전용** 설정은 지정할 수 있습니다. 두 사용 권한을 모두 포함하는 더 높은 Azure RBAC 역할 또는 사용자 지정 역할이 있는 계정만 Azure AD 관리자를 설정 또는 제거하고 **Azure Active Directory 인증 전용** 설정을 지정할 수 있습니다. 이러한 역할 중 하나는 [기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할입니다.
- Azure Portal에서 **Azure Active Directory 인증 전용** 을 사용하거나 사용하지 않도록 설정한 후에는 **SQL Server** 메뉴에서 **활동 로그** 항목을 볼 수 있습니다.
    :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-portal-sql-server-activity-log.png" alt-text="Azure Portal의 활동 로그 항목":::
- **Azure Active Directory 관리자** 가 지정된 경우 올바른 권한이 있는 사용자만 **Azure Active Directory 인증 전용** 설정을 사용하거나 사용하지 않도록 설정할 수 있습니다. Azure AD 관리자가 설정되지 않은 경우에는 **Azure Active Directory 인증 전용** 설정이 비활성화 상태를 유지하며 사용하거나 사용하지 않도록 설정할 수 없습니다. Azure AD 관리자를 설정하지 않은 경우에는 API를 사용하여 Azure AD 전용 인증을 사용하도록 설정하지 못합니다.
- 적절한 권한이 있는 사용자는 Azure AD 전용 인증을 사용하도록 설정한 경우 Azure AD 관리자를 변경할 수 있습니다.
- 적절한 권한이 있는 사용자는 Azure Portal에서 Azure AD 관리자를 변경하고 Azure AD 전용 인증을 사용하거나 사용하지 않도록 설정할 수 있습니다. 두 작업은 모두 Azure Portal에서 한 번의 **저장** 으로 완료할 수 있습니다. Azure AD 전용 인증을 사용하도록 설정하려면 Azure AD 관리자를 설정해야 합니다.
- Azure AD 전용 인증 기능을 사용하도록 설정한 경우 Azure AD 관리자를 제거하는 것은 지원되지 않습니다. Azure AD 전용 인증을 사용하는 경우 API를 사용하여 Azure AD 관리자를 제거하지 못합니다.
    - **Azure Active Directory 인증 전용** 설정을 사용하도록 설정하면 Azure Portal에서 **관리자 제거** 단추가 비활성화됩니다.
- Azure AD 관리자를 제거하고 **Azure Active Directory 인증 전용** 설정을 사용하지 않도록 지정하는 것은 허용되지만, 작업을 완료하려면 올바른 사용자 권한이 있어야 합니다. 두 작업은 모두 Azure Portal에서 한 번의 **저장** 으로 완료할 수 있습니다.
- 적절한 권한이 있는 Azure AD 사용자는 기존 SQL 사용자를 가장할 수 있습니다.
    - Azure AD 전용 인증 기능을 사용하도록 설정한 경우에도 SQL 인증 사용자 간에 가장이 계속 작동합니다.

### <a name="limitations-for-azure-ad-only-authentication-in-sql-database"></a>SQL Database에서 Azure AD 전용 인증에 대 한 제한 사항

SQL Database에 대해 Azure AD 전용 인증을 사용 하는 경우 다음 기능이 지원 되지 않습니다.

- [서버 역할 Azure SQL Database](security-server-roles.md)
- [탄력적 작업](job-automation-overview.md)
- [SQL 데이터 동기화](sql-data-sync-data-sql-server-sql-database.md)
- [CDC (변경 데이터 캡처)](/sql/relational-databases/track-changes/about-change-data-capture-sql-server)
- [트랜잭션 복제](/azure/azure-sql/managed-instance/replication-transactional-overview) -복제 참가자 간 연결에는 인증이 필요 SQL 하므로 azure AD 전용 인증을 사용 하는 경우에는 트랜잭션 복제를 사용 하 여 azure SQL Managed Instance, 온-프레미스 SQL Server 또는 azure에서 변경 된 내용을 푸시하는 시나리오에 대 한 SQL Database에 대해 트랜잭션 복제가 지원 되지 않습니다.  Azure SQL Database의 데이터베이스에 대 한 VM SQL Server 인스턴스
- [SQL 인사이트](/azure/azure-monitor/insights/sql-insights-overview)
- Azure AD 그룹 구성원 계정에 대 한 EXEC AS 문

### <a name="limitations-for-azure-ad-only-authentication-in-managed-instance"></a>Managed Instance에서 Azure AD 전용 인증에 대 한 제한 사항

Managed Instance에 대해 Azure AD 전용 인증을 사용 하는 경우 다음 기능이 지원 되지 않습니다.

- [트랜잭션 복제](/azure/azure-sql/managed-instance/replication-transactional-overview) 
- [Managed Instance의 SQL 에이전트 작업](../managed-instance/job-automation-managed-instance.md) 은 Azure AD 전용 인증을 지원 합니다. 그러나 관리 되는 인스턴스에 대 한 액세스 권한이 있는 azure ad 그룹의 멤버인 azure ad 사용자는 SQL 에이전트 작업을 소유할 수 없습니다.
- [SQL 인사이트](/azure/azure-monitor/insights/sql-insights-overview)
- Azure AD 그룹 구성원 계정에 대 한 EXEC AS 문

제한 사항에 대 한 자세한 내용은 [Azure SQL Managed Instance & SQL Server 간의 T SQL 차이점](../managed-instance/transact-sql-tsql-differences-sql-server.md#logins-and-users)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: Azure SQL을 사용한 Azure Active Directory 전용 인증 사용](authentication-azure-ad-only-authentication-tutorial.md)

> [!div class="nextstepaction"]
> [Azure SQL에서 Azure AD 전용 인증이 사용하도록 설정된 서버 만들기](authentication-azure-ad-only-authentication-create-server.md)
