---
title: 지식 저장소 개념
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 및 다른 애플리케이션에서 보강된 문서를 보고, 변형시키고, 사용할 수 있는 Azure Storage에 해당 문서를 보냅니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: baa4a78e23b83fa7c138e71b92dba12ba23a3ab6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528702"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Azure Cognitive Search의 지식 저장소

지식 저장소는 독립적인 분석이나 다운스트림 처리를 위해 [AI 보강 파이프라인](cognitive-search-concept-intro.md)의 출력을 Azure Storage에 저장하는 Azure Cognitive Search의 한 기능입니다. *보강된 문서* 는 AI 프로세스를 사용하여 추출, 구조화 및 분석된 콘텐츠에서 만든 파이프라인의 출력입니다. 표준 AI 파이프라인에서 보강된 문서는 일시적이며 인덱싱 중에만 사용된 후에 삭제됩니다. 지식 저장소를 만들면 보강된 문서가 검사나 기타 지식 마이닝 시나리오를 위해 보존됩니다.

과거에 인지 검색을 사용한 적이 있으면 *기술 세트* 를 사용하여 보강 시퀀스를 통해 문서를 이동시킨다는 것을 이미 알고 있을 것입니다. 결과는 검색 인덱스이거나 지식 저장소의 프로젝션일 수 있습니다. 검색 인덱스와 지식 저장소의 두 출력은 동일한 파이프라인의 부산물입니다. 동일한 입력에서 파생되지만 출력은 서로 다른 애플리케이션에서 구조화, 저장, 사용됩니다.

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="기술 세트가 있는 파이프라인" border="false":::

<!-- previous version of the architecture diagram
:::image type="content" source="media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png" alt-text="Knowledge store in pipeline diagram" border="false"::: -->

물리적으로 지식 저장소는 [Azure Storage](../storage/common/storage-account-overview.md)이며 Azure Table Storage, Azure Blob Storage 또는 둘 다 해당합니다. Azure Storage에 연결할 수 있는 모든 도구 또는 프로세스는 지식 저장소의 콘텐츠를 사용할 수 있습니다.

Storage Explorer를 통해 표시된 지식 저장소는 테이블, 개체 또는 파일 모음일 뿐입니다. 다음 예제에서는 데이터 원본에서 전달되었거나 보강을 통해 생성된 필드가 있는 3개의 테이블로 구성된 지식 저장소를 보여 줍니다(“감정 점수” 및 “translated_text” 참조).

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="보강 트리에서 읽기 및 쓰기 기술" border="true":::

## <a name="benefits-of-knowledge-store"></a>지식 저장소의 이점

지식 저장소는 BLOB과 같은 비정형 및 준정형 데이터 파일, 분석을 실행한 이미지 파일 또는 새 양식으로 변형된 정형 데이터에서 수집한 구조, 상황 및 실제 콘텐츠를 제공합니다. [단계별 연습](knowledge-store-create-rest.md)에서 밀도가 높은 JSON 문서를 하위 구조로 분할하고, 새 구조로 재구성하고, 다른 방법으로 기계 학습 및 데이터 과학 워크로드와 같은 다운스트림 프로세스에 사용할 수 있도록 만드는 방법을 미리 확인할 수 있습니다.

AI 보강 파이프라인에서 생성할 수 있는 기능을 확인하는 것이 유용하지만 지식 저장소의 진정한 잠재력은 데이터를 변형시키는 능력입니다. 기본 기술 세트로 시작하고, 이를 반복하여 증가하는 수준의 구조를 추가한 다음, Azure Cognitive Search 이외의 다른 앱에서 사용할 수 있는 새 구조로 결합할 수 있습니다.

지식 저장소의 이점을 자세히 열거하면 다음과 같습니다.

+ 보강된 문서를 검색 이외의 [분석 및 보고 도구](#tools-and-apps)에 사용합니다. 파워 쿼리 포함 Power BI는 매력적인 선택이지만 Azure Storage에 연결할 수 있는 모든 도구 또는 앱은 사용자가 만드는 지식 저장소에서 끌어올 수 있습니다.

+ 단계 및 기술 세트 정의를 디버깅하는 동안 AI 인덱싱 파이프라인을 정교화합니다. 지식 저장소는 AI 인덱싱 파이프라인에 있는 기술 세트 정의의 생성물을 보여줍니다. 보강이 어떻게 보이는지 정확히 확인할 수 있으므로 해당 결과를 사용하여 더 나은 기술 세트를 디자인할 수 있습니다. Azure Storage의 [Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)를 사용하여 지식 저장소의 콘텐츠를 볼 수 있습니다.

+ 데이터를 새 양식으로 변형합니다. 셰이핑 조정은 기술 세트로 코딩되지만 Azure Cognitive Search의 [셰이퍼 기술](cognitive-search-skill-shaper.md)은 명시적 제어와 여러 셰이프를 만드는 기능을 제공합니다. 셰이핑을 사용하면 관계를 유지하면서 데이터의 사용 목적에 맞는 프로젝션을 정의할 수 있습니다.

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="knowledge-store-definition"></a>지식 저장소 정의

인덱서 실행 중에 Azure Table Storage, Azure Blob Storage 또는 둘 다를 사용하여 [Azure Storage 계정](../storage/common/storage-account-overview.md)에 지식 저장소가 생성됩니다. 

Azure Storage 내의 데이터 구조는 기술 세트에서 `knowledgeStore` 정의의 `projections` 요소를 통해 지정됩니다. [프로젝션](knowledge-store-projection-overview.md)은 테이블, 개체 또는 파일로 표현될 수 있으며, 여러 세트(또는 ‘프로젝션 그룹’)를 사용하여 각기 다른 용도로 여러 데이터 구조를 만들 수 있습니다.

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

+ `files`는 이미지 파일을 Blob Storage에 프로젝션합니다. `file`은 문서에서 추출되어 Blob 스토리지로 그대로 전송되는 이미지입니다.

## <a name="create-a-knowledge-store"></a>지식 저장소 만들기

지식 저장소를 만들려면 포털이나 API를 사용합니다. [Azure Storage](../storage/index.yml), [기술 세트](cognitive-search-working-with-skillsets.md), [인덱서](search-indexer-overview.md)가 필요합니다. 인덱서에는 검색 인덱스가 필요하므로 인덱스 정의도 제공해야 합니다.

### <a name="azure-portal"></a>[**Azure portal**](#tab/kstore-portal)

**데이터 가져오기** 마법사를 사용하여 [4단계로 첫 번째 지식 저장소를 만듭니다](knowledge-store-connect-power-bi.md). 마법사에서 안내하는 작업은 다음과 같습니다.

1. 원시 콘텐츠를 제공하는 지원되는 데이터 원본을 선택합니다.

1. 보강 지정: Cognitive Services 리소스를 연결하고 기술을 선택한 다음, 지식 저장소를 지정합니다. 이 단계에서 Azure Storage 계정을 선택하고 개체, 테이블 또는 둘 다 만들지 여부를 선택합니다.

1. 인덱스 스키마를 만듭니다. 마법사에는 이러한 스키마가 필요하며 사용자를 위해 유추할 수 있습니다.

1. 마법사를 실행합니다. 이 마지막 단계에서 추출, 보강 및 저장이 수행됩니다.

마법사를 사용하는 경우 코드에서 처리해야 하는 몇 가지 추가 작업이 내부적으로 처리됩니다. 셰이핑과 프로젝션(Azure Storage의 물리적 데이터 구조 정의)이 모두 자동으로 생성됩니다. 지식 저장소를 수동으로 만드는 경우 코드에서 해당 단계를 처리해야 합니다.

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

REST API 버전 `2020-06-30`을 사용하면 기술 세트에 추가하여 지식 저장소를 만들 수 있습니다.

+ [기술 세트 만들기(api-version=2020-06-30)](/rest/api/searchservice/create-skillset)
+ [기술 세트 업데이트(api-version=2020-06-30)](/rest/api/searchservice/update-skillset)

`knowledgeStore`는 [기술 세트](cognitive-search-working-with-skillsets.md) 내에 정의되며, [인덱서](search-indexer-overview.md)에서 호출됩니다. Azure Cognitive Search는 보강하는 동안 공간을 Azure Storage 계정에 만들고, 구성에 따라 보강된 문서를 Blob 또는 테이블로 프로젝션합니다.

코드에서 처리해야 하는 작업:

+ Azure Storage에서 빌드하려는 프로젝션 지정(테이블, 개체, 파일)
+ 기술 세트에 셰이퍼 기술을 포함하여 프로젝션의 스키마와 콘텐츠 확인
+ 프로젝션에 명명된 셰이프 할당

쉽게 살펴보는 방법은 [Postman을 사용하여 첫 번째 지식 저장소를 만드는 것](knowledge-store-create-rest.md)입니다.

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

또는 [프로젝션](knowledge-store-projection-overview.md)을 자세히 살펴봅니다. 조각화, 인라인 셰이핑, 관계 등의 고급 프로젝션 개념을 보여 주는 예제를 살펴보려면 [지식 저장소의 프로젝션 정의](knowledge-store-projections-examples.md)에서 시작합니다.
