---
title: '빠른 시작: Java용 Text Analytics v3 클라이언트 라이브러리 | Microsoft Docs'
description: Java용 v3 Text Analytics 클라이언트 라이브러리를 시작합니다.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 08/17/2021
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: 3284cef03ce899a096816ba23736d29fdc228eb5
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864954"
---
<a name="HOLTop"></a>

# <a name="version-32-preview1"></a>[버전 3.2-preview.1](#tab/version-3-2)

[v3.2-preview.1 참조 문서](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-preview) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics) | [v3.2-preview 패키지](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.2.0-beta.1) | [샘플](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/textanalytics/azure-ai-textanalytics/src/samples)

# <a name="version-31"></a>[버전 3.1](#tab/version-3-1)

[참조 설명서](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-stable) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics) | [v3.1 패키지](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0) | [샘플](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

---

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) 버전 8 이상
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics 리소스를 만들어"  target="_blank">Text Analytics 리소스를 만들어</a> 키와 엔드포인트를 가져옵니다.  배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 Text Analytics API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* Analyze(분석) 기능을 사용하려면 표준 가격 책정 계층을 사용하는 Text Analytics 리소스가 필요합니다.

## <a name="setting-up"></a>설치

### <a name="add-the-client-library"></a>클라이언트 라이브러리 추가

# <a name="version-32-preview"></a>[버전 3.2-preview](#tab/version-3-2)

선호하는 IDE 또는 개발 환경에서 Maven 프로젝트를 만듭니다. 그런 다음, 프로젝트의 *pom.xml* 파일에 다음 종속성을 추가합니다. 온라인에서 [다른 빌드 도구용](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.2.0-beta.1) 구현 구문을 찾을 수 있습니다.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.2.0-beta.1</version>
    </dependency>
</dependencies>
```

이 버전의 Text Analytics API에 포함된 기능:

* 감정 분석
* 오피니언 마이닝
* 언어 검색
* 엔터티 인식
* 엔터티 연결
* 개인 식별 정보 인식
* 핵심 문구 추출
* 비동기 메서드
* 의료 분야 Text Analytics
* 텍스트 요약

# <a name="version-31"></a>[버전 3.1](#tab/version-3-1)

선호하는 IDE 또는 개발 환경에서 Maven 프로젝트를 만듭니다. 그런 다음, 프로젝트의 *pom.xml* 파일에 다음 종속성을 추가합니다. 온라인에서 [다른 빌드 도구용](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0) 구현 구문을 찾을 수 있습니다.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0</version>
    </dependency>
</dependencies>
```

이 버전의 Text Analytics API에 포함된 기능:

* 감정 분석
* 오피니언 마이닝
* 언어 검색
* 엔터티 인식
* 엔터티 연결
* 개인 식별 정보 인식
* 핵심 문구 추출
* 비동기 메서드
* 의료 분야 Text Analytics

---

`TextAnalyticsSamples.java`라는 Java 파일을 만듭니다. 파일을 열고, 다음 `import` 문을 추가합니다.

```java
import com.azure.ai.textanalytics.TextAnalyticsAsyncClient;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;

import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;

import java.util.Arrays;
import com.azure.core.util.Context;
import com.azure.core.util.polling.SyncPoller;

import com.azure.core.util.polling.LongRunningOperationStatus;
import com.azure.ai.textanalytics.util.*;
import com.azure.core.http.rest.PagedResponse;
```

java 파일에서 아래와 같이 새 클래스를 추가하고 Azure 리소스의 키와 엔드포인트를 추가합니다.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

다음 main 메서드를 클래스에 추가합니다. 나중에 여기서 호출되는 메서드를 정의합니다.

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);
    sentimentAnalysisExample(client);
    sentimentAnalysisWithOpinionMiningExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizePiiEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
    analyzeOperationExample(client);
}
```


## <a name="object-model"></a>개체 모델

Text Analytics 클라이언트는 키를 사용하여 Azure를 인증하고 텍스트를 단일 문자열 또는 일괄 처리로 허용하는 함수를 제공하는 `TextAnalyticsClient` 개체입니다. 텍스트는 API에 동기식 또는 비동기식으로 보낼 수 있습니다. 응답 개체에는 보내는 각 문서에 대한 분석 정보가 포함됩니다. 

[!INCLUDE [text-analytics-character-limits](../character-limits.md)]

## <a name="authenticate-the-client"></a>클라이언트 인증

Text Analytics 리소스의 키 및 엔드포인트를 사용하여 `TextAnalyticsClient` 개체를 인스턴스화하는 메서드를 만듭니다. 이 예제는 API의 버전 3.0 및 3.1에 대해 동일합니다.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```


프로그램의 `main()` 메서드에서 인증 메서드를 호출하여 클라이언트를 인스턴스화합니다.

## <a name="sentiment-analysis"></a>정서 분석

앞에서 만든 클라이언트를 사용하고 해당 `analyzeSentiment()` 함수를 호출하는 `sentimentAnalysisExample()`이라는 새 함수를 만듭니다. 반환된 `AnalyzeSentimentResult` 개체에는 성공하면 `documentSentiment` 및 `sentenceSentiments`가 포함되고 그렇지 않으면 `errorMessage`가 포함됩니다. 

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>출력

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

## <a name="opinion-mining"></a>의견 마이닝

오피니언 마이닝을 사용하여 감정 분석을 수행하려면 이전에 만든 클라이언트를 사용하는 `sentimentAnalysisWithOpinionMiningExample()`이라는 새 함수를 만들고, 옵션 개체 `AnalyzeSentimentOptions`를 설정하여 해당 `analyzeSentiment()` 함수를 호출합니다. 반환된 `AnalyzeSentimentResult` 개체에는 성공하면 `documentSentiment` 및 `sentenceSentiments`가 포함되고 그렇지 않으면 `errorMessage`가 포함됩니다. 

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]


```java
static void sentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    // The document that needs be analyzed.
    String document = "Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.";

    System.out.printf("Document = %s%n", document);

    AnalyzeSentimentOptions options = new AnalyzeSentimentOptions().setIncludeOpinionMining(true);
    final DocumentSentiment documentSentiment = client.analyzeSentiment(document, "en", options);
    SentimentConfidenceScores scores = documentSentiment.getConfidenceScores();
    System.out.printf(
            "Recognized document sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
            documentSentiment.getSentiment(), scores.getPositive(), scores.getNeutral(), scores.getNegative());


    documentSentiment.getSentences().forEach(sentenceSentiment -> {
        SentimentConfidenceScores sentenceScores = sentenceSentiment.getConfidenceScores();
        System.out.printf("\tSentence sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
                sentenceSentiment.getSentiment(), sentenceScores.getPositive(), sentenceScores.getNeutral(), sentenceScores.getNegative());
        sentenceSentiment.getOpinions().forEach(opinion -> {
            TargetSentiment targetSentiment = opinion.getTarget();
            System.out.printf("\t\tTarget sentiment: %s, target text: %s%n", targetSentiment.getSentiment(),
                    targetSentiment.getText());
            for (AssessmentSentiment assessmentSentiment : opinion.getAssessments()) {
                System.out.printf("\t\t\t'%s' assessment sentiment because of \"%s\". Is the assessment negated: %s.%n",
                        assessmentSentiment.getSentiment(), assessmentSentiment.getText(), assessmentSentiment.isNegated());
            }
        });
    });
}
```

### <a name="output"></a>출력

```console
Document = Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.
Recognized document sentiment: negative, positive score: 0.010000, neutral score: 0.140000, negative score: 0.850000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: atmosphere
            'negative' assessment sentiment because of "bad". Is the assessment negated: false.
    Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Target sentiment: negative, target text: Staff
            'negative' assessment sentiment because of "friendly". Is the assessment negated: true.
            'negative' assessment sentiment because of "helpful". Is the assessment negated: true.
```

## <a name="language-detection"></a>언어 검색

앞에서 만든 클라이언트를 사용하고 해당 `detectLanguage()` 함수를 호출하는 `detectLanguageExample()`이라는 새 함수를 만듭니다. 반환된 `DetectLanguageResult` 개체에는 검색된 기본 언어, 성공하는 경우 검색된 다른 언어 목록 또는 그렇지 않은 경우 `errorMessage`가 포함됩니다. 이 예제는 API의 버전 3.0 및 3.1에 대해 동일합니다.

> [!Tip]
> 일부 경우에는 입력에 따라 언어를 명확하게 구분하는 것이 어려울 수 있습니다. 2자로 된 국가 코드는 `countryHint` 매개 변수를 사용하여 지정할 수 있습니다. 기본적으로 API는 "US"를 기본 countryHint로 사용합니다. 이 동작을 제거하려면 이 값을 빈 문자열의 `countryHint = ""`로 설정하여 이 매개 변수를 다시 설정하면 됩니다. 다른 기본값을 설정하려면 `TextAnalyticsClientOptions.DefaultCountryHint` 속성을 설정하고 클라이언트를 초기화 중에 이를 전달합니다.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>출력

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```

## <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)

앞에서 만든 클라이언트를 사용하고 해당 `recognizeEntities()` 함수를 호출하는 `recognizeEntitiesExample()`이라는 새 함수를 만듭니다. 반환된 `CategorizedEntityCollection` 개체에는 성공하는 경우 `CategorizedEntity` 목록이 포함되고, 그렇지 않은 경우 `errorMessage`가 포함됩니다.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s, offset: %s, length: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore(),
            entity.getOffset(),
            entity.getLength());
    }
}
```

### <a name="output"></a>출력

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61, offset: 8, length: 4.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82, offset: 16, length: 7.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8, offset: 24, length: 9.
```


## <a name="personally-identifiable-information-pii-recognition"></a>PII(개인 식별 정보) 인식

앞에서 만든 클라이언트를 사용하고 해당 `recognizePiiEntities()` 함수를 호출하는 `recognizePiiEntitiesExample()`이라는 새 함수를 만듭니다. 반환된 `PiiEntityCollection` 개체에는 성공하는 경우 `PiiEntity` 목록이 포함되고, 그렇지 않은 경우 `errorMessage`가 포함됩니다. 또한 `*****`로 대체되는 식별 가능한 모든 엔터티가 포함된 입력 텍스트로 구성된 교정된 텍스트가 포함됩니다.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```java
static void recognizePiiEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String document = "My SSN is 859-98-0987";
    PiiEntityCollection piiEntityCollection = client.recognizePiiEntities(document);
    System.out.printf("Redacted Text: %s%n", piiEntityCollection.getRedactedText());
    piiEntityCollection.forEach(entity -> System.out.printf(
        "Recognized Personally Identifiable Information entity: %s, entity category: %s, entity subcategory: %s,"
            + " confidence score: %f.%n",
        entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
}
```

### <a name="output"></a>출력

```console
Redacted Text: My SSN is ***********
Recognized Personally Identifiable Information entity: 859-98-0987, entity category: U.S. Social Security Number (SSN), entity subcategory: null, confidence score: 0.650000.
```

## <a name="entity-linking"></a>엔터티 연결

앞에서 만든 클라이언트를 사용하고 해당 `recognizeLinkedEntities()` 함수를 호출하는 `recognizeLinkedEntitiesExample()`이라는 새 함수를 만듭니다. 반환된 `LinkedEntityCollection` 개체에는 성공하는 경우 `LinkedEntity` 목록이 포함되고, 그렇지 않은 경우 `errorMessage`가 포함됩니다. 연결된 엔터티가 고유하게 식별되므로 동일한 엔터티의 발생은 `LinkedEntity` 개체 아래에서 `LinkedEntityMatch` 개체 목록으로 그룹화됩니다.


```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f, Offset: %s, Length: %s%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore(),
            linkedEntityMatch.getOffset(),
            linkedEntityMatch.getLength());
        }
    }
}
```

### <a name="output"></a>출력

```console
Linked Entities:
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55, Offset: 9, Length: 0
Text: Microsoft, Score: 0.55, Offset: 9, Length: 150
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63, Offset: 10, Length: 25
Text: Gates, Score: 0.63, Offset: 5, Length: 161
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60, Offset: 10, Length: 40
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32, Offset: 7, Length: 54
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33, Offset: 5, Length: 89
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88, Offset: 11, Length: 116
```

## <a name="key-phrase-extraction"></a>핵심 문구 추출

앞에서 만든 클라이언트를 사용하고 해당 `extractKeyPhrases()` 함수를 호출하는 `extractKeyPhrasesExample()`이라는 새 함수를 만듭니다. 반환된 `ExtractKeyPhraseResult` 개체에는 성공하는 경우 핵심 구 목록이 포함되고, 그렇지 않은 경우 `errorMessage`가 포함됩니다. 이 예제는 API의 버전 3.0 및 3.1에 대해 동일합니다.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>출력

```console
Recognized phrases: 
cat
veterinarian
```
---

## <a name="extract-health-entities"></a>상태 엔터티 추출

[!INCLUDE [health operation pricing](../health-operation-pricing-caution.md)]

Text Analytics를 사용해 비동기 요청을 수행하여 텍스트에서 의료 엔터티를 추출할 수 있습니다. 아래 샘플은 기본적인 예를 보여줍니다. [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeHealthcareEntities.java)에서 더 많은 고급 샘플을 찾을 수 있습니다.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]


```java
static void healthExample(TextAnalyticsClient client){
    List<TextDocumentInput> documents = Arrays.asList(
            new TextDocumentInput("0",
                    "Prescribed 100mg ibuprofen, taken twice daily."));

    AnalyzeHealthcareEntitiesOptions options = new AnalyzeHealthcareEntitiesOptions().setIncludeStatistics(true);

    SyncPoller<AnalyzeHealthcareEntitiesOperationDetail, AnalyzeHealthcareEntitiesPagedIterable>
            syncPoller = client.beginAnalyzeHealthcareEntities(documents, options, Context.NONE);

    System.out.printf("Poller status: %s.%n", syncPoller.poll().getStatus());
    syncPoller.waitForCompletion();

    // Task operation statistics
    AnalyzeHealthcareEntitiesOperationDetail operationResult = syncPoller.poll().getValue();
    System.out.printf("Operation created time: %s, expiration time: %s.%n",
            operationResult.getCreatedAt(), operationResult.getExpiresAt());
    System.out.printf("Poller status: %s.%n", syncPoller.poll().getStatus());

    for (AnalyzeHealthcareEntitiesResultCollection resultCollection : syncPoller.getFinalResult()) {
        // Model version
        System.out.printf(
                "Results of Azure Text Analytics \"Analyze Healthcare Entities\" Model, version: %s%n",
                resultCollection.getModelVersion());

        for (AnalyzeHealthcareEntitiesResult healthcareEntitiesResult : resultCollection) {
            System.out.println("Document ID = " + healthcareEntitiesResult.getId());
            System.out.println("Document entities: ");
            // Recognized healthcare entities
            for (HealthcareEntity entity : healthcareEntitiesResult.getEntities()) {
                System.out.printf(
                        "\tText: %s, normalized name: %s, category: %s, subcategory: %s, confidence score: %f.%n",
                        entity.getText(), entity.getNormalizedText(), entity.getCategory(),
                        entity.getSubcategory(), entity.getConfidenceScore());
            }
            // Recognized healthcare entity relation groups
            for (HealthcareEntityRelation entityRelation : healthcareEntitiesResult.getEntityRelations()) {
                System.out.printf("Relation type: %s.%n", entityRelation.getRelationType());
                for (HealthcareEntityRelationRole role : entityRelation.getRoles()) {
                    HealthcareEntity entity = role.getEntity();
                    System.out.printf("\tEntity text: %s, category: %s, role: %s.%n",
                            entity.getText(), entity.getCategory(), role.getName());
                }
            }
        }
    }
}
```

`main()` 메서드에서 위의 코드 예를 호출합니다. 

```java
public static void main(String[] args) {
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);
    healthExample(client);
}
```

### <a name="output"></a>output

```console
Poller status: IN_PROGRESS.
Operation created time: 2021-07-20T19:45:50Z, expiration time: 2021-07-21T19:45:50Z.
Poller status: SUCCESSFULLY_COMPLETED.
Results of Azure Text Analytics "Analyze Healthcare Entities" Model, version: 2021-05-15
Document ID = 0
Document entities: 
    Text: 100mg, normalized name: null, category: Dosage, subcategory: null, confidence score: 1.000000.
    Text: ibuprofen, normalized name: ibuprofen, category: MedicationName, subcategory: null, confidence score: 1.000000.
    Text: twice daily, normalized name: null, category: Frequency, subcategory: null, confidence score: 1.000000.
Relation type: DosageOfMedication.
    Entity text: 100mg, category: Dosage, role: Dosage.
    Entity text: ibuprofen, category: MedicationName, role: Medication.
Relation type: FrequencyOfMedication.
    Entity text: ibuprofen, category: MedicationName, role: Medication.
    Entity text: twice daily, category: Frequency, role: Frequency.
```

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Analyze(분석) 작업을 통해 비동기적으로 API 사용

분석 작업을 사용하여 NER, 핵심 구 추출, 감정 분석 및 PII 검색에 대한 비동기 일괄 처리 요청을 수행할 수 있습니다. 아래 샘플에서는 하나의 작업에 대한 기본 예제를 보여줍니다. [GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample_AnalyzeActions.md)에서 더 많은 고급 샘플을 찾을 수 있습니다.

[!INCLUDE [Analyze Batch Action pricing](../analyze-operation-pricing-caution.md)]

`beginAnalyzeBatchActions()` 함수를 호출하는 `analyzeBatchActionsExample()`이라는 새 함수를 만듭니다. 그러면 장기 실행 작업이 실행되고 결과가 폴링됩니다.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```java
static void analyzeActionsExample(TextAnalyticsClient client){
    List<TextDocumentInput> documents = new ArrayList<>();
    documents.add(new TextDocumentInput("0", "Microsoft was founded by Bill Gates and Paul Allen."));


    SyncPoller<AnalyzeActionsOperationDetail, AnalyzeActionsResultPagedIterable> syncPoller =
            client.beginAnalyzeActions(documents,
                    new TextAnalyticsActions().setDisplayName("Example analyze task")
                            .setRecognizeEntitiesActions(new RecognizeEntitiesAction())
                            .setExtractKeyPhrasesActions(
                                    new ExtractKeyPhrasesAction().setModelVersion("latest")),
                    new AnalyzeActionsOptions().setIncludeStatistics(false),
                    Context.NONE);

    // Task operation statistics details
    while (syncPoller.poll().getStatus() == LongRunningOperationStatus.IN_PROGRESS) {
        final AnalyzeActionsOperationDetail operationDetail = syncPoller.poll().getValue();
        System.out.printf("Action display name: %s, Successfully completed actions: %d, in-process actions: %d,"
                        + " failed actions: %d, total actions: %d%n",
                operationDetail.getDisplayName(), operationDetail.getSucceededCount(),
                operationDetail.getInProgressCount(), operationDetail.getFailedCount(),
                operationDetail.getTotalCount());
    }

    syncPoller.waitForCompletion();

    Iterable<PagedResponse<AnalyzeActionsResult>> pagedResults = syncPoller.getFinalResult().iterableByPage();
    for (PagedResponse<AnalyzeActionsResult> perPage : pagedResults) {
        System.out.printf("Response code: %d, Continuation Token: %s.%n", perPage.getStatusCode(),
                perPage.getContinuationToken());
        for (AnalyzeActionsResult actionsResult : perPage.getElements()) {
            System.out.println("Entities recognition action results:");
            for (RecognizeEntitiesActionResult actionResult : actionsResult.getRecognizeEntitiesResults()) {
                if (!actionResult.isError()) {
                    for (RecognizeEntitiesResult documentResult : actionResult.getDocumentsResults()) {
                        if (!documentResult.isError()) {
                            for (CategorizedEntity entity : documentResult.getEntities()) {
                                System.out.printf(
                                        "\tText: %s, category: %s, confidence score: %f.%n",
                                        entity.getText(), entity.getCategory(), entity.getConfidenceScore());
                            }
                        } else {
                            System.out.printf("\tCannot recognize entities. Error: %s%n",
                                    documentResult.getError().getMessage());
                        }
                    }
                } else {
                    System.out.printf("\tCannot execute Entities Recognition action. Error: %s%n",
                            actionResult.getError().getMessage());
                }
            }

            System.out.println("Key phrases extraction action results:");
            for (ExtractKeyPhrasesActionResult actionResult : actionsResult.getExtractKeyPhrasesResults()) {
                if (!actionResult.isError()) {
                    for (ExtractKeyPhraseResult documentResult : actionResult.getDocumentsResults()) {
                        if (!documentResult.isError()) {
                            System.out.println("\tExtracted phrases:");
                            for (String keyPhrases : documentResult.getKeyPhrases()) {
                                System.out.printf("\t\t%s.%n", keyPhrases);
                            }
                        } else {
                            System.out.printf("\tCannot extract key phrases. Error: %s%n",
                                    documentResult.getError().getMessage());
                        }
                    }
                } else {
                    System.out.printf("\tCannot execute Key Phrases Extraction action. Error: %s%n",
                            actionResult.getError().getMessage());
                }
            }
        }
    }
}
```

애플리케이션에 이 예제를 추가한 후 `main()` 메서드에서 호출합니다.

```java
analyzeBatchActionsExample(client);
```

### <a name="output"></a>출력

```console
Action display name: Example analyze task, Successfully completed actions: 1, in-process actions: 1, failed actions: 0, total actions: 2
Response code: 200, Continuation Token: null.
Entities recognition action results:
    Text: Microsoft, category: Organization, confidence score: 1.000000.
    Text: Bill Gates, category: Person, confidence score: 1.000000.
    Text: Paul Allen, category: Person, confidence score: 1.000000.
Key phrases extraction action results:
    Extracted phrases:
        Bill Gates.
        Paul Allen.
        Microsoft.
```

분석 작업을 사용하여 NER, 핵심 구 추출, 감정 분석 및 PII 검색을 수행할 수도 있습니다. GitHub의 [Analyze(분석) 샘플](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/AnalyzeActionsAsync.java)을 참조하세요.

## <a name="text-summarization"></a>텍스트 요약

# <a name="version-32-preview1"></a>[버전 3.2-preview.1](#tab/version-3-2)

Text Analytics를 사용하여 많은 양의 텍스트를 요약할 수 있습니다. 이 메서드는 비동기식이며 장기 실행 작업이 완료되면 맨 위 문장을 반환합니다.

```java
static void summarizationExample(TextAnalyticsClient client) {
    List<String> documents = new ArrayList<>();
    documents.add(
            "The extractive summarization feature in Text Analytics uses natural language processing techniques "
            + "to locate key sentences in an unstructured text document. "
            + "These sentences collectively convey the main idea of the document. This feature is provided as an API for developers. "
            + "They can use it to build intelligent solutions based on the relevant information extracted to support various use cases. "
            + "In the public preview, extractive summarization supports several languages. "
            + "It is based on pretrained multilingual transformer models, part of our quest for holistic representations. "
            + "It draws its strength from transfer learning across monolingual and harness the shared nature of languages "
            + "to produce models of improved quality and efficiency.");

    SyncPoller<AnalyzeActionsOperationDetail, AnalyzeActionsResultPagedIterable> syncPoller =
            client.beginAnalyzeActions(documents,
                    new TextAnalyticsActions().setDisplayName("{tasks_display_name}")
                            .setExtractSummaryActions(
                                    new ExtractSummaryAction()),
                    "en",
                    new AnalyzeActionsOptions());

    syncPoller.waitForCompletion();

    syncPoller.getFinalResult().forEach(actionsResult -> {
        System.out.println("Extractive Summarization action results:");
        for (ExtractSummaryActionResult actionResult : actionsResult.getExtractSummaryResults()) {
            if (!actionResult.isError()) {
                for (ExtractSummaryResult documentResult : actionResult.getDocumentsResults()) {
                    if (!documentResult.isError()) {
                        System.out.println("\tExtracted summary sentences:");
                        for (SummarySentence summarySentence : documentResult.getSentences()) {
                            System.out.printf(
                                    "\t\t Sentence text: %s, length: %d, offset: %d, rank score: %f.%n",
                                    summarySentence.getText(), summarySentence.getLength(),
                                    summarySentence.getOffset(), summarySentence.getRankScore());
                        }
                    } else {
                        System.out.printf("\tCannot extract summary sentences. Error: %s%n",
                                documentResult.getError().getMessage());
                    }
                }
            } else {
                System.out.printf("\tCannot execute Extractive Summarization action. Error: %s%n",
                        actionResult.getError().getMessage());
            }
        }
    });
}
```

애플리케이션에 이 예제를 추가한 후 `main()` 메서드에서 호출합니다.

```java
summarizationExample(client);
```

# <a name="version-31"></a>[버전 3.1](#tab/version-3-1)

이 기능은 버전 3.1에서 사용할 수 없습니다.

---
