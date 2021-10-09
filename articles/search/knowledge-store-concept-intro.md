---
title: 지식 저장소 개념
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 및 다른 애플리케이션에서 보강된 문서를 보고, 변형시키고, 사용할 수 있는 Azure Storage에 해당 문서를 보냅니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 1463c4e879b364f8242d55aa58b424a8cc6f9cf9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706799"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Azure Cognitive Search의 지식 저장소

기술 자료 저장소는 Cognitive Search [AI 보강 파이프라인](cognitive-search-concept-intro.md) 을 통해 생성 되는 데이터 싱크로, 기술 자료와 같은 비 검색 시나리오에서 독립 분석 또는 다운스트림 처리를 위해 테이블 및 blob Azure Storage 컨테이너에 보강 콘텐츠를 저장 합니다.

과거에 인식 기술을 사용 하는 경우 *기술력과* 는 엔터티를 인식 하거나 텍스트를 번역 하는 것과 같이 원자성 변환을 호출 하는 강화 시퀀스를 통해 문서를 이동 한다는 것을 이미 알고 있습니다. 결과는 검색 인덱스이거나 지식 저장소의 프로젝션일 수 있습니다. 검색 인덱스와 기술 자료 저장소의 두 출력은 동일한 파이프라인의 상호 배타적인 제품입니다. 동일한 입력에서 파생 되지만 다양 한 응용 프로그램에서 구조화 되 고 저장 되 고 사용 되는 출력이 생성 됩니다.

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="기술 세트가 있는 파이프라인" border="false":::

물리적으로 지식 저장소는 [Azure Storage](../storage/common/storage-account-overview.md)이며 Azure Table Storage, Azure Blob Storage 또는 둘 다 해당합니다. Azure Storage에 연결할 수 있는 모든 도구 또는 프로세스는 지식 저장소의 콘텐츠를 사용할 수 있습니다.

Storage Explorer를 통해 표시된 지식 저장소는 테이블, 개체 또는 파일 모음일 뿐입니다. 다음 예제에서는 데이터 원본에서 전달되었거나 보강을 통해 생성된 필드가 있는 3개의 테이블로 구성된 지식 저장소를 보여 줍니다(“감정 점수” 및 “translated_text” 참조).

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="보강 트리에서 읽기 및 쓰기 기술" border="true":::

## <a name="benefits-of-knowledge-store"></a>지식 저장소의 이점

기술 자료 저장소의 주요 이점은 2 배입니다. 콘텐츠를 유연 하 게 액세스 하 고 데이터를 셰이핑 하는 기능입니다.

Cognitive Search 쿼리를 통해서만 액세스할 수 있는 검색 인덱스와 달리, 기술 자료 저장소는 Azure Storage에 대 한 연결을 지 원하는 모든 도구, 앱 또는 프로세스에서 액세스할 수 있습니다. 이러한 유연성으로 인해 보강 파이프라인에서 생성 된 분석 및 보강 콘텐츠를 사용 하기 위한 새로운 시나리오가 열립니다.

데이터를 강화 하는 것과 동일한 기술를 사용 하 여 데이터를 표시할 수도 있습니다. Power BI와 같은 일부 도구는 테이블에서 더 잘 작동 하는 반면, 데이터 과학 워크 로드에는 blob 형식의 복잡 한 데이터 구조가 필요할 수 있습니다. 기술에 [Shaper 기술을](cognitive-search-skill-shaper.md) 추가 하면 데이터의 모양을 제어할 수 있습니다. 그런 다음 이러한 셰이프를 테이블 또는 blob의 프로젝션에 전달 하 여 데이터의 용도에 맞는 물리적 데이터 구조를 만들 수 있습니다.

다음 비디오에서는 이러한 이점과 기타에 대해 모두 설명 합니다.

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="knowledge-store-definition"></a>지식 저장소 정의

기술 자료 저장소는 기술 정의 내에 정의 되며 다음과 같은 두 가지 구성 요소가 있습니다. 

+ Azure Storage에 대 한 연결 문자열

+ 정보 저장소가 테이블, 개체 또는 파일로 구성 되어 있는지 여부를 결정 하는 [**프로젝션**](knowledge-store-projection-overview.md) 입니다. 

프로젝션 요소가 배열인 경우 하나의 기술 자료 저장소 내에서 여러 테이블 개체-파일 조합 집합을 만들 수 있습니다.

```json
"knowledgeStore":{
   "storageConnectionString":"<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>",
   "projections":[
      {
         "tables":[ ],
         "objects":[ ],
         "files":[ ]
      }
   }
```

이 구조에서 지정하는 프로젝션의 형식에 따라 지식 저장소에서 사용하는 스토리지 형식이 결정됩니다.

+ `tables`는 보강된 콘텐츠를 Table Storage에 프로젝션합니다. 분석 도구에 입력하기 위해 테이블 형식 보고 구조가 필요하거나 다른 데이터 저장소로 데이터 프레임을 내보낼 경우 테이블 프로젝션을 정의합니다. 동일한 프로젝션 그룹 내에서 여러 `tables`를 지정하여 보강 문서의 하위 집합이나 단면을 가져올 수 있습니다. 동일한 프로젝션 그룹 내에서 테이블 관계를 사용할 수 있게 보존됩니다.

+ `objects`는 JSON 문서를 Blob Storage에 프로젝션합니다. `object`의 물리적 표현은 보강 문서를 나타내는 계층적 JSON 구조입니다.

+ `files`는 이미지 파일을 Blob Storage에 프로젝션합니다. `file`은 문서에서 추출되어 Blob 스토리지로 그대로 전송되는 이미지입니다. 이름이 "files" 이지만 파일 저장소는 Blob Storage 표시 되지 않습니다.

## <a name="create-a-knowledge-store"></a>지식 저장소 만들기

지식 저장소를 만들려면 포털이나 API를 사용합니다. [Azure Storage](../storage/index.yml), [기술 세트](cognitive-search-working-with-skillsets.md), [인덱서](search-indexer-overview.md)가 필요합니다. 인덱서에는 검색 인덱스가 필요하므로 인덱스 정의도 제공해야 합니다.

완성 된 기술 자료에 대 한 가장 빠른 경로를 위한 포털 방법으로 이동 합니다. 또는 개체가 정의 되 고 관련 되는 방식을 깊이 있게 이해 하려면 REST API을 선택 합니다.

### <a name="azure-portal"></a>[**Azure portal**](#tab/kstore-portal)

**데이터 가져오기** 마법사를 사용 하 여 [**4 단계에서 첫 번째 정보 저장소를 만듭니다**](knowledge-store-create-portal.md) .

1. 데이터 원본 정의

1. 기술을 정의 하 고 기술 자료 저장소를 지정 합니다.

1. 인덱스 스키마를 정의 합니다. 마법사에는 이러한 스키마가 필요하며 사용자를 위해 유추할 수 있습니다.

1. 마법사를 실행합니다. 이 마지막 단계에서 추출, 보강 및 저장이 수행됩니다.

이 마법사는 수동으로 처리 해야 하는 작업을 자동화 합니다. 특히 Azure Storage의 물리적 데이터 구조에 대 한 정의 등의 셰이핑 및 프로젝션이 모두 생성 됩니다. 

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

[**Postman을 사용 하 여 첫 번째 정보 저장소 만들기**](knowledge-store-create-rest.md) 는이 [기술 자료 저장소 컬렉션](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store)에 속하는 개체와 요청을 안내 하는 자습서입니다.

REST API 버전 `2020-06-30`을 사용하면 기술 세트에 추가하여 지식 저장소를 만들 수 있습니다.

+ [기술 세트 만들기(api-version=2020-06-30)](/rest/api/searchservice/create-skillset)
+ [기술 세트 업데이트(api-version=2020-06-30)](/rest/api/searchservice/update-skillset)

기술 내에서:

+ Azure Storage에서 빌드하려는 프로젝션 지정(테이블, 개체, 파일)
+ 기술 세트에 셰이퍼 기술을 포함하여 프로젝션의 스키마와 콘텐츠 확인
+ 프로젝션에 명명된 셰이프 할당

### <a name="net-sdk"></a>[**.NET SDK**](#tab/kstore-dotnet)

.NET 개발자의 경우 Azure.Search.Documents 클라이언트 라이브러리의 [KnowledgeStore 클래스](/dotnet/api/azure.search.documents.indexes.models.knowledgestore)를 사용합니다.

---

<a name="tools-and-apps"></a>

## <a name="connect-with-apps"></a>앱을 사용하여 연결

보강이 스토리지에 존재하면 Azure Blob 또는 Table Storage에 연결하는 도구나 기술을 사용하여 콘텐츠를 검색, 분석 또는 사용할 수 있습니다. 다음 목록으로 시작합니다.

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) - 보강된 문서 구조 및 콘텐츠를 봅니다. 이 도구를 지식 저장소 콘텐츠를 보기 위한 기준선 도구로 고려합니다.

+ 보고 및 분석을 위한 [Power BI](knowledge-store-connect-power-bi.md) 

+ [Azure Data Factory](../data-factory/index.yml) - 추가 조작을 위한 도구입니다.

## <a name="content-lifecycle"></a>콘텐츠 수명 주기

인덱서와 기술 세트를 실행할 때마다 기술 세트나 기본 원본 데이터가 변경된 경우 지식 저장소가 업데이트됩니다. 인덱서에서 선택한 모든 변경 내용이 보강 프로세스를 통해 지식 저장소의 프로젝션에 전파되어 프로젝션된 데이터가 원래 데이터 원본 콘텐츠의 현재 표현이 되도록 합니다. 

> [!Note]
> 프로젝션의 데이터를 편집할 수 있지만 원본 데이터의 문서가 업데이트된 경우 다음에 파이프라인을 호출하면 편집 내용을 모두 덮어씁니다. 

### <a name="changes-in-source-data"></a>원본 데이터의 변경 내용

변경 내용 추적을 지원하는 데이터 원본의 경우 인덱서에서 새 문서와 변경된 문서를 처리하고, 이미 처리된 기존 문서는 무시합니다. 타임스탬프 정보는 데이터 원본에 따라 다르지만 인덱서는 Blob 컨테이너에서 `lastmodified` 날짜를 확인하여 수집해야 하는 Blob을 결정합니다.

### <a name="changes-to-a-skillset"></a>기술 세트의 변경 내용

기술 세트를 변경하는 경우, 가능하면 기존 보강을 재사용하기 위해 [보강된 문서의 캐싱을 사용하도록 설정](cognitive-search-incremental-indexing-conceptual.md)해야 합니다.

증분 캐싱을 사용하지 않는 경우 인덱서는 뒤로 돌아가지 않고 항상 높은 워터마크 순서로 문서를 처리합니다. Blob에서 인덱서는 인덱서 설정이나 기술 세트의 변경 내용과 관계없이 `lastModified`로 정렬된 Blob을 처리합니다. 기술 세트를 변경하는 경우 이전에 처리된 문서는 새 기술 세트를 반영하도록 업데이트되지 않습니다. 기술 세트 변경 후에 처리된 문서는 새 기술 세트를 사용하므로 인덱스 문서에 이전 기술 세트와 새 기술 세트가 모두 포함됩니다.

증분 캐싱을 사용하는 경우 인덱서는 기술 세트 업데이트 후에 기술 세트 변경의 영향을 받지 않은 모든 보강을 재사용합니다. 변경된 기술과 격리된, 독립적인 보강과 마찬가지로 업스트림 보강을 캐시에서 풀합니다.

### <a name="deletions"></a>삭제

인덱서는 Azure Storage의 구조와 콘텐츠를 만들고 업데이트하지만 삭제하지는 않습니다. 인덱서나 기술 세트가 삭제된 경우에도 프로젝션은 계속 존재합니다. 더 이상 필요하지 않은 경우 스토리지 계정의 소유자가 프로젝션을 삭제해야 합니다. 

## <a name="next-steps"></a>다음 단계

지식 저장소는 보강된 문서를 지속적으로 유지하므로 기술 세트를 디자인하거나 Azure Storage 계정에 액세스할 수 있는 모든 클라이언트 애플리케이션에서 사용할 새 구조와 콘텐츠를 만들 때 유용합니다.

보강된 문서를 만드는 가장 간단한 방법은 [포털을 사용](knowledge-store-create-portal.md)하는 것이지만 프로그래매틱 방식으로 개체를 만들고 참조하는 방법에 대한 인사이트를 원하는 경우에 더 유용한 REST API와 Postman을 사용할 수도 있습니다.

> [!div class="nextstepaction"]
> [Postman 및 REST를 사용하여 지식 저장소 만들기](knowledge-store-create-rest.md)
