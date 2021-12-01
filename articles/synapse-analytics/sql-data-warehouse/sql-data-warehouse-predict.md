---
title: PREDICT를 사용하여 기계 학습 모델 채점
description: 전용 SQL 풀에서 T-SQL PREDICT 함수를 사용하여 기계 학습 모델을 채점하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: WilliamDAssafMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 07/21/2020
ms.author: wiassaf
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 3895e1f3131747471d4bda4dea01f050ba55c58d
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133364906"
---
# <a name="score-machine-learning-models-with-predict"></a>PREDICT를 사용하여 기계 학습 모델 채점

전용 SQL 풀은 익숙한 T-SQL 언어를 사용하여 기계 학습 모델을 채점하는 기능을 제공합니다. T-SQL [PREDICT](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest)를 사용하여 기록 데이터로 학습된 기존 기계 학습 모델을 가져와서 데이터 웨어하우스의 보안 경계 내에서 점수를 매길 수 있습니다. PREDICT 함수는 [ONNX(Open Neural Network Exchange)](https://onnx.ai/) 모델 및 데이터를 입력으로 사용합니다. 이 기능은 채점을 위해 데이터 웨어하우스 외부로 중요한 데이터를 이동하는 단계를 제거합니다. 데이터 전문가가 친숙한 T-SQL 인터페이스를 사용하여 기계 학습 모델을 손쉽게 배포하고 해당 작업에 적합한 프레임워크로 작업하는 데이터 과학자와 원활하게 협업할 수 있도록 하는 것이 목표입니다.

> [!NOTE]
> 이 기능은 현재 서버리스 SQL 풀에서 지원되지 않습니다.

이 기능을 사용하려면 모델을 Synapse SQL 외부에서 학습해야 합니다. 모델을 빌드한 후 데이터 웨어하우스에 로드하고 T-SQL Predict 구문을 사용해 점수를 매겨서 데이터에서 인사이트를 얻습니다.

![predictoverview](./media/sql-data-warehouse-predict/datawarehouse-overview.png)

## <a name="training-the-model"></a>모델 학습

전용 SQL 풀에는 미리 학습된 모델이 필요합니다. 전용 SQL 풀에서 예측을 수행하는 데 사용되는 기계 학습 모델을 학습하는 경우 다음 요인을 염두에 두어야 합니다.

- 전용 SQL 풀은 ONNX 형식 모델만 지원합니다. ONNX는 다양한 프레임워크 간에 모델을 교환하여 상호 운용성을 지원하는 오픈 소스 모델 형식입니다. 기본적으로 지원하거나 사용 가능한 변환 패키지가 있는 프레임워크를 사용하여 기존 모델을 ONNX 형식으로 변환할 수 있습니다. 예를 들어, [sklearn-onnx](https://github.com/onnx/sklearn-onnx) 패키지는 scikit-learn 모델을 ONNX로 변환합니다. [ONNX GitHub 리포지토리](https://github.com/onnx/tutorials#converting-to-onnx-format)는 지원되는 프레임워크 및 예제 목록을 제공합니다.

   학습에 [자동화된 ML](../../machine-learning/concept-automated-ml.md)을 사용하는 경우에는 *enable_onnx_compatible_models* 매개 변수를 TRUE로 설정하여 ONNX 형식 모델을 생성해야 합니다. [자동화된 Machine Learning Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)은 자동화된 ML을 사용하여 ONNX 형식의 기계 학습 모델을 만드는 방법의 예제를 보여 줍니다.

- 입력 데이터에 대해 지원되는 데이터 형식은 다음과 같습니다.
    - int, bigint, real, float
    - char, varchar, nvarchar

- 채점 데이터는 학습 데이터와 동일한 형식이어야 합니다. 다차원 배열과 같은 복합 데이터 형식은 PREDICT에서 지원되지 않습니다. 따라서 학습을 위해 모든 입력을 포함하는 단일 배열을 전달하는 대신 모델의 각 입력이 채점 테이블의 단일 열에 해당하는지 확인합니다.

- 모델 입력의 이름 및 데이터 형식이 새 예측 데이터의 열 이름 및 데이터 형식과 일치해야 합니다. 온라인으로 제공되는 다양한 오픈 소스 도구를 사용하여 ONNX 모델을 시각화하면 디버깅하는 데 도움이 될 수 있습니다.

## <a name="loading-the-model"></a>모델 로드

모델은 전용 SQL 풀 사용자 테이블에 16진수 문자열로 저장됩니다. ID 및 설명과 같은 추가 열을 모델 테이블에 추가하여 모델을 식별할 수 있습니다. 모델 열의 데이터 형식으로 varbinary(max)를 사용합니다. 모델을 저장하는 데 사용할 수 있는 테이블에 대한 코드 예제는 다음과 같습니다.

```sql
-- Sample table schema for storing a model and related data
CREATE TABLE [dbo].[Models]
(
    [Id] [int] IDENTITY(1,1) NOT NULL,
    [Model] [varbinary](max) NULL,
    [Description] [varchar](200) NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    HEAP
)
GO

```

모델이 16진수 문자열로 변환되고 테이블 정의가 지정되면 [COPY 명령](/sql/t-sql/statements/copy-into-transact-sql?preserve-view=true&view=azure-sqldw-latest) 또는 PolyBase를 사용하여 전용 SQL 풀 테이블에 모델을 로드합니다. 다음 코드 샘플에서는 복사 명령을 사용하여 모델을 로드합니다.

```sql
-- Copy command to load hexadecimal string of the model from Azure Data Lake storage location
COPY INTO [Models] (Model)
FROM '<enter your storage location>'
WITH (
    FILE_TYPE = 'CSV',
    CREDENTIAL=(IDENTITY= 'Shared Access Signature', SECRET='<enter your storage key here>')
)
```

## <a name="scoring-the-model"></a>모델 채점

데이터 웨어하우스에 모델 및 데이터가 로드되면 **T-SQL PREDICT** 함수를 사용하여 모델의 점수를 매깁니다. 새 입력 데이터가 모델을 빌드하는 데 사용되는 학습 데이터와 동일한 형식이어야 합니다. T-SQL PREDICT는 모델 및 새 채점 입력 데이터라는 두 가지 입력을 사용하고 출력에 대한 새 열을 생성합니다. 모델은 변수, 리터럴 또는 스칼라 sub_query로 지정할 수 있습니다. [WITH common_table_expression](/sql/t-sql/queries/with-common-table-expression-transact-sql?preserve-view=true&view=azure-sqldw-latest)을 사용하여 데이터 매개 변수에 대해 명명된 결과 집합을 지정합니다.

아래 예제에서는 예측 함수를 사용하는 샘플 쿼리를 보여 줍니다. 이름이 *Score* 이고 데이터 형식이 *float* 인 추가 열이 생성되고, 여기에 예측 결과가 포함됩니다. 모든 입력 데이터 열과 출력 예측 열은 select 문을 사용하여 표시할 수 있습니다. 자세한 내용은 [PREDICT(Transact-SQL)](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest)을 참조하세요.

```sql
-- Query for ML predictions
SELECT d.*, p.Score
FROM PREDICT(MODEL = (SELECT Model FROM Models WHERE Id = 1),
DATA = dbo.mytable AS d, RUNTIME = ONNX) WITH (Score float) AS p;
```

## <a name="next-steps"></a>다음 단계

PREDICT 함수에 대해 자세히 알아보려면 [PREDICT(Transact-SQL)](/sql/t-sql/queries/predict-transact-sql?preserve-view=true&view=azure-sqldw-latest)을 참조하세요.