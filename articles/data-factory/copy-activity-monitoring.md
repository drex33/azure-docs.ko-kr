---
title: 복사 작업 모니터
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics에서 복사 작업 실행을 모니터링하는 방법을 알아보세요.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: ed65bb5bcde37bf4ad18d9650697d51640ac1e4a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124767587"
---
# <a name="monitor-copy-activity"></a>복사 작업 모니터

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Synapse 파이프라인에서 복사 작업 실행을 모니터링하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="monitor-visually"></a>시각적으로 모니터링

파이프라인을 만들어 게시한 후에는 트리거와 연결하거나 임시 실행을 수동으로 시작할 수 있습니다. 사용자 환경에서 기본적으로 모든 파이프라인 실행을 모니터링할 수 있습니다. [Azure Data Factory 및 Synapse 파이프라인 시각적 모니터링](monitor-visually.md)에서 일반적인 모니터링에 대해 알아보세요.

복사 작업 실행을 모니터링하려면 서비스 인스턴스의 **Data Factory Studio** 또는 **Azure Synapse Studio** UI로 이동합니다. **모니터링** 탭에서 파이프라인 실행 목록이 보이면 **파이프라인 이름** 링크를 클릭하여 파이프라인 실행의 활동 실행 목록에 액세스합니다.

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

:::image type="content" source="./media/copy-activity-overview/monitor-pipeline-run.png" alt-text="파이프라인 실행 모니터링":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

:::image type="content" source="./media/copy-activity-overview/monitor-pipeline-run-synapse.png" alt-text="파이프라인 실행 모니터링":::

---

이 수준에서는 복사 작업 입력, 출력, 오류(복사 작업 실행이 실패한 경우)에 대한 링크뿐만 아니라 기간/상태와 같은 통계도 볼 수 있습니다. 복사 작업 이름 옆의 **자세히** 단추(안경)를 클릭하면 복사 작업 실행에 대한 자세한 정보가 제공됩니다. 

:::image type="content" source="./media/copy-activity-overview/monitor-copy-activity-run.png" alt-text="복사 작업 실행 모니터링":::

이 그래픽 모니터링 보기에서 이 서비스는 데이터 읽기/쓰기 볼륨, 원본에서 싱크로 복사된 데이터 파일/행 수, 처리량, 복사 시나리오에 적용된 구성, 복사 작업이 진행되는 단계와 소요되는 기간 및 세부 정보를 비롯한 복사 작업 실행 정보를 제공합니다. 가능한 각 메트릭과 메트릭에 대한 세부 설명을 다룬 [이 표](#monitor-programmatically)를 참조하세요. 

일부 시나리오에서는 복사 작업을 실행할 때, 예제에서 보이는 것과 같이 복사 작업 모니터링 보기의 맨 위에 **“성능 튜닝 팁”** 이 표시됩니다. 이 팁은 특정 복사 실행에서 서비스가 식별한 병목 상태를 알려주고, 복사 처리량 향상을 위해 어떤 점을 바꿔야 하는지 제안합니다. [자동 성능 튜닝 팁](copy-activity-performance-troubleshooting.md#performance-tuning-tips)에 대해 자세히 알아보세요.

아래의 **실행 세부 정보 및 기간** 은 복사 작업이 진행되는 주요 단계를 설명하며, 이는 복사 성능 문제를 해결하는 데 특히 유용합니다. 복사 실행 시 가장 오랜 기간이 소요되는 작업이 복사 실행의 병목 현상에 해당합니다. 각 스테이지가 나타내는 내용과 자세한 문제 해결 지침을 다루는 [복사 작업 성능 문제 해결](copy-activity-performance-troubleshooting.md)을 참조하세요.

**예: Amazon S3에서 Azure Data Lake Storage Gen2로 복사**

:::image type="content" source="./media/copy-activity-overview/monitor-copy-activity-run-details.png" alt-text="복사 작업 실행 세부 정보 모니터링":::

## <a name="monitor-programmatically"></a>프로그래밍 방식으로 모니터링

복사 작업 실행 세부 정보 및 성능 특성도 **복사 작업 실행 결과** > **출력** 섹션에 반환되며, 이는 UI 모니터링 보기를 렌더링하는 데 사용됩니다. 다음은 반환될 수 있는 속성의 전체 목록입니다. 내 복사 시나리오에 적용할 수 있는 속성만 표시됩니다. 일반적인 프로그래밍 방식으로 활동 실행을 모니터링하는 방법에 대한 자세한 내용은 [프로그래밍 방식으로 Azure Data Factory 또는 Synapse 파이프라인 모니터링](monitor-programmatically.md)을 참조하세요.

| 속성 이름  | 설명 | 출력 단위 |
|:--- |:--- |:--- |
| DataRead | 원본에서 읽어 온 실제 데이터 양입니다. | Int64 값(바이트) |
| DataWritten | 싱크에 기록/커밋된 실제 데이터 양입니다. 각 데이터 저장소가 데이터를 저장하는 방법과 관련되므로 `dataRead`의 크기와 크기가 다를 수 있습니다. | Int64 값(바이트) |
| filesRead | 파일 기반 원본에서 읽어 온 파일의 수입니다. | Int64 값(단위 없음) |
| filesWritten | 파일 기반 싱크에 기록/커밋된 파일의 수입니다. | Int64 값(단위 없음) |
| filesSkipped | 파일 기반 원본에서 건너뛴 파일의 수입니다. | Int64 값(단위 없음) |
| dataConsistencyVerification | 원본 저장소와 대상 저장소 간에 복사한 데이터가 일관되는지 볼 수 있는 데이터 일관성 확인에 대한 세부 정보입니다. [이 문서](copy-activity-data-consistency.md#monitoring)에서 자세히 알아보세요. | Array |
| sourcePeakConnections | 원본 데이터 저장소에 설정된 복사 작업을 실행하는 동안의 최대 동시 연결 수입니다. | Int64 값(단위 없음) |
| sinkPeakConnections | 싱크 데이터 저장소에 설정된 복사 작업을 실행하는 동안의 최대 동시 연결 수입니다. | Int64 값(단위 없음) |
| rowsRead | 원본에서 읽어 온 행의 수입니다. 이 메트릭은 원본 및 싱크 데이터 세트가 이진 형식 유형이거나 동일한 설정을 사용하는 다른 형식 유형인 경우 등 파일을 구문 분석하지 않고 있는 그대로 복사하는 경우에는 적용되지 않습니다. | Int64 값(단위 없음) |
| rowsCopied | 싱크로 복사된 행의 수입니다. 이 메트릭은 원본 및 싱크 데이터 세트가 이진 형식 유형이거나 동일한 설정을 사용하는 다른 형식 유형인 경우 등 파일을 구문 분석하지 않고 있는 그대로 복사하는 경우에는 적용되지 않습니다.  | Int64 값(단위 없음) |
| rowsSkipped | 건너뛴 비호환 행의 수입니다. `enableSkipIncompatibleRow`을 true로 설정하여 호환되지 않는 행을 건너뛰도록 설정할 수 있습니다. | Int64 값(단위 없음) |
| copyDuration | 복사 실행의 기간입니다. | Int32 값(초) |
| throughput | `dataRead`를 `copyDuration`으로 나눠서 계산한 데이터 전송 속도입니다. | 부동 소수점 수(KBps) |
| sourcePeakConnections | 원본 데이터 저장소에 설정된 복사 작업을 실행하는 동안의 최대 동시 연결 수입니다. | Int32 값(단위 없음) |
| sinkPeakConnections| 싱크 데이터 저장소에 설정된 복사 작업을 실행하는 동안의 최대 동시 연결 수입니다.| Int32 값(단위 없음) |
| sqlDwPolyBase | Azure Synapse Analytics로 데이터를 복사할 때 PolyBase를 사용할지 여부입니다. | 부울 |
| redshiftUnload | Redshift에서 데이터를 복사할 때 UNLOAD를 사용할지 여부입니다. | 부울 |
| hdfsDistcp | HDFS에서 데이터를 복사할 때 DistCp를 사용할지 여부입니다. | 부울 |
| effectiveIntegrationRuntime | 활동 실행에 사용된 IR(통합 런타임) 또는 런타임이며, `<IR name> (<region if it's Azure IR>)` 형식으로 표시됩니다. | 텍스트(문자열) |
| usedDataIntegrationUnits | 복사 중 효율적인 데이터 통합 단위입니다. | Int32 값 |
| usedParallelCopies | 복사 동안 유효한 parallelCopies입니다. | Int32 값 |
| logPath | Blob 스토리지에서 건너뛴 데이터의 세션 로그의 경로입니다. [내결함성](copy-activity-overview.md#fault-tolerance)을 참조하세요. | 텍스트(문자열) |
| executionDetails | 복사 작업이 거치는 스테이지 및 이에 해당하는 단계, 기간, 구성 등에 대한 세부 정보입니다. 이 섹션은 변경될 수 있으므로 구문 분석하지 않는 것이 좋습니다. 복사 성능을 이해하고 문제를 해결하는 데 도움이 되는 방법을 더 잘 이해하려면 [시각적 모니터링](#monitor-visually) 섹션을 참조하세요. | Array |
| perfRecommendation | 복사 성능 튜닝 팁입니다. 자세한 내용은 [성능 튜닝 팁](copy-activity-performance-troubleshooting.md#performance-tuning-tips)을 참조하세요. | Array |
| billingReference | 지정된 실행에 대한 청구 사용량입니다. [활동 실행 수준에서 사용량 모니터링](plan-manage-costs.md#monitor-consumption-at-activity-run-level)에 대해 자세히 알아보세요. | Object |
| durationInQueue | 복사 작업이 실행되기 전의 대기 시간(초)입니다. | Object |

**예:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "filesSkipped": 0,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "dataConsistencyVerification": 
    { 
        "VerificationResult": "Verified", 
        "InconsistentData": "None" 
    },
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

\- [복사 작업 개요](copy-activity-overview.md)

\- [복사 작업 성능](copy-activity-performance.md)