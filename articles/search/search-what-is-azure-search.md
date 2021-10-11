---
title: Azure Cognitive Search 소개
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search는 Microsoft에서 제공하는 완전 관리형 클라우드 검색 서비스입니다. 사용 사례, 개발 워크플로, 다른 Microsoft 검색 제품과의 비교 및 시작 방법을 살펴봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 09/28/2021
ms.custom: contperf-fy21q1
ms.openlocfilehash: b61ddd82671835e96b3115816ef57ca44fca942b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207815"
---
# <a name="what-is-azure-cognitive-search"></a>Azure Cognitive Search란?

Azure Cognitive Search([이전의 “Azure Search”](whats-new.md#new-service-name))는 웹, 모바일 및 엔터프라이즈 애플리케이션의 프라이빗 이기종 콘텐츠에 대한 풍부한 검색 환경을 추가할 수 있는 인프라, API 및 도구를 개발자에게 제공하는 클라우드 검색 서비스입니다.

검색은 카탈로그 또는 문서 검색, 소매 또는 데이터 과학을 위한 기술 자료 등을 비롯한 일반적인 시나리오를 통해 사용자에게 텍스트 콘텐츠를 표시하는 모든 앱에 기반이 됩니다.

검색 서비스를 만들 때 다음 기능을 사용합니다.

+ 검색 인덱스에 사용자 소유 콘텐츠를 저장할 수 있는 전체 텍스트 검색용 검색 엔진
+ 고급 콘텐츠 추출 및 변환을 위한 [텍스트 분석](search-analyzers.md) 및 [선택적 AI 보강](cognitive-search-concept-intro.md)을 갖춘 풍부한 인덱싱
+ 간단한 구문, 전체 Lucene 구문 및 자동 완성 검색을 비롯한 풍부한 쿼리 기능
+ .NET, Python, Java 및 JavaScript용 Azure SDK의 REST API 및 클라이언트 라이브러리를 통한 프로그래밍 기능
+ 데이터 계층, 기계 학습 계층 및 AI(Cognitive Services)에서 Azure 통합

구조적으로 검색 서비스는 인덱싱되지 않은 데이터를 포함하는 외부 데이터 저장소와 검색 인덱스에 쿼리 요청을 보내고 응답을 처리하는 클라이언트 앱 사이에 배치됩니다.

![Azure Cognitive Search 아키텍처](media/search-what-is-azure-search/azure-search-diagram.svg "Azure Cognitive Search 아키텍처")

Azure 플랫폼 전반에 걸쳐 Cognitive Search는 Azure 데이터 원본에서 데이터 수집/검색을 자동화하는 *인덱서* 의 형태로 다른 Azure 서비스와 통합할 수 있으며, 이미지 및 자연어와 같은 Cognitive Services의 소모성 AI 또는 Azure Machine Learning에서 생성하거나 Azure Functions 내부에서 래핑하는 사용자 지정 AI를 통합하는 *기술 세트* 의 형태로도 통합할 수 있습니다.

## <a name="inside-a-search-service"></a>검색 서비스 내부

검색 서비스 자체의 두 가지 기본 워크로드는 *인덱싱* 및 *쿼리* 입니다. 

+ [인덱싱](search-what-is-an-index.md)은 콘텐츠를 검색 서비스에 로드하여 검색 가능하게 만드는 흡입구 프로세스입니다. 내부적으로 인바운드 텍스트는 토큰으로 처리되고 빠른 검색을 위해 반전된 인덱스에 저장됩니다. JSON 문서 형식의 모든 텍스트를 업로드할 수 있습니다.

  또한 콘텐츠에 혼합된 파일이 포함된 경우 [인식 기술](cognitive-search-working-with-skillsets.md)을 통해 *AI 보강* 을 추가할 수 있습니다. AI 보강은 애플리케이션 파일에 포함된 텍스트를 추출할 수 있으며 콘텐츠를 분석하여 텍스트가 아닌 파일에서 텍스트와 구조를 유추할 수도 있습니다. 

  분석을 제공하는 기술은 Microsoft에서 미리 정의된 기술이거나 사용자가 만든 사용자 지정 기술입니다. 이후 분석과 변환은 이전에 존재하지 않았던 새로운 정보 및 구조를 초래하여 많은 검색 및 지식 마이닝 시나리오에 대한 높은 효용을 제공합니다.

+ 인덱스가 검색 가능한 텍스트로 채워지면 클라이언트 앱이 검색 서비스에 쿼리 요청을 보내고 응답을 처리할 때 [쿼리](search-query-overview.md)가 발생할 수 있습니다. 모든 쿼리 실행은 서비스에서 만들고 소유하고 저장하는 검색 인덱스를 통해 실행됩니다. 클라이언트 앱에서 검색 환경은 Azure Cognitive Search의 API를 사용하여 정의되며 관련성 튜닝, 자동 완성, 동의어 일치, 유사 일치, 패턴 일치, 필터 및 정렬을 포함할 수 있습니다.

기능은 정보 검색의 내재된 복잡성을 표시하는 간단한 [REST API](/rest/api/searchservice/) 또는 [.NET SDK](search-howto-dotnet-sdk.md)를 통해 표시됩니다. 또한 Azure Portal을 통해 인덱스와 기술 세트의 프로토타입 생성과 쿼리 수행을 위한 도구로 서비스 관리 및 컨텐츠 관리를 수행할 수 있습니다. 이 서비스는 클라우드에서 실행되므로 인프라 및 가용성은 Microsoft에서 관리합니다.

## <a name="why-use-cognitive-search"></a>Cognitive Search를 사용하는 이유는 무엇인가요?

Azure Cognitive Search가 적합한 애플리케이션 시나리오는 다음과 같습니다.

+ 이기종 콘텐츠를 프라이빗 사용자 정의 검색 인덱스로 통합합니다. 인덱싱 및 쿼리 워크로드를 전용 검색 서비스로 오프로드합니다.

+ 관련성 튜닝, 패싯 탐색, 필터(지리 공간 검색 포함), 동의어 매핑 및 자동 완성과 같은 관련 기능을 쉽게 구현합니다.

+ Azure Blob Storage 또는 Cosmos DB에 저장된 크고 구분되지 않는 텍스트, 이미지 파일 또는 애플리케이션 파일을 검색 가능한 JSON 문서로 변환합니다. 이는 외부 처리를 추가하는 [인식 기술](cognitive-search-concept-intro.md)을 통해 인덱스를 처리하는 동안 수행됩니다.

+ 언어 또는 사용자 지정 텍스트 분석을 추가합니다. 영어가 아닌 콘텐츠가 있는 경우 Azure Cognitive Search는 Lucene 분석기와 Microsoft의 자연어 프로세서를 모두 지원합니다. 또한 분음 부호 필터링이나 문자열의 패턴 인식 및 유지와 같은 원시 콘텐츠의 특수 처리를 수행하도록 분석기를 구성할 수도 있습니다.

특정 기능에 대한 자세한 내용은 [Azure Cognitive Search의 기능](search-features-list.md)을 참조하세요.

## <a name="how-to-get-started"></a>시작하는 방법

핵심 검색 기능에 대한 엔드투엔드 탐색은 다음 네 단계로 수행할 수 있습니다.

1. 공유되는 체험 계층 또는 서비스에서만 사용하는 전용 리소스에 대한 [청구 가능 계층](https://azure.microsoft.com/pricing/details/search/)에서 [**검색 서비스를 만듭니다**](search-create-service-portal.md). 공유 서비스에서 모든 빠른 시작 및 자습서를 완료할 수 있습니다.

1. 포털, [REST API](/rest/api/searchservice/create-index), [.NET SDK](search-howto-dotnet-sdk.md) 또는 다른 SDK를 사용하여 [**검색 인덱스를 만듭니다**](search-what-is-an-index.md). 인덱스 스키마는 검색 가능한 콘텐츠의 구조를 정의합니다.

1. ["push" 모델](tutorial-optimize-indexing-push-api.md)을 통해 [**콘텐츠를 업로드**](search-what-is-data-import.md)하여 모든 원본에서 JSON 문서를 푸시하거나, 원본 데이터가 Azure에 있는 경우 ["pull" 모델(인덱서)](search-indexer-overview.md)을 사용합니다.

1. 포털, [REST API](search-get-started-rest.md), [.NET SDK](/dotnet/api/azure.search.documents.searchclient.search) 또는 다른 SDK에서 [검색 탐색기](search-explorer.md)를 사용하여 [**인덱스를 쿼리**](search-query-overview.md)합니다.

초기 탐색의 경우 [**데이터 가져오기 마법사**](search-get-started-portal.md)와 기본 제공 Azure 데이터 원본으로 시작하여 몇 분 내에 인덱스를 만들고, 로드하고, 쿼리합니다.

복잡한 솔루션 또는 사용자 지정 솔루션에 대한 도움이 필요하면 Cognitive Search 기술에 대해 심도 깊은 전문 지식을 갖춘 [**파트너에게 문의**](resource-partners-knowledge-mining.md)하세요.

## <a name="compare-search-options"></a>검색 옵션 비교

고객이 Azure Cognitive Search와 다른 검색 관련 솔루션을 비교하는 방법을 질문하는 경우가 많습니다. 다음 표에 주요 차이점이 요약되어 있습니다.

| 비교 대상 | 주요 차이점 |
|-------------|-----------------|
| Microsoft Search | [Microsoft Search](/microsoftsearch/overview-microsoft-search)는 SharePoint의 콘텐츠를 쿼리해야 하는 Microsoft 365 인증 사용자를 위한 것입니다. Microsoft 및 기타 원본의 커넥터를 통해 외부 콘텐츠를 허용하는 기능을 사용하여 관리자가 사용하도록 설정하고, 구성하는 즉시 사용 가능한 검색 환경으로 제공됩니다. 사용자의 시나리오를 설명하는 경우 Microsoft Search를 사용한 Microsoft 365는 탐색하기에 유용한 옵션입니다.<br/><br/>이와는 대조적으로 Azure Cognitive Search는 종종 다양한 원본에서 사용자가 정의하고 소유한 데이터와 문서로 채워진 인덱스에 대해 쿼리를 실행합니다. Azure Cognitive Search는 [인덱서](search-indexer-overview.md)를 통해 일부 Azure 데이터 원본에 대한 크롤러 기능을 갖추고 있지만, 인덱스 스키마를 준수하는 모든 JSON 문서를 통합형 단일 검색 가능한 리소스로 푸시할 수 있습니다. 기계 학습 및 어휘 분석기를 포함하도록 인덱싱 파이프라인을 사용자 지정할 수도 있습니다. Cognitive Search는 대규모 솔루션의 플러그 인 구성 요소가 되도록 빌드되었으므로 모든 플랫폼에서 거의 모든 앱에 검색을 통합할 수 있습니다.|
|Bing | [Bing Web Search API](../cognitive-services/bing-web-search/index.yml)는 Bing.com의 인덱스에서 일치하는 용어를 검색합니다. 인덱스는 HTML, XML 및 공개 사이트의 다른 웹 콘텐츠로 빌드됩니다. 동일한 기반의 [Bing Custom Search](/azure/cognitive-services/bing-custom-search/)는 개별 웹 사이트에 적용되는 웹 콘텐츠 유형에 대해 동일한 크롤러 기술을 제공합니다.<br/><br/>Cognitive Search에서 인덱스를 정의하고 채울 수 있습니다. [인덱서](search-indexer-overview.md)를 사용하여 Azure 데이터 원본에서 데이터를 탐색하거나 인덱스에 맞는 JSON 문서를 검색 서비스로 푸시할 수 있습니다. |
|데이터베이스 검색 | 많은 데이터베이스 플랫폼에는 기본 제공 검색 환경이 있습니다. SQL Server에는 [전체 텍스트 검색](/sql/relational-databases/search/full-text-search) 환경이 있습니다. Cosmos DB 및 유사 기술에는 쿼리 가능한 인덱스가 있습니다. 검색 및 스토리지가 결합된 제품을 평가할 때는 어떤 방향으로 갈지 결정하기가 어려울 수 있습니다. 대부분의 솔루션은 둘 다 사용합니다. 즉, 스토리지에는 DBMS를 사용하고, 특수화된 검색 기능에는 Azure Cognitive Search를 사용합니다.<br/><br/>DBMS 검색과 비교하여, Azure Cognitive Search는 이기종 원본의 콘텐츠를 저장하고, 언어 인식 테스트 처리(형태소 분석, 분류 정리, 어형)와 같은 특수화된 텍스트 처리 기능을 [56개 언어](/rest/api/searchservice/language-support)로 제공합니다. 또한 오타 자동 교정, [동의어](/rest/api/searchservice/synonym-map-operations), [제안](/rest/api/searchservice/suggestions), [채점 컨트롤](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [패싯](search-faceted-navigation.md) 및  [사용자 지정 토큰화](/rest/api/searchservice/custom-analyzers-in-azure-search)도 지원합니다. Azure Cognitive Search의 [전체 텍스트 검색 엔진](search-lucene-query-architecture.md)은 정보 검색 업계 표준인 Apache Lucene을 기반으로 합니다. Azure Cognitive Search는 데이터를 반전된 인덱스의 형태로 유지하지만 진정한 데이터 스토리지를 대체하지는 않으며 그러한 용량에서 사용하는 것은 권장되지 않습니다. 자세한 내용은 이 [포럼 게시물](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data)을 참조하세요. <br/><br/>리소스 사용률도 이 범주의 또 다른 변곡점입니다. 인덱싱 및 일부 쿼리 작업은 계산 집약적인 경우가 많습니다. DBMS에서 클라우드의 전용 솔루션으로 검색을 오프로딩하면 트랜잭션 처리를 위한 시스템 리소스가 보존됩니다. 또한 검색 과정을 외부에서 진행하면 규모를 쿼리 볼륨에 맞게 쉽게 조정할 수 있습니다.|
|전용 검색 솔루션 | 광범위한 기능을 제공하는 전용 검색을 결정한 경우 온-프레미스 솔루션 또는 클라우드 서비스의 최종 범주 비교를 해야 합니다. 대다수의 검색 기술은 인덱싱 및 쿼리 파이프라인 제어, 풍부한 쿼리 및 필터링 구문에 대한 액세스, 순위 및 관련성 제어, 자체 주도형 및 지능형 검색 기능을 제공합니다. <br/><br/>클라우드 서비스는 최소한의 오버헤드 및 유지 관리, 규모 조정이 가능한 턴키 솔루션을 원하는 경우에 적합합니다. <br/><br/>클라우드 패러다임 내에서는 일부 공급자가 전체 텍스트 검색, 지리적 검색 및 특정 수준의 검색 입력 모호성을 처리하는 기능을 비롯하여 비슷한 기준 기능을 제공합니다. 일반적으로 이러한 기능은 [특수 기능](search-features-list.md) 또는 자동 맞춤을 결정하는 API, 도구 및 관리의 전반적인 편리성 및 간편성을 나타냅니다. |

주로 정보 검색 및 콘텐츠 탐색 모두에 대한 검색을 사용하는 앱의 경우 클라우드 공급자 중에서 Azure Cognitive Search는 Azure의 콘텐츠 저장소 및 데이터베이스에 대한 전체 텍스트 검색 워크로드에 가장 강력합니다. 

주요 장점은 다음과 같습니다.

+ 인덱싱 계층에서의 Azure 데이터 통합(크롤러)
+ 외부 인터넷 보안 요구 사항을 지원하기 위한 Azure Private Link 통합
+ AI 처리와 통합하여 검색할 수 없는 콘텐츠 형식을 텍스트로 검색할 수 있도록 합니다.
+ 56개 언어로 안정적인 전체 텍스트 검색을 수행하기 위한 분석기를 사용한 언어 및 사용자 지정 분석
+ [중요한 기능](search-features-list.md): 풍부한 쿼리 언어, 관련성 튜닝, 패싯, 자동 완성, 동의어, 지역 검색 및 결과 컴포지션.
+ Azure 규모, 안정성 및 세계 수준의 가용성

Azure Cognitive Search에서 가장 광범위한 기능을 활용할 수 있는 고객에게는 온라인 카탈로그, 기간 업무 프로그램 및 문서 검색 애플리케이션이 포함됩니다.

## <a name="watch-this-video"></a>이 동영상 시청

이 15분 길이의 동영상에서 Azure Cognitive Search의 주요 기능을 검토합니다.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]