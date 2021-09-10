---
title: 인덱서 개요
titleSuffix: Azure Cognitive Search
description: 검색 가능한 데이터를 추출하고 Azure Cognitive Search 인덱스를 채우도록 Azure SQL Database, SQL Managed Instance, Azure Cosmos DB 또는 Azure storage를 크롤링합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 7a0ac8a344bc48a9d1ce14b326724236c229d096
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528649"
---
# <a name="indexers-in-azure-cognitive-search"></a>Azure Cognitive Search의 인덱서

Azure Cognitive Search의 *인덱서* 는 외부 Azure 데이터 원본에서 검색 가능한 문자와 메타데이터를 추출하고 인덱스와 원본 데이터 사이의 필드 간 매핑에 따라 검색 인덱스를 채우는 크롤러입니다. 이 접근 방식은 인덱스에 데이터를 추가하는 코드를 작성하지 않고도 서비스에서 데이터를 가져오기 때문에 ‘끌어오기 모델’로도 불립니다. 또한 인덱서는 Cognitive Search의 [AI 보강](cognitive-search-concept-intro.md) 기능을 구동하여 경로에 있는 콘텐츠의 외부 처리를 인덱스로 통합시킵니다.

인덱서는 [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Table Storage](search-howto-indexing-azure-tables.md), [Blob Storage](search-howto-indexing-azure-blob-storage.md)에 대한 개별 인덱서를 사용하는 Azure 전용입니다. 인덱서를 구성할 때 인덱스(대상)뿐만 아니라 데이터 원본(origin)을 지정합니다. Blob Storage와 같은 여러 원본에는 해당 콘텐츠 형식에 특정한 추가 구성 속성이 있습니다.

요청 시 또는 5분 간격으로 반복 실행되는 데이터 새로 고침 일정에 따라 인덱서를 실행할 수 있습니다. 자주 업데이트하려면 Azure Cognitive Search와 외부 데이터 원본 모두에서 데이터를 동시에 업데이트하는 [‘푸시 모델’](search-what-is-data-import.md)이 필요합니다.

## <a name="usage-scenarios"></a>사용 시나리오

인덱서를 데이터 수집의 유일한 수단으로 사용하거나, 필요에 따라 콘텐츠를 로드하고 선택적으로 변환 또는 보강하는 기술 조합의 일부로 사용할 수 있습니다. 주요 시나리오가 다음 테이블에 요약되어 있습니다.

| 시나리오 |전략 |
|----------|---------|
| 단일 데이터 원본 | 가장 간단한 패턴입니다. 하나의 데이터 원본이 검색 인덱스에 대한 유일한 콘텐츠 공급자입니다. 원본에서 검색 인덱스의 문서 키로 사용할 고유 값을 포함 하는 하나의 필드를 식별합니다. 고유 값은 식별자로 사용됩니다. 다른 모든 원본 필드는 암시적으로 또는 명시적으로 인덱스의 해당 필드에 매핑됩니다. </br></br>중요한 요점은 문서 키의 값이 원본 데이터에서 발생한다는 것입니다. 검색 서비스는 키 값을 생성하지 않습니다. 후속 실행에서 새 키가 있는 들어오는 문서가 추가되지만, 기존 키가 있는 들어오는 문서는 인덱스 필드가 Null 인지 아니면 채워져 있는지에 따라 병합되거나 덮어쓰기됩니다. |
| 여러 데이터 원본 | 인덱스는 여러 소스의 콘텐츠를 수락할 수 있습니다. 여기서 각 실행은 다른 소스에서 새 콘텐츠를 가져옵니다. </br></br>한 가지 결과는 각 인덱서가 실행된 후 각 소스에서 전체 문서가 완전히 생성된 후 문서를 얻는 인덱스일 수 있습니다. 예를 들어 문서 1-100는 Blob Storage에서, 문서 101-200는 Azure SQL 등에서 비롯됩니다. 이 시나리오의 과제는 들어오는 모든 데이터에 대해 작동하는 인덱스 스키마와 검색 인덱스에 있는 문서 키 구조를 디자인하는 것입니다. 기본적으로 Blob 컨테이너 및 SQL 테이블의 기본 키에서 문서를 고유하게 식별하는 값은 metadata_storage_path입니다. 콘텐츠 원본에 관계없이 공통 형식으로 키 값을 제공하도록 하나 또는 두 가지 소스를 수정해야 한다고 가정할 수 있습니다. 이 시나리오에서는 단일 인덱스로 끌어올 수 있도록 데이터를 균질화하기 위해 일정 수준의 사전 처리를 수행해야 합니다. </br></br>다른 결과는 첫 번째 실행에서 부분적으로 채워지는 검색 문서일 수 있고, 다른 소스에서 값을 가져오기 위해 후속 실행에 의해 추가로 채워질 수 있습니다. 예를 들어, 필드 1-10은 Blob Storage, 11-20은 Azure SQL 등에서 가져온 것입니다. 이 패턴의 과제는 각 인덱싱 실행이 동일한 문서를 대상으로 하는지 확인하는 것입니다. 필드를 기존 문서에 병합하려면 문서 키와 일치해야 합니다. 이 시나리오의 데모를 보려면 [자습서: 여러 데이터 원본의 인덱스](tutorial-multiple-data-sources.md)를 참조하세요. |
| 여러 인덱서 | 여러 데이터 원본을 사용하는 경우 런타임 매개 변수, 일정 또는 필드 매핑을 변경해야 할 때 여러 인덱서가 필요할 수도 있습니다. 여러 인덱서-데이터 원본 집합은 동일한 인덱스를 대상으로 할 수 있지만 인덱스의 기존 값을 덮어쓸 수 있는 인덱서 실행에 주의해야 합니다. 두 번째 인덱서와 데이터 원본이 동일한 문서 및 필드를 대상으로 하는 경우 첫 번째 실행의 모든 값을 덮어씁니다. 필드 값이 전부 바뀝니다. 인덱서는 여러 실행의 값을 동일한 필드로 병합할 수 없습니다.</br></br>또 다른 다중 인덱서 사용 사례는 [Cognitive Search 외 하위 지역 간 스케일 아웃](search-performance-optimization.md#data-sync)입니다. 서로 다른 하위 지역에 동일한 검색 인덱스의 복사본이 있을 수 있습니다. 검색 인덱스 콘텐츠를 동기화하기 위해, 동일한 데이터 원본에서 여러 개의 인덱서를 끌어올 수 있습니다. 여기서 각 인덱서는 서로 다른 검색 인덱스를 대상으로 합니다.</br></br>매우 큰 데이터 집합의 [병렬 인덱싱](search-howto-large-index.md#parallel-indexing)에서 다중 인덱서 전략이 필요합니다. 각 인덱서는 데이터의 하위 집합을 대상으로 합니다. |
| 콘텐츠 변환 | Cognitive Search는 이미지 분석과 자연어 처리를 추가하여 검색 가능한 새 콘텐츠와 구조를 만드는 선택적 [AI 보강](cognitive-search-concept-intro.md) 동작을 지원합니다. AI 보강은 연결된 [기술 세트](cognitive-search-working-with-skillsets.md)를 통한 인덱서를 기반으로 합니다. AI 보강을 수행하기 위해 인덱서에 인덱스와 Azure 데이터 원본이 필요하지만 해당 시나리오에서는 기술 세트 프로세싱을 인덱서 실행에 추가합니다. |

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>지원되는 데이터 원본

Azure와 Azure 외부의 인덱서 크롤링 데이터 저장소.

+ [Amazon Redshift](search-how-to-index-power-query-data-sources.md)(미리 보기)
+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Azure MySQL](search-howto-index-mysql.md)(미리 보기)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Elasticsearch](search-how-to-index-power-query-data-sources.md)(미리 보기)
+ [PostgreSQL](search-how-to-index-power-query-data-sources.md)(미리 보기)
+ [Salesforce 개체](search-how-to-index-power-query-data-sources.md)(미리 보기)
+ [Salesforce 보고서](search-how-to-index-power-query-data-sources.md)(미리 보기)
+ [Smartsheet](search-how-to-index-power-query-data-sources.md)(미리 보기)
+ [Snowflake](search-how-to-index-power-query-data-sources.md)(미리 보기)
+ [SQL Managed Instance](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [Azure Virtual Machines의 SQL Server](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

클라이언트 앱에 대한 Azure 가상 네트워크를 사용하는 경우, 표준 인터넷 연결(공용) 또는 암호화된 개인 연결을 사용하여 원격 데이터 원본에 대한 인덱서 연결을 만들 수 있습니다. 또한 신뢰할 수 있는 서비스 ID를 사용하여 인증하도록 연결을 설정할 수 있습니다. 보안 연결에 대한 자세한 내용은 [프라이빗 엔드포인트를 통해 액세스 권한 부여](search-indexer-securing-resources.md#granting-access-via-private-endpoints) 및 [관리 ID를 사용하여 데이터 원본에 연결](search-howto-managed-identities-data-sources.md)을 참조하세요.

## <a name="stages-of-indexing"></a>인덱싱 단계

초기 실행 시 인덱스가 비어 있으면 인덱서는 테이블이나 컨테이너에 제공된 모든 데이터를 읽습니다. 후속 실행에서 인덱서는 일반적으로 변경된 데이터만 검색할 수 있습니다. Blob 데이터의 경우 변경 검색은 자동으로 검색됩니다. Azure SQL 또는 Cosmos DB 같은 다른 데이터 원본의 경우 변경 검색을 사용하도록 설정해야 합니다.

인덱서는 수신하는 각 문서에 대하여, 인덱싱을 위한 문서 검색에서 최종 검색 엔진 ‘전달’까지 여러 단계를 구현하거나 조정합니다. 필요에 따라 기술 세트가 정의되어 있다고 가정하고, 인덱서는 [기술 세트 실행 및 출력](cognitive-search-concept-intro.md)을 구동합니다.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="인덱서 스테이지" border="false":::

<a name="document-cracking"></a>

### <a name="stage-1-document-cracking"></a>스테이지 1: 문서 크래킹

문서 크래킹은 파일을 열고 콘텐츠를 추출하는 프로세스입니다. 텍스트 기반 콘텐츠는 서비스의 파일, 테이블의 행, 컨테이너, 컬렉션의 항목에서 추출할 수 있습니다. 기술 및 [이미지 기술](cognitive-search-concept-image-scenarios.md)을 인덱서에 추가하는 경우, 문서 크래킹은 이미지를 추출하고 처리를 위해 큐에 대기할 수도 있습니다.

데이터 원본에 따라 인덱서는 잠재적으로 인덱싱 가능한 콘텐츠를 추출하기 위해 다른 작업을 수행합니다.

+ 문서가 [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#supported-document-formats) 내의 PDF 또는 기타 지원되는 파일 형식과 같은 파일인 경우, 인덱서가 파일을 열고 텍스트, 이미지, 메타데이터를 추출합니다. 인덱서가 [SharePoint](search-howto-index-sharepoint-online.md#supported-document-formats) 및 [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md#supported-document-formats)에서 파일을 열 수도 있습니다.

+ 문서가 [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) 레코드인 경우, 인덱서가 각 레코드의 각 필드에서 이진이 아닌 콘텐츠를 추출합니다.

+ 문서가 [Cosmos DB](search-howto-index-cosmosdb.md)의 기록인 경우, 인덱서는 Cosmos DB 문서의 필드 및 하위 필드에서 이진이 아닌 콘텐츠를 추출합니다.

### <a name="stage-2-field-mappings"></a>스테이지 2: 필드 매핑 

인덱서는 원본 필드에서 문자를 추출하여 인덱스나 정보 저장소의 대상 필드에 보냅니다. 필드 이름과 형식이 일치하는 경우 경로는 명확합니다. 그러나 출력에 다른 이름이나 형식이 필요할 수 있으며, 이 경우에 필드를 매핑하는 방법을 인덱서에 알려야 합니다. 

해당 단계는 문서 크래킹 후, 변환 전 인덱서가 원본 문서를 읽을 때 발생합니다. [필드 매핑](search-indexer-field-mappings.md)을 정의할 때 원본 필드의 값은 수정 없이 대상 필드에 그대로 보내집니다. 

### <a name="stage-3-skillset-execution"></a>스테이지 3: 기술 세트 실행

기술 세트 실행은 기본 제공 또는 사용자 지정 AI 처리를 호출하는 선택적 단계입니다. 원본 데이터가 이진 이미지인 경우에는 이미지 분석 형식의 OCR(광학 문자 인식)에 필요할 수도 있고, 콘텐츠가 다른 언어로 되어 있는 경우 언어 번역이 필요할 수도 있습니다. 

어떤 변환이든 보강이 발생하는 곳에 기술 세트를 실행합니다. 인덱서가 파이프라인인 경우 [기술 세트](cognitive-search-defining-skillset.md)를 ‘파이프라인 내의 파이프라인’이라고 생각할 수 있습니다.

### <a name="stage-4-output-field-mappings"></a>스테이지 4: 출력 필드 매핑

기술 세트를 포함하는 경우 출력 필드 매핑을 포함해야 할 가능성이 높습니다. 기술 세트 출력은 사실 *보강 문서* 라고 하는 정보의 트리입니다. 출력 필드 매핑을 사용하여 인덱스의 필드에 매핑할 해당 트리의 파트를 선택할 수 있습니다. [출력 필드 매핑을 정의](cognitive-search-output-field-mapping.md)하는 방법 알아보기.

필드 매핑은 데이터 원본의 축자 값을 대상 필드에 연결하는 반면, 출력 필드 매핑은 보강 문서의 변환된 값을 인덱스의 대상 필드에 연결하는 방법을 인덱서에 알려줍니다. 선택적인 것으로 간주되는 필드 매핑과 달리, 인덱스에 상주해야 하는 변형된 콘텐츠에 대해서는 항상 출력 필드 매핑을 정의해야 합니다.

다음 이미지는 문서 크래킹, 필드 매핑, 기술 세트 실행 및 출력 필드 매핑과 같은 인덱서 스테이지의 샘플 인덱서 [디버그 세션](cognitive-search-debug-session.md) 표현을 보여 줍니다.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="샘플 디버그 세션" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>기본 워크플로

인덱서는 데이터 원본에 고유한 기능을 제공할 수 있습니다. 이러한 점에서 인덱서 또는 데이터 원본 구성의 일부 측면은 인덱서 유형에 따라 달라집니다. 그러나 인덱서는 모두 동일한 기본 구성 및 요구 사항을 공유합니다. 인덱서 모두에 공통되는 단계는 아래에서 다룹니다.

### <a name="step-1-create-a-data-source"></a>1단계: 데이터 소스 만들기

인덱서에는 연결 문자열 및 자격 증명을 제공하는 *데이터 원본* 개체가 필요합니다. [데이터 원본 만들기(REST)](/rest/api/searchservice/create-data-source) 또는 [SearchIndexerDataSourceConnection 클래스](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)를 호출하여 리소스를 만듭니다.

데이터 소스는 데이터 소스를 사용하는 인덱서와는 별도로 구성 및 관리됩니다. 즉 데이터 소스를 여러 인덱서에서 사용하여 한 번에 둘 이상의 인덱스를 로드할 수 있습니다.

### <a name="step-2-create-an-index"></a>2단계: 인덱스 만들기

인덱서는 데이터 수집과 관련된 몇 가지 작업을 자동화하지만 인덱스를 만드는 작업은 일반적으로 포함되지 않습니다. 필수 구성 요소로서 외부 데이터 원본의 인덱스와 일치하는 필드를 포함한 미리 정의된 인덱스가 있어야 합니다. 필드는 이름 및 데이터 형식으로 일치해야 합니다. 그렇지 않은 경우 [필드 매핑을 정의](search-indexer-field-mappings.md)하여 연결을 설정할 수 있습니다. 인덱스를 구조화하는 방법에 대한 자세한 내용은 [인덱스 만들기(REST)](/rest/api/searchservice/Create-Index) 또는 [SearchIndex 클래스](/dotnet/api/azure.search.documents.indexes.models.searchindex)를 참조하세요.

> [!Tip]
> 인덱서가 인덱스를 생성할 수 없지만 포털에서 **데이터 가져오기** 마법사를 통해 가능합니다. 대부분의 경우 마법사는 원본에서 기존 메타데이터의 인덱스 스키마를 유추할 수 있고 마법사가 활성화되어 있는 동안 인라인으로 편집할 수 있는 예비 인덱스 스키마를 표시합니다. 서비스에서 인덱스가 생성되면 포털에서 추가 편집 작업은 새 필드를 추가하는 것으로 제한됩니다. 인덱스를 수정하지 않고 만들기 위해 마법사를 사용합니다. 자동 학습은 [포털 연습](search-get-started-portal.md)의 단계를 수행합니다.

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>3단계: 인덱서 만들기 및 실행(또는 예약)

검색 서비스에서 처음 [인덱서를 만들](/rest/api/searchservice/Create-Indexer) 때 인덱서가 실행됩니다. 데이터 원본에 액세스할 수 있는지 또는 기술 세트가 유효한지 확인하는 인덱서를 만들거나 실행하는 경우에만 해당합니다. 첫 번째 실행 후 [인덱서 실행](/rest/api/searchservice/run-indexer)을 사용하여 요청 시 다시 실행하거나 [되풀이 일정을 정의](search-howto-schedule-indexers.md)할 수 있습니다. 

포털 또는 [인덱서 상태 가져오기 API](/rest/api/searchservice/get-indexer-status)를 통해 인덱서 상태를 모니터링할 수 있습니다. 또한 [인덱스에 대하여 쿼리 실행](search-query-create.md)을 통해 결과가 예상한 결과인지 확인해야 합니다.

## <a name="next-steps"></a>다음 단계

이제 소개가 끝났으니, 다음 단계는 인덱서 속성과 매개 변수, 예약 및 인덱서 모니터링 검토입니다. 또는 특정 소스에 대한 자세한 내용을 보려면 [지원되는 데이터 원본](#supported-data-sources) 목록을 다시 참조하면 됩니다.

+ [인덱서 만들기](search-howto-create-indexers.md)
+ [인덱서를 다시 설정하여 실행하기](search-howto-run-reset-indexers.md)
+ [인덱서 일정](search-howto-schedule-indexers.md)
+ [필드 매핑 정의](search-indexer-field-mappings.md)
+ [인덱서 상태 모니터링](search-howto-monitor-indexers.md)