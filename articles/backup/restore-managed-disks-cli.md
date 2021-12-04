---
title: Azure CLI를 통해 Azure Managed Disks 복원
description: Azure CLI를 사용하여 Azure Managed Disks를 복원하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: 6e4c2d6fa2f876f0bced45bc8cf116e7be6db30a
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133541549"
---
# <a name="restore-azure-managed-disks-using-azure-cli"></a>Azure CLI를 사용하여 Azure Managed Disks 복원

이 문서에서는 Azure CLI를 사용하여 Azure Backup이 만든 복원 지점에서 [Azure Managed Disks](../virtual-machines/managed-disks-overview.md)를 복원하는 방법을 설명합니다.

> [!IMPORTANT]
> CLI를 통해 Azure Managed Disks 백업 및 복원에 대한 지원은 미리 보기 상태이며 Az 2.15.0 버전 이상에서 확장으로 사용할 수 있습니다. **az dataprotection** 명령을 실행하면 확장이 자동으로 설치됩니다. 확장에 대해 [자세히 알아보세요](/cli/azure/azure-cli-extensions-overview).

현재 백업을 수행한 기존 원본 디스크를 교체하여 복원하는 OLR(원래 위치 복구) 옵션은 지원되지 않습니다. 복구 지점에서 복원하여 원본 디스크와 동일한 리소스 그룹 또는 다른 리소스 그룹에 새 디스크를 만들 수 있습니다. 이를 ALR(대체 위치 복구)이라고 합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- 새 디스크를 만드는 복원

- 복원 작업 상태 추적

예제에서 _testBkpVaultRG_ 리소스 그룹의 기존 백업 자격 증명 모음 _TestBkpVault_ 를 참조합니다.

## <a name="restore-to-create-a-new-disk"></a>새 디스크를 만드는 복원

### <a name="setting-up-permissions"></a>권한 설정

백업 자격 증명 모음은 관리 ID를 사용하여 다른 Azure 리소스에 액세스합니다. 백업에서 복원하려면 백업 자격 증명 모음의 관리 ID에 디스크를 복원할 리소스 그룹에 대한 권한 집합이 필요합니다.

백업 자격 증명 모음에는 시스템 할당 관리 ID가 사용됩니다. 이 ID는 리소스당 하나로 제한되며 이 리소스의 수명 주기에 연결됩니다. Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 관리 ID에 대한 권한을 부여할 수 있습니다. 관리 ID는 Azure 리소스에서만 사용할 수 있는 특수 유형의 서비스 주체입니다. [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보세요.

[여기](restore-managed-disks.md#restore-to-create-a-new-disk)에 설명된 대로 디스크가 복원/생성되는 대상 리소스 그룹에서 자격 증명 모음의 시스템 할당 관리 ID와 관련된 권한을 할당합니다.

### <a name="fetching-the-relevant-recovery-point"></a>관련 복구 지점 가져오기

[az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list) 명령을 사용하여 자격 증명 모음 내의 모든 백업 인스턴스를 나열한 다음, [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_show) 명령을 사용하여 관련 인스턴스를 페치합니다. 또는 확장된 시나리오의 경우 [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph)를 사용하여 자격 증명 모음 및 구독 간에 백업 인스턴스를 나열할 수 있습니다.

```azurecli-interactive
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDisk --datasource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk


[
  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
    "extendedLocation": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.Compute/disks",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
        "resourceLocation": "westus",
        "resourceName": "CLITestDisk",
        "resourceType": "Microsoft.Compute/disks",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": null,
      "datasourceAuthCredentials": null,
      "friendlyName": "CLITestDisk",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
        "policyParameters": {
          "dataStoreParametersList": [
            {
              "dataStoreType": "OperationalStore",
              "objectType": "AzureOperationalStoreParameters",
              "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/snapshotrg"
            }
          ]
        },
        "policyVersion": null
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded"
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "testBkpVaultRG",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "TestBkpVault",
    "zones": null
  }
]


```

인스턴스가 식별되면 [az dataprotection recovery-point list](/cli/azure/dataprotection/recovery-point?view=azure-cli-latest&preserve-view=true#az_dataprotection_recovery_point_list) 명령을 사용하여 관련 복구 지점을 페치합니다.

```azurecli-interactive
az dataprotection recovery-point list --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 -g testBkpVaultRG --vault-name TestBkpVault

{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166/recoveryPoints/5081ad8f1e6c4548ae89536d0d45c493",
"name": "5081ad8f1e6c4548ae89536d0d45c493",
"properties": {
"friendlyName": "0f598ced-cbfe-4169-b962-ee94b0210490",
"objectType": "AzureBackupDiscreteRecoveryPoint",
"policyName": "DiskPSPolicy2",
"policyVersion": null,
"recoveryPointDataStoresDetails": [
{
"creationTime": "2021-06-08T09:01:57.708319+00:00",
"expiryTime": "2021-06-15T09:01:57.708319+00:00",
"id": "c2ad4629-f2ef-49b6-b3f8-50f3eb5404f4",
"metaData": null,
"rehydrationExpiryTime": null,
"rehydrationStatus": null,
"state": "COMMITTED",
"type": "OperationalStore",
"visible": true
}
],
"recoveryPointId": "5081ad8f1e6c4548ae89536d0d45c493",
"recoveryPointTime": "2021-06-08T09:01:57.708319+00:00",
"recoveryPointType": "Incremental",
"retentionTagName": "Default",
"retentionTagVersion": "637553616953961153"
},
"resourceGroup": "testBkpVaultRG",
"systemData": null,
"type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
},
{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166/recoveryPoints/039322cc563049bcbdb77bd695d4c02c",
"name": "039322cc563049bcbdb77bd695d4c02c",
"properties": {
"friendlyName": "af6512b6-aa38-4966-b8e1-660c4eccdc0d",
"objectType": "AzureBackupDiscreteRecoveryPoint",
"policyName": "DiskPSPolicy2",
"policyVersion": null,
"recoveryPointDataStoresDetails": [
{
"creationTime": "2021-06-08T05:01:55.426507+00:00",
"expiryTime": "2021-06-15T05:01:55.426507+00:00",
"id": "c2ad4629-f2ef-49b6-b3f8-50f3eb5404f4",
"metaData": null,
"rehydrationExpiryTime": null,
"rehydrationStatus": null,
"state": "COMMITTED",
"type": "OperationalStore",
"visible": true
}
],
"recoveryPointId": "039322cc563049bcbdb77bd695d4c02c",
"recoveryPointTime": "2021-06-08T05:01:55.426507+00:00",
"recoveryPointType": "Incremental",
"retentionTagName": "Default",
"retentionTagVersion": "637553616953961153"
},
"resourceGroup": "testBkpVaultRG",
"systemData": null,
"type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
}
]
```

예를 들어 아래 쿼리는 최신 복구 지점을 반환합니다.

```azurecli-interactive
az dataprotection recovery-point list --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 -g testBkpVaultRG --vault-name TestBkpVault --query "[0].id"

"/subscriptions/62b829ee-7936-40c9-a1c9-47a93f9f3965/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/sarath-vault/backupInstances/clitest-clitest-3165cfe7-a932-11eb-9d24-9cfce85d4fae/recoveryPoints/5081ad8f1e6c4548ae89536d0d45c493"
```

### <a name="preparing-the-restore-request"></a>복원 요청 준비

[위에서](#setting-up-permissions) 자세히 설명한 것처럼 권한이 할당된 대상 리소스 그룹과 필요한 디스크 이름을 사용하여 만들 새 디스크의 ARM ID를 구성합니다. 다른 구독의 _targetrg_ 리소스 그룹에서 _CLITestDisk2_ 라는 디스크의 예제를 사용합니다.

```azurecli-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/CLITestDisk2
```

모든 관련 세부 정보를 사용하여 복원 요청을 준비하려면 [az dataprotection backup-instance restore initialize-for-data-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery) 명령을 사용합니다.

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDisk --restore-location southeastasia --source-datastore OperationalStore --recovery-point-id 5081ad8f1e6c4548ae89536d0d45c493 --target-resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/CLITestDisk2 > restore.json
```

```json
{
  "object_type": "AzureBackupRecoveryPointBasedRestoreRequest",
  "recovery_point_id": "77594ce0470849e79b86a6875b726dca",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Compute/disks",
      "object_type": "Datasource",
      "resource_id": "//subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/CLITestDisk2",
      "resource_location": "southeastasia",
      "resource_name": "CLITestDisk2",
      "resource_type": "Microsoft.Compute/disks",
      "resource_uri": ""
    },
    "object_type": "RestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}

```

또한 JSON 파일이 [az dataprotection backup-instance validate-for-restore](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_validate_for_restore) 명령을 사용하여 새 리소스를 생성하는 데 성공하는지 확인할 수도 있습니다.

```azurecli-interactive
az dataprotection backup-instance validate-for-restore -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 --restore-request-object restore.json
```

### <a name="trigger-the-restore"></a>복원 트리거

[az dataprotection backup-instance restore trigger](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_trigger) 명령을 사용하여 위에서 준비한 요청으로 복원을 트리거합니다.

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 --restore-request-object restore.json
```

## <a name="tracking-job"></a>작업 추적

[az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) 명령을 사용하여 모든 작업을 추적합니다. 모든 작업을 나열하고 특정 작업 세부 정보를 가져올 수 있습니다.

Az.ResourceGraph를 사용하여 모든 백업 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) 명령을 사용하여 모든 백업 자격 증명 모음에 있을 수 있는 관련 작업을 가져옵니다.

```azurepowershell-interactive
az dataprotection job list-from-resourcegraph --datasource-type AzureDisk --operation Restore
```

## <a name="next-steps"></a>다음 단계

[Azure Disk Backup FAQ](./disk-backup-faq.yml)