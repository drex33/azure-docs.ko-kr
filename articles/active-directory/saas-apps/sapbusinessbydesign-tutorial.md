---
title: '자습서: SAP Business ByDesign과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SAP Business ByDesign 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 69fceff94faebb3acce3a4c512d811841d455ce5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341448"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>자습서: SAP Business ByDesign과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP Business ByDesign을 통합하는 방법에 대해 알아봅니다. SAP Business ByDesign을 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* SAP Business ByDesign에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 SAP Business ByDesign에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SAP Business ByDesign SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* SAP Business ByDesign에서 **SP** 시작 SSO를 지원합니다.

## <a name="add-sap-business-bydesign-from-the-gallery"></a>갤러리에서 SAP Business ByDesign 추가

SAP Business ByDesign의 Azure AD 통합을 구성하려면 갤러리의 SAP Business ByDesign을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **SAP Business ByDesign** 을 입력합니다.
1. 결과 패널에서 **SAP Business ByDesign** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 SAP Business ByDesign에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SAP Business ByDesign의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SAP Business ByDesign에서 Azure AD SSO를 구성하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[SAP Business ByDesign SSO 구성](#configure-sap-business-bydesign-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[SAP Business ByDesign 테스트 사용자 만들기](#create-sap-business-bydesign-test-user)** - Azure AD를 대표하여 SAP Business ByDesign에서 Britta Simon에 해당하는 사용자가 있어야 합니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **SAP Business ByDesign** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<servername>.sapbydesign.com` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<servername>.sapbydesign.com` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [SAP Business ByDesign 클라이언트 지원 팀](https://www.sap.com/products/cloud-analytics.support.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. SAP Business ByDesign 애플리케이션은 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![image1](common/edit-attribute.png)

6. **편집** 아이콘을 클릭하여 **이름 식별자 값** 을 편집합니다.

    ![image2](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. **사용자 클레임 관리** 섹션에서 다음 단계를 수행합니다.

    ![image3](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. **변환** 을 **원본** 으로 선택합니다.

    b. **변환** 드롭다운 목록에서 **ExtractMailPrefix()** 를 선택합니다.

    > [!NOTE]
    > 기본적으로 ByD는 사용자 매핑에 대해 **지정되지 않은** NameID 형식을 사용합니다. ByD는 ByD 사용자 별칭에 대한 SAML 어설션의 NameID를 매핑합니다. 또한 ByD는 이름 ID 형식 **emailAddress** 를 지원합니다. 이 경우 ByD는 ByD 직원 연락처 데이터의 ByD 사용자 이메일 주소에 대해 SAM 어설션의 NameID를 매핑합니다. 자세한 내용은 [이 SAP 블로그](https://blogs.sap.com/2017/05/24/single-sign-on-sso-with-sap-business-bydesign/)에서 참조할 수 있습니다.

8. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

9. **설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 SAP Business ByDesign에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **SAP Business ByDesign** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-sap-business-bydesign-sso"></a>SAP Business ByDesign SSO 구성

1. 관리자 권한으로 SAP Business ByDesign 포털에 로그인합니다.

2. **애플리케이션 및 사용자 관리 일반 작업** 으로 이동하여 **ID 공급자** 탭을 클릭합니다.

3. **새 ID 공급자** 를 클릭하고 Azure Portal에서 다운로드한 메타데이터 XML 파일을 선택합니다. 시스템은 메타데이터를 가져와서 필수 서명 인증서 및 암호화 인증서를 자동으로 업로드합니다.

    ![Single Sign-On1 구성](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. **어설션 소비자 서비스 URL** 을 SAML 요청에 포함하려면 **어설션 소비자 서비스 URL 포함** 을 선택합니다.

5. **Single Sign-on 활성화** 를 클릭합니다.

6. 변경 내용을 저장합니다.

7. **내 시스템** 탭을 클릭합니다.

    ![Single Sign-On2 구성](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. **Azure AD 로그온 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    ![Single Sign-On3 구성](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. 직원이 **수동 ID 공급자 선택** 을 선택하여 사용자 ID 및 암호 또는 SSO를 사용하는 로그온 중 하나를 수동으로 선택할 수 있는지 여부를 지정합니다.

10. **SSO URL** 섹션에서 시스템에 로그온하려는 직원이 사용해야 하는 URL을 지정합니다.
    직원에게 전송된 URL 드롭다운 목록에서 다음 옵션 중 하나를 선택할 수 있습니다.

    **SSO가 아닌 URL**

    시스템은 직원에게 정상적인 시스템 URL만을 보냅니다. 직원은 SSO를 사용하여 로그온할 수 없고 대신 암호 또는 인증서를 사용해야 합니다.

    **SSO URL**

    시스템은 직원에게 SSO URL만을 보냅니다. 직원은 SSO를 사용하여 로그온할 수 있습니다. IdP를 통해 인증 요청이 리디렉션됩니다.

    **자동 선택**

    SSO가 활성 상태가 아닌 경우 시스템은 직원에게 정상적인 시스템 URL을 보냅니다. SSO가 활성 상태인 경우 시스템은 직원이 암호를 가지는지 여부를 확인합니다. 암호를 사용할 수 있는 경우 SSO URL와 SSO가 아닌 URL은 직원에게 전송됩니다. 그러나 직원에게 암호가 없는 경우 SSO URL만이 직원에게 전송됩니다.

11. 변경 내용을 저장합니다.

### <a name="create-sap-business-bydesign-test-user"></a>SAP Business ByDesign 테스트 사용자 만들기

이 섹션에서는 SAP Business ByDesign에서 Britta Simon이라는 사용자를 만듭니다. [SAP Business ByDesign 클라이언트 지원 팀](https://www.sap.com/products/cloud-analytics.support.html)과 협력하여 SAP Business ByDesign 플랫폼에 사용자를 추가합니다. 

> [!NOTE]
> NameID 값이 SAP Business ByDesign 플랫폼에서 사용자 이름 필드와 일치하는지 확인하세요.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

1. Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 SAP Business ByDesign 로그온 URL로 리디렉션됩니다. 

2. SAP Business ByDesign 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

3. Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 SAP Business ByDesign 타일을 클릭하면 SAP Business ByDesign 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* SAP Business ByDesign을 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
