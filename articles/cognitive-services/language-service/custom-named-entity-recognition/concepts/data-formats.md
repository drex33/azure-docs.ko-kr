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
ms.openlocfilehash: 2583aeb8efe3b60003a9e414b9c9a2cba737bc93
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132863863"
---
# <a name="data-formats-accepted-by-custom-ner"></a>사용자 지정 NER에서 허용되는 데이터 서식

모델에서 학습을 위해 데이터를 사용하는 경우 데이터가 특정 형식일 것으로 예상합니다. Language Studio에서 데이터에 태그를 지정하면 이 문서에 설명된 JSON 형식으로 변환됩니다. 파일에 수동으로 태그를 지정할 수도 있습니다.


## <a name="json-file-format"></a>JSON 파일 형식

엔터티에 태그를 지정하면 태그가 다음 JSON 형식으로 저장됩니다. 태그 파일을 업로드하는 경우 동일한 형식을 따라야 합니다.

```json
{
    "extractors": [
        {
            "name": "Entity1"
        },
        {
            "name": "Entity2"
        }
    ],
    "documents": [
        {
            "location": "file1.txt",
            "language": "en-us",
            "extractors": [
                {
                    "regionOffset": 0,
                    "regionLength": 5129,
                    "labels": [
                        {
                            "extractorName": "Entity1",
                            "offset": 77,
                            "length": 10
                        },
                        {
                            "extractorName": "Entity2",
                            "offset": 3062,
                            "length": 8
                        }
                    ]
                }
            ]
        },
        {
            "location": "file2.txt",
            "language": "en-us",
            "extractors": [
                {
                    "regionOffset": 0,
                    "regionLength": 6873,
                    "labels": [
                        {
                            "extractorName": "Entity2",
                            "offset": 60,
                            "length": 7
                        },
                        {
                            "extractorName": "Entity1",
                            "offset": 2805,
                            "length": 10
                        }
                    ]
                }
            ]
        }
    ]
}
```

### <a name="data-description"></a>데이터 설명

* `extractors`: 데이터에 대한 추출기의 배열입니다. 각 추출기는 데이터에서 추출하려는 엔터티 중 하나를 나타냅니다.
* `documents`: 태그가 지정된 문서의 배열입니다.
  * `location`: 파일의 경로입니다. 파일은 스토리지 컨테이너의 루트에 있어야 합니다.
  * `language`: 파일의 언어입니다. [지원되는 문화권 언어](../language-support.md) 중 하나를 사용합니다.
  * `extractors`: 파일에서 추출할 추출기 개체의 배열입니다.
    * `regionOffset`: 텍스트 시작의 포함 문자 위치.
    * `regionLength`: UTF16 문자로 나타낸 경계 상자의 길이입니다. 학습은 이 지역의 데이터만 고려합니다.
    * `labels`: 지정된 영역 내에 태그가 지정된 모든 엔터티의 배열입니다.
      * `extractorName`: 추출할 엔터티의 형식입니다.
      * `offset`: 엔터티 시작의 포함 문자 위치입니다. 이는 경계 상자와 관련이 없습니다.
      * `length`: UTF16 문자로 나타낸 엔터티의 길이입니다.

## <a name="next-steps"></a>다음 단계

데이터 태그 지정에 대한 자세한 내용은 [방법 도움말](../how-to/tag-data.md)을 참조하세요. 데이터 태그 지정을 완료하면 [모델을 학습](../how-to/train-model.md)할 수 있습니다.  
