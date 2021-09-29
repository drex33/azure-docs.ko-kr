---
title: '자습서: CylancePROTECT와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory 및 CylancePROTECT 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: 6ed85566f1654e117c2e0871b44fcc14ea096060
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124783808"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cylanceprotect"></a>자습서: CylancePROTECT와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 CylancePROTECT를 통합하는 방법에 대해 알아봅니다. Azure AD와 CylancePROTECT를 통합하는 경우 다음을 수행할 수 있습니다.

* CylancePROTECT에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 CylancePROTECT에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* CylancePROTECT SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* CylancePROTECT에서 **IDP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-cylanceprotect-from-the-gallery"></a>갤러리에서 CylancePROTECT 추가

CylancePROTECT의 Azure AD 통합을 구성하려면 갤러리의 CylancePROTECT를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **CylancePROTECT** 를 입력합니다.
1. 결과 패널에서 **CylancePROTECT** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-cylanceprotect"></a>CylancePROTECT에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 CylancePROTECT에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 CylancePROTECT의 관련 사용자 간에 연결 관계를 설정해야 합니다.

CylancePROTECT에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[CylancePROTECT SSO 구성](#configure-cylanceprotect-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[CylancePROTECT 테스트 사용자 만들기](#create-cylanceprotect-test-user)** - Azure AD 표현과 연결된 B.Simon에 해당하는 사용자를 CylancePROTECT에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **CylancePROTECT** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **SAML로 Single Sign-On 설정** 페이지에서 다음 필드에 값을 입력합니다.

    a. **식별자** 텍스트 상자에 다음 URL 중 하나를 입력합니다.
    
    | 지역 | URL 값 |
    |----------|---------|
    | 아시아 태평양 북동쪽(APNE1)| `https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | 아시아 태평양 남동쪽(AU) | `https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml` |
    | 중유럽(EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | 북아메리카|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | 남아메리카(SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

    b. **회신 URL** 텍스트 상자에서 다음 URL 중 하나를 입력합니다.
    
    | 지역 | URL 값 |
    |----------|---------|
    | 아시아 태평양 북동쪽(APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | 아시아 태평양 남동쪽(AU)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | 중유럽(EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | 북아메리카|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | 남아메리카(SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

1. CylancePROTECT 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며 이는 SAML 토큰 특성 구성에 사용자 할당 특성 매핑을 추가합니다. 다음 스크린샷에서는 **nameidentifier** 가 **user.userprincipalname** 과 매핑되는 기본 특성 목록을 보여줍니다. CylancePROTECT 애플리케이션에서는 **nameidentifier** 가 **user.mail** 과 매핑되고 다른 모든 나머지 클레임이 제거되므로 특성 매핑을 변경하기 위해 **편집** 아이콘을 클릭하여 특성 매핑을 편집해야 합니다.

    ![이미지](common/edit-attribute.png)

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **CylancePROTECT 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

> [!NOTE]
> 다운로드한 Base64 인코딩 인증서를 텍스트 편집기에서 열고 **START** 와 **END** 태그 사이에 있는 텍스트만 복사하여 Cylance 관리 포털에 붙여넣습니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 CylancePROTECT에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **CylancePROTECT** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-cylanceprotect-sso"></a>CylancePROTECT SSO 구성

**CylancePROTECT** 쪽에서 Single Sign-On을 구성하려면 Azure Portal에서 다운로드한 **인증서(Base64)** 와 적절히 복사한 URL을 [CylancePROTECT 지원 팀](https://www.cylance.com/en-us/resources/support/support-overview.html)에 보내야 합니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다. 자세한 내용 Cylance 설명서: [https://support.cylance.com/s/](https://support.cylance.com/s/)를 사용하세요.

### <a name="create-cylanceprotect-test-user"></a>CylancePROTECT 테스트 사용자 만들기

이 섹션에서는 CylancePROTECT에서 Britta Simon이라는 사용자를 만듭니다. 콘솔 관리자와 협력하여 CylancePROTECT 플랫폼에서 사용자를 추가합니다. Azure Active Directory 계정 보유자는 활성화되기 전에 전자 메일을 받고 링크를 따라 계정을 확인합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 [이 애플리케이션 테스트]를 클릭하면 SSO를 설정한 CylancePROTECT에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 CylancePROTECT 타일을 클릭하면 SSO를 설정한 CylancePROTECT에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.


## <a name="next-steps"></a>다음 단계

CylancePROTECT가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).