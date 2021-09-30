---
title: 데이터 수명 주기를 자동으로 관리하여 비용 최적화
titleSuffix: Azure Storage
description: Azure Storage 수명 주기 관리 정책을 사용하여 핫, 쿨 및 보관 계층 간에 데이터를 이동할 수 있는 자동화된 규칙을 만듭니다.
author: tamram
ms.author: tamram
ms.date: 08/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: 245bcbfd59644946ac6f039e35fe02147054cc8c
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273234"
---
# <a name="optimize-costs-by-automatically-managing-the-data-lifecycle"></a>데이터 수명 주기를 자동으로 관리하여 비용 최적화

데이터 집합에는 고유한 수명 주기가 있습니다. 수명 주기 초기에는 사람들이 일부 데이터에 자주 액세스합니다 하지만 액세스 필요성은 데이터가 오래될 수록 크게 줄어듭니다. 어떤 데이터는 클라우드에서 유휴 상태로 유지되고 저장된 후에는 어쩌다 한 번씩 액세스됩니다. 어떤 데이터 세트는 생성된 후 며칠 또는 몇 달 만에 만료되고 또 어떤 데이터 세트는 수명 주기 내내 적극적으로 읽히고 수정됩니다. Azure Storage 수명 주기 관리는 Blob 데이터를 적절한 액세스 계층으로 전환하거나 수명 주기가 끝나면 데이터를 만료하는 데 사용할 수 있는 규칙 기반 정책을 제공하는 경우가 많습니다.

수명 주기 관리 정책을 사용하면 다음을 수행할 수 있습니다.

- 성능 최적화를 위해 액세스하는 경우 Blob을 쿨에서 핫으로 즉시 전환합니다.
- 비용 최적화를 위해 개체가 일정 기간 동안 액세스 또는 수정되지 않은 경우 Blob, Blob 버전 및 Blob 스냅샷을 더 높은 수준의 쿨 스토리지 계층으로 전환합니다. 이 시나리오에서 수명 주기 관리 정책은 개체를 핫에서 쿨로, 핫에서 보관으로 또는 쿨에서 보관으로 이동합니다.
- 수명 주기 종료 시 Blob, Blob 버전 및 Blob 스냅샷을 삭제합니다.
- 스토리지 계정 수준에서 하루에 한 번 실행할 규칙을 정의합니다.
- 이름 접두사나 [Blob 인덱스 태그](storage-manage-find-blobs.md)를 필터로 사용하여 컨테이너나 Blob 하위 집합에 규칙을 적용합니다.

수명 주기 초기 단계에는 데이터에 자주 액세스하지만 2주 후에는 가끔씩만 데이터에 액세스하는 시나리오를 고려해 보겠습니다. 첫 번째 달 이후에는 데이터 세트에 거의 액세스하지 않습니다. 이 시나리오에서 초기 단계 동안에는 핫 스토리지 계층이 가장 적절합니다. 가끔 접근하는 경우에는 쿨 스토리지가 가장 적합합니다. 보관 스토리지는 데이터가 생성된 지 한 달이 넘은 경우 가장 적절한 계층 옵션입니다. 수명 주기 관리 정책 규칙을 사용하여 해당 수명을 기준으로 데이터를 적절한 스토리지 계층으로 이동하면 요구 사항에 맞는 가장 저렴한 솔루션을 설계할 수 있습니다.

수명 주기 관리 정책은 범용 v2, 프리미엄 블록 Blob 및 Blob Storage 계정의 블록 Blob 및 추가 Blob에 대해 지원됩니다. 수명 주기 관리는 *$logs* 및 *$web* 컨테이너와 같은 시스템 컨테이너에 영향을 주지 않습니다.

> [!IMPORTANT]
> 데이터 세트를 읽을 수 있어야 하는 경우에는 Blob을 보관 계층으로 이동하는 정책을 설정하지 마십시오. 보관 계층의 Blob이 처음에 리하이드레이션되지 않는 한 이 Blob을 읽을 수 없으며 이 프로세스는 시간과 비용이 많이 소모될 수 있습니다. 자세한 내용은 [보관 계층의 Blob 리하이드레이션 개요](archive-rehydrate-overview.md)를 참조하세요.

## <a name="lifecycle-management-policy-definition"></a>수명 주기 관리 정책 정의

수명 주기 관리 정책은 JSON 문서의 규칙 컬렉션입니다. 다음 샘플 JSON에서는 전체 규칙 정의를 보여줍니다.

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

정책은 다음 표의 설명대로 규칙 컬렉션입니다.

| 매개 변수 이름 | 매개 변수 형식 | 참고 |
|----------------|----------------|-------|
| `rules`        | 규칙 개체의 배열 | 정책에 하나 이상의 규칙이 필요합니다. 정책에 최대 100개의 규칙을 정의할 수 있습니다.|

정책 내 각 규칙에는 다음 표에서 설명한 몇 가지 매개 변수가 있습니다.

| 매개 변수 이름 | 매개 변수 형식 | 참고 | 필수 |
|--|--|--|--|
| `name` | 문자열 | 규칙 이름은 최대 256자의 영숫자를 포함할 수 있습니다. 규칙 이름은 대/소문자를 구분합니다. 정책 내에서 고유해야 합니다. | True |
| `enabled` | 부울 | 규칙을 일시적으로 사용하지 않을 수 있는 선택적 부울입니다. 설정되지 않은 경우 기본값은 true입니다. | 거짓 |
| `type` | 열거형 값 | 현재 유효한 형식은 `Lifecycle`입니다. | True |
| `definition` | 수명 주기 규칙을 정의하는 개체 | 각 정의는 필터 집합과 작업 집합으로 구성됩니다. | True |

## <a name="lifecycle-management-rule-definition"></a>수명 주기 관리 규칙 정의

정책 내 각 규칙 정의에는 필터 집합과 작업 집합이 포함되어 있습니다. [필터 집합](#rule-filters)은 컨테이너 또는 개체 이름 내에서 개체의 특정 집합으로 규칙 작업을 제한합니다. [작업 집합](#rule-actions)은 계층을 적용하거나 필터링된 개체 집합에 대한 작업을 삭제합니다.

### <a name="sample-rule"></a>샘플 규칙

다음 샘플 규칙은 계정을 필터링하여 `sample-container` 내에 존재하고 `blob1`로 시작하는 개체에 대한 작업을 실행합니다.

- 마지막으로 수정한 시점으로부터 30일 후 Blob을 쿨 계층으로 이동
- 마지막으로 수정한 시점으로부터 90일 후 Blob을 보관 계층으로 이동
- 마지막으로 수정한 시점으로부터 2,555일(7년) 후 BLOB 삭제
- 생성 후 90일이 지난 이전 Blob 버전 삭제

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "sample-rule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "delete": {
              "daysAfterCreationGreaterThan": 90
            }
          },
          "baseBlob": {
            "tierToCool": {
              "daysAfterModificationGreaterThan": 30
            },
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 2555
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "sample-container/blob1"
          ]
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>규칙 필터

필터는 규칙 작업을 스토리지 계정 내의 BLOB 작업 하위 집합으로 제한합니다. 둘 이상의 필터를 정의하는 경우 논리적 `AND`가 모든 필터에서 실행됩니다.

필터에는 다음이 포함됩니다.

| 필터 이름 | 필터 형식 | 참고 | 필수 여부 |
|-------------|-------------|-------|-------------|
| blobTypes   | 미리 정의된 열거형 값의 배열입니다. | 현재 릴리스에서는 `blockBlob` 및 `appendBlob`을 지원합니다. `appendBlob`에는 삭제만 지원되며 계층 설정은 지원되지 않습니다. | 예 |
| prefixMatch | 일치시킬 접두사에 대한 문자열 배열입니다. 각 규칙에서 접두사(대/소문자 구분)를 최대 10개까지 정의할 수 있습니다. 접두사 문자열은 컨테이너 이름으로 시작해야 합니다. 예를 들어 규칙에 대해 `https://myaccount.blob.core.windows.net/sample-container/blob1/...` 아래의 모든 Blob을 일치시키려는 경우 prefixMatch는 `sample-container/blob1`입니다. | prefixMatch를 정의하지 않으면 규칙은 스토리지 계정 내의 모든 Blob에 적용됩니다. | 아니요 |
| blobIndexMatch | 일치시킬 Blob 인덱스 태그 키와 값 조건으로 구성된 사전 값의 배열입니다. 각 규칙에서 Blob 인덱스 태그 조건을 최대 10개까지 정의할 수 있습니다. 예를 들어 규칙의 `https://myaccount.blob.core.windows.net/`에서 모든 Blob을 `Project = Contoso`와 일치시키려는 경우 blobIndexMatch는 `{"name": "Project","op": "==","value": "Contoso"}`입니다. | blobIndexMatch를 정의하지 않으면 규칙은 스토리지 계정 내의 모든 Blob에 적용됩니다. | 아니요 |

알려진 문제 및 제한 사항과 함께 Blob 인덱스 기능에 대한 자세한 내용은 [Blob 인덱스를 사용하여 Azure Blob Storage에서 데이터 관리 및 찾기](storage-manage-find-blobs.md)를 참조하세요.

### <a name="rule-actions"></a>규칙 작업

실행 조건이 충족되면 필터링된 Blob에 작업이 적용됩니다.

수명 주기 관리는 Blob, 이번 Blob 버전 및 Blob 스냅샷의 계층 이동과 삭제를 지원합니다. 기본 Blob, 이전 Blob 버전 또는 Blob 스냅샷에 대한 각 규칙에 하나 이상의 작업을 정의합니다.

| 작업                      | 기본 Blob                                  | 스냅샷      | 버전
|-----------------------------|--------------------------------------------|---------------|---------------|
| tierToCool                  | `blockBlob`에 지원됨                  | 지원됨     | 지원됨     |
| enableAutoTierToHotFromCool | `blockBlob`에 지원됨                  | 지원되지 않음 | 지원되지 않음 |
| tierToArchive               | `blockBlob`에 지원됨                  | 지원됨     | 지원됨     |
| delete                      | `blockBlob` 및 `appendBlob`에 대해 지원됨 | 지원됨     | 지원됨     |

> [!NOTE]
> 동일한 Blob에 작업을 두 개 이상 정의하는 경우 수명 주기 관리는 가장 저렴한 작업을 Blob에 적용합니다. 예를 들어 `delete` 작업은 `tierToArchive` 작업보다 저렴합니다. `tierToArchive` 작업은 `tierToCool` 작업보다 저렴합니다.

실행 조건은 보존 기간을 기준으로 합니다. 기본 Blob는 마지막으로 수정된 시간을 사용하고, Blob 버전은 버전 생성 시간을 사용하고, Blob 스냅샷은 스냅샷 생성 시간으로 보존 기간을 추적합니다.

| 작업 실행 조건 | 조건 값 | Description |
|--|--|--|
| daysAfterModificationGreaterThan | 일 단위로 보존 기간을 나타내는 정수 값 | 기본 Blob 작업에 대한 조건 |
| daysAfterCreationGreaterThan | 일 단위로 보존 기간을 나타내는 정수 값 | Blob 버전 및 Blob 스냅샷 작업에 대한 조건 |
| daysAfterLastAccessTimeGreaterThan | 일 단위로 보존 기간을 나타내는 정수 값 | 액세스 추적을 사용 하는 경우 기본 blob 동작에 대 한 조건 |

## <a name="examples-of-lifecycle-policies"></a>수명 주기 정책 예

다음 예는 수명 주기 정책 규칙을 사용하여 일반 시나리오를 해결하는 방법을 보여줍니다.

### <a name="move-aging-data-to-a-cooler-tier"></a>오래된 데이터를 쿨 저장소 계층으로 이동

이 예제는 `sample-container/blob1` 또는 `container2/blob2` 접두사가 붙은 블록 Blob을 전환하는 방법을 보여줍니다. 이 정책은 30일 넘게 수정되지 않은 BLOB을 쿨 스토리지 계층으로 전환하고, 90일 동안 수정되지 않은 BLOB을 보관 스토리지 계층으로 전환합니다.

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "sample-container/blob1", "container2/blob2" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="move-data-based-on-last-accessed-time"></a>마지막으로 액세스한 시간을 기준으로 데이터 이동

마지막 액세스 시간 추적을 사용하여 Blob을 마지막으로 읽거나 쓴 시점의 레코드를 유지하고 필터로 사용하여 Blob 데이터의 계층화와 보존을 관리할 수 있습니다. 마지막 액세스 시간 추적을 사용하는 방법은 [선택적으로 액세스 시간 추적 사용](lifecycle-management-policy-configure.md#optionally-enable-access-time-tracking)을 참조하세요.

마지막 액세스 시간 추적을 사용하면 Blob을 읽거나 쓸 때 `LastAccessTime`이라는 Blob 속성이 업데이트됩니다. [Blob 가져오기](/rest/api/storageservices/get-blob) 작업은 액세스 작업으로 간주됩니다. [Blob 속성 가져오기](/rest/api/storageservices/get-blob-properties), [Blob 메타데이터 가져오기](/rest/api/storageservices/get-blob-metadata) 및 [Blob 태그 가져오기](/rest/api/storageservices/get-blob-tags)는 액세스 작업이 아니므로 마지막 액세스 시간을 업데이트하지 않습니다.

읽기 액세스 대기 시간에 대한 영향을 최소화하기 위해 최근 24시간 동안의 첫 번째 읽기만 마지막 액세스 시간을 업데이트합니다. 동일한 24시간 동안의 이후 읽기는 마지막 액세스 시간을 업데이트하지 않습니다. 읽기 간에 Blob이 수정되는 경우 마지막 액세스 시간은 두 값 중 더 최근입니다.

다음 예에서는 30일 동안 액세스하지 않은 Blob이 쿨 스토리지로 이동됩니다. `enableAutoTierToHotFromCool` 속성은 Blob이 쿨로 계층화된 후 다시 액세스되는 경우 자동으로 쿨에서 다시 핫으로 계층화되어야 하는지 여부를 나타내는 부울 값입니다.

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

### <a name="archive-data-after-ingest"></a>수집 후 데이터 보관

일부 데이터는 클라우드에 유휴 상태로 유지되며 드물게 액세스됩니다. 다음 수명 주기 정책은 수집 직후 데이터를 보관하도록 구성되었습니다. 이 예에서는 `archivecontainer`라는 컨테이너의 블록 Blob을 보관 계층으로 전환합니다. 마지막으로 수정한 시간으로부터 0일 후 Blob에 대해 작업을 수행하여 전환을 완료합니다.

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

> [!NOTE]
> 효율성을 높이기 위해 Blob을 보관 계층에 직접 업로드하는 것이 좋습니다. [Blob 배치](/rest/api/storageservices/put-blob)나 [블록 목록 배치](/rest/api/storageservices/put-block-list) 작업의 *x-ms-access-tier* 헤더에서 보관 계층을 지정할 수 있습니다. REST 버전 2018-11-09 이상 또는 최신 Blob 스토리지 클라이언트 라이브러리에서 *x-ms-access-tier* 헤더가 지원됩니다.

### <a name="expire-data-based-on-age"></a>보존 기간에 따라 데이터 만료

일부 데이터는 생성되고 며칠 또는 몇 달 후에 만료될 것으로 예상됩니다. 데이터 보존 기간에 따라 삭제하여 데이터를 만료하도록 수명 주기 관리 정책을 구성할 수 있습니다. 다음 예에서는 365일보다 오래된 모든 블록 Blob을 삭제하는 정책을 보여줍니다.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-data-with-blob-index-tags"></a>Blob 인덱스 태그를 사용하여 데이터 삭제

일부 데이터는 명시적으로 삭제하도록 표시된 경우에만 만료되어야 합니다. Blob 인덱스 키/값 특성으로 태그가 지정된 데이터를 만료하도록 수명 주기 관리 정책을 구성할 수 있습니다. 다음 예에서는 `Project = Contoso` 태그가 지정된 모든 블록 Blob을 삭제하는 정책을 보여줍니다. Blob 인덱스에 대한 자세한 내용은 [Blob 인덱스를 사용하여 Azure Blob Storage에서 데이터 관리 및 찾기(미리 보기)](storage-manage-find-blobs.md)를 참조하세요.

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="manage-versions"></a>버전 관리

수명이 지속되는 동안 정기적으로 수정하고 액세스하는 데이터의 경우 Blob Storage 버전 관리를 사용하도록 설정하여 이전 버전의 개체를 자동으로 유지 관리할 수 있습니다. 정책을 생성하여 이전 버전을 계층화하거나 삭제할 수 있습니다. 버전 보존 기간은 버전 생성 시간을 평가하여 확인합니다. 이 정책 규칙은 버전 생성 후 90일이 경과한 컨테이너 `activedata` 내의 이전 버전을 쿨 계층으로 계층화하고 365일이 경과한 이전 버전을 삭제합니다.

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "versionrule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "tierToCool": {
              "daysAfterCreationGreaterThan": 90
            },
            "delete": {
              "daysAfterCreationGreaterThan": 365
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "activedata"
          ]
        }
      }
    }
  ]
}
```

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

| Storage 계정 유형                | Blob Storage(기본 지원)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) |![예](../media/icons/yes-icon.png)              | ![예](../media/icons/yes-icon.png) |
| Premium 블록 Blob          | ![예](../media/icons/yes-icon.png)|![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |

<sup>1</sup> Data Lake Storage Gen2와 NFS(네트워크 파일 시스템) 3.0 프로토콜 모두에는 계층 구조 네임스페이스를 사용하는 스토리지 계정이 필요합니다.

## <a name="regional-availability-and-pricing"></a>지역 가용성 및 가격

수명 주기 관리 기능은 모든 Azure 지역에서 사용할 수 있습니다.

수명 주기 관리 정책은 무료입니다. [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) API 호출에 대한 표준 작업 비용이 고객에게 청구됩니다. 삭제 작업은 무료입니다.

Blob의 마지막 액세스 시간에 대한 각 업데이트 비용은 [기타 작업](https://azure.microsoft.com/pricing/details/storage/blobs/) 범주에서 청구됩니다.

가격 책정에 대한 자세한 내용은 [블록 Blob 가격](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요.

## <a name="faq"></a>FAQ

**새 정책을 만들었습니다. 작업이 즉시 실행되지 않는 이유는 무엇인가요?**

플랫폼은 하루에 한 번 수명 주기 정책을 실행합니다. 정책을 구성한 후 일부 작업을 처음 실행하는 데 최대 24시간이 걸릴 수 있습니다.

**기존 정책을 업데이트하는 경우 작업이 실행되는 데 얼마나 걸리나요?**

업데이트된 정책은 적용되는 데 최대 24시간이 걸립니다. 정책이 적용되면 작업이 실행되는 데 최대 24시간이 걸릴 수 있습니다. 따라서 정책 작업을 완료하는 데 최대 48시간이 걸릴 수 있습니다. 업데이트 시 규칙을 사용하지 않거나 삭제하도록 하고 enableAutoTierToHotFromCool을 사용했으면 핫 계층으로 자동 계층화가 계속 발생합니다. 예를 들어 마지막 액세스를 기준으로 enableAutoTierToHotFromCool이 포함된 규칙을 설정합니다. 규칙이 사용되지 않거나 삭제되고 Blob이 현재 쿨 상태에서 액세스되면 수명 주기 관리 외부에 액세스할 때 적용되므로 다시 핫으로 이동합니다. 수명 주기 관리 규칙이 사용되지 않거나 삭제되면 Blob이 핫에서 쿨로 이동하지 않습니다. autoTierToHotFromCool을 방지하는 유일한 방법은 마지막 액세스 시간 추적을 끄는 것입니다.

**보관된 Blob을 수동으로 리하이드레이션했습니다. 보관 계층으로 다시 일시 이동되지 않게 하려면 어떻게 해야 하나요?**

Blob을 한 액세스 계층에서 다른 액세스 계층으로 이동하면 마지막 수정 시간이 변경되지 않습니다. 보관된 Blob을 핫 계층으로 수동 리하이드레이션하면 수명 주기 관리 엔진에 의해 보관 계층으로 다시 이동됩니다. 이 Blob에 영향을 주는 규칙을 일시적으로 사용하지 않게 설정하여 다시 보관되지 않도록 합니다 Blob을 다시 보관 계층으로 안전하게 이동할 수 있는 경우 규칙을 다시 사용하도록 설정합니다. 핫 또는 쿨 계층에 영구적으로 유지해야 하는 경우 Blob을 다른 위치에 복사할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [수명 주기 관리 정책 구성](lifecycle-management-policy-configure.md)
- [Blob 데이터에 대 한 핫, 쿨 및 보관 액세스 계층](access-tiers-overview.md)
- [Blob 인덱스를 사용하여 Azure Blob Storage에서 데이터 관리 및 찾기](storage-manage-find-blobs.md)
