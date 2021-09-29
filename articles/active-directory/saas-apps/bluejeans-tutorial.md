---
title: '자습서: BlueJeans for Azure AD와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 BlueJeans for Azure AD 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2021
ms.author: jeedes
ms.openlocfilehash: 9d4931b07c4564f4a04c8ec46f0666e696854aa4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124747872"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>자습서: BlueJeans for Azure AD와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 BlueJeans for Azure AD를 연결하는 방법에 대해 알아봅니다. BlueJeans for Azure AD와 Azure AD를 연결하는 경우 다음을 수행할 수 있습니다.

* BlueJeans for Azure AD에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 BlueJeans for Azure AD에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* BlueJeans for Azure AD Azure AD SSO(Single Sign-On)가 설정된 구독

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* BlueJeans for Azure AD에서 **SP** 시작 SSO를 지원합니다.

* BlueJeans for Azure AD에서 [**자동화된** 사용자 프로비저닝](bluejeans-provisioning-tutorial.md)을 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-bluejeans-for-azure-ad-from-the-gallery"></a>갤러리에서 BlueJeans for Azure AD 추가

BlueJeans for Azure AD의 Azure AD 통합을 구성하려면 갤러리의 BlueJeans for Azure AD를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **BlueJeans for Azure AD** 를 입력합니다.
1. 결과 패널에서 **BlueJeans for Azure AD** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-bluejeans-for-azure-ad"></a>BlueJeans for Azure AD에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 BlueJeans for Azure AD에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 BlueJeans for Azure AD의 관련 사용자 간에 연결 관계를 설정해야 합니다.

BlueJeans for Azure AD에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[BlueJeans for Azure AD SSO 구성](#configure-bluejeans-for-azure-ad-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[BlueJeans for Azure AD 테스트 사용자 만들기](#create-bluejeans-for-azure-ad-test-user)** - B.Simon의 Azure AD 표현과 연결되는 해당 사용자를 BlueJeans for Azure AD에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **BlueJeans for Azure AD** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그인 URL** 텍스트 상자에서 `https://<companyname>.bluejeans.com` 패턴을 사용하여 URL을 입력합니다.

    a. **식별자(엔터티 ID)** 텍스트 상자에 `http://samlsp.bluejeans.com` URL을 입력합니다.

    a. **회신 URL** 텍스트 상자에서 URL `https://bluejeans.com/sso/saml2/`을 입력합니다.

    > [!NOTE]
    > 로그온 URL 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이 값을 얻으려면 [BlueJeans for Azure AD 클라이언트 지원 팀](https://support.bluejeans.com/contact)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. BlueJeans 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며, SAML 토큰 특성 구성에 사용자 할당 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 BlueJeans 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | Name |  원본 특성|
    | ---------| --------- |
    | Phone | user.telephonenumber |
    | title | user.jobtitle |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **BlueJeans for Azure AD 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B. Simon에게 BlueJeans for Azure AD에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **BlueJeans for Azure AD** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 &quot;기본 액세스&quot; 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name=&quot;configure-bluejeans-for-azure-ad-sso&quot;></a>BlueJeans for Azure AD SSO 구성

1. 다른 웹 브라우저 창에서 **BlueJeans for Azure AD** 회사 사이트에 관리자로 로그인합니다.

2. **관리자 \> 그룹 설정 \> 보안** 으로 이동합니다.

    ![스크린샷은 그룹 설정 및 보안이 선택된 상태에서 관리 탭이 선택된 브라우저 창의 일부를 보여줍니다.](./media/bluejeans-tutorial/admin.png &quot;Admin")

3. **보안** 섹션에서 다음 단계를 수행합니다.

    ![SAML Single Sign On](./media/bluejeans-tutorial/security.png "SAML Single Sign On")

    a. **SAML Single Sign On** 을 선택합니다.

    b. **자동 프로비전닝 사용** 을 선택합니다.

4. 다음 단계로 이동합니다.

    ![인증서 경로](./media/bluejeans-tutorial/certificate.png "인증서 경로")

    a. **파일 선택** 을 클릭하여 Azure Portal에서 다운로드한 base-64로 인코드된 인증서를 업로드합니다.

    b. Azure Portal에서 복사한 **로그인 URL** 값을 **로그인 URL** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한 **암호 변경 URL** 값을 **암호 변경 URL** 텍스트 상자에 붙여넣습니다.

    d. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.

5. 다음 단계로 이동합니다.

    ![변경 내용 저장](./media/bluejeans-tutorial/changes.png "변경 내용 저장")

    a. **사용자 Id** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`을 입력합니다.

    b. **전자 메일 주소** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`를 입력합니다.

    다. **변경 내용 저장** 을 클릭합니다.

### <a name="create-bluejeans-for-azure-ad-test-user"></a>BlueJeans for Azure AD 테스트 사용자 만들기

이 섹션의 목적은 BlueJeans for Azure AD에서 B.Simon이라는 사용자를 만드는 것입니다. BlueJeans for Azure AD는 기본적으로 사용하도록 설정되는 자동 사용자 프로비저닝을 지원합니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](bluejeans-provisioning-tutorial.md)에서 제공합니다.

**사용자를 수동으로 만들어야 할 경우 다음 단계를 수행합니다.**

1. **BlueJeans for Azure AD** 회사 사이트에 관리자 권한으로 로그인합니다.

2. **관리자 \> 사용자 관리 \> 사용자 추가** 로 이동합니다.

    ![스크린샷은 사용자 관리 및 사용자 추가가 선택된 상태에서 관리 탭이 선택된 브라우저 창의 일부를 보여줍니다.](./media/bluejeans-tutorial/add-user.png "Admin")

    > [!IMPORTANT]
    > **사용자 추가** 탭은 **보안 탭** 에서 **자동 프로비저닝 사용** 이 선택 취소된 경우에만 사용할 수 있습니다.

3. **사용자 추가** 섹션에서 다음 단계를 수행합니다.

    ![스크린샷은 이 단계에서 설명한 정보를 입력하는 사용자 추가 섹션을 보여줍니다.](./media/bluejeans-tutorial/new-user.png "사용자 추가")

    a. **이름** 텍스트 상자에 사용자의 이름(예: **B**)을 입력합니다.

    b. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

    다. **BlueJeans for Azure AD 사용자 이름 선택** 텍스트 상자에 사용자 이름(예: **Brittasimon**)을 입력합니다.

    d. **암호 만들기** 텍스트 상자에 암호를 입력합니다.

    e. **회사** 텍스트 상자에 회사를 입력합니다.

    f. **이메일 주소** 텍스트 상자에 `b.simon@contoso.com`과 같은 사용자의 이메일을 입력합니다.

    g. **BlueJeans for Azure AD 모임 ID 만들기** 텍스트 상자에 모임 ID를 입력합니다.

    h. **중재자 암호 선택** 텍스트 상자에 암호를 입력합니다.

    i. **계속** 을 클릭합니다.

    ![스크린샷은 사용자 추가 단추가 선택된 상태에서 설정 및 기능을 볼 수 있는 사용자 추가 섹션을 보여줍니다.](./media/bluejeans-tutorial/settings.png "사용자 추가")

    J. **사용자 추가** 를 클릭합니다.

> [!NOTE]
> BlueJeans for Azure AD에서 제공하는 기타 모든 BlueJeans for Azure AD 사용자 계정 만들기 도구 또는 API를 사용하여 Azure AD 사용자 계정을 프로비저닝할 수 있습니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 BlueJeans for Azure AD 로그온 URL로 리디렉션됩니다. 

* BlueJeans for Azure AD 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 BlueJeans for Azure AD 타일을 클릭하면 BlueJeans for Azure AD 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

BlueJeans for Azure AD가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).