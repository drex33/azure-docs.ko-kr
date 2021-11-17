---
title: '자습서: Saba TalentSpace와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Saba TalentSpace 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
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
ms.openlocfilehash: 1e50d7e81699897862e0c3121795f5b4adc29569
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132321381"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>자습서: Saba TalentSpace와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Saba TalentSpace를 통합하는 방법에 대해 알아봅니다. Azure AD와 Saba TalentSpace를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Saba TalentSpace에 액세스할 수 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Saba TalentSpace에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Saba TalentSpace SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Saba TalentSpace에서 **SP** 시작 SSO를 지원합니다.

## <a name="add-saba-talentspace-from-the-gallery"></a>갤러리에서 Saba TalentSpace 추가

Saba TalentSpace가 Azure AD에 통합되도록 구성하려면 갤러리의 Saba TalentSpace를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Saba TalentSpace** 를 입력합니다.
1. 결과 패널에서 **Saba TalentSpace** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-saba-talentspace"></a>Saba TalentSpace에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Saba TalentSpace에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Saba TalentSpace의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Saba TalentSpace에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Saba TalentSpace SSO 구성](#configure-saba-talentspace-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Saba TalentSpace 테스트 사용자 만들기](#create-saba-talentspace-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Saba TalentSpace에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Saba TalentSpace** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://global.hgncloud.com/[companyname]/saml/login` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://global.hgncloud.com/[companyname]/saml/metadata` 패턴을 사용하는 URL을 입력합니다.

    다. **회신 URL(Assertion Consumer Service URL)** 텍스트 상자에 `https://global.hgncloud.com/[companyname]/saml/SSO` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Saba TalentSpace 클라이언트 지원 팀](https://support.saba.com/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Saba TalentSpace 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Saba TalentSpace에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Saba TalentSpace** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-saba-talentspace-sso"></a>Saba TalentSpace SSO 구성

1. 다른 브라우저 창에서 **Saba TalentSpace** 애플리케이션에 관리자 권한으로 로그온합니다.

2. **옵션** 탭을 클릭합니다.
  
    !["옵션" 탭이 선택된 "Saba TalentSpace" 홈페이지를 보여주는 스크린샷.](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. 왼쪽 탐색 창에서 **SAML 구성** 을 클릭합니다.
  
    !["SAML 구성"이 선택된 "사용자 인터페이스" 왼쪽 탐색 창을 보여주는 스크린샷.](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. **SAML 구성** 페이지에서 다음 단계를 수행합니다.

    !["설정" 옵션이 강조 표시된 "SAML 구성" 페이지를 보여주는 스크린샷.](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. **고유 식별자** 로 **NameID** 를 선택합니다.

    b. **고유 식별자 매핑 대상** 으로 **Username** 을 선택합니다.
  
    다. 다운로드한 메타데이터 파일을 업로드하려면 **찾아보기** 를 클릭하여 파일을 선택하고 **파일 업로드** 를 클릭합니다.

    d. 구성을 테스트하려면 **테스트 실행** 을 클릭합니다.

    > [!NOTE]
    > "*SAML 테스트가 완료되었습니다. 이 창을 닫으십시오.* " 메시지가 표시될 때까지 기다려야 합니다. 그런 다음 열려 있는 브라우저 창을 닫습니다. **SAML 사용** 확인란은 테스트가 완료된 경우에만 사용하도록 설정됩니다.

    e. **SAML 사용** 을 선택합니다.

    f. **변경 내용 저장** 을 클릭합니다.

### <a name="create-saba-talentspace-test-user"></a>Saba TalentSpace 테스트 사용자 만들기

이 섹션은 Saba TalentSpace에서 Britta Simon이라는 사용자를 만들기 위한 것입니다.

**Saba TalentSpace에서 Britta Simon이라는 사용자를 만들려면 다음 단계를 수행합니다.**

1. **Saba TalentSpace** 애플리케이션에 관리자 권한으로 로그온합니다.

2. **사용자 센터** 탭을 클릭한 후 **사용자 만들기** 를 클릭합니다.

    !["사용자 센터" 탭과 선택된 "사용자 만들기"를 보여주는 스크린샷.](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. **새 사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![Azure AD Connect의 정의](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. **이름** 텍스트 상자에서 사용자의 이름(예: **B**)을 입력합니다.

    b. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

    다. **사용자 이름** 텍스트 상자에서 Azure Portal에서와 동일한 사용자 이름인 **B.Simon** 을 입력합니다.

    d. **암호** 텍스트 상자에서 B.Simon에 대한 암호를 입력합니다.

    e. **저장** 을 클릭합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Saba TalentSpace 로그온 URL로 리디렉션됩니다. 

* Saba TalentSpace 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Saba TalentSpace 타일을 클릭하면 SSO를 설정한 Saba TalentSpace에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

 Saba TalentSpace가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
