---
title: '자습서: Azure AD SSO와 Perception United States(비 UltiPro) 통합'
description: Azure Active Directory와 Perception United States(비 UltiPro) 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/05/2021
ms.author: jeedes
ms.openlocfilehash: 62e963cdf3c0cdb60648e032a0520fdd71a90874
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856299"
---
# <a name="tutorial-azure-ad-sso-integration-with-perception-united-states-non-ultipro"></a>자습서: Azure AD SSO와 Perception United States(비 UltiPro) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Perception United States(비 UltiPro)를 통합하는 방법에 대해 알아봅니다. Perception United States(비 UltiPro)를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Perception United States(비 UltiPro)에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정을 사용하여 Perception United States(비 UltiPro)에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Perception United States(비 UltiPro) SSO(Single Sign-On)가 설정된 구독.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Perception United States(비 UltiPro)에서 **IDP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-perception-united-states-non-ultipro-from-the-gallery"></a>갤러리에서 Perception United States(비 UltiPro) 추가

Perception United States(비 UltiPro)의 Azure AD 통합을 구성하려면 갤러리의 Perception United States(비 UltiPro)를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Perception United States(비 UltiPro)** 를 입력합니다.
1. 결과 패널에서 **Perception United States(비 UltiPro)** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-perception-united-states-non-ultipro"></a>Perception United States(비 UltiPro)에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Perception United States(비 UltiPro)에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Perception United States(비 UltiPro)의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Perception United States(비 UltiPro)에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Perception United States(비 UltiPro) SSO 구성](#configure-perception-united-states-non-ultipro-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Perception United States(비 UltiPro) 테스트 사용자 만들기](#create-perception-united-states-non-ultipro-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Perception United States(비 UltiPro)에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Perception United States(비 UltiPro)** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 페이지에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에서 `https://perception.kanjoya.com/sp` URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://perception.kanjoya.com/sso?idp=<entity_id>` 패턴을 사용하여 URL을 입력합니다.

    다. **Perception United States(비 UltiPro)** 애플리케이션은 <entity_id>와 같은 **Azure AD 식별자** 값을 요구합니다. 이 값을 **Perception United States(비 UltiPro) 설정** 섹션에서 가져와서 URI로 인코딩할 수 있습니다. URI 인코딩 값을 가져오려면 **http://www.url-encode-decode.com/** 링크로 이동합니다.

    d. URI 인코딩된 값을 얻은 후 아래에서 설명된 것과 같이 **회신 URL** 과 결합합니다.

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. **회신 URL** 텍스트 상자에 위의 값을 붙여넣습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **Perception United States(비 UltiPro) 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 B.Simon이 Azure Single Sign-On을 사용할 수 있도록 Perception United States(비 UltiPro)에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Perception United States(비 UltiPro)** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-perception-united-states-non-ultipro-sso"></a>Perception United States(비 UltiPro) SSO 구성

1. 다른 웹 브라우저 창에서 Perception United States(비 UltiPro) 회사 사이트에 관리자로 로그인합니다.

2. 주 도구 모음에서 **계정 설정** 을 클릭합니다.

    ![주 도구 모음에서 선택한 "계정 설정"을 보여주는 스크린샷.](./media/perceptionunitedstates-tutorial/user.png)

3. **계정 설정** 페이지에서 다음 단계를 수행합니다.

    ![Perception United States(비 UltiPro) 사용자](./media/perceptionunitedstates-tutorial/account.png)

    a. **회사 이름** 텍스트 상자에 **회사** 의 이름을 입력합니다.
    
    b. **계정 이름** 텍스트 상자에 **계정** 의 이름을 입력합니다.

    다. **기본 회신 전자 메일** 텍스트 상자에 유효한 **전자 메일** 을 입력합니다.

    d. **SAML 2.0** 으로 **SSO ID 공급자** 를 선택합니다.

4. **SSO 구성** 페이지에서 다음 단계를 수행합니다.

    ![Perception United States(비 UltiPro) SSO 구성.](./media/perceptionunitedstates-tutorial/configuration.png)

    a. **전자 메일** 로 **SAML NameID 유형** 을 선택합니다.

    b. **SSO 구성 이름** 텍스트 상자에 **구성** 의 이름을 입력합니다.
    
    다. **ID 공급자 이름** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다. 

    d. **SAML 도메인 텍스트 상자** 에 @contoso.com과 같은 도메인을 입력합니다.

    e. **다시 업로드** 를 클릭하여 **메타데이터 XML** 파일을 업로드합니다.

    f. **업데이트** 를 클릭합니다.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Perception United States(비 UltiPro) 테스트 사용자 만들기

이 섹션에서는 Perception United States(비 UltiPro)에서 Britta Simon이라는 사용자를 만듭니다. Perception United States(비 UltiPro) 플랫폼에 사용자를 추가하려면 [Perception United States(비 UltiPro) 지원 팀](https://www.ultimatesoftware.com/Contact/ContactUs)에 문의하세요.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 Perception United States(비 UltiPro)에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Perception United States(비 UltiPro) 타일을 클릭하면 SSO가 설정된 Perception United States(비 UltiPro)에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Perception United States(비 UltiPro)를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).