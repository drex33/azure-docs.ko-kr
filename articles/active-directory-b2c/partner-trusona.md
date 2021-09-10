---
title: Trusona 및 Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Azure AD B2C에서 ID 공급자로 Trusona를 추가하여 암호 없는 인증을 사용하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2efd5fdc49f5ae6724b9d1dc3b45d54329f17a61
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113223953"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>Azure Active Directory B2C와 Trusona 통합

Trusona는 암호 없는 인증, 다단계 인증 및 디지털 라이선스 검색을 사용하여 로그인을 보호하는 ISV(독립 소프트웨어 공급업체) 공급자입니다. 이 문서에서는 Trusona를 Azure AD B2C의 ID 공급자로 추가하여 암호 없는 인증을 사용하도록 설정하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Azure 구독에 연결된 [Azure AD B2C 테넌트](tutorial-create-tenant.md)
* Trusona의 [체험 계정](https://www.trusona.com/)

## <a name="scenario-description"></a>시나리오 설명

이 시나리오에서 Trusona는 Azure AD B2C의 ID 공급자 역할을 하여 암호 없는 인증을 사용하도록 설정합니다. 솔루션의 구성 요소는 다음과 같습니다.

* 로그인 및 등록 정책이 결합된 Azure AD B2C
* ID 공급자로 Azure AD B2C에 추가된 Trusona
* 다운로드 가능한 Trusona 앱

![Trusona 아키텍처 다이어그램](media/partner-trusona/trusona-architecture-diagram.png)

| 단계 | 설명 |
|------|------|
|1     | 사용자가 애플리케이션에 로그인하거나 등록하려고 합니다. 사용자는 Azure AD B2C 등록 및 로그인 정책을 통해 인증됩니다. 등록하는 동안 Trusona 앱에서 이전에 확인된 사용자의 이메일 주소가 사용됩니다.     |
|2     | Azure B2C는 암시적 흐름을 사용하여 사용자를 Trusona OIDC(OpenID Connect) ID 공급자로 리디렉션합니다.     |
|3     | 데스크톱 PC 기반 로그인의 경우 Trusona는 Trusona 앱을 사용하여 검사하기 위한 고유한 상태 비저장, 애니메이션 및 동적 QR 코드를 표시합니다. 모바일 기반 로그인의 경우 Trusona는 "딥 링크"를 사용하여 Trusona 앱을 엽니다. 이러한 두 가지 방법은 디바이스 및 궁극적으로 사용자 검색에 사용됩니다.     |
|4     | 사용자는 Trusona 앱을 사용하여 표시된 QR 코드를 검사합니다.     |
|5     | 사용자의 계정이 Trusona 클라우드 서비스에서 발견되고 인증이 준비됩니다.     |
|6     | Trusona 클라우드 서비스는 Trusona 앱에 전송된 푸시 알림을 통해 사용자에게 인증 챌린지를 발급합니다.<br>a. 사용자에게 인증 챌린지를 묻는 메시지가 표시됩니다. <br> b. 사용자가 챌린지를 수락하거나 거부하도록 선택합니다. <br> 다. 사용자에게 OS 보안(예: 생체 인식, 암호, PIN 또는 패턴)을 사용하여 보안 Enclave/신뢰할 수 있는 실행 환경에서 프라이빗 키로 챌린지를 확인하고 서명하라는 메시지가 표시됩니다. <br> d. Trusona 앱은 실시간으로 인증의 매개 변수를 기반으로 동적 재생 방지 페이로드를 생성합니다. <br> e. 전체 응답은 보안 Enclave/신뢰할 수 있는 실행 환경의 프라이빗 키로 서명(두 번째로)되고 확인을 위해 Trusona 클라우드 서비스로 반환됩니다.      |
|7     |  Trusona 클라우드 서비스는 id_token을 사용하여 사용자를 시작 애플리케이션으로 다시 리디렉션합니다. Azure AD B2C는 ID 공급자를 설정하는 동안 구성된 Trusona의 게시된 OpenID 구성을 사용하여 id_token을 확인합니다.    |
|  |  |

## <a name="onboard-with-trusona"></a>Trusona로 온보딩

1. [양식](https://www.trusona.com/)을 작성하여 Trusona 계정을 만들고 시작합니다.

2. 앱 스토어에서 Trusona 모바일 앱을 다운로드합니다. 앱을 설치하고 이메일을 등록합니다.

3. 소프트웨어에서 보낸 보안 "매직 링크"를 통해 이메일을 확인합니다.  

4. 셀프 서비스에 대한 [Trusona Developer의 대시보드](https://dashboard.trusona.com)로 이동합니다.

5. **준비됨** 을 선택하고 Trusona 앱을 사용하여 직접 인증합니다.

6. 왼쪽 탐색 패널에서 **OIDC 통합** 을 선택합니다.

7. **OpenID Connect 통합 만들기** 를 선택합니다.

8. 선택한 **이름** 을 입력하고 **클라이언트 리디렉션 호스트 필드** 에서 이전에 제공한 도메인 정보(예: Contoso)를 사용합니다.  

   > [!NOTE]
   > Azure Active Directory의 초기 도메인 이름이 클라이언트 리디렉션 호스트로 사용됩니다.

9. [Trusona 통합 가이드](https://docs.trusona.com/integrations/aad-b2c-integration/)의 지침을 따릅니다. 메시지가 표시되면 이전 단계에서 언급한 초기 도메인 이름(예: Contoso)을 사용합니다.  

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

### <a name="add-a-new-identity-provider"></a>새 ID 공급자 추가

> [!NOTE]
> Azure 구독에 연결된 [Azure AD B2C 테넌트](tutorial-create-tenant.md)가 아직 없으면 만듭니다.

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.

3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.

4. **대시보드** > **Azure Active Directory B2C** > **ID 공급자** 로 이동합니다.

3. **ID 공급자** 를 선택합니다.

4. **추가** 를 선택합니다.

### <a name="configure-an-identity-provider"></a>ID 공급자 구성  

1. **ID 공급자 유형** > **OpenID Connect(미리 보기)** 를 선택합니다.

2. 양식을 작성하여 ID 공급자를 설정합니다.  

   | 속성 | 값  |
   | :--- | :--- |
   | 메타데이터 URL | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | 클라이언트 ID | Trusona에서 사용자에게 이메일을 전송합니다. |
   | 범위 | OpenID 프로필 이메일 |
   | 응답 형식 | Id_token |
   | 응답 모드  | Form_post |

3. **확인** 을 선택합니다.  

4. **이 ID 공급자의 클레임 매핑** 을 선택합니다.  

5. 양식을 작성하여 ID 공급자를 매핑합니다.

   | 속성 | 값  |
   | :--- | :--- |
   | UserID | Sub  |
   | 표시 이름 | nickname |
   | 이름 | given_name |
   | Surname | Family_name |
   | 응답 모드 | 이메일 |

6. **확인** 을 선택하여 새 OIDC ID 공급자에 대한 설정을 완료합니다.

### <a name="create-a-user-flow-policy"></a>사용자 흐름 정책 만들기

이제 B2C ID 공급자 내에 나열된 **새 OpenID Connect ID 공급자** 로 Trusona가 표시됩니다.

1. Azure AD B2C 테넌트에서 **정책** 아래 **사용자 흐름** 을 선택합니다.

1. **새 사용자 흐름** 을 선택합니다.

1. **등록 및 로그인** 을 선택하고, 버전을 선택한 다음 **만들기** 를 선택합니다.

1. 정책의 **이름** 을 입력합니다.

1. **ID 공급자** 섹션에서 새로 만든 **Trusona ID 공급자** 를 선택합니다.

   > [!NOTE]
   > Trusona는 본질적으로 다단계이므로 다단계 인증을 사용하지 않도록 설정하는 것이 좋습니다(Trusona는 다단계가 기본값).

1. **만들기** 를 선택합니다.

1. **사용자 특성 및 클레임** 에서 **자세히 표시** 를 선택합니다. 양식에서 이전 섹션에서 ID 공급자를 설정하는 동안 지정한 특성을 하나 이상 선택합니다.

1. **확인** 을 선택합니다.  

### <a name="test-the-policy"></a>정책 테스트

1. 만든 정책을 선택합니다.

1. **사용자 흐름 실행** 을 선택하고 설정을 선택합니다.

   1. **애플리케이션**: 등록된 앱을 선택합니다.
 
   1. **회신 URL**: 리디렉션 URL을 선택합니다.
   
1. **사용자 흐름 실행** 을 선택합니다. Trusona OIDC 게이트웨이로 리디렉션해야 합니다. Trusona 게이트웨이에서 Trusona 모바일 SDK를 사용하여 Trusona 앱 또는 사용자 지정 앱으로 표시된 보안 QR 코드를 검사합니다.

1. 보안 QR 코드를 검사한 후 정의한 회신 URL로 리디렉션해야 합니다.

## <a name="next-steps"></a>다음 단계  

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](custom-policy-overview.md)

- [AAD B2C에서 사용자 지정 정책 시작](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
