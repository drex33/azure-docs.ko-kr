---
title: Azure Cost Management 내보내기로 recurringly 대량 비용 데이터 집합 검색
description: 이 문서를 사용 하면 Cost Management에서 내보내기로 많은 양의 데이터를 정기적으로 내보낼 수 있습니다.
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a27e64e7b5c94d033529c08ea02e757ba8857549
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705849"
---
# <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>내보내기를 사용하여 대규모 비용 데이터 세트 순환 검색

이 문서를 사용 하면 Cost Management에서 내보내기로 많은 양의 데이터를 정기적으로 내보낼 수 있습니다. 내보내기는 집계되지 않은 비용 데이터를 검색하는 데 추천되는 방법입니다. 특히 사용량 파일이 너무 커서 사용량 세부 정보 API를 사용하여 안정적으로 호출하고 다운로드할 수 없는 경우에 적합합니다. 내보낸 데이터는 선택한 Azure Storage 계정에 저장됩니다. 여기서 자신의 시스템에 로드하고 필요에 따라 분석할 수 있습니다. Azure Portal에서 내보내기를 구성하려면 [데이터 내보내기](tutorial-export-acm-data.md)를 참조하세요.

내보내기를 다양한 범위로 자동화하려면 다음 섹션의 샘플 API 요청이 적절한 시작 지점입니다. 내보내기 API를 사용하여 일반 환경 구성의 일부로 자동 내보내기를 만들 수 있습니다. 자동 내보내기는 필요한 데이터를 확보하는 데 도움이 됩니다. Azure 사용을 확장할 때 조직의 시스템에서 사용할 수 있습니다.

## <a name="common-export-configurations"></a>일반적인 내보내기 구성

첫 번째 내보내기를 만들기 전에 시나리오와 이를 사용하도록 설정하는 데 필요한 구성 옵션을 고려해야 합니다. 고려할 내보내기 옵션은 다음과 같습니다.

- **되풀이** - 내보내기 작업이 실행되는 빈도와 파일이 Azure Storage 계정에 저장되는 시기를 결정합니다. 매일, 매주 및 매월 중에서 선택합니다. 조직의 내부 시스템에서 사용하는 데이터 가져오기 작업과 일치하도록 되풀이를 구성해 봅니다.
- **되풀이 기간** - 내보내기가 유효한 상태로 유지되는 기간을 결정합니다. 파일은 되풀이 기간 동안에만 내보냅니다.
- **시간 프레임** - 지정된 실행에서 내보내기로 생성되는 데이터 양을 결정합니다. 일반적인 옵션은 MonthToDate 및 WeekToDate입니다.
- **StartDate** - 내보내기 일정을 시작하려는 시기를 구성합니다. 내보내기는 StartDate에 만들어지고, 이후에는 되풀이에 따라 만들어집니다.
- **유형** - 다음과 같은 세 가지 내보내기 형식이 있습니다.
  - ActualCost - 지정된 기간 동안 발생한 총 사용량 및 비용을 청구서에 표시합니다.
  - AmortizedCost - 지정된 기간의 총 사용량 및 비용을 표시하며, 해당되는 예약 구매 비용에는 상환이 적용됩니다.
  - Usage - 2020년 7월 20일 이전에 만든 모든 내보내기는 Usage 형식입니다. 예약된 모든 내보내기를 ActualCost 또는 AmortizedCost로 업데이트합니다.
- **열** – 내보내기 파일에 포함하려는 데이터 필드를 정의합니다. 이러한 필드는 사용량 세부 정보 API에서 사용할 수 있는 필드와 일치합니다. 자세한 내용은 [사용량 세부 정보 API](/rest/api/consumption/usagedetails/list)를 참조하세요.

## <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>구독에 대한 월간 누계 매일 내보내기 만들기

요청 URL: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

## <a name="copy-large-azure-storage-blobs"></a>대량 Azure 스토리지 Blob 복사

Cost Management를 사용하여 Azure 사용량 세부 정보를 Blob으로 Azure Storage 계정에 내보내도록 예약할 수 있습니다. 결과 Blob 크기는 기가바이트를 초과할 수 있습니다. Cost Management 팀은 Azure Storage 팀과 협력 하 여 대량 Azure Storage blob 복사를 테스트 합니다. 결과는 다음 섹션에서 설명하고 있습니다. 스토리지 Blob을 한 Azure 지역에서 다른 지역으로 복사하는 것과 비슷한 결과가 나타날 수 있습니다.

성능을 테스트하기 위해 팀에서 Blob을 미국 서부 지역의 스토리지 계정에서 동일한 지역 및 다른 지역으로 전송했습니다. 이 팀은 동일한 지역의 2GB/초에서 동남 아시아 지역에 있는 스토리지 계정의 150MB/초까지 다양한 속도를 측정했습니다.

### <a name="test-configuration"></a>테스트 구성

Blob 전송 속도를 측정하기 위해 팀에서 NuGet을 통해 최신 버전(v2.0.1)의 Azure DML(데이터 이동 라이브러리)을 참조하는 간단한 .NET 콘솔 애플리케이션을 만들었습니다. DML은 Azure Storage 팀에서 제공하는 SDK이며, 전송 서비스에 대한 프로그래밍 방식 액세스를 쉽게 합니다. 그런 다음, 여러 지역에서 Standard V2 스토리지 계정을 만들었고, 미국 서부를 원본 지역으로 사용했습니다. 각각 10개의 2GB 블록 Blob이 포함된 컨테이너로 스토리지 계정을 채웠습니다. DML의 _TransferManager.CopyDirectoryAsync()_ 메서드를 _CopyMethod.ServiceSideSyncCopy_ 옵션과 함께 사용하여 컨테이너를 다른 스토리지 계정에 복사했습니다. 테스트는 12개 코어 및 1GbE 네트워크를 통해 Windows 10을 실행하는 컴퓨터에서 수행되었습니다.

사용된 애플리케이션 설정은 다음과 같습니다.

- _TransferManager.Configurations.ParallelOperations_ = _Environment.ProcessorCount \* 32_. 팀에서 전체 처리량에 가장 큰 영향을 주는 설정을 찾았습니다. 코어 수의 32배 값은 최상의 처리량을 테스트 클라이언트에 제공했습니다.
- _ServicePointManager.DefaultConnectionLimit = int.MaxValue_. 이를 최댓값으로 설정하면 전송 병렬 처리에 대한 전체 제어가 위의 _ParallelOperations_ 설정에 효과적으로 전달됩니다.
- _TransferManager.Configurations.BlockSize = 4,194,304_. 4MB의 전송 속도에 약간의 영향을 주었으므로 테스트에 가장 적합한 것으로 입증되었습니다.

자세한 내용 및 샘플 코드는 [다음 단계](#next-steps) 섹션의 링크를 참조하세요.

### <a name="test-results"></a>테스트 결과

| **테스트 번호** | **대상 지역** | **Blob** | **시간(초)** | **MB/초** | **설명** |
| --- | --- | --- | --- | --- | --- |
| 1 | WestUS | 2GB x 10 | 10 | 2,000 |   |
| 2 | WestUS2 | 2GB x 10 | 33 | 600 |   |
| 3 | EastUS | 2GB x 10 | 67 | 300 |   |
| 4 | EastUS | 2GB x 10 x 4 | 99 | 200 | 4개 병렬 전송(8개 스토리지 계정 사용, 전송당 평균 4개 서부 지역 스토리지 계정에서 4개 동부 지역 스토리지 계정으로) |
| 6 | EastUS | 2GB x 10 x 4 | 92 | 870 | 4개 병렬 전송(한 스토리지 계정에서 다른 스토리지 계정으로) |
| 5 | EastUS | 2GB x 10 x 8 | 148 | 135 | 8개 병렬 전송(8개 스토리지 계정 사용, 전송당 평균 4개 서부 지역 스토리지 계정에서 4x2개 동부 지역 스토리지 계정으로) |
| 7 | 동남 아시아 | 2GB x 10 | 133 | 150 |   |
| 8 | 동남 아시아 | 2GB x 10 x 4 | 444 | 180 | 4개 병렬 전송(한 스토리지 계정에서 다른 스토리지 계정으로) |

### <a name="sync-transfer-characteristics"></a>동기 전송 특성

DML을 통해 사용되는 서비스 쪽 동기 전송의 몇 가지 특성은 다음과 같으며, 해당 사용과 관련이 있습니다.

- DML은 단일 Blob 또는 디렉터리를 전송할 수 있습니다. 디렉터리 전송의 경우 검색 패턴을 사용하여 Blob 접두사에서 일치 항목을 찾을 수 있습니다.
- 블록 Blob 전송은 병렬로 수행됩니다. 전송 프로세스가 완료되면 모든 전송이 완료됩니다. 개별 Blob 블록이 병렬로 전송됩니다.
- 전송은 클라이언트에서 비동기적으로 실행됩니다. 전송 상태는 _TransferContext_ 개체에 정의할 수 있는 메서드에 대한 콜백을 통해 정기적으로 사용할 수 있습니다.
- 전송은 진행 중에 검사점을 만들고 _TransferCheckpoint_ 개체를 표시합니다. 개체는 _TransferContext_ 개체를 통해 최신 검사점을 나타냅니다. 전송이 취소/중단되기 전에 _TransferCheckpoint_ 가 저장되면 최대 7일 동안 검사점에서 전송을 다시 시작할 수 있습니다. 전송은 최신 검사점뿐만 아니라 모든 검사점에서 다시 시작할 수 있습니다.
- 검사점 기능을 구현하지 않고 클라이언트 전송 프로세스가 중지되고 다시 시작되는 경우입니다.
  - Blob 전송이 완료되기 전에 전송이 다시 시작됩니다.
  - 일부 Blob이 완료되면 완료되지 않은 Blob에 대해서만 전송이 다시 시작됩니다.
- 클라이언트 실행을 일시 중지하면 전송도 일시 중지됩니다.
- Blob 전송 기능은 일시적인 오류로부터 클라이언트를 추상화합니다. 예를 들어 스토리지 계정 제한으로 인해 일반적으로 전송이 실패하지는 않지만 전송 속도가 느려집니다.
- 서비스 쪽 전송은 CPU/메모리, 일부 네트워크 대역폭 및 연결에 대한 클라이언트 리소스 사용량이 적습니다.

### <a name="async-transfer-characteristics"></a>비동기 전송 특성

_TransferManager.CopyDirectoryAsync()_ 메서드는 _CopyMethod.ServiceSideAsyncCopy_ 옵션을 사용하여 호출할 수 있습니다. 클라이언트 관점에서 동기 전송 메커니즘과 비슷하게 작동하지만, 다음과 같은 작동상의 차이점이 있습니다.

- 전송 속도가 동일한 동기 전송보다 훨씬 느립니다(일반적으로 10MB/초 미만).
- 클라이언트 프로세스가 종료되어도 전송이 계속됩니다.
- 검사점이 지원되지만 _TransferCheckpoint_ 를 사용하여 전송을 다시 시작하는 것은 검사점 시간이 아니라 전송의 현재 상태에서 다시 시작합니다.

### <a name="test-summary"></a>테스트 요약

Azure Blob 스토리지는 서비스 쪽 동기 전송 기능을 통해 높은 글로벌 전송 속도를 지원합니다. .NET 애플리케이션에서 이 기능을 사용하는 것은 데이터 이동 라이브러리를 사용하는 간단한 방법입니다. Cost Management 내보내기에서 한 시간 내에 수백 기가바이트의 데이터를 스토리지 계정에 안정적으로 복사할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure Storage 데이터 이동 라이브러리](https://github.com/Azure/azure-storage-net-data-movement) 원본 참조
- [데이터 이동 라이브러리를 사용하여 데이터 전송](../../storage/common/storage-use-data-movement-library.md)
- [AzureDmlBackup 샘플 애플리케이션](https://github.com/markjbrown/AzureDmlBackup) 원본 참조
- [Azure Blob Storage를 사용한 높은 처리량](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage) 참조