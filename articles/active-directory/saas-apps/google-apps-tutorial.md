---
title: '자습서: Google Cloud (G Suite) Connector와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Google Cloud (G Suite) Connector 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/24/2021
ms.author: jeedes
ms.openlocfilehash: 941008288936a5b46d66305e8e8a8d70bcfc14b5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124833810"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>자습서: Google Cloud (G Suite) Connector와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Google Cloud (G Suite) Connector를 통합하는 방법에 대해 알아봅니다. Azure AD와 Google Cloud (G Suite) Connector를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Google Cloud (G Suite) Connector에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Google Cloud (G Suite) Connector에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독
* Google Cloud (G Suite) Connector SSO(Single Sign-On)를 사용하도록 설정된 구독
* Google Apps 구독 또는 Google Cloud Platform 구독.

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다. 이 문서는 새 사용자 Single Sign-On 환경을 사용하여 만들어졌습니다. 이전 환경을 계속 사용하고 있으면 설정이 다르게 표시됩니다. G Suite 애플리케이션의 Single Sign-On 설정에서 새로운 환경을 설정할 수 있습니다. **Azure AD, 엔터프라이즈 애플리케이션** 으로 이동하고, **Google Cloud (G Suite) Connector** 를 선택하고, **Single Sign-On** 을 선택한 다음, **새 환경 사용해 보기** 를 클릭합니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
* 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

1. **Q: 이 통합이 Google Cloud Platform SSO와 Azure AD의 통합을 지원하나요?**

    A: 예. Google Cloud Platform과 Google Apps는 동일한 인증 플랫폼을 공유합니다. 따라서 GCP 통합을 수행하려면 Google Apps로 SSO를 구성해야 합니다.

2. **Q: Chromebooks 및 기타 크롬 디바이스는 Azure AD Single Sign-On과 호환되나요?**
  
    A: 예, 사용자는 Azure AD 자격 증명을 사용하여 Chromebook 디바이스에 로그인할 수 있습니다. 사용자에게 자격 증명을 요구하는 메시지가 두 번 표시되는 이유에 대한 자세한 내용은 이 [Google Cloud (G Suite) Connector 지원 문서](https://support.google.com/chrome/a/answer/6060880)를 참조하세요.

3. **Q: Single Sign-On을 사용하도록 설정한 경우 사용자가 자신의 Azure AD 자격 증명을 사용하여 Google 클래스룸, GMail, Google 드라이브, YouTube 등과 같은 Google 제품에 로그인할 수 있나요?**

    A: 예, [Google Cloud (G Suite) Connector](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583)에 따라 조직에서 사용하거나 사용하지 않도록 설정할 수 있습니다.

4. **Q: 내 Google Cloud (G Suite) Connector 사용자 중 일부만 Single Sign-On을 사용할 수 있나요?**

    A: 아니요, Single Sign-On을 즉시 설정하려면 모든 Google Cloud (G Suite) Connector 사용자가 Azure AD 자격 증명으로 인증해야 합니다. Google Cloud (G Suite) Connector는 여러 ID 공급자를 사용하도록 지원하지 않으므로 Google Cloud (G Suite) Connector 환경의 ID 공급자는 Azure AD 또는 Google 중 하나일 수 있지만 동시에 둘 다일 수는 없습니다.

5. **Q: 사용자가 Windows를 통해 로그인하는 경우 암호를 요구하는 메시지가 표시되지 않고 Google Cloud (G Suite) Connector에 자동으로 인증되나요?**

    A: 이 시나리오에는 두 가지 옵션을 사용할 수 있습니다. 첫째, [Azure Active Directory 조인](../devices/overview.md)을 통해 Windows 10 디바이스에 로그인할 수 있습니다. 또는 [AD FS(Active Directory Federation Services)](../hybrid/plan-connect-user-signin.md) 배포를 통해 Azure AD에 Single Sign-On을 사용할 수 있도록 설정한 온-프레미스 Active Directory에 도메인 가입한 Windows 디바이스에 로그인할 수 있습니다. 두 옵션 모두에서 Azure AD와 Google Cloud (G Suite) Connector 간에 Single Sign-On을 사용하도록 설정하려면 다음 자습서의 단계를 수행해야 합니다.

6. **Q: "잘못된 이메일" 오류 메시지가 표시되면 어떻게 해야 하나요?**

    A: 이 설치의 경우 사용자가 로그인할 수 있으려면 이메일 특성이 필요합니다. 이 특성은 수동으로 설정할 수 없습니다.

    이메일 특성은 유효한 Exchange 라이선스가 있는 모든 사용자에 대한 자동으로 채워집니다. 사용자가 이메일을 사용할 수 없는 경우 애플리케이션이 액세스 권한을 얻기 위해 이 특성을 가져와야 하므로 이 오류가 수신됩니다.

    관리자 계정으로 portal.office.com으로 이동한 다음, 관리 센터, 청구서 발송, 구독을 클릭하고 Microsoft 365 구독을 선택한 다음, 사용자에게 할당을 클릭하고 가입을 확인할 사용자를 선택한 후 오른쪽 창에서 라이선스 편집을 클릭합니다.

    Microsoft 365 라이선스가 할당되면 적용하는 데 몇 분 정도 걸립니다. 그 후 user.mail 특성이 자동으로 채워지며, 문제가 해결됩니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Google Cloud (G Suite) Connector에서 **SP** 시작 SSO를 지원합니다.

* Google Cloud (G Suite) Connector에서 [**자동** 사용자 프로비저닝](./g-suite-provisioning-tutorial.md)을 지원합니다.

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>갤러리에서 Google Cloud (G Suite) Connector 추가

Google Cloud (G Suite) Connector가 Azure AD에 통합되도록 구성하려면 갤러리의 Google Cloud (G Suite) Connector를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Google Cloud (G Suite) Connector** 를 입력합니다.
1. 결과 패널에서 **Google Cloud (G Suite) Connector** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Google Cloud (G Suite) Connector에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Google Cloud (G Suite) Connector에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Google Cloud (G Suite) Connector의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Google Cloud(G Suite) Connector에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Google Cloud (G Suite) Connector SSO 구성](#configure-google-cloud-g-suite-connector-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Google Cloud (G Suite) Connector 테스트 사용자 만들기](#create-google-cloud-g-suite-connector-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Google Cloud (G Suite) Connector에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Google Cloud(G Suite) Connector** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 **Gmail** 에 대해 구성하려는 경우 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.

    | **식별자** |
    |----|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` | 
    | `https://google.com/a/<yourdomain.com>` |

    b. **회신 URL** 텍스트 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다. 

    | **회신 URL** |
    |-----|
    | `https://www.google.com/acs` |
    | `https://www.google.com/a/<yourdomain.com>/acs` |
    
    c. **로그온 URL** 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

1. **기본 SAML 구성** 섹션에서 **Google Cloud Platform** 에 대해 구성하려는 경우 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.
    
    | **식별자** |
    |-----|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    b. **회신 URL** 텍스트 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다. 
    
    | **회신 URL** |
    |-----|
    | `https://www.google.com/acs` |
    | `https://www.google.com/a/<yourdomain.com>/acs` |
    
    c. **로그온 URL** 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 해당 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. Google Cloud (G Suite) Connector는 Single Sign-On 구성에서 엔터티 ID/식별자 값을 제공하지 않으므로 **도메인 특정 발급자** 옵션의 선택을 취소하면 식별자 값은 `google.com`이 됩니다. **도메인 특정 발급자** 옵션을 선택하면 `google.com/a/<yourdomainname.com>`이 됩니다. **도메인 특정 발급자** 옵션을 선택/선택 취소하려면 자습서의 뒷부분에서 설명하는 **Google Cloud (G Suite) Connector SSO 구성** 섹션으로 이동해야 합니다. 자세한 내용은 [Google Cloud (G Suite) Connector 클라이언트 지원 팀](https://www.google.com/contact/)에 문의하세요.

1. Google Cloud (G Suite) Connector 애플리케이션에는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 하는 특정 형식의 SAML 어설션이 필요합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다. **고유한 사용자 ID** 의 기본값은 **user.userprincipalname** 이지만, 이 사용자 ID는 Google Cloud (G Suite) Connector에서 사용자의 이메일 주소와 매핑되어야 합니다. 목록에서 **user.mail** 특성을 사용하거나 조직 구성을 기반으로 적절한 특성 값을 사용할 수 있기 위해서입니다.

    ![이미지](common/default-attributes.png)

    > [!NOTE]
    > SAML 응답에 DisplayName 및 Surname 특성에 비표준 ASCII 문자가 포함되지 않았는지 확인합니다.    

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Google Cloud (G Suite) Connector 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    ```Logout URL
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0
    ```

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Google Cloud (G Suite) Connector에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Google Cloud (G Suite) Connector** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-google-cloud-g-suite-connector-sso"></a>Google Cloud (G Suite) Connector SSO 구성

1. 브라우저에서 새 탭을 열고 관리자 계정을 사용하여 [Google Cloud (G Suite) Connector 관리 콘솔](https://admin.google.com/)에 로그인합니다.

2. **보안** 을 클릭합니다. 링크가 보이지 않으면 화면 아래쪽에 있는 **기타 컨트롤** 메뉴에 숨겨져 있을 수 있습니다.

    ![보안을 클릭합니다.](./media/google-apps-tutorial/gapps-security.png)

3. **보안** 페이지에서 **SSO(Single Sign-On) 설정** 을 클릭합니다.

    ![SSO를 클릭합니다.](./media/google-apps-tutorial/security-gapps.png)

4. 다음 구성을 변경합니다.

    ![SSO를 구성합니다.](./media/google-apps-tutorial/configuration.png)

    a. **Setup SSO with third party identity provider**(타사 ID 공급자로 SSO 설정)을 선택합니다.

    b. Azure Portal에서 복사한 **로그인 URL** 값을 Google Cloud (G Suite) Connector의 **Sign-in page URL(로그인 페이지 URL)** 필드에 붙여넣습니다.

    다. Azure Portal에서 복사한 **로그아웃 URL** 값을 Google Cloud (G Suite) Connector의 **Sign-out page URL(로그아웃 페이지 URL)** 필드에 붙여넣습니다.

    d. Google Cloud (G Suite) Connectors에서 **Verification certificate(확인 인증서)** 에 대해 Azure Portal에서 다운로드한 인증서를 업로드합니다.   

    e. Azure AD에서 위의 **기본 SAML 구성** 에 설명된 참고 사항에 따라 **도메인 특정 발급자 사용** 옵션을 선택/선택 취소합니다.

    f. Google Cloud(G Suite) 커넥터의 **암호 변경 URL** 필드에 값을 `https://account.activedirectory.windowsazure.com/changepassword.aspx`로 입력합니다.

    g. **저장** 을 클릭합니다.

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Google Cloud (G Suite) Connector 테스트 사용자 만들기

이 섹션에서는 [Google Cloud (G Suite) Connector에서 B.Simon이라는 사용자를 만듭니다](https://support.google.com/a/answer/33310?hl=en). Google Cloud (G Suite) Connector에서 사용자가 수동으로 만들어지면 이제 사용자가 해당 Microsoft 365 로그인 자격 증명을 사용하여 로그인할 수 있습니다.

Google Cloud (G Suite) Connector는 자동 사용자 프로비저닝도 지원합니다. 자동 사용자 프로비저닝을 구성하려면 먼저 [자동 사용자 프로비저닝을 수행하도록 Google Cloud (G Suite) Connector를 구성](./g-suite-provisioning-tutorial.md)해야 합니다.

> [!NOTE]
> Single Sign-On을 테스트하기 전에 Azure AD에서 프로비전이 설정되지 않은 경우 해당 사용자가 Google Cloud (G Suite) Connector에 이미 있는지 확인하세요.

> [!NOTE]
> 사용자를 수동으로 만들어야 하는 경우 [Google 지원 팀](https://www.google.com/contact/)에 문의하세요.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Google Cloud(G Suite) Connector 로그온 URL로 리디렉션됩니다. 

* Google Cloud(G Suite) Connector 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Google Cloud(G Suite) Connector 타일을 클릭하면 Google Cloud(G Suite) Connector 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Google Cloud(G Suite) Connector가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).