---
title: Azure Synapse Analytics의 Machine Learning
description: Azure Synapse Analytics의 기계 학습 기능 개요
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye
ms.date: 10/01/2021
author: nelgson
ms.author: negust
ms.openlocfilehash: e092cf94e5126b5bfa7bd42cc2f362f6d7a9da96
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402099"
---
# <a name="machine-learning-capabilities-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 Machine Learning 기능

Azure Synapse Analytics는 다양한 기계 학습 기능을 제공합니다. 이 문서에서는 Azure Synapse의 컨텍스트에서 Machine Learning을 적용하는 방법에 대한 개요를 제공합니다.

이 개요에서는 데이터 과학 프로세스 관점에서 기계 학습과 Synapse의 다양한 기능에 대해 설명합니다.

일반적인 데이터 과학 프로세스의 모습에 대해 잘 알고 있을 수 있습니다. 대부분의 기계 학습 프로젝트에서 수행하는 잘 알려진 프로세스입니다.

높은 수준의 프로세스에는 다음 단계가 포함됩니다.
* 비즈니스 해석(이 아티클에서는 설명하지 않음)
* 데이터 취득 및 이해
* 모델링
* 모델 배포 및 점수 매기기

이 문서에서는 데이터 과학 프로세스 관점에서 다양한 분석 엔진의 Azure Synapse 기계 학습 기능을 설명합니다. 데이터 과학 프로세스의 각 단계에 대해 도움이 되는 Azure Synapse 기능이 요약되어 있습니다.

## <a name="data-acquisition-and-understanding"></a>데이터 취득 및 이해

대부분의 기계 학습 프로젝트에는 잘 설정된 단계가 포함되며, 이러한 단계 중 하나는 데이터에 액세스하고 해당 데이터를 이해하는 것입니다.

### <a name="data-source-and-pipelines"></a>데이터 원본 및 파이프라인

Azure Synapse의 고유하게 통합된 부분인 [Azure Data Factory](../../data-factory/introduction.md) 덕분에 강력한 도구 세트를 데이터 수집 및 데이터 오케스트레이션 파이프라인에 사용할 수 있습니다. 이를 통해 데이터 파이프라인을 손쉽게 빌드하여 기계 학습에 사용할 수 있는 형식으로 데이터에 액세스하여 변환할 수 있습니다. Synapse의 [데이터 파이프라인에 대해 자세히 알아보세요](../../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json). 

### <a name="data-preparation-and-explorationvisualization"></a>데이터 준비 및 탐색/시각화

기계 학습 프로세스의 중요한 부분은 탐색 및 시각화를 통해 데이터를 이해하는 것입니다.

데이터가 저장되는 위치에 따라 Synapse는 분석 및 기계 학습을 위해 탐색하고 준비하는 다양한 도구 세트를 제공합니다. 데이터 탐색을 시작하는 가장 빠른 방법 중 하나는 데이터 레이크의 데이터에 직접 Apache Spark 또는 서버리스 SQL 풀을 사용하는 것입니다.

* [Apache Spark for Azure Synapse](../spark/apache-spark-overview.md)는 대규모로 데이터를 변환, 준비 및 탐색하는 기능을 제공합니다. 이러한 Spark 풀은 데이터를 대규모로 처리하기 위해 PySpark/Python, Scala 및 .NET 등의 도구를 제공합니다. 강력한 시각화 라이브러리를 사용하면 데이터를 더 잘 이해할 수 있도록 데이터 탐색 환경을 향상시킬 수 있습니다. [Spark를 사용하여 Synapse에서 데이터를 탐색하고 시각화하는 방법에 대해 자세히 알아보세요](../get-started-analyze-spark.md).

* [서버리스 SQL 풀](../sql/on-demand-workspace-overview.md)은 데이터 레이크에서 직접 TSQL을 사용하여 데이터를 탐색하는 방법을 제공합니다. 서버리스 SQL 풀은 Synapse Studio에서 몇 가지 기본 제공 시각화도 제공합니다. [서버리스 SQL 풀을 사용하여 데이터를 탐색하는 방법에 대해 자세히 알아보세요](../get-started-analyze-sql-on-demand.md).

## <a name="modeling"></a>모델링

Azure Synapse에서 기계 학습 모델 학습은 PySpark/Python, Scala 또는 .NET과 같은 도구를 사용하여 Apache Spark 풀에서 수행할 수 있습니다.

### <a name="train-models-on-spark-pools-with-mllib"></a>MLlib를 사용하여 Spark 풀에서 모델 학습

기계 학습 모델은 다양한 알고리즘 및 라이브러리를 활용하여 학습될 수 있습니다. [Spark MLlib](http://spark.apache.org/docs/latest/ml-guide.html)는 대부분의 고전적인 기계 학습 문제를 해결하는 데 도움이 될 수 있는 확장 가능한 기계 학습 알고리즘을 제공합니다. Synapse에서 MLlib를 사용하여 모델을 학습하는 방법에 대한 자습서는 [Apache Spark MLlib를 사용한 기계 학습 앱 및 Azure Synapse Analytics 빌드](../spark/apache-spark-machine-learning-mllib-notebook.md)를 참조하세요.

MLlib 외에도 [Scikit Learn](https://scikit-learn.org/stable/)과 같이 인기 있는 라이브러리를 사용하여 모델을 개발할 수도 있습니다. Synapse Spark 풀에 라이브러리를 설치하는 방법에 대한 자세한 내용은 [Azure Synapse Analytics에서 Apache Spark를 위한 라이브러리 관리](../spark/apache-spark-azure-portal-add-libraries.md)를 참조하세요.

### <a name="train-models-with-azure-machine-learning-automated-ml"></a>Azure Machine Learning 자동화된 ML을 사용하여 모델 학습

기계 학습에 대해 사전 지식이 크게 필요하지 않은 기계 학습 모델을 학습하는 또 다른 방법은 자동화된 ML을 사용하는 것입니다. [자동화된 ML](../../machine-learning/concept-automated-ml.md)은 기계 학습 모델 세트를 자동으로 학습하고 사용자가 특정 메트릭에 따라 최상의 모델을 선택할 수 있게 해 주는 기능입니다. Azure Synapse Notebooks의 Azure Machine Learning과 원활한 통합 덕분에 사용자는 통과 Azure Active Directory 인증으로 Synapse에서 자동화된 ML을 손쉽게 활용할 수 있습니다.  즉, Azure Machine Learning 작업 영역을 지정할 필요가 없으며 자격 증명을 입력하지 않아도 됩니다. [자동화된 Machine Learning으로 Python에서 모델 학습](../spark/apache-spark-azure-machine-learning-tutorial.md)은 Synapse Spark 풀에서 Azure Machine Learning 자동화된 ML을 사용하여 모델을 학습하는 방법을 설명하는 자습서입니다.

## <a name="model-deployment-and-scoring"></a>모델 배포 및 점수 매기기

Azure Synapse에서 또는 Azure Synapse 외부에서 학습된 모델은 일괄 처리 점수 매기기에 손쉽게 사용할 수 있습니다. 현재 Synapse에는 일괄 처리 점수 매기기를 실행할 수 있는 두 가지 방법이 있습니다.

* Synapse SQL 풀에서 [TSQL PREDICT 함수](../sql-data-warehouse/sql-data-warehouse-predict.md)를 사용하여 데이터가 있는 위치에서 예측을 올바르게 실행할 수 있습니다. 이 강력하고 확장 가능한 기능을 통해 데이터 웨어하우스에서 데이터를 이동하지 않고도 데이터를 보강할 수 있습니다. 새롭게 [안내된 기반 기계 학습 모델 환경이 Synapse Studio에](./tutorial-sql-pool-model-scoring-wizard.md) 도입되었습니다. 여기서는 Synapse SQL 풀의 Azure Machine Learning 모델 레지스트리에서 ONNX 모델을 배포하여 예측을 사용한 일괄 처리 점수 매기기를 수행할 수 있습니다.

* Azure Synapse에서 기계 학습 모델에 대해 일괄 처리 점수 매기기를 수행하는 또 다른 옵션은 Azure Synapse의 Apache Spark 풀을 활용하는 것입니다. 모델 학습에 사용되는 라이브러리에 따라 코드 환경을 사용하여 일괄 처리 점수 매기기를 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Synapse Analytics 시작](../get-started.md)
* [작업 영역 만들기](../get-started-create-workspace.md)
* [빠른 시작: Synapse에서 새로운 Azure Machine Learning 연결된 서비스 만들기](quickstart-integrate-azure-machine-learning.md)
* [자습서: 기계 학습 모델 점수 매기기 마법사 - 전용 SQL 풀](tutorial-sql-pool-model-scoring-wizard.md)