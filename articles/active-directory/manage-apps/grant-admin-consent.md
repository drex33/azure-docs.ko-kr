---
title: 애플리케이션에 테넌트 전체 관리자 동의 부여 - Azure AD
description: 최종 사용자가 애플리케이션에 로그인할 때 동의 여부를 묻지 않도록 애플리케이션에 테넌트 전체 동의를 부여하는 방법을 알아봅니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/21/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee3ff6fc063de5eebd4a7cc300fdc0a5ceeba560
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123038394"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>애플리케이션에 대한 테넌트 전체 관리자 동의 부여

  애플리케이션에 테넌트 전체 관리자 동의를 부여하는 방법을 알아봅니다. 이 문서에서는 이 목적을 달성하는 다양한 방법을 제공합니다.

애플리케이션에 동의하는 방법에 대한 자세한 내용은 [Azure Active Directory 동의 프레임워크](../develop/consent-framework.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

테넌트 전체 관리자 동의를 부여하려면 조직을 대신하여 동의할 수 있는 사용자로 로그인해야 합니다. 여기에는 [전역 관리자](../roles/permissions-reference.md#global-administrator) 및 [권한 있는 역할 관리자](../roles/permissions-reference.md#privileged-role-administrator)가 포함됩니다. Microsoft Graph 또는 Azure AD Graph에 대한 애플리케이션 권한이 필요하지 않은 애플리케이션의 경우, [애플리케이션 관리자](../roles/permissions-reference.md#application-administrator) 및 [클라우드 애플리케이션 관리자](../roles/permissions-reference.md#cloud-application-administrator)도 여기에 포함됩니다. [애플리케이션에 권한을 부여할 수 있는 권한](../roles/custom-consent-permissions.md)을 포함하는 [사용자 지정 디렉터리 역할](../roles/custom-create.md)이 할당된 사용자에게는 테넌트 전체 동의를 부여할 수 있는 권한도 부여될 수 있습니다.

> [!WARNING]
> 애플리케이션에 테넌트 전체 관리자 동의를 부여하면 앱과 앱 게시자에게 조직의 데이터에 대한 액세스 권한이 부여됩니다. 동의를 부여하기 전에 애플리케이션에서 요청하는 권한을 신중하게 검토합니다.

> [!IMPORTANT]
> 애플리케이션에 테넌트 전체 관리자 동의가 부여되면 사용자 할당을 요구하도록 구성되지 않은 한 모든 사용자가 앱에 로그인할 수 있습니다. 애플리케이션에 로그인할 수 있는 사용자를 제한하려면 사용자 할당을 요구하고 애플리케이션에 사용자 또는 그룹을 할당합니다. 자세한 내용은 [사용자 및 그룹을 할당하는 메서드](./assign-user-or-group-access-portal.md)를 참조하세요.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Azure Portal에서 관리자 동의 부여

### <a name="grant-admin-consent-in-enterprise-apps"></a>엔터프라이즈 앱에서 관리자 동의 허용

애플리케이션이 테넌트에 이미 프로비저닝된 경우 '엔터프라이즈 애플리케이션'을 통해 테넌트 전체 관리자 동의를 부여할 수 있습니다. 예를 들어 하나 이상의 사용자가 이미 애플리케이션에 동의한 경우 테넌트에서 애플리케이션을 프로비저닝할 수 있습니다. 자세한 내용은 [애플리케이션을 Azure Active Directory에 추가하는 방법 및 이유](../develop/active-directory-how-applications-are-added.md)를 참조하세요.

**엔터프라이즈 애플리케이션** 에 나열된 앱에 테넌트 전체 관리자 동의를 부여하려면

1. 관리자 동의를 부여할 수 있는 역할로 [Azure Portal](https://portal.azure.com)에 로그인합니다([필수 조건](#prerequisites) 참조).
2. **Azure Active Directory** 를 선택한 다음 **엔터프라이즈 애플리케이션** 을 선택합니다.
3. 테넌트 전체 관리자 동의를 부여하려는 애플리케이션을 선택합니다.
4. **권한** 을 선택한 다음 **관리자 동의 부여** 를 클릭합니다. 이 예제에서는 10,000ft Plans 애플리케이션을 사용합니다.

   :::image type="content" source="media/grant-tenant-wide-admin-consent/grant-tenant-wide-admin-consent.png" alt-text="테넌트 전체 관리자 동의를 부여하는 방법을 보여 주는 스크린샷":::

5. 애플리케이션에 필요한 권한을 신중하게 검토합니다.
6. 애플리케이션에 필요한 권한에 동의하면 동의를 부여합니다. 그렇지 않으면 **취소** 를 클릭하거나 창을 닫습니다.

> [!WARNING]
> **엔터프라이즈 애플리케이션** 을 통해 테넌트 전체 관리자 동의를 부여하면 이전에 테넌트 전체에 부여된 모든 권한이 해지됩니다. 사용자가 이전에 부여한 권한은 영향을 받지 않습니다.

### <a name="grant-admin-consent-in-app-registrations"></a>앱 등록에서 관리자 동의 부여

조직에서 개발한 애플리케이션 또는 Azure AD 테넌트에서 직접 등록된 애플리케이션의 경우 Azure Portal에서 **앱 등록** 을 통해 테넌트 전체 관리자 동의를 부여할 수도 있습니다.

**앱 등록** 에서 테넌트 전체 관리자 동의를 부여하려면

1. 관리자 동의를 부여할 수 있는 역할로 [Azure Portal](https://portal.azure.com)에 로그인합니다([필수 조건](#prerequisites) 참조).
2. **Azure Active Directory** 를 선택한 다음, **앱 등록** 을 선택합니다.
3. 테넌트 전체 관리자 동의를 부여하려는 애플리케이션을 선택합니다.
4. **API 권한** 을 선택한 다음 **관리자 동의 부여** 를 클릭합니다.
5. 애플리케이션에 필요한 권한을 신중하게 검토합니다.
6. 애플리케이션에 필요한 권한에 동의하면 동의를 부여합니다. 그렇지 않으면 **취소** 를 클릭하거나 창을 닫습니다.

> [!WARNING]
> **앱 등록** 을 통해 테넌트 전체 관리자 동의를 부여하면 이전에 테넌트 전체에 부여된 모든 권한이 해지됩니다. 사용자가 이전에 부여한 권한은 영향을 받지 않습니다.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>테넌트 전체 관리자 동의를 부여하기 위한 URL 생성

위에서 설명한 방법 중 하나를 사용하여 테넌트 전체 관리자 동의를 부여하는 경우 Azure Portal에서 창이 열리고 테넌트 전체 관리자 동의를 묻는 메시지가 표시됩니다. 애플리케이션의 클라이언트 ID(애플리케이션 ID라고도 함)를 알고 있으면 동일한 URL을 빌드하여 테넌트 전체 관리자 동의를 부여할 수 있습니다.

테넌트 전체 관리자 동의 URL은 다음 형식을 따릅니다.

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

여기서

* `{client-id}`는 애플리케이션의 클라이언트 ID(앱 ID라고도 함)입니다.
* `{tenant-id}`는 조직의 테넌트 ID 또는 확인된 도메인 이름입니다.

언제나처럼 동의를 부여하기 전에 애플리케이션에서 요청하는 권한을 신중하게 검토합니다.

> [!WARNING]
> 이 URL을 통해 테넌트 전체 관리자 동의를 부여하면 이전에 테넌트 전체에 부여된 모든 권한이 해지됩니다. 사용자가 이전에 부여한 권한은 영향을 받지 않습니다.

## <a name="next-steps"></a>다음 단계

[최종 사용자가 애플리케이션에 동의하는 방법 구성](configure-user-consent.md)

[관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)

[Microsoft ID 플랫폼의 권한 및 동의](../develop/v2-permissions-and-consent.md)

[Microsoft Azure AD Q&A](/answers/topics/azure-active-directory.html)
