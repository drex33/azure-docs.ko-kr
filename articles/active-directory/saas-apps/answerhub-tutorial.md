---
title: '자습서: AnswerHub와 Azure AD SSO 통합'
description: Azure Active Directory 및 AnswerHub 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: jeedes
ms.openlocfilehash: b4e701cb930a9d65a49be2cd83d2e6bf81c3a18b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985608"
---
# <a name="tutorial-azure-ad-sso-integration-with-answerhub"></a>자습서: AnswerHub와 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 AnswerHub를 통합하는 방법에 대해 알아봅니다. Azure AD와 AnswerHub를 통합하면 다음을 수행할 수 있습니다.

* AnswerHub에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 AnswerHub에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* AnswerHub SSO(Single Sign-On)가 설정된 구독.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* AnswerHub에서 SP 시작 SSO를 지원합니다.

## <a name="add-answerhub-from-the-gallery"></a>갤러리에서 AnswerHub 추가

AnswerHub의 Azure AD 통합을 구성하려면 갤러리의 AnswerHub를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **AnswerHub** 를 입력합니다.
1. 결과 패널에서 **AnswerHub** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="set-up-and-test-azure-ad-sso-for-answerhub"></a>AnswerHub에 대한 Azure AD SSO 설정 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 AnswerHub에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 AnswerHub의 관련 사용자 간에 연결 관계를 설정해야 합니다.

AnswerHub에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[AnswerHub SSO 구성](#configure-answerhub-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[AnswerHub 테스트 사용자 만들기](#create-answerhub-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 AnswerHub에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **AnswerHub** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 완료합니다.

    a. **식별자(엔터티 ID)** 상자에 이 패턴이 있는 URL `https://<company>.answerhub.com`을 입력합니다.
    
    b. **로그온 URL** 상자에 이 패턴이 있는 URL `https://<company>.answerhub.com`을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 해당 값을 실제 식별자 및 로그온 URL로 업데이트합니다. 값을 얻으려면 [AnswerHub 지원 팀](mailto:success@answerhub.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 요구 사항에 따라 **인증서(Base64)** 옆의 **다운로드** 링크를 선택하고 해당 인증서를 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **AnswerHub 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 AnswerHub에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **AnswerHub** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-answerhub-sso"></a>AnswerHub SSO 구성

이 섹션에서는 AnswerHub의 Single Sign-On을 설정합니다.  

**AnswerHub Single Sign-On을 구성하려면:**

1. 다른 웹 브라우저 창에서 AnswerHub 회사 사이트에 관리자 권한으로 로그인합니다.

    > [!NOTE]
    > AnswerHub를 구성하는 데 도움이 필요하면 [AnswerHub 지원 팀](mailto:success@answerhub.com.)에 문의하세요.

2. **관리** 로 이동합니다.

3. **사용자 및 그룹** 탭의 왼쪽 창에 있는 **소셜 설정** 섹션에서 **SAML 설정** 을 선택합니다.

4. **IDP 구성** 탭에서 다음 단계를 완료합니다.

    ![스크린샷은 사용자 및 그룹 탭이 선택된 AnswerHub 페이지를 보여줍니다.](./media/answerhub-tutorial/admin.png "SAML 설정")  

    a. **IDP 로그인 URL** 상자에 Azure Portal에서 복사한 **로그인 URL** 을 붙여넣습니다.

    b. **IDP 로그아웃 URL** 상자에 Azure Portal에서 복사한 **로그아웃 URL** 을 붙여넣습니다.

    다. Azure Portal의 **사용자 특성** 섹션에서 선택한 **식별자** 값을 **IDP 이름 식별자 형식** 상자에 입력합니다.

    d. **키 및 인증서** 를 선택합니다.

5. **키 및 인증서** 섹션에서 다음 단계를 완료합니다.

    ![키 및 인증서 섹션](./media/answerhub-tutorial/users.png "키 및 인증서")  

    a. 메모장의 Azure Portal에서 다운로드한 Base64로 인코딩된 인증서를 열고 콘텐츠를 복사한 다음, **IDP 공유 키(x509 형식)** 상자에 붙여넣습니다.

    b. **저장** 을 선택합니다.

6. **IDP 구성** 탭에서 다시 **저장** 을 선택합니다.

### <a name="create-answerhub-test-user"></a>AnswerHub 테스트 사용자 만들기

Azure AD 사용자가 AnswerHub에 로그인하도록 설정하려면 해당 사용자를 AnswerHub에 추가해야 합니다. AnswerHub에서 이 작업은 수동으로 수행됩니다.

**사용자 계정을 설정하려면:**

1. **AnswerHub** 회사 사이트에 관리자 권한으로 로그인합니다.

2. **관리** 로 이동합니다.

3. **사용자 및 그룹** 탭을 선택합니다.

4. 왼쪽 창의 **사용자 관리** 섹션에서 **사용자 만들기 또는 가져오기** 를 선택한 다음, **사용자 및 그룹** 을 선택합니다.

    ![스크린샷은 사용자 및 그룹 탭이 선택되고 사용자 만들기 또는 가져오기 링크가 호출된 AnswerHub 페이지를 보여줍니다.](./media/answerhub-tutorial/groups.png "사용자 및 그룹")

5. 추가하려는 유효한 Azure AD 계정의 **이메일 주소**, **사용자 이름** 및 **암호** 를 적절한 상자에 입력한 다음, **저장** 을 선택합니다.

> [!NOTE]
> 기타 모든 사용자 계정 생성 도구 또는 AnswerHub에서 제공된 API를 사용하여 Azure AD 사용자 계정을 설정할 수 있습니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 AnswerHub 로그온 URL로 리디렉션됩니다. 

* AnswerHub 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 AnswerHub 타일을 클릭하면 AnswerHub 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

AnswerHub를 구성하면 세션 제어를 적용하여 조직의 중요한 데이터 반출과 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).