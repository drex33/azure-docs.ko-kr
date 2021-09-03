---
title: TDE 보호기 제거(PowerShell 및 Azure CLI)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: BYOK(Bring Your Own Key) 지원을 통해 TDE를 사용하여 Azure SQL Database 또는 Azure Synapse Analytics에 손상 가능성이 있는 TDE 보호기에 대응하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 06/23/2021
ms.openlocfilehash: 6d3027afae6b1d4121582014bb2b525ba2fd2c44
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090262"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell을 사용하여 TDE(Transparent Data Encryption) 보호기 제거
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


이 항목에서는 Azure Key Vault에서 고객 관리형 키 - BYOK(Bring Your Own Key) 지원을 통해 TDE를 사용하여 Azure SQL Database 또는 Azure Synapse Analytics에 손상 가능성이 있는 TDE 보호기에 대응하는 방법을 설명합니다. TDE의 BYOK 지원에 대해 자세한 내용은 [개요 페이지](transparent-data-encryption-byok-overview.md)를 참조하세요.

> [!CAUTION]
> 이 문서에 설명된 절차는 극단적인 경우 또는 테스트 환경에서만 수행해야 합니다. Azure Key Vault에서 적극적으로 사용되는 TDE 보호기를 삭제하면 **데이터베이스를 사용할 수 없으므로** 단계를 신중하게 검토하세요.

키가 손상되어 서비스나 사용자가 키에 무단 액세스한 것으로 의심될 경우 키를 삭제하는 것이 가장 좋습니다.

Key Vault에서 TDE 보호기를 삭제하면 최대 10분 내에 암호화된 모든 데이터베이스가 해당 오류 메시지와 함께 모든 연결을 거부하기 시작하고 해당 상태를 [액세스할 수 없음](./transparent-data-encryption-byok-overview.md#inaccessible-tde-protector)으로 변경합니다.

이 방법 가이드에서는 손상된 인시던트 대응 후 데이터베이스를 **액세스할 수 없도록** 렌더링하는 접근 방식에 대해 설명합니다.

> [!NOTE]
> 이 문서는 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics(전용 SQL 풀(이전의 SQL DW))에 적용됩니다. Synapse 작업 영역 내의 전용 SQL 풀을 위한 투명한 데이터 암호화에 대한 문서는 [Azure Synapse Analytics 암호화](../../synapse-analytics/security/workspaces-encryption.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
- Azure PowerShell이 설치되어 작동해야 합니다.
- 이 방법 가이드에서는 이미 Azure SQL Database나 Azure Synapse에 대해 Azure Key Vault의 키를 TDE 보호기로 사용하고 있다고 가정합니다. 자세한 내용은 [BYOK 지원을 통한 투명한 데이터 암호화](transparent-data-encryption-byok-overview.md)를 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Az 모듈 설치 지침은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요. 특정 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요.

> [!IMPORTANT]
> PowerShell Azure RM(Resource Manager) 모듈은 여전히 지원되지만 향후 모든 개발은 Az.Sql 모듈을 위한 것입니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 계속 수신할 예정입니다.  Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 호환성에 대한 자세한 내용은 [새로운 Azure PowerShell Az 모듈 소개](/powershell/azure/new-azureps-module-az)를 참조하세요.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

설치에 대한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

* * *

## <a name="check-tde-protector-thumbprints"></a>TDE 보호기 지문 확인

다음 단계에서는 지정된 데이터베이스의 VLF(가상 로그 파일)에서 여전히 사용 중인 TDE 보호기 지문을 확인하는 방법을 간략하게 설명합니다.
데이터베이스의 현재 TDE 보호기 지문 및 데이터베이스 ID는 다음을 실행하여 찾을 수 있습니다.

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

다음 쿼리는 사용 중인 각 VLF 및 TDE 보호기를 반환합니다. 각 지문은 AKV(Azure Key Vault)의 서로 다른 키를 나타냅니다.

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

또는 PowerShell이나 Azure CLI를 사용할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 명령 **Get-AzureRmSqlServerKeyVaultKey** 는 쿼리에 사용된 TDE 보호기의 지문을 제공하므로 AKV에서 유지할 키와 삭제할 키를 확인할 수 있습니다. 데이터베이스에서 더 이상 사용하지 않는 키만 Azure Key Vault에서 안전하게 삭제할 수 있습니다.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell 명령 **az sql server key show** 는 쿼리에 사용된 TDE 보호기의 지문을 제공하므로 AKV에서 유지할 키와 삭제할 키를 확인할 수 있습니다. 데이터베이스에서 더 이상 사용하지 않는 키만 Azure Key Vault에서 안전하게 삭제할 수 있습니다.

* * *

## <a name="keep-encrypted-resources-accessible"></a>암호화된 리소스를 액세스 가능한 상태로 유지

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Key Vault에 새 키](/powershell/module/az.keyvault/add-azkeyvaultkey)를 만듭니다. 액세스 제어는 자격 증명 모음 수준에서 프로비전되므로 이 새 키는 손상 가능성이 있는 TDE 보호기와는 별도의 키 자격 증명 모음에 만들어야 합니다.

2. [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) 및 [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용하여 새 키를 서버에 추가하고 서버의 새 TDE 보호기로 업데이트합니다.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet을 사용하여 서버 및 복제본이 새 TDE 보호기로 업데이트되어야 합니다.

   > [!NOTE]
   > 새 TDE 보호기가 서버 아래의 모든 데이터베이스와 보조 데이터베이스로 전파되는 데 몇 분 정도 걸릴 수 있습니다.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Key Vault에 [새 키의 백업](/powershell/module/az.keyvault/backup-azkeyvaultkey)을 작성합니다.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) cmdlet을 사용하여 Key Vault에서 손상된 키를 삭제합니다.

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. [Restore-AzureKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet을 사용하여 Key Vault에 키를 복원하려면 다음을 수행합니다.

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

명령 참조는 [Azure CLI Key Vault](/cli/azure/keyvault/key)를 참조하세요.

1. [Key Vault에 새 키](/cli/azure/keyvault/key#az_keyvault_key_create)를 만듭니다. 액세스 제어는 자격 증명 모음 수준에서 프로비전되므로 이 새 키는 손상 가능성이 있는 TDE 보호기와는 별도의 키 자격 증명 모음에 만들어야 합니다.

2. 새 키를 서버에 추가하고 서버의 새 TDE 보호기로 업데이트합니다.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. 서버 및 복제본이 새 TDE 보호기로 업데이트된 상태여야 합니다.

   > [!NOTE]
   > 새 TDE 보호기가 서버 아래의 모든 데이터베이스와 보조 데이터베이스로 전파되는 데 몇 분 정도 걸릴 수 있습니다.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Key Vault에 새 키의 백업을 작성합니다.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Key Vault에서 손상된 키를 제거합니다.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. 나중에 Key Vault에 대한 키를 복원하려면 다음을 수행합니다.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>암호화된 리소스에 액세스할 수 없도록 설정

1. 손상 가능성이 있는 키로 암호화된 데이터베이스를 삭제합니다.

   데이터베이스 및 로그 파일은 자동으로 백업되므로 (키를 제공하는 한) 어느 지점에서나 데이터베이스의 시점 복원을 수행할 수 있습니다. 최대 10분의 가장 최근 트랜잭션에 대한 데이터 손실 가능성을 차단하기 위해 활성 TDE 보호기를 삭제하기 전에 데이터베이스를 삭제해야 합니다.

2. Key Vault에서 TDE 보호기의 키 자료를 백업합니다.
3. Key Vault에서 잠재적으로 손상된 키 제거

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>다음 단계

- 보안 요구 사항에 맞게 서버의 TDE 보호기를 회전하는 방법을 알아봅니다([PowerShell을 사용하여 투명한 데이터 암호화 보호기 회전](transparent-data-encryption-byok-key-rotation.md)).
- TDE에 대한 BYOK(Bring Your Own Key) 지원 시작: [PowerShell을 사용하여 Key Vault에서 고유 키로 TDE 설정](transparent-data-encryption-byok-configure.md)