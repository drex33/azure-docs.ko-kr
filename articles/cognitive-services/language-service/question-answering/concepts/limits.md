---
title: 제한 및 경계 - 질문 답변
description: 질문 답변에는 기술 자료 및 서비스의 일부에 대한 메타 제한이 있습니다. 테스트 및 게시하기 위해서는 기술 자료를 그러한 제한 내로 유지하는 것이 중요합니다.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 185a76aeb4aeeadf4fea9b0b316b5905b1f598a5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483052"
---
# <a name="project-limits-and-boundaries"></a>Project 제한 및 경계

아래에 제공된 질문 답변 제한은 [Azure Cognitive Search 가격 책정 계층 제한과](../../../../search/search-limits-quotas-capacity.md) 질문 답변 제한의 조합입니다. 두 제한 집합 모두 리소스당 만들 수 있는 기술 자료 수와 각 기술 자료가 증가할 수 있는 규모에 영향을 미칩니다.

## <a name="knowledge-bases"></a>기술 자료

[Azure Cognitive Search 계층 제한](../../../../search/search-limits-quotas-capacity.md)을 기반으로 하는 최대 기술 자료 수

|**Azure Cognitive Search 계층** | **Free** | **기본** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|게시할 수 있는 기술 자료의 최대 수|2|14|49|199|199|2,999|

 예를 들어 계층에 15개의 허용된 인덱스가 있는 경우 14개의 기술 자료(게시된 기술 자료당 하나의 인덱스)를 게시할 수 있습니다. 15번째 인덱스 `testkb` 는 작성 및 테스트를 위한 모든 기술 자료로 사용됩니다.

## <a name="extraction-limits"></a>추출 제한

### <a name="file-naming-constraints"></a>파일 명명 제약 조건

파일 이름에는 다음 문자를 포함할 수 없습니다.

|문자 사용 안 함|
|--|
|작은따옴표 `'`|
|큰따옴표 `"`|

### <a name="maximum-file-size"></a>최대 파일 크기

|형식|최대 파일 크기(MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>최대 파일 수

> [!NOTE]
> 질문 답변은 현재 추가할 수 있는 원본 수에 제한이 없습니다. 현재 처리량은 관리 API와 예측 API 모두에 대해 초당 10개의 트랜잭션으로 제한됩니다.

### <a name="maximum-number-of-deep-links-from-url"></a>URL의 최대 딥 링크 수

URL 페이지에서 질문 답변 쌍을 추출하기 위해 크롤링할 수 있는 최대 **딥** 링크 수는 20개입니다.

## <a name="metadata-limits"></a>메타데이터 제한

메타데이터는 과 같은 텍스트 기반 `key:value` 쌍으로 `product:windows 10` 제공됩니다. 소문자로 저장되고 비교됩니다. 최대 메타데이터 필드 수는 **[Azure Cognitive Search 계층 제한](../../../../search/search-limits-quotas-capacity.md)** 을 기반으로 합니다.

단일 언어 리소스에 여러 언어가 있는 프로젝트를 선택하는 경우 프로젝트/기술 자료당 전용 테스트 인덱스가 있습니다. 따라서 언어 서비스의 프로젝트/기술 자료별로 제한이 적용됩니다.

|**Azure Cognitive Search 계층** | **Free** | **기본** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|언어 서비스당 최대 메타데이터 필드(기술 자료당)|1,000|100*|1,000|1,000|1,000|1,000|

여러 언어를 가진 프로젝트가 있는 옵션을 선택하지 않으면 언어 서비스의 모든 기술 자료 간에 제한이 적용됩니다.

|**Azure Cognitive Search 계층** | **Free** | **기본** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|언어 서비스당 최대 메타데이터 필드(모든 기술 자료)|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>이름 및 값

다음 표에서는 메타데이터 이름 및 값에 대한 길이와 허용 가능한 문자를 나열합니다.

|항목|허용되는 문자|Regex 패턴 일치|최대 문자 수|
|--|--|--|--|
|이름(키)|허용:<br>영숫자(문자 및 숫자)<br>`_`(밑줄)<br> 공백은 포함할 수 없습니다.|`^[a-zA-Z0-9_]+$`|100|
|값|제외:<br>`:`(콜론)<br>`|`(세로줄)<br>하나의 값만 허용|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>기술 자료 콘텐츠 제한
기술 자료에서 전반적인 콘텐츠 제한 사항은 아래와 같습니다.
* 대답 텍스트 길이: 25,000자
* 질문 텍스트 길이: 1,000자
* 메타데이터 키 텍스트 길이: 100자
* 메타데이터 값 텍스트 길이: 500자
* 메타데이터 이름에 지원되는 문자: 알파벳, 숫자 및 `_`
* 메타데이터 값의 지원되는 문자: 모든 문자(`:` 및 `|` 제외)
* 파일 이름 길이: 200
* 지원되는 파일 형식: “.tsv”, “.pdf”, “.txt”, “.docx”, “.xlsx”
* 최대 대체 질문 수: 300
* 최대 질문-대답 쌍 수: 선택한 **[Azure Cognitive Search 계층](../../../../search/search-limits-quotas-capacity.md#document-limits)** 에 따라 다릅니다. 질문 및 대답 쌍은 Azure Cognitive Search 인덱스의 문서에 매핑됩니다.
* URL/HTML 페이지: 100만 문자

## <a name="create-project-call-limits"></a>프로젝트 호출 제한을 만듭니다.

각 프로젝트/기술 자료 만들기 작업에 대한 제한을 나타냅니다. 즉, 새 *프로젝트 만들기를* 선택하거나 REST API 호출하여 프로젝트/기술 자료 만들기를 선택합니다.

* 대답별 권장 최대 대체 질문 수: 300
* 최대 URL 수: 10
* 최대 파일 수: 10
* 호출당 허용되는 최대 QnA 수: 1000개

## <a name="update-knowledge-base-call-limits"></a>기술 자료 호출 제한 업데이트

각 업데이트 동작에 대한 제한을 나타냅니다. 즉, *저장을* 선택하거나 업데이트 요청으로 REST API 호출합니다.
* 각 원본 이름 길이: 300
* 추가 또는 삭제된 권장 최대 대체 질문 수: 300
* 추가 또는 삭제된 최대 메타데이터 필드 수: 10
* 새로 고칠 수 있는 최대 URL 수: 5
* 호출당 허용되는 최대 QnA 수: 1000개

## <a name="add-unstructured-file-limits"></a>비구조적 파일 한도 추가

> [!NOTE]
> * 허용 한도보다 크기가 큰 파일을 사용해야 하는 경우 파일을 API로 보내기 전에 큰 파일을 작은 파일로 분할할 수 있습니다. 

이러한 제한은 비정형 파일을 사용하여 *새 프로젝트를 만들거나* REST API 호출하여 기술 자료 만들기에 사용되는 경우의 제한을 나타냅니다.
* 파일 길이: 처음 32,000자 추출
* 파일당 최대 3개의 응답입니다.

## <a name="prebuilt-question-answering-limits"></a>미리 빌드된 질문 답변 한도

> [!NOTE]
> * 허용 한도보다 크기가 큰 문서를 사용해야 하는 경우 텍스트를 API로 보내기 전에 큰 텍스트를 작은 텍스트 청크로 분할할 수 있습니다. 
> * 문서는 텍스트 문자의 단일 문자열입니다.  

이러한 제한은 프로젝트/기술 자료 생성 없이도 REST API 사용하여 질문에 답변할 때의 제한을 나타냅니다.
* 문서 수: 5개
* 단일 문서의 최대 크기: 5,120자
* 문서당 최대 3개의 응답입니다.