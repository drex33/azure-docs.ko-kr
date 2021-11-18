---
title: Azure Storage Blob 인벤토리
description: Azure Storage 인벤토리는 스토리지 계정 내의 모든 Blob 데이터에 대한 개요를 가져오는 데 도움이 되는 도구입니다.
services: storage
author: normesta
ms.service: storage
ms.date: 10/11/2021
ms.topic: conceptual
ms.author: normesta
ms.reviewer: klaasl
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 0de81ce6f7f6f042ce0e0cb933a85566af7f3875
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724162"
---
# <a name="azure-storage-blob-inventory"></a>Azure Storage Blob 인벤토리

Azure Storage Blob 인벤토리 기능은 스토리지 계정 내의 컨테이너, Blob, 스냅샷 및 Blob 버전의 개요를 제공합니다. 인벤토리 보고서를 사용하여 전체 데이터 크기, 사용 기간, 암호화 상태, 불변성 정책, 법적 보류 등의 다양한 Blob 및 컨테이너 특성을 이해할 수 있습니다. 이 보고서는 비즈니스 및 규정 준수 요구 사항에 대한 데이터 개요를 제공합니다.

## <a name="inventory-features"></a>인벤토리 기능

다음 목록에서는 Azure Storage Blob 인벤토리의 현재 릴리스에서 제공되는 기능에 대해 설명합니다.

- **Blob 및 컨테이너에 대한 인벤토리 보고서**

  Blob 및 컨테이너에 대한 인벤토리 보고서를 생성할 수 있습니다. Blob에 대한 보고서에는 기본 Blob, 스냅샷, Blob 버전 및 만든 시간, 마지막으로 수정한 시간 등의 관련 속성이 포함될 수 있습니다. 컨테이너에 대한 보고서는 컨테이너 및 불변성 정책 상태, 법적 보존 상태와 같은 컨테이너 관련 속성을 설명합니다.

- **사용자 지정 스키마**

  보고서에 표시할 필드를 선택할 수 있습니다. 지원되는 필드 목록에서 선택합니다. 이 목록은 이 문서의 뒷부분에 나와 있습니다.

- **CSV 및 Apache Parquet 출력 형식**

  인벤토리 보고서는 CSV 또는 Apache Parquet 출력 형식으로 생성할 수 있습니다.

- **인벤토리 보고서별 매니페스트 파일 및 Azure Event Grid 이벤트**

  매니페스트 파일 및 Azure Event Grid 이벤트는 인벤토리 보고서별로 생성됩니다. 이 내용은 이 문서의 뒷부분에서 설명합니다.

## <a name="enabling-inventory-reports"></a>인벤토리 보고서 사용

스토리지 계정에 하나 이상의 규칙이 있는 정책을 추가하여 Blob 인벤토리 보고서를 사용합니다. 자세한 지침은 [Azure Storage Blob 인벤토리 보고서 사용](blob-inventory-how-to.md)을 참조하세요.

## <a name="upgrading-an-inventory-policy"></a>인벤토리 정책 업그레이드

2021년 6월 전에 인벤토리를 구성한 기존 Azure Storage Blob 인벤토리 사용자인 경우 정책을 로드하고 변경한 다음, 정책을 다시 저장하면 새 기능을 사용할 수 있습니다. 정책을 다시 로드하면 정책의 새 필드가 기본값으로 채워집니다. 원한다면 이 값을 변경할 수 있습니다. 또한 다음 두 가지 기능을 사용할 수 있습니다.

- 이제 대상 컨테이너가 정책뿐 아니라 모든 규칙에 대해 지원됩니다.

- 이제 매니페스트 파일 및 Azure Event Grid 이벤트가 정책이 아닌 규칙별로 생성됩니다.

## <a name="inventory-policy"></a>인벤토리 정책

인벤토리 보고서는 하나 이상의 규칙을 포함하는 인벤토리 정책을 추가하여 구성됩니다. 인벤토리 정책은 JSON 문서에 있는 규칙의 컬렉션입니다.

```json
{
  "enabled": true,
  "rules": [
  {
    "enabled": true,
    "name": "inventoryrule1",
    "destination": "inventory-destination-container",
    "definition": {. . .}
  },
  {
    "enabled": true,
    "name": "inventoryrule2",
    "destination": "inventory-destination-container",
    "definition": {. . .}
  }]
}
```

Azure Portal의 **Blob 인벤토리** 섹션에서 **코드 보기** 탭을 선택하여 인벤토리 정책에 대한 JSON을 표시합니다.

| 매개 변수 이름 | 매개 변수 형식 | 참고 | 필수 여부 |
|--|--|--|--|
| 사용 | boolean | 전체 정책을 비활성화하는 데 사용됩니다. **true** 로 설정되면 규칙 수준 사용 필드가 이 매개 변수를 재정의합니다. 사용하지 않도록 설정하면 모든 규칙에 대한 인벤토리가 사용하지 않도록 설정됩니다. | 예 |
| 규칙 | 규칙 개체의 배열 | 정책에 하나 이상의 규칙이 필요합니다. 정책마다 최대 100개의 규칙이 지원됩니다. | 예 |

## <a name="inventory-rules"></a>인벤토리 규칙

규칙은 인벤토리 보고서를 생성하기 위한 필터링 조건 및 출력 매개 변수를 캡처합니다. 각 규칙은 인벤토리 보고서를 만듭니다. 규칙에는 겹치는 접두사가 있을 수 있습니다. Blob은 규칙 정의에 따라 1개보다 많은 인벤토리에 표시될 수 있습니다.

정책 내의 각 규칙에는 다음과 같은 몇 가지 매개 변수가 있습니다.

| 매개 변수 이름 | 매개 변수 형식 | 참고 | 필수 여부 |
|--|--|--|--|
| name | 문자열 | 규칙 이름은 최대 256자의 대/소문자를 구분하는 영숫자를 포함할 수 있습니다. 이름은 정책 내에서 고유해야 합니다. | 예 |
| 사용 | boolean | 규칙을 사용하거나 사용하지 않도록 설정할 수 있는 플래그입니다. 기본값은 **true** 입니다. | 예 |
| 정의 | JSON 인벤토리 규칙 정의 | 각 정의는 규칙 필터 집합으로 구성됩니다. | 예 |
| destination | 문자열 | 모든 인벤토리 파일이 생성될 대상 컨테이너입니다. 대상 컨테이너는 이미 있어야 합니다. |

글로벌 **Blob 인벤토리 사용** 플래그는 규칙의 *사용* 매개 변수보다 우선 적용됩니다.

### <a name="rule-definition"></a>규칙 정의

| 매개 변수 이름 | 매개 변수 형식 | 참고 | 필수 |
|--|--|--|--|
| filters | json : | 필터는 Blob 또는 컨테이너가 인벤토리에 포함되는지 여부를 결정합니다. | Yes |
| format | 문자열 | 인벤토리 파일의 출력을 결정합니다. 유효한 값은 `csv`(CSV 형식의 경우) 및 `parquet`(Apache Parquet 형식의 경우)입니다.| Yes |
| objectType | 문자열 | 이 인벤토리 규칙이 Blob에 대한 것인지 아니면 컨테이너에 대한 것인지를 나타냅니다. 유효한 값은 `blob` 및 `container`입니다. |Yes |
| schedule | 문자열 | 이 규칙을 실행할 일정입니다. 유효한 값은 `daily` 및 `weekly`입니다. | Yes |
| schemaFields | Json 배열 | 인벤토리에 포함될 스키마 필드의 목록입니다. | Yes |

### <a name="rule-filters"></a>규칙 필터

Blob 인벤토리 보고서를 사용자 지정하는 데 사용할 수 있는 몇 가지 필터는 다음과 같습니다.

| 필터 이름 | 필터 형식 | 참고 | 필수 여부 |
|--|--|--|--|
| blobTypes | 미리 정의된 열거형 값의 배열 | 유효한 값은 계층 구조 네임스페이스 사용 계정의 경우 `blockBlob` 및 `appendBlob`이고, 다른 계정의 경우 `blockBlob`, `appendBlob` 및 `pageBlob`입니다. 이 필드는 컨테이너의 인벤토리에는 적용되지 않습니다(objectType: `container`). | 예 |
| prefixMatch | 일치시킬 접두사에 대한 최대 10개의 문자열 배열입니다. | *prefixMatch* 를 정의하지 않거나 빈 접두사를 제공하는 경우 규칙은 스토리지 계정 내의 모든 Blob에 적용됩니다. 접두사는 컨테이너 이름 접두사 또는 컨테이너 이름이어야 합니다. 예: `container`, `container1/foo` | No |
| includeSnapshots | boolean | 인벤토리에 스냅샷을 포함할지 여부를 지정합니다. 기본값은 `false`입니다. 이 필드는 컨테이너의 인벤토리에는 적용되지 않습니다(objectType: `container`). | No |
| includeBlobVersions | boolean | 인벤토리에 Blob 버전을 포함할지 여부를 지정합니다. 기본값은 `false`입니다. 이 필드는 컨테이너의 인벤토리에는 적용되지 않습니다(objectType: `container`). | No |

Azure Portal의 **Blob 인벤토리** 섹션에서 **코드 보기** 탭을 선택하여 인벤토리 규칙에 대한 JSON을 표시합니다. 필터는 규칙 정의 내에서 지정됩니다.

```json
{
  "destination": "inventory-destination-container",
  "enabled": true,
  "rules": [
  {
    "definition": {
      "filters": {
        "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
        "prefixMatch": ["inventorytestcontainer1", "inventorytestcontainer2/abcd", "etc"],
        "includeSnapshots": false,
        "includeBlobVersions": true,
      },
      "format": "csv",
      "objectType": "blob",
      "schedule": "daily",
      "schemaFields": ["Name", "Creation-Time"]
    },
    "enabled": true,
    "name": "blobinventorytest",
    "destination": "inventorydestinationContainer"
  },
  {
    "definition": {
      "filters": {
        "prefixMatch": ["inventorytestcontainer1", "inventorytestcontainer2/abcd", "etc"]
      },
      "format": "csv",
      "objectType": "container",
      "schedule": "weekly",
      "schemaFields": ["Name", "HasImmutabilityPolicy", "HasLegalHold"]
    },
    "enabled": true,
    "name": "containerinventorytest",
    "destination": "inventorydestinationContainer"
    }
  ]
}
```

### <a name="custom-schema-fields-supported-for-blob-inventory"></a>Blob 인벤토리에 대해 지원되는 사용자 지정 스키마 필드

- Name(필수)
- Creation-Time
- Last-Modified
- Content-Length
- Content-MD5
- BlobType
- AccessTier
- AccessTierChangeTime
- Expiry-Time
- hdi_isfolder
- 소유자
- 그룹
- 사용 권한
- Acl
- Snapshot(보고서에 스냅샷을 포함하도록 선택하는 경우에 사용 가능한 필수 필드)
- VersionId(보고서에 Blob 버전을 포함하도록 선택하는 경우에 사용 가능한 필수 필드)
- IsCurrentVersion(보고서에 Blob 버전을 포함하도록 선택하는 경우에 사용 가능한 필수 필드)
- 메타데이터
- LastAccessTime

### <a name="custom-schema-fields-supported-for-container-inventory"></a>컨테이너 인벤토리에 대해 지원되는 사용자 지정 스키마 필드

- Name(필수)
- Last-Modified
- LeaseStatus
- LeaseState
- LeaseDuration
- PublicAccess
- HasImmutabilityPolicy
- HasLegalHold
- 메타데이터

## <a name="inventory-run"></a>인벤토리 실행

Blob 인벤토리 실행은 매일 자동으로 예약됩니다. 인벤토리 실행을 완료하는 데 최대 24시간이 걸릴 수 있습니다. 계층 구조 네임스페이스가 설정된 계정의 경우 실행은 2일 정도 걸릴 수 있으며 처리 중인 파일 수에 따라 해당 2일이 끝날 때까지 실행이 완료되지 않을 수 있습니다. 실행이 성공적으로 완료되지 않으면 후속 실행을 확인하여 지원 담당자에게 문의하기 전에 완료되었는지 확인합니다. 실행 성능은 달라질 수 있으므로 실행이 완료되지 않으면 후속 실행이 실행될 수 있습니다.

인벤토리 정책은 전체로 읽거나 쓸 수 있습니다. 부분 업데이트는 지원되지 않습니다.

> [!IMPORTANT]
> 스토리지 계정에 대해 방화벽 규칙을 사용하도록 설정하면 인벤토리 요청이 차단될 수 있습니다. 신뢰할 수 있는 Microsoft 서비스에 대한 예외를 제공하여 이러한 요청의 차단을 해제할 수 있습니다. 자세한 내용은 [방화벽 및 가상 네트워크 구성](../common/storage-network-security.md#exceptions)의 예외 섹션을 참조하세요.

## <a name="inventory-completed-event"></a>인벤토리 완료 이벤트

규칙에 대한 인벤토리 실행이 완료되면 `BlobInventoryPolicyCompleted` 이벤트가 생성됩니다. 인벤토리가 실행되기 전에 사용자 오류로 인해 인벤토리 실행이 실패하는 경우에도 이 이벤트가 발생합니다. 예를 들어 잘못된 정책 또는 대상 컨테이너가 없어서 발생하는 오류는 이 이벤트를 트리거합니다. 다음 json은 `BlobInventoryPolicyCompleted` 이벤트 예시를 보여줍니다.

```json
{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "data": {
    "scheduleDateTime": "2021-05-28T03:50:27Z",
    "accountName": "testaccount",
    "ruleName": "Rule_1",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "manifestBlobUrl": "https://testaccount.blob.core.windows.net/inventory-destination-container/2021/05/26/13-25-36/Rule_1/Rule_1.csv"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-05-28T15:03:18Z"
}
```

다음 표에는 `BlobInventoryPolicyCompleted` 이벤트의 스키마가 설명되어 있습니다.

|필드|형식|설명|
|---|---|
|scheduleDateTime|문자열|인벤토리 정책이 예약된 시간입니다.|
|accountName|문자열|스토리지 계정 이름입니다.|
|ruleName|문자열|규칙 이름입니다.|
|policyRunStatus|문자열|인벤토리 실행의 상태입니다. 가능한 값은 `Succeeded`, `PartiallySucceeded` 및 `Failed`입니다.|
|policyRunStatusMessage|문자열|인벤토리 실행에 대한 상태 메시지입니다.|
|policyRunId|문자열|인벤토리 실행의 정책 실행 ID입니다.|
|manifestBlobUrl|문자열|인벤토리 실행을 위한 매니페스트 파일의 Blob URL입니다.|

## <a name="inventory-output"></a>인벤토리 출력

인벤토리가 실행될 때마다 해당 규칙에 대해 지정된 인벤토리 대상 컨테이너에 파일 세트가 생성됩니다. 인벤토리 출력이 다음 경로: `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/<ruleName`에서 생성됩니다. 여기에서

- *accountName* 은 Azure Blob Storage 계정 이름입니다.
- *inventory-destination-container* 는 인벤토리 규칙에서 지정한 대상 컨테이너입니다.
- *YYYY/MM/DD/HH-MM-SS* 는 인벤토리가 실행되기 시작한 시간입니다.
- *ruleName* 은 인벤토리 규칙 이름입니다.

### <a name="inventory-files"></a>인벤토리 파일

규칙에 대한 인벤토리를 실행할 때마다 다음 파일이 생성됩니다.

- **인벤토리 파일:** 규칙에 대한 인벤토리 실행은 하나 이상의 CSV 또는 Apache Parquet 형식의 파일을 생성합니다. 일치하는 개체 수가 많으면 단일 파일 대신 여러 개의 파일이 생성됩니다. 각 파일에는 일치하는 개체와 해당 메타데이터가 포함됩니다. 

  > [!NOTE]
  > Apache Parquet 형식의 보고서는 날짜를 `timestamp_millis [number of milliseconds since 1970-01-01 00:00:00 UTC` 형식으로 표시합니다.

  CSV 형식 파일의 경우 첫 번째 행은 항상 스키마 행입니다. 다음 이미지는 Microsoft Excel에서 열린 인벤토리 CSV 파일을 보여 줍니다.

  :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Microsoft Excel에서 열린 인벤토리 CSV 파일의 스크린샷":::

  > [!IMPORTANT]
  > 인벤토리 파일에 표시되는 Blob 경로는 특정 순서로 표시되지 않을 수 있습니다. 

- **체크섬 파일:** 체크섬 파일에는 manifest.json 파일 내용의 MD5 체크섬이 포함됩니다. 체크섬 파일의 이름은 `<ruleName>-manifest.checksum`입니다. 체크섬 파일이 생성되면 인벤토리 실행이 완료되었다는 뜻입니다.

- **매니페스트 파일:** manifest.json 파일에는 해당 규칙에 대해 생성된 인벤토리 파일의 세부 정보가 포함됩니다. 파일 이름은 `<ruleName>-manifest.json`입니다. 또한 이 파일은 사용자가 제공하는 규칙 정의와 해당 규칙에 대한 인벤토리의 경로를 캡처합니다. 다음 json은 샘플 manifest.json 파일의 콘텐츠를 보여줍니다.

  ```json
  {
  "destinationContainer" : "inventory-destination-container",
  "endpoint" : "https://testaccount.blob.core.windows.net",
  "files" : [
    {
      "blob" : "2021/05/26/13-25-36/Rule_1/Rule_1.csv",
      "size" : 12710092
    }
  ],
  "inventoryCompletionTime" : "2021-05-26T13:35:56Z",
  "inventoryStartTime" : "2021-05-26T13:25:36Z",
  "ruleDefinition" : {
    "filters" : {
      "blobTypes" : [ "blockBlob" ],
      "includeBlobVersions" : false,
      "includeSnapshots" : false,
      "prefixMatch" : [ "penner-test-container-100003" ]
    },
    "format" : "csv",
    "objectType" : "blob",
    "schedule" : "daily",
    "schemaFields" : [
      "Name",
      "Creation-Time",
      "BlobType",
      "Content-Length",
      "LastAccessTime",
      "Last-Modified",
      "Metadata",
      "AccessTier"
    ]
  },
  "ruleName" : "Rule_1",
  "status" : "Succeeded",
  "summary" : {
    "objectCount" : 110000,
    "totalObjectSize" : 23789775
  },
  "version" : "1.0"
  }
  ```

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구

인벤토리 가격 책정은 청구 기간 동안 검사되는 Blob 및 컨테이너 수를 기반으로 합니다. 예를 들어 계정에 100만 개의 Blob이 포함되어 있고 Blob 인벤토리가 일주일에 한 번 실행되도록 설정되었다고 가정하겠습니다. 4주 후에는 400만 개의 Blob 항목이 검사됩니다.

Blob 인벤토리 대금 청구는 2021년 10월 1일부터 시작됩니다. 지역별 가격 책정은 이 시간에 게시됩니다. 지역별 조정이 적용되지 않은 기준 가격은 Blob 스토리지에 대해 검사되는 항목 100만 개당 약 $0.0025 USD, Data Lake Storage Gen2를 사용하는 경우에는 $0.0035 USD입니다. 인벤토리 파일을 만든 후에는 인벤토리에서 생성한 파일을 계정에서 저장하고, 읽고, 쓰기 위한 추가적인 표준 데이터 스토리지 및 작업 요금이 발생합니다.

인벤토리 보고서가 완료되면 인벤토리 보고서를 스토리지 계정에서 저장하고, 읽고, 쓰기 위한 추가적인 표준 데이터 스토리지 및 작업 요금이 발생합니다.

다른 규칙의 접두사와 겹치는 접두사가 규칙에 포함되어 있는 경우 여러 인벤토리 보고서에 동일한 Blob이 표시될 수 있습니다. 이 경우 두 인스턴스 모두에 대한 요금이 청구됩니다. 예를 들어 한 규칙의 `prefixMatch` 요소가 `["inventory-blob-1", "inventory-blob-2"]`로 설정되었고 다른 규칙의 `prefixMatch` 요소가 `["inventory-blob-10", "inventory-blob-20"]`으로 설정되었다고 가정하겠습니다. `inventory-blob-200`이라는 개체는 두 인벤토리 보고서에 모두 표시됩니다.

`includeSnapshots` 및 `includeVersions` 필터를 `false`로 설정했더라도 Blob의 스냅샷 및 버전에 대한 요금이 계산됩니다. 이러한 필터 값은 요금 청구에 영향을 주지 않습니다. 이러한 필터 값은 보고서에 표시되는 항목을 필터링하는 데만 사용할 수 있습니다.

Azure Storage Blob 인벤토리의 가격 책정에 대한 자세한 내용은 [Azure Blob Storage 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요.

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

| Storage 계정 유형 | Blob Storage(기본 지원) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|--|--|--|--|--|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png)  <sup>2</sup>              | ![예](../media/icons/yes-icon.png) <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |
| Premium 블록 Blob | ![예](../media/icons/yes-icon.png)| ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> | ![예](../media/icons/yes-icon.png)  <sup>2</sup> |

<sup>1</sup> Data Lake Storage Gen2, NFS(네트워크 파일 시스템) 3.0 프로토콜 및 SFTP(보안 파일 전송 프로토콜) 지원은 모두 계층 구조 네임스페이스를 사용하도록 설정된 스토리지 계정이 필요합니다.

<sup>2</sup> 기능은 미리 보기 수준에서 지원됩니다.

## <a name="known-issues"></a>알려진 문제

이 섹션에서는 Azure Storage Blob 인벤토리 기능의 제한 사항 및 알려진 문제에 대해 설명합니다.

### <a name="inventory-job-fails-to-complete-for-hierarchical-namespace-enabled-accounts"></a>계층 구조 네임스페이스를 사용하는 계정에 대한 인벤토리 작업이 완료되지 않음

수억 개의 Blob 및 계층 구조 네임스페이스를 사용하도록 설정된 계정의 경우 2일 이내에 인벤토리 작업이 완료되지 않을 수 있습니다. 이 경우 인벤토리 파일이 생성되지 않습니다. 작업이 성공적으로 완료되지 않으면 지원 담당자에게 문의하기 전에 후속 작업이 완료되었는지 확인합니다. 작업의 성능은 달라질 수 있으므로 작업이 완료되지 않으면 후속 작업이 수행될 수 있습니다.

### <a name="inventory-job-cannot-write-inventory-reports"></a>인벤토리 작업에서 인벤토리 보고서를 쓸 수 없음

개체 복제 정책 때문에 인벤토리 작업에서 대상 컨테이너에 인벤토리 보고서를 기록하지 못할 수 있습니다. 상황에 따라 보고서를 보관할 수도 있고, 보고서가 부분적으로 완료되면 보고서를 변경할 수 없을 수도 있습니다. 이로 인해 인벤토리 작업이 실패할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Storage Blob 인벤토리 보고서 사용](blob-inventory-how-to.md)
- [컨테이너당 Blob의 개수 및 총 크기 계산](calculate-blob-count-size.md)
- [Azure Blob Storage 수명 주기 관리](./lifecycle-management-overview.md)
