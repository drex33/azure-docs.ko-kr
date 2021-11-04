---
title: 사용자 지정 텍스트 분류 데이터 서식
titleSuffix: Azure Cognitive Services
description: 사용자 지정 엔터티 추출에서 허용되는 데이터 서식에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: f99dd2b0d540cad6fad605e264df4b6054240e35
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101517"
---
# <a name="accepted-data-formats"></a>허용되는 데이터 서식

모델에서 학습을 위해 데이터를 사용하는 경우 데이터가 특정 형식일 것으로 예상합니다. Language Studio에서 데이터에 태그를 지정하면 이 문서에 설명된 JSON 형식으로 변환됩니다. 파일에 수동으로 태그를 지정할 수도 있습니다.


## <a name="json-file-format"></a>JSON 파일 형식

태그 파일은 아래 `json` 형식이어야 합니다.

```json
{
    "classifiers": [
        {
            "name": "Class1"
        },
        {
            "name": "Class2"
        }
    ],
    "documents": [
        {
            "location": "doc1.txt",
            "language": "en-us",
            "classifiers": [
                {
                    "classifierName": "Class2"
                },
                {
                    "classifierName": "Class1"
                }
            ]
        }
    ]
}
```

### <a name="data-description"></a>데이터 설명

* `classifiers`: 데이터에 대한 분류자의 배열입니다. 각 분류기는 데이터에 태그를 지정하려는 클래스 중 하나를 나타냅니다.
* `documents`: 태그가 지정된 문서의 배열입니다. 예:
  * `location`: 태그가 포함된 JSON 파일의 경로입니다. 태그 파일은 스토리지 컨테이너의 루트에 있어야 합니다.
  * `language`: 문서의 언어입니다. [지원되는 문화권 언어](../language-support.md) 중 하나를 사용합니다.
  * `classifiers`: 문서에 할당된 분류자 개체의 배열입니다. 단일 분류 프로젝트에서 작업하는 경우 분류기는 하나만 있어야 합니다.

## <a name="next-steps"></a>다음 단계

데이터 태그 지정에 대한 자세한 내용은 [방법 도움말](../how-to/tag-data.md)을 참조하세요. 데이터 태그 지정을 완료하면 [모델을 학습](../how-to/train-model.md)할 수 있습니다.  
