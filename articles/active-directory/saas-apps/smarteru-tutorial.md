---
title: '자습서: SmarterU와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SmarterU 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/27/2021
ms.author: jeedes
ms.openlocfilehash: b69194889522d120bb39ae472b5f8f59a3a2d144
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316929"
---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>자습서: SmarterU와 Azure Active Directory 통합

> [!NOTE]
> SmarterU를 Azure Active Directory와 통합하는 프로세스도 [SmarterU 도움말 시스템](https://help.smarteru.com/ID2053086)에 문서화되어 유지 관리됩니다.

이 자습서에서는 Azure AD(Azure Active Directory)와 SmarterU를 통합하는 방법에 대해 알아봅니다. Azure AD와 SmarterU를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 SmarterU에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 Azure AD 계정으로 SmarterU에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SmarterU SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* SmarterU는 **IDP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-smarteru-from-the-gallery"></a>갤러리에서 SmarterU 추가

SmarterU의 Azure AD 통합을 구성하려면 갤러리의 SmarterU를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **SmarterU** 를 입력합니다.
1. 결과 패널에서 **SmarterU** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-smarteru"></a>SmarterU에서 Azure AD SSO 구성 및 테스트

**B. Simon** 이라는 테스트 사용자를 사용하여 SmarterU에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SmarterU의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SmarterU에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[SmarterU SSO 구성](#configure-smarteru-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[SmarterU 테스트 사용자 만들기](#create-smarteru-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 SmarterU에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **SmarterU** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    **식별자** 텍스트 상자에서 `https://www.smarteru.com/` URL을 입력합니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **SmarterU 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 SmarterU에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **SmarterU** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-smarteru-sso"></a>SmarterU SSO 구성

1. 다른 웹 브라우저 창에서 SmarterU 회사 사이트에 관리자로 로그인합니다.

1. 위쪽의 도구 모음에서 **계정 설정** 을 클릭합니다.

    ![계정 설정](./media/smarteru-tutorial/settings.png)

1. 계정 구성 페이지에서 다음 단계를 수행합니다.

    ![외부 기관](./media/smarteru-tutorial/configuration.png) 

    a. **외부 권한 부여를 사용하도록 설정** 을 선택합니다.
  
    b. **마스터 로그인 제어** 섹션에서 **SmarterU** 탭을 선택합니다.
  
    다. **사용자 기본 로그인** 섹션에서 **SmarterU** 탭을 선택합니다.
  
    d. **SAML 사용** 을 선택합니다.
  
    e. 다운로드한 메타데이터 파일의 내용을 복사한 다음, **IdP 메타데이터** 텍스트 상자에 붙여넣습니다.

    f. **식별자 특성/클레임** 을 선택합니다.
  
    g. **저장** 을 클릭합니다.

### <a name="create-smarteru-test-user"></a>SmarterU 테스트 사용자 만들기

Azure AD 사용자가 SmarterU에 로그인할 수 있도록 하려면 SmarterU로 프로비저닝되어야 합니다. SmarterU의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. **SmarterU** 테넌트에 로그인합니다.

1. **사용자** 로 이동하십시오.

1. 사용자 섹션에서 다음 단계를 수행합니다.

    ![새 사용자](./media/smarteru-tutorial/add-user.png)  

    a. **+사용자** 를 클릭합니다.

    b. Azure AD 사용자 계정의 관련 특성 값을 다음 텍스트 상자에 입력합니다. **기본 이메일**, **직원 ID**, **암호**, **암호 확인**, **이름**, **성**.

    다. **활성** 을 클릭합니다.

    d. **저장** 을 클릭합니다.

> [!NOTE]
> 다른 SmarterU 사용자 계정 생성 도구 또는 SmarterU가 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비저닝할 수 있습니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 SmarterU에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 SmarterU 타일을 클릭하면 SSO를 설정한 SmarterU에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

SmarterU가 구성되면 세션 제어를 적용하여 조직에서 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
