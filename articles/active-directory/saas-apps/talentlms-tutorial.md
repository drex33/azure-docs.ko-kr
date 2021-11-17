---
title: '자습서: TalentLMS와 Azure AD SSO 통합'
description: Azure Active Directory와 TalentLMS 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2021
ms.author: jeedes
ms.openlocfilehash: 1d9d9adec6c8b9db9429cebba29fdf4a7e5a304f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282698"
---
# <a name="tutorial-azure-ad-sso-integration-with-talentlms"></a>자습서: TalentLMS와 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 TalentLMS를 통합하는 방법에 대해 알아봅니다. Azure AD와 TalentLMS를 통합하면 다음을 수행할 수 있습니다.

* TalentLMS에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 TalentLMS에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

TalentLMS와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* TalentLMS Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* TalentLMS는 **SP** 시작 SSO를 지원합니다.

## <a name="add-talentlms-from-the-gallery"></a>갤러리에서 TalentLMS 추가

TalentLMS가 Azure AD에 통합되도록 구성하려면 갤러리의 TalentLMS를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **TalentLMS** 를 입력합니다.
1. 결과 패널에서 **TalentLMS** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-talentlms"></a>TalentLMS에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 TalentLMS에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 TalentLMS의 관련 사용자 간에 연결 관계를 설정해야 합니다.

TalentLMS에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[TalentLMS SSO 구성](#configure-talentlms-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[TalentLMS 테스트 사용자 만들기](#create-talentlms-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 TalentLMS에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **TalentLMS** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자(엔터티 ID)** 텍스트 상자에서 `http://<tenant-name>.talentlms.com` 패턴을 사용하는 URL을 입력합니다.

    b. **로그온 URL** 텍스트 상자에서 `https://<tenant-name>.TalentLMSapp.com` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 해당 값을 실제 식별자 및 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [TalentLMS 클라이언트 지원 팀](https://www.talentlms.com/contact)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML 서명 인증서** 섹션에서 **편집** 단추를 클릭하여 **SAML 서명 인증서** 대화 상자를 엽니다.

    ![SAML 서명 인증서 편집](common/edit-certificate.png)

6. **SAML 서명 인증서** 섹션에서 **지문** 을 복사하여 컴퓨터에 저장합니다.

    ![지문 값 복사](common/copy-thumbprint.png)

7. **설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 TalentLMS에 대한 액세스 권한을 부여하여 B.Simon이 Azure Single Sign-On을 사용할 수 있도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **TalentLMS** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-talentlms-sso"></a>TalentLMS SSO 구성

1. 다른 웹 브라우저 창에서 TalentLMS 회사 사이트에 관리자로 로그인합니다.

1. **계정 및 설정** 섹션에서 **사용자** 탭을 클릭합니다.

    ![계정 및 설정](./media/talentlms-tutorial/user.png "계정 및 설정")

1. **SSO(Single Sign-On)** 를 클릭합니다.

1. Single Sign-On 섹션에서 다음 단계를 수행 합니다.

    ![Single Sign-On](./media/talentlms-tutorial/certificate.png "Single Sign-On")

    a. **SSO 통합 형식** 목록에서 **SAML 2.0** 을 선택합니다.

    b. **IDP(ID 공급자)** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    다. Azure Portal의 **지문** 값을 **Certificate Fingerprint**(인증서 지문) 텍스트 상자에 붙여넣습니다.

    d.  Azure Portal에서 복사한 **원격 로그인 URL** 값을 **로그인 URL** 텍스트 상자에 붙여넣습니다.

    e. Azure Portal에서 복사한 **로그아웃 URL** 값을 **원격 로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    f. 다음을 입력합니다.

    * **TargetedID**(대상 ID) 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`을 입력합니다.

    * **이름** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`을 입력합니다.

    * **성** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`을 입력합니다.

    * **메일** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`를 입력합니다.

1. **저장** 을 클릭합니다.

### <a name="create-talentlms-test-user"></a>TalentLMS 테스트 사용자 만들기

Azure AD 사용자가 TalentLMS에 로그인할 수 있도록 하려면 TalentLMS로 프로비저닝되어야 합니다. TalentLMS의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. 자신의 **TalentLMS** 테넌트에 로그인합니다.

1. **사용자** 를 클릭한 다음 **사용자 추가** 를 클릭합니다.

1. **사용자 추가** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![사용자 추가](./media/talentlms-tutorial/account.png "사용자 추가")  

    a. **이름** 텍스트 상자에 사용자의 이름(예: `Britta`)을 입력합니다.

    b. **성** 텍스트 상자에 사용자의 성(예: `Simon`)을 입력합니다.
 
    다. **전자 메일 주소** 텍스트 상자에 `brittasimon@contoso.com`과 같은 사용자의 메일 주소를 입력합니다.

    d. **사용자 추가** 를 클릭합니다.

> [!NOTE]
> 다른 TalentLMS 사용자 계정 생성 도구 또는 TalentLMS가 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비저닝할 수 있습니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 TalentLMS 로그온 URL로 리디렉션됩니다. 

* TalentLMS 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 TalentLMS 타일을 클릭하면 TalentLMS 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

TalentLMS가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
