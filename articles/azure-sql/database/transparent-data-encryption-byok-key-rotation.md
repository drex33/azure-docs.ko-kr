---
title: TDE 보호기 회전(PowerShell 및 Azure CLI)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: PowerShell 및 Azure CLI를 사용하여 Azure SQL Database 및 Azure Synapse Analytics에서 사용하는 Azure 서버의 TDE(투명한 데이터 암호화) 보호기를 회전하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 06/23/2021
ms.openlocfilehash: c14d209f5f4b846b51a99ac875e2304129b4e765
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090280"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>TDE(투명한 데이터 암호화) 보호기 회전
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


이 문서에서는 Azure Key Vault에서 TDE 보호기를 사용하는 [서버](logical-servers.md)에 대한 키 회전을 설명합니다. 서버의 논리적 TDE 보호기를 회전한다는 것은 서버의 데이터베이스를 보호하는 새 비대칭 키로 전환한다는 의미입니다. 키 회전은 온라인 작업이며, 전체 데이터베이스가 아닌 데이터베이스의 데이터 암호화 키를 단지 해독하고 다시 암호화하는 작업이기 때문에 완료되는 데 몇 초밖에 걸리지 않습니다.

이 가이드에서는 서버에서 TDE 보호기를 회전하는 두 가지 옵션을 설명합니다.

> [!NOTE]
> Azure Synapse Analytics에서 일시 중지된 전용 SQL 풀은 키를 회전하기 전에 반드시 다시 시작해야 합니다.

> [!IMPORTANT]
> 롤오버 후 키의 이전 버전은 삭제하지 마세요. 키가 롤오버될 때 구 데이터베이스 백업 같은 일부 데이터는 이전 키로 계속 암호화되어 있습니다.

> [!NOTE]
> 이 문서는 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics(전용 SQL 풀(이전의 SQL DW))에 적용됩니다. Synapse 작업 영역 내의 전용 SQL 풀을 위한 투명한 데이터 암호화에 대한 문서는 [Azure Synapse Analytics 암호화](../../synapse-analytics/security/workspaces-encryption.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 이 방법 가이드에서는 이미 Azure SQL Database나 Azure Synapse Analytics에 대해 Azure Key Vault의 키를 TDE 보호기로 사용하고 있다고 가정합니다. [BYOK 지원을 통한 투명한 데이터 암호화](transparent-data-encryption-byok-overview.md)를 참조하세요.
- Azure PowerShell이 설치되어 작동해야 합니다.
- [권장 선택 사항] HSM(하드웨어 보안 모듈)이나 로컬 키 저장소의 TDE 보호기에 대한 키 자료를 먼저 만들고 키 자료를 Azure Key Vault로 가져옵니다. [HSM(하드웨어 보안 모듈) 및 Key Vault 사용 지침](../../key-vault/general/overview.md)에 따라 자세히 알아봅니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az 모듈 설치 지침은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요. 특정 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요.

> [!IMPORTANT]
> PowerShell Azure RM(Resource Manager) 모듈은 여전히 지원되지만 향후 모든 개발은 Az.Sql 모듈을 위한 것입니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 계속 수신할 예정입니다.  Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다. 호환성에 대한 자세한 내용은 [새로운 Azure PowerShell Az 모듈 소개](/powershell/azure/new-azureps-module-az)를 참조하세요.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

설치는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

* * *

## <a name="manual-key-rotation"></a>수동 키 회전

수동 키 회전은 다음의 명령을 사용하여 완전히 새로운 키를 추가합니다. 새로운 키는 새로운 키 이름이나 심지어 다른 키 자격 증명 모음 아래에 위치할 수 있습니다. 이 방법을 사용하면 고가용성 및 지리적으로 분산된 시나리오를 지원하기 위해 서로 다른 키 자격 증명 모음에 동일한 키를 추가할 수 있습니다.

> [!NOTE]
> 키 자격 증명 모음 이름과 키 이름을 결합한 길이는 94자를 초과할 수 없습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) 및 [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlets를 사용합니다.

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create), [az sql server key create](/cli/azure/sql/server/key#az_sql_server_key_create) 및 [az sql server tde-key set](/cli/azure/sql/server/tde-key#az_sql_server_tde_key_set) 명령을 사용합니다.

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>TDE 보호기 모드 스위치

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- TDE 보호기를 Microsoft 관리에서 BYOK 모드로 전환하려면 [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용합니다.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- TDE 보호기를 BYOK 모드에서 Microsoft 관리로 전환하려면 [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet을 사용합니다.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 예에서는 [az sql server tde-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)를 사용합니다.

- TDE 보호기를 Microsoft 관리에서 BYOK 모드로 전환하려면

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- TDE 보호기를 BYOK 모드에서 Microsoft 관리로 전환하려면

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>다음 단계

- 보안 위험이 있는 경우 잠재적으로 손상될 수 있는 TDE 보호기를 제거하는 방법을 알아봅니다([잠재적으로 손상될 수 있는 키 제거](transparent-data-encryption-byok-remove-tde-protector.md)).

- TDE에 대한 Azure Key Vault 통합 및 BYOK(Bring Your Own Key) 지원 시작: [PowerShell을 사용하여 Key Vault에서 고유 키로 TDE를 설정합니다](transparent-data-encryption-byok-configure.md).
