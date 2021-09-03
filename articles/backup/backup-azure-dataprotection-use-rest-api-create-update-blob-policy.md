---
title: 데이터 보호 REST API를 사용하여 Blob에 대한 백업 정책 만들기
description: 이 문서에서는 REST API를 사용하여 Blob에 대한 백업 정책을 만들고 관리하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 472d6a4f-7914-454b-b8e4-062e8b556de3
ms.openlocfilehash: 96c76eb592b0fb7b94aa8da6c4f975878dc8c913
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471565"
---
# <a name="create-azure-data-protection-backup-policies-for-blobs-using-rest-api"></a>REST API를 사용하여 Blob에 대한 Azure Data Protection 백업 정책 만들기

> [!IMPORTANT]
> 정책을 계속 만들고 Azure Blob의 백업을 구성하기 전에 [이 섹션](blob-backup-configure-manage.md#before-you-start)을 읽어보세요.

백업 정책은 일반적으로 백업의 보존 및 일정을 제어합니다. Blob의 운영 백업은 본질적으로 연속적이므로 백업 일정을 예약할 필요가 없습니다. 정책은 기본적으로 보존 기간을 지정하는 데 필요합니다. 백업 정책을 재사용하여 여러 스토리지 계정을 자격 증명 모음에 백업하도록 구성할 수 있습니다.

>[!NOTE]
>복원 기간이 길면 복원 작업을 완료하는 데 시간이 더 오래 걸립니다. 또한 일련의 데이터를 복원하는 데 걸리는 시간은 복원 기간 중에 수행된 쓰기 및 삭제 작업의 건수에 따라 달라집니다. 예를 들어 100만 개의 개체에 하루에 3천 개의 개체가 추가되고 1천 개의 개체가 삭제되는 계정의 경우 30일 전의 시점으로 복원하는 데 약 2시간이 소요됩니다. 변동률이 이와 같은 계정에는 보존 기간과 복원 범위를 90일 전을 초과하지 않도록 설정하는 것이 좋습니다.

Azure Recovery Services 자격 증명 모음에 대한 백업 정책을 만드는 단계는 [백업 정책 REST API 문서](/rest/api/dataprotection/backup-policies/create-or-update)에 간략하게 설명되어 있습니다. 이 문서를 참조로 사용하여 스토리지 계정의 Blob에 대한 정책을 만들어 보겠습니다.

## <a name="create-a-policy"></a>정책 만들기

> [!IMPORTANT]
> 현재 기존 정책의 업데이트 또는 수정을 지원하지 않습니다. 대안은 필요한 세부 정보로 새 정책을 만들고 관련 백업 인스턴스에 할당하는 것입니다.

Azure Backup 정책을 만들려면 다음 *PUT* 작업을 사용합니다.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

URI에서 `{policyName}` 및 `{vaultName}`을 제공합니다. 요청 본문에 추가 정보를 제공합니다.

## <a name="create-the-request-body"></a>요청 본문 만들기

예를 들어 Blob 백업의 백업을 만들려면 요청 본문의 구성 요소는 다음과 같습니다.

|이름  |필수  |Type  |설명  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy:[BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | BaseBackupPolicyResource 속성        |

요청 본문의 전체 정의 목록은 [백업 정책 REST API 문서](/rest/api/dataprotection/backup-policies/create-or-update)를 참조하세요.

### <a name="example-request-body"></a>요청 본문 예제

다음 요청 본문에서는 Blob 백업에 대한 백업 정책을 정의합니다.

정책은 다음과 같습니다.

- 보존 기간은 30일입니다.
- 백업이 로컬이고 백업 자격 증명 모음에 데이터가 저장되지 않기 때문에 데이터 저장소는 '운영 저장소'입니다.

```json
{
  "properties": {
    "datasourceTypes": [
      "Microsoft.Storage/storageAccounts/blobServices"
    ],
    "objectType": "BackupPolicy",
    "policyRules": [
      {
        "name": "Default",
        "objectType": "AzureRetentionRule",
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
        ]
      }
    ]
  }
}
```

> [!IMPORTANT]
> DateTime만 지원하는 시간 형식입니다. 시간 형식만 따로 지원하지는 않습니다.

## <a name="responses"></a>응답

백업 정책 만들기/업데이트는 동기 작업이며 작업이 성공하면 성공을 반환합니다.

|Name  |유형  |설명  |
|---------|---------|---------|
|200 정상     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  정상       |

### <a name="example-responses"></a>예제 응답

작업이 완료되면 응답 본문에서 정책 콘텐츠를 사용하여 200(정상)을 반환합니다.

```json
{
  "id": "/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups//TestBkpVaultRG/providers/Microsoft.RecoveryServices/vaults/testBkpVault/backupPolicies/TestBlobPolicy",
  "name": "TestBlobPolicy",
  "type": "Microsoft.DataProtection/backupVaults/backupPolicies",
  "properties": {
    "policyRules": [
      {
        "lifecycles": [
          {
            "deleteAfter": {
              "objectType": "AbsoluteDeleteOption",
              "duration": "P30D"
            },
            "sourceDataStore": {
              "dataStoreType": "OperationalStore",
              "objectType": "DataStoreInfoBase"
            }
          }
        ],
        "isDefault": true,
        "name": "Default",
        "objectType": "AzureRetentionRule"
      }
    ],
    "datasourceTypes": [
      "Microsoft.Storage/storageAccounts/blobServices"
    ],
    "objectType": "BackupPolicy"
  }
}
```

## <a name="next-steps"></a>다음 단계

스토리지 계정의 Blob에 대한 보호를 사용하도록 설정합니다.

Azure Backup REST API에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Data Protection REST API](/rest/api/dataprotection/)
- [Azure REST API 시작하기](/rest/api/azure/)
