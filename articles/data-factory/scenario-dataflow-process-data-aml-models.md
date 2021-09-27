---
title: 데이터 흐름을 사용하여 AutoML(자동화된 Machine Learning) 모델에서 데이터 처리
description: Azure Data Factory 데이터 흐름을 사용하여 AutoML(자동화된 Machine Learning) 모델에서 데이터를 처리하는 방법을 알아봅니다.
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.subservice: tutorials
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: 084e4c0fa3ecf94685e1789273764c548c07147a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730777"
---
# <a name="process-data-from-automated-machine-learning-models-by-using-data-flows"></a>데이터 흐름을 사용하여 자동화된 Machine Learning 모델에서 데이터 처리

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

AutoML(자동화된 Machine Learning)은 분류, 회귀, 시계열 예측에 내가 지정하는 대상 메트릭을 사용함으로써 최상의 모델을 자동으로 학습시키고 조정 및 획득할 목적으로 기계 학습 프로젝트에서 채택되고 있습니다.

AutoML의 한 가지 문제는 데이터 웨어하우스의 원시 데이터 또는 트랜잭션 데이터가 10GB에 육박하는 거대한 데이터 세트일 수 있다는 것입니다. 데이터 세트가 크면 모델을 학습시키는 데 더 많은 시간이 필요하므로 Azure Machine Learning 모델을 학습시키기 전에 데이터 처리를 최적화하는 것이 좋습니다. 이 자습서에서는 Azure Data Factory를 사용하여 데이터 세트를 기계 학습 데이터 세트를 위한 AutoML 파일로 분할하는 방법을 설명합니다.

AutoML 프로젝트에는 다음과 같은 세 가지 데이터 처리 시나리오가 있습니다.

* 모델을 학습시키기 전에 대량의 데이터를 AutoML 파일로 분할합니다.

     모델을 학습시키기 전에 데이터를 처리하는 데에는 [Pandas 데이터 프레임](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html)이 일반적으로 사용됩니다. Pandas 데이터 프레임은 크기가 1GB 미만인 데이터에 대해서는 잘 작동하지만 1GB를 초과하는 경우에는 데이터 처리 속도가 느려집니다. 경우에 따라 메모리 부족 오류 메시지가 나타날 수도 있습니다. [Parquet 파일](https://parquet.apache.org/) 형식은 이진 칼럼 형식이므로 기계 학습에는 이 형식을 사용하는 것이 좋습니다.
    
     Data Factory 매핑 데이터 흐름은 데이터 변환을 시각적으로 디자인한 것으로서 데이터 엔지니어가 코드를 작성하는 부담에서 벗어나게 해 줍니다. 매핑 데이터 흐름은 파이프라인에서 확장된 Spark 클러스터를 사용하기 때문에 대량의 데이터를 처리하는 강력한 방법이 됩니다.

* 학습 데이터 세트 및 테스트 데이터 세트를 분할합니다.
    
    학습 데이터 세트는 학습 모델에 사용됩니다. 테스트 데이터 세트는 기계 학습 프로젝트에서 모델을 평가하는 데 사용됩니다. 매핑 데이터 흐름에 대한 조건부 분할 작업은 학습 데이터 및 테스트 데이터를 분할합니다.

* 정규화되지 않은 데이터를 제거합니다.

    행이 없는 Parquet 파일처럼 정규화되지 않은 데이터를 제거하는 것이 좋습니다. 이 자습서에서는 집계 작업을 사용하여 행 개수를 가져옵니다. 행 개수는 정규화되지 않은 데이터를 제거하는 조건이 됩니다.

## <a name="preparation"></a>준비

다음 Azure SQL Database 테이블을 사용합니다.

```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>Parquet로 데이터 형식 변환

다음 데이터 흐름은 SQL Database 테이블을 Parquet 파일 형식으로 변환합니다.

- **원본 데이터 세트**: SQL Database의 트랜잭션 테이블입니다.
- **싱크 데이터 세트**: Parquet 형식의 Blob Storage입니다.

## <a name="remove-unqualified-data-based-on-row-count"></a>행 개수를 기준으로 정규화되지 않은 데이터 제거

2 미만의 행 개수를 제거해야 한다고 가정해 보겠습니다.

1. 집계 작업을 사용하여 행 개수를 가져옵니다. **그룹화 기준** 은 Col2를 기준으로, **집계** 의 행 개수를 `count(1)`로 하여 사용합니다.

    :::image type="content" source="./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png" alt-text="행 개수를 가져오는 집계 작업 구성을 보여 주는 스크린샷.":::

1. 싱크 작업을 사용하여 **싱크** 탭에서 **싱크 형식** 을 **캐시** 로 선택합니다. 그런 다음 **설정** 탭의 **키 열** 드롭다운 목록에서 원하는 열을 선택합니다.

    :::image type="content" source="./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png" alt-text="캐시된 싱크에 있는 행 개수를 가져오는 CacheSink 작업 구성을 보여 주는 스크린샷.":::

1. 파생 열 작업을 사용하여 원본 스트림에 행 개수 열을 추가합니다. **파생 열 설정** 탭에서 `CacheSink#lookup` 식을 사용하여 CacheSink에서 행 개수를 가져옵니다.

    :::image type="content" source="./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png" alt-text="행 개수를 Source1에 추가하는 파생 열 작업 구성을 보여 주는 스크린샷.":::

1. 조건부 분할 작업을 사용하여 정규화되지 않은 데이터를 제거합니다. 이 예제에서 행 개수는 Col2 열을 기반으로 합니다. 2 미만의 행 개수를 제거하는 것이 조건이므로 행 2개(ID=2 및 ID=7)가 제거됩니다. 데이터 관리를 위해 정규화되지 않은 데이터를 Blob Storage에 저장합니다.

    :::image type="content" source="./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png" alt-text="2보다 크거나 같은 데이터를 가져오는 조건부 분할 작업 구성을 보여 주는 스크린샷.":::

> [!NOTE]
>    * 이후 단계에서 원래 원본에서 사용될 행 개수를 가져오기 위한 새 원본을 만듭니다.
>    * 성능 관점에서 CacheSink를 사용합니다.

## <a name="split-training-data-and-test-data"></a>학습 데이터 및 테스트 데이터 분할

파티션별로 학습 데이터와 테스트 데이터를 분할하려고 합니다. 이 예제에서는 Col2의 동일한 값에 대해 상위 두 행을 테스트 데이터로 가져오고 나머지 행을 학습 데이터로 가져옵니다.

1. 창 작업을 사용하여 파티션별로 하나의 열 행 수를 추가합니다. **대상** 탭에서 파티션에 대한 열을 선택합니다. 이 자습서에서는 Col2에 대해 분할을 수행합니다. **정렬** 탭에서 순서를 지정합니다. 이 자습서에서는 ID를 기준으로 합니다. **창 열** 탭에서 순서를 지정하여 행 수로 열 1개를 추가합니다.

    :::image type="content" source="./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png" alt-text="행 수로 새 열 1개를 추가하는 창 작업 구성을 보여 주는 스크린샷.":::

1. 조건부 분할 작업을 사용하여 각 파티션의 상위 두 행을 테스트 데이터 세트로 분할하고 나머지 행을 학습 데이터 세트로 분할합니다. **조건부 분할 설정** 탭에서 `lesserOrEqual(RowNum,2)` 식을 조건으로 사용합니다.

    :::image type="content" source="./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png" alt-text="현재 데이터 세트를 학습 데이터 세트와 테스트 데이터 세트로 분할하는 조건부 분할 작업 구성을 보여 주는 스크린샷.":::

## <a name="partition-the-training-and-test-datasets-with-parquet-format"></a>Parquet 형식을 사용하여 학습 및 테스트 데이터 세트 분할

**최적화** 탭에서 싱크 작업을 사용하여 **파티션당 고유 값** 을 사용해 열을 파티션의 열 키로 설정합니다.

:::image type="content" source="./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png" alt-text="학습 데이터 세트의 파티션을 설정하는 싱크 작업 구성을 보여 주는 스크린샷.":::

전체 파이프라인 논리를 다시 살펴보겠습니다.

:::image type="content" source="./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png" alt-text="전체 파이프라인의 논리를 보여 주는 스크린샷.":::

## <a name="next-steps"></a>다음 단계

매핑 데이터 흐름 [변환](concepts-data-flow-overview.md)을 사용하여 나머지 데이터 흐름 논리를 빌드합니다.
