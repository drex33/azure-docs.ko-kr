---
title: LoginRadius와 Azure Active Directory 통합 | Microsoft Docs
description: Azure Active Directory 및 LoginRadius 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: 1774e5f7099d130fe1692911fbcf1f7794fa7e20
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285142"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>자습서: LoginRadius와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 LoginRadius를 통합하는 방법에 대해 알아봅니다. LoginRadius를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* LoginRadius에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 LoginRadius에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

LoginRadius와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* LoginRadius Single Sign-On이 설정된 구독.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* LoginRadius에서 **SP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-loginradius-from-the-gallery"></a>갤러리에서 LoginRadius 추가

LoginRadius의 Azure AD 통합을 구성하려면 갤러리의 LoginRadius를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **LoginRadius** 를 입력합니다.
1. 결과 패널에서 **LoginRadius** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-loginradius"></a>LoginRadius에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 LoginRadius에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 LoginRadius의 관련 사용자 간에 연결 관계를 설정해야 합니다.

LoginRadius에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[LoginRadius SSO 구성](#configure-loginradius-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[LoginRadius 테스트 사용자 만들기](#create-loginradius-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 LoginRadius에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **LoginRadius** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

   1. **식별자(엔터티 ID)** 텍스트 상자에 URL `https://lr.hub.loginradius.com/`을 입력합니다.

   1. **회신 URL(Assertion Consumer Service URL)** 텍스트 상자에 LoginRadius ACS URL `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx`를 입력합니다. 

   1. **로그온 URL** 텍스트 상자에 URL `https://secure.loginradius.com/login`을 입력합니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 선택하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **LoginRadius 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 LoginRadius에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **LoginRadius** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-loginradius-sso"></a>LoginRadius SSO 구성

이 섹션에서는 LoginRadius 관리자 콘솔에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

1. LoginRadius [관리자 콘솔](https://adminconsole.loginradius.com/login) 계정에 로그인합니다.

2. [LoginRadius 관리자 콘솔](https://secure.loginradius.com/account/team)에서 **팀 관리** 섹션으로 이동합니다.

3. **Single Sign-On** 탭을 선택한 다음, **Azure AD** 를 선택합니다.

   ![LoginRadius 팀 관리 콘솔의 Single Sign-On 메뉴를 보여 주는 스크린샷](./media/loginradius-tutorial/azure-ad.png)

4. Azure AD 설정 페이지에서 다음 단계를 완료합니다.

   ![LoginRadius 팀 관리 콘솔에서 Azure Active Directory 구성을 보여 주는 스크린샷](./media/loginradius-tutorial/single-sign-on.png)

    1. **ID 공급자 위치** 에서 Azure AD 계정에서 가져오는 로그온 엔드포인트를 입력합니다.

    1. **ID 공급자 로그아웃 URL** 에서 Azure AD 계정에서 가져오는 로그아웃 엔드포인트를 입력합니다.
 
    1. **ID 공급자 인증서** 에서 Azure AD 계정에서 가져오는 Azure AD 인증서를 입력합니다. 헤더와 바닥글로 인증서 값을 입력합니다. 예: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. **서비스 공급자 인증서** 및 **서버 공급자 인증서 키** 에서 인증서와 키를 입력합니다. 

       명령줄(Linux/Mac)에서 다음 명령을 실행하여 자체 서명된 인증서를 만들 수 있습니다.

       - SP에 대한 인증서 키를 가져오는 명령: `openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - SP에 대한 인증서를 가져오는 명령: `openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`

       > [!NOTE]
       > 헤더와 바닥글로 인증서 및 인증서 키 값을 입력해야 합니다.
       > - 인증서 값 예제 형식: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - 인증서 키 값 예제 형식: `-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. **데이터 매핑** 섹션에서 필드(SP 필드)를 선택하고 해당하는 Azure AD 필드(IdP 필드)를 입력합니다.

    다음은 Azure AD에 대해 나열된 몇 가지 필드 이름입니다.

    | 필드    | 프로필 키                                                          |
    | --------- | -------------------------------------------------------------------- |
    | Email     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | FirstName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > **이메일** 필드 매핑이 필요합니다. **FirstName** 및 **LastName** 필드 매핑은 선택 사항입니다.

### <a name="create-loginradius-test-user"></a>LoginRadius 테스트 사용자 만들기

1. LoginRadius [관리자 콘솔](https://adminconsole.loginradius.com/login) 계정에 로그인합니다.

2. LoginRadius 관리자 콘솔에서 팀 관리 섹션으로 이동합니다.

   ![LoginRadius 관리자 콘솔을 보여 주는 스크린샷](./media/loginradius-tutorial/team-management.png)

3. 측면 메뉴에서 **팀 멤버 추가** 를 선택하여 양식을 엽니다. 

4. **팀 멤버 추가** 양식에서 사용자의 세부 정보를 제공하고 사용자에게 부여할 사용 권한을 할당하여 LoginRadius 사이트에서 Britta Simon이라는 사용자를 만듭니다. 역할을 기반으로 하는 사용 권한에 대한 자세한 내용은 LoginRadius [팀 멤버 관리](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) 문서의 [역할 액세스 권한](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) 섹션을 참조하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 MyApps를 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

1. 브라우저에서 https://accounts.loginradius.com/auth.aspx 로 이동하여 **Fed SSO 로그인** 을 선택합니다.
2. LoginRadius 앱 이름을 입력한 다음, **로그인** 을 선택합니다.
3. Azure AD 계정에 로그인하라는 팝업이 열립니다.
4. 인증 후에 팝업이 닫히고 LoginRadius 관리자 콘솔에 로그인됩니다.

## <a name="next-steps"></a>다음 단계

LoginRadius가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
