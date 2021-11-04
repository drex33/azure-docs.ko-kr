---
title: Azure API Management | 버전 Microsoft Docs
description: Azure API Management 버전 개념에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 10/31/2021
ms.author: jodowns
ms.openlocfilehash: a89860216563480de4803cc6e4a7e9f24853190f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471101"
---
# <a name="versions-in-azure-api-management"></a>Azure API Management 버전

버전을 사용하면 개발자에게 관련 API 그룹을 표시할 수 있습니다. 버전을 사용하여 API의 주요 변경 내용을 안전하게 처리할 수 있습니다. 클라이언트는 준비가 되면 새 API 버전을 사용하도록 선택할 수 있지만 기존 클라이언트는 계속해서 이전 버전을 사용합니다. 버전은 버전 식별자(선택한 문자열 값)를 통해 구별되며, 버전 지정 체계를 사용하면 클라이언트가 사용하려는 API 버전을 식별할 수 있습니다.

대부분의 경우 각 API 버전은 자체 독립 API로 간주될 수 있습니다. 서로 다른 두 API 버전에는 서로 다른 작업 집합과 다른 정책이 있을 수 있습니다.

버전을 사용하면 다음을 수행할 수 있습니다.

- 여러 버전의 API를 동시에 게시합니다.
- 경로, 쿼리 문자열 또는 헤더를 사용하여 버전을 구분합니다.
- 버전(숫자, 날짜 또는 이름)을 식별하려는 문자열 값을 사용합니다.
- 개발자 포털에서 함께 그룹화한 API 버전을 표시합니다.
- 기존(버전이 없는) API를 사용하여 기존 클라이언트를 중단하지 않고 새 버전을 만듭니다.

[연습을 수행하여 버전을 시작합니다.](./api-management-get-started-publish-versions.md)

## <a name="versioning-schemes"></a>버전 관리 체계

API 개발자는 버전에 대한 요구 사항이 다릅니다. Azure API Management 버전 관리의 단일 접근 방식을 규정하지 않고 대신 몇 가지 옵션을 제공합니다.

### <a name="path-based-versioning"></a>경로 기반 버전

경로 버전 지정 체계를 사용하는 경우 API 요청에 대한 URL 경로에 버전 식별자를 포함해야 합니다.

예를 들어 및 는 `https://apis.contoso.com/products/v1` `https://apis.contoso.com/products/v2` 동일한 `products` API를 참조하지만 각각 및 버전을 참조할 수 `v1` `v2` 있습니다.

경로 기반 버전 지정을 사용하는 경우 API 요청 URL의 형식은 `https://{yourDomain}/{apiName}/{versionIdentifier}/{operationId}` 입니다.

### <a name="header-based-versioning"></a>헤더 기반 버전

헤더 버전 지정 체계를 사용하는 경우 모든 API 요청에 대한 HTTP 요청 헤더에 버전 식별자를 포함해야 합니다. HTTP 요청 헤더의 이름을 지정할 수 있습니다.

예를 들어 라는 사용자 지정 헤더를 만들 수 `Api-Version` 있으며 클라이언트는 `v1` 이 헤더의 값에 또는 를 지정할 수 `v2` 있습니다.

### <a name="query-string-based-versioning"></a>쿼리 문자열 기반 버전

쿼리 문자열 버전 지정 체계를 사용하는 경우 모든 API 요청에 대한 쿼리 문자열 매개 변수에 버전 식별자를 포함해야 합니다. 쿼리 문자열 매개 변수의 이름을 지정할 수 있습니다.

쿼리 문자열 기반 버전 지정을 사용하는 경우 API 요청 URL의 형식은 `https://{yourDomain}/{apiName}/{operationId}?{queryStringParameterName}={versionIdentifier}` 입니다.

예를 들어 및 는 `https://apis.contoso.com/products?api-version=v1` `https://apis.contoso.com/products/api-version=v2` 동일한 `products` API를 참조하지만 각각 및 버전을 참조할 수 `v1` `v2` 있습니다.

## <a name="original-versions"></a>원래 버전

버전이 지정되지 않은 API에 버전을 추가하면 `Original` 버전 식별자가 지정되지 않은 상태에서 버전이 자동으로 만들어지고 기본 URL에 응답합니다. `Original`버전은 버전을 추가하는 프로세스로 인해 기존 호출자가 손상되지 않도록 합니다. 시작 시 버전이 활성화된 새 API를 만드는 경우 `Original` 버전이 만들어지지 않습니다.

## <a name="how-versions-are-represented"></a>버전을 나타내는 방법

Azure API Management 단일 논리 API에 대한 *버전 집합을* 나타내는 버전 집합이라는 리소스를 유지 관리합니다. 버전 집합에는 버전이 지정된 API의 표시 이름과 요청을 지정된 버전으로 [지정하는 데 사용되는 버전 지정 체계가](#versioning-schemes) 포함됩니다.

API의 각 버전은 자체 API 리소스로 유지 관리되며, 이 리소스는 버전 집합과 연결됩니다. 버전 집합에는 다른 작업 또는 정책이 있는 API가 포함될 수 있습니다. 집합의 버전 간에 중요한 변경 내용을 만들 수 있습니다.

Azure Portal 버전 집합을 만듭니다. Azure Portal 설정된 버전에 대한 이름과 설명을 수정할 수 있습니다.

최종 버전이 삭제되면 버전 집합이 자동으로 삭제됩니다.

[Azure CLI, Azure PowerShell, Resource Manager](/cli/azure/apim/api/versionset) [템플릿](/azure/templates/microsoft.apimanagement/service/apiversionsets)또는 [Azure Resource Manager](/powershell/module/az.apimanagement/#api-management) [API](/rest/api/apimanagement/2020-12-01/api-version-set)를 사용하여 버전 집합을 직접 보고 관리할 수 있습니다.

### <a name="migrating-a-non-versioned-api-to-a-versioned-api"></a>버전이 없는 API를 버전이 있는 API로 마이그레이션

Azure Portal 사용하여 기존 API에서 버전 변경을 사용하도록 설정하면 API Management 리소스가 다음과 같이 변경됩니다.

 * 새 버전 집합이 만들어집니다.
 * 기존 버전은 유지 관리되며 [ `Original` API 버전 으로 구성됩니다.](#original-versions) API는 버전 집합에 연결되지만 버전 식별자를 지정할 필요는 없습니다.
 * 새 버전은 새 API로 만들어지고 버전 집합에 연결됩니다. 이 새 API는 버전 지정 체계 및 식별자를 사용하여 액세스해야 합니다.

## <a name="versions-and-revisions"></a>버전 및 수정 버전

버전 및 수정 버전은 별개의 기능입니다. 각 버전에는 버전이 없는 API와 마찬가지로 여러 개의 수정 버전이 있을 수 있습니다. 버전을 사용하지 않고 수정 버전을 사용하거나 다른 방법을 사용할 수 있습니다. 일반적으로 버전은 API 버전을 주요 변경 내용과 구분하는 데 사용되고, 수정 버전은 API에 대한 사소한 변경 내용에 사용할 수 있습니다.

수정 버전에 호환성이 손상되는 변경이 포함되어 있거나, 정식으로 수정 버전을 베타/테스트 버전으로 전환하려는 경우 수정 버전에서 버전을 만들 수 있습니다. Azure Portal을 사용하여 수정 버전 탭의 수정 버전 상황에 맞는 메뉴에서 '수정 버전에서 버전 만들기'를 클릭합니다.

## <a name="developer-portal"></a>개발자 포털

[개발자 포털은](./api-management-howto-developer-portal.md) API의 각 버전을 개별적으로 나열합니다.

![버전이 관리되는 API 목록을 표시하는 API Management 개발자 포털](media/api-management-versions/portal-list.png)

API의 세부 정보는 해당 API의 모든 버전 목록도 표시합니다. `Original`버전 식별자 없이 버전이 표시됩니다.

![API Management API의 세부 정보 및 해당 API에 대한 버전 목록을 표시하는 개발자 포털](media/api-management-versions/portal-details.png)

> [!TIP]
> API 버전은 개발자 포털에 표시되기 전에 제품에 추가해야 합니다.
