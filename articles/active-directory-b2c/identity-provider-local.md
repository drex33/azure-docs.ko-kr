---
title: Azure AD B2C 로컬 계정 ID 공급자 설정
titleSuffix: Azure AD B2C
description: 사용자가 Azure Active Directory B2C 테넌트에서 등록 또는 로그인하는 데 사용할 수 있는 ID 유형(이메일, 사용자 이름, 전화 번호)을 정의합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/22/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1de783a3e6696e9ca8a8f618ce3744a91e774692
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112198857"
---
# <a name="set-up-the-local-account-identity-provider"></a>로컬 계정 ID 공급자 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

이 문서에서는 Azure AD B2C 로컬 계정에 대한 로그인 방법을 결정하는 방법을 설명합니다. 로컬 계정은 사용자가 애플리케이션에 등록하거나 관리자가 계정을 만들 때 Azure AD B2C 디렉터리에 생성된 계정을 참조합니다. 사용자 이름 및 암호는 로컬로 저장되며, Azure AD B2C는 로컬 계정에 대한 ID 공급자 역할을 합니다.

로컬 계정에 몇 가지 로그인 방법을 사용할 수 있습니다.

- **이메일**: 사용자가 이메일 주소 및 암호를 사용하여 앱에 등록하고 로그인할 수 있습니다. 이메일 등록은 로컬 계정 ID 공급자 설정에서 기본적으로 사용하도록 설정됩니다.
- **사용자 이름**: 사용자는 사용자 이름 및 암호를 사용하여 등록하고 로그인할 수 있습니다.
- **전화(또는 “암호 없는 인증”)** : 사용자가 기본 로그인 식별자로 전화 번호를 사용하여 앱에 등록하고 로그인할 수 있습니다. 암호를 만들 필요가 없습니다. 일회용 암호는 SMS 문자 메시지를 통해 사용자에게 전송됩니다.
- **전화 또는 이메일**: 사용자는 전화 번호 또는 이메일 주소를 입력하여 등록하거나 로그인할 수 있습니다. 사용자 입력에 따라 Azure AD B2C가 사용자를 등록 또는 로그인 페이지의 해당 흐름으로 이동합니다.
- **전화 복구**: 전화 등록 또는 로그인을 사용하도록 설정한 경우 전화 복구를 사용하면 전화기가 없을 때 계정을 복구하는 데 사용할 수 있는 이메일 주소를 제공할 수 있습니다.

이러한 방법에 대해 자세히 알아보려면 [로그인 옵션](sign-in-options.md)을 참조하세요. 

사용자 ID가 Facebook 또는 Google과 같은 페더레이션 ID 공급자에 의해 관리되는 소셜 또는 엔터프라이즈 ID에 대해 설정을 구성하려면 [ID 공급자 추가](add-identity-provider.md)를 참조하세요.

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>로컬 계정 ID 공급자 설정 구성


Azure AD B2C **ID 공급자** 목록에서 **로컬 계정** 공급자를 구성하여 테넌트에서 사용할 수 있도록 로컬 계정 로그인 방법(이메일, 사용자 이름 또는 전화 번호)을 선택할 수 있습니다. 그런 다음, 사용자 흐름을 설정하는 경우 테넌트 전체를 사용하도록 설정한 로컬 계정 로그인 방법 중 하나를 선택할 수 있습니다. 사용자 흐름에 대해 하나의 로컬 계정 로그인 방법만 선택할 수 있지만 각 사용자 흐름에 대해 다른 옵션을 선택할 수 있습니다.

테넌트 수준에서 로컬 계정 로그인 옵션을 설정하려면 다음을 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, Azure AD 테넌트가 포함된 디렉터리를 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.
1. **관리** 아래에서 **ID 공급자** 를 선택합니다.
1. ID 공급자 목록에서 **로컬 계정** 을 선택합니다.
1. **로컬 IDP 구성** 페이지에서 Azure AD B2C 테넌트에 사용자 흐름을 사용하도록 설정하려는 하나 이상의 ID 유형을 선택합니다. 여기에서 옵션을 선택하면 테넌트 전체를 사용할 수 있습니다. 사용자 흐름을 만들거나 수정할 때 여기에서 사용하도록 설정하는 옵션을 선택할 수 있습니다.

   - **전화**: 가입 시 확인되고 사용자 ID로 사용될 전화 번호를 묻는 메시지가 표시됩니다.
   - **사용자 이름**: 사용자가 고유한 사용자 ID를 만들 수 있습니다. 이메일 주소는 사용자로부터 수집되어 확인됩니다.
   - **이메일**: 가입 시 확인되고 사용자 ID로 사용될 이메일 주소를 묻는 메시지가 표시됩니다.
1. **저장** 을 선택합니다.

## <a name="configure-your-user-flow"></a>사용자 흐름 구성

1. Azure Portal 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다.
1. **정책** 에서 **사용자 흐름(정책)** 을 선택합니다.
1. 등록 및 로그인 환경을 구성하려는 사용자 흐름을 선택합니다.
1. **ID 공급자** 를 선택합니다.
1. **로컬 계정** 에서 **이메일 등록**, **사용자 ID 등록**, **전화 등록**, **전화/이메일 등록** 또는 **없음** 중 하나를 선택합니다.

### <a name="enable-the-recovery-email-prompt"></a>복구 메일 프롬프트를 사용하도록 설정

**전화 등록**, **전화/이메일 등록** 옵션을 선택하는 경우 복구 메일 프롬프트를 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. Azure AD B2C의 **정책** 에서 **사용자 흐름** 을 선택합니다.
1. 목록에서 사용자 흐름을 선택합니다.
1. **설정** 아래에서 **속성** 을 선택합니다.
1. **전화 번호 등록 및 로그인에 대한 복구 메일 프롬프트를 사용하도록 설정(미리 보기)** 에서 다음을 선택합니다.
   - **On** 은 가입 및 로그인 시 복구 메일 프롬프트를 표시합니다.
   - **Off** 는 복구 메일 프롬프트를 숨깁니다.
1. **저장** 을 선택합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>시작 팩 가져오기

사용자 지정 정책은 사용자 경험을 정의하기 위해 Azure AD B2C 테넌트에 업로드하는 XML 파일 집합입니다. 몇 가지 미리 작성된 정책이 포함된 시작 팩이 제공됩니다. 관련 스타터 팩을 다운로드합니다. 

- [이메일 로그인](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [사용자 이름 로그인](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [전화 로그인](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless) [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) 신뢰 당사자 정책을 선택합니다. 
- [전화 또는 이메일 로그인](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) 신뢰 당사자 정책을 선택합니다.

시작 팩을 다운로드한 후

1. 각 파일에서 `yourtenant` 문자열을 Azure AD B2C 테넌트의 이름으로 바꿉니다. 예를 들어 B2C 테넌트의 이름이 *contosob2c* 인 경우 모든 `yourtenant.onmicrosoft.com` 인스턴스는 `contosob2c.onmicrosoft.com`이 됩니다.

1. [Azure Active Directory B2C에서 사용자 지정 정책 시작](tutorial-create-user-flows.md?pivots=b2c-custom-policy)의 [사용자 지정 정책에 애플리케이션 ID 추가](tutorial-create-user-flows.md?pivots=b2c-custom-policy#add-application-ids-to-the-custom-policy) 섹션에 설명된 단계를 수행합니다. 예를 들어 필수 구성 요소 *IdentityExperienceFramework* 및 *ProxyIdentityExperienceFramework* 를 완료할 때 등록한 두 애플리케이션의 **애플리케이션(클라이언트) ID** 로 `/phone-number-passwordless/`**`Phone_Email_Base.xml`** 을 업데이트합니다.
1. 정책 파일을 업로드합니다.

::: zone-end

## <a name="next-steps"></a>다음 단계

- [외부 ID 공급자 추가](add-identity-provider.md)
- [사용자 흐름 만들기](tutorial-create-user-flows.md)
