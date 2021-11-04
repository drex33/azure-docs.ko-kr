---
title: 데이터 보호 REST API 사용하여 Azure PostgreSQL 데이터베이스에 대한 백업 정책 만들기
description: 이 문서에서는 REST API 사용하여 Azure PostgreSQL 데이터베이스에 대한 백업 정책을 만들고 관리하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: v-amallick
ms.assetid: 759ee63f-148b-464c-bfc4-c9e640b7da6b
ms.openlocfilehash: 2caa9d89ae69d9710f42dc4b3c958d6fa8873098
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102930"
---
# <a name="create-azure-data-protection-backup-policies-for-azure-postgresql-databases-using-rest-api"></a>REST API 사용하여 Azure PostgreSQL 데이터베이스에 대한 Azure Data Protection 백업 정책 만들기

백업 정책은 백업의 보존 및 일정을 제어합니다. Azure PostgreSQL 데이터베이스 백업은 장기 보존을 제공하고 매일 백업을 지원합니다.

기존 백업 정책을 다시 사용하여 PostgreSQL 데이터베이스에 대한 백업을 자격 증명 모음에 구성하거나 [REST API 사용하여 Azure Recovery Services 자격](/rest/api/dataprotection/backup-policies/create-or-update)증명 모음에 대한 백업 정책을 만들 수 있습니다.

## <a name="understanding-postgresql-backup-policy"></a>PostgreSQL 백업 정책 이해

디스크 백업은 하루에 여러 백업을 제공하고 Blob 백업은 트리거 없이 *연속* 백업이지만 PostgreSQL 백업은 보관 보호를 제공합니다. 자격 증명 모음으로 처음 전송되는 백업 데이터는 정의된 규칙 또는 *수명 주기* 에 따라 *보관* 계층으로 이동할 수 있습니다. 이 컨텍스트에서 PostgreSQL에 대한 백업 정책 개체를 이해해 보겠습니다.

-  PolicyRule
   -  BackupRule
      -  BackupParameter
         -  BackupType(이 경우 전체 데이터베이스 백업)
         -  초기 데이터 저장소(백업이 처음에 있는 위치)
         -  트리거(백업이 트리거되는 방법)
            -  일정 기반
            -  기본 태그 지정 조건(예약된 모든 백업에 대한 기본 '태그'입니다. 이 태그는 백업을 보존 규칙에 연결합니다.
   -  기본 보존 규칙(기본적으로 초기 데이터 저장소의 모든 백업에 적용되는 규칙)

따라서 이 개체는 트리거되는 백업 유형, 트리거되는 방법(일정을 통해), 태그가 지정된 백업, 백업 위치(데이터 저장소) 및 데이터 저장소에 있는 백업 데이터의 수명 주기를 정의합니다. PostgreSQL의 기본 PowerShell 개체는 매주 *전체* 백업을 트리거하도록 하며, 3개월 동안 저장된 자격 증명 모음에 저장됩니다.

*정책에 보관* 계층을 추가하려는 경우 데이터를 자격 증명 모음에서 보관으로 이동할 시기, 데이터가 보관에 유지되는 기간 및 *예약된* 백업 중 보관 가능한 로 태그를 지정해야 하는 시점을 결정해야 합니다. 따라서 백업 데이터의 수명 주기를 자격 증명 모음 데이터 저장소에서 보관 데이터 저장소로 정의하고 보관 데이터 저장소에  보관할 기간을 지정하는 *보존 규칙을* 추가해야 합니다. 그런 다음 예약된 백업을 보관할 수 있는 것으로 표시하는 '태그'를 _추가해야 합니다._ 결과 PowerShell 개체는 다음과 같습니다.

-  PolicyRule
   -  BackupRule
      -  BackupParameter
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
         - 대상 데이터 저장소에 복사

PostgreSQL 데이터베이스를 백업하는 정책을 만들려면 다음 작업을 수행합니다.

## <a name="create-a-policy"></a>정책 만들기

>[!IMPORTANT]
>현재 기존 정책 업데이트 또는 수정은 지원되지 않습니다. 또는 필요한 세부 정보를 사용하여 새 정책을 만들고 관련 백업 인스턴스에 할당할 수 있습니다.

Azure Backup 정책을 만들려면 다음 *PUT* 작업을 사용합니다.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

URI에서 `{policyName}` 및 `{vaultName}`을 제공합니다. 또한 요청 본문에 정보가 제공됩니다.

## <a name="create-the-request-body"></a>요청 본문 만들기

예를 들어 POstgreSQL 백업에 대한 정책을 만들려면 요청 본문에 다음 구성 요소가 필요합니다.

|이름  |필수  |Type  |설명  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy:[BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | BaseBackupPolicyResource 속성        |

요청 본문의 전체 정의 목록은 백업 [정책 REST API 문서를 참조하세요.](/rest/api/dataprotection/backup-policies/create-or-update)

### <a name="example-request-body"></a>요청 본문 예제

정책은 다음과 같습니다.

- 매주 백업에 대해 예약된 트리거를 선택하고 시작 시간을 선택합니다. (시간 + P1W).
- 백업이 _자격 증명 모음으로_ 직접 전송되기 때문에 데이터 저장소는 자격 증명 모음 저장소 입니다.
- 백업은 3개월(P3M) 동안 자격 증명 모음에 보존됩니다.

```json
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

>[!IMPORTANT]
>시간 형식은 _DateTime_ 만 지원합니다. _Time만_ 지원되지 않습니다. 하루 중 시간은 백업이 완료된 시간이 아니라 백업 시작 시간을 나타냅니다.

위의 **JSON을** 두 가지 변경 내용으로 업데이트해 보겠습니다. 즉, 요일의 백업과 PostgreSQL 데이터베이스 백업의 장기 보존을 위해 *보관* 데이터 저장소를 추가해 보겠습니다.

다음 예제에서는 매주 매주 일요일, 수요일 및 금요일마다 백업하도록 주별 백업을 수정합니다. 일정 날짜 배열은 날짜를 언급하고 해당 날짜의 요일은 요일로 간주합니다. 또한 이러한 일정이 매주 반복하도록 지정해야 합니다. 따라서 일정 간격은 *1이고* 간격 유형은 *매주* 입니다.

**예약된 트리거:**

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T22:00:00+00:00/P1W",
            "R/2021-08-18T22:00:00+00:00/P1W",
            "R/2021-08-20T22:00:00+00:00/P1W"
          ],
          "timeZone": "UTC"
        }
```

*보관* 보호를 추가하려면 아래와 같이 정책 JSON을 수정해야 합니다.

위의 JSON에는 기본 보존 규칙에 따라 초기 데이터 저장소에 대한 수명 주기가 있습니다. 이 시나리오에서 규칙은 3개월 후에 백업 데이터를 삭제한다고 표시합니다. 데이터가 *보관* 데이터 *저장소로 이동되는* 시기를 정의하는 새 보존 규칙을 추가해야 합니다. 즉, 백업 데이터가 먼저 보관 데이터 저장소에 복사된 다음 자격 증명 모음 데이터 저장소에서 삭제됩니다. 또한 규칙은 보관* 데이터 저장소에 데이터를 보관하는 기간을 정의해야 합니다. 이 새 규칙의 이름을 **매월로** 지정하고 백업을 6개월 동안 자격 증명 모음 데이터 저장소에 보존한 다음 보관 데이터 저장소에 복사하도록 정의합니다. 그런 다음 자격 증명 모음 데이터 저장소에서 *삭제하면* 보관 데이터 저장소에서 24개월 동안 데이터를 보존한 다음 보관 데이터 저장소의 데이터를 삭제합니다.

**보존 수명 주기:**

```json
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
    },
    {
      "isDefault": false,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P6M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": {
            "copyAfter": {
              "objectType": "CopyOnExpiryOption"
            },
            "dataStore": {
              "dataStoreType": "ArchiveStore",
              "objectType": "DataStoreInfoBase"
            }
          }
        },
        {
          "deleteAfter": {
            "duration": "P24M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "ArchiveStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": null
        }
      ],
      "name": "Monthly",
      "objectType": "AzureRetentionRule"
    }
```

보존 규칙을 추가할 때마다 정책의 *트리거* 속성에 해당 태그를 추가해야 합니다. 다음 예제에서는 적용할 해당 보존 규칙과 정확히 동일한 이름의 조건(월의 첫 번째 성공적인 백업)과 함께 새 *태그를* 만듭니다. 

이 예제에서는 태그 조건의 이름을 *매월* 로 지정해야 합니다.

**태그 지정 조건:**

```json
{
  "criteria": [
    {
      "absoluteCriteria": [
        "FirstOfMonth"
      ],
      "objectType": "ScheduleBasedBackupCriteria"
    }
  ],
  "isDefault": false,
  "tagInfo": {
    "tagName": "Monthly"
  },
  "taggingPriority": 15
}
```

모든 변경 내용을 포함하면 정책 JSON이 다음과 같이 표시됩니다.

```json
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
            "R/2021-08-15T22:00:00+00:00/P1W",
            "R/2021-08-18T22:00:00+00:00/P1W",
            "R/2021-08-20T22:00:00+00:00/P1W"
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
          },
          {
            "criteria": [
              {
                "absoluteCriteria": [
                  "FirstOfMonth"
                ],
                "objectType": "ScheduleBasedBackupCriteria"
              }
            ],
            "isDefault": false,
            "tagInfo": {
              "tagName": "Monthly"
            },
            "taggingPriority": 15
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
    },
    {
      "isDefault": false,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P6M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": {
            "copyAfter": {
              "objectType": "CopyOnExpiryOption"
            },
            "dataStore": {
              "dataStoreType": "ArchiveStore",
              "objectType": "DataStoreInfoBase"
            }
          }
        },
        {
          "deleteAfter": {
            "duration": "P24M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "ArchiveStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": null
        }
      ],
      "name": "Monthly",
      "objectType": "AzureRetentionRule"
    }
  ]
}

```

정책 만들기에 대한 자세한 내용은 [PostGreSQL 데이터베이스 백업 정책](backup-azure-database-postgresql.md#create-backup-policy) 문서를 참조하세요.

## <a name="responses"></a>응답

백업 정책 만들기/업데이트는 동기 작업이며 작업이 성공하면 _확인을_ 반환합니다.

|이름  |Type  |설명  |
|---------|---------|---------|
|200 정상     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  정상       |

### <a name="example-responses"></a>예제 응답

작업이 완료되면 응답 본문에서 정책 콘텐츠를 사용하여 200(정상)을 반환합니다.

```json
{
    "properties": {
        "policyRules": [
            {
                "backupParameters": {
                    "backupType": "Full",
                    "objectType": "AzureBackupParams"
                },
                "trigger": {
                    "schedule": {
                        "repeatingTimeIntervals": [
                            "R/2021-08-15T22:00:00+00:00/P1W",
                            "R/2021-08-18T22:00:00+00:00/P1W",
                            "R/2021-08-20T22:00:00+00:00/P1W"
                        ],
                        "timeZone": "UTC"
                    },
                    "taggingCriteria": [
                        {
                            "tagInfo": {
                                "tagName": "Monthly",
                                "id": "Monthly_"
                            },
                            "taggingPriority": 15,
                            "isDefault": false,
                            "criteria": [
                                {
                                    "absoluteCriteria": [
                                        "FirstOfMonth"
                                    ],
                                    "objectType": "ScheduleBasedBackupCriteria"
                                }
                            ]
                        },
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
                    "dataStoreType": "VaultStore",
                    "objectType": "DataStoreInfoBase"
                },
                "name": "BackupWeekly",
                "objectType": "AzureBackupRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P6M"
                        },
                        "targetDataStoreCopySettings": [
                            {
                                "dataStore": {
                                    "dataStoreType": "ArchiveStore",
                                    "objectType": "DataStoreInfoBase"
                                },
                                "copyAfter": {
                                    "objectType": "CopyOnExpiryOption"
                                }
                            }
                        ],
                        "sourceDataStore": {
                            "dataStoreType": "VaultStore",
                            "objectType": "DataStoreInfoBase"
                        }
                    },
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P24M"
                        },
                        "targetDataStoreCopySettings": [],
                        "sourceDataStore": {
                            "dataStoreType": "ArchiveStore",
                            "objectType": "DataStoreInfoBase"
                        }
                    }
                ],
                "isDefault": false,
                "name": "Monthly",
                "objectType": "AzureRetentionRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P3M"
                        },
                        "targetDataStoreCopySettings": [],
                        "sourceDataStore": {
                            "dataStoreType": "VaultStore",
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
            "Microsoft.DBforPostgreSQL/servers/databases"
        ],
        "objectType": "BackupPolicy"
    },
    "id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/DebRG1/providers/Microsoft.DataProtection/backupVaults/DebBackupVault/backupPolicies/OssPolicy1",
    "name": "OssPolicy1",
    "type": "Microsoft.DataProtection/backupVaults/backupPolicies"
}
```

## <a name="next-steps"></a>다음 단계

[Azure 디스크에 대한 보호 사용](backup-azure-dataprotection-use-rest-api-backup-disks.md)

Azure Backup REST Api에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [Azure Data Protection REST API](/rest/api/dataprotection/)
- [Azure REST API 시작하기](/rest/api/azure/)
