---
title: '자습서: Eccentex AppBase for Azure와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Eccentex AppBase for Azure 간 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/02/2021
ms.author: jeedes
ms.openlocfilehash: d66b09aac2d03fe81cc38ffb53ac1bb29dae6f8f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124813619"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-eccentex-appbase-for-azure"></a>자습서: Eccentex AppBase for Azure와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Eccentex AppBase for Azure를 연결하는 방법을 알아봅니다. Eccentex AppBase for Azure를 Azure AD와 연결하면 다음을 수행할 수 있습니다.

* Eccentex AppBase for Azure에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Eccentex AppBase for Azure에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Eccentex AppBase for Azure SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Eccentex AppBase for Azure에서 **SP** 시작 SSO를 지원합니다.

* Eccentex AppBase for Azure에서 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

## <a name="add-eccentex-appbase-for-azure-from-the-gallery"></a>갤러리에서 Eccentex AppBase for Azure 추가

Eccentex AppBase for Azure의 Azure AD 통합을 구성하려면 갤러리의 Eccentex AppBase for Azure를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Eccentex AppBase for Azure** 를 입력합니다.
1. 결과 패널에서 **Eccentex AppBase for Azure** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-eccentex-appbase-for-azure"></a>Eccentex AppBase for Azure에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Eccentex AppBase for Azure에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Eccentex AppBase for Azure의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Eccentex AppBase for Azure에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Eccentex AppBase for Azure SSO 구성](#configure-eccentex-appbase-for-azure-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Eccentex AppBase for Azure 테스트 사용자 만들기](#create-eccentex-appbase-for-azure-test-user)** - B.Simon의 Azure AD 표현과 연결되는 해당 사용자를 Eccentex AppBase for Azure에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Eccentex AppBase for Azure** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자(엔터티 ID)** 텍스트 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다.

    | **식별자** |
    |--------|
    | `https://<CustomerName>.appbase.com/Ecx.Web` |
    | `https://<CustomerName>.eccentex.com:<PortNumber>/Ecx.Web` |

    b. **로그온 URL** 텍스트 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다.

    | **URL에 로그인** |
    |---------|
    | `https://<CustomerName>.appbase.com/Ecx.Web/Account/sso?tenantCode=<TenantCode>&authCode=<AuthConfigurationCode>`|
    | `https://<CustomerName>.eccentex.com:<PortNumber>/Ecx.Web/Account/sso?tenantCode=<TenantCode>&authCode=<AuthConfigurationCode>` |

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 해당 값을 실제 식별자 및 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [Eccentex AppBase for Azure 클라이언트 지원 팀](mailto:eccentex.support@eccentex.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(원시)** 를 찾고, **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificateraw.png)

1. **Eccentex AppBase for Azure 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Eccentex AppBase for Azure에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Eccentex AppBase for Azure** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 &quot;기본 액세스&quot; 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name=&quot;configure-eccentex-appbase-for-azure-sso&quot;></a>Eccentex AppBase for Azure SSO 구성

1. Eccentex AppBase for Azure 회사 사이트에 관리자 권한으로 로그인합니다.

1. **기어** 아이콘으로 이동하여 **사용자 관리** 를 클릭합니다.

    ![SAML 계정 설정을 보여주는 스크린샷](./media/eccentex-appbase-for-azure-tutorial/settings.png &quot;계정")

1. **사용자 관리** > **인증 구성** 으로 이동하여 **SAML 추가** 단추를 클릭합니다.

    ![SAML 설정을 보여주는 스크린샷](./media/eccentex-appbase-for-azure-tutorial/users.png "SAML 설정")

1. **새 SAML 구성** 페이지에서 다음 단계를 수행합니다.

    ![Azure SAML 구성을 보여주는 스크린샷](./media/eccentex-appbase-for-azure-tutorial/configuration.png "SAML 구성")

    1. **이름** 텍스트 상자에 짧은 구성 이름을 입력합니다. 

    1. **발급자 Url** 텍스트 상자에 Azure Portal에서 복사한 Azure **애플리케이션 ID** 를 입력합니다.

    1. **애플리케이션 Url** 값을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **식별자(엔터티 ID)** 텍스트 상자에 붙여넣습니다.

    1. **AppBase 새 사용자 온보딩** 의 드롭다운에서 **초대만** 을 선택합니다.

    1. **AppBase 인증 실패 동작** 의 드롭다운에서 **오류 페이지 표시** 를 선택합니다.

    1. 인증서 암호화에 따라 **서명 다이제스트 방법** 및 **서명 방법** 을 선택합니다.

    1. **인증서 사용** 드롭다운에서 **수동 업로드** 를 선택합니다.

    1. **인증 컨텍스트 클래스 이름** 의 드롭다운에서 **암호** 를 선택합니다.

    1. **서비스 공급자-ID 공급자 바인딩** 의 드롭다운에서 **HTTP-리디렉션** 을 선택합니다.

        > [!NOTE]
        > **아웃바운드 요청 서명** 을 선택하지 않습니다.

    1. **Assertion Consumer Service Url** 값을 복사하고, 이 값을 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **회신 URL** 텍스트 상자에 붙여넣습니다.

    1. **인증 요청 대상 Url** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    1. **서비스 공급자 리소스 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    1. **아티팩트 식별 Url** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    1. **인증 요청 프로토콜 바인딩** 의 드롭다운에서 **HTTP-POST** 를 선택합니다.

    1. **인증 요청 이름 ID 정책** 의 드롭다운에서 **영구** 를 선택합니다.

    1. **아티팩트 응답기 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    1. **응답 서명 확인 적용** 확인란을 선택합니다.

    1. Azure Portal에서 다운로드한 **인증서(Raw)** 를 메모장에서 열고, 그 내용을 **SAML 상호 인증서 업로드** 텍스트 상자에 붙여넣습니다.

    1. **로그아웃 응답 프로토콜 바인딩** 의 드롭다운에서 **HTTP-POST** 를 선택합니다.

    1. **AppBase 사용자 지정 로그아웃 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.
    
    1. **저장** 을 클릭합니다.

### <a name="create-eccentex-appbase-for-azure-test-user"></a>Eccentex AppBase for Azure 테스트 사용자 만들기

이 섹션에서는 Eccentex AppBase for Azure에서 Britta Simon이라는 사용자를 만듭니다. Eccentex AppBase for Azure는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Eccentex AppBase for Azure에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Eccentex AppBase for Azure 로그온 URL로 리디렉션됩니다. 

* Eccentex AppBase for Azure 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Eccentex AppBase for Azure 타일을 클릭하면 Eccentex AppBase for Azure 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Eccentex AppBase for Azure를 구성한 후에는 세션 제어를 적용하여 중요한 조직 데이터의 반출과 반입을 실시간으로 방지할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).