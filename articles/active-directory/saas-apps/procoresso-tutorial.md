---
title: '자습서: Procore SSO와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Procore SSO 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/21/2021
ms.author: jeedes
ms.openlocfilehash: 56918491f2fad1357f565ed5c07942d0c65c4114
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112552445"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>자습서: Procore SSO와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Procore SSO를 통합하는 방법을 알아봅니다. Azure AD와 Procore SSO를 통합하면 다음을 수행할 수 있습니다.

* Procore SSO에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Procore SSO에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

Procore SSO와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Procore SSO Single Sign-On이 설정된 구독

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Procore SSO는 **IDP** 시작 SSO를 지원합니다.

## <a name="add-procore-sso-from-the-gallery"></a>갤러리에서 Procore SSO 추가

Procore SSO가 Azure AD로 통합되도록 구성하려면 Procore SSO를 갤러리에서 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Procore SSO** 를 입력합니다.
1. 결과 패널에서 **Procore SSO** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-procore-sso"></a>Procore SSO에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Procore SSO에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Procore SSO의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Procore SSO에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행하세요.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Procore SSO 구성](#configure-procore-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Procore SSO 테스트 사용자 만들기](#create-procore-sso-test-user)** - 사용자의 Azure AD 표현과 연결된 B.Simon에 해당하는 사용자를 Procore SSO에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Procore SSO** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. 앱이 Azure와 이미 사전 통합되었으므로 사용자는 **기본 SAML 구성** 섹션에서 아무 단계도 수행할 필요가 없습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **Procore SSO 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Procore SSO에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Procore SSO** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-procore-sso"></a>Procore SSO 구성 

1. **Procore SSO** 쪽에서 Single Sign-On을 구성하려면 Procore 회사 사이트에 관리자로 로그인합니다.

2. 도구 상자 드롭다운에서 **관리** 를 클릭하여 SSO 설정 페이지를 엽니다.

    ![스크린샷은 디렉터리가 선택된 Procore 회사 사이트를 보여줍니다.](./media/procoresso-tutorial/admin.png)

3. 아래 설명에 따라 값을 상자에 붙여넣습니다.

    ![스크린샷은 사용자 추가 대화 상자를 보여줍니다.](./media/procoresso-tutorial/setting.png)   

    a. **Single Sign On 발급자 URL** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    b. **SAML 로그온 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 식별자** 값을 붙여넣습니다.

    다. 이제 Azure Portal에 위에서 다운로드한 **페더레이션 메타데이터 XML** 을 열고 **X509Certificate** 라는 태그에 인증서를 복사합니다. 복사한 값을 **Single Sign-On x509 인증서** 상자에 붙여 넣습니다.

4. **변경 내용 저장** 을 클릭합니다.

5. 이러한 설정 후에 Procore에 로그인할 때 사용한 **도메인 이름**(예: **contoso.com**)을 [Procore 지원 팀](https://support.procore.com/)에 보내서 해당 도메인에 대해 페더레이션된 SSO를 활성화하도록 해야 합니다.

### <a name="create-procore-sso-test-user"></a>Procore SSO 테스트 사용자 만들기

Procore SSO 쪽에서 Procore 테스트 사용자를 만들려면 다음 단계를 수행하세요.

1. Procore 회사 사이트에 관리자로 로그인합니다.    

2. 도구 상자 드롭다운에서 **디렉터리** 를 클릭하여 회사 디렉터리 페이지를 엽니다.

    ![스크린샷은 도구 상자에서 디렉터리가 선택된 Procore 회사 사이트를 보여줍니다.](./media/procoresso-tutorial/directory.png)

3. **Add a Person**(사람 추가) 옵션을 클릭하여 양식을 열고 다음 옵션을 입력합니다.

    ![스크린샷은 사용자 정보를 입력할 수 있는 Boylan Construction에 사람 추가를 보여줍니다.](./media/procoresso-tutorial/user.png)

    a. **이름** 텍스트 상자에 사용자의 이름(예: **Britta**)을 입력합니다.

    b. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

    다. **이메일 주소** 텍스트 상자에 사용자의 이메일 주소(예: BrittaSimon@contoso.com)를 입력합니다.

    d. **Permission Template**(권한 템플릿)에 **Apply Permission Template Later**(권한 템플릿 나중에 적용)를 선택합니다.

    e. **만들기** 를 클릭합니다.

4. 새로 추가된 연락처에 대한 세부 정보를 확인하고 업데이트합니다.

    ![스크린샷은 사용자 설정을 확인할 수 있는 편집 페이지를 보여줍니다.](./media/procoresso-tutorial/details.png)

5. **Save and Send Invitiation**(저장 및 초대 보내기)(메일을 통한 초대가 필요한 경우) 또는 **저장**(바로 저장)을 클릭하여 사용자 등록을 완료합니다.
    
    ![스크린샷을 초대를 저장하고 보낼 수 있는 현재 프로젝트 설정을 보여줍니다.](./media/procoresso-tutorial/save.png)

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 Procore SSO에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Procore SSO 타일을 클릭하면 SSO를 설정한 Procore SSO에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Procore SSO를 구성하면 세션 제어를 적용하여 조직에서 중요한 데이터의 반출과 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
