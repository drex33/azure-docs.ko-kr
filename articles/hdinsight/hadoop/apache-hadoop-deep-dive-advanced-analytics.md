---
title: 딥 다이브 분석 - 고급 분석 - Azure HDInsight
description: 고급 분석이 알고리즘을 사용하여 Azure HDInsight에서 빅 데이터를 처리하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: c0475ee0a97e3d9e3dd376d84028cedca3cfa70b
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112676422"
---
# <a name="deep-dive---advanced-analytics"></a>딥 다이브 분석 - 고급 분석

## <a name="what-is-advanced-analytics-for-hdinsight"></a>HDInsight 고급 분석이란?

HDInsight는 대량의 구조화된 데이터, 구조화되지 않은 데이터 및 빠르게 이동하는 데이터로부터 가치 있는 통찰력을 얻을 수 있는 기능을 제공합니다. 고급 분석은 확장성 있는 아키텍처, 통계 및 기계 학습 모델 및 지능형 대시보드를 사용하여 의미 있는 통찰력을 제공합니다. 기계 학습 또는 *예측 분석* 은 데이터의 관계를 식별하고 학습하여 예측을 수행하고 의사 결정을 안내하는 알고리즘을 사용합니다.

## <a name="advanced-analytics-process"></a>고급 분석 프로세스

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png" alt-text="고급 분석 프로세스 흐름" border="false":::

비즈니스 문제를 확인하고 데이터 수집 및 처리를 시작한 후에는 예측하려는 질문을 나타내는 모델을 만들어야 합니다. 모델은 하나 이상의 기계 학습 알고리즘을 사용하여 비즈니스 요구에 가장 적합한 유형의 예측을 만듭니다.  대부분의 데이터는 모델을 학습하는 데 사용해야 하고, 나머지는 모델을 테스트하거나 평가하는 데 사용해야 합니다.

모델을 생성, 로드, 테스트 및 평가한 후 다음 단계에서는 모델을 배포하여 질문에 대한 답변을 제공하기 시작합니다. 마지막 단계는 모델의 성능을 모니터링하고 필요에 따라 튜닝하는 것입니다.

## <a name="common-types-of-algorithms"></a>일반적인 알고리즘 형식

고급 분석 솔루션은 일단의 기계 학습 알고리즘을 제공합니다. 다음은 알고리즘 범주 및 관련된 일반적인 비즈니스 사용 사례를 요약한 것입니다.

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png" alt-text="Machine Learning 범주 요약" border="false":::

가장 적합한 알고리즘을 선택하는 것과 함께 학습용 데이터를 제공해야 하는지 여부를 고려해야 합니다. 기계 학습 알고리즘은 다음과 같이 분류됩니다.

* 감독됨 - 결과를 제공하기 전에 레이블이 지정된 일단의 데이터에 대해 알고리즘을 학습해야 합니다.
* 반감독됨 - 학습 초기 단계에서는 사용할 수 없었던 강사의 대화형 쿼리를 통해 추가 대상에 의한 알고리즘 확장이 가능합니다.
* 자율 - 알고리즘에 학습 데이터가 필요하지 않습니다.
* 보충 - 알고리즘에서 소프트웨어 에이전트를 사용하여 특정 컨텍스트 내에서 이상적인 동작을 결정합니다(종종 로봇 공학에서 사용됨)

| 알고리즘 범주| Windows Server Update Services와 함께 | 학습 유형 | 알고리즘 |
| --- | --- | --- | -- |
| 분류 | 사람 또는 사물을 그룹으로 분류합니다. | 감독됨 | 의사 결정 트리, 로지스틱 회귀, 신경망 |
| Clustering | 일단의 예제를 동일 그룹으로 분할합니다. | 자율 | K-평균 클러스터링 |
| 패턴 검색 | 데이터에서 빈번한 연결을 확인합니다. | 자율 | 연결 규칙 |
| 회귀 | 숫자 결과를 예측합니다. | 감독됨 | 선형 회귀, 신경망 |
| 강화 | 최적의 로봇 동작을 결정합니다. | 강화 | 몬테카를로 시뮬레이션, 딥 마인드 |

## <a name="machine-learning-on-hdinsight"></a>HDInsight의 기계 학습

HDInsight에는 고급 분석 워크플로에 대한 몇 가지 기계 학습 옵션이 있습니다.

* Machine Learning 및 Apache Spark
* Azure Machine Learning 및 Apache Hive
* Apache Spark 및 딥 러닝

### <a name="machine-learning-and-apache-spark"></a>Machine Learning 및 Apache Spark

[HDInsight Spark](../spark/apache-spark-overview.md)는 Azure에서 호스팅되는 [Apache Spark](https://spark.apache.org/) 제품으로, 메모리 내 처리를 사용하여 빅 데이터 분석을 향상시키는 통합형 오픈 소스 병렬 데이터 처리 프레임워크입니다. 속도, 간편한 사용 및 정교한 분석을 위해 Spark 처리 엔진이 빌드되었습니다. Spark는 메모리 내 분산형 계산 기능을 지원하여 기계 학습 및 그래프 계산에 사용된 반복 알고리즘에 적합합니다.

이 분산 환경에 알고리즘 모델링 기능을 제공하는 확장성 있는 세 가지 기계 학습 라이브러리가 있습니다.

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) - MLlib에는 Spark RDD에 기반하여 빌드된 원래 API가 포함되어 있습니다.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html) - SparkML은 ML 파이프라인을 생성하기 위해 Spark DataFrames에 기반하여 빌드된 높은 수준의 고급 API를 제공하는 새로운 패키지입니다.
* [**MMLSpark**](https://github.com/Azure/mmlspark) - MMLSpark(Apache Spark용 Microsoft Machine Learning 라이브러리)는 Spark에서 데이터 과학자의 생산성을 높이고, 실험 속도를 높이고, 매우 큰 데이터 세트에서 최첨단 기계 학습 기술(딥 러닝 포함)을 활용하도록 설계되었습니다. MMLSpark 라이브러리는 PySpark에서 모델을 작성하기 위한 일반적인 모델링 작업을 간소화합니다.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning 및 Apache Hive

[Azure Machine Learning Studio(클래식)](https://studio.azureml.net/)는 예측 분석을 모델링하는 도구를 제공할 뿐 아니라, 예측 모델을 사용할 준비가 된 웹 서비스로 배포하는 데 사용할 수 있는 완전한 관리형 서비스도 제공합니다. Azure Machine Learning은 클라우드에서 완전한 예측 분석 솔루션을 만들어 예측 모델을 빠르게 만들고, 테스트하고, 운영하고, 관리할 수 있는 도구를 제공합니다. 대형 알고리즘 라이브러리에서 선택하고, 웹 기반 스튜디오를 사용하여 모델을 작성하고, 이 모델을 웹 서비스로 쉽게 배포합니다.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark 및 딥 러닝

[딥 러닝](https://www.microsoft.com/research/group/dltc/)은 인간 두뇌의 생물학적 프로세스에서 영감을 얻은 *DNN(딥 신경망)* 을 사용하는 기계 학습의 한 분야입니다. 많은 연구자들은 딥 러닝이 인공 지능에 대한 유망한 접근 방식이라고 봅니다. 딥 러닝의 예로 언어 변환기, 이미지 인식 시스템 및 컴퓨터 추론이 있습니다. 딥 러닝에서 자체의 작업을 진행하도록 지원하기 위해 Microsoft는 추가 비용이 없고 사용하기 쉬운 오픈 소스 [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)를 개발했습니다. 이 도구 키트는 광범위한 Microsoft 제품, 규모에 맞게 딥 러닝을 배포해야 하는 요구 사항이 있는 전세계 회사 및 최신 알고리즘 및 기술에 관심이 있는 학생들이 광범위하게 사용하고 있습니다.

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>시나리오 - 도시 개발 패턴을 식별하기 위한 이미지 점수 매기기

HDInsight를 사용하는 고급 분석 기계 학습 파이프라인의 예를 살펴보겠습니다.

이 시나리오에서는 HDInsight Spark 클러스터의 PySpark를 사용하여 Azure Blob Storage 계정에 저장된 큰 이미지 컬렉션을 채점하기 위해 딥 러닝 프레임워크인 Microsoft's Cognitive Toolkit (CNTK)에서 생성한 DNN을 운용하는 방법에 대해 알아봅니다. 이 방법은 일반적인 DNN 사용 사례와 항공 이미지 분류에 적용되며, 도시 개발의 최근 패턴을 식별하는 데 사용할 수 있습니다.  미리 학습된 이미지 분류 모델을 사용합니다. 이 모델은 [CIFAR-10 데이터 세트](https://www.cs.toronto.edu/~kriz/cifar.html)에서 미리 학습되어 보류 중인 10,000개 이미지에 적용되었습니다.

이 고급 분석 시나리오에는 다음 세 가지 주요 작업이 있습니다.

1. Apache Spark 2.1.0 배포판을 사용하여 Azure HDInsight Hadoop 클러스터를 만듭니다.
2. 사용자 지정 스크립트를 실행하여 Azure HDInsight Spark 클러스터의 모든 노드에 Microsoft Cognitive Toolkit를 설치합니다.
3. HDInsight Spark 클러스터에 미리 빌드된 Jupyter Notebook을 업로드하여 Spark Python API(PySpark)를 통해 학습된 Microsoft Cognitive Toolkit 딥 러닝 모델을 Azure Blob Storage 계정의 파일에 적용합니다.

이 예에서는 Alex Krizhevsky, Vinod Nair 및 Geoffrey Hinton이 컴파일하고 배포한 CIFAR-10 이미지 집합을 사용합니다. CIFAR-10 데이터 세트에는 상호 배타적인 10개의 클래스에 속하는 60,000개의 32×32 컬러 이미지가 포함되어 있습니다.

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png" alt-text="Machine Learning 예제 이미지" border="false":::

데이터 세트에 대한 자세한 내용은 Alex Krizhevsky의 [작은 이미지에서 여러 계층의 기능 학습](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf)을 참조하세요.

데이터 세트는 50,000개 이미지의 학습 집합과 10,000개 이미지의 테스트 집합으로 분할되었습니다. 첫 번째 집합은 Cognitive Toolkit GitHub 리포지토리에서 [이 자습서](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet)에 따라 Microsoft Cognitive Toolkit를 사용하여 20개 계층 딥 합성곱 ResNet(deep convolutional residual network) 모델을 학습하는 데 사용되었습니다. 나머지 10,000개 이미지는 모델의 정확도를 테스트하는 데 사용되었습니다. 여기서 분산 컴퓨팅이 작동됩니다. 이미지를 사전 처리하고 점수를 매기는 작업은 매우 쉽게 병렬 처리할 수 있습니다. 보유 중인 저장된 학습 모델을 다음과 같이 사용했습니다.

* PySpark를 사용하여 이미지와 학습된 모델을 클러스터의 작업자 노드에 배포했습니다.
* Python을 사용하여 HDInsight Spark 클러스터의 각 노드에서 이미지를 사전 처리했습니다.
* Cognitive Toolkit를 사용하여 모델을 로드하고 각 노드에서 사전 처리된 이미지에 대한 점수를 매겼습니다.
* Jupyter Notebook을 사용하여 PySpark 스크립트를 실행하고, 결과를 집계하고, [Matplotlib](https://matplotlib.org/)를 사용하여 모델 성능을 시각화했습니다.

4개의 작업자 노드가 있는 클러스터에서 10,000개의 이미지 전체를 사전 처리하고 점수를 매기는 데 1분 미만이 걸립니다. 이 모델은 9,100개(91%)까지의 이미지에 대한 레이블을 정확하게 예측합니다. 혼동 행렬에서는 가장 일반적인 분류 오류를 보여 줍니다. 예를 들어 행렬에서는 개가 고양이로 잘못 지정되거나 그 반대로 잘못 지정되는 레이블이 다른 레이블 쌍보다 더 자주 발생한다는 것을 보여 줍니다.

:::image type="content" source="./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png" alt-text="Machine Learning 결과 차트" border="false":::

### <a name="try-it-out"></a>기능 직접 사용해 보기

해당 솔루션을 엔드투엔드로 구현하려면 [이 자습서](../spark/apache-spark-microsoft-cognitive-toolkit.md)에 따라 다음 내용을 수행합니다. HDInsight Spark 클러스터를 설정하고, Cognitive Toolkit을 설치하며 10,000개의 CIFAR 이미지에 대한 점수를 매기는 Jupyter Notebook을 실행합니다.

## <a name="next-steps"></a>다음 단계

Apache Hive 및 Azure Machine Learning

* [Apache Hive 및 Azure Machine Learning 엔드투엔드](/azure/architecture/data-science-process/hive-walkthrough)
* [1 TB 데이터 세트에서 Azure HDInsight Hadoop 클러스터 사용](/azure/architecture/data-science-process/hive-criteo-walkthrough)

Apache Spark 및 MLLib

* [HDInsight에서 Apache Spark를 사용하는 Machine Learning](/azure/architecture/data-science-process/spark-overview)
* [Machine Learning을 사용하는 Apache Spark: HDInsight의 Apache Spark를 사용하여 HVAC 데이터를 사용한 건물 온도 분석](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning을 사용하는 Apache Spark: HDInsight의 Apache Spark를 사용하여 식품 검사 결과 예측](../spark/apache-spark-machine-learning-mllib-ipython.md)

딥 러닝, Cognitive Toolkit 등

* [Azure 데이터 과학 가상 머신](../../machine-learning/data-science-virtual-machine/overview.md)
* [Azure HDInsight에 H2O.ai 소개](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
