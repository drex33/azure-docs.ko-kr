---
title: 역할 기반 권한 부여
titleSuffix: Azure Cognitive Search
description: 서비스 관리 및 콘텐츠 작업에 대한 세분화된 권한을 위해 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/04/2021
ms.openlocfilehash: ceb65226c30d6ee9768388bb18807dd7cf6d6f85
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070587"
---
# <a name="use-role-based-authorization-in-azure-cognitive-search"></a>Azure Cognitive Search에서 역할 기반 권한 부여 사용

Azure는 플랫폼에서 실행 중인 모든 서비스에 대해 글로벌 [RBAC(역할 기반 액세스 제어) 권한 부여 시스템](../role-based-access-control/role-assignments-portal.md)을 제공합니다. Cognitive Search에서 다음과 같은 방법으로 역할을 사용할 수 있습니다.

+ 서비스 관리에 일반적으로 사용할 수 있는 역할을 사용 합니다.

+ [**요청에서 사용할 수 있는**](https://aka.ms/azure-cognitive-search/rbac-preview)콘텐츠 관리 (인덱스 및 기타 최상위 개체 만들기 및 관리)에 대해 새로운 미리 보기 역할을 사용 합니다.

> [!NOTE]
> Search Service 참여자는 "미리 보기" 기능이 포함 된 "일반 공급" 역할입니다. 서비스 및 콘텐츠 관리 작업의 진정한 하이브리드을 지원 하 여 지정 된 검색 서비스에 대 한 모든 작업을 허용 하는 유일한 역할입니다. 이 역할에 대 한 콘텐츠 관리의 미리 보기 기능을 얻으려면 [**미리 보기에 등록**](https://aka.ms/azure-cognitive-search/rbac-preview)합니다.

몇 가지 RBAC 시나리오는 지원 **되지 않으며** 이 문서에서 다루지 않습니다.

+ 아웃 바운드 인덱서 연결은 ["관리 되는 id를 사용 하 여 데이터 원본에 대 한 인덱서 연결 설정"](search-howto-managed-identities-data-sources.md)에 설명 되어 있습니다. 관리 id가 할당 된 검색 서비스의 경우 신뢰할 수 있는 검색 서비스에 의해 blob에 대 한 Azure Blob Storage, 읽기 액세스 등의 외부 데이터 서비스를 허용 하는 역할 할당을 만들 수 있습니다.

+ 검색 결과에 대 한 사용자 id 액세스 (행 수준 보안 또는 문서 수준 보안이 라고도 함)는 지원 되지 않습니다. 문서 수준 보안을 위해 [보안 필터](search-security-trimming-for-azure-search.md)를 사용하여 사용자 ID별로 결과를 트리밍하고 요청자가 액세스 권한이 없어야 하는 문서를 제거하는 것이 해결 방법입니다.

## <a name="built-in-roles-used-in-search"></a>검색에 사용 되는 기본 제공 역할

Cognitive Search 기본 제공 역할에는 일반적으로 사용 가능 하 고 미리 보기 역할이 포함 되며 할당 된 멤버 자격은 Azure Active Directory 사용자 및 그룹으로 구성 됩니다.

역할 할당은 검색 서비스를 만들거나 관리하는 데 사용되는 모든 도구 및 클라이언트 라이브러리에서 누적되고 광범위하게 사용됩니다. 이러한 클라이언트에는 Azure sdk의 Azure Portal, 관리 REST API, Azure PowerShell, Azure CLI 및 관리 클라이언트 라이브러리가 포함 됩니다.

역할은 검색 서비스 전체에 적용 되며 소유자가 할당 해야 합니다. 특정 인덱스나 기타 최상위 개체에 역할을 할당할 수 없습니다.

Azure Cognitive Search에서 RBAC 사용에 대한 지역, 계층 또는 가격 책정 제한은 없지만 검색 서비스는 Azure 퍼블릭 클라우드에 있어야 합니다.

| 역할 | 적용 대상 | Description |
| ---- | ---------- | ----------- |
| [소유자](../role-based-access-control/built-in-roles.md#owner) | 서비스 ops (일반적으로 사용 가능) | Azure 역할을 할당 하는 기능을 포함 하 여 검색 리소스에 대 한 모든 권한을 제공 합니다. 구독 관리자는 기본적으로 멤버입니다. |
| [기여자](../role-based-access-control/built-in-roles.md#contributor) | 서비스 ops (일반적으로 사용 가능) | 소유자와 동일한 액세스 수준-역할을 할당 하거나 권한 부여 옵션을 변경할 수 없습니다. |
| [판독기](../role-based-access-control/built-in-roles.md#reader) | 서비스 ops (일반적으로 사용 가능) | 부분적으로 서비스 정보에 대한 액세스가 제한됩니다. 포털에서 읽기 권한자 역할은 서비스 개요 페이지, Essentials 섹션 및 모니터링 탭의 정보에 액세스할 수 있습니다. 모든 기타 탭 및 페이지는 제한이 해제되어 있습니다. </br></br>이 역할은 리소스 그룹, 서비스 상태, 위치, 구독 이름 및 ID, 태그, URL, 가격 측정 계층, 복제본, 파티션 및 검색 단위와 같은 서비스 정보에 액세스할 수 있습니다. </br></br>이 역할은 검색 대기 시간, 제한 요청의 백분율, 초당 평균 쿼리 등 서비스 메트릭에 액세스할 수 있습니다. </br></br>API 키, 역할 할당, 콘텐츠(인덱스 또는 동의어 맵) 또는 콘텐츠 메트릭(스토리지 사용, 개체 수)에 액세스할 수 없습니다. |
| [Search 서비스 기여자](../role-based-access-control/built-in-roles.md#search-service-contributor) | 서비스 ops (일반적으로 사용 가능) 및 최상위 개체 (미리 보기) | 이 역할은 서비스 수준에서 참가자의 조합 이지만 인덱스, 동의어 맵, 인덱서, 데이터 원본 및 기술력과에 대 한 모든 작업에 대 한 모든 액세스 권한이 [`Microsoft.Search/searchServices/*`](/azure/role-based-access-control/resource-provider-operations#microsoftsearch) 있습니다. 이 역할은 서비스를 완전히 관리 해야 하는 검색 서비스 관리자를 위한 것입니다. </br></br>기여자와 마찬가지로이 역할의 멤버는 역할 할당을 만들거나 관리 하거나 권한 부여 옵션을 변경할 수 없습니다. |
| [검색 인덱스 데이터 기여자](../role-based-access-control/built-in-roles.md#search-index-data-contributor) | Documents 컬렉션 (미리 보기) | 검색 서비스에 있는 모든 인덱스의 콘텐츠에 대 한 모든 권한을 제공 합니다. 이 역할은 인덱스의 문서 컬렉션을 가져오거나 새로 고치거 나 쿼리 해야 하는 개발자 또는 인덱스 소유자를 위한 것입니다. |
| [검색 인덱스 데이터 읽기 권한자](../role-based-access-control/built-in-roles.md#search-index-data-reader) | Documents 컬렉션 (미리 보기) | 검색 서비스의 검색 인덱스에 대 한 읽기 전용 액세스를 제공 합니다. 이 역할은 쿼리를 실행 하는 앱 및 사용자에 대 한 역할을 합니다. |

> [!NOTE]
> Azure 리소스는 [컨트롤 플레인과 데이터 평면](../azure-resource-manager/management/control-plane-and-data-plane.md) 작업 범주의 개념을 가집니다. Cognitive Search에서 "제어 평면"은 [관리 REST API](/rest/api/searchmanagement/) 또는 이와 동등한 클라이언트 라이브러리에서 지원 되는 모든 작업을 나타냅니다. "데이터 평면"은 검색 서비스 끝점에 대 한 작업 (예: 인덱싱 또는 쿼리) 또는 [검색 REST API](/rest/api/searchservice/) 또는 동등한 클라이언트 라이브러리에 지정 된 기타 작업을 나타냅니다. 대부분의 역할은 하나의 평면에만 적용 됩니다. 예외는 둘 다에서 작업을 지 원하는 Search Service 기여자입니다.

## <a name="step-1-preview-sign-up"></a>1 단계: 등록 미리 보기

**적용 대상:** 검색 인덱스 데이터 참여자, 검색 인덱스 데이터 판독기, Search Service 기여자

일반적으로 사용 가능한 역할 (소유자, 참가자, 읽기 권한자) 또는 Search Service 참여자의 서비스 수준 작업만 사용 하는 경우이 단계를 건너뜁니다.

새로운 기본 제공 미리 보기 역할은 검색 서비스의 콘텐츠에 대해 세부적인 사용 권한 집합을 제공 합니다. 기본 제공 역할은 항상 Azure Portal에 표시 되지만 서비스를 등록 하려면 서비스를 등록 해야 합니다.

미리 보기 프로그램에 등록 하려면 다음을 수행 합니다.

+ [이 양식 작성](https://aka.ms/azure-cognitive-search/rbac-preview)

등록 요청을 처리하는 데 영업일 기준 최대 2일까지 걸릴 수 있습니다. 서비스가 준비되면 이메일을 받게 됩니다.

## <a name="step-2-preview-configuration"></a>2 단계: 구성 미리 보기

**적용 대상:** 검색 인덱스 데이터 참여자, 검색 인덱스 데이터 판독기, Search Service 기여자

일반적으로 사용 가능한 역할 (소유자, 참가자, 읽기 권한자) 또는 Search Service 참여자의 서비스 수준 작업만 사용 하는 경우이 단계를 건너뜁니다.

이 단계에서는 OAuth2 액세스 토큰을 제공 하는 데이터 요청에 대 한 **인증** 헤더를 인식 하도록 검색 서비스를 구성 합니다.

### <a name="azure-portal"></a>[**Azure portal**](#tab/config-svc-portal)

1. [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true) 구문을 사용하여 포털을 엽니다.

1. 검색 서비스로 이동합니다.

1. 왼쪽 탐색 창에서 **키** 를 선택합니다.

1. **API 액세스 제어** 메커니즘을 선택합니다. 

   | 옵션 | 상태 | 설명 |
   |--------|--------|-------------|
   | API 키 | 일반 공급(기본) | 권한 부여를 위해 요청 헤더에 [관리자 또는 쿼리 API 키](search-security-api-keys.md)가 필요합니다. 역할이 사용되지 않습니다. |
   | 역할 기반 액세스 제어 | 미리 보기 | 다음 단계에서 설명하는 작업을 완료하려면 역할 할당의 멤버 자격이 필요합니다. 각 요청에는 인증 헤더가 필요합니다. 이 옵션을 선택하면 2021-04-30-preview REST API를 지원하는 클라이언트로 제한됩니다. |
   | 모두 | 미리 보기 | 요청은 API 키 또는 권한 부여 토큰을 사용하여 유효합니다. |

옵션이 표시 되지 않으면 포털 URL을 확인 합니다.

선택 항목을 저장할 수 없거나 "API 액세스 제어에서 검색 서비스를 업데이트 하지 못했습니다 `<name>` . DisableLocalAuth가이 구독에 대해 미리 보기 이며 사용할 수 없습니다. "라는 구독 등록이 시작 되지 않았거나 처리 되지 않았습니다.

### <a name="rest-api"></a>[**REST API**](#tab/config-svc-rest)

관리 REST API 버전 2021-04-01-미리 보기, [만들기 또는 업데이트 서비스](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)를 사용 하 여 서비스를 구성 합니다.

Postman 또는 다른 웹 테스트 도구를 사용 하는 경우 요청을 설정 하는 방법에 대 한 도움말은 아래 팁을 참조 하세요.

1. " [Authoptions"](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) 를 "aadOrApiKey"로 설정 합니다.

   필요에 따라 ["AadAuthFailureMode"](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#aadauthfailuremode) 를 설정 하 여 인증에 실패할 경우 403 대신 401이 반환 되는지 여부를 지정 합니다. "DisableLocalAuth"의 기본값은 false 이므로 설정 하지 않아도 되지만, authOptions가 설정 될 때마다 false가 되어야 함을 강조 하기 위해 아래에 나열 되어 있습니다.

    ```http
    PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "disableLocalAuth": false,
        "authOptions": {
          "aadOrApiKey": {
            "aadAuthFailureMode": "http401WithBearerChallenge"
          }
        }
      }
   }
    ```

1. 서비스에 [역할을 할당](#assign-roles) 하 고 데이터 평면에 대해 제대로 작동 하는지 확인 합니다.

> [!TIP]
> 관리 REST API 호출은 Azure Active Directory를 통해 인증 됩니다. 보안 원칙 및 요청을 설정 하는 방법에 대 한 지침은이 블로그 게시물 [Postman을 사용 하 여 AZURE REST api 게시물 (2021)](https://blog.jongallant.com/2021/02/azure-rest-apis-postman-2021/)을 참조 하세요. 이전 예제는 블로그 게시물에 제공 된 명령 및 Postman 컬렉션을 사용 하 여 테스트 되었습니다.

---

<a name="assign-roles"></a>

## <a name="step-3-assign-roles"></a>3 단계: 역할 할당

역할은 Azure 역할 기반 액세스 제어 설명서에 설명된 [지원되는 접근 방식](../role-based-access-control/role-assignments-steps.md) 중 하나를 사용하여 할당할 수 있습니다.

역할 할당을 관리 하려면 **소유자** 이거나 [Microsoft. Authorization/roleassignments/write](/azure/templates/microsoft.authorization/roleassignments) 권한이 있어야 합니다.

### <a name="azure-portal"></a>[**Azure portal**](#tab/roles-portal)

1. 미리 보기 역할의 경우 다음 구문을 사용 하 여 포털을 [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true) 엽니다. URL에 `feature.enableRbac=true`가 표시됩니다.

   > [!NOTE]
   > 미리 보기 역할에 할당 된 사용자 및 그룹의 경우 기능 플래그를 사용 하 여 포털을 여는 경우에만 인덱스 및 인덱서와 같은 포털 콘텐츠가 표시 됩니다. 

1. 검색 서비스로 이동합니다.

1. 왼쪽 탐색 창에서 **액세스 제어(IAM)** 를 선택합니다.

1. 오른쪽의 **이 리소스에 대한 액세스 부여** 에서 **역할 할당 추가** 를 선택합니다.

1. 해당 하는 역할을 찾은 다음 Azure Active Directory 사용자 또는 그룹 id를 할당 합니다.

   + 소유자
   + 참가자
   + 판독기
   + Search 서비스 참가자
   + 인덱스 데이터 참여자 검색 (미리 보기)
   + 검색 인덱스 데이터 판독기 (미리 보기)

### <a name="powershell"></a>[**PowerShell**](#tab/roles-powershell)

[PowerShell을 사용하여 역할을 할당](../role-based-access-control/role-assignments-powershell.md)하면 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 호출하여 Azure 사용자 또는 그룹 이름과 할당 범위를 제공합니다.

시작하기 전에 Azure 및 AzureAD 모듈을 로드하고 Azure에 연결해야 합니다.

```powershell
Import-Module -Name Az
Import-Module -Name AzureAD
Connect-AzAccount
```

서비스 범위가 한정된 구문은 다음 예제와 유사해야 합니다.

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>"
```

개별 인덱스로 범위 지정:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Search Index Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Search/searchServices/<search-service>/indexes/<index-name>"
```

인덱스, 동의어 맵, 인덱서, 데이터 원본 및 기술 세트와 같은 최상위 리소스에 대한 액세스 범위만 지정할 수 있습니다. Azure 역할을 사용하여 검색 문서(인덱스 콘텐츠)에 대한 액세스를 제어할 수 없습니다.

---

## <a name="step-4-test"></a>4단계: 테스트

### <a name="azure-portal"></a>[**Azure portal**](#tab/test-portal)

1. 미리 보기 역할의 경우 다음 구문을 사용 하 여 포털을 [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true) 엽니다. 

   > [!NOTE]
   > 미리 보기 역할에 할당 된 사용자 및 그룹의 경우 기능 플래그를 사용 하 여 포털을 여는 경우에만 인덱스 및 인덱서와 같은 포털 콘텐츠가 표시 됩니다. 

1. 검색 서비스로 이동합니다.

1. 개요 페이지에서 **인덱스** 탭을 선택합니다.

   + 검색 인덱스 데이터 판독기의 멤버는 검색 탐색기를 사용 하 여 인덱스를 쿼리할 수 있습니다. 모든 API 버전을 사용하여 액세스를 확인할 수 있습니다. 쿼리를 실행 하 고 결과를 볼 수는 있지만 인덱스 정의를 볼 수는 없습니다.

   + 검색 인덱스 데이터 참여자의 멤버는 **새** 인덱스를 선택 하 여 새 인덱스를 만들 수 있습니다. 새 인덱스 저장은 서비스에 대한 쓰기 권한을 확인합니다.

### <a name="rest-api"></a>[**REST API**](#tab/test-rest)

+ Azure Active Directory에 애플리케이션을 등록합니다.

+ [검색 REST API](/rest/api/searchservice/) (지원 되는 모든 버전)를 사용 하 고 요청에 대 한 **인증** 헤더를 설정 하 여 **API 키** 헤더를 바꾸는 코드를 수정 합니다.

  :::image type="content" source="media/search-security-rbac/rest-authorization-header.png" alt-text="권한 부여 헤더가 있는 HTTP 요청의 스크린샷" border="true":::

특정 환경에 대 한 토큰을 획득 하는 방법에 대 한 자세한 내용은 [Microsoft ID 플랫폼 인증 라이브러리](/azure/active-directory/develop/reference-v2-libraries)를 참조 하세요.

### <a name="net-sdk"></a>[**.NET SDK**](#tab/test-dotnet)

Azure SDK for .net은 NuGet 갤러리의 인증 헤더를 지원 합니다. [| 11.4.0](https://www.nuget.org/packages/Azure.Search.Documents/11.4.0-beta.2) 패키지를 검색 합니다.

Azure Active Directory를 사용 하 여 응용 프로그램을 등록 하 고 권한 부여 토큰을 얻고 전달 하려면 추가 구성이 필요 합니다.

+ OAuth 토큰을 가져올 때 범위는 ""입니다 https://search.azure.com/.default . SDK를 사용 하려면 대상이 "" 여야 합니다 https://search.azure.com . ". 기본값"은 Azure AD 규칙입니다.

+ SDK는 사용자에 게 앱에서 부여 되어야 하는 "user_impersonation" 범위가 있는지 확인 하지만 SDK 자체는 ""을 요청 합니다 https://search.azure.com/.default .

[클라이언트 암호 자격 증명](/dotnet/api/azure.core.tokencredential)을 사용 하는 예제:

```csharp
var tokenCredential =  new ClientSecretCredential(aadTenantId, aadClientId, aadSecret);
SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, tokenCredential);
```

[.net 용 Azure sdk](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/identity/Azure.Identity) 를 사용 하 여 AAD 인증 사용에 대 한 자세한 내용은 SDK의 GitHub 리포지토리에서 제공 됩니다.

> [!NOTE]
> 403 오류가 발생 하면 검색 서비스가 미리 보기 프로그램에 등록 되어 있고 미리 보기 역할 할당에 대해 서비스가 구성 되어 있는지 확인 합니다.

---

## <a name="disable-api-key-authentication"></a>API 키 인증 사용 안 함

API 키는 삭제할 수 없지만 서비스에서 사용하지 않도록 설정할 수 있습니다. Search Service 참여자, 검색 인덱스 데이터 참가자, 검색 인덱스 데이터 판독기 역할 및 Azure AD 인증을 사용 하는 경우 API 키를 사용 하지 않도록 설정 하 여 검색 서비스에서 콘텐츠 관련 요청에 대 한 헤더의 API 키를 전달 하는 모든 데이터 관련 요청을 거부할 수 있습니다.

[키 기반 인증](search-security-api-keys.md)을 사용 하지 않도록 설정 하려면 관리 REST API 버전 2021-04-01-미리 보기를 사용 하 여 [업데이트 서비스](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)에 대 한 두 개의 연속 요청을 보냅니다.

기능을 사용 하지 않도록 설정 하려면 소유자 또는 참가자 권한이 필요 합니다. Postman 또는 다른 웹 테스트 도구를 사용 하 여 다음 단계를 완료 합니다 (아래 설명 참조).

1. 첫 번째 요청에서 ["Authoptions"](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions) 를 "aadOrApiKey"로 설정 하 여 Azure AD 인증을 사용 하도록 설정 합니다. 옵션은 Azure AD 또는 기본 API 키 중 하나를 사용할 수 있음을 나타냅니다.

    ```http
    PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "authOptions": {
          "aadOrApiKey": {
            "aadAuthFailureMode": "http401WithBearerChallenge"
          }
        }
      }
   }
    ```

1. 두 번째 요청에서 ["Disablelocalauth"](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#request-body) 를 true로 설정 합니다. 이 단계에서는 Azure AD 인증만 사용 하 여 "aadOrApiKey" 옵션의 API 키 부분을 해제 합니다.

    ```http
    PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups/{{resource-group}}/providers/Microsoft.Search/searchServices/{{search-service-name}}?api-version=2021-04-01-Preview
    {
      "location": "{{region}}",
      "sku": {
        "name": "standard"
      },
      "properties": {
        "disableLocalAuth": true
      }
    }
    ```

1 단계와 2 단계를 결합할 수 없습니다. 1 단계에서 "disableLocalAuth"는 "AuthOptions"를 설정 하는 데 필요한 요구 사항을 충족 하기 위해 false이 고, 두 번째 단계는 해당 값을 true로 변경 합니다.

키 인증을 다시 사용 하도록 설정 하려면 마지막 요청을 다시 실행 하 고, "disableLocalAuth"를 false로 설정 합니다. 검색 서비스는 요청에 대 한 API 키의 수락을 자동으로 다시 시작 합니다 (지정 된 경우).

> [!TIP]
> 관리 REST API 호출은 Azure Active Directory를 통해 인증 됩니다. 보안 원칙 및 요청을 설정 하는 방법에 대 한 지침은이 블로그 게시물 [Postman을 사용 하 여 AZURE REST api 게시물 (2021)](https://blog.jongallant.com/2021/02/azure-rest-apis-postman-2021/)을 참조 하세요. 이전 예제는 블로그 게시물에 제공 된 명령 및 Postman 컬렉션을 사용 하 여 테스트 되었습니다.

## <a name="conditional-access"></a>조건부 액세스

[조건부 액세스](../active-directory/conditional-access/overview.md) 는 Azure Active Directory에서 조직 정책을 적용 하는 데 사용 하는 도구입니다. 조건부 액세스 정책을 사용 하 여 조직의 보안을 유지 하는 데 필요한 경우 적합 한 액세스 제어를 적용할 수 있습니다. 역할 기반 액세스 제어를 사용 하 여 Azure Cognitive Search 서비스에 액세스할 때 조건부 액세스는 조직 정책을 적용할 수 있습니다.

Azure Cognitive Search에 대 한 조건부 액세스 정책을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.
1. Azure Portal에 [로그인](https://portal.azure.com)합니다.
1. **AZURE AD 조건부 액세스** 를 검색 합니다.
1. **정책** 을 선택 합니다.
1. **+ 새 정책** 을 선택합니다.
1. 정책의 **클라우드 앱 또는 작업** 섹션에서 정책을 설정 하는 방법에 따라 **Azure Cognitive Search** 를 클라우드 앱으로 추가 합니다.
1. 정책의 나머지 매개 변수를 업데이트 합니다. 예를 들어이 정책이 적용 되는 사용자 및 그룹을 지정 합니다. 
1. 해당 정책을 저장합니다.

> [!IMPORTANT]
> 검색 서비스에 관리 id가 할당 되어 있는 경우 특정 검색 서비스는 조건부 액세스 정책의 일부로 포함 하거나 제외할 수 있는 클라우드 앱으로 표시 됩니다. 특정 검색 서비스에는 조건부 액세스 정책을 적용할 수 없습니다. 대신 일반 **Azure Cognitive Search** 클라우드 앱을 선택 해야 합니다.
