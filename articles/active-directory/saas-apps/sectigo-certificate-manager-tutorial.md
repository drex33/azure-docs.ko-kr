---
title: '자습서: Sectigo Certificate Manager와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Sectigo Certificate Manager 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2021
ms.author: jeedes
ms.openlocfilehash: 4b3283780e611e37ef6cdae1feb6eb4b22f8f255
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306627"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>자습서: Sectigo Certificate Manager와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Sectigo Certificate Manager를 통합하는 방법을 알아봅니다. Sectigo Certificate Manager를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Sectigo Certificate Manager에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자는 자신의 Azure AD 계정으로 Sectigo Certificate Manager에 자동으로 로그인되도록 합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

Sectigo Certificate Manager와 Azure AD 연결을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Sectigo Certificate Manager 계정

> [!NOTE]
> Sectigo Certificate Manager의 여러 인스턴스를 실행합니다. Sectigo Certificate Manager의 주 인스턴스는 **https:\//cert-manager.com** 이며, 이 URL은 이 자습서에서 사용됩니다.  계정이 다른 인스턴스에 있으면 이 URL을 적절하게 조정해야 합니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트한 후 Azure AD에 Sectigo Certificate Manager를 통합합니다.

Sectigo Certificate Manager는 다음 기능을 지원합니다.

* **SP 시작 Single Sign-On**.
* **IDP 시작 Single Sign-On**.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Azure Portal에서 Sectigo Certificate Manager 추가

Azure AD에 Sectigo Certificate Manager 연결을 구성하려면 갤러리에서 Sectigo Certificate Manager를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Sectigo Certificate Manager** 를 입력합니다.
1. 결과 패널에서 **Sectigo Certificate Manager** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-sectigo-certificate-manager"></a>Sectigo Certificate Manager용 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Sectigo Certificate Manager에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Sectigo Certificate Manager의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Sectigo Certificate Manager에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Sectigo Certificate Manager SSO 구성](#configure-sectigo-certificate-manager-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Sectigo Certificate Manager 테스트 사용자 만들기](#create-sectigo-certificate-manager-test-user)** - 사용자의 Azure AD 표현과 연결된 Sectigo Certificate Manager에서 B.Simon의 상대 사용자를 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Sectigo Certificate Manager** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    1. **식별자(엔터티 ID)** 상자에서 주 Sectigo Certificate Manager 인스턴스에 대해 **https:\//cert-manager.com/shibboleth** 를 입력합니다.

    1. **회신 URL** 상자에서 주 Sectigo Certificate Manager 인스턴스에 대해 **https:\//cert-manager.com/Shibboleth.sso/SAML2/POST** 를 입력합니다.
        
    > [!NOTE]
    > 일반적으로 **로그온 URL** 은 **SP 시작 모드** 에 필수이지만, Sectigo Certificate Manager에서 로그인할 필요는 없습니다.        

1. 필요에 따라 **기본 SAML 구성** 섹션에서 **IDP 시작 모드** 를 구성하고, **테스트** 가 작동하도록 허용하려면 다음 단계를 수행합니다.

    1. **추가 URL 설정** 을 선택합니다.

    1. **릴레이 상태** 상자에서 Sectigo Certificate Manager 고객별 URL을 입력합니다. 주 Sectigo Certificate Manager 인스턴스에 대해 **https:\//cert-manager.com/customer/\<customerURI\>/idp** 를 입력합니다.

1. **사용자 특성 및 클레임** 섹션에서 다음 단계를 수행합니다.

    1. **추가 클레임** 을 모두 삭제합니다.
    
    1. **새 클레임 추가** 를 선택하고, 다음 4개의 클레임을 추가합니다.
    
        | Name | 네임스페이스 | 원본 | 원본 특성 | Description |
        | --- | --- | --- | --- | --- |
        | eduPersonPrincipalName | 비어 있음 | attribute | user.userprincipalname | 관리자에 대한 Sectigo Certificate Manager의 **IdP Person ID(IdP 사용자 ID)** 필드와 일치해야 합니다. |
        | mail | 비어 있음 | attribute | user.mail | 필수 |
        | givenName | 비어 있음 | attribute | user.givenname | Optional |
        | sn | 비어 있음 | attribute | user.surname | Optional |

       ![Sectigo Certificate Manager - 4개의 새 클레임 추가](media/sectigo-certificate-manager-tutorial/additional-claims.png)

1. **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 옆에 있는 **다운로드** 를 선택합니다. XML 파일을 컴퓨터에 저장합니다.

    ![페더레이션 메타데이터 XML 다운로드 옵션](common/metadataxml.png)

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

이 섹션에서는 B.Simon에게 Azure Single Sign-On을 사용할 수 있도록 Sectigo Certificate Manager에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Sectigo Certificate Manager** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-sectigo-certificate-manager-sso"></a>Sectigo Certificate Manager SSO 구성

Sectigo Certificate Manager 쪽에서 Single Sign-On을 구성하려면 다운로드한 페더레이션 메타데이터 XML 파일을 [Sectigo Certificate Manager 지원 팀](https://sectigo.com/support)으로 보냅니다. Sectigo Certificate Manager 지원 팀은 사용자가 보내는 정보를 사용하여 SAML Single Sign-On 연결이 양쪽에서 제대로 설정되어 있는지 확인합니다.

### <a name="create-sectigo-certificate-manager-test-user"></a>Sectigo Certificate Manager 테스트 사용자 만들기

이 섹션에서는 Sectigo Certificate Manager에서 Britta Simon이라는 사용자를 만듭니다. [Sectigo Certificate Manager 지원 팀](https://sectigo.com/support)과 협력하여 Sectigo Certificate Manager 플랫폼에서 사용자를 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 Azure AD Single Sign-On 구성을 테스트합니다.

#### <a name="test-from-sectigo-certificate-manager-sp-initiated-single-sign-on"></a>Sectigo Certificate Manager에서 테스트(SP 시작 Single Sign-On)

고객별 URL(주 Sectigo Certificate Manager 인스턴스의 경우 https:\//cert-manager.com/customer/\<customerURI\>/)을 찾아 **Or Sign In With** 아래의 단추를 선택합니다.  올바르게 구성되면 Sectigo Certificate Manager에 자동으로 로그인됩니다.

#### <a name="test-from-azure-single-sign-on-configuration-idp-initiated-single-sign-on"></a>Azure Single Sign-On 구성에서 테스트(IDP 시작 Single Sign-On)

**Sectigo Certificate Manager** 애플리케이션 통합 창에서 **Single Sign-On**, **테스트** 단추를 차례로 선택합니다.  올바르게 구성되면 Sectigo Certificate Manager에 자동으로 로그인됩니다.

#### <a name="test-by-using-the-my-apps-portal-idp-initiated-single-sign-on"></a>내 앱 포털을 사용하여 테스트(IDP 시작 Single Sign-On)

내 앱 포털에서 **Sectigo Certificate Manager** 를 선택합니다.  올바르게 구성되면 Sectigo Certificate Manager에 자동으로 로그인됩니다. My Apps 포털에 대한 자세한 내용은 [My Apps 포털에서 앱 액세스 및 사용](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Sectigo Certificate Manager가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
