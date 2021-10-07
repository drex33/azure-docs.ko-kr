---
title: 데이터 보호 REST API를 사용 하 여 디스크에 대 한 백업 정책 만들기
description: 이 문서에서는 REST API를 사용 하 여 디스크에 대 한 백업 정책을 만들고 관리 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/06/2021
ms.assetid: ecc107c0-311c-42d0-a094-654d7ee30443
ms.openlocfilehash: 0df0b23d921076959718e79d5674aa8d561639e3
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129622396"
---
# <a name="create-azure-data-protection-backup-policies-for-disks-using-rest-api"></a>REST API를 사용 하 여 디스크에 대 한 Azure 데이터 보호 백업 정책 만들기

백업 정책은 백업의 보존 및 일정을 제어 합니다. Azure Disk Backup은 하루에 여러 백업을 제공합니다.

백업 정책을 다시 사용 하 여 여러 Azure 디스크에 대 한 백업을 자격 증명 모음에 구성 하거나 [REST API를 사용 하 여 azure Recovery Services 자격 증명 모음에 대 한 백업 정책을 만들](/rest/api/dataprotection/backup-policies/create-or-update)수 있습니다.

디스크 백업에 대 한 정책을 만들려면 다음 작업을 수행 합니다.

## <a name="create-a-policy"></a>정책 만들기

>[!IMPORTANT]
>현재, 기존 정책을 업데이트 하거나 수정 하는 것은 지원 되지 않습니다. 또는 필요한 세부 정보를 사용 하 여 새 정책을 만들고 관련 백업 인스턴스에 할당할 수 있습니다.

Azure Backup 정책을 만들려면 다음 *PUT* 작업을 사용 합니다.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

URI에서 `{policyName}` 및 `{vaultName}`을 제공합니다. 요청 본문에 추가 정보를 제공합니다.

## <a name="create-the-request-body"></a>요청 본문 만들기

예를 들어 디스크 백업에 대 한 정책을 만들려면 요청 본문에 다음 구성 요소가 필요 합니다.

|이름  |필수  |Type  |설명  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy:[BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | BaseBackupPolicyResource 속성        |

요청 본문의 전체 정의 목록은 [백업 정책 REST API 문서](/rest/api/dataprotection/backup-policies/create-or-update)를 참조하세요.

### <a name="example-request-body"></a>요청 본문 예제

정책은 다음과 같습니다.

- 4 시간 마다 예약 된 트리거입니다 (PT4H). 그런 다음 백업이 하루에 동일 하 게 분산 되도록 4 시간 간격으로 거의 간격으로 백업이 수행 됩니다.
- 트리거 간격을 4, 6, 8 또는 12 시간 간격으로 선택할 수 있습니다. 하루에 한 번 백업 일정을 예약 하려면 **P1D** 을 사용 합니다. 백업은 하루에 한 번 규정 된 시간에 트리거됩니다.
- 데이터 저장소는 백업이 로컬 이며 백업 자격 증명 모음에 저장 되는 데이터가 없으므로 _운영 스토어_ 입니다. 작업 저장소에서 각 백업 인스턴스는 7 일 동안 (P7D) 저장 됩니다.

```json
{
"properties": {
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
}
```

>[!IMPORTANT]
>시간 형식은 DateTime만 지원 합니다. 시간만 지원 하지 않습니다. 시간은 백업 시작 시간을 나타내며 백업이 완료 된 시간은 아닙니다.

백업 작업을 완료하는 데 필요한 시간은 디스크 크기 및 연속 백업 간의 변동 비율을 비롯한 다양한 요인에 따라 달라집니다. 그러나 Azure Disk Backup은 프로덕션 애플리케이션 성능에 영향을 주지 않는 [증분 스냅샷](../virtual-machines/disks-incremental-snapshots.md)을 사용하는 에이전트 없는 백업입니다.

정책 만들기에 관한 자세한 내용은 [Azure Disk Backup 정책](backup-managed-disks.md#create-backup-policy) 설명서를 참조하세요.

## <a name="responses"></a>응답

백업 정책 만들기/업데이트는 동기 작업이며 작업이 성공하면 성공을 반환합니다.

|Name  |Type  |Description  |
|---------|---------|---------|
|200 정상     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  정상       |

### <a name="example-responses"></a>예제 응답

작업이 완료되면 응답 본문에서 정책 콘텐츠를 사용하여 200(정상)을 반환합니다.

```json
{
    "id": "/subscriptions/73307177-bb00-4801-bd11-894b2f2d5162/resourceGroups/RG-BV/providers/Microsoft.DataProtection/backupVaults/BV-JPE-GRS/backupPolicies/DiskBackupPolicy-03",
    "name": "DiskBackupPolicy-03",
    "type": "Microsoft.DataProtection/backupVaults/backupPolicies",
    "properties": {
        "policyRules": [
            {
                "backupParameters": {
                    "backupType": "Incremental",
                    "objectType": "AzureBackupParams"
                },
                "trigger": {
                    "schedule": {
                        "repeatingTimeIntervals": [
                            "R/2021-07-01T19:00:00+00:00/P1D"
                        ],
                      },
                    "taggingCriteria": [
                        {
                            "tagInfo": {
                                "tagName": "Default",
                                "id": "Default_"
                            },
                            "taggingPriority": 99,
                            "isDefault": true
                        }
                    ],
                    "objectType": "ScheduleBasedTriggerContext"
                },
                "dataStore": {
                    "dataStoreType": "OperationalStore",
                    "objectType": "DataStoreInfoBase"
                },
                "name": "BackupDaily",
                "objectType": "AzureBackupRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P7D"
                        },
                        "targetDataStoreCopySettings": [],
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
            "Microsoft.Compute/disks"
        ],
        "objectType": "BackupPolicy"
    }
}
```

## <a name="next-steps"></a>다음 단계

[Azure 디스크에 대 한 보호 사용](backup-azure-dataprotection-use-rest-api-backup-disks.md)

Azure Backup REST Api에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure Data Protection REST API](/rest/api/dataprotection/)
- [Azure REST API 시작하기](/rest/api/azure/)
