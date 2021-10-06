---
title: '빠른 시작: Microsoft ID 플랫폼에 앱 등록 | Azure'
description: 이 빠른 시작에서는 애플리케이션을 Microsoft ID 플랫폼에 등록하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 06/14/2021
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperf-fy21q1, contperf-fy21q2, contperf-fy21q4
ms.openlocfilehash: c608856e6238844638e63c3a719b3d534b98d33b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615323"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>빠른 시작: Microsoft ID 플랫폼에 애플리케이션 등록

이 빠른 시작에서는 Microsoft ID 플랫폼에서 애플리케이션과 해당 사용자에게 인증 및 권한 부여 서비스를 제공할 수 있도록 Azure Portal에서 앱을 등록합니다.

Microsoft ID 플랫폼은 등록된 애플리케이션에 대해서만 IAM(ID 및 액세스 관리)을 수행합니다. 웹 또는 모바일 앱과 같은 클라이언트 애플리케이션이든, 클라이언트 앱을 지원하는 웹 API이든 간에 등록하는 경우 애플리케이션 및 ID 공급자인 Microsoft ID 플랫폼 간에 신뢰 관계가 설정됩니다.

> [!TIP]
> Azure AD B2C용 애플리케이션을 등록하려면 [자습서: Azure AD B2C에서 웹 애플리케이션 등록](../../active-directory-b2c/tutorial-register-applications.md)의 단계를 수행합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure 계정에는 Azure AD(Azure Active Directory)에서 애플리케이션을 관리할 수 있는 권한이 있어야 합니다. 다음 Azure AD 역할에는 필요한 권한이 포함됩니다.
  - [애플리케이션 관리자](../roles/permissions-reference.md#application-administrator)
  - [애플리케이션 개발자](../roles/permissions-reference.md#application-developer)
  - [클라우드 애플리케이션 관리자](../roles/permissions-reference.md#cloud-application-administrator)
- [테넌트 설정](quickstart-create-new-tenant.md) 빠른 시작 완료

## <a name="register-an-application"></a>애플리케이션 등록

애플리케이션을 등록하면 앱과 Microsoft ID 플랫폼 간에 신뢰 관계가 설정됩니다. 신뢰는 단방향입니다. 즉, 앱은 Microsoft ID 플랫폼을 신뢰하지만, 반대로는 신뢰하지 않습니다.

다음 단계에 따라 앱 등록을 만듭니다.

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>에 로그인합니다.
1. 여러 테넌트에 액세스할 수 있는 경우 위쪽 메뉴의 **디렉터리 + 구독** 필터 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::를 사용하여 애플리케이션을 등록하려는 테넌트로 전환합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **관리** 아래에서 **앱 등록** > **새 등록** 을 선택합니다.
1. 애플리케이션의 표시 **이름** 을 입력합니다. 애플리케이션 사용자는 앱을 사용할 때 표시 이름(예: 로그인 중)을 볼 수 있습니다.
   언제든지 표시 이름을 변경할 수 있으며 여러 앱 등록에서 같은 이름을 공유할 수 있습니다. 앱 등록 시 자동으로 생성된 애플리케이션(클라이언트) ID는 표시 이름이 아니라 ID 플랫폼 내에서 앱을 고유하게 식별합니다.
1. 애플리케이션을 사용할 수 있는 사용자(_로그인 대상_ 이라고도 함)를 지정합니다.

   | 지원되는 계정 유형                                                      | Description                                                                                                                                                                                                                                                                                                                                                                                 |
   | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **이 조직 디렉터리의 계정만**                           | _사용자_ 의 테넌트에 있는 사용자(또는 게스트)만 사용할 수 있도록 애플리케이션을 빌드하는 경우 이 옵션을 선택합니다.<br><br>_LOB(기간 업무)_ 애플리케이션이라고도 하는 이 앱은 Microsoft ID 플랫폼의 _단일 테넌트_ 애플리케이션입니다.                                                                                                                                          |
   | **모든 조직 디렉터리의 계정**                                 | _모든_ Azure AD(Azure Active Directory) 테넌트의 사용자가 애플리케이션을 사용할 수 있도록 하려면 이 옵션을 선택합니다. 예를 들어 여러 조직에 제공하려는 SaaS(Software-as-a-Service) 애플리케이션을 빌드하는 경우에 이 옵션이 적합합니다.<br><br>이 유형의 앱은 Microsoft ID 플랫폼에서 _다중 테넌트_ 애플리케이션이라고 합니다. |
   | **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정** | 가장 폭넓은 고객을 대상으로 하려면 이 옵션을 사용합니다.<br><br>이 옵션을 선택하면 개인 _Microsoft 계정_ 이 있는 사용자도 지원할 수 있는 _다중 테넌트_ 애플리케이션을 등록하게 됩니다.                                                                                                                                                                               |
   | **개인 Microsoft 계정**                                              | 개인 Microsoft 계정이 있는 사용자만을 위한 애플리케이션을 빌드하는 경우 이 옵션을 선택합니다. 개인 Microsoft 계정에는 Skype, Xbox, Live 및 Hotmail 계정이 포함됩니다.                                                                                                                                                                                                      |

1. **리디렉션 URI(선택 사항)** 에는 아무것도 입력하지 않습니다. 리디렉션 URI는 다음 섹션에서 구성합니다.
1. **등록** 을 선택하여 초기 앱 등록을 완료합니다.

   :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="애플리케이션 등록 창을 보여 주는 웹 브라우저의 Azure Portal에 대한 스크린샷":::

등록이 완료되면 Azure Portal에 앱 등록의 **개요** 창이 표시됩니다. **애플리케이션(클라이언트) ID** 가 표시됩니다. _클라이언트 ID_ 라고도 하는 이 값은 Microsoft ID 플랫폼에서 애플리케이션을 고유하게 식별합니다.

> [!IMPORTANT]
> 새 앱 등록은 기본적으로 사용자에게 표시되지 않습니다. 사용자가 [내 앱 페이지](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)에서 앱을 볼 준비가 되면 사용할 수 있습니다. 앱을 사용하려면 Azure Portal에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션** 으로 이동하여 앱을 선택합니다. 그런 다음, **속성** 페이지에서 **사용자에게 표시하시겠습니까?** 를 예로 전환합니다.

애플리케이션의 코드 또는 더 일반적으로 애플리케이션에서 사용되는 인증 라이브러리도 클라이언트 ID를 사용합니다. ID는 ID 플랫폼에서 받는 보안 토큰의 유효성을 검사하는 과정의 일부로 사용됩니다.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="앱 등록의 개요 창을 보여 주는 웹 브라우저의 Azure Portal에 대한 스크린샷":::

## <a name="add-a-redirect-uri"></a>리디렉션 URI 추가

_리디렉션 URI_ 는 Microsoft ID 플랫폼에서 사용자의 클라이언트를 리디렉션하고 인증 후에 보안 토큰을 보내는 위치입니다.

예를 들어 프로덕션 웹 애플리케이션에서 리디렉션 URI는 `https://contoso.com/auth-response`와 같이 앱이 실행되는 퍼블릭 엔드포인트인 경우가 많습니다. 일반적으로 개발 중에 앱을 로컬로 실행하는 엔드포인트(예: `https://127.0.0.1/auth-response` 또는 `http://localhost/auth-response`)를 추가합니다.

[플랫폼 설정](#configure-platform-settings)을 구성하여 등록된 애플리케이션에 대한 리디렉션 URI를 추가하고 수정합니다.

### <a name="configure-platform-settings"></a>플랫폼 설정 구성

리디렉션 URI를 포함하여 각 애플리케이션 유형에 대한 설정은 Azure Portal의 **플랫폼 구성** 에서 구성됩니다. **웹** 및 **단일 페이지 애플리케이션** 과 같은 일부 플랫폼에서는 리디렉션 URI를 수동으로 지정해야 합니다. 모바일 및 데스크톱과 같은 다른 플랫폼의 경우 다른 설정을 구성할 때 생성된 리디렉션 URI에서 선택할 수 있습니다.

대상으로 하는 플랫폼 또는 디바이스에 따라 애플리케이션 설정을 구성하려면 다음을 수행합니다.

1. Azure Portal의 **앱 등록** 에서 애플리케이션을 선택합니다.
1. **관리** 에서 **인증** 을 선택합니다.
1. **플랫폼 구성** 에서 **플랫폼 추가** 를 선택합니다.
1. **플랫폼 구성** 아래에서 애플리케이션 유형(플랫폼)에 대한 타일을 선택하여 설정을 구성합니다.

   :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Azure Portal의 플랫폼 구성 창에 대한 스크린샷" border="false":::

   | 플랫폼                            | 구성 설정                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
   | ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Web**                             | 앱에 대한 **리디렉션 URI** 를 입력합니다. 이 URI는 Microsoft ID 플랫폼에서 사용자의 클라이언트를 리디렉션하고 인증 후에 보안 토큰을 보내는 위치입니다.<br/><br/>서버에서 실행되는 표준 웹 애플리케이션의 경우 이 플랫폼을 선택합니다.                                                                                                                                                                                                                                                                   |
   | **단일 페이지 애플리케이션**         | 앱에 대한 **리디렉션 URI** 를 입력합니다. 이 URI는 Microsoft ID 플랫폼에서 사용자의 클라이언트를 리디렉션하고 인증 후에 보안 토큰을 보내는 위치입니다.<br/><br/>JavaScript 또는 프레임워크(예: Angular, Vue.js, React.js 또는 Blazor WebAssembly)를 사용하여 클라이언트 쪽 웹앱을 빌드하는 경우 이 플랫폼을 선택합니다.                                                                                                                                                                                    |
   | **iOS/macOS**                     | 앱 **번들 ID** 를 입력합니다. **빌드 설정** 또는 _Info.plist_ 의 Xcode에서 찾을 수 있습니다.<br/><br/>**번들 ID** 를 지정하면 리디렉션 URI가 생성됩니다.                                                                                                                                                                                                                                                                                                                                                              |
   | **Android**                         | 앱 **패키지 이름** 을 입력합니다. _AndroidManifest.xml_ 파일에서 찾을 수 있습니다. 또한 **서명 해시** 를 생성하여 입력합니다.<br/><br/>이러한 설정을 지정하면 리디렉션 URI가 생성됩니다.                                                                                                                                                                                                                                                                                                                            |
   | **모바일 및 데스크톱 애플리케이션** | **제안된 리디렉션 URI** 중 하나를 선택합니다. 또는 **사용자 지정 리디렉션 URI** 를 지정합니다.<br/><br/>임베디드 브라우저를 사용하는 데스크톱 애플리케이션의 경우 다음을 권장합니다.<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>시스템 브라우저를 사용하는 데스크톱 애플리케이션의 경우 다음을 권장합니다.<br/>`http://localhost`<br/><br/>최신 MSAL(Microsoft 인증 라이브러리) 또는 broker를 사용하지 않는 모바일 애플리케이션의 경우 이 플랫폼을 선택합니다. 데스크톱 애플리케이션의 경우에도 이 플랫폼을 선택합니다. |

1. **구성** 을 선택하여 플랫폼 구성을 완료합니다.

### <a name="redirect-uri-restrictions"></a>리디렉션 URI에 대한 제한 사항

앱 등록에 추가하는 리디렉션 URI의 형식에는 몇 가지 제한 사항이 있습니다. 이러한 제한 사항에 대한 자세한 내용은 [리디렉션 URI(회신 URL) 제한 사항](reply-url.md)을 참조하세요.

## <a name="add-credentials"></a>자격 증명 추가

자격 증명은 웹 API에 액세스하는 [기밀 클라이언트 애플리케이션](msal-client-applications.md)에서 사용됩니다. 기밀 클라이언트의 예로 웹앱, 다른 웹 API 또는 서비스 유형 및 디먼 유형 애플리케이션이 있습니다. 자격 증명을 사용하면 애플리케이션에서 자체적으로 인증할 수 있으므로 런타임에 사용자의 상호 작용이 필요하지 않습니다.

인증서와 클라이언트 암호(문자열)를 모두 자격 증명으로 기밀 클라이언트 앱 등록에 추가할 수 있습니다.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="앱 등록에서 인증서 및 비밀 창을 보여 주는 Azure Portal의 스크린샷":::

### <a name="add-a-certificate"></a>인증서 추가

_퍼블릭 키_ 라고도 하는 인증서는 클라이언트 암호보다 더 안전한 것으로 간주되기 때문에 권장되는 자격 증명 유형입니다. 애플리케이션에서 인증 방법으로 인증서를 사용하는 방법에 대한 자세한 내용은 [Microsoft ID 플랫폼 애플리케이션 인증 인증서 자격 증명](active-directory-certificate-credentials.md)을 참조하세요.

1. Azure Portal의 **앱 등록** 에서 애플리케이션을 선택합니다.
1. **인증서 및 비밀** > **인증서 업로드** 를 차례로 선택합니다.
1. 업로드하려는 파일을 선택합니다. _.cer_, _.pem_, _.crt_ 파일 형식 중 하나여야 합니다.
1. **추가** 를 선택합니다.

### <a name="add-a-client-secret"></a>클라이언트 암호 추가

_애플리케이션 암호_ 라고도 하는 클라이언트 암호는 앱에서 ID 자체에 대한 인증서 대신 사용할 수 있는 문자열 값입니다.

클라이언트 암호는 인증서 자격 증명보다 덜 안전한 것으로 간주됩니다. 애플리케이션 개발자는 사용 편리성 때문에 로컬 앱 개발 중에 클라이언트 암호를 사용하기도 합니다. 그러나 프로덕션 환경에서는 실행 중인 모든 애플리케이션에 대해 인증서 자격 증명을 사용해야 합니다.

1. Azure Portal의 **앱 등록** 에서 애플리케이션을 선택합니다.
1. **인증서 및 비밀** > **새 클라이언트 암호** 를 차례로 선택합니다.
1. 클라이언트 비밀에 대한 설명을 추가합니다.
1. 암호에 대해 만료를 선택하거나 사용자 지정 수명을 지정합니다.
    - 클라이언트 암호 수명은 2년(24개월) 이하로 제한됩니다. 사용자 지정 수명은 24개월 이상으로 지정할 수 없습니다.
    - 12개월 미만의 만료 값을 설정하는 것이 좋습니다.
1. **추가** 를 선택합니다.
1. 클라이언트 애플리케이션 코드에서 사용할 _비밀 값을 기록_ 합니다. 이 비밀 값은 이 페이지에서 나가면 _다시 표시되지 않습니다_.

애플리케이션 보안 권장 사항에 대해서는 [Microsoft ID 플랫폼 모범 사례 및 권장 사항](identity-platform-integration-checklist.md#security)을 참조하세요.

## <a name="next-steps"></a>다음 단계

클라이언트 애플리케이션은 일반적으로 웹 API의 리소스에 액세스해야 합니다. Microsoft ID 플랫폼을 사용하여 클라이언트 애플리케이션을 보호할 수 있습니다. 또한 이 플랫폼을 사용하여 웹 API에 대한 범위 기반 권한 기반 액세스 권한을 부여할 수도 있습니다.

웹 API에 대한 다른 앱 등록을 만들고 해당 범위를 공개하려면 시리즈의 다음 빠른 시작으로 이동하세요.

> [!div class="nextstepaction"]
> [웹 API를 공개하도록 애플리케이션 구성](quickstart-configure-app-expose-web-apis.md)