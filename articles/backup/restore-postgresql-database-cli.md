---
title: Azure CLI를 통해 Azure PostgreSQL 데이터베이스 복원
description: Azure CLI를 사용 하 여 Azure PostgreSQL 데이터베이스를 복원 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/25/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 48c1135e77a60fe06b86d8480a23b4ea0af8b72b
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133540277"
---
# <a name="restore-azure-postgresql-databases-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure PostgreSQL 데이터베이스 복원

이 문서에서는 Azure Backup 여 백업 된 Azure PostgreSQL 서버에 [Azure PostgreSQL 데이터베이스](../postgresql/overview.md#azure-database-for-postgresql---single-server) 를 복원 하는 방법을 설명 합니다.

PaaS 데이터베이스의 경우 백업이 수행 된 위치에서 기존 데이터베이스를 대체 하 여 복원 하는 OLR (Original-Location 복구) 옵션이 지원 되지 않습니다. 복구 지점에서 복원 하 여 동일한 Azure PostgreSQL 서버 또는 다른 PostgreSQL 서버에 새 데이터베이스를 만들 수 있습니다. 이를 ALR (Alternate-Location 복구) 라고 하며,이를 통해 원본 데이터베이스와 복원 된 (새 데이터베이스) 데이터베이스를 모두 유지할 수 있습니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- 새 PostgreSQL 데이터베이스를 만들기 위해 복원

- 복원 작업 상태 추적

예제에서 _testBkpVaultRG_ 리소스 그룹 아래에 있는 기존 백업 자격 증명 모음 _TestBkpVault_ 를 참조합니다.

## <a name="restore-a-backed-up-postgresql-database"></a>백업 된 PostgreSQL 데이터베이스 복원

### <a name="set-up-permissions"></a>권한 설정

백업 자격 증명 모음은 관리 Id를 사용 하 여 다른 Azure 리소스에 액세스 합니다. 백업에서 복원 하려면 백업 자격 증명 모음의 관리 되는 id에 데이터베이스를 복원 해야 하는 Azure PostgreSQL 서버에 대 한 권한 집합이 필요 합니다.

대상 PostgreSQL 서버에서 자격 증명 모음의 시스템 할당 관리 id에 대 한 관련 사용 권한을 할당 하려면 [Azure PostgreSQL database를 백업 하는 데 필요한 권한 집합](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-restore)을 참조 하세요.

복구 지점을 저장소 계정에 파일로 복원 하려면 [백업 자격 증명 모음의 시스템 할당 관리 id에 대상 저장소 계정에 대 한 액세스 권한이 있어야](./restore-azure-database-postgresql.md#restore-permissions-on-the-target-storage-account)합니다.

### <a name="fetch-the-relevant-recovery-point"></a>관련 복구 지점 가져오기

자격 증명 모음 내의 모든 백업 인스턴스를 나열 하려면 [az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list) 명령을 사용 하 여 [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_show) 명령을 사용 하 여 관련 인스턴스를 인출 합니다. 또는 _확장_ 된 시나리오의 경우 [az dataprotection backup-instance list-sourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph) 명령을 사용 하 여 자격 증명 모음 및 구독 간에 백업 인스턴스를 나열할 수 있습니다.

```azurecli
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL -subscriptions "xxxxxxxx-xxxx-xxxx-xxxx"

  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
    "extendedLocation": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
        "resourceLocation": "westus",
        "resourceName": "postgres",
        "resourceProperties": null,
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceUri": ""
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "objectType": "DatasourceSet",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
        "resourceLocation": "westus",
        "resourceName": "testpostgresql",
        "resourceProperties": null,
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceUri": ""
      },
      "datasourceAuthCredentials": {
        "objectType": "SecretStoreBasedAuthCredentials",
        "secretStoreResource": {
          "secretStoreType": "AzureKeyVault",
          "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
          "value": null
        }
      },
      "friendlyName": "testpostgresql\\empdb11",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
        "policyParameters": null,
        "policyVersion": ""
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded",
      "validationType": null
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "testBkpVaultRG",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "testBkpVault",
    "zones": null
  }
.
.
.
.
.
```

인스턴스가 식별되면 [az dataprotection recovery-point list](/cli/azure/dataprotection/recovery-point?view=azure-cli-latest&preserve-view=true#az_dataprotection_recovery_point_list) 명령을 사용하여 관련 복구 지점을 페치합니다.

```azurecli
az dataprotection recovery-point list --backup-instance-name testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149 -g testBkpVaultRG --vault-name TestBkpVault

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/recoveryPoints/9da55e757af94261afa009b43cd3222a",
  "name": "9da55e757af94261afa009b43cd3222a",
  "properties": {
    "friendlyName": "2031fdb43a914114b6ce644eb6fcb5ce",
    "objectType": "AzureBackupDiscreteRecoveryPoint",
    "policyName": "oss-clitest-policy",
    "policyVersion": null,
    "recoveryPointDataStoresDetails": [
      {
        "creationTime": "2021-09-13T15:17:41.209845+00:00",
        "expiryTime": null,
        "id": "beddea84-7b30-42a5-a752-7c75baf96a52",
        "metaData": "{\"objectType\":\"PostgresBackupMetadata\",\"version\":\"1.0\",\"postgresVersion\":\"11\",\"dbName\":\"postgres\",\"serverName\":\"testpostgresql\",\"serverFQDN\":\"testpostgresql.postgres.database.azure.com\",\"usernameUsed\":\"backupadmin@testpostgresql\",\"backupToolPath\":\"postgresql-11.6-1\\\\bin\\\\pg_dump.exe\",\"backupType\":\"Full\",\"backupDumpFormat\":\"CUSTOM\",\"backupToolArgsFormat\":\"--no-acl --no-owner --serializable-deferrable --no-tablespaces --quote-all-identifiers -Fc -d postgres://{0}:{1}@{2}:5432/{3}?sslmode=verify-full&sslrootcert=E:\\\\approot\\\\Plugins\\\\Postgres\\\\..\\\\..\\\\postgres-root.crt\",\"storageUnits\":{\"1\":\"DbBackupDumpData\"},\"streamNamesInFirstStorageUnit\":[\"dbbkpdmpdatastream-1631546260050\"],\"pitId\":\"2031fdb43a914114b6ce644eb6fcb5ce\",\"bytesTransferred\":2063,\"dataSourceSize\":8442527,\"backupToolVersion\":\"11\"}",
        "rehydrationExpiryTime": null,
        "rehydrationStatus": null,
        "state": "COMMITTED",
        "type": "VaultStore",
        "visible": true
      }
    ],
    "recoveryPointId": "9da55e757af94261afa009b43cd3222a",
    "recoveryPointTime": "2021-09-13T15:17:41.209845+00:00",
    "recoveryPointType": "Full",
    "retentionTagName": "default",
    "retentionTagVersion": "637671427933449525"
  },
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
}
```

보관 계층에서 복구 지점을 인출 해야 하는 경우 _recoveryPointDataStoreDetails_ 의 _형식_ 변수는 _ArchiveStore_ 가 됩니다.

### <a name="prepare-the-restore-request"></a>복원 요청 준비

PostgreSQL 데이터베이스에 대 한 다양 한 복원 옵션이 있습니다. 복구 지점을 다른 데이터베이스로 복원 하거나 파일로 복원할 수 있습니다. 복구 지점은 보관 계층에도 있을 수 있습니다.

#### <a name="restore-as-database"></a>데이터베이스로 복원

만들 새 PostgreSQL 데이터베이스 ( [위에](#set-up-permissions)설명 된 대로 사용 권한이 할당 된 대상 PostgreSQL 서버) 및 필수 PostgreSQL 데이터베이스 이름에 대 한 Azure Resource Manager ID (ARM id)를 생성 합니다. 예를 들어, 다른 구독을 사용 하 여 리소스 그룹 **targetrg** 의 대상 PostgreSQL 서버 **targetossserver** 아래에서 PostgreSQL 데이터베이스의 이름을 **emprestored21** 으로 지정할 수 있습니다.

```azurecli
$targetOssId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21"
```

모든 관련 세부 정보를 사용하여 복원 요청을 준비하려면 [az dataprotection backup-instance restore initialize-for-data-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery) 명령을 사용합니다.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore VaultStore --target-resource-id $targetOssId --recovery-point-id 9da55e757af94261afa009b43cd3222a --secret-store-type AzureKeyVault --secret-store-uri "https://restoreoss-test.vault.azure.net/secrets/dbauth3" > OssRestoreReq.JSON
```

아카이브 기반 복구 지점의 경우 다음을 수행 해야 합니다.

1. 보관 데이터 저장소에서 자격 증명 모음 저장소로 리하이드레이션
1. 원본 데이터 저장소를 수정 합니다.
1. 다른 매개 변수를 추가 하 여 리하이드레이션 우선 순위를 지정 합니다.
1. 자격 증명 모음 데이터 저장소에서 담당 하는 복구 지점을 보존할 기간을 지정 합니다.
1. 이 복구 지점의 데이터베이스로 복원 합니다.

다음 명령을 사용 하 여 위에서 언급 한 모든 작업에 대 한 요청을 한 번에 준비할 수 있습니다.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore ArchiveStore --target-resource-id $targetOssId --recovery-point-id 9da55e757af94261afa009b43cd3222a --secret-store-type AzureKeyVault --secret-store-uri "https://restoreoss-test.vault.azure.net/secrets/dbauth3" --rehydration-priority Standard --rehydration-duration 12 > OssRestoreFromArchiveReq.JSON
```

#### <a name="restore-as-files"></a>파일로 복원

[위에서](#set-up-permissions)설명한 대로 사용 권한이 할당 된 저장소 계정 내에서 컨테이너의 URI를 가져옵니다. 예를 들어 다른 구독이 있는 **testossstorageaccount** 저장소 계정 아래에 **testcontainerrestore** 라는 컨테이너가 있습니다.

```azurecli
$contURI = "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

해당 하는 모든 세부 정보를 사용 하 여 복원 요청을 준비 하려면 [az dataprotection backup-인스턴스 복원-데이터 복구-파일-복구](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery_as_files) 명령을 사용 합니다.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery-as-files --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore VaultStore -target-blob-container-url $contURI --target-file-name "empdb11_postgresql-westus_1628853549768" --recovery-point-id 9da55e757af94261afa009b43cd3222a > OssRestoreAsFilesReq.JSON
```

보관 기반 복구 지점의 경우 원본 데이터 저장소를 수정 하 고 아래 설명 된 대로, 리하이드레이션 된 복구 지점의 보존 기간 (일)을 추가 합니다.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery-as-files --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore ArchiveStore -target-blob-container-url $contURI --target-file-name "empdb11_postgresql-westus_1628853549768" --recovery-point-id 9da55e757af94261afa009b43cd3222a --rehydration-priority Standard --rehydration-duration 12 > OssRestoreAsFilesReq.JSON
```

또한 [az dataprotection backup-instance validate-restore](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_validate_for_restore) 명령을 사용 하 여 JSON 파일이 새 리소스를 만드는 데 성공 하는지 확인할 수 있습니다.

### <a name="trigger-the-restore"></a>복원 트리거

위 단계에서 준비 된 요청으로 복원 작업을 트리거하려면 [az dataprotection backup-instance restore trigger](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_trigger) 명령을 사용 합니다.

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149 --restore-request-object OssRestoreReq.JSON
```

## <a name="tracking-job"></a>작업 추적

[az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) 명령을 사용하여 모든 작업을 추적합니다. 모든 작업을 나열하고 특정 작업 세부 정보를 가져올 수 있습니다.

_Az. ResourceGraph_ 를 사용 하 여 모든 백업 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [Az dataprotection 작업 목록-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) 명령을 사용 하 여 모든 백업 자격 증명 모음에 있는 관련 작업을 가져옵니다.

```azurecli
az dataprotection job list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL --operation Restore
```

## <a name="next-steps"></a>다음 단계

- [Azure PostgreSQL Backup 개요](backup-azure-database-postgresql-overview.md)