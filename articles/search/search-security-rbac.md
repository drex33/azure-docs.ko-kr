---
title: 역할 기반 권한 부여
titleSuffix: Azure Cognitive Search
description: 서비스 관리 및 콘텐츠 작업에 대한 세분화된 권한을 위해 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/23/2021
ms.openlocfilehash: 6ffad57e87b61b9198102ddf8ae4ec8f1a9002ac
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567574"
---
# <a name="use-role-based-authorization-in-azure-cognitive-search"></a>Azure Cognitive Search에서 역할 기반 권한 부여 사용

Azure는 플랫폼에서 실행 중인 모든 서비스에 대해 글로벌 [RBAC(역할 기반 액세스 제어) 권한 부여 시스템](../role-based-access-control/role-assignments-portal.md)을 제공합니다. Cognitive Search에서는 다음과 같은 방법으로 역할 권한 부여를 사용할 수 있습니다.

+ 소유자, 기여자 및 읽기 권한자 역할을 통해 검색 서비스 자체에서 용량 추가 또는 키 회전과 같은 컨트롤 플레인 작업에 대한 액세스를 허용합니다.

+ 인덱스 만들기 또는 쿼리와 같은 데이터 평면 작업에 대한 액세스를 허용합니다. 이 기능은 현재 공개 미리 보기로 제공되고 있습니다([요청을 통해](https://aka.ms/azure-cognitive-search/rbac-preview)). 구독이 등록되면 이 문서의 지침에 따라 기능을 사용합니다.

+ [관리 ID를 사용](search-howto-managed-identities-data-sources.md)하여 아웃바운드 인덱서 연결을 만들 수 있습니다. 관리 ID가 할당된 검색 서비스의 경우 Azure Blob Storage 같은 외부 데이터 서비스를 확장하는 역할 할당을 만들어 신뢰할 수 있는 검색 서비스에서 Blob에 대한 읽기 액세스를 허용할 수 있습니다.

이 문서에서는 컨트롤 플레인 및 데이터 평면 작업에 대한 역할인 처음 두 글머리 기호에 중점을 둡니다. 아웃바운드 인덱서 호출에 대한 자세한 내용은 [관리 ID 구성](search-howto-managed-identities-data-sources.md)으로 시작합니다.

일부 RBAC 시나리오는 직접 지원되지 **않고** 다음을 포함합니다.

+ [사용자 지정 역할](../role-based-access-control/custom-roles.md)

+ 검색 결과에 대한 사용자 ID 액세스(행 수준 보안 또는 문서 수준 보안이라고도 함)

  > [!Tip]
  > 문서 수준 보안을 위해 [보안 필터](search-security-trimming-for-azure-search.md)를 사용하여 사용자 ID별로 결과를 트리밍하고 요청자가 액세스 권한이 없어야 하는 문서를 제거하는 것이 해결 방법입니다.
  >

## <a name="roles-used-in-search"></a>검색에서 사용되는 역할

기본 제공 역할에는 일반적으로 사용 가능한 미리 보기 역할이 포함되며 할당된 멤버 자격은 Azure Active Directory 사용자 및 그룹으로 구성됩니다.

역할 할당은 검색 서비스를 만들거나 관리하는 데 사용되는 모든 도구 및 클라이언트 라이브러리에서 누적되고 광범위하게 사용됩니다. 클라이언트에는 Azure SDK의 Azure Portal, 관리 REST API, Azure PowerShell, Azure CLI 및 관리 클라이언트 라이브러리가 포함됩니다. 

Azure Cognitive Search에서 RBAC 사용에 대한 지역, 계층 또는 가격 책정 제한은 없지만 검색 서비스는 Azure 퍼블릭 클라우드에 있어야 합니다.

| 역할 | 상태 | 적용 대상 | Description |
| ---- | -------| ---------- | ----------- |
| [소유자](../role-based-access-control/built-in-roles.md#owner) | Stable | 제어 평면 | Azure 역할을 할당하는 기능을 포함한 리소스에 대한 모든 권한. 구독 관리자는 기본적으로 멤버입니다. |
| [기여자](../role-based-access-control/built-in-roles.md#contributor) | Stable | 제어 평면 | 소유자와 동일한 수준의 액세스에서 역할을 할당하는 기능을 제외합니다. |
| [판독기](../role-based-access-control/built-in-roles.md#reader) | Stable | 제어 평면 | 부분적으로 서비스 정보에 대한 액세스가 제한됩니다. 포털에서 읽기 권한자 역할은 서비스 개요 페이지, Essentials 섹션 및 모니터링 탭의 정보에 액세스할 수 있습니다. 모든 기타 탭 및 페이지는 제한이 해제되어 있습니다. </br></br>이 역할은 리소스 그룹, 서비스 상태, 위치, 구독 이름 및 ID, 태그, URL, 가격 측정 계층, 복제본, 파티션 및 검색 단위와 같은 서비스 정보에 액세스할 수 있습니다. </br></br>이 역할은 검색 대기 시간, 제한 요청의 백분율, 초당 평균 쿼리 등 서비스 메트릭에 액세스할 수 있습니다. </br></br>API 키, 역할 할당, 콘텐츠(인덱스 또는 동의어 맵) 또는 콘텐츠 메트릭(스토리지 사용, 개체 수)에 액세스할 수 없습니다. |
| [Search 서비스 기여자](../role-based-access-control/built-in-roles.md#search-service-contributor) | 미리 보기 | 제어 평면 | 검색 서비스 및 개체 정의에 대한 모든 권한을 제공하지만 인덱싱된 데이터에는 액세스할 수 없습니다. 이 역할은 읽기 권한자 역할이 제공하는 것보다 더 많은 정보가 필요하지만 인덱스 또는 동의어 맵 콘텐츠에 액세스할 수 없는 서비스 관리자를 위한 것입니다.|
| [검색 인덱스 데이터 기여자](../role-based-access-control/built-in-roles.md#search-index-data-contributor) | 미리 보기 | 데이터 평면 | 인덱스 데이터에 대한 모든 권한을 제공하지만 그 외에는 아무것도 제공하지 않습니다. 이 역할은 콘텐츠를 만들고 로드할 책임이 있지만 검색 서비스 정보에 액세스할 수 없는 개발자 또는 인덱스 소유자를 위한 것입니다. 범위는 검색 서비스의 모든 최상위 리소스(인덱스, 동의어 맵, 인덱서, 데이터 원본, 기술 세트)입니다. |
| [검색 인덱스 데이터 읽기 권한자](../role-based-access-control/built-in-roles.md#search-index-data-reader) | 미리 보기 | 데이터 평면 | 인덱스 데이터에 대한 읽기 전용 액세스를 제공합니다. 이 역할은 인덱스에 대해 쿼리를 실행하는 사용자를 위한 것입니다. 범위는 검색 서비스의 모든 최상위 리소스(인덱스, 동의어 맵, 인덱서, 데이터 원본, 기술 세트)입니다. |

## <a name="scope-control-plane-and-data-plane"></a>범위: 컨트롤 플레인과 데이터 평면

Azure 리소스는 [컨트롤 플레인과 데이터 평면](../azure-resource-manager/management/control-plane-and-data-plane.md) 작업 범주의 개념을 가집니다. Cognitive Search의 기본 제공 역할은 한 평면 또는 다른 평면에 적용됩니다.

| 범주 | 작업 |
|----------|------------|
| 제어 평면 | 작업에는 서비스 만들기, 업데이트 및 삭제, API 키 관리, 파티션 및 복제본 조정 등이 포함됩니다. [관리 REST API](/rest/api/searchmanagement/) 및 이와 동등한 클라이언트 라이브러리는 컨트롤 플레인에 적용 가능한 작업을 정의합니다. |
| 데이터 평면 | 서비스에서 호스트되는 모든 개체 및 데이터를 포함하는 검색 서비스 엔드포인트에 대한 작업입니다. 인덱싱, 쿼리 및 관련된 모든 작업은 [검색 REST API](/rest/api/searchservice/) 및 동등한 클라이언트 라이브러리를 통해 액세스되는 데이터 평면을 대상으로 합니다. </br></br>현재 역할 할당을 사용하여 개별 인덱스, 동의어 맵, 인덱서, 데이터 원본 또는 기술 세트에 대한 액세스를 제한할 수 없습니다. |

## <a name="configure-search-for-data-plane-authentication"></a>데이터 평면 인증 검색 구성

미리 보기 데이터 평면 역할(검색 인덱스 데이터 기여자 또는 검색 인덱스 데이터 읽기 권한자) 및 Azure AD 인증을 사용하는 경우 OAuth2 액세스 토큰을 제공하는 데이터 요청에 대한 **권한 부여** 헤더를 인식하도록 검색 서비스를 구성해야 합니다. 이 섹션에서는 검색 서비스를 구성하는 방법을 설명합니다. 컨트롤 플레인 역할(소유자, 기여자, 읽기 권한자)을 사용하는 경우 이 단계를 건너뛸 수 있습니다.

시작하기 전에 RBAC 미리 보기에 [가입](https://aka.ms/azure-cognitive-search/rbac-preview)하십시오. 이 기능을 사용하려면 먼저 구독을 프로그램에 등록해야 합니다. 등록 요청을 처리하는 데 영업일 기준 최대 2일까지 걸릴 수 있습니다. 서비스가 준비되면 이메일을 받게 됩니다.

### <a name="azure-portal"></a>[**Azure portal**](#tab/config-svc-portal)

1. [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true) 구문을 사용하여 포털을 엽니다.

1. 검색 서비스로 이동합니다.

1. 왼쪽 탐색 창에서 **키** 를 선택합니다.

1. **API 액세스 제어** 메커니즘을 선택합니다. 이러한 옵션이 표시되지 않으면 포털 URL을 확인합니다. 선택 항목을 저장할 수 없는 경우 구독 등록에 문제가 있습니다. 

   | 옵션 | 상태 | 설명 |
   |--------|--------|-------------|
   | API 키 | 일반 공급(기본) | 권한 부여를 위해 요청 헤더에 [관리자 또는 쿼리 API 키](search-security-api-keys.md)가 필요합니다. 역할이 사용되지 않습니다. |
   | 역할 기반 액세스 제어 | 미리 보기 | 다음 단계에서 설명하는 작업을 완료하려면 역할 할당의 멤버 자격이 필요합니다. 각 요청에는 인증 헤더가 필요합니다. 이 옵션을 선택하면 2021-04-30-preview REST API를 지원하는 클라이언트로 제한됩니다. |
   | 모두 | 미리 보기 | 요청은 API 키 또는 권한 부여 토큰을 사용하여 유효합니다. |

검색 서비스가 RBAC를 사용하도록 설정되면 포털에서 역할을 할당하고 콘텐츠를 보기 위해 URL에서 기능 플래그가 필요합니다. **인덱스 및 인덱서와 같은 콘텐츠는 기능 플래그를 통해 여는 경우에만 포털에 표시됩니다.** 나중에 기본 동작을 복원하려면 API 키 선택을 **API 키** 로 되돌려야 합니다.

### <a name="rest-api"></a>[**REST API**](#tab/config-svc-rest)

관리 REST API 버전 2021-04-01-Preview를 사용하여 서비스를 구성합니다.

1. [서비스 만들기 및 업데이트](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)를 호출합니다.

1. [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions)를 `aadOrApiKey`로 설정합니다. 구문은 [이 예제](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#searchcreateorupdateserviceauthoptions)를 참조하세요.

1. [AadAuthFailureMode](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#aadauthfailuremode)를 설정하여 인증에 실패하면 401 또는 403 오류를 반환할지 여부를 지정합니다.

---

## <a name="assign-roles"></a>역할 할당

역할은 Azure 역할 기반 액세스 제어 설명서에 설명된 [지원되는 접근 방식](../role-based-access-control/role-assignments-steps.md) 중 하나를 사용하여 할당할 수 있습니다.

역할 할당을 관리하려면 소유자이거나 [Microsoft.Authorization/roleAssignments/write](/azure/templates/microsoft.authorization/roleassignments) 권한이 있어야 합니다.

### <a name="azure-portal"></a>[**Azure portal**](#tab/rbac-portal)

포털 URL에서 기능 플래그를 설정하여 검색 서비스 기여자, 검색 인덱스 데이터 기여자 및 검색 인덱스 데이터 읽기 권한자의 미리 보기 역할로 작업합니다.

1. [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true) 구문을 사용하여 포털을 엽니다. URL에 `feature.enableRbac=true`가 표시됩니다.

1. 검색 서비스로 이동합니다.

1. 왼쪽 탐색 창에서 **액세스 제어(IAM)** 를 선택합니다.

1. 오른쪽의 **이 리소스에 대한 액세스 부여** 에서 **역할 할당 추가** 를 선택합니다.

1. 적용 가능한 역할(소유자, 기여자, 읽기 권한자, 검색 서비스 기여자, 검색 인덱스 데이터 기여자, 검색 인덱스 데이터 읽기 권한자)을 찾은 다음, Azure Active Directory 사용자 또는 그룹 ID를 할당합니다.

### <a name="powershell"></a>[**PowerShell**](#tab/rbac-powershell)

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

## <a name="configure-requests-and-test"></a>요청 구성 및 테스트

프로그래밍 방식으로 테스트하려면 Search REST API(지원되는 모든 버전)를 사용하도록 코드를 수정하고 요청에 대한 권한 부여 헤더를 설정합니다. Azure SDK 중 하나를 사용하는 경우 해당 베타 릴리스를 확인하여 권한 부여 헤더를 사용할 수 있는지 확인합니다. 

애플리케이션에 따라 Azure Active Directory로 애플리케이션을 등록하거나 권한 부여 토큰을 가져오고 전달하려면 추가 구성이 필요합니다.

또는 Azure Portal 및 자신에게 할당된 역할을 사용하여 테스트할 수 있습니다.

1. [https://ms.portal.azure.com/?feature.enableRbac=true](https://ms.portal.azure.com/?feature.enableRbac=true) 구문을 사용하여 포털을 엽니다. 

   이전 단계에서 서비스가 RBAC를 사용하도록 설정되어 있지만 RBAC 동작을 호출하려면 포털에 기능 플래그가 필요합니다. **인덱스 및 인덱서와 같은 콘텐츠는 기능 플래그를 통해 여는 경우에만 포털에 표시됩니다.** 기본 동작을 복원하려면 API 키 선택을 **API 키** 로 되돌려야 합니다.

1. 검색 서비스로 이동합니다.

1. 개요 페이지에서 **인덱스** 탭을 선택합니다.

   + 검색 인덱스 데이터 읽기 권한자의 멤버 자격은 검색 탐색기를 사용하여 인덱스를 쿼리합니다. 모든 API 버전을 사용하여 액세스를 확인할 수 있습니다.

   + 검색 인덱스 데이터 기여자의 경우 **새 인덱스** 를 선택하여 새 인덱스를 만듭니다. 새 인덱스 저장은 서비스에 대한 쓰기 권한을 확인합니다.

## <a name="disable-api-key-authentication"></a>API 키 인증 사용 안 함

API 키는 삭제할 수 없지만 서비스에서 사용하지 않도록 설정할 수 있습니다. 검색 인덱스 데이터 기여자 및 검색 인덱스 데이터 읽기 권한자 역할 및 Azure AD 인증을 사용하는 경우 API 키를 사용하지 않도록 설정하여 검색 서비스에서 키를 제공하는 모든 데이터 관련 요청을 거부할 수 있습니다.

이 작업에는 미리 보기 관리 REST API 버전 2021-04-01-preview를 사용합니다.

1. [DataPlaneAuthOptions](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update#dataplaneauthoptions)를 `aadOrApiKey`로 설정합니다.

1. [역할을 할당](#assign-roles)하고 올바르게 작동하는지 확인합니다.

1. `disableLocalAuth`를 **True** 로 설정합니다.

`disableLocalAuth`를 **False** 로 설정하는 마지막 단계를 되돌리면 검색 서비스가 요청에 대한 API 키 수락을 자동으로 재개합니다(지정된 것으로 가정).