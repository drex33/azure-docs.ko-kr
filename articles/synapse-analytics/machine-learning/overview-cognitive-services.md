---
title: Azure Synapse Analytics의 Cognitive Services
description: Azure Cognitive Services의 미리 학습된 모델을 사용하여 Azure Synapse Analytics의 AI(인공 지능)로 데이터를 보강합니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye, negust, ruxu
ms.date: 06/30/2021
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1e288c0cb81b99e3b7c3a925d05380529446aa06
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133368778"
---
# <a name="cognitive-services-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 Cognitive Services

Azure Cognitive Services의 미리 학습된 모델을 사용하면 Azure Synapse Analytics의 AI(인공 지능)로 데이터를 보강할 수 있습니다.

[Azure Cognitive Services](../../cognitive-services/what-are-cognitive-services.md)는 AI 또는 데이터 과학 기술이 없는 경우에도 데이터에 인지 인텔리전스를 추가하는 클라우드 기반 서비스입니다. Synapse Analytics의 데이터에서 이러한 서비스를 사용할 수 있는 몇 가지 방법이 있습니다.

- Synapse Analytics의 Cognitive Services 마법사는 Spark 테이블의 데이터를 사용하여 인식 서비스에 연결하는 Synapse Notebook에서 PySpark 코드를 생성합니다. 그런 다음, 이 서비스에서 미리 학습된 기계 학습 모델을 사용하여 데이터에 AI를 추가하는 작업을 수행합니다. 자세한 내용은 [감정 분석 마법사](tutorial-cognitive-services-sentiment.md) 및 [변칙 검색 마법사](tutorial-cognitive-services-anomaly.md)를 확인하세요.

- [SynapseML](https://github.com/microsoft/SynapseML)(Synapse Machine Learning)을 사용하면 다양한 Spark 데이터 원본에서 강력하고 확장성이 뛰어난 예측 및 분석 모델을 빌드할 수 있습니다. Synapse Spark는 [Cognitive Services on Spark](https://github.com/microsoft/SynapseML/tree/master/notebooks/features/cognitive_services)를 포함한 기본 제공 SynapseML 라이브러리를 제공합니다. 자세한 내용은 이 [자습서](#tutorials)를 확인하세요.

- 마법사에서 생성된 PySpark 코드 또는 자습서에 제공된 예제 SynapseML 코드부터는 데이터에 다른 인지 서비스를 사용하는 사용자 고유의 코드를 작성할 수 있습니다. 사용 가능한 서비스에 대한 자세한 내용은 [Azure Cognitive Services 무엇인가요?](../../cognitive-services/what-are-cognitive-services.md)를 참조하세요.




## <a name="get-started"></a>시작하기

[Azure Synapse의 Cognitive Services를 사용하기 위한 사전 필수 구성 요소](tutorial-configure-cognitive-services-synapse.md) 자습서에서는 Synapse Analytics의 Cognitive Services를 사용하기 전에 수행해야 하는 몇 가지 단계를 안내합니다.

## <a name="tutorials"></a>자습서

다음 자습서에서는 Synapse Analytics의 Cognitive Services를 사용하는 전체 예제를 제공합니다.

- [Cognitive Services를 사용한 감정 분석](tutorial-cognitive-services-sentiment.md) - 고객 의견의 예제 데이터 세트를 사용하여 각 행의 주석 감정을 나타내는 열이 있는 Spark 테이블을 빌드합니다.

- [Cognitive Services를 통한 변칙 검색](tutorial-cognitive-services-anomaly.md) - 시계열 데이터의 예제 데이터 세트를 사용하여 각 행의 데이터가 변칙인지 여부를 나타내는 열이 있는 Spark 테이블을 빌드합니다.

- [Microsoft Machine Learning for Apache Spark를 사용하여 기계 학습 애플리케이션 빌드](tutorial-build-applications-use-mmlspark.md) - 이 자습서에서는 SynapseML을 사용하여 Cognitive Services의 여러 모델에 액세스하는 방법을 보여 줍니다.

- [Applied AI Service를 사용한 Form Recognizer](tutorial-form-recognizer-use-mmlspark.md)에서는 [Form Recognizer](../../applied-ai-services/form-recognizer/index.yml)를 사용하여 양식과 문서를 분석하고 Azure Synapse Analytics에서 텍스트와 데이터를 추출하는 방법을 보여줍니다. 

- [Cognitive Service를 사용한 텍스트 분석](tutorial-text-analytics-use-mmlspark.md)에서는 [Text Analytics](../../cognitive-services/text-analytics/index.yml)를 사용하여 Azure Synapse Analytics에서 구조화되지 않은 텍스트를 분석하는 방법을 보여줍니다.

- [Cognitive Service를 사용한 번역기](tutorial-translator-use-mmlspark.md)에서는 [번역기](../../cognitive-services/Translator/index.yml)를 사용하여 Azure Synapse Analytics에서 지능형 다국어 솔루션을 빌드하는 방법을 보여줍니다.

- [Cognitive Service를 사용한 Computer Vision](tutorial-computer-vision-use-mmlspark.md)에서는 [Computer Vision](../../cognitive-services/computer-vision/index.yml)을 사용하여 Azure Synapse Analytics에서 이미지를 분석하는 방법을 보여줍니다.

## <a name="available-cognitive-services-apis"></a>사용 가능한 Cognitive Services API
### <a name="bing-image-search"></a>Bing 이미지 검색

| API 형식                                   | SynapseML API                  | Cognitive Service API(버전)                                                                                               | DEP VNet 지원 |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
|Bing 이미지 검색|BingImageSearch|이미지 - Visual Search V7.0| 지원되지 않음|

### <a name="anomaly-detector"></a>Anomaly Detector

| API 형식                                   | SynapseML API                  | Cognitive Service API(버전)                                                                                               | DEP VNet 지원 |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| 마지막 변칙 검색                        | DetectLastAnomaly              | 마지막 포인트 검색 V1.0                                                                                                          | 지원됨        |
| 변칙 검색                           | DetectAnomalies                | 전체 시리즈 검색 V1.0                                                                                                       | 지원됨        |
| Simple DetectAnomalies                     | SimpleDetectAnomalies          | 전체 시리즈 검색 V1.0                                                                                                       | 지원됨        |

### <a name="computer-vision"></a>Computer Vision

| API 형식                                   | SynapseML API                  | Cognitive Service API(버전)                                                                                               | DEP VNet 지원 |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| OCR                                        | OCR                            | 인쇄된 텍스트 인식 V2.0                                                                                                     | 지원됨        |
| 텍스트 인식                             | RecognizeText                  | 텍스트 인식 V2.0                                                                                                             | 지원됨        |
| 이미지 읽기                                 | ReadImage                      | 읽기 V3.1                                                                                                                       | 지원됨        |
| 썸네일 생성                        | GenerateThumbnails             | 썸네일 생성 2.0                                                                                                         | 지원됨        |
| 이미지 분석                              | AnalyzeImage                   | 이미지 분석 V2.0                                                                                                              | 지원됨        |
| 도메인 특정 콘텐츠 인식          | RecognizeDomainSpecificContent | 도메인 기준 이미지 분석 V2.0                                                                                                    | 지원됨        |
| 태그 이미지                                  | TagImage                       | 태그 이미지 V2.0                                                                                                                  | 지원됨        |
| 이미지 설명                             | DescribeImage                  | 이미지 설명 V2.0                                                                                                             | 지원됨        |


### <a name="translator"></a>변환기

| API 형식                                   | SynapseML API                  | Cognitive Service API(버전)                                                                                               | DEP VNet 지원 |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| 텍스트 번역                             | Translate                      | 번역 V3.0                                                                                                                  | 지원되지 않음    |
| 텍스트 음역                         | Transliterate                  | 음역 V3.0                                                                                                              | 지원되지 않음    |
| 언어 감지                            | Detect                         | V3.0 검색                                                                                                                     | 지원되지 않음    |
| 문장 나누기                             | BreakSentence                  | 문장 나누기V3.0                                                                                                             | 지원되지 않음    |
| 사전 조회(대체 번역) | DictionaryLookup               | 사전 조회 V3.0                                                                                                          | 지원되지 않음    |
| 문서 번역                       | DocumentTranslator             | 문서 번역 V1.0                                                                                                       | 지원되지 않음    |


### <a name="face"></a>Face

| API 형식                                   | SynapseML API                  | Cognitive Service API(버전)                                                                                               | DEP VNet 지원 |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| 얼굴 감지                                | DetectFace                     | Url V1.0을 통해 검색                                                                                                            | 지원됨        |
| 유사 얼굴 찾기                          | FindSimilarFace                | 유사 얼굴 찾기 V1.0                                                                                                               | 지원됨        |
| 얼굴 그룹화                                | GroupFaces                     | 그룹 V1.0                                                                                                                      | 지원됨        |
| 얼굴 식별                             | IdentifyFaces                  | 식별 V1.0                                                                                                                   | 지원됨        |
| 얼굴 확인                               | VerifyFaces                    | 대면 V1.0 확인                                                                                                        | 지원됨        |

### <a name="form-recognizer"></a>Form Recognizer
| API 형식                                   | SynapseML API                  | Cognitive Service API(버전)                                                                                               | DEP VNet 지원 |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| 레이아웃 분석                             | AnalyzeLayout                  | 레이아웃 비동기 분석 V2.1                                                                                                       | 지원됨        |
| 영수증 분석                           | AnalyzeReceipts                | 영수증 비동기 분석 V2.1                                                                                                      | 지원됨        |
| 명함 분석                     | AnalyzeBusinessCards           | 명함 비동기 분석 V2.1                                                                                                | 지원됨        |
| 청구성 분석                           | AnalyzeInvoices                | 청구서 비동기 분석 V2.1                                                                                                      | 지원됨        |
| ID 문서 분석                       | AnalyzeIDDocuments             | ID(식별) 문서 모델 V2.1                                                                                         | 지원됨        |
| 사용자 지정 모델 나열                         | ListCustomModels               | 사용자 지정 모델 나열 V2.1                                                                                                         | 지원됨        |
| 사용자 지정 모델 가져오기                           | GetCustomModel                 | 사용자 지정 모델 가져오기 V2.1                                                                                                          | 지원됨        |
| 사용자 지정 모델 분석                       | AnalyzeCustomModel             | 사용자 지정 모델을 사용한 분석 V2.1                                                                                                  | 지원됨        |

### <a name="speech-to-text"></a>음성 텍스트 변환
| API 형식                                   | SynapseML API                  | Cognitive Service API(버전)                                                                                               | DEP VNet 지원 |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| 음성 텍스트 변환                             | SpeechToText                   | SpeechToText V1.0 |  지원되지 않음    |
| 음성 텍스트 변환 SDK                         | SpeechToTextSDK                | 음성 SDK 버전 1.14.0 사용                                                                                                 | 지원되지 않음    |


### <a name="text-analytics"></a>텍스트 분석

| API 형식                                   | SynapseML API                  | Cognitive Service API(버전)                                                                                               | DEP VNet 지원 |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| 텍스트 감정 V2                          | TextSentimentV2                | 감정 V2.0                                                                                                                  | 지원됨        |
| 언어 감지기 V2                       | LanguageDetectorV2             | 언어 V2.0                                                                                                                  | 지원됨        |
| 엔터티 감지기 V2                         | EntityDetectorV2               | 엔터티 연결 V2.0                                                                                                           | 지원됨        |
| NER V2                                     | NERV2                          | 엔터티 인식 일반 V2.0                                                                                               | 지원됨        |
| 핵심 구 추출기 V2                    | KeyPhraseExtractorV2           | 핵심 구 V2.0                                                                                                                | 지원됨        |
| 텍스트 감정                             | TextSentiment                  | 감정 V3.1                                                                                                                  | 지원됨        |
| 핵심 구 추출기                       | KeyPhraseExtractor             | 핵심 구 V3.1                                                                                                                | 지원됨        |
| PII                                        | PII                            | 엔터티 인식 Pii V3.1                                                                                                   | 지원됨        |
| NER                                        | NER                            | 엔터티 인식 일반 V3.1                                                                                               | 지원됨        |
| 언어 감지기                          | LanguageDetector               | 언어 V3.1                                                                                                                  | 지원됨        |
| 엔터티 감지기                            | EntityDetector                 | 엔터티 연결 V3.1                                                                                                           | 지원됨        |


## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics의 Machine Learning 기능](what-is-machine-learning.md)
- [Cognitive Services란?](../../cognitive-services/what-are-cognitive-services.md)
- [Synapse Analytics 갤러리의 샘플 Notebook 사용](quickstart-gallery-sample-notebook.md)
