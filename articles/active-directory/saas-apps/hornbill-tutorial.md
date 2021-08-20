---
title: '자습서: Hornbill과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Hornbill 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/23/2021
ms.author: jeedes
ms.openlocfilehash: 2cdc549a5231fed6c6819c0952cb2d7431757852
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114688341"
---
# <a name="tutorial-azure-active-directory-integration-with-hornbill"></a>자습서: Hornbill과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Hornbill을 통합하는 방법에 대해 알아봅니다. Azure AD와 Hornbill을 통합하면 다음을 수행할 수 있습니다.

* Hornbill에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Hornbill에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Hornbill SSO(Single Sign-On)가 설정된 구독

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Hornbill은 **SP** 시작 SSO를 지원합니다.
* Hornbill은 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

## <a name="add-hornbill-from-the-gallery"></a>갤러리에서 Hornbill 추가

Hornbill의 Azure AD 통합을 구성하려면 갤러리의 Hornbill을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Hornbill** 을 입력합니다.
1. 결과 패널에서 **Hornbill** 을 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-hornbill"></a>Hornbill에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Hornbill에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Hornbill의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Hornbill에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Hornbill SSO 구성](#configure-hornbill-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Hornbill 테스트 사용자 만들기](#create-hornbill-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Hornbill에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Hornbill** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/lib/saml/auth/simplesaml/module.php/saml/sp/metadata.php/saml` 패턴을 사용하는 URL을 입력합니다.

    b. **로그온 URL** 텍스트 상자에서 `https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 해당 값을 실제 식별자 및 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [Hornbill 클라이언트 지원 팀](https://www.hornbill.com/support/?request/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL** 을 복사한 후 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Hornbill에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Hornbill** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-hornbill-sso"></a>Hornbill SSO 구성

1. 다른 웹 브라우저 창에서 Hornbill에 보안 관리자로 로그인합니다.

2. 홈 페이지에서 **시스템** 을 클릭합니다.

    ![Hornbill 시스템](./media/hornbill-tutorial/system.png   "Hornbill 시스템")

3. **보안** 으로 이동합니다.

    ![Hornbill 보안](./media/hornbill-tutorial/security.png "Hornbill 보안")

4. **SSO 프로필** 을 클릭합니다.

    ![Hornbill 단일](./media/hornbill-tutorial/profile.png "Hornbill 단일")

5. 페이지 오른쪽 상단에서 **로고 추가** 를 클릭합니다.

    ![Hornbill 추가](./media/hornbill-tutorial/add-logo.png "Hornbill 추가")

6. **프로필 세부 정보** 막대에서 **SAML 메타 로고 가져오기** 를 클릭합니다.

    ![Hornbill 로고](./media/hornbill-tutorial/logo.png "Hornbill 로고")

7. Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 을 **URL** 텍스트 상자의 팝업 페이지에 붙여넣고 **프로세스** 를 클릭합니다.

    ![Hornbill 프로세스](./media/hornbill-tutorial/process.png "Hornbill 프로세스")

8. 프로세스를 클릭하면 **프로필 세부 정보** 섹션에 값이 자동으로 채워집니다.

    ![Hornbill 페이지1](./media/hornbill-tutorial/page.png "Hornbill 페이지1")

    ![Hornbill 페이지2](./media/hornbill-tutorial/services.png "Hornbill 페이지2")

    ![Hornbill 페이지3](./media/hornbill-tutorial/details.png "Hornbill 페이지3")

9. **변경 내용 저장** 을 클릭합니다.

### <a name="create-hornbill-test-user"></a>Hornbill 테스트 사용자 만들기

이 섹션에서는 Hornbill에서 Britta Simon이라는 사용자를 만듭니다. Hornbill은 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. Hornbill에 사용자가 아직 없는 경우 인증 후 새 사용자를 만듭니다.

> [!Note]
> 사용자를 수동으로 만들어야 하는 경우 [Hornbill 클라이언트 지원 팀](https://www.hornbill.com/support/?request/)에 문의하세요.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Hornbill 로그온 URL로 리디렉션됩니다. 

* Hornbill 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Hornbill 타일을 클릭하면 Hornbill 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Hornbill이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).