---
title: Microsoft Azure Maps를 사용한 인증
titleSuffix: Azure Maps
description: Azure Maps에서 요청을 인증하는 두 가지 방법, 즉, 공유 키 인증 및 Azure Active Directory(Azure AD) 인증에 대해 알아봅니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 05/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: 13e6ce8cb6c004b0c1785d645e48f3f18c9dbdf8
ms.sourcegitcommit: 6f30424a4ab8dffc4e690086e898ab52bc4da777
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2021
ms.locfileid: "132902137"
---
# <a name="authentication-with-azure-maps"></a>Azure Maps 인증

Azure Maps에서는 요청을 인증하는 두 가지 방법인 공유 키 인증 및 [Azure Active Directory(Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) 인증을 지원합니다. 이 문서에서는 Azure Maps 서비스의 구현을 안내하는 데 도움이 되는 두 가지 인증 방법을 설명합니다.

> [!NOTE]
> Azure Maps와의 보안 통신을 개선하기 위해 이제 TLS(전송 계층 보안) 1.2를 지원하고 TLS 1.0 및 1.1에 대한 지원을 중단하고 있습니다. 현재 TLS 1.x를 사용하는 경우 TLS 1.2 준비 상태를 평가하고 [TLS 1.0 문제 해결](/security/solving-tls1-problem)에 설명된 테스트를 사용하여 마이그레이션 계획을 수립하세요.

## <a name="shared-key-authentication"></a>공유 키 인증

 Azure Maps 계정이 만들어진 후 기본 및 보조 키가 생성됩니다. 공유 키 인증을 사용한 Azure Maps를 호출할 때 기본 키를 구독 키로 사용하는 것이 좋습니다. 공유 키 인증은 Azure Maps 계정에서 생성한 키를 Azure Maps 서비스로 전달합니다. Azure Maps 서비스에 요청할 때마다 ‘구독 키’를 URL에 매개 변수로 추가해야 합니다. 키 롤링 변경 등의 시나리오에서 보조 키를 사용할 수 있습니다.

*URL에서 구독 키를* 매개 변수로 사용하는 예제:

```http
https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
```  

Azure Portal에서 키를 보는 방법에 대한 자세한 내용은 [인증 관리](./how-to-manage-authentication.md#view-authentication-details)를 참조하세요.

> [!NOTE]
> 기본 키와 보조 키는 중요한 데이터로 취급되어야 합니다. 공유 키는 모든 Azure Maps REST API를 인증하는 데 사용됩니다.  공유 키를 사용하는 사용자는 API 키를 중앙에서 관리될 수 있는 환경 변수 또는 보안 비밀 저장소를 통해 추상화해야 합니다.

## <a name="azure-ad-authentication"></a>Azure AD 인증

Azure 구독은 세분화된 액세스 제어를 사용할 수 있도록 Azure AD 테넌트와 함께 제공됩니다. Azure Maps는 Azure AD를 사용하여 Azure Maps 서비스에 대한 인증을 제공합니다. Azure AD는 Azure AD 테넌트에 등록된 사용자 및 애플리케이션에 대한 ID 기반 인증을 제공합니다.

Azure Maps는 Azure Maps 계정을 포함하고 있는 Azure 구독과 연결된 Azure AD 테넌트에 대한 **OAuth 2.0** 액세스 토큰을 허용합니다. Azure Maps는 다음에 대한 토큰을 허용합니다.

* Azure AD 사용자
* 사용자가 위임한 권한을 사용하는 파트너 애플리케이션
* Azure 리소스에 대한 관리 ID

Azure Maps는 각 Azure Maps 계정에 대해 *고유 식별자(클라이언트 ID)* 를 생성합니다. 이 클라이언트 ID를 추가 매개 변수와 함께 사용하는 경우 Azure AD에서 토큰을 요청할 수 있습니다.

Azure AD를 구성하고 Azure Maps에 대한 토큰을 요청하는 방법에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

Azure AD를 사용하여 인증하는 방법에 대한 일반 정보는 [인증 이란?](../active-directory/develop/authentication-vs-authorization.md)을 참조하세요.

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 리소스 및 Azure Maps에 대한 관리형 ID

[Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)는 Azure AD를 사용하여 인증할 수 있는 자동 관리형 애플리케이션을 기반으로 하는 보안 주체를 Azure 서비스에 제공합니다. Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하면 관리 ID 보안 주체에 Azure Maps 서비스에 액세스할 수 있는 권한을 부여할 수 있습니다. 관리 ID의 몇 가지 예는 Azure App Service, Azure Functions 및 Azure Virtual Machines입니다. 관리 ID 목록에 대해서는 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)를 참조하세요.

### <a name="configuring-application-azure-ad-authentication"></a>애플리케이션 Azure AD 인증 구성

애플리케이션은 Azure AD에서 제공하는 하나 이상의 시나리오를 사용하는 Azure AD 테넌트로 인증합니다. 각 Azure AD 애플리케이션 시나리오는 비즈니스에서 요구하는 바에 따라 다양한 요구 사항을 나타냅니다. 일부 애플리케이션에는 사용자 로그인 경험이 필요할 수 있으며, 다른 애플리케이션에는 애플리케이션 로그인 경험이 필요할 수 있습니다. 자세한 내용은 [인증 흐름 및 애플리케이션 시나리오](../active-directory/develop/authentication-flows-app-scenarios.md)를 참조하세요.

애플리케이션이 액세스 토큰을 받은 후 SDK 및/또는 애플리케이션은 다른 REST API HTTP 헤더 외에도 다음과 같은 필수 HTTP 헤더 집합을 사용하여 HTTPS 요청을 보냅니다.

| 헤더 이름    | 값               |
| :------------- | :------------------ |
| x-ms-client-id | 30d7cc….9f55        |
| 권한 부여  | Bearer eyJ0e….HNIVN |

> [!NOTE]
> `x-ms-client-id`는 Azure Maps 인증 페이지에 표시되는 Azure Maps 계정 기반 GUID입니다.

Azure AD OAuth 전달자 토큰을 사용하는 Azure Maps 경로 요청의 예는 다음과 같습니다.

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

클라이언트 ID를 보는 방법에 대한 내용은 [인증 세부 정보 보기](./how-to-manage-authentication.md#view-authentication-details)를 참조하세요.

## <a name="authorization-with-role-based-access-control"></a>역할 기반 액세스 제어를 사용하는 권한 부여

Azure Maps는 개별 Azure AD 사용자, 그룹, 애플리케이션, Azure 리소스 및 Azure 관리 ID를 포함하여 [Azure RBAC](../role-based-access-control/overview.md)(Azure 역할 기반 액세스 제어)의 모든 보안 주체 유형에 대한 액세스를 지원합니다. 보안 주체 유형에는 역할 정의라고도 하는 권한 집합이 부여됩니다. 역할 정의는 REST API 작업에 대한 권한을 제공합니다. 하나 이상의 Azure Maps 계정에 대한 액세스를 적용하는 것을 범위라고 합니다. 보안 주체, 역할 정의 및 범위를 적용하는 경우 역할 할당이 만들어집니다.

다음 섹션에서는 Azure RBAC와 Azure Maps 통합의 개념 및 구성 요소에 대해 설명합니다. Azure Maps 계정을 설정하는 프로세스의 일부로, Azure AD 디렉터리는 Azure Maps 계정이 있는 Azure 구독에 연결됩니다.

Azure RBAC를 구성하는 경우 보안 주체를 선택하여 역할 할당에 적용합니다. Azure Portal에 역할 할당을 추가하는 방법에 대한 자세한 내용은 [Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

### <a name="picking-a-role-definition"></a>역할 정의 선택

애플리케이션 시나리오를 지원하기 위해 다음과 같은 역할 정의 형식이 있습니다.

| Azure 역할 정의       | 설명                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Azure Maps 데이터 읽기 권한자      | 변경할 수 없는 Azure Maps REST API에 대한 액세스를 제공합니다.                                                       |
| Azure Maps Data Contributor | 변경 가능한 Azure Maps REST API에 대한 액세스를 제공합니다. 변경 가능성은 쓰기 및 삭제 작업으로 정의됩니다. |
| 사용자 지정 역할 정의      | Azure Maps REST API에 제한된 액세스를 유연성 있게 사용할 수 있도록 하는 특수 역할을 만듭니다.                      |

일부 Azure Maps 서비스에는 Azure Maps REST API에 대한 쓰기 또는 삭제 작업을 수행하기 위해 상승된 권한이 필요할 수 있습니다. 쓰기 또는 삭제 작업을 제공하는 서비스에는 Azure 지도 데이터 기여자 역할이 필요합니다. 다음 표에서는 쓰기 또는 삭제 작업을 사용할 때 Azure 지도 데이터 기여자가 적용할 수 있는 서비스에 대해 설명합니다. 읽기 작업만 필요한 경우 Azure 지도 데이터 기여자 역할 대신 Azure 지도 데이터 판독기 역할을 사용할 수 있습니다.

| Azure Maps 서비스 | Azure Maps 역할 정의  |
| :----------------- | :-------------------------- |
| 데이터               | Azure Maps Data Contributor |
| 만든 이            | Azure Maps Data Contributor |
| 공간            | Azure Maps Data Contributor |

Azure RBAC 설정을 보는 방법에 대한 내용은 [Azure Maps에 대한 Azure RBAC 구성 방법](./how-to-manage-authentication.md)을 참조하세요.

#### <a name="custom-role-definitions"></a>사용자 지정 역할 정의

애플리케이션 보안의 한 가지 측면은 최소 권한의 원칙, 즉 현재 작업을 수행하는 데 필요한 권한으로만 액세스 권한을 제한하는 사례입니다. 이렇게 하려면 액세스 제어에 대한 세분성이 필요한 사용 사례를 지원하는 사용자 지정 역할 정의를 만듭니다. 사용자 지정 역할 정의를 만들려면 정의에 대해 포함하거나 제외할 특정 데이터 작업을 선택합니다.

그런 다음 사용자 지정 역할 정의를 모든 보안 주체에 대한 역할 할당에 사용할 수 있습니다. Azure 사용자 지정 역할 정의에 대한 자세한 정보는 [Azure 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요.

사용자 지정 역할이 애플리케이션 보안을 향상시킬 수 있는 몇 가지 예제 시나리오는 다음과 같습니다.

| 시나리오                                                                                                                                                                                                                 | 사용자 지정 역할 데이터 작업                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| 기본 지도 타일과 다른 REST API가 없는 퍼블릭 연결 또는 대화형 로그인 웹 페이지입니다.                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| 역방향 지오코딩만 필요하고 다른 REST API는 필요하지 않은 애플리케이션입니다.                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| Azure 지도 Creator 기반 지도 데이터 및 기본 맵 타일 REST API의 읽기를 요청하는 보안 주체에 대한 역할입니다.                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| 작성자 기반 지도 데이터의 읽기, 쓰기 및 삭제가 필요한 보안 주체에 대한 역할입니다. 이 역할은 지도 데이터 편집기 역할로 정의할 수 있지만 기본 지도 타일과 같은 다른 REST API에 대한 액세스를 허용하지 않습니다. | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>범위 이해

역할 할당을 만들 때 Azure 리소스 계층 구조 내에서 정의됩니다. 계층의 맨 위에는 [관리 그룹](../governance/management-groups/overview.md)이 있고, 가장 낮은 계층은 Azure Maps 계정과 같은 Azure 리소스입니다.
리소스 그룹에 역할 할당을 할당하면 그룹의 여러 Azure Maps 계정 또는 리소스에 대한 액세스를 설정할 수 있습니다.

> [!TIP]
> Microsoft의 일반적인 권장 사항은 Azure Maps 계정 범위에 대한 액세스 권한을 할당하는 것입니다. 동일한 Azure 구독에 존재하는 **다른 Azure Maps 계정에 대해 의도하지 않은 액세스** 를 차단하기 때문입니다.

## <a name="next-steps"></a>다음 단계

Azure RBAC에 대한 자세한 정보는 다음을 참조하세요.
> [!div class="nextstepaction"]
> [Azure 역할 기반 Access Control](../role-based-access-control/overview.md)

Azure AD와 Azure Maps를 통한 애플리케이션 인증 방법에 대한 자세한 정보는 다음을 참조하세요.
> [!div class="nextstepaction"]
> [Azure Maps의 인증 관리](./how-to-manage-authentication.md)

Azure AD를 통한 Azure Maps 맵 컨트롤 인증 방법에 대한 자세한 정보는 다음을 참조하세요.
> [!div class="nextstepaction"]
> [Azure Maps 맵 컨트롤 사용](./how-to-use-map-control.md)