---
title: API 버전
titleSuffix: Azure Cognitive Search
description: .NET SDK의 Azure Cognitive Search REST API 및 클라이언트 라이브러리에 대한 버전 정책입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 9a53eba2b46333f3be08432ce69b22cfb3d9ef9c
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073528"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure Cognitive Search의 API 버전

Azure Cognitive Search는 정기적으로 기능 업데이트를 배포합니다. 항상 그렇지는 않지만 경우에 따라 이러한 업데이트에는 이전 버전과 호환성을 유지하기 위해 API의 새 버전이 필요하기도 합니다. 새 버전을 게시하면 코드에서 검색 서비스 업데이트를 통합 하는 시기와 방법을 제어할 수 있습니다.

새 API 버전을 사용하여 코드를 업그레이드하려면 다소의 작업이 필요하므로, Azure Cognitive Search 팀은 필요한 경우에만 새 버전을 게시하는 것을 규칙으로 합니다. API의 일부를 변경하여 더 이상 이전 버전과 호환되지 않는 경우에만 새 버전이 필요합니다. 그러한 변경은 기존 기능의 수정 또는 기존 API 노출 영역을 변경하는 새로운 기능으로 인해 발생할 수 있습니다.

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>지원되지 않는 버전

일부 API 버전은 더 이상 지원되지 않으며 검색 서비스에서 거부됩니다.

+ **2015-02-28**
+ **2015-02-28-Preview**
+ **2014-07-31-미리 보기**
+ **2014-10-20-미리 보기**

또한 [**3.0.0-rc**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc)보다 오래된 Azure Cognitive Search .NET SDK 버전은 해당 REST API 버전 중 하나를 대상으로 하기 때문에 사용이 중지됩니다.

위에 나열된 버전에 대한 지원은 2020년 10월 15일에 중단되었습니다. 중단된 버전을 사용하는 코드가 있는 경우 [기존 코드](search-api-migration.md)를 최신 [REST API 버전](/rest/api/searchservice/) 또는 최신 Azure SDK로 마이그레이션할 수 있습니다.

## <a name="rest-apis"></a>REST API

| REST API | 링크 |
|----------|------|
| Search Service(데이터 평면) | [API 버전](/rest/api/searchservice/search-service-api-versions) |
| 관리(컨트롤 플레인) | [API 버전](/rest/api/searchmanagement/management-api-versions) |

## <a name="azure-sdk-for-net"></a>Azure SDK for .NET

다음 표에서는 최신 SDK 버전에 대한 링크를 제공합니다. 

| SDK 버전 | 상태 | Description |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11](/dotnet/api/overview/azure/search.documents-readme) | Stable | 2020년 7월에 처음 릴리스된 Azure .NET SDK의 새 클라이언트 라이브러리입니다. |
| [Microsoft.Azure.Search 10](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | 2019년 5월에 릴리스됨. 이는 Microsoft.Azure.Search 패키지의 최신 버전입니다. Azure.Search.Documents로 이어집니다. |
| [Microsoft.Azure.Management.Search 4.0.0](/dotnet/api/overview/azure/search/management) | Stable | 관리 REST api-버전=2020-08-01을 대상으로 합니다.  |
| Microsoft.Azure.Management.Search 3.0.0 | Stable | 관리 REST api-version=2015-08-19을 대상으로 합니다.  |

## <a name="azure-sdk-for-java"></a>Java용 Azure SDK

| SDK 버전 | 상태 | Description  |
|-------------|--------|------------------------------|
| [Java azure-search-documents 11](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | Stable | Azure Java SDK의 새 클라이언트 라이브러리가 2020년 7월에 릴리스되었습니다. 검색 REST api-version=2019-05-06을 대상으로 합니다. |
| [Java 관리 클라이언트 1.35.0](/java/api/overview/azure/search/management) | Stable | 관리 REST api-version=2015-08-19을 대상으로 합니다. |

## <a name="azure-sdk-for-javascript"></a>JavaScript용 Azure SDK

| SDK 버전 | 상태 | Description  |
|-------------|--------|------------------------------|
| [JavaScript @azure/search-documents 11.0](https://www.npmjs.com/package/@azure/search-documents) | Stable | Azure JavaScript & TypesScript SDK의 새 클라이언트 라이브러리가 2020년 7월에 릴리스되었습니다. 검색 REST api-version = 2016-09-01을 대상으로 합니다. |
| [JavaScript @azure/arm-search](https://www.npmjs.com/package/@azure/arm-search) | Stable | 관리 REST api-version=2015-08-19을 대상으로 합니다. |

## <a name="azure-sdk-for-python"></a>Python용 Azure SDK

| SDK 버전 | 상태 | Description  |
|-------------|--------|------------------------------|
| [Python azure-search-documents 11.0](https://pypi.org/project/azure-search-documents/) | Stable | Azure Python SDK의 새 클라이언트 라이브러리가 2020년 7월에 릴리스되었습니다. 검색 REST api-version=2019-05-06을 대상으로 합니다. |
| [Python azure-mgmt-search 8.0](https://pypi.org/project/azure-mgmt-search/) | Stable | 관리 REST api-version=2015-08-19을 대상으로 합니다. |

## <a name="all-azure-sdks"></a>모든 Azure SDK

베타 클라이언트 라이브러리 및 설명서를 찾는 경우 [이 페이지에는](https://azure.github.io/azure-sdk/releases/latest/index.html) 모든 Azure SDK 라이브러리 패키지, 코드 및 문서에 대한 링크가 포함되어 있습니다. 
