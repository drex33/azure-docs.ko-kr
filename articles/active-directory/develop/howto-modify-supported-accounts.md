---
title: '방법: 애플리케이션에서 지원하는 계정 유형 변경 | Azure'
titleSuffix: Microsoft identity platform
description: 이 방법 문서에서는 Microsoft ID 플랫폼에 등록된 애플리케이션을 구성하여 애플리케이션에 액세스할 수 있는 사용자 또는 계정을 변경합니다.
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
ms.openlocfilehash: c89cb9c43ae274170e0cedb01b1908a874b7317e
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039951"
---
# <a name="modify-the-accounts-supported-by-an-application"></a>애플리케이션에서 지원되는 계정 수정

Microsoft ID 플랫폼을 사용하여 애플리케이션을 등록할 때 해당 계정 유형으로 액세스할 수 있는 사용자를 지정했습니다. 예를 들어 조직 *단일 테넌트* 앱인 조직에서만 계정을 지정했을 수 있습니다. 또는 *다중 테넌트* 앱인 조직(사용자 포함)에 계정을 지정했을 수 있습니다.

다음 섹션에서는 Azure Portal에서 애플리케이션의 등록을 수정하여 애플리케이션에 액세스할 수 있는 사람 또는 계정 유형을 변경하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure AD 테넌트에 등록된 애플리케이션](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>다른 계정을 지원하기 위해 애플리케이션을 변경합니다.

기존 앱 등록에서 지원되는 계정 유형에 대해 다른 설정을 지정하려면 다음을 수행합니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트를 선택합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 아래에서 **앱 등록** 을 선택한 다음, 애플리케이션을 선택합니다.
1. 이제 애플리케이션을 사용할 수 있는 사용자(*로그인 대상* 이라고도 함)를 지정합니다.

    | 지원되는 계정 유형 | Description |
    |-------------------------|-------------|
    | **이 조직 디렉터리의 계정만** | *사용자* 의 테넌트에 있는 사용자(또는 게스트)만 사용할 수 있도록 애플리케이션을 빌드하는 경우 이 옵션을 선택합니다.<br><br>이는 *LOB(기간 업무)* 애플리케이션이라고 하는 경우가 많으며, Microsoft ID 플랫폼의 **단일 테넌트** 애플리케이션입니다. |
    | **모든 조직 디렉터리의 계정** | *모든* Azure AD 테넌트에 있는 사용자가 애플리케이션을 사용할 수 있도록 하려면 이 옵션을 선택합니다. 예를 들어 여러 조직에 제공하려는 SaaS(Software-as-a-Service) 애플리케이션을 빌드하는 경우에 이 옵션이 적합합니다.<br><br>이는 Microsoft ID 플랫폼에서 **다중 테넌트** 애플리케이션이라고 합니다. |
1. **저장** 을 선택합니다.

### <a name="why-changing-to-multi-tenant-can-fail"></a>다중 테넌트로 변경하지 못할 수 있는 이유

애플리케이션 ID URI(앱 ID URI) 이름 충돌로 인해 애플리케이션 등록을 단일 테넌트에서 다중 테넌트로 전환하는 작업이 실패할 수 있습니다. 예제 앱 ID URI는 `https://contoso.onmicrosoft.com/myapp`입니다.

앱 ID URI는 프로토콜 메시지에서 애플리케이션을 식별하는 방법 중 하나입니다. 단일 테넌트 애플리케이션의 경우 앱 ID URI는 해당 테넌트 내에서만 고유하면 됩니다. 다중 테넌트 애플리케이션의 경우 Azure AD가 모든 테넌트에서 앱을 찾을 수 있도록 전역적으로 고유해야 합니다. 앱 ID URI의 호스트 이름이 Azure AD 테넌트의 [확인된 게시자 도메인](howto-configure-publisher-domain.md) 중 하나와 일치하도록 하여 전역 고유성이 적용됩니다.

예를 들어, 테넌트의 이름이 *contoso.onmicrosoft.com* 이면 `https://contoso.onmicrosoft.com/myapp`은 유효한 앱 ID URI입니다. 테넌트에 *contoso.com* 의 확인된 도메인이 있으면 유효한 앱 ID URI도 `https://contoso.com/myapp`이 됩니다. 앱 ID URI가 두 번째 패턴인 `https://contoso.com/myapp`을 따르지 않으면 앱 등록을 다중 테넌트로 변환하지 못합니다.

확인된 게시자 도메인 구성에 대한 자세한 내용은 [확인된 도메인 구성](howto-configure-publisher-domain.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[앱을 단일 테넌트에서 다중 테넌트로 변환](howto-convert-app-to-be-multi-tenant.md)하기 위한 요구 사항을 자세히 알아봅니다.
