---
title: '자습서: Cognitive Service를 사용하는 Translator'
description: Azure Synapse Analytics에서 Translator를 사용하는 방법을 알아봅니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 11/02/2021
author: ruixinxu
ms.author: ruxu
ms.custom: ignite-fall-2021
ms.openlocfilehash: e0ae2aea56e7bfa255daba30c99b49bc66b5b4b5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101654"
---
# <a name="tutorial-translator-with-cognitive-service"></a>자습서: Cognitive Service를 사용하는 Translator

[Translator](../../cognitive-services/Translator/index.yml)는 언어 번역 및 기타 언어 관련 작업을 수행할 수 있는 [Azure Cognitive Service](../../cognitive-services/index.yml)입니다. 이 자습서에서는 [Translator](../../cognitive-services/Translator/index.yml)를 사용하여 Azure Synapse Analytics에서 지능형 다국어 솔루션을 빌드하는 방법을 알아봅니다.

이 자습서에서는 다음 기능을 수행하기 위해 [MMLSpark](https://github.com/Azure/mmlspark)와 함께 Translator를 사용하는 방법을 보여줍니다.

> [!div class="checklist"]
> - 텍스트 번역
> - 음역 텍스트
> - 언어 검색
> - 문장 나누기
> - 사전 조회
> - 사전 예제

Azure 구독이 없는 경우 [시작하기 전에 체험 계정을 만듭니다](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>사전 요구 사항

- [Azure Synapse Analytics 작업 영역](../get-started-create-workspace.md)(기본 스토리지로 구성된 Azure Data Lake Storage Gen2 스토리지 계정이 있음). 사용하는 Data Lake Storage Gen2 파일 시스템의 *Storage Blob 데이터 기여자* 여야 합니다.
- Azure Synapse Analytics 작업 영역의 Spark 풀. 자세한 내용은 [Azure Synapse에서 Spark 풀 만들기](../quickstart-create-sql-pool-studio.md)를 참조하세요.
- [Azure Synaps에서 Cognitive Services 구성](tutorial-configure-cognitive-services-synapse.md) 자습서에서 설명하는 사전 구성 단계

## <a name="get-started"></a>시작
Synapse Studio를 열고 새 Notebook을 만듭니다. 시작하려면 [MMLSpark](https://github.com/Azure/mmlspark)를 가져옵니다. 

```python
import mmlspark
from mmlspark.cognitive import *
from notebookutils import mssparkutils
from pyspark.sql.functions import col, flatten
```

## <a name="configure-translator"></a>Translator 구성

[사전 구성 단계](tutorial-configure-cognitive-services-synapse.md)에서 구성한 연결된 Translator를 사용합니다. 

```python
cognitive_service_name = "<Your linked service for translator>"
```

## <a name="translate-text"></a>텍스트 번역
Translator 서비스의 핵심 작업은 텍스트를 번역하는 것입니다. 

```python
df = spark.createDataFrame([
  (["Hello, what is your name?", "Bye"],)
], ["text",])

translate = (Translate()
    .setLinkedService(cognitive_service_name)
    .setTextCol("text")
    .setToLanguage(["zh-Hans", "fr"])
    .setOutputCol("translation")
    .setConcurrency(5))

display(translate
      .transform(df)
      .withColumn("translation", flatten(col("translation.translations")))
      .withColumn("translation", col("translation.text"))
      .select("translation"))
```

### <a name="expected-results"></a>예상 결과

```json
["你好，你叫什么名字？","Bonjour, quel est votre nom?","再见","Au revoir"]
```

## <a name="transliterate-text"></a>텍스트 음역

음역은 발음 유사성을 기반으로 하여 단어 또는 구를 한 언어의 스크립트(알파벳)에서 다른 언어로 변환하는 과정입니다.

```python
transliterateDf =  spark.createDataFrame([
  (["こんにちは", "さようなら"],)
], ["text",])

transliterate = (Transliterate()
    .setLinkedService(cognitive_service_name)
    .setLanguage("ja")
    .setFromScript("Jpan")
    .setToScript("Latn")
    .setTextCol("text")
    .setOutputCol("result"))

display(transliterate
    .transform(transliterateDf)
    .withColumn("text", col("result.text"))
    .withColumn("script", col("result.script"))
    .select("text", "script"))
```

### <a name="expected-results"></a>예상 결과

|text|스크립트|
|--|--|
|"["Kon'nichiwa","sayonara"]"|"["Latn","Latn"]"|

## <a name="detect-language"></a>언어 감지
번역이 필요하다는 것을 알고 있지만 Translator 서비스로 보낼 텍스트의 언어를 모르는 경우 언어 감지 작업을 사용할 수 있습니다. 

```python
detectDf =  spark.createDataFrame([
  (["Hello, what is your name?"],)
], ["text",])

detect = (Detect()
    .setLinkedService(cognitive_service_name)
    .setTextCol("text")
    .setOutputCol("result"))

display(detect
    .transform(detectDf)
    .withColumn("language", col("result.language"))
    .select("language"))
```

### <a name="expected-results"></a>예상 결과

```json
"["en"]"
```

## <a name="break-sentence"></a>문장 나누기

텍스트에서 문장 경계의 위치를 식별합니다.

```python
bsDf =  spark.createDataFrame([
  (["Hello, what is your name?"],)
], ["text",])

breakSentence = (BreakSentence()
    .setLinkedService(cognitive_service_name)
    .setTextCol("text")
    .setOutputCol("result"))

display(breakSentence
    .transform(bsDf)
    .withColumn("sentLen", flatten(col("result.sentLen")))
    .select("sentLen"))
```

### <a name="expected-results"></a>예상 결과

```json
"[25]"
```

## <a name="dictionary-lookup-alternate-translations"></a>사전 조회(대체 번역)
엔드포인트를 사용하면 단어 또는 구에 대한 대체 번역을 가져올 수 있습니다. 

```python
dictDf = spark.createDataFrame([
  (["fly"],)
], ["text",])

dictionaryLookup = (DictionaryLookup()
    .setLinkedService(cognitive_service_name)
    .setFromLanguage("en")
    .setToLanguage("es")
    .setTextCol("text")
    .setOutputCol("result"))

display(dictionaryLookup
    .transform(dictDf)
    .withColumn("translations", flatten(col("result.translations")))
    .withColumn("normalizedTarget", col("translations.normalizedTarget"))
    .select("normalizedTarget"))
```

### <a name="expected-results"></a>예상 결과

|normalizedTarget|
|----|
|"["volar","mosca","operan","pilotar","moscas","marcha"]"|

## <a name="dictionary-examples-translations-in-context"></a>사전 예제(컨텍스트의 번역)

사전 조회가 수행되면 원본 텍스트 및 번역을 dictionary/examples 엔드포인트에 전달하여 문장 또는 구의 컨텍스트에서 두 용어를 모두 표시하는 예제 목록을 가져올 수 있습니다.

```python
dictDf = spark.createDataFrame([
  ([("fly", "volar")],)
], ["textAndTranslation",])

dictionaryExamples = (DictionaryExamples()
    .setLinkedService(cognitive_service_name)
    .setFromLanguage("en")
    .setToLanguage("es")
    .setTextAndTranslationCol("textAndTranslation")
    .setOutputCol("result"))

display(dictionaryExamples
    .transform(dictDf)
    .withColumn("examples", flatten(col("result.examples")))
    .select("examples"))
```

### <a name="expected-results"></a>예상 결과

```json

[{"sourcePrefix":"I mean, for a guy who could ","sourceSuffix":".","targetPrefix":"Quiero decir, para un tipo que podía ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"Now it's time to make you ","sourceSuffix":".","targetPrefix":"Ahora es hora de que te haga ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"One happy thought will make you ","sourceSuffix":".","targetPrefix":"Uno solo te hará ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"They need machines to ","sourceSuffix":".","targetPrefix":"Necesitan máquinas para ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"That should really ","sourceSuffix":".","targetPrefix":"Eso realmente debe ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"It sure takes longer when you can't ","sourceSuffix":".","targetPrefix":"Lleva más tiempo cuando no puedes ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I have to ","sourceSuffix":" home in the morning.","targetPrefix":"Tengo que ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":" a casa por la mañana."},{"sourcePrefix":"You taught me to ","sourceSuffix":".","targetPrefix":"Me enseñaste a ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I think you should ","sourceSuffix":" with the window closed.","targetPrefix":"Creo que debemos ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":" con la ventana cerrada."},{"sourcePrefix":"They look like they could ","sourceSuffix":".","targetPrefix":"Parece que pudieran ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"But you can ","sourceSuffix":", for instance?","targetPrefix":"Que puedes ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":", por ejemplo."},{"sourcePrefix":"At least until her kids can be able to ","sourceSuffix":".","targetPrefix":"Al menos hasta que sus hijos sean capaces de ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I thought you could ","sourceSuffix":".","targetPrefix":"Pensé que podías ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I was wondering what it would be like to ","sourceSuffix":".","targetPrefix":"Me preguntaba cómo sería ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"But nobody else can ","sourceSuffix":".","targetPrefix":"Pero nadie puede ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."}]
```
## <a name="clean-up-resources"></a>리소스 정리
Spark 인스턴스가 종료되도록 하려면 연결된 세션(Notebook)을 종료합니다. Apache Spark 풀에 지정된 **유휴 시간** 에 도달하면 풀이 종료됩니다. Notebook 오른쪽 상단에 있는 상태 표시줄에서 **세션 중지** 를 선택할 수도 있습니다.

![screenshot-showing-stop-session](./media/tutorial-build-applications-use-mmlspark/stop-session.png)

## <a name="next-steps"></a>다음 단계

* [Synapse 샘플 Notebooks 체크 아웃](https://github.com/Azure-Samples/Synapse/tree/main/MachineLearning) 
* [MMLSpark GitHub 리포지토리](https://github.com/Azure/mmlspark)
