---
title: '자습서: SAP HANA와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SAP HANA 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/02/2021
ms.author: jeedes
ms.openlocfilehash: 69c209447ec76537a2dfa119acfe578f99e936b2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124789734"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>자습서: SAP HANA와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP HANA를 통합하는 방법에 대해 알아보겠습니다. Azure AD와 SAP HANA를 통합하면 다음 작업을 수행할 수 있습니다.

* SAP HANA에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 SAP HANA에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

SAP HANA와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- SSO(Single Sign-On)를 사용하도록 설정된 SAP HANA 구독
- 공용 IaaS, 온-프레미스, Azure VM 또는 Azure의 SAP 큰 인스턴스에서 실행 중인 HANA 인스턴스
- XSA 관리 웹 인터페이스 및 HANA 인스턴스에 설치된 HANA Studio

> [!NOTE]
> 이 자습서의 단계를 테스트할 때 SAP HANA의 프로덕션 환경을 사용하지 않는 것이 좋습니다. 애플리케이션 개발 또는 스테이징 환경에서 통합을 먼저 테스트한 다음, 프로덕션 환경을 사용하세요.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* SAP HANA Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* SAP HANA는 **IDP** 시작 SSO를 지원합니다.
* SAP HANA는 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.


## <a name="adding-sap-hana-from-the-gallery"></a>갤러리에서 SAP HANA 추가

SAP HANA의 Azure AD 통합을 구성하려면 갤러리의 SAP HANA를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **SAP HANA** 를 입력합니다.
1. 결과 패널에서 **SAP HANA** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>SAP HANA에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 SAP HANA에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SAP HANA의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SAP HANA에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
2. **[SAP HANA SSO 구성](#configure-sap-hana-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[SAP HANA 테스트 사용자 만들기](#create-sap-hana-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SAP HANA에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **SAP HANA** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    **회신 URL** 텍스트 상자에 `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > [회신 URL] 값은 실제 값이 아닙니다. 실제 회신 URL로 값을 업데이트합니다. 값을 얻으려면 [SAP HANA 클라이언트 지원 팀](https://cloudplatform.sap.com/contact.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. SAP HANA 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    !["편집" 아이콘이 선택된 "사용자 특성" 섹션을 보여주는 스크린샷.](common/edit-attribute.png)

6. **사용자 특성 및 클레임** 대화 상자의 **사용자 특성** 섹션에서 다음 단계를 수행합니다.
 
    a. **편집** 아이콘을 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    !["편집" 아이콘이 선택된 "사용자 특성 및 클레임" 대화 상자를 보여주는 스크린샷.](./media/saphana-tutorial/tutorial_usermail.png)

    ![이미지](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. **변환** 목록에서 **ExtractMailPrefix()** 를 선택합니다.

    다. **매개 변수 1** 목록에서 **user.mail** 을 선택합니다.

    d. **저장** 을 클릭합니다.

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 SAP HANA에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **SAP HANA** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-sap-hana-sso"></a>SAP HANA SSO 구성

1. SAP HANA 쪽에 Single Sign-On을 구성하려면 해당 HTTPS 엔드포인트로 이동하여 **HANA XSA 웹 콘솔** 에 로그인합니다.

    > [!NOTE]
    > 기본 구성에서는 URL이 요청을 로그온 화면으로 리디렉션하며, 인증된 SAP HANA 데이터베이스 사용자의 자격 증명이 필요합니다. 로그인하는 사용자는 SAML 관리 작업을 수행할 권한이 있어야 합니다.

2. XSA 웹 인터페이스에서 **SAML ID 공급자** 로 이동합니다. 여기에서 화면 아래쪽의 **+** 단추를 선택하여 **ID 공급자 정보 추가** 창을 표시합니다. 다음 단계를 수행합니다.

    ![ID 공급자 추가](./media/saphana-tutorial/sap1.png)

    a. **ID 공급자 정보 추가** 창에서 메타데이터 XML(Azure Portal에서 다운로드한)의 콘텐츠를 **메타데이터** 상자에 붙여넣습니다.

    !["메타데이터" 및 "이름" 상자가 강조 표시된 "ID 공급자 정보 추가" 창을 보여주는 스크린샷.](./media/saphana-tutorial/sap2.png)

    b. XML 문서의 콘텐츠가 유효하면 구문 분석 프로세스를 통해 **일반 데이터** 화면 영역의 **제목, 엔터티 ID 및 발급자** 필드에 필요한 정보가 추출됩니다. **대상** 화면 영역의 URL 필드에 필요한 정보도 추출됩니다(예: **기준 URL 및 SingleSignOn URL (*)** 필드).

    ![ID 공급자 설정 추가](./media/saphana-tutorial/sap3.png)

    다. **일반 데이터** 화면 영역의 **이름** 상자에 새 SAML SSO ID 공급자의 이름을 입력합니다.

    > [!NOTE]
    > SAML IDP의 이름은 필수이며 고유해야 합니다. SAP HANA XS 애플리케이션에서 사용할 인증 방법으로 SAML을 선택할 때 표시되는 SAML IDP 목록에 나타납니다. 예를 들어 XS 아티팩트 관리 도구의 **인증** 화면 영역에서 이 작업을 수행할 수 있습니다.

3. **저장** 을 선택하여 SAML ID 공급자의 세부 정보를 저장하고 알려진 SAML IDP 목록에 새 SAML IDP를 추가합니다.

    ![저장 단추](./media/saphana-tutorial/sap4.png)

4. HANA Studio **Configuration**(구성) 탭의 시스템 속성 내에서 **saml** 을 기준으로 설정을 필터링합니다. 그런 다음 **assertion_timeout** 을 **10초** 에서 **120초** 로 조정합니다.

    ![assertion_timeout 설정](./media/saphana-tutorial/sap7.png)


### <a name="create-sap-hana-test-user"></a>SAP HANA 테스트 사용자 만들기

Azure AD 사용자가 SAP HANA에 로그인할 수 있도록 SAP HANA에 프로비전해야 합니다.
SAP HANA는 기본적으로 사용하도록 설정되는 **Just-In-Time 프로비전** 을 지원합니다.

사용자를 수동으로 만들어야 할 경우 다음 단계를 수행합니다.

>[!NOTE]
>사용자가 사용하는 외부 인증을 변경할 수 있습니다. Kerberos와 같은 외부 시스템으로 인증할 수 있습니다. 외부 ID에 대한 자세한 내용은 [도메인 관리자](https://cloudplatform.sap.com/contact.html)에게 문의하세요.

1. 관리자 권한으로 [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us)를 연 다음 SAML SSO에 DB 사용자를 사용하도록 설정합니다.

    ![사용자 만들기](./media/saphana-tutorial/sap5.png)

2. **SAML** 왼쪽의 보이지 않는 확인란을 선택한 다음 **구성** 링크를 선택합니다.

3. **추가** 를 선택하여 SAML IDP를 추가합니다.  적절한 SAML IDP를 선택한 다음 **확인** 을 선택합니다.

4. **외부 ID**(이 경우 BrittaSimon)를 추가합니다. 그런 다음, **확인** 을 선택합니다.

   > [!Note]
   > 사용자에 대한 **외부 ID** 필드를 채워야 하며 이 필드는 Azure AD에서 SAML 토큰의 **NameID** 필드와 일치해야 합니다. **모두** 옵션을 선택하면 현재 Azure AD에서 지원되지 않는 NameID 필드에서 SPProvderID 속성을 보내는 데 IDP가 필요하므로 이 확인란을 선택하면 안 됩니다. 자세한 내용은 [이](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.05/en-US/db6db355bb571014b56eb25057daec5f.html) 문서를 참조하세요.

5. 테스트를 위해 모든 **XS** 역할을 사용자에게 할당합니다.

    ![역할 할당](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > 사용 사례에 적합한 권한만 부여해야 합니다.

6. 사용자를 저장합니다.

### <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 SAP HANA에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 SAP HANA 타일을 클릭하면 SSO를 설정한 SAP HANA에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.


## <a name="next-steps"></a>다음 단계

SAP HANA가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).