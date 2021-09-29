---
title: '자습서: SAML SSO for Jira by Resolution GmbH와 Azure Active Directory 연결 | Microsoft Docs'
description: Azure Active Directory 및 SAML SSO for Jira by resolution GmbH 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: jeedes
ms.openlocfilehash: 79ec9389840582ea7841bfd0edc4bda3a6f16f01
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746563"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>자습서: SAML SSO for Jira by resolution GmbH와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAML SSO for Jira by resolution GmbH를 통합하는 방법에 대해 알아봅니다. Azure AD와 SAML SSO for Jira by resolution GmbH를 통합하면 다음을 수행할 수 있습니다.

* SAML SSO for Jira by resolution GmbH에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 SAML SSO for Jira by resolution GmbH에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SAML SSO for Jira by resolution GmbH SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* SAML SSO for Jira by resolution GmbH는 **SP** 및 **IDP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>갤러리에서 SAML SSO for Jira by resolution GmbH 추가

Azure AD에 SAML SSO for Jira by resolution GmbH를 통합하도록 구성하려면 갤러리에서 관리되는 SaaS 앱 목록으로 SAML SSO for Jira by resolution GmbH를 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **SAML SSO for Jira by resolution GmbH** 를 입력합니다.
1. 결과 패널에서 **SAML SSO for Jira by resolution GmbH** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-jira-by-resolution-gmbh"></a>SAML SSO for Jira by resolution GmbH에서 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 SAML SSO for Jira by resolution GmbH에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SAML SSO for Jira by resolution GmbH의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SAML SSO for Jira by resolution GmbH에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[SAML SSO for Jira by resolution GmbH SSO 구성](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[SAML SSO for Jira by resolution GmbH 테스트 사용자 만들기](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** - 사용자의 Azure AD 표현과 연결된 SAML SSO for Jira by resolution GmbH에 B.Simon에 해당하는 사용자를 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **SAML SSO for Jira by resolution GmbH** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 애플리케이션을 **IDP** 시작 모드로 구성하려는 경우 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/samlsso` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/samlsso` 패턴을 사용하여 URL을 입력합니다.

    다. 애플리케이션을 **SP** 시작 모드로 구성하려는 경우 **추가 URL 설정** 을 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/samlsso` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 식별자, 회신 URL 및 로그온 URL의 경우 **\<server-base-url>** 를 Jira 인스턴스의 기본 URL로 바꿉니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다. 문제가 있으면 [SAML SSO for Jira by resolution GmbH 클라이언트 지원팀](https://www.resolution.de/go/support)에 문의하세요.

4. **SAML을 사용하여 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 SAML SSO for Jira by resolution GmbH에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **SAML SSO for Jira by resolution GmbH** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-saml-sso-for-jira-by-resolution-gmbh-sso"></a>SAML SSO for Jira by resolution GmbH SSO 구성 

1. 다른 웹 브라우저 창에서 Jira 인스턴스에 관리자로 로그인합니다.

2. 오른쪽에 있는 코그를 마우스로 가리키고 **앱 관리** 를 클릭합니다.
    
    !["Cog" 아이콘과 드롭다운에서 선택한 "앱 관리"를 가리키는 화살표를 보여주는 스크린샷.](./media/samlssojira-tutorial/add-on-1.png)

3. 관리자 액세스 페이지로 리디렉션되면 **암호** 를 입력하고 **확인** 단추를 클릭합니다.

    !["관리자 액세스" 페이지를 보여주는 스크린샷.](./media/samlssojira-tutorial/add-on-2.png)

4. 일반적으로 Jira에서 Atlassian Marketplace로 리디렉션됩니다. 그렇지 않은 경우 왼쪽 패널에서 **새 앱 찾기** 를 클릭합니다. **SAML SSO(Single Sign-On) for JIRA** 를 검색하고 **설치** 단추를 클릭하여 SAML 플러그 인을 설치합니다.

    !["SAML Single Sign On(SSO) Jira, SAML/SSO" 앱에 대한 "설치" 단추를 가리키는 화살표가 있는 "Atlassian Marketplace for JIRA" 페이지를 보여주는 스크린샷.](./media/samlssojira-tutorial/store.png)

5. 플러그 인 설치가 시작됩니다. 완료되면 **닫기** 단추를 클릭합니다.

    !["설치" 대화 상자를 보여주는 스크린샷.](./media/samlssojira-tutorial/store-2.png)

    !["설치 및 준비 완료!"를 보여주는 스크린샷. "닫기" 단추가 선택된 대화 상자.](./media/samlssojira-tutorial/store-3.png)

6. 그런 다음, **관리** 를 클릭합니다.

    !["관리" 단추가 선택된 "SAML Single Sign On(SSO) Jira, SAML/SSO" 앱을 보여주는 스크린샷.](./media/samlssojira-tutorial/store-4.png)
    
7. 다음으로 **구성** 을 클릭하여 방금 설치된 플러그 인을 구성합니다.

    !["SAML SingleSignOn for Jira" 앱에 대해 "구성" 단추가 선택된 "앱 관리"페이지를 보여주는 스크린샷.](./media/samlssojira-tutorial/store-5.png)

8. **SAML SingleSignOn 플러그인 구성** 마법사에서 **새 IDP 추가** 를 클릭하여 새 ID 공급 기업으로 Azure AD를 구성합니다.

    ![스크린샷은 "새 IdP 추가" 단추가 선택된 "시작" 페이지를 보여줍니다.](./media/samlssojira-tutorial/add-on-4.png) 

9. **SAML ID 공급 기업 선택** 페이지에서 다음 단계를 수행합니다.

    !["IdP 유형" 및 "이름" 텍스트 상자가 강조 표시되고 "다음" 단추가 선택된 "SAML ID 공급자 선택" 페이지를 보여주는 스크린샷.](./media/samlssojira-tutorial/identity-provider.png)
 
    a. **Azure AD** 를 IdP 유형으로 설정합니다.
    
    b. ID 공급 기업의 **이름** 을 추가합니다(예: Azure AD).
    
    다. ID 공급 기업의 **설명**(선택 사항)을 추가합니다(예: Azure AD).
    
    d. **다음** 을 클릭합니다.
    
10. **ID 공급자 구성** 페이지에서 **다음** 을 클릭합니다.
 
    !["ID 공급자 구성" 페이지를 보여주는 스크린샷.](./media/samlssojira-tutorial/configuration.png)

11. **SAML IdP 메타데이터 가져오기** 페이지에서 다음 단계를 수행합니다.

    !["메타데이터 XML 파일 선택" 작업이 선택된 "SAML IdP 메타데이터 가져오기" 페이지를 보여주는 스크린샷.](./media/samlssojira-tutorial/metadata.png)

    a. **메타데이터 XML 파일 선택** 단추를 클릭하고 전에 다운로드한 **페더레이션 메타데이터 XML** 파일을 선택합니다.

    b. **가져오기** 단추를 클릭합니다.
     
    다. 가져오기가 완료될 때까지 잠시 대기합니다.  
     
    d. **다음** 단추를 클릭합니다.
    
12. **사용자 ID 특성 및 변환** 페이지에서 **다음** 단추를 클릭합니다.

    !["다음" 단추가 선택된 "사용자 ID 특성 및 변환" 페이지를 보여주는 스크린샷.](./media/samlssojira-tutorial/transformation.png)
    
13. **사용자 만들기 및 업데이트** 페이지에서 **저장 및 다음** 을 클릭하여 설정을 저장합니다.
    
    !["저장 및 다음" 단추가 선택된 "사용자 생성 및 업데이트" 페이지를 보여주는 스크린샷.](./media/samlssojira-tutorial/update.png)
    
14. **설정 테스트** 페이지에서 **테스트 건너뛰기 및 수동으로 구성** 을 클릭하여 현재 사용자 테스트를 건너뜁니다. 이는 다음 섹션에서 수행되며 Azure Portal에서 몇 가지 설정이 필요합니다.
    
    !["테스트 건너뛰기 및 수동으로 구성" 단추가 선택된 "설정 테스트" 페이지를 보여주는 스크린샷.](./media/samlssojira-tutorial/test.png)
    
15. **확인** 을 클릭하여 경고를 건너뜁니다.
    
    !["확인" 단추가 선택된 경고 대화 상자를 보여주는 스크린샷.](./media/samlssojira-tutorial/warning.png)

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>SAML SSO for Jira by resolution GmbH 테스트 사용자 만들기

Azure AD 사용자가 SAML SSO for Jira by resolution GmbH에 로그인하도록 설정하려면 SAML SSO for Jira by resolution GmbH에 프로비저닝되어야 합니다. 이 자습서의 경우 프로비저닝을 직접 수행해야 합니다. 그러나 SAML SSO plugin by resolution에 사용할 수 있는 다른 프로비저닝 모델도 있습니다(예: **Just In Time** 프로비저닝). [SAML SSO by resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all)에서 해당 설명서를 참조하세요. 질문이 있는 경우 [resolution 지원 팀](https://www.resolution.de/go/support)에 지원을 요청하세요.

**사용자 계정을 수동으로 프로비저닝하려면 다음 단계를 수행합니다.**

1. Jira 인스턴스에 관리자로 로그인합니다.

2. 택한 코그 위로 마우스를 가져가고 **사용자 관리** 를 선택합니다.

   ![드롭다운에서 "사용자 관리"가 선택된 "Cog" 아이콘을 가리키는 화살표를 보여주는 스크린샷.](./media/samlssojira-tutorial/user-1.png)

3. 관리자 액세스 페이지로 리디렉션되면 **암호** 를 입력하고 **확인** 단추를 클릭합니다.

    !["암호" 텍스트 상자가 강조 표시된 "관리자 액세스" 페이지를 보여주는 스크린샷.](./media/samlssojira-tutorial/user-2.png) 

4. **사용자 관리** 탭 섹션에서 **사용자 만들기** 를 클릭합니다.

    !["사용자 만들기" 단추가 선택된 "사용자 관리" 탭을 보여주는 스크린샷.](./media/samlssojira-tutorial/user-3-new.png) 

5. **“새 사용자 만들기”** 대화 상자 페이지에서 다음 단계를 수행합니다. Azure AD에서와 같이 사용자를 정확하게 만들어야 합니다.

    ![직원 추가](./media/samlssojira-tutorial/user-4-new.png) 

    a. **이메일 주소** 텍스트 상자에 사용자의 이메일 주소를 입력합니다(<b>BrittaSimon@contoso.com</b>).

    b. **전체 이름** 텍스트 상자에 사용자의 전체 이름을 입력합니다. **Britta Simon**.

    다. **사용자 이름** 텍스트 상자에 사용자의 이메일 주소를 입력합니다(<b>BrittaSimon@contoso.com</b>). 

    d. **암호** 텍스트 상자에 사용자의 암호를 입력합니다.

    e. **사용자 만들기** 를 클릭하여 사용자 만들기를 완료합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 SAML SSO for Jira by resolution GmbH 로그온 URL로 리디렉션됩니다.  

* SAML SSO for Jira by resolution GmbH 로그온 URL로 바로 이동하고 거기서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 SAML SSO for Jira by resolution GmbH에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 SAML SSO for Jira by resolution GmbH 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 SAML SSO for Jira by resolution GmbH에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="enable-sso-redirection-for-jira"></a>Jira에 대한 SSO 리디렉션 사용

이전 섹션에서 설명한 대로, 현재 두 가지 방법으로 Single Sign-On을 트리거할 수 있습니다. **Azure Portal** 을 사용하거나 **Jira 인스턴스에 대한 특별 링크** 를 사용하는 것입니다. SAML SSO plugin by resolution GmbH를 사용하면 **Jira 인스턴스를 가리키는 모든 URL에 액세스** 하기만 하면 Single Sign-On을 트리거할 수 있습니다.

기본적으로 Jira에 액세스하는 모든 사용자는 플러그 인에서 옵션을 활성화한 후 Single Sign-On으로 리디렉션됩니다.

SSO 리디렉션을 활성화하려면 **Jira 인스턴스** 에서 다음을 수행합니다.

1. Jira에서 SAML SSO 플러그 인의 구성 페이지에 액세스합니다.
1. 왼쪽 패널에서 **리디렉션** 을 클릭합니다.

   ![왼쪽 탐색에서 리디렉션 링크를 강조 표시하는 Jira SAML SingleSignOn 플러그 인 구성 페이지의 부분 스크린샷.](./media/samlssojira-tutorial/configure-1.png)

1. **SSO 리디렉션 사용** 을 선택합니다.

   ![선택한 "SSO 리디렉션 사용" 확인란이 강조 표시된 Jira SAML SingleSignOn 플러그 인 구성 페이지의 부분 스크린샷.](./media/samlssojira-tutorial/configure-2.png) 

1. 오른쪽 위 모서리의 **설정 저장** 단추를 누릅니다.

옵션을 활성화한 후에도 `https://<server-base-url>/login.jsp?nosso`로 이동하여 **nosso 사용** 옵션을 선택하면 계속 사용자 이름/암호 프롬프트에 도달할 수 있습니다. 항상 그렇듯이, **\<server-base-url>** 를 기본 URL로 바꿉니다.

## <a name="next-steps"></a>다음 단계

SAML SSO for Jira by resolution GmbH가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).