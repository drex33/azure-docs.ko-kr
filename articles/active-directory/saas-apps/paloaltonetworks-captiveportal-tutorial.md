---
title: '자습서: Palo Alto Networks Captive Portal과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Palo Alto Networks Captive Portal 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 7b23857f3a14bc659f2c3a16113d6b238cc3a3a2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344335"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>자습서: Palo Alto Networks Captive Portal과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Palo Alto Networks Captive Portal을 통합하는 방법에 대해 알아봅니다.
Palo Alto Networks Captive Portal을 Azure AD와 통합하면 다음과 같은 이점을 누릴 수 있습니다.

* Palo Alto Networks Captive Portal에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Palo Alto Networks Captive Portal에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Palo Alto Networks Captive Portal과 Azure AD를 통합하려면 다음 항목이 필요합니다.

* Azure Active Directory 구독 Azure AD가 없으면 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 얻을 수 있습니다.
* Palo Alto Networks Captive Portal SSO(Single Sign-on)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Palo Alto Networks Captive Portal에서 **IDP** 시작 SSO를 지원합니다.
* Palo Alto Networks Captive Portal에서 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

## <a name="adding-palo-alto-networks-captive-portal-from-the-gallery"></a>갤러리에서 Palo Alto Networks Captive Portal 추가

Palo Alto Networks Captive Portal의 Azure AD 통합을 구성하려면 갤러리의 Palo Alto Networks Captive Portal을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Palo Alto Networks Captive Portal** 을 입력합니다.
1. 결과 패널에서 **Palo Alto Networks Captive Portal** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 구성 및 테스트

이 섹션에서는 **B.Simon** 이라는 테스트 사용자를 기반으로 하여 Palo Alto Networks Captive Portal에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Palo Alto Networks Captive Portal의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Palo Alto Networks Captive Portal에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - 사용자 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B.Simon이 Azure AD Single Sign-On을 사용하도록 설정합니다.
2. **[Palo Alto Networks Captive Portal SSO 구성](#configure-palo-alto-networks-captive-portal-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Palo Alto Networks Captive Portal 테스트 사용자 만들기](#create-a-palo-alto-networks-captive-portal-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Palo Alto Networks Captive Portal에 만듭니다.
3. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Palo Alto Networks Captive Portal** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 창에서 다음 단계를 수행합니다.

   1. **식별자** 에 대해 `https://<customer_firewall_host_name>/SAML20/SP` 패턴이 있는 URL을 입력합니다.

   2. **회신 URL** 에 대해 `https://<customer_firewall_host_name>/SAML20/SP/ACS` 패턴이 있는 URL을 입력합니다.

      > [!NOTE]
      > 실제 식별자 및 회신 URL로 이 단계의 자리 표시자 값을 업데이트합니다. 실제 값을 얻으려면 [Palo Alto Networks Captive Portal 클라이언트 지원 팀](https://support.paloaltonetworks.com/support)에 문의하세요.

5. **SAML 서명 인증서** 섹션의 **페더레이션 메타데이터 XML** 옆에서 **다운로드** 를 선택합니다. 다운로드한 파일을 컴퓨터에 저장합니다.

    ![페더레이션 메타데이터 XML 다운로드 링크](common/metadataxml.png)

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

이 섹션에서는 B.Simon이 Azure Single Sign-On을 사용할 수 있도록 Palo Alto Networks Captive Portal에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Palo Alto Networks Captive Portal** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-palo-alto-networks-captive-portal-sso"></a>Palo Alto Networks Captive Portal SSO 구성

다음으로 Palo Alto Networks Captive Portal에서 Single Sign-On을 설정합니다.

1. 다른 브라우저 창에서 Palo Alto Networks 웹 사이트에 관리자로 로그인합니다.

2. **디바이스** 탭을 선택합니다.

    ![Palo Alto Networks 웹 사이트 디바이스 탭](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. 메뉴에서 **SAML ID 공급 기업** 을 선택한 다음, **가져오기** 를 선택합니다.

    ![가져오기 단추](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. **SAML ID 공급 기업 서버 프로필 가져오기** 대화 상자에서 다음 단계를 완료합니다.

    ![Palo Alto Networks Single Sign-on 구성](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. **프로필 이름** 에 대해 **AzureAD-CaptivePortal** 과 같은 이름을 입력합니다.
    
    2. **ID 공급 기업 메타데이터** 옆에서 **찾아보기** 를 선택합니다. Azure Portal에서 다운로드한 metadata.xml 파일을 선택합니다.
    
    3. **확인** 을 선택합니다.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Palo Alto Networks Captive Portal 테스트 사용자 만들기

다음으로 Palo Alto Networks Captive Portal에 *Britta Simon* 이라는 사용자를 만듭니다. Palo Alto Networks Captive Portal은 기본적으로 사용하도록 설정되는 Just-In-Time 프로비전을 지원합니다. 이 섹션의 작업을 완료하지 않아도 됩니다. Palo Alto Networks Captive Portal에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

> [!NOTE]
> 사용자를 수동으로 만들어야 하는 경우 [Palo Alto Networks Captive Portal 클라이언트 지원 팀](https://support.paloaltonetworks.com/support)에 문의하세요.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 Palo Alto Networks Captive Portal에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Palo Alto Networks Captive Portal 타일을 클릭하면 SSO를 설정한 Palo Alto Networks Captive Portal 애플리케이션에 자동으로 로그온됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.


## <a name="next-steps"></a>다음 단계

Palo Alto Networks Captive Portal이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
