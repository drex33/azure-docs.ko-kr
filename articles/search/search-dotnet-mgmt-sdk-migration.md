---
title: Azure Search .NET Management SDK로 업그레이드
titleSuffix: Azure Cognitive Search
description: 이전 버전에서 Azure Search .NET Management SDK로 업그레이드합니다. 새로운 기능 및 마이그레이션에 필요한 코드 변경에 대해 알아봅니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/16/2021
ms.openlocfilehash: 823b758c24ab9b66c47ea76803eb54af8d7c1fb1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631726"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Azure Search .NET Management SDK의 버전 업그레이드

이 문서에서는 Search Service를 프로비저닝하거나 프로비전을 해제하고, 용량을 조정하고, API 키를 관리하는 데 사용되는 Azure Search .NET Management SDK의 후속 버전으로 마이그레이션하는 방법을 설명합니다

Management SDK는 특정 버전의 Management REST API를 대상으로 합니다. 개념 및 작업에 대한 자세한 내용은 [검색 관리(REST)](/rest/api/searchmanagement/)를 참조하세요.

## <a name="versions"></a>버전

| SDK 버전 | 해당 REST API 버전 | 기능 추가 또는 동작 변경 |
|-------------|--------------------------------|-------------------------------------|
| [3.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/3.0.0) | api-version=2020-30-20 | 엔드포인트 보안(IP 방화벽 및 [Azure Private Link](../private-link/private-endpoint-overview.md)와 통합)이 추가되었습니다. |
| [2.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/2.0.0) | api-version=2019-10-01 | 유용성이 개선되었습니다. [쿼리 키 나열](/rest/api/searchmanagement/2021-04-01-preview/query-keys/list-by-search-service) 에 호환성이 손상되는 변경이 적용되었습니다(GET이 중단됨). |
| [1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/1.0.1) | api-version=2015-08-19  | 첫 번째 버전 |

## <a name="how-to-upgrade"></a>업그레이드하는 방법

1. NuGet 패키지 관리자 콘솔을 사용하거나 Visual Studio에서 프로젝트 참조를 마우스 오른쪽 단추로 클릭하고 “NuGet 패키지 관리...”를 선택하여 `Microsoft.Azure.Management.Search`에 대한 NuGet 참조를 업데이트합니다.

1. NuGet에서 새 패키지와 해당 종속성을 다운로드했으면 프로젝트를 다시 빌드합니다. 코드가 구성된 방식에 따라 프로젝트가 성공적으로 다시 빌드될 수도 있으며, 이 경우 작업이 완료되었습니다.

1. 빌드가 실패하는 경우 변경된 일부 SDK 인터페이스를 구현했기 때문일 수 있습니다(예: 단위 테스트를 위해). 이 문제를 해결하려면 `BeginCreateOrUpdateWithHttpMessagesAsync`와 같은 최신 메서드를 구현해야 합니다.

1. 빌드 오류를 모두 수정한 후에는 새로운 기능을 활용하도록 애플리케이션을 변경할 수 있습니다. 

## <a name="upgrade-to-30"></a>3\.0으로 업그레이드

버전 3.0에서는 IP 범위에 대한 액세스를 제한하고 퍼블릭 인터넷에 표시되지 않아야 하는 Search Service를 위해 선택적으로 Azure Private Link와 통합되어 프라이빗 엔드포인트 보호를 추가합니다.

### <a name="new-apis"></a>새로운 API

| API | 범주| 세부 정보 |
|-----|--------|------------------|
| [NetworkRuleSet](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#networkruleset) | IP 방화벽 | 서비스 엔드포인트에 대한 액세스를 허용된 IP 주소 목록으로 제한합니다. 개념 및 포털 지침은 [IP 방화벽 구성](service-configure-firewall.md)을 참조하세요. |
| [공유 Private Link 리소스](/rest/api/searchmanagement/2021-04-01-preview/shared-private-link-resources) | Private Link | Search Service에서 사용할 공유 프라이빗 링크 리소스를 만듭니다.  |
| [프라이빗 엔드포인트 연결](/rest/api/searchmanagement/2021-04-01-preview/private-endpoint-connections) | Private Link | 프라이빗 엔드포인트를 통해 Search Service에 대한 연결을 설정하고 관리합니다. 개념 및 포털 지침은 [프라이빗 엔드포인트 만들기](service-create-private-endpoint.md)를 참조하세요.|
| [Private Link 리소스](/rest/api/searchmanagement/2021-04-01-preview/private-link-resources) | Private Link | 프라이빗 엔드포인트 연결이 있는 Search Service의 경우 동일한 가상 네트워크에서 사용되는 모든 서비스 목록을 가져옵니다. 검색 솔루션이 Azure 데이터 원본(Azure Storage, Cosmos DB, Azure SQL)에서 끌어온 인덱서를 포함하거나 Cognitive Services 또는 Key Vault를 사용하는 경우 모든 리소스의 엔드포인트가 가상 네트워크에 있어야 하며, 이 API에서 목록이 반환되어야 합니다. |
| [PublicNetworkAccess](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#publicnetworkaccess)| Private Link | 서비스 만들기 또는 업데이트 요청의 속성입니다. 사용할 수 없는 경우 프라이빗 링크가 유일한 액세스 형식입니다. |

### <a name="breaking-changes"></a>주요 변경 내용

[쿼리 키 나열](/rest/api/searchmanagement/2021-04-01-preview/query-keys/list-by-search-service) 요청에서 더 이상 GET을 사용할 수 없습니다. 이전 릴리스에서는 GET 또는 POST를 사용할 수 있었지만 이번 릴리스와 이후의 모든 릴리스에서는 POST만 지원됩니다. 

## <a name="upgrade-to-20"></a>2\.0으로 업그레이드

Azure Search .NET Management SDK 버전 2는 부 업그레이드이므로 최소한의 작업만으로 코드를 변경할 수 있습니다. SDK 변경 내용은 엄격히 SDK 자체의 유용성을 개선하기 위한 클라이언트 쪽 변경입니다. 변경 내용은 다음과 같습니다.

* 이제 `Services.CreateOrUpdate` 및 해당 비동기 버전은 프로비저닝 `SearchService`를 자동으로 폴링하며, 서비스 프로비저닝이 완료될 때까지 반환되지 않습니다. 따라서 이러한 폴링 코드를 직접 작성하지 않아도 됩니다.

* 서비스 프로비저닝을 여전히 수동으로 폴링하려는 경우 새로운 `Services.BeginCreateOrUpdate` 메서드 또는 비동기 버전 중 하나를 사용할 수 있습니다.

* 새 메서드 `Services.Update` 및 해당 비동기 버전이 SDK에 추가되었습니다. 이러한 메서드는 HTTP PATCH를 사용하여 서비스의 증분 업데이트를 지원합니다. 예를 들어, 이제 원하는 `partitionCount` 및 `replicaCount` 속성만 포함하는 `SearchService` 인스턴스를 이러한 메서드에 전달하여 서비스를 크기를 조정할 수 있습니다. `Services.Get`을 호출하고, 반환된 `SearchService`를 수정한 후 `Services.CreateOrUpdate`에 전달하는 이전 방식은 계속 지원되지만 더 이상 필요하지 않습니다. 

## <a name="next-steps"></a>다음 단계

문제가 발생할 경우 질문을 게시하기에 가장 적합한 포럼은 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest)입니다. 버그를 발견하는 경우 [Azure .NET SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/issues)에 문제를 제출할 수 있습니다. 이슈 제목에 “[검색]”이라는 레이블을 지정해야 합니다.