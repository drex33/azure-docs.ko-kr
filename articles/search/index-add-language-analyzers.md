---
title: 문자열 필드에 언어 분석기 추가
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 비영어 쿼리 및 인덱스를 위한 다국어 어휘 분석 기능입니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 3a8a235e204826c26f20cc146003e9290331fe07
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113091540"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Azure Cognitive Search 인덱스의 문자열 필드에 언어 분석기 추가

‘언어 분석기’는 대상 언어의 언어 규칙을 사용하여 어휘 분석을 수행하는 특정 유형의 [텍스트 분석기](search-analyzers.md)입니다. 검색 가능한 모든 필드에 **analyzer** 속성이 있습니다. 영어 및 중국어 텍스트의 개별 필드와 같이 콘텐츠가 번역된 문자열로 구성된 경우 각 필드에 언어 분석기를 지정하여 해당 분석기의 풍부한 언어 기능에 액세스할 수 있습니다.

## <a name="when-to-use-a-language-analyzer"></a>언어 분석기를 사용해야 하는 경우

단어 또는 문장 구조를 인식하는 것이 텍스트 구문 분석에 도움이 되는 경우 언어 분석기를 고려해야 합니다. 일반적인 예는 불규칙한 동사 형식(“가져오기”와 “가져온”)이나 복수 명사(“마우스”와 “마우스들”)를 연결하는 것입니다. 언어적 인식을 사용하지 않을 경우 관련 문자열은 물리적 특성만으로 구문 분석되어 연결을 알 수 없습니다. 많은 양의 텍스트에 해당 콘텐츠가 포함될 가능성이 높기 때문에 설명, 검토 또는 요약으로 구성된 필드는 언어 분석기에 적합합니다.

또한 콘텐츠가 서구권이 아닌 언어 문자열로 구성된 경우 언어 분석기를 고려해야 합니다. [기본 분석기](search-analyzers.md#default-analyzer)는 언어에 구애받지 않지만, 공백과 특수 문자(하이픈 및 슬래시)를 사용하여 문자열을 구분하는 개념은 비서구권 언어보다 서구권 언어에 적합합니다. 

예를 들어, CJK(중국어, 일본어, 한국어) 및 기타 아시아 언어에서는 공백이 반드시 단어 구분 기호로 사용되는 것이 아닙니다. 다음 일본어 문자열을 살펴보겠습니다. 해당 문자열에는 공백이 없으므로 언어에 구애받지 않는 분석기에서 전체 문자열을 하나의 토큰으로 분석할 수 있으나, 실제로 문자열은 구문입니다.

```
これは私たちの銀河系の中ではもっとも重く明るいクラスの球状星団です。
(This is the heaviest and brightest group of spherical stars in our galaxy.)
```

위의 예에서 성공적인 쿼리를 위해서는 쿼리에 전체 토큰 또는 접미사 와일드카드 문자를 사용하는 부분 토큰이 포함되어야 합니다. 따라서 검색 경험이 자연스럽지 않고 제한적입니다.

더 나은 경험은 明るい(밝은), 私たちの(우리의), 銀河系(은하계) 등의 개별 단어를 검색하는 것입니다. Cognitive Search에서 사용할 수 있는 일본어 분석기 중 하나를 사용하면 해당 동작을 가능하게 할 수 있습니다. 해당 분석기는 텍스트 청크를 대상 언어의 의미 있는 단어로 분할하는 데 더 적합하기 때문입니다.

## <a name="comparing-lucene-and-microsoft-analyzers"></a>Lucene과 Microsoft 분석기 비교

Azure Cognitive Search는 Lucene을 통해 지원되는 35개 언어 분석기와 Office 및 Bing에서 사용되는 Microsoft 소유 자연어 처리 기술을 통해 지원되는 50개 언어 분석기를 지원합니다.

일부 개발자는 보다 친숙하고 간단한 Lucene의 오픈 소스 솔루션을 선호할 수 있습니다. Lucene 언어 분석기가 더 빠르지만 Microsoft 분석기에는 분류 정리, 단어 분해(독일어, 덴마크어, 네덜란드어, 스웨덴어, 노르웨이어, 에스토니아어, 핀란드어, 헝가리어, 슬로바키아어 등의 언어) 및 엔터티 인식(URL, 메일, 날짜, 숫자)과 같은 고급 기능이 있습니다. 가능한 경우 Microsoft 분석기와 Lucene 분석기를 비교하여 어떤 것이 더 적합한지 결정해야 합니다. [분석 API](/rest/api/searchservice/test-analyzer)를 사용하여 특정 분석기를 통해 지정된 텍스트에서 생성된 토큰을 볼 수 있습니다.

언어에 따라 Microsoft 분석기를 사용한 인덱싱은 Lucene보다 평균 두세 배 정도 더 느립니다. 검색 성능은 평균 크기 쿼리에 크게 영향을 받지 않아야 합니다. 

### <a name="english-analyzers"></a>영어 분석기

기본 분석기는 표준 Lucene으로, 영어에서는 잘 작동하지만 Lucene 영어 분석기 또는 Microsoft 영어 분석기만큼 효과적이지 않을 수 있습니다.

+ Lucene 영어 분석기는 표준 분석기를 확장합니다. 이 분석기는 단어에서 소유격(후행 's)을 제거하고, Porter 형태소 분석 알고리즘에 따라 형태소 분석을 적용하며, 영어의 중지 단어를 제거합니다.  

+ Microsoft 영어 분석기는 형태소 분석 대신 분류 정리를 수행합니다. 따라서 어형이 변화되고 불규칙한 단어 형태를 훨씬 잘 처리하여 더 관련도 높은 검색 결과를 제공할 수 있습니다. 

## <a name="how-to-specify-a-language-analyzer"></a>언어 분석기 지정 방법

필드 정의 중에 Edm.String 형식의 “검색 가능” 필드에 언어 분석기를 설정합니다.

필드 정의에는 여러 분석기 관련 속성이 있지만, 언어 분석기에는 “analyzer” 속성만 사용할 수 있습니다. “analyzer”의 값은 지원 분석기 목록에서 언어 분석기 중 하나여야 합니다.

```json
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
```

인덱스를 만들고 필드 속성을 설정하는 방법에 대한 자세한 내용은 [인덱스 만들기(REST)](/rest/api/searchservice/create-index)를 참조하세요. 텍스트 분석에 대한 자세한 내용은 [Azure Cognitive Search의 분석기](search-analyzers.md)를 참조하세요.

<a name="language-analyzer-list"></a>

## <a name="supported-language-analyzers"></a>지원되는 언어 분석기

 다음은 Lucene 및 Microsoft 분석기 이름으로 지원되는 언어의 목록입니다.  

| 언어 | Microsoft 분석기 이름 | Lucene 분석기 이름 |
|----------|-------------------------|----------------------|
| 아랍어   | ar.microsoft | ar.lucene |
| 아르메니아어 |           | hy.lucene |
| 벵골어   | bn.microsoft |  |
| 바스크어   |  | eu.lucene |
| 불가리아어 | bg.microsoft | bg.lucene |
| 카탈로니아어  | ca.microsoft | ca.lucene |
| 중국어 간체 | zh-Hans.microsoft | zh-Hans.lucene |
| 중국어 번체 | zh-Hant.microsoft | zh-Hant.lucene |
| 크로아티아어 | hr.microsoft |  |
| 체코어 | cs.microsoft | cs.lucene |
| 덴마크어 | da.microsoft | da.lucene |
| 네덜란드어 | nl.microsoft | nl.lucene |
| 영어 | en.microsoft | en.lucene |
| 에스토니아어 | et.microsoft |  |
| 핀란드어 | fi.microsoft | fi.lucene |
| 프랑스어 | fr.microsoft | fr.lucene |
| 갈리시아어 |  | gl.lucene |
| 독일어 | de.microsoft | de.lucene |
| 그리스어 | el.microsoft | el.lucene |
| 구자라트어 | gu.microsoft |  |
| 히브리어 | he.microsoft |  |
| 힌디어 | hi.microsoft | hi.lucene |
| 헝가리어 | hu.microsoft | hu.lucene |
| 아이슬란드어 | is.microsoft |  |
| 인도네시아어(공용어) | id.microsoft | id.lucene |
| 아일랜드어 |  | ga.lucene |
| 이탈리아어 | it.microsoft | it.lucene |
| 일본어 | ja.microsoft | ja.lucene |
| 칸나다어 | kn.microsoft |  |
| 한국어 | ko.microsoft | ko.lucene |
| 라트비아어 | lv.microsoft | lv.lucene |
| 리투아니아어 | lt.microsoft |  |
| 말라얄람어 | ml.microsoft |  |
| 말레이어(라틴 문자) | ms.microsoft |  |
| 마라티어 | mr.microsoft |  |
| 노르웨이어 | nb.microsoft | no.lucene |
| 페르시아어 |  | fa.lucene |
| 폴란드어 | pl.microsoft | pl.lucene |
| 포르투갈어(브라질) | pt-Br.microsoft | pt-Br.lucene |
| 포르투갈어(포르투갈) | pt-Pt.microsoft | pt-Pt.lucene |
| 펀잡어 | pa.microsoft |  |
| 루마니아어 | ro.microsoft | ro.lucene |
| 러시아어 | ru.microsoft | ru.lucene |
| 세르비아어(키릴 자모) | sr-cyrillic.microsoft |  |
| 세르비아어(라틴 문자) | sr-latin.microsoft |  |
| 슬로바키아어 | sk.microsoft |  |
| 슬로베니아어 | sl.microsoft |  |
| 스페인어 | es.microsoft | es.lucene |
| 스웨덴어 | sv.microsoft | sv.lucene |
| 타밀어 | ta.microsoft |  |
| 텔루구어 | te.microsoft |  |
| 태국어 | th.microsoft | th.lucene |
| 터키어 | tr.microsoft | tr.lucene |
| 우크라이나어 | uk.microsoft |  |
| 우르두어 | ur.microsoft |  |
| 베트남어 | vi.microsoft |  |

 이름에 **Lucene** 주석이 포함된 모든 분석기는 [Apache Lucene 언어 분석기](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )를 통해 구동됩니다.

## <a name="see-also"></a>참고 항목  

+ [인덱스 만들기](search-what-is-an-index.md)
+ [다중 언어 인덱스 만들기](search-language-support.md)
+ [인덱스 만들기(REST API)](/rest/api/searchservice/create-index)  
+ [LexicalAnalyzerName 클래스(.NET용 Azure SDK)](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername)
