---
title: '자습서: RStudio Connect SAML Authentication과 Azure AD SSO 통합'
description: Azure Active Directory와 RStudio Connect SAML Authentication 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/14/2021
ms.author: jeedes
ms.openlocfilehash: 76550ad5f3a37058d37015e8e2d71ad727b2d5b8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665818"
---
# <a name="tutorial-azure-ad-sso-integration-with-rstudio-connect-saml-authentication"></a>자습서: RStudio Connect SAML Authentication과 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 RStudio Connect SAML Authentication을 통합하는 방법을 알아봅니다. Azure AD와 RStudio Connect SAML Authentication을 통합하면 다음을 수행할 수 있습니다.

* RStudio Connect SAML Authentication에 대한 액세스 권한이 있는 Azure AD를 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 RStudio Connect SAML Authentication에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

RStudio Connect SAML Authentication과 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* RStudio Connect SAML Authentication. [45일 무료 평가판](https://www.rstudio.com/products/connect/)이 제공됩니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* RStudio Connect SAML Authentication에서 **SP 및 IDP** 시작 SSO를 지원합니다.

* RStudio Connect SAML Authentication에서 **Just In Time** 사용자 프로비저닝을 지원합니다.

## <a name="add-rstudio-connect-saml-authentication-from-the-gallery"></a>갤러리에서 RStudio Connect SAML Authentication 추가

RStudio Connect SAML Authentication이 Azure AD에 통합되도록 구성하려면 갤러리의 RStudio Connect SAML Authentication을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **RStudio Connect SAML Authentication** 을 입력합니다.
1. 결과 패널에서 **RStudio Connect SAML Authentication** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-rstudio-connect-saml-authentication"></a>RStudio Connect SAML Authentication에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 RStudio Connect SAML Authentication에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 RStudio Connect SAML Authentication의 관련 사용자 간에 연결 관계를 설정해야 합니다.

RStudio Connect SAML Authentication에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
2. **[RStudio Connect SAML Authentication SSO 구성](#configure-rstudio-connect-saml-authentication-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[RStudio Connect SAML Authentication 테스트 사용자 만들기](#create-rstudio-connect-saml-authentication-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 RStudio Connect SAML Authentication에 만듭니다.
3. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **RStudio Connect SAML Authentication** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행하고, 다음과 같이 `<example.com>`을 RStudio Connect SAML Authentication 서버 주소 및 포트로 바꿉니다.

    a. **식별자** 텍스트 상자에서 `https://<example.com>/__login__/saml` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<example.com>/__login__/saml/acs` 패턴을 사용하여 URL을 입력합니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<example.com>/` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값은 RStudio Connect SAML Authentication 서버 주소(위 예제의 `https://example.com`)에서 확인됩니다. 문제가 있는 경우 [RStudio Connect SAML Authentication 지원 팀](mailto:support@rstudio.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

6. RStudio Connect SAML Authentication 애플리케이션에는 특정 형식의 SAML 어설션이 필요하기 때문에, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 **nameidentifier** 가 **user.userprincipalname** 과 매핑되는 기본 특성 목록을 보여줍니다. RStudio Connect SAML Authentication 애플리케이션에서는 **nameidentifier** 가 **user.mail** 과 매핑되므로 특성 매핑을 변경하기 위해 **편집** 아이콘을 클릭하여 특성 매핑을 편집해야 합니다.

    ![이미지](common/edit-attribute.png)

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL** 을 복사한 후 컴퓨터에 저장합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 RStudio Connect SAML Authentication에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **RStudio Connect SAML Authentication** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-rstudio-connect-saml-authentication-sso"></a>RStudio Connect SAML Authentication SSO 구성

**RStudio Connect SAML Authentication** 에 대한 Single Sign-On을 구성하려면 위에서 사용한 **앱 페더레이션 메타데이터 Url** 및 **서버 주소** 를 사용해야 합니다. 이 작업은 `/etc/rstudio-connect.rstudio-connect.gcfg`에 있는 RStudio Connect SAML Authentication 구성 파일에서 수행됩니다.

다음은 구성 파일 예제입니다.

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect SAML Authentication server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect SAML Authentication server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

**서버 주소** 를 `Server.Address` 값에 저장하고, **앱 페더레이션 메타데이터 Url** 을 `SAML.IdPMetaData` 값에 저장합니다. 이 샘플 구성은 암호화되지 않은 HTTP 연결을 사용하는 반면, Azure AD는 암호화된 HTTPS 연결을 사용해야 합니다. RStudio Connect SAML Authentication 앞에 있는 [역방향 프록시](https://docs.rstudio.com/connect/admin/proxy/)를 사용하거나 [HTTPS를 직접 사용](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS)하도록 RStudio Connect SAML Authentication을 구성할 수 있습니다. 

구성에 문제가 있는 경우 [RStudio Connect SAML Authentication 관리자 가이드](https://docs.rstudio.com/connect/admin/authentication/saml/)를 읽거나 [RStudio 지원 팀](mailto:support@rstudio.com)에 이메일을 보내 도움을 받을 수 있습니다.

### <a name="create-rstudio-connect-saml-authentication-test-user"></a>RStudio Connect SAML Authentication 테스트 사용자 만들기

이 섹션에서는 RStudio Connect SAML Authentication에서 Britta Simon이라는 사용자를 만듭니다. RStudio Connect SAML Authentication은 기본적으로 사용하도록 설정되는 Just-In-Time 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. RStudio Connect SAML Authentication에 사용자가 아직 없는 경우 RStudio Connect SAML Authentication에 액세스하려고 할 때 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 RStudio Connect SAML Authentication 로그온 URL로 리디렉션됩니다.  

* RStudio Connect SAML Authentication 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 SSO를 설정한 RStudio Connect SAML Authentication에 자동으로 로그인되어야 합니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 RStudio Connect SAML Authentication 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작할 수 있도록 애플리케이션 로그온 페이지로 리디렉션되며 IDP 모드로 구성된 경우에는 SSO를 설정한 RStudio Connect SAML Authentication에 자동으로 로그인되어야 합니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

RStudio Connect SAML Authentication이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).