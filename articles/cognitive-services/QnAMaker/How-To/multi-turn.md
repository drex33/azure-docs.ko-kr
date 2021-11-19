---
title: 멀티 턴 대화 - QnA Maker
description: 프롬프트와 컨텍스트를 사용하여 한 질문에서 다른 질문으로 봇의 멀티 턴을 관리합니다. 멀티 턴은 이전 질문의 컨텍스트가 다음 질문과 답변에 영향을 주는 앞뒤로 대화를 하는 기능입니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.author: mbullwin
author: mrbullwinkle
ms.topic: conceptual
ms.date: 11/18/2021
ms.openlocfilehash: 6930823f1298cc442a4d1f96e0eae10ed90b0cd6
ms.sourcegitcommit: 11ca7ba5a017429c22a6b0bc02acb70b83a2984a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132817910"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>추가 작업 프롬프트를 사용하여 여러 대화 설정 만들기

후속 프롬프트와 컨텍스트를 사용하여 한 질문에서 다른 질문으로 봇의 _멀티 턴_ 을 관리합니다.

멀티 턴의 작동 방식을 확인하려면 다음 데모 비디오를 참조하세요.

[![QnA Maker의 멀티 턴 대화](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>멀티 턴 대화란?

한 번에 답할 수 없는 질문들이 있습니다. 클라이언트 애플리케이션(챗봇) 대화를 디자인할 때 사용자는 올바른 대답을 결정하기 위해 필터링하거나 구체화해야 하는 질문을 할 수 있습니다. 사용자에게 *후속 프롬프트* 를 표시하여 질문을 통해 이 흐름을 만들 수 있습니다.

사용자가 질문을 하면 QnA Maker는 답변 _및_ 후속 프롬프트를 반환합니다. 이 응답을 통해 후속 질문을 선택 사항으로 제시할 수 있습니다.

> [!CAUTION]
> 멀티 턴 프롬프트는 FAQ 문서에서 추출되지 않습니다. 멀티 턴 추출이 필요한 경우 QnA 쌍을 FAQ로 지정하는 물음표를 제거합니다.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>챗봇을 사용한 멀티 턴 대화의 예

멀티 턴을 사용하면 다음 이미지와 같이 챗봇이 사용자와의 대화를 관리하여 최종 답변을 결정합니다.

![대화를 통해 사용자를 안내하는 프롬프트가 있는 멀티 턴 대화 상자](../media/conversational-context/conversation-in-bot.png)

앞의 이미지에서 사용자는 **My account(내 계정)** 를 입력하여 대화를 시작했습니다. 기술 자료에는 3개의 연결된 질문 및 답변 쌍이 있습니다. 답변을 구체화하기 위해 사용자는 기술 자료의 3가지 선택 항목 중 하나를 선택합니다. 질문(#1)에는 3가지 옵션(#2)으로 챗봇에 표시되는 3가지 후속 프롬프트가 있습니다.

사용자가 옵션(#3)을 선택하면 다음의 구체화 옵션 목록(#4)이 표시됩니다. 사용자가 올바른 최종 답변(#6)을 결정할 때까지 이 시퀀스는 계속됩니다(#5).

### <a name="use-multi-turn-in-a-bot"></a>봇에서 멀티 턴 사용

KB를 게시 한 후에는 **Bot 만들기** 단추를 선택 하 여 QnA Maker 봇을 Azure bot service에 배포할 수 있습니다. 봇에 사용하도록 설정한 채팅 클라이언트에 프롬프트가 나타납니다.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>문서의 구조체에서 멀티 턴 대화 만들기

기술 자료를 만들 때 **Populate your KB(KB 채우기)** 섹션에 **Enable multi-turn extraction from URLs, .pdf or .docx files(URL, .pdf 또는 .docx 파일에서 멀티 턴 추출 사용)** 확인란이 표시됩니다.

![멀티 턴 추출을 사용하도록 설정하는 확인란](../media/conversational-context/enable-multi-turn.png)

이 옵션을 선택하면 QnA Maker가 문서 구조체에 있는 계층 구조를 추출합니다. 계층 구조는 후속 프롬프트로 변환되고 계층 구조의 루트는 부모 QnA 역할을 합니다. 일부 문서에서 계층의 루트에는 답변으로 사용할 수 있는 콘텐츠가 없습니다. ' 기본 대답 텍스트 '를 제공 하 여 해당 계층을 추출 하는 대체 대답 텍스트로 사용할 수 있습니다.

멀티 턴 구조체는 URL, PDF 파일 또는 .DOCX 파일에서만 유추할 수 있습니다. 구조체의 예는 [Microsoft Surface 사용 설명서 PDF 파일](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)의 이미지를 참조하세요.

:::image type="content" source="../media/conversational-context/import-file-with-conversational-structure.png" alt-text="스크린샷은 사용 설명서의 구조체 예를 보여줍니다." lightbox="../media/conversational-context/import-file-with-conversational-structure.png":::

### <a name="building-your-own-multi-turn-document"></a>사용자 고유의 멀티 턴 문서 빌드

다중 턴 문서를 만드는 경우에는 다음 지침을 염두에 두어야 합니다.

* 제목 및 부제목을 사용하여 계층 구조를 나타냅니다. 예를 들어 QnA 및 h2를 사용 하 여 프롬프트로 사용 해야 하는 QnA를 나타내려면 h1을 사용 합니다. 작은 제목 크기를 사용하여 후속 계층 구조를 나타냅니다. 스타일, 색 또는 기타 메커니즘을 사용하여 문서의 구조체를 암시하지 마세요. QnA Maker는 멀티 턴 프롬프트를 추출하지 않습니다.

* 제목의 첫 번째 문자는 대문자여야 합니다.

* 제목 끝에 물음표(`?`)를 사용하지 않습니다.

* [샘플 문서](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)를 예로 사용하여 사용자 고유의 멀티 턴 문서를 만들 수 있습니다.

### <a name="adding-files-to-a-multi-turn-kb"></a>멀티 턴 KB에 파일 추가

계층적 문서를 추가하면 QnA Maker가 구조체에서 후속 프롬프트를 결정하여 대화 흐름을 만듭니다.

1. QnA Maker에서 **url에서 다중 수준 추출 사용, .pdf 또는 .docx 파일** 사용으로 만든 기존 기술 자료를 선택 합니다.
1. **설정** 페이지로 이동하여 추가할 파일 또는 URL을 선택합니다.
1. 기술 자료를 **저장하고 학습** 합니다.

> [!Caution]
> 내보낸 TSV 또는 XLS 멀티 턴 기술 자료 파일을 새 기술 자료 또는 빈 기술 자료의 데이터 원본으로 사용할 수 없습니다. 내보낸 멀티 턴 프롬프트를 기술 자료에 추가하려면 QnA Maker 포털의 **설정** 페이지에서 해당 파일 형식을 **가져와야** 합니다.

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Create API를 사용하여 멀티 턴 프롬프트가 있는 기술 자료 만들기

[QnA Maker Create API](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)를 사용하여 멀티 턴 프롬프트가 있는 기술 사례를 만들 수 있습니다. 프롬프트가 `context` 속성의 `prompts` 배열에 추가됩니다.

## <a name="show-questions-and-answers-with-context"></a>상황별 질문 및 답변 표시

표시 되는 질문 및 답변 쌍을 컨텍스트 대화를 포함 하는 쌍 으로만 줄입니다.

**보기 옵션** 을 선택한 다음, **Show context(컨텍스트 표시)** 를 선택합니다. 목록에 후속 프롬프트가 포함된 질문 및 답변 쌍이 표시됩니다.

![상황별 대화로 질문 및 답변 쌍 필터링](../media/conversational-context/filter-question-and-answers-by-context.png)

멀티 턴 컨텍스트가 첫 번째 열에 표시됩니다.

:::image type="content" source="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png" alt-text="컨텍스트 섹션이 강조 표시된 스크린샷" lightbox="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png":::

앞의 이미지에서 **#1** 은 현재 질문을 나타내는 열의 굵은 텍스트를 나타냅니다. 부모 질문은 행의 최상위 항목입니다. 아래 질문은 연결된 질문 및 답변 쌍입니다. 이러한 항목은 선택 가능하므로 다른 컨텍스트 항목으로 즉시 이동할 수 있습니다.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>기존 질문 및 답변 쌍을 후속 프롬프트로 추가

원래 질문인 **My account(내 계정)** 에는 **Accounts and signing in(계정 및 로그인)** 등의 후속 프롬프트가 있습니다.

!["Accounts and signing in(계정 및 로그인)" 답변 및 후속 프롬프트](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

현재 연결되어 있지 않은 기존 질문 및 답변 쌍에 후속 프롬프트를 추가합니다. 질문이 질문 및 답변 쌍에 연결되어 있지 않기 때문에 현재 보기 설정을 변경해야 합니다.

1. 기존 질문 및 답변 쌍을 후속 프롬프트로 연결하려면 질문 및 답변 쌍에 대한 행을 선택합니다. Surface 설명서의 경우 **로그아웃** 을 검색하여 목록을 줄입니다.
1. **로그아웃** 행의 **답변** 열에서 **후속 프롬프트 추가** 를 선택합니다.
1. **후속 프롬프트** 팝업 창의 필드에 다음 값을 입력합니다.

    |필드|값|
    |--|--|
    |표시 텍스트|**Turn off the device(디바이스 끄기)** 를 입력합니다. 이는 후속 프롬프트에 표시할 사용자 지정 텍스트입니다.|
    |컨텍스트 전용| 이 확인란을 선택합니다. 질문이 컨텍스트를 지정하는 경우에만 답변이 반환됩니다.|
    |답변에 대한 링크|**Use the sign-in screen(로그인 화면 사용)** 을 입력하여 기존 질문 및 답변 쌍을 찾습니다.|

1.  하나의 일치 항목이 반환됩니다. 이 답변을 후속으로 선택한 다음, **저장** 을 선택합니다.

    !["후속 작업 프롬프트" 페이지](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. 후속 프롬프트를 추가한 후 위쪽 탐색 모음에서 **저장 후 학습** 을 선택합니다.

### <a name="edit-the-display-text"></a>표시 텍스트 편집

후속 프롬프트가 만들어지고 기존 질문 및 답변 쌍이 **답변에 대한 링크** 로 입력되면 새 **표시 텍스트** 를 입력할 수 있습니다. 이 텍스트는 기존 질문을 대체하지 않으며 새 대체 질문을 추가하지 않습니다. 이러한 값과는 별개입니다.

1. 표시 텍스트를 편집하려면 **컨텍스트** 필드에서 질문을 검색하고 선택합니다.
1. 해당 질문에 대한 행의 답변 열에서 후속 프롬프트를 선택합니다.
1. 편집하려는 표시 텍스트를 선택한 다음, **편집** 을 선택합니다.

    ![표시 텍스트에 대한 편집 명령](../media/conversational-context/edit-existing-display-text.png)

1. **후속 프롬프트** 팝업 창에서 기존 표시 텍스트를 변경합니다.
1. 표시 텍스트 편집을 마치면 **저장** 을 선택합니다.
1. 위쪽 탐색 모음의 **저장 후 학습**

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>기존 질문 및 답변 쌍을 후속 프롬프트로 추가

기술 자료에 새 질문 및 답변 쌍을 추가하는 경우 각 쌍을 기존 질문에 후속 프롬프트로 연결해야 합니다.

1. 기술 자료 도구 모음에서 **Accounts and signing in(계정 및 로그인)** 에 대한 기존 질문 및 답변 쌍을 검색하여 선택합니다.

1. 이 질문에 대한 **답변** 열에서 **후속 프롬프트 추가** 를 선택합니다.
1. **후속 프롬프트(미리 보기)** 에서 다음 값을 입력하여 새 후속 프롬프트를 만듭니다.

    |필드|값|
    |--|--|
    |표시 텍스트|*Windows 계정을 만드세요*. 후속 프롬프트에 표시할 사용자 지정 텍스트입니다.|
    |컨텍스트 전용|이 확인란을 선택합니다. 질문이 컨텍스트를 지정하는 경우에만 이 답변이 반환됩니다.|
    |답변에 대한 링크|답변으로 다음 텍스트를 입력합니다.<br>*신규 또는 기존 이메일 계정으로 Windows 계정을 [만드세요](https://account.microsoft.com/)* .<br>데이터베이스를 저장하고 학습하면 이 텍스트가 변환됩니다. |
    |||

    ![새 프롬프트 질문 및 답변 만들기](../media/conversational-context/create-child-prompt-from-parent.png)

1. **새로 만들기** 를 선택한 다음, **저장** 을 선택합니다.

    새 질문 및 답변 쌍이 만들어지고 선택한 질문이 후속 프롬프트로 연결됩니다. 두 질문에 대한 **컨텍스트** 열은 후속 프롬프트 관계를 나타냅니다.

1. **보기 옵션** 을 선택한 다음, [**Show context (PREVIEW)(컨텍스트 표시(미리 보기))**](#show-questions-and-answers-with-context)를 선택합니다.

    새 질문은 어떻게 연결되는지를 보여줍니다.

    ![새 후속 프롬프트 만들기](../media/conversational-context/new-qna-follow-up-prompt.png)

    부모 질문은 새 질문을 선택 항목 중 하나로 표시합니다.

    :::image type="content" source="../media/conversational-context/child-prompt-created.png" alt-text="스크린샷에서 두 질문에 대한 컨텍스트 열은 후속 프롬프트 관계를 나타냅니다." lightbox="../media/conversational-context/child-prompt-created.png":::

1. 후속 프롬프트를 추가한 후 위쪽 탐색 모음에서 **저장 후 학습** 을 선택합니다.

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>후속 프롬프트를 테스트하는 동안 멀티 턴 보기

**테스트** 창에서 후속 작업 프롬프트로 질문을 테스트하면 응답에 후속 작업 프롬프트가 포함됩니다.

![응답에 후속 작업 프롬프트가 포함됩니다.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>최초 답변과 후속 프롬프트를 반환하는 JSON 요청

빈 `context` 개체를 사용하여 사용자의 질문에 대한 답변을 요청하고 후속 프롬프트를 포함합니다.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>최초 답변과 후속 프롬프트 반환하는 JSON 응답

이전 섹션에서는 **Accounts and signing in(계정 및 로그인)** 에 대한 답변과 후속 프롬프트를 요청했습니다. 응답에는에 있는 프롬프트 정보와 `answers[0].context` 사용자에 게 표시할 텍스트가 포함 됩니다.

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n  Go to Start, and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

`prompts` 배열은 `displayText` 속성과 `qnaId` 값에 텍스트를 제공합니다. 이러한 답변을 대화 흐름에서 다음에 표시되는 선택 항목으로 표시하고 다음 요청에서 선택한 `qnaId`를 QnA Maker로 다시 보낼 수 있습니다.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>최초가 아닌 답변과 후속 프롬프트를 반환하는 JSON 요청

이전 컨텍스트를 포함하도록 `context` 개체를 채웁니다.

다음 JSON 요청에서 현재 질문은 *Use Windows Hello to sign in(Windows Hello를 사용하여 계정에 로그인)* 이고, 이전 질문은 *Accounts and signing in(계정 및 로그인)* 이었습니다.

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
```

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>최초가 아닌 답변과 후속 프롬프트를 반환하는 JSON 응답

QnA Maker _GenerateAnswer_ JSON 응답은 `answers` 개체에 있는 첫 번째 항목의 `context` 속성에 후속 프롬프트를 포함합니다.

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>QnA Maker ID로 기술 자료 쿼리

사용자 지정 응용 프로그램을 작성 하는 경우 초기 질문의 응답에서 추가 작업 프롬프트와 연결 된 `qnaId` 이 반환 됩니다. 이제 ID가 있으므로 추가 작업 프롬프트의 요청 본문에 전달할 수 있습니다. 요청 본문에 `qnaId` 및 컨텍스트 개체(이전 QnA Maker 속성 포함)가 포함된 경우 GenerateAnswer는 순위 알고리즘을 사용하여 질문 텍스트로 답변을 찾는 대신 ID별로 정확한 질문을 반환합니다.

## <a name="display-order-is-supported-in-the-update-api"></a>Update API에서 표시 순서 지원

JSON 응답에서 반환되는 [표시 텍스트 및 표시 순서](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)는 [Update API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update)에서 편집할 수 있도록 지원됩니다.

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Update API로 멀티 턴 프롬프트 추가 또는 삭제

[QnA Maker Update API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update)를 사용하여 멀티 턴 프롬프트를 추가하거나 삭제할 수 있습니다.  프롬프트가 `context` 속성의 `promptsToAdd` 배열과 `promptsToDelete` 배열에 추가됩니다.

## <a name="export-knowledge-base-for-version-control"></a>버전 제어를 위한 기술 자료 내보내기

QnA Maker는 내보낸 파일에 멀티 턴 대화 단계를 포함하여 버전 제어를 지원합니다.

## <a name="next-steps"></a>다음 단계

* 이 [대화 상자 샘플](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/adaptive-dialog/07.qnamaker/QnAMaker.csproj)의 상황별 대화에 대해 자세히 알아보거나 [멀티 턴 대화를 위한 개념적 봇 디자인](/azure/bot-service/bot-builder-conversations)에 대해 자세히 알아보세요.
