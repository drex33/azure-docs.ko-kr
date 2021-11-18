---
title: '자습서: SAP Qualtrics와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 SAP Qualtrics 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 7ebc4387dccd7a434602ab6ad8f9713f58300233
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307083"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>자습서: SAP Qualtrics와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP Qualtrics를 통합하는 방법을 알아봅니다. Azure AD와 SAP Qualtrics를 통합하면 다음을 수행할 수 있습니다.

* SAP Qualtrics에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 SAP Qualtrics에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SSO(Single Sign-On)에 대해 SAP Qualtrics 구독을 사용하도록 설정했습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* SAP Qualtrics에서 **SP** 및 **IDP** 시작 SSO를 지원합니다.
* SAP Qualtrics에서 **Just In Time** 사용자 프로비저닝을 지원합니다.

## <a name="add-sap-qualtrics-from-the-gallery"></a>갤러리에서 SAP Qualtrics 추가

SAP Qualtrics의 Azure AD 통합을 구성하려면 갤러리의 SAP Qualtrics를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **SAP Qualtrics** 를 입력합니다.
1. 결과에서 **SAP Qualtrics** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>SAP Qualtrics용 Azure AD Single Sign-On 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 SAP Qualtrics에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SAP Qualtrics의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SAP Qualtrics에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. [Azure AD SSO 구성](#configure-azure-ad-sso) - 사용자가 이 기능을 사용할 수 있도록 구성합니다.
    1. [Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
    1. [Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. [SAP Qualtrics SSO 구성](#configure-sap-qualtrics-sso) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. [SAP Qualtrics 테스트 사용자 만들기](#create-sap-qualtrics-test-user) - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SAP Qualtrics에 만듭니다.
1. [SSO 테스트](#test-sso) - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **SAP Qualtrics** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾습니다. **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 선택하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **SAML로 Single Sign-On 설정** 페이지에서 다음 필드 값을 입력합니다.
    
    a. **식별자** 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다.

    `https://< DATACENTER >.qualtrics.com`
   
    b. **회신 URL** 텍스트 상자에서 다음 패턴을 사용하는 URL을 입력합니다.

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    다. **릴레이 상태** 텍스트 상자에서 다음 패턴을 사용하는 URL을 입력합니다.

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 을 선택하고 다음 단계를 수행합니다.

    **로그온 URL** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 식별자, 회신 URL 및 릴레이 상태로 이러한 값을 업데이트합니다. 이러한 값을 가져오려면 [Qualtrics 클라이언트 지원 팀](https://www.qualtrics.com/support/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 아이콘을 선택하여 **앱 페더레이션 메타데이터 URL** 을 복사하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, 암호를 적어둡니다.
   1. **만들기** 를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 SAP Qualtrics에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 차례로 선택합니다.
1. 애플리케이션 목록에서 **SAP Qualtrics** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고, **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택합니다. 화면 하단에서 **선택** 을 누릅니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택합니다. 화면 하단에서 **선택** 을 누릅니다.
1. **할당 추가** 대화 상자에서 **할당** 을 선택합니다.

## <a name="configure-sap-qualtrics-sso"></a>SAP Qualtrics SSO 구성

SAP Qualtrics 쪽에서 Single Sign-On을 구성하려면 Azure Portal에서 복사한 **앱 페더레이션 메타데이터 Url** 을 [SAP Qualtrics 지원 팀](https://www.qualtrics.com/support/)으로 보냅니다. 지원 팀은 SAML SSO 연결이 양쪽에서 올바르게 설정되었는지 확인합니다.

### <a name="create-sap-qualtrics-test-user"></a>SAP Qualtrics 테스트 사용자 만들기

SAP Qualtrics는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 수행할 추가 작업이 없습니다. SAP Qualtrics에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 SAP Qualtrics 로그온 URL로 리디렉션됩니다.  

* SAP Qualtrics 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 SAP Qualtrics에 자동으로 로그인됩니다.

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 SAP Qualtrics 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 SAP Qualtrics에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

SAP Qualtrics를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. 자세한 내용은 [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법 알아보기](/cloud-app-security/proxy-deployment-any-app)를 참조하세요.
