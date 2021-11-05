---
title: Azure Active Directory B2C와 itsme OpenID Connect
titleSuffix: Azure AD B2C
description: Client_secret 사용자 흐름 정책을 사용하여 itsme OIDC와 Azure AD B2C 인증을 통합하는 방법에 대해 알아봅니다. itsme은 디지털 ID 앱입니다. 카드 판독기, 암호, 2-단계 인증 및 여러 PIN 코드 없이 안전하게 로그인할 수 있습니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 73802a0d429d2eb71ec24be8b18d635eb5c2f003
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044573"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 itsme OpenID Connect (OIDC) 구성

Itsme digital ID 앱을 사용하면 카드 판독기, 암호, 2-단계 인증 또는 여러 PIN 코드 없이 안전하게 로그인할 수 있습니다. itsme 앱은 확인된 ID로 강력한 고객 인증을 제공합니다. 이 문서에서는 클라이언트 암호 사용자 흐름 정책을 사용하여 itsme Openid Connect(OIDC)와 Azure AD B2C 인증을 통합하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Azure 구독에 연결된 [Azure AD B2C 테넌트](tutorial-create-tenant.md).
* itsme에서 제공하는 클라이언트 ID(파트너 코드라고도 함)입니다.
* itsme에서 제공하는 서비스 코드입니다.
* itsme 계정에 대한 클라이언트 암호입니다.

## <a name="scenario-description"></a>시나리오 설명

![itsme 아키텍처 다이어그램](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

| 단계 | 설명 |
|------|------|
|1     | 웹 사이트 또는 애플리케이션에서 Azure AD B2C 사용자 흐름을 조정하여 **itsme에 로그인** 단추를 포함합니다. 사용자가 이 단추를 클릭하면 상호 작용 흐름이 시작됩니다.  |
|2     | Azure AD B2C는 itsme 클라이언트 암호 API에 권한 부여 요청을 전송하여 OpenID connect 흐름을 시작합니다. 잘 알려진/OpenID-구성 엔드포인트는 엔드포인트에 대한 정보를 포함하고 있습니다.  |
|3     | itsme 환경은 사용자를 itsme 식별 페이지로 리디렉션하여 사용자가 전화 번호를 입력할 수 있도록 합니다.  |
|4     | Itsme 환경은 사용자로부터 전화 번호를 수신하고 정확성의 유효성을 검사합니다.  |
|5     | 전화 번호가 활성 itsme 사용자에 속하는 경우 itsme 앱에 대한 작업이 생성됩니다.  |
|6     | 사용자가 itsme 앱을 열어 요청을 확인하고 작업을 확인합니다.  |
|7     |  앱은 itsme 환경에 작업이 확인되었음을 알립니다. |
|8     |  Itsme 환경에서는 Azure AD B2C로 OAuth 권한 부여 코드를 반환합니다. |
|9     |  권한 부여 코드를 사용하여 Azure AD B2C는 토큰 요청을 수행합니다. |
| 10 | itsme 환경은 토큰 요청을 확인하고, 여전히 유효한 경우 요청된 사용자 정보를 포함하는 OAuth 액세스 토큰 및 ID 토큰을 반환합니다. |
| 11 | 마지막으로 사용자는 인증된 사용자로 리디렉션 URL로 리디렉션됩니다.  |
|   |   |

## <a name="onboard-with-itsme"></a>itsme으로 등록

1. Itsme를 사용하여 계정을 만들려면 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace)에서 itsme를 방문하세요.

1. onboarding@itsme.be에 이메일을 보내 itsme 계정을 활성화합니다. B2C 설정에 필요한 **파트너 코드** 및 **서비스 코드** 를 받게 됩니다.

1. itsme 파트너 계정이 활성화된 후에는 **클라이언트 암호** 에 대한 일회성 링크를 포함하는 이메일을 받게 됩니다.

1. [itsme](https://business.itsme.be/en)의 지침에 따라 구성을 완료합니다.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>Azure AD B2C에서 새 ID 공급자를 설정합니다.

> [!NOTE]
> Azure 구독에 연결된 [Azure AD B2C 테넌트](tutorial-create-tenant.md)가 아직 없으면 만듭니다.

1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택(하거나, **추가 서비스** 를 선택하고 **모든 서비스** 검색 상자를 사용하여 *Azure AD B2C* 를 검색합니다).
1. **ID 공급자** 를 선택한 다음, **새 OpenID Connect 공급자** 를 선택합니다.
1. 다음 정보로 양식을 작성합니다.

   |속성 | 값 |
   |------------ |------- |
   | 이름 | itsme |
   | 메타데이터 URL | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>`<environment>`은 `e2e` (테스트 환경) 또는 `prd` (프로덕션) 중 하나인 곳  |
   | ClientID     | **클라이언트 ID**(**파트너 코드** 라고도 함)  |
   | 클라이언트 암호 | **클라이언트 암호** |
   | 범위  | openid service: YOURSERVICECODE profile email [phone] [address]  |
   |응답 형식 | code |
   |응답 모드 | Query |
   |도메인 힌트 | *이 열은 비워둘 수 있습니다* |
   |UserID | sub |
   |표시 이름 | name |
   |이름 | given_name |
   |Surname | family_name |
   |메일 | 이메일|

1. **저장** 을 선택합니다.

### <a name="configure-a-user-flow"></a>사용자 흐름 구성

1. Azure AD B2C 테넌트에서, **정책** 아래 **사용자 흐름** 을 선택합니다.

1. **새 사용자 흐름** 을 선택합니다.

1. **등록 및 로그인** 을 선택하고, 버전을 선택한 다음, **만들기** 를 선택합니다.

1. **이름** 을 입력합니다.

1. **ID 공급자** 섹션에서 **itsme** 를 선택합니다.

1. **만들기** 를 선택합니다.

1. 사용자 흐름 이름을 선택하여 새로 만든 사용자 흐름을 엽니다.

1. **속성** 을 선택하고 다음 값을 조정합니다.

   * **액세스 및 ID 토큰 수명(분)** 를 **5** 로 바꿉니다.
   * **새로 고침 토큰 슬라이딩 윈도우 수명** 을 **만료 없음** 으로 변경합니다.

### <a name="register-an-application"></a>애플리케이션 등록

1. B2C 테넌트에서 **관리** 아래에서 **앱 등록** > **새 등록** 을 선택합니다.

1. 애플리케이션의 **이름** 을 입력하고 **리디렉션 URI** 를 입력합니다. 테스트를 위해 `https://jwt.ms`을 입력하십시오.

1. 다단계 인증을 **사용하지 않도록** 설정했는지 확인합니다.

1. **등록** 을 선택합니다.

   a. 테스트 목적인 경우 **인증** 을 선택하고 **암시적 권한 부여** 에서 **액세스 토큰** 및 **ID 토큰** 확인란을 선택합니다.  

   b. **저장** 을 선택합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테넌트에서 **정책** 아래 **사용자 흐름** 을 선택합니다.

1. 이전에 만든 사용자 흐름을 선택합니다.

1. **사용자 흐름 실행** 을 선택합니다.

   a. **애플리케이션**: *등록된 앱을 선택합니다*

   b. **회신 URL**: *리디렉션 URL을 선택합니다*

1. itsme **식별** 페이지가 나타납니다.  

1. 휴대폰 번호를 입력한 후 **전송** 을 선택합니다.

1. itsme 앱에서 작업을 확인합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

* [Azure AD B2C의 사용자 지정 정책](custom-policy-overview.md)

* [Azure AD B2C의 사용자 지정 정책 시작하기](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
