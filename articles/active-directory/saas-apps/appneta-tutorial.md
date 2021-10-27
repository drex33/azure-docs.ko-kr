---
title: '자습서: AppNeta Performance Manager와 Azure AD SSO 통합'
description: Azure Active Directory와 AppNeta Performance Managerr 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: jeedes
ms.openlocfilehash: 43abd1fef96a6730405fae438e3441f4982ae79c
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129995124"
---
# <a name="tutorial-azure-ad-sso-integration-with-appneta-performance-manager"></a>자습서: AppNeta Performance Manager와 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 AppNeta Performance Manager를 통합하는 방법에 대해 알아봅니다. AppNeta Performance Manager를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

- AppNeta Performance Manager에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
- 사용자가 자신의 Azure AD 계정으로 AppNeta Performance Manager에 자동으로 로그온되도록 설정합니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
- AppNeta Performance Manager SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

- AppNeta Performance Manager에서 **SP** 시작 SSO를 지원합니다.
- AppNeta Performance Manager에서 **Just In Time** 사용자 프로비저닝을 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-appneta-performance-manager-from-the-gallery"></a>갤러리에서 AppNeta Performance Manager 추가

AppNeta Performance Manager의 Azure AD 통합을 구성하려면 갤러리의 AppNeta Performance Manager를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **AppNeta Performance Manager** 를 입력합니다.
1. 결과 패널에서 **AppNeta Performance Manager** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-appneta-performance-manager"></a>AppNeta Performance Manager용 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 AppNeta Performance Manager에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 AppNeta Performance Manager의 관련 사용자 간에 연결 관계를 설정해야 합니다.

AppNeta Performance Manager에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
   1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
   1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[AppNeta Performance Manager SSO 구성](#configure-appneta-performance-manager-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
   1. **[AppNeta Performance Manager 테스트 사용자 만들기](#create-appneta-performance-manager-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 AppNeta Performance Manager에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **AppNeta Performance Manager** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](./media/appneta-tutorial/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

   a. **로그온 URL** 텍스트 상자에서 `https://<subdomain>.pm.appneta.com` 패턴을 사용하는 URL을 입력합니다.

   b. [회신 URL(Assertion Consumer Service URL)] 필드에서 `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`를 입력합니다.

   > [!NOTE]
   > 위의 로그온 URL 값은 하나의 예입니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이 값을 얻으려면 [AppNeta Performance Manager 고객 지원 팀](mailto:support@appneta.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. AppNeta Performance Manager 애플리케이션은 특정 형식의 SAML 어설션이 필요하기 때문에 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여줍니다.

   ![SAML 토큰의 기본 특성을 보여 주는 스크린샷](./media/appneta-tutorial/edit-attribute.png)

1. 위에서 언급한 특성 외에도 AppNeta Performance Manager 애플리케이션에는 아래에서 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

   | Name      | 원본 특성       |
   | --------- | ---------------------- |
   | firstName | user.givenname         |
   | lastName  | user.surname           |
   | 이메일     | user.userprincipalname |
   | name      | user.userprincipalname |
   | groups    | user.assignedroles     |
   | phone     | user.telephonenumber   |
   | title     | user.jobtitle          |
   |           |                        |

1. "groups" SAML 어설션을 제대로 전달하려면 앱 역할을 구성하고 AppNeta Performance Manager 내에서 설정된 역할 매핑과 일치하도록 값을 설정해야 합니다. **Azure Active Directory** > **앱 등록** >  **모든 애플리케이션** 아래에서 **Appneta Performance Manager** 를 선택합니다.

   ![아래쪽에 Appneta Performance Manager를 사용한 앱 등록을 보여주는 스크린샷. ](./media/appneta-tutorial/app-registrations.png)

1. 왼쪽 창에서 **앱 역할** 을 클릭합니다. 다음 화면이 나타납니다.

   ![아래쪽에 Appneta Performance Manager가 있는 앱 역할을 보여주는 스크린샷. ](./media/appneta-tutorial/app-roles.png)

1. **앱 역할 만들기** 를 클릭합니다.
1. **앱 역할 만들기** 화면에서 다음 단계를 수행합니다.
   1. **표시 이름** 필드에 역할 이름을 입력합니다.
   1. **허용되는 멤버 유형** 필드에서 **사용자/그룹** 을 선택합니다.
   1. **값** 필드에서 AppNeta Performance Manager 역할 매핑에 설정된 보안 그룹의 값을 입력합니다.
   1. **설명** 필드에 역할에 대한 설명을 입력합니다.
   1. **적용** 을 클릭합니다.

   ![설명된 대로 필드가 채워진 앱 역할 만들기 대화 상자의 스크린샷. ](./media/appneta-tutorial/create-app-role.png)

1. 역할을 만든 후에는 사용자/그룹에 매핑해야 합니다. **Azure Active Directory** > **엔터프라이즈 애플리케이션** > **Appneta Performance Manager** > **사용자 및 그룹** 으로 이동합니다.
1. 사용자/그룹을 선택한 다음, 원하는 앱 역할을 할당합니다(이전 단계에서 생성됨).
1. 앱 역할이 매핑되면 **Azure Active Directory** > **엔터프라이즈 애플리케이션** > **Appneta Performance Manager** > **Single Sign-On** 으로 이동합니다.
1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

   ![인증서 다운로드 링크](common/metadataxml.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 AppNeta Performance Manager에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **AppNeta Performance Manager** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 역할을 위에서 설명한 대로 설정한 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

   > [!NOTE]
   > 실제로는 개별 사용자가 아닌 그룹을 애플리케이션에 추가합니다.

## <a name="configure-appneta-performance-manager-sso"></a>AppNeta Performance Manager SSO 구성

**AppNeta Performance Manager** 쪽에서 Single Sign-On을 구성하려면 다운로드한 **페더레이션 메타데이터 XML** 을 [AppNeta Performance Manager 지원 팀](mailto:support@appneta.com)에 보내야 합니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

### <a name="create-appneta-performance-manager-test-user"></a>AppNeta Performance Manager 테스트 사용자 만들기

이 섹션에서는 AppNeta Performance Manager에서 B.Simon이라는 사용자를 만듭니다. AppNeta Performance Manager는 Just-In-Time 사용자 프로비저닝을 지원하며, 기본적으로 사용하도록 설정되어 있습니다. 이 섹션에 작업 항목이 없습니다. AppNeta Performance Manager에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

> [!Note]
> 사용자를 수동으로 만들어야 하는 경우 [AppNeta Performance Manager 지원 팀](mailto:support@appneta.com)에 문의하세요.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

- Azure Portal에서 **이 애플리케이션 테스트** 를 선택합니다. 그러면 로그인 흐름을 시작할 수 있는 AppNeta Performance Manager 로그온 URL로 리디렉션됩니다.

- AppNeta Performance Manager 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

- Microsoft 내 앱을 사용할 수 있습니다. 내 앱 포털에서 AppNeta Performance Manager 타일을 클릭하면 AppNeta Performance Manager 로그온 URL로 리디렉션됩니다. 내 앱 포털에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

AppNeta Performance Manager가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
