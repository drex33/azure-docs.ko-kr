---
title: '자습서: Amazon Business와 Azure Active Directory 연결 | Microsoft Docs'
description: Azure Active Directory 및 Amazon Business 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/16/2021
ms.author: jeedes
ms.openlocfilehash: 00d40649effd5aec98233e81e937c96f0d23e284
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132336490"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>자습서: Azure Active Directory와 Amazon Business 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Amazon Business를 통합하는 방법에 대해 알아봅니다. Azure AD와 Amazon Business를 통합하면 다음 작업을 수행할 수 있습니다.

* Azure AD에서 Amazon Business에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Amazon Business에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Amazon Business SSO(Single Sign-On)를 사용하도록 설정된 구독. [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) 페이지로 이동하여 Amazon Business 계정을 만듭니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 기존 Amazon Business 계정에서 Azure AD SSO를 구성하고 테스트합니다.

* Amazon Business에서 **SP 및 IDP** 시작 SSO를 지원합니다.
* Amazon Business에서 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-amazon-business-from-the-gallery"></a>갤러리에서 Amazon Business 추가

Amazon Business가 Azure AD에 통합되도록 구성하려면 갤러리에서 Amazon Business를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Amazon Business** 를 입력합니다.
1. 결과 패널에서 **Amazon Business** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-amazon-business"></a>Amazon Business에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Amazon Business에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Amazon Business의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Amazon Business에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Amazon Business SSO 구성](#configure-amazon-business-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Amazon Business 테스트 사용자 만들기](#create-amazon-business-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Amazon Business에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Amazon Business** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 시작 모드에서 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    1. **식별자(엔터티 ID)** 텍스트 상자에서 다음 URL 중 하나를 입력합니다.

       | URL | 지역 |
       |-|-|
       | `https://www.amazon.com`| 북아메리카 |
       | `https://www.amazon.co.jp`| 동아시아 |
       | `https://www.amazon.de`| 유럽 |

    1. **회신 URL** 텍스트 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다.

       | URL | 지역 |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`| 북아메리카 |
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`| 동아시아 |
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`| 유럽 |

       > [!NOTE]
       > [회신 URL] 값은 실제 값이 아닙니다. 실제 회신 URL로 이 값을 업데이트하세요. `<idpid>` 값은 자습서의 뒷부분에서 설명하는 Amazon Business SSO 구성 섹션에서 가져올 수 있습니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 Amazon Business 구성에서 제공된 전체 URL을 **추가 URL 설정** 섹션의 **로그온 URL** 에 추가해야 합니다.

1. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **사용자 특성 및 클레임** 섹션에서 **연필** 아이콘을 클릭하여 특성을 편집합니다.

    ![Givenname user.givenname 및 Emailaddress User.mail과 같은 기본값이 있는 사용자 특성 및 클레임을 보여주는 스크린샷](media/amazon-business-tutorial/map-attribute.png)

1. 특성을 편집하고, 이러한 특성의 **네임스페이스** 값을 메모장에 복사합니다.

    ![클레임 이름 및 값에 대한 열이 있는 [사용자 특성 및 클레임]을 보여주는 스크린샷](media/amazon-business-tutorial/attribute.png)

1. 위에서 언급한 특성 외에도 Amazon Business 애플리케이션에는 SAML 응답에서 다시 전달되는 몇 가지 특성이 추가로 필요합니다. **그룹 클레임** 대화 상자의 **사용자 특성 및 클레임** 섹션에서 다음 단계를 수행합니다.

    1. **클레임에서 반환되는 그룹** 옆에 있는 **펜** 을 클릭합니다.

        ![선택한 클레임에서 반환된 그룹의 아이콘이 있는 [사용자 특성 및 클레임]을 보여주는 스크린샷](./media/amazon-business-tutorial/claim.png)

        ![이 절차에 설명된 값이 있는 [그룹 클레임]을 보여주는 스크린샷](./media/amazon-business-tutorial/group-claim.png)

    1. 라디오 단추 목록에서 **모든 그룹** 을 선택합니다.

    1. **원본 특성** 으로 **그룹 ID** 를 선택합니다.

    1. **그룹 클레임 이름 사용자 지정** 확인란을 선택하고, 조직 요구 사항에 따라 그룹 이름을 입력합니다.

    1. **저장** 을 클릭합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드하여 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Amazon Business 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

> [!NOTE]
> 관리자는 필요에 따라 테넌트에서 테스트 사용자를 만들어야 합니다. 다음 단계에서는 테스트 사용자를 만드는 방법을 보여 줍니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Azure Portal에서 Azure AD 보안 그룹 만들기

1. **Azure Active Directory > 모든 그룹** 을 클릭합니다.

    ![[그룹] 창에서 Azure Active Directory 및 [모든 그룹]을 선택한 Azure Portal 메뉴를 보여주는 스크린샷](./media/amazon-business-tutorial/all-groups-tab.png)

1. **새 그룹** 을 클릭합니다.

    ![[새 그룹] 단추를 보여주는 스크린샷](./media/amazon-business-tutorial/new-group-tab.png)

1. **그룹 유형**, **그룹 이름**, **그룹 설명**, **멤버 자격 유형** 을 입력합니다. 화살표를 클릭하여 멤버를 선택하고 그룹에 추가하려는 멤버를 검색하거나 클릭합니다. **선택** 을 클릭하여 선택한 멤버를 추가하고 **만들기** 를 클릭합니다.

    ![멤버 선택 및 외부 사용자 초대를 비롯한 옵션이 포함된 [그룹] 창을 보여주는 스크린샷](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Amazon Business에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Amazon Business** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

    >[!NOTE]
    > Azure AD에서 사용자를 할당하지 않으면 다음 오류가 발생합니다.

    ![로그인할 수 없다는 오류 메시지를 보여주는 스크린샷](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Azure Portal에서 Azure AD 보안 그룹 할당

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Amazon Business** 를 차례로 선택합니다.
2. 애플리케이션 목록에서 **Amazon Business** 를 입력하고 선택합니다.
3. 왼쪽 메뉴에서 **사용자 및 그룹** 을 선택합니다.
4. **사용자 추가** 를 클릭합니다.
5. 사용하려는 보안 그룹을 검색하고 그룹을 클릭하여 멤버 선택 섹션에 추가합니다. **선택** 을 클릭한 다음, **할당** 을 클릭합니다.

    ![보안 그룹 검색](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Azure Portal에서 엔터프라이즈 애플리케이션에 그룹이 할당되었다는 알림을 받으려면 메뉴 모음에서 알림을 선택합니다.

## <a name="configure-amazon-business-sso"></a>Amazon Business SSO 구성

1. 다른 웹 브라우저 창에서 Amazon Business 회사 사이트에 관리자 권한으로 로그인합니다.

1. **User Profile**(사용자 프로필)을 클릭하고, **Business Settings**(비즈니스 설정)를 선택합니다.

    ![User Profile](media/amazon-business-tutorial/user-profile.png)

1. **System integrations**(시스템 통합) 마법사에서 **SSO(Single Sign-On)** 를 선택합니다.

    ![SSO(Single Sign-On)](media/amazon-business-tutorial/sso-settings.png)

1. **Set up SSO**(SSO 설정) 마법사에서 조직의 요구 사항에 따라 공급자를 선택하고 **Next**(다음)를 클릭합니다.

    ![Microsoft Azure AD 및 [다음]을 선택한 [SSO 설정]을 보여주는 스크린샷](media/amazon-business-tutorial/default-group.png)

    > [!NOTE]
    > Microsoft ADFS는 목록에 있는 옵션이지만 Azure AD SSO에서는 작동하지 않습니다.

1. **New user account defaults**(새 사용자 계정 기본값) 마법사에서 **Default Group**(기본 그룹)을 선택한 다음, 조직의 사용자 역할에 따라 **Default Buying Role**(기본 구매 역할)을 선택하고 **Next**(다음)를 클릭합니다.

    ![Microsoft SSO, [요청자] 및 [다음]이 선택된 [새로운 사용자 계정 기본값]을 보여주는 스크린샷](media/amazon-business-tutorial/group.png)

1. **Upload your metadata file**(메타데이터 파일 업로드) 마법사에서 **Browse**(찾아보기)를 클릭하여 Azure Portal에서 다운로드한 **메타데이터 XML** 파일을 업로드하고, **Upload**(업로드)를 클릭합니다.

    ![xml 파일을 찾아 업로드할 수 있는 [메타데이터 파일 업로드]를 보여주는 스크린샷](media/amazon-business-tutorial/connection-data.png)

1. 다운로드한 메타데이터 파일이 업로드되면 **Connection Data**(연결 데이터) 섹션의 필드가 자동으로 채워집니다. 그런 다음, **Next**(다음)를 클릭합니다.

    ![Azure AD 식별자, 로그인 URL 및 SAML 서명 인증서를 지정할 수 있는 연결 데이터를 보여주는 스크린샷](media/amazon-business-tutorial/connection.png)

1. **Upload your Attribute statement**(특성 문 업로드)를 마법사에서 **Skip**(건너뛰기)을 클릭합니다.

    ![특성 명령문을 검색할 수 있는 [특성 명령문 업로드]의 스크린샷. 하지만 이 예에서는 [건너뛰기]를 선택합니다.](media/amazon-business-tutorial/upload-attribute.png)

1. **Attribute mapping**(특성 매핑) 마법사에서 **+ Add a field**(+ 필드 추가) 옵션을 클릭하여 요구 사항 필드를 추가합니다. Azure Portal의 **사용자 특성 및 클레임** 섹션에서 복사한 네임스페이스가 포함된 특성 값을 **SAML AttributeName** 필드에 추가하고, **Next**(다음)를 클릭합니다.

    ![Amazon 데이터 SAML 특성 이름을 편집할 수 있는 [특성 매핑]을 보여주는 스크린샷](media/amazon-business-tutorial/attribute-mapping.png)

1. **Amazon connection data**(Amazon 연결 데이터) 마법사에서 **Next**(다음)를 클릭합니다.

    ![[다음]을 클릭하여 계속 진행할 수 있는 Amazon 연결 데이터를 보여주는 스크린샷](media/amazon-business-tutorial/amazon-connect.png)

1. 구성된 단계의 **Status**(상태)를 확인하고, **Start testing**(테스트 시작)을 클릭합니다.

    ![[테스트 시작] 옵션이 있는 SSO 연결 세부 정보를 보여주는 스크린샷](media/amazon-business-tutorial/status.png)

1. **Test SSO Connection**(SSO 연결 테스트) 마법사에서 **Test**(테스트)를 클릭합니다.

    ![[테스트] 단추가 있는 [SSO 연결 테스트]를 보여주는 스크린샷](media/amazon-business-tutorial/test.png)

1. **IDP initiated URL**(IDP 시작 URL) 마법사에서 **Activate**(활성화)를 클릭하기 전에 **idpid** 에 할당된 값을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **회신 URL** 의 **idpid** 매개 변수에 붙여넣습니다.

    ![테스트에 필요한 URL을 가져온 후 [활성화]를 선택할 수 있는 IDP 시작 URL을 보여주는 스크린샷](media/amazon-business-tutorial/activate.png)

1. **Are you ready to switch to active SSO?** (활성 SSO로 전환할 준비가 되었습니까?) 마법사에서 **I have fully tested SSO and am ready to go live**(SSO가 완전히 테스트되었으며 라이브로 전환할 준비가 되었습니다) 확인란을 선택하고, **Switch to active**(활성으로 전환)를 클릭합니다.

    ![[활성으로 전환]을 선택할 수 있는 [활성 SSO로 전환할 준비가 되었습니까?] 확인 메시지를 보여주는 스크린샷](media/amazon-business-tutorial/switch-active.png)

1. 마지막으로 **SSO Connection details**(SSO 연결 세부 정보) 섹션에서 **Status**(상태)가 **Active**(활성)로 표시됩니다.

    ![상태가 [활성]인 [SSO 연결 세부 정보]를 보여주는 스크린샷](media/amazon-business-tutorial/details.png)

    > [!NOTE]
    > **SP** 시작 모드에서 애플리케이션을 구성하려면 다음 단계를 완료하고 Azure Portal에서 **추가 URL 설정** 섹션의 **로그온 URL** 텍스트 상자에 있는 상단의 스크린샷에서 로그온 URL을 붙여넣습니다. 이때 다음 형식을 사용합니다.
    >
    > `https://www.amazon.<TLD>/bb/feature/sso/action/start?domain_hint=<UNIQUE_ID>`

### <a name="create-amazon-business-test-user"></a>Amazon Business 테스트 사용자 만들기

이 섹션에서는 Amazon Business에서 B.Simon이라는 사용자를 만듭니다. Amazon Business는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Amazon Business에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Amazon Business 로그온 URL로 리디렉션됩니다.  

* Amazon Business 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Amazon Business에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Amazon Business 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Amazon Business에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Amazon Business가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
