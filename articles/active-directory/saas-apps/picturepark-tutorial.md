---
title: '자습서: Picturepark와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Picturepark 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/01/2021
ms.author: jeedes
ms.openlocfilehash: 92bc5dd433c5cdc32b2598a35aadcce6be7b1c9c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828003"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>자습서: Picturepark와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Picturepark를 통합하는 방법을 알아봅니다. Azure AD와 Picturepark를 통합하면 다음을 수행할 수 있습니다.

* Picturepark에 액세스할 수 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Picturepark에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

Picturepark와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Picturepark Single Sign-On 지원 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Picturepark는 **SP** 시작 SSO를 지원합니다.

## <a name="add-picturepark-from-the-gallery"></a>갤러리에서 Picturepark 추가

Picturepark가 Azure AD에 통합되도록 구성하려면 갤러리의 Picturepark를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Picturepark** 를 입력합니다.
1. 결과 패널에서 **Picturepark** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-picturepark"></a>Picturepark에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Picturepark에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Picturepark의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Picturepark에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Picturepark SSO 구성](#configure-picturepark-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Picturepark 테스트 사용자 만들기](#create-picturepark-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Picturepark에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Picturepark** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자(엔터티 ID)** 텍스트 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다.

    | 식별자 URL |
    |---|
    |`https://<COMPANY_NAME>.current-picturepark.com`|
    |`https://<COMPANY_NAME>.picturepark.com`|
    |`https://<COMPANY_NAME>.next-picturepark.com`|
    |
    
    b. **로그온 URL** 텍스트 상자에서 `https://<COMPANY_NAME>.picturepark.com` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 해당 값을 실제 식별자 및 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [Picturepark 클라이언트 지원 팀](https://picturepark.com/company/picturepark-customer-support)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML 서명 인증서** 섹션에서 **편집** 단추를 클릭하여 **SAML 서명 인증서** 대화 상자를 엽니다.

    ![SAML 서명 인증서 편집](common/edit-certificate.png)

6. **SAML 서명 인증서** 섹션에서 **지문** 을 복사하여 컴퓨터에 저장합니다.

    ![지문 값 복사](common/copy-thumbprint.png)

7. **Picturepark 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다. **로그인 URL** 에는 `https://login.microsoftonline.com/_my_directory_id_/wsfed` 패턴의 값을 사용합니다.

    > [!Note]
    > _my_directory_id_ 는 Azure AD 구독의 테넌트 ID입니다.

    ![구성 URL 복사](./media/picturepark-tutorial/configure.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Picturepark에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Picturepark** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 &quot;기본 액세스&quot; 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name=&quot;configure-picturepark-sso&quot;></a>Picturepark SSO 구성

1. 다른 웹 브라우저 창에서 Picturepark 회사 사이트에 관리자로 로그인합니다.

2. 위쪽 도구 모음에서 **관리 도구** 를 클릭하고 **관리 콘솔** 을 클릭합니다.
   
    ![관리 콘솔](./media/picturepark-tutorial/tools.png &quot;관리 콘솔")

3. **인증** 을 클릭한 후 **ID 공급자** 를 클릭합니다.
   
    ![인증](./media/picturepark-tutorial/identity-provider.png "인증")

4. **ID 공급자 구성** 섹션에서 다음 단계를 수행합니다.
   
    ![ID 공급자 구성](./media/picturepark-tutorial/add-configuration.png "ID 공급자 구성")
   
    a. **추가** 를 클릭합니다.
  
    b. 구성 이름을 입력합니다.
   
    다. **기본값으로 설정** 을 선택합니다.
   
    d. Azure Portal에서 복사한 **로그인 URL** 값을 **발급자 URI** 텍스트 상자에 붙여넣습니다.
   
    e. **SAML 서명 인증서** 섹션에서 복사한 **지문** 값을 **Trusted Issuer Thumb Print**(신뢰할 수 있는 발급자 지문) 텍스트 상자에 붙여넣습니다. 

5. **JoinDefaultUsersGroup** 을 클릭합니다.

6. **클레임** 텍스트 상자에서 **Emailaddress** 특성을 설정하려면 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`를 입력하고 **저장** 을 클릭합니다.

      ![Configuration](./media/picturepark-tutorial/claim.png "구성")

### <a name="create-picturepark-test-user"></a>Picturepark 테스트 사용자 만들기

Azure AD 사용자가 Picturepark에 로그인하려면 Picturepark에 프로비저닝되어야 합니다. Picturepark의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **Picturepark** 테넌트에 로그인합니다.

1. 위쪽 도구 모음에서 **관리 도구** 를 클릭하고 **사용자** 를 클릭합니다.
   
    ![사용자](./media/picturepark-tutorial/user.png "사용자")

1. **사용자 개요** 탭에서 **새로 만들기** 를 클릭합니다.
   
    ![사용자 관리](./media/picturepark-tutorial/new-user.png "사용자 관리")

1. **사용자 만들기** 대화 상자에서 프로비전할 유효한 Azure Active Directory 사용자에 대해 다음 단계를 수행합니다.
   
    ![사용자 만들기](./media/picturepark-tutorial/details.png "사용자 만들기")
   
    a. **이메일 주소** 텍스트 상자에 사용자의 **이메일 주소** 를 입력합니다(`BrittaSimon@contoso.com`).  
   
    b. **암호** 와 **Confirm 암호 확인** 텍스트 상자에 BrittaSimon의 **암호** 를 입력합니다. 
   
    다. **이름** 텍스트 상자에 사용자의 **이름** 을 **Britta** 로 입력합니다. 
   
    d. **성** 텍스트 상자에 사용자의 **성** 을 **Simon** 으로 입력합니다.
   
    e. **회사** 텍스트 상자에 사용자의 **회사 이름** 을 입력합니다. 
   
    f. **국가** 텍스트 상자에 사용자의 **국가/지역** 을 입력합니다.
  
    g. **우편 번호** 텍스트 상자에 구/군/시의 **우편 번호** 를 입력합니다.
   
    h. **구/군/시** 텍스트 상자에 사용자의 **구/군/시 이름** 을 입력합니다.

    i. **언어** 를 선택합니다.
   
    j. **만들기** 를 클릭합니다.

>[!NOTE]
>다른 Picturepark 사용자 계정 생성 도구 또는 Picturepark가 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비전할 수 있습니다.
>

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Picturepark 로그온 URL로 리디렉션됩니다. 

* Picturepark 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Picturepark 타일을 클릭하면 Picturepark 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Picturepark가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).