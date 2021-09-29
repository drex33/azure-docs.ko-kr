---
title: '자습서: Miro와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Miro 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/26/2021
ms.author: jeedes
ms.openlocfilehash: 6e2b3c611c5bc06e29fb739f01dc69605509a63a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124795187"
---
# <a name="tutorial-integrate-miro-with-azure-active-directory"></a>자습서: Azure Active Directory와 Miro 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Miro를 통합하는 방법에 대해 알아봅니다. 이 자습서의 다른 버전은 help.miro.com에서 찾을 수 있습니다. Azure AD와 Miro를 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서 Miro에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Miro에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Miro SSO(Single Sign-On)이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. 
* Miro는 **SP 및 IDP** 시작 SSO 및 **Just-In-Time** 사용자 프로비저닝을 지원합니다.
* Miro는 [**자동** 사용자 프로비저닝 및 프로비저닝 해제](miro-provisioning-tutorial.md)(권장)를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-miro-from-the-gallery"></a>갤러리에서 Miro 추가

Miro의 Azure AD 통합을 구성하려면 갤러리에서 Miro를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Miro** 를 입력합니다.
1. 결과 패널에서 **Miro** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-miro"></a>Miro에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Miro에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Miro의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Miro에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Miro SSO 구성](#configure-miro-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Miro 테스트 사용자 만들기](#create-miro-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Miro에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Miro** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    **식별자** 텍스트 상자에서 `https://miro.com/` URL을 입력합니다.

5. 애플리케이션을 **SP** 시작 모드로 구성하려는 경우 **로그온 URL** 텍스트 상자에서 URL(`https://miro.com/sso/login/`)을 입력합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드하고 본인의 컴퓨터에 저장합니다. Miro 쪽에서 SSO를 구성하는 데 필요합니다.

   ![인증서 다운로드 링크](common/certificatebase64.png "인증서 다운로드 링크")

1. **Miro 설정** 섹션에서 로그인 URL을 복사합니다. Miro 쪽에서 SSO를 구성하는 데 필요합니다.

   ![로그인 URL 복사](./media/miro-tutorial/login.png "로그인 URL 복사")

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Miro에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Miro** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

* 또는 애플리케이션의 **속성** 으로 이동하여 **사용자 할당 필요** 를 해제합니다. 
![할당 요구 사항 사용 안 함](./media/miro-tutorial/properties.png "할당 요구 사항 사용 안 함")

## <a name="configure-miro-sso"></a>Miro SSO 구성

Miro 쪽에서 Single Sign-On을 구성하려면 이전에 다운로드한 인증서와 이전에 복사한 로그인 URL을 사용합니다. Miro 계정 설정에서 **보안** 섹션으로 이동하여 **SSO/SAML 사용** 을 설정합니다. 

1. 로그인 URL을 **SAML 로그인 URL** 필드에 붙여넣습니다.
1. 텍스트 편집기를 사용하여 인증서 파일을 열고, 인증서 순서를 복사합니다. 이 순서를 **키 x509 인증서** 필드에 붙여넣습니다.
![Miro 설정](./media/miro-tutorial/security.png "Miro 설정")

1. **도메인** 필드에서 도메인 주소를 입력하고, **추가** 를 클릭하고, 확인 절차를 따릅니다. 다른 도메인 주소가 있는 경우 이를 반복합니다. Miro SSO 기능은 목록에 있는 도메인의 최종 사용자를 위해 작동합니다. 
![도메인](./media/miro-tutorial/add-domain.png "도메인")

1. Just-In-Time 프로비전(사용자를 Miro에 등록하는 동안 구독으로 끌어오기)을 사용할지 여부를 결정하고, **저장** 을 클릭하여 Miro 쪽에서 SSO 구성을 완료합니다.
![Just-In-Time 프로비전](./media/miro-tutorial/save-configuration.png "Just-In-Time 프로비전") 

### <a name="create-miro-test-user"></a>Miro 테스트 사용자 만들기

이 섹션에서는 Miro에서 B.Simon이라는 사용자를 만듭니다. Miro는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에는 작업 항목이 없습니다. Miro에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 B.Simon 테스트 사용자를 사용하여 다음 옵션을 통해 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Miro 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하고, B.Simon으로 로그인하도록 선택합니다. 그러면 SSO를 설정한 Miro에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Miro 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Miro에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.