---
title: '자습서: Azure Active Directory와 BambooHR 통합| Microsoft Docs'
description: Azure Active Directory 및 BambooHR 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: 34adca214e5673340b12c8dfcdbdfd8a5bac9e0e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124759608"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>자습서: BambooHR과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 BambooHR을 통합하는 방법에 대해 알아봅니다. Azure AD와 BambooHR을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 BambooHR에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 BambooHR에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* BambooHR SSO(Single Sign-On)가 설정된 구독.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* BambooHR에서 **SP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.


## <a name="adding-bamboohr-from-the-gallery"></a>갤러리에서 BambooHR 추가

BambooHR의 Azure AD 통합을 구성하려면 갤러리의 BambooHR을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **BambooHR** 을 입력합니다.
1. 결과 패널에서 **BambooHR** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-sso-for-bamboohr"></a>BambooHR에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 BambooHR에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 BambooHR의 관련 사용자 간에 연결 관계를 설정해야 합니다.

BambooHR에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
2. **[BambooHR SSO 구성](#configure-bamboohr-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[BambooHR 테스트 사용자 만들기](#create-bamboohr-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 해당 사용자를 BambooHR에 만듭니다.
3. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **BambooHR** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<company>.bamboohr.com` 패턴을 사용하는 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.

    | 회신 URL |
    |-----------|
    | `https://<company>.bamboohr.com/saml/consume.php` |
    | `https://<company>.bamboohr.co.uk/saml/consume.php` |

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 회신 URL로 이러한 값을 업데이트합니다. 값을 가져오려면 [BambooHR 클라이언트 지원 팀](https://www.bamboohr.com/contact.php)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **BambooHR 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 BambooHR에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **BambooHR** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 &quot;기본 액세스&quot; 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name=&quot;configure-bamboohr-sso&quot;></a>BambooHR SSO 구성

1. 새 창에서 BambooHR 회사 사이트에 관리자 권한으로 로그인합니다.

2. 홈페이지에서 다음을 수행합니다.
   
    ![BambooHR Single Sign-On 페이지](./media/bamboo-hr-tutorial/ic796691.png &quot;Single Sign-On")   

    a. **앱** 을 선택합니다.
   
    b. **앱** 창에서 **Single Sign-On** 을 선택합니다.
   
    다. **SAML Single Sign-On** 을 선택합니다.

3. **SAML Single Sign-On** 창에서 다음을 수행합니다.
   
    ![SAML Single Sign-On 창](./media/bamboo-hr-tutorial/IC796692.png "SAML Single Sign-On")
   
    a. 6단계에서 Azure Portal로부터 복사한 **로그인 URL** 을 **SSO 로그인 URL** 상자에 붙여넣습니다.
      
    b. 메모장에서 Azure Portal에서 다운로드한 base-64로 인코딩된 인증서를 열고, 콘텐츠를 복사한 다음, **X.509 인증서** 상자에 붙여넣습니다.
   
    c. **저장** 을 선택합니다.

### <a name="create-bamboohr-test-user"></a>BambooHR 테스트 사용자 만들기

Azure AD 사용자가 BambooHR에 로그인할 수 있도록 다음을 수행하여 BambooHR에서 수동으로 설정합니다.

1. **BambooHR** 사이트에 관리자 권한으로 로그인합니다.

2. 위쪽 도구 모음에서 **설정** 을 선택합니다.
   
    ![설정 단추](./media/bamboo-hr-tutorial/IC796694.png "설정")

3. **개요** 를 선택합니다.

4. 왼쪽 창에서 **보안** > **사용자** 를 선택합니다.

5. 설정하려는 유효한 Azure AD 계정의 사용자 이름, 암호 및 이메일 주소를 입력합니다.

6. **저장** 을 선택합니다.
        
>[!NOTE]
>Azure AD 사용자 계정을 설정하기 위해 BambooHR 사용자 계정 생성 도구 또는 API를 사용할 수도 있습니다.

### <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

1. Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 BambooHR 로그온 URL로 리디렉션됩니다. 

2. BambooHR 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

3. Microsoft 액세스 패널을 사용할 수 있습니다. 액세스 패널에서 BambooHR 타일을 클릭하면 BambooHR 로그온 URL로 리디렉션됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.


## <a name="next-steps"></a>다음 단계

BambooHR이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).