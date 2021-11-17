---
title: '자습서: Azure Active Directory와 MOVEit Transfer - Azure AD 통합 통합 | Microsoft Docs'
description: Azure Active Directory와 MOVEit Transfer - Azure AD 통합 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 618cc7b60da58101995d98b6c04155255dcc916c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342303"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>자습서: Azure Active Directory와 MOVEit Transfer - Azure AD 통합 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 MOVEit Transfer - Azure AD integration을 통합하는 방법에 대해 알아봅니다. Azure AD와 MOVEit Transfer - Azure AD integration을 통합하면 다음을 수행할 수 있습니다.

* MOVEit Transfer - Azure AD integration에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 MOVEit Transfer - Azure AD integration에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* MOVEit Transfer - Azure AD integration SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* MOVEit Transfer - Azure AD 통합은 **SP** 시작 SSO를 지원합니다.

## <a name="add-moveit-transfer---azure-ad-integration-from-the-gallery"></a>갤러리에서 Add MOVEit Transfer - Azure AD integration 추가

MOVEit Transfer - Azure AD 통합의 Azure AD 통합을 구성하려면 갤러리의 MOVEit Transfer - Azure AD 통합을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **MOVEit Transfer - Azure AD integration** 을 입력합니다.
1. 결과 패널에서 **MOVEit Transfer - Azure AD integration** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-moveit-transfer---azure-ad-integration"></a>MOVEit Transfer - Azure AD integration에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 MOVEit Transfer - Azure AD integration에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 MOVEit Transfer - Azure AD integration의 관련 사용자 간에 연결 관계를 설정해야 합니다.

MOVEit Transfer - Azure AD integration에서 Azure AD SSO를 구성하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[MOVEit Transfer - Azure AD integration SSO 구성](#configure-moveit-transfer---azure-ad-integration-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[MOVEit Transfer - Azure AD integration 테스트 사용자 만들기](#create-moveit-transfer---azure-ad-integration-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 MOVEit Transfer - Azure AD integration에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **MOVEit Transfer - Azure AD integration** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **서비스 공급자 메타데이터 파일** 이 있으면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **메타데이터 파일 업로드** 를 클릭합니다.

    ![메타데이터 파일 업로드](common/upload-metadata.png)

    b. **폴더 로고** 를 클릭하여 메타데이터 파일을 선택하고 **업로드** 를 클릭합니다.

    ![메타데이터 파일 선택](common/browse-upload-metadata.png)

    다. 메타데이터 파일이 성공적으로 업로드되면 **식별자** 및 **회신 URL** 값이 **기본 SAML 구성** 섹션에 자동으로 채워집니다.

    ![MOVEit Transfer - Azure AD 통합 도메인 및 URL Single Sign-On 정보](common/sp-identifier-reply.png)

    **로그온 URL** 텍스트 상자에 `https://contoso.com` URL을 입력합니다.

    > [!NOTE]
    > **로그온 URL** 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. [MOVEit Transfer - Azure AD 통합 클라이언트 지원 팀](https://community.ipswitch.com/s/support)에 문의하여 해당 값을 구하세요. 자습서의 **MOVEit Transfer - Azure AD 통합 Single Sign-On** 섹션 뒷부분에 설명되어 있는 **서비스 공급자 메타데이터 URL** 에서 **서비스 공급자 메타데이터 파일** 을 다운로드할 수 있습니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **MOVEit Transfer - Azure AD 통합** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 B.Simon이 Azure Single Sign-On을 사용할 수 있도록 MOVEit Transfer - Azure AD integration에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **MOVEit Transfer - Azure AD 통합** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="configure-moveit-transfer---azure-ad-integration-sso"></a>MOVEit Transfer - Azure AD integration SSO 구성

1. MOVEit Transfer 테넌트에 관리자 권한으로 로그온합니다.

2. 왼쪽 탐색 창에서 **설정** 을 클릭합니다.

    ![앱 쪽의 설정 섹션](./media/moveittransfer-tutorial/settings.png)

3. **보안 정책 -> 사용자 인증** 아래의 **Single Sign On** 링크를 클릭합니다.

    ![앱 쪽의 보안 정책](./media/moveittransfer-tutorial/sso.png)

4. 메타데이터 URL 링크를 클릭하여 메타데이터 문서를 다운로드합니다.

    ![서비스 공급자 메타데이터 URL](./media/moveittransfer-tutorial/metadata.png)
    
   * **기본 SAML 구성** 섹션에서 **entityID** 가 **식별자** 와 일치하는지 확인합니다.
   * **AssertionConsumerService** 위치 URL이 **기본 SAML 구성** 섹션의 **회신 URL** 과 일치하는지 확인합니다.
    
     ![앱 쪽에서 Single Sign-On 구성](./media/moveittransfer-tutorial/xml.png)

5. **ID 공급자 추가** 단추를 클릭하여 새 페더레이션 ID 공급자를 추가합니다.

    ![ID 공급자 추가](./media/moveittransfer-tutorial/idp.png)

6. **찾아보기...** 를 클릭하여 Azure Portal에서 다운로드한 메타데이터 파일을 선택한 다음 **ID 공급자 추가** 를 클릭하여 다운로드한 파일을 업로드합니다.

    ![SAML ID 공급자](./media/moveittransfer-tutorial/saml.png)

7. **페더레이션 ID 공급자 설정 편집...** 페이지에서 **활성화** 로 "**예**"를 선택하고 **저장** 을 클릭합니다.

    ![페더레이션 ID 공급자 설정](./media/moveittransfer-tutorial/save.png)

8. **페더레이션 ID 공급자 사용자 설정 편집** 페이지에서 다음 작업을 수행합니다.
    
    ![페더레이션 ID 공급자 설정 편집](./media/moveittransfer-tutorial/attributes.png)
    
    a. **로그인 이름** 으로 **SAML NameID** 를 선택합니다.
    
    b. **전체 이름** 으로 **기타** 를 선택하고 **특성 이름** 텍스트 상자에 `http://schemas.microsoft.com/identity/claims/displayname` 값을 입력합니다.
    
    다. **전자 메일** 로 **기타** 를 선택하고 **특성 이름** 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` 값을 입력합니다.
    
    d. **SignOn에서 계정 자동 만들기** 로 **예** 를 선택합니다.
    
    e. **저장** 단추를 클릭합니다.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>MOVEit Transfer - Azure AD 통합 테스트 사용자 만들기

이 섹션은 MOVEit Transfer - Azure AD 통합에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. MOVEit Transfer - Azure AD 통합은 적시에 프로비전을 지원하며 사용하도록 설정되었습니다. 이 섹션에 작업 항목이 없습니다. 새 사용자가 아직 존재하지 않는 경우 MOVEit Transfer - Azure AD 통합에 액세스하는 동안 만들어집니다.

>[!NOTE]
>사용자를 수동으로 만들어야 하는 경우 [MOVEit Transfer - Azure AD 통합 클라이언트 지원 팀](https://community.ipswitch.com/s/support)에 문의해야 합니다.

### <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 MOVEit Transfer - Azure AD integration 로그온 URL로 리디렉션됩니다. 

* MOVEit Transfer - Azure AD integration 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 MOVEit Transfer - Azure AD integration 타일을 클릭하면 SSO를 설정한 MOVEit Transfer - Azure AD integration에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

MOVEit Transfer - Azure AD integration이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
