---
title: Azure Active Directory B2C와 IDology 통합
titleSuffix: Azure AD B2C
description: IDology에서 Azure AD B2C의 샘플 온라인 결제 앱을 통합하는 방법을 알아봅니다. IDology는 여러 솔루션을 포함하는 IID 확인 및 교정 공급자입니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 5069ef001ad20024ce72387d0bc02a840514aa74
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036040"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 IDology를 구성하기 위한 자습서 

이 샘플 자습서에서는 Azure AD B2C를 [IDology](https://www.idology.com/solutions/)와 통합하는 방법에 대한 참고 자료를 제공합니다. IDology는 여러 솔루션을 포함하는 IID 확인 및 교정 공급자입니다. 이 샘플에서는 IDology에서 제공하는 ExpectID solution를 다룹니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 Azure 구독에 연결

## <a name="scenario-description"></a>시나리오 설명

IDology 통합에는 다음 구성 요소가 포함됩니다.

- Azure AD B2C: 사용자의 자격 증명 확인을 담당하는 권한 부여 서버입니다. ID 공급자라고도 합니다.
- IDology – IDology 서비스는 사용자가 제공하는 입력을 사용하여 사용자의 ID를 확인합니다.
- 사용자 지정 Rest API – 이 API는 Azure AD 와 IDology 서비스 간의 통합을 구현합니다.

다음 아키텍처 다이어그램은 구현된 상황을 보여줍니다.

![IDology 아키텍처 다이어그램](media/partner-idology/idology-architecture-diagram.png)

| 단계 | 설명 |
|------|------|
|1     | 사용자가 로그인 페이지에 도착합니다. |
|2     | 사용자가 등록을 선택하여 새 계정을 만들고 페이지에 정보를 입력합니다. Azure AD B2C가 사용자 특성을 수집합니다. |
|3     | Azure AD B2C는 중간 계층 API를 호출하고 사용자 특성을 전달합니다. |
|4     | 중간 계층 API는 사용자 특성을 수집하고 IDOlogy API에서 사용할 수 있는 형식으로 변환합니다. 그런 다음 IDology에 정보를 보냅니다. |
|5     | IDology는 정보를 사용하고 처리한 후 중간 계층 API에 결과를 반환합니다. |
|6     | 중간 계층 API는 정보를 처리하고 Azure AD B2C에 관련 정보를 다시 보냅니다. |
|7     | Azure AD B2C는 중간 계층 API에서 정보를 다시 받습니다. **실패** 응답이 표시되면 사용자에게 오류 메시지가 표시됩니다. **성공** 응답이 표시되면 사용자가 인증되고 디렉터리에 기록됩니다. |
|      |      |

> [!NOTE]
> 또한 Azure AD B2C는 고객에게 단계별 인증을 수행하도록 요청할 수 있지만, 이 시나리오는 이 자습서의 범위를 벗어나는 것입니다.

## <a name="onboard-with-idology"></a>IDology로 등록

1. IDology은 [여기](https://www.idology.com/solutions/)에서 찾을 수 있는 다양한 솔루션을 제공합니다. 이 샘플의 경우, ExpectID를 사용합니다.

2. IDology 계정을 만들려면 [IDology](https://www.idology.com/request-a-demo/microsoft-integration-signup/)에 문의하세요.

3. 계정이 만들어지면 API 구성에 필요한 정보를 받게 됩니다. 다음 섹션은 이 프로세스를 설명합니다.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

### <a name="part-1---deploy-the-api"></a>1부 - API 배포

제공된 [API 코드](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/Api)를 Azure 서비스에 배포합니다. 이러한 [지침](/visualstudio/deployment/quickstart-deploy-to-azure)에 따라 Visual Studio에서 코드를 게시할 수 있습니다.

필요한 설정을 사용하여 Azure AD를 구성하려면 배포된 서비스의 URL이 필요합니다.

### <a name="part-2---configure-the-api"></a>2부 - API 구성 

애플리케이션 설정은 [Azure의 App Service에서 구성](../app-service/configure-common.md#configure-app-settings)할 수 있습니다. 이 방법을 사용하면 설정을 리포지토리로 체크인하지 않고도 안전하게 구성할 수 있습니다. Rest API에 대한 다음 설정을 제공해야 합니다.

| 애플리케이션 설정 | 원본 | 메모 |
| :-------- | :------------| :-----------|
|IdologySettings:ApiUsername | IDology 계정 구성 |     |
|IdologySettings:ApiPassword | IDology 계정 구성 |     |
|WebApiSettings:ApiUsername |API에 대한 사용자 이름 정의| ExtId 구성에서 사용됨 |
|WebApiSettings:ApiPassword | API에 대한 암호를 정의합니다. | ExtId 구성에서 사용됨

### <a name="part-3---create-api-policy-keys"></a>3부 - API 정책 키 만들기

이 [문서](secure-rest-api.md#add-rest-api-username-and-password-policy-keys)를 참조하여 두 개의 정책 키를 만듭니다. 하나는 API 사용자 이름, 하나는 위에서 정의한 API 암호입니다.

샘플 정책은 다음 키 이름을 사용합니다.

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>4부 - Azure AD B2C 정책 구성

1. 이 [문서](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)를 참조하여 [LocalAccount 스타터 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)을 다운로드하고 Azure AD B2C 테넌트에 대한 정책을 구성합니다. **사용자 지정 정책 테스트** 섹션을 완료할 때까지 지침을 따릅니다.

2. [여기](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy)에서 두 개의 샘플 정책을 다운로드합니다.

3. 두 가지 샘플 정책을 업데이트합니다.

   1. 두 정책을 모두 엽니다.

      1. `Idology-ExpectId-API` 섹션에서 위에 배포된 API의 위치로 `ServiceUrl` 메타데이터 항목을 업데이트합니다.

      1. `yourtenant`은 Azure AD B2C 테넌트의 이름으로 바꿉니다.
      예를 들어 Azure AD B2C 테넌트의 이름이  `contosotenant`인 경우 모든  `yourtenant.onmicrosoft.com`  인스턴스는 `contosotenant.onmicrosoft.com`가 됩니다.

   1. TrustFrameworkExtensions.xml 파일을 엽니다.

      1.  `<TechnicalProfile Id="login-NonInteractive">` 요소를 찾습니다.  `IdentityExperienceFrameworkAppId` 의 두 인스턴스를 이전에 만든 IdentityExperienceFramework 애플리케이션의 애플리케이션 ID로 바꿉니다.

      1.  `ProxyIdentityExperienceFrameworkAppId` 의 두 인스턴스를 이전에 만든 ProxyIdentityExperienceFramework 애플리케이션의 애플리케이션 ID로 바꿉니다.

4. 이전에 업로드한 SignInorSignUp.xml 및 TrustFrameworkExtensions.xml를 1단계의 Azure AD B2C에서 업데이트된 두 개의 샘플 정책과 바꿉니다.

> [!NOTE]
> 모범 사례로, 고객은 특성 컬렉션 페이지에서 동의 알림을 추가하는 것이 좋습니다. 신원 확인을 위해 타사 서비스에 정보가 보내짐을 사용자에게 알립니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테넌트를 열고 **정책** 아래에서 **사용자 흐름** 을 선택합니다.

2. 이전에 만든 **사용자 흐름** 을 선택합니다.

3. **사용자 흐름 실행** 을 선택하고 설정을 선택합니다.

   1. **애플리케이션** - 등록된 앱을 선택합니다(JWT는 샘플).

   1. **회신 URL** - **리디렉션 URL** 을 선택합니다.

   1. **사용자 흐름 실행** 을 선택합니다.

4. 등록 흐름을 진행하고 계정을 만듭니다.

5. 로그아웃합니다.

6. 로그인 흐름을 진행합니다.

7. **계속** 을 입력하면 IDology 퍼즐이 표시됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
