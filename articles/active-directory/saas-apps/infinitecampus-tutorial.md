---
title: '자습서: Infinite Campus와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Infinite Campus 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2021
ms.author: jeedes
ms.openlocfilehash: 817bc6c7b425af9cc02bc339967cb29b1c4b81fd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285237"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>자습서: Infinite Campus와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Infinite Campus를 통합하는 방법에 대해 알아봅니다. Azure AD와 Infinite Campus를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Infinite Campus에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Infinite Campus에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

Infinite Campus와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Infinite Campus Single Sign-On이 설정된 구독
* 구성을 완료하려면 최소한 Azure Active Directory 관리자이고, "SIS(Student Information System)"의 캠퍼스 제품 보안 역할을 갖고 있어야 합니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Infinite Campus는 **SP** 시작 SSO를 지원합니다.

## <a name="add-infinite-campus-from-the-gallery"></a>갤러리에서 Infinite Campus 추가

Infinite Campus의 Azure AD 통합을 구성하려면 갤러리의 Infinite Campus를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Infinite Campus** 를 입력합니다.
1. 결과 패널에서 **Infinite Campus** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-infinite-campus"></a>Infinite Campus에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Infinite Campus에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Infinite Campus의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Infinite Campus에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Infinite Campus SSO 구성](#configure-infinite-campus-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Infinite Campus 테스트 사용자 만들기](#create-infinite-campus-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Infinite Campus에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Infinite Campus** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. 기본 SAML 구성 섹션에서 다음 단계를 수행합니다(도메인은 호스팅 모델에 따라 달라지지만, **FULLY-QUALIFIED-DOMAIN** 값은 설치된 Infinite Campus와 일치해야 함).

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. **식별자** 텍스트 상자에서 `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>` 패턴을 사용하여 URL을 입력합니다.

    다. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL** 을 복사한 후 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Infinite Campus에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Infinite Campus** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-infinite-campus-sso"></a>Infinite Campus SSO 구성

1. 다른 웹 브라우저 창에서 Infinite Campus에 보안 관리자 권한으로 로그인합니다.

2. 왼쪽 메뉴에서 **시스템 관리** 를 클릭합니다.

    ![관리자](./media/infinitecampus-tutorial/admin.png)

3. **사용자 보안** > **SAML 관리** > **SSO 서비스 공급자 구성** 으로 이동합니다.

    ![saml](./media/infinitecampus-tutorial/security.png)

4. **SSO 서비스 공급자 구성** 페이지에서 다음 단계를 수행합니다.

    ![sso](./media/infinitecampus-tutorial/configuration.png)

    a. **SAML Single Sign-On 사용** 을 선택합니다.

    b. **이름** 을 포함하도록 **선택적 특성 이름** 을 편집합니다.

    다. **IDP(ID 공급 기업) 서버 데이터 검색 옵션 선택** 섹션에서 **메타데이터 URL** 을 선택하고, Azure Portal에서 복사한 **앱 페더레이션 메타데이터 Url** 값을 상자에 붙여넣은 다음, **동기화** 를 클릭합니다.

    d. **동기화** 를 클릭하면 값이 **SSO 서비스 공급자 구성** 페이지에 자동으로 입력됩니다. 이러한 값이 위의 4단계에서 본 값과 일치하는지 확인할 수 있습니다.

    e. **저장** 을 클릭합니다.

### <a name="create-infinite-campus-test-user"></a>Infinite Campus 테스트 사용자 만들기

Infinite Campus는 인구 통계 기반 아키텍처입니다. Infinite Campus 플랫폼에 사용자를 추가하려면 [Infinite Campus 지원 팀](mailto:sales@infinitecampus.com)에 문의하세요.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Infinite Campus 로그온 URL로 리디렉션됩니다. 

* Infinite Campus 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Infinite Campus 타일을 클릭하면 Infinite Campus 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Infinite Campus가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
