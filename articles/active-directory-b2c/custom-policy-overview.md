---
title: Azure Active Directory B2C 사용자 지정 정책 개요 | Microsoft Docs
description: Azure Active Directory B2C 사용자 지정 정책 및 ID 환경 프레임워크에 대한 항목입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 5c27384e58a23501224e279e987115e4981ebf99
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122633610"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Azure AD B2C 사용자 지정 정책 개요

사용자 지정 정책은 Azure AD B2C(Azure Active Directory B2C) 테넌트의 동작을 정의하는 구성 파일입니다. Azure AD B2C 포털에서는 가장 일반적인 ID 작업에 대해서도 [사용자 흐름](user-flow-overview.md)이 미리 정의되어 있지만 ID 개발자는 다양한 작업을 완료하기 위해 사용자 지정 정책을 완벽하게 편집할 수 있습니다.

사용자 지정 정책은 완전히 구성 가능하며 정책을 기반으로 합니다. 사용자 지정 정책은 표준 프로토콜의 엔터티 간 신뢰를 조율합니다. 예를 들어 OpenID Connect, OAuth, SAML 및 일부 비표준 항목(예: REST API 기반 시스템 간 클레임 교환)을 사용할 수 있습니다. 이 프레임워크는 사용자에게 친숙한 화이트 레이블 환경을 제공합니다.

사용자 지정 정책은 계층적 체인에서 서로를 참조하는 하나 이상의 XML 형식 파일로 표시됩니다. XML 요소는 구성 요소, 사용자 및 다른 당사자와의 상호 작용, 비즈니스 논리를 정의합니다. 

## <a name="custom-policy-starter-pack"></a>사용자 지정 정책 시작 팩

Azure AD B2C 사용자 지정 정책 [스타터 팩](tutorial-create-user-flows.md?pivots=b2c-custom-policy#get-the-starter-pack)에는 신속하게 시작하기 위한 여러 가지 미리 빌드된 정책이 제공됩니다. 이러한 각 시작 팩 각각에는 설명한 시나리오를 수행하는 데 필요한 최소한의 기술 프로필 및 사용자 경험이 포함되어 있습니다.

- **LocalAccounts** - 로컬 계정만 사용할 수 있습니다.
- **SocialAccounts** - 소셜(또는 페더레이션) 계정만 사용할 수 있습니다.
- **SocialAndLocalAccounts** - 로컬 계정과 소셜 계정을 모두 사용할 수 있습니다. 대부분의 샘플이 이 정책을 참조합니다.
- **SocialAndLocalAccountsWithMFA** - 소셜, 로컬 및 다단계 인증 옵션을 사용할 수 있습니다.

[Azure AD B2C 샘플 GitHub 리포지토리](https://github.com/azure-ad-b2c/samples)에서 로컬 계정 정책 향상, 소셜 계정 정책 향상, MFA 향상, 사용자 인터페이스 향상, 일반 향상, 앱 마이그레이션, 사용자 마이그레이션, 조건부 액세스, 웹 테스트 및 CI/CD와 같은 향상된 몇 가지 Azure AD B2C 사용자 지정 CIAM 사용자 경험에 대한 샘플을 찾을 수 있습니다.
 
## <a name="understanding-the-basics"></a>기본 사항 이해 

### <a name="claims"></a>클레임

클레임은 Azure AD B2C 정책 실행 중에 데이터의 임시 스토리지를 제공합니다. 이름, 성 또는 사용자나 다른 시스템에서 얻은 다른 클레임(클레임 교환)과 같은 사용자 정보를 저장할 수 있습니다. [클레임 스키마](claimsschema.md)에서 클레임을 선언합니다. 

정책이 실행되면 Azure AD B2C는 내부 및 외부 당사자와 클레임을 주고 받은 다음, 이러한 클레임의 일부를 신뢰 당사자 애플리케이션에 토큰의 일부로 보냅니다. 클레임은 다음과 같은 방법으로 사용됩니다. 

- 디렉터리 사용자 개체에 대해 클레임을 저장하거나, 읽거나, 업데이트합니다.
- 외부 ID 공급자로부터 클레임을 받습니다.
- 사용자 지정 REST API 서비스를 사용하여 클레임을 보내거나 받습니다.
- 가입 또는 프로필 편집 흐름 중에 데이터를 사용자의 클레임으로 수집합니다.

### <a name="manipulating-your-claims"></a>클레임 조작

[클레임 변환](claimstransformations.md)은 지정된 클레임을 다른 클레임으로 변환하거나, 클레임을 평가하거나, 클레임 값을 설정하는 데 사용할 수 있는 미리 정의된 함수입니다. 예를 들어 문자열 컬렉션에 항목을 추가하거나, 문자열의 대/소문자를 변경하거나, 날짜 및 시간 클레임을 평가합니다. 클레임 변환은 변환 메서드를 지정합니다. 

### <a name="customize-and-localize-your-ui"></a>UI 사용자 지정 및 지역화

웹 브라우저에 페이지를 표시하여 사용자로부터 정보를 수집하려는 경우 [자체 어설션 기술 프로필](self-asserted-technical-profile.md)을 사용합니다. 자체 어설션 기술 프로필을 편집하여 [클레임을 추가하고 사용자 입력을 사용자 지정](./configure-user-input.md)할 수 있습니다.

자체 어설션 기술 프로필에 대한 [사용자 인터페이스를 사용자 지정](customize-ui-with-html.md)하려면 [콘텐츠 정의](contentdefinitions.md) 요소에서 사용자 지정된 HTML 콘텐츠를 사용하여 URL을 지정합니다. 자체 어설션 기술 프로필에서는 이 콘텐츠 정의 ID를 가리킵니다.

언어별 문자열을 사용자 지정하려면 [localization](localization.md) 요소를 사용합니다. 콘텐츠 정의는 로드할 지역화된 리소스 목록을 지정하는 [localization](localization.md) 참조를 포함할 수 있습니다. Azure AD B2C는 URL에서 로드된 HTML 콘텐츠와 사용자 인터페이스 요소를 병합한 다음, 사용자에게 해당 페이지를 표시합니다. 

## <a name="relying-party-policy-overview"></a>신뢰 당사자 정책 개요

SAML 프로토콜에서는 서비스 공급자라고 부르는 신뢰 당사자 애플리케이션은 [신뢰 당사자 정책](relyingparty.md)을 호출하여 특정 사용자 경험을 실행합니다. 신뢰 당사자 정책은 실행할 사용자 경험 및 토큰에 포함되는 클레임 목록을 지정합니다. 

![정책 실행 흐름을 보여주는 다이어그램](./media/custom-policy-overview/custom-policy-execution.png)

동일한 정책을 사용하는 모든 신뢰 당사자 애플리케이션은 동일한 토큰 클레임을 수신하고, 사용자는 동일한 사용자 경험을 거칩니다.

### <a name="user-journeys"></a>사용자 경험

[사용자 경험](userjourneys.md)을 사용하면 사용자가 애플리케이션에 대한 액세스 권한을 얻기 위해 준수해야 하는 경로를 통해 비즈니스 논리를 정의할 수 있습니다. 사용자는 애플리케이션에 제공되어야 하는 클레임을 검색하기 위한 사용자 경험을 거치게 됩니다. 사용자 경험은 일련의 [오케스트레이션 단계](userjourneys.md#orchestrationsteps)를 거쳐 빌드됩니다. 사용자는 토큰을 획득하기 위한 마지막 단계에 도달해야 합니다. 

다음 지침에서는 [소셜 및 로컬 계정 스타터 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts) 정책에 오케스트레이션 단계를 추가하는 방법에 대해 설명합니다. 다음은 추가된 REST API 호출의 예제입니다.

![사용자 지정된 사용자 경험](media/custom-policy-overview/user-journey-flow.png)


### <a name="orchestration-steps"></a>오케스트레이션 단계

오케스트레이션 단계에서는 의도한 용도 또는 기능을 구현하는 메서드를 참조합니다. 이 메서드를 [기술 프로필](technicalprofiles.md)이라고 합니다. 비즈니스 논리를 더 잘 나타내도록 사용자 경험을 분기해야 하는 경우 오케스트레이션 단계는 [하위 경험](subjourneys.md)을 참조합니다. 하위 경험에는 자체 오케스트레이션 단계가 포함됩니다.

사용자는 토큰을 획득하려면 사용자 경험의 마지막 오케스트레이션 단계에 도달해야 합니다. 그러나 사용자가 모든 오케스트레이션 단계를 통과할 필요는 없습니다. 오케스트레이션 단계에 정의된 [전제 조건](userjourneys.md#preconditions)에 따라 오케스트레이션 단계를 조건부로 실행하면 됩니다. 

오케스트레이션 단계가 완료되면 Azure AD B2C는 출력된 클레임을 **클레임 모음** 에 저장합니다. 클레임 모음의 클레임은 사용자 경험의 추가 오케스트레이션 단계에서 활용할 수 있습니다.

다음 다이어그램은 사용자 경험의 오케스트레이션 단계에서 클레임 모음에 액세스할 수 있는 방법을 보여줍니다.

![Azure AD B2C 사용자 경험](media/custom-policy-overview/user-journey-diagram.png)

### <a name="technical-profile"></a>기술 프로필

기술 프로필은 다양한 유형의 당사자와 통신하는 인터페이스를 제공합니다. 사용자 경험은 오케스트레이션 단계를 통해 호출 기술 프로필을 결합하여 비즈니스 논리를 정의합니다.

모든 유형의 기술 프로필은 동일한 개념을 공유합니다. 입력 클레임을 보내고, 클레임 변환을 실행하고, 구성된 주체와 통신합니다. 프로세스가 완료되면 기술 프로필이 출력 클레임을 클레임 모음에 반환합니다. 자세한 내용은 [기술 프로필 개요](technicalprofiles.md)를 참조하세요.

### <a name="validation-technical-profile"></a>유효성 검사 기술 프로필

사용자가 사용자 인터페이스와 상호 작용할 때, 수집된 데이터의 유효성을 검사해야 하는 경우가 있습니다. 사용자와 상호 작용하려면 [자체 어설션 기술 프로필](self-asserted-technical-profile.md)을 사용해야 합니다.

사용자 입력의 유효성을 검사하기 위해 [유효성 검사 기술 프로필](validation-technical-profile.md)이 자체 어설션 기술 프로필에서 호출됩니다. 유효성 검사 기술 프로필은 비대화형 기술 프로필을 호출하는 방법입니다. 이 경우 기술 프로필은 출력 클레임 또는 오류 메시지를 반환할 수 있습니다. 오류 메시지는 화면에서 사용자에게 렌더링되므로, 사용자가 다시 시도할 수 있습니다.

다음 다이어그램에서는 Azure AD B2C에서 유효성 검사 기술 프로필을 사용하여 사용자 자격 증명의 유효성을 검사하는 방법을 보여줍니다.

![유효성 검사 기술 프로필 다이어그램](media/custom-policy-overview/validation-technical-profile.png)

## <a name="inheritance-model"></a>상속 모델

각 스타터 팩에는 다음 파일이 포함되어 있습니다.

- **기본 파일** - 대부분의 정의를 포함합니다. 정책의 문제 해결 및 장기적인 유지 관리에 도움이 되도록 이 파일을 최소한으로 변경하는 것이 좋습니다.
- **확장 파일** - 테넌트에 대한 고유한 구성 변경 내용을 포함합니다. 이 정책 파일은 기본 파일에서 파생됩니다. 이 파일을 사용하여 새 기능을 추가하거나 기존 기능을 재정의할 수 있습니다. 예를 들어 이 파일을 사용하여 새 ID 공급자와 페더레이션합니다.
- **RP(신뢰 당사자)** 파일 - 웹, 모바일 또는 데스크톱 애플리케이션과 같은 신뢰 당사자 애플리케이션이 직접 호출하는 단일 작업 중심 파일입니다. 가입, 로그인, 암호 재설정 또는 프로필 편집과 같은 고유한 작업마다 자체적인 신뢰 당사자 정책 파일이 필요합니다. 이 정책 파일은 확장 파일에서 파생됩니다.

상속 모델은 다음과 같습니다.

- 모든 수준의 자식 정책은 부모 정책에서 상속하고 새 요소를 추가하여 확장할 수 있습니다.
- 좀 더 복잡한 시나리오의 경우 상속 수준을 더 추가할 수 있습니다(최대 10개).
- 신뢰 당사자 정책을 추가할 수 있습니다. 예를 들어 내 계정을 삭제하고 전화 번호, SAML 신뢰 당사자 정책 등을 변경합니다.

다음 다이어그램은 정책 파일과 신뢰 당사자 애플리케이션 간의 관계를 보여 줍니다.

![보안 프레임워크 정책 상속 모델을 보여주는 다이어그램](media/custom-policy-overview/policies.png)


## <a name="guidance-and-best-practices"></a>지침 및 모범 사례

### <a name="best-practices"></a>모범 사례

Azure AD B2C 사용자 지정 정책 내에서 고유한 비즈니스 논리를 통합하여 필요한 사용자 환경을 빌드하고 서비스의 기능을 확장할 수 있습니다. 새로 시작하는 고객을 위한 모범 사례 및 권장 사항이 준비되어 있습니다.

- **확장 정책** 또는 **신뢰 당사자 정책** 내에서 논리를 만들어 보세요. 동일한 ID를 참조하여 기본 정책을 재정의하는 새 요소를 추가할 수 있습니다. 이렇게 하면 프로젝트를 스케일 아웃할 수 있을 뿐 아니라 나중에 Microsoft에서 새로운 스타터 팩을 출시할 때 기본 정책을 더 쉽게 업그레이드할 수 있습니다.
- **기본 정책** 내에서는 되도록이면 변경 작업을 수행하지 않는 것이 좋습니다. 꼭 변경해야 하는 경우에는 변경 위치를 주석으로 표시합니다.
- 기술 프로필 메타데이터와 같은 요소를 재정의하는 경우 기본 정책의 기술 프로필 전체를 복사하지 말고, 요소의 필요한 부분만 복사합니다. 변경 방법에 대한 예제는 [이메일 검증을 사용하지 않도록 설정](./disable-email-verification.md)을 참조하세요.
- 핵심 기능이 공유되는 기술 프로필의 중복을 줄이려면 [기술 프로필 포함](technicalprofiles.md#include-technical-profile)을 사용합니다.
- 로그인하는 동안 Azure AD 디렉터리에 쓰지 않습니다. 제한 문제가 발생할 수 있습니다.
- 정책에 REST API와 같은 외부 종속성이 있는 경우 고가용성을 확보해야 합니다.
- 보다 나은 사용자 환경을 제공할 수 있도록 [온라인 콘텐츠 전송](../cdn/index.yml)을 사용하여 사용자 지정 HTML 템플릿을 전역적으로 배포합니다. Azure CDN(Content Delivery Network)을 사용하면 로드 시간을 줄이고, 대역폭을 절약하고, 응답 속도를 높일 수 있습니다.
- 사용자 경험을 변경하려면 기본 정책의 사용자 경험 전체를 확장 정책으로 복사합니다. 복사한 사용자 경험에 고유한 사용자 경험 ID를 제공합니다. 그런 다음, [신뢰 당사자 정책](relyingparty.md)에서 새로운 사용자 경험을 가리키도록 [default user journey](relyingparty.md#defaultuserjourney) 요소를 변경합니다.

## <a name="troubleshooting"></a>문제 해결

Azure AD B2C 정책을 사용하여 개발할 때 사용자 경험을 실행하는 동안 오류나 예외가 발생할 수 있습니다. 이 경우 Application Insights를 사용하여 조사할 수 있습니다.

- Application Insights를 Azure AD B2C와 통합하여 [예외를 진단](troubleshoot-with-application-insights.md)합니다.
- [Visual Studio Code용 Azure AD B2C 확장](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)은 정책 이름 및 시간을 기반으로 [로그에 액세스하고 시각화](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md)하는 데 도움이 될 수 있습니다.
- 사용자 지정 정책 설정에서 가장 일반적인 오류는 형식이 잘못 지정된 XML입니다. XML 파일을 업로드하기 전에 [XML 스키마 유효성 검사](troubleshoot-custom-policies.md)를 사용하여 오류를 식별합니다.

## <a name="continuous-integration"></a>연속 통합

Azure Pipelines에서 설정하는 CI/CD(연속 통합 및 지속적인 업데이트) 파이프라인을 사용하여 [소프트웨어 제공 및 코드 제어 자동화에 Azure AD B2C 사용자 지정 정책을 포함](deploy-custom-policies-devops.md)할 수 있습니다. 개발, 테스트 및 프로덕션과 같은 다양한 Azure AD B2C 환경에 배포하는 경우 Azure Pipelines를 사용하여 수동 프로세스를 제거하고 자동화된 테스트를 수행하는 것이 좋습니다.

## <a name="prepare-your-environment"></a>환경 준비

다음과 같이 Azure AD B2C 사용자 지정 정책을 시작합니다.

1. [Azure AD B2C 테넌트 만들기](tutorial-create-tenant.md)
1. 정책을 테스트할 수 있도록 Azure Portal 사용하여 [웹 애플리케이션을 등록](tutorial-register-applications.md)합니다.
1. 필요한 [정책 키](tutorial-create-user-flows.md?pivots=b2c-custom-policy#add-signing-and-encryption-keys)를 추가하고 [Identity Experience Framework 애플리케이션을 등록](tutorial-create-user-flows.md?pivots=b2c-custom-policy#register-identity-experience-framework-applications)합니다.
1. [Azure AD B2C 정책 스타터 팩을 다운로드](tutorial-create-user-flows.md?pivots=b2c-custom-policy#get-the-starter-pack)하여 테넌트에 업로드합니다. 
1. 스타터 팩을 업로드한 후에는 [가입 또는 로그인 정책을 테스트](tutorial-create-user-flows.md?pivots=b2c-custom-policy#test-the-custom-policy)합니다.
1. [VS Code(Visual Studio Code)](https://code.visualstudio.com/)를 다운로드하여 설치하는 것이 좋습니다. Visual Studio Code는 데스크톱에서 실행되며 Windows, macOS 및 Linux에 사용할 수 있는 간단하지만 강력한 소스 코드 편집기입니다. VS Code를 사용하면 [VS Code용 Azure AD B2C 확장](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)을 설치하여 Azure AD B2C 사용자 지정 정책 XML 파일을 빠르게 탐색하고 편집할 수 있습니다.
 
## <a name="next-steps"></a>다음 단계

Azure AD B2C 정책을 설정하고 테스트한 후에는 정책을 사용자 지정할 수 있습니다. 다음 문서에서 방법을 알아보세요.

- 사용자 지정 정책을 사용하여 [클레임 추가 및 사용자 입력 사용자 지정](./configure-user-input.md). 스타터 팩 기술 프로필의 일부를 사용자 지정하여 클레임을 정의하고 사용자 인터페이스에 클레임을 추가하는 방법을 알아봅니다.
- 사용자 지정 정책을 사용하여 애플리케이션의 [사용자 인터페이스 사용자 지정](customize-ui-with-html.md). 고유의 HTML 콘텐츠를 만들고 콘텐츠 정의를 사용자 지정하는 방법을 알아봅니다.
- 사용자 지정 정책을 사용하여 애플리케이션의 [사용자 인터페이스 지역화](./language-customization.md). 지역화된 리소스 요소를 추가하여 지원되는 언어 목록을 설정하고 언어별 레이블을 제공하는 방법을 알아봅니다.
- 정책 개발 및 테스트 중에 [이메일 확인을 사용하지 않도록 설정](./disable-email-verification.md)할 수 있습니다. 기술 프로필 메타데이터를 덮어쓰는 방법을 알아봅니다.
- 사용자 지정 정책을 사용하여 [Google 계정으로 로그인하도록 설정](./identity-provider-google.md). OAuth2 기술 프로필을 사용하여 새 클레임 공급자를 만드는 방법을 알아봅니다. 그런 다음, Google 로그인 옵션을 포함하도록 사용자 경험을 사용자 지정합니다.
- 사용자 지정 정책의 문제를 진단하려면 [Application Insights 사용하여 Azure Active Directory B2C 로그 수집](troubleshoot-with-application-insights.md). 새 기술 프로필을 추가하고 신뢰 당사자 정책을 구성하는 방법을 알아봅니다.
