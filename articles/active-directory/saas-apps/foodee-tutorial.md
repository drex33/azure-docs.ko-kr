---
title: '자습서: Foodee와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Foodee 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2021
ms.author: jeedes
ms.openlocfilehash: ce15d098f1713a1483f4a5c054f2c348d92eb228
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604932"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-foodee"></a>자습서: Foodee와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Foodee를 통합하는 방법에 대해 알아봅니다. Foodee를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Foodee에 액세스할 수 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Foodee에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Foodee SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Foodee에서 **SP 및 IDP** 시작 SSO를 지원합니다.
* Foodee에서 **Just In Time** 사용자 프로비저닝을 지원합니다.
* Foodee에서 [자동화된 사용자 프로비저닝](foodee-provisioning-tutorial.md)을 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-foodee-from-the-gallery"></a>갤러리에서 Foodee 추가

Foodee가 Azure AD에 통합되도록 구성하려면 갤러리의 Foodee를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Foodee** 를 입력합니다.
1. 결과 패널에서 **Foodee** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-foodee"></a>Foodee에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Foodee에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Foodee의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Foodee에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Foodee SSO 구성](#configure-foodee-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Foodee 테스트 사용자 만들기](#create-foodee-test-user)** - Foodee에서 B.Simon의 Azure AD 표현과 연결된 해당 사용자를 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Foodee** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    **회신 URL** 텍스트 상자에 `https://concierge.food.ee/sso/saml/<INSTANCENAME>/consume` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://concierge.food.ee/sso/saml/<INSTANCENAME>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Foodee 클라이언트 지원 팀](mailto:dev@food.ee)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Foodee 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Foodee에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Foodee** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="configure-foodee-sso"></a>Foodee SSO 구성

1. Foodee 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **Foodee 설정** 을 클릭하면 Foodee 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Foodee에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-4단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. Foodee를 수동으로 설정하려면 새 웹 브라우저 창을 열고 Foodee 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

4. 페이지의 오른쪽 위 모서리에서 **프로필 로고** 를 클릭한 다음, **Single Sign On** 으로 이동하여 다음 단계를 수행합니다.

   ![Foodee 구성](./media/foodee-tutorial/profile-logo.png)

   1. **IDP NAME**(IDP 이름) 텍스트 상자에서 ex:Azure와 같은 이름을 입력합니다.
   1. 메모장에서 페더레이션 메타데이터 XML을 열고, 내용을 복사하여 **IDP METADATA XML**(IDP 메타데이터 XML) 텍스트 상자에 붙여넣습니다.
   1. **저장** 을 클릭합니다.

### <a name="create-foodee-test-user"></a>Foodee 테스트 사용자 만들기

이 섹션에서는 Foodee에서 B.Simon이라는 사용자를 만듭니다. B. Simon는 기본적으로 사용하도록 설정된 Just-In-Time 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Foodee에 사용자가 아직 없는 경우 Foodee에 액세스하려고 할 때 새 사용자가 만들어집니다.

Foodee에서 자동화된 사용자 프로비저닝도 지원합니다. 자동화된 사용자 프로비저닝 구성 방법에 대한 자세한 내용은 [여기](./foodee-provisioning-tutorial.md)를 참조하세요.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

SP 시작:

* Azure Portal에서 이 애플리케이션 테스트를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Foodee 로그온 URL로 리디렉션됩니다.

* Foodee 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

IDP 시작:

* Azure Portal에서 이 애플리케이션 테스트를 클릭합니다. 그러면 SSO를 설정한 Foodee에 자동으로 로그인되어야 합니다.

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Foodee 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작할 수 있는 애플리케이션 로그온 페이지로 리디렉션됩니다. IDP 모드로 구성된 경우에는 SSO를 설정한 Foodee에 자동으로 로그인되어야 합니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

Foodee를 구성하면 세션 제어를 적용하여 조직의 중요한 데이터 반출과 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).