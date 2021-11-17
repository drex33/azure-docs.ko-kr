---
title: '자습서: Catchpoint와 Azure Active Directory SSO(Single Sign-On) 통합'
description: Azure Active Directory와 Catchpoint 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 7c313863cc4c25a7285c9c682225259e67235bd0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132326819"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>자습서: Catchpoint와 Azure Active Directory Single Sign-On 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Catchpoint를 통합하는 방법을 알아봅니다. Azure AD와 Catchpoint를 연결하는 경우 다음을 수행할 수 있습니다.

- Catchpoint에 대한 사용자 액세스를 Azure AD에서 제어합니다.
- Azure AD 계정을 사용하여 자동으로 로그인하도록 Catchpoint 로그인을 설정합니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
- SSO(Single Sign-On)가 설정된 Catchpoint 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

- Catchpoint에서 SP 시작 및 IDP 시작 SSO를 지원합니다.
- Catchpoint에서 Just-In-Time 사용자 프로비저닝을 지원합니다.

## <a name="add-catchpoint-from-the-gallery"></a>갤러리에서 Catchpoint 추가

Catchpoint의 Azure AD 통합을 구성하려면 Catchpoint를 관리형 SaaS 앱 목록에 추가합니다.

1. 회사, 학교 또는 개인 Microsoft 계정을 사용하여 Azure Portal에 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Catchpoint** 를 입력합니다.
1. 결과 패널에서 **Catchpoint** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-catchpoint"></a>Catchpoint에 대한 Azure AD SSO 구성 및 테스트

SSO가 작동하려면 Azure AD 사용자를 Catchpoint의 사용자와 연결해야 합니다. 이 자습서에서는 **B.Simon** 이라는 테스트 사용자를 구성합니다.

다음 섹션을 완료합니다.

1. [Azure AD SSO 구성](#configure-azure-ad-sso) - 사용자가 이 기능을 사용할 수 있도록 설정합니다.
   - [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
   - [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. [Catchpoint SSO 구성](#configure-catchpoint-sso) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
   - [Catchpoint 테스트 사용자 만들기](#create-a-catchpoint-test-user) - B.Simon Azure AD 테스트 계정을 Catchpoint의 비슷한 사용자 계정에 연결하도록 허용합니다.
1. [SSO 테스트](#test-sso) - 구성이 작동하는지 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 다음 단계를 수행하여 Azure AD SSO를 사용하도록 설정합니다.

1. Azure Portal에 로그인합니다.
1. **Catchpoint** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 연필 아이콘을 선택하여 **기본 SAML 구성** 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. 다음과 같이 Catchpoint에 대한 시작 모드를 구성합니다.

   - **IDP** 시작 모드의 경우 다음 필드의 값을 입력합니다.
     - **식별자**: `https://portal.catchpoint.com/SAML2`
     - **회신 URL**: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - **SP** 시작 모드의 경우 **추가 URL 설정** 을 선택하고 다음 값을 입력합니다.
     - **로그온 URL**: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Catchpoint 애플리케이션은 특정 형식의 SAML 어설션이 필요합니다. SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가합니다. 다음 표에는 기본 특성 목록이 나와 있습니다.

   | 속성                   | 원본 특성       |
   | ---------------------- | ---------------------- |
   | Givenname              | user.givenneame        |
   | Surname                | user.surname           |
   | Emailaddress           | user.mail              |
   | 속성                   | user.userprincipalname |
   | 고유한 사용자 ID | user.userprincipalname |

   ![사용자 특성 및 클레임 목록 스크린샷](common/default-attributes.png)

1. 또한 Catchpoint 애플리케이션은 SAML 응답에서 다른 특성이 전달되어야 합니다. 다음 표를 참조하세요. 이 특성도 미리 채워져 있지만, 검토 후 요구 사항에 맞게 업데이트할 수 있습니다.

   | 속성      | 원본 특성  |
   | --------- | ----------------- |
   | 네임스페이스 | user.assignedrole |

   > [!NOTE]
   > `namespace` 클레임은 계정 이름과 매핑되어야 합니다. 이 계정 이름은 Azure AD의 역할로 설정되어 SAML 응답에서 다시 전달되어야 합니다. Azure AD의 역할에 대한 자세한 내용은 [엔터프라이즈 애플리케이션의 SAML 토큰에서 발급된 역할 클레임 구성](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui)을 참조하세요.

1. **SAML로 Single Sign-On 설정** 페이지로 이동합니다. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾습니다. **다운로드** 를 선택하여 인증서를 컴퓨터에 저장합니다.

   ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Catchpoint 설정** 섹션에서 이후 단계에 필요한 URL을 복사합니다.

   ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal을 사용하여 B.Simon이라는 Azure AD 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예를 들어 다음과 같이 입력합니다. `B.Simon@contoso.com`
   1. **암호 표시** 확인란을 선택합니다. 표시된 암호 값을 적어 둡니다.
   1. **만들기** 를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Catchpoint에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 차례로 선택합니다.
1. 애플리케이션 목록에서 **Catchpoint** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택합니다. 화면 하단에서 **선택** 을 클릭합니다.
1. 위에서 설명한 대로 역할을 설정한 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다.
1. **할당 추가** 대화 상자에서 **할당** 을 선택합니다.

## <a name="configure-catchpoint-sso"></a>Catchpoint SSO 구성

1. 다른 웹 브라우저 창에서 Catchpoint 애플리케이션에 관리자로 로그인합니다.

1. **설정** 아이콘을 클릭한 다음, **SSO ID 공급자** 를 선택합니다.

   ![SSO ID 공급자가 선택된 Catchpoint 설정 스크린샷](./media/catchpoint-tutorial/configuration1.png)

1. **Single Sign-On** 페이지에서 다음 필드에 값을 입력합니다.

   ![Catchpoint Single Sign On 페이지 스크린샷](./media/catchpoint-tutorial/configuration2.png)

   | 필드                        | 값                                                                                                           |
   | ---------------------------- | --------------------------------------------------------------------------------------------------------------- |
   | **Namespace**                | 올바른 네임스페이스 값을 입력합니다.                                                                                        |
   | **ID 공급자 발급자** | Azure Portal의 `Azure AD Identifier` 값을 입력합니다.                                                          |
   | **Single Sign On URL**       | Azure Portal의 `Login URL` 값을 입력합니다.                                                                    |
   | **MSSQLSERVER에 대한 프로토콜 속성**              | Azure Portal에서 다운로드한 `Certificate (Base64)` 파일의 콘텐츠입니다. 메모장을 사용하여 살펴보고 복사하세요. |

   **메타데이터 업로드** 옵션을 선택하여 **페더레이션 메타데이터 XML** 을 업로드할 수도 있습니다.

1. **저장** 을 선택합니다.

### <a name="create-a-catchpoint-test-user"></a>Catchpoint 테스트 사용자 만들기

Catchpoint는 기본적으로 사용하도록 설정된 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에는 작업 항목이 없습니다. Catchpoint의 사용자 중에 B.Simon이 아직 없는 경우 인증 후에 B.Simon이 만들어집니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

#### <a name="sp-initiated"></a>SP 시작:

- Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Catchpoint 로그온 URL로 리디렉션됩니다.

- Catchpoint 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

- Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Catchpoint에 자동으로 로그인됩니다.

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Catchpoint 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Catchpoint에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

> [!NOTE]
> 로그인 페이지를 통해 Catchpoint 애플리케이션에 로그인한 경우 **Catchpoint 자격 증명** 을 입력한 다음, **회사 자격 증명(SSO)** 필드에 유효한 **네임스페이스** 값을 입력하고 **로그인** 을 선택합니다.
>
> ![Catchpoint 구성](./media/catchpoint-tutorial/loginimage.png)

## <a name="next-steps"></a>다음 단계

Catchpoint가 구성되면 세션 제어를 적용할 수 있습니다. 이 예방 조치는 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호합니다. 세션 제어는 조건부 액세스가 확장된 것입니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
