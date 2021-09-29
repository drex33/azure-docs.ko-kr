---
title: Python 샘플
titleSuffix: Azure Cognitive Search
description: Python용 Azure.NET SDK 또는 REST를 사용하는 Azure Cognitive Search 데모 Python 코드 샘플을 찾습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/11/2021
ms.openlocfilehash: 17ce9f711572d1d760e44676ad8e8b51c5333985
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218412"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search에 대한 Python 코드 샘플

Azure Cognitive Search 솔루션의 기능 및 워크플로를 보여 주는 Python 코드 샘플에 대해 알아봅니다. 이러한 샘플은 [**Python용 Azure SDK**](/azure/developer/python/)에 대해 [**Azure Cognitive Search 클라이언트 라이브러리**](/python/api/overview/azure/search-documents-readme)를 사용하며 다음 링크를 통해 탐색할 수 있습니다.

| 대상 | 링크 |
|--------|------|
| 패키지 다운로드 | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| API 참조 | [azure-search-documents](/python/api/azure-search-documents)  |
| API 테스트 사례 | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| 소스 코드 | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>SDK 샘플

Azure SDK 개발 팀의 코드 샘플에서 API 사용을 보여 줍니다. 이러한 샘플은 GitHub의 [**azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) 에서 찾을 수 있습니다.

| 샘플 | Description |
|---------|-------------|
| [인증](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | 클라이언트를 구성하고 서비스에 인증하는 방법을 보여줍니다. | 
| [인덱스 만들기-읽기-업데이트-삭제 작업](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | [검색 인덱스](search-what-is-an-index.md)를 만들고, 업데이트하고, 가져오고, 나열하고, 삭제하는 방법을 보여 줍니다. |
| [인덱서 만들기-읽기-업데이트-삭제 작업](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | [인덱서](search-indexer-overview.md)를 만들고, 업데이트하고, 가져오고, 나열하고, 재설정하고, 삭제하는 방법을 보여 줍니다. |
| [검색 인덱서 데이터 원본](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | [지원되는 Azure 데이터 원본](search-indexer-overview.md#supported-data-sources)의 인덱서 기반 인덱싱에 필요한 인덱서 데이터 원본을 만들고, 업데이트하고, 가져오고, 나열하고, 삭제하는 방법을 보여 줍니다. |
| [동의어](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | [동의어 맵](search-synonyms.md)을 만들고, 업데이트하고, 가져오고, 나열하고, 삭제하는 방법을 보여 줍니다.  |
| [문서 로드](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | [데이터 가져오기](search-what-is-data-import.md) 작업에서 문서를 인덱스에 업로드하거나 병합하는 방법을 보여줍니다. |
| [단순 쿼리](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | [기본 쿼리](search-query-overview.md)를 설정하는 방법을 보여줍니다. |
| [필터 쿼리](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | [필터 식](search-filters.md)을 설정하는 방법을 보여줍니다. |
| [패싯 쿼리](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | [패싯](search-faceted-navigation.md)을 사용하는 방법을 보여줍니다. |

## <a name="doc-samples"></a>문서 샘플

Cognitive Search 팀의 코드 샘플은 기능 및 워크플로를 보여줍니다. 이러한 샘플은 대부분 자습서, 빠른 시작 및 방법 문서에서 참조됩니다. GitHub의 [**Azure-Samples/azure-search-python-samples**](https://github.com/Azure-Samples/azure-search-python-samples)에서 이러한 샘플을 찾을 수 있습니다.

| 샘플 | 아티클 |
|---------|---------|
| [빠른 시작](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | [빠른 시작: Python에서 검색 인덱스 만들기](search-get-started-python.md)에 대한 소스 코드입니다. 이 문서에서는 샘플 데이터를 사용하여 검색 인덱스를 만들고, 로드하고, 쿼리하는 기본 워크플로를 다룹니다. |
| [search-website](https://github.com/azure-samples/azure-search-python-samples/tree/master/search-website) | [자습서: 웹앱에 검색 추가](tutorial-python-overview.md)에 대한 소스 코드입니다. 리치 클라이언트와 앱 호스팅 및 검색 요청 처리를 위한 구성 요소가 포함된 엔드투엔드 검색 앱을 보여 줍니다.|
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | [자습서: Python 및 AI를 사용하여 Azure Blob에서 검색 가능한 콘텐츠 생성](cognitive-search-tutorial-blob-python.md)의 소스 코드입니다. 이 문서에서는 인식 기술 세트를 사용하여 Blob 인덱서를 만드는 방법을 보여줍니다. 여기서 기술 세트는 원시 콘텐츠를 만들고 변환하여 검색 및 사용이 가능하도록 합니다. |
| [AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | [예제: Python을 사용하여 사용자 지정 기술 만들기](cognitive-search-custom-skill-python.md)의 소스 코드입니다. 이 문서에서는 Azure Machine Learning에서 딥 러닝 모델과의 인덱서 및 기술 세트 통합을 보여줍니다. |

> [!Tip]
> [샘플 브라우저](/samples/browse/?languages=python&products=azure-cognitive-search)를 통해 제품, 서비스 및 언어별로 필터링하여 GitHub에서 Microsoft 코드 샘플을 검색해 보세요.
