---
title: Azure Active Directory B2C에서 사용자 흐름 및 사용자 지정 정책 | Microsoft Docs
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 기본 제공 사용자 흐름과 사용자 지정 정책 확장 가능 정책 프레임워크에 대해 자세히 알아보세요.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b663904df2d116bdfa92a0dcef4b05c0806fec14
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112199145"
---
# <a name="user-flows-and-custom-policies-overview"></a>사용자 흐름 및 사용자 지정 정책 비교

Azure AD B2C에서 사용자가 애플리케이션에 대한 액세스 권한을 얻기 위해 수행하는 비즈니스 논리를 정의할 수 있습니다. 예를 들어 사용자가 로그인하거나 등록하거나 프로필을 편집하거나 암호를 다시 설정하는 경우 사용자가 따라야 하는 단계의 순서를 결정할 수 있습니다. 시퀀스를 완료한 후 사용자는 토큰을 획득하고 애플리케이션에 대한 액세스 권한을 얻습니다. 

Azure AD B2C에서는 ID 사용자 환경을 제공하기 위한 두 가지 방법이 있습니다.

* **사용자 흐름** 은 Microsoft에서 제공하는 미리 정의되고 기본 제공되는 구성 가능한 정책이므로 가입, 로그인 및 정책 편집 환경을 몇 분 내에 만들 수 있습니다.

* **사용자 지정 정책** 을 사용하면 복잡한 ID 환경 시나리오에 적합한 고유의 사용자 경험을 만들 수 있습니다.

다음 스크린샷은 사용자 흐름 설정 UI를 사용자 지정 정책 구성 파일에 대비하여 보여 줍니다.

![다음 스크린샷은 사용자 흐름 설정 UI를 사용자 지정 정책 구성 파일에 대비하여 보여 줍니다.](media/user-flow-overview/user-flow-vs-custom-policy.png)

이 문서에서는 사용자 흐름과 사용자 지정 정책에 대한 간략한 개요를 제공하고 비즈니스 요구 사항에 가장 적합한 방법을 결정하는 데 도움이 됩니다.

## <a name="user-flows"></a>사용자 흐름

가장 일반적인 ID 작업을 설정하기 위해 Azure Portal에는 *사용자 흐름* 이라는 미리 정의되고 구성 가능한 몇 가지 정책이 있습니다.

애플리케이션의 ID 환경 동작을 제어하기 위해 다음과 같은 사용자 흐름 설정을 구성할 수 있습니다.

* 로그인에 사용되는 계정 유형(예: Facebook과 같은 소셜 계정 또는 로그인에 이메일 주소와 암호를 사용하는 로컬 계정)
* 소비자로부터 수집할 특성(예: 이름, 우편 번호 또는 거주 국가/지역)
* Azure AD MFA(Multi-Factor Authentication)
* 사용자 인터페이스의 사용자 지정
* 사용자가 사용자 흐름을 완료한 후에 애플리케이션에서 받는 토큰의 클레임 세트
* 세션 관리
* 등...

앱에 대한 일반적인 ID 시나리오의 대부분은 사용자 흐름을 통해 효율적으로 정의되고 구현됩니다. 사용자 지정 정책에 대한 완전한 유연성이 필요한 복잡한 사용자 경험 시나리오가 없는 경우 기본 제공 사용자 흐름을 사용하는 것이 좋습니다.

## <a name="custom-policies"></a>사용자 지정 정책

사용자 지정 정책은 Azure AD B2C 테넌트 사용자 환경의 동작을 정의하는 구성 파일입니다. Azure AD B2C 포털에서는 가장 일반적인 ID 작업에 대해서도 사용자 흐름이 미리 정의되어 있지만 ID 개발자는 다양한 작업을 완료하기 위해 사용자 지정 정책을 완벽하게 편집할 수 있습니다.

사용자 지정 정책은 완전히 구성 가능하며 정책을 기반으로 합니다. 표준 프로토콜에서 엔터티 간의 신뢰를 오케스트레이션합니다. 예를 들어 OpenID Connect, OAuth, SAML 및 일부 비표준 항목(예: REST API 기반 시스템 간 클레임 교환)을 사용할 수 있습니다. 이 프레임워크는 사용자에게 친숙한 화이트 레이블 환경을 제공합니다.

사용자 지정 정책은 단계를 조합하여 사용자 경험을 구성할 수 있는 기능을 제공합니다. 예를 들면 다음과 같습니다.

* 다른 ID 공급자와 페더레이션
* 자사 및 타사 MFA(다단계 인증) 챌린지
* 모든 사용자 입력 수집
* REST API 통신을 사용하여 외부 시스템과 통합

각 사용자 경험은 정책에 의해 정의됩니다. 조직에 가장 적합한 사용자 환경을 사용하도록 설정하는 데 필요한 만큼의 정책을 빌드할 수 있습니다.

![IEF에서 사용하도록 설정된 복잡한 사용자 경험의 예를 보여 주는 다이어그램](media/user-flow-overview/custom-policy-diagram.png)

사용자 지정 정책은 계층 구조 체인에서 서로를 참조하는 여러 XML 파일로 정의됩니다. XML 요소는 클레임 스키마, 클레임 변환, 콘텐츠 정의, 클레임 공급자, 기술 프로필 및 사용자 경험 오케스트레이션 단계 및 ID 환경의 다른 측면을 정의합니다.

사용자 지정 정책의 강력한 유연성은 복잡한 ID 시나리오를 구축해야 하는 경우에 가장 적합합니다. 사용자 지정 정책을 구성하는 개발자는 신뢰할 수 있는 관계를 세부적으로 신중하게 정의하여 메타데이터 엔드포인트, 정확한 클레임 정의 교환을 포함하고 ID 공급자 각각의 필요에 따라 비밀, 키 및 인증서를 구성해야 합니다.

사용자 지정 정책에 대해서는 [Azure Active Directory B2C의 사용자 지정 정책](custom-policy-overview.md)에서 자세히 알아보세요.

## <a name="comparing-user-flows-and-custom-policies"></a>사용자 흐름 및 사용자 지정 정책 비교

아래 표에서는 Azure AD B2C 사용자 흐름 및 사용자 지정 정책을 사용하도록 설정할 수 있는 시나리오를 자세히 비교합니다.

| 컨텍스트 | 사용자 흐름 | 사용자 지정 정책 |
|-|-------------------|-----------------|
| 대상 사용자 | ID 전문 지식이 있거나 없는 모든 애플리케이션 개발자 | ID 전문가, 시스템 통합 서비스, 컨설턴트 및 사내 ID 팀. OpenID Connect 흐름에 만족하고 ID 공급자와 클레임 기반 인증을 이해합니다. |
| 구성 방법 | 사용자에게 친숙한 UI(사용자 인터페이스)를 사용하는 Azure Portal | XML 파일 직접 편집 및 Azure Portal에 업로드 |
| UI 사용자 지정 | HTML, CSS 및 [JavaScript](javascript-and-page-layout.md)를 비롯한 [전체 UI 사용자 지정](customize-ui-with-html.md)<br><br>사용자 지정 문자열을 사용하는 [다국어 지원](language-customization.md) | 동일 |
| 특성 사용자 지정 | 표준 및 사용자 지정 특성 | 동일 |
| 토큰 및 세션 관리 | [토큰 사용자 지정](configure-tokens.md) 및 [세션 동작](session-behavior.md). | 동일 |
| ID 공급자 | Azure Active Directory 테넌트를 사용한 페더레이션과 같이 [사전 정의된 로컬](identity-provider-local.md) 또는 [소셜 공급자](add-identity-provider.md) | 표준 기반 OIDC, OAUTH 및 SAML  REST API와의 통합을 사용하여 인증을 수행할 수도 있습니다. |
| ID 작업 | 로컬 또는 많은 소셜 계정을 사용하여 [등록 또는 로그인](add-sign-up-and-sign-in-policy.md)<br><br>[셀프 서비스 암호 재설정](add-password-reset-policy.md)<br><br>[프로필 편집](add-profile-editing-policy.md)<br><br>Multi-Factor Authentication.<br><br>액세스 토큰 흐름. | 사용자 지정 ID 공급자를 사용하는 사용자 흐름과 동일한 작업을 완료하거나 사용자 지정 범위를 사용합니다.<br><br>등록 시 다른 시스템에서 사용자를 프로비전합니다.<br><br>고유한 이메일 서비스 공급자를 사용하여 환영 이메일을 보냅니다.<br><br>Azure AD B2C 외부의 사용자 저장소를 사용합니다.<br><br>API를 사용하여 신뢰할 수 있는 시스템으로 사용자 제공 정보의 유효성을 검사합니다. |

## <a name="application-integration"></a>애플리케이션 통합

테넌트에 여러 유형의 많은 사용자 흐름 또는 사용자 정책을 만들고 필요에 따라 애플리케이션에서 사용할 수 있습니다. 사용자 흐름과 사용자 지정 정책은 모두 애플리케이션에서 재사용할 수 있습니다. 이러한 유연성 덕분에 코드 변경 없이 또는 최소한의 변경으로 ID 환경을 정의하고 수정할 수 있습니다. 

사용자가 애플리케이션에 로그인하려는 경우 애플리케이션은 사용자 흐름 또는 사용자 지정 정책 제공 엔드포인트에 대한 권한 부여 요청을 시작합니다. 사용자 흐름 또는 사용자 지정 정책은 사용자의 환경을 정의하고 제어합니다. 사용자 흐름을 완료하면 Azure AD B2C에서 토큰을 생성한 다음 사용자를 애플리케이션으로 다시 리디렉션합니다.

![Azure AD B2C 로그인 페이지 간의 흐름을 보여 주는 화살표가 있는 모바일 앱](media/user-flow-overview/app-integration.png)

여러 애플리케이션에서 동일한 사용자 흐름 또는 사용자 지정 정책을 사용할 수 있습니다. 단일 애플리케이션은 여러 사용자 흐름 또는 사용자 지정 정책을 사용할 수 있습니다.

예를 들어 애플리케이션에 로그인하기 위해 애플리케이션은 *가입 또는 로그인* 사용자 흐름을 사용합니다. 사용자가 로그인한 후 프로필을 편집하려고 할 수도 있습니다. 프로필을 편집하기 위해 애플리케이션은 이번에는 *프로필 편집* 사용자 흐름을 사용하여 다른 권한 부여 요청을 시작합니다.

애플리케이션은 사용자 흐름 또는 사용자 지정 정책 이름을 포함하는 표준 HTTP 인증 요청을 사용하여 사용자 흐름을 트리거합니다. 사용자 지정 [토큰](tokens-overview.md)은 응답으로 수신됩니다.


## <a name="next-steps"></a>다음 단계

- 권장 사용자 흐름을 만들려면 [자습서: 사용자 흐름 만들기](tutorial-create-user-flows.md)의 지침을 따르세요.
- [Azure AD B2C의 사용자 흐름 버전](user-flow-versions.md)에 대해 알아봅니다.
- [Azure AD B2C 사용자 지정 정책](custom-policy-overview.md)에 대해 자세히 알아봅니다.
