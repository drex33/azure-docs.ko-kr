---
title: Azure CLI 통해 Azure PostgreSQL 데이터베이스 복원
description: Azure CLI 사용하여 Azure PostgreSQL 데이터베이스를 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/25/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 3b748c8279740b4ef3a4e3b97b6acbc4a5aae6c2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707391"
---
# <a name="restore-azure-postgresql-databases-using-azure-cli"></a>Azure CLI 사용하여 Azure PostgreSQL 데이터베이스 복원

이 문서에서는 Azure Backup 백업된 [Azure PostgreSQL](../postgresql/overview.md#azure-database-for-postgresql---single-server) 서버로 Azure PostgreSQL 데이터베이스를 복원하는 방법을 설명합니다.

PaaS 데이터베이스인 경우 백업이 수행된 기존 데이터베이스를 교체하여 복원하는 OLR(Original-Location Recovery) 옵션은 지원되지 않습니다. 복구 지점에서 복원하여 동일한 Azure PostgreSQL 서버 또는 다른 PostgreSQL 서버에 새 데이터베이스를 만들 수 있습니다. 이를 원본 데이터베이스와 복원된(새) 데이터베이스를 모두 유지하는 데 도움이 되는 ALR(Alternate-Location Recovery)이라고 합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- 복원하여 새 PostgreSQL 데이터베이스 만들기

- 복원 작업 상태 추적

예제에서 _testBkpVaultRG_ 리소스 그룹 아래에 있는 기존 백업 자격 증명 모음 _TestBkpVault_ 를 참조합니다.

## <a name="restore-a-backed-up-postgresql-database"></a>백업된 PostgreSQL 데이터베이스 복원

### <a name="set-up-permissions"></a>권한 설정

Backup 자격 증명 모음은 관리 ID를 사용하여 다른 Azure 리소스에 액세스합니다. 백업에서 복원하려면 백업 자격 증명 모음의 관리 ID에 데이터베이스를 복원해야 하는 Azure PostgreSQL 서버에 대한 권한 집합이 필요합니다.

대상 PostgreSQL 서버에서 자격 증명 모음의 시스템 할당 관리 ID에 대한 관련 권한을 할당하려면 [Azure PostgreSQL 데이터베이스를 백업하는 데 필요한 권한 집합을 참조하세요.](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-restore)

복구 지점을 스토리지 계정에 파일로 복원하려면 Backup 자격 증명 [모음의 시스템 할당 관리 ID에 대상 스토리지 계정 에 대한 액세스 권한이 필요합니다.](./restore-azure-database-postgresql.md#restore-permissions-on-the-target-storage-account)

### <a name="fetch-the-relevant-recovery-point"></a>관련 복구 지점 가져오기

자격 증명 모음 내의 모든 백업 인스턴스를 나열하려면 [az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list) 명령을 사용한 다음 [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_show) 명령을 사용하여 관련 인스턴스를 가져옵니다. 또는 대규모 _시나리오의_ 경우 [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph) 명령을 사용하여 자격 증명 모음 및 구독에서 백업 인스턴스를 나열할 수 있습니다.

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

보관 계층에서 복구 지점을 가져와야 하는 경우 _recoveryPointDataStoreDetails의_ _형식_ 변수는 _ArchiveStore_ 입니다.

### <a name="prepare-the-restore-request"></a>복원 요청 준비

PostgreSQL 데이터베이스에 대한 다양한 복원 옵션이 있습니다. 복구 지점을 다른 데이터베이스로 복원하거나 파일로 복원할 수 있습니다. 복구 지점은 보관 계층에도 있을 수 있습니다.

#### <a name="restore-as-database"></a>데이터베이스로 복원

만들 새 PostgreSQL 데이터베이스의 Azure Resource Manager ID(ARM ID) 및 필요한 PostgreSQL 데이터베이스 [이름(위에서](#set-up-permissions)설명한 대로 권한이 할당된 대상 PostgreSQL 서버 사용)을 생성합니다. 예를 들어 다른 구독을 사용하는 리소스 그룹 **targetrg의** 대상 PostgreSQL 서버 **targetossserver에서** PostgreSQL 데이터베이스의 이름을 **emprestored21로** 지정할 수 있습니다.

```azurecli
$targetOssId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21"
```

모든 관련 세부 정보를 사용하여 복원 요청을 준비하려면 [az dataprotection backup-instance restore initialize-for-data-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery) 명령을 사용합니다.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore VaultStore --target-resource-id $targetOssId --recovery-point-id 9da55e757af94261afa009b43cd3222a --secret-store-type AzureKeyVault --secret-store-uri "https://restoreoss-test.vault.azure.net/secrets/dbauth3" > OssRestoreReq.JSON
```

보관 기반 복구 지점의 경우 다음을 수행해야 합니다.

1. 보관 데이터 저장소에서 자격 증명 모음 저장소로 리하이딩
1. 원본 데이터 저장소를 수정합니다.
1. 다른 매개 변수를 추가하여 리하일레이션 우선 순위를 지정합니다.
1. 리하위드 복구 지점을 자격 증명 모음 데이터 저장소에 보존할 기간을 지정합니다.
1. 이 복구 지점에서 데이터베이스로 복원합니다.

다음 명령을 사용하여 위에서 언급한 모든 작업에 대한 요청을 한 번에 준비합니다.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore ArchiveStore --target-resource-id $targetOssId --recovery-point-id 9da55e757af94261afa009b43cd3222a --secret-store-type AzureKeyVault --secret-store-uri "https://restoreoss-test.vault.azure.net/secrets/dbauth3" --rehydration-priority Standard --rehydration-duration 12 > OssRestoreFromArchiveReq.JSON
```

#### <a name="restore-as-files"></a>파일로 복원

[위에서](#set-up-permissions)설명한 대로 권한이 할당된 스토리지 계정 내에서 컨테이너의 URI를 가져옵니다. 예를 들어 다른 구독을 가진 스토리지 계정 **testossstorageaccount에** 있는 **testcontainerrestore라는** 컨테이너가 있습니다.

```azurecli
$contURI = "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

[az dataprotection backup-instance restore initialize-for-data-recovery-as-files](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery_as_files) 명령을 사용하여 모든 관련 세부 정보로 복원 요청을 준비합니다.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery-as-files --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore VaultStore -target-blob-container-url $contURI --target-file-name "empdb11_postgresql-westus_1628853549768" --recovery-point-id 9da55e757af94261afa009b43cd3222a > OssRestoreAsFilesReq.JSON
```

보관 기반 복구 지점의 경우 원본 데이터 저장소를 수정하고 아래 설명된 대로 리하위드 복구 지점의 리하이드레이션 우선 순위와 보존 기간(일)을 추가합니다.

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery-as-files --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore ArchiveStore -target-blob-container-url $contURI --target-file-name "empdb11_postgresql-westus_1628853549768" --recovery-point-id 9da55e757af94261afa009b43cd3222a --rehydration-priority Standard --rehydration-duration 12 > OssRestoreAsFilesReq.JSON
```

[az dataprotection backup-instance validate-for-restore](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_validate_for_restore) 명령을 사용하여 JSON 파일이 새 리소스를 만드는 데 성공하는지 확인할 수도 있습니다.

### <a name="trigger-the-restore"></a>복원 트리거

[az dataprotection backup-instance restore trigger](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_trigger) 명령을 사용하여 위에서 준비한 요청으로 복원 작업을 트리거합니다.

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149 --parameters OssRestoreReq.JSON
```

## <a name="tracking-job"></a>작업 추적

[az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) 명령을 사용하여 모든 작업을 추적합니다. 모든 작업을 나열하고 특정 작업 세부 정보를 가져올 수 있습니다.

_Az.ResourceGraph를_ 사용하여 모든 Backup 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) 명령을 사용하여 모든 Backup 자격 증명 모음에 있는 관련 작업을 얻을 수 있습니다.

```azurecli
az dataprotection job list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL --operation Restore
```

## <a name="next-steps"></a>다음 단계

- [Azure PostgreSQL 백업 개요](backup-azure-database-postgresql-overview.md)