---
title: '자습서: Adobe Experience Manager와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Adobe Experience Manager 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2021
ms.author: jeedes
ms.openlocfilehash: 6eb0bc2f41d202c5406b3f021c02da4494a76d04
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132321869"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>자습서: Adobe Experience Manager와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Adobe Experience Manager를 통합하는 방법을 알아봅니다. Adobe Experience Manager를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Adobe Experience Manager에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Adobe Experience Manager에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Adobe Experience Manager SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Adobe Experience Manager에서 **SP 및 IDP** 시작 SSO를 지원합니다.

* Adobe Experience Manager에서 **Just-In-Time** 사용자 프로비전을 지원합니다.

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>갤러리에서 Adobe Experience Manager 추가

Adobe Experience Manager의 Azure AD 통합을 구성하려면 갤러리의 Adobe Experience Manager를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Adobe Experience Manager** 를 입력합니다.
1. 결과 패널에서 **Adobe Experience Manager** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-sso-for-adobe-experience-manager"></a>Adobe Experience Manager에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Adobe Experience Manager에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Adobe Experience Manager의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Adobe Experience Manager에서 Azure AD SSO를 구성하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
2. **[Adobe Experience Manager SSO 구성](#configure-adobe-experience-manager-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Adobe Experience Manager 테스트 사용자 만들기](#create-adobe-experience-manager-test-user)** - Azure AD 표현과 연결된 Britta Simon에 해당하는 사용자를 Adobe Experience Manager에 만듭니다.
6. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Adobe Experience Manager** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 상자에서 AEM 서버에 대해서도 정의한 고유한 값을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<AEM Server Url>/saml_login` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > [회신 URL] 값은 실제 값이 아닙니다. 회신 URL 값을 실제 회신 URL로 업데이트합니다. 이 값을 얻으려면 [Adobe Experience Manager 클라이언트 지원 팀](https://helpx.adobe.com/support/experience-manager.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그온 URL** 텍스트 상자에서 Adobe Experience Manager 서버 URL을 입력합니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

7. **Adobe Experience Manager 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Adobe Experience Manager에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Adobe Experience Manager** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-adobe-experience-manager-sso"></a>Adobe Experience Manager SSO 구성

1. 다른 브라우저 창에서 **Adobe Experience Manager** 관리자 포털을 엽니다.

2. **설정** > **보안** > **사용자** 를 선택합니다.

    ![Adobe Experience Manager에서 사용자 타일을 보여주는 스크린샷.](./media/adobe-experience-manager-tutorial/user-1.png)

3. **관리자** 또는 다른 관련 사용자를 선택합니다.

    ![Adminisrator 사용자를 강조 표시하는 스크린샷.](./media/adobe-experience-manager-tutorial/tutorial-admin-6.png)

4. **계정 설정** > **TrustStore 관리** 를 선택합니다.

    ![계정 설정에서 TrustStore 관리를 보여주는 스크린샷.](./media/adobe-experience-manager-tutorial/manage-trust.png)

5. **CER 파일의 인증서 추가** 에서 **인증서 파일 선택** 을 클릭합니다. Azure Portal에서 다운로드한 인증서 파일을 찾아서 선택합니다.

    ![인증서 파일 선택 단추를 강조 표시하는 스크린샷.](./media/adobe-experience-manager-tutorial/user-2.png)

6. 인증서는 TrustStore에 추가됩니다. 인증서의 별칭을 적어둡니다.

    ![인증서가 TrustStore에 추가되었음을 보여주는 스크린샷.](./media/adobe-experience-manager-tutorial/tutorial-admin-7.png)

7. **사용자** 페이지에서 **인증 서비스** 를 선택합니다.

    ![화면에서 인증 서비스를 강조 표시하는 스크린샷.](./media/adobe-experience-manager-tutorial/tutorial-admin-8.png)

8. **계정 설정** > **키 저장소 만들기/관리** 를 선택합니다. 암호를 제공하여 키 저장소를 만듭니다.

    ![키 저장소 관리를 강조 표시하는 스크린샷.](./media/adobe-experience-manager-tutorial/tutorial-admin-9.png)

9. 관리 화면으로 돌아갑니다. **설정** > **작업** > **웹 콘솔** 을 선택합니다.

    ![설정 섹션의 작업 아래에서 웹 콘솔을 강조 표시하는 스크린샷.](./media/adobe-experience-manager-tutorial/tutorial-admin-1.png)

    구성 페이지가 열립니다.

    ![Single Sign-On 저장 단추 구성](./media/adobe-experience-manager-tutorial/tutorial-admin-2.png)

10. **Adobe Granite SAML 2.0 인증 처리기** 를 찾습니다. **추가** 아이콘을 선택합니다.

    ![Adobe Granite SAML 2.0 인증 처리기를 강조 표시하는 스크린샷.](./media/adobe-experience-manager-tutorial/tutorial-admin-3.png)

11. 이 페이지에서 다음 작업을 수행합니다.

    ![Single Sign-On 구성 저장 단추](./media/adobe-experience-manager-tutorial/tutorial-admin-4.png)

    a. **경로** 상자에 **/** 를 입력합니다.

    b. Azure Portal에서 복사한 **로그인 URL** 값을 **IDP URL** 상자에 입력합니다.

    다. **IDP 인증서 별칭** 상자에 TrustStore에서 추가한 **인증서 별칭** 값을 입력합니다.

    d. Azure Portal에서 구성한 고유의 **Azure ID 식별자** 값을 **보안 제공 엔터티 ID** 상자에 입력합니다.

    e. Azure Portal에서 구성한 **회신 URL** 값을 **어설션 소비자 서비스 URL** 상자에 입력합니다.

    f. 키 저장소에서 설정한 **암호** 를 **키 저장소 암호** 상자에 입력합니다.

    g. 자신의 사례와 관련된 **이름 ID** 또는 다른 사용자 ID를 **사용자 특성 ID** 상자에 입력합니다.

    h. **CRX 사용자 자동 작성** 을 선택합니다.

    i. Azure Portal에서 가져온 고유의 **로그아웃 URL** 값을 **로그아웃 URL** 상자에 입력합니다.

    j. **저장** 을 선택합니다.

### <a name="create-adobe-experience-manager-test-user"></a>Adobe Experience Manager 테스트 사용자 만들기

이 섹션에서는 Adobe Experience Manager에서 Britta Simon이라는 사용자를 만듭니다. **CRX 사용자 자동 작성** 옵션을 선택한 경우 사용자는 인증을 성공한 후에 자동으로 생성됩니다.

수동으로 사용자를 만들려는 경우 [Adobe Experience Manager 지원팀](https://helpx.adobe.com/support/experience-manager.html)과 협력하여 Adobe Experience Manager 플랫폼에 사용자를 추가하세요.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Adobe Experience Manager 로그온 URL로 리디렉션됩니다.  

* Adobe Experience Manager 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Adobe Experience Manager에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Adobe Experience Manager 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우 SSO를 설정한 Adobe Experience Manager에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.


## <a name="next-steps"></a>다음 단계

Adobe Experience Manager가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
