---
title: 기술 자료 편집 - QnA Maker
description: QnA Maker는 사용하기 쉬운 편집 환경을 제공하여 기술 자료 콘텐츠를 관리할 수 있게 해줍니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/16/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1b67744b66eaf5563505f72b6b151f40ed44f602
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069238"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>기술 자료에서 QnA 쌍 편집

QnA Maker는 사용하기 쉬운 편집 환경을 제공하여 기술 자료 콘텐츠를 관리할 수 있게 해줍니다.

QnA 쌍은 파일 또는 URL과 같은 데이터 원본에서 추가되거나 편집 원본으로 추가됩니다. 편집 원본은 QnA 쌍이 QnA 포털에 수동으로 추가되었음을 나타냅니다. 모든 QnA 쌍을 편집할 수 있습니다.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

<a name="add-an-editorial-qna-set"></a>

## <a name="question-and-answer-pairs"></a>질문 및 대답 쌍

기술 자료는 질문 및 대답(QnA) 쌍으로 구성됩니다.  각 쌍에는 하나의 대답이 있으며, 한 쌍에는 해당 _대답_ 과 관련된 모든 정보가 포함됩니다. 대답은 데이터베이스 행 또는 데이터 구조 인스턴스와 비슷할 수 있습니다. 질문 및 대답(QnA) 쌍에 **필요한** 설정은 다음과 같습니다.

* **질문** - QnA Maker의 기계 학습에 사용되는 사용자 쿼리 텍스트로, 표현은 다르지만 대답이 같은 사용자 질문의 텍스트에 연결됩니다.
* **대답** - 쌍의 대답은 사용자 쿼리가 관련 질문과 일치할 경우 반환되는 대답입니다.

각 쌍은 **ID** 로 나타납니다.

쌍에 대한 **선택적** 설정에는 다음이 포함됩니다.

* **질문의 다른 형태** - 이를 통해 QnA Maker가 보다 다양한 질문 구문에 대한 올바른 대답을 반환할 수 있습니다.
* **메타데이터**: 메타데이터는 QnA 쌍과 연결된 태그이며 키-값 쌍으로 표현됩니다. 메타데이터 태그는 QnA 쌍을 필터링하고 쿼리 매칭이 수행되는 집합을 제한하는 데 사용됩니다.
* 멀티 턴 대화를 이어가는 데 사용되는 **멀티 턴 프롬프트**

![QnA Maker 기술 자료](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="add-an-editorial-qna-pair"></a>편집 QnA 쌍 추가

기술 자료를 채울 기존 콘텐츠가 없는 경우 편집자로서 QnA Maker 포털에 QnA 쌍을 추가할 수도 있습니다.

1. [QnA 포털](https://www.qnamaker.ai/)에 로그인한 다음, 기술 자료를 선택하여 QnA 쌍을 추가합니다.
1. 기술 자료의 **편집** 페이지에서 **QnA 쌍 추가** 를 선택하여 새 QnA 쌍을 추가합니다.

    > [!div class="mx-imgBorder"]
    > ![QnA 쌍 추가](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

1. 새 QnA 쌍 행에서 필요한 질문 및 답변 필드를 추가합니다. 다른 필드는 선택 사항입니다. 모든 필드는 언제든지 변경될 수 있습니다.

1. 필요에 따라 **[대체 구문](../Quickstarts/add-question-metadata-portal.md#add-additional-alternatively-phrased-questions)** 을 추가합니다. 대체 구문이란 원래 질문과 크게 다르지만 동일한 답을 제공해야 하는 모든 형태의 질문입니다.

    기술 자료가 게시되고 [활성 학습](use-active-learning.md) 기능이 설정되어 있는 경우에는 QnA Maker에서 사용자가 동의할 수 있는 대체 구문 옵션을 수집합니다. 이러한 선택 항목은 예측 정확도를 높이기 위해 선택됩니다.

1. 필요에 따라 **[메타데이터](../Quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)** 를 추가합니다. 메타데이터를 보려면 상황에 맞는 메뉴에서 **옵션 보기** 를 선택합니다. 메타데이터는 채팅 봇과 같은 클라이언트 애플리케이션에서 제공하는 응답에 대한 필터를 제공합니다.

1. 필요에 따라 **[후속 프롬프트](multiturn-conversation.md)** 를 추가합니다. 후속 프롬프트는 클라이언트 애플리케이션에 사용자에게 제공할 추가 대화 경로를 제공합니다.

1. **저장 및 학습** 을 선택하여 새 QnA 쌍을 포함하는 예측을 확인합니다.

## <a name="rich-text-editing-for-answer"></a>답변에 대한 서식 있는 텍스트 편집

답변 텍스트의 서식 있는 텍스트 편집 기능을 사용하면 간단한 도구 모음에서 markdown 스타일링을 제공합니다.

1. 답변에 대한 텍스트 영역을 선택하면 서식 있는 텍스트 편집기 도구 모음이 QnA 쌍 행에 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![QnA 쌍 행의 질문과 답변이 포함된 서식 있는 텍스트 편집기의 스크린샷](../media/qnamaker-how-to-edit-kb/rich-text-control-qna-pair-row.png)

    답변에 이미 있는 모든 텍스트는 사용자가 봇에서 볼 때 올바르게 표시됩니다.

1. 텍스트를 편집합니다. 서식 있는 텍스트 편집 도구 모음에서 서식 지정 기능을 선택하거나 토글 기능을 사용하여 markdown 구문으로 전환합니다.

    > [!div class="mx-imgBorder"]
    > ![서식 있는 텍스트 편집기를 사용하여 텍스트를 작성하고 서식을 지정하고 markdown으로 저장합니다.](../media/qnamaker-how-to-edit-kb/rich-text-display-image.png)

    |서식 있는 텍스트 편집기 기능|바로 가기 키|
    |--|--|
    |서식 있는 텍스트 편집기와 markdown 사이를 전환합니다. `</>`|Ctrl+M|
    |굵게 **B**|CTR+LB|
    |기울임꼴, 기울임꼴 **_I_** 로 표시|CTRL+I|
    |정렬되지 않은 목록||
    |정렬된 목록||
    |단락 스타일||
    |이미지 - 공용 URL에서 사용할 수 있는 이미지를 추가합니다.|Ctrl+G|
    |공개적으로 사용할 수 있는 URL에 대한 링크를 추가합니다.|Ctrl+K|
    |이모티콘 - 선택된 이모티콘에서 추가합니다.|Ctrl+E|
    |고급 메뉴 - 실행 취소|CTRL+Z|
    |고급 메뉴 - 다시 실행|Ctrl+Y|

1. 서식 있는 텍스트 도구 모음의 이미지 아이콘을 사용하여 답변에 이미지를 추가합니다. 내부 편집기에는 이미지에 대해 공개적으로 액세스할 수 있는 이미지 URL과 대체 텍스트가 필요합니다.


    > [!div class="mx-imgBorder"]
    > ![스크린샷은 입력한 이미지에 대해 공개적으로 액세스할 수 있는 이미지 URL과 대체 텍스트가 있는 내부 편집기를 표시합니다.](../media/qnamaker-how-to-edit-kb/add-image-url-alternate-text.png)

1. 답변에서 텍스트를 선택한 다음, 도구 모음에서 링크 아이콘을 선택하거나 도구 모음에서 링크 아이콘을 선택한 다음, 새 텍스트와 URL을 입력하여 URL에 대한 링크를 추가합니다.

    > [!div class="mx-imgBorder"]
    > ![서식 있는 텍스트 편집기를 사용하여 공개적으로 액세스할 수 있는 이미지와 해당 ALT 텍스트를 추가합니다.](../media/qnamaker-how-to-edit-kb/add-link-to-answer-rich-text-editor.png)

## <a name="edit-a-qna-pair"></a>QnA 쌍 편집

QnA 쌍의 모든 필드는 원래 데이터 원본에 관계 없이 편집할 수 있습니다. 일부 필드는 상황에 맞는 도구 모음에 있는 현재 **뷰 옵션** 설정 때문에 표시되지 않을 수 있습니다.

## <a name="delete-a-qna-pair"></a>QnA 쌍 삭제

QnA를 삭제하려면 QnA 행의 맨 오른쪽에 있는 **삭제** 아이콘을 클릭합니다. 영구 작업입니다. 실행을 취소할 수 없습니다. 쌍을 삭제하기 전에 **게시** 페이지에서 KB를 내보내는 것이 좋습니다.

![QnA 쌍 삭제](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>QnA 쌍 ID 찾기

QnA 쌍 ID를 찾아야 하는 경우 다음 두 위치에서 찾을 수 있습니다.

* 관심이 있는 QnA 쌍 행의 삭제 아이콘을 마우스로 가리킵니다. 가리키기 텍스트는 QnA 쌍 ID를 포함합니다.
* 기술 자료를 내보냅니다. 기술 자료의 각 QnA 쌍은 QnA 쌍 ID를 포함합니다.

## <a name="add-alternate-questions"></a>대체 질문 추가

기존 QnA 쌍에 대체 질문을 추가하여 사용자 쿼리와 일치할 가능성을 높입니다.

![대체 질문 추가](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>QnA 쌍 연결

QnA 쌍 연결은 [후속 프롬프트](multiturn-conversation.md)와 함께 제공됩니다. 이는 기술 자료 수준에서 관리되는 QnA 쌍 간의 논리적 연결입니다. QnA Maker 포털에서 후속 프롬프트를 편집할 수 있습니다.

답변의 메타데이터에 QnA 쌍을 연결할 수 없습니다.

## <a name="add-metadata"></a>메타데이터 추가

먼저 **보기 옵션** 을 선택한 다음, **메타데이터 표시** 를 선택하여 메타데이터 쌍을 추가합니다. 메타데이터 열이 표시됩니다. 그런 다음, **+** 서명을 선택하여 메타데이터 쌍을 추가합니다. 이 쌍은 하나의 키와 하나의 값으로 구성됩니다.

메타데이터에 대한 QnA Maker 포털 빠른 시작에서 메타데이터에 대해 자세히 알아보세요.
* [제작 - QnA 쌍에 메타데이터 추가](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [쿼리 예측 - 메타데이터를 통한 응답 필터링](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

## <a name="save-changes-to-the-qna-pairs"></a>QnA 쌍에 대한 변경 내용 저장

변경 내용이 손실되지 않도록 하려면 편집한 후 주기적으로 **저장 및 학습** 을 선택합니다.

![메타데이터 추가](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>서식 있는 텍스트 편집 및 markdown 사용 시기

답변의 [서식 있는 텍스트 편집](#add-an-editorial-qna-set)을 사용하면 작성자로서 서식 도구 모음을 사용하여 텍스트를 신속하게 선택하고 형식을 지정할 수 있습니다.

[Markdown](../reference-markdown-format.md)은 CI/CD 파이프라인의 일부로 가져오거나 [일괄 처리 테스트](../index.yml)를 위해 가져올 기술 자료를 만드는 데 콘텐츠를 자동으로 생성해야 하는 경우 더 유용한 도구입니다.

## <a name="editing-your-knowledge-base-locally"></a>로컬로 기술 자료 편집

기술 자료를 만든 후에는 로컬 파일을 통해 내보냈다가 다시 가져오는 것보다는 [QnA Maker 포털](https://qnamaker.ai)에서 기술 자료 텍스트를 편집하는 것이 좋습니다. 그러나 로컬로 기술 자료를 편집해야 하는 경우가 있을 수 있습니다.

**설정** 페이지에서 기술 자료를 내보낸 후 Microsoft Excel을 사용하여 기술 자료를 편집합니다. 다른 애플리케이션을 사용하여 내보낸 파일을 편집하도록 선택하면 TSV와 완전히 호환되지 않기 때문에 애플리케이션에서 구문 오류가 발생할 수 있습니다. Microsoft Excel의 TSV 파일의 경우 일반적으로 서식 지정 오류가 발생하지 않습니다.

편집을 완료했으면 **설정** 페이지를 통해 TSV 파일을 다시 가져옵니다. 그러면 현재 기술 자료가 가져온 기술 자료로 완전히 바뀝니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료에 대한 공동 작업](../index.yml)

* [QnA Maker에서 사용하는 Azure 리소스 관리](set-up-qnamaker-service-azure.md)
