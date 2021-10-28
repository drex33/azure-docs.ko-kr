---
title: Azure Active Directory B2C를 사용하여 Keyless를 구성하기 위한 자습서
titleSuffix: Azure AD B2C
description: 암호 없는 인증을 위해 Azure Active Directory B2C를 사용하여 Keyless를 구성하기 위한 자습서
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: bc8bc67ca2fd7a023b68f6aa2e21527e26281dd3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588431"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 Keyless 구성하기

이 샘플 자습서에서는 [Keyless](https://keyless.io/)를 포함한 Azure Active Directory(AD) B2C 구성 방법에 대한 참고 자료를 제공합니다. Azure AD B2C를 ID 공급자로 사용하여 Keyless와 고객 애플리케이션을 통합하여 사용자에게 진정한 암호 없는 인증을 제공할 수 있습니다.

Keyless의 솔루션인 **Keyless Zero-Knowledge Biometric(ZKB™)** 은 고객 환경을 개선하고 개인 정보를 보호하는 동시에 사기 행위, 피싱 및 자격 증명 재사용을 없애는 암호 없는 다단계 인증을 제공합니다.

## <a name="pre-requisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

- Azure 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테넌트](./tutorial-create-tenant.md). 테넌트는 Azure 구독에 연결되어야 합니다.

- Keyless 클라우드 테넌트, 체험 [평가판 계정](https://keyless.io/go) 받기

- 사용자의 디바이스에 설치된 Keyless Authenticator 앱입니다.

## <a name="scenario-description"></a>시나리오 설명

Keyless 통합에는 다음 구성 요소가 포함됩니다.

- Azure AD B2C - 사용자의 자격 증명(ID 공급자라고도 함)을 확인하는 역할을 하는 권한 부여 서버입니다.

- 웹 및 모바일 애플리케이션 - Keyless와 Azure AD B2C를 사용하여 보호하도록 선택한 모바일 또는 웹 애플리케이션입니다.

- Keyless 모바일 앱 – Keyless 모바일 앱은 Azure AD B2C 지원 애플리케이션 인증에 사용됩니다.

다음 아키텍처 다이어그램에서 구현을 보여 줍니다.

![Keyless 아키텍처 다이어그램을 보여 주는 이미지](./media/partner-keyless/keyless-architecture-diagram.png)

|단계 | 설명 |
|:-----| :-----------|
| 1. | 사용자가 로그인 페이지에 들어옵니다. 사용자가 로그인/등록을 선택하고 사용자 이름을 입력합니다.
| 2. | 애플리케이션은 ID 확인을 위해 Azure AD B2C에 사용자 특성을 보냅니다.
| 3. | Azure AD B2C는 사용자 특성을 수집하고 Keyless에 대한 특성을 보내서 Keyless 모바일 앱으로 사용자를 인증합니다.
| 4. | Keyless는 얼굴 생체 인식 검사의 형태로 개인 정보 유지 인증을 위해 등록된 사용자의 모바일 디바이스에 푸시 알림을 보냅니다.
| 5. | 사용자가 푸시 알림에 응답한 후에 확인 결과에 따라 고객 애플리케이션에 대한 액세스 권한이 부여되거나 거부됩니다.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

### <a name="add-a-new-identity-provider"></a>새 ID 공급자 추가

새 ID 공급자를 추가하려면 다음 단계를 따르세요.

1. Azure AD B2C 테넌트의 전역 관리자로 **[Azure Portal](https://portal.azure.com/#home)** 에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **대시보드** > **Azure Active Directory B2C** >  **ID 공급자** 로 이동합니다.
1. **ID 공급자** 를 선택합니다.
1. **추가** 를 선택합니다.

### <a name="configure-an-identity-provider"></a>ID 공급자 구성

ID 공급자를 구성하려면 다음 단계를 따르세요.

1. **ID 공급자 유형** > **OpenID 연결(미리 보기)** 를 선택합니다.
1. 양식을 작성하여 ID 공급자를 설정합니다.

   |속성 | 값 |
   |:-----| :-----------|
   | 이름   | Keyless |
   | 메타데이터 URL | 호스트된 Keyless 인증 앱의 URI 뒤에 ‘ https://keyless.auth/.well-known/openid-configuration ’과 같은 특정 경로를 삽입합니다. |
   | 클라이언트 암호 | Keyless 인증 인스턴스와 연결된 암호입니다. 이전에 구성된 암호와 동일하지 않습니다. 선택한 복합 문자열을 삽입합니다. 이 암호는 나중에 Keyless 컨테이너 구성에서 사용됩니다.|
   | 클라이언트 ID | 클라이언트 ID입니다. 이 ID는 나중에 Keyless 컨테이너 구성에서 사용됩니다.|
   | 범위 | openid |
   | 응답 형식 | id_token |
   | 응답 모드 | form_post|

1. **확인** 을 선택합니다.

1. **이 ID 공급자의 클레임 매핑** 을 선택합니다.

1. 양식을 작성하여 ID 공급자를 매핑합니다.

   |속성 | 값 |
   |:-----| :-----------|
   | UserID    | 원본 구독 |
   | 표시 이름 | 원본 구독 |
   | 응답 모드 | 원본 구독 |

1. **저장** 을 선택하여 새 OIDC(Open ID 연결) ID 공급자에 대한 설정을 완료합니다.

### <a name="create-a-user-flow-policy"></a>사용자 흐름 정책 만들기

이제 B2C ID 공급자 내에 나열된 새 OIDC ID 공급자로 Keyless가 표시됩니다.

1. Azure AD B2C 테넌트에서 **Policies** 아래 **사용자 흐름** 을 선택합니다.

2. **새** 사용자 흐름 선택

3. **등록 및 로그인** 을 선택하고, **버전** 을 선택한 다음, **만들기** 를 선택합니다.

4. 정책의 **이름** 을 입력합니다.

5. ID 공급자 섹션에서 새로 만든 Keyless ID 공급자를 선택합니다.

6. 사용자 흐름의 매개 변수를 설정합니다. 이름을 삽입하고 만든 ID 공급자를 선택합니다. 메일 주소를 추가할 수도 있습니다. 이 경우 Azure는 로그인 프로시저를 키 Keyless로 직접 리디렉션하지 않습니다. 대신 사용자가 사용하려는 옵션을 선택할 수 있는 화면이 표시됩니다.

7. **Multi-factor Authentication** 필드는 그대로 둡니다.

8. **조건부 액세스 정책 적용** 을 선택합니다.

9. **사용자 특성 및 토큰 클레임** 아래, 특성 수집 옵션에서 **메일 주소** 를 선택합니다. Azure AD B2C가 클라이언트 애플리케이션에 반환할 수 있는 클레임과 함께 Azure Active Directory에서 사용자에 대한 정보를 수집할 수 있는 모든 특성을 추가할 수 있습니다.

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

5. 사용자 특성을 만든 후 흐름 중에 Keyless가 호출됩니다. 흐름이 불완전한 경우 사용자가 디렉터리에 저장되지 않았는지 확인합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](tutorial-create-user-flows.md?pivots=b2c-custom-policy)