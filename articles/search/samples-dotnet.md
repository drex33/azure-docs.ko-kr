---
title: .NET 샘플
titleSuffix: Azure Cognitive Search
description: .NET 클라이언트 라이브러리를 사용하는 Azure Cognitive Search 데모 C# 코드 샘플을 찾습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 0f4ce35a43702f958656b1ad077e6c0f84bc88bb
ms.sourcegitcommit: 079426f4980fadae9f320977533b5be5c23ee426
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129418711"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search용 .NET(C#) 코드 샘플

Azure Cognitive Search 솔루션의 기능 및 워크플로를 보여주는 C# 코드 샘플에 대해 알아봅니다. 이러한 샘플은 다음 링크를 통해 탐색할 수 있는 [ **.NET용 Azure SDK**](/dotnet/azure/)에 대해 [**Azure Cognitive Search 클라이언트 라이브러리**](/dotnet/api/overview/azure/search)를 사용합니다.

| 대상 | 링크 |
|--------|------|
| 패키지 다운로드 | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| API 참조 | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| API 테스트 사례 | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| 소스 코드 | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>SDK 샘플

Azure SDK 개발 팀의 코드 샘플에서 API 사용을 보여줍니다. 이러한 샘플은 GitHub의 [**Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/)에서 찾을 수 있습니다.

| 샘플 | Description |
|---------|-------------|
| [동기적으로 "Hello world"](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | 동기 메서드를 사용하여 클라이언트를 만들고 인증하고 오류를 처리하는 방법을 보여줍니다.|
| [비동기적으로 "Hello world"](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | 비동기 메서드를 사용하여 클라이언트를 만들고 인증하고 오류를 처리하는 방법을 보여줍니다.  |
| [서비스 수준 작업](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | 인덱스, 인덱서, 데이터 원본, 기술 세트 및 동의어 맵을 만드는 방법을 보여줍니다. 또한 이 샘플에서는 서비스 통계를 가져오는 방법과 인덱스를 쿼리하는 방법을 보여줍니다.  |
| [인덱스 작업](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | 기존 인덱스에 대한 작업을 수행하는 방법을 보여줍니다. 이 경우 인덱스에 저장된 문서 수를 가져옵니다.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | 지원되지 않는 데이터 형식으로 작업하는 방법을 보여줍니다.  |
| [문서 인덱싱(푸시 모델)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | JSON 페이로드를 서비스의 인덱스에 보내는 "푸시" 모델 인덱싱입니다.   |
| [암호화 키 샘플](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | 고객 관리형 암호화 키를 사용하여 중요한 콘텐츠에 대한 보호 계층을 추가하는 방법을 보여줍니다.  |

## <a name="doc-samples"></a>문서 샘플

Cognitive Search 팀의 코드 샘플은 기능 및 워크플로를 보여줍니다. 이러한 샘플은 대부분 자습서, 빠른 시작 및 방법 문서에서 참조됩니다. 이러한 샘플은 GitHub의 [**Azure-Samples/azure-search-dotnet-samples**](https://github.com/Azure-Samples/azure-search-dotnet-samples) 및 [**Azure-Samples/search-dotnet-getting-started**](https://github.com/Azure-Samples/search-dotnet-getting-started/)에서 찾을 수 있습니다.

| 샘플 | 아티클  |
|---------|-------------|
| [빠른 시작](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | [빠른 시작: 검색 인덱스 만들기](search-get-started-dotnet.md)에 대한 소스 코드입니다. 샘플 데이터를 사용하여 검색 인덱스를 만들고, 로드하고, 쿼리하는 기본 워크플로를 다룹니다. |
| [search-website](https://github.com/azure-samples/azure-search-dotnet-samples/tree/master/search-website) | [자습서: 웹앱에 검색 추가](tutorial-csharp-overview.md)에 대한 소스 코드입니다. 다양한 클라이언트와 앱을 호스트하고 검색 요청을 처리하기 위한 구성 요소를 포함하는 엔드투엔드 검색 앱을 보여 줍니다.|
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | [방법: .NET 클라이언트 라이브러리 사용](search-howto-dotnet-sdk.md)에 대한 소스 코드입니다. 기본 워크플로를 단계별로 안내하지만 API 사용에 관해 자세히 설명합니다.  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | 소스 코드 [예: C#에서 동의어 추가](search-synonyms-tutorial-sdk.md)에 대한 소스 코드입니다. 동의어 목록은 쿼리 확장에 사용되어 인덱스 외부에 있는 일치 가능한 용어를 제공합니다. |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | [자습서: .NET SDK를 사용하여 Azure SQL 데이터 인덱싱](search-indexer-tutorial.md)에 대한 소스 코드입니다. 이 문서에서는 일정, 필드 매핑 및 매개 변수를 포함하는 Azure SQL 인덱서를 구성하는 방법을 보여줍니다.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | [방법: 데이터 암호화를 위해 고객 관리형 키 구성](search-security-manage-encryption-keys.md)에 대한 소스 코드입니다. |
| [C#에서 첫 번째 앱 만들기](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  [자습서: 첫 번째 검색 앱 만들기](tutorial-csharp-create-first-app.md)에 대한 소스 코드입니다. 대부분의 샘플은 콘솔 애플리케이션이지만, 이 MVC 샘플은 웹페이지를 사용해 샘플 호텔 인덱스를 표시하여 기본 검색, 페이지 매김, 자동 완성 및 제안된 쿼리, 패싯 및 필터를 보여줍니다. |
| [multiple-data-sources](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | [자습서: 여러 데이터 원본 인덱싱](tutorial-multiple-data-sources.md)에 대한 소스 코드입니다. |
|  [optimize-data-indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | [자습서: 푸시 API를 사용하여 인덱싱 최적화](tutorial-optimize-indexing-push-api.md)에 대한 소스 코드입니다.  |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | [자습서: .NET SDK를 사용하여 Azure Blob에서 AI 생성 검색 가능 콘텐츠](cognitive-search-tutorial-blob-dotnet.md)에 대한 소스 코드입니다.  |

> [!Tip]
> [샘플 브라우저](/samples/browse/?languages=csharp&products=azure-cognitive-search)를 통해 제품, 서비스 및 언어별로 필터링하여 GitHub에서 Microsoft 코드 샘플을 검색해 보세요.

## <a name="other-samples"></a>기타 샘플

다음 샘플은 Cognitive Search 팀에서도 게시하지만 설명서에서 참조되지 않습니다. 관련된 추가 정보 파일은 사용 지침을 제공합니다.

| 샘플 | Description |
|---------|-------------|
| [Azure AD를 사용하여 Data Lake Gen2 인덱싱](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/data-lake-gen2-acl-indexing/README.md) | Azure AD 및 역할 기반 액세스 제어를 통해 보호되는 Azure Data Lake Gen2 파일 및 폴더의 인덱서 연결 및 인덱싱을 보여 주는 소스 코드입니다. |
| [azure-search-power-skills](https://github.com/Azure-Samples/azure-search-power-skills)  | 사용자 고유의 솔루션에 통합할 수 있는 사용 가능한 사용자 지정 기술에 대한 소스 코드입니다.  |
| [지식 마이닝 솔루션 가속기](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | 엔드투엔드 지식 마이닝 솔루션을 프로토타이핑하는 데 도움이 되는 템플릿, 지원 파일 및 분석 보고서를 포함합니다.  |
| [코로나19 검색 앱 리포지토리](https://github.com/liamca/covid19search) | Cognitive Search 기반 [코로나19 검색 앱](https://covid19search.azurewebsites.net/)에 대한 소스 코드 리포지토리입니다. |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | [JFK 솔루션](https://www.microsoft.com/ai/ai-lab-jfk-files)에 대해 자세히 알아보세요. |
| [검색 + QnA Maker 액셀러레이터](https://github.com/Azure-Samples/search-qna-maker-accelerator) | 검색 기능과 QnA Maker의 기능을 결합하는 [솔루션](https://techcommunity.microsoft.com/t5/azure-ai/qna-with-azure-cognitive-search/ba-p/2081381)입니다. 라이브 [데모 사이트](https://aka.ms/qnaWithAzureSearchDemo)를 참조하세요. |
