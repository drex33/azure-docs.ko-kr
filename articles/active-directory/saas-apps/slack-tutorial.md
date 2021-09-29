---
title: '자습서: Slack과 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 Slack 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: b6e5d89969ea37b9be7376e2d0443166a695312d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124752424"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>자습서: Slack과 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Slack을 연결하는 방법에 대해 알아봅니다. Azure AD와 Slack을 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서는 Slack에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Slack에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Slack SSO(Single Sign-On)가 설정된 구독

> [!NOTE]
> 하나의 테넌트에서 둘 이상의 Slack 인스턴스와 통합해야 하는 경우 각 애플리케이션의 식별자는 변수가 될 수 있습니다.

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Slack이 **SP** 에서 시작된 SSO를 지원
* Slack이 **JIT(Just-in-time)** 사용자 프로비전을 지원
* Slack이 [**자동화된** 사용자 프로비전](./slack-provisioning-tutorial.md)을 지원

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-slack-from-the-gallery"></a>갤러리에서 Slack 추가

Azure AD에 Slack을 통합하도록 구성하려면 갤러리의 Slack을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Slack** 을 입력합니다.
1. 결과 패널에서 **Slack** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-slack"></a>Slack에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Slack에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Slack의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Slack에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Slack SSO 구성](#configure-slack-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Slack 테스트 사용자 만들기](#create-slack-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Slack에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Slack** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<DOMAIN NAME>.slack.com/sso/saml/start` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에 `https://slack.com` URL을 입력합니다.
    
    다. **회신 URL** 에 다음 URL 패턴 중 하나를 입력합니다.
    
    | 회신 URL|
    |----------|
    | `https://<DOMAIN NAME>.slack.com/sso/saml` |
    | `https://<DOMAIN NAME>.enterprise.slack.com/sso/saml` |

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 회신 URL로 이러한 값을 업데이트해야 합니다. 값을 구하려면 [Slack 클라이언트 지원팀](https://slack.com/help/contact)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

    > [!NOTE]
    > 테넌트와 통합해야 하는 둘 이상의 Slack 인스턴스가 있는 경우 **식별자(엔터티 ID)** 의 값은 변수가 될 수 있습니다. `https://<DOMAIN NAME>.slack.com` 패턴을 사용합니다. 이 시나리오에서는 동일한 값을 사용하여 Slack의 다른 설정과도 쌍으로 연결해야 합니다.

1. Slack 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/edit-attribute.png)

1. 위에서 언급한 특성 외에도 Slack 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다. `email` 특성도 추가해야 합니다. 사용자에게 이메일 주소가 없는 경우 **emailaddress** 를 **user.userprincipalname** 에 매핑하고 **이메일** 을 **user.userprincipalname** 에 매핑합니다.

    | Name | 원본 특성 |
    | -----|---------|
    | emailaddress | user.userprincipalname |
    | 이메일 | user.userprincipalname |

   > [!NOTE]
   > SP(서비스 공급자) 구성을 설정하려면 SAML 구성 페이지에서 **고급 옵션** 옆의 있는 **확장** 을 클릭해야 합니다. **Service Provider Issuer** 상자에 작업 영역 URL을 입력합니다. 기본값은 slack.com입니다. 

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Slack 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Slack에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Slack** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.

1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-slack-sso"></a>Slack SSO 구성

1. Slack 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **Slack 설정** 을 클릭하면 Slack 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Slack에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-6단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. Slack을 수동으로 설정하려면 다른 웹 브라우저 창에서 관리자 권한으로 Slack 회사 사이트에 로그인합니다.

2. **Microsoft Azure AD**, **팀 설정** 으로 차례로 이동합니다.

     ![Microsoft Azure AD에서 Single Sign-On 구성](./media/slack-tutorial/tutorial-slack-team-settings.png)

3. **팀 설정** 섹션에서 **인증** 탭을 클릭한 다음 **설정 변경** 을 클릭합니다.

    ![팀 설정에서 Single Sign-On 설정 구성](./media/slack-tutorial/tutorial-slack-authentication.png)

4. **SAML 인증 설정** 대화 상자에서 다음 단계를 수행합니다.

    ![SAML 인증 설정에서 Single Sign-On 설정 구성](./media/slack-tutorial/tutorial-slack-save-authentication.png)

    a.  **SAML 2.0 엔드포인트(HTTP)** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    b.  **ID 공급자 발급자** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    다.  다운로드한 인증서 파일을 메모장에서 열고 내용을 클립보드에 복사한 다음, **공용 인증서** 텍스트 상자에 붙여넣습니다.

    d. 위의 세 가지 설정을 Slack 팀에 적합하게 구성합니다. 설정에 대한 자세한 내용은 **Slack의 SSO 구성 가이드** 를 참조하세요. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    ![앱 쪽에서 Single Sign-On 구성](./media/slack-tutorial/tutorial-slack-expand.png)

    e. **확장** 을 클릭하고 **서비스 공급자 인증서 발급자** 텍스트 상자에 `https://slack.com`을 입력합니다.

    f.  **구성 저장** 을 클릭하십시오.
    
    > [!NOTE]
    > Azure AD와 통합해야 하는 둘 이상의 Slack 인스턴스가 있는 경우 `https://<DOMAIN NAME>.slack.com`을 **서비스 공급자 인증서 발급자** 로 설정하여 Azure 애플리케이션 **식별자** 설정과 쌍으로 연결할 수 있습니다.

### <a name="create-slack-test-user"></a>Slack 테스트 사용자 만들기

이 섹션의 목적은 Slack에서 B.Simon이라는 사용자를 만드는 것입니다. Slack은 just-in-time 프로비전을 지원하며 기본적으로 사용하도록 설정됩니다. 이 섹션에 작업 항목이 없습니다. 새 사용자가 아직 존재하지 않는 경우 Slack에 액세스하는 동안 만들어집니다. Slack은 자동 사용자 프로비전도 지원합니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](slack-provisioning-tutorial.md)에서 제공합니다.

> [!NOTE]
> 사용자를 수동으로 만들어야 하는 경우 [Slack 지원 팀](https://slack.com/help/contact)에 문의해야 합니다.

> [!NOTE]
> Azure AD Connect는 온-프레미스 Active Directory ID를 Azure AD에 동기화할 수 있는 동기화 도구로, 이러한 동기화된 사용자는 다른 클라우드 사용자와 마찬가지로 애플리케이션을 사용할 수 있습니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Slack 로그온 URL로 리디렉션됩니다.

* Slack 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Slack 타일을 클릭하면 Slack 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Slack이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](/cloud-app-security/proxy-deployment-aad)