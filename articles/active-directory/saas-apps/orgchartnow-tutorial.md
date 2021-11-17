---
title: '자습서: OrgChart Now와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 OrgChart Now 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: b326114013502840e05284ba6d7049273ea42a44
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287625"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>자습서: OrgChart Now와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 OrgChart Now를 통합하는 방법에 대해 알아봅니다. Azure AD와 OrgChart Now를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 OrgChart Now에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 OrgChart Now에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* OrgChart Now SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* OrgChart Now는 **SP** 및 **IDP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-orgchart-now-from-the-gallery"></a>갤러리에서 OrgChart Now 추가

OrgChart Now의 Azure AD 통합을 구성하려면 갤러리의 OrgChart Now를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **OrgChart Now** 를 입력합니다.
1. 결과 패널에서 **OrgChart Now** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-orgchart-now"></a>OrgChart Now에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 OrgChart Now에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 OrgChart Now의 관련 사용자 간에 연결 관계를 설정해야 합니다.

OrgChart Now에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[OrgChart Now SSO 구성](#configure-orgchart-now-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[OrgChart Now 테스트 사용자 만들기](#create-orgchart-now-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 OrgChart Now에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성 

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **OrgChart Now** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    **식별자** 텍스트 상자에서 `https://sso2.orgchartnow.com` URL을 입력합니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > `<YourEntityID>`는 **OrgChart Now 설정** 섹션에서 복사한 **Azure AD 식별자** 로, 자습서의 뒷부분에 설명되어 있습니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

7. **OrgChart Now 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 OrgChart Now에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **OrgChart Now** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-orgchart-now-sso"></a>OrgChart Now SSO 구성

**OrgChart Now** 쪽에서 Single Sign-On을 구성하려면 Azure Portal에서 다운로드한 **페더레이션 메타데이터 XML** 과 적절히 복사한 URL을 [OrgChart Now 지원 팀](mailto:ocnsupport@officeworksoftware.com)으로 보내야 합니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

### <a name="create-orgchart-now-test-user"></a>OrgChart Now 테스트 사용자 만들기

Azure AD 사용자가 OrgChart Now에 로그인할 수 있도록 하려면 이 사용자를 OrgChart Now로 프로비전해야 합니다. 

1. OrgChart Now는 JIT(Just-In-Time) 프로비전을 지원하며, 기본적으로 사용하도록 설정됩니다. 새 사용자가 아직 없는 경우 OrgChart Now에 액세스하려는 중에 만들어집니다. JIT(Just-In-Time) 사용자 프로비전 기능은 SSO 요청이 인식할 수 있는 IDP에서 나오고, SAML 어설션의 전자 메일을 사용자 목록에서 찾을 수 없을 때만 **읽기 전용** 사용자를 만듭니다. 이 자동 프로비전 기능을 사용하려면 OrgChart Now에 **General** 이라는 액세스 그룹을 들어야 합니다. 아래 단계에 따라 액세스 계정을 만드세요.

    a. UI의 오른쪽 위 모서리에 있는 **톱니** 아이콘을 클릭한 후에 **그룹 관리** 옵션으로 이동합니다.

      ![OrgChart Now 그룹](./media/orgchartnow-tutorial/groups.png)   

    b. **추가** 아이콘을 선택하고 그룹 이름을 **General** 로 지정한 후 **확인** 을 클릭합니다. 

      ![OrgChart Now 추가](./media/orgchartnow-tutorial/general.png)

    다. 일반 또는 읽기 전용 사용자가 액세스할 수 있게 하려는 폴더를 선택합니다.
    
      ![OrgChart Now 폴더](./media/orgchartnow-tutorial/folders.png)

    d. 관리 사용자만 수정할 수 있도록 폴더를 **잠급니다**. 그런 후 **확인** 을 누릅니다.

      ![OrgChart Now 잠금](./media/orgchartnow-tutorial/lock.png)

2. **관리** 사용자 및 **읽기/쓰기** 사용자를 만들려면 SSO를 통해 해당 권한 수준에 액세스하기 위해 사용자를 수동으로 만들어야 합니다. 사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

    a. 보안 관리자 권한으로 OrgChart Now에 로그인합니다.

    b. 오른쪽 위 모서리에서 **설정** 을 클릭한 후 **사용자 관리** 로 이동합니다.

      ![OrgChart Now 설정](./media/orgchartnow-tutorial/settings.png)

    다. **추가** 를 클릭하고 다음 단계를 수행합니다.

      ![OrgChart Now 관리](./media/orgchartnow-tutorial/manage-users.png)

    1. **사용자 ID** 텍스트 상자에 사용자 ID(예: **brittasimon\@contoso.com**)를 입력합니다.

    1. **이메일 주소** 텍스트 상자에 사용자의 이메일(예: **brittasimon\@contoso.com**)을 입력합니다.

    1. **추가** 를 클릭합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 OrgChart Now 로그온 URL로 리디렉션됩니다.  

* OrgChart Now 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 OrgChart Now에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 OrgChart Now 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 OrgChart Now에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

OrgChart Now가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
