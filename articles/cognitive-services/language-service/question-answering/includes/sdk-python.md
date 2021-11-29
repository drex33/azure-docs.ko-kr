---
title: '빠른 시작: Python용 질문 답변 클라이언트 라이브러리'
description: 이 빠른 시작에서는 Python용 질문 답변 클라이언트 라이브러리를 시작하는 방법을 보여 줍니다.
ms.topic: include
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/11/2021
ms.openlocfilehash: 47f430fcb77bda090edf2f931d78c58cfc1c1755
ms.sourcegitcommit: fc912bf0540585f44c09c6d63728c05c5dda558b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133136304"
---
이 빠른 시작을 Python용 질문 답변 클라이언트 라이브러리에 사용하여 다음을 수행합니다.

* 기술 자료에서 답변을 얻습니다.
* 질문과 함께 보내는 텍스트 본문에서 답변을 얻습니다.
* 질문에 대한 답변의 신뢰도 점수를 얻습니다.

[API 참조 설명서][questionanswering_refdocs] | [소스 코드][questionanswering_client_src] | [패키지(PyPI)][questionanswering_pypi_package] | [Python 샘플][questionanswering_samples] |

[questionanswering_client_class]: https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient
[questionanswering_client_src]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cognitivelanguage/azure-ai-language-questionanswering/
[questionanswering_pypi_package]: https://pypi.org/project/azure-ai-language-questionanswering/
[questionanswering_refdocs]: https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html
[questionanswering_samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/cognitivelanguage/azure-ai-language-questionanswering/samples/README.md

## <a name="prerequisites"></a>필수 구성 요소

> [!NOTE]
> 이 설명서는 최신 릴리스에는 적용되지 않습니다. 최신 릴리스에서 Python API를 사용하는 방법에 대한 자세한 내용은 [Python 빠른 시작에 대한 답변](https://docs.microsoft.com/azure/cognitive-services/language-service/question-answering/quickstart/sdk?pivots=programming-language-python)을 참조합니다.

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* 질문 답변 - API 키와 엔드포인트를 생성하기 위해 사용자 지정 질문 답변 기능이 사용하도록 설정된 [언어 리소스](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics)가 필요합니다.
    * 언어 리소스가 배포된 후 **리소스로 이동** 을 선택합니다. API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
* 쿼리할 기존 기술 자료. 기술 자료가 설치되지 않은 경우 [**Language Studio 빠른 시작**](../quickstart/sdk.md)의 지침을 따를 수 있습니다. 또는 이 [Surface 사용자 가이드 URL](https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf)을 데이터 원본으로 사용하는 기술 자료를 추가합니다.

## <a name="setting-up"></a>설치

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

Python을 설치한 후, 다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install azure-ai-language-questionanswering
```

## <a name="query-a-knowledge-base"></a>기술 자료 쿼리

### <a name="generate-an-answer-from-a-knowledge-base"></a>기술 자료에서 답변 생성

아래 예제에서는 질문에 대한 답변을 얻기 위해 [get_answers](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient.get_answers)를 사용하여 기술 자료를 쿼리할 수 있습니다. 이 코드는 전용 .py 파일 또는 [Jupyter Notebook/Lab](https://jupyter.org/)의 셀에 복사할 수 있습니다.

아래 코드를 업데이트하고, 다음 변수에 대한 고유한 값을 제공해야 합니다.

|변수 이름 | 값 |
|--------------------------|-------------|
| `endpoint`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트 예제는 `https://southcentralus.api.cognitive.microsoft.com/`입니다.|
| `credential` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 가동 중지 시간이 0인 안전한 키 회전을 위해 항상 두 개의 유효한 키가 있어야 합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `knowledge_base_project` | 질문 답변 프로젝트의 이름입니다.|
| `deployment`             | 두 개의 가능한 값은 `test` 및 `production`입니다. `production`은 **Language Studio** > **질문 답변** > **기술 자료 배포** 에서 기술 자료를 배포했는지 여부에 따라 달라집니다.|

```python
from azure.core.credentials import AzureKeyCredential
from azure.ai.language.questionanswering import QuestionAnsweringClient

endpoint = "https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/"
credential = AzureKeyCredential("{YOUR-LANGUAGE-RESOURCE-KEY}")
knowledge_base_project = "{YOUR-PROJECT-NAME}"
deployment = "production"

def main():
    client = QuestionAnsweringClient(endpoint, credential)
    with client:
        question="How much battery life do I have left?"
        output = client.get_answers(
            question = question,
            project_name=knowledge_base_project,
            deployment_name=deployment
        )
    print("Q: {}".format(question))
    print("A: {}".format(output.answers[0].answer))

if __name__ == '__main__':
    main()
```

이 예제의 변수는 하드 코딩되어 있습니다. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 예를 들어 [Azure Key Vault](../../../../key-vault/general/overview.md)는 보안 키 스토리지를 제공합니다.

위의 코드를 실행할 때 필수 구성 요소의 데이터 원본을 사용하는 경우 다음과 같은 답변이 표시됩니다.

```
Q: How much battery life do I have left?
A: If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
```

질문 답변에서 이 답변이 올바른 응답이라고 확신하는 정도에 대한 정보를 확인하려면 추가 출력 문을 기존 출력 문 아래에 추가합니다.

```python
print("Q: {}".format(question))
print("A: {}".format(output.answers[0].answer))
print("Confidence Score: {}".format(output.answers[0].confidence_score)) # add this line 
```

이제 신뢰도 점수가 포함된 결과가 표시됩니다.

```
Q: How much battery life do I have left?
A: If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
Confidence Score: 0.9185
```

신뢰도 점수는 0과 1 사이의 값을 반환합니다. 이를 백분율로 생각하여 100을 곱합니다. 이 경우 0.9185의 신뢰도 점수는 질문 답변에서 기술 자료를 기반으로 한 질문에 대한 올바른 답변이라고 91.85% 확신한다는 것을 의미합니다.

신뢰도 점수가 특정 임계값 미만인 답변을 제외하려면 `confidence_threshold` 매개 변수를 추가하도록 [AnswerOptions](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.models.html#azure.ai.language.questionanswering.models.AnswersOptions)를 수정할 수 있습니다.

```python
        output = client.get_answers(
            confidence_threshold = 0.95, #add this line
            question = question,
            project_name=knowledge_base_project,
            deployment_name=deployment
        )
```

이전 코드 실행에서 신뢰도 점수가 `.9185`임을 알고 있으므로 임계값을 `.95`로 설정하면 [기본 답변](../how-to/change-default-answer.md)이 반환됩니다.

```
Q: How much battery life do I have left?
A: No good match found in KB
Confidence Score: 0.0
```

## <a name="query-text-without-a-knowledge-base"></a>기술 자료 없이 텍스트 쿼리

[get_answers_from_text](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient.get_answers_from_text)를 사용하여 기술 자료 없이 질문 답변을 사용할 수도 있습니다. 이 경우 요청이 보내지면 답변을 검색하려는 질문 및 관련 텍스트 레코드 모두를 질문 답변에 제공합니다.

다음 예제에서는 `endpoint` 및 `credential`에 대한 변수만 수정하면 됩니다.

```python
import os
from azure.core.credentials import AzureKeyCredential
from azure.ai.language.questionanswering import QuestionAnsweringClient
from azure.ai.language.questionanswering import models as qna

endpoint = "https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/"
credential = AzureKeyCredential("YOUR-LANGUAGE-RESOURCE-KEY")

def main():
    client = QuestionAnsweringClient(endpoint, credential)
    with client:
        question="How long does it takes to charge a surface?"
        input = qna.AnswersFromTextOptions(
            question=question,
            text_documents=[
                "Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. " +
                "It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it.",
                "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. " +
                "The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface.",
            ]
        )


        output = client.get_answers_from_text(input)

    best_answer = [a for a in output.answers if a.confidence > 0.9][0]
    print(u"Q: {}".format(input.question))
    print(u"A: {}".format(best_answer.answer))
    print("Confidence Score: {}".format(output.answers[0].confidence))

if __name__ == '__main__':
    main()
```

이 코드는 전용 .py 파일 또는 [Jupyter Notebook/Lab](https://jupyter.org/)의 새 셀에 복사할 수 있습니다. 이 예제에서 반환하는 결과는 다음과 같습니다.

```
Q: How long does it takes to charge surface?
A: Power and charging. It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it.
Confidence Score: 0.9254655838012695
```

이 경우 모든 답변을 반복하고 0.9보다 큰 가장 높은 신뢰도 점수를 가진 답변만 반환합니다. [get_answers_from_text](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.html#azure.ai.language.questionanswering.QuestionAnsweringClient.get_answers_from_text)에서 사용할 수 있는 옵션에 대해 자세히 알아보려면 [AnswersFromTextOptions 매개 변수](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-language-questionanswering/1.0.0/azure.ai.language.questionanswering.models.html#azure.ai.language.questionanswering.models.AnswersFromTextOptions)를 검토하세요.