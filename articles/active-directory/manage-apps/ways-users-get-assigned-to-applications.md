---
title: 사용자가 앱에 할당되는 방법 이해
description: ID 관리를 위해 Azure Active Directory를 사용하는 앱에 사용자를 할당하는 방법을 이해합니다.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 5a7955a183893540e6c8de3981ad561200e31f70
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613570"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Azure Active Directory의 앱에 사용자를 할당하는 방법 이해

이 문서에서는 테넌트의 애플리케이션에 사용자가 할당되는 방법을 이해하는 데 도움을 줍니다.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Azure AD의 애플리케이션에 사용자가 할당되는 방법

사용자는 애플리케이션에 액세스하려면 어떤 방식으로든 먼저 애플리케이션에 할당되어야 합니다. 할당은 관리자, 비즈니스 대리자, 경우에 따라 사용자 자신이 수행할 수 있습니다. 아래에서는 사용자가 애플리케이션에 할당될 수 있는 방법을 설명합니다.

* 관리자가 애플리케이션에 직접 [사용자를 할당](./assign-user-or-group-access-portal.md)
* 관리자가 다음을 포함하여 해당 사용자가 애플리케이션의 구성원인 [그룹 할당](./assign-user-or-group-access-portal.md)

  * 온-프레미스에서 동기화된 그룹
  * 클라우드에서 만든 정적 보안 그룹
  * 클라우드에서 만든 [동적 보안 그룹](../enterprise-users/groups-dynamic-membership.md)
  * 클라우드에서 만든 Microsoft 365 그룹
  * [모든 사용자](../fundamentals/active-directory-groups-create-azure-portal.md) 그룹
* 관리자가 [셀프 서비스 애플리케이션 액세스](./manage-self-service-access.md)를 통해 사용자가 **비즈니스 승인 없이** [My Apps](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)**앱 추가** 를 사용하여 애플리케이션을 추가하도록 허용
* 관리자가 [셀프 서비스 애플리케이션 액세스](./manage-self-service-access.md)를 통해 사용자가 **선택된 비즈니스 승인자 세트의 사전 승인이 있는 경우에만** [내 앱](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)**앱 추가** 를 사용하여 애플리케이션을 추가하도록 허용
* 관리자가 [셀프 서비스 그룹 관리](../enterprise-users/groups-self-service-management.md)를 통해 사용자가 **비즈니스 승인 없이** 애플리케이션이 할당된 그룹에 가입하도록 허용
* 관리자가 [셀프 서비스 그룹 관리](../enterprise-users/groups-self-service-management.md)를 통해 사용자가 **선택된 비즈니스 승인자 세트의 사전 승인이 있는 경우에만** 애플리케이션이 할당된 그룹에 가입하도록 허용
* 관리자가 [Microsoft 365](https://products.office.com/)와 같은 자사 애플리케이션에 대해 직접, 사용자에게 라이선스 할당
* 관리자가 해당 [Microsoft 365](https://products.office.com/)와 같은 자사 애플리케이션의 구성원인 그룹에 라이선스 할당
* [관리자가 모든 사용자가 사용할 수 있게 애플리케이션에 동의](../develop/howto-convert-app-to-be-multi-tenant.md)한 다음, 사용자가 애플리케이션에 로그인
* 사용자가 애플리케이션에 로그인하여 [애플리케이션에 직접 동의](../develop/howto-convert-app-to-be-multi-tenant.md)

## <a name="next-steps"></a>다음 단계

* [애플리케이션 관리에 대한 빠른 시작 시리즈](view-applications-portal.md)
* [애플리케이션 관리란?](what-is-application-management.md)
* [Single Sign-On이란?](what-is-single-sign-on.md)