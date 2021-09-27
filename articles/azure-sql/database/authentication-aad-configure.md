---
title: Azure Active Directory 인증 구성
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Azure AD를 구성한 후 Azure Active Directory 인증을 사용하여 SQL Database, SQL Managed Instance 및 Azure Synapse Analytics에 연결하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2, devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/11/2021
ms.openlocfilehash: 4bcfc687e1a91fd2ecc652ced2481963974e155f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643349"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>Azure SQL에서 Azure AD 인증 구성 및 관리

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

이 문서에서는 Azure AD(Azure Active Directory) 인스턴스를 만들고 채운 다음, [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) 및 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)에서 Azure AD를 사용하는 방법을 보여 줍니다. 개요는 [Azure Active Directory 인증](authentication-aad-overview.md)을 참조하세요.

## <a name="azure-ad-authentication-methods"></a>Azure AD 인증 방법

Azure AD 인증에서 지원하는 인증 방법은 다음과 같습니다.

- Azure AD 클라우드 전용 ID
- 다음을 지원하는 Azure AD 하이브리드 ID:
  - 원활한 SSO(Single Sign-On)와 결합된 두 가지 옵션이 있는 클라우드 인증
    - Azure AD 암호 해시 인증
    - Azure AD 통과 인증
  - 페더레이션 인증

Azure AD 인증 방법 및 선택하는 방법에 대한 자세한 내용은 [Azure Active Directory 하이브리드 ID 솔루션에 적합한 인증 방법 선택](../../active-directory/hybrid/choose-ad-authn.md)을 참조하세요.

Azure AD 하이브리드 ID, 설정 및 동기화에 대한 자세한 내용은 다음을 참조하세요.

- 암호 해시 인증 - [Azure AD Connect 동기화를 사용하여 암호 해시 동기화 구현](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- 통과 인증 - [Azure Active Directory 통과 인증](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- 페더레이션 인증 - [Azure에서 Active Directory Federation Services 배포](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) 및 [Azure AD Connect 및 페더레이션 ](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

## <a name="create-and-populate-an-azure-ad-instance"></a>Azure AD 인스턴스 만들기 및 채우기

Azure AD 인스턴스를 만들고 사용자 및 그룹으로 채웁니다. Azure AD는 초기 Azure AD 관리되는 도메인일 수 있습니다. Azure AD는 Azure AD와 페더레이션된 온-프레미스 Active Directory Domain Services일 수도 있습니다.

자세한 내용은 다음을 참조하세요.
- [Azure Active Directory와 온-프레미스 ID 통합](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Azure AD에 고유한 도메인 이름 추가](../../active-directory/fundamentals/add-custom-domain.md)
- [Microsoft Azure는 이제 Windows Server Active Directory를 통한 페더레이션 지원](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Azure Active Directory란?](../../active-directory/fundamentals/active-directory-whatis.md)
- [Windows PowerShell을 사용하여 Azure AD 관리](/powershell/module/azuread)
- [포트 및 프로토콜이 필요한 하이브리드 ID](../../active-directory/hybrid/reference-connect-ports.md)

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Azure Active Directory에 Azure 구독 연결 또는 추가

1. 해당 디렉터리를 데이터베이스를 호스트하는 Azure 구독에서 신뢰할 수 있는 디렉터리로 만들어 Azure Active Directory에 데이터베이스를 연결합니다. 자세한 내용은 [Azure Active Directory 테넌트에 Azure 구독 연결 또는 추가](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)를 참조하세요.

2. Azure Portal의 디렉터리 전환기를 사용하여 도메인과 연결된 구독으로 전환합니다.

   > [!IMPORTANT]
   > 모든 Azure 구독은 Azure AD 인스턴스와 트러스트 관계가 있습니다. 이는 Azure 구독이 사용자, 서비스, 디바이스를 인증하는 해당 디렉터리를 신뢰함을 의미합니다. 여러 구독에서 동일한 디렉터리를 신뢰할 수 있지만 구독은 하나의 디렉터리만 신뢰합니다. 구독이 디렉터리와 갖는 이 트러스트 관계는 구독이 Azure의 다른 모든 리소스(웹 사이트, 데이터베이스 등)와 갖는 관계와 다르며 구독의 하위 리소스와 더 유사합니다. 구독이 만료되면 구독과 연결된 다른 리소스에 대한 액세스도 중지됩니다. 하지만 디렉터리는 Azure에 남아 있으며 해당 디렉터리와 다른 구독을 연결하여 디렉터리 사용자를 계속 관리할 수 있습니다. 리소스에 대한 자세한 내용은 [Azure의 리소스 액세스 이해](../../active-directory/external-identities/add-users-administrator.md)를 참조하세요. 이러한 신뢰 관계에 대한 자세한 내용은 [Azure Active Directory에 Azure 구독을 연결하거나 추가하는 방법](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)을 참조하세요.

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>SQL Database의 서버를 사용하는 Azure AD 관리자

Azure의 각 [서버](logical-servers.md)(SQL Database 또는 Azure Synapse를 호스팅함)는 전체 서버의 관리자인 단일 서버 관리자 계정으로 시작됩니다. 두 번째 관리자 계정을 Azure AD 계정으로 만듭니다. 이 보안 주체는 서버의 마스터 데이터베이스에서 포함된 데이터베이스 사용자로 만들어집니다. 관리자 계정은 모든 사용자 데이터베이스에서 **db_owner** 역할의 멤버이며, 각 사용자 데이터베이스를 **dbo** 사용자로 입력합니다. 관리자 계정에 대한 자세한 내용은 [데이터베이스 및 로그인 관리](logins-create-manage.md)를 참조하세요.

Azure Active Directory와 함께 지역에서 복제를 사용할 때 Azure Active Directory 관리자는 주 서버와 보조 서버 모두에 대해 구성되어야 합니다. Azure Active Directory 관리자가 서버에 없는 경우 다음 Azure Active Directory 로그인 및 사용자는 서버에 `Cannot connect` 오류를 받습니다.

> [!NOTE]
> Azure AD 계정(서버 관리자 계정 포함)을 기반으로 하지 않는 사용자는 Azure AD를 사용하여 제안된 데이터베이스 사용자의 유효성을 검사할 수 있는 권한이 없으므로 Azure AD 기반 사용자를 만들 수 없습니다.

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>Azure AD 관리자 프로비전(SQL Managed Instance)

> [!IMPORTANT]
> Azure SQL Managed Instance를 프로비전하는 경우에만 다음 단계를 수행합니다. 이 작업은 Azure AD에서 전역 관리자 또는 권한 있는 역할 관리자만 실행할 수 있습니다.
>
> **공개 미리 보기** 에서 **디렉터리 읽기 권한자** 역할을 Azure AD의 그룹에 할당할 수 있습니다. 그런 다음, 그룹 소유자는 관리되는 인스턴스 ID를 이 그룹의 멤버로 추가할 수 있습니다. 그러면 SQL Managed Instance에 대한 Azure AD 관리자를 프로비전할 수 있습니다. 이 기능에 대한 자세한 내용은 [Azure SQL용 Azure Active Directory의 Directory Readers 역할](authentication-aad-directory-readers-role.md)을 참조하세요.

SQL Managed Instance에는 보안 그룹 멤버 자격을 통한 사용자 인증 또는 새 사용자 만들기와 같은 작업을 성공적으로 수행하기 위해 Azure AD를 읽을 수 있는 권한이 필요합니다. 이를 위해 Azure AD를 읽을 수 있는 SQL Managed Instance 권한을 부여해야 합니다. 이 작업은 Azure Portal 또는 PowerShell을 사용하여 수행할 수 있습니다.

### <a name="azure-portal"></a>Azure Portal

Azure Portal을 사용하여 SQL Managed Instance Azure AD 읽기 권한을 부여하려면 Azure AD에서 전역 관리자 권한으로 로그인하고 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 오른쪽 위 모서리에 있는 가능한 Active Directory 드롭다운 목록에서 연결을 선택합니다.

2. 정확한 Active Directory를 기본 Azure AD로 선택합니다.

   이 단계에서는 Active Directory와 연결된 구독을 SQL Managed Instance와 연결하여 동일한 구독이 Azure AD 인스턴스 및 SQL Managed Instance 둘 모두에 사용되도록 합니다.

3. Azure AD 통합에 사용하려는 SQL Managed Instance로 이동합니다.

   ![선택한 SQL Managed Instance에 대해 열려 있는 Active Directory 관리자 페이지를 보여 주는 Azure Portal의 스크린샷](./media/authentication-aad-configure/aad.png)

4. Active Directory 관리자 페이지 위쪽에서 배너를 선택하고 현재 사용자에게 권한을 부여합니다.

    ![Active Directory에 액세스하기 위해 SQL Managed Instance에 권한을 부여하는 대화 상자의 스크린샷. 권한 부여 단추가 선택되어 있습니다.](./media/authentication-aad-configure/grant-permissions.png)

5. 작업이 성공하면 오른쪽 위 모서리에 다음과 같은 알림이 표시됩니다.

    ![관리되는 인스턴스에 대한 Active Directory 읽기 권한이 성공적으로 업데이트되었음을 확인하는 알림의 스크린샷](./media/authentication-aad-configure/success.png)

6. 이제 SQL Managed Instance에 대한 Azure AD 관리자를 선택할 수 있습니다. 이에 대해 Active Directory 관리자 페이지에서 **관리자 설정** 명령을 선택합니다.

    ![선택한 SQL Managed Instance에 대한 Active Directory 관리자 페이지에서 강조 표시된 관리자 설정 명령을 보여 주는 스크린샷](./media/authentication-aad-configure/set-admin.png)

7. Azure AD 관리자 페이지에서 사용자를 검색하고, 관리자가 될 사용자 또는 그룹을 선택한 다음, **선택** 을 선택합니다.

   Active Directory 관리 페이지에 해당 Active Directory에 모든 멤버와 그룹이 표시됩니다. 회색으로 표시된 사용자나 그룹은 Azure AD 관리자로 지원되지 않기 때문에 선택할 수 없습니다. [Azure AD 기능 및 제한 사항](authentication-aad-overview.md#azure-ad-features-and-limitations) 에서 지원되는 관리자 목록을 참조하세요. Azure RBAC(Azure 역할 기반 액세스 제어)는 Azure Portal에만 적용되며 SQL Database, SQL Managed Instance 또는 Azure Synapse로 전파되지 않습니다.

    ![Azure Active Directory 관리자 추가](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. Active Directory 관리자 페이지의 위쪽에서 **저장** 을 선택합니다.

    ![위쪽 행의 관리자 설정 및 관리자 제거 단추 옆에 저장 단추가 있는 Active Directory 관리자 페이지의 스크린샷](./media/authentication-aad-configure/save.png)

    관리자 변경 과정에는 몇 분 정도 소요될 수 있습니다. 그런 다음 새 관리자가 Active Directory 관리자 상자에 표시됩니다.

SQL Managed Instance에 대한 Azure AD 관리자가 프로비전되면 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) 구문을 사용하여 Azure AD 서버 보안 주체(로그인) 만들기를 시작할 수 있습니다. 자세한 내용은 [SQL Managed Instance 개요](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration)를 참조하세요.

> [!TIP]
> 나중에 관리자를 제거하려면 Active Directory 관리자 페이지 위쪽에서 **관리자 제거** 를 선택한 다음, **저장** 을 선택합니다.

### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 SQL Managed Instance Azure AD 읽기 권한을 부여하려면 다음 스크립트를 실행합니다.

```powershell
# Gives Azure Active Directory read permission to a Service Principal representing the SQL Managed Instance.
# Can be executed only by a "Global Administrator" or "Privileged Role Administrator" type of user.

$aadTenant = "<YourTenantId>" # Enter your tenant ID
$managedInstanceName = "MyManagedInstance"

# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}

# Get service principal for your SQL Managed Instance
$roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
    exit
}
if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}

# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
    Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

    #Write-Output "Dumping service principal '$($managedInstanceName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
}
else {
    Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
}
```

### <a name="powershell-for-sql-managed-instance"></a>SQL Managed Instance에 대한 PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell cmdlet을 실행하려면 Azure powershell을 설치하고 실행해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/)을 참조하세요.

> [!IMPORTANT]
> PowerShell Azure RM(Resource Manager) 모듈은 Azure SQL Managed Instance에서 계속 지원되지만 향후의 모든 개발은 Az.Sql 모듈을 위한 것입니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 계속 수신할 예정입니다.  Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 호환성에 대한 자세한 내용은 [새로운 Azure PowerShell Az 모듈 소개](/powershell/azure/new-azureps-module-az)를 참조하세요.

Azure AD 관리자를 프로비전하려면 다음 Azure PowerShell 명령을 실행합니다.

- 연결 AzAccount
- Select-AzSubscription

SQL Managed Instance에 대한 Azure AD 관리자를 프로비전하고 관리하는 데 사용되는 cmdlet이 다음 표에 나와 있습니다.

| Cmdlet 이름 | 설명 |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |현재 구독의 SQL Managed Instance에 대한 Azure AD 관리자를 프로비전합니다. (현재 구독에 있어야 함)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |현재 구독의 SQL Managed Instance에 대한 Azure AD 관리자를 제거합니다. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |현재 구독의 SQL Managed Instance에 대한 Azure AD 관리자 정보를 반환합니다.|

다음 명령은 ResourceGroup01이라는 리소스 그룹과 연결된 ManagedInstance01이라는 SQL Managed Instance에 대한 Azure AD 관리자 정보를 가져옵니다.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

다음 명령은 ManagedInstance01이라는 SQL Managed Instance에 대해 DBA라는 Azure AD 관리자 그룹을 프로비전합니다. 이 서버는 ResourceGroup01 리소스 그룹과 연결됩니다.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

다음 명령은 ResourceGroup01 리소스 그룹과 연결된 ManagedInstanceName01이라는 SQL Managed Instance에 대한 Azure AD 관리자를 제거합니다.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 CLI 명령을 호출하여 SQL Managed Instance에 대한 Azure AD 관리자를 프로비전할 수도 있습니다.

| 명령 | Description |
| --- | --- |
|[az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_create) | SQL Managed Instance에 대한 Azure Active Directory 관리자를 프로비전합니다(현재 구독에 있어야 함). |
|[az sql mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_delete) | SQL Managed Instance에 대한 Azure Active Directory 관리자를 제거합니다. |
|[az sql mi ad-admin list](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_list) | 현재 SQL Managed Instance에 대해 구성된 Azure Active Directory 관리자에 대한 정보를 반환합니다. |
|[az sql mi ad-admin update](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_update) | SQL Managed Instance에 대한 Active Directory 관리자를 업데이트합니다. |

CLI 명령에 대한 자세한 내용은 [az sql mi](/cli/azure/sql/mi)를 참조하세요.

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>Azure AD 관리자 프로비전(SQL Database)

> [!IMPORTANT]
> SQL Database 또는 Azure Synapse용 [서버](logical-servers.md)를 프로비전하는 경우에만 다음 단계를 수행합니다.

다음 두 절차에서는 Azure Portal 및 PowerShell을 사용하여 서버에 대한 Azure Active Directory 관리자를 프로비전하는 방법을 보여 줍니다.

### <a name="azure-portal"></a>Azure Portal

1. [Azure Portal](https://portal.azure.com/)의 상단 오른쪽 끝에서 해당 연결을 선택하여 가능한 Active Directory 목록을 드롭다운합니다. 정확한 Active Directory를 기본 Azure AD로 선택합니다. 이 단계에서는 구독에 연결된 Active Directory를 서버와 연결하여 동일한 구독이 두 Azure AD 및 서버 모두에 사용되도록 합니다.

2. **SQL 서버** 를 검색하여 선택합니다.

    ![SQL 서버를 검색하여 선택](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > 이 페이지에서 **SQL Server** 를 선택하기 전에 이름 옆에 있는 **별모양** 을 선택하면 범주를 *즐겨 찾기에 추가* 하고 왼쪽 탐색 모음에 **SQL Server** 를 추가할 수 있습니다.

3. **SQL Server** 페이지에서 **Active Directory 관리자** 를 선택합니다.

4. **Active Directory 관리자** 페이지에서 **관리자 설정** 을 선택합니다.

    ![SQL 서버에서 Active Directory 관리자 설정](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. **관리자 추가** 페이지에서 사용자를 검색하고 관리자가 될 사용자 또는 그룹을 선택한 다음, **선택** 을 선택합니다. Active Directory 관리자 페이지에는 Active Directory의 모든 멤버와 그룹이 표시됩니다. 회색으로 표시된 사용자나 그룹은 Azure AD 관리자로 지원되지 않기 때문에 선택할 수 없습니다. ([SQL Database 또는 Azure Synapse를 통한 인증에 Azure Active Directory 인증 사용](authentication-aad-overview.md)의 **Azure AD 기능 및 제한 사항** 섹션에서 지원되는 관리자 목록을 참조하세요.) Azure RBAC(Azure 역할 기반 액세스 제어)는 포털에만 적용되며 SQL Server로 전파되지 않습니다.

    ![Azure Active Directory 관리자 선택](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. **Active Directory 관리자** 페이지의 위쪽에서 **저장** 을 선택합니다.

    ![관리자 저장](./media/authentication-aad-configure/save-admin.png)

관리자 변경 과정에는 몇 분 정도 소요될 수 있습니다. 그런 다음 새 관리자가 **Active Directory 관리자** 상자에 표시됩니다.

   > [!NOTE]
   > Azure AD 관리자를 설정하는 경우 새 관리자 이름(사용자 또는 그룹)은 이미 서버 인증 사용자로 가상 master 데이터베이스에 있을 수 없습니다. 새 관리자 이름이 있으면 Azure AD 관리자 설정은 실패하며, 생성 작업을 롤백하고 해당 관리자(이름)이 이미 존재한다는 것을 나타냅니다. 이러한 서버 인증 사용자는 Azure AD에 속하지 않으므로 Azure AD 인증을 사용하여 서버에 연결하려는 작업은 모두 실패합니다.

나중에 관리자를 제거하려면 **Active Directory 관리자** 페이지 위쪽에서 **관리자 제거** 를 선택한 다음, **저장** 을 선택합니다.

### <a name="powershell-for-sql-database-and-azure-synapse"></a>SQL Database 및 Azure Synapse에 대한 PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell cmdlet을 실행하려면 Azure powershell을 설치하고 실행해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/)을 참조하세요. Azure AD 관리자를 프로비전하려면 다음 Azure PowerShell 명령을 실행합니다.

- 연결 AzAccount
- Select-AzSubscription

SQL Database 및 Azure Synapse에 대한 Azure AD 관리자를 프로비전하고 관리하는 데 사용되는 cmdlet은 다음과 같습니다.

| Cmdlet 이름 | 설명 |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |SQL Database 또는 Azure Synapse를 호스팅하는 서버에 대한 Azure Active Directory 관리자를 프로비전합니다. (현재 구독에 있어야 함) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |SQL Database 또는 Azure Synapse를 호스팅하는 서버에 대한 Azure Active Directory 관리자를 제거합니다.|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |SQL Database 또는 Azure Synapse를 호스팅하는 서버에 대해 현재 구성된 Azure Active Directory 관리자에 대한 정보를 반환합니다. |

get-help PowerShell 명령을 이러한 각 명령에 대한 자세한 정보를 확인합니다. 예들 들어 `get-help Set-AzSqlServerActiveDirectoryAdministrator`입니다.

다음 스크립트는 리소스 그룹 **Group-23** 에서 **demo_server** 서버에 대해 이름이 **DBA_Group**(개체 ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`)인 Azure AD 관리자 그룹을 프로비전합니다.

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** 입력 매개 변수에는 Azure AD 표시 이름이나 사용자 계정 이름이 허용됩니다. 예를 들어 ``DisplayName="John Smith"`` 및 ``DisplayName="johns@contoso.com"``를 지정합니다. Azure AD 그룹에는 Azure AD 표시 이름만 지원됩니다.

> [!NOTE]
> Azure PowerShell 명령 `Set-AzSqlServerActiveDirectoryAdministrator`는 지원되지 않는 사용자에 대한 Azure AD 관리자 프로비전을 차단하지 않습니다. 지원되지 않는 사용자를 프로비전할 수는 있지만 데이터베이스에 연결할 수는 없습니다.

다음 예제에서는 **ObjectID** 옵션을 사용합니다.

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> **DisplayName** 이 고유하지 않을 경우 Azure AD **ObjectID** 가 필수입니다. **ObjectID** 및 **DisplayName** 값을 검색하려면 Azure 클래식 포털의 Active Directory 섹션을 사용하고 사용자 또는 그룹의 속성을 확인합니다.

다음 예제에서는 서버에 대한 현재 Azure AD 관리자 정보를 반환합니다.

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

다음 예제에서는 Azure AD 관리자를 제거합니다.

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 CLI 명령을 호출하여 Azure AD 관리자를 프로비전할 수 있습니다.

| 명령 | Description |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) | SQL Database 또는 Azure Synapse를 호스팅하는 서버에 대한 Azure Active Directory 관리자를 프로비전합니다. (현재 구독에 있어야 함) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) | SQL Database 또는 Azure Synapse를 호스팅하는 서버에 대한 Azure Active Directory 관리자를 제거합니다. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) | SQL Database 또는 Azure Synapse를 호스팅하는 서버에 대해 현재 구성된 Azure Active Directory 관리자에 대한 정보를 반환합니다. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) | SQL Database 또는 Azure Synapse를 호스팅하는 서버에 대한 Active Directory 관리자를 업데이트합니다. |

CLI 명령에 대한 자세한 내용은 [az sql server](/cli/azure/sql/server)를 참조하세요.

* * *

> [!NOTE]
> REST API를 사용하여 Azure Active Directory 관리자를 프로비전할 수도 있습니다. 자세한 내용은 [서비스 관리 REST API 참조 및 Azure SQL Database에 대한 작업](/rest/api/sql/)을 참조하세요.

## <a name="set-or-unset-the-azure-ad-admin-using-service-principals"></a>서비스 주체를 사용하여 Azure AD 관리자 설정 또는 설정 해제

서비스 주체가 Azure SQL에 대해 Azure AD 관리자를 설정하거나 설정 해제하게 하려면 추가 API 권한이 필요합니다. [Directory.Read.All](/graph/permissions-reference#application-permissions-18) 애플리케이션 API 권한을 Azure AD의 애플리케이션에 추가해야 합니다.

> [!NOTE]
> Azure AD 서비스 주체로 Azure Portal을 사용할 수 없으므로 Azure AD 관리자 설정에 관한 이 섹션은 PowerShell 또는 CLI 명령을 사용하는 경우에만 적용됩니다.

:::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-directory-reader-all-permissions.png" alt-text="Azure AD의 Directory.Reader.All 권한":::

또한 서비스 주체에는 SQL Database에 대한 [**SQL Server Contributor**](../../role-based-access-control/built-in-roles.md#sql-server-contributor) 역할 또는 SQL Managed Instance에 대한 [**SQL Managed Instance Contributor**](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) 역할이 필요합니다.

자세한 내용은 [서비스 주체(Azure AD 애플리케이션)](authentication-aad-service-principal.md)를 참조하세요.

## <a name="configure-your-client-computers"></a>클라이언트 컴퓨터 구성

애플리케이션 또는 사용자가 Azure AD ID를 사용하여 SQL Database 또는 Azure Synapse에 연결하는 모든 클라이언트 컴퓨터에서 다음 소프트웨어를 설치해야 합니다.

- [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers)에서 .NET Framework 4.6 이상
- SQL Server용 Azure Active Directory 인증 라이브러리(*ADAL.DLL*) *ADAL.DLL* 라이브러리가 포함된 최신 SSMS, ODBC 및 OLE DB 드라이버를 설치하기 위한 다운로드 링크는 다음과 같습니다.
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [ODBC Driver 17 for SQL Server](/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)
  - [SQL Server용 OLE DB 드라이버 18](/sql/connect/oledb/download-oledb-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)

다음을 통해 이러한 요구 사항을 충족할 수 있습니다.

- 최신 버전의 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 또는 [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)를 설치하면 .NET Framework 4.6 요구 사항이 충족됩니다.
  - SSMS는 x86 버전의 *ADAL.DLL* 을 설치합니다.
  - SSDT는 amd64 버전의 *ADAL.DLL* 을 설치합니다.
  - [Visual Studio 다운로드](https://www.visualstudio.com/downloads/download-visual-studio-vs)의 최신 Visual Studio는 .NET Framework 4.6 요구 사항을 충족하지만, 필요한 amd64 버전의 *ADAL.DLL* 을 설치하지 않습니다.

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>Azure AD ID에 매핑되어 있는 포함된 사용자 만들기

SQL Managed Instance는 Azure AD 서버 보안 주체(로그인)를 지원하므로 포함된 데이터베이스 사용자를 사용할 필요가 없습니다. Azure AD 서버 보안 주체(로그인)를 사용하면 Azure AD 사용자, 그룹 또는 애플리케이션에서 로그인을 만들 수 있습니다. 즉, 포함된 데이터베이스 사용자가 아닌 Azure AD 서버 로그인을 사용하여 SQL Managed Instance로 인증할 수 있습니다. 자세한 내용은 [SQL Managed Instance 개요](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration)를 참조하세요. Azure AD 서버 보안 주체(로그인)를 만드는 구문은 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)을 참조하세요.

그러나 SQL Database 및 Azure Synapse에서 Azure Active Directory 인증을 사용하려면 Azure AD ID를 기반으로 하여 포함된 데이터베이스 사용자를 사용해야 합니다. 포함된 데이터베이스 사용자는 master 데이터베이스에 로그인할 수 없으며, 데이터베이스와 연결된 Azure AD의 ID에 매핑됩니다. Azure AD ID는 개별 사용자 계정 또는 그룹일 수 있습니다. 포함된 데이터베이스 사용자에 대한 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](/sql/relational-databases/security/contained-database-users-making-your-database-portable)를 참조하세요.

> [!NOTE]
> 데이터베이스 사용자(관리자 예외)는 Azure Portal을 사용하여 만들 수 없습니다. Azure 역할은 SQL Database, SQL Managed Instance 또는 Azure Synapse의 데이터베이스로 전파되지 않습니다. Azure 역할은 Azure 리소스를 관리하는 데 사용되며, 데이터베이스 권한에는 적용되지 않습니다. 예를 들어 **SQL Server 기여자** 역할은 SQL Database, SQL Managed Instance 또는 Azure Synapse의 데이터베이스에 연결할 수 있는 액세스 권한을 부여하지 않습니다. TRANSACT-SQL 문을 사용하여 데이터베이스에 직접 액세스 권한을 부여해야 합니다.

> [!WARNING]
> `CREATE LOGIN` 및 `CREATE USER` T-SQL 문에 사용자 이름으로 포함된 콜론(`:`) 또는 앰퍼샌드(`&`)와 같은 특수 문자는 지원되지 않습니다.

> [!IMPORTANT]
> 2,048개 이상의 Azure AD 보안 그룹의 멤버인 Azure AD 사용자와 서비스 주체(Azure AD 애플리케이션)는 SQL Database, Managed Instance, Azure Synapse의 데이터베이스에 로그인할 수 없습니다.


Azure AD 기반의 포함된 데이터베이스 사용자(데이터베이스를 소유한 서버 관리자 아님)를 만들려면 **ALTER ANY USER** 이상의 권한이 있는 사용자인 Azure AD ID를 통해 데이터베이스에 연결합니다. 그런 다음 아래 TRANSACT-SQL 구문을 사용합니다.

```sql
CREATE USER [<Azure_AD_principal_name>] FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* 은 Azure AD 사용자의 사용자 계정 이름이거나 Azure AD 그룹의 표시 이름일 수 있습니다.

**예:** Azure AD 페더레이션 또는 관리 도메인 사용자를 나타내는 포함된 데이터베이스 사용자를 만드는 방법

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Azure AD 또는 페더레이션 도메인 그룹을 나타내는 포함된 데이터베이스 사용자를 만들려면 보안 그룹의 표시 이름을 제공하십시오.

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Azure AD 토큰을 사용하여 연결할 애플리케이션을 나타내는 포함된 데이터베이스 사용자를 만들려면 다음을 실행합니다.

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> 이 명령을 사용하려면 로그인한 사용자를 대신하여 SQL에서 Azure AD("외부 공급자")에 액세스해야 합니다. 경우에 따라 Azure AD에서 예외를 SQL에 반환하는 상황이 발생할 수 있습니다. 이러한 경우 사용자에게 Azure AD 관련 오류 메시지가 포함된 33134 SQL 오류가 표시됩니다. 대부분의 경우 액세스가 거부되었거나, 사용자가 리소스에 액세스하려면 MFA에 등록해야 하거나, 사전 인증을 통해 자사 애플리케이션 간의 액세스를 처리해야 한다는 오류가 표시됩니다. 처음 두 경우에서 문제는 일반적으로 사용자의 Azure AD 테넌트에 설정된 조건부 액세스 정책으로 인해 발생합니다. 이러한 정책은 사용자가 외부 공급자에 액세스하지 못하도록 합니다. '00000002-0000-0000-c000-000000000000'(Azure AD Graph API의 애플리케이션 ID) 애플리케이션에 대한 액세스를 허용하도록 조건부 액세스 정책을 업데이트하면 문제가 해결됩니다. 사전 인증을 통해 자사 애플리케이션 간의 액세스를 처리해야 한다는 오류가 표시되는 경우 사용자가 서비스 주체로 로그인했기 때문에 문제가 발생합니다. 대신 사용자가 실행하면 명령이 성공합니다.

> [!TIP]
> Azure 구독과 연결된 Azure Active Directory 이외의 Azure Active Directory에서 사용자를 직접 만들 수 없습니다. 그러나 연결된 Active Directory에서 가져온 사용자(외부 사용자로 알려짐)인 다른 Active Directory의 멤버는 테넌트 Active Directory의 Active Directory 그룹에 추가할 수 있습니다. 해당 AD 그룹에 대해 포함된 데이터베이스 사용자를 만들면 외부 Active Directory의 사용자는 SQL Database에 액세스할 수 있습니다.

Azure Active Directory 기반의 포함된 데이터베이스 사용자 만들기와 관련한 자세한 내용은 [CREATE USER(Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)를 참조하세요.

> [!NOTE]
> 서버에 대한 Azure Active Directory 관리자를 제거하면 Azure AD 인증 사용자가 서버에 연결할 수 없습니다. 필요한 경우 사용할 수 없는 Azure AD 사용자는 SQL Database 관리자가 수동으로 삭제할 수 있습니다.

> [!NOTE]
> **연결 시간 초과 만료됨** 을 수신하면 연결 문자열의 `TransparentNetworkIPResolution` 매개 변수를 false로 설정하지 않아도 됩니다. 자세한 내용은 [.NET Framework 4.6.1의 연결 시간 초과 문제 – TransparentNetworkIPResolution](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution)을 참조하세요.

데이터베이스 사용자를 만들 때 해당 사용자는 **연결** 권한을 부여 받으며 **공용** 역할의 멤버로서 해당 데이터베이스에 연결할 수 있습니다. 처음에 이 사용자에게 제공되는 권한은 **공용** 역할에 부여된 권한이거나 사용자가 속해 있는 Azure AD 그룹에 부여된 권한뿐입니다. Azure AD 기반의 포함된 데이터베이스 사용자를 프로비전한 후에는 다른 사용자 유형에 권한을 부여하는 것과 같은 방식으로 이 사용자에게 추가 권한을 부여할 수 있습니다. 일반적으로 데이터베이스 역할에 권한을 부여하고 역할에 사용자를 추가합니다. 자세한 내용은 [데이터베이스 엔진의 권한 기초](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)를 참조하세요. 특수 SQL Database 역할에 대한 자세한 내용은 [Azure SQL Database에서 데이터베이스 및 로그인 관리](logins-create-manage.md)를 참조하세요.
관리되는 도메인에 외부 사용자로 가져온 페더레이션된 도메인 사용자 계정은 관리되는 도메인 ID를 사용해야 합니다.

> [!NOTE]
> Azure AD 사용자는 데이터베이스 메타데이터에서 E 형식(EXTERNAL_USER) 및 그룹의 경우 X 형식(EXTERNAL_GROUPS)으로 표시됩니다. 자세한 내용은 [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql)를 참조하세요.

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>SSMS 또는 SSDT를 사용하여 데이터베이스에 연결  

Azure AD 관리자가 제대로 설정되었는지 확인하려면 Azure AD 관리자 계정을 사용하여 **master** 데이터베이스에 연결합니다.
Azure AD 기반의 포함된 데이터베이스 사용자(데이터베이스를 소유한 서버 관리자 아님)를 프로비전하려면 해당 데이터베이스에 대한 액세스가 있는 Azure AD ID로 데이터베이스에 연결합니다.

> [!IMPORTANT]
> Azure Active Directory 인증에 대한 지원은 2016부터 [SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) 및 2015부터 [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt)에서 사용 가능합니다. SSMS의 2016년 8월 릴리스에는 관리자가 전화 통화, 문자 메시지, PIN이 있는 스마트 카드 또는 모바일 앱 알림을 사용하여 Multi-Factor Authentication을 요구할 수 있도록 하는 Active Directory 유니버설 인증도 포함되어 있습니다.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>SSMS 또는 SSDT를 사용하여 연결하는 데 Azure AD ID 사용

다음 절차에서는 SQL Server Management Studio 또는 SQL Server Database Tools를 사용하여 SQL Database를 Azure AD ID에 연결하는 방법을 보여 줍니다.

### <a name="active-directory-integrated-authentication"></a>Active Directory 통합 인증

페더레이션된 도메인의 Azure Active Directory 자격 증명을 사용하여 Windows에 로그인하거나 통과 및 암호 해시 인증을 위한 원활한 Single Sign-On에 대해 구성된 관리되는 도메인에 로그인한 경우 이 방법을 사용합니다. 자세한 내용은 [Azure Active Directory Seamless Single Sign-On](../../active-directory/hybrid/how-to-connect-sso.md)을 참조하세요.

1. Management Studio 또는 Data Tools를 시작하고, **서버에 연결**(또는 **데이터베이스에 엔진 연결**) 대화 상자의 **인증** 상자에서 **Azure Active Directory - 통합** 을 선택합니다. 연결에 대한 기존 자격 증명이 있으므로 암호 입력이 필요하지 않습니다.

   ![AD 통합 인증 선택][11]

2. **옵션** 단추를 선택하고 **연결 속성** 페이지의 **데이터베이스에 연결** 상자에서 연결하려는 사용자 데이터베이스의 이름을 입력합니다. 자세한 내용은 SSMS 17.x 및 18.x의 연결 속성 간의 차이점에 대한 [다단계 Azure AD 인증](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter) 문서를 참조하세요.

   ![데이터베이스 이름 선택][13]

### <a name="active-directory-password-authentication"></a>Active Directory 암호 인증

Azure AD 관리 도메인을 사용하여 Azure AD 사용자 이름과 연결할 때 이 방법을 사용합니다. 예를 들어 원격으로 작업하는 경우 도메인에 액세스하지 않고 페더레이션된 계정에 사용할 수도 있습니다.

이 방법을 사용하여 Azure AD 클라우드 전용 ID 사용자 또는 Azure AD 하이브리드 ID를 사용하는 사용자를 사용하여 SQL Database 또는 SQL Managed Instance의 데이터베이스에 인증합니다. 이 방법은 Windows 자격 증명을 사용하려고 하지만 로컬 컴퓨터가 도메인에 조인되지 않은 사용자를 지원합니다(예: 원격 액세스 사용). 이 경우 Windows 사용자는 도메인 계정 및 암호를 표시할 수 있으며, SQL Database, SQL Managed Instance 또는 Azure Synapse의 데이터베이스에 인증할 수 있습니다.

1. Management Studio 또는 Data Tools를 시작하고, **서버에 연결**(또는 **데이터베이스 엔진에 연결**) 대화 상자의 **인증** 상자에서 **Azure Active Directory - 암호** 를 선택합니다.

2. **사용자 이름** 상자에서 Azure Active Directory 사용자 이름을 **username\@domain.com** 형식으로 입력합니다. 사용자 이름은 Azure Active Directory의 계정이거나 Azure Active Directory가 있는 관리되거나 페더레이션된 도메인의 계정이어야 합니다.

3. **암호** 상자에서 Azure Active Directory 계정 또는 관리되거나 페더레이션된 도메인 계정의 사용자 암호를 입력합니다.

    ![AD 암호 인증 선택][12]

4. **옵션** 단추를 선택하고 **연결 속성** 페이지의 **데이터베이스에 연결** 상자에서 연결하려는 사용자 데이터베이스의 이름을 입력합니다. (이전 옵션의 그래픽을 참조하세요.)

### <a name="active-directory-interactive-authentication"></a>Active Directory 대화형 인증

이 방법은 MFA(Multi-Factor Authentication)를 사용하거나 사용하지 않고 암호를 대화형으로 요청하는 대화형 인증에 사용합니다. 이 방법을 사용하여 Azure AD 클라우드 전용 ID 사용자 또는 Azure AD 하이브리드 ID를 사용하는 사용자에 대한 SQL Database, SQL Managed Instance 및 Azure Synapse의 데이터베이스에 인증할 수 있습니다.

자세한 내용은 [SQL Database 및 Azure Synapse에서 다단계 Azure AD 인증 사용(MFA에 대한 SSMS 지원)](authentication-mfa-ssms-overview.md)을 참조하세요.

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>클라이언트 애플리케이션에서 연결하는 데 Azure AD ID 사용

다음 절차에서는 클라이언트 애플리케이션에서 Azure AD ID를 사용하여 SQL Database에 연결하는 방법을 보여 줍니다.

### <a name="active-directory-integrated-authentication"></a>Active Directory 통합 인증

통합 Windows 인증을 사용하려면 도메인의 Active Directory가 Azure Active Directory와 페더레이션되어야 하거나, 통과 또는 암호 해시 인증을 위한 원활한 Single Sign-On에 대해 구성된 관리되는 도메인이어야 합니다. 자세한 내용은 [Azure Active Directory Seamless Single Sign-On](../../active-directory/hybrid/how-to-connect-sso.md)을 참조하세요.

> [!NOTE]
> 통과 및 암호 해시 인증의 원활한 Single Sign-On에 대해 통합 Windows 인증용 [MSAL.NET(Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)이 지원되지 않습니다.

데이터베이스에 연결하는 클라이언트 애플리케이션(또는 서비스)은 사용자의 도메인 자격 증명으로 도메인에 조인된 컴퓨터에서 실행해야 합니다.

통합 인증 및 Azure AD ID를 사용하여 데이터베이스에 연결하려면 데이터베이스 연결 문자열의 인증 키워드를 `Active Directory Integrated`로 설정해야 합니다. 다음 C# 코드 예제에서는 ADO.NET을 사용합니다.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

연결 문자열 키워드 `Integrated Security=True`는 Azure SQL Database 연결에 지원되지 않습니다. ODBC 연결을 설정할 때는 공백을 제거하고 인증을 'ActiveDirectoryIntegrated'로 설정해야 합니다.

### <a name="active-directory-password-authentication"></a>Active Directory 암호 인증

Azure AD 클라우드 전용 ID 사용자 계정 또는 Azure AD 하이브리드 ID를 사용하는 사용자를 사용하여 데이터베이스에 연결하려면 인증 키워드를 `Active Directory Password`로 설정해야 합니다. 연결 문자열에는 사용자 ID/UID 및 암호/PWD 키워드와 값이 포함되어 있어야 합니다. 다음 C# 코드 예제에서는 ADO.NET을 사용합니다.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

[Azure AD 인증 GitHub 데모](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)에서 사용할 수 있는 데모 코드 샘플을 사용한 Azure AD 인증 방법에 대해 자세히 알아보세요.

## <a name="azure-ad-token"></a>Azure AD 토큰

이 인증 방법을 통해 중간 계층 서비스에서 Azure AD로부터 토큰을 가져와서 SQL Database, SQL Managed Instance 또는 Azure Synapse의 데이터베이스에 연결할 수 있는 [JWT(JSON Web Token)](../../active-directory/develop/id-tokens.md)를 획득할 수 있습니다. 이 방법을 사용하면 인증서 기반 인증을 사용하는 서비스 ID, 서비스 주체 및 애플리케이션을 포함한 다양한 애플리케이션 시나리오를 사용할 수 있습니다. Azure AD 토큰 인증을 사용하려면 다음 네 가지 기본 단계를 완료해야 합니다.

1. Azure Active Directory에 애플리케이션을 등록하고, 코드에 대한 클라이언트 ID를 가져옵니다.
2. 애플리케이션을 나타내는 데이터베이스 사용자를 만듭니다(이전 6단계에서 완료).
3. 애플리케이션을 실행하는 클라이언트 컴퓨터에서 인증서를 만듭니다.
4. 인증서를 애플리케이션의 키로 추가합니다.

샘플 연결 문자열:

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token";
conn.Open();
```

자세한 내용은 [SQL Server 보안 블로그](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth)를 참조하세요. 인증서 추가에 대한 정보는 [Azure Active Directory에서 인증서 기반 인증 시작](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)을 참조하세요.

### <a name="sqlcmd"></a>sqlcmd

다음 문은 [다운로드 센터](https://www.microsoft.com/download/details.aspx?id=53591)에서 사용할 수 있는 sqlcmd 버전 13.1을 사용하여 연결합니다.

> [!NOTE]
> `-G` 명령을 사용하는 `sqlcmd`는 시스템 ID에서 작동하지 않으며 사용자 주체 로그인이 필요합니다.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Azure AD 인증 문제 해결

Azure AD 인증 문제 해결에 대한 지침은 다음 블로그에서 확인할 수 있습니다. <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>다음 단계

- SQL Database의 로그인, 사용자 및 데이터베이스 역할 및 권한에 대한 개요는 [로그인, 사용자 및 데이터베이스 역할 및 사용자 계정](logins-create-manage.md)을 참조하세요.
- 데이터베이스 보안 주체에 대한 자세한 내용은 [보안 주체](/sql/relational-databases/security/authentication-access/principals-database-engine)를 참조하세요.
- 데이터베이스 역할에 대한 자세한 내용은 [데이터베이스 역할](/sql/relational-databases/security/authentication-access/database-level-roles)을 참조하세요.
- SQL Database의 방화벽 규칙에 대한 자세한 내용은 [SQL Database 방화벽 규칙](firewall-configure.md)을 참조하세요.
- Azure AD 게스트 사용자를 Azure AD 관리자로 설정하는 방법에 대한 자세한 내용은 [Azure AD 게스트 사용자 만들기 및 Azure AD 관리자로 설정](authentication-aad-guest-users.md)을 참조하세요.
- Azure SQL을 사용하여 보안 주체 서비스를 제공하는 방법에 대한 자세한 내용은 [Azure AD 애플리케이션을 사용하여 Azure AD 사용자 만들기](authentication-aad-service-principal-tutorial.md)를 참조하세요.

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png
