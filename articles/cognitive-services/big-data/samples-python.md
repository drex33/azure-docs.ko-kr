---
title: 빅 데이터용 Cognitive Services - Python 샘플
description: Azure Databricks용 Python에서 Cognitive Services 샘플을 사용하여 빅 데이터에 대한 MMLSpark 파이프라인을 실행해 봅니다.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 10/28/2021
ms.author: marhamil
ms.openlocfilehash: acf81e5d9b0502e2eab309ed2fdc239ffae9afc7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450289"
---
# <a name="python-samples-for-cognitive-services-for-big-data"></a>빅 데이터용 Cognitive Services에 대한 Python 샘플

다음 코드 조각은 실행할 준비가 되었으며, Python을 사용하여 Spark에서 Cognitive Services 사용을 시작하는 데 도움이 됩니다.

이 문서의 샘플에서 사용하는 Cognitive Services는 다음과 같습니다.

- 언어 서비스 - 문장 세트의 감정(또는 분위기)을 가져옵니다.
- Computer Vision - 이미지 세트와 연결된 태그(한 단어 설명)를 가져옵니다.
- Bing Image Search - 웹에서 자연어 쿼리와 관련된 이미지를 검색합니다.
- 음성 텍스트 변환 - 오디오 파일을 전사하여 텍스트 기반 기록을 추출합니다.
- Anomaly Detector - 시계열 데이터 내의 변칙을 감지합니다.

## <a name="prerequisites"></a>필수 구성 요소

1. [시작](getting-started.md)의 단계에 따라 Azure Databricks 및 Cognitive Services 환경을 설정합니다. 이 자습서에서는 MMLSpark를 설치하는 방법과 Databricks에서 Spark 클러스터를 만드는 방법을 보여 줍니다.
1. Azure Databricks에서 새 Notebook이 만들어지면 아래의 **공유 코드** 를 복사하여 Notebook의 새 셀에 붙여넣습니다.
1. 아래에서 서비스 샘플을 선택하고, 이를 복사하여 Notebook의 두 번째 새 셀에 붙여넣습니다.
1. 서비스 구독 키 자리 표시자를 사용자 고유의 키로 바꿉니다.
1. 셀의 오른쪽 위 모서리에서 실행 단추(삼각형 아이콘)를 선택한 다음, **셀 실행** 을 선택합니다.
1. 결과가 테이블의 셀 아래에 표시됩니다.

## <a name="shared-code"></a>공유 코드

시작하려면 다음 코드를 프로젝트에 추가해야 합니다.

```python
from mmlspark.cognitive import *

# A general Cognitive Services key for the Language service and Computer Vision (or use separate keys that belong to each service)
service_key = "ADD_YOUR_SUBSCRIPION_KEY"
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY"
# An Anomaly Dectector subscription key
anomaly_key = "ADD_YOUR_SUBSCRIPION_KEY"

# Validate the key
assert service_key != "ADD_YOUR_SUBSCRIPION_KEY"
```    

## <a name="language-service-sample"></a>언어 서비스 샘플

[언어 서비스](../language-service/index.yml)는 텍스트에서 지능형 인사이트를 추출하기 위한 몇 가지 알고리즘을 제공합니다. 예를 들어 지정된 입력 텍스트의 감정을 찾을 수 있습니다. 이 서비스는 0.0과 1.0 사이의 점수를 반환합니다. 여기서 낮은 점수는 부정적인 감정을 나타내고, 높은 점수는 긍정적인 감정을 나타냅니다.  이 샘플에서는 세 개의 간단한 문장을 사용하고, 각각에 대한 감정을 반환합니다.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

# Run the Language service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format
display(sentiment.transform(df).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-result"></a>예상된 결과

| text                                      | 감정                                             |
|:------------------------------------------|:------------------------------------------------------|
| I am so happy today, its sunny!           | 긍정                                              |
| I am frustrated by this rush hour traffic | 부정                                              |
| The cognitive services on spark aint bad  | 긍정                                              |

## <a name="computer-vision-sample"></a>Computer Vision 샘플

[Computer Vision](../computer-vision/index.yml)은 이미지를 분석하여 얼굴, 개체 및 자연어 설명과 같은 구조를 식별합니다. 이 샘플에서는 태그를 이미지 목록에 지정합니다. 태그는 인식 가능한 개체, 사람, 경치, 동작과 같이 이미지의 항목에 대한 한 단어 설명입니다.

```python

# Create a dataframe with the image URLs
df = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select("image", "analysis_results.description.tags"))
```

### <a name="expected-result"></a>예상된 결과

| 이미지 | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ['skating' 'person' 'man' 'outdoor' 'riding' 'sport' 'skateboard' 'young' 'board' 'shirt' 'air' 'black' 'park' 'boy' 'side' 'jumping' 'trick' 'ramp' 'doing' 'flying']
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ['dog' 'outdoor' 'fence' 'wooden' 'small' 'brown' 'building' 'sitting' 'front' 'bench' 'standing' 'table' 'walking' 'board' 'beach' 'white' 'holding' 'bridge' 'track']                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ['outdoor' 'grass' 'house' 'building' 'old' 'home' 'front' 'small' 'church' 'stone' 'large' 'grazing' 'yard' 'green' 'sitting' 'leading' 'sheep' 'brick' 'bench' 'street' 'white' 'country' 'clock' 'sign' 'parked' 'field' 'standing' 'garden' 'water' 'red' 'horse' 'man' 'tall' 'fire' 'group']


## <a name="bing-image-search-sample"></a>Bing Image Search 샘플

[Bing Image Search](../bing-image-search/overview.md)는 웹을 검색하여 사용자의 자연어 쿼리와 관련된 이미지를 검색합니다. 이 샘플에서는 따옴표를 사용하여 이미지를 찾는 텍스트 쿼리를 사용합니다. 쿼리와 관련된 사진이 포함된 이미지 URL의 목록을 반환합니다.

```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 10
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(100)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bing_search_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")

# This displays the full results returned, uncomment to use
# display(bingSearch.transform(bingParameters))

# Since we have two services, they are put into a pipeline
pipeline = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
display(pipeline.transform(bingParameters))
```

### <a name="expected-result"></a>예상된 결과

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |


## <a name="speech-to-text-sample"></a>음성 텍스트 변환 샘플
[음성 텍스트 변환](../speech-service/index-speech-to-text.yml) 서비스는 음성 오디오의 스트림 또는 파일을 텍스트로 변환합니다. 이 샘플에서는 두 개의 오디오 파일을 전사합니다. 첫 번째 파일은 이해하기 쉽고, 두 번째 파일은 더 어렵습니다.

```python

# Create a dataframe with our audio URLs, tied to the column called "url"
df = spark.createDataFrame([("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav",),
                           ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3",)
                           ], ["url"])

# Run the Speech-to-text service to translate the audio into text
speech_to_text = (SpeechToTextSDK()
    .setSubscriptionKey(service_key)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked"))

# Show the results of the translation
display(speech_to_text.transform(df).select("url", "text.DisplayText"))
```

### <a name="expected-result"></a>예상된 결과

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | 사용자 지정 음성은 오디오 데이터를 사용자 지정 음성 포털의 해당 인식 결과와 비교하여 모델의 인식 품질을 시각적으로 검사할 수 있는 도구를 제공합니다. 업로드된 오디오를 재생하여 제공된 인식 결과가 올바른지 확인할 수 있습니다. 이 도구를 사용하면 오디오 데이터를 전사하지 않고도 Microsoft의 기준 음성 텍스트 변환 모델 또는 학습된 사용자 지정 모델의 품질을 빠르게 검사할 수 있습니다. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | 점잖은 분의 생각에 대한 시각적 검사를 추가합니다.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | 내 말을 듣습니다. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | 나도 들을 수 있는 라디오에 안심할 수 있어서 좋습니다. |


## <a name="anomaly-detector-sample"></a>Anomaly Detector 샘플

[Anomaly Detector](../anomaly-detector/index.yml)는 시계열 데이터의 변칙을 감지하는 데 유용합니다. 이 샘플에서는 서비스를 사용하여 전체 시계열에서 변칙을 찾습니다.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df = spark.createDataFrame([
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
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomaly_key)
  .setLocation("eastus")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
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

## <a name="arbitrary-web-apis"></a>임의의 웹 API

HTTP on Spark를 사용하면 빅 데이터 파이프라인에서 웹 서비스를 사용할 수 있습니다. 다음 예제에서는 [세계 은행 API](http://api.worldbank.org/v2/country/)를 사용하여 다양한 전 세계 국가에 대한 정보를 가져옵니다.

```python
from requests import Request
from mmlspark.io.http import HTTPTransformer, http_udf
from pyspark.sql.functions import udf, col

# Use any requests from the python requests library
def world_bank_request(country):
  return Request("GET", "http://api.worldbank.org/v2/country/{}?format=json".format(country))

# Create a dataframe with spcificies which countries we want data on
df = (spark.createDataFrame([("br",),("usa",)], ["country"])
  .withColumn("request", http_udf(world_bank_request)(col("country"))))

# Much faster for big data because of the concurrency :)
client = (HTTPTransformer()
      .setConcurrency(3)
      .setInputCol("request")
      .setOutputCol("response"))

# Get the body of the response
def get_response_body(resp):
  return resp.entity.content.decode()

# Show the details of the country data returned
display(client.transform(df).select("country", udf(get_response_body)(col("response")).alias("response")))
```

### <a name="expected-result"></a>예상된 결과

| country   | 응답 |
|:----------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| br | [{"page":1,"pages":1,"per_page":"50","total":1},[{"id":"BRA","iso2Code":"BR","name":"Brazil","region":{"id":"LCN","iso2code":"ZJ","value":"Latin America & Caribbean "},"adminregion":{"id":"LAC","iso2code":"XJ","value":"Latin America & Caribbean (excluding high income)"},"incomeLevel":{"id":"UMC","iso2code":"XT","value":"Upper middle income"},"lendingType":{"id":"IBD","iso2code":"XF","value":"IBRD"},"capitalCity":"Brasilia","longitude":"-47.9292","latitude":"-15.7801"}]] |
| usa  | [{"page":1,"pages":1,"per_page":"50","total":1},[{"id":"USA","iso2Code":"US","name":"United States","region":{"id":"NAC","iso2code":"XU","value":"North America"},"adminregion":{"id":"","iso2code":"","value":""},"incomeLevel":{"id":"HIC","iso2code":"XD","value":"High income"},"lendingType":{"id":"LNX","iso2code":"XX","value":"Not classified"},"capitalCity":"Washington D.C.","longitude":"-77.032","latitude":"38.8895"}]] |

## <a name="see-also"></a>참고 항목

* [레시피: Anomaly Detection](./recipes/anomaly-detection.md)
* [레시피: 아트 탐색기](./recipes/art-explorer.md)