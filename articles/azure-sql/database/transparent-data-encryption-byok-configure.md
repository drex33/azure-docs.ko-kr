---
title: Azure Key Vault로 SQL TDE 사용
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: PowerShell 또는 Azure CLI를 통해 미사용 데이터 암호화에 TDE(투명한 데이터 암호화)를 사용하도록 Azure SQL Database 및 Azure Synapse Analytics를 구성하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 06/23/2021
ms.openlocfilehash: 56ac53d8c7b1361e01a5ca72478c68f6db15873f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090298"
---
# <a name="powershell-and-the-azure-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell 및 Azure CLI: Azure Key Vault에서 고객 관리형 키로 투명한 데이터 암호화를 사용하도록 설정
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

이 문서에서는 Azure SQL Database 또는 Azure Synapse Analytics에서 TDE(투명한 데이터 암호화)에 대해 Azure Key Vault의 키를 사용하는 방법에 대해 설명합니다. Azure Key Vault 통합으로 TDE - BYOK(Bring Your Own Key) 지원에 대해 자세히 알아보려면 [Azure Key Vault에서 고객 관리형 키로 TDE](transparent-data-encryption-byok-overview.md)를 참조하세요. 

> [!NOTE] 
> 이제 Azure SQL은 관리형 HSM에 저장된 RSA 키를 TDE 보호기로 사용하도록 지원합니다. 이 기능은 **퍼블릭 미리 보기** 상태입니다. Azure Key Vault 관리형 HSM은 FIPS 140-2 수준 3 유효성이 검사된 HSM을 사용하여 클라우드 애플리케이션용 암호화 키를 보호할 수 있는 완전 관리형 고가용 단일 테넌트 표준 규격 클라우드 서비스입니다. [관리형 HSM](../../key-vault/managed-hsm/index.yml)에 대해 자세히 알아봅니다.

> [!NOTE]
> 이 문서는 Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics(전용 SQL 풀(이전의 SQL DW))에 적용됩니다. Synapse 작업 영역 내의 전용 SQL 풀을 위한 투명한 데이터 암호화에 관한 설명서는 [Azure Synapse Analytics 암호화](../../synapse-analytics/security/workspaces-encryption.md)를 참조하세요.

## <a name="prerequisites-for-powershell"></a>PowerShell용 필수 구성 요소

- Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
- [권장되는 선택 사항] TDE 보호기 키 자료의 로컬 복사본을 만들기 위한 HSM(하드웨어 보안 모듈) 또는 로컬 키 저장소가 있어야 합니다.
- Azure PowerShell이 설치되어 작동해야 합니다.
- TDE에 사용할 Azure Key Vault 및 키를 만듭니다.
  - [HSM(하드웨어 보안 모듈) 및 Key Vault 사용 지침](../../key-vault/keys/hsm-protected-keys.md)
    - 키 자격 증명 모음에는 TDE에 사용할 다음 속성이 있어야 합니다.
  - [일시 삭제](../../key-vault/general/soft-delete-overview.md) 및 제거 보호
- 키에는 TDE에 사용할 다음 특성이 있어야 합니다.
  - 만료 날짜 없음
  - 사용 안 함 없음
  - *가져오기*, *키 래핑*, *키 래핑 해제* 작업도 수행 가능
- **(미리 보기)** 관리형 HSM 키를 사용하려면 지침에 따라 [Azure CLI를 사용하여 관리형 HSM을 만들고 활성화](../../key-vault/managed-hsm/quick-create-cli.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az 모듈 설치 지침은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요. 특정 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요.

Key Vault에 대한 자세한 내용은 [Key Vault의 PowerShell 지침](../../key-vault/secrets/quick-create-powershell.md) 및 [PowerShell에서 Key Vault 일시 삭제 사용하는 방법](../../key-vault/general/key-vault-recovery.md)을 참조하세요.

> [!IMPORTANT]
> PowerShell Azure RM(Resource Manager) 모듈은 여전히 지원되지만 향후 모든 개발은 Az.Sql 모듈을 위한 것입니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 계속 수신할 예정입니다.  Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 호환성에 대한 자세한 내용은 [새로운 Azure PowerShell Az 모듈 소개](/powershell/azure/new-azureps-module-az)를 참조하세요.

## <a name="assign-an-azure-active-directory-azure-ad-identity-to-your-server"></a>서버에 Azure AD(Azure Active Directory) ID 할당

기존 [서버](logical-servers.md)가 있는 경우 다음과 같이 Azure AD(Azure Active Directory) ID를 서버에 추가합니다.

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

서버를 만드는 경우 -Identity 태그가 있는 [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet을 사용하여 서버를 만드는 동안 Azure AD ID를 추가합니다.

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>서버에 Key Vault 권한 부여

서버에서 키 자격 증명 모음의 키를 TDE에 사용하기 전에 [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet을 사용하여 해당 키 자격 증명 모음에 대한 액세스 권한을 서버에 부여합니다.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```
관리형 HSM의 서버에 권한을 추가하려면 '관리형 HSM Crypto Service 암호화 사용자' 로컬 RBAC 역할을 서버에 추가합니다. 이렇게 하면 서버에서 관리형 HSM의 키에 대해 가져오기, 키 래핑, 키 래핑 해제 작업을 수행할 수 있습니다.
[관리형 HSM에서 서버 액세스를 프로비저닝하는 방법에 대한 지침](../../key-vault/managed-hsm/role-management.md)

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Key Vault 키를 서버에 추가하고 TDE 보호기를 설정합니다

- [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) cmdlet을 사용하여 키 자격 증명 모음에서 키 ID를 검색
- [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) cmdlet을 사용하여 Key Vault의 키를 서버에 추가합니다.
- [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용하여 키를 모든 서버 리소스에 대한 TDE 보호기로 설정합니다.
- [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet을 사용하여 TDE 보호기가 의도한 대로 구성되었는지 확인합니다.

> [!NOTE]
> **(미리 보기)** 관리형 HSM 키의 경우 Az.Sql 2.11.1 버전의 PowerShell을 사용합니다.

> [!NOTE]
> 키 자격 증명 모음 이름과 키 이름을 결합한 길이는 94자를 초과할 수 없습니다.

> [!TIP]
> Key Vault의 KeyId 예제: `https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h`
>
> 관리형 HSM의 KeyId 예제:<br/>https://contosoMHSM.managedhsm.azure.net/keys/myrsakey

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>TDE 설정

[Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet을 사용하여 TDE를 해제합니다.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

이제 데이터베이스 또는 데이터 웨어하우스에서 Key Vault에 있는 암호화 키를 사용하여 TDE를 사용할 수 있도록 설정되었습니다.

## <a name="check-the-encryption-state-and-encryption-activity"></a>암호화 상태 및 암호화 작업 확인

[Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption)을 사용하여 암호화 상태를 가져오고, [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity)를 사용하여 데이터베이스 또는 데이터 웨어하우스의 암호화 진행률을 확인합니다.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에 요구되는 버전(버전 2.0 이상)을 설치하고 Azure 구독에 연결하려면 [Azure 플랫폼 간 CLI 2.0 설치 및 구성](/cli/azure/install-azure-cli)을 참조하세요.

Key Vault에 대한 자세한 내용은 [CLI 2.0을 사용한 Key Vault 관리](../../key-vault/general/manage-with-cli2.md) 및 [CLI로 Key Vault 일시 삭제 사용하는 방법](../../key-vault/general/key-vault-recovery.md)을 참조하세요.

## <a name="assign-an-azure-ad-identity-to-your-server"></a>서버에 Azure AD ID 할당

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> 서버를 만드는 과정에서 얻은 “principalID”를 보관합니다. 이 ID는 다음 단계에서 키 자격 증명 모음 권한을 할당하는 데 사용되는 개체 ID입니다.

## <a name="grant-key-vault-permissions-to-your-server"></a>서버에 Key Vault 권한 부여

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> 다음 단계를 위해 새 키의 키 URI 또는 keyID를 보관합니다(예: `https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h`).

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Key Vault 키를 서버에 추가하고 TDE 보호기를 설정합니다

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> 키 자격 증명 모음 이름과 키 이름을 결합한 길이는 94자를 초과할 수 없습니다.

## <a name="turn-on-tde"></a>TDE 설정

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

이제 데이터베이스 또는 데이터 웨어하우스에서 Azure Key Vault에 있는 고객 관리형 암호화 키로 TDE를 사용할 수 있습니다.

## <a name="check-the-encryption-state-and-encryption-activity"></a>암호화 상태 및 암호화 작업 확인

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>유용한 PowerShell cmdlet

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- TDE를 해제하려면 [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet을 사용합니다.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- 서버에 추가된 Key Vault 키 목록을 반환하려면 [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet을 사용합니다.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- 서버에서 Key Vault 키를 제거하려면 [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey)를 사용합니다.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

- 일반 데이터베이스 설정은 [az sql](/cli/azure/sql)을 참조하세요.

- 자격 증명 모음 키 설정에 대한 자세한 내용은 [az sql server key](/cli/azure/sql/server/key)를 참조하세요.

- TDE 설정에 대해서는 [az sql server tde-key](/cli/azure/sql/server/tde-key) 및 [az sql db tde](/cli/azure/sql/db/tde)를 참조하세요.

* * *

## <a name="troubleshooting"></a>문제 해결

문제가 발생하면 다음과 같이 확인합니다.

- 키 자격 증명 모음을 찾을 수 없으면 올바른 구독에 있는지 확인합니다.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli
   az account show - s <SubscriptionId>
   ```

   * * *

- 새 키를 서버에 추가할 수 없거나 TDE 보호기로 업데이트할 수 없으면 다음을 확인합니다.
   - 키의 만료 날짜가 없어야 합니다.
   - 키에는 사용하도록 설정된 *가져오기*, *키 래핑* 및 *키 래핑 해제* 작업이 있어야 합니다.

## <a name="next-steps"></a>다음 단계

- 보안 요구 사항에 맞게 서버의 TDE 보호기를 회전하는 방법을 알아봅니다([PowerShell을 사용하여 투명한 데이터 암호화 보호기 회전](transparent-data-encryption-byok-key-rotation.md)).
- 보안 위험이 있는 경우 잠재적으로 손상될 수 있는 TDE 보호기를 제거하는 방법을 알아봅니다([잠재적으로 손상될 수 있는 키 제거](transparent-data-encryption-byok-remove-tde-protector.md)).
