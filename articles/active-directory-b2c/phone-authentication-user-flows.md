---
title: 로컬 계정 ID 공급자 설정하기
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 테넌트에서 사용자 흐름을 설정하는 경우 로컬 계정 인증에 사용할 수 있는 ID 유형(메일, 사용자 이름, 전화 번호)을 정의합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b0d0c77ffbf6e8c8493abe2f9356aaa0e171f1f2
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967297"
---
# <a name="set-up-phone-sign-up-and-sign-in-for-user-flows"></a>사용자 흐름에 대해 전화 가입 및 로그인 설정

메일 및 사용자 이름 외에도 전화 가입 및 로컬 계정 ID 공급자에 로그인을 추가하여 전화 번호를 테넌트 전체의 가입 옵션으로 사용하도록 설정할 수 있습니다. 로컬 계정에 대한 전화 가입 및 로그인을 사용하도록 설정한 후 사용자 흐름에 전화 가입을 추가할 수 있습니다.

사용자 흐름에서 전화 가입 및 로그인을 설정하려면 다음 단계를 수행합니다.

- 사용자의 ID로 전화 번호를 수락하려면 로컬 계정 ID 공급자에서 [전화로 가입하고 로그인 테넌트 전체를 구성](#configure-phone-sign-up-and-sign-in-tenant-wide)합니다. 

- 사용자가 전화 번호를 사용하여 애플리케이션에 가입할 수 있도록 하려면 [사용자 흐름에 전화 번호를 추가](#add-phone-sign-up-to-a-user-flow)합니다.

- 전화가 없는 사용자가 계정을 복구하는 데 사용하는 메일을 지정하도록 하려면 [복구 메일 프롬프트를 사용하도록 설정(미리 보기)](#enable-the-recovery-email-prompt-preview)합니다.

- 가입 또는 로그인 흐름 중에 사용자에게 [동의 정보를 표시합니다](#enable-consent-information). 기본 동의 정보를 표시하거나 사용자 고유의 동의 정보를 사용자 지정할 수 있습니다.

전화 가입으로 사용자 흐름을 구성하는 경우 MFA(다단계 인증)는 기본적으로 사용되지 않습니다. 전화 가입을 통해 사용자 흐름에서 MFA를 사용할 수 있으나, 여기에는 전화 번호가 기본 식별자로 사용되기 때문에 일회용 메일 암호가 두 번째 인증 요소에 사용할 수 있는 유일한 옵션이 됩니다.

## <a name="configure-phone-sign-up-and-sign-in-tenant-wide"></a>전화 가입 및 로그인 테넌트 전체 구성

메일 가입은 로컬 계정 ID 공급자 설정에서 기본적으로 사용하도록 설정됩니다. 메일 가입, 사용자 이름 또는 전화 번호를 선택하거나 선택 취소하여 테넌트에서 지원할 ID 유형을 변경할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, Azure AD 테넌트가 포함된 디렉터리를 선택합니다.

3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.

4. **관리** 아래에서 **ID 공급자** 를 선택합니다.

5. ID 공급자 목록에서 **로컬 계정** 을 선택합니다.

   ![ID 공급자 로컬 계정 선택](media/phone-authentication-user-flows/identity-provider-local-account.png)

1. **로컬 IDP 구성** 페이지에서 소비자가 Azure AD B2C 테넌트의 로컬 계정을 만드는 데 사용할 수 있는 허용 가능한 ID 유형 중 하나로 **전화** 가 선택되어 있는지 확인합니다.

   ![허용되는 ID 유형 선택](media/phone-authentication-user-flows/configure-local-idp.png)

1. **저장** 을 선택합니다.

## <a name="add-phone-sign-up-to-a-user-flow"></a>사용자 흐름에 전화 가입 추가

로컬 계정에 대한 ID 옵션으로 전화 가입을 추가한 후 사용자 흐름에 추가하는 것은 최신 **권장** 사용자 흐름 버전인 경우에만 가능합니다. 다음은 새 사용자 흐름에 전화 가입을 추가하는 방법을 보여 주는 예제입니다. 그러나 기존 권장 버전 사용자 흐름에 전화 가입을 추가할 수도 있습니다(**사용자 흐름** > *사용자 흐름 이름* > **ID 공급자** > **로컬 계정 전화 가입** 을 선택). 

새 사용자 흐름에 전화 가입을 추가하는 방법을 보여 주는 예제입니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

    ![B2C 테넌트, 디렉터리 및 구독 창, Azure Portal](./media/phone-authentication-user-flows/directory-subscription-pane.png)

3. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
4. **정책** 아래에서 **사용자 흐름** 을 선택한 다음, **새 사용자 흐름** 을 선택합니다.

    ![새 사용자 흐름 단추가 강조 표시된 포털의 사용자 흐름 페이지](./media/phone-authentication-user-flows/sign-up-sign-in-user-flow.png)

5. **사용자 흐름 만들기** 탭에서 **가입 및 로그인** 사용자 흐름을 선택합니다.

    ![가입 및 로그인 흐름이 강조 표시된 사용자 흐름 페이지 선택](./media/phone-authentication-user-flows/select-user-flow-type.png)

6. **버전 선택** 아래에서 **추천** 을 선택한 다음, **만들기** 를 선택합니다. (사용자 흐름 버전에 대해 [자세히 알아보세요](user-flow-versions.md).)

    ![사용자 흐름 단추 만들기](./media/phone-authentication-user-flows/select-version.png)

7. 사용자 흐름에 대한 **이름** 을 입력합니다. 예를 들어 *signupsignin1* 과 같습니다.
8. **ID 공급자** 섹션의 **로컬 계정** 에서 **전화 등록** 을 선택합니다.

   ![사용자 흐름 전화 등록 옵션 선택됨](media/phone-authentication-user-flows/user-flow-phone-signup.png)

9. **소셜 ID 공급자** 에서 해당 사용자 흐름을 허용하려는 다른 ID 공급자를 선택합니다.

   > [!NOTE]
   > MFA(다단계 인증)는 가입 사용자 흐름에서 기본적으로 사용되지 않습니다. 사용자 흐름에서 전화 가입으로 MFA를 사용할 수 있으나, 여기에는 전화 번호가 기본 식별자로 사용되기 때문에 일회용 메일 암호가 두 번째 인증 요소에 사용할 수 있는 유일한 옵션이 됩니다.

1. **사용자 특성 및 토큰 클레임** 섹션에서 가입 중에 사용자로부터 수집하여 보내려는 클레임 및 특성을 선택합니다. 예를 들어 **자세히 보기** 를 선택하고 **국가/지역**, **표시 이름** 및 **우편 번호** 의 특성 및 클레임을 선택합니다. **확인** 을 선택합니다.

1. **만들기** 를 선택하여 사용자 흐름을 추가합니다. *B2C_1* 이라는 접두사가 이름 앞에 자동으로 붙습니다.

## <a name="enable-the-recovery-email-prompt-preview"></a>복구 메일 프롬프트 사용하기(미리 보기)

사용자 흐름에서 전화 가입 및 로그인을 사용하도록 설정할 때 복구 메일 기능도 사용하도록 설정하는 것이 좋습니다. 해당 기능은 사용자가 휴대폰을 사용하지 않을 경우 계정을 복구하는 데 사용할 수 있는 메일 주소를 제공합니다. 해당 메일 주소는 계정 복구에만 사용되며, 로그인에 사용할 수 없습니다.

- 복구 메일 프롬프트가 **On** 인 경우 처음 등록하는 사용자에게 백업 메일을 확인하라는 메시지가 표시됩니다. 이전에 복구 메일을 제공하지 않은 사용자는 다음 로그인 시 백업 메일을 확인하라는 메시지가 표시됩니다.

- 복구 메일이 **Off** 인 경우 사용자가 등록하거나 로그인하면 복구 메일 프롬프트가 표시되지 않습니다.
 
사용자 흐름 속성에서 복구 메일 프롬프트를 사용하도록 설정할 수 있습니다.

> [!NOTE]
> 시작하기 전에 위에서 설명한대로 [사용자 흐름에 전화 가입을 추가](#add-phone-sign-up-to-a-user-flow)했는지 확인합니다.

### <a name="to-enable-the-recovery-email-prompt"></a>복구 메일 프롬프트를 사용하도록 설정하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
4. Azure AD B2C의 **정책** 에서 **사용자 흐름** 을 선택합니다.
5. 목록에서 사용자 흐름을 선택합니다.
6. **설정** 아래에서 **속성** 을 선택합니다.
7. **전화 번호 등록 및 로그인에 대한 복구 메일 프롬프트를 사용하도록 설정(미리 보기)** 에서 다음을 선택합니다.

   - **On** 은 가입 및 로그인 시 복구 메일 프롬프트를 표시합니다.
   - **Off** 는 복구 메일 프롬프트를 숨깁니다.

    ![복구 메일 사용을 통한 사용자 흐름 속성](./media/phone-authentication-user-flows/recovery-email-settings.png)

8. **저장** 을 선택합니다.

### <a name="to-test-the-recovery-email-prompt"></a>복구 메일 프롬프트를 테스트하려면

사용자 흐름에서 전화 가입, 로그인, 복구 메일 프롬프트를 사용하도록 설정한 후 **사용자 흐름 실행** 을 통해 사용자 환경을 테스트할 수 있습니다.

1. **정책** > **사용자 흐름** 을 차례로 선택한 다음, 만든 사용자 흐름을 선택합니다. 사용자 흐름 개요 페이지에서 **사용자 흐름 실행** 을 선택합니다.

2. **애플리케이션** 에 대해 1단계에서 등록한 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.

3. **사용자 흐름 실행** 을 선택하고 다음 동작을 확인합니다.

   - 처음 등록하는 사용자에게 복구 메일을 제공하라는 메시지가 표시됩니다. 
   - 이미 등록되었지만 복구 메일을 제공하지 않은 사용자는 로그인 시 복구 메일을 제공하라는 메시지가 표시됩니다.

4. 메일 주소를 입력하고 **확인 코드 보내기** 를 선택합니다. 입력한 메일의 받은 편지함으로 코드가 전송되는지 확인합니다. 코드를 검색하여 **확인 코드** 상자에 입력합니다. **코드 확인** 을 선택합니다.

## <a name="enable-consent-information"></a>동의 정보 사용

가입 및 로그인 흐름에 동의 정보를 포함하는 것이 좋습니다. 샘플 텍스트가 제공됩니다. [CTIA 웹 사이트의](https://www.ctia.org/programs) 짧은 코드 모니터링 안내서를 참조하고 사용자 고유의 규정 준수 요구 사항을 충족하기 위한 최종 텍스트 및 기능 구성에 대한 지침은 사용자 고유의 법률 또는 규정 준수 전문가에게 문의하세요.
>
> *전화 번호를 제공하면 *&lt;삽입: 애플리케이션 이름&gt;* 에 로그인할 수 있도록 문자 메시지로 전송되는 일회성 암호를 받는 데 동의하게 됩니다. 표준 메시지 및 데이터 요금이 적용될 수 있습니다.*
>
> *&lt;삽입: 개인정보처리방침에 대한 링크&gt;*<br/>*&lt;삽입: 서비스 약관에 대한 링크&gt;*

동의 정보를 사용하도록 설정하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
4. Azure AD B2C의 **정책** 에서 **사용자 흐름** 을 선택합니다.
5. 목록에서 사용자 흐름을 선택합니다.
6. **사용자 지정** 아래에서 **언어** 를 선택합니다.
7. 동의 텍스트를 표시하려면 **사용자 언어 지정 사용** 을 선택합니다.
  
    ![사용자 언어 지정을 사용하도록 설정](./media/phone-authentication-user-flows/enable-language-customization.png)

8. 동의 정보를 사용자 지정하려면 목록에서 언어를 선택합니다.
9. 언어 패널에서  **전화 로그인 페이지** 를 선택합니다.
10. 기본값 다운로드를 선택합니다.

    ![기본값 다운로드](./media/phone-authentication-user-flows/phone-sign-in-language-override.png)

11. 다운로드한 JSON 파일을 엽니다. 다음 텍스트를 검색하고 사용자 지정합니다.

    - **disclaimer_link_1_url**: **재정의** 를 "true"로 변경하고 개인 정보에 대한 URL을 추가합니다.

    - **disclaimer_link_2_url**: **재정의** 를 "true"로 변경하고 사용 약관에 대한 URL을 추가합니다.  

    - **disclaimer_msg_intro**: **재정의** 를 "true"로 변경하고 **값** 을 원하는 고지 사항 문자열로 변경합니다.  

12. 파일을 저장합니다. **새 재정의 업로드** 에서 파일을 찾아 선택합니다. "성공적으로 업로드된 재정의" 알림이 표시되는지 확인합니다.

13. **전화 가입 페이지** 를 선택한 다음, 10~12단계를 반복합니다. 


## <a name="get-a-users-phone-number-in-your-directory"></a>디렉터리에서 사용자의 전화번호 가져오기

1. Graph 탐색기에서 다음 요청을 실행합니다.

   `GET https://graph.microsoft.com/v1.0/users/{object_id}?$select=identities`

1. 반환된 응답에서 `issuerAssignedId` 속성을 찾습니다.

   ```json
       "identities": [
           {
               "signInType": "phoneNumber",
               "issuer": "contoso.onmicrosoft.com",
               "issuerAssignedId": "+11231231234"
           }
       ]
   ```

## <a name="next-steps"></a>다음 단계

- [외부 ID 공급자 추가](add-identity-provider.md)
- [사용자 흐름 만들기](tutorial-create-user-flows.md)
