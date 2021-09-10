---
title: 권한 부여 기본 사항 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼의 권한 부여 기본 사항을 알아봅니다.
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/23/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: johngarland, mamarxen, ianbe, marsma
ms.openlocfilehash: 4dcdb90daa9bb8cd1df79ec399eb9372fca02c61
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037192"
---
# <a name="authorization-basics"></a>권한 부여 기본 사항

**권한 부여**(약자로 *AuthZ* 라고도 함)는 리소스나 기능에 대한 액세스 권한을 평가하는 데 사용되는 권한을 설정하는 데 사용됩니다.  반대로 **인증**(약자로 *AuthN* 이라고도 함)은 사용자나 서비스와 같은 엔터티가 실제로 클레임되는지를 입증하는 데 중점을 둡니다.

권한 부여에는 엔터티가 액세스할 수 있는 기능(또는 리소스) 또는 엔터티가 액세스할 수 있는 데이터 및 해당 데이터로 수행할 수 있는 작업 지정이 포함될 수 있습니다. 이를 흔히 *액세스 제어* 라고 합니다.

> [!NOTE]
> 인증 및 권한 부여는 사용자로만 제한되지 않는 개념입니다. 서비스나 디먼 애플리케이션은 특정 사용자를 대신하여 리소스를 직접 요청하도록 빌드되는 경우가 많습니다. 이러한 주제를 논의할 때 "엔터티"라는 용어는 사용자나 애플리케이션을 나타내는 데 사용됩니다.


## <a name="authorization-approaches"></a>권한 부여 방법

권한 부여를 처리하는 몇 가지 일반적인 방법이 있습니다. [역할 기반 액세스 제어](./custom-rbac-for-developers.md)는 현재 Microsoft ID 플랫폼을 사용하는 가장 일반적인 방법입니다.


### <a name="authentication-as-authorization"></a>권한 부여로서 인증 

가장 간단한 권한 부여 형태는 요청을 만드는 엔터티가 인증되었는지 여부에 따라 액세스 권한을 부여하거나 거부하는 것입니다. 요청자가 자신의 클레임을 입증할 수 있으면 보호된 리소스나 기능에 액세스할 수 있습니다.

### <a name="access-control-lists"></a>액세스 제어 목록

ACL(액세스 제어 목록)을 통한 권한 부여에는 리소스나 기능에 대한 액세스 권한 유무에 관계없이 특정 엔터티의 명시적 목록을 유지 관리하는 작업이 포함됩니다. ACL은 authentication-as-authorization을 통해 세분화된 제어를 제공하지만 엔터티 수가 증가하면 관리하기가 어려워집니다.

### <a name="role-based-access-control"></a>역할 기반 액세스 제어 

RBAC(역할 기반 액세스 제어)는 애플리케이션에서 권한 부여를 적용하는 가장 일반적인 방법입니다. RBAC를 사용하는 경우 역할은 엔터티에서 수행할 수 있는 활동의 종류를 설명하기 위해 정의됩니다. 애플리케이션 개발자는 개별 엔터티가 아닌 역할에 대한 액세스 권한을 부여합니다. 그런 다음, 관리자는 다른 엔터티에 역할을 할당하여 어떤 엔터티가 어떤 리소스와 기능에 액세스하는지를 제어할 수 있습니다.

고급 RBAC 구현에서 역할은 권한 컬렉션에 매핑될 수 있습니다. 여기서 권한은 수행할 수 있는 세분화된 작업을 설명합니다. 그런 다음, 역할은 권한 조합으로 구성됩니다. 엔터티가 할당된 다양한 역할에 부여된 권한을 교차하여 애플리케이션에 대한 엔터티 전체 권한 집합을 컴퓨팅합니다. 이 방법의 좋은 예는 Azure 구독의 리소스에 대한 액세스를 제어하는 RBAC 구현입니다.

> [!NOTE]
> [애플리케이션 RBAC](./custom-rbac-for-developers.md)는 [Azure RBAC](/azure/role-based-access-control/overview) 및 [Azure AD RBAC](../roles/custom-overview.md#understand-azure-ad-role-based-access-control)와 다릅니다. Azure 사용자 지정 역할 및 기본 제공 역할은 모두 Azure RBAC에 포함되며 Azure 리소스를 관리하는 데 도움이 됩니다. Azure AD RBAC를 사용하여 Azure AD 리소스를 관리할 수 있습니다.

### <a name="attribute-based-access-control"></a>특성 기반 액세스 제어 

ABAC(특성 기반 액세스 제어)는 더욱 세분화된 액세스 제어 메커니즘입니다. 이 방법에서 규칙은 엔터티의 특성, 액세스 중인 리소스 및 현재 환경에 적용되어 일부 리소스나 기능에 대한 액세스가 허용되는지 여부를 결정합니다. 예를 들어 관리자만 영업일 오전 9시부터 오후 5시까지 "근무 시간 동안에만 관리자"라는 메타데이터 태그로 식별된 파일에 액세스하도록 허용할 수 있습니다. 이 경우 사용자 특성(관리자 상태), 리소스 특성(파일의 메타데이터 태그) 및 환경 특성(현재 시간)을 검사하여 액세스를 결정합니다.

ABAC의 한 가지 장점은 매우 구체적인 역할이나 RBAC 할당을 많이 만들 필요 없이 규칙과 조건 평가를 통해 더욱 세분화된 동적 액세스 제어를 달성할 수 있다는 점입니다.

Azure Active Directory를 사용하여 ABAC를 달성하는 한 가지 방법은 [동적 그룹](../enterprise-users/groups-create-rule.md)을 사용하는 것입니다. 동적 그룹을 사용하면 관리자가 원하는 값을 가진 특정 사용자 특성에 따라 동적으로 사용자를 그룹에 할당할 수 있습니다.  예를 들어 Author라는 직위가 있는 모든 사용자가 Authos 그룹에 동적으로 할당되는 Authors 그룹을 만들 수 있습니다.  역할을 그룹에 매핑하고 사용자를 동적으로 그룹에 할당하는 권한 부여에 RBAC와 함께 동적 그룹을 사용할 수 있습니다.

[Azure ABAC](../../role-based-access-control/conditions-overview.md)는 현재 사용 가능한 ABAC 솔루션의 예입니다. Azure ABAC는 특정 작업의 맥락에서 특성을 기반으로 역할 할당 조건을 추가하여 Azure RBAC에서 빌드됩니다. 

## <a name="implementing-authorization"></a>권한 부여 구현

권한 부여 논리는 액세스 제어가 필요한 애플리케이션이나 솔루션 내에서 구현되는 경우가 많습니다. 대부분의 경우 애플리케이션 개발 플랫폼은 권한 부여 구현을 간소화하는 미들웨어나 기타 API 솔루션을 제공합니다. 예에는 ASP.NET에서 [AuthorizeAttribute](/aspnet/core/security/authorization/simple?view=aspnetcore-5.0&preserve-view=true) 사용이나 Angular에서 [경로 가드](./scenario-spa-sign-in.md?tabs=angular2#sign-in-with-a-pop-up-window) 사용이 포함됩니다.

인증된 엔터티에 대한 정보를 사용하는 권한 부여 방법의 경우 애플리케이션은 인증 중에 교환된 정보를 평가합니다. [보안 토큰](./security-tokens.md) 내에 제공된 정보를 사용하는 경우가 이 예에 해당됩니다. 정보가 보안 토큰에 포함되지 않은 경우 애플리케이션은 외부 리소스를 추가로 호출할 수 있습니다.

개발자가 권한 부여 논리를 애플리케이션 내에 완전히 포함시킬 필요는 없습니다. 대신 전용 권한 부여 서비스를 사용하여 권한 부여 구현과 관리를 한 곳에서 제어할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- 애플리케이션의 사용자 지정 역할 기반 액세스 제어 구현에 대한 자세한 내용은 [애플리케이션 개발자를 위한 역할 기반 액세스 제어](./custom-rbac-for-developers.md)를 참조하세요.
- Microsoft ID 플랫폼과 통합할 수 있도록 애플리케이션을 등록하는 프로세스에 대해 알아보려면 [애플리케이션 모델](./application-model.md)을 참조하세요.
- 간단한 인증 기반 권한 부여를 구성하는 예는 [Azure AD 로그인을 사용하도록 App Service 또는 Azure Functions 앱 구성](/azure/app-service/configure-authentication-provider-aad)을 참조하세요.
