---
title: 빅 데이터용 Cognitive Services 시작
description: Azure Databricks에서 Cognitive Services를 사용하여 MMLSpark 파이프라인을 설정하고 샘플을 실행합니다.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 10/28/2021
ms.author: marhamil
ms.openlocfilehash: f8d8e0ebbed2acc51c04a5ae83743e1fbf37ae6d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446565"
---
# <a name="getting-started"></a>시작

환경 설정은 데이터에 대한 파이프라인을 빌드하기 위한 첫 번째 단계입니다. 환경이 준비되면 샘플을 빠르고 쉽게 실행할 수 있습니다.

이 문서에서는 다음 단계를 수행하여 시작합니다.

1. [Cognitive Services 리소스 만들기](#create-a-cognitive-services-resource)
1. [Apache Spark 클러스터 만들기](#create-an-apache-spark-cluster)
1. [샘플 사용해 보기](#try-a-sample)

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services 리소스 만들기

빅 데이터 Cognitive Services를 사용하려면 먼저 워크플로에 대한 Cognitive Service를 만들어야 합니다. Cognitive Services에는 Azure에서 호스팅되는 클라우드 서비스와 사용자가 관리하는 컨테이너화된 서비스의 두 가지 기본 유형이 있습니다. 더 간단한 클라우드 기반 Cognitive Services부터 시작하는 것이 좋습니다.

### <a name="cloud-services"></a>클라우드 서비스

클라우드 기반 Cognitive Services는 Azure에서 호스팅되는 인텔리전트 알고리즘입니다. 이러한 서비스는 학습하지 않고 사용할 수 있으며 인터넷에 연결하기만 하면 됩니다. [Azure Portal](../cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows) 또는 [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows)를 사용하여 Cognitive Service를 만들 수 있습니다.

### <a name="containerized-services-optional"></a>컨테이너화된 서비스(선택 사항)

애플리케이션 또는 워크로드에서 대규모 데이터 세트를 사용하거나 프라이빗 네트워킹이 필요하거나 클라우드에 연결할 수 없는 경우 클라우드 서비스와 통신하지 못할 수 있습니다. 이 경우 컨테이너화된 Cognitive Services에는 다음과 같은 이점이 있습니다.

* **낮은 연결 수준**: 컨테이너화된 Cognitive Services는 온-클라우드 및 오프-클라우드 컴퓨팅 환경 모두에서 배포할 수 있습니다. 애플리케이션에서 클라우드에 연결할 수 없는 경우 컨테이너화된 Cognitive Services를 애플리케이션에 배포하는 것이 좋습니다.

* **짧은 대기 시간**: 컨테이너화된 서비스에는 클라우드와의 왕복 통신이 필요하지 않으므로 응답이 훨씬 더 짧은 대기 시간으로 반환됩니다.

* **개인 정보 및 데이터 보안**: 중요한 데이터가 네트워크를 떠나지 않도록 컨테이너화된 서비스를 개인 네트워크에 배포할 수 있습니다.

* **높은 확장성**: 컨테이너화된 서비스에는 "요금 제한"이 없으며, 사용자 관리 컴퓨터에서 실행됩니다. 따라서 훨씬 더 큰 워크로드를 처리하기 위해 Cognitive Services의 크기를 무제한으로 조정할 수 있습니다.

[이 가이드](../cognitive-services-container-support.md?tabs=luis)에 따라 컨테이너화된 Cognitive Service를 만듭니다.

## <a name="create-an-apache-spark-cluster"></a>Apache Spark 클러스터 만들기

[Apache Spark&trade;](http://spark.apache.org/)는 빅 데이터를 처리하도록 설계된 분산 컴퓨팅 프레임워크입니다. 사용자는 Azure Databricks, Azure Synapse Analytics, HDInsight 및 Azure Kubernetes Services와 같은 서비스를 사용하여 Azure에서 Apache Spark 작업을 수행할 수 있습니다. 빅 데이터 Cognitive Services를 사용하려면 먼저 클러스터를 만들어야 합니다. Spark 클러스터가 이미 있는 경우 예제를 자유롭게 사용해 보세요.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks는 한 번 클릭 설정, 간소화된 워크플로 및 대화형 작업 영역이 있는 Apache Spark 기반 분석 플랫폼입니다. 데이터 과학자, 엔지니어 및 비즈니스 분석가 간에 협업하는 데 자주 사용됩니다. Azure Databricks에서 빅 데이터 Cognitive Services를 사용하려면 다음 단계를 수행합니다.

1. [Azure Databricks 작업 영역 만들기](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)
1. [Databricks에서 Spark 클러스터 만들기](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal#create-a-spark-cluster-in-databricks)
1. 빅 데이터 Cognitive Services 설치
    * Databricks 작업 영역에 새 라이브러리 만들기  
       <img src="media/create-library.png" alt="Create library" width="50%"/>
    * maven 좌표(좌표:  `com.microsoft.ml.spark:mmlspark_2.11:1.0.0-rc3`, 리포지토리: `https://mmlspark.azureedge.net/maven`) 입력  
      <img src="media/library-coordinates.png" alt="Library Coordinates" width="50%"/>
    * 클러스터에 라이브러리 설치  
      <img src="media/install-library.png" alt="Install Library on Cluster" width="50%"/>

### <a name="azure-synapse-analytics-optional"></a>Azure Synapse Analytics(선택 사항)

필요에 따라 Synapse Analytics를 사용하여 Spark 클러스터를 만들 수 있습니다. Azure Synapse Analytics는 엔터프라이즈 데이터 웨어하우징과 빅 데이터 분석을 통합합니다. 서버리스 주문형 리소스 또는 프로비저닝된 리소스를 규모에 맞게 사용하여 데이터를 사용자의 용어로 자유롭게 쿼리할 수 있습니다. Azure Synapse Analytics 사용을 시작하려면 다음 단계를 수행합니다.

1. [Synapse 작업 영역(미리 보기) 만들기](../../synapse-analytics/quickstart-create-workspace.md)
1. [Azure Portal을 사용하여 새 서버리스 Apache Spark 풀(미리 보기)을 만듭니다](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md).

Azure Synapse Analytics에는 Cognitive Services용 빅 데이터가 기본적으로 설치됩니다.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

컨테이너화된 Cognitive Services를 사용하는 경우 Spark를 컨테이너와 함께 배포하는 데 인기 있는 한 가지 옵션은 Azure Kubernetes Service입니다.

Azure Kubernetes Service를 시작하려면 다음 단계를 수행합니다.

1. [Azure Portal을 사용하여 AKS(Azure Kubernetes Service) 클러스터 배포](../../aks/kubernetes-walkthrough-portal.md)
1. [Apache Spark 2.4.0 Helm 차트 설치](https://hub.helm.sh/charts/microsoft/spark)
1. [Helm을 사용하여 Cognitive Service 컨테이너 설치](../computer-vision/deploy-computer-vision-on-premises.md)

## <a name="try-a-sample"></a>샘플 사용해 보기

Spark 클러스터 및 환경이 설정되면 간단한 샘플을 실행할 수 있습니다. 이 섹션에서는 Azure Databricks에서 Cognitive Services용 빅 데이터를 사용하는 방법을 보여 줍니다.

먼저, Azure Databricks에서 Notebook을 만들 수 있습니다. 다른 Spark 클러스터 공급자의 경우 해당 Notebook 또는 Spark 제출을 사용합니다.

1. **Azure Databricks** 메뉴에서 **새 Notebook** 을 선택하여 새 Databricks Notebook을 만듭니다.

    <img src="media/new-notebook.png" alt="Create a new notebook" width="50%"/>

1. **Notebook 만들기** 대화 상자에서 이름을 입력하고 언어로 **Python** 을 선택한 다음, 이전에 만든 Spark 클러스터를 선택합니다.

    <img src="media/databricks-notebook-details.jpg" alt="New notebook details" width="50%"/>

    **만들기** 를 선택합니다.

1. 이 코드 조각을 새 Notebook에 붙여넣습니다.

```python
from mmlspark.cognitive import *
from pyspark.sql.functions import col

# Add your subscription key from the Language service (or a general Cognitive Service key)
service_key = "ADD-SUBSCRIPTION-KEY-HERE"

df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

results = sentiment.transform(df)

# Show the results in a table
display(results.select("text", col("sentiment")[0].getItem("score").alias("sentiment")))

```

1. Azure Portal의 언어 리소스에 있는 **키 및 엔드포인트** 메뉴에서 구독 키를 가져옵니다.
1. Databricks Notebook 코드의 구독 키 자리 표시자를 구독 키로 바꿉니다.
1. Notebook 셀의 오른쪽 위에서 재생 또는 삼각형 기호를 선택하여 샘플을 실행합니다. 필요에 따라 Notebook의 위쪽에서 **모두 실행** 을 선택하여 모든 셀을 실행합니다. 대답이 테이블의 셀 아래에 표시됩니다.

### <a name="expected-results"></a>예상 결과

| text                                      |   감정 |
|:------------------------------------------|------------:|
| I am so happy today, its sunny!           |   0.978959  |
| I am frustrated by this rush hour traffic |   0.0237956 |
| The cognitive services on spark aint bad  |   0.888896  |

## <a name="next-steps"></a>다음 단계

- [간단한 Python 예제](samples-python.md)
- [간단한 Scala 예제](samples-scala.md)
- [레시피: 예측 유지 관리](recipes/anomaly-detection.md)
- [레시피: 인텔리전트 아트 탐색](recipes/art-explorer.md)
