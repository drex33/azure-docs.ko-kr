---
title: 빅 데이터에 대한 Cognitive Services
description: Python, Java 및 Scala를 사용하여 큰 데이터 세트에서 Azure Cognitive Services를 활용하는 방법을 알아봅니다. 빅 데이터용 Cognitive Services를 사용하면 지속적으로 향상되는 인텔리전트 모델을 Apache Spark&trade; 및 SQL 컴퓨팅에 직접 포함할 수 있습니다.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.date: 10/28/2021
ms.author: marhamil
ms.openlocfilehash: 71b89cc6b54ff99864bdd0f15ebcee472a36cd70
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434487"
---
# <a name="azure-cognitive-services-for-big-data"></a>빅 데이터용 Azure Cognitive Services

![빅 데이터용 Azure Cognitive Services](media/cognitive-services-big-data-overview.svg)

빅 데이터용 Azure Cognitive Services는 사용자가 [Apache Spark&trade;](/dotnet/spark/what-is-spark)를 통해 테라바이트 단위의 데이터를 위한 채널로 Cognitive Services를 사용할 수 있도록 지원합니다. 빅 데이터용 Cognitive Services를 사용하면 데이터 저장소가 있는 대규모 인텔리전트 애플리케이션을 쉽게 만들 수 있습니다.

빅 데이터용 Cognitive Services를 사용하면 지속적으로 향상되는 인텔리전트 모델을 Apache Spark&trade; 및 SQL 컴퓨팅에 직접 포함할 수 있습니다. 이러한 도구를 사용하면 개발자가 낮은 수준의 네트워킹 세부 정보를 알고 있을 필요가 없으므로 스마트 분산 애플리케이션을 만드는 데 집중할 수 있습니다.

## <a name="features-and-benefits"></a>기능 및 혜택

빅 데이터용 Cognitive Services는 [컨테이너화된 Cognitive Services](../cognitive-services-container-support.md)뿐만 아니라 전 세계 모든 지역의 서비스도 사용할 수 있습니다. 컨테이너는 대기 시간이 매우 짧은 응답을 사용하는 연결성이 낮거나 없는 배포를 지원합니다. 컨테이너화된 Cognitive Services는 Spark 클러스터의 작업자 노드 또는 외부 오케스트레이터(예: Kubernetes)에서 로컬로 직접 실행할 수 있습니다.

## <a name="supported-services"></a>지원되는 서비스

API 및 SDK를 통해 액세스되는 [Cognitive Services](../index.yml)는 개발자가 AI 또는 데이터 과학 기술을 사용하지 않고도 인텔리전트 애플리케이션을 빌드할 수 있도록 지원합니다. Cognitive Services를 사용하면 애플리케이션에서 보고, 듣고, 말하고, 인식하고, 추론할 수 있습니다. Cognitive Services를 사용하려면 애플리케이션에서 네트워크를 통해 데이터를 서비스에 보내야 합니다. 수신되면 서비스에서 인텔리전트 응답을 반환합니다. 빅 데이터 워크로드에 사용할 수 있는 서비스는 다음과 같습니다.

### <a name="vision"></a>시각

|서비스 이름|서비스 설명|
|:-----------|:------------------|
|[Computer Vision](../computer-vision/index.yml "Computer Vision")| Computer Vision 서비스는 이미지를 처리하고 정보를 반환하는 고급 알고리즘에 대한 액세스를 제공합니다. |
|[Face](../face/index.yml "Face")| Face 서비스는 개선된 얼굴 인식 알고리즘에 액세스할 수 있도록 하여 얼굴 특성 감지 및 인식을 지원합니다. |

### <a name="speech"></a>Speech

|서비스 이름|서비스 설명|
|:-----------|:------------------|
|[Speech Service](../speech-service/index.yml "Speech Service")|Speech Service는 음성 인식, 음성 합성, 음성 번역 및 화자 검증/식별과 같은 기능에 대한 액세스를 제공합니다.|

### <a name="decision"></a>의사 결정

|서비스 이름|서비스 설명|
|:-----------|:------------------|
|[Anomaly Detector](../anomaly-detector/index.yml "Anomaly Detector") | Anomaly Detector(미리 보기) 서비스를 사용하면 시계열 데이터의 변칙을 모니터링하고 감지할 수 있습니다.|

### <a name="language"></a>언어

|서비스 이름|서비스 설명|
|:-----------|:------------------|
|[언어 서비스](../language-service/index.yml "언어 서비스")| 언어 서비스는 감정 분석, 핵심 구 추출 및 언어 감지를 위해 원시 텍스트에 대한 자연어 처리를 제공합니다.|

### <a name="search"></a>검색

|서비스 이름|서비스 설명|
|:-----------|:------------------|
|[Bing Image Search](/azure/cognitive-services/bing-image-search "Bing 이미지 검색")|Bing Image Search 서비스는 사용자의 쿼리와 관련이 있다고 확인된 이미지의 표시를 반환합니다.|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>빅 데이터용 Cognitive Services에 지원되는 프로그래밍 언어

빅 데이터용 Cognitive Services는 Apache Spark를 기반으로 합니다. Apache Spark는 Java, Scala, Python, R 및 기타 여러 언어를 지원하는 분산 컴퓨팅 라이브러리입니다. 현재 지원되는 언어는 다음과 같습니다.

### <a name="python"></a>Python

PySpark API를 [Microsoft ML for Apache Spark](https://aka.ms/spark)의 `mmlspark.cognitive` 네임스페이스에 제공합니다. 자세한 내용은 [Python 개발자 API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html)를 참조하세요. 사용 예제는 [Python 샘플](samples-python.md)을 참조하세요.

### <a name="scala-and-java"></a>Scala 및 Java

Scala 및 Java 기반 Spark API를 [Microsoft ML for Apache Spark](https://aka.ms/spark)의 `com.microsoft.ml.spark.cognitive` 네임스페이스에 제공합니다. 자세한 내용은 [Scala 개발자 API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package)를 참조하세요. 사용 예제는 [Scala 샘플](samples-scala.md)을 참조하세요.

## <a name="supported-platforms-and-connectors"></a>지원되는 플랫폼 및 커넥터

빅 데이터용 Cognitive Services에는 Apache Spark가 필요합니다. 빅 데이터용 Cognitive Services를 지원하는 몇 가지 Apache Spark 플랫폼이 있습니다.

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks)는 Microsoft Azure Cloud Services 플랫폼에 대해 최적화된 Apache Spark 기반 분석 플랫폼입니다. 한 번 클릭 설정, 간소화된 워크플로 및 데이터 과학자, 데이터 엔지니어 및 비즈니스 분석가 간의 협업을 지원하는 대화형 작업 영역을 제공합니다.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

[Azure Synapse Analytics](/azure/databricks/data/data-sources/azure/synapse-analytics)는 대규모 병렬 처리를 사용하는 엔터프라이즈 데이터 웨어하우스입니다. Synapse Analytics를 사용하면 페타바이트 단위의 데이터에서 복합 쿼리를 빠르게 실행할 수 있습니다. Azure Synapse Analytics는 직관적인 Jupyter Notebook 인터페이스를 사용하여 Spark 작업을 실행할 수 있는 관리형 Spark 풀을 제공합니다.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

[AKS(Azure Kubernetes Service)](../../aks/index.yml)는 Docker 컨테이너 및 분산 애플리케이션을 대규모로 조정합니다. AKS는 Azure에서 Kubernetes 사용을 간소화하는 관리되는 Kubernetes 제품입니다. Kubernetes는 Cognitive Service 크기 조정, 대기 시간 및 네트워킹을 세부적으로 제어할 수 있습니다. 그러나 Apache Spark에 익숙하지 않은 경우 Azure Databricks 또는 Azure Synapse Analytics를 사용하는 것이 좋습니다.

### <a name="data-connectors"></a>데이터 커넥터

Spark 클러스터가 있는 경우 다음 단계는 데이터에 연결하는 것입니다. Apache Spark에는 광범위한 데이터베이스 커넥터 컬렉션이 있습니다. 이러한 커넥터를 사용하면 저장된 위치에 관계없이 애플리케이션에서 큰 데이터 세트를 사용할 수 있습니다. 지원되는 데이터베이스 및 커넥터에 대한 자세한 내용은[Azure Databricks에 지원되는 데이터 원본 목록](/azure/databricks/data/data-sources/)을 참조하세요.

## <a name="concepts"></a>개념

### <a name="spark"></a>Spark

[Apache Spark&trade;](http://spark.apache.org/)는 대규모 데이터를 처리하기 위한 통합 분석 엔진입니다. 병렬 처리 프레임워크는 빅 데이터 및 분석 애플리케이션의 성능을 향상시킵니다. Spark는 핵심 애플리케이션 코드를 변경하지 않고도 일괄 처리 및 스트림 처리 시스템으로 작동할 수 있습니다.

Spark의 기본은 DataFrame입니다. 이는 Apache Spark 작업자 노드에 분산된 데이터의 테이블 형식 컬렉션입니다. Spark DataFrame은 관계형 데이터베이스의 테이블 또는 R/Python의 데이터 프레임과 비슷하지만 크기가 무제한입니다. DataFrame은 정형 데이터 파일, Hive의 테이블 또는 외부 데이터베이스와 같은 여러 원본에서 생성할 수 있습니다. 데이터가 Spark DataFrame에 저장되면 다음을 수행할 수 있습니다.

   - 테이블 조인 및 필터와 같은 SQL 스타일 계산을 수행합니다.
   - MapReduce 스타일 병렬 처리를 사용하여 함수를 큰 데이터 세트에 적용합니다.
   - Microsoft Machine Learning for Apache Spark를 사용하여 분산 Machine Learning을 적용합니다.
   - 빅 데이터용 Cognitive Services를 사용하여 즉시 사용 가능한 인텔리전트 서비스를 통해 데이터를 보강합니다.

### <a name="microsoft-machine-learning-for-apache-spark-mmlspark"></a>MMLSpark(Microsoft Machine Learning for Apache Spark)

[MMLSpark(Microsoft Machine Learning for Apache Spark)](https://mmlspark.blob.core.windows.net/website/index.html#install)는 Apache Spark를 기반으로 하는 오픈 소스 분산 ML(기계 학습) 라이브러리입니다. 이 패키지에는 빅 데이터용 Cognitive Services가 포함되어 있습니다. 또한 MMLSpark에는 LightGBM, Vowpal Wabbit, OpenCV, LIME 등과 같은 몇 가지 다른 Apache Spark용 ML 도구가 포함되어 있습니다. MMLSpark를 사용하면 모든 Spark 데이터 원본에서 강력한 예측 및 분석 모델을 빌드할 수 있습니다.

### <a name="http-on-spark"></a>HTTP on Spark

빅 데이터용 Cognitive Services는 인텔리전트 웹 서비스를 빅 데이터와 통합할 수 있는 방법의 예입니다. 웹 서비스는 전 세계의 많은 애플리케이션을 강화시키며, 대부분의 서비스는 HTTP(Hypertext Transfer Protocol)를 통해 통신합니다. *임의* 의 웹 서비스를 대규모로 사용하기 위해 HTTP on Spark가 제공됩니다. HTTP on Spark를 사용하면 모든 웹 서비스를 통해 테라바이트 단위의 데이터를 전달할 수 있습니다. 이 기술을 내부적으로 사용하여 빅 데이터용 Cognitive Services를 강화시킵니다.

## <a name="developer-samples"></a>개발자 샘플

- [레시피: 예측 유지 관리](recipes/anomaly-detection.md)
- [레시피: 인텔리전트 아트 탐색](recipes/art-explorer.md)

## <a name="blog-posts"></a>블로그 게시물

- [Cognitive Services가 Apache Spark에서 작동하는 방법에 대해 자세히 알아보기&trade;](https://azure.microsoft.com/blog/dear-spark-developers-welcome-to-azure-cognitive-services/)
- [Spark에서 Deep Learning 및 Computer Vision을 사용하여 눈표범 구조](http://www.datawizard.io/2017/06/27/saving-snow-leopards-with-deep-learning-and-computer-vision-on-spark/)
- [Microsoft Research 팟캐스트: MMLSpark, AI for Good 강화 - Mark Hamilton](https://blubrry.com/microsoftresearch/49485070/092-mmlspark-empowering-ai-for-good-with-mark-hamilton/)
- [교육용 백서: 대규모 인텔리전트 마이크로서비스](https://arxiv.org/abs/2009.08044)

## <a name="webinars-and-videos"></a>웨비나 및 비디오

- [Spark의 Azure Cognitive Services: 임베디드 인텔리전트 서비스를 사용하는 클러스터](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Spark Summit 기조 연설: 확장 가능한 AI for Good](https://databricks.com/session_eu19/scalable-ai-for-good)
- [Cosmos DB의 빅 데이터용 Cognitive Services](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)
- [대규모 인텔리전트 마이크로서비스에 대한 Lightning Talk](https://www.youtube.com/watch?v=BtuhmdIy9Fk&t=6s)

## <a name="next-steps"></a>다음 단계

- [빅 데이터용 Cognitive Services 시작](getting-started.md)
- [간단한 Python 예제](samples-python.md)
- [간단한 Scala 예제](samples-scala.md)