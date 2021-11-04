---
title: 가져오기 문서 형식 지침 - QnA Maker
description: 이러한 지침을 가져오는 문서에 사용하여 콘텐츠에 대한 최상의 결과를 얻습니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.custom: ignite-fall-2021
ms.openlocfilehash: 875987a881e8e370d99530bfa975a73964d7d80c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080660"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>가져오는 문서 및 URL에 대한 형식 지침

다음 서식 지정 지침을 검토하여 콘텐츠에 대한 최상의 결과를 얻습니다.

## <a name="formatting-considerations"></a>서식 지정 고려 사항

파일 또는 URL을 가져오면 QnA Maker에서 콘텐츠를 [markdown 형식](https://en.wikipedia.org/wiki/Markdown)으로 변환하고 저장합니다. 변환 프로세스는 `\n\n`과 같이 새 줄을 텍스트에 추가합니다. markdown 형식에 대한 지식은 변환된 콘텐츠를 이해하고 기술 자료 콘텐츠를 관리하는 데 도움이 됩니다.

기술 자료의 콘텐츠를 직접 추가하거나 편집하는 경우 **markdown 서식 지정** 을 사용하여 서식 있는 텍스트 콘텐츠를 만들거나 이미 답변에 있는 markdown 형식 콘텐츠를 변경합니다. QnA Maker는 대부분의 markdown 형식을 지원하여 서식 있는 텍스트 기능을 콘텐츠에 제공합니다. 그러나 챗봇과 같은 클라이언트 애플리케이션에서 동일한 markdown 형식 집합을 지원하지 않을 수 있습니다. 클라이언트 애플리케이션의 답변 표시를 테스트하는 것이 중요합니다.

[콘텐츠 형식 및 예제](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base)의 전체 목록을 참조하세요.

## <a name="basic-document-formatting"></a>기본 문서 서식 지정

QnA Maker는 다음과 같은 시각적 단서를 기반으로 하여 파일의 섹션, 하위 섹션 및 관계를 식별합니다.

* 글꼴 크기
* 글꼴 스타일
* 번호 매기기
* 색

> [!NOTE]
> 현재 업로드된 문서에서의 이미지 추출은 지원되지 않습니다.

### <a name="product-manuals"></a>제품 설명서

설명서는 일반적으로 제품과 함께 제공되는 지침 자료입니다. 사용자가 제품을 설정, 사용, 유지 관리 및 문제를 해결하는 데 도움이 됩니다. QnA Maker는 설명서를 처리할 때 제목과 부제목을 질문으로, 후속 콘텐츠를 답변으로 추출합니다. [여기](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)에서 예제를 참조하세요.

아래는 인덱스 페이지와 계층적 콘텐츠가 포함된 설명서의 예입니다.

> [!div class="mx-imgBorder"]
> ![기술 자료를 위한 제품 설명서 예제](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> 추출은 목차 및/또는 색인 페이지와 계층적 제목이 있는 명확한 구조를 가진 설명서에서 가장 잘 작동합니다.

### <a name="brochures-guidelines-papers-and-other-files"></a>브로슈어, 지침, 논문 및 기타 파일

그 외의 문서 유형도 구조와 레이아웃이 명확하다면 QA 쌍을 생성하도록 처리할 수 있습니다. 여기에는 브로슈어, 지침, 보고서, 백서, 과학 논문, 정책, 서적 등이 포함됩니다. 예는 [여기](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)서 참조하세요.

아래는 인덱스 없는 반 구조화된 문서의 예입니다.

> [!div class="mx-imgBorder"]
> ![Azure Blob Storage 반 구조화된 Doc](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="unstructured-document-support"></a>비정형 문서 지원

이제 사용자 지정 질문 대답은 비정형 문서를 지원합니다. 잘 정의된 계층적 방식으로 구성된 콘텐츠가 없거나, 집합 구조가 없거나, 콘텐츠가 자유롭게 흐르는 문서는 비정형 문서로 간주될 수 있습니다.

다음은 비정형 PDF 문서의 예입니다.

> [!div class="mx-imgBorder"]
> ![기술 자료에 대한 비정형 문서 예제](./media/qnamaker-concepts-datasources/unstructured-qna-pdf.png)

 현재 이 기능은 문서 업로드를 통해서만 사용할 수 있으며 PDF 및 DOC 파일 형식에만 사용할 수 있습니다.

> [!IMPORTANT]
> 비정형 파일/콘텐츠에 대한 지원은 질문 응답에서만 사용할 수 있습니다.

### <a name="structured-qna-document"></a>구조화된 QnA 문서

DOC 파일에서 구조화된 질문-답변의 형식은 줄마다 질문과 답변이 번갈아 반복되는 양식으로, 아래와 같이 한 줄에 질문이 하나 나오면 그 다음 줄에 답변이 나옵니다.

```text
Question1

Answer1

Question2

Answer2
```

아래는 구조화된 QnA Word 문서의 예입니다.

> [!div class="mx-imgBorder"]
> ![기술 자료를 위한 구조화된 QnA 문서 예제](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

### <a name="structured-txt-tsv-and-xls-files"></a>구조화된 *TXT*, *TSV* 및 *XLS* 파일

또한 구조화된 *.txt*, *.tsv* 또는 *.xls* 파일 양식의 QnA를 QnA Maker에 업로드하여 기술 자료를 만들거나 보강할 수 있습니다.  이러한 자료는 일반 텍스트일 수도 있고, RTF 또는 HTML 콘텐츠를 가질 수도 있습니다. [QnA 쌍](./How-To/edit-knowledge-base.md#question-and-answer-pairs)에는 QnA 쌍을 범주별로 그룹화하는 데 사용할 수 있는 선택적 메타데이터 필드가 있습니다.

| 질문  | Answer  | 메타데이터(1개 키: 1개 값) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

원본 파일의 추가 열은 무시됩니다.

#### <a name="example-of-structured-excel-file"></a>구조화된 Excel 파일의 예

아래는 HTML 콘텐츠가 포함된 구조화된 QnA *.xls* 파일의 예입니다.

> [!div class="mx-imgBorder"]
> ![기술 자료를 위한 구조화된 QnA 문서 예제](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

#### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel 파일의 단일 답변에 대한 대체 질문의 예

다음은 단일 답변에 대한 몇 가지 대체 질문이 있는 구조화된 QnA *.xls* 파일의 예입니다.

> [!div class="mx-imgBorder"]
> ![Excel 파일의 단일 답변에 대한 대체 질문의 예](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

파일을 가져오면 아래와 같이 질문-답변 쌍이 기술 자료에 있습니다.

> [!div class="mx-imgBorder"]
> ![기술 자료로 가져온 단일 답변에 대한 대체 질문의 스크린샷](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

### <a name="structured-data-format-through-import"></a>가져오기를 통한 구조화된 데이터 형식

기술 자료를 가져오면 기존 기술 자료의 콘텐츠가 바뀝니다. 가져오기에는 데이터 원본 정보를 포함하는 구조화된 .tsv 파일이 필요합니다. 이 정보는 QnA Maker가 질문-답변 쌍을 그룹화하고 특정 데이터 원본에 귀속하는 데 도움이 됩니다. [QnA 쌍](./How-To/edit-knowledge-base.md#question-and-answer-pairs)에는 QnA 쌍을 범주별로 그룹화하는 데 사용할 수 있는 선택적 메타데이터 필드가 있습니다.

| 질문  | Answer  | 원본| 메타데이터(1개 키: 1개 값) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Editorial|    `Key:Value`       |

<a href="#formatting-considerations"></a>

### <a name="multi-turn-document-formatting"></a>다중 전환 문서 서식 지정

* 제목 및 부제목을 사용하여 계층 구조를 나타냅니다. 예를 들어 h1은 부모 QnA를 나타내고, h2는 프롬프트로 사용해야 하는 QnA를 나타냅니다. 작은 제목 크기를 사용하여 후속 계층 구조를 나타냅니다. 스타일, 색 또는 기타 메커니즘을 사용하여 문서의 구조체를 암시하지 마세요. QnA Maker는 멀티 턴 프롬프트를 추출하지 않습니다.
* 제목의 첫 번째 문자는 대문자여야 합니다.
* 제목 끝에 물음표(`?`)를 사용하지 않습니다.

**샘플 문서**:<br>[Surface Pro(docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso 혜택(docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso 혜택(pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="faq-urls"></a>FAQ URL

QnA Maker는 3가지 형식으로 FAQ 웹 페이지를 지원할 수 있습니다.

* 일반 FAQ 페이지
* 링크가 포함된 FAQ 페이지
* 항목 홈페이지가 포함된 FAQ 페이지

### <a name="plain-faq-pages"></a>일반 FAQ 페이지

가장 일반적인 형식의 FAQ 페이지로, 동일한 페이지에서 답변이 질문 바로 뒤에 표시됩니다.

아래는 일반 FAQ 페이지의 예입니다.

> [!div class="mx-imgBorder"]
> ![기술 자료를 위한 일반 FAQ 페이지 예제](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>링크가 포함된 FAQ 페이지

이러한 종류의 FAQ 페이지에서는 질문이 함께 집계되어 동일한 페이지의 다른 섹션 또는 다른 페이지에 있는 답변에 연결됩니다.

아래는 동일한 페이지에 있는 섹션의 링크가 포함된 FAQ 페이지의 예입니다.

> [!div class="mx-imgBorder"]
> ![기술 자료를 위한 섹션 링크 FAQ 페이지 예제](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>자식 답변 페이지에 대한 부모 항목 페이지 링크

이 형식의 FAQ에는 각 항목이 다른 페이지의 해당 질문 및 답변 세트에 연결되는 항목 페이지가 있습니다. QnA Maker는 연결된 모든 페이지를 크롤링하여 해당 질문과 답변을 추출합니다.

여러 페이지의 FAQ 섹션에 대한 링크가 포함된 항목 페이지의 예는 아래와 같습니다.

> [!div class="mx-imgBorder"]
> ![기술 자료를 위한 딥 링크 FAQ 페이지 예제](./media/qnamaker-concepts-datasources/topics-faq.png)

### <a name="support-urls"></a>지원 URL

QnA Maker는 지정된 태스크를 수행하는 방법, 지정된 문제를 진단하고 해결하는 방법 및 지정된 프로세스에 대한 어떤 모범 사례가 있는지를 설명하는 웹 문서와 같은 반정형화된 지원 웹 페이지를 처리할 수 있습니다. 추출은 계층 제목이 포함된 명확한 구조를 가진 콘텐츠에서 최적으로 작동합니다.

> [!NOTE]
> 지원 문서에 대한 추출은 새 기능이며 초기 단계에 있습니다. 잘 구조화된 단순 페이지에서 최적으로 작동하며 복잡한 헤더/바닥글을 포함하지 않습니다.

> [!div class="mx-imgBorder"]
> ![QnA Maker는 계층 제목과 함께 명확한 구조가 제시되는 반정형화된 웹 페이지에서 추출을 지원합니다.](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)

## <a name="import-and-export-knowledge-base"></a>기술 자료 가져오기 및 내보내기

내보낸 기술 자료의 **TSV 및 XLS 파일** 은 QnA Maker 포털의 **설정** 페이지에서 파일을 가져와야만 사용할 수 있습니다. 이러한 파일은 기술 자료를 만드는 중에 또는 **설정** 페이지의 **+ 파일 추가** 또는 **+ URL 추가** 기능에서 데이터 원본으로 사용할 수 없습니다. 

이러한 **TSV 및 XLS 파일** 을 통해 기술 자료를 가져오면 QnA 쌍이 내보낸 기술 자료에서 QnA가 추출된 원본이 아니라 편집 원본에 추가됩니다. 


## <a name="next-steps"></a>다음 단계

[콘텐츠 형식 및 예제](./concepts/data-sources-and-content.md#content-types-of-documents-you-can-add-to-a-knowledge-base)의 전체 목록을 참조하세요.
