---
title: '자습서: Marketo와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Marketo 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 7885b284ca4930203dd38e9b90957af7ef6b4c1f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285059"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>자습서:Azure Active Directory와 Marketo 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Marketo를 통합하는 방법에 대해 알아봅니다.
Marketo를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Marketo에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Marketo에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Marketo와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Marketo Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Marketo에서 **IDP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-marketo-from-the-gallery"></a>갤러리에서 Marketo 추가

Marketo의 Azure AD 통합을 구성하려면 갤러리의 Marketo를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Marketo** 를 입력합니다.
1. 결과 패널에서 **Marketo** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-marketo"></a>Marketo에 대한 Azure AD SSO 구성 및 테스트

이 섹션에서는 **Britta Simon** 이라는 테스트 사용자를 기반으로 Marketo에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Marketo의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Marketo에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD SSO를 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD SSO를 사용할 수 있도록 합니다.
2. **[Marketo SSO 구성](#configure-marketo-sso)** - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
    1. **[Marketo 테스트 사용자 만들기](#create-marketo-test-user)** - Britta Simon의 Azure AD 표현과 연결된 대응 사용자를 Marketo에 만듭니다.
3. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Marketo** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://saml.marketo.com/sp` URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://login.marketo.com/saml/assertion/<munchkinid>` 패턴을 사용하여 URL을 입력합니다.

    다. **릴레이 상태** 텍스트 상자에서 `https://<munchkinid>.marketo.com/` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 회신 URL 및 릴레이 상태로 값을 업데이트합니다. 이러한 값을 얻으려면 [Marketo 클라이언트 지원 팀](https://investors.marketo.com/contactus.cfm)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **Marketo 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Marketo에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Marketo** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-marketo-sso"></a>Marketo SSO 구성

1. Marketo 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **Marketo 설정** 을 클릭하면 Marketo 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Marketo에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-6단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. Marketo를 수동으로 설정하려면 다른 웹 브라우저 창에서 관리자 권한으로 Marketo 회사 사이트에 로그인합니다.

1. 애플리케이션의 Munchkin ID를 가져오려면 다음 작업을 수행합니다.
   
    a. 관리자 자격 증명을 사용하여 Marketo 앱에 로그인합니다.
   
    b. 위쪽 탐색 창에서 **관리자** 단추를 클릭합니다.
   
    ![Single Sign-On1 구성](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    다. 통합 메뉴로 이동하여 **Munchkin 링크** 를 클릭합니다.
   
    ![Single Sign-On2 구성](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. 화면에 표시된 Munchkin ID를 복사하고 Azure AD 구성 마법사에서 회신 URL을 완료합니다.
   
    ![Single Sign-On3 구성](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. 아래 단계에 따라 애플리케이션에서 SSO를 구성합니다.
   
    a. 관리자 자격 증명을 사용하여 Marketo 앱에 로그인합니다.
   
    b. 위쪽 탐색 창에서 **관리자** 단추를 클릭합니다.
   
    ![Single Sign-On4 구성](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    다. 통합 메뉴로 이동하여 **Single Sign On** 을 클릭합니다.
   
    ![Single Sign-On5 구성](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. SAML 설정을 사용하려면 **편집** 단추를 클릭합니다.
   
    ![Single Sign-on 구성6](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. Single Sign-On 설정을 사용하도록 **설정** 합니다.
   
    f. **발급자 ID** 텍스트 상자에 **Azure AD 식별자** 를 붙여넣습니다.
   
    g. **엔터티 ID** 텍스트 상자에 URL을 `http://saml.marketo.com/sp`로 입력합니다.
   
    h. **이름 식별자 요소** 로 사용자 ID 위치를 선택합니다.
   
    ![Single Sign-On7 구성](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > 사용자 ID가 UPN 값이 아닌 경우 특성 탭에서 값을 바꿉니다.
   
    i. Azure AD 구성 마법사에서 다운로드한 인증서를 업로드합니다. 설정을 **저장** 합니다.
   
    j. 리디렉션 페이지 설정을 편집합니다.
   
    k. **로그인 URL** 텍스트 상자에 **로그인 URL** 을 붙여넣습니다.
   
    l. **로그아웃 URL** 텍스트 상자에 **로그아웃 URL** 을 붙여넣습니다.
   
    m. **오류 URL** 에서 **Marketo 인스턴스 URL** 을 복사하고 **저장** 단추를 클릭하여 설정을 저장합니다.
   
    ![Single Sign-On8 구성](./media/marketo-tutorial/tutorial_marketo_10.png)

3. 사용자에 대한 SSO를 사용하려면 다음 작업을 완료합니다.
   
    a. 관리자 자격 증명을 사용하여 Marketo 앱에 로그인합니다.
   
    b. 위쪽 탐색 창에서 **관리자** 단추를 클릭합니다.
   
    ![Single Sign-On9 구성](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    다. **보안** 메뉴로 이동하여 **로그인 설정** 을 클릭합니다.
   
    ![Single Sign-On10 구성](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. **SSO 필요** 옵션을 선택하고 설정을 **저장** 합니다.
   
    ![Single Sign-On11 구성](./media/marketo-tutorial/tutorial_marketo_14.png)


### <a name="create-marketo-test-user"></a>Marketo 테스트 사용자 만들기

이 섹션에서는 Marketo에서 Britta Simon이라는 사용자를 만듭니다. 다음 단계에 따라 Marketo 플랫폼에서 사용자를 만듭니다.

1. 관리자 자격 증명을 사용하여 Marketo 앱에 로그인합니다.

2. 위쪽 탐색 창에서 **관리자** 단추를 클릭합니다.
   
    ![user1 테스트](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. **보안** 메뉴로 이동하여 **사용자 및 역할** 을 클릭합니다.
   
    ![user2 테스트](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. 사용자 탭에서 **새 사용자 초대** 링크를 클릭합니다.
   
    ![user3 테스트](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. 새 사용자 초대 마법사에서 다음 정보를 입력합니다.
   
    a. 텍스트 상자에 사용자 **메일** 주소를 입력합니다.
   
    ![user4 테스트](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. 텍스트 상자에 **이름** 을 입력합니다.
   
    다. 텍스트 상자에 **성** 을 입력합니다.
   
    d. **다음** 을 클릭합니다.

6. **권한** 탭에서 **사용자 역할** 을 선택하고 **다음** 을 클릭합니다.
   
    ![user5 테스트](./media/marketo-tutorial/tutorial_marketo_17.png)
7. **보내기** 단추를 클릭하여 사용자 초대를 보냅니다.
   
    ![user6 테스트](./media/marketo-tutorial/tutorial_marketo_18.png)

8. 사용자는 이메일 알림을 받으면 링크를 클릭하고 암호를 변경하여 계정을 활성화해야 합니다. 

### <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 [이 애플리케이션 테스트]를 클릭하면 SSO를 설정한 Marketo에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Marketo 타일을 클릭하면 SSO를 설정한 Marketo에 자동으로 로그인되어야 합니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Marketo가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
