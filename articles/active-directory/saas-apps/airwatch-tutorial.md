---
title: '자습서: AirWatch와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 AirWatch 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 0b6c102cc7f7bd7c8c32a2ebc04ddce5458a2563
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748265"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>자습서: Azure Active Directory와 AirWatch 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 AirWatch를 통합하는 방법에 대해 알아봅니다. Azure AD와 AirWatch를 통합할 경우 다음을 수행할 수 있습니다.

* AirWatch에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 AirWatch에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.
 
* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* AirWatch SSO(Single Sign-On)가 설정된 구독.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. 

* AirWatch에서 **SP** 시작 SSO를 지원합니다.

## <a name="add-airwatch-from-the-gallery"></a>갤러리에서 AirWatch 추가

AirWatch의 Azure AD 통합을 구성하려면 갤러리의 AirWatch를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **AirWatch** 를 입력합니다.
1. 결과 패널에서 **AirWatch** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-airwatch"></a>AirWatch에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 AirWatch에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 AirWatch의 관련 사용자 간에 링크 관계를 설정해야 합니다.

AirWatch에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[AirWatch SSO 구성](#configure-airwatch-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[AirWatch 테스트 사용자 만들기](#create-airwatch-test-user)** - B.Simon의 Azure AD 표현과 연결되는 대응 사용자를 AirWatch에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **AirWatch** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 페이지에서 다음 필드에 대한 값을 입력합니다.

    1. **로그온 URL** 텍스트 상자에서 `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode` 패턴을 사용하는 URL을 입력합니다.

    1. **식별자(엔터티 ID)** 텍스트 상자에 값을 `AirWatch`로 입력합니다.

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이 값을 얻으려면 [AirWatch Client 지원 팀](https://www.air-watch.com/company/contact-us/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. AirWatch 애플리케이션은 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

1. 위의 이미지와 같이 SAML 토큰 특성을 구성하기 위해 **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 **편집 아이콘** 을 사용하여 클레임을 편집하거나 **새 클레임 추가** 를 사용하여 클레임을 추가하고, 다음 단계를 수행합니다.

    | 속성 |  원본 특성|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. **새 클레임 추가** 를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스** 를 비워 둡니다.

    d. 원본을 **특성** 으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인** 을 클릭합니다.

    g. **저장** 을 클릭합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 메타데이터 XML을 컴퓨터에 다운로드 및 저장합니다.

   ![인증서 다운로드 링크](common/metadataxml.png)

1. **AirWatch 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 AirWatch에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **AirWatch** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 &quot;기본 액세스&quot; 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name=&quot;configure-airwatch-sso&quot;></a>AirWatch SSO 구성

1. 다른 웹 브라우저 창에서 AirWatch 회사 사이트에 관리자로 로그인합니다.

1. 설정 페이지에서 **설정 > 엔터프라이즈 통합 > 디렉터리 서비스** 를 선택합니다.

   ![설정](./media/airwatch-tutorial/services.png &quot;설정")

1. **사용자** 탭을 클릭하고 **Base DN** 텍스트 상자에 도메인 이름을 입력한 다음 **저장** 을 클릭합니다.

   ![기본 DN 텍스트 상자를 강조 표시하는 스크린샷.](./media/airwatch-tutorial/user.png "사용자")

1. **서버** 탭을 클릭합니다.

   ![Server](./media/airwatch-tutorial/directory.png "서버")

1. **LDAP** 섹션에서 다음 단계를 수행합니다.

    ![LDAP 섹션에서 수행해야 하는 변경 내용을 보여주는 스크린샷.](./media/airwatch-tutorial/ldap.png "LDAP")   

    a. **디렉터리 유형** 으로 **없음** 을 선택합니다.

    b. **인증에 SAML 사용** 을 선택합니다.

1. **SAML 2.0** 섹션에서 다운로드한 인증서를 업로드하려면 **업로드** 를 클릭합니다.

    ![업로드](./media/airwatch-tutorial/uploads.png "업로드")

1. **요청** 섹션에서 다음 단계를 수행합니다.

    ![요청 섹션](./media/airwatch-tutorial/request.png "요청")  

    a. **요청 바인딩 형식** 으로 **POST** 를 선택합니다.

    b. Azure Portal의 **AirWatch에서 Single Sign-On 구성** 대화 상자 페이지에서 **로그인 URL** 값을 복사한 후 **ID 공급자 Single Sign-On URL** 텍스트 상자에 붙여넣습니다.

    다. **NameID 형식** 으로 **전자 메일 주소** 를 선택합니다.

    d. **인증 요청 보안** 으로 **없음** 을 선택합니다.

    e. **저장** 을 클릭합니다.

1. **사용자** 탭을 다시 클릭합니다.

    ![사용자](./media/airwatch-tutorial/users.png "사용자")

1. **특성** 섹션에서 다음 단계를 수행합니다.

    ![Attribute](./media/airwatch-tutorial/attributes.png "attribute")

    a. **개체 식별자** 텍스트 상자에 `http://schemas.microsoft.com/identity/claims/objectidentifier`를 입력합니다.

    b. **사용자 이름** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`를 입력합니다.

    다. **표시 이름** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`을 입력합니다.

    d. **이름** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`을 입력합니다.

    e. **성** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`을 입력합니다.

    f. **전자 메일 주소** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`를 입력합니다.

    g. **저장** 을 클릭합니다.

### <a name="create-airwatch-test-user"></a>AirWatch 테스트 사용자 만들기

Azure AD 사용자가 AirWatch에 로그인할 수 있도록 하려면 AirWatch로 프로비저닝되어야 합니다. AirWatch의 경우 프로비전은 수동 작업입니다.

**사용자 프로비전을 구성하려면 다음 단계를 수행합니다.**

1. **AirWatch** 회사 사이트에 관리자로 로그인합니다.

2. 왼쪽의 탐색 창에서 **계정** 과 **사용자** 를 차례로 클릭합니다.
  
   ![사용자](./media/airwatch-tutorial/accounts.png "사용자")

3. **사용자** 메뉴에서 **목록 보기** 와 **추가 > 사용자 추가** 를 차례로 클릭합니다.
  
   ![사용자 추가 및 추가 단추를 강조 표시하는 스크린샷.](./media/airwatch-tutorial/add.png "사용자 추가")

4. **사용자 추가/편집** 대화 상자에서 다음 단계를 수행합니다.

   ![사용자 추가](./media/airwatch-tutorial/save.png "사용자 추가")

   a. 관련된 텍스트 상자에 프로비전할 유효한 Azure Active Directory 계정의 **사용자 이름**, **암호**, **암호 확인**, **이름**, **성**, **전자 메일 주소** 를 입력합니다.

   b. **저장** 을 클릭합니다.

> [!NOTE]
> 다른 AirWatch 사용자 계정 생성 도구 또는 AirWatch가 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비저닝할 수 있습니다.

### <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 AirWatch 로그온 URL로 리디렉션됩니다. 

* AirWatch 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 AirWatch 타일을 클릭하면 AirWatch 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

AirWatch가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).