---
title: '자습서: Central Desktop와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Central Desktop 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/04/2021
ms.author: jeedes
ms.openlocfilehash: 87b28323e418e77b0eeccb866de3a48a07c1c546
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124765857"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>자습서: Central Desktop와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Central Desktop을 통합하는 방법에 대해 알아봅니다. Azure AD와 Central Desktop을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Central Desktop에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 Azure AD 계정으로 Central Desktop에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Central Desktop SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Central Desktop은 **SP** 시작 SSO를 지원합니다.

## <a name="add-central-desktop-from-the-gallery"></a>갤러리에서 Central Desktop 추가

Central Desktop과 Azure AD의 통합을 구성하려면 갤러리의 Central Desktop을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Central Desktop** 을 입력합니다.
1. 결과 패널에서 **Central Desktop** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-central-desktop"></a>Central Desktop에서 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Central Desktop에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Central Desktop의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Central Desktop에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Resource Central SSO 구성](#configure-central-desktop-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Central Desktop 테스트 사용자 만들기](#create-central-desktop-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Central Desktop에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Central Desktop** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다.

    | **식별자** |
    |-------|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php` |
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php` |

    b. **회신 URL** 텍스트 상자에서 `https://<companyname>.centraldesktop.com/saml2-assertion.php` 패턴을 사용하여 URL을 입력합니다.

    다. **로그인 URL** 텍스트 상자에서 `https://<companyname>.centraldesktop.com` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 해당 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [Central Desktop 클라이언트 지원 팀](https://imeetcentral.com/contact-us)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(원시)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificateraw.png)

6. **Central Desktop 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Central Desktop에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Central Desktop** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 &quot;기본 액세스&quot; 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name=&quot;configure-central-desktop-sso&quot;></a>Central Desktop SSO 구성

1. **Central Desktop** 테넌트에 로그인합니다.

2. **설정** 으로 이동합니다. **고급** 을 선택한 다음, **Single Sign-On** 을 선택합니다.

    ![설정 - 고급](./media/central-desktop-tutorial/settings.png &quot;설정 - 고급")

3. **Single Sign-On 설정** 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-On 설정](./media/central-desktop-tutorial/configuration.png "Single Sign On 설정")

    a. **SAML v2 Single Sign-On 사용** 을 선택합니다.

    b. **SSO URL** 상자에 Azure Portal에서 복사한 **Azure Ad 식별자** 값을 붙여넣습니다.

    다. **SSO 로그인 URL** 상자에 Azure Portal에서 복사한 **로그인 URL** 을 붙여넣습니다.

    d. **SSO 로그아웃 URL** 상자에 Azure Portal에서 복사한 **로그아웃 URL** 을 붙여넣습니다.

4. **메시지 서명 확인 방법** 섹션에서 다음 단계를 수행합니다.

    ![메시지 서명 확인 방법](./media/central-desktop-tutorial/certificate.png "메시지 서명 확인 방법")

    a. **인증서** 를 선택합니다.

    b. **SSO 인증서** 목록에서 **RSH SHA256** 을 선택합니다.

    다. 다운로드한 인증서를 메모장에서 엽니다. 그런 다음, 인증서의 내용을 복사하여 **SSO 인증서** 필드에 붙여넣습니다.

    d. **SAMLv2 로그인 페이지에 대한 링크 표시** 를 선택합니다.

    e. **업데이트** 를 선택합니다.

### <a name="create-central-desktop-test-user"></a>Central Desktop 테스트 사용자 만들기

Azure AD 사용자가 로그인할 수 있도록 Central Desktop 애플리케이션에 프로비전되어야 합니다. 이 섹션은 Central Desktop에 Azure AD 사용자 계정을 만드는 방법을 설명합니다.

> [!NOTE]
> Azure AD 사용자 계정을 프로비전하려면 다른 Central Desktop 사용자 계정 생성 도구 또는 Central Desktop가 제공한 API를 사용합니다.

**Central Desktop에 사용자 계정을 프로 비전하려면**

1. Central Desktop 테넌트에 로그인합니다.

2. **사람** 을 선택한 다음, **내부 멤버 추가** 를 선택합니다.

    ![사람](./media/central-desktop-tutorial/members.png "사람")

3. **새 멤버의 메일 주소** 상자에 프로비전할 Azure AD 계정을 입력한 후, **다음** 을 선택합니다.

    ![새 멤버의 메일 주소](./media/central-desktop-tutorial/add-members.png "새 멤버의 이메일 주소")

4. **내부 멤버 추가** 를 선택합니다.

    ![내부 멤버 추가](./media/central-desktop-tutorial/account.png "내부 멤버 추가")

   > [!NOTE]
   > 추가한 사용자는 해당 계정을 활성화하기 위한 확인 링크가 포함된 메일을 받습니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Central Desktop 로그온 URL로 리디렉션됩니다. 

* Central Desktop 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Central Desktop 타일을 클릭하면 Central Desktop 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Central Desktop이 구성되면 세션 제어를 적용하여 조직에서 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).