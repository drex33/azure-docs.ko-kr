---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: a8af7d5c76552ade3ffd5d50b308cee6b0b1446c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100872"
---
## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)

## <a name="sign-in-to-language-studio"></a>Language Studio에 로그인

1. [Language Studio](https://aka.ms/languageStudio)로 이동하여 Azure 계정으로 로그인합니다. 

2. 표시되는 **언어 리소스 선택** 창에서 Azure 구독을 찾고 언어 리소스를 선택합니다. 리소스가 없는 경우 새 리소스 그룹을 만들 수 있습니다.

    > [!NOTE]
    > 현재 표준(**S**) 가격 책정 계층의 리소스만 대화형 Language Understanding 서비스와 함께 사용할 수 있습니다.
    
    :::image type="content" source="../../media/quickstart-language-resource.png" alt-text="Language Studio의 리소스 선택 화면을 보여 주는 스크린샷" lightbox="../../media/quickstart-language-resource.png":::

## <a name="create-a-conversation-project"></a>대화 프로젝트 만들기

계정과 연결된 언어 리소스가 있으면 대화형 Language Understanding 프로젝트를 만듭니다. 이 빠른 시작에서는 특정 사람의 이메일 읽기, 이메일 삭제, 이메일에 문서 첨부와 같은 이메일 명령을 식별할 수 있는 프로젝트를 만듭니다.

1. [Language Studio](https://aka.ms/languageStudio)에서 **대화형 Language Understanding** 섹션을 찾아 **대화형 Language Understanding** 을 클릭합니다. 그러면 **대화 프로젝트** 페이지로 이동합니다. 
 
    
    :::image type="content" source="../../media/projects-page.png" alt-text="Language Studio의 대화 프로젝트 페이지를 보여 주는 스크린샷" lightbox="../../media/projects-page.png":::

2. **새 프로젝트 만들기** 를 클릭합니다. **대화 프로젝트** 를 선택한 후 **다음** 을 클릭합니다.

그런 후 다음 세부 정보를 제공해야 합니다.

|값  | 설명  |
|---------|---------|
|Name     | 프로젝트의 이름입니다.        |
|Description    | 선택적인 프로젝트 설명입니다.        |
|텍스트 기본 언어     | 프로젝트의 기본 언어입니다. 학습 데이터는 기본적으로 이 언어로 되어 있어야 합니다. 이 빠른 시작에서는 **영어** 를 선택합니다.        |
|여러 언어 사용     |  한 번에 여러 언어를 지원하도록 프로젝트를 사용하도록 설정할지 여부. 이 빠른 시작의 경우 이 옵션을 사용하도록 설정합니다.       |

완료되면 **다음** 을 클릭하고 세부 정보를 검토한 다음 **프로젝트 만들기** 를 클릭하여 프로세스를 완료합니다. 이제 프로젝트에 **스키마 빌드** 화면이 표시되어야 합니다.

## <a name="build-schema"></a>스키마 빌드

1. **스키마 빌드** 페이지에서 의도 또는 엔터티 탭을 선택하고 **추가** 를 클릭합니다. 의도 또는 엔터티 만들기를 완료하기 전에 이름을 입력하라는 메시지가 표시됩니다.

2. 다음 이름으로 3개의 의도를 만듭니다.
    - **읽기**
    - **Delete**
    - **연결**

3. 다음 이름으로 3개의 엔터티를 만듭니다.
    - **보낸 사람**
    - **FileName**
    - **FileType**


의도를 클릭하면 [태그 발화](../../how-to/tag-utterances.md) 페이지로 이동하여 의도의 예를 추가하고 엔터티에 레이블을 지정할 수 있습니다.


:::image type="content" source="../../media/quickstart-intents.png" alt-text="Language Studio의 스키마 페이지를 보여 주는 스크린샷" lightbox="../../media/quickstart-intents.png":::

## <a name="tag-utterances"></a>태그 발화

태그 발화 페이지에서 의도에 몇 가지 예를 추가해 보겠습니다. **의도 선택** 이라는 드롭다운 상자에서 **읽기** 의도를 선택합니다.

**발화 예를 작성하고 Enter 키를 누릅니다.** 라는 텍스트 상자에 "*Carol이 보낸 이메일 읽기*" 문장을 쓰고 Enter 키를 눌러 예제 발화로 추가합니다.

"*Carol*"단어 위로 커서를 끌고 **Sender** 엔터티를 선택하여 엔터티로 "*Carol*" 레이블을 지정합니다.

다음 의도 및 엔터티를 사용하여 이러한 발화의 나머지 부분을 추가합니다.

|발화|Intent|엔터티|
|--|--|--|
|*John의 이메일 읽어보기*|**읽기**|"John": **Sender**|
|*Matt의 이메일 내용*|**읽기**|"Matt": **Sender**|
|*Martha가 보낸 마지막 이메일 삭제*|**Delete**|"Martha": **Sender**|
|*삭제*|**Delete**|_엔터티 없음_|
|*이 항목 제거*|**Delete**|_엔터티 없음_|
|*삭제된 폴더로 이동*|**Delete**|_엔터티 없음_|
|*보고서 q1이라는 Excel 파일 첨부*|**연결**|"excel": **FileType**, <br> "reports q1" -> **FileName**|
|*PowerPoint 파일 첨부*|**연결**|"PowerPoint": **FileType**|
|*이름이 서명된 계약서가 포함된 PDF 파일 추가* |**연결**|"PDF": **FileType**, <br> "서명된 계약": **파일 이름**|


완료되면 **변경 내용 저장** 을 클릭하여 발화 및 레이블을 프로젝트에 저장합니다. 변경 사항이 저장되면 단추 옆의 아이콘이 녹색으로 바뀝니다. 그런 다음 **모델 학습** 페이지로 이동합니다.

:::image type="content" source="../../media/quickstart-utterances.png" alt-text="Language Studio의 의도 태깅 화면을 보여 주는 스크린샷" lightbox="../../media/quickstart-utterances.png":::

## <a name="train-your-model-and-view-its-details"></a>모델 학습 및 세부 정보 보기

화면 왼쪽에서 **모델 학습** 을 클릭합니다. 모델을 학습시키려면 모델의 이름을 제공해야 합니다. "*v0.1*"과 같은 이름을 쓰고 Enter 키를 누릅니다. 

**학습** 을 클릭하기 전에 **학습으로 평가 실행** 을 끕니다. 

**모델 세부 정보 보기** 페이지가 표시되어야 합니다. 교육이 완료될 때까지 기다리세요(약 5분 정도 소요될 수 있음). 학습에 성공하면 화면 왼쪽에서 **모델 배포** 를 선택합니다.

## <a name="deploy-your-model"></a>모델 배포

화면 왼쪽의 **모델 배포** 페이지에서 학습된 모델을 선택하고 **모델 배포** 단추를 클릭합니다. 표시되는 화면에서 **배포** 를 클릭합니다.

## <a name="test-your-model"></a>모델 테스트

화면 왼쪽에서 **모델 테스트** 를 클릭하고 모델 링크를 선택합니다. "*trash this one*" 발화를 작성하고 **테스트 실행** 을 클릭합니다. 

이제 엔터티가 없는 **삭제** 로 상위 의도가 표시됩니다.

다음과 같은 다른 발화를 테스트할 수 있습니다.
* "*내 docx 파일 첨부*", 
* "*Jason의 이메일 읽기*", 
* "*CLU 데모라는 ppt 파일을 첨부합니다*".

다음과 같은 다른 언어로 된 발화를 테스트할 수도 있습니다.

* "*Joindre le fichier PDF*"(프랑스어: "*PDF 파일 첨부*"), 
* "*lesen sie die e-mail von Macy*"(독일어: "*Read Macy's e-mail*")
