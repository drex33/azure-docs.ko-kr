---
title: '자습서: TOPdesk - Public과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 TOPdesk - Public 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 54afb9b36df429702ee9a17c30fbad43c53e3b91
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282463"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>자습서: TOPdesk - Public과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 TOPdesk - Public을 통합하는 방법에 대해 알아봅니다. Azure AD와 TOPdesk - Public을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 TOPdesk - Public에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 TOPdesk - Public에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* TOPdesk - Public SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* TOPdesk - Public에서 **SP** 시작 SSO를 지원합니다.

## <a name="add-topdesk---public-from-the-gallery"></a>갤러리에서 TOPdesk - Public 추가

TOPdesk - Public이 Azure AD에 통합되도록 구성하려면 갤러리의 TOPdesk - Public을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **TOPdesk - Public** 을 입력합니다.
1. 결과 패널에서 **TOPdesk - Public** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---public"></a>TOPdesk - Public에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 TOPdesk - Public에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 TOPdesk - Public의 관련 사용자 간에 연결 관계를 설정해야 합니다.

TOPdesk - Public에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[TOPdesk - Public SSO 구성](#configure-topdesk---public-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[TOPdesk - Public 테스트 사용자 만들기](#create-topdesk---public-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 TOPdesk - Public에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **TOPdesk - Public** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4.  **서비스 공급자 메타데이터 파일** 이 있으면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    >[!NOTE]
    >**서비스 공급자 메타데이터 파일** 은 이 자습서의 뒷부분에 설명되어 있는 **TOPdesk - Public Single Sign-On 구성** 섹션에서 구할 수 있습니다.

    a. **메타데이터 파일 업로드** 를 클릭합니다.
    
    ![메타데이터 파일 업로드](common/upload-metadata.png)

    b. **폴더 로고** 를 클릭하여 메타데이터 파일을 선택하고 **업로드** 를 클릭합니다.

    ![메타데이터 파일 선택](common/browse-upload-metadata.png)

    다. 메타데이터 파일이 성공적으로 업로드되면 **식별자** 및 **회신 URL** 값이 기본 SAML 구성 섹션에 자동으로 채워집니다.

    d. **로그인 URL** 텍스트 상자에서 `https://<companyname>.topdesk.net` 패턴을 사용하여 URL을 입력합니다.

    e. **식별자 URL** 텍스트 상자의 TOPdesk 구성에서 검색할 수 있는 TOPdesk 메타데이터 URL을 입력합니다. `https://<companyname>.topdesk.net/saml-metadata/<identifier>` 패턴을 사용해야 합니다.
    
    f. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > **식별자** 와 **회신 URL** 값이 자동으로 채워지지 않으면 수동으로 입력해야 합니다. 식별자의 경우 앞서 언급한 패턴을 따르고 회신 URL은 이 자습서의 뒷부분에 설명되어 있는 **TOPdesk - Public Single Sign-On 구성** 섹션에서 가져옵니다. **로그온 URL** 값은 실제가 아니므로 이 값은 실제 로그온 URL로 업데이트해야 합니다. 이 값을 얻으려면 [TOPdesk - Public 클라이언트 지원 팀](https://my.topdesk.com/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **TOPdesk - Public 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 TOPdesk - Public에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **TOPdesk-Public** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-topdesk---public-sso"></a>TOPdesk - Public SSO 구성

1. **TOPdesk - Public** 회사 사이트에 관리자 권한으로 로그온합니다.

2. **TOPdesk** 메뉴에서 **Settings** 를 클릭합니다.
   
    ![설정](./media/topdesk-public-tutorial/menu.png "설정")

3. **Login Settings** 를 클릭합니다.
   
    ![로그인 설정](./media/topdesk-public-tutorial/login.png "로그인 설정")

4. **Login Settings** 메뉴를 확장하고 **General** 을 클릭합니다.
   
    ![일반 설정](./media/topdesk-public-tutorial/general.png "General Settings(일반 설정)")

5. **SAML login** 구성 섹션의 **Public** 섹션에서 다음 단계를 수행합니다.
   
    ![기술 설정](./media/topdesk-public-tutorial/public.png "기술 설정")
   
    a. **Download** 를 클릭하여 공용 메타데이터 파일을 다운로드한 후 컴퓨터에 로컬로 저장합니다.
   
    b. 다운로드한 메타데이터 파일을 연 다음 **AssertionConsumerService** 노드를 찾습니다.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/service.png "AssertionConsumerService")
   
    다. **AssertionConsumerService** 값을 복사하여 **기본 SAML 구성** 섹션의 **회신 URL** 텍스트 상자에 붙여넣습니다.      
   
6. 인증서 파일을 만들려면 다음 단계를 수행하십시오.
    
    ![MSSQLSERVER에 대한 프로토콜 속성](./media/topdesk-public-tutorial/certificate-file.png "인증서")
    
    a. Azure Portal에서 다운로드한 메타데이터 파일을 엽니다.
    
    b. **xsi:type** 이 **fed:ApplicationServiceType** 인 **RoleDescriptor** 노드를 확장합니다.
    
    다. **X509Certificate** 노드 값을 복사합니다.
    
    d. 복사한 **X509Certificate** 값을 컴퓨터의 파일에 로컬로 저장합니다.

7. **Public** 섹션에서 **Add** 를 클릭합니다.
    
    ![SAML 로그인](./media/topdesk-public-tutorial/add.png "공용")

8. **SAML configuration assistant** 대화 상자 페이지에서 다음 단계를 수행합니다.
    
    ![SAML 구성 도우미](./media/topdesk-public-tutorial/configuration.png "SAML 구성 도우미")
    
    a. Azure Portal에서 다운로드한 메타데이터 파일을 업로드하려면 **페더레이션 메타데이터** 에서 **찾아보기** 를 클릭합니다.

    b. 인증서 파일을 업로드하려면 **Certificate (RSA)** 에서 **Browse** 를 클릭합니다.

    다. TOPdesk 지원 팀에서 받은 로고 파일을 업로드하려면 **Logo icon** 에서 **Browse** 를 클릭합니다.

    d. **사용자 이름 특성** 텍스트 상자에서 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`를 입력합니다.

    e. **Display name** 텍스트 상자에 구성의 이름을 입력합니다.

    f. **저장** 을 클릭합니다.

### <a name="create-topdesk---public-test-user"></a>TOPdesk - Public 테스트 사용자 만들기

Azure AD 사용자가 TOPdesk - Public에 로그인할 수 있도록 하려면 TOPdesk - Public에 프로비저닝되어야 합니다. TOPdesk - Public의 경우 프로비전은 수동 작업입니다.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>사용자 프로비전을 구성하려면

1. **TOPdesk - Public** 회사 사이트에 관리자 권한으로 로그온합니다.

2. 위쪽 메뉴에서 **TOPdesk \> 새로 만들기 \> 지원 파일 \> 사람** 순으로 클릭합니다.
   
    ![Person](./media/topdesk-public-tutorial/files.png "사람")

3. 새로운 사용자 대화 상자에서 다음 단계를 수행합니다.
   
    ![새로운 사람](./media/topdesk-public-tutorial/new.png "새로운 사람")
   
    a. 일반 탭을 클릭합니다.

    b. **성** 텍스트 상자에서 사용자의 성을 입력합니다(예: Simon).
 
    다. 계정에 대해 **Site** 를 선택합니다.
 
    d. **저장** 을 클릭합니다.

> [!NOTE]
> 다른 TOPdesk - Public 사용자 계정 생성 도구 또는 TOPdesk - Public에서 제공하는 API를 사용하여 Azure AD 사용자 계정을 프로비전할 수 있습니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 TOPdesk - Public 로그온 URL로 리디렉션됩니다. 

* TOPdesk - Public 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 TOPdesk - Public 타일을 클릭하면 TOPdesk - Public 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

TOPdesk - Public이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
