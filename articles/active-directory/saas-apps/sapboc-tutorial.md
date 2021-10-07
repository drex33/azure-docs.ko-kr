---
title: '자습서: SAP Analytics Cloud와 Azure Active Directory 연결 | Microsoft Docs'
description: Azure Active Directory 및 SAP Analytics Cloud 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
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
ms.openlocfilehash: da2bd04dceb158d2fbd5b73530ff0e6083c52e9a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124813199"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>자습서: Azure Active Directory와 SAP Analytics Cloud 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP Analytics Cloud를 통합하는 방법에 대해 알아봅니다. SAP Analytics Cloud를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 SAP Analytics Cloud에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 SAP Analytics Cloud에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SAP Analytics Cloud SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* SAP Analytics Cloud에서 **SP** 시작 SSO를 지원합니다.

* SAP Analytics Cloud에서 [자동화된 사용자 프로비저닝](sap-analytics-cloud-provisioning-tutorial.md)을 지원합니다. 

## <a name="add-sap-analytics-cloud-from-the-gallery"></a>갤러리에서 SAP Analytics Cloud 추가

SAP Analytics Cloud가 Azure AD에 통합되도록 구성하려면 갤러리에서 SAP Analytics Cloud를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **SAP Analytics Cloud** 를 입력합니다.
1. 결과 패널에서 **SAP Analytics Cloud** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-sap-analytics-cloud"></a>SAP Analytics Cloud에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 SAP Analytics Cloud에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SAP Analytics Cloud의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SAP Analytics Cloud에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[SAP Analytics Cloud SSO 구성](#configure-sap-analytics-cloud-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[SAP Analytics Cloud 테스트 사용자 만들기](#create-sap-analytics-cloud-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 SAP Analytics Cloud에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **SAP Analytics Cloud** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **식별자(엔터티 ID)** 텍스트 상자에서 다음 패턴 중 하나를 사용하여 값을 입력합니다.

    | **식별자 URL** |
    |----|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    b. **로그온 URL** 텍스트 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다.
    
    | **URL에 로그인** |
    |------|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    > [!NOTE] 
    > 이러한 URL의 값은 데모용으로만 사용합니다. 값을 실제 식별자 및 로그온 URL로 업데이트합니다. 로그온 URL을 얻으려면 [SAP Analytics Cloud 클라이언트 지원 팀](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/)에 문의하세요. 식별자 URL은 관리 콘솔에서 SAP Analytics Cloud 메타데이터를 다운로드하여 가져올 수 있습니다. 이것은 자습서의 뒷부분에 설명되어 있습니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **SAP Analytics Cloud 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 SAP Analytics Cloud에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **SAP Analytics Cloud** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-sap-analytics-cloud-sso"></a>SAP Analytics Cloud SSO 구성

1. 다른 웹 브라우저 창에서 SAP Analytics Cloud 회사 사이트에 관리자 권한으로 로그인합니다.

2. **메뉴** > **시스템** > **관리** 를 선택합니다.
    
    ![메뉴, 시스템 및 관리 선택](./media/sapboc-tutorial/configure-1.png)

3. **보안** 탭에서 **편집**(펜) 아이콘을 선택합니다.
    
    ![보안 탭에서 편집 아이콘 선택](./media/sapboc-tutorial/configure-2.png)  

4. **인증 방법** 으로 **SAML SSO(Single Sign-On)** 를 선택합니다.

    ![인증 방법으로 SAML Single Sign-On 선택](./media/sapboc-tutorial/configure-3.png)  

5. 서비스 공급자 메타데이터를 다운로드하려면(1단계) **다운로드** 를 선택합니다. 메타데이터 파일에서 **entityID** 값을 찾고 복사합니다. Azure Portal의 **기본 SAML 구성** 대화 상자에서 값을 **식별자** 상자에 붙여넣습니다.

    ![entityID 값 복사 및 붙여넣기](./media/sapboc-tutorial/configure-4.png)  

6. Azure Portal에서 다운로드한 파일에서 서비스 공급자 메타데이터를 업로드하려면(2단계) **ID 공급자 메타데이터 업로드** 아래에서 **업로드** 를 선택합니다.  

    ![ID 공급자 메타데이터 업로드 아래에서 업로드 선택](./media/sapboc-tutorial/configure-5.png)

7. **사용자 특성** 목록에서 구현에 사용할 사용자 특성을 선택합니다(3단계). 이 사용자 특성은 ID 공급자에 매핑됩니다. 사용자의 페이지에서 사용자 지정 특성 입력하려면 **사용자 지정 SAML 매핑** 옵션을 사용합니다. 또는 **전자 메일** 이나 **사용자 ID** 를 사용자 특성으로 선택할 수 있습니다. 이 예에서는 Azure Portal의 **사용자 특성 및 클레임** 섹션에서 사용자 ID 클레임을 **userprincipalname** 특성으로 매핑하므로 **이메일** 을 선택했습니다. 여기서는 모든 성공적인 SAML 응답에서 SAP Analytics Cloud 애플리케이션에 보내는 고유한 사용자 이메일을 제공합니다.

    ![사용자 특성 선택](./media/sapboc-tutorial/configure-6.png)

8. ID 공급자(4단계)를 사용하여 계정을 확인하려면 **로그인 자격 증명(전자 메일)** 상자에 사용자의 전자 메일 주소를 입력합니다. 그런 다음 **계정 확인** 을 선택합니다. 시스템은 사용자 계정에 로그인 자격 증명을 추가합니다.

    ![전자 메일을 입력하고 계정 확인을 선택합니다.](./media/sapboc-tutorial/configure-7.png)

9. **저장** 아이콘을 선택합니다.

    ![저장 아이콘](./media/sapboc-tutorial/save.png)

### <a name="create-sap-analytics-cloud-test-user"></a>SAP Analytics Cloud 테스트 사용자 만들기

Azure AD 사용자는 먼저 SAP Analytics Cloud에서 프로비저닝한 후에 SAP Analytics Cloud에 로그인해야 합니다. SAP Analytics Cloud에서 프로비저닝은 수동 작업입니다.

사용자 계정을 프로비전하려면:

1. SAP Analytics Cloud 회사 사이트에 관리자 권한으로 로그인합니다.

2. **메뉴** > **보안** > **사용자** 를 선택합니다.

    ![직원 추가](./media/sapboc-tutorial/user-1.png)

3. **사용자** 페이지에서 새 사용자 세부 정보를 추가하려면 **+** 를 선택합니다. 

    ![사용자 추가 페이지](./media/sapboc-tutorial/user-4.png)

    그리고 나서 다음 단계를 완료합니다.

    1. **사용자 ID** 상자에서 사용자의 사용자 ID(예: **B**)를 입력합니다.

    1. **이름** 텍스트 상자에서 사용자의 이름(예: **B**)을 입력합니다.

    1. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

    1. **표시 이름** 텍스트 상자에서 사용자의 전체 이름(예: **B.Simon**)을 입력합니다.

    1. **전자 메일** 상자에 사용자의 이메일 주소(예: `b.simon@contoso.com`)를 입력합니다.

    1. **역할 선택** 페이지에서 사용자에 대한 적절한 역할을 선택하고 **확인** 을 선택합니다.

        ![직무 선택](./media/sapboc-tutorial/user-3.png)

    1. **저장** 아이콘을 선택합니다.

> [!NOTE]
> SAP Analytics Cloud는 자동화된 사용자 프로비저닝도 지원합니다. 자동화된 사용자 프로비저닝 구성 방법에 대한 자세한 내용은 [여기](./sap-analytics-cloud-provisioning-tutorial.md)에서 제공합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 SAP Analytics Cloud 로그온 URL로 리디렉션됩니다. 

* SAP Analytics Cloud 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 SAP Analytics Cloud 타일을 클릭하면 SAP Analytics Cloud 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

SAP Analytics Cloud가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).