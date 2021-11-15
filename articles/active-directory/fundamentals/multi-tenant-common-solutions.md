---
title: Azure Active Directory의 다중 테넌트 사용자 관리에 대한 일반적인 솔루션
description: 게스트 계정을 사용하여 Azure Active Directory 테넌트에서 사용자 액세스를 구성하는 데 사용되는 일반적인 솔루션에 대해 알아봅니다.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/25/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48412e7ad5c7ff90f1e5b89542f55b57fec4a534
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273640"
---
# <a name="common-solutions-for-multi-tenant-user-management"></a>다중 테넌트 사용자 관리에 대한 일반적인 솔루션

고객이 현재 도구를 사용하여 해결한 두 가지 특정 문제가 있습니다. 해당 솔루션은 아래에서 자세히 설명하고 있습니다. Microsoft는 가능한 경우 단일 테넌트를 권장하며, 이러한 문제를 더 쉽게 ​​해결할 수 있는 도구를 개발하고 있습니다. 단일 테넌트가 사용자의 시나리오에서 작동하지 않는 경우 이러한 솔루션은 오늘날 고객에게 효과적입니다. 

## <a name="automatic-user-lifecycle-management-and-resource-allocation-across-tenants"></a>테넌트 간 자동 사용자 수명 주기 관리 및 리소스 할당

고객은 이전에 긴밀한 비즈니스 관계를 맺은 경쟁업체를 인수합니다. 조직은 회사 ID를 유지 관리합니다.

### <a name="current-state"></a>현재 상태

현재 조직에서 서로의 디렉터리에 표시되도록 서로의 사용자를 연락처 메일 개체로 동기화하고 있습니다. 

* 각 리소스 테넌트에는 다른 테넌트의 모든 사용자에 대해 사용하도록 설정된 메일 연락처 개체가 있습니다.

* 테넌트 전체에서 애플리케이션에 액세스할 수 없습니다.

### <a name="goals"></a>목표

이 고객의 목표는 다음과 같았습니다.

* 모든 사용자가 각 조직의 GAL에 계속 표시됩니다.

   * 홈 테넌트의 사용자 계정 수명 주기 변경 내용이 리소스 테넌트 GAL에 자동으로 반영됩니다. 

   * 홈 테넌트의 특성 변경 내용(예: 부서, 이름, SMTP 주소)이 리소스 테넌트 GAL 및 홈 GAL에 자동으로 반영됩니다.

* 사용자가 리소스 테넌트에서 애플리케이션 및 리소스에 액세스할 수 있습니다.

* 사용자가 리소스에 대한 셀프 서비스 액세스 요청을 수행할 수 있습니다.

### <a name="solution-architecture"></a>솔루션 아키텍처 

조직에서 MIM과 같은 동기화 엔진을 통해 지점 간 아키텍처를 사용합니다.

![지점 간 아키텍처의 예](media/multi-tenant-common-solutions/point-to-point-sync.png)

각 테넌트 관리자는 다음을 수행하여 사용자 개체를 만듭니다.

1. 사용자의 데이터베이스가 최신 상태인지 확인합니다.

1. [MIM을 배포하고 구성](/microsoft-identity-manager/microsoft-identity-manager-deploy)합니다.

   1. 기존 연락처 개체를 처리합니다.

   1. 다른 테넌트의 멤버에 대한 B2B 외부 멤버 개체를 만듭니다.

   1. 사용자 개체 특성을 동기화합니다.

1. [권한 관리](../governance/entitlement-management-overview.md) 액세스 패키지를 배포하고 구성합니다.

   1. 공유할 리소스

   1. 만료 및 액세스 검토 정책

## <a name="sharing-on-premises-apps-across-tenants"></a>테넌트 간 온-프레미스 앱 공유

여러 피어 조직이 있는 이 고객은 테넌트 중 하나에서 온-프레미스 애플리케이션을 공유해야 합니다.

### <a name="current-state"></a>현재 상태

여러 피어 조직이 메시 토폴로지에서 B2B 게스트 사용자를 동기화하여 테넌트 전체에서 리소스를 클라우드 애플리케이션에 할당할 수 있습니다. 현재 상태는 다음과 같습니다.

* Azure AD에서 애플리케이션을 공유합니다.

* 리소스 테넌트의 사용자 수명 주기 관리가 홈 테넌트를 기반으로 하여 자동화되도록 합니다. 즉, 추가, 수정, 삭제가 반영됩니다.

* 회사 A의 멤버 사용자만 회사 A의 온-프레미스 앱에 액세스합니다.

![다중 테넌트 시나리오](media/multi-tenant-user-management-scenarios/mesh.png)

### <a name="goals"></a>목표

현재 기능과 함께 다음을 원합니다.

* 외부 게스트 사용자에게 회사 A의 온-프레미스 리소스에 대한 액세스를 제공합니다. 

* SAML 인증이 있는 앱

* Windows 통합 인증 및 Kerberos가 있는 앱

### <a name="solution-architecture"></a>솔루션 아키텍처

회사 A에서 현재 자체 멤버를 위해 Azure 애플리케이션 프록시를 통해 SSO를 온-프레미스 앱에 제공하고 있습니다.

![애플리케이션 액세스의 예](media/multi-tenant-common-solutions/app-access-scenario.png)

게스트 사용자가 동일한 온-프레미스 애플리케이션에 액세스할 수 있도록 하기 위해 테넌트 A의 관리자가 다음을 수행합니다.

1. [SAML 앱에 대한 액세스를 구성](../external-identities/hybrid-cloud-to-on-premises.md#access-to-saml-apps)합니다.

2. [다른 애플리케이션에 대한 액세스를 구성](../external-identities/hybrid-cloud-to-on-premises.md#access-to-iwa-and-kcd-apps)합니다.

3. [MIM](../external-identities/hybrid-cloud-to-on-premises.md#create-b2b-guest-user-objects-through-mim) 또는 [PowerShell](https://www.microsoft.com/en-us/download/details.aspx?id=51495)을 통해 온-프레미스 게스트 사용자를 만듭니다.

B2B 협업에 대한 자세한 내용은 다음을 참조하세요.

[Azure AD의 B2B 사용자에게 온-프레미스 리소스에 대한 액세스 권한 부여](../external-identities/hybrid-cloud-to-on-premises.md)

[하이브리드 조직에 대한 Azure Active Directory B2B 협업](../external-identities/hybrid-organizations.md)

## <a name="next-steps"></a>다음 단계
[다중 테넌트 사용자 관리 소개](multi-tenant-user-management-introduction.md)

[다중 테넌트 최종 사용자 관리 시나리오](multi-tenant-user-management-scenarios.md)

[일반적인 다중 테넌트 고려 사항](multi-tenant-common-considerations.md)
