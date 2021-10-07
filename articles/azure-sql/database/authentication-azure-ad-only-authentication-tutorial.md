---
title: Azure Active Directory 전용 인증 사용
description: 이 문서에서는 Azure SQL Database 및 Azure SQL Managed Instance에서 Azure AD(Azure Active Directory) 전용 인증 기능을 사용하도록 설정하는 방법을 안내합니다.
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/31/2021
ms.openlocfilehash: 76425e37c83ea5ac44653e20753e35606b89d370
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643235"
---
# <a name="tutorial-enable-azure-active-directory-only-authentication-with-azure-sql"></a>자습서: Azure SQL을 사용한 Azure Active Directory 전용 인증 사용

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 이 문서에서 설명하는 **Azure AD 전용 인증** 기능은 **퍼블릭 미리 보기** 로 제공됩니다. 

이 문서에서는 Azure SQL Database 및 Azure SQL Managed Instance에서 [Azure AD 전용 인증](authentication-azure-ad-only-authentication.md) 기능을 사용하도록 설정하는 방법을 안내합니다. Azure AD 전용 인증이 사용하도록 설정된 SQL Database 또는 Managed Instance를 프로비저닝하려는 경우 [Azure SQL에서 Azure AD 전용 인증이 사용하도록 설정된 서버 만들기](authentication-azure-ad-only-authentication-create-server.md)를 참조하세요.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> - Azure AD 전용 인증을 사용하도록 설정하는 역할 할당
> - Azure Portal, Azure CLI 또는 PowerShell을 통해 Azure AD 전용 인증을 사용하도록 설정
> - Azure AD 전용 인증이 사용하도록 설정되었는지 확인
> - Azure SQL에 대한 연결 테스트
> - Azure Portal, Azure CLI 또는 PowerShell을 통해 Azure AD 전용 인증을 사용하지 않도록 설정


## <a name="prerequisites"></a>사전 요구 사항

- Azure AD 인스턴스. 자세한 내용은 [Azure SQL에서 Azure AD 인증 구성 및 관리](authentication-aad-configure.md)를 참조하세요.
- 데이터베이스와 로그인 또는 사용자가 있는 SQL Database 또는 SQL Managed Instance. [빠른 시작: Azure SQL Database 단일 데이터베이스 만들기](single-database-create-quickstart.md)(Azure SQL Database를 아직 만들지 않은 경우) 또는 [빠른 시작: Azure SQL Managed Instance 만들기](../managed-instance/instance-create-quickstart.md)를 참조하세요.

## <a name="assign-role-to-enable-azure-ad-only-authentication"></a>Azure AD 전용 인증을 사용하도록 설정하는 역할 할당

Azure AD 전용 인증을 사용하거나 사용하지 않도록 설정하려면 이 자습서에서 해당 작업을 실행하는 Azure AD 사용자에게 선택한 기본 제공 역할이 필요합니다. 이 자습서에서는 [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할을 사용자에게 할당합니다.

Azure AD 계정에 역할을 할당하는 방법에 대한 자세한 내용은 [Azure Active Directory를 사용하여 사용자에게 관리자 및 관리자가 아닌 역할 할당](../../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)을 참조하세요.

Azure AD 전용 인증을 사용하거나 사용하지 않도록 설정하는 데 필요한 권한에 대한 자세한 내용은 [Azure AD 전용 인증 문서의 권한 섹션](authentication-azure-ad-only-authentication.md#permissions)을 참조하세요.

1. 예제에서는 `UserSqlSecurityManager@contoso.onmicrosoft.com` 사용자에게 **SQL 보안 관리자** 역할을 할당합니다. Azure AD 역할을 할당할 수 있는 권한 있는 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. SQL 서버 리소스로 이동한 다음, 메뉴에서 **액세스 제어(IAM)** 를 선택합니다. **추가** 단추를 선택한 다음, 드롭다운 메뉴에서 **역할 할당 추가** 를 선택합니다.

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-access-control.png" alt-text="Azure Portal의 액세스 제어 창":::

1. **역할 할당 추가** 창에서 **SQL 보안 관리자** 역할을 선택한 다음, Azure AD 전용 인증을 사용하거나 사용하지 않도록 설정할 수 있는 사용자를 선택합니다.

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-access-control-add-role.png" alt-text="Azure Portal의 역할 할당 추가 창":::

1. **저장** 을 클릭합니다.

## <a name="enable-azure-ad-only-authentication"></a>Azure AD 전용 인증 사용

# <a name="portal"></a>[포털](#tab/azure-portal)

## <a name="enable-in-sql-database-using-azure-portal"></a>Azure Portal을 통해 SQL Database에서 사용

Azure Portal에서 Azure AD 전용 인증을 사용하도록 설정하려면 아래 단계를 참조하세요.

1. [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 있는 사용자로 [Azure Portal](https://portal.azure.com/)로 이동합니다.
1. SQL 서버 리소스로 이동한 다음, **설정** 메뉴에서 **Azure Active Directory** 를 선택합니다.

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-portal.png" alt-text="Azure AD 전용 인증 사용 메뉴":::

1. **Azure Active Directory 관리자** 를 추가하지 않은 경우 이 설정을 먼저 지정해야 Azure AD 전용 인증을 사용하도록 설정할 수 있습니다.
1. **이 서버에서 Azure Active Directory 인증만 지원** 확인란을 선택합니다.
1. **Azure AD 인증만 사용** 팝업이 표시됩니다. **예** 를 클릭하여 기능을 사용하도록 설정하고 설정을 **저장** 합니다.

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

현재 포털에서 SQL Managed Instance에 대한 Azure AD 전용 인증을 관리할 수는 없습니다.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="enable-in-sql-database-using-azure-cli"></a>Azure CLI를 통해 SQL Database에서 사용

Azure CLI를 통해 Azure SQL Database에서 Azure AD 전용 인증을 사용하도록 설정하려면 아래 명령을 참조하세요. [최신 버전의 Azure CLI를 설치](/cli/azure/install-azure-cli-windows)합니다. Azure CLI 버전이 **2.14.2** 이상이어야 합니다. 명령에 대한 자세한 내용은 [az sql server ad-only-auth](/cli/azure/sql/server/ad-only-auth)를 참조하세요.

API를 사용하여 Azure AD 전용 인증을 관리하는 방법에 대한 자세한 내용은 [API를 사용하여 Azure AD 전용 인증 관리](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)를 참조하세요.

> [!NOTE]
> Azure AD 전용 인증을 사용하도록 설정하려면 먼저 서버의 Azure AD 관리자를 설정해야 합니다. 그러지 않으면 Azure CLI 명령이 실패합니다.
>
> Azure AD 전용 인증을 사용하도록 설정하기 위해 명령을 수행하는 사용자에게 필요한 권한 및 작업은 [Azure AD 전용 인증](authentication-azure-ad-only-authentication.md#permissions) 문서를 참조하세요.

1. [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 있는 계정을 사용하여 [Azure에 로그인](/cli/azure/authenticate-azure-cli)합니다.

   ```azurecli
   az login
   ```

1. `<myserver>`를 SQL 서버 이름으로, `<myresource>`를 SQL 서버가 포함된 Azure 리소스로 바꾸어 다음 명령을 실행합니다.

   ```azurecli
   az sql server ad-only-auth enable --resource-group <myresource> --name <myserver>
   ```

## <a name="enable-in-sql-managed-instance-using-azure-cli"></a>Azure CLI를 통해 SQL Managed Instance에서 사용

Azure CLI를 통해 Azure SQL Managed Instance에서 Azure AD 전용 인증을 사용하도록 설정하려면 아래 명령을 참조하세요. [최신 버전의 Azure CLI를 설치](/cli/azure/install-azure-cli-windows)합니다. 

1. [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 있는 계정을 사용하여 [Azure에 로그인](/cli/azure/authenticate-azure-cli)합니다.

   ```azurecli
   az login
   ```

1. `<myserver>`를 SQL 서버 이름으로, `<myresource>`를 SQL 서버가 포함된 Azure 리소스로 바꾸어 다음 명령을 실행합니다.

   ```azurecli
   az sql mi ad-only-auth enable --resource-group <myresource> --name <myserver>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="enable-in-sql-database-using-powershell"></a>PowerShell을 통해 SQL Database에서 사용

PowerShell을 통해 Azure SQL Database에서 Azure AD 전용 인증을 사용하도록 설정하려면 아래 명령을 참조하세요. 명령을 실행하려면 [Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0) 모듈 이상이 필요합니다. 명령에 대한 자세한 내용은 [Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlinstanceactivedirectoryonlyauthentication)을 참조하세요.

API를 사용하여 Azure AD 전용 인증을 관리하는 방법에 대한 자세한 내용은 [API를 사용하여 Azure AD 전용 인증 관리](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)를 참조하세요.

> [!NOTE]
> Azure AD 전용 인증을 사용하도록 설정하려면 먼저 서버의 Azure AD 관리자를 설정해야 합니다. 그러지 않으면 PowerShell 명령이 실패합니다.
>
> Azure AD 전용 인증을 사용하도록 설정하기 위해 명령을 수행하는 사용자에게 필요한 권한 및 작업은 [Azure AD 전용 인증](authentication-azure-ad-only-authentication.md#permissions) 문서를 참조하세요. 사용자에게 권한이 없으면 다음 오류가 표시됩니다.
>
> ```output
> Enable-AzSqlServerActiveDirectoryOnlyAuthentication : The client
> 'UserSqlServerContributor@contoso.onmicrosoft.com' with object id
> '<guid>' does not have authorization to perform
> action 'Microsoft.Sql/servers/azureADOnlyAuthentications/write' over scope
> '/subscriptions/<guid>...'
> ```

1. [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 있는 계정을 사용하여 [Azure에 로그인](/powershell/azure/authenticate-azureps)합니다.

   ```powershell
   Connect-AzAccount
   ```

1. `<myserver>`를 SQL 서버 이름으로, `<myresource>`를 SQL 서버가 포함된 Azure 리소스로 바꾸어 다음 명령을 실행합니다.

   ```powershell
   Enable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName <myserver>  -ResourceGroupName <myresource>
   ```

## <a name="enable-in-sql-managed-instance-using-powershell"></a>PowerShell을 통해 SQL Managed Instance에서 사용

PowerShell을 통해 Azure SQL Managed Instance에서 Azure AD 전용 인증을 사용하도록 설정하려면 아래 명령을 참조하세요. 명령을 실행하려면 [Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0) 모듈 이상이 필요합니다. 

API를 사용하여 Azure AD 전용 인증을 관리하는 방법에 대한 자세한 내용은 [API를 사용하여 Azure AD 전용 인증 관리](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)를 참조하세요.


1. [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 있는 계정을 사용하여 [Azure에 로그인](/powershell/azure/authenticate-azureps)합니다.

   ```powershell
   Connect-AzAccount
   ```

1. `<myinstance>`를 SQL Managed Instance 이름으로, `<myresource>`를 SQL 관리형 인스턴스가 포함된 Azure 리소스로 바꾸어 다음 명령을 실행합니다.

   ```powershell
   Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName <myinstance> -ResourceGroupName <myresource>
   ```

---

## <a name="check-the-azure-ad-only-authentication-status"></a>Azure AD 전용 인증 상태 확인

서버 또는 인스턴스에 대해 Azure AD 전용 인증이 사용하도록 설정되었는지 확인합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

[Azure Portal](https://portal.azure.com/)에서 **SQL 서버** 리소스로 이동합니다. **설정** 메뉴에서 **Azure Active Directory** 를 선택합니다. Azure AD 전용 인증에 대한 포털 지원은 Azure SQL Database에서만 사용할 수 있습니다.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

이러한 명령을 사용하여 Azure SQL Database 또는 SQL Managed Instance의 [논리 서버](logical-servers.md)에 대해 Azure AD 전용 인증이 사용하도록 설정되었는지 확인할 수 있습니다. [SQL Server 기여자](../../role-based-access-control/built-in-roles.md#sql-server-contributor) 및 [SQL Managed Instance 기여자](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) 역할의 멤버는 명령을 사용하여 Azure AD 전용 인증 상태를 확인할 수 있지만 기능을 사용하거나 사용하지 않도록 설정할 수는 없습니다.

## <a name="check-status-in-sql-database"></a>SQL Database에서 상태 확인

1. [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 있는 계정을 사용하여 [Azure에 로그인](/cli/azure/authenticate-azure-cli)합니다. API를 사용하여 Azure AD 전용 인증을 관리하는 방법에 대한 자세한 내용은 [API를 사용하여 Azure AD 전용 인증 관리](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)를 참조하세요.

   ```azurecli
   az login
   ```

1. `<myserver>`를 SQL 서버 이름으로, `<myresource>`를 SQL 서버가 포함된 Azure 리소스로 바꾸어 다음 명령을 실행합니다.

   ```azurecli
   az sql server ad-only-auth get --resource-group <myresource> --name <myserver>
   ```

1. 다음 출력이 표시됩니다.

   ```json
   {
    "azureAdOnlyAuthentication": true,
    "/subscriptions/<guid>/resourceGroups/mygroup/providers/Microsoft.Sql/servers/myserver/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/servers"
   }
   ```

## <a name="check-status-in-sql-managed-instance"></a>SQL Managed Instance에서 상태 확인

1. [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 있는 계정을 사용하여 [Azure에 로그인](/cli/azure/authenticate-azure-cli)합니다.

   ```azurecli
   az login
   ```

1. `<myserver>`를 SQL 서버 이름으로, `<myresource>`를 SQL 서버가 포함된 Azure 리소스로 바꾸어 다음 명령을 실행합니다.

   ```azurecli
   az sql mi ad-only-auth get --resource-group <myresource> --name <myserver>
   ```

1. 다음 출력이 표시됩니다.

   ```json
   {
    "azureAdOnlyAuthentication": true,
    "id": "/subscriptions/<guid>/resourceGroups/myresource/providers/Microsoft.Sql/managedInstances/myinstance/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/managedInstances"
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이러한 명령을 사용하여 Azure SQL Database 또는 SQL Managed Instance의 [논리 서버](logical-servers.md)에 대해 Azure AD 전용 인증이 사용하도록 설정되었는지 확인할 수 있습니다. [SQL Server 기여자](../../role-based-access-control/built-in-roles.md#sql-server-contributor) 및 [SQL Managed Instance 기여자](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) 역할의 멤버는 명령을 사용하여 Azure AD 전용 인증 상태를 확인할 수 있지만 기능을 사용하거나 사용하지 않도록 설정할 수는 없습니다.

기능을 사용하도록 설정하면 상태가 **True** 로 반환되고, 사용하지 않도록 설정하면 **False** 로 반환됩니다.

## <a name="check-status-in-sql-database"></a>SQL Database에서 상태 확인

1. [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 있는 계정을 사용하여 [Azure에 로그인](/powershell/azure/authenticate-azureps)합니다. API를 사용하여 Azure AD 전용 인증을 관리하는 방법에 대한 자세한 내용은 [API를 사용하여 Azure AD 전용 인증 관리](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)를 참조하세요.

   ```powershell
   Connect-AzAccount
   ```

1. `<myserver>`를 SQL 서버 이름으로, `<myresource>`를 SQL 서버가 포함된 Azure 리소스로 바꾸어 다음 명령을 실행합니다.

   ```powershell
   Get-AzSqlServerActiveDirectoryOnlyAuthentication  -ServerName <myserver> -ResourceGroupName <myresource>
   ```

## <a name="check-status-in-sql-managed-instance"></a>SQL Managed Instance에서 상태 확인

1. [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 있는 계정을 사용하여 [Azure에 로그인](/powershell/azure/authenticate-azureps)합니다.

   ```powershell
   Connect-AzAccount
   ```

1. `<myinstance>`를 SQL Managed Instance 이름으로, `<myresource>`를 SQL 관리형 인스턴스가 포함된 Azure 리소스로 바꾸어 다음 명령을 실행합니다.

   ```powershell
   Get-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName <myinstance> -ResourceGroupName <myresource>
   ```

---

## <a name="test-sql-authentication-with-connection-failure"></a>연결 실패로 SQL 인증 테스트

Azure AD 전용 인증을 사용하도록 설정한 후 [SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms)에서 [SQL Database 또는 Managed Instance에 대한 연결](connect-query-ssms.md)을 테스트합니다. 연결을 위해 SQL 인증을 사용합니다.

다음 출력과 비슷한 로그인 실패 메시지가 표시됩니다.

```output
Cannot connect to <myserver>.database.windows.net.
Additional information:
  Login failed for user 'username'. Reason: Azure Active Directory only authentication is enabled.
  Please contact your system administrator. (Microsoft SQL Server, Error: 18456)
```

## <a name="disable-azure-ad-only-authentication"></a>Azure AD 전용 인증 사용 안 함

Azure AD 전용 인증 기능을 사용하지 않도록 설정하면 Azure SQL에 대한 SQL 인증과 Azure AD 인증을 모두 허용하게 됩니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 있는 사용자로 [Azure Portal](https://portal.azure.com/)로 이동합니다.
1. SQL 서버 리소스로 이동한 다음, **설정** 메뉴에서 **Azure Active Directory** 를 선택합니다.
1. Azure AD 전용 인증 기능을 사용하지 않도록 설정하려면 **이 서버에서 Azure Active Directory 인증만 지원** 확인란의 선택을 취소하고 설정을 **저장** 합니다.

현재 포털에서 SQL Managed Instance에 대한 Azure AD 전용 인증을 관리할 수는 없습니다.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="disable-in-sql-database-using-azure-cli"></a>Azure CLI를 통해 SQL Database에서 사용 안 함

Azure CLI를 통해 Azure SQL Database에서 Azure AD 전용 인증을 사용하지 않도록 설정하려면 아래 명령을 참조하세요. 

1. [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 있는 계정을 사용하여 [Azure에 로그인](/cli/azure/authenticate-azure-cli)합니다.

   ```azurecli
   az login
   ```

1. `<myserver>`를 SQL 서버 이름으로, `<myresource>`를 SQL 서버가 포함된 Azure 리소스로 바꾸어 다음 명령을 실행합니다.

   ```azurecli
   az sql server ad-only-auth disable --resource-group <myresource> --name <myserver>
   ```

1. Azure AD 전용 인증을 사용하지 않도록 설정한 후 상태를 확인하면 다음 출력이 표시됩니다.

   ```json
   {
    "azureAdOnlyAuthentication": false,
    "/subscriptions/<guid>/resourceGroups/mygroup/providers/Microsoft.Sql/servers/myserver/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/servers"
   }
   ```

## <a name="disable-in-sql-managed-instance-using-azure-cli"></a>Azure CLI를 통해 SQL Managed Instance에서 사용 안 함

Azure CLI를 통해 Azure SQL Managed Instance에서 Azure AD 전용 인증을 사용하지 않도록 설정하려면 아래 명령을 참조하세요. 

1. [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 있는 계정을 사용하여 [Azure에 로그인](/cli/azure/authenticate-azure-cli)합니다.

   ```azurecli
   az login
   ```

1. `<myserver>`를 SQL 서버 이름으로, `<myresource>`를 SQL 서버가 포함된 Azure 리소스로 바꾸어 다음 명령을 실행합니다.

   ```azurecli
   az sql mi ad-only-auth disable --resource-group <myresource> --name <myserver>
   ```

1. Azure AD 전용 인증을 사용하지 않도록 설정한 후 상태를 확인하면 다음 출력이 표시됩니다.

   ```json
   {
    "azureAdOnlyAuthentication": false,
    "id": "/subscriptions/<guid>/resourceGroups/myresource/providers/Microsoft.Sql/managedInstances/myinstance/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/managedInstances"
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="disable-in-sql-database-using-powershell"></a>PowerShell을 통해 SQL Database에서 사용 안 함

PowerShell을 통해 Azure SQL Database에서 Azure AD 전용 인증을 사용하지 않도록 설정하려면 아래 명령을 참조하세요.

1. [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 있는 계정을 사용하여 [Azure에 로그인](/powershell/azure/authenticate-azureps)합니다.

   ```powershell
   Connect-AzAccount
   ```

1. `<myserver>`를 SQL 서버 이름으로, `<myresource>`를 SQL 서버가 포함된 Azure 리소스로 바꾸어 다음 명령을 실행합니다.

   ```powershell
   Disable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName <myserver>  -ResourceGroupName <myresource>
   ```

## <a name="disable-in-sql-managed-instance-using-powershell"></a>PowerShell을 통해 SQL Managed Instance에서 사용 안 함

PowerShell을 통해 Azure SQL Managed Instance에서 Azure AD 전용 인증을 사용하지 않도록 설정하려면 아래 명령을 참조하세요.

1. [SQL 보안 관리자](../../role-based-access-control/built-in-roles.md#sql-security-manager) 역할이 있는 계정을 사용하여 [Azure에 로그인](/powershell/azure/authenticate-azureps)합니다.

   ```powershell
   Connect-AzAccount
   ```

1. `<myinstance>`를 SQL Managed Instance 이름으로, `<myresource>`를 SQL 관리형 인스턴스가 포함된 Azure 리소스로 바꾸어 다음 명령을 실행합니다.

   ```powershell
   Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName <myinstance> -ResourceGroupName <myresource>
   ```

---

## <a name="test-connecting-to-azure-sql-again"></a>Azure SQL에 대한 연결 다시 테스트

Azure AD 전용 인증을 사용하지 않도록 설정한 후 SQL 인증 로그인을 사용하여 연결을 테스트합니다. 이제 서버 또는 인스턴스에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure SQL을 사용한 Azure AD 전용 인증](authentication-azure-ad-only-authentication.md)
- [Azure SQL에서 Azure AD 전용 인증이 사용하도록 설정된 서버 만들기](authentication-azure-ad-only-authentication-create-server.md)
- [Azure Policy를 사용하여 Azure SQL로 Azure Active Directory 전용 인증 적용](authentication-azure-ad-only-authentication-policy-how-to.md)
