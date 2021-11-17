---
title: '자습서: Amazon Managed Grafana와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Amazon Managed Grafana 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/25/2021
ms.author: jeedes
ms.openlocfilehash: e018fa55b37018478e2c965ffefe1c113beebb30
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339089"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-managed-grafana"></a>자습서: Amazon Managed Grafana와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Amazon Managed Grafana를 통합하는 방법에 대해 알아봅니다. Amazon Managed Grafana를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Amazon Managed Grafana에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Amazon Managed Grafana에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* AWS(Amazon Web Services) [체험 계정](https://aws.amazon.com/free/)
* Amazon Managed Grafana SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Amazon Managed Grafana에서 **SP** 시작 SSO를 지원합니다.
* Amazon Managed Grafana에서 **Just In Time** 사용자 프로비저닝을 지원합니다.

## <a name="add-amazon-managed-grafana-from-the-gallery"></a>갤러리에서 Amazon Managed Grafana 추가

Amazon Managed Grafana가 Azure AD에 통합되도록 구성하려면 갤러리에서 Amazon Managed Grafana를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Amazon Managed Grafana** 를 입력합니다.
1. 결과 패널에서 **Amazon Managed Grafana** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-amazon-managed-grafana"></a>Amazon Managed Grafana에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Amazon Managed Grafana에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Amazon Managed Grafana의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Amazon Managed Grafana에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Amazon Managed Grafana SSO 구성](#configure-amazon-managed-grafana-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Amazon Managed Grafana 테스트 사용자 만들기](#create-amazon-managed-grafana-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Amazon Managed Grafana에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Amazon Managed Grafana** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<namespace>.grafana-workspace.<region>.amazonaws.com/saml/metadata` 패턴을 사용하는 URL을 입력합니다.

    b. **로그온 URL** 텍스트 상자에서 `https://<namespace>.grafana-workspace.<region>.amazonaws.com/login/saml` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 해당 값을 실제 식별자 및 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [Amazon Managed Grafana 클라이언트 지원 팀](https://aws.amazon.com/contact-us/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. Amazon Managed Grafana 애플리케이션에는 특정 형식의 SAML 어설션이 필요하므로 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 Amazon Managed Grafana 애플리케이션에는 SAML 응답에 몇 가지 특성이 추가로 다시 전달되어야 하며 해당 특성은 아래와 같습니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.
    
    | Name | 원본 특성 |
    | ----------| --------- |
    | displayName | user.displayname |
    | mail | user.userprincipalname |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Amazon Managed Grafana 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Amazon Managed Grafana에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Amazon Managed Grafana** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-amazon-managed-grafana-sso"></a>Amazon Managed Grafana SSO 구성

1. Amazon Managed Grafana 콘솔에 관리자 권한으로 로그인합니다.

1. **작업 영역 만들기** 를 클릭합니다. 

    ![작업 영역 만들기를 보여주는 스크린샷](./media/amazon-managed-grafana-tutorial/console.png "작업 영역")

1. **Specify workspace details**(작업 영역 세부 정보 지정) 페이지에서 고유한 **작업 영역 이름** 을 입력하고 **다음** 을 클릭합니다.

    ![작업 영역 세부 정보를 보여주는 스크린샷](./media/amazon-managed-grafana-tutorial/details.png "세부 정보 지정")

1. **설정 구성** 페이지에서 **SAML(Security Assertion Markup Language)** 확인란을 선택하고 **서비스 관리** 를 권한 유형으로 사용하도록 설정하고 **다음** 을 클릭합니다.

    ![작업 영역 설정을 보여주는 스크린샷](./media/amazon-managed-grafana-tutorial/security.png "설정")

1. **Service managed permission settings**(서비스 관리 권한 설정)에서 **현재 계정** 을 선택하고 **다음** 을 클릭합니다.

    ![사용 권한 설정을 보여주는 스크린샷](./media/amazon-managed-grafana-tutorial/setting.png "권한")

1. **검토 및 만들기** 페이지에서 작업 영역 세부 정보를 모두 확인하고 **작업 영역 만들기** 를 클릭합니다.

    ![검토 및 만들기 페이지를 보여주는 스크린샷](./media/amazon-managed-grafana-tutorial/review-workspace.png "작업 영역 만들기")

1. 작업 영역을 만든 후 **설치 완료** 를 클릭하여 SAML 구성을 완료합니다.

    ![SAML 구성을 보여주는 스크린샷](./media/amazon-managed-grafana-tutorial/setup.png "SAML 구성")

1. **SAML(Security Assertion Markup Language)** 페이지에서 다음 단계를 수행합니다.

    ![SAML 설치를 보여주는 스크린샷](./media/amazon-managed-grafana-tutorial/configuration.png "SAML 설정")

    1. **Service provider identifier(Entity ID)** (서비스 공급자 식별자(엔터티 ID)) 값을 복사하고, 이 값을 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **식별자** 텍스트 상자에 붙여넣습니다.

    1. **Service provider reply URL(Assertion consumer service URL)** (서비스 공급자 회신 URL(어설션 소비자 서비스 URL)) 값을 복사하고, 이 값을 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **회신 URL** 텍스트 상자에 붙여넣습니다.

    1. **Service provider login URL**(서비스 공급자 로그인 URL) 값을 복사하고 이 값을 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **로그온 URL** 텍스트 상자에 붙여넣습니다.

    1. Azure Portal에서 다운로드한 **페더레이션 메타데이터 XML** 을 메모장으로 열고 **파일 선택** 옵션을 클릭하여 XML 파일을 업로드합니다.

    1. **Assertion mapping**(어설션 매핑) 섹션에서 요구 사항에 따라 필요한 값을 입력합니다.

    1. **Save SAML configuration**(SAML 구성 저장)을 클릭합니다.

### <a name="create-amazon-managed-grafana-test-user"></a>Amazon Managed Grafana 테스트 사용자 만들기

이 섹션에서는 Amazon Managed Grafana에서 Britta Simon이라는 사용자를 만듭니다. Amazon Managed Grafana는 Just-In-Time 사용자 프로비저닝을 지원하며, 기본적으로 사용하도록 설정되어 있습니다. 이 섹션에 작업 항목이 없습니다. Amazon Managed Grafana에 사용자가 없으면 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Amazon Managed Grafana 로그온 URL로 리디렉션됩니다. 

* Amazon Managed Grafana 로그온 URL로 직접 이동하여 거기에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Amazon Managed Grafana 타일을 클릭하면 Amazon Managed Grafana 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Amazon Managed Grafana가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
