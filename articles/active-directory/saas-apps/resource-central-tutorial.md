---
title: '자습서: Resource Central - 회의실 예약 시스템용 SAML SSO와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Resource Central - 회의실 예약 시스템용 SAML SSO 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2021
ms.author: jeedes
ms.openlocfilehash: 0db8c8d14dbd3e09ebb798c41ea2d7b526acabc9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124753194"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-resource-central--saml-sso-for-meeting-room-booking-system"></a>자습서: Resource Central - 회의실 예약 시스템용 SAML SSO와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Resource Central – 회의실 예약 시스템용 SAML SSO를 Azure Active Directory(Azure AD)와 통합하는 방법에 대해 알아봅니다. Resource Central – 회의실 예약 시스템용 SAML SSO를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Resource Central – 회의실 예약 시스템용 SAML SSO에 액세스할 권한이 있는 사용자를 제어합니다.
* 사용자가 Azure AD 계정을 사용하여 Resource Central – 회의실 예약 시스템용 SAML SSO에 자동으로 로그인할 수 있도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Resource Central – 회의실 예약 시스템 SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Resource Central – 회의실 예약 시스템용 SAML SSO는 **SP** 시작 SSO를 지원

* Resource Central – 회의실 예약 시스템용 SAML SSO는 **Just In Time** 사용자 프로비저닝을 지원

## <a name="add-resource-central--saml-sso-for-meeting-room-booking-system-from-the-gallery"></a>Resource Central – 회의실 예약 시스템용 SAML SSO를 갤러리에서 추가

Resource Central – 회의실 예약 시스템용 SAML SSO를 Azure AD에 통합하도록 구성하려면 갤러리에서 관리형 SaaS 앱 목록에 Resource Central – 회의실 예약 시스템용 SAML SSO를 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Resource Central – 회의실 예약 시스템용 SAML SSO** 를 입력합니다.
1. 결과 창에서 **Resource Central – 회의실 예약 시스템용 SAML SSO** 를 선택한 다음 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-resource-central--saml-sso-for-meeting-room-booking-system"></a>Resource Central - 회의실 예약 시스템용 SAML SSO에 대한 Azure AD SSO를 구성 및 테스트

**B.Simon** 이라 불리는 테스트 사용자를 사용하여 Resource Central - 회의실 예약 시스템용 SAML SSO와 함께 Azure AD SSO를 구성 및 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Resource Central - 회의실 예약 시스템용 SAML SSO의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Resource Central – 회의실 예약 시스템용 SAML SSO와 함께 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행하세요.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
    1. **[Resource Central - 회의실 예약 시스템 테스트 사용자에 대한 SAML SSO 만들기](#create-resource-central-saml-sso-for-meeting-room-booking-system-test-user)** - 사용자의 Azure AD 표현과 연결된 Resource Central – 회의실 예약 시스템용 SAML SSO에 B.Simon에 해당하는 사용자를 만듭니다.
1. **[Resource Central - 회의실 예약 시스템 SSO에 대한 SSO 구성](#configure-resource-central-saml-sso-for-meeting-room-booking-system-sso)** - 애플리케이션 쪽에서 SSO 설정을 구성합니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Resource Central – 회의실 예약 시스템용 SAML SSO** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 에서 다음 필드에 값을 입력합니다.

   1. **로그온 URL** 텍스트 상자에서 `https://<DOMAIN_NAME>/ResourceCentral` 패턴을 사용하는 URL을 입력합니다.

   1. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<DOMAIN_NAME>/ResourceCentral` 패턴을 사용하는 URL을 입력합니다.

   1. **회신 URL** 텍스트 상자에서 `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/Acs` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL, 식별자 및 회신 URL 값으로 업데이트하세요. 이 값을 얻으려면 [Resource Central - 회의실 예약 시스템용 SAML SSO 클라이언트 지원 팀](mailto:st@aod.vn)에 문의하세요.  Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 에서 **인증서(Base64)** 를 찾고, **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Resource Central – 회의실 예약 시스템용 SAML SSO 설정** 에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 `username@companydomain.extension`을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Resource Central – 회의실 예약 시스템용 SAML SSO에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Resource Central – 회의실 예약 시스템용 SAML SSO** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 창에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 창의 **사용자** 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 **기본 액세스** 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 창에서 **할당** 단추를 클릭합니다.

### <a name="create-resource-central-saml-sso-for-meeting-room-booking-system-test-user"></a>Resource Central – 회의실 예약 시스템용 SAML SSO 테스트 사용자 만들기

이 섹션에서는 **Resource Central – 회의실 예약 시스템용 SAML SSO** 에 **B.Simon** 이라는 사용자를 만듭니다.

1. Resource Central – 회의실 예약 시스템용 SAML SSO에서 **보안** > **사람** > **새로 만들기** 를 차례로 선택합니다.
  
    :::image type="content" source="./media/resource-central/new-person.png" alt-text="새로 만들기 단추가 강조 표시된 Resource Central의 사람 창을 보여 주는 스크린샷":::

1. **사람 세부 정보** 에서 **표시 이름에** 사용자 **B.Simon** 을 입력합니다. **SMTP 주소** 에 사용자의 Azure AD 사용자 이름을 입력합니다. 예: `B.Simon@contoso.com`.

    :::image type="content" source="./media/resource-central/person.png" alt-text="Resource Central의 사람 세부 정보 창을 보여 주는 스크린샷":::

## <a name="configure-resource-central-saml-sso-for-meeting-room-booking-system-sso"></a>Resource Central – 회의실 예약 시스템용 SAML SSO 구성

이 섹션에서는 **Resource Central 시스템 관리자** 에서 Single Sign-On을 구성합니다.

1. Resource Central – 회의실 예약 시스템 시스템 관리자용 SAML SSO에서 **외부 인증** 을 선택합니다.
1.  **구성 사용** 에서 **예** 를 선택합니다.

    ![Resource Central - 회의실 예약 시스템용 SAML SSO의 외부 인증 창에서 선택한 구성 사용 옵션을 보여주는 스크린샷.](./media/resource-central/enable.png)

1. **인증 프로토콜** 에서 **SAML2** 를 선택합니다. 

   :::image type="content" source="./media/resource-central/protocol.png" alt-text="Resource Central에서 인증 프로토콜에 대해 선택된 SAML2를 보여 주는 스크린샷":::

1. **SAML2 구성** 에서 다음 필드에 값을 입력합니다.

    1. **식별자(엔터티 ID)** , **로그인 URL**, **로그아웃 URL** 및 **Azure AD 식별자** 에 관련 URL을 입력합니다.

       :::image type="content" source="./media/resource-central/auth.png" alt-text="Resource Central에서 SAML2 구성 창의 스크린샷":::

        **Resource Central – 회의실 예약 시스템용 SAML SSO** 창에서 URL을 복사합니다.

        :::image type="content" source="./media/resource-central/setup.png" alt-text="Resource Central에서 Resource Central 설정 창의 스크린샷":::

   1. **반환 URL** 에 `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/CallbackHandler`를 입력합니다.
  
1. **인증서** 에서 인증서를 업로드하고 암호를 입력합니다.

   ![Resource Central – 회의실 예약 시스템용 SAML SSO의 인증서 섹션 스크린샷.](./media/resource-central/cert.png)
   
1. **저장** 을 선택합니다.

1. **Azure Portal** 로 돌아갑니다. **SAML 서명 인증서** 에서 인증서를 업로드하고 암호를 입력합니다.

   ![Azure Portal에서 인증서 가져오기 창의 스크린샷](./media/resource-central/cert2.png).

1. **추가** 를 선택합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 Azure AD Single Sign-On 구성을 테스트합니다. Single Sign-On을 테스트할 때는 다음 세 가지 옵션을 사용할 수 있습니다.

* Azure Portal에서 **이 애플리케이션 테스트** 를 선택합니다. 링크는 Resource Central – 회의실 예약 시스템용 SAML SSO 로그온 URL로 리디렉션되며 여기에서 로그인을 시작할 수 있습니다.

* Resource Central – 회의실 예약 시스템용 SAML SSO 로그온 URL로 직접 이동하여 로그인을 시작합니다.

   :::image type="content" source="./media/resource-central/test.png" alt-text="Resource Central Single Sign-On 테스트 웹 페이지의 스크린샷":::

* Microsoft에서 내 앱 포털을 사용합니다. 내 앱 포털에서 **Resource Central – 회의실 예약 시스템용 SAML SSO** 타일을 선택하여 Resource Central – 회의실 예약 시스템용 SAML SSO 로그온 URL로 리디렉션합니다. 자세한 내용은 [내 앱 포털에서 앱에 로그인하여 시작](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure AD로 Single Sign-On(SSO)을 위한 Resource Central – 회의실 예약 시스템용 SAML SSO를 설정한 후 세션 제어를 적용하면 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).