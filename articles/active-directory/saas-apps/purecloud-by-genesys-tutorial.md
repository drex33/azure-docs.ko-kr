---
title: '자습서: Genesys Cloud for Azure와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Genesys Cloud for Azure 간 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/26/2021
ms.author: jeedes
ms.openlocfilehash: fdc6e6839af33a154f331940bba354c05e6b3f64
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110580294"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-genesys-cloud-for-azure"></a>자습서: Genesys Cloud for Azure와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Genesys Cloud for Azure를 연결하는 방법을 알아봅니다. Genesys Cloud for Azure를 Azure AD와 연결하면 다음을 수행할 수 있습니다.

* Genesys Cloud for Azure에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Genesys Cloud for Azure에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Genesys Cloud for Azure SSO(Single Sign-On) 사용 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Genesys Cloud for Azure는 **SP 및 IDP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-genesys-cloud-for-azure-from-the-gallery"></a>갤러리에서 Genesys Cloud for Azure 추가

Azure AD에 Genesys Cloud for Azure를 통합하도록 구성하려면 갤러리의 Genesys Cloud for Azure를 관리형 SaaS 앱 목록에 추가해야 합니다. 이렇게 하려면 다음 단계를 수행하세요.

1. 회사 또는 학교 계정을 사용하거나 개인 Microsoft 계정을 사용하여 Azure Portal에 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Genesys Cloud for Azure** 를 입력합니다.
1. 결과 패널에서 **Genesys Cloud for Azure** 를 선택하고 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-genesys-cloud-for-azure"></a>Genesys Cloud for Azure에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Genesys Cloud for Azure에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Genesys Cloud for Azure의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Genesys Cloud for Azure에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. 사용자가 이 기능을 사용할 수 있도록 **[Azure AD SSO를 구성](#configure-azure-ad-sso)** 합니다.
    1. **[Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)** 하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)** 하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Genesys Cloud for Azure SSO를 구성](#configure-genesys-cloud-for-azure-sso)** 하여 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Genesys Cloud for Azure 테스트 사용자를 생성](#create-genesys-cloud-for-azure-test-user)** 하여 B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Genesys Cloud for Azure에서 만듭니다.
1. **[SSO를 테스트](#test-sso)** 하여 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Genesys Cloud for Azure** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 선택하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 상자에 사용자 지역에 해당하는 URL을 입력합니다.
    
    | 식별자 URL |
    |---|
    | https://login.mypurecloud.com/saml |
    | https://login.mypurecloud.de/saml |
    | https://login.mypurecloud.jp/saml |
    | https://login.mypurecloud.ie/saml |
    | https://login.mypurecloud.com.au/saml |
    |

    b. **회신 URL** 상자에 사용자 지역에 해당하는 URL을 입력합니다.

    | 회신 URL |
    |---|
    | https://login.mypurecloud.com/saml |
    | https://login.mypurecloud.de/saml |
    | https://login.mypurecloud.jp/saml |
    | https://login.mypurecloud.ie/saml |
    | https://login.mypurecloud.com.au/saml |
    |

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 을 선택하고, 다음 단계를 수행합니다.

    **로그온 URL** 상자에 사용자 지역에 해당하는 URL을 입력합니다.
    
    |로그온 URL |
    |---|
    | https://login.mypurecloud.com |
    | https://login.mypurecloud.de |
    | https://login.mypurecloud.jp |
    | https://login.mypurecloud.ie |
    | https://login.mypurecloud.com.au |
    |

1. Genesys Cloud for Azure 애플리케이션은 특정 형식의 SAML 어설션을 예상하므로 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여줍니다.

    ![이미지](common/default-attributes.png)

1. 또한 다음 표와 같이 Genesys Cloud for Azure 애플리케이션에는 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | 속성 | 원본 특성|
    | ---------------| --------------- |
    | Email | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 다운로드하고 본인의 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Genesys Cloud for Azure 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에 username@companydomain.extension 형식의 사용자 이름을 입력합니다. 예: `B.Simon@contoso.com`
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어 둡니다.
   1. **만들기** 를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Genesys Cloud for Azure에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Genesys Cloud for Azure** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-genesys-cloud-for-azure-sso"></a>Genesys Cloud for Azure SSO 구성

1. 다른 웹 브라우저 창에서 Genesys Cloud for Azure에 관리자 권한으로 로그인합니다.

1. 맨 위에서 **관리자** 를 선택한 다음, **통합** 아래에 있는 **Single Sign-On** 으로 이동합니다.

    ![스크린샷은 Single Sign-On을 선택할 수 있는 PureCloud 관리자 창을 보여줍니다.](./media/purecloud-by-genesys-tutorial/configure-1.png)

1. **ADFS/Azure AD(Premium)** 탭으로 전환한 다음, 다음 단계를 수행합니다.

    ![스크린샷은 설명된 값을 입력할 수 있는 통합 페이지를 보여줍니다.](./media/purecloud-by-genesys-tutorial/configure-2.png)

    a. **찾아보기** 를 선택하여 Azure Portal에서 다운로드한 base-64 인코딩 인증서를 **ADFS 인증서** 에 업로드합니다.

    b. **ADFS 발급자 URI** 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    다. **대상 URI** 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    d. **신뢰 당사자 식별자** 값의 경우 Azure Portal로 이동한 다음, **Genesys Cloud for Azure** 애플리케이션 통합 페이지에서 **속성** 탭을 선택하고 **애플리케이션 ID** 값을 복사합니다. 이것을 **신뢰 당사자 식별자** 상자에 붙여넣습니다.

    ![애플리케이션 ID 값을 찾을 수 있는 속성 창 스크린샷](./media/purecloud-by-genesys-tutorial/configuration.png)

    e. **저장** 을 선택합니다.

### <a name="create-genesys-cloud-for-azure-test-user"></a>Genesys Cloud for Azure 테스트 사용자 만들기

Azure AD 사용자가 Genesys Cloud for Azure에 로그인할 수 있게 하려면 Genesys Cloud for Azure에 프로비저닝해야 합니다. Genesys Cloud for Azure에서 프로비저닝은 수동 작업입니다.

**사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.**

1. 관리자 권한으로 Genesys Cloud for Azure에 로그인합니다.

1. 맨 위에서 **관리자** 를 선택하고 **사용자 및 사용 권한** 아래에 있는 **사용자** 로 이동합니다.

    ![스크린샷은 사용자를 선택할 수 있는 PureCloud 관리자 창을 보여줍니다.](./media/purecloud-by-genesys-tutorial/configure-3.png)

1. **사용자** 페이지에서 **사람 추가** 를 선택합니다.

    ![스크린샷은 사용자를 추가할 수 있는 사용자 페이지를 보여줍니다.](./media/purecloud-by-genesys-tutorial/configure-4.png)

1. **조직에 사람 추가** 대화 상자에서 다음 단계를 수행합니다.

    ![스크린샷은 설명된 값을 입력할 수 있는 페이지를 보여줍니다.](./media/purecloud-by-genesys-tutorial/configure-5.png)

    a. **전체 이름** 상자에서 사용자의 이름을 입력합니다. 다음은 그 예입니다.  **B.simon**.

    b. **이메일** 상자에서 사용자의 이메일을 입력합니다. 예: **b.simon\@contoso.com**.

    다. **만들기** 를 선택합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Genesys Cloud for Azure 로그온 URL로 리디렉션됩니다.  

* Genesys Cloud for Azure 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Genesys Cloud for Azure에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Genesys Cloud for Azure 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위한 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Genesys Cloud for Azure에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Genesys Cloud for Azure를 구성한 후에는 세션 제어를 적용하여 중요한 조직 데이터의 반출과 침입을 실시간으로 방지할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).