---
title: '자습서: FreshDesk와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 FreshDesk 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 53ab7bcc6944d3f7650018238bd326037c5fff41
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746950"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>자습서: FreshDesk와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 FreshDesk를 통합하는 방법에 대해 알아봅니다. Azure AD와 FreshDesk를 통합하면 다음 작업을 수행할 수 있습니다.

* FreshDesk에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 FreshDesk에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.
 
* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* FreshDesk SSO(Single Sign-On)가 설정된 구독.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* FreshDesk에서 **SP** 시작 SSO를 지원합니다.

## <a name="add-freshdesk-from-the-gallery"></a>갤러리에서 FreshDesk 추가

FreshDesk의 Azure AD 통합을 구성하려면 갤러리의 FreshDesk를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **FreshDesk** 를 입력합니다.
1. 결과 패널에서 **FreshDesk** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>FreshDesk에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 FreshDesk에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 FreshDesk의 관련 사용자 간에 연결 관계를 설정해야 합니다.

FreshDesk에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[FreshDesk SSO 구성](#configure-freshdesk-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[FreshDesk 테스트 사용자 만들기](#create-freshdesk-test-user)** - Azure AD 표현과 연결된 Britta Simon에 해당하는 사용자를 FreshDesk에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

1. Azure Portal의 **FreshDesk** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<tenant-name>.freshdesk.com` 패턴 또는 Freshdesk에서 제안한 다른 값을 사용하는 URL을 입력합니다.

    b. **ID(엔터티 ID)** 텍스트 상자에서 `https://<tenant-name>.freshdesk.com` 패턴 또는 Freshdesk에서 제안한 다른 값을 사용하는 URL을 입력합니다.
     
    다. **회신 URL** 텍스트 상자에서 `https://<tenant-name>.freshdesk.com/login/saml` 패턴을 사용하여 URL을 입력합니다.
    
    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL, 식별자 및 회신 URL로 업데이트합니다. 이 값을 얻으려면 [FreshDesk Client 지원 팀](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. FreshDesk 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 할당 특성 매핑을 추가해야 합니다. 다음 스크린샷은 기본 특성 목록을 보여주며, **고유한 사용자 식별자** 는 **user.userprincipalname** 과 매핑되지만, FreshDesk는 이 클레임이 **user.mail** 과 매핑되어야 하므로 [편집] 아이콘을 클릭하고 특성 매핑을 변경하여 특성 매핑을 편집해야 합니다.

    ![이미지](common/edit-attribute.png)

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **FreshDesk 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 FreshDesk에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **FreshDesk** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 &quot;기본 액세스&quot; 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name=&quot;configure-freshdesk-sso&quot;></a>FreshDesk SSO 구성

1. 다른 웹 브라우저 창에서 Freshdesk 회사 사이트에 관리자로 로그인합니다.

2. **보안 아이콘** 을 선택하고 **보안** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-On](./media/freshdesk-tutorial/configure-1.png &quot;Single Sign On")
  
    a. **Single Sign On** 에서 **켬** 을 선택합니다.

    b. **로그인 방법** 에서 **SAML SSO** 를 선택합니다.

    다. Azure Portal에서 복사한 **엔터티 ID** 값을 **IDP에서 제공한 엔터티 ID** 텍스트 상자에 붙여넣습니다.

    d. **SAML SSO URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    e. **서명 옵션** 의 드롭다운에서 **서명된 어설션만** 을 선택합니다.

    f. **로그아웃 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.

    g. 앞에서 얻은 **인증서(Base64)** 값을 **보안 인증서** 텍스트 상자에 붙여넣습니다.
  
    h. **저장** 을 클릭합니다.

## <a name="create-freshdesk-test-user"></a>FreshDesk 테스트 사용자 만들기

Azure AD 사용자가 FreshDesk에 로그인할 수 있도록 하려면 FreshDesk로 프로비전되어야 합니다.  
FreshDesk의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **Freshdesk** 테넌트에 로그인합니다.

1. 왼쪽 메뉴에서 **관리자** 를 클릭하고, **일반 설정** 에서 **에이전트** 를 클릭합니다.
  
    ![에이전트](./media/freshdesk-tutorial/create-user-1.png "에이전트")

1. **새 에이전트** 를 클릭합니다.

    ![새 에이전트](./media/freshdesk-tutorial/create-user-2.png "새 에이전트")

1. [에이전트 정보] 대화 상자에서 필수 필드를 입력하고 **에이전트 만들기** 를 클릭합니다.

    ![에이전트 정보](./media/freshdesk-tutorial/create-user-3.png "에이전트 정보")

    >[!NOTE]
    >Azure AD 계정 보유자는 활성화되기 전에 계정을 확인하기 위한 링크를 포함한 전자 메일을 받습니다.
    >
    >[!NOTE]
    >다른 Freshdesk 사용자 계정 생성 도구 또는 Freshdesk가 제공한 API를 사용하여 Azure AD 사용자 계정을 FreshDesk에 프로비저닝할 수 있습니다.

### <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 FreshDesk 로그온 URL로 리디렉션됩니다. 

* FreshDesk 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 FreshDesk 타일을 클릭하면 SSO를 설정한 FreshDesk에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

FreshDesk가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).