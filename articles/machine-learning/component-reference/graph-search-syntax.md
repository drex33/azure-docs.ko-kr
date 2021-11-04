---
title: 그래프 검색 쿼리 구문
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에서 검색 쿼리 구문을 사용하여 파이프라인 그래프에서 노드를 검색하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/24/2021
ms.openlocfilehash: 74cf0b897529e8bb198b6f82a57e187662a4a285
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570032"
---
# <a name="graph-search-query-syntax"></a>그래프 검색 쿼리 구문

이 문서에서는 Azure Machine Learning의 그래프 검색 기능에 대해 알아봅니다. 

그래프 검색을 사용하면 파이프라인을 디버깅하거나 빌드할 때 노드를 신속하게 탐색할 수 있습니다. 툴바의 입력 상자에 키워드 또는 쿼리를 입력하거나 왼쪽 패널의 검색 탭에서 검색을 트리거할 수 있습니다. 일치하는 모든 결과가 캔버스에 노란색으로 강조 표시되고, 왼쪽 패널에서 결과를 선택하면 캔버스의 노드가 빨간색으로 강조 표시됩니다.

![예제 그래프 검색 환경을 보여 주는 스크린샷](media/search/graph-search-0322.png)

그래프 검색은 노드 이름 및 설명에 대한 전체 텍스트 키워드 검색을 지원합니다. RunStatus, duration, computeTarget 등의 노드 속성을 기준으로 필터링할 수도 있습니다. 키워드 검색은 Lucene 쿼리를 기반으로 합니다. 전체 검색 쿼리는 다음과 같습니다.  

**[[lucene 쿼리] | [filter 쿼리]]** 

Lucene 쿼리 또는 필터 쿼리를 사용할 수 있습니다. 둘 다 사용하려면 **|** 구분 기호를 사용합니다. 필터 쿼리의 구문은 Lucene 쿼리보다 엄격합니다. 따라서 고객 입력을 둘 다로 구문 분석할 수 있으면, 필터 쿼리가 적용됩니다.

예를 들어 `data OR model | compute in {cpucluster}`의 경우 이름이나 주석에 `data` 또는 `model`이 포함된 노드를 검색하기 위한 것이며, compute는 cpucluster입니다.
 

## <a name="lucene-query"></a>Lucene 쿼리

그래프 검색은 "이름" 및 "설명" 노드에서 전체 텍스트 검색 구문으로 Lucene 단순 쿼리를 사용합니다. 다음과 같은 Lucene 연산자가 지원됩니다.

 
- 및/또는
- **?** 과 **\*** 연산자를 사용하는 와일드 카드 일치.

### <a name="examples"></a>예

- 단순 검색: `JSON Data`

- AND/OR: `JSON AND Validation`

- 다중 AND/OR: `(JSON AND Validation) OR (TSV AND Training)`

 
- 와일드 카드 일치: 
    - `machi?e learning`
    - `mach*ing`
 
>[!NOTE]
> "*" 문자로 Lucene 쿼리를 시작할 수 없습니다.

##  <a name="filter-query"></a>필터 쿼리

 
필터 쿼리는 다음 패턴을 사용합니다.
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
다음 노드 속성을 키로 사용할 수 있습니다.

- runStatus
- compute
- duration
- reuse
- 게시
- tags

그리고 다음 연산자를 사용할 수 있습니다.

- Greater or equal: `>=`
- Less or equal: `<=`
- Greater: `>`
- Less: `<`
- Equal: `==`
- Contain: `=`
- NotEqual: `!=`
- In: `in`

 
 

### <a name="example"></a>예제

- duration > 100;
- status in { Failed,NotStarted}
- compute in {gpu-cluster}; runStatus in {Completed}

## <a name="technical-notes"></a>기술 정보

- 여러 필터 간의 관계는 "AND"입니다.
- `>=,  >,  <, or  <=`를 선택하면 값이 자동으로 숫자 형식으로 변환됩니다. 그렇지 않으면 문자열 형식이 비교에 사용됩니다.
- 모든 문자열 형식 값의 경우, 비교 시 대/소문자를 구분하지 않습니다.
- "In" 연산자는 컬렉션이 값으로 예상되고, 컬렉션 구문은 `{name1, name2, name3}`입니다.
- 키워드 사이에 공백은 무시됩니다.