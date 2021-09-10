---
title: Azure CLI를 통해 Azure Blob 복원
description: Azure CLI를 사용하여 Azure Blob을 지정 시점으로 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: 620b98d40c66b8af2360559c6f71db5ef6c07114
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2021
ms.locfileid: "113644314"
---
# <a name="restore-azure-blobs-to-point-in-time-using-azure-cli"></a>Azure CLI를 사용하여 Azure Blob을 시점으로 복원

이 문서에서는 Azure Backup을 사용하여 [Blob](blob-backup-overview.md)을 모든 시점으로 복원하는 방법을 설명합니다.

> [!IMPORTANT]
> CLI를 통한 Azure Blob 백업 및 복원에 대한 지원은 미리 보기로 제공되며 Az 2.15.0 버전 이상에서 확장으로 사용할 수 있습니다. **az dataprotection** 명령을 실행하면 확장이 자동으로 설치됩니다. 확장에 대해 [자세히 알아보세요](/cli/azure/azure-cli-extensions-overview).

> [!IMPORTANT]
> Azure Backup을 사용하여 Azure Blob을 복원하기 전에 [중요 사항](blob-restore.md#before-you-start)을 참조하세요.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- Azure Blob을 지정 시점으로 복원

- 복원 작업 상태 추적

예제에서 _testBkpVaultRG_ 리소스 그룹 아래에 있는 기존 백업 자격 증명 모음 _TestBkpVault_ 를 참조합니다.

## <a name="restoring-azure-blobs-within-a-storage-account"></a>스토리지 계정 내에서 Azure Blob 복원

### <a name="fetching-the-valid-time-range-for-restore"></a>복원에 유효한 시간 범위 가져오기

Blob에 대한 작업 백업이 연속적이기 때문에 복원할 고유한 지점이 없습니다. 대신 Blob을 모든 시점으로 복원할 수 있는 유효한 시간 범위를 가져와야 합니다. 이 예제에서는 지난 30일 이내에 복원할 유효한 시간 범위를 확인해 보겠습니다.

먼저 관련 백업 인스턴스 ID를 가져와야 합니다. [az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list) 명령을 사용하여 자격 증명 모음 내의 모든 백업 인스턴스를 나열한 다음, [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_show) 명령을 사용하여 관련 인스턴스를 페치합니다. 또는 확장된 시나리오의 경우 [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph) 명령을 사용하여 자격 증명 모음 및 구독 간에 백업 인스턴스를 나열할 수 있습니다.

```azurecli-interactive
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureBlob --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"

[
  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
    "extendedLocation": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
        "resourceLocation": "southeastasia",
        "resourceName": "CLITestSA",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": null,
      "datasourceAuthCredentials": null,
      "friendlyName": "CLITestSA",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy",
        "policyParameters": {
          "dataStoreParametersList": [
            {
              "dataStoreType": "OperationalStore",
              "objectType": "AzureOperationalStoreParameters",
              "resourceGroupId": ""
            }
          ]
        },
        "policyVersion": ""
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded"
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "rg-bv",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "TestBkpVault",
    "zones": null
  }
]
```

인스턴스가 식별되면 [az dataprotection restorable-time-range find](/cli/azure/dataprotection/restorable-time-range?view=azure-cli-latest&preserve-view=true#az_dataprotection_restorable_time_range_find) 명령을 사용하여 관련 복구 범위를 가져옵니다.

```azurecli-interactive
az dataprotection restorable-time-range find --start-time 2021-05-30T00:00:00 --end-time 2021-05-31T00:00:00 --source-data-store-type OperationalStore -g testBkpVaultRG --vault-name TestBkpVault --backup-instances CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036

{
  "id": "CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
  "name": null,
  "properties": {
    "objectType": "AzureBackupFindRestorableTimeRangesResponse",
    "restorableTimeRanges": [
      {
        "endTime": "2021-05-31T00:00:00.0000000Z",
        "objectType": "RestorableTimeRange",
        "startTime": "2021-06-13T18:53:44.4465407Z"
      }
    ]
  },
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges"
}
```

### <a name="preparing-the-restore-request"></a>복원 요청 준비

복원 시점이 수정되면 복원할 여러 옵션이 있습니다.

#### <a name="restoring-all-the-blobs-to-a-point-in-time"></a>모든 Blob을 시점으로 복원

이 옵션을 사용하면 선택한 시점으로 다시 롤백하여 스토리지 계정의 모든 블록 Blob을 복원할 수 있습니다. 많은 양의 데이터를 포함하거나 높은 변동이 발생하는 스토리지 계정은 복원하는 데 시간이 더 오래 걸릴 수 있습니다. 모든 블록 blob을 복원하려면 [az dataprotection backup-instance restore initialize-for-data-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery) 명령을 사용합니다. 복원 위치와 대상 리소스 ID는 보호된 스토리지 계정과 동일합니다.

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --target-resource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA" --point-in-time 2021-06-02T18:53:44.4465407Z

{
  "object_type": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "recovery_point_time": "2021-06-02T18:53:44.4465407Z.0000000Z",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Storage/storageAccounts/blobServices",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
      "resource_location": "southeastasia",
      "resource_name": "CLITestSA",
      "resource_type": "Microsoft.Storage/storageAccounts",
      "resource_uri": ""
    },
    "object_type": "RestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}


az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --target-resource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA" --point-in-time 2021-06-02T18:53:44.4465407Z > restore.json
```

#### <a name="restoring-selected-containers"></a>선택한 컨테이너 복원

이 옵션을 사용하면 복원할 최대 10개의 컨테이너를 찾아 선택할 수 있습니다. 선택한 컨테이너를 복원하려면 [az dataprotection backup-instance restore initialize-for-item-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_item_recovery) 명령을 사용합니다.

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --container-list container1 container2

{
  "object_type": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "recovery_point_time": "2021-06-02T18:53:44.4465407Z.0000000Z",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Storage/storageAccounts/blobServices",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
      "resource_location": "southeastasia",
      "resource_name": "CLITestSA",
      "resource_type": "Microsoft.Storage/storageAccounts",
      "resource_uri": ""
    },
    "object_type": "ItemLevelRestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_criteria": [
      {
        "max_matching_value": "container1-0",
        "min_matching_value": "container1",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      },
      {
        "max_matching_value": "container2-0",
        "min_matching_value": "container2",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      }
    ],
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}


az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --container-list container1 container2 > restore.json
```

#### <a name="restoring-containers-using-a-prefix-match"></a>접두사 일치를 사용하여 컨테이너 복원

이 옵션을 사용하면 접두사 일치를 사용하여 Blob의 하위 집합을 복원할 수 있습니다. 지정된 시점에 해당 Blob을 이전 상태로 되돌리기 위해 단일 컨테이너 내에서 또는 여러 컨테이너에서 사전순으로 최대 10개의 Blob 범위를 지정할 수 있습니다. 몇 가지 주의할 사항은 다음과 같습니다.

- 슬래시(/)를 사용하여 Blob 접두사에서 컨테이너 이름을 구분할 수 있습니다.
- 지정된 범위의 시작은 포함되지만 지정된 범위는 제외됩니다.

접두사를 사용하여 Blob 범위를 복원하는 방법에 대해 [자세히 알아보세요](blob-restore.md#use-prefix-match-for-restoring-blobs).

선택한 컨테이너를 복원하려면 [az dataprotection backup-instance restore initialize-for-item-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_item_recovery) 명령을 사용합니다.

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --from-prefix-pattern container1/text1 container2/text4 --to-prefix-pattern container1/text4 container2/text41

{
  "object_type": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "recovery_point_time": "2021-06-02T18:53:44.4465407Z.0000000Z",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Storage/storageAccounts/blobServices",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
      "resource_location": "southeastasia",
      "resource_name": "CLITestSA",
      "resource_type": "Microsoft.Storage/storageAccounts",
      "resource_uri": ""
    },
    "object_type": "ItemLevelRestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_criteria": [
       {
        "max_matching_value": "container1/text4",
        "min_matching_value": "container1/text1",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      },
      {
        "max_matching_value": "container2/text41",
        "min_matching_value": "container2/text4",
        "object_type": "RangeBasedItemLevelRestoreCriteria"
      }
    ],
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}



az dataprotection backup-instance restore initialize-for-item-recovery --datasource-type AzureBlob --restore-location southeastasia --source-datastore OperationalStore --backup-instance-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036" --point-in-time 2021-06-02T18:53:44.4465407Z --from-prefix-pattern container1/text1 container2/text4 --to-prefix-pattern container1/text4 container2/text41 > restore.json
```

### <a name="trigger-the-restore"></a>복원 트리거

[az dataprotection backup-instance restore trigger](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_trigger) 명령을 사용하여 위에서 준비한 요청으로 복원을 트리거합니다.

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036 --restore-request-object restore.json
```

## <a name="tracking-job"></a>작업 추적

[az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) 명령을 사용하여 모든 작업을 추적합니다. 모든 작업을 나열하고 특정 작업 세부 정보를 가져올 수 있습니다.

Az.ResourceGraph를 사용하여 모든 백업 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) 명령을 사용하여 모든 백업 자격 증명 모음에 있을 수 있는 관련 작업을 가져옵니다.

```azurepowershell-interactive
az dataprotection job list-from-resourcegraph --datasource-type AzureBlob --operation Restore
```

## <a name="next-steps"></a>다음 단계

[Azure Blob 백업의 지원 매트릭스](blob-backup-support-matrix.md)
