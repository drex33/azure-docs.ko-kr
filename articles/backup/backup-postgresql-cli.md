---
title: Azure CLI를 사용 하 여 장기 보존으로 Azure Database for PostgreSQL 백업
description: Azure CLI를 사용 하 여 Azure Database for PostgreSQL를 백업 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/24/2021
ms.custom: devx-track-azurecli
ms.author: v-amallick
ms.openlocfilehash: ad6602c5a7b9307643703aef87f29738cd6d99fa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103722"
---
# <a name="back-up-azure-postgresql-databases-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure PostgreSQL 데이터베이스 백업

이 문서에서는 Azure CLI를 사용 하 여 [Azure PostgreSQL 데이터베이스](../postgresql/overview.md#azure-database-for-postgresql---single-server) 를 백업 하는 방법을 설명 합니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

-  Backup 자격 증명 모음 만들기
-  백업 정책 만들기
-  Azure PostgreSQL 데이터베이스의 백업 구성
-  주문형 백업 작업 실행

InformgreSQL 데이터베이스에 대해 지원 되는 시나리오 및 제한 사항은 [support matrix](backup-azure-database-postgresql-overview.md#support-matrix)를 참조 하세요.

## <a name="create-a-backup-vault"></a>Backup 자격 증명 모음 만들기

백업 자격 증명 모음은 Azure Database for PostgreSQL 서버, 저장소 계정의 blob 및 Azure 디스크와 같이 Azure Backup에서 지 원하는 다양 한 새 워크 로드에 대 한 백업 데이터를 저장 하는 Azure의 저장소 엔터티입니다. 백업 자격 증명 모음은 관리 오버 헤드를 최소화 하면서 백업 데이터를 구성 하는 데 도움이 됩니다. Backup 자격 증명 모음은 Azure의 Azure Resource Manager 모델을 기반으로 하며 백업 데이터를 보호할 수 있도록 하는 향상된 기능을 제공합니다.

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

자격 증명 모음을 만든 후에는 Azure PostgreSQL 데이터베이스를 보호 하는 백업 정책을 만들어 보겠습니다.

## <a name="create-a-backup-policy"></a>백업 정책 만들기

### <a name="understanding-postgresql-backup-policy"></a>PostGreSQL 백업 정책 이해

디스크 백업은 하루에 여러 백업을 제공 하 고 blob 백업은 트리거가 없는 _연속_ 백업입니다. PostgreSQL Backup은 보관 보호를 제공 합니다. 자격 증명 모음으로 처음 전송 된 백업 데이터는 정의 된 규칙 또는 _수명 주기_ 에 따라 _보관_ 계층으로 이동할 수 있습니다. 이 컨텍스트에서는 PostgreSQL에 대 한 백업 정책 개체에 대해 알아보겠습니다.

- PolicyRule
   -  BackupRule
      - BackupParameter 변수
        -  BackupType (이 경우 전체 데이터베이스 백업)
        -  초기 데이터 저장소 (백업 위치는 처음에)
        -  트리거 (백업이 트리거되는 방법)
           -  일정 기반
           -  모든 예약 된 백업에 대 한 기본 태그 지정 조건 (기본 ' 태그 ')입니다. 이 태그는 백업을 보존 규칙에 연결 합니다.
   -  기본 보존 규칙 (기본적으로 초기 데이터 저장소에서 모든 백업에 적용 되는 규칙)

따라서이 개체는 트리거되는 백업 유형, 일정을 통해 트리거되는 방법, 태그가 지정 된 항목, 데이터 저장소 (데이터 저장소) 및 데이터 저장소에 있는 백업 데이터의 수명 주기를 정의 합니다. PostgreSQL에 대 한 기본 PowerShell 개체는 매주 *전체* 백업을 트리거하고 세 달 동안 저장 된 자격 증명 모음에 도달 합니다.

*보관* 계층을 정책에 추가 하려면 데이터를 자격 증명 모음에서 보관으로 이동할 시기, 데이터가 보관 저장소에 유지 되는 기간, 예약 된 백업에 _보관할 수_ 로 태그를 지정 해야 하는 시간을 결정 해야 합니다. 따라서 *보존 규칙* 을 추가 해야 합니다. 여기에서 백업 데이터의 수명 주기는 자격 증명 데이터 저장소에서 보관 데이터 저장소로 정의 되 고 *보관* 데이터 저장소에 유지 되는 기간입니다. 그런 다음 예약 된 백업을 _보관할 자격이_ 있는 것으로 표시 하는 *태그* 를 추가 해야 합니다.

결과 PowerShell 개체는 다음과 같습니다.

-  PolicyRule
   -  BackupRule
      - BackupParameter 변수
        -  BackupType (이 경우 전체 데이터베이스 백업)
        -  초기 데이터 저장소 (백업 위치는 처음에)
        -  트리거 (백업이 트리거되는 방법)
           -  일정 기반
           -  모든 예약 된 백업에 대 한 기본 태그 지정 조건 (기본 ' 태그 ')입니다. 이 태그는 백업을 보존 규칙에 연결 합니다.
           -  이름이 ' X ' 인 새 보존 규칙의 새 태그 지정 조건
   -  기본 보존 규칙 (기본적으로 초기 데이터 저장소에서 모든 백업에 적용 되는 규칙)
   -  ' X '로 이름이 지정 된 새 보존 규칙
      -  수명 주기
         -  원본 데이터 저장소
         -  원본 데이터 저장소의 기간 후 삭제
         -  대상 데이터 저장소에 복사

### <a name="retrieve-the-policy-template"></a>정책 템플릿 검색

Azure PostgreSQL 데이터베이스 백업에 대 한 백업 정책의 내부 구성 요소를 이해 하려면 [az dataprotection backup-policy-template-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template) 명령을 사용 하 여 정책 템플릿을 검색 합니다. 이 명령은 지정된 데이터 원본 형식에 관한 기본 정책 템플릿을 반환합니다. 이 정책 템플릿을 사용하여 새 정책을 만듭니다.

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

정책 템플릿은 트리거(백업을 트리거하는 항목 결정)와 수명 주기(백업을 삭제/복사/이동할 시기 결정)로 구성됩니다. Azure PostgreSQL 데이터베이스 백업에서 트리거의 기본값은 예약 된 주별 트리거 (7 일 마다 백업 1 개)이 고 각 백업이 3 개월 동안 유지 되는 것입니다.

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
> Azure PowerShell에서 _개체_ 를 준비 위치로 사용 하 여 모든 수정 작업을 수행할 수 있습니다. *개체* 에 대 한 개념이 없으므로 Azure CLI 파일을 사용 해야 합니다. 각 편집 작업은 새 파일로 리디렉션해야 합니다. 여기서 콘텐츠는 입력 파일에서 읽어서 출력 파일로 다시 전달 됩니다. 나중에 스크립트에서를 사용 하는 동안 필요에 따라 파일 이름을 바꿀 수 있습니다.

#### <a name="modify-the-schedule"></a>일정 수정

기본 정책 템플릿은 매주 한 번 백업을 제공 합니다. 백업에 대 한 일정은 일주일에 한 번 수행 되도록 수정할 수 있습니다. 일정을 수정 하려면 [az dataprotection backup-policy trigger set](/cli/azure/dataprotection/backup-policy/trigger?view=azure-cli-latest#az_dataprotection_backup_policy_trigger_set&preserve-view=true) 명령을 사용 합니다.

다음 예에서는 매주 일요일, 수요일 및 금요일 마다 백업 하도록 주간 백업을 수정 합니다. 일정 날짜 배열은 날짜를 언급 하 고 해당 날짜의 요일은 요일로 사용 됩니다. 또한 매주이 일정을 반복 하도록 지정 해야 합니다. 따라서 일정 간격은 "1"이 고 간격 유형은 "매주"입니다.

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

*보관* 보호를 추가 하려면 아래와 같이 정책 템플릿을 수정 해야 합니다.

기본 템플릿은 기본 보존 규칙의 초기 데이터 저장소에 대 한 수명 주기를 갖습니다. 이 시나리오에서 규칙은 3 개월 후 백업 데이터를 삭제 하는 것을 말합니다. 데이터를 *보관* 데이터 저장소로 *이동* 하는 경우를 정의 하는 새 보존 규칙을 추가 해야 합니다. 즉, 백업 데이터는 먼저 보관 데이터 저장소로 복사 된 후 자격 증명 저장소에서 삭제 됩니다. 또한이 규칙은 데이터가 *보관* 데이터 저장소에 보관 되는 기간에 대해 정의 해야 합니다. [Az dataprotection 백업-정책 보존-규칙 만들기-수명 주기](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest#az_dataprotection_backup_policy_retention_rule_create_lifecycle&preserve-view=true) 명령을 사용 하 여 새 주기를 만들고 [az dataprotection backup-policy 보존이 설정](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest#az_dataprotection_backup_policy_retention_rule_set&preserve-view=true) 된 명령을 사용 하 여 새 규칙 또는 기존 규칙에 연결 합니다.

다음 예에서는 *매월* 이라는 새 보존 규칙을 만듭니다 .이 규칙은 매월 첫 번째 성공한 백업이 자격 증명 모음에 보관 되 고, 보관 계층으로 이동 하 고, 보관 계층에서 24 개월 동안 유지 되어야 합니다.

```azurecli
az dataprotection backup-policy retention-rule create-lifecycle --retention-duration-count 6 --retention-duration-type Months --source-datastore VaultStore --target-datastore ArchiveStore --copy-option CopyOnExpiryOption > VaultToArchiveLifeCycle.JSON

az dataprotection backup-policy retention-rule create-lifecycle --retention-duration-count 24 --retention-duration-type Months -source-datastore ArchiveStore > OnArchiveLifeCycle.JSON

az dataprotection backup-policy retention-rule set --lifecycles .\VaultToArchiveLifeCycle.JSON .\OnArchiveLifeCycle.JSON --name Monthly --policy .\EditedOSSPolicy.JSON > AddedRetentionRulePolicy.JSON
```

#### <a name="add-a-tag-and-the-relevant-criteria"></a>태그 및 관련 조건 추가

보존 규칙이 만들어지면 백업 정책의 *Trigger* 속성에 해당 *태그* 를 만들어야 합니다. [Az dataprotection backup-정책 태그 만들기-절대 조건](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_create_absolute_criteria&preserve-view=true) 명령을 사용 하 여 새 태그 지정 조건을 만들고 [az dataprotection backup-policy tag set](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_set&preserve-view=true) 명령을 사용 하 여 기존 태그를 업데이트 하거나 새 태그를 만듭니다.

다음 예에서는 적용 되는 해당 보존 규칙과 동일한 이름으로 조건 (월의 첫 번째 백업)과 함께 새 *태그* 를 만듭니다.

이 예제에서는 태그 조건의 이름을 *매월* 로 지정 해야 합니다.

```azurecli
az dataprotection backup-policy tag create-absolute-criteria --absolute-criteria FirstOfMonth > tagCriteria.JSON
az dataprotection backup-policy tag set --criteria .\tagCriteria.JSON --name Monthly --policy .\AddedRetentionRulePolicy.JSON > AddedRetentionRuleAndTag.JSON
```

일정이 주별 백업 (위 예제에 지정 된 대로 일요일, 수요일, 목요일 마다) 인 경우 일요일 및 금요일 백업을 보관 하려는 경우 [az dataprotection backup-policy tag create-generic criteria](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_create_generic_criteria&preserve-view=true) 명령을 사용 하 여 다음과 같이 태깅 조건을 변경할 수 있습니다.

```azurecli
az dataprotection backup-policy tag create-generic-criteria --days-of-week Sunday Friday > tagCriteria.JSON
az dataprotection backup-policy tag set --criteria .\tagCriteria.JSON --name Monthly --policy .\AddedRetentionRulePolicy.JSON > AddedRetentionRuleAndTag.JSON
```

### <a name="create-a-new-postgresql-backup-policy"></a>새 PostgreSQL 백업 정책 만들기

요구 사항에 따라 템플릿이 수정 되 면 [az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest#az_dataprotection_backup_policy_create&preserve-view=true) 명령을 사용 하 여 수정 된 템플릿을 사용 하 여 정책을 만듭니다.

```azurecli
az dataprotection backup-policy create --backup-policy-name FinalOSSPolicy --policy AddedRetentionRuleAndTag.JSON --resource-group testBkpVaultRG --vault-name TestBkpVault
```

## <a name="configure-backup"></a>백업 구성

자격 증명 모음 및 정책을 만든 후에는 Azure PostgreSQL 데이터베이스를 보호 하기 위해 고려해 야 하는 세 가지 중요 한 사항이 있습니다.

### <a name="key-entities-involved"></a>관련된 주요 엔터티

#### <a name="postgresql-database-to-be-protected"></a>보호할 PostGreSQL 데이터베이스

보호할 PostgreSQL의 Azure Resource Manager ID (ARM ID)를 인출 합니다. 이는 데이터베이스의 식별자 역할을 합니다. PostgreSQL server **testposgresql** 아래에 **empdb11** 라는 데이터베이스의 예를 사용 합니다 .이 데이터베이스는 다른 구독에 있는 리소스 그룹 **ossrg** 에 있습니다.

다음 예에서는 bash를 사용 합니다.

```azurecli
ossId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossrg/providers/Microsoft.DBforPostgreSQL/servers/archive-postgresql-ccy/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Azure Key Vault

Azure Backup 서비스는 PostgreSQL 데이터베이스에 연결 하기 위해 사용자 이름과 암호를 저장 하지 않습니다. 대신, 백업 관리자가 키를 키 자격 증명 모음으로 초기값 *으로 전환 해야* 합니다. 그런 다음 백업 서비스는 키 자격 증명 모음에 액세스 하 고 키를 읽은 다음 데이터베이스에 액세스 합니다. 관련 키의 비밀 식별자를 적어 둡니다.

다음 예에서는 bash를 사용 합니다.

```azure-cli
keyURI="https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>Backup 자격 증명 모음

백업 자격 증명 모음은 PostgreSQL 서버에 연결한 다음 키 자격 증명 모음에 있는 키를 통해 데이터베이스에 액세스 해야 합니다. 따라서 PostgreSQL 서버 및 주요 자격 증명 모음에 대 한 액세스 권한이 필요 합니다. 백업 자격 증명 모음 MSI에 액세스 권한이 부여 됩니다.

PostgreSQL 서버에서 백업 자격 증명 모음 MSI에 부여 해야 하는 [적절 한 사용 권한과](/azure/backup/backup-azure-database-postgresql-overview#set-of-permissions-needed-for-azure-postgresql-database-backup) 데이터베이스에 대 한 키가 저장 되는 Azure Key vault에 대해 알아보세요.

### <a name="prepare-the-request"></a>요청 준비

모든 관련 사용 권한을 설정하면 백업 구성은 2개의 단계로 수행됩니다.

1. [Az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest#az_dataprotection_backup_instance_initialize&preserve-view=true) 명령을 사용 하 여 관련 자격 증명 모음, 정책, PostgreSQL 데이터베이스를 사용 하 여 관련 요청을 준비 합니다.
1. [Az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest#az_dataprotection_backup_instance_create&preserve-view=true) 명령을 사용 하 여 데이터베이스를 보호 하기 위한 요청을 제출 합니다.

```azurecli
az dataprotection backup-instance initialize --datasource-id $ossId --datasource-type AzureDatabaseForPostgreSQL -l <vault-location> --policy-id <policy_arm_id>  --secret-store-type AzureKeyVault --secret-store-uri $keyURI > OSSBkpInstance.JSON

az dataprotection backup-instance create --resource-group testBkpVaultRG --vault-name TestBkpVault TestBkpvault --backup-instance .\OSSBkpInstance.JSON
```

## <a name="run-an-on-demand-backup"></a>주문형 백업 실행

백업을 트리거하는 동안 보존 규칙을 지정 해야 합니다. 정책에서 보존 규칙을 보려면 보존 규칙의 정책 JSON 파일을 탐색 합니다. 다음 예제에는 이름이 **Default** 와 **매월** 인 두 개의 보존 규칙이 있습니다. 주문형 백업에 대 한 **월간** 규칙을 사용 합니다.

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

## <a name="trigger-an-on-demand-backup-using-command"></a>명령을 사용 하 여 주문형 백업 트리거

[az dataprotection backup-instance adhoc-backup](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_adhoc_backup) 명령을 사용하여 주문형 백업을 트리거합니다.

```azurecli
az dataprotection backup-instance adhoc-backup --name "ossrg-empdb11" --rule-name "Monthly" --resource-group testBkpVaultRG --vault-name TestBkpVault
```

## <a name="track-jobs"></a>작업 추적

[az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) 명령을 사용하여 모든 작업을 추적합니다. 모든 작업을 나열하고 특정 작업 세부 정보를 가져올 수 있습니다.

_Az. ResourceGraph_ 를 사용 하 여 모든 백업 자격 증명 모음에서 모든 작업을 추적할 수도 있습니다. [Az dataprotection 작업 목록-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) 명령을 사용 하 여 백업 자격 증명 모음에 있는 관련 작업을 가져옵니다.

```azurecli
az dataprotection job list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL --status Completed
```

## <a name="next-steps"></a>다음 단계

- [Azure CLI를 사용 하 여 Azure PostgreSQL database 복원](restore-postgresql-database-cli.md)
