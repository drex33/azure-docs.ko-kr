---
title: 데이터 흐름 작업
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Azure Synapse Analytics 파이프라인 내에서 데이터 흐름을 실행하는 방법입니다.
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.author: makromer
ms.date: 09/09/2021
ms.openlocfilehash: cfb385432b149fae0720f657228710c324d6569b
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133568536"
---
# <a name="data-flow-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure Data Factory 및 Azure Synapse Analytics의 데이터 흐름 작업

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

데이터 흐름 작업을 사용하여 매핑 데이터 흐름을 통해 데이터를 변환하고 이동합니다. 데이터 흐름을 처음 접하는 경우 [매핑 데이터 흐름 개요](concepts-data-flow-overview.md)를 참조하세요.

## <a name="create-a-data-flow-activity-with-ui"></a>UI를 사용 하 여 데이터 Flow 작업 만들기

파이프라인에서 데이터 Flow 작업을 사용 하려면 다음 단계를 완료 합니다.

1. 파이프라인 활동 창에서 _데이터 Flow_ 을 검색 하 고 파이프라인 캔버스로 데이터 Flow 활동을 끌어 놓습니다.
1. 캔버스에서 새 데이터 Flow 작업을 선택 합니다 (아직 선택 하지 않은 경우), 해당 **설정** 탭에서 세부 정보를 편집 합니다.

   :::image type="content" source="media/control-flow-execute-data-flow-activity/data-flow-activity.png" alt-text="데이터 Flow 작업에 대 한 UI를 표시 합니다.":::

1. 기존 데이터 흐름을 선택 하거나 새로 만들기 단추를 사용 하 여 새 흐름을 만듭니다.  구성을 완료 하는 데 필요한 다른 옵션을 선택 합니다.

## <a name="syntax"></a>구문

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "traceLevel": "Fine",
      "runConcurrently": true,
      "continueOnError": true,      
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>형식 속성

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
dataflow | 실행되는 데이터 흐름에 대한 참조입니다. | DataFlowReference | 예
integrationRuntime | 데이터 흐름이 실행되는 컴퓨팅 환경입니다. 지정하지 않으면 자동 확인 Azure 통합 런타임이 사용됩니다. | IntegrationRuntimeReference | 예
compute.coreCount | Spark 클러스터에서 사용되는 코어 수입니다. 자동 확인 Azure 통합 런타임이 사용되는 경우에만 지정할 수 있습니다. | 8, 16, 32, 48, 80, 144, 272 | 예
compute.computeType | Spark 클러스터에서 사용되는 컴퓨팅 유형입니다. 자동 확인 Azure 통합 런타임이 사용되는 경우에만 지정할 수 있습니다. | "General", "ComputeOptimized", "MemoryOptimized" | 예
staging.linkedService | Azure Synapse Analytics 원본 또는 싱크를 사용하는 경우 PolyBase 스테이징에 사용되는 스토리지 계정을 지정합니다.<br/><br/>Azure Storage가 VNet 서비스 엔드포인트로 구성된 경우 스토리지 계정에 '신뢰할 수 있는 Microsoft 서비스 허용'이 설정된 관리 ID 인증을 사용해야 합니다. [Azure Storage에서 VNet 서비스 엔드포인트 사용의 영향](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)을 참조하세요. 또한 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 및 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity)에 대해 각각 필요한 구성을 알아봅니다.<br/> | LinkedServiceReference | 데이터 흐름이 Azure Synapse Analytics에서 읽거나 쓰는 경우에만
staging.folderPath | Azure Synapse Analytics 원본 또는 싱크를 사용하는 경우 PolyBase 스테이징에 사용되는 Blob Storage 계정의 폴더 경로입니다. | String | 데이터 흐름이 Azure Synapse Analytics에서 읽거나 쓰는 경우에만
traceLevel | 데이터 흐름 작업 실행의 로깅 수준을 설정합니다. | Fine, Coarse, None | 예

:::image type="content" source="media/data-flow/activity-data-flow.png" alt-text="데이터 흐름 실행":::

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>런타임에 동적으로 데이터 흐름 컴퓨팅 크기 설정

코어 수 및 컴퓨팅 유형 속성을 런타임에 들어오는 원본 데이터의 크기에 맞춰 동적으로 설정할 수 있습니다. 조회 또는 메타데이터 가져오기와 같은 파이프라인 작업을 사용하여 원본 데이터 세트 데이터의 크기를 찾습니다. 그런 다음 데이터 흐름 작업 속성에서 동적 콘텐츠 추가를 사용합니다.

> [!NOTE]
> Azure Synapse 데이터 흐름에서 드라이버 및 작업자 노드 코어를 선택할 때, 최소 3 개의 노드가 항상 사용됩니다.

:::image type="content" source="media/data-flow/dyna1.png" alt-text="동적 데이터 흐름":::

[여기에서 이 기술에 대해 설명하는 간단한 동영상 자습서를 확인하세요.](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>데이터 흐름 통합 런타임

데이터 흐름 작업 실행에 사용할 통합 런타임을 선택합니다. 기본값으로 이 서비스는 4개의 작업자 코어와 함께 Azure Integration Runtime 자동 해결을 사용합니다. 이 IR은 범용 컴퓨팅 유형을 사용하며 서비스 인스턴스와 동일한 지역에서 실행됩니다. 작업 가능 파이프라인의 경우, 데이터 흐름 활동 실행을 위한 특정 지역, 계산 유형, 코어 수, TTL을 정의하는 고유한 Azure Integration Runtime을 만드는 것이 좋습니다.

8+8(총 16개 v-코어) 구성의 범용(컴퓨팅 최적화는 큰 규모의 워크로드에는 권장되지 않음) 최소 컴퓨팅 형식 및 10분이 대부분의 프로덕션 워크로드에 대한 최소 권장 사항입니다. Azure IR는 소형 TTL을 설정하여 콜드 클러스터에 대해 몇 분의 시작 시간을 발생시키지 않는 웜 클러스터를 유지할 수 있습니다. Azure IR 데이터 흐름 구성에서 “빠른 다시 사용”을 선택하여 데이터 흐름의 실행 속도를 높일 수 있습니다. 자세한 내용은 [Azure 통합 런타임](concepts-integration-runtime.md)을 참조하세요.

:::image type="content" source="media/data-flow/ir-new.png" alt-text="Azure Integration Runtime":::

> [!IMPORTANT]
> 데이터 흐름 작업의 통합 런타임 선택은 파이프라인의 '트리거된 실행'에만 적용됩니다. 데이터 흐름을 사용하는 파이프라인 디버깅은 디버그 세션에 지정된 클러스터에서 실행됩니다.

### <a name="polybase"></a>PolyBase

Azure Synapse Analytics를 싱크 또는 원본으로 사용하는 경우 PolyBase 일괄 처리 로드를 위한 스테이징 위치를 선택해야 합니다. PolyBase에서는 데이터를 행 단위로 로드하는 대신 일괄 처리로 대량 로드할 수 있습니다. PolyBase는 Azure Synapse Analytics로의 로드 시간을 대폭 단축합니다.

## <a name="logging-level"></a>로깅 수준

데이터 흐름 활동의 모든 파이프라인 실행에서 모든 자세한 원격 분석 로그를 완전히 기록할 필요가 없는 경우 필요에 따라 로깅 수준을 ‘기본’ 또는 ‘없음’으로 설정할 수 있습니다. '자세한 정보' 모드(기본값)에서 데이터 흐름을 실행하는 경우 서비스에 데이터 변환 중 각 개별 파티션 수준에서 작업을 완전히 기록하도록 요청하는 것입니다. 이 작업은 비용이 많이 들 수 있으므로 문제를 해결하는 경우에만 자세한 정보를 사용하면 전체 데이터 흐름 및 파이프라인 성능을 향상시킬 수 있습니다. ‘기본’ 모드에서는 변환 기간만 기록하고, ‘없음’의 경우 기간 요약만 제공합니다.

:::image type="content" source="media/data-flow/logging.png" alt-text="로깅 수준":::

## <a name="sink-properties"></a>싱크 속성

데이터 흐름의 그룹화 기능을 사용하면 싱크의 실행 순서를 설정할 수 있고 동일한 그룹 번호를 사용하여 싱크를 그룹화할 수 있습니다. 그룹을 쉽게 관리할 수 있도록 ADF에게 동일한 그룹의 싱크를 동시에 실행하도록 요청할 수 있습니다. 또한 싱크 중 하나에 오류가 발생한 후에도 계속하도록 싱크 그룹을 설정할 수도 있습니다.

데이터 흐름 싱크의 기본 동작은 각 싱크를 순차적으로 실행하고 싱크에서 오류가 발생할 경우 데이터 흐름이 실패하도록 하는 것입니다. 또한 데이터 흐름 속성으로 이동하여 싱크에 대해 다른 우선 순위를 설정하지 않는 한 모든 싱크는 기본적으로 동일한 그룹으로 지정됩니다.

:::image type="content" source="media/data-flow/sink-properties.png" alt-text="싱크 속성":::

### <a name="first-row-only"></a>첫 행 전용

이 옵션은 “작업에 출력”에 대해 캐시 싱크를 사용하는 것으로 설정된 데이터 흐름에만 사용할 수 있습니다. 파이프라인에 직접 삽입되는 데이터 흐름의 출력은 2MB로 제한됩니다. “첫 행 전용”을 설정하면 데이터 흐름 활동 출력을 파이프라인에 직접 삽입하는 경우 데이터 흐름에서 데이터 출력을 제한할 수 있습니다.

## <a name="parameterizing-data-flows"></a>데이터 흐름 매개 변수화

### <a name="parameterized-datasets"></a>매개 변수가 있는 데이터 세트

데이터 흐름에서 매개 변수가 있는 데이터 세트를 사용하는 경우 **설정** 탭에서 매개 변수 값을 설정합니다.

:::image type="content" source="media/data-flow/params.png" alt-text="데이터 흐름 실행 매개 변수":::

### <a name="parameterized-data-flows"></a>매개 변수가 있는 데이터 흐름

데이터 흐름에 매개 변수가 있는 경우 **매개 변수** 탭에서 데이터 흐름 매개 변수의 동적 값을 설정합니다. 파이프라인 식 언어 또는 데이터 흐름 식 언어를 사용하여 동적 또는 리터럴 매개 변수 값을 할당할 수 있습니다. 자세한 내용은 [데이터 흐름 매개 변수](parameters-data-flow.md)를 참조하세요.

### <a name="parameterized-compute-properties"></a>매개 변수가 있는 컴퓨팅 속성

자동 확인 Azure 통합 런타임을 사용하고 compute.coreCount 및 compute.computeType의 값을 지정하는 경우 코어 수 또는 컴퓨팅 유형을 매개 변수화할 수 있습니다.

:::image type="content" source="media/data-flow/parameterize-compute.png" alt-text="데이터 흐름 실행 매개 변수 예제":::

## <a name="pipeline-debug-of-data-flow-activity"></a>데이터 흐름 작업의 파이프라인 디버그

데이터 흐름 작업을 사용하여 파이프라인 디버그를 실행하려면 위쪽 표시줄의 **데이터 흐름 디버그** 슬라이더를 통해 데이터 흐름 디버그 모드로 전환해야 합니다. 디버그 모드를 사용하면 활성 Spark 클러스터에 대해 데이터 흐름을 실행할 수 있습니다. 자세한 내용은 [디버그 모드](concepts-data-flow-debug-mode.md)를 참조하세요.

:::image type="content" source="media/data-flow/debug-button-3.png" alt-text="디버그 단추를 표시하는 스크린샷":::

디버그 파이프라인은 데이터 흐름 작업 설정에 지정된 통합 런타임 환경이 아닌 활성 디버그 클러스터에 대해 실행됩니다. 디버그 모드를 시작할 때 디버그 컴퓨팅 환경을 선택할 수 있습니다.

## <a name="monitoring-the-data-flow-activity"></a>데이터 흐름 작업 모니터링

데이터 흐름 작업은 분할, 단계 시간 및 데이터 계보 정보를 볼 수 있는 특별한 모니터링 환경을 포함합니다. **작업** 아래의 안경 아이콘을 통해 모니터링 창을 엽니다. 자세한 내용은 [데이터 흐름 모니터링](concepts-data-flow-monitoring.md)을 참조하세요.

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>데이터 흐름 작업 결과를 후속 작업에 사용

데이터 흐름 작업은 각 싱크에 쓰여진 행 수와 각 원본에서 읽은 행 수에 대한 메트릭을 출력합니다. 이러한 결과는 활동 실행 결과의 `output` 섹션에 반환됩니다. 반환되는 메트릭은 아래 JSON 형식으로 되어 있습니다.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

예를 들어 이름이 'dataflowActivity'인 작업에서 'sink1'이라는 싱크에 기록된 행 수를 가져오려면 `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`을 사용합니다.

해당 싱크에 사용된 'source1'이라는 원본에서 읽은 행 수를 가져오려면 `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`를 사용합니다.

> [!NOTE]
> 싱크에 기록된 행이 없으면 해당 싱크가 메트릭에 표시되지 않습니다. `contains` 함수를 사용하여 존재 여부를 확인할 수 있습니다. 예를 들어 `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')`은 sink1에 기록된 행이 있는지 여부를 확인합니다.

## <a name="next-steps"></a>다음 단계

지원되는 다음 제어 흐름 작업을 참조하세요. 

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 작업 실행](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
