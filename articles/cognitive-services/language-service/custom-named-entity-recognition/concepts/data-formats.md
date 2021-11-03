---
title: 사용자 지정 NER 데이터 서식
titleSuffix: Azure Cognitive Services
description: 사용자 지정 NER에서 허용하는 데이터 서식에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: de37751b3d814110b5d37f1b7c84d41278b2bea9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101756"
---
# <a name="data-formats-accepted-by-custom-ner"></a>사용자 지정 NER에서 허용되는 데이터 서식

모델에서 학습을 위해 데이터를 사용하는 경우 데이터가 특정 형식일 것으로 예상합니다. Language Studio에서 데이터에 태그를 지정하면 이 문서에 설명된 JSON 형식으로 변환됩니다. 파일에 수동으로 태그를 지정할 수도 있습니다.


## <a name="json-file-format"></a>JSON 파일 형식

엔터티에 태그를 지정하면 태그가 다음 JSON 형식으로 저장됩니다. 태그 파일을 업로드하는 경우 동일한 형식을 따라야 합니다.

```json
{
    //List of entity names. Their index within this array is used as an ID. 
    "entityNames": [
        "entity_name1",
        "entity_name2"
    ],
    "documents": "path_to_document", //Relative file path to get the text.
    "culture": "en-US", //Standard culture strings supported by CultureInfo.
    "entities": [
        {
            "regionStart": 0,
            "regionLength": 69,
            "labels": [
                {
                    "entity": 0, // Index of the entity in the "entityNames" array. Positions are relative to the original text (not bounding box)
                    "start": 4,
                    "length": 10
                },
                {
                    "entity": 1,
                    "start": 18,
                    "length": 11
                }
            ]
        }
    ]    
}
```

다음 목록은 위 샘플의 다양한 JSON 속성을 설명합니다.

* `entityNames`: 엔터티 이름의 배열입니다. 배열 내 엔터티의 인덱스가 ID로 사용됩니다.
* `documents`: 태그가 지정된 문서의 배열입니다.
  * `location`: JSON 파일과 관련된 문서의 경로입니다. 예를 들어, 태그 파일 `file.txt`과 동일한 수준에 있는 문서, 한 디렉터리 수준 `dir1/file.txt` 안에 있는 문서의 경우.
  * `culture`: 문서의 문화/언어입니다. <!-- See [language support](../language-support.md) for more information. -->
  * `entities`: 엔터티 인식 태그를 지정합니다.
    * `regionStart`: 텍스트 시작의 포함 문자 위치.
    * `regionLength`: UTF16 문자로 나타낸 경계 상자의 길이입니다. 학습은 이 영역의 데이터만 고려하므로 태그가 지정된 파일인 경우 `regionStart`을 0으로 설정하고 `regionLength`를 파일의 마지막 문자의 마지막 인덱스로 설정합니다. 태그가 없는 파일의 일부로 영역을 정의하여 학습에 음성 샘플을 도입하려는 경우 이 영역을 설정할 수도 있습니다.

    * `labels`: 경계 상자 내에서 발생하는 모든 태그입니다.
      * `entity`: `entityNames` 배열에 있는 엔터티의 인덱스입니다.
      * `start`: 문서 텍스트에서 태그 시작의 포함 문자 위치입니다. 이는 경계 상자와 관련이 없습니다.
      * `length`: UTF16 문자로 나타낸 태그의 길이입니다.

## <a name="next-steps"></a>다음 단계

데이터 태그 지정에 대한 자세한 내용은 [방법 도움말](../how-to/tag-data.md)을 참조하세요. 데이터 태그 지정을 완료하면 [모델을 학습](../how-to/train-model.md)할 수 있습니다.  
