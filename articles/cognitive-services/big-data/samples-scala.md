---
title: 빅 데이터 Scala 샘플에 대한 Cognitive Services
description: Azure Databricks용 Cognitive Services를 사용하여 빅 데이터에 대한 MMLSpark 파이프라인을 실행해 봅니다.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 10/28/2021
ms.author: marhamil
ms.openlocfilehash: 154317217ce22196ddde1e4a3c04c39d5eafc0ba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469809"
---
# <a name="quick-examples"></a>빠른 예제

다음 코드 조각은 실행할 준비가 되었으며 Spark에서 Cognitive Services 사용을 시작하는 데 도움이 됩니다. 아래 샘플은 Scala에 있습니다.

이 샘플에서는 다음과 같은 Cognitive Services를 사용합니다.

- 언어 서비스 - 문장 세트의 감정(또는 분위기)을 가져옵니다.
- Computer Vision - 이미지 세트와 연결된 태그(한 단어 설명)를 가져옵니다.
- Bing Image Search - 웹에서 자연어 쿼리와 관련된 이미지를 검색합니다.
- 음성 텍스트 변환 - 오디오 파일을 전사하여 텍스트 기반 기록을 추출합니다.
- Anomaly Detector - 시계열 데이터 내의 변칙을 감지합니다.

## <a name="prerequisites"></a>필수 구성 요소

1. [시작하기](getting-started.md) 단계에 따라 Azure Databricks 및 Cognitive Services 환경을 설정합니다. 이 자습서에는 MMLSpark를 설치하는 방법과 Databricks에서 Spark 클러스터를 만드는 방법이 포함되어 있습니다.
1. Azure Databricks에서 새 Notebook을 만든 후 아래 **공유 코드** 를 복사하여 Notebook의 새 셀에 붙여넣습니다.
1. 아래에서 서비스 샘플을 선택하고, 이를 복사하여 Notebook의 두 번째 새 셀에 붙여넣습니다.
1. 서비스 구독 키 자리 표시자를 사용자 고유의 키로 바꿉니다.
1. 셀의 오른쪽 위 모서리에서 실행 단추(삼각형 아이콘)를 선택한 다음, **셀 실행** 을 선택합니다.
1. 결과가 테이블의 셀 아래에 표시됩니다.

## <a name="shared-code"></a>공유 코드
시작하려면 다음 코드를 프로젝트에 추가합니다.

```scala
import com.microsoft.ml.spark.cognitive._
import spark.implicits._ 

val serviceKey = "ADD-YOUR-SUBSCRIPTION-KEY"
val location = "eastus"
```

## <a name="language-service"></a>언어 서비스

[언어 서비스](../language-service/index.yml)는 텍스트에서 지능형 인사이트를 추출하기 위한 몇 가지 알고리즘을 제공합니다. 예를 들어 지정된 입력 텍스트의 감정을 찾을 수 있습니다. 이 서비스는 `0.0`과 `1.0` 간에 점수를 반환합니다. 낮은 점수는 부정 감정을 나타내고 높은 점수는 긍정 감정을 나타냅니다.  아래 샘플에서는 세 개의 간단한 문장을 사용하고 각각의 감정 점수를 반환합니다.

```scala
import org.apache.spark.sql.functions.col

val df = Seq(
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US")
).toDF("text", "language")

val sentiment = new TextSentiment()
    .setTextCol("text")
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language")

display(sentiment.transform(df).select(col("text"), col("sentiment")(0).getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>예상된 결과

| text                                      | 감정                                             |
|:------------------------------------------|:------------------------------------------------------|
| I am so happy today, its sunny!           | 0.9789592027664185                                    |
| I am frustrated by this rush hour traffic | 0.023795604705810547                                  |
| The cognitive services on spark aint bad  | 0.8888956308364868                                    |

## <a name="computer-vision"></a>Computer Vision

[Computer Vision](../computer-vision/index.yml)은 이미지를 분석하여 얼굴, 개체, 자연어 설명 등의 구조를 식별합니다.
이 샘플에서는 태그를 이미지 목록에 지정합니다. 태그는 인식 가능한 개체, 사람, 경치, 동작과 같이 이미지의 항목에 대한 한 단어 설명입니다.

```scala
// Create a dataframe with the image URLs
val df = Seq(
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg")
).toDF("image")

// Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
val analysis = new AnalyzeImage()
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setVisualFeatures(Seq("Categories","Color","Description","Faces","Objects","Tags"))
    .setOutputCol("results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

// Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select(col("image"), col("results").getItem("tags").getItem("name")).alias("results")))

// Uncomment for full results with all visual feature requests
//display(analysis.transform(df).select(col("image"), col("results")))
``` 

### <a name="expected-result"></a>예상된 결과

| 이미지 | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ['skating' 'person' 'man' 'outdoor' 'riding' 'sport' 'skateboard' 'young' 'board' 'shirt' 'air' 'black' 'park' 'boy' 'side' 'jumping' 'trick' 'ramp' 'doing' 'flying']
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ['dog' 'outdoor' 'fence' 'wooden' 'small' 'brown' 'building' 'sitting' 'front' 'bench' 'standing' 'table' 'walking' 'board' 'beach' 'white' 'holding' 'bridge' 'track']                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ['outdoor' 'grass' 'house' 'building' 'old' 'home' 'front' 'small' 'church' 'stone' 'large' 'grazing' 'yard' 'green' 'sitting' 'leading' 'sheep' 'brick' 'bench' 'street' 'white' 'country' 'clock' 'sign' 'parked' 'field' 'standing' 'garden' 'water' 'red' 'horse' 'man' 'tall' 'fire' 'group'] 

## <a name="bing-image-search"></a>Bing 이미지 검색

[Bing Image Search](../bing-image-search/overview.md)는 웹을 검색하여 사용자의 자연어 쿼리와 관련된 이미지를 검색합니다. 이 샘플에서는 따옴표를 사용하여 이미지를 찾는 텍스트 쿼리를 사용합니다. 쿼리와 관련된 사진이 포함된 이미지 URL의 목록을 반환합니다.


```scala
import org.apache.spark.ml.Pipeline

// Number of images Bing will return per query
val imgsPerBatch = 10

// A list of offsets, used to page into the search results
val df = (0 until 100).map(o => Tuple1(o*imgsPerBatch)).toSeq.toDF("offset")

// Run the Bing Image Search service with our text query
val bingSearch = new BingImageSearch()
    .setSubscriptionKey(bingSearchKey)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images")

// Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
val getUrls = BingImageSearch.getUrlTransformer("images", "url")

// This displays the full results returned, uncomment to use
// display(bingSearch.transform(bingParameters))

// Since we have two services, they are put into a pipeline
val pipeline = new Pipeline().setStages(Array(bingSearch, getUrls))

// Show the results of your search: image URLs
display(pipeline.fit(df).transform(df))
```

### <a name="expected-result"></a>예상된 결과

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |

## <a name="speech-to-text"></a>음성 텍스트 변환

[음성 텍스트 변환](../speech-service/index-speech-to-text.yml) 서비스는 음성 오디오의 스트림 또는 파일을 텍스트로 변환합니다. 이 샘플에서는 두 개의 오디오 파일을 전사합니다. 첫 번째 파일은 이해하기 쉽고, 두 번째 파일은 더 어렵습니다.

```scala
import org.apache.spark.sql.functions.col

// Create a dataframe with audio URLs, tied to the column called "url"
val df = Seq(("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav"),
             ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3")).toDF("url")

// Run the Speech-to-text service to translate the audio into text
val speechToText = new SpeechToTextSDK()
    .setSubscriptionKey(serviceKey)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked")

// Show the results of the translation
display(speechToText.transform(df).select(col("url"), col("text").getItem("DisplayText")))
```

### <a name="expected-result"></a>예상된 결과

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | 사용자 지정 음성은 오디오 데이터를 사용자 지정 음성 포털의 해당 인식 결과와 비교하여 모델의 인식 품질을 시각적으로 검사할 수 있는 도구를 제공합니다. 업로드된 오디오를 재생하여 제공된 인식 결과가 올바른지 확인할 수 있습니다. 이 도구를 사용하면 오디오 데이터를 전사하지 않고도 Microsoft의 기준 음성 텍스트 변환 모델 또는 학습된 사용자 지정 모델의 품질을 빠르게 검사할 수 있습니다. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | 점잖은 분의 생각에 대한 시각적 검사를 추가합니다.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | 내 말을 듣습니다. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | 작업 라디오인 경우에도 사용할 수 있습니다. |

## <a name="anomaly-detector"></a>Anomaly Detector

[Anomaly Detector](../anomaly-detector/index.yml)는 시계열 데이터에서 이상 상태를 감지하는 데 유용합니다. 이 샘플에서는 서비스를 사용하여 전체 시계열에서 변칙을 찾습니다.

```scala
import org.apache.spark.sql.functions.{col, lit}

val anomalyKey = "84a2c303cc7e49f6a44d692c27fb9967"

val df = Seq(
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0),
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0)
  ).toDF("timestamp", "value").withColumn("group", lit("series1"))

// Run the Anomaly Detector service to look for irregular data
val anamolyDetector = new SimpleDetectAnomalies()
    .setSubscriptionKey(anomalyKey)
    .setLocation("eastus")
    .setTimestampCol("timestamp")
    .setValueCol("value")
    .setOutputCol("anomalies")
    .setGroupbyCol("group")
    .setGranularity("monthly")

// Show the full results of the analysis with the anomalies marked as "True"
display(anamolyDetector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
```

### <a name="expected-result"></a>예상된 결과

| timestamp            |   값 | isAnomaly   |
|:---------------------|--------:|:------------|
| 1972-01-01T00:00:00Z |     826 | False       |
| 1972-02-01T00:00:00Z |     799 | False       |
| 1972-03-01T00:00:00Z |     890 | False       |
| 1972-04-01T00:00:00Z |     900 | False       |
| 1972-05-01T00:00:00Z |     766 | False       |
| 1972-06-01T00:00:00Z |     805 | False       |
| 1972-07-01T00:00:00Z |     821 | False       |
| 1972-08-01T00:00:00Z |   20000 | True        |
| 1972-09-01T00:00:00Z |     883 | False       |
| 1972-10-01T00:00:00Z |     898 | False       |
| 1972-11-01T00:00:00Z |     957 | False       |
| 1972-12-01T00:00:00Z |     924 | False       |
| 1973-01-01T00:00:00Z |     881 | False       |
| 1973-02-01T00:00:00Z |     837 | False       |
| 1973-03-01T00:00:00Z |    9000 | True        |