---
title: '자습서: Keeper Password Manager & Digital Vault와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Keeper Password Manager & Digital Vault 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: 12471ef2816f5e4cb1e5f545f6d5670be59b4863
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183927"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>자습서: Keeper Password Manager & Digital Vault와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Keeper Password Manager & Digital Vault를 통합하는 방법에 대해 알아봅니다.
이렇게 통합하면 다음과 같은 이점을 누릴 수 있습니다.

* Keeper Password Manager & Digital Vault에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Keeper Password Manager & Digital Vault에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.


## <a name="prerequisites"></a>사전 요구 사항

Keeper Password Manager & Digital Vault와 Azure AD의 통합을 구성하려면 다음이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 얻을 수 있습니다.
* SSO(Single Sign-On)를 사용하도록 설정된 Keeper Password Manager & Digital Vault 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Keeper Password Manager & Digital Vault에서 SP 시작 SSO를 지원합니다.
* Keeper Password Manager는 [**자동** 사용자 프로비저닝 및 프로비저닝 해제](keeper-password-manager-digitalvault-provisioning-tutorial.md)(권장)를 지원합니다.
* Keeper Password Manager & Digital Vault에서 Just-In-Time 사용자 프로비저닝을 지원합니다.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>갤러리에서 Keeper Password Manager & Digital Vault 추가

Keeper Password Manager & Digital Vault가 Azure AD에 통합되도록 구성하려면 갤러리의 애플리케이션을 관리형 SaaS(Software as a Service) 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Keeper Password Manager & Digital Vault** 를 입력합니다.
1. 결과 패널에서 **Keeper Password Manager & Digital Vault** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Keeper Password Manager 및 Digital Vault에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Keeper Password Manager & Digital Vault에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Keeper Password Manager & Digital Vault의 관련 사용자 간에 연결된 관계를 설정해야 합니다.

Keeper Password Manager & Digital Vault에서 Azure AD SSO를 구성하고 테스트하려면 다음을 수행합니다.

1. [Azure AD SSO 구성](#configure-azure-ad-sso) - 사용자가 이 기능을 사용할 수 있도록 구성합니다.

    * [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user) - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
    * [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user) - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.

1. [Keeper Password Manager 및 Digital Vault SSO 구성](#configure-keeper-password-manager--digital-vault-sso) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * [Keeper Password Manager & Digital Vault 테스트 사용자 만들기](#create-a-keeper-password-manager--digital-vault-test-user) - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Keeper Password Manager & Digital Vault에 만듭니다.
1. [SSO 테스트](#test-sso) - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Keeper Password Manager & Digital Vault** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾습니다. **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 선택하여 설정을 편집합니다.

   ![연필 아이콘이 강조 표시된 SAML로 Single Sign-On 설정의 스크린샷](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 에 대해 다음 패턴을 사용하는 URL을 입력합니다.
    * 클라우드 SSO의 경우: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * 온-프레미스 SSO의 경우: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. **식별자(엔터티 ID)** 에 대해 다음 패턴을 사용하는 URL을 입력합니다.
    * 클라우드 SSO의 경우: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * 온-프레미스 SSO의 경우: `https://<KEEPER_FQDN>/sso-connect`

    다. **회신 URL** 에 대해 다음 패턴을 사용하는 URL을 입력합니다.
    * 클라우드 SSO의 경우: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * 온-프레미스 SSO의 경우: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL, 식별자 및 회신 URL로 업데이트하세요. 해당 값을 얻으려면 [Keeper Password Manager & Digital Vault 클라이언트 지원 팀](https://keepersecurity.com/contact.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. Keeper Password Manager & Digital Vault 애플리케이션에는 사용자 지정 특성 매핑을 SAML 토큰 특성 구성에 추가해야 하는 특정 형식의 SAML 어설션이 필요합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![사용자 특성 및 클레임의 스크린샷](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 Keeper Password Manager & Digital Vault 애플리케이션에는 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성은 다음 표에 나와 있습니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | Name | 원본 특성|
    | ------------| --------- |
    | 첫째 | user.givenname |
    | 마지막 | user.surname |
    | Email | user.mail |

5. **SAML로 Single Sign-On 설정** 의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 선택합니다. 이렇게 하면 요구 사항에 따라 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하여 컴퓨터에 저장합니다.

    ![다운로드가 강조 표시된 SAML 서명 인증서의 스크린샷](common/metadataxml.png)

6. **Keeper Password Manager & Digital Vault 설정** 에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![URL이 강조 표시된 Keeper Password Manager & Digital Vault 설정의 스크린샷](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션에서는 Azure Portal에서 `B.Simon`이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 에 `B.Simon`를 입력합니다.  
   1. **사용자 이름** 으로 `username@companydomain.extension`을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 를 선택한 다음, 표시되는 값을 적어둡니다.
   1. **만들기** 를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 B.Simon에게 Keeper Password Manager & Digital Vault에 대한 액세스 권한을 부여하여 해당 사용자가 Azure Single Sign-On을 사용하도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 차례로 선택합니다.
1. 애플리케이션 목록에서 **Keeper Password Manager &amp; Digital Vault** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택합니다. **할당 추가** 에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 의 사용자 목록에서 **B.Simon** 을 선택합니다. 화면 하단에서 **선택** 을 누릅니다.
1. 사용자에게 역할을 할당할 필요가 있으면 **역할 선택** 목록에서 선택할 수 있습니다. 이 앱에 대해 설정된 역할이 없으면 **기본 액세스** 역할이 선택됩니다.
1. **할당 추가** 에서 **할당** 을 선택합니다.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Keeper Password Manager 및 Digital Vault SSO 구성

앱에 대한 SSO를 구성하려면 [Keeper 지원 가이드](https://docs.keeper.io/sso-connect-guide/)의 지침을 참조하세요.

### <a name="create-a-keeper-password-manager--digital-vault-test-user"></a>Keeper Password Manager & Digital Vault 테스트 사용자 만들기

Azure AD 사용자가 Keeper Password Manager & Digital Vault에 로그인할 수 있도록 하려면 해당 사용자를 프로비저닝해야 합니다. 애플리케이션에서 Just-In-Time 사용자 프로비저닝을 지원하며, 인증 후에 사용자가 애플리케이션에 자동으로 만들어집니다. 사용자를 수동으로 설정하려면 [Keeper 지원](https://keepersecurity.com/contact.html)에 문의하세요.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 선택합니다. 그러면 로그온을 시작할 수 있는 Keeper Password Manager & Digital Vault 로그온 URL로 리디렉션됩니다. 

* 애플리케이션에 대한 로그온 URL로 직접 이동하여 여기서 로그인을 시작할 수 있습니다.

* Microsoft 액세스 패널을 사용할 수 있습니다. 액세스 패널에서 **Keeper Password Manager & Digital Vault** 타일을 선택하면 애플리케이션에 대한 로그온 URL로 리디렉션됩니다. 액세스 패널에 대한 자세한 내용은 [내 앱 포털에서 로그인하고 앱 시작](../user-help/my-apps-portal-end-user-access.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

Keeper Password Manager & Digital Vault가 구성되면 세션 제어를 적용할 수 있습니다. 이를 통해 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호합니다. 세션 제어는 조건부 액세스에서 확장됩니다. 자세한 내용은 [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](/cloud-app-security/proxy-deployment-aad)를 참조하세요.