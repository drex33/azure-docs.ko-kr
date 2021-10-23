---
title: Azure Active Directory 인증만 사용하도록 설정된 서버 만들기
description: 이 문서에서는 SQL 인증을 사용하여 연결을 사용하지 않도록 설정하는 Azure AD(Azure Active Directory) 전용 인증이 설정된 Azure SQL 논리 서버 또는 Managed Instance를 만드는 과정을 안내합니다.
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/19/2021
ms.openlocfilehash: 20784a944ff886d645a030fd468fc7aa45258a1d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256075"
---
# <a name="create-server-with-azure-ad-only-authentication-enabled-in-azure-sql"></a>Azure SQL에서 Azure AD 전용 인증이 사용하도록 설정된 서버 만들기

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 이 문서에서 설명하는 **Azure AD 전용 인증** 기능은 **퍼블릭 미리 보기** 로 제공됩니다. 이 기능에 대한 자세한 내용은 [Azure SQL을 사용하는 Azure AD 전용 인증](authentication-azure-ad-only-authentication.md)을 참조하세요. Azure AD 전용 인증은 현재 Azure Synapse Analytics에서 사용할 수 없습니다.

이 방법 가이드에서는 프로비전하는 동안 [Azure AD 전용 인증을](authentication-azure-ad-only-authentication.md) 사용하는 Azure SQL Database 또는 [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) [논리 서버를](logical-servers.md) 만드는 단계를 간략하게 설명합니다. Azure AD 전용 인증 기능을 사용하면 사용자가 SQL 인증을 사용하여 서버 또는 관리되는 인스턴스에 연결할 수 없으며 Azure AD 인증을 통해서만 연결할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure CLI를 사용하는 경우 버전 2.26.1 이상이 필요합니다. 최신 버전을 설치하고 사용하는 방법에 대한 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
- PowerShell을 사용하는 경우 [Az 6.1.0](https://www.powershellgallery.com/packages/Az/6.1.0) 이상 모듈이 필요합니다.
- Azure CLI, PowerShell 또는 Rest API를 사용하여 관리되는 인스턴스를 프로비저닝하는 경우 시작하기 전에 가상 네트워크 및 서브넷을 만들어야 합니다. 자세한 내용은 [Azure SQL Managed Instance의 가상 네트워크 만들기](../managed-instance/virtual-network-subnet-create-arm-template.md)를 참조하세요.

## <a name="permissions"></a>사용 권한

논리 서버 또는 관리되는 인스턴스를 프로비전하려면 이러한 리소스를 만들 수 있는 적절한 권한이 있어야 합니다. 구독 [소유자](../../role-based-access-control/built-in-roles.md#owner), [기여자](../../role-based-access-control/built-in-roles.md#contributor), [서비스 관리자](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles) 및 [공동 관리자](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)와 같이 더 높은 권한이 있는 Azure 사용자는 SQL 서버 또는 관리되는 인스턴스를 만들 수 있는 권한이 있습니다. 최소 권한 Azure RBAC 역할을 사용하여 이러한 리소스를 만들려면 SQL Database에 대한 [SQL Server 기여자](../../role-based-access-control/built-in-roles.md#sql-server-contributor) 및 Managed Instance에 대한 [SQL Managed Instance 기여자](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) 역할을 사용합니다.

[SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) Azure RBAC 역할에는 Azure AD 전용 인증을 사용하도록 설정한 서버 또는 인스턴스를 만들 수 있는 충분한 권한이 없습니다. 서버 또는 인스턴스를 만든 후에는 Azure AD 전용 인증 기능을 관리하기 위해 [SQL Security Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 필요합니다.

## <a name="provision-with-azure-ad-only-authentication-enabled"></a>Azure AD 전용 인증을 사용하도록 설정하여 프로비저닝

다음 섹션에서는 서버 또는 인스턴스에 대해 Azure AD 관리자 집합을 사용하여 논리 서버 또는 관리형 인스턴스를 만들고 서버를 만드는 동안 Azure AD 전용 인증을 사용하도록 설정하는 방법에 대한 예제와 스크립트를 제공합니다. 이 기능에 대한 자세한 내용은 [Azure AD 전용 인증](authentication-azure-ad-only-authentication.md)을 참조하세요.

이 예제에서는 시스템 할당 서버 관리자 및 암호를 사용하여 서버 또는 관리되는 인스턴스를 만드는 동안 Azure AD 전용 인증을 사용하도록 설정합니다. 이렇게 하면 Azure AD 전용 인증을 사용하는 경우 서버 관리자 액세스가 방지되며 Azure AD 관리자만 리소스에 액세스할 수 있습니다. 서버를 만드는 동안 고유한 서버 관리자와 암호를 포함하는 매개 변수를 API에 추가하는 것은 선택 사항입니다. 그러나 Azure AD 전용 인증을 사용하지 않도록 설정할 때까지 암호를 다시 설정할 수 없습니다.

서버 또는 관리되는 인스턴스를 만든 후 기존 속성을 변경하려면 다른 기존 API를 사용해야 합니다. 자세한 내용은 [API를 사용하여 Azure AD 전용 인증 관리](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis) 및 [Azure SQL Azure AD 인증 구성 및 관리를 참조하세요.](authentication-aad-configure.md)

> [!NOTE]
> Azure AD 전용 인증이 false(기본값)로 설정된 경우 서버 관리자 및 암호는 서버 또는 관리되는 인스턴스를 만드는 동안 모든 API에 포함되어야 합니다.

## <a name="azure-sql-database"></a>Azure SQL Database

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal SQL 배포 옵션 선택](https://portal.azure.com/#create/Microsoft.AzureSQL) 페이지로 이동하세요.

1. Azure Portal 로그인하지 않은 경우 메시지가 표시되면 로그인합니다.

1. **SQL 데이터베이스** 에서 **리소스 유형** 을 **단일 데이터베이스** 로 설정한 상태로 두고 **만들기** 를 선택합니다.

1. **SQL 데이터베이스 만들기** 양식의 **기본** 탭에 있는 **프로젝트 세부 정보** 아래에서 원하는 Azure **구독** 을 선택합니다.

1. **리소스 그룹** 에 **새로 만들기** 를 선택하고, 새 리소스 그룹의 이름을 입력한 다음, **확인** 을 선택합니다.

1. 데이터베이스 이름 에 **데이터베이스의** 이름을 입력합니다.

1. **서버에서** **새로 만들기를** 선택하고 새 서버 양식을 다음 값으로 채웁니다.

   - **서버 이름:** 고유한 서버 이름을 입력합니다. 서버 이름은 구독 내에서만 고유하지 않고 Azure의 모든 서버에 대해 전역적으로 고유해야 합니다. 값을 입력하면 Azure Portal 사용할 수 있는지 여부를 알 수 있습니다.
   - **위치:** 드롭다운 목록에서 위치를 선택합니다.
   - **인증 방법:** **Azure AD(Azure Active Directory) 인증만 사용을** 선택합니다.
   - **관리자 설정을** 선택합니다. 그러면 논리 서버 Azure AD 관리자로 Azure AD 보안 주체를 선택하는 메뉴가 표시됩니다. 완료되면 **선택** 단추를 사용하여 관리자를 설정합니다.

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-portal-create-server.png" alt-text="Azure AD 전용 인증을 사용하도록 설정된 서버를 만드는 스크린샷":::
    
1. 완료되면 **다음: 네트워킹** 을 선택합니다.

1. **네트워킹** 탭에서 **연결 방법** 에 대해 **퍼블릭 엔드포인트** 를 선택합니다.

1. **방화벽 규칙** 의 경우 **현재 클라이언트 IP 주소 추가** 를 **예** 로 설정합니다. **Azure 서비스 및 리소스가 이 서버에 액세스할 수 있도록 허용** 을 **아니요** 로 설정된 상태로 둡니다. 

1. **연결 정책** 및 **최소 TLS 버전** 설정을 기본값으로 둡니다.

1. 페이지 하단에서 **다음: 보안** 을 선택합니다. 환경에 대한 SQL , **원장,** **ID** 및 **투명한 데이터 암호화에** **대한 Azure Defender** 설정을 구성합니다. 이러한 설정을 건너뛸 수도 있습니다.

   > [!NOTE]
   > 사용자 할당 관리 ID(UMI)를 사용하는 것은 Azure AD 전용 인증에서 지원되지 않습니다. **ID** 섹션의 서버 ID를 UMI로 설정하지 마십시오.

1. 페이지 아래쪽에서 **검토 + 만들기** 를 선택합니다.

1. **검토 + 만들기** 페이지에서 검토 후 **만들기** 를 선택합니다.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 명령은 새 `az sql server create` 논리 서버를 프로비전하는 데 사용됩니다. 아래 명령은 Azure AD 전용 인증을 사용하도록 설정하여 새 서버를 프로비저닝합니다.

서버 SQL 관리자 로그인이 자동으로 만들어지고 암호는 임의의 암호로 설정됩니다. 이 서버를 만들 때 SQL 인증 연결을 사용할 수 없으므로 SQL 관리자 로그인은 사용되지 않습니다.

서버 Azure AD 관리자는 `<AzureADAccount>`에 대해 설정한 계정이 되며 서버를 관리하는 데 사용할 수 있습니다.

예제에서 다음 값을 바꿉니다.

- `<AzureADAccount>`: Azure AD 사용자 또는 그룹이 될 수 있습니다. 예를 들어 `DummyLogin`
- `<AzureADAccountSID>`: 사용자의 Azure AD 개체 ID
- `<ResourceGroupName>`: 논리 서버의 리소스 그룹 이름
- `<ServerName>`: 고유한 논리 서버 이름 사용

```azurecli
az sql server create --enable-ad-only-auth --external-admin-principal-type User --external-admin-name <AzureADAccount> --external-admin-sid <AzureADAccountSID> -g <ResourceGroupName> -n <ServerName>
```

자세한 내용은 [az sql server create](/cli/azure/sql/server#az_sql_server_create)를 참조하세요.

만든 후 서버 상태를 확인하려면 다음 명령을 참조하세요.

```azurecli
az sql server show --name <ServerName> --resource-group <ResourceGroupName> --expand-ad-admin
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 명령 `New-AzSqlServer`는 새 Azure SQL 논리 서버를 프로비저닝하는 데 사용됩니다. 아래 명령은 Azure AD 전용 인증을 사용하도록 설정하여 새 서버를 프로비저닝합니다. 

서버 SQL 관리자 로그인이 자동으로 만들어지고 암호는 임의의 암호로 설정됩니다. 이 서버를 만들 때 SQL 인증 연결을 사용할 수 없으므로 SQL 관리자 로그인은 사용되지 않습니다.

서버 Azure AD 관리자는 `<AzureADAccount>`에 대해 설정한 계정이 되며 서버를 관리하는 데 사용할 수 있습니다.

예제에서 다음 값을 바꿉니다.

- `<ResourceGroupName>`: 논리 서버의 리소스 그룹 이름
- `<Location>`: 서버의 위치(예: `West US` 또는 `Central US`)
- `<ServerName>`: 고유한 논리 서버 이름 사용
- `<AzureADAccount>`: Azure AD 사용자 또는 그룹이 될 수 있습니다. 예를 들어 `DummyLogin`

```powershell
New-AzSqlServer -ResourceGroupName "<ResourceGroupName>" -Location "<Location>" -ServerName "<ServerName>" -ServerVersion "12.0" -ExternalAdminName "<AzureADAccount>" -EnableActiveDirectoryOnlyAuthentication
```

자세한 내용은 [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)를 참조하세요.

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

[서버 - 만들기 또는 업데이트](/rest/api/sql/2020-11-01-preview/servers/create-or-update) Rest API를 사용하여 프로비전하는 동안 Azure AD 전용 인증이 활성화된 논리 서버를 만들 수 있습니다. 

아래 스크립트는 논리 서버를 프로비전하고, Azure AD 관리자를 `<AzureADAccount>` 로 설정하고, Azure AD 전용 인증을 사용하도록 설정합니다. 서버 SQL 관리자 로그인이 자동으로 만들어지고 암호가 임의의 암호로 설정됩니다. 이 프로비저닝 동안 SQL 인증 연결을 사용할 수 없으므로 SQL 관리자 로그인은 사용되지 않습니다.

프로비저닝이 완료되면 Azure AD 관리자 `<AzureADAccount>`를 사용하여 서버를 관리할 수 있습니다.

예제에서 다음 값을 바꿉니다.

- `<tenantId>`: [Azure Portal](https://portal.azure.com), **Azure Active Directory** 리소스로 이동하여 찾을 수 있습니다. **개요** 창에 **테넌트 ID** 가 표시됩니다.
- `<subscriptionId>`: 구독 ID를 Azure Portal에서 찾을 수 있습니다.
- `<ServerName>`: 고유한 논리 서버 이름 사용
- `<ResourceGroupName>`: 논리 서버의 리소스 그룹 이름
- `<AzureADAccount>`: Azure AD 사용자 또는 그룹이 될 수 있습니다. 예를 들어 `DummyLogin`
- `<Location>`: 서버의 위치(예: `westus2` 또는 `centralus`)
- `<objectId>`: [Azure Portal](https://portal.azure.com), **Azure Active Directory** 리소스로 이동하여 찾을 수 있습니다. **사용자** 창에서 Azure AD 사용자를 검색하고 해당 **개체 ID** 를 찾습니다.

```rest
Import-Module Azure
Import-Module MSAL.PS

$tenantId = '<tenantId>'
$clientId = '1950a258-227b-4e31-a9cf-717495945fc2' # Static Microsoft client ID used for getting a token
$subscriptionId = '<subscriptionId>'
$uri = "urn:ietf:wg:oauth:2.0:oob" 
$authUrl = "https://login.windows.net/$tenantId"
$serverName = "<ServerName>"
$resourceGroupName = "<ResourceGroupName>"

Login-AzAccount -tenantId $tenantId

# login as a user with SQL Server Contributor role or higher 

# Get a token

$result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -TenantId $tenantId -Scopes "https://management.core.windows.net/.default"

#Authetication header
$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

# Enable Azure AD-only auth 
# No server admin is specified, and only Azure AD admin and Azure AD-only authentication is set to true
# Server admin (login and password) is generated by the system

# Authentication body
# The sid is the Azure AD Object ID for the user 

$body = '{ 
"location": "<Location>",
"properties": { "administrators":{ "login":"<AzureADAccount>", "sid":"<objectId>", "tenantId":"<tenantId>", "principalType":"User", "azureADOnlyAuthentication":true }
  }
}'

# Provision the server

Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/?api-version=2020-11-01-preview -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

서버 상태를 확인하려면 다음 스크립트를 사용할 수 있습니다.

```rest
$uri = 'https://management.azure.com/subscriptions/'+$subscriptionId+'/resourceGroups/'+$resourceGroupName+'/providers/Microsoft.Sql/servers/'+$serverName+'?api-version=2020-11-01-preview&$expand=administrators/activedirectory'

$responce=Invoke-WebRequest -Uri $uri -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"

$responce.statuscode

$responce.content
```

# <a name="arm-template"></a>[ARM 템플릿](#tab/arm-template)

ARM 템플릿에 대한 자세한 내용은 [Azure SQL Database 및 SQL Managed Instance용 Azure Resource Manager 템플릿](arm-templates-content-guide.md)을 참조하세요.

ARM 템플릿을 사용 하 여 사용 하도록 설정 된 서버 및 azure ad 전용 인증에 대 한 azure ad 관리자 집합을 사용 하 여 논리 서버를 프로 비전 하려면 azure ad 전용 인증 빠른 시작 템플릿을 사용 하 여 [azure SQL 논리 서버](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sql/sql-logical-server-aad-only-auth) 를 참조 하세요.

다음 템플릿을 사용할 수도 있습니다. [Azure Portal의 사용자 지정 배포](https://portal.azure.com/#create/Microsoft.Template)를 사용하고 **편집기에서 사용자 고유의 템플릿을 빌드** 합니다. 다음으로, 예제에 붙여넣은 후에 구성을 **저장** 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "server": {
            "type": "string",
            "defaultValue": "[uniqueString('sql', resourceGroup().id)]",
            "metadata": {
                "description": "The name of the logical server."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "aad_admin_name": {
            "type": "String",
            "metadata": {
                "description": "The name of the Azure AD admin for the SQL server."
            }
        },
        "aad_admin_objectid": {
            "type": "String",
            "metadata": {
                "description": "The Object ID of the Azure AD admin."
            }
        },
        "aad_admin_tenantid": {
            "type": "String",
            "defaultValue": "[subscription().tenantId]",
            "metadata": {
                "description": "The Tenant ID of the Azure Active Directory"
            }
        },
        "aad_admin_type": {
            "defaultValue": "User",
            "allowedValues": [
                "User",
                "Group",
                "Application"
            ],
            "type": "String"
        },
        "aad_only_auth": {
            "defaultValue": true,
            "type": "Bool"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('server')]",
            "location": "[parameters('location')]",
            "properties": {
                "administrators": {
                    "login": "[parameters('aad_admin_name')]",
                    "sid": "[parameters('aad_admin_objectid')]",
                    "tenantId": "[parameters('aad_admin_tenantid')]",
                    "principalType": "[parameters('aad_admin_type')]",
                    "azureADOnlyAuthentication": "[parameters('aad_only_auth')]"
                }
            }
        }
    ]
}
```

---

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

# <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure Portal에서 [SQL 배포 옵션 선택](https://portal.azure.com/#create/Microsoft.AzureSQL) 페이지로 이동 합니다.

1. Azure Portal에 아직 로그인 하지 않은 경우 메시지가 표시 되 면 로그인 합니다.

1. **관리 되는 인스턴스 SQL** 에서 **리소스 유형** 을 **단일 인스턴스로** 설정 하 고 **만들기** 를 선택 합니다.

1. **Project 세부** 정보 및 **Managed Instance 세부 정보** 에 대 한 **기본 사항** 탭에 필요한 필수 정보를 입력 합니다. 이는 SQL Managed Instance를 프로 비전 하는 데 필요한 최소 정보 집합입니다.

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-managed-instance-create-basic.png" alt-text="Managed Instance 기본 만들기 탭의 Azure Portal 스크린샷 ":::

   구성 옵션에 대 한 자세한 내용은 빠른 시작 [: Azure SQL Managed Instance 만들기](../managed-instance/instance-create-quickstart.md)를 참조 하세요.

1. **인증** 에서 **인증 방법** 에 대해 **Azure Active Directory (Azure AD) 인증만 사용** 을 선택 합니다.

1. Azure ad 보안 주체를 관리 되는 인스턴스 Azure AD 관리자로 선택 하는 메뉴를 표시 하는 **관리자 설정** 을 선택 합니다. 완료 되 면 **선택** 단추를 사용 하 여 관리자를 설정 합니다.

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-managed-instance-create-basic-choose-authentication.png" alt-text="Azure Portal Managed Instance 기본 만들기 탭의 스크린샷 및 Azure AD 전용 인증 선택":::

1. 설정의 나머지는 기본값을 그대로 둘 수 있습니다. **네트워킹**, **보안** 또는 다른 탭과 설정에 대 한 자세한 내용은 [빠른 시작: Azure SQL Managed Instance 만들기](../managed-instance/instance-create-quickstart.md)문서에서 가이드를 따르세요.

1. 설정 구성을 완료 한 후 **검토 + 만들기** 를 선택 하 여 계속 합니다. **만들기** 를 선택하여 관리형 인스턴스를 프로비저닝하기 시작합니다.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 명령 `az sql mi create`는 새 Azure SQL Managed Instance를 프로비저닝하는 데 사용됩니다. 아래 명령은 Azure AD 전용 인증을 사용하도록 설정하여 새 관리되는 인스턴스를 프로비저닝합니다.

> [!NOTE]
> 이 스크립트를 사용하려면 먼저 가상 네트워크와 서브넷을 만들어야 합니다.

관리되는 인스턴스 SQL 관리자 로그인이 자동으로 만들어지고 암호는 임의의 암호로 설정됩니다. 이 프로비저닝 동안 SQL 인증 연결을 사용할 수 없으므로 SQL 관리자 로그인은 사용되지 않습니다.

Azure AD 관리자는 `<AzureADAccount>`에 대해 설정한 계정이 되며 프로비저닝이 완료되면 인스턴스를 관리하는 데 사용할 수 있습니다.

예제에서 다음 값을 바꿉니다.

- `<AzureADAccount>`: Azure AD 사용자 또는 그룹이 될 수 있습니다. 예를 들어 `DummyLogin`
- `<AzureADAccountSID>`: 사용자의 Azure AD 개체 ID
- `<managedinstancename>`: 만들려는 관리되는 인스턴스의 이름을 지정합니다.
- `<ResourceGroupName>`: 관리되는 인스턴스에 대한 리소스 그룹의 이름입니다. 리소스 그룹에는 생성된 가상 네트워크 및 서브넷도 포함되어야 합니다.
- `subnet` 매개 변수는 `<Subscription ID>`, `<ResourceGroupName>`, `<VNetName>` 및 `<SubnetName>`으로 업데이트해야 합니다. 구독 ID를 Azure Portal에서 찾을 수 있습니다.

```azurecli
az sql mi create --enable-ad-only-auth --external-admin-principal-type User --external-admin-name <AzureADAccount> --external-admin-sid <AzureADAccountSID> -g <ResourceGroupName> -n <managedinstancename> --subnet /subscriptions/<Subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>
```

자세한 내용은 [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create)를 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 명령 `New-AzSqlInstance`는 새 Azure SQL Managed Instance를 프로비저닝하는 데 사용됩니다. 아래 명령은 Azure AD 전용 인증을 사용하도록 설정하여 새 관리되는 인스턴스를 프로비저닝합니다.

> [!NOTE]
> 이 스크립트를 사용하려면 먼저 가상 네트워크와 서브넷을 만들어야 합니다.

관리되는 인스턴스 SQL 관리자 로그인이 자동으로 만들어지고 암호는 임의의 암호로 설정됩니다. 이 프로비저닝 동안 SQL 인증 연결을 사용할 수 없으므로 SQL 관리자 로그인은 사용되지 않습니다.

Azure AD 관리자는 `<AzureADAccount>`에 대해 설정한 계정이 되며 프로비저닝이 완료되면 인스턴스를 관리하는 데 사용할 수 있습니다.

예제에서 다음 값을 바꿉니다.

- `<managedinstancename>`: 만들려는 관리되는 인스턴스의 이름을 지정합니다.
- `<ResourceGroupName>`: 관리되는 인스턴스에 대한 리소스 그룹의 이름입니다. 리소스 그룹에는 생성된 가상 네트워크 및 서브넷도 포함되어야 합니다.
- `<Location>`: 서버의 위치(예: `West US` 또는 `Central US`)
- `<AzureADAccount>`: Azure AD 사용자 또는 그룹이 될 수 있습니다. 예를 들어 `DummyLogin`
- `SubnetId` 매개 변수는 `<Subscription ID>`, `<ResourceGroupName>`, `<VNetName>` 및 `<SubnetName>`으로 업데이트해야 합니다. 구독 ID를 Azure Portal에서 찾을 수 있습니다.


```powershell
New-AzSqlInstance -Name "<managedinstancename>" -ResourceGroupName "<ResourceGroupName>" -ExternalAdminName "<AzureADAccount>" -EnableActiveDirectoryOnlyAuthentication -Location "<Location>" -SubnetId "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4
```

자세한 내용은 [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)를 참조하세요.

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

[관리되는 인스턴스 - 만들기 또는 업데이트](/rest/api/sql/2020-11-01-preview/managed-instances/create-or-update) Rest API를 사용하여 프로비저닝하는 동안 Azure AD 전용 인증을 사용하도록 설정하여 관리되는 인스턴스를 만들 수 있습니다.

> [!NOTE]
> 이 스크립트를 사용하려면 먼저 가상 네트워크와 서브넷을 만들어야 합니다.

아래 스크립트는 관리되는 인스턴스를 프로비저닝하고, Azure AD 관리자를 `<AzureADAccount>`로 설정하고, Azure AD 전용 인증을 사용하도록 설정합니다. 인스턴스 SQL 관리자 로그인이 자동으로 만들어지고 암호가 임의의 암호로 설정됩니다. 이 프로비저닝 동안 SQL 인증 연결을 사용할 수 없으므로 SQL 관리자 로그인은 사용되지 않습니다.

프로비저닝이 완료되면 Azure AD 관리자 `<AzureADAccount>`를 사용하여 인스턴스를 관리할 수 있습니다.

예제에서 다음 값을 바꿉니다.

- `<tenantId>`: [Azure Portal](https://portal.azure.com), **Azure Active Directory** 리소스로 이동하여 찾을 수 있습니다. **개요** 창에 **테넌트 ID** 가 표시됩니다.
- `<subscriptionId>`: 구독 ID를 Azure Portal에서 찾을 수 있습니다.
- `<instanceName>`: 고유한 관리되는 인스턴스 이름을 사용합니다.
- `<ResourceGroupName>`: 논리 서버에 대 한 리소스 그룹의 이름
- `<AzureADAccount>`: Azure AD 사용자 또는 그룹이 될 수 있습니다. 예를 들어 `DummyLogin`
- `<Location>`: 서버의 위치(예: `westus2` 또는 `centralus`)
- `<objectId>`: [Azure Portal](https://portal.azure.com), **Azure Active Directory** 리소스로 이동하여 찾을 수 있습니다. **사용자** 창에서 Azure AD 사용자를 검색하고 해당 **개체 ID** 를 찾습니다.
- `subnetId` 매개 변수는 `<ResourceGroupName>`, `Subscription ID`, `<VNetName>` 및 `<SubnetName>`으로 업데이트해야 합니다.


```rest
Import-Module Azure
Import-Module MSAL.PS

$tenantId = '<tenantId>'
$clientId = '1950a258-227b-4e31-a9cf-717495945fc2' # Static Microsoft client ID used for getting a token
$subscriptionId = '<subscriptionId>'
$uri = "urn:ietf:wg:oauth:2.0:oob" 
$instanceName = "<instanceName>"
$resourceGroupName = "<ResourceGroupName>"
$scopes ="https://management.core.windows.net/.default"

Login-AzAccount -tenantId $tenantId

# Login as an Azure AD user with permission to provision a managed instance

$result = Get-MsalToken -RedirectUri $uri -ClientId $clientId -TenantId $tenantId -Scopes $scopes

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

$body = '{
"name": "<instanceName>", "type": "Microsoft.Sql/managedInstances", "identity": { "type": "SystemAssigned"},"location": "<Location>", "sku": {"name": "GP_Gen5", "tier": "GeneralPurpose", "family":"Gen5","capacity": 8},
"properties": {"administrators":{ "login":"<AzureADAccount>", "sid":"<objectId>", "tenantId":"<tenantId>", "principalType":"User", "azureADOnlyAuthentication":true },
"subnetId": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/<VNetName>/subnets/<SubnetName>",
"licenseType": "LicenseIncluded", "vCores": 8, "storageSizeInGB": 2048, "collation": "SQL_Latin1_General_CP1_CI_AS", "proxyOverride": "Proxy", "timezoneId": "UTC", "privateEndpointConnections": [], "storageAccountType": "GRS", "zoneRedundant": false 
  }
}'

# To provision the instance, execute the `PUT` command

Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$instanceName/?api-version=2020-11-01-preview -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"

```

결과를 확인하려면 `GET` 명령을 실행합니다.

```rest
Invoke-RestMethod -Uri https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$instanceName/?api-version=2020-11-01-preview -Method GET -Headers $authHeader  | Format-List
```

# <a name="arm-template"></a>[ARM 템플릿](#tab/arm-template)

인스턴스에 대해 Azure AD 관리자를 설정하고 Azure AD 전용 인증을 사용하도록 설정하여 새로운 관리되는 인스턴스, 가상 네트워크 및 서브넷을 프로비저닝하려면 다음 템플릿을 사용합니다.

[Azure Portal의 사용자 지정 배포](https://portal.azure.com/#create/Microsoft.Template)를 사용하고 **편집기에서 사용자 고유의 템플릿을 빌드** 합니다. 다음으로, 예제에 붙여넣은 후에 구성을 **저장** 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "managedInstanceName": {
            "type": "String",
            "metadata": {
                "description": "Enter managed instance name."
            }
        },
        "aad_admin_name": {
            "type": "String",
            "metadata": {
                "description": "The name of the Azure AD admin for the SQL managed instance."
            }
        },
        "aad_admin_objectid": {
            "type": "String",
            "metadata": {
                "description": "The Object ID of the Azure AD admin."
            }
        },
        "aad_admin_tenantid": {
            "type": "String",
            "defaultValue": "[subscription().tenantId]",
            "metadata": {
                "description": "The Tenant ID of the Azure Active Directory"
            }
        },
        "aad_admin_type": {
            "defaultValue": "User",
            "allowedValues": [
                "User",
                "Group",
                "Application"
            ],
            "type": "String"
        },
        "aad_only_auth": {
            "defaultValue": true,
            "type": "Bool"
        },
        "location": {
            "defaultValue": "[resourceGroup().location]",
            "type": "String",
            "metadata": {
                "description": "Enter location. If you leave this field blank resource group location would be used."
            }
        },
        "virtualNetworkName": {
            "type": "String",
            "defaultValue": "SQLMI-VNET",
            "metadata": {
                "description": "Enter virtual network name. If you leave this field blank name will be created by the template."
            }
        },
        "addressPrefix": {
            "defaultValue": "10.0.0.0/16",
            "type": "String",
            "metadata": {
                "description": "Enter virtual network address prefix."
            }
        },
        "subnetName": {
            "type": "String",
            "defaultValue": "ManagedInstances",
            "metadata": {
                "description": "Enter subnet name. If you leave this field blank name will be created by the template."
            }
        },
        "subnetPrefix": {
            "defaultValue": "10.0.0.0/24",
            "type": "String",
            "metadata": {
                "description": "Enter subnet address prefix."
            }
        },
        "skuName": {
            "defaultValue": "GP_Gen5",
            "allowedValues": [
                "GP_Gen5",
                "BC_Gen5"
            ],
            "type": "String",
            "metadata": {
                "description": "Enter sku name."
            }
        },
        "vCores": {
            "defaultValue": 16,
            "allowedValues": [
                8,
                16,
                24,
                32,
                40,
                64,
                80
            ],
            "type": "Int",
            "metadata": {
                "description": "Enter number of vCores."
            }
        },
        "storageSizeInGB": {
            "defaultValue": 256,
            "minValue": 32,
            "maxValue": 8192,
            "type": "Int",
            "metadata": {
                "description": "Enter storage size."
            }
        },
        "licenseType": {
            "defaultValue": "LicenseIncluded",
            "allowedValues": [
                "BasePrice",
                "LicenseIncluded"
            ],
            "type": "String",
            "metadata": {
                "description": "Enter license type."
            }
        }
    },
    "variables": {
        "networkSecurityGroupName": "[concat('SQLMI-', parameters('managedInstanceName'), '-NSG')]",
        "routeTableName": "[concat('SQLMI-', parameters('managedInstanceName'), '-Route-Table')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2020-06-01",
            "name": "[variables('networkSecurityGroupName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "allow_tds_inbound",
                        "properties": {
                            "description": "Allow access to data",
                            "protocol": "Tcp",
                            "sourcePortRange": "*",
                            "destinationPortRange": "1433",
                            "sourceAddressPrefix": "VirtualNetwork",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 1000,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "allow_redirect_inbound",
                        "properties": {
                            "description": "Allow inbound redirect traffic to Managed Instance inside the virtual network",
                            "protocol": "Tcp",
                            "sourcePortRange": "*",
                            "destinationPortRange": "11000-11999",
                            "sourceAddressPrefix": "VirtualNetwork",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 1100,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "deny_all_inbound",
                        "properties": {
                            "description": "Deny all other inbound traffic",
                            "protocol": "*",
                            "sourcePortRange": "*",
                            "destinationPortRange": "*",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Deny",
                            "priority": 4096,
                            "direction": "Inbound"
                        }
                    },
                    {
                        "name": "deny_all_outbound",
                        "properties": {
                            "description": "Deny all other outbound traffic",
                            "protocol": "*",
                            "sourcePortRange": "*",
                            "destinationPortRange": "*",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Deny",
                            "priority": 4096,
                            "direction": "Outbound"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/routeTables",
            "apiVersion": "2020-06-01",
            "name": "[variables('routeTableName')]",
            "location": "[parameters('location')]",
            "properties": {
                "disableBgpRoutePropagation": false
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2020-06-01",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[variables('routeTableName')]",
                "[variables('networkSecurityGroupName')]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[parameters('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetPrefix')]",
                            "routeTable": {
                                "id": "[resourceId('Microsoft.Network/routeTables', variables('routeTableName'))]"
                            },
                            "networkSecurityGroup": {
                                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
                            },
                            "delegations": [
                                {
                                    "name": "miDelegation",
                                    "properties": {
                                        "serviceName": "Microsoft.Sql/managedInstances"
                                    }
                                }
                            ]
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Sql/managedInstances",
            "apiVersion": "2020-11-01-preview",
            "name": "[parameters('managedInstanceName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[parameters('virtualNetworkName')]"
            ],
            "sku": {
                "name": "[parameters('skuName')]"
            },
            "identity": {
                "type": "SystemAssigned"
            },
            "properties": {
                "subnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
                "storageSizeInGB": "[parameters('storageSizeInGB')]",
                "vCores": "[parameters('vCores')]",
                "licenseType": "[parameters('licenseType')]",
                "administrators": {
                    "login": "[parameters('aad_admin_name')]",
                    "sid": "[parameters('aad_admin_objectid')]",
                    "tenantId": "[parameters('aad_admin_tenantid')]",
                    "principalType": "[parameters('aad_admin_type')]",
                    "azureADOnlyAuthentication": "[parameters('aad_only_auth')]"
                }
            }
        }
    ]
}
```

---

### <a name="grant-directory-readers-permissions"></a>디렉터리 읽기 권한자 권한 부여

관리되는 인스턴스에 대한 배포가 완료되면 Managed Instance에 Azure Active Directory에 액세스하기 위한 **읽기** 권한이 있어야 합니다. 충분한 권한이 있는 사용자가 Azure Portal에서 표시된 메시지를 클릭하여 읽기 권한을 부여할 수 있습니다. 자세한 내용은 [Azure SQL용 Azure Active Directory의 Directory Readers 역할](authentication-aad-directory-readers-role.md)을 참조하세요.

:::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-portal-read-permissions.png" alt-text="필요한 읽기 권한을 보여 주는 Azure Portal의 Active Directory 관리 메뉴 스크린샷":::

## <a name="limitations"></a>제한 사항

- 서버 관리자 암호를 다시 설정하려면 Azure AD 전용 인증 기능을 사용하지 않도록 설정해야 합니다.
- Azure AD 전용 인증을 사용하지 않도록 설정한 경우 모든 API를 사용할 때 서버 관리자 및 암호를 사용하여 서버를 만들어야 합니다.

## <a name="next-steps"></a>다음 단계

- 이미 SQL 서버 또는 관리되는 인스턴스가 있으며 Azure AD 전용 인증을 사용하도록 설정하려면 [자습서: Azure SQL을 사용한 Azure Active Directory 전용 인증 사용](authentication-azure-ad-only-authentication-tutorial.md)을 참조하세요.
- Azure AD 전용 인증 기능에 대한 자세한 내용은 [Azure SQL을 사용한 Azure AD 전용 인증](authentication-azure-ad-only-authentication.md)을 참조하세요.
- azure AD 전용 인증을 사용 하 여 서버 만들기를 적용 하려는 경우 [azure를 사용 하는 Azure Active Directory 전용 인증에 대 한 Azure Policy](authentication-azure-ad-only-authentication-policy.md) 를 참조 하세요 SQL