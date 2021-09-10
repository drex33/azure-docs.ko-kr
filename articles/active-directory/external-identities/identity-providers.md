---
title: 외부 ID용 ID 공급자 - Azure AD
description: 외부 사용자와 공유하기 위한 기본 ID 공급자로 Azure AD를 사용하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca0592741018f054640b1d695f643695e4782518
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222145"
---
# <a name="identity-providers-for-external-identities"></a>외부 ID용 ID 공급자

‘ID 공급자’는 애플리케이션에 인증 서비스를 제공하는 동시에 ID 정보를 만들고 유지 관리합니다. 외부 사용자와 앱 및 리소스를 공유하는 경우 Azure AD는 공유를 위한 기본 ID 공급자입니다. 즉, 이미 Azure AD 또는 Microsoft 계정이 있는 외부 사용자를 초대하는 경우 사용자가 추가 구성 없이 자동으로 로그인할 수 있습니다.

외부 ID는 다양한 ID 공급자를 제공합니다.

- **Azure Active Directory 계정**: 게스트 사용자는 Azure AD 회사 또는 학교 계정을 사용하여 B2B 협업 초대를 사용하거나 등록 사용자 흐름을 완료할 수 있습니다. [Azure Active Directory](azure-ad-account.md)는 기본적으로 허용되는 ID 공급자 중 하나입니다. 이 ID 공급자를 사용자 흐름에 사용할 수 있도록 설정하는 데 추가 구성이 필요하지 않습니다.

- **Microsoft 계정**: 게스트 사용자는 자신의 개인 Microsoft 계정(MSA)을 사용하여 B2B 협업 초대를 교환할 수 있습니다. 셀프 서비스 가입 사용자 흐름을 설정할 때 [Microsoft 계정](microsoft-account.md)을 허용된 ID 공급자 중 하나로 추가할 수 있습니다. 이 ID 공급자를 사용자 흐름에 사용할 수 있도록 설정하는 데 추가 구성이 필요하지 않습니다.

- **메일 일회성 암호**: 초대장을 교환하거나 공유 리소스에 액세스할 때 게스트 사용자는 임시 코드를 요청할 수 있으며, 임시 코드는 해당 메일 주소로 전송됩니다. 그런 다음, 이 코드를 입력하여 로그인을 계속합니다. 이메일 일회용 암호 기능은 B2B 게스트 사용자를 다른 방법으로 인증할 수 없는 경우 인증합니다. 셀프 서비스 가입 사용자 흐름을 설정할 때 **메일 일회성 암호** 를 허용된 ID 공급자 중 하나로 추가할 수 있습니다. 일부 설정이 필요합니다. [이메일 일회용 암호 인증](one-time-passcode.md)을 참조하세요.

- **Google**: Google 페더레이션을 통해 외부 사용자는 자신의 Gmail 계정으로 앱에 로그인하여 초대를 사용할 수 있습니다. 셀프 서비스 등록 사용자 흐름에서 Google 페더레이션을 사용할 수도 있습니다. [Google을 ID 공급자로 추가](google-federation.md)하는 방법을 참조하세요.
   > [!IMPORTANT]
   >
   > - **2021년 7월 12일부터**, Azure AD B2B 고객이 사용자 지정 또는 기간 업무 애플리케이션에 대한 셀프 서비스 등록과 함께 사용하기 위해 새로운 Google 통합을 설정하는 경우 인증이 시스템 웹 보기로 이동될 때까지 Google ID를 사용한 인증이 작동하지 않습니다. [자세히 알아보기](google-federation.md#deprecation-of-web-view-sign-in-support).
   > - **2021년 9월 30부터** Google은 [포함된 웹 보기 로그인 지원을 중단](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)합니다. 앱이 포함된 웹 보기로 사용자를 인증하고 [외부 사용자 초대](google-federation.md) 또는 셀프 서비스 등록을 위해 [Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) 또는 Azure AD B2B와 함께 Google 페더레이션을 사용하는 경우 Google Gmail 사용자는 인증할 수 없습니다. [자세히 알아봅니다](google-federation.md#deprecation-of-web-view-sign-in-support).


- **Facebook**: 앱을 빌드할 때 사용자가 자신의 Facebook 계정을 사용하여 앱에 등록할 수 있도록 셀프 서비스 등록을 구성하고 Facebook 페더레이션을 사용하도록 설정할 수 있습니다. Facebook은 셀프 서비스 등록 사용자 흐름에만 사용할 수 있으며 사용자가 초대를 사용할 때 로그인 옵션으로 사용할 수 없습니다. [Facebook을 ID 공급자로 추가](facebook-federation.md)하는 방법을 참조하세요.

- **SAML/WS-Fed ID 공급자 페더레이션**: SAML 또는 WS-Fed 프로토콜을 지원하는 외부 IdP와 페더레이션을 설정할 수도 있습니다. SAML/WS-Fed IdP 페더레이션을 통해 외부 사용자는 기존 소셜 또는 엔터프라이즈 계정으로 앱에 로그인하여 초대를 사용할 수 있습니다. [SAML/WS-Fed IdP 페더레이션을 설정](direct-federation.md)하는 방법을 참조하세요.
   > [!NOTE]
   > 페더레이션된 SAML/WS-Fed IdP는 셀프 서비스 등록 사용자 흐름에서 사용할 수 없습니다.

## <a name="adding-social-identity-providers"></a>소셜 ID 공급자 추가

셀프 서비스 등록에는 기본적으로 Azure AD가 사용하도록 설정되므로 사용자는 언제든지 Azure AD 계정을 사용하여 등록할 수 있습니다. 하지만 Google 또는 Facebook과 같은 소셜 ID 공급자를 비롯한 다른 ID 공급자를 사용하도록 설정할 수 있습니다. Azure AD 테넌트에서 소셜 ID 공급자를 설정하려면 ID 공급자에서 애플리케이션을 만들고 자격 증명을 구성합니다. 클라이언트 또는 앱 ID와 클라이언트 또는 앱 비밀을 가져올 수 있습니다. 그런 다음, Azure AD 테넌트에 추가할 수 있습니다.

Azure AD 테넌트에 ID 공급자를 추가하면 다음을 수행합니다.

- 외부 사용자를 조직의 앱 또는 리소스에 초대하는 경우 외부 사용자가 해당 ID 공급자와 함께 자신의 계정을 사용하여 로그인할 수 있습니다.
- 앱에 대해 [셀프 서비스 가입](self-service-sign-up-overview.md)을 사용하도록 설정하면 외부 사용자는 추가한 ID 공급자와 함께 자신의 계정을 사용하여 앱에 가입할 수 있습니다. 가입 페이지에서 이용할 수 있는 소셜 ID 공급자 옵션을 선택할 수 있습니다.

   ![Google 및 Facebook 옵션을 사용하여 로그인 화면을 보여 주는 스크린샷](media/identity-providers/sign-in-with-social-identity.png)

최적의 로그인 환경을 위해 가능한 경우 ID 공급자와 페더레이션하여 초대된 게스트가 앱에 액세스할 때 원활한 로그인 환경을 제공할 수 있도록 합니다.  

## <a name="next-steps"></a>다음 단계

애플리케이션에 로그인하기 위해 ID 공급자를 추가하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [이메일 일회용 암호 인증 추가](one-time-passcode.md)
- 허용되는 소셜 ID 공급자로 [Google 추가](google-federation.md)
- 허용되는 소셜 ID 공급자로 [Facebook 추가](facebook-federation.md)
- ID 공급자가 SAML 2.0 또는 WS-Fed 프로토콜을 지원하는 모든 조직과 [SAML/WS-Fed IdP 페더레이션을 설정](direct-federation.md)합니다. SAML/WS-Fed IdP 페더레이션은 셀프 서비스 등록 사용자 흐름에 대한 옵션이 아닙니다.
