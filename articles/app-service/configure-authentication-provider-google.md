---
title: Google 인증 구성
description: App Service 또는 Azure Functions 앱의 ID 공급자로 Google 인증을 구성하는 방법에 대해 알아봅니다.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 46ccfd04722cb9333a8f6e546505ed1e10f657af
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537235"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Google 로그인을 사용하도록 App Service 또는 Azure Functions 앱 구성

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 항목에서는 Google을 인증 공급자로 사용하도록 Azure App Service 또는 Azure Functions을 구성하는 방법을 보여줍니다.

이 항목의 절차를 완료하려면 검증된 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)으로 이동하세요.

## <a name="register-your-application-with-google"></a><a name="register"> </a>Google을 사용하여 애플리케이션 등록

1. [서버 쪽 앱용 Google 로그인](https://developers.google.com/identity/sign-in/web/server-side-flow)의 Google 설명서를 따라 클라이언트 ID 및 클라이언트 암호를 만듭니다. 코드를 변경할 필요가 없습니다. 다음 정보를 사용합니다:
    - **권한이 부여된 JavaScript 원본** 의 경우 *\<app-name>* 에서 앱 이름과 함께 `https://<app-name>.azurewebsites.net`을 사용합니다.
    - **권한이 부여된 리디렉션 URI** 의 경우 `https://<app-name>.azurewebsites.net/.auth/login/google/callback`을 사용합니다.
1. 앱 ID 및 앱 암호 값을 복사합니다.

    > [!IMPORTANT]
    > 앱 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 클라이언트 애플리케이션 내에 배포하지 마세요.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>애플리케이션에 Google 정보 추가

1. [Azure Portal]에 로그인하고 앱으로 이동합니다.
1. 왼쪽의 메뉴에서 **인증** 을 선택합니다. **ID 공급자 추가** 를 클릭합니다.
1. ID 공급자 드롭다운에서 **Google** 을 선택합니다. 앞에서 얻은 앱 ID 및 앱 암호 값을 붙여 넣습니다.

    암호는 `GOOGLE_PROVIDER_AUTHENTICATION_SECRET`으로 명명된 슬롯 고정이란 [애플리케이션 설정](./configure-common.md#configure-app-settings)으로 저장됩니다. Azure Key Vault에서 암호를 관리하려는 경우 나중에 [Key Vault 참조](./app-service-key-vault-references.md)를 사용하도록 해당 설정을 업데이트할 수 있습니다.

1. 이것이 애플리케이션에 대해 구성된 첫 번째 ID 공급자인 경우 **App Service 인증 설정** 섹션도 표시됩니다. 그렇지 않으면 다음 단계로 이동할 수 있습니다.
    
    이러한 옵션은 애플리케이션이 인증되지 않은 요청에 응답하는 방법을 결정하며 기본 선택 항목은 이 새 공급자를 사용하여 로그인하도록 모든 요청을 리디렉션합니다. 지금 이 동작의 사용자 지정을 변경하거나, 나중에 기본 **인증** 화면에서 **인증 설정** 옆의 **편집** 을 선택하여 이 설정을 조정할 수 있습니다. 이러한 옵션에 대한 자세한 정보는 [인증 흐름](overview-authentication-authorization.md#authentication-flow)을 참조하세요.

1. **추가** 를 클릭합니다.

> [참고] 범위를 추가하는 경우: 공급자의 등록 포털에서 애플리케이션에 어떤 권한이 있는지 정의할 수 있습니다. 앱은 로그인 시 이러한 권한을 활용하는 범위를 요청할 수 있습니다.

이제 앱에서 Google을 인증에 사용할 준비가 되었습니다. 공급자는 **인증** 화면에 나열됩니다. 여기에서 공급자 구성을 편집하거나 삭제할 수 있습니다.

## <a name="next-steps"></a><a name="related-content"> </a>다음 단계

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

