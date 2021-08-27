---
title: JavaScript 샘플
titleSuffix: Azure Cognitive Search
description: JavaScript용 Azure.NET SDK를 사용하는 Azure Cognitive Search 데모 JavaScript 코드 샘플을 찾습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/11/2021
ms.openlocfilehash: 6bc5a504f1716ff10b56fd30b8991f6d9e4009c7
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770215"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search에 대한 JavaScript 코드 샘플

Azure Cognitive Search 솔루션의 기능 및 워크플로를 보여 주는 JavaScript 코드 샘플에 대해 알아봅니다. 이러한 샘플은 [**Azure SDK For JavaScript**](/azure/developer/javascript/)에 대해 [**Azure Cognitive Search 클라이언트 라이브러리**](/javascript/api/overview/azure/search-documents-readme)를 사용하며 다음 링크를 통해 탐색할 수 있습니다.

| 대상 | 링크 |
|--------|------|
| 패키지 다운로드 | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| API 참조 | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| API 테스트 사례 | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| 소스 코드 | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>SDK 샘플

Azure SDK 개발 팀의 코드 샘플에서 API 사용을 보여 줍니다. 이러한 샘플은 GitHub의 [**azure-sdk-for-js/tree/master/sdk/search/search-documents/samples**](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples)에서 찾을 수 있습니다.

### <a name="javascript-sdk-samples"></a>JavaScript SDK 샘플

| 샘플 | Description |
|---------|-------------|
| [인덱스](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) | [검색 인덱스](search-what-is-an-index.md)를 만들고, 업데이트하고, 가져오고, 나열하고, 삭제하는 방법을 보여 줍니다. 이 샘플 범주에는 서비스 통계 샘플도 포함됩니다. |
| [dataSourceConnections(인덱서의 경우)](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/search/search-documents/samples/v11/javascript/dataSourceConnectionOperations.js) | [지원되는 Azure 데이터 원본](search-indexer-overview.md#supported-data-sources)의 인덱서 기반 인덱싱에 필요한 인덱서 데이터 원본을 만들고, 업데이트하고, 가져오고, 나열하고, 삭제하는 방법을 보여 줍니다. |
| [인덱서](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |  [인덱서](search-indexer-overview.md)를 만들고, 업데이트하고, 가져오고, 나열하고, 재설정하고, 삭제하는 방법을 보여 줍니다.|
| [기술 세트](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) |   연결된 인덱서이고 인덱싱 중에 AI 기반 보강을 수행하는 [기술 세트](cognitive-search-working-with-skillsets.md)를 만들고, 업데이트하고, 가져오고, 나열하고, 삭제하는 방법을 보여 줍니다. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/javascript) | [동의어 맵](search-synonyms.md)을 만들고, 업데이트하고, 가져오고, 나열하고, 삭제하는 방법을 보여 줍니다.  |

### <a name="typescript-samples"></a>TypeScript 샘플

| 샘플 | Description |
|---------|-------------|
| [인덱스](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/typescript/src) | [검색 인덱스](search-what-is-an-index.md)를 만들고, 업데이트하고, 가져오고, 나열하고, 삭제하는 방법을 보여 줍니다. 이 샘플 범주에는 서비스 통계 샘플도 포함됩니다. |
| [dataSourceConnections(인덱서의 경우)](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/search/search-documents/samples/v11/typescript/src/dataSourceConnectionOperations.ts) | [지원되는 Azure 데이터 원본](search-indexer-overview.md#supported-data-sources)의 인덱서 기반 인덱싱에 필요한 인덱서 데이터 원본을 만들고, 업데이트하고, 가져오고, 나열하고, 삭제하는 방법을 보여 줍니다. |
| [인덱서](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/search/search-documents/samples/v11/typescript/src) |  [인덱서](search-indexer-overview.md)를 만들고, 업데이트하고, 가져오고, 나열하고, 재설정하고, 삭제하는 방법을 보여 줍니다.|
| [기술 세트](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/search/search-documents/samples/v11/typescript/src/skillSetOperations.ts) |   연결된 인덱서이고 인덱싱 중에 AI 기반 보강을 수행하는 [기술 세트](cognitive-search-working-with-skillsets.md)를 만들고, 업데이트하고, 가져오고, 나열하고, 삭제하는 방법을 보여 줍니다. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/search/search-documents/samples/v11/typescript/src/synonymMapOperations.ts) | [동의어 맵](search-synonyms.md)을 만들고, 업데이트하고, 가져오고, 나열하고, 삭제하는 방법을 보여 줍니다.  |

## <a name="doc-samples"></a>문서 샘플

Cognitive Search 팀의 코드 샘플은 기능 및 워크플로를 보여줍니다. 이러한 샘플은 대부분 자습서, 빠른 시작 및 방법 문서에서 참조됩니다. GitHub의 [**Azure-Samples/azure-search-javascript-samples**](https://github.com/Azure-Samples/azure-search-javascript-samples)에서 이러한 샘플을 찾을 수 있습니다.

| 샘플 | 아티클 |
|---------|---------|
| [빠른 시작](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | [빠른 시작: JavaScript에서 검색 인덱스 만들기](search-get-started-javascript.md)에 대한 소스 코드입니다. 샘플 데이터를 사용하여 검색 인덱스를 만들고, 로드하고, 쿼리하는 기본 워크플로를 다룹니다. |
| [search-website](https://github.com/azure-samples/azure-search-javascript-samples/tree/master/search-website) | [자습서: 웹앱에 검색 추가](tutorial-javascript-overview.md)에 대한 소스 코드입니다. 다양한 클라이언트와 앱을 호스팅하고 검색 요청을 처리하기 위한 구성 요소를 포함하는 엔드투엔드 검색 앱을 보여줍니다.|

> [!Tip]
> [샘플 브라우저](/samples/browse/?languages=javascript&products=azure-cognitive-search)를 통해 제품, 서비스 및 언어별로 필터링하여 GitHub에서 Microsoft 코드 샘플을 검색해 보세요.

## <a name="other-samples"></a>기타 샘플

다음 샘플은 Cognitive Search 팀에서도 게시하지만 설명서에서 참조되지 않습니다. 관련된 추가 정보 파일은 사용 지침을 제공합니다.

| 샘플 | Description |
|---------|-------------|
| [azure-search-react-template](https://github.com/dereklegenzoff/azure-search-react-template) | Azure Cognitive Search에 대한 반응 템플릿(github.com) |
