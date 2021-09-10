---
title: '자습서: Meta Networks Connector와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Meta Networks Connector 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/24/2021
ms.author: jeedes
ms.openlocfilehash: 3b30a3f86f5d152590cdfcef88c2116d47b33aa7
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123036986"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>자습서: Meta Networks Connector와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Meta Networks Connector를 통합하는 방법에 대해 알아봅니다. Azure AD와 Meta Networks Connector를 연결하는 경우 다음을 수행할 수 있습니다.

* Meta Networks Connector에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Meta Networks Connector에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Meta Networks Connector SSO(Single Sign-on)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Meta Networks Connector는 **SP** 및 **IDP** 시작 SSO를 지원합니다.
 
* Meta Networks Connector는 **Just In Time** 사용자 프로비저닝을 지원합니다.

* Meta Networks Connector는 [자동 사용자 프로비저닝](meta-networks-connector-provisioning-tutorial.md)을 지원합니다.

## <a name="add-meta-networks-connector-from-the-gallery"></a>갤러리에서 Meta Networks Connector 추가

Meta Networks Connector가 Azure AD에 통합되도록 구성하려면 갤러리의 Meta Networks Connector를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Meta Networks Connector** 를 입력합니다.
1. 결과 패널에서 **Meta Networks Connector** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-meta-networks-connector"></a>Meta Networks Connector에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Meta Networks Connector에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Meta Networks Connector의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Meta Networks Connector에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Meta Networks Connector SSO 구성](#configure-meta-networks-connector-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Meta Networks Connector 테스트 사용자 만들기](#create-meta-networks-connector-test-user)** - B.Simon의 Azure AD 표현과 연결된 대응 사용자를 Meta Networks Connector에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Meta Networks Connector** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에서 `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml` 패턴을 사용하여 URL을 입력합니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    a. **로그인 URL** 텍스트 상자에서 `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login` 패턴을 사용하여 URL을 입력합니다.

    b. **릴레이 상태** 텍스트 상자에서 `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 자습서 뒷부분에 설명된 실제 식별자, 응답 URL 및 로그온 URL로 이 값을 업데이트합니다.

6. Meta Networks Connector 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, 이는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집** 아이콘을 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![스크린샷은 편집 아이콘이 선택된 사용자 특성을 보여줍니다.](common/edit-attribute.png)
    
7. 위에서 언급한 특성 외에도, Meta Networks Connector 애플리케이션에는 SAML 응답에 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 다음 단계를 수행하여 아래 표와 같은 SAML 토큰 특성을 추가합니다.
    
    | 속성 | 원본 특성 | 네임스페이스|
    | ---------------| --------------- | -------- |
    | firstname | user.givenname | |
    | lastname | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | phone | user.telephonenumber | |

    a. **새 클레임 추가** 를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![스크린샷은 새 클레임을 추가하는 옵션이 있는 사용자 클레임을 보여줍니다.](common/new-save-attribute.png)

    ![스크린샷은 설명된 값을 입력할 수 있는 사용자 클레임 관리 대화 상자를 보여줍니다.](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스** 를 비워 둡니다.

    d. 원본을 **특성** 으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인** 을 클릭합니다.

    g. **저장** 을 클릭합니다.

8. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

9. **Meta Networks Connector 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Meta Networks Connector에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Meta Networks Connector** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-meta-networks-connector-sso"></a>Meta Networks Connector SSO 구성

1. 브라우저에서 새 탭을 열고 Meta Networks Connector 관리자 계정으로 로그인합니다.
    
    > [!NOTE]
    > Meta Networks Connector는 보안 시스템입니다. 따라서 해당 포털에 액세스하기 전에 공용 IP 주소를 해당 허용 목록에 추가해야 합니다. 공용 IP 주소를 가져오려면 [여기](https://whatismyipaddress.com/)에 지정된 아래 링크를 따릅니다. IP 주소를 [Meta Networks Connector 클라이언트 지원팀](mailto:support@metanetworks.com)으로 보내 IP 주소를 허용 목록에 추가합니다.
    
2. **관리자** 로 이동한 후 **설정** 을 선택합니다.
    
    ![스크린샷은 관리 메뉴에서 선택한 설정을 보여줍니다.](./media/metanetworksconnector-tutorial/menu.png)
    
3. **인터넷 트래픽 로깅** 및 **Force VPN MFA**(강제 VPN MFA)를 해제로 설정해야 합니다.
    
    ![스크린샷은 이러한 설정을 해제하는 것을 보여줍니다.](./media/metanetworksconnector-tutorial/settings.png)
    
4. **관리자** 로 이동한 후 **SAML** 을 선택합니다.
    
    ![스크린샷은 관리 메뉴에서 선택한 SAML을 보여줍니다.](./media/metanetworksconnector-tutorial/admin.png)
    
5. **세부 정보** 페이지에서 다음 단계를 수행합니다.
    
    ![스크린샷은 설명된 값을 입력할 수 있는 세부 정보 페이지를 보여줍니다.](./media/metanetworksconnector-tutorial/details.png)
    
    a. **SSO URL** 값을 복사한 후 **Meta Networks Connector 도메인 및 URL** 섹션의 **로그인 URL** 텍스트 상자에 붙여넣습니다.
    
    b. **받는 사람 URL** 값을 복사한 후 **Meta Networks Connector 도메인 및 URL** 섹션의 **회신 URL** 텍스트 상자에 붙여넣습니다.
    
    다. **대상 URL(SP 엔터티 ID)** 값을 복사한 후 **Meta Networks Connector 도메인 및 URL** 섹션의 **식별자(엔터티 ID)** 텍스트 상자에 붙여넣습니다.
    
    d. SAML을 사용하도록 설정합니다.
    
6. **일반** 탭에서 다음 단계를 수행합니다.

    ![스크린샷은 설명된 값을 입력할 수 있는 일반 페이지를 보여줍니다.](./media/metanetworksconnector-tutorial/configuration.png)

    a. **ID 공급자 Single Sign-On URL** 에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    b. **ID 공급자 발급자** 에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    다. Azure Portal에서 다운로드한 인증서를 메모장에서 열고 **X 509 인증서** 텍스트 상자에 붙여넣습니다.

    d. **Just-in-Time 프로비전** 을 사용하도록 설정합니다.

### <a name="create-meta-networks-connector-test-user"></a>Meta Networks Connector 테스트 사용자 만들기

이 섹션에서는 Meta Networks Connector에서 Britta Simon이라는 사용자를 만듭니다. Meta Networks Connector는 기본적으로 사용하도록 설정된 Just-In-Time 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. Meta Networks Connector에 사용자가 아직 없는 경우 Meta Networks Connector에 액세스하려고 할 때 새 사용자가 만들어집니다.

>[!Note]
>사용자를 수동으로 만들어야 하는 경우 [Meta Networks Connector 클라이언트 지원 팀](mailto:support@metanetworks.com)에 문의하세요.

Meta Networks는 자동 사용자 프로비저닝도 지원합니다. 자동 사용자 프로비저닝 구성 방법에 대한 자세한 내용은 [여기](./meta-networks-connector-provisioning-tutorial.md)에서 제공합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Meta Networks Connector 로그온 URL로 리디렉션됩니다.  

* Meta Networks Connector 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Meta Networks Connector에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Meta Networks Connector 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Meta Networks Connector에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Meta Networks Connector가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).