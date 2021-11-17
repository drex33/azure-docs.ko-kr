---
title: '자습서: Profit.co와 Azure AD SSO 통합'
description: Azure Active Directory와 Profit.co 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.author: jeedes
ms.openlocfilehash: ae8e76d33dbab90478a361c16f8f3abbb3346e36
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341771"
---
# <a name="tutorial-azure-ad-sso-integration-with-profitco"></a>자습서: Profit.co와 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Profit.co를 통합하는 방법에 대해 알아봅니다. Azure AD와 Profit.co를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Profit.co에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Profit.co에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Profit.co SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Profit.co는 IDP 시작 SSO를 지원합니다.

## <a name="add-profitco-from-the-gallery"></a>갤러리에서 Profit.co 추가

Profit.co가 Azure AD에 통합되도록 구성하려면 갤러리의 Profit.co를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Profit.co** 를 입력합니다.
1. 결과 패널에서 **Profit.co** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-profitco"></a>Profit.co에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Profit.co에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Profit.co의 관련 사용자 간에 연결 관계를 설정합니다.

Profit.co를 사용하여 Azure AD SSO를 구성하고 테스트하는 일반적인 단계는 다음과 같습니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
    1. **[Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)** 하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Profit.co SSO 구성](#configure-profitco-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Profit.co 테스트 사용자 만들기](#create-a-profitco-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Profit.co에 만듭니다.
1. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Profit.co** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾습니다. **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 선택하여 설정을 편집합니다.

   ![연필 아이콘이 강조 표시된 SAML로 Single Sign-On 설정 페이지의 스크린샷](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 애플리케이션이 미리 구성되어 있으며 이미 Azure에 필요한 URL이 채워져 있습니다. 사용자는 **저장** 단추를 선택하여 구성을 저장해야 합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **복사** 단추를 선택합니다. 그러면 **앱 페더레이션 메타데이터 URL** 이 복사되어 컴퓨터에 저장됩니다.

    ![복사 단추가 강조 표시된 SAML 서명 인증서의 스크린샷](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 필드에 표시된 값을 적어둡니다.
   1. **만들기** 를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Profit.co에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 차례로 선택합니다.
1. 애플리케이션 목록에서 **Profit.co** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고, **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택합니다. **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택합니다. 그런 다음, 화면 아래쪽에서 **선택** 단추를 선택합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택합니다. 그런 다음, 화면 아래쪽에서 **선택** 단추를 선택합니다.
1. **할당 추가** 대화 상자에서 **할당** 을 선택합니다.

## <a name="configure-profitco-sso"></a>Profit.co SSO 구성

Profit.co 쪽에서 Single Sign-On을 구성하려면 앱 페더레이션 메타데이터 URL을 [Profit.co 지원 팀](mailto:support@profit.co)으로 보내야 합니다. 해당 팀에서 SAML SSO 연결이 양쪽에 제대로 설정되도록 구성합니다.

### <a name="create-a-profitco-test-user"></a>Profit.co 테스트 사용자 만들기

이 섹션에서는 Profit.co에서 B.Simon이라는 사용자를 만듭니다. [Profit.co 지원 팀](mailto:support@profit.co)과 협력하여 사용자를 Profit.co 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭합니다. 그러면 SSO를 설정한 Profit.co에 자동으로 로그인되어야 합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Profit.co 타일을 클릭하면 SSO를 설정한 Profit.co에 자동으로 로그인되어야 합니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Profit.co를 구성하면 세션 제어를 적용하여 조직의 중요한 데이터 반출과 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
