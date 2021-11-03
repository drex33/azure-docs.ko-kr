---
title: 모범 사례 - 질문 답변
description: 이러한 모범 사례를 사용하여 프로젝트를 개선하고 애플리케이션/채팅 봇의 최종 사용자에게 더 나은 결과를 제공합니다.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 552e3d014ffecf5f43977420c9aa8c6bb1b59080
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103048"
---
# <a name="question-answering-best-practices"></a>질문 답변 모범 사례

모범 사례를 사용하여 기술 자료를 개선하고 애플리케이션 또는 챗봇 최종 사용자에게 보다 나은 결과를 제공할 수 있습니다.

## <a name="extraction"></a>추출

질문 답변은 콘텐츠에서 질문 답변 쌍을 추출하는 알고리즘을 지속적으로 개선하고 지원되는 파일 및 HTML 형식 목록을 확장하는 것입니다. 일반적으로 FAQ 페이지는 독립 실행형이어야 하며, 다른 정보와 결합되지 않아야 합니다. 제품 설명서는 제목에 명확해야 하고 인덱스 페이지까지 명확하면 더욱 좋습니다.

## <a name="creating-good-questions-and-answers"></a>좋은 질문 및 답변 만들기

### <a name="good-questions"></a>좋은 질문

가장 좋은 질문은 간단합니다. 각 질문의 키워드 또는 문구를 고려한 다음, 해당 키워드 또는 문구에 대한 간단한 질문을 만듭니다.

필요한 개수만큼 대체 질문을 추가하되, 간단한 변경으로 유지합니다. 질문의 주요 목표에 속하지 않는 단어 또는 관용구를 더 추가해도 질문 응답 알고리즘이 일치를 찾는 데 도움이 되지 않습니다.

### <a name="add-relevant-alternative-questions"></a>관련 대체 질문 추가

사용자는 텍스트 대화형 스타일(`How do I add a toner cartridge to my printer?`) 또는 키워드 검색(예: `toner cartridge`)을 사용하여 질문을 입력할 수 있습니다. 최상의 답변을 올바르게 반환하려면 프로젝트에 두 가지 질문 스타일이 모두 있어야 합니다. 고객이 입력하는 키워드를 잘 모르는 경우 [Azure Monitor](../how-to/analytics.md) 데이터를 사용하여 쿼리를 분석합니다.

### <a name="good-answers"></a>좋은 답변

가장 적합한 답변은 간단한 답변이지만, 너무 간단하지 않아야 합니다. `yes` 및 `no`와 같은 답변은 사용하지 마세요. 답변을 다른 원본에 연결되거나 답변에서 미디어 및 링크를 사용하여 풍부한 경험을 제공해야 하는 경우 메타데이터 태그 지정을 사용하여 답변을 구분한 다음, `strictFilters` 속성에 메타데이터 태그가 있는 쿼리를 제출하여 올바른 답변 버전을 가져옵니다.

|답변|후속 프롬프트 추가|
|--|--|
|키보드의 전원 단추를 사용하여 Surface 노트북의 전원을 켭니다.|* 절전 모드, 종료 및 다시 시작 키 조합<br>* Surface 노트북을 하드 부팅하는 방법<br>* Surface 노트북의 BIOS를 변경하는 방법<br>* 절전 모드, 종료 및 다시 시작 간의 차이점|
|고객 서비스는 하루 24시간 동안 전화, Skype 및 문자 메시지를 통해 이용 가능합니다.|* 판매를 위한 연락처 정보<br> * 직접 방문을 위한 사무실 및 매장 위치와 시간<br> * Surface 노트북용 액세서리|

## <a name="chit-chat"></a>잠답

봇에 잡담을 추가하여 적은 노력으로 봇을 대화형이고 몰입도 높게 만듭니다. 프로젝트를 만들 때 미리 정의된 개인 정보에서 잡담 데이터 원본을 쉽게 추가하고 언제든지 변경할 수 있습니다. [KB에 잡담을 추가](../How-To/chit-chat.md)하는 방법을 알아봅니다.

잡담은 [여러 언어](../how-to/chit-chat.md#language-support)로 지원됩니다.

### <a name="choosing-a-personality"></a>개성 선택

잡담은 미리 정의된 여러 개성에 지원됩니다.

|개성 |질문 답변 데이터 세트 파일 |
|---------|-----|
|Professional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|친숙한 |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|재치있는 |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|배려하는 |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|열정적인 |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

응답은 격식체에서 비격식체의 범위까지 지정됩니다. 봇에 원하는 어조와 가장 가깝게 맞춰진 개성을 선택해야 합니다. [데이터 세트](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)를 보고, 봇에 대한 기준으로 사용되는 데이터 세트를 선택한 다음, 응답을 사용자 지정할 수 있습니다.

### <a name="edit-bot-specific-questions"></a>봇 관련 질문 편집

잠답 데이터 집합의 일부이며 일반 답변으로 채워진 몇 가지 봇 관련 질문이 있습니다. 이 대답을 변경하여 봇 세부 정보를 가장 잘 반영합니다.

다음 잡담 질문 답변 쌍을 보다 구체적으로 만드는 것이 좋습니다.

* 귀하는 누구인가요?
* 무엇을 할 수 있나요?
* 나이가 어떻게 되세요?
* 만든 사람은 누구인가요?
* 안녕하세요.

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>메타데이터 태그를 사용하여 사용자 지정 잡담 추가

사용자 고유의 잡담 질문 답변 쌍을 추가하는 경우 이러한 답변이 반환되도록 메타데이터를 추가해야 합니다. 메타데이터 이름/값 쌍은 `editorial:chitchat`입니다.

## <a name="searching-for-answers"></a>답변 검색

질문 답변 REST API 질문과 대답을 모두 사용하여 사용자 쿼리에 대한 최상의 답변을 검색합니다.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>답변이 관련이 없는 경우에만 질문 검색

답변을 검색하지 않으려면 [`RankerType=QuestionOnly`](#choosing-ranker-type)를 사용합니다.

이에 대한 예는 머리글자어가 질문이고 전체 형식이 답변인 카탈로그가 기술 자료인 경우입니다. 답변의 값은 적절한 답변을 검색하는 데 도움이 되지 않습니다.

## <a name="rankingscoring"></a>순위 지정/채점

지원되는 순위 기능을 최대한 활용하고 있는지 확인합니다. 그러면 특정 사용자 쿼리에 적절하게 답변할 가능성이 높아집니다.

### <a name="choosing-a-threshold"></a>임계값 선택

임계값으로 사용되는 기본 [신뢰도 점수는](confidence-score.md) 0이지만 필요에 따라 프로젝트에 대한 [임계값을 변경할](confidence-score.md#set-threshold) 수 있습니다. 모든 프로젝트가 다르므로 프로젝트에 가장 적합한 임계값을 테스트하고 선택해야 합니다.

### <a name="choosing-ranker-type"></a>Ranker 유형 선택

기본적으로 질문 답변은 질문 및 답변을 통해 검색됩니다. 질문을 통해서만 검색하려는 경우 답변을 생성하려면 `RankerType=QuestionOnly` REST API 요청의 POST 본문에서 를 사용합니다.

### <a name="add-alternate-questions"></a>대체 질문 추가

사용자 쿼리와의 일치 가능성을 높이기 위한 대체 질문입니다. 대체 질문은 같은 질문을 여러 가지 방식으로 받을 수 있는 경우에 유용합니다. 여기에는 문장 구조 및 단어 스타일 변경이 포함됩니다.

|원래 쿼리|대체 쿼리|변경|
|--|--|--|
|주차 가능하나요?|자동차공원이 있나요?|문장 구조|
 |Hi|Yo<br>Hey there!|단어 스타일 또는 속어|

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>메타데이터 태그를 사용하여 질문과 답변 필터링

메타데이터는 클라이언트 애플리케이션에서 모든 답변을 받지 않고 메타데이터 태그를 기준으로 사용자 쿼리 결과의 범위를 축소하는 기능을 추가합니다. 프로젝트/기술 자료 답변은 쿼리가 동일한 경우에도 메타데이터 태그에 따라 다를 수 있습니다. 예를 들어 식당 지점의 위치가 다르면 *"주차장 위치는 어디인가요"* 에 대한 대답이 달라질 수 있습니다. 즉, 메타데이터는 *위치: 시애틀* 또는 *위치: 레드몬드* 입니다.

### <a name="use-synonyms"></a>동의어 사용

영어의 동의어는 일부 지원되지만 대/소문자를 구분하지 않는 [단어 변경을](../tutorials/adding-synonyms.md) 사용하여 다른 형식을 사용하는 키워드에 동의어를 추가합니다.

|원래 단어|동의어|
|--|--|
|구입|구매<br>인터넷 뱅킹<br>인터넷 뱅킹|

---

### <a name="use-distinct-words-to-differentiate-questions"></a>고유한 단어를 사용하여 질문을 구분

프로젝트/기술 자료의 질문과 사용자 쿼리를 일치시키는 순위 알고리즘은 각 질문이 다른 필요성을 해결하는 경우에 가장 적합합니다. 질문 사이에 같은 단어 집합을 반복하면 해당 단어가 포함된 특정 사용자 쿼리에 대해 올바른 대답이 선택될 확률이 감소합니다.

예를 들어 다음 질문이 있는 두 개의 개별 질문 답변 쌍이 있을 수 있습니다.

|질문|
|--|
|주차 *위치* 는 어디인가요|
|ATM *위치* 는 어디인가요|

이러한 두 질문은 매우 유사한 단어로 표현되므로 이러한 유사성으로 인해  *"where is the `<x>` location"과* 같은 문구가 있는 많은 사용자 쿼리에 대해 매우 유사한 점수가 발생할 수 있습니다. 대신 프로젝트에서 많은 질문에 있을 수 있는 "위치"와 같은 단어를 피하여  *"여기서 는 요금제인지"* 및 *"ATM은 어디인가요"와* 같은 쿼리를 명확하게 구분해 보세요.

## <a name="collaborate"></a>공동 작업

질문에 답변하면 사용자가 프로젝트/기술 자료로 공동 작업할 수 있습니다. 사용자는 프로젝트/기술 자료에 액세스하기 위해 연결된 Azure 리소스 그룹에 액세스해야 합니다. 기술 자료 편집 및 유지 관리를 아웃소싱하려는 조직도 있을 것이며, 이 경우에도 여전히 Azure 리소스에 대한 액세스를 보호할 수 있습니다. 이 편집기 승인자 모델은 서로 다른 구독의 프로젝트에 대답하는 동일한 질문으로 두 개의 동일한 언어 리소스를 설정하고 편집 테스트 주기에 대해 하나를 선택하여 수행됩니다. 테스트가 완료되면 프로젝트 콘텐츠를 내보내고 [가져오기-내보내기](../how-to/migrate-knowledge-base.md) 프로세스와 함께 최종적으로 프로젝트를 배포하고 엔드포인트를 업데이트할 승인자의 언어 리소스로 전송됩니다.

## <a name="active-learning"></a>능동적 학습

[활성 학습](../tutorials/active-learning.md)은 품질의 범위가 넓은 사용자 기반 쿼리의 수가 많을 때 가장 적절한 대체 질문을 제안합니다. 그러므로 클라이언트 애플리케이션의 사용자 쿼리가 검열되지 않고 활성 학습 피드백 루프에 참여할 수 있도록 해야 합니다. Language Studio 포털에서 질문이 제안되면 해당 제안을 검토하고 수락하거나 거부할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기술 자료 편집](../How-to/manage-knowledge-base.md)
