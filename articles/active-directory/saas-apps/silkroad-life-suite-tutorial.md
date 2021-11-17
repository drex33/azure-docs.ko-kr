---
title: '자습서: SilkRoad Life Suite와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 SilkRoad Life Suite 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2021
ms.author: jeedes
ms.openlocfilehash: 8bef3229ca8ca814e55d9dba34c79646367bbd7c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132304693"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>자습서: SilkRoad Life Suite와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SilkRoad Life Suite를 통합하는 방법에 대해 알아봅니다. Azure AD와 SilkRoad Life Suite를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 SilkRoad Life Suite에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 SilkRoad Life Suite에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

SilkRoad Life Suite와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SilkRoad Life Suite Single-Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* SilkRoad Life Suite에서 **SP** 시작 SSO를 지원합니다.

## <a name="add-silkroad-life-suite-from-the-gallery"></a>갤러리에서 SilkRoad Life Suite 추가

SilkRoad Life Suite가 Azure AD에 통합되도록 구성하려면 갤러리의 SilkRoad Life Suite를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **SilkRoad Life Suite** 를 입력합니다.
1. 결과 패널에서 **SilkRoad Life Suite** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-silkroad-life-suite"></a>SilkRoad Life Suite에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 SilkRoad Life Suite에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SilkRoad Life Suite의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SilkRoad Life Suite에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[SilkRoad Life Suite SSO 구성](#configure-silkroad-life-suite-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[SilkRoad Life Suite 테스트 사용자 만들기](#create-silkroad-life-suite-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 SilkRoad Life Suite에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **SilkRoad Life Suite** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **서비스 공급자 메타데이터 파일** 이 있으면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    > [!NOTE]
    > 이 자습서의 뒷부분에서 설명할 **서비스 공급자 메타데이터 파일** 을 가져오겠습니다.

    a. **메타데이터 파일 업로드** 를 클릭합니다.

    ![스크린샷은 메타데이터 파일 업로드 링크가 있는 기본 SAML 구성을 보여줍니다.](common/upload-metadata.png)

    b. **폴더 로고** 를 클릭하여 메타데이터 파일을 선택하고 **업로드** 를 클릭합니다.

    ![스크린샷은 파일을 선택하고 업로드할 수 있는 대화 상자를 보여줍니다.](common/browse-upload-metadata.png)

    다. 메타데이터 파일이 성공적으로 업로드되면 **식별자** 및 **회신 URL** 값이 기본 SAML 구성 섹션에 자동으로 채워집니다.

    > [!Note]
    > **식별자** 및 **회신 URL** 값이 자동으로 채워지지 않으면 요구 사항에 따라 값을 수동으로 입력합니다.

    d. **로그인 URL** 텍스트 상자에서 `https://<SUBDOMAIN>.silkroad-eng.com/Authentication/` 패턴을 사용하여 URL을 입력합니다.

5. **서비스 공급자 메타데이터 파일** 이 없으면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다.

    | 식별자 URL |
    |-----|
    |`https://<SUBDOMAIN>.silkroad-eng.com/Authentication/SP`|
    |`https://<SUBDOMAIN>.silkroad.com/Authentication/SP`|
    

    b. **회신 URL** 텍스트 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다.

    | 회신 URL |
    |-----|
    |`https://<SUBDOMAIN>.silkroad-eng.com/Authentication/`|
    |`https://<SUBDOMAIN>.silkroad.com/Authentication/`|

    다. **로그인 URL** 텍스트 상자에서 `https://<SUBDOMAIN>.silkroad-eng.com/Authentication/` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 해당 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [SilkRoad Life Suite 클라이언트 지원 팀](https://www.silkroad.com/locations/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

7. **SilkRoad Life Suite 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 B.Simon에게 SilkRoad Life Suite에 대한 액세스 권한을 부여하여 해당 사용자가 Azure Single Sign-On을 사용하도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **SilkRoad Life Suite** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-silkroad-life-suite-sso"></a>SilkRoad Life Suite SSO 구성

1. SilkRoad 회사 사이트에 관리자로 로그인합니다.

    > [!NOTE]
    > Microsoft Azure AD와 페더레이션을 구성하기 위한 SilkRoad Life 인증 애플리케이션에 대한 액세스를 얻으려면 SilkRoad 지원 또는 SilkRoad 서비스 담당자에게 문의하세요.

1. **서비스 공급자** 로 이동한 다음 **페더레이션 세부 정보** 를 클릭합니다.

    ![스크린샷은 서비스 공급자에서 선택한 페더레이션 정보를 보여줍니다.](./media/silkroad-life-suite-tutorial/details.png)

1. **페더레이션 메타데이터 다운로드** 를 클릭한 후, 컴퓨터에 메타데이터 파일을 저장 합니다. 다운로드한 페더레이션 메타데이터를 Azure Portal의 **기본 SAML 구성** 섹션에서 **서비스 공급자 메타데이터 파일** 로 사용합니다.

    ![스크린샷은 페더레이션 메타데이터 다운로드 링크를 보여줍니다.](./media/silkroad-life-suite-tutorial/metadata.png)

1. **SilkRoad** 애플리케이션에서 **인증 원본** 을 클릭합니다.

    ![스크린샷은 선택된 인증 원본을 보여줍니다.](./media/silkroad-life-suite-tutorial/sources.png) 

1. **인증 원본 추가** 를 클릭합니다.

    ![스크린샷은 인증 원본 추가 링크를 보여줍니다.](./media/silkroad-life-suite-tutorial/add-source.png)

1. **인증 원본 추가** 섹션에서 다음 단계를 수행합니다.

    ![스크린샷은 파일 데이터를 통해 ID 공급자 만들기 단추가 선택된 인증 원본 추가를 보여줍니다.](./media/silkroad-life-suite-tutorial/metadata-file.png)
  
    a. **옵션2 - 메타데이터 파일** 에서 **찾아보기** 를 클릭하여 Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다.
  
    b. **파일 데이터를 사용하여 ID 공급자 만들기** 를 클릭합니다.

1. **인증 원본** 섹션에서 **편집** 을 클릭합니다.

    ![스크린샷은 편집 옵션이 선택된 인증 원본을 보여줍니다.](./media/silkroad-life-suite-tutorial/edit-source.png)

1. **인증 원본 편집** 대화 상자에서 다음 단계를 수행합니다.

    ![스크린샷은 설명된 값을 입력할 수 있는 인증 원본 대화 상자를 보여줍니다.](./media/silkroad-life-suite-tutorial/authentication.png)

    a. **사용** 을 **예** 로 선택합니다.

    b. **EntityId** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    다. **IdP 설명** 텍스트 상자에 구성에 대한 설명을 입력합니다(예: **Azure AD SSO**).

    d. **메타데이터 파일** 텍스트 상자에서, Azure Portal에서 다운로드한 **메타데이터** 파일을 업로드합니다.
  
    e. **IdP 이름** 텍스트 상자에 구성에 적용되는 이름을 입력합니다(예: *Azure SP*).
  
    f. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃 서비스 URL** 텍스트 상자에 붙여넣습니다.

    g. Azure Portal에서 복사한 **로그인 URL** 값을 **로그온 서비스 URL** 텍스트 상자에 붙여넣습니다.

    h. **저장** 을 클릭합니다.

1. 다른 모든 인증 원본을 사용하지 않도록 설정합니다.

    ![스크린샷은 다른 원본을 비활성화할 수 있는 인증 원본을 보여줍니다. ](./media/silkroad-life-suite-tutorial/manage-source.png)

### <a name="create-silkroad-life-suite-test-user"></a>SilkRoad Life Suite 테스트 사용자 만들기

이 섹션에서는 SilkRoad Life Suite에서 Britta Simon이라는 사용자를 만듭니다. [SilkRoad Life Suite 클라이언트 지원 팀](https://www.silkroad.com/locations/)과 함께 작업하여 SilkRoad Life Suite 플랫폼에서 사용자를 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 SilkRoad Life Suite 로그온 URL로 리디렉션됩니다. 

* SilkRoad Life Suite 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 SilkRoad Life Suite 타일을 클릭하면 SilkRoad Life Suite 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

SilkRoad Life Suite가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
