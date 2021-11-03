---
author: aahill
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2b575c37bec045f63b4697a6ff87be4bcab7df6a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101178"
---
[참조 설명서](/python/api/azure-ai-textanalytics/azure.ai.textanalytics?preserve-view=true&view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics) | [패키지(PiPy)](https://pypi.org/project/azure-ai-textanalytics/5.1.0/) | [샘플](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/textanalytics/azure-ai-textanalytics/samples)


## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Azure 구독이 있으면 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="언어 리소스 만들기"  target="_blank">언어 리소스를 생성</a>하여 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* 분석 기능을 사용하려면 표준(S) 가격 책정 계층을 사용하는 언어 리소스가 필요합니다.

## <a name="setting-up"></a>설치

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Python을 설치한 후, 다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install azure-ai-textanalytics==5.1.0
```

## <a name="code-example"></a>코드 예제

새 Python 파일을 만들고 아래 코드를 복사합니다. `key` 변수를 리소스의 키로 바꾸고 `endpoint` 변수를 리소스의 엔드포인트로 바꾸어야 합니다. 

[!INCLUDE [find the key and endpoint for a resource](../../../includes/find-azure-resource-info.md)]

```python
key = "paste-your-key-here"
endpoint = "paste-your-endpoint-here"

from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

# Authenticate the client using your key and endpoint 
def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()

# Example function for recognizing entities from text
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\tLength: \t", entity.length, "\tOffset: \t", entity.offset, "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

## <a name="output"></a>출력

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61   Length:          4      Offset:          18

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82   Length:          7      Offset:          26

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8    Length:          9      Offset:          34
```
