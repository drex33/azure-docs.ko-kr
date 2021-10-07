---
title: '자습서: Reward Gateway와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Reward Gateway 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: bd6b11956ffe32a2ae59cd6d6ab262ab3013832f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124753092"
---
# <a name="tutorial-azure-active-directory-integration-with-reward-gateway"></a>자습서: Reward Gateway와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Reward Gateway를 통합하는 방법에 대해 알아봅니다. Azure AD와 Reward Gateway를 통합하는 경우 다음을 수행할 수 있습니다.

* Reward Gateway에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Reward Gateway에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

Reward Gateway와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Reward Gateway Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Reward Gateway는 **IDP** 시작 SSO를 지원합니다.

* Reward Gateway는 [자동화된 사용자 프로비저닝](reward-gateway-provisioning-tutorial.md)을 지원합니다.

## <a name="add-reward-gateway-from-the-gallery"></a>갤러리에서 Reward Gateway 추가

Reward Gateway의 Azure AD 통합을 구성하려면 갤러리의 Reward Gateway를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Reward Gateway** 를 입력합니다.
1. 결과 패널에서 **Reward Gateway** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-reward-gateway"></a>Reward Gateway에 대한 Azure AD SSO 구성 및 테스트

**B. Simon** 이라는 테스트 사용자를 사용하여 Reward Gateway에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Reward Gateway의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Reward Gateway에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Reward Gateway SSO 구성](#configure-reward-gateway-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Reward Gateway 테스트 사용자 만들기](#create-reward-gateway-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Reward Gateway에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Reward Gateway** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **SAML로 Single Sign-On 설정** 페이지에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에 다음 패턴 중 하나를 사용하여 URL을 입력합니다.

    | 식별자 URL |
    |---|
    | `https://<COMPANY_NAME>.rewardgateway.com` |
    | `https://<COMPANY_NAME>.rewardgateway.co.uk/` |
    | `https://<COMPANY_NAME>.rewardgateway.co.nz/` |
    | `https://<COMPANY_NAME>.rewardgateway.com.au/`|
    |

    b. **회신 URL** 텍스트 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다.

    | 회신 URL |
    |---|
    | `https://<COMPANY_NAME>.rewardgateway.com/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<COMPANY_NAME>.rewardgateway.co.uk/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<COMPANY_NAME>.rewardgateway.co.nz/Authentication/EndLogin?idp=<Unique Id>` |
    | `https://<COMPANY_NAME>.rewardgateway.com.au/Authentication/EndLogin?idp=<Unique Id>` |
    |

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 가져오려면 보상 관리자 포털에서 통합 설정을 시작합니다. 자세한 내용은 https://success.rewardgateway.com/hc/en-us/articles/360038650573-Microsoft-Azure-for-Authentication 에서 찾을 수 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **Reward Gateway 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Reward Gateway에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Reward Gateway** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-reward-gateway-sso"></a>Reward Gateway SSO 구성

**보상 게이트웨이** 쪽에서 Single Sign-On을 구성하려면 보상 관리자 포털에서 통합 설정을 시작합니다. 다운로드한 메타데이터를 사용하여 서명 인증서를 가져오고 구성하는 동안 업로드합니다. 자세한 내용은 https://success.rewardgateway.com/hc/en-us/articles/360038650573-Microsoft-Azure-for-Authentication 에서 찾을 수 있습니다.

### <a name="create-reward-gateway-test-user"></a>Reward Gateway 테스트 사용자 만들기

이 섹션에서는 Reward Gateway에서 Britta Simon이라는 사용자를 만듭니다. Reward Gateway 플랫폼에서 사용자를 추가하려면 [Reward Gateway 지원 팀](mailto:clientsupport@rewardgateway.com)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

Reward Gateway는 자동 사용자 프로비저닝도 지원합니다. 자동 사용자 프로비저닝 구성 방법에 대한 자세한 내용은 [여기](./reward-gateway-provisioning-tutorial.md)에서 제공합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 Reward Gateway에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Reward Gateway 타일을 클릭하면 SSO를 설정한 Reward Gateway에 자동으로 로그인되어야 합니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Reward Gateway를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).