---
title: '자습서: SkyDesk Email과 Azure AD SSO 통합'
description: Azure Active Directory 및 SkyDesk Email 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/13/2021
ms.author: jeedes
ms.openlocfilehash: 4d28d57a20f1878e32067faa9e68f6f3ac30640b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245551"
---
# <a name="tutorial-azure-ad-sso-integration-with-skydesk-email"></a>자습서: SkyDesk Email과 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SkyDesk Email을 통합하는 방법에 대해 알아봅니다. SkyDesk Email을 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* SkyDesk Email에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 SkyDesk Email에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SkyDesk Email SSO(Single Sign-On)이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* SkyDesk Email에서는 **SP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-skydesk-email-from-the-gallery"></a>갤러리에서 SkyDesk Email 추가

SkyDesk Email의 Azure AD 통합을 구성하려면 갤러리의 SkyDesk Email을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **SkyDesk Email** 을 입력합니다.
1. 결과 패널에서 **SkyDesk Email** 을 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-skydesk-email"></a>SkyDesk Email에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 SkyDesk Email에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SkyDesk Email의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SkyDesk Email에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[SkyDesk Email SSO 구성](#configure-skydesk-email-sso)** -애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[SkyDesk Email 테스트 사용자 만들기](#create-skydesk-email-test-user)** - 사용자의 Azure AD 표현과 연결된 SkyDesk Email에 B.Simon의 상대 사용자를 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **SkyDesk Email** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://mail.skydesk.jp/portal/<companyname>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 값을 얻으려면 [SkyDesk Email 클라이언트 지원 팀](https://www.skydesk.jp/apps/support/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **SkyDesk Email 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 SkyDesk Email에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **SkyDesk Email** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-skydesk-email-sso"></a>SkyDesk Email SSO 구성

1. 다른 웹 브라우저에서 SkyDesk Email 계정에 관리자 권한으로 로그온합니다.

1. 위쪽 메뉴에서 **설정** 을 클릭한 다음 **조직** 을 클릭합니다.

    ![스크린샷은 설치 메뉴에서 선택한 조직을 보여줍니다.](./media/skydeskemail-tutorial/menu.png)
  
1. 왼쪽 패널에서 **도메인** 을 클릭합니다.

    ![스크린샷은 제어판에서 선택한 도메인을 보여줍니다.](./media/skydeskemail-tutorial/domain.png)

1. **도메인 추가** 를 클릭합니다.

    ![스크린샷은 선택된 도메인 추가를 보여줍니다.](./media/skydeskemail-tutorial/user.png)

1. 도메인 이름을 입력하고 도메인을 확인합니다.

    ![스크린샷은 도메인을 입력할 수 있는 도메인 추가 탭을 보여줍니다.](./media/skydeskemail-tutorial/details.png)

1. 왼쪽 패널에서 **SAML 인증** 을 클릭합니다.

    ![스크린샷은 제어판에서 선택한 SAML 인증을 보여줍니다.](./media/skydeskemail-tutorial/authentication.png)

1. **SAML 인증** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![스크린샷은 설명된 값을 입력할 수 있는 SAML 인증 세부 정보 대화 상자를 보여줍니다.](./media/skydeskemail-tutorial/portal.png)

    > [!NOTE]
    > SAML 기반 인증을 사용하려면 **확인된 도메인** 또는 **포털 URL** 설정이 있어야 합니다. 고유의 이름으로 포털 URL을 설정할 수 있습니다.

    ![스크린샷은 이름을 입력하는 포털 URL을 보여줍니다.](./media/skydeskemail-tutorial/file.png)

    a. Azure Portal에서 복사한 **로그인 URL** 값을 **로그인 URL** 텍스트 상자에 붙여넣습니다.

    b. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃** URL 텍스트 상자에 붙여넣습니다.

    다. **암호 변경 URL** 은 선택 사항이므로 비워 둡니다.

    d. **파일에서 키 가져오기** 를 클릭하여 Azure Portal에서 다운로드한 인증서를 선택하고 **열기** 를 클릭하여 인증서를 업로드합니다.

    e. **알고리즘** 으로 **RSA** 를 선택합니다.

    f. **확인** 을 클릭하여 변경 내용을 저장합니다.

### <a name="create-skydesk-email-test-user"></a>SkyDesk Email 테스트 사용자 만들기

이 섹션에서는 SkyDesk Email에서 Britta Simon이라는 사용자를 만듭니다.

SkyDesk Email의 왼쪽 패널에서 **사용자 액세스** 를 클릭하고 사용자 이름을 입력합니다.

![스크린샷은 제어판에서 선택한 사용자 액세스를 보여줍니다.](./media/skydeskemail-tutorial/create-users.png)

> [!NOTE]
> 일괄 사용자를 만들어야 하는 경우에는 [SkyDesk Email 클라이언트 지원 팀](https://www.skydesk.jp/apps/support/)에 문의해야 합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 SkyDesk Email 로그온 URL로 리디렉션됩니다. 

* SkyDesk Email 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 SkyDesk Email 타일을 클릭하면 SkyDesk Email 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

SkyDesk Email을 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).