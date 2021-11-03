---
title: QnA Maker 포털에서 질문 및 답변 추가
description: 이 문서에서는 질문에 대한 올바른 답변을 찾을 수 있도록 메타데이터가 포함된 질문 및 답변 쌍을 추가하는 방법을 보여줍니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/26/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9f7d1c7238e742ab8d04cf349bd8560db861b801
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080710"
---
# <a name="add-questions-and-answer-with-qna-maker-portal"></a>QnA Maker 포털을 사용하여 질문 및 답변 추가

기술 자료가 생성되면 메타데이터가 포함된 QnA(질문 및 답변) 쌍을 추가하여 답변을 필터링합니다. 다음 테이블의 질문은 Azure 서비스 제한에 대한 것이지만 각각 다른 Azure 검색 서비스와 관련이 있습니다.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

<a name="qna-table"></a>

|Pair|질문|Answer|메타데이터|
|--|--|--|--|
|#1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../concepts/azure-resources.md) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|#2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

메타데이터가 QnA 쌍에 추가되면 클라이언트 애플리케이션에서 다음을 수행할 수 있습니다.

* 특정 메타데이터와만 일치하는 답변을 요청합니다.
* 모든 답변을 받지만 각 답변의 메타데이터에 따라 해당 답변을 사후 처리합니다.


## <a name="prerequisites"></a>사전 요구 사항

* [이전 빠른 시작](./create-publish-knowledge-base.md) 완료

## <a name="sign-in-to-the-qna-maker-portal"></a>QnA Maker 포털에 로그인

1. [QnA Maker 포털](https://www.qnamaker.ai)에 로그인합니다.

1. [이전 빠른 시작](./create-publish-knowledge-base.md)에서 기존 기술 자료를 선택합니다.

## <a name="add-additional-alternatively-phrased-questions"></a>대체 구문이 추가된 질문 추가

현재 기술 자료에는 QnA Maker 문제 해결 QnA 쌍이 있습니다. 이러한 쌍은 만들기 프로세스 중에 URL이 기술 자료에 추가될 때 만들어졌습니다.

이 URL을 가져올 때 하나의 답변이 있는 하나의 질문만 만들어졌습니다. 이 절차에서는 추가 질문을 추가합니다.

1. **편집** 페이지에서 질문 및 답변 쌍 위에 있는 검색 텍스트 상자를 사용하여 `How large a knowledge base can I create?` 질문을 찾습니다.

1. **질문** 열에서 **+ 대체 구문 추가** 를 선택한 다음, 다음 표에 제공된 각각의 새 구문을 추가합니다.

    |대체 구문|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. **저장 및 학습** 을 선택하여 기술 자료를 다시 학습시킵니다.

1. **테스트** 를 선택한 다음, 새 대체 구문 중 하나에 가깝지만 정확히 동일한 단어가 아닌 질문을 입력합니다.

    `What GB size can a knowledge base be?`

    올바른 답은 markdown 형식으로 반환됩니다.

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../concepts/azure-resources.md) for more details.`

    반환된 답변 아래에서 **검사** 를 선택하면 질문에 부합하지만 동일한 높은 수준으로 신뢰할 수 없는 더 많은 답변이 표시될 수 있습니다.

    대체 구문의 가능한 모든 조합을 추가하지 않습니다. QnA Maker의 [활성 학습](../how-to/improve-knowledge-base.md)을 켜면 기술 자료에서 사용자의 요구를 충족하는 데 가장 적합한 대체 구문을 찾을 수 있습니다.

1. **테스트** 를 다시 선택하여 테스트 창을 닫습니다.

## <a name="add-metadata-to-filter-the-answers"></a>답변을 필터링하기 위한 메타데이터 추가

메타데이터가 질문 및 답변 쌍에 추가되면 클라이언트 애플리케이션에서 필터링된 답변을 요청할 수 있습니다. 이 필터는 [첫 번째 및 두 번째 순위 매기기](../concepts/query-knowledge-base.md#ranker-process)를 적용하기 전에 적용됩니다.

1. [이 빠른 시작의 첫 번째 테이블](#qna-table)에서 메타데이터 없이 두 번째 질문 및 답변 쌍을 추가한 후, 다음 단계를 계속 진행합니다.

1. **보기 옵션** 을 선택한 다음, **메타데이터 표시** 를 선택합니다.

1. 방금 추가한 QnA 쌍에 대해 **메타데이터 태그 추가** 를 선택한 다음, `service` 이름과 `search` 값을 추가합니다. `service:search`와 같이 표시됩니다.

1. 이름이 `link_in_answer`이고 값이 `false`인 다른 메타데이터 태그를 추가합니다. `link_in_answer:false`와 같이 표시됩니다.

1. 테이블에서 첫 번째 답변(`How large a knowledge base can I create?`)을 검색합니다.

1. 동일한 두 메타데이터 태그에 대한 메타데이터 쌍을 추가합니다.

    `link_in_answer` : `true`<br>
    `service`: `qna_maker`

    이제 서로 다른 값이 있는 동일한 메타데이터 태그가 포함된 두 개의 질문이 있습니다.

1. **저장 및 학습** 을 선택하여 기술 자료를 다시 학습시킵니다.

1. 위쪽 메뉴에서 **게시** 를 선택하여 게시 페이지로 이동합니다.
1. **게시** 단추를 선택하여 현재 기술 자료를 엔드포인트에 게시합니다.
1. 기술 자료가 게시된 후 다음 빠른 시작을 계속 진행하여 기술 자료에서 답변을 생성하는 방법을 알아봅니다.

## <a name="what-did-you-accomplish"></a>수행했던 작업은 무엇인가요?

기술 자료를 편집하여 추가 질문을 지원하고, 답변 또는 답변이 반환된 후 최상위 답변이나 후 처리를 검색하는 동안 필터링을 지원하기 위해 이름/값 쌍을 제공했습니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 빠른 시작을 계속 진행하지 않을 경우 Azure Portal에서 QnA Maker 및 Bot 프레임워크 리소스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Postman 또는 cURL을 사용하여 답변 가져오기](get-answer-from-knowledge-base-using-url-tool.md)
