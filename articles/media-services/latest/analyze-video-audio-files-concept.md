---
title: 비디오 및 오디오 파일 분석
description: Azure Media Services에서 AudioAnalyzerPreset 및 VideoAnalyzerPreset을 사용하여 오디오 및 비디오 콘텐츠를 분석하는 방법을 알아봅니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: inhenkel
ms.openlocfilehash: 4271224c3ebb78274d13617ac30d8e376c301108
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646064"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Azure Media Services로 비디오 및 오디오 파일 분석

[!INCLUDE [regulation](../../azure-video-analyzer/video-analyzer-for-media-docs/includes/regulation.md)]

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services v3를 사용하면 Azure Video Analyzer for Media(이전의 Video Indexer)를 사용하여 비디오 및 오디오 파일에서 인사이트를 추출할 수 있습니다. 이 문서에서는 이와 같은 인사이트를 추출하는 데 사용되는 Media Services v3 분석기 사전 설정을 설명합니다. 더 자세한 인사이트가 필요한 경우 미디어용 비디오 분석기를 직접 사용합니다. Video Analyzer for Media 및 Media Services 분석기 사전 설정을 사용하는 경우를 이해하려면 [비교 문서](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md)를 확인하세요.

Audio Analyzer 사전 설정에는 기본 및 표준의 두 가지 모드가 있습니다. 아래 표에서 두 모드의 차이에 대한 설명을 참조하세요.

Media Services v3 사전 설정을 사용하여 콘텐츠를 분석하려면 **변환** 을 만들고 [VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset) 또는 **AudioAnalyzerPreset** 사전 설정 중 하나를 사용하는 **작업** 을 제출합니다. **VideoAnalyzerPreset** 을 사용하는 방법이 나와 있는 자습서는 [Azure Media Services로 비디오 분석](analyze-videos-tutorial.md)을 참조하세요.

## <a name="compliance-privacy-and-security"></a>규정 준수, 개인 정보 보호 및 보안

중요한 알림을 사용하는 경우 Video Analyzer for Media 사용 시 적용 가능한 모든 법률을 준수해야 하며, 다른 사용자의 권리를 침해하거나 다른 사용자에게 해로운 방법으로 Video Analyzer for Media 또는 기타 Azure 서비스를 사용해서는 안 됩니다. 생체 인식 데이터를 비롯한 동영상을 처리하고 저장하기 위해 Video Analyzer for Media 서비스에 업로드하려면 동영상에 나오는 모든 사람의 적절한 동의를 포함하여 적절한 권한이 모두 있어야 합니다. Video Analyzer for Media의 규정 준수, 개인 정보 보호 및 보안에 대해 알아보려면 Azure [Cognitive Services 사용 약관](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)을 참조하세요. Microsoft의 개인 정보 보호 의무 및 데이터 처리에 대한 내용은 Microsoft의 [개인정보처리방침](https://privacy.microsoft.com/PrivacyStatement), [Online Services 사용 약관](https://www.microsoft.com/licensing/product-licensing/products)(“OST”) 및 [Data Processing 추록](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67)(“DPA”)을 검토하세요. 데이터 보존, 삭제/소멸을 비롯한 추가 개인 정보 보호 정보는 OST 및 [여기](../../azure-video-analyzer/video-analyzer-for-media-docs/faq.yml)에서 사용할 수 있습니다. Video Analyzer for Media를 사용하는 것은 Cognitive Services 사용 약관, OST, DPA 및 개인정보처리방침을 따르는 것에 동의하는 것입니다.

## <a name="built-in-presets"></a>기본 제공 미리 설정

Media Services에서 현재 지원하는 기본 제공 분석기 미리 설정은 다음과 같습니다.  

|**미리 설정 이름**|**시나리오/모드**|**세부 정보**|
|---|---|---|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|오디오 표준 모드 분석|사전 설정은 음성 기록을 포함하여 미리 정의된 AI 기반 분석 작업 세트를 적용합니다. 현재 사전 설정은 단일 언어 음성을 포함하는 단일 오디오 트랙을 사용하여 콘텐츠를 처리하도록 지원합니다. BCP-47 형식의 ‘language tag-region’을 사용하여 입력에서 오디오 페이로드의 언어를 지정할 수 있습니다. 지원되는 언어는 영어('en-US', 'en-GB' 및 'en-AU'), 스페인어('es-ES' 및 'es-MX'), 프랑스어('fr-FR' 및 'fr-CA'), 이탈리아어('it-IT'), 일본어('ja-JP'), 포르투갈어('pt-BR'), 중국어('zh-CN'), 독일어('de-DE'), 아랍어('ar-BH', 'ar-EG', 'ar-IQ', 'ar-JO', 'ar-KW', 'ar-LB', 'ar-OM', 'ar-QA', 'ar-SA' 및 'ar-SY'), 러시아어('ru-RU'), 힌디어('hi-IN'), 한국어('ko-KR'), 덴마크어('da-DK'), 노르웨이어('nb-NO'), 스웨덴어('sv-SE'), 핀란드어('fi-FI'), 태국어('th-TH') 및 터키어('tr-TR')입니다.<br/><br/> 언어가 지정되지 않았거나 null로 설정된 경우 자동 언어 감지 기능은 감지된 첫 번째 언어를 선택하고 파일의 지속시간 동안 선택된 언어를 계속 사용합니다. 자동 언어 감지 기능은 현재 영어, 중국어, 프랑스어, 독일어, 이탈리아어, 일본어, 스페인어, 러시아어 및 포르투갈어를 지원합니다. 첫 번째 언어가 감지된 후 언어 간에 동적으로 전환하는 기능은 지원되지 않습니다. 자동 언어 검색 기능은 분명히 구별할 수 있는 음성이 포함된 오디오 녹음에 가장 적합합니다. 자동 언어 감지 기능으로 언어를 찾지 못하면 대화 내용 기록은 영어를 다시 사용합니다.|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|오디오 기본 모드 분석|이 사전 설정 모드는 음성 텍스트 변환 대화 내용 기록을 수행하고 VTT 자막/캡션 파일을 생성합니다. 이 모드의 출력에는 키워드, 기록, 타이밍 정보만 포함된 Insights JSON 파일이 포함됩니다. 자동 언어 감지 기능과 화자 분할 기능은 이 모드에 포함되어 있지 않습니다. 지원되는 언어 목록은 위의 표준 모드와 동일합니다.|
|[VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|오디오 및 비디오 분석|오디오 및 비디오 모두에서 통찰력(풍부한 메타데이터)을 추출하고 JSON 형식 파일을 출력합니다. 비디오 파일을 처리할 때 오디오 통찰력만 추출할지 여부를 지정할 수 있습니다. 자세한 내용은 [비디오 분석](analyze-videos-tutorial.md)을 참조하세요.|
|[FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset)|비디오에 있는 얼굴 감지|비디오를 분석하여 비디오에 있는 모든 얼굴을 감지할 때 사용할 설정을 설명합니다.|

### <a name="audioanalyzerpreset-standard-mode"></a>AudioAnalyzerPreset 표준 모드

미리 설정을 사용하면 오디오 또는 비디오 파일에서 여러 오디오 인사이트를 추출할 수 있습니다.

출력에는 오디오 대본의 VTT 파일과 JSON 파일(모든 인사이트 포함)이 포함됩니다. 이 사전 설정은 입력 파일의 언어를 [BCP47](https://tools.ietf.org/html/bcp47) 문자열의 형태로 지정하는 속성을 허용합니다. 오디오 인사이트는 다음 정보를 포함합니다.

* **오디오 대화 내용 기록**: 타임스탬프가 있는 음성의 대본입니다. 여러 언어가 지원됩니다.
* **화자 인덱싱**: 화자와 해당 음성의 매핑입니다.
* **음성 감정 분석**: 오디오 대화 내용 기록에 대해 수행한 감정 분석의 출력입니다.
* **키워드**: 오디오 기록에서 추출한 키워드입니다.

### <a name="audioanalyzerpreset-basic-mode"></a>AudioAnalyzerPreset 기본 모드

미리 설정을 사용하면 오디오 또는 비디오 파일에서 여러 오디오 인사이트를 추출할 수 있습니다.

출력에는 오디오 대본의 VTT 파일과 JSON 파일이 포함됩니다. 이 사전 설정은 입력 파일의 언어를 [BCP47](https://tools.ietf.org/html/bcp47) 문자열의 형태로 지정하는 속성을 허용합니다. 출력에는 다음이 포함됩니다.

* **오디오 대화 내용 기록**: 타임스탬프가 있는 음성의 대본입니다. 여러 언어가 지원되지만 자동 언어 감지 기능과 화자 분할 기능은 포함되어 있지 않습니다.
* **키워드**: 오디오 대화 내용 기록에서 추출한 키워드입니다.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

미리 설정을 사용하면 비디오 파일에서 여러 오디오 및 비디오 인사이트를 추출할 수 있습니다. 출력에는 JSON 파일(모든 인사이트 포함), 비디오 대본의 VTT 파일 및 썸네일 컬렉션이 포함됩니다. 이 사전 설정은 [BCP47](https://tools.ietf.org/html/bcp47) 문자열(비디오의 언어를 나타냄)을 속성으로 수락합니다. 비디오 인사이트에는 앞서 언급한 모든 오디오 인사이트와 다음과 같은 추가 항목이 포함됩니다.

* **얼굴 추적**: 비디오에 얼굴이 나타나는 시간입니다. 얼굴마다 Face ID와 해당 썸네일 컬렉션이 있습니다.
* **시각적 텍스트**: 광학 인식을 통해 감지된 텍스트입니다. 텍스트에는 타임스탬프가 표시되며 오디오 대본 외에 키워드를 추출하는 데 사용되기도 합니다.
* **키 프레임**: 비디오에서 추출한 키 프레임 컬렉션입니다.
* **시각적 콘텐츠 조정**: 본질적으로 성인용 또는 선정적이라는 플래그가 지정된 비디오의 일부입니다.
* **주석**: 미리 정의된 개체 모델을 기반으로 비디오에 주석을 추가한 결과입니다.

## <a name="insightsjson-elements"></a>insights.json 요소

출력에는 비디오 또는 오디오에서 찾은 모든 인사이트가 포함된 JSON 파일(insights.json)이 포함됩니다. JSON에는 다음 요소가 포함될 수 있습니다.

### <a name="transcript"></a>대본

|Name|Description|
|---|---|
|id|줄 ID입니다.|
|text|자체 대본입니다.|
|언어|대본 언어입니다. 각 줄마다 다른 언어가 사용될 수 있는 대본을 지원하기 위한 요소입니다.|
|인스턴스|이 줄이 나타나는 시간 범위 목록입니다. 인스턴스가 대본인 경우 인스턴스는 하나만 있습니다.|

예제:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|이름|Description|
|---|---|
|id|OCR 줄 ID입니다.|
|text|OCR 텍스트입니다.|
|신뢰도|인식 신뢰도입니다.|
|언어|OCR 언어입니다.|
|인스턴스|이 OCR이 표시된 시간 범위 목록(동일한 OCR이 여러 번 나타날 수 있음)입니다.|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>얼굴

|이름|Description|
|---|---|
|id|얼굴 ID입니다.|
|name|얼굴 이름입니다. 'Unknown #0', 식별된 유명인 또는 고객 교육을 받은 사용자일 수 있습니다.|
|신뢰도|얼굴 인식 신뢰도입니다.|
|description|유명인에 대한 설명입니다. |
|thumbnailId|얼굴 썸네일의 ID입니다.|
|knownPersonId|내부 ID입니다(알려진 사용자인 경우).|
|referenceId|Bing ID입니다(Bing 유명인인 경우).|
|referenceType|현재는 그냥 Bing입니다.|
|title|직함입니다(유명인인 경우)(예: "Microsoft CEO").|
|imageUrl|이미지 URL입니다(유명인인 경우).|
|인스턴스|지정된 시간 범위 내에서 얼굴이 나타난 인스턴스입니다. 인스턴스마다 thumbnailsId가 있습니다. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="shots"></a>샷

|Name|Description|
|---|---|
|id|샷 ID입니다.|
|keyFrames|샷 내의 키 프레임 목록(각각 ID와 인스턴스 시간 범위 목록이 있음)입니다. 키 프레임 인스턴스에는 keyFrame의 썸네일 ID와 thumbnailId 필드가 있습니다.|
|인스턴스|이 샷의 시간 범위 목록(샷에는 인스턴스가 하나만 있음)입니다.|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>통계

|Name|Description|
|---|---|
|CorrespondenceCount|동영상의 해당 항목 수입니다.|
|WordCount|화자별 단어 수입니다.|
|SpeakerNumberOfFragments|비디오에서 화자가 있는 조각의 양입니다.|
|SpeakerLongestMonolog|화자의 가장 긴 단독 발언입니다. 단독 발언 내에 화자의 침묵이 있으면 포함됩니다. 단독 발언의 시작과 끝 부분에 있는 침묵은 제거됩니다.|
|SpeakerTalkToListenRatio|화자의 단독 발언에 소요된 시간(그 사이 침묵 제외)을 비디오의 총 시간으로 나눈 값을 기반으로 계산됩니다. 시간은 소수점 이하 세 자리에서 반올림됩니다.|


### <a name="sentiments"></a>감정

감정은 sentimentType 필드(긍정적/중립/부정적)로 집계됩니다. 예: 0-0.1, 0.1-0.2.

|Name|Description|
|---|---|
|id|감정 ID입니다.|
|averageScore |해당 감정 유형의 모든 인스턴스의 모든 점수 평균 – 긍정적/중립/부정적|
|인스턴스|이 감정이 나타나는 시간 범위 목록입니다.|
|sentimentType |가능한 유형은 'Positive', 'Neutral' 또는 'Negative'입니다.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

### <a name="labels"></a>레이블

|Name|Description|
|---|---|
|id|레이블 ID입니다.|
|name|레이블 이름(예: '컴퓨터', 'TV')입니다.|
|언어|레이블 이름 언어(번역된 경우)입니다. BCP-47|
|인스턴스|레이블이 나타나는 시간 범위 목록(레이블은 여러 번 나타날 수 있음)입니다. 인스턴스마다 신뢰도 필드가 있습니다. |

```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="keywords"></a>키워드

|Name|Description|
|---|---|
|id|키워드 ID입니다.|
|text|키워드 텍스트입니다.|
|신뢰도|키워드의 인식 신뢰도입니다.|
|언어|키워드 언어(번역된 경우)입니다.|
|인스턴스|키워드가 표시되는 시간 범위 목록(키워드가 여러 번 나타날 수 있음)입니다.|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

visualContentModeration 블록에는 Video Analyzer for Media에서 잠재적으로 성인용 콘텐츠가 있는 것으로 확인된 시간 범위가 포함되어 있습니다. visualContentModeration이 비어 있는 경우에는 식별된 성인 콘텐츠가 없는 것입니다.

성인 또는 외설 콘텐츠가 포함된 것으로 확인된 비디오는 프라이빗 보기만 가능합니다. 사용자는 콘텐츠를 사람이 검토하도록 요청을 제출할 수 있으며 이 경우 `IsAdult` 특성에 사람이 검토한 결과가 포함됩니다.

|이름|Description|
|---|---|
|id|시각 콘텐츠 조정 ID입니다.|
|adultScore|성인 점수(Content Moderator 기반)입니다.|
|racyScore|외설 점수(Content Moderator 기반)입니다.|
|인스턴스|이 시각 콘텐츠 조정이 나타난 시간 범위 목록입니다.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>다음 단계

[자습서: Azure Media Services로 비디오 분석](analyze-videos-tutorial.md)
