---
title: 기술 자료 마이그레이션 - QnA Maker
description: 기술 자료를 마이그레이션하려면 한 기술 자료를 내보낸 다음, 다른 기술 자료로 가져와야 합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: ea0a02366f2c2d2c3fd656d9a6dbce111d632422
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121199"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>내보내기-가져오기를 사용하여 기술 자료 마이그레이션

다음과 같은 몇 가지 이유로 기술 자료 복사본을 만들 수 있습니다.

* QnA Maker GA의 기술 자료를 사용자 지정 질문 답변으로 복사 
* 백업 및 복원 프로세스를 구현하려면 
* CI/CD 파이프라인과 통합 
* 데이터를 다른 지역으로 이동하려는 경우

## <a name="prerequisites"></a>필수 구성 요소

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 일반 공급(안정적인 릴리스)](#tab/v1)

> * Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/cognitive-services/) 계정을 만듭니다.
> * Azure Portal에서 만든 [QnA Maker 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)입니다. 리소스를 만들 때 선택한 Azure Active Directory ID, 구독, QnA 리소스 이름을 기억하세요.
> * 새 [QnA Maker 서비스](../How-To/set-up-qnamaker-service-azure.md) 설정

# <a name="custom-question-answering-preview-release"></a>[사용자 지정 질문 답변(미리 보기 릴리스)](#tab/v2)

> * Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/cognitive-services/) 계정을 만듭니다.
> * Azure Portal에서 사용자 지정 질문 답변 기능이 활성화된 [Text Analytics 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)입니다. 리소스를 만들 때 선택한 Azure Active Directory ID, 구독 및 Text Analytics 리소스 이름을 기억하세요.
> * [사용자 지정 질문 답변](../How-To/set-up-qnamaker-service-azure.md) 설정

---

## <a name="export-a-knowledge-base"></a>기술 자료 내보내기
1. [QnA Maker 포털](https://qnamaker.ai)에 로그인합니다.
1. 마이그레이션할 기술 자료를 선택합니다.

1. **설정** 페이지에는 **QnA**, **동의어** 또는 **기술 자료 복제본** 을 내보낼 수 있는 옵션이 있습니다. .tsv/.xlsx 형식으로 데이터를 다운로드하도록 선택할 수 있습니다.

   1. **QnA**: QnA를 내보낼 때 모든 QnA 쌍(질문, 답변, 메타데이터, 후속 프롬프트 및 데이터 원본 이름 포함)이 다운로드됩니다. 질문 및 답변과 함께 내보낸 QnA ID는 [업데이트 API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update)를 사용하여 특정 QnA 쌍을 업데이트하는 데 사용할 수 있습니다. 특정 QnA 쌍의 QnA ID는 여러 내보내기 작업에서 변경되지 않고 유지됩니다.
   2. **동의어**: 기술 자료에 추가된 동의어를 내보낼 수 있습니다.
   4. **기술 자료 복제본**: 동의어 및 기타 설정이 포함된 전체 기술 자료를 다운로드하려는 경우 이 옵션을 선택할 수 있습니다.

## <a name="import-a-knowledge-base"></a>기술 자료 가져오기
1. qnamaker.ai 포털의 상단 메뉴에서 **기술 자료 만들기** 를 클릭한 다음 URL이나 파일을 추가하지 않고 _빈_ 기술 자료를 만듭니다. 새 기술 자료에 대해 원하는 이름을 설정한 다음  **KB 만들기** 를 클릭합니다. 

1. 이 새로운 기술 자료에서 **설정** 탭을 열고 _기술 자료 가져오기_ 에서 **QnA**, **동의어** 또는 **기술 자료 복제본** 옵션 중 하나를 선택합니다. 

   1. **QnA**: 이 옵션은 모든 QnA 쌍을 가져옵니다. **새 기술 자료에서 만든 QnA 쌍은 내보낸 파일에 있는 것과 동일한 QnA ID를 가져야 합니다.** [SampleQnAs.xlsx](https://aka.ms/qnamaker-sampleqnas), [SampleQnAs.tsv](https://aka.ms/qnamaker-sampleqnastsv)를 참조하여 QnA를 가져올 수 있습니다.
   2. **동의어**: 이 옵션은 동의어를 기술 자료로 가져오는 데 사용할 수 있습니다. [SampleSynonyms.xlsx](https://aka.ms/qnamaker-samplesynonyms), [SampleSynonyms.tsv](https://aka.ms/qnamaker-samplesynonymstsv)를 참조하여 동의어를 가져올 수 있습니다.
   3. **기술 자료 복제본**: 이 옵션은 QnA, 동의어 및 설정이 있는 KB 복제본을 가져오는 데 사용할 수 있습니다. 자세한 내용은 [KBReplicaSampleExcel](https://aka.ms/qnamaker-samplereplica), [KBReplicaSampleTSV](https://aka.ms/qnamaker-samplereplicatsv)를 참조하세요. 구조화되지 않은 콘텐츠도 복제본에 추가하려면 [CustomQnAKBReplicaSample](https://aka.ms/qnamaker-samplev2replica)을 참조하세요.

      복제본을 가져올 때 QnA 또는 구조화되지 않은 콘텐츠가 필요합니다. 구조화되지 않은 문서는 사용자 지정 질문 답변에만 유효합니다.
      복제본을 가져올 때 동의어 파일은 필수가 아닙니다.
      복제본을 가져올 때 설정 파일은 필수입니다.

         |설정|QnA Maker KB로 가져올 때 업데이트가 허용되나요?|사용자 지정 질문 응답 KB로 가져올 때 업데이트가 허용되나요?|
         |:--|--|--|
         |DefaultAnswerForKB|아니요|예|
         |EnableActiveLearning(True/False)|예|아니요|
         |EnableMultiTurnExtraction(True/False)|예|예|
         |DefaultAnswerforMultiturn|예|예|
         |언어|아니요|아니요|

1. 테스트 패널을 사용하여 새 기술 자료를 **테스트** 합니다. [기술 자료 테스트](../How-To/test-knowledge-base.md) 방법을 알아보세요.

1. 기술 자료를 **게시** 하고 채팅 봇을 만듭니다. [기술 자료 게시](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) 방법을 알아보세요.

   > [!div class="mx-imgBorder"]
   > ![기술 자료 마이그레이션](../media/qnamaker-how-to-migrate-kb/import-export-kb.png)

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>QnA Maker에서 기술 자료를 프로그래밍 방식으로 마이그레이션

마이그레이션 프로세스는 다음 REST API를 사용하여 프로그래밍 방식으로 실행할 수 있습니다.

**내보내기**

* [기술 자료 다운로드 API](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/download)

**가져오기**

* [API 바꾸기(동일한 기술 자료 ID로 다시 로드)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/replace)
* [API 만들기(새 기술 자료 ID로 로드)](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase/create)


## <a name="chat-logs"></a>채팅 로그
새 기술 자료는 Application Insights를 사용하여 채팅 로그를 저장하기 때문에 채팅 로그를 마이그레이션할 방법이 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 편집](../How-To/edit-knowledge-base.md)
