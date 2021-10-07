---
title: Azure Portal을 사용하여 검색 인덱스로 데이터 가져오기
titleSuffix: Azure Cognitive Search
description: 인덱스를 만들고 로드 하는 데 사용 되는 Azure Portal의 데이터 가져오기 마법사에 대해 알아보고, 자연어 처리, 번역, OCR 및 이미지 분석을 위한 기본 제공 기술을 사용 하 여 AI 보강를 선택적으로 호출 합니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: 169689e34339f33b1a1477056612b1801b2d9fef
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614197"
---
# <a name="import-data-wizard-in-azure-cognitive-search"></a>Azure Cognitive Search에서 데이터 가져오기 마법사

Azure Portal의 **데이터 가져오기 마법사** 는 검색 서비스에서 인덱싱 및 AI 보강 사용 되는 여러 개체를 만듭니다. Azure Cognitive Search을 처음 접하는 경우 가장 강력한 기능 중 하나입니다. 최소한의 노력으로 Azure Cognitive Search의 기능을 대부분 사용 하는 인덱싱 또는 보강 파이프라인을 만들 수 있습니다.

개념 증명 테스트를 위해 마법사를 사용 하는 경우이 문서에서는 더 효과적으로 사용할 수 있도록 마법사의 내부 기능을 설명 합니다.

이 문서는 단계별로 설명 하지 않습니다. 기본 제공 샘플 데이터와 함께 마법사를 사용 하는 방법에 대 한 도움말은 [빠른 시작: 검색 인덱스 만들기](search-get-started-portal.md) 또는 [빠른 시작: 기술 만들기](cognitive-search-quickstart-blob.md)를 참조 하세요.

## <a name="starting-the-wizard"></a>마법사 시작

[Azure Portal](https://portal.azure.com)의 대시보드에서 검색 서비스 페이지를 열거나 서비스 목록에서 [서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 위쪽의 서비스 개요 페이지에서 **데이터 가져오기** 를 클릭 합니다.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="데이터 가져오기 명령의 스크린샷" border="true":::

마법사가 브라우저 창에서 완전히 확장 되어 작업 공간을 늘릴 수 있습니다. 여러 페이지가 매우 조밀 하 게 됩니다.

Azure Cosmos DB, Azure SQL Database, SQL Managed Instance 및 Azure Blob Storage를 비롯한 다른 Azure 서비스에서도 **데이터 가져오기** 를 시작할 수 있습니다. 서비스 개요 페이지의 왼쪽 탐색 창에서 **Azure Cognitive Search 추가** 를 찾아보세요.

## <a name="objects-created-by-the-wizard"></a>마법사에서 만든 개체

마법사가 다음 테이블의 개체를 출력 합니다. 개체를 만든 후에는 포털에서 해당 JSON 정의를 검토 하거나 코드에서 호출할 수 있습니다.

| Object | 설명 | 
|--------|-------------|
| [인덱서](/rest/api/searchservice/create-indexer)  | 오류 처리 및 64기반 인코딩을 위한 데이터 원본, 대상 인덱스, 선택적 기술 세트, 선택적 일정 및 선택적 구성 설정을 지정하는 구성 개체입니다. |
| [데이터 원본](/rest/api/searchservice/create-data-source)  | 자격 증명을 포함하여 원본 데이터에 대한 연결 정보를 유지합니다. 데이터 원본 개체는 인덱서에 독점적으로 사용됩니다. | 
| [Index](/rest/api/searchservice/create-index) | 전체 텍스트 검색 및 기타 쿼리에 사용되는 실제 데이터 구조입니다. | 
| [기술 집합](/rest/api/searchservice/create-skillset) | (선택 사항) 이미지 파일에서 정보를 분석하고 추출하는 것을 포함하여 콘텐츠를 조작, 변형 및 셰이핑하기 위한 전체 지침 집합입니다. 매우 간단하고 제한된 구조체를 제외하고 보강을 제공하는 Cognitive Services 리소스 참조가 포함됩니다. | 
| [지식 저장소](knowledge-store-concept-intro.md) | (선택 사항) 는 [AI 보강 파이프라인](cognitive-search-concept-intro.md) 의 출력을 독립적인 분석 또는 다운스트림 처리를 위해 Azure Storage의 테이블 및 blob에 저장 합니다. |

## <a name="benefits-and-limitations"></a>혜택 및 제한

코드를 작성 하기 전에 프로토타입 및 개념 증명 테스트를 위해 마법사를 사용할 수 있습니다. 마법사는 외부 데이터 원본에 연결하고 데이터를 샘플링하여 초기 인덱스를 만든 다음 데이터를 JSON 문서로 Azure Cognitive Search 인덱스에 가져옵니다. 

기술력과를 평가 하는 경우 마법사가 모든 출력 필드 매핑을 처리 하 고 도우미 함수를 추가 하 여 사용 가능한 개체를 만듭니다. 구문 분석 모드를 지정 하면 텍스트 분할이 추가 됩니다. 마법사에서 이미지 내용으로 텍스트 설명을 reunite 수 있도록 이미지 분석을 선택한 경우 텍스트 병합이 추가 됩니다. 기술 자료 저장소 옵션을 선택한 경우 유효한 프로젝션을 지원 하기 위해 Shaper 기술이 추가 되었습니다. 위의 모든 작업에는 학습 곡선이 제공 됩니다. 보강을 처음 사용 하는 경우 이러한 단계를 처리 하는 기능을 통해 많은 시간과 노력 없이도 기술 가치를 측정할 수 있습니다.

샘플링은 인덱스 스키마를 유추 하는 프로세스로, 몇 가지 제한 사항이 있습니다. 데이터 원본을 만들면 마법사는 임의의 문서 샘플을 선택 하 여 데이터 원본의 일부인 열을 결정 합니다. 초대형 데이터 원본의 경우 몇 시간이 걸릴 수 있으므로 모든 파일을 읽는 것은 아닙니다. 문서를 선택하면 필드 이름이나 형식과 같은 원본 메타데이터를 사용하여 인덱스 스키마에 필드 컬렉션을 만듭니다. 원본 데이터의 복잡성에 따라 정확도를 위해 초기 스키마를 편집하거나 완결성을 위해 확장해야 할 수 있습니다. 인덱스 정의 페이지에서 변경 내용을 인라인으로 만들 수 있습니다.

전반적으로 마법사를 사용하면 다음과 같은 이점이 있습니다: 요구 사항이 충족되면 몇 분 내에 쿼리 가능한 인덱스의 프로토타입을 만들 수 있습니다. 데이터를 JSON 문서로 serialize 하는 것과 같은 인덱싱의 일부 복잡성은 마법사에서 처리 됩니다.

이 마법사는 제한 없이 사용 됩니다. 제약 조건은 다음과 같이 요약 됩니다.

+ 마법사는 반복 또는 재사용을 지원하지 않습니다. 마법사의 각 단계에서 새 인덱스, 기술 세트 및 인덱서 구성을 만듭니다. 데이터 원본만 유지되며 마법사에서 재사용할 수 있습니다. 다른 개체를 편집 하거나 구체화 하려면 개체를 삭제 하 고 다시 시작 하거나 REST Api 또는 .NET SDK를 사용 하 여 구조를 수정 합니다.

+ 원본 콘텐츠는 [지원 되는 데이터 원본](search-indexer-overview.md#supported-data-sources)에 있어야 합니다.

+ 샘플링은 원본 데이터의 하위 집합에 대한 것입니다. 대형 데이터 원본의 경우 마법사에서 필드를 누락할 수 있습니다. 샘플링이 충분하지 않은 경우 스키마를 확장하거나 유추된 데이터 형식을 수정해야 할 수 있습니다.

+ 포털에 표시 되는 AI 보강는 기본 제공 기술의 하위 집합으로 제한 됩니다. 

+ 마법사에서 만들 수 있는 [기술 자료 저장소](knowledge-store-concept-intro.md)는 몇 가지 기본 프로젝션으로 제한 되며 기본 명명 규칙을 사용 합니다. 이름 또는 프로젝션을 사용자 지정 하려면 REST 또는 Sdk를 통해 기술 자료 저장소를 만들어야 합니다.

## <a name="workflow"></a>워크플로

마법사는 네 가지 주요 단계로 구성 됩니다.

1. 지원 되는 Azure 데이터 원본에 커넥트 합니다.

1. 원본 데이터를 샘플링 하 여 유추 되는 인덱스 스키마를 만듭니다.

1. 필요에 따라 AI 강화를 추가 하 여 콘텐츠 및 구조를 추출 하거나 생성 합니다. 기술 자료 저장소 만들기에 대 한 입력은이 단계에서 수집 됩니다.

1. 마법사를 실행 하 여 개체를 만들고, 데이터를 로드 하 고, 일정 및 기타 구성 옵션을 설정 합니다.

<a name="data-source-inputs"></a>

### <a name="data-source-configuration-in-the-wizard"></a>마법사의 데이터 원본 구성

**데이터 가져오기** 마법사는 azure Cognitive Search 인덱서에 제공 되는 내부 논리를 사용 하 여 지원 되는 외부 [데이터 원본](search-indexer-overview.md#supported-data-sources) 에 연결 합니다 .이는 소스를 샘플링 하 고, 메타 데이터를 읽고, 콘텐츠와 구조를 읽고, azure Cognitive Search에 대 한 후속 가져오기를 위한 JSON으로 콘텐츠를 serialize 하는 데 사용 됩니다.

일부 미리 보기 데이터 원본은 마법사에서 사용할 수 없는 것으로 보장 됩니다. 각 데이터 원본은 다른 변경 내용 다운스트림을 도입할 가능성이 있으므로 기술 정의 및 인덱스 스키마 유추와 같은 마법사의 모든 환경을 완벽 하 게 지 원하는 경우에만 데이터 원본 목록에 미리 보기 데이터 원본이 추가 됩니다.

단일 테이블, 데이터베이스 보기 또는 해당 데이터 구조에서만 가져올 수 있지만 구조에는 계층 구조 또는 중첩된 하위 구조가 포함될 수 있습니다. 자세한 내용은 [복합 형식 모델링 방법](search-howto-complex-data-types.md)을 참조하세요.

### <a name="skillset-configuration-in-the-wizard"></a>마법사의 기술 구성

데이터 원본 유형이 특정 기본 제공 기술에 대 한 가용성을 알리기 때문에 기술 구성은 데이터 원본 정의 후에 수행 됩니다. 특히 Blob Storage에서 파일을 인덱싱하는 경우 해당 파일의 구문 분석 모드를 선택 하면 감정 분석을 사용할 수 있는지 여부가 결정 됩니다.

그러면 마법사에서 선택한 기술을 추가 하지만 성공적인 결과를 달성 하는 데 필요한 다른 기술도 추가 합니다. 예를 들어 기술 자료 저장소를 지정 하는 경우 마법사는 Shaper 기술을 추가 하 여 예측 (또는 실제 데이터 구조)을 지원 합니다.

기술력과은 선택 사항이 며 AI 보강을 원하지 않는 경우 페이지 맨 아래에 표시 되는 단추를 선택 하 여 계속 건너뛸 수 있습니다.

<a name="index-definition"></a>

### <a name="index-schema-configuration-in-the-wizard"></a>마법사의 인덱스 스키마 구성

마법사는 데이터 원본을 샘플링 하 여 필드와 필드 형식을 검색 합니다. 데이터 원본에 따라 메타 데이터 인덱싱에 대 한 필드도 제공 될 수도 있습니다.

샘플링은 정확 하지 않은 연습 이므로 인덱스를 검토 하 여 다음 사항을 고려 하십시오.

1. 필드 목록이 정확한 가요? 샘플링에서 선택 되지 않은 필드가 데이터 원본에 포함 되어 있는 경우 샘플링이 누락 된 새 필드를 수동으로 추가 하 고, 검색 환경에 값을 추가 하지 않거나 [필터 식](search-query-odata-filter.md) 또는 [점수 매기기 프로필](index-add-scoring-profiles.md)에 사용 되지 않는 모든 필드를 제거할 수 있습니다.

1. 데이터 형식이 들어오는 데이터에 적합한가요? Azure Cognitive Search는 [EDM(엔터티 데이터 모델) 데이터 형식](/rest/api/searchservice/supported-data-types)을 지원합니다. Azure SQL 데이터의 경우 동등한 값을 레이아웃하는 [매핑 차트](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping)가 있습니다. 자세한 배경은 [필드 매핑 및 변환](search-indexer-field-mappings.md)을 참조하세요.

1. *키* 로 사용할 수 있는 필드가 하나 있나요? 이 필드는 Edm.string이어야 하며 문서를 고유하게 식별해야 합니다. 관계형 데이터의 경우 기본 키에 매핑 될 수 있습니다. Blob의 경우 `metadata-storage-path`일 수 있습니다. 필드 값에 공백 또는 대시를 포함하는 경우 **고급 옵션** 의 **인덱서 만들기** 단계에서 **Base-64 인코딩 키** 옵션을 설정하여 이러한 문자에 대한 유효성 검사를 비활성화해야 합니다.

1. 특성을 설정하여 인덱스에서 해당 필드가 사용되는 방법을 결정합니다. 

   특성이 인덱스에 있는 필드의 실제 식을 결정하므로 이 단계를 신중하게 진행하세요. 나중에 프로그래밍 방식으로라도 특성을 변경하려면 대부분의 경우 인덱스를 끊고 다시 빌드해야 합니다. **검색 가능(Searchable)** 및 **검색 가능(Retrievable)** 과 같은 핵심 특성은 [스토리지에 미치는 영향을 무시](search-what-is-an-index.md#index-size)할 수 있습니다. 필터를 사용하도록 설정하거나 제안기를 사용하면 스토리지 요구 사항이 증가합니다. 

   + **검색 가능** 을 선택하면 전체 텍스트를 검색할 수 있습니다. 자유 형식 쿼리 또는 쿼리 식에 사용되는 모든 필드에는 이 특성이 있어야 합니다. **검색 가능** 으로 표시한 각 필드에 대해 반전된 인덱스가 만들어집니다.

   + **검색 가능** 은 검색 결과에 필드를 반환합니다. 검색 결과에 콘텐츠를 제공하는 모든 필드에는 이 특성이 있어야 합니다. 이 필드를 설정해도 인덱스 크기는 두드러지게 영향을 받지 않습니다.

   + **필터링 가능** 을 선택하면 필드를 필터 식에서 참조할 수 있습니다. **$filter** 식에 사용되는 모든 필드는 이 특성이 있어야 합니다. 필터 식은 정확한 일치를 위한 것입니다. 텍스트 문자열은 그대로 유지되므로 축자 콘텐츠를 수용하기 위해 더 많은 스토리지가 필요합니다.

   + **패싯 가능** 을 통해 필드를 패싯 탐색에 사용할 수 있습니다. **필터링 가능** 으로도 표시되는 필드만 **패싯 가능** 으로 표시될 수 있습니다.

   + **정렬 가능** 을 선택하면 필드를 정렬에 사용할 수 있습니다. **$Orderby** 식에 사용되는 모든 필드는 이 특성이 있어야 합니다.

1. [어휘 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)이 필요한가요? **검색 가능한** Edm.string 필드의 경우 언어 향상 인덱싱 및 쿼리를 원하는 경우 **분석기** 를 설정할 수 있습니다. 

   기본값은 표준 Lucene이지만 불규칙한 명사 및 동사 형태를 확인하는 것과 같은 고급 어휘 처리에 Microsoft의 분석기를 사용하려는 경우 Microsoft 영어를 선택할 수도 있습니다. 언어 분석기만 포털에서 지정할 수 있습니다. 사용자 지정 분석기 또는 키워드, 패턴, 등과 같은 비언어 분석기를 사용하려면 프로그래밍 방식으로 수행해야 합니다. 분석기에 대한 자세한 내용은 [언어 분석기 추가](search-language-support.md)를 참조하세요.

1. 자동 완성 또는 제안된 결과 형식의 자동 완성 기능이 필요한가요? 선택한 필드에 [자동 완성 쿼리 제안 및 자동 완성](index-add-suggesters.md)을 사용하도록 설정하려면 **제안기** 의 확인란을 선택합니다. 제안기는 인덱스의 토큰화 된 용어 수에 추가되므로 더 많은 스토리지를 소비합니다.

### <a name="indexer-configuration-in-the-wizard"></a>마법사의 인덱서 구성

마법사의 마지막 페이지에서는 인덱서 구성에 대한 사용자 입력을 수집합니다. 일정을 [지정하고](search-howto-schedule-indexers.md) 데이터 원본 유형에 따라 다른 옵션을 설정할 수 있습니다.

내부적으로 마법사는 다음도 설정합니다. 이 설정은 생성될 때까지 인덱서에 표시되지 않습니다.

+ 데이터 [원본과 인덱스](search-indexer-field-mappings.md) 간의 필드 매핑
+ 기술 [출력과 인덱스](cognitive-search-output-field-mapping.md) 간의 출력 필드 매핑

## <a name="next-steps"></a>다음 단계

마법사의 이점과 제한 사항을 이해하는 가장 좋은 방법은 단계별로 진행하는 것입니다. 다음 빠른 시작에서는 각 단계를 안내합니다.

> [!div class="nextstepaction"]
> [빠른 시작: Azure Portal 사용하여 검색 인덱스 만들기](search-get-started-portal.md)