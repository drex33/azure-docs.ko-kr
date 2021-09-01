---
title: '방법: Microsoft ID 플랫폼에서 등록된 앱 제거 | Azure'
titleSuffix: Microsoft identity platform
description: 이 방법 문서에서는 Microsoft ID 플랫폼에 등록된 애플리케이션을 제거하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 0cea2fdd28c486dbd4b1ca3872cb4e5afaa0379a
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039684"
---
# <a name="remove-an-application-registered-with-the-microsoft-identity-platform"></a>Microsoft ID 플랫폼에 등록된 애플리케이션 제거

Microsoft ID 플랫폼에 애플리케이션을 등록한 Enterprise 개발자 및 SaaS(Software-as-a-Service) 공급자는 애플리케이션 등록을 제거해야 할 수 있습니다.

다음 섹션에서는 아래와 같은 작업 방법을 알아봅니다.

* 사용자 또는 해당 조직이 작성한 애플리케이션 제거
* 다른 조직이 작성한 애플리케이션 제거

## <a name="prerequisites"></a>사전 요구 사항

* [Azure AD 테넌트에 등록된 애플리케이션](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>사용자 또는 해당 조직이 작성한 애플리케이션 제거

사용자 또는 해당 조직이 등록한 애플리케이션은 테넌트에서 애플리케이션 개체와 서비스 주체 개체 모두로 표시됩니다. 자세한 내용은 [애플리케이션 개체 및 서비스 사용자 개체](./app-objects-and-service-principals.md)를 참조하세요.

> [!NOTE]
> 애플리케이션을 삭제하면 애플리케이션의 홈 디렉터리에 있는 서비스 주체 개체도 삭제됩니다. 다중 테넌트 애플리케이션의 경우에는 다른 디렉터리의 서비스 주체 개체가 삭제되지 않습니다.

애플리케이션을 삭제하려면 애플리케이션의 소유자 목록에 있거나 관리 권한이 있어야 합니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 앱이 등록된 테넌트를 선택합니다.
1. **Azure Active Directory** 검색하고 선택합니다. 
1. **관리** 에서 **앱 등록** 을 선택하고 구성하려는 애플리케이션을 선택합니다. 앱을 선택하면 애플리케이션의 **개요** 페이지가 나타납니다.
1. **개요** 페이지에서 **삭제** 를 선택합니다.
1. 삭제 결과를 읽습니다.  창 아래에 상자가 나타나면 상자를 선택합니다.
1. **삭제** 를 선택하여 앱 삭제를 확인합니다.

## <a name="remove-an-application-authored-by-another-organization"></a>다른 조직이 작성한 애플리케이션 제거

테넌트의 컨텍스트에서 **앱 등록** 을 볼 경우 **모든 앱** 탭에 표시되는 애플리케이션의 하위 집합이 다른 테넌트의 것이고 동의 프로세스 중 해당 테넌트에 등록된 것입니다. 구체적으로 말하면 해당하는 애플리케이션 개체 없이 테넌트의 서비스 주체 개체에 의해서만 제공됩니다. 애플리케이션 및 서비스 주체 개체의 차이에 대한 자세한 내용은 [Azure AD의 애플리케이션 및 서비스 주체 개체](./app-objects-and-service-principals.md)를 참조하세요.

(동의를 표시한 후에) 자사의 디렉토리에 대한 애플리케이션 액세스 권한을 제거하려면 회사 관리자는 해당 서비스 주체를 제거해야 합니다. 관리자에게는 전역 관리자 액세스 권한이 있어야 하고 Azure Portal을 통해 애플리케이션을 제거하거나 [Azure AD PowerShell Cmdlet](/previous-versions/azure/jj151815(v=azure.100))을 사용하여 액세스 권한을 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Microsoft ID 플랫폼의 [애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)에 대해 자세히 알아봅니다.
