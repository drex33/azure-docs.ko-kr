---
title: '자습서: Costpoint와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Costpoint 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: dc4297e677dd94db6e3464d20a65784dff441b38
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124779182"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>자습서: Costpoint와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Costpoint를 통합하는 방법에 대해 알아봅니다. Azure AD와 Costpoint를 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서 Costpoint에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Costpoint에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Costpoint SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. 

* Costpoint는 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="generate-costpoint-metadata"></a>Costpoint 메타데이터 생성

Costpoint SAML SSO 구성은 **DeltekCostpoint711Security.pdf** 가이드에 설명되어 있습니다. Deltek Costpoint 지원 사이트에서 이 가이드를 다운로드 하여 **SAML Single Sign-on Setup**(SAML Single Sign-On 설정) > **Configure SAML Single Sign-on between Costpoint and Microsoft Azure**(Costpoint와 Microsoft Azure 간에 SAML Single Sign-On 구성) 섹션을 참조하세요. 지침에 따라 **Costpoint SP 페더레이션 메타데이터 XML** 파일을 생성합니다. 

!["Weblogic - 보안" 탭이 선택된 "제품 구성 유틸리티"를 보여주는 스크린샷.](./media/costpoint-tutorial/configuration-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>갤러리에서 Costpoint 추가

Costpoint의 Azure AD 통합을 구성하려면 갤러리의 Costpoint를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Costpoint** 를 입력합니다.
1. 결과 패널에서 **Costpoint** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-costpoint"></a>Costpoint에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Costpoint에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Costpoint의 관련 사용자 간에 연결이 형성되어야 합니다.

Costpoint에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Costpoint SSO 구성](#configure-costpoint-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Costpoint 테스트 사용자 만들기](#create-costpoint-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Costpoint에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. **Costpoint** 애플리케이션 통합 페이지에서 **Single Sign-On** 을 클릭합니다.

1. **기본 SAML 구성** 섹션에서 서비스 공급자 메타데이터 파일이 있는 경우, 다음 단계를 완료합니다. 

   > [!NOTE]
   > 서비스 공급자 메타데이터 파일은 [Costpoint 메타데이터 생성](#generate-costpoint-metadata)에서 가져올 수 있습니다. 이 파일을 사용하는 방법은 자습서의 뒷부분에 설명되어 있습니다.
 
   1. **메타데이터 파일 업로드** 단추를 선택한 다음, Costpoint를 통해 이전에 생성된 **Costpoint SP 페더레이션 메타데이터 XML** 파일을 선택한 후 **추가** 단추를 선택하여 파일을 업로드합니다.

      ![메타데이터 파일 업로드](./media/costpoint-tutorial/upload-metadata.png)
    
   1. 메타데이터 파일이 성공적으로 업로드되면 Costpoint 섹션에서 **식별자** 및 **회신 URL** 값이 자동으로 채워집니다.

      > [!NOTE]
      > **식별자** 및 **회신 URL** 값이 자동으로 채워지지 않으면 요구 사항에 따라 수동으로 값을 입력합니다. **식별자(엔터티 ID)** 및 **회신 URL(Assertion Consumer Service URL)** 이 올바르게 설정되어 있고 **ACS URL** 이 **/LoginServlet.cps** 로 끝나는 유효한 Costpoint URL인지 확인합니다.

   1. **추가 URL 설정** 을 선택합니다. **릴레이 상태** 의 경우, `system=[your system]` 패턴을 사용하여 값(예: **system=DELTEKCP**)을 입력합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **복사** 아이콘을 선택하여 **앱 페더레이션 메타데이터 URL** 을 복사하고 메모장에 저장합니다.

   ![SAML 서명 인증서](common/copy-metadataurl.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Costpoint에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Costpoint** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-costpoint-sso"></a>Costpoint SSO 구성

1. Costpoint 구성 유틸리티로 돌아갑니다. **IdP 페더레이션 메타데이터 XML** 텍스트 상자에 *앱 페더레이션 메타데이터 URL* 파일의 내용을 붙여넣습니다. 

   ![Costpoint 구성 유틸리티](./media/costpoint-tutorial/configuration-utility-metadata.png)

1. **DeltekCostpoint711Security.pdf** 가이드의 지침에 따라 Costpoint SAML 설정을 완료합니다.

### <a name="create-costpoint-test-user"></a>Costpoint 테스트 사용자 만들기

이 섹션에서는 Costpoint에서 사용자를 만듭니다. 사용자 ID가 **B.SIMON** 이고 사용자 이름이 **B.Simon** 이라고 가정합니다. [Costpoint 클라이언트 지원 팀](https://www.deltek.com/about/contact-us)과 협력하여 사용자를 Costpoint 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

사용자가 생성된 후 사용자의 **인증 방법** 은 **Active Directory** 여야 하고, **SAML Single Sign-On** 확인란이 선택되어 있어야 하며, Azure Active Directory의 사용자 이름은 **Active Directory 또는 인증서 ID** 여야 합니다(아래 스크린샷 참조).

![Costpoint 사용자](./media/costpoint-tutorial/costpoint-user.png)

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Costpoint 로그온 URL로 리디렉션됩니다.  

* Costpoint 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Costpoint에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Costpoint 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Costpoint에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Costpoint가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).