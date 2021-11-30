---
title: 문서 추출 인식 기술
titleSuffix: Azure Cognitive Search
description: 보강 파이프라인 내 파일에서 콘텐츠를 추출합니다.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: reference
ms.date: 08/12/2021
ms.author: chalton
ms.openlocfilehash: a95ffad271693c09aba884688e2830278c3aff5b
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271121"
---
# <a name="document-extraction-cognitive-skill"></a>문서 추출 인식 기술

**문서 추출** 기술은 보강 파이프라인에서 파일의 콘텐츠를 추출합니다. 이렇게 하면 다른 기술에서 생성될 수 있는 파일을 사용하여 기술 세트를 실행하기 전에 일반적으로 발생하는 문서 추출 단계를 활용할 수 있습니다.

> [!NOTE]
> 이 기술은 Cognitive Services로 제한되지 않으며 Cognitive Services 키 요구 사항이 없습니다.
> 이 기술은 텍스트와 이미지를 추출합니다. 텍스트 추출은 무료입니다. 이미지 추출은 [Azure Cognitive Search에서 계량됩니다](https://azure.microsoft.com/pricing/details/search/). 무료 검색 서비스에서는 빠른 시작, 자습서 및 소규모 작업을 무료로 완료할 수 있도록 하루에 인덱서당 20개의 트랜잭션 비용이 면제됩니다. 기본, 표준 이상의 경우 이미지 추출에 대한 요금이 청구됩니다.
>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="supported-document-formats"></a>지원되는 문서 형식

DocumentExtractionSkill은 다음과 같은 문서 형식에서 텍스트를 추출할 수 있습니다.

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 입력 | 허용되는 값 | 설명 |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | 순수한 텍스트 또는 json이 아닌 파일에서 문서를 추출하려면 `default`로 설정합니다. 일반 텍스트 파일의 성능을 개선하려면 `text`로 설정합니다. json 파일에서 구조화된 콘텐츠를 추출하려면 `json`으로 설정합니다. `parsingMode`가 명시적으로 정의되지 않은 경우 `default`로 자동 설정됩니다. |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | 각 파일에서 모든 메타데이터 및 텍스트 콘텐츠를 추출하려면 `contentAndMetadata`로 설정합니다. [콘텐츠 형식의 메타데이터 속성](search-blob-metadata-properties.md)(예: .png 파일에만 고유한 메타데이터)만 추출하려면 `allMetadata`로 설정합니다. `dataToExtract`가 명시적으로 정의되지 않은 경우 `contentAndMetadata`로 자동 설정됩니다. |
| `configuration` | 아래 내용을 참조하세요. | 문서를 추출하는 방법을 조정하는 선택적 매개 변수의 사전입니다. 지원되는 구성 속성에 대한 설명은 아래 표를 참조하세요. |

| 구성 매개 변수   | 허용되는 값 | 설명 |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | 데이터 세트에 포함된 이미지 또는 이미지 파일을 무시하려면 `none`으로 설정합니다. 이것이 기본값입니다. <br/>[인식 기술을 사용한 이미지 분석의 경우](cognitive-search-concept-image-scenarios.md), 기술이 [문서 크래킹](search-indexer-overview.md#document-cracking)의 일환으로 정규화된 이미지의 배열을 만들도록 `generateNormalizedImages`로 설정합니다. 이 작업을 수행하려면 `parsingMode`를 `default`로, `dataToExtract`를 `contentAndMetadata`로 설정해야 합니다. 정규화된 이미지는 시각적 검색 결과에 이미지를 포함할 때 일관된 렌더링을 위해 크기 및 회전이 지정되어 균일한 이미지를 출력하는 추가 처리를 의미합니다(예: [JFK 데모](https://github.com/Microsoft/AzureSearch_JFK_Files)에서 볼 수 있는 그래프 컨트롤의 동일 크기 사진). 이 옵션을 사용할 때 각 이미지에 대해 해당 정보가 생성됩니다.  <br/>`generateNormalizedImagePerPage`로 설정하면 포함된 이미지를 추출하는 대신 PDF 파일이 다른 방식으로 처리되며, 각 페이지는 이미지로 렌더링되고 그에 따라 정규화됩니다.  PDF가 아닌 파일 형식은 `generateNormalizedImages`가 설정된 것과 마찬가지로 처리됩니다.
| `normalizedImageMaxWidth` | 50~10,000 사이의 정수 | 정규화된 이미지의 최대 너비(픽셀 단위)가 생성되었습니다. 기본값은 2000입니다. | 
| `normalizedImageMaxHeight` | 50~10,000 사이의 정수 | 정규화된 이미지의 최대 높이(픽셀 단위)가 생성되었습니다. 기본값은 2000입니다. |

> [!NOTE]
> 정규화된 이미지의 최대 너비와 높이에 대한 2000 픽셀이라는 기본값은 [OCR 기술](cognitive-search-skill-ocr.md) 및 [이미지 분석 기술](cognitive-search-skill-image-analysis.md)에서 지원하는 최대 크기에 기반합니다. [OCR 기술](cognitive-search-skill-ocr.md)은 영어가 아닌 언어의 경우 4200, 영어의 경우 10000의 최대 너비 및 높이를 지원합니다.  최대 한도를 늘리면 기술 세트 정의 및 문서의 언어에 따라 큰 이미지 처리에 실패할 수 있습니다. 
## <a name="skill-inputs"></a>기술 입력

| 입력 이름     | 설명 |
|--------------------|-------------|
| `file_data` | 콘텐츠를 추출해야 하는 파일입니다. |

"File_data" 입력은 다음과 같이 정의된 개체여야 합니다.

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

or

```json
{
  "$type": "file",
  "url": "URL to download file",
  "sasToken": "OPTIONAL: SAS token for authentication if the URL provided is for a file in blob storage"
}
```

이 파일 참조 개체는 다음 세 가지 방법 중 하나로 생성될 수 있습니다.

 - 인덱서 정의에서 `allowSkillsetToReadFileData` 매개 변수를 "true"로 설정합니다.  그러면 Blob 데이터 원본에서 다운로드한 원본 파일 데이터를 나타내는 개체인 `/document/file_data` 경로가 생성됩니다. 이 매개 변수는 Blob Storage의 데이터에만 적용됩니다.

 - 인덱서 정의의 `imageAction` 매개 변수를 `none` 이외의 값으로 설정합니다.  이렇게 하면 개별적으로 전달되는 경우 이 기술에 대한 입력에 필요한 규칙을 따르는 이미지 배열이 생성됩니다(즉, `/document/normalized_images/*`).

 - 사용자 지정 기술이 정확히 위와 같이 정의된 json 개체를 반환합니다.  `$type` 매개 변수는 정확히 `file`로 설정되어야 하고 `data` 매개 변수는 파일 콘텐츠의 base 64로 인코딩된 바이트 배열 데이터여야 합니다. 또는 `url` 매개 변수는 해당 위치에서 파일을 다운로드할 수 있는 액세스 권한이 있는 올바른 형식의 URL이어야 합니다.

## <a name="skill-outputs"></a>기술 출력

| 출력 이름    | 설명 |
|--------------|-------------|
| `content` | 문서의 텍스트 콘텐츠. |
| `normalized_images`   | `imageAction`를 `none` 이외의 값으로 설정한 경우 새 *normalized_images* 필드에는 이미지 배열이 포함됩니다. 각 이미지의 출력 형식에 대한 자세한 내용은 [이미지 추출에 대한 설명서](cognitive-search-concept-image-scenarios.md)를 참조하세요. |

##  <a name="sample-definition"></a>샘플 정의

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "extracted_content"
      },
      {
        "name": "normalized_images",
        "targetName": "extracted_normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>샘플 입력

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>샘플 출력

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [인식 검색 시나리오의 이미지에서 정보를 처리하고 추출하는 방법](cognitive-search-concept-image-scenarios.md)