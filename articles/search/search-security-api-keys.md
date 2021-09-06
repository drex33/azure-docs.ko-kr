---
title: API 키 인증
titleSuffix: Azure Cognitive Search
description: API 키는 서비스 엔드포인트에 대한 인바운드 액세스를 제어합니다. 관리자 키는 쓰기 액세스를 부여합니다. 쿼리 키는 읽기 전용 액세스에 대해서만 만들 수 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/25/2021
ms.openlocfilehash: f452aa6ababd338ccc86b7c7c40854367ed46e41
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460627"
---
# <a name="use-api-keys-for-azure-cognitive-search-authentication"></a>Azure Cognitive Search 인증에 대해 API 키 사용

Cognitive Search는 기본 인증 방법으로 API 키를 사용합니다. 인덱스를 만들거나 쿼리하는 요청과 같은 검색 서비스에 대한 인바운드 요청의 경우 API 키만 인증 옵션으로 사용할 수 있습니다. 몇 가지 아웃바운드 요청 시나리오, 특히 인덱서와 관련된 시나리오는 Azure Active Directory ID 및 역할을 사용할 수 있습니다.

API 키는 서비스가 만들어질 때 생성됩니다. 요청에 유효한 API 키를 전달하는 것은 요청이 권한 있는 클라이언트에서 온 것으로 간주됩니다. 키에는 다음 두 가지 종류가 있습니다. *관리 키* 는 서비스에 대한 쓰기 권한을 전달하고 시스템 정보를 쿼리하는 권한도 부여합니다. *쿼리 키* 는 읽기 권한을 전달하며 앱에서 특정 인덱스를 쿼리하는 데 사용할 수 있습니다. 

> [!NOTE]
> Azure RBAC(역할 기반 액세스 제어)를 사용하는 데이터 평면 작업에 대한 권한 부여는 현재 미리 보기로 제공됩니다. 이 미리 보기 기능을 사용하여 API 키를 추가하거나 [검색을 위해 Azure 역할로](search-security-rbac.md) 바꿀 수 있습니다. 

## <a name="using-api-keys-in-search"></a>검색에서 API 키 사용

검색 서비스에 연결할 때 모든 요청에는 서비스용으로 특별히 생성된 API 키가 포함되어야 합니다.

+ [REST 솔루션](search-get-started-rest.md)에서 API 키는 일반적으로 요청 헤더에 지정됩니다.

+ [.NET 솔루션](search-howto-dotnet-sdk.md)에서 키는 보통 구성 설정으로 지정된 다음 [AzureKeyCredential](/dotnet/api/azure.azurekeycredential)로 전달됩니다.

[Azure Portal](https://portal.azure.com)에서 또는 [PowerShell](/powershell/module/az.search), [Azure CLI](/cli/azure/search)나 [REST API](/rest/api/searchmanagement/)를 통해 API 키를 보고 관리할 수 있습니다.

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="Portal 페이지, 설정 검색, 키 섹션" border="false":::

## <a name="what-is-an-api-key"></a>API 키란?

API 키는 임의로 생성된 숫자와 문자로 구성된 고유한 문자열로, 검색 서비스에 대한 모든 요청에 전달됩니다. 요청 자체와 키가 모두 유효한 경우 서비스는 요청을 수락합니다. 

검색 서비스에 액세스하는 데 사용되는 두 가지 키 유형은 다음과 같습니다. 관리자(읽기-쓰기) 및 쿼리(읽기 전용)

|키|Description|제한|  
|---------|-----------------|------------|  
|Admin|서비스를 관리하며 인덱스, 인덱서 및 데이터 원본을 만들고 삭제하는 기능을 비롯한 모든 작업에 전체 권한을 부여합니다.<br /><br /> 포털에서 *기본* 및 *보조* 키라고 하는 두 개의 관리자 키는 서비스를 만들 때 생성되고 요청 시 개별적으로 다시 생성할 수 있습니다. 키가 두 개이면 서비스에 대해 액세스를 지속하는 데 하나의 키를 사용하는 동안 다른 키를 롤오버할 수 있습니다.<br /><br /> 관리자 키는 HTTP 요청 헤더에서만 지정됩니다. URL에 관리자 API 키를 배치할 수 없습니다.|서비스당 최대 2개|  
|쿼리|인덱스 및 문서에 대한 읽기 전용 액세스를 부여하며 일반적으로 검색 요청을 수행하는 클라이언트 애플리케이션에 배포됩니다.<br /><br /> 쿼리 키는 요청 시 생성됩니다.<br /><br /> 검색, 제안 또는 조회 작업의 HTTP 요청 헤더에서 쿼리 키를 지정할 수 있습니다. 또는 쿼리 키를 URL에 매개 변수로 전달할 수 있습니다. 클라이언트 애플리케이션이 요청을 생성하는 방법에 따라 키를 쿼리 매개 변수로 전달하는 것이 쉬울 수 있습니다.<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|서비스당 50개|  

 시각적으로는 관리자 키 및 쿼리 키 간의 구분이 없습니다. 두 키는 임의로 생성된 32개의 영숫자 문자로 구성된 문자열입니다. 애플리케이션에서 지정된 키의 형식을 잃어버린 경우 [포털에서 키 값을 확인](https://portal.azure.com)할 수 있습니다.  

> [!NOTE]  
> 요청 URI에서 `api-key`와 같은 중요한 데이터를 전달하는 낮은 수준의 보안 사례로 간주됩니다. 따라서 Azure Cognitive Search에서는 쿼리 키만을 쿼리 문자열의 `api-key`로 허용하며, 인덱스 콘텐츠가 공개적으로 제공되어야 하는 경우가 아니면 중요한 데이터를 요청 URI에서 전달해서는 안 됩니다. 일반적으로 `api-key`를 요청 헤더로 전달하는 것이 좋습니다.  

## <a name="find-existing-keys"></a>기존 키 찾기

포털에서 또는 [PowerShell](/powershell/module/az.search), [Azure CLI](/cli/azure/search)나 [REST API](/rest/api/searchmanagement/)를 통해 액세스 키를 가져올 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 구독에 대한 [검색 서비스](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)를 나열합니다.
1. 서비스를 선택하고 개요 페이지에서 **설정** >**키** 를 클릭하여 관리자 및 쿼리 키를 봅니다.

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="Portal 페이지, 설정 보기, 키 섹션" border="false":::

## <a name="create-query-keys"></a>쿼리 키 만들기

쿼리 키는 문서 컬렉션을 대상으로 하는 작업의 인덱스 내 문서에 대한 읽기 전용 액세스에 사용됩니다. 검색, 필터 및 제안 쿼리는 쿼리 키를 사용하는 모든 작업입니다. 인덱스 정의나 인덱서 상태와 같은 시스템 데이터 또는 개체 정의를 반환하는 모든 읽기 전용 작업에는 관리자 키가 필요합니다.

클라이언트 앱에서 액세스 및 작업을 제한하는 것은 서비스에서 검색 자산을 보호하는 데 있어 필수적입니다. 클라이언트 앱에서 시작하는 모든 쿼리에 대해 항상 관리자 키 대신 쿼리 키를 사용하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 구독에 대한 [검색 서비스](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)를 나열합니다.
3. 서비스를 선택하고 개요 페이지에서 **설정** >**키** 를 클릭합니다.
4. **쿼리 키 관리** 를 클릭합니다.
5. 서비스에 대해 이미 생성된 쿼리 키를 사용하거나 최대 50개의 새 쿼리 키를 만듭니다. 기본 쿼리 키의 이름은 없지만, 관리를 위해 추가 쿼리 키의 이름을 지정할 수 있습니다.

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="쿼리 키 만들기 또는 사용" border="false":::

> [!Note]
> 쿼리 키 사용 사례를 보여 주는 코드 예제는 [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)에서 확인할 수 있습니다.

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>관리자 키 다시 생성

두 개의 관리자 키는 비즈니스 연속성에 대한 보조 키를 사용하여 기본 키를 회전할 수 있도록 각 서비스에 대해 생성됩니다.

1. **설정** >**키** 페이지에서 보조 키를 복사합니다.
2. 모든 애플리케이션의 경우 보조 키를 사용하도록 API 키 설정을 업데이트합니다.
3. 기본 키를 다시 생성합니다.
4. 새 기본 키를 사용하도록 모든 애플리케이션을 업데이트합니다.

실수로 두 키를 동시에 다시 생성하면 해당 키를 사용하는 모든 클라이언트 요청이 HTTP 403 금지됨 오류와 함께 실패합니다. 그러나 콘텐츠는 삭제되지 않으며 사용자를 영구적으로 차단하지 않습니다. 

포털을 통해 또는 프로그래밍 방식으로 서비스에 계속 액세스할 수 있습니다. 관리 기능은 서비스 API 키가 아닌 구독 ID를 통해 작동하므로 가용 API 키가 없는 경우에도 계속 이용할 수 있습니다. 

포털 또는 관리 계층을 통해 새 키를 만든 후, 새 키를 가진 상태에서 요청에 해당 키를 제공하면 콘텐츠(인덱스, 인덱서, 데이터 소스, 동의어 맵)에 대한 액세스가 복원됩니다.

## <a name="secure-api-keys"></a>API 키 보안

[역할 할당](search-security-rbac.md)에서 키를 읽고 관리할 수 있는 사용자를 결정합니다. 다음 역할의 멤버는 키를 보고 다시 생성할 수 있습니다. 소유자, 기여자, [Search Service 기여자](../role-based-access-control/built-in-roles.md#search-service-contributor) 읽기 권한자 역할은 API 키에 액세스할 수 없습니다.

구독 관리자는 모든 API 키를 보고 다시 생성할 수 있습니다. 예방 조치로 역할 할당을 검토하여 관리 키에 대한 액세스 권한이 있는 사용자를 파악할 수 있습니다.

1. Azure Portal의 검색 서비스 페이지로 이동합니다.
1. 왼쪽 탐색 창에서 **액세스 제어(IAM)** , **역할 할당** 탭을 차례로 선택합니다.
1. **범위** 를 **이 리소스** 로 설정하여 서비스에 대한 역할 할당을 확인합니다.

## <a name="see-also"></a>참조

+ [Azure Cognitive Search의 보안](search-security-overview.md)
+ [Azure Cognitive Search의 Azure 역할 기반 액세스 제어](search-security-rbac.md)
+ [Powershell을 사용하여 관리](search-manage-powershell.md) 