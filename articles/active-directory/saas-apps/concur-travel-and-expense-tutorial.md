---
title: '자습서: SAP Concur Travel and Expense와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 SAP Concur Travel and Expense 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 7acaceca83c4dcdee549c974d864c32355617b66
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124817143"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-concur-travel-and-expense"></a>자습서: SAP Concur Travel and Expense와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP Concur Travel and Expense를 통합하는 방법을 알아봅니다. Azure AD와 SAP Concur Travel and Expense를 통합하면 다음 작업을 수행할 수 있습니다.

* SAP Concur Travel and Expense에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 SAP Concur Travel and Expense에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SAP Concur Travel and Expense 구독
* Concur 사용자 계정의 “회사 관리자” 역할 [Concur SSO 셀프 서비스 도구](https://www.concursolutions.com/nui/authadmin/ssoadmin)로 이동하여 올바른 액세스 권한이 있는지 테스트할 수 있습니다. 액세스 권한이 없는 경우 Concur 지원 또는 구현 프로젝트 관리자에게 문의하세요. 

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 Azure AD SSO를 구성하고 테스트합니다.

* SAP Concur Travel and Expense는 **IDP** 및 **SP** 시작 SSO를 지원합니다.
* SAP Concur Travel and Expense는 프로덕션 환경과 구현 환경에서 SSO 테스트를 지원합니다. 

> [!NOTE]
> 이 애플리케이션의 식별자는 미국, EMEA 및 중국의 세 개 지역 각각에 대한 고정 문자열 값입니다. 따라서 한 테넌트의 각 지역에 대해 하나의 인스턴스만 구성할 수 있습니다. 

## <a name="adding-sap-concur-travel-and-expense-from-the-gallery"></a>갤러리에서 SAP Concur Travel and Expense 추가

Azure AD에 SAP Concur Travel and Expense 통합을 구성하려면 갤러리에서 SAP Concur Travel and Expense를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **SAP Concur Travel and Expense** 를 입력합니다.
1. 결과 창에서 **SAP Concur Travel and Expense** 를 선택한 다음 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-sap-concur-travel-and-expense"></a>SAP Concur Travel and Expense에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 SAP Concur Travel and Expense에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SAP Concur Travel and Expense의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SAP Concur Travel and Expense에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[SAP Concur Travel and Expense SSO 구성](#configure-sap-concur-travel-and-expense-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[SAP Concur Travel and Expense 테스트 사용자 만들기](#create-sap-concur-travel-and-expense-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 SAP Concur Travel and Expense에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **SAP Concur Travel and Expense** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 애플리케이션은 **IDP** 시작 모드로 미리 구성되어 있으며 필요한 URL은 이미 Azure로 미리 채워져 있습니다. 사용자는 **저장** 단추를 클릭하여 구성을 저장해야 합니다.

    > [!NOTE]
    > 식별자(엔터티 ID) 및 회신 URL(Assertion Consumer Service URL)은 지역별로 한정됩니다. Concur 엔터티의 데이터 센터에 따라 선택하세요. Concur 엔터티의 데이터 센터를 모르는 경우 Concur 지원 담당자에게 문의하세요. 

5. **SAML로 Single Sign-On 설정** 페이지에서 **사용자 특성** 에 대한 편집/연필 모양 아이콘을 클릭하여 설정을 편집합니다. 고유한 사용자 ID는 Concur 사용자 로그인 ID와 일치해야 합니다. 일반적으로 **user.userprincipalname** 을 **user.mail** 로 변경해야 합니다.

    ![사용자 특성 편집](common/edit-attribute.png)

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 메타데이터를 컴퓨터에 다운로드 및 저장합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 SAP Concur Travel and Expense에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **SAP Concur Travel and Expense** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.

1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-sap-concur-travel-and-expense-sso"></a>SAP Concur Travel and Expense SSO 구성

1. SAP Concur Travel and Expense 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **SAP Concur Travel and Expense 설정** 을 클릭하면 SAP Concur Travel and Expense 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 SAP Concur Travel and Expense에 로그인합니다. 브라우저 확장에서 애플리케이션을 자동으로 구성하고 3-7단계를 자동화합니다.

    ![설정 구성](common/setup-sso.png)

3. 다른 웹 브라우저 창에서 SAP Concur Travel and Expense를 수동으로 설정하려면 다운로드한 **페더레이션 메타데이터 XML** 을 [Concur SSO 셀프 서비스 도구](https://www.concursolutions.com/nui/authadmin/ssoadmin)에 업로드하고 관리자 권한으로 SAP Concur Travel and Expense 회사 사이트에 로그인해야 합니다.

1. **추가** 를 클릭합니다.
1. IdP에 대한 사용자 지정 이름(예: “Azure AD(US)”)을 입력합니다. 
1. **XML 파일 업로드** 를 클릭하고 이전에 다운로드한 **페더레이션 메타데이터 XML** 을 연결합니다.
1. **메타데이터 추가** 를 클릭하여 변경 내용을 저장합니다.

    ![Concur SSO 셀프 서비스 도구 스크린샷](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-sap-concur-travel-and-expense-test-user"></a>SAP Concur Travel and Expense 테스트 사용자 만들기

이 섹션에서는 SAP Concur Travel and Expense에 B.Simon이라는 사용자를 만듭니다. Concur Travel and Expense 플랫폼에 사용자를 추가하려면 SAP Concur Travel and Expense 지원 팀에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다. 

> [!NOTE]
> B. Simon의 Concur 로그인 ID는 Azure AD에서의 B.Simon의 고유 식별자와 일치해야 합니다. 예를 들어 B.Simon의 Azure AD 고유 식별자가 `B.Simon@contoso.com`인 경우 B.Simon의 Concur 로그인 ID도 `B.Simon@contoso.com`여야 합니다. 

## <a name="configure-concur-mobile-sso"></a>Concur 모바일 SSO 구성
Concur 모바일 SSO를 사용하도록 설정하려면 Concur 지원 팀에게 **사용자 액세스 URL** 을 제공해야 합니다. Azure AD에서 **사용자 액세스 URL** 을 가져오려면 아래 단계를 따르세요.
1. **엔터프라이즈 애플리케이션** 으로 이동합니다.
1. **SAP Concur Travel and Expense** 를 클릭합니다.
1. **속성** 을 클릭합니다.
1. **사용자 액세스 URL** 을 복사하여 이 URL을 Concur 지원 팀에게 제공합니다.

> [!NOTE]
> SSO를 구성하는 셀프 서비스 옵션을 사용할 수 없으므로 Concur 지원 팀과 협력하여 모바일 SSO를 사용하도록 설정합니다. 

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 SAP Concur Travel and Expense 로그온 URL로 리디렉션됩니다.

* SAP Concur Travel and Expense 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 SAP Concur Travel and Expense에 자동으로 로그인됩니다.

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 SAP Concur Travel and Expense 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 SAP Concur Travel and Expense에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

SAP Concur Travel and Expense를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).