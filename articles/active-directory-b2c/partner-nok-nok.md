---
title: Nok Nok을 사용하여 Azure Active Directory B2C를 구성하는 방법에 대한 자습서
titleSuffix: Azure AD B2C
description: 암호 없는 FIDO2 인증을 사용하도록 Azure Active Directory B2C에서 Nok Nok을 구성하는 자습서
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/04/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 586c8373e32f210d6f2f53c773fbe58ef5a2181b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536258"
---
# <a name="tutorial-configure-nok-nok-with-azure-active-directory-b2c-to-enable-passwordless-fido2-authentication"></a>자습서: 암호 없는 FIDO2 인증을 사용하도록 Azure Active Directory B2C에서 Nok Nok 구성

이 샘플 자습서에서는 Nok Nok S3 인증 도구 모음을 Azure AD(Active Directory) B2C 테넌트에 통합하는 방법을 알아봅니다. [Nok Nok](https://noknok.com/)은 모바일 및 웹 애플리케이션에 대해 FIDO UAF, FIDO U2F, WebAuthn 및 FIDO2와 같은 FIDO 공인 다단계 인증을 사용하도록 설정합니다. Nok Nok 고객은 사용자 환경을 분산하는 동시에 보안 수준을 높일 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

- Azure 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 Azure 구독에 연결

- 무료 Nok Nok [평가판 테넌트](https://noknok.com/products/strong-authentication-service/)를 받습니다.

## <a name="scenario-description"></a>시나리오 설명

사용자에 대해 암호 없는 FIDO 인증을 사용하려면 Azure AD B2C 테넌트에 ID 공급자로 Nok Nok을 사용하도록 설정합니다. Nok Nok 통합에는 다음 구성 요소가 포함됩니다.

- **Azure AD B2C** – 사용자의 자격 증명 확인을 담당하는 권한 부여 서버입니다.

- **웹 및 모바일 애플리케이션** - Nok Nok과 Azure AD B2C를 사용하여 보호하도록 선택한 모바일 또는 웹 애플리케이션입니다.

- **Nok nok 앱 SDK 또는 Nok Nok Passport 앱** – Azure AD B2C 기반 애플리케이션을 인증하는 데 사용할 애플리케이션입니다. 이 애플리케이션은 [Apple App Store](https://apps.apple.com/us/app/nok-nok-passport/id1050437340) 및 [Google Play Store](https://play.google.com/store/apps/details?id=com.noknok.android.passport2&hl=en&gl=US)에서 제공합니다.

다음 아키텍처 다이어그램은 구현된 상황을 보여 줍니다. Nok Nok은 OIDC(Open ID Connect)를 사용하여 Azure AD B2C에 대한 ID 공급자 역할을 수행함으로써 암호 없는 인증을 사용하도록 설정합니다.

![Nok Nok 및 Azure AD B2C의 아키텍처 다이어그램을 보여 주는 이미지](./media/partner-nok-nok/nok-nok-architecture-diagram.png)

| 단계 | 설명 |
|:------|:-----------|
| 1. | 사용자가 로그인 페이지를 엽니다. 사용자가 로그인/등록을 선택하고 사용자 이름을 입력합니다. |
| 2. | Azure AD B2C가 사용자를 Nok Nok OIDC 인증 공급자로 리디렉션합니다. |
| 3a. | 모바일 기반 인증의 경우 Nok Nok은 QR 코드를 표시하거나, 최종 사용자의 모바일 디바이스에 푸시 알림 요청을 보냅니다. |
| 3b. | 데스크톱/PC 기반 로그인의 경우 Nok Nok은 최종 사용자를 웹 애플리케이션 로그인 페이지로 리디렉션하여 암호 없는 인증 프롬프트를 시작합니다. |
|4a. | 사용자는 Nok Nok 앱 SDK 또는 Nok Nok Passport 앱을 사용하여 스마트폰에 표시된 QR 코드를 스캔합니다.|
| 4b. | 사용자는 웹 애플리케이션의 로그인 페이지에 사용자 이름을 입력하고 다음을 선택합니다. |
| 5a. | 사용자의 스마트폰에 인증을 요청하는 메시지가 표시됩니다. <BR> 사용자는 생체 인식, 디바이스 PIN 또는 기타 로밍 인증자를 통해 암호 없는 인증을 수행합니다.|
| 5b. | 사용자의 웹 애플리케이션에 인증을 요청하는 메시지가 표시됩니다. <BR> 사용자는 생체 인식, 디바이스 PIN 또는 기타 로밍 인증자를 통해 암호 없는 인증을 수행합니다. |
| 6. | Nok Nok 서버는 FIDO 어설션의 유효성을 검사하고 유효성 검사 시 Azure AD B2C에 OIDC 인증 응답을 보냅니다.|
| 7. | 응답에 따라 사용자에게 액세스 권한이 부여되거나 거부됩니다. |

## <a name="onboard-with-nok-nok"></a>Nok Nok 온보딩

[Nok Nok 클라우드 양식](https://noknok.com/contact/)을 작성하여 사용자 고유의 Nok Nok 테넌트를 만듭니다. 양식을 제출하고 나면 테넌트 액세스 방법을 설명하는 이메일을 받게 됩니다. 이 이메일에는 Nok Nok 가이드 액세스도 포함됩니다. Nok Nok 통합 가이드에서 제공하는 지침에 따라 Nok Nok 클라우드 테넌트의 OIDC 구성을 완료합니다.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

### <a name="add-a-new-identity-provider"></a>새 ID 공급자 추가

새 ID 공급자를 추가하려면 다음 단계를 따르세요.

1. Azure AD B2C 테넌트의 전역 관리자로 **[Azure Portal](https://portal.azure.com/#home)** 에 로그인합니다.

2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.

3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.

4. **대시보드** > **Azure Active Directory B2C** >  **ID 공급자** 로 이동합니다.

5. **ID 공급자** 를 선택합니다.

6. **추가** 를 선택합니다.

### <a name="configure-an-identity-provider"></a>ID 공급자 구성 

ID 공급자를 구성하려면 다음 단계를 따르세요.

1. **ID 공급자 유형** > **OpenID 연결(미리 보기)** 를 선택합니다.
2. 양식을 작성하여 ID 공급자를 설정합니다.

   |속성 | 값 |
   |:-----| :-----------|
   | Name   | Nok Nok 인증 공급자 |
   | 메타데이터 URL | 호스트된 Nok Nok 인증 앱의 URI와, 이어 특정 경로(예: ‘ https://demo.noknok.com/mytenant/oidc/.well-known/openid-configuration ’)를 삽입합니다. |
   | 클라이언트 암호 | Nok Nok 플랫폼에서 제공하는 클라이언트 암호를 사용합니다.|
   | 클라이언트 ID | Nok Nok 플랫폼에서 제공하는 클라이언트 ID를 사용합니다.|
   | 범위 | OpenID 프로필 이메일 |
   | 응답 형식 | code |
   | 응답 모드 | form_post|

3. **확인** 을 선택합니다.

4. **이 ID 공급자의 클레임 매핑** 을 선택합니다.

5. 양식을 작성하여 ID 공급자를 매핑합니다.

   |속성 | 값 |
   |:-----| :-----------|
   | UserID    | 원본 구독 |
   | 표시 이름 | 원본 구독 |
   | 응답 모드 | 원본 구독 |

6. **저장** 을 선택하여 새 OIDC ID 공급자에 대한 설정을 완료합니다.

### <a name="create-a-user-flow-policy"></a>사용자 흐름 정책 만들기

이제 B2C ID 공급자 내에 나열된 새 OIDC ID 공급자로 Nok Nok이 표시됩니다.

1. Azure AD B2C 테넌트에서 **Policies** 아래 **사용자 흐름** 을 선택합니다.

2. **새** 사용자 흐름 선택

3. **등록 및 로그인** 을 선택하고, **버전** 을 선택한 다음, **만들기** 를 선택합니다.

4. 정책의 **이름** 을 입력합니다.

5. ID 공급자 섹션에서 새로 만든 Nok Nok ID 공급자를 선택합니다.

6. 사용자 흐름의 매개 변수를 설정합니다. 이름을 삽입하고 만든 ID 공급자를 선택합니다. 메일 주소를 추가할 수도 있습니다. 이 경우 Azure는 로그인 프로시저를 키 Nok Nok으로 직접 리디렉션하지 않습니다. 대신 사용자가 사용하려는 옵션을 선택할 수 있는 화면이 표시됩니다.

7. **Multi-factor Authentication** 필드는 그대로 둡니다.

8. **조건부 액세스 정책 적용** 을 선택합니다.

9. **사용자 특성 및 토큰 클레임** 아래, 특성 수집 옵션에서 **메일 주소** 를 선택합니다. Azure AD B2C가 클라이언트 애플리케이션에 반환할 수 있는 클레임과 함께 Azure AD에서 사용자에 대한 정보를 수집할 수 있는 모든 특성을 추가할 수 있습니다.

10. **만들기** 를 선택합니다.

11. 생성이 완료되면 새 **사용자 흐름** 을 선택합니다.

12. 왼쪽 창에서 **애플리케이션 클레임** 을 선택합니다. 옵션에서 **메일** 확인란을 선택하고 **저장** 을 선택합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. Azure AD B2C 테넌트를 열고 정책에서 ID Experience Framework를 선택합니다.

2. 이전에 만든 SignUpSignIn을 선택합니다.

3. 사용자 흐름 실행을 선택하고 설정을 선택합니다.

   a. 애플리케이션: 등록된 앱을 선택합니다(JWT는 샘플).

   b. 회신 URL: 리디렉션 URL을 선택합니다.

   다. 사용자 흐름 실행을 선택합니다.

4. 등록 흐름으로 이동하여 계정 만들기

5. 사용자 특성을 만든 후 흐름 중에 Nok Nok이 호출됩니다. 흐름이 불완전한 경우 사용자가 디렉터리에 저장되지 않았는지 확인합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
