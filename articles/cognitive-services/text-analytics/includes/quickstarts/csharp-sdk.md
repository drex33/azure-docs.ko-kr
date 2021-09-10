---
title: '빠른 시작: C#용 Text Analytics 클라이언트 라이브러리 | Microsoft Docs'
description: C#용 Text Analytics 클라이언트 라이브러리 시작
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 08/17/2021
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 2b4a3a8049c7d6752c581200277a21d794a248c9
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864885"
---
<a name="HOLTop"></a>

# <a name="version-32-preview1"></a>[버전 3.2-preview.1](#tab/version-3-2)

[v3.2-preview 참조 문서](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet-preview) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) | [v3.2-preview 패키지(NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.2.0-beta.1) | [v3.2-preview 샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-31"></a>[버전 3.1](#tab/version-3-1)

[v3.1 참조 설명서](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) | [v3.1 패키지(NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0) | [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

---

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics 리소스를 만들어"  target="_blank">Text Analytics 리소스를 만들어</a> 키와 엔드포인트를 가져옵니다.  배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 Text Analytics API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* Analyze(분석) 기능을 사용하려면 표준 가격 책정 계층을 사용하는 Text Analytics 리소스가 필요합니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-net-core-application"></a>새 .NET Core 애플리케이션 만들기

Visual Studio IDE를 사용하여 새 .NET Core 콘솔 앱을 만듭니다. 이렇게 하면 *program.cs* 라는 단일 C# 원본 파일이 포함된 "Hello World" 프로젝트가 생성됩니다.

# <a name="version-32-preview1"></a>[버전 3.2-preview.1](#tab/version-3-2)

**솔루션 탐색기** 에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택하여 클라이언트 라이브러리를 설치합니다. 열리는 패키지 관리자에서 **찾아보기** 를 선택하고 `Azure.AI.TextAnalytics`를 검색합니다. **시험판 포함** 이 선택되어 있는지 확인합니다. `5.2.0-beta.1` 버전, **설치** 를 차례로 선택합니다. [패키지 관리자 콘솔](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)을 사용할 수도 있습니다.

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

**솔루션 탐색기** 에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택하여 클라이언트 라이브러리를 설치합니다. 열리는 패키지 관리자에서 **찾아보기** 를 선택하고 `Azure.AI.TextAnalytics`를 검색합니다. `5.1.0` 버전, **설치** 를 차례로 선택합니다. [패키지 관리자 콘솔](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)을 사용할 수도 있습니다.

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

*program.cs* 파일을 열고 다음 `using` 지시문을 추가합니다.

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

애플리케이션의 `Program` 클래스에서 리소스의 키 및 엔드포인트에 대한 변수를 만듭니다.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

애플리케이션의 `Main` 메서드를 바꿉니다. 나중에 여기서 호출되는 메서드를 정의합니다. 

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    SentimentAnalysisWithOpinionMiningExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    RecognizePIIExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

## <a name="object-model"></a>개체 모델

Text Analytics 클라이언트는 키를 사용하여 Azure를 인증하고 텍스트를 단일 문자열 또는 일괄 처리로 허용하는 함수를 제공하는 `TextAnalyticsClient` 개체입니다. 텍스트는 API에 동기식 또는 비동기식으로 보낼 수 있습니다. 응답 개체에는 보내는 각 문서에 대한 분석 정보가 포함됩니다. 

서비스 버전 `3.x`를 사용하는 경우 선택적 `TextAnalyticsClientOptions` 인스턴스를 사용하여 다양한 기본 설정(예: 기본 언어 또는 국가/지역 힌트)으로 클라이언트를 초기화할 수 있습니다. Azure Active Directory 토큰을 사용하여 인증할 수도 있습니다. 

[!INCLUDE [text-analytics-character-limits](../character-limits.md)]

## <a name="authenticate-the-client"></a>클라이언트 인증

이전의 main 메서드에서 엔드포인트와 자격 증명을 사용하여 새 클라이언트 개체를 만들었는지 확인합니다.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```


## <a name="sentiment-analysis"></a>정서 분석

앞에서 만든 클라이언트를 사용하고 해당 `AnalyzeSentiment()` 함수를 호출하는 `SentimentAnalysisExample()`이라는 새 함수를 만듭니다. 반환된 `Response<DocumentSentiment>` 개체에는 전체 입력 문서의 감정 레이블과 점수가 포함되며, 성공하는 경우 각 문장에 대한 감정 분석도 포함됩니다. 오류가 발생하면 `RequestFailedException`이 throw됩니다.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>출력

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

## <a name="opinion-mining"></a>의견 마이닝

이전에 만든 클라이언트를 사용하는 `SentimentAnalysisWithOpinionMiningExample()`이라는 새 함수를 만들고, `AnalyzeSentimentOptions` 모음에서 `IncludeOpinionMining` 옵션을 사용하여 해당 `AnalyzeSentimentBatch()` 함수를 호출합니다. 반환된 `AnalyzeSentimentResultCollection` 개체에는 `Response<DocumentSentiment>`를 나타내는 `AnalyzeSentimentResult`의 컬렉션이 포함됩니다. `SentimentAnalysis()`와 `SentimentAnalysisWithOpinionMiningExample()` 간의 차이점은 후자는 각 문장에 `SentenceOpinion`을 포함하여 분석된 대상과 관련된 평가를 표시한다는 것입니다. 오류가 발생하면 `RequestFailedException`이 throw됩니다.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static void SentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    var documents = new List<string>
    {
        "The food and service were unacceptable, but the concierge were nice."
    };

    AnalyzeSentimentResultCollection reviews = client.AnalyzeSentimentBatch(documents, options: new AnalyzeSentimentOptions()
    {
        IncludeOpinionMining = true
    });

    foreach (AnalyzeSentimentResult review in reviews)
    {
        Console.WriteLine($"Document sentiment: {review.DocumentSentiment.Sentiment}\n");
        Console.WriteLine($"\tPositive score: {review.DocumentSentiment.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {review.DocumentSentiment.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {review.DocumentSentiment.ConfidenceScores.Neutral:0.00}\n");
        foreach (SentenceSentiment sentence in review.DocumentSentiment.Sentences)
        {
            Console.WriteLine($"\tText: \"{sentence.Text}\"");
            Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
            Console.WriteLine($"\tSentence positive score: {sentence.ConfidenceScores.Positive:0.00}");
            Console.WriteLine($"\tSentence negative score: {sentence.ConfidenceScores.Negative:0.00}");
            Console.WriteLine($"\tSentence neutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");

            foreach (SentenceOpinion sentenceOpinion in sentence.Opinions)
            {
                Console.WriteLine($"\tTarget: {sentenceOpinion.Target.Text}, Value: {sentenceOpinion.Target.Sentiment}");
                Console.WriteLine($"\tTarget positive score: {sentenceOpinion.Target.ConfidenceScores.Positive:0.00}");
                Console.WriteLine($"\tTarget negative score: {sentenceOpinion.Target.ConfidenceScores.Negative:0.00}");
                foreach (AssessmentSentiment assessment in sentenceOpinion.Assessments)
                {
                    Console.WriteLine($"\t\tRelated Assessment: {assessment.Text}, Value: {assessment.Sentiment}");
                    Console.WriteLine($"\t\tRelated Assessment positive score: {assessment.ConfidenceScores.Positive:0.00}");
                    Console.WriteLine($"\t\tRelated Assessment negative score: {assessment.ConfidenceScores.Negative:0.00}");
                }
            }
        }
        Console.WriteLine($"\n");
    }
}
```

### <a name="output"></a>출력

```console
Document sentiment: Positive

        Positive score: 0.84
        Negative score: 0.16
        Neutral score: 0.00

        Text: "The food and service were unacceptable, but the concierge were nice."
        Sentence sentiment: Positive
        Sentence positive score: 0.84
        Sentence negative score: 0.16
        Sentence neutral score: 0.00

        Target: food, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: service, Value: Negative
        Target positive score: 0.01
        Target negative score: 0.99
                Related Assessment: unacceptable, Value: Negative
                Related Assessment positive score: 0.01
                Related Assessment negative score: 0.99
        Target: concierge, Value: Positive
        Target positive score: 1.00
        Target negative score: 0.00
                Related Assessment: nice, Value: Positive
                Related Assessment positive score: 1.00
                Related Assessment negative score: 0.00

Press any key to exit.
```

## <a name="language-detection"></a>언어 검색

앞에서 만든 클라이언트를 사용하고 해당 `DetectLanguage()` 함수를 호출하는 `LanguageDetectionExample()`이라는 새 함수를 만듭니다. 반환된 `Response<DetectedLanguage>` 개체에는 해당 이름 및 ISO-6391 코드와 함께 검색된 언어가 포함됩니다. 오류가 발생하면 `RequestFailedException`이 throw됩니다.

> [!Tip]
> 일부 경우에는 입력에 따라 언어를 명확하게 구분하는 것이 어려울 수 있습니다. 2자로 된 국가/지역 코드는 `countryHint` 매개 변수를 사용하여 지정할 수 있습니다. 기본적으로 API는 "US"를 기본 countryHint로 사용합니다. 이 동작을 제거하려면 이 값을 빈 문자열의 `countryHint = ""`로 설정하여 이 매개 변수를 다시 설정하면 됩니다. 다른 기본값을 설정하려면 `TextAnalyticsClientOptions.DefaultCountryHint` 속성을 설정하고 클라이언트를 초기화 중에 이를 전달합니다.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>출력

```console
Language:
        French, ISO-6391: fr
```

## <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)

앞에서 만든 클라이언트를 사용하는 `EntityRecognitionExample()`이라는 새 함수를 만들고, `RecognizeEntities()` 함수를 호출하여 결과를 반복합니다. 반환된 `Response<CategorizedEntityCollection>` 개체에는 검색된 엔터티 `CategorizedEntity`의 컬렉션이 포함됩니다. 오류가 발생하면 `RequestFailedException`이 throw됩니다.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
    }
}
```

### <a name="output"></a>출력

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

## <a name="personally-identifiable-information-pii-recognition"></a>PII(개인 식별 정보) 인식

앞에서 만든 클라이언트를 사용하는 `RecognizePIIExample()`이라는 새 함수를 만들고, `RecognizePiiEntities()` 함수를 호출하여 결과를 반복합니다. 반환된 `PiiEntityCollection`은 검색된 PII 엔터티의 목록을 나타냅니다. 오류가 발생하면 `RequestFailedException`이 throw됩니다.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static void RecognizePIIExample(TextAnalyticsClient client)
{
    string document = "A developer with SSN 859-98-0987 whose phone number is 800-102-1100 is building tools with our APIs.";

    PiiEntityCollection entities = client.RecognizePiiEntities(document).Value;

    Console.WriteLine($"Redacted Text: {entities.RedactedText}");
    if (entities.Count > 0)
    {
        Console.WriteLine($"Recognized {entities.Count} PII entit{(entities.Count > 1 ? "ies" : "y")}:");
        foreach (PiiEntity entity in entities)
        {
            Console.WriteLine($"Text: {entity.Text}, Category: {entity.Category}, SubCategory: {entity.SubCategory}, Confidence score: {entity.ConfidenceScore}");
        }
    }
    else
    {
        Console.WriteLine("No entities were found.");
    }
}
```

### <a name="output"></a>출력

```console
Redacted Text: A developer with SSN *********** whose phone number is ************ is building tools with our APIs.
Recognized 2 PII entities:
Text: 859-98-0987, Category: U.S. Social Security Number (SSN), SubCategory: , Confidence score: 0.65
Text: 800-102-1100, Category: Phone Number, SubCategory: , Confidence score: 0.8
```

## <a name="entity-linking"></a>엔터티 연결

앞에서 만든 클라이언트를 사용하는 `EntityLinkingExample()`이라는 새 함수를 만들고, `RecognizeLinkedEntities()` 함수를 호출하여 결과를 반복합니다. 반환된 `Response<LinkedEntityCollection>` 개체에는 검색된 엔터티 `LinkedEntity`의 컬렉션이 포함됩니다. 오류가 발생하면 `RequestFailedException`이 throw됩니다. 연결된 엔터티가 고유하게 식별되므로 동일한 엔터티의 발생은 `LinkedEntity` 개체 아래에서 `LinkedEntityMatch` 개체 목록으로 그룹화됩니다.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}");
            Console.WriteLine($"\t\tLength: {match.Length}");
            Console.WriteLine($"\t\tOffset: {match.Offset}\n");
        }
    }
}
```

### <a name="output"></a>출력

```console
Linked Entities:
        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 0

                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 150

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63
                Length: 10
                Offset: 25

                Text: Gates
                Score: 0.63
                Length: 5
                Offset: 161

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60
                Length: 10
                Offset: 40

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32
                Length: 7
                Offset: 54

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
                Length: 5
                Offset: 89

        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88
                Length: 11
                Offset: 116
```

## <a name="key-phrase-extraction"></a>핵심 문구 추출

앞에서 만든 클라이언트를 사용하고 해당 `ExtractKeyPhrases()` 함수를 호출하는 `KeyPhraseExtractionExample()`이라는 새 함수를 만듭니다. 반환된 `<Response<KeyPhraseCollection>` 개체에는 검색된 핵심 구의 목록이 포함됩니다. 오류가 발생하면 `RequestFailedException`이 throw됩니다.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>출력

```console
Key phrases:
    cat
    veterinarian
```

## <a name="extract-health-entities"></a>상태 엔터티 추출

[!INCLUDE [health operation pricing](../health-operation-pricing-caution.md)]

다음 using 문을 아직 추가하지 않은 경우 C# 파일에 추가합니다.

```csharp
using System.Threading.Tasks;
using System.Collections.Generic;
using System.Linq;
```

Text Analytics를 사용해 비동기 요청을 수행하여 텍스트에서 의료 엔터티를 추출할 수 있습니다. 아래 샘플은 기본적인 예를 보여줍니다. [GitHub](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample7_AnalyzeHealthcareEntities.md)에서 더 많은 고급 샘플을 찾을 수 있습니다.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]

```csharp
static async Task healthExample(TextAnalyticsClient client)
{
    string document = "Prescribed 100mg ibuprofen, taken twice daily.";

    List<string> batchInput = new List<string>()
    {
        document
    };
    AnalyzeHealthcareEntitiesOperation healthOperation = await client.StartAnalyzeHealthcareEntitiesAsync(batchInput);
    await healthOperation.WaitForCompletionAsync();

    await foreach (AnalyzeHealthcareEntitiesResultCollection documentsInPage in healthOperation.Value)
    {
        Console.WriteLine($"Results of Azure Text Analytics \"Healthcare Async\" Model, version: \"{documentsInPage.ModelVersion}\"");
        Console.WriteLine("");

        foreach (AnalyzeHealthcareEntitiesResult entitiesInDoc in documentsInPage)
        {
            if (!entitiesInDoc.HasError)
            {
                foreach (var entity in entitiesInDoc.Entities)
                {
                    // view recognized healthcare entities
                    Console.WriteLine($"  Entity: {entity.Text}");
                    Console.WriteLine($"  Category: {entity.Category}");
                    Console.WriteLine($"  Offset: {entity.Offset}");
                    Console.WriteLine($"  Length: {entity.Length}");
                    Console.WriteLine($"  NormalizedText: {entity.NormalizedText}");
                }
                Console.WriteLine($"  Found {entitiesInDoc.EntityRelations.Count} relations in the current document:");
                Console.WriteLine("");

                // view recognized healthcare relations
                foreach (HealthcareEntityRelation relations in entitiesInDoc.EntityRelations)
                {
                    Console.WriteLine($"    Relation: {relations.RelationType}");
                    Console.WriteLine($"    For this relation there are {relations.Roles.Count} roles");

                    // view relation roles
                    foreach (HealthcareEntityRelationRole role in relations.Roles)
                    {
                        Console.WriteLine($"      Role Name: {role.Name}");

                        Console.WriteLine($"      Associated Entity Text: {role.Entity.Text}");
                        Console.WriteLine($"      Associated Entity Category: {role.Entity.Category}");
                        Console.WriteLine("");
                    }
                    Console.WriteLine("");
                }
            }
            else
            {
                Console.WriteLine("  Error!");
                Console.WriteLine($"  Document error code: {entitiesInDoc.Error.ErrorCode}.");
                Console.WriteLine($"  Message: {entitiesInDoc.Error.Message}");
            }
            Console.WriteLine("");
        }
    }
}
```

애플리케이션에 이 예제를 추가한 후 `main()` 메서드에서 `await`를 사용하여 호출합니다. 분석 작업은 비동기식이므로 `Main()` 메서드를 `async Task` 유형으로 업데이트해야 합니다.

```csharp
static async Task Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    await healthExample(client);
}
```

```console
Results of Azure Text Analytics "Healthcare Async" Model, version: "2021-05-15"

  Entity: 100mg
  Category: Dosage
  Offset: 11
  Length: 5
  NormalizedText:
  Entity: ibuprofen
  Category: MedicationName
  Offset: 17
  Length: 9
  NormalizedText: ibuprofen
  Entity: twice daily
  Category: Frequency
  Offset: 34
  Length: 11
  NormalizedText:
  Found 2 relations in the current document:

    Relation: DosageOfMedication
    For this relation there are 2 roles
      Role Name: Dosage
      Associated Entity Text: 100mg
      Associated Entity Category: Dosage

      Role Name: Medication
      Associated Entity Text: ibuprofen
      Associated Entity Category: MedicationName


    Relation: FrequencyOfMedication
    For this relation there are 2 roles
      Role Name: Medication
      Associated Entity Text: ibuprofen
      Associated Entity Category: MedicationName

      Role Name: Frequency
      Associated Entity Text: twice daily
      Associated Entity Category: Frequency
```


## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>Analyze(분석) 작업을 통해 비동기적으로 API 사용

분석 작업을 사용하여 NER, 핵심 구 추출, 감정 분석 및 PII 검색에 대한 비동기 일괄 처리 요청을 수행할 수 있습니다. 아래 샘플에서는 하나의 작업에 대한 기본 예제를 보여줍니다. [GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample_AnalyzeActions.md)에서 더 많은 고급 샘플을 찾을 수 있습니다.

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

다음 using 문을 아직 추가하지 않은 경우 C# 파일에 추가합니다.

```csharp
using System.Threading.Tasks;
using System.Collections.Generic;
using System.Linq;
```

앞에서 만든 클라이언트를 사용하고 해당 `StartAnalyzeBatchActionsAsync()` 함수를 호출하는 `AnalyzeOperationExample()`이라는 새 함수를 만듭니다. 반환된 작업에는 `AnalyzeBatchActionsResult` 개체가 포함됩니다. 장기 실행 작업이므로 업데이트할 값의 `operation.WaitForCompletionAsync()`에 `await`를 지정합니다. `WaitForCompletionAsync()`가 완료되면 `operation.Value`에서 컬렉션을 업데이트해야 합니다. 오류가 발생하면 `RequestFailedException`이 throw됩니다.

[!INCLUDE [The following method applies to both v3.1 and v3.2-preview](../method-applies-both-versions.md)]


```csharp
static async Task AnalyzeOperationExample(TextAnalyticsClient client)
{
    string inputText = "Microsoft was founded by Bill Gates and Paul Allen.";

    var batchDocuments = new List<string> { inputText };


    TextAnalyticsActions actions = new TextAnalyticsActions()
    {
        RecognizeEntitiesActions = new List<RecognizeEntitiesAction>() { new RecognizeEntitiesAction() },
        ExtractKeyPhrasesActions = new List<ExtractKeyPhrasesAction>() { new ExtractKeyPhrasesAction() },
        DisplayName = "Analyze Operation Quick Start Example"
    };

    AnalyzeActionsOperation operation = await client.StartAnalyzeActionsAsync(batchDocuments, actions);

    await operation.WaitForCompletionAsync();

    Console.WriteLine($"Status: {operation.Status}");
    Console.WriteLine($"Created On: {operation.CreatedOn}");
    Console.WriteLine($"Expires On: {operation.ExpiresOn}");
    Console.WriteLine($"Last modified: {operation.LastModified}");
    if (!string.IsNullOrEmpty(operation.DisplayName))
        Console.WriteLine($"Display name: {operation.DisplayName}");
    Console.WriteLine($"  Succeeded actions: {operation.ActionsSucceeded}");
    Console.WriteLine($"  Failed actions: {operation.ActionsFailed}");
    Console.WriteLine($"  In progress actions: {operation.ActionsInProgress}");

    await foreach (AnalyzeActionsResult documentsInPage in operation.Value)
    {
        RecognizeEntitiesResultCollection entitiesResult = documentsInPage.RecognizeEntitiesResults.FirstOrDefault().DocumentsResults;
        ExtractKeyPhrasesResultCollection keyPhrasesResults = documentsInPage.ExtractKeyPhrasesResults.FirstOrDefault().DocumentsResults;

        Console.WriteLine("Recognized Entities");

        foreach (RecognizeEntitiesResult result in entitiesResult)
        {
            Console.WriteLine($"  Recognized the following {result.Entities.Count} entities:");

            foreach (CategorizedEntity entity in result.Entities)
            {
                Console.WriteLine($"  Entity: {entity.Text}");
                Console.WriteLine($"  Category: {entity.Category}");
                Console.WriteLine($"  Offset: {entity.Offset}");
                Console.WriteLine($"  Length: {entity.Length}");
                Console.WriteLine($"  ConfidenceScore: {entity.ConfidenceScore}");
                Console.WriteLine($"  SubCategory: {entity.SubCategory}");
            }
            Console.WriteLine("");
        }

        Console.WriteLine("Key Phrases");
        
        foreach (ExtractKeyPhrasesResult documentResults in keyPhrasesResults)
        {
            Console.WriteLine($"  Recognized the following {documentResults.KeyPhrases.Count} Keyphrases:");

            foreach (string keyphrase in documentResults.KeyPhrases)
            {
                Console.WriteLine($"  {keyphrase}");
            }
            Console.WriteLine("");
        }
        
    }
}
```

애플리케이션에 이 예제를 추가한 후 `main()` 메서드에서 `await`를 사용하여 호출합니다. 분석 작업은 비동기식이므로 `Main()` 메서드를 `async Task` 유형으로 업데이트해야 합니다.

```csharp
static async Task Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    await AnalyzeOperationExample(client);
}
```

### <a name="output"></a>출력

```console
Status: succeeded
Created On: 3/10/2021 2:25:01 AM +00:00
Expires On: 3/11/2021 2:25:01 AM +00:00
Last modified: 3/10/2021 2:25:05 AM +00:00
Display name: Analyze Operation Quick Start Example
Total actions: 1
  Succeeded actions: 1
  Failed actions: 0
  In progress actions: 0
Recognized Entities
    Recognized the following 3 entities:
    Entity: Microsoft
    Category: Organization
    Offset: 0
    ConfidenceScore: 0.83
    SubCategory: 
    Entity: Bill Gates
    Category: Person
    Offset: 25
    ConfidenceScore: 0.85
    SubCategory: 
    Entity: Paul Allen
    Category: Person
    Offset: 40
    ConfidenceScore: 0.9
    SubCategory: 
```

## <a name="text-summarization"></a>텍스트 요약

# <a name="version-32-preview1"></a>[버전 3.2-preview.1](#tab/version-3-2)

Text Analytics를 사용하여 많은 양의 텍스트를 요약할 수 있습니다. 

다음 using 문을 아직 추가하지 않은 경우 C# 파일에 추가합니다.

```csharp
using System.Threading.Tasks;
using System.Collections.Generic;
```

클라이언트를 인수로 사용하는 `TextSummarizationExample()`이라는 새 함수를 만듭니다. 그러면 장기 실행 작업이 실행되고 결과가 폴링됩니다. 

```csharp
static async Task TextSummarizationExample(TextAnalyticsClient client)
{
    string document = @"The extractive summarization feature in Text Analytics uses natural language processing techniques to locate key sentences in an unstructured text document. 
        These sentences collectively convey the main idea of the document. This feature is provided as an API for developers. 
        They can use it to build intelligent solutions based on the relevant information extracted to support various use cases. 
        In the public preview, extractive summarization supports several languages. It is based on pretrained multilingual transformer models, part of our quest for holistic representations. 
        It draws its strength from transfer learning across monolingual and harness the shared nature of languages to produce models of improved quality and efficiency." ;

    // Prepare analyze operation input. You can add multiple documents to this list and perform the same
    // operation to all of them.
    var batchInput = new List<string>
    {
        document
    };

    TextAnalyticsActions actions = new TextAnalyticsActions()
    {
        ExtractSummaryActions = new List<ExtractSummaryAction>() { new ExtractSummaryAction() }
    };

    // Start analysis process.
    AnalyzeActionsOperation operation = await client.StartAnalyzeActionsAsync(batchInput, actions);
    await operation.WaitForCompletionAsync();
    // View operation status.
    Console.WriteLine($"AnalyzeActions operation has completed");
    Console.WriteLine();

    Console.WriteLine($"Created On   : {operation.CreatedOn}");
    Console.WriteLine($"Expires On   : {operation.ExpiresOn}");
    Console.WriteLine($"Id           : {operation.Id}");
    Console.WriteLine($"Status       : {operation.Status}");

    Console.WriteLine();
    // View operation results.
    await foreach (AnalyzeActionsResult documentsInPage in operation.Value)
    {
        IReadOnlyCollection<ExtractSummaryActionResult> summaryResults = documentsInPage.ExtractSummaryResults;

        foreach (ExtractSummaryActionResult summaryActionResults in summaryResults)
        {
            if (summaryActionResults.HasError)
            {
                Console.WriteLine($"  Error!");
                Console.WriteLine($"  Action error code: {summaryActionResults.Error.ErrorCode}.");
                Console.WriteLine($"  Message: {summaryActionResults.Error.Message}");
                continue;
            }

            foreach (ExtractSummaryResult documentResults in summaryActionResults.DocumentsResults)
            {
                if (documentResults.HasError)
                {
                    Console.WriteLine($"  Error!");
                    Console.WriteLine($"  Document error code: {documentResults.Error.ErrorCode}.");
                    Console.WriteLine($"  Message: {documentResults.Error.Message}");
                    continue;
                }

                Console.WriteLine($"  Extracted the following {documentResults.Sentences.Count} sentence(s):");
                Console.WriteLine();

                foreach (SummarySentence sentence in documentResults.Sentences)
                {
                    Console.WriteLine($"  Sentence: {sentence.Text}");
                    Console.WriteLine();
                }
            }
        }
    }

}

```

애플리케이션에 이 예제를 추가한 후 `main()` 메서드에서 `await`를 사용하여 호출합니다. 분석 작업은 비동기식이므로 `Main()` 메서드를 `async Task` 유형으로 업데이트해야 합니다.

```csharp
static async Task Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    await TextSummarizationExample(client);
}
```

### <a name="output"></a>출력

```console
AnalyzeActions operation has completed

Created On   : 8/17/2021 9:06:07 PM +00:00
Expires On   : 8/18/2021 9:06:07 PM +00:00
Id           : 70dc2e88-e930-483e-9d96-1ba5f0f27f18
Status       : succeeded

Extracted the following 3 sentence(s):

Sentence: The extractive summarization feature in Text Analytics uses natural language processing techniques to locate key sentences in an unstructured text document.
Sentence: This feature is provided as an API for developers.
Sentence: They can use it to build intelligent solutions based on the relevant information extracted to support various use cases.

```

# <a name="version-31"></a>[버전 3.1](#tab/version-3-1)

이 기능은 버전 3.1에서 사용할 수 없습니다.

---
