---
title: Azure CLI 사용하여 장기 보존으로 Azure Database for PostgreSQL 백업
description: Azure CLI 사용하여 Azure Database for PostgreSQL 백업하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/24/2021
ms.custom: devx-track-azurecli
ms.author: v-amallick
ms.openlocfilehash: 2f032091eb6a0e7807046bb59dfc37709fb4c272
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717775"
---
# <a name="back-up-azure-postgresql-databases-using-azure-cli"></a>Azure CLI 사용하여 Azure PostgreSQL 데이터베이스 백업

이 문서에서는 Azure CLI 사용하여 [Azure PostgreSQL 데이터베이스를](../postgresql/overview.md#azure-database-for-postgresql---single-server) 백업하는 방법을 설명합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

-  Backup 자격 증명 모음 만들기
-  백업 정책 만들기
-  Azure PostgreSQL 데이터베이스의 백업 구성
-  주문형 백업 작업 실행

informgreSQL 데이터베이스 지원 시나리오 및 제한은 [지원 매트릭스를 참조하세요.](backup-azure-database-postgresql-overview.md#support-matrix)

## <a name="create-a-backup-vault"></a>Backup 자격 증명 모음 만들기

Backup 자격 증명 모음은 Azure Database for PostgreSQL 서버, 스토리지 계정의 Blob 및 Azure 디스크와 같이 Azure Backup 지원하는 다양한 새 워크로드에 대한 백업 데이터를 저장하는 Azure의 스토리지 엔터티입니다. 백업 자격 증명 모음은 관리 오버헤드를 최소화하면서 백업 데이터를 구성하는 데 도움이 됩니다. Backup 자격 증명 모음은 Azure의 Azure Resource Manager 모델을 기반으로 하며 백업 데이터를 보호할 수 있도록 하는 향상된 기능을 제공합니다.

Backup 자격 증명 모음을 만들기 전에 자격 증명 모음 내에서 데이터의 스토리지 중복도를 선택합니다. 그런 다음, 해당 스토리지 중복도 및 위치를 사용하여 Backup 자격 증명 모음을 만듭니다.

이 문서에서는 리소스 그룹 _testBkpVaultRG_ 아래에서 _westus_ 지역에 Backup 자격 증명 모음 _TestBkpVault_ 를 만듭니다. [az dataprotection vault create](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_create) 명령을 사용하여 Backup 자격 증명 모음을 만듭니다. [백업 자격 증명 모음 만들기](./backup-vault-overview.md#create-a-backup-vault)에 대해 자세히 알아보세요.

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

자격 증명 모음을 만든 후 Azure PostgreSQL 데이터베이스를 보호하는 Backup 정책을 만들어 보겠습니다.

## <a name="create-a-backup-policy"></a>백업 정책 만들기

### <a name="understanding-postgresql-backup-policy"></a>PostGreSQL 백업 정책 이해

디스크 백업은 하루에 여러 백업을 제공하고 Blob 백업은 트리거 없이 _연속_ 백업이지만 PostgreSQL 백업은 보관 보호를 제공합니다. 자격 증명 모음에 처음 전송된 백업 데이터는 정의된 규칙 또는 _수명 주기_ 에 따라 _보관_ 계층으로 이동할 수 있습니다. 이 컨텍스트에서 PostgreSQL에 대한 백업 정책 개체를 살펴보겠습니다.

- PolicyRule
   -  BackupRule
      - BackupParameter
        -  BackupType(이 경우 전체 데이터베이스 백업)
        -  초기 데이터 저장소(백업이 처음에 있는 위치)
        -  트리거(백업이 트리거되는 방법)
           -  일정 기반
           -  기본 태그 지정 조건(예약된 모든 백업에 대한 기본 '태그'입니다. 이 태그는 백업을 보존 규칙에 연결합니다.
   -  기본 보존 규칙(기본적으로 초기 데이터 저장소의 모든 백업에 적용되는 규칙)

따라서 이 개체는 트리거되는 백업 유형, 트리거되는 방법(일정을 통해), 태그가 지정된 백업, 백업 위치(데이터 저장소) 및 데이터 저장소에 있는 백업 데이터의 수명 주기를 정의합니다. PostgreSQL의 기본 PowerShell 개체는 매주 *전체* 백업을 트리거하도록 하며, 자격 증명 모음에 도달하여 3개월 동안 저장됩니다.

*보관* 계층을 정책에 추가하려는 경우 데이터를 자격 증명 모음에서 보관으로 이동할 시기, 데이터가 보관에 유지되는 기간 및 _예약된_ 백업 중 보관 가능한 로 태그를 지정해야 하는 시점을 결정해야 합니다. 따라서 백업 데이터의 수명 주기를 자격 증명 모음 데이터 저장소에서 보관 데이터 저장소로 정의하고 보관 데이터 저장소에 보관할 기간을  지정하는 *보존 규칙* 를 추가해야 합니다. 그런 다음 예약된 백업을 _보관할 수_ 있는 것으로 표시하는 *태그를* 추가해야 합니다.

결과 PowerShell 개체는 다음과 같습니다.

-  PolicyRule
   -  BackupRule
      - BackupParameter
        -  BackupType(이 경우 전체 데이터베이스 백업)
        -  초기 데이터 저장소(백업이 처음에 있는 위치)
        -  트리거(백업이 트리거되는 방법)
           -  일정 기반
           -  기본 태그 지정 조건(예약된 모든 백업에 대한 기본 '태그'입니다. 이 태그는 백업을 보존 규칙에 연결합니다.
           -  이름이 'X'인 새 보존 규칙에 대한 새 태그 지정 조건
   -  기본 보존 규칙(기본적으로 초기 데이터 저장소의 모든 백업에 적용되는 규칙)
   -  'X'로 명명된 새 보존 규칙
      -  수명 주기
         -  원본 데이터 저장소
         -  원본 데이터 저장소에서 기간 후 삭제
         -  대상 데이터 저장소에 복사

### <a name="retrieve-the-policy-template"></a>정책 템플릿 검색

Azure PostgreSQL 데이터베이스 백업에 대한 Backup 정책의 내부 구성 요소를 이해하려면 [az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template) 명령을 사용하여 정책 템플릿을 검색합니다. 이 명령은 지정된 데이터 원본 형식에 관한 기본 정책 템플릿을 반환합니다. 이 정책 템플릿을 사용하여 새 정책을 만듭니다.

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDatabaseForPostgreSQL
{
  "datasourceTypes": [
    "Microsoft.DBforPostgreSQL/servers/databases"
  ],
  "name": "OssPolicy1",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "backupParameters": {
        "backupType": "Full",
        "objectType": "AzureBackupParams"
      },
      "dataStore": {
        "dataStoreType": "VaultStore",
        "objectType": "DataStoreInfoBase"
      },
      "name": "BackupWeekly",
      "objectType": "AzureBackupRule",
      "trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T06:30:00+00:00/P1W"
          ],
          "timeZone": "UTC"
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
            "duration": "P3M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": []
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
  ]
}
```

정책 템플릿은 트리거(백업을 트리거하는 항목 결정)와 수명 주기(백업을 삭제/복사/이동할 시기 결정)로 구성됩니다. Azure PostgreSQL 데이터베이스 백업에서 트리거의 기본값은 예약된 매주 트리거(7일마다 백업 1개)이며 각 백업을 3개월 동안 보존합니다.

**예약된 트리거:**

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T06:30:00+00:00/P1W"
          ],
          "timeZone": "UTC"
        }
```

**기본 보존 규칙 수명 주기:**

```json
 {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P3M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": []
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
```

### <a name="modifying-the-policy-template"></a>정책 템플릿 수정

> [!IMPORTANT]
> Azure PowerShell _개체를_ 준비 위치로 사용하여 모든 수정을 수행할 수 있습니다. Azure CLI 개체 개념이 없으면 파일을 사용해야 *합니다.* 각 편집 작업은 새 파일로 리디렉션되어야 합니다. 여기서 콘텐츠는 입력 파일에서 읽고 출력 파일로 다시 전달됩니다. 나중에 스크립트에서 를 사용하는 동안 필요에 따라 파일 이름을 바꿀 수 있습니다.

#### <a name="modify-the-schedule"></a>일정 수정

기본 정책 템플릿은 매주 한 번씩 백업을 제공합니다. 백업 일정이 매주 며칠씩 발생하도록 수정할 수 있습니다. 일정을 수정하려면 [az dataprotection backup-policy trigger set](/cli/azure/dataprotection/backup-policy/trigger?view=azure-cli-latest#az_dataprotection_backup_policy_trigger_set&preserve-view=true) 명령을 사용합니다.

다음 예에서는 매주 일요일, 수요일 및 금요일마다 백업하도록 주별 백업을 수정합니다. 일정 날짜 배열은 날짜를 언급하고 해당 날짜의 요일은 요일로 간주합니다. 또한 이러한 일정이 매주 반복하도록 지정해야 합니다. 따라서 일정 간격은 "1"이고 간격 유형은 "매주"입니다.

```azurecli
az dataprotection backup-policy trigger create-schedule --interval-type Weekly --interval-count 1 --schedule-days 2021-08-15T22:00:00 2021-08-18T22:00:00 2021-08-20T22:00:00
[
  "R/2021-08-15T22:00:00+00:00/P1W",
  "R/2021-08-18T22:00:00+00:00/P1W",
  "R/2021-08-20T22:00:00+00:00/P1W"
]

az dataprotection backup-policy trigger set --policy .\OSSPolicy.json  --schedule R/2021-08-15T22:00:00+00:00/P1W R/2021-08-18T22:00:00+00:00/P1W R/2021-08-20T22:00:00+00:00/P1W > EditedOSSPolicy.json
```

#### <a name="add-a-new-retention-rule"></a>새 보존 규칙 추가

*보관* 보호를 추가하려면 아래와 같이 정책 템플릿을 수정해야 합니다.

기본 템플릿에는 기본 보존 규칙에 따라 초기 데이터 저장소에 대한 수명 주기가 있습니다. 이 시나리오에서 규칙은 3개월 후에 백업 데이터를 삭제한다고 표시합니다. 데이터가 *보관* 데이터 *저장소로 이동되는* 시기를 정의하는 새 보존 규칙을 추가해야 합니다. 즉, 백업 데이터가 먼저 보관 데이터 저장소에 복사된 다음 자격 증명 모음 데이터 저장소에서 삭제됩니다. 또한 규칙은 데이터가 *보관* 데이터 저장소에 보관되는 기간도 정의해야 합니다. [az dataprotection backup-policy retention-rule create-lifecycle](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest#az_dataprotection_backup_policy_retention_rule_create_lifecycle&preserve-view=true) 명령을 사용하여 새 수명 주기를 만들고 [az dataprotection backup-policy retention-rule set](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest#az_dataprotection_backup_policy_retention_rule_set&preserve-view=true) 명령을 사용하여 새 규칙 또는 기존 규칙에 연결합니다.

다음 예제에서는 매월 첫 번째 성공한 백업을 6개월 동안 자격 증명 모음에 보관하고 보관 계층으로 이동한 후 24개월 동안 보관 계층에 보관해야 하는 *월별이라는* 새 보존 규칙을 만듭니다.

```azurecli
az dataprotection backup-policy retention-rule create-lifecycle --retention-duration-count 6 --retention-duration-type Months --source-datastore VaultStore --target-datastore ArchiveStore --copy-option CopyOnExpiryOption > VaultToArchiveLifeCycle.JSON

az dataprotection backup-policy retention-rule create-lifecycle --retention-duration-count 24 --retention-duration-type Months -source-datastore ArchiveStore > OnArchiveLifeCycle.JSON

az dataprotection backup-policy retention-rule set --lifecycles .\VaultToArchiveLifeCycle.JSON .\OnArchiveLifeCycle.JSON --name Monthly --policy .\EditedOSSPolicy.JSON > AddedRetentionRulePolicy.JSON
```

#### <a name="add-a-tag-and-the-relevant-criteria"></a>태그 및 관련 조건 추가

보존 규칙이 만들어지면 Backup 정책의 *트리거* 속성에 해당 *태그를* 만들어야 합니다. [az dataprotection backup-policy tag create-absolute-criteria](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_create_absolute_criteria&preserve-view=true) 명령을 사용하여 새 태그 지정 조건을 만들고 [az dataprotection backup-policy tag set](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_set&preserve-view=true) 명령을 사용하여 기존 태그를 업데이트하거나 새 태그를 만듭니다.

다음 예제에서는 적용할 해당 보존 규칙과 동일한 이름으로 조건(월의 첫 번째 성공적인 백업)과 함께 새 *태그를* 만듭니다.

이 예제에서는 태그 조건의 이름을 *매월* 로 지정해야 합니다.

```azurecli
az dataprotection backup-policy tag create-absolute-criteria --absolute-criteria FirstOfMonth > tagCriteria.JSON
az dataprotection backup-policy tag set --criteria .\tagCriteria.JSON --name Monthly --policy .\AddedRetentionRulePolicy.JSON > AddedRetentionRuleAndTag.JSON
```

일정이 매주 여러 백업(위 예제에 지정된 대로 매주 일요일, 수요일, 목요일)이고 일요일 및 금요일 백업을 보관하려는 경우 [az dataprotection backup-policy tag create-generic-criteria](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_create_generic_criteria&preserve-view=true) 명령을 사용하여 태그 지정 조건을 다음과 같이 변경할 수 있습니다.

```azurecli
az dataprotection backup-policy tag create-generic-criteria --days-of-week Sunday Friday > tagCriteria.JSON
az dataprotection backup-policy tag set --criteria .\tagCriteria.JSON --name Monthly --policy .\AddedRetentionRulePolicy.JSON > AddedRetentionRuleAndTag.JSON
```

### <a name="create-a-new-postgresql-backup-policy"></a>새 PostgreSQL 백업 정책 만들기

요구 사항에 따라 템플릿이 수정되면 [az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest#az_dataprotection_backup_policy_create&preserve-view=true) 명령을 사용하여 수정된 템플릿을 사용하여 정책을 만듭니다.

```azurecli
az dataprotection backup-policy create --backup-policy-name FinalOSSPolicy --policy AddedRetentionRuleAndTag.JSON --resource-group testBkpVaultRG --vault-name TestBkpVault
```

## <a name="configure-backup"></a>백업 구성

자격 증명 모음 및 정책이 만들어지면 Azure PostgreSQL 데이터베이스를 보호하기 위해 고려해야 하는 세 가지 중요한 지점이 있습니다.

### <a name="key-entities-involved"></a>관련된 주요 엔터티

#### <a name="postgresql-database-to-be-protected"></a>보호할 PostGreSQL 데이터베이스

보호할 PostgreSQL의 AZURE RESOURCE MANAGER ID(ARM ID)를 가져옵니다. 데이터베이스의 식별자로 사용됩니다. 다른 구독의 리소스 그룹 **ossrg에** 있는 PostgreSQL 서버 **testposgresql** 아래에 **empdb11이라는** 데이터베이스의 예를 사용합니다.

다음 예제에서는 bash를 사용합니다.

```azurecli
ossId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossrg/providers/Microsoft.DBforPostgreSQL/servers/archive-postgresql-ccy/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Azure Key Vault

Azure Backup 서비스는 PostgreSQL 데이터베이스에 연결할 사용자 이름과 암호를 저장하지 않습니다. 대신 백업 관리자가 *키를 키* 자격 증명 모음에 시드한 다음 Backup 서비스가 키 자격 증명 모음에 액세스하고 키를 읽은 다음 데이터베이스에 액세스해야 합니다. 관련 키의 비밀 식별자를 확인합니다.

다음 예제에서는 bash를 사용합니다.

```azure-cli
keyURI="https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>Backup 자격 증명 모음

백업 자격 증명 모음은 PostgreSQL 서버에 연결한 다음 키 자격 증명 모음에 있는 키를 통해 데이터베이스에 액세스해야 합니다. 따라서 PostgreSQL 서버 및 키 자격 증명 모음에 액세스해야 합니다. Backup 자격 증명 모음의 MSI에 대한 액세스 권한이 부여됩니다.

데이터베이스에 대한 키가 저장되는 PostgreSQL 서버 및 Azure Key Vault에서 Backup 자격 증명 모음의 MSI에 부여해야 하는 [적절한 사용 권한을 읽어보십시오.](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-backup)

### <a name="prepare-the-request"></a>요청 준비

모든 관련 사용 권한을 설정하면 백업 구성은 2개의 단계로 수행됩니다.

1. [az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest#az_dataprotection_backup_instance_initialize&preserve-view=true) 명령을 사용하여 관련 자격 증명 모음, 정책, PostgreSQL 데이터베이스를 사용하여 관련 요청을 준비합니다.
1. [az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest#az_dataprotection_backup_instance_create&preserve-view=true) 명령을 사용하여 데이터베이스를 보호하기 위한 요청을 제출합니다.

```azurecli
az dataprotection backup-instance initialize --datasource-id $ossId --datasource-type AzureDatabaseForPostgreSQL -l <vault-location> --policy-id <policy_arm_id>  --secret-store-type AzureKeyVault --secret-store-uri $keyURI > OSSBkpInstance.JSON

az dataprotection backup-instance create --resource-group testBkpVaultRG --vault-name TestBkpVault TestBkpvault --backup-instance .\OSSBkpInstance.JSON
```

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

백업을 트리거하는 동안 보존 규칙을 지정해야 합니다. 정책에서 보존 규칙을 보려면 보존 규칙에 대한 정책 JSON 파일을 탐색합니다. 다음 예제에는 **이름이 Default** 및 Monthly인 두 개의 보존 규칙이 **있습니다.** 주문형 백업에 **월별** 규칙을 사용합니다.

```azurecli
az dataprotection backup-policy show  -g ossdemorg --vault-name ossdemovault-1 --subscription e3d2d341-4ddb-4c5d-9121-69b7e719485e --name osspol5
{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/resourceGroups/ossdemorg/providers/Microsoft.DataProtection/backupVaults/ossdemovault-1/backupPolicies/osspol5",
  "name": "osspol5",
  "properties": {
    "datasourceTypes": [
      "Microsoft.DBforPostgreSQL/servers/databases"
    ],
    "objectType": "BackupPolicy",
    "policyRules": [
      {
        "backupParameters": {
          "backupType": "Full",
          "objectType": "AzureBackupParams"
        },
        "dataStore": {
          "dataStoreType": "VaultStore",
          "objectType": "DataStoreInfoBase"
        },
        "name": "BackupWeekly",
        "objectType": "AzureBackupRule",
        "trigger": {
          "objectType": "ScheduleBasedTriggerContext",
          "schedule": {
            "repeatingTimeIntervals": [
              "R/2020-04-04T20:00:00+00:00/P1W",
              "R/2020-04-01T20:00:00+00:00/P1W"
            ],
            "timeZone": "UTC"
          },
          "taggingCriteria": [
            {
              "criteria": [
                {
                  "absoluteCriteria": [
                    "FirstOfMonth"
                  ],
                  "daysOfMonth": null,
                  "daysOfTheWeek": null,
                  "monthsOfYear": null,
                  "objectType": "ScheduleBasedBackupCriteria",
                  "scheduleTimes": null,
                  "weeksOfTheMonth": null
                }
              ],
              "isDefault": false,
              "tagInfo": {
                "eTag": null,
                "id": "Monthly_",
                "tagName": "Monthly"
              },
              "taggingPriority": 15
            },
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
        "isDefault": false,
        "lifecycles": [
          {
            "deleteAfter": {
              "duration": "P10Y",
              "objectType": "AbsoluteDeleteOption"
            },
            "sourceDataStore": {
              "dataStoreType": "VaultStore",
              "objectType": "DataStoreInfoBase"
            },
            "targetDataStoreCopySettings": []
          }
        ],
        "name": "Monthly",
        "objectType": "AzureRetentionRule"
      },
      {
        "isDefault": true,
        "lifecycles": [
          {
            "deleteAfter": {
              "duration": "P1Y",
              "objectType": "AbsoluteDeleteOption"
            },
            "sourceDataStore": {
              "dataStoreType": "VaultStore",
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
  "resourceGroup": "ossdemorg",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupPolicies"
}
```

## <a name="trigger-an-on-demand-backup-using-command"></a>명령을 사용하여 주문형 백업 트리거

[az dataprotection backup-instance adhoc-backup](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_adhoc_backup) 명령을 사용하여 주문형 백업을 트리거합니다.

```azurecli
az dataprotection backup-instance adhoc-backup --name "ossrg-empdb11" --rule-name "Monthly" --resource-group testBkpVaultRG --vault-name TestBkpVault
```

## <a name="track-jobs"></a>작업 추적

[az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) 명령을 사용하여 모든 작업을 추적합니다. 모든 작업을 나열하고 특정 작업 세부 정보를 가져올 수 있습니다.

_Az.ResourceGraph를_ 사용하여 모든 Backup 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) 명령을 사용하여 Backup 자격 증명 모음에 있는 관련 작업을 가져옵니다.

```azurecli
az dataprotection job list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL --status Completed
```

## <a name="next-steps"></a>다음 단계

- [Azure CLI 사용하여 Azure PostgreSQL 데이터베이스 복원](restore-postgresql-database-cli.md)