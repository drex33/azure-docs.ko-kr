---
title: '자습서: Field iD와 Azure AD SSO 통합'
description: Azure Active Directory 및 Field iD 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2021
ms.author: jeedes
ms.openlocfilehash: ac235f95bfb88b8bab0733b1dbbc6c0f3e09af90
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808809"
---
# <a name="tutorial-azure-ad-sso-integration-with-field-id"></a>자습서: Field iD와 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Field iD를 연결하는 방법을 알아봅니다. Azure AD와 Field iD를 연결하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서 Field iD에 액세스할 수 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Field iD에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Field iD SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Field iD는 IDP 시작 SSO를 지원합니다.

## <a name="add-field-id-from-the-gallery"></a>갤러리에서 Field iD 추가

Azure AD에 Field iD 통합을 구성하려면 갤러리의 Field iD를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Field iD** 를 입력합니다.
1. 결과 패널에서 **Field iD** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-field-id"></a>Field iD용 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Field iD에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Field iD의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Field iD에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 완료합니다.

1. [Azure AD SSO 구성](#configure-azure-ad-sso) - 사용자가 이 기능을 사용할 수 있도록 구성합니다.
   1. [Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
   1. [Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. [Field iD SSO 구성](#configure-field-id-sso) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
   1. [Field iD 테스트 사용자 만들기](#create-a-field-id-test-user) - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Field iD에 만듭니다.
1. [SSO 테스트](#test-sso) - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Field iD** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾습니다. 그런 다음, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 선택하여 설정을 편집합니다.

   ![연필 아이콘이 강조 표시된 SAML로 Single Sign-On 설정 페이지의 스크린샷](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

   a. **식별자** 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://<tenantname>.fieldid.com/fieldid`

   b. **회신 URL** 텍스트 상자에서 `https://<tenantname>.fieldid.com/fieldid/saml/SSO/alias/<Tenant Name>` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [Field iD 지원 팀](mailto:support@ecompliance.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 아이콘을 선택하여 **앱 페더레이션 메타데이터 URL** 을 복사합니다. 인증서를 컴퓨터에 저장합니다.

    ![복사 아이콘이 강조 표시된 SAML 서명 인증서의 스크린샷](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 에 `B.Simon`를 입력합니다.  
   1. **사용자 이름** 에 username@companydomain.extension(예: `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Field iD에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 차례로 선택합니다.
1. 애플리케이션 목록에서 **Field iD** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고, **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 을 선택합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택합니다. 화면 하단에서 **선택** 을 누릅니다.
1. **할당 추가** 대화 상자에서 **할당** 을 선택합니다.

## <a name="configure-field-id-sso"></a>Field iD SSO 구성

Field iD 쪽에서 Single Sign-On을 구성하려면 **앱 페더레이션 메타데이터 URL** 을 [Field iD 지원 팀](mailto:support@ecompliance.com)으로 보냅니다. 두 항목은 SAML SSO 연결이 양쪽에서 올바르게 설정되도록 보장합니다.

### <a name="create-a-field-id-test-user"></a>Field iD 테스트 사용자 만들기

이 섹션에서는 Field iD에서 Britta Simon이라는 사용자를 만듭니다. [Field iD 지원 팀](mailto:support@ecompliance.com)과 협력하여 사용자를 Field iD 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 Field iD에 자동으로 로그인되어야 합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Field iD 타일을 클릭하면 SSO를 설정한 Field iD에 자동으로 로그인되어야 합니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Field iD가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).