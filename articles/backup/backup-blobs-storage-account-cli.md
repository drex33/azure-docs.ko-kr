---
title: Azure CLI를 사용하여 Azure Blob 백업
description: Azure CLI를 사용하여 Azure Blob을 백업하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 08/06/2021
ms.openlocfilehash: e8ffd1d369df816bd1020b0b2ec2c1696e6c433e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122530015"
---
# <a name="back-up-azure-blobs-in-a-storage-account-using-azure-cli"></a>Azure CLI를 사용하여 스토리지 계정의 Azure Blob 백업

이 문서에서는 Azure CLI를 사용하여 [Azure Blob](./blob-backup-overview.md)을 백업하는 방법을 설명합니다.

> [!IMPORTANT]
> CLI를 통한 Azure Blob 백업 및 복원에 대한 지원은 미리 보기로 제공되며 Az 2.15.0 버전 이상에서 확장으로 사용할 수 있습니다. **az dataprotection** 명령을 실행하면 확장이 자동으로 설치됩니다. 확장에 대해 [자세히 알아보세요](/cli/azure/azure-cli-extensions-overview).

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

- 시작하기 전에

- Backup 자격 증명 모음 만들기

- 백업 정책 만들기

- Azure Blob의 백업 구성

- 주문형 백업 작업 실행

Azure Blob 지역 가용성, 지원되는 시나리오 및 제한 사항에 대한 자세한 내용은 [지원 매트릭스](blob-backup-support-matrix.md)를 참조하세요.

## <a name="before-you-start"></a>시작하기 전에

시작하기 전에 [필수 조건](./blob-backup-configure-manage.md#before-you-start) 및 [지원 매트릭스](./blob-backup-support-matrix.md)를 참조하세요.

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

> [!IMPORTANT]
> 자격 증명 모음의 Backup 스토리지 중복성이 표시되지만 Blob의 운영 백업에는 중복성이 적용되지 않습니다. 이는 백업은 본질적으로 로컬이고 데이터가 Backup 자격 증명 모음에 저장되지 않기 때문입니다. 여기서 Backup 자격 증명 모음은 스토리지 계정에서 블록 Blob의 보호를 관리하는 데 도움이 되는 관리 엔터티입니다.

자격 증명 모음을 만든 후에 스토리지 계정에서 Azure Blob을 보호하는 Backup 정책을 만들어 보겠습니다.

## <a name="create-a-backup-policy"></a>백업 정책 만들기

> [!IMPORTANT]
> 정책을 만들고 Azure Blob의 백업을 구성하기 전에 [이 섹션](blob-backup-configure-manage.md#before-you-start)을 읽어보세요.

Azure Blob 백업에 대한 Backup 정책의 내부 구성 요소를 이해하려면 [az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template) 명령을 사용하여 정책 템플릿을 검색합니다. 이 명령은 지정된 데이터 원본 형식에 관한 기본 정책 템플릿을 반환합니다. 이 정책 템플릿을 사용하여 새 정책을 만듭니다.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureBlob

{
  "datasourceTypes": [
    "Microsoft.Storage/storageAccounts/blobServices"
  ],
  "name": "BlobPolicy1",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P30D",
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

정책 템플릿은 수명 주기(백업을 삭제/복사/이동할 시기 결정)만으로 구성됩니다. Blob의 운영 백업은 본질적으로 연속적이므로 백업을 수행하기 위한 일정이 필요하지 않습니다.

```json
"policyRules": [
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P30D",
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
```

> [!NOTE]
> 오랜 기간에 걸쳐 복원하면 복원 작업을 완료하는 데 시간이 더 오래 걸립니다. 또한 일련의 데이터를 복원하는 데 걸리는 시간은 복원 기간 중에 수행된 쓰기 및 삭제 작업의 건수에 따라 달라집니다. 예를 들어 100만 개의 개체에 하루에 3천 개의 개체가 추가되고 1천 개의 개체가 삭제되는 계정의 경우 30일 전의 시점으로 복원하는 데 약 2시간이 소요됩니다.<br><br>변동률이 이와 같은 계정의 보존 기간 및 복원 범위는 과거 90일을 초과하지 않는 것이 좋습니다.

정책 JSON에 원하는 값이 모두 있으면 계속해서 [az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_create) 명령을 사용하여 정책 개체에서 새 정책을 만듭니다.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureBlob > policy.json
az dataprotection backup-policy create -g testBkpVaultRG --vault-name TestBkpVault -n BlobBackup-Policy --policy policy.json

{
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy",
    "name": "BlobBackup-Policy",
    "properties": {
      "datasourceTypes": [
        "Microsoft.Storage/storageAccounts/blobServices"
      ],
      "objectType": "BackupPolicy",
      "policyRules": [
        {
          "isDefault": true,
          "lifecycles": [
            {
              "deleteAfter": {
                "duration": "P2D",
                "objectType": "AbsoluteDeleteOption"
              },
              "sourceDataStore": {
                "dataStoreType": "OperationalStore",
                "objectType": "DataStoreInfoBase"
              },
              "targetDataStoreCopySettings": []
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

자격 증명 모음과 정책이 생성된 후 스토리지 계정 내 모든 Azure Blob을 보호하기 위해 고려해야 할 두 가지 중요한 사항이 있습니다.

### <a name="key-entities-involved"></a>관련된 주요 엔터티

#### <a name="storage-account-that-contains-the-blobs-to-be-protected"></a>보호할 Blob이 포함된 스토리지 계정

보호할 Blob이 포함된 스토리지 계정의 Azure Resource Manager ID를 페치합니다. 이 ID는 스토리지 계정의 식별자로 사용됩니다. 동남 아시아 지역에 있는 다른 구독의 리소스 그룹 _blobrg_ 아래에서 _CLITestSA_ 라는 스토리지 계정의 예제를 사용합니다.

```azurecli-interactive
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
```

#### <a name="backup-vault"></a>Backup 자격 증명 모음

스토리지 계정 내에 있는 Blob에서 백업을 사용하려면 백업 자격 증명 모음에는 스토리지 계정에 대한 권한이 있어야 합니다. 자격 증명 모음의 시스템이 할당한 관리 ID는 해당 권한을 할당하는 데 사용됩니다.

### <a name="assign-permissions"></a>권한 할당

RBAC를 통해 자격 증명 모음(자격 증명 모음 MSI로 표시) 및 관련 스토리지 계정에 몇 가지 권한을 할당해야 합니다. 이 작업은 포털 또는 PowerShell을 통해 수행할 수 있습니다. 모든 [관련 권한](blob-backup-configure-manage.md#grant-permissions-to-the-backup-vault-on-storage-accounts)에 관해 자세히 알아봅니다.

### <a name="prepare-the-request"></a>요청 준비

모든 관련 사용 권한을 설정하면 백업 구성은 2개의 단계로 수행됩니다. 먼저 [az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_initialize) 명령을 사용하여 관련 자격 증명 모음, 정책, 스토리지 계정을 사용하여 관련 요청을 준비합니다. 그런 다음, [az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_create) 명령을 사용하여 디스크를 보호하기 위한 요청을 제출합니다.

```azurecli-interactive
az dataprotection backup-instance initialize --datasource-type AzureBlob  -l southeastasia --policy-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy" --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA" > backup_instance.json
```

```azurecli-interactive
az dataprotection backup-instance create -g testBkpVaultRG --vault-name TestBkpVault --backup-instance backup_instance.json

{
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "name": "CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
        "resourceLocation": "southeastasia",
        "resourceName": "CLITestSA",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
      },
      "dataSourceSetInfo": null,
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
    "resourceGroup": "testBkpVaultRG",
    "systemData": null,
    "type": "Microsoft.DataProtection/backupVaults/backupInstances"
  }
```

> [!IMPORTANT]
> 스토리지 계정이 Blob 백업용으로 구성되면 변경 피드 및 삭제 잠금과 몇 가지 기능이 영향을 받습니다. [자세히 알아보기](blob-backup-configure-manage.md#effects-on-backed-up-storage-accounts).

## <a name="next-steps"></a>다음 단계

[Azure CLI를 사용하여 Azure Blob 복원 ](restore-blobs-storage-account-cli.md)