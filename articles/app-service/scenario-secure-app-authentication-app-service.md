---
title: 자습서 - Azure App Service의 웹앱에 인증 추가 | Azure
description: 이 자습서에서는 Azure App Service에서 실행되는 웹앱에 인증 및 권한 부여를 사용하도록 설정하는 방법에 대해 알아봅니다. 웹앱에 액세스할 수 있는 사람을 조직 내 사용자로 제한합니다.
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 09/14/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: a14d63d171c3e6bdbcbe0c706f5f614eae7e853b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128628613"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>자습서: Azure App Service에서 실행되는 웹앱에 인증 추가

Azure App Service에서 실행되는 웹앱에 인증을 사용하도록 설정하고, 액세스 권한을 조직 내 사용자로 제한하는 방법을 알아봅니다.

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="사용자 로그인을 보여주는 다이어그램." border="false":::

App Service는 기본 제공 인증 및 권한 부여 지원을 제공하므로 웹앱에서 코드를 최소한으로 작성하거나 전혀 작성하지 않고도 사용자를 로그인하고 데이터에 액세스할 수 있습니다. App Service 인증/권한 부여 모듈을 반드시 사용해야 하는 것은 아니지만 앱의 인증 및 권한 부여를 간소화하는 데 도움이 됩니다. 이 문서에서는 Azure AD(Azure Active Directory)를 ID 공급자로 사용하여 App Service 인증/권한 부여 모듈로 웹앱을 보호하는 방법을 보여줍니다.

인증/권한 부여 모듈은 Azure Portal 및 앱 설정을 통해 사용하도록 설정하고 구성할 수 있습니다. SDK 및 특정 언어가 필요하거나 애플리케이션 코드를 변경할 필요가 없습니다. Azure AD, Microsoft 계정, Facebook, Google 및 Twitter를 비롯한 다양한 ID 공급자가 지원됩니다. 인증/권한 부여 모듈을 사용하도록 설정하면 모든 수신 HTTP 요청이 이 모듈을 통과한 후 앱 코드에 의해 처리됩니다. 자세히 알아보려면 [Azure App Service에서 인증 및 권한 부여](overview-authentication-authorization.md)를 참조하세요.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
>
> * 웹앱에 대한 인증을 구성합니다.
> * 웹앱에 액세스할 수 있는 사람을 조직 내 사용자로 제한합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>App Service에서 웹앱 만들기 및 게시

이 자습서에서는 App Service에 배포된 웹앱이 필요합니다. 기존 웹앱을 사용할 수도 있고, [ASP.NET Core 빠른 시작](quickstart-dotnetcore.md)에 따라 새 웹앱을 만들고 App Service에 게시할 수도 있습니다.

기존 웹앱을 사용하든 새 웹앱을 만들든, 웹앱 이름과 웹앱이 배포되는 리소스 그룹의 이름을 기록해 두어야 합니다. 이 자습서 전체에서 이 이름이 필요합니다. 

## <a name="configure-authentication-and-authorization"></a>인증 및 권한 부여 구성

이제 App Service에서 실행되는 웹앱이 생겼습니다. 다음으로 웹앱에 인증 및 권한 부여를 사용하도록 설정합니다. Azure AD를 ID 공급자로 사용합니다. 자세한 내용은 [App Service 애플리케이션에 대해 Azure AD 인증 구성](configure-authentication-provider-aad.md)을 참조하세요.

[Azure Portal](https://portal.azure.com) 메뉴에서 **리소스 그룹** 을 선택하거나 검색하여 어느 페이지에서든 **리소스 그룹** 을 선택합니다.

**리소스 그룹** 에서 리소스 그룹을 찾아 선택합니다. **개요** 에서 앱의 관리 페이지를 선택합니다.

:::image type="content" alt-text="앱의 관리 페이지 선택을 보여주는 스크린샷." source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

앱의 왼쪽 메뉴에서 **인증** 을 선택한 다음, **ID 공급자 추가** 를 클릭합니다.

**ID 공급자 추가** 페이지에서 **ID 공급자** 로 **Microsoft** 를 선택하여 Microsoft 및 Azure AD ID에 로그인합니다.

**앱 등록** > **앱 등록 유형** 에 대해 **새 앱 등록 만들기** 를 선택합니다.

**앱 등록** > **지원 계정 유형** 에 대해 **현재 테넌트-단일 테넌트** 를 선택합니다.

**App Service 인증 설정** 섹션에서 **인증** 을 **인증 필요** 로 설정하고 **인증되지 않은 요청** 을 **HTTP 302 리디렉션 찾음: 웹 사이트에 권장됨** 으로 설정합니다.

**ID 공급자 추가** 페이지의 맨 아래에서 **추가** 를 클릭하여 웹앱에 대한 인증을 사용하도록 설정합니다.

:::image type="content" alt-text="인증 구성을 보여 주는 스크린샷" source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

이제 App Service 인증 및 권한 부여를 통해 보호되는 앱이 생겼습니다.

> [!NOTE]
> 다른 테넌트의 계정을 허용하려면 '인증' 블레이드에서 'ID 공급자'를 편집하여 '발급자 URL'을 'https://login.microsoftonline.com/common/v2.0 '으로 변경합니다.
>

## <a name="verify-limited-access-to-the-web-app"></a>액세스를 웹앱으로 제한

App Service 인증/권한 부여 모듈을 사용하도록 설정하면 Azure AD 테넌트에서 앱 등록이 생성됩니다. 앱 등록의 표시 이름은 웹앱과 동일합니다. 설정을 확인하려면 포털 메뉴에서 **Azure Active Directory** 를 선택하고 **앱 등록** 을 선택합니다. 이전에 생성된 앱 등록을 선택합니다. [개요]에서 **지원되는 계정 유형** 이 **내 조직만** 으로 설정되어 있는지 확인합니다.

:::image type="content" alt-text="액세스 확인을 보여주는 스크린샷." source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

앱에 액세스할 수 있는 사람이 조직 내 사용자로 제한되는지 확인하려면 브라우저를 incognito 또는 프라이빗 모드로 시작하고 `https://<app-name>.azurewebsites.net`으로 이동합니다. 인증되지 않은 사용자가 사이트에 액세스할 수 없는 것을 확인하는 보안 로그인 페이지로 이동해야 합니다. 사이트에 대한 액세스 권한을 얻으려면 조직 내 사용자로 로그인합니다. 새 브라우저를 시작하고 개인 계정으로 로그인하면 조직 외부의 사용자가 액세스할 수 없는 것을 확인할 수도 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 완료하고 웹앱 또는 관련 리소스가 더 이상 필요하지 않으면 [만든 리소스를 정리](scenario-secure-app-clean-up-resources.md)합니다.

## <a name="next-steps"></a>다음 단계

본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.

> [!div class="checklist"]
>
> * 웹앱에 대한 인증을 구성합니다.
> * 웹앱에 액세스할 수 있는 사람을 조직 내 사용자로 제한합니다.

> [!div class="nextstepaction"]
> [App Service에서 스토리지 액세스](scenario-secure-app-access-storage.md)
