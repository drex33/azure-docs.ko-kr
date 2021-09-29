---
title: '자습서: Splan Visitor와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Splan Visitor 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/21/2021
ms.author: jeedes
ms.openlocfilehash: a2c33808be505209ff5eca58480b9fd5f03d9d4f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124785537"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>자습서: Splan Visitor와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Splan Visitor를 통합하는 방법을 알아봅니다. Azure AD와 Splan Visitor를 통합하면 다음을 수행할 수 있습니다.

* Azure AD를 사용하여 Splan Visitor에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Splan Visitor에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Splan Visitor SSO(Single Sign-On)를 사용하도록 설정된 구독

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Splan Visitor에서 IdP 시작 SSO를 지원합니다.

## <a name="add-splan-visitor-from-the-gallery"></a>갤러리에서 Splan Visitor 추가

Splan Visitor가 Azure AD에 통합되도록 구성하려면 갤러리의 Splan Visitor를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. 회사 계정, 학교 계정 또는 개인 Microsoft 계정을 사용하여 Azure Portal에 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Splan Visitor** 를 입력합니다.
1. 결과 패널에서 **Splan Visitor** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>Splan Visitor에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Splan Visitor에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Splan Visitor의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Splan Visitor에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon 테스트 사용자를 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Splan Visitor SSO 구성](#configure-splan-visitor-sso)** - Splan Visitor를 사용하여 Single Sign-On 설정을 구성합니다.
    1. **[Splan Visitor 테스트 사용자 만들기](#create-a-splan-visitor-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Splan Visitor에 만듭니다.
1. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Splan Visitor** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 **연필** 아이콘을 선택하여 설정을 편집합니다.

   ![기본 SAML 구성에 대한 편집 아이콘을 강조 표시하는 스크린샷](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 애플리케이션이 미리 구성되어 있고, 필요한 URL이 Azure로 미리 채워져 있습니다. **저장** 단추를 선택하여 구성을 저장합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾습니다. **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![페더레이션 메타데이터 XML 다운로드 링크를 강조 표시하는 스크린샷](common/metadataxml.png)

1. **Splan Visitor 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 섹션을 강조 표시하는 스크린샷](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B. Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에서 **B.Simon** 을 입력합니다.  
   1. **사용자 이름**  필드에서 사용자 이름을 _username@companydomain.extension_ 형식으로 입력합니다. 예를 들어 **B.Simon@contoso.com** 을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Splan Visitor에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Splan Visitor** 를 선택하여 앱 개요를 엽니다.
1. **관리** 섹션을 찾은 다음, **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 을 클릭합니다.
1. 사용자에게 역할을 할당하는 경우 **역할 선택** 드롭다운 메뉴에서 해당 역할을 선택합니다. 이 앱에 대해 설정된 역할이 없는 경우 **기본 액세스** 역할이 선택된 상태로 둡니다.
1. **할당 추가** 대화 상자에서 **할당** 을 선택합니다.

## <a name="configure-splan-visitor-sso"></a>Splan Visitor SSO 구성

Splan Visitor에서 Single Sign-On을 구성하려면 Azure Portal에서 다운로드한 **페더레이션 메타데이터 XML** 과 적절히 복사한 URL을 [Splan Visitor 지원 팀](mailto:support@splan.com)에 보냅니다. 이렇게 하면 SAML SSO 연결이 양쪽 모두에서 올바르게 설정됩니다.

### <a name="create-a-splan-visitor-test-user"></a>Splan Visitor 테스트 사용자 만들기

Splan Visitor에서 **Britta Simon** 이라는 테스트 사용자를 만듭니다. [Splan Visitor 지원 팀](mailto:support@splan.com)과 협력하여 사용자를 Splan Visitor에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트

다음 옵션 중 하나를 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* **Azure 포털**: **이 애플리케이션 테스트** 를 선택하여 SSO를 설정한 Splan Visitor에 자동으로 로그인합니다.
* **Microsoft 내 앱 포털**: **Splan Visitor** 타일을 선택하여 SSO를 설정한 Splan Visitor에 자동으로 로그인합니다. [내 앱] 포털에 대한 자세한 내용은 [[내 앱] 포털에서 앱에 로그인하여 시작](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Splan Visitor가 구성되면 [Microsoft Cloud App Security에서 세션 제어를 적용하는 방법을 알아볼 수 있습니다](/cloud-app-security/proxy-deployment-any-app). 세션 제어를 사용하면 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다.