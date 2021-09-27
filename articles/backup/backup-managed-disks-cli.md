---
title: Azure CLI를 사용하여 Azure Managed Disks 백업
description: Azure CLI를 사용하여 Azure Managed Disks를 백업하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: ce1e4b3f88e844165581c95f74955de04686855b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659703"
---
# <a name="back-up-azure-managed-disks-using-azure-cli"></a>Azure CLI를 사용하여 Azure Managed Disks 백업

이 문서에서는 Azure CLI를 사용하여 [Azure Managed Disk](../virtual-machines/managed-disks-overview.md)를 백업하는 방법을 설명합니다.

> [!IMPORTANT]
> CLI를 통해 Azure Managed Disks 백업 및 복원에 대한 지원은 미리 보기 상태이며 Az 2.15.0 버전 이상에서 확장으로 사용할 수 있습니다. **az dataprotection** 명령을 실행하면 확장이 자동으로 설치됩니다. 확장에 대해 [자세히 알아보세요](/cli/azure/azure-cli-extensions-overview).

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- Backup 자격 증명 모음 만들기

- 백업 정책 만들기

- Azure Disk의 백업 구성

- 주문형 백업 작업 실행

Azure Disk Backup 지역 가용성, 지원되는 시나리오 및 제한 사항에 대한 정보는 [지원 매트릭스](disk-backup-support-matrix.md)를 참조하세요.

## <a name="create-a-backup-vault"></a>Backup 자격 증명 모음 만들기

Backup 자격 증명 모음은 Azure Database for PostgreSQL 서버 및 스토리지 계정의 Blob 및 Azure Disk와 같이 Azure Backup이 지원하는 다양한 최신 워크로드에 대한 백업 데이터를 보유하는 Azure의 스토리지 엔터티입니다. Backup 자격 증명 모음을 사용하면 관리 오버헤드를 최소화하면서 백업 데이터를 쉽게 구성할 수 있습니다. Backup 자격 증명 모음은 Azure의 Azure Resource Manager 모델을 기반으로 하며 백업 데이터를 보호할 수 있도록 하는 향상된 기능을 제공합니다.

Backup 자격 증명 모음을 만들기 전에 자격 증명 모음 내에서 데이터의 스토리지 중복도를 선택합니다. 그런 다음, 해당 스토리지 중복도 및 위치를 사용하여 Backup 자격 증명 모음을 만듭니다. 이 문서에서는 리소스 그룹 _testBkpVaultRG_ 아래에서 _westus_ 지역에 Backup 자격 증명 모음 _TestBkpVault_ 를 만듭니다. [az dataprotection vault create](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_create) 명령을 사용하여 Backup 자격 증명 모음을 만듭니다. [백업 자격 증명 모음 만들기](./backup-vault-overview.md#create-a-backup-vault)에 대해 자세히 알아보세요.

```azurecli-interactive
az dataprotection backup-vault create -g testBkpVaultRG --vault-name TestBkpVault -l westus --type SystemAssigned --storage-settings datastore-type="VaultStore" type="LocallyRedundant"

{
  "eTag": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault",
  "identity": {
    "principalId": "2ca1d5f7-38b3-4b61-aa45-8147d7e0edbc",
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "SystemAssigned"
  },
  "location": "westus",
  "name": "TestBkpVault",
  "properties": {
    "provisioningState": "Succeeded",
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  },
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "tags": null,
  "type": "Microsoft.DataProtection/backupVaults"
}
```

자격 증명 모음을 만든 후에는 Azure 디스크를 보호하는 백업 정책을 만들어 보겠습니다.

## <a name="create-a-backup-policy"></a>백업 정책 만들기

Azure Disk Backup에 대한 Backup 정책의 내부 구성 요소를 이해하려면 [az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template) 명령을 사용하여 정책 템플릿을 검색합니다. 이 명령은 지정된 데이터 원본 형식에 관한 기본 정책 템플릿을 반환합니다. 이 정책 템플릿을 사용하여 새 정책을 만듭니다.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDisk

{
  "datasourceTypes": [
    "Microsoft.Compute/disks"
  ],
  "name": "DiskPolicy",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "backupParameters": {
        "backupType": "Incremental",
        "objectType": "AzureBackupParams"
      },
      "dataStore": {
        "dataStoreType": "OperationalStore",
        "objectType": "DataStoreInfoBase"
      },
      "name": "BackupHourly",
      "objectType": "AzureBackupRule",
      "trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2020-04-05T13:00:00+00:00/PT4H"
          ]
        },
        "taggingCriteria": [
          {
            "isDefault": true,
            "tagInfo": {
              "id": "Default_",
              "tagName": "Default"
            },
            "taggingPriority": 99
          }
        ]
      }
    },
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
  ]
}

```

정책 템플릿은 트리거(백업을 트리거하는 항목 결정)와 수명 주기(백업을 삭제/복사/이동할 시기 결정)로 구성됩니다. Azure Disk Backup에서 트리거의 기본값은 4시간(PT4H)마다 예약된 매시간 트리거이며 각 백업을 7일 동안 유지합니다.

**예약된 트리거:**

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2020-04-05T13:00:00+00:00/PT4H"
          ]
        }

```

**기본 보존 수명 주기:**

```json
"lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ]
```

Azure Disk Backup은 하루에 여러 백업을 제공합니다. 더 자주 백업해야 하는 경우 **시간별** 백업 빈도를 선택하여 4, 6, 8 또는 12시간 간격으로 백업을 수행할 수 있습니다. 백업은 선택한 **시간** 간격을 기준으로 예약됩니다.

예를 들어 **4시간마다** 를 선택하면 대략 4시간 간격으로 백업이 수행되므로 백업이 하루 동안 균등하게 분산됩니다. 하루에 한 번 백업하면 **매일** 백업 빈도를 선택합니다. 매일 백업 빈도에서 백업을 수행할 시간을 지정할 수 있습니다.

>[!IMPORTANT]
>시간은 백업이 완료되는 시간이 아니라 백업 시작 시간을 나타냅니다.

백업 작업을 완료하는 데 필요한 시간은 디스크 크기 및 연속 백업 간의 변동 비율을 비롯한 다양한 요인에 따라 달라집니다. 그러나 Azure Disk Backup은 프로덕션 애플리케이션 성능에 영향을 주지 않는 [증분 스냅샷](../virtual-machines/disks-incremental-snapshots.md)을 사용하는 에이전트 없는 백업입니다.

   >[!NOTE]
   >선택한 자격 증명 모음에 전역 중복 설정이 있을 수 있지만, 현재 Azure Disk Backup은 스냅샷 데이터 저장소만 지원합니다. 모든 백업은 구독의 리소스 그룹에 저장되며 Backup 자격 증명 모음 스토리지로 복사되지 않습니다.

정책 만들기에 관한 자세한 내용은 [Azure Disk Backup 정책](backup-managed-disks.md#create-backup-policy) 설명서를 참조하세요.

템플릿이 JSON 파일로 다운로드되면 필요에 따라 예약 및 보존을 위해 편집할 수 있습니다. 그런 다음, 결과 JSON을 사용하여 새 정책을 만듭니다. 시간별 빈도 또는 보존 기간을 편집하려면 [az dataprotection backup-policy trigger set](/cli/azure/dataprotection/backup-policy/trigger?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_trigger_set) 및/또는 [az dataprotection backup-policy retention-rule set](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_retention_rule_set) 명령을 사용합니다. 정책 JSON에 원하는 값이 모두 있으면 계속해서 [az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_create) 명령을 사용하여 정책 개체에서 새 정책을 만듭니다.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDisk > policy.json
az dataprotection backup-policy create -g testBkpVaultRG --vault-name TestBkpVault -n mypolicy --policy policy.json

{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/mypolicy",
"name": "mypolicy",
"properties": {
"datasourceTypes": [
"Microsoft.Compute/disks"
],
"objectType": "BackupPolicy",
"policyRules": [
{
"backupParameters": {
"backupType": "Incremental",
"objectType": "AzureBackupParams"
},
"dataStore": {
"dataStoreType": "OperationalStore",
"objectType": "DataStoreInfoBase"
},
"name": "BackupHourly",
"objectType": "AzureBackupRule",
"trigger": {
"objectType": "ScheduleBasedTriggerContext",
"schedule": {
"repeatingTimeIntervals": [
"R/2020-04-05T13:00:00+00:00/PT4H"
]
},
"taggingCriteria": [
{
"criteria": null,
"isDefault": true,
"tagInfo": {
"eTag": null,
"id": "Default_",
"tagName": "Default"
},
"taggingPriority": 99
}
]
}
},
{
"isDefault": true,
"lifecycles": [
{
"deleteAfter": {
"duration": "P7D",
"objectType": "AbsoluteDeleteOption"
},
"sourceDataStore": {
"dataStoreType": "OperationalStore",
"objectType": "DataStoreInfoBase"
},
"targetDataStoreCopySettings": null
}
],
"name": "Default",
"objectType": "AzureRetentionRule"
}
]
},
"resourceGroup": "testBkpVaultRG",
"systemData": null,
"type": "Microsoft.DataProtection/backupVaults/backupPolicies"
}
```

## <a name="configure-backup"></a>백업 구성

자격 증명 모음 및 정책을 만들면 Azure Disk를 보호하기 위해 고려해야 할 세 가지 중요한 사항이 있습니다.

### <a name="key-entities-involved"></a>관련된 주요 엔터티

#### <a name="disk-to-be-protected"></a>보호할 디스크

보호할 디스크의 ARM ID 및 위치를 가져옵니다. 디스크의 식별자 역할을 합니다. 다른 구독에서 리소스 그룹 _diskrg_ 아래에 있는 _CLITestDisk_ 라는 디스크의 예를 사용합니다.

```azurecli-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
```

#### <a name="snapshot-resource-group"></a>스냅샷 리소스 그룹

디스크 스냅샷은 구독 내의 리소스 그룹에 저장됩니다. 지침으로 Azure Backup 서비스에서 사용할 스냅샷 데이터 저장소로 전용 리소스 그룹을 만드는 것이 좋습니다. 전용 리소스 그룹을 사용하면 리소스 그룹에 대한 액세스 권한을 제한할 수 있기 때문에 안전하고 쉽게 백업 데이터를 관리할 수 있습니다. 디스크 스냅샷을 배치할 리소스 그룹의 ARM ID 기록해 두기

```azurecli-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Backup 자격 증명 모음

스냅샷을 트리거하고 수명 주기를 관리할 수 ​​있으려면 Backup 자격 증명 모음에 디스크 및 스냅샷 리소스 그룹에 관한 권한이 있어야 합니다. 자격 증명 모음의 시스템이 할당한 관리 ID는 해당 권한을 할당하는 데 사용됩니다. [az dataprotection backup-vault update](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_update) 명령을 사용하여 Recovery Services 자격 증명 모음에 대한 시스템 할당 관리 ID를 사용하도록 설정합니다.

```azurecli-interactive
az dataprotection backup-vault update -g testBkpVaultRG --vault-name TestBkpVault --type SystemAssigned
```

### <a name="assign-permissions"></a>권한 할당

RBAC를 통해 자격 증명 모음(자격 증명 모음 MSI로 표시) 및 관련 디스크 및/또는 디스크 RG에 몇 가지 권한을 할당해야 합니다. 이 작업은 Azure Portal 또는 CLI를 통해 수행할 수 있습니다. 관련 권한을 할당하려면 [관리 디스크의 백업을 구성하기 위한 필수 구성요약을 참조하세요.](backup-managed-disks-ps.md#assign-permissions)

### <a name="prepare-the-request"></a>요청 준비

모든 관련 사용 권한을 설정하면 백업 구성은 2개의 단계로 수행됩니다. 먼저 [az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_initialize) 명령을 사용하여 관련 자격 증명 모음, 정책, 디스크 및 스냅샷 리소스 그룹을 사용하여 관련 요청을 준비합니다. initialize 명령은 JSON 파일을 반환합니다. 그러면 사용자는 스냅샷 리소스 그룹 값을 업데이트해야 합니다. 그런 다음, [az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_create) 명령을 사용하여 디스크를 보호하기 위한 요청을 제출합니다.

```azurecli-interactive
az dataprotection backup-instance initialize --datasource-type AzureDisk  -l southeastasia --policy-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/mypolicy" --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk" > backup_instance.json
```

JSON 파일을 열고 ```data_store_parameters_list``` 섹션 아래의 ``` resource_group_id ```에서 **스냅샷 리소스 그룹 ID** 를 편집합니다.

```json
{
  "backup_instance_name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "properties": {
    "data_source_info": {
      "datasource_type": "Microsoft.Compute/disks",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
      "resource_location": "southeastasia",
      "resource_name": "CLITestDisk",
      "resource_type": "Microsoft.Compute/disks",
      "resource_uri": ""
    },
    "data_source_set_info": null,
    "object_type": "BackupInstance",
    "policy_info": {
      "policy_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
      "policy_parameters": {
        "data_store_parameters_list": [
          {
            "data_store_type": "OperationalStore",
            "object_type": "AzureOperationalStoreParameters",
            "resource_group_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/snapshotrg"
          }
        ]
      }
    }
  }
}
```

> [!NOTE]
> 백업 인스턴스 이름은 클라이언트에서 생성되므로 고유한 값이 됩니다. 데이터 원본 이름 및 고유 GUID를 기준으로 합니다. 백업 인스턴스를 나열하면 백업 인스턴스의 이름 및 관련 데이터 원본 이름을 확인할 수 있습니다.

편집된 JSON 파일을 사용하여 Azure Managed Disk의 백업 인스턴스를 만듭니다.

```azurecli-interactive
az dataprotection backup-instance create -g testBkpVaultRG --vault-name TestBkpVault --backup-instance backup_instance.json


{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "properties": {
    "currentProtectionState": "ProtectionConfigured",
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/disks",
      "objectType": "Datasource",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
      "resourceLocation": "southeastasia",
      "resourceName": "CLITestDisk",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
    },
    "dataSourceSetInfo": null,
    "friendlyName": "CLITestDisk",
    "objectType": "BackupInstance",
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/sarath-rg"
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
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupInstances"
}
```

백업 인스턴스가 만들어지면 예약된 정책을 기다리지 않으려는 경우 주문형 백업을 트리거할 수 있습니다.

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

[az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true) 명령을 사용하여 자격 증명 모음 내의 모든 백업 인스턴스를 나열한 다음, [az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true) 명령을 사용하여 관련 인스턴스를 가져옵니다. 또는 확장된 시나리오의 경우 [az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph) 명령을 사용하여 자격 증명 모음 및 구독 간에 백업 인스턴스를 나열할 수 있습니다.

```azurecli-interactive
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDisk --datasource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk


[
  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
    "extendedLocation": null,
    "id": "//subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
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

백업을 트리거하는 동안 보존 규칙을 지정할 수 있습니다. 정책에서 보존 규칙을 보려면 보존 규칙의 정책 JSON을 확인합니다. 아래 예제에서는 이름이 _dafault_ 인 규칙이 표시되며 주문형 백업에 해당 규칙을 사용합니다.

```JSON
{
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
```

[az dataprotection backup-instance adhoc-backup](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_adhoc_backup) 명령을 사용하여 주문형 백업을 트리거합니다.

```azurecli-interactive
az dataprotection backup-instance adhoc-backup --name "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166" --rule-name "Default" --resource-group "000pikumar" --vault-name "PratikPrivatePreviewVault1"
```

## <a name="tracking-jobs"></a>작업 추적

[az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) 명령을 사용하여 모든 작업을 추적합니다. 모든 작업을 나열하고 특정 작업 세부 정보를 가져올 수 있습니다.

Az.ResourceGraph를 사용하여 모든 백업 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) 명령을 사용하여 모든 백업 자격 증명 모음에 있을 수 있는 관련 작업을 가져옵니다.

```azurepowershell-interactive
az dataprotection job list-from-resourcegraph --datasource-type AzureDisk --status Completed
```

## <a name="next-steps"></a>다음 단계

[Azure CLI를 사용하여 Azure Managed Disks 복원](restore-managed-disks-cli.md)
