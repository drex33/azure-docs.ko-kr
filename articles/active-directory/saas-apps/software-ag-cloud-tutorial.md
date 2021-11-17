---
title: '자습서: Software AG Cloud와 Azure AD SSO 통합'
description: Azure Active Directory와 Software AG Cloud 간에 Single Sign-On를 구성하는 방법에 대해 알아봅니다.
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2021
ms.author: jeedes
ms.openlocfilehash: 163e2aae9355d24873cd9864c84cefe71a57ab18
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459250"
---
# <a name="tutorial-azure-ad-sso-integration-with-software-ag-cloud"></a>자습서: Software AG Cloud와 Azure AD SSO 통합

이 자습서에서는 Software AG Cloud와 Azure AD(Azure Active Directory)를 통합하는 방법에 대해 알아봅니다. Azure AD와 Software AG Cloud를 통합하면 다음을 수행할 수 있습니다.

* Software AG Cloud에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Software AG Cloud에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Software AG Cloud SSO(Single Sign-On)가 설정된 구독

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Software AG Cloud에서 **SP** 시작 SSO를 지원합니다.
* Software AG Cloud에서 **Just In Time** 사용자 프로비저닝을 지원합니다.

## <a name="add-software-ag-cloud-from-the-gallery"></a>갤러리에서 Software AG Cloud 추가

Software AG Cloud가 Azure AD에 통합되도록 구성하려면 갤러리의 Software AG Cloud를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Software AG Cloud** 를 입력합니다.
1. 결과 패널에서 **Software AG Cloud** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-software-ag-cloud"></a>Software AG Cloud용 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Software AG Cloud에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Software AG Cloud의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Software AG Cloud에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Software AG Cloud SSO 구성](#configure-software-ag-cloud-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Software AG Cloud 테스트 사용자 만들기](#create-software-ag-cloud-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Software AG Cloud에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Software AG Cloud** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    1. **식별자(엔터티 ID)** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.

        `https://<SUBDOMAIN>.softwareag.cloud/auth/realms/TENANT-NAME`
    
    1. **로그온 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다.

        `https://<SUBDOMAIN>.softwareag.cloud/auth/realms/TENANT-NAME/broker/IDENTITY-PROVIDER-NAME/endpoint`    

        > [!NOTE]
        > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Software AG Cloud 클라이언트 지원 팀](mailto:support@softwareag.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Software AG Cloud 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B. Simon에게 Software AG Cloud에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Software AG Cloud** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-software-ag-cloud-sso"></a>Software AG Cloud SSO 구성

1. 다른 웹 브라우저 창에서 관리자 권한으로 Software AG Cloud 웹 사이트에 로그인합니다.

1.  **관리** 를 클릭합니다.

    ![Software AG Cloud 관리 구성](./media/software-ag-cloud-tutorial/admin.png)

1. **Single Sign-On > ID 공급자 추가** 로 이동합니다.

    ![Software AG Cloud ID 공급자 구성](./media/software-ag-cloud-tutorial/add-identity-provider.png)

1. 다음 페이지에서 다음 단계를 수행합니다.

    ![Software AG Cloud 구성 다음 단계](./media/software-ag-cloud-tutorial/configuration.png)

    a. **ID 공급자 표시 이름** 텍스트 상자에 이름(예: `azure ad`)을 입력합니다.

    b. **Software AG Cloud 리디렉션 URI에서 사용할 ID 공급자 고유 식별자** 텍스트 상자에 ID 공급자의 고유 이름을 입력합니다. **Software AG Cloud 리디렉션 URI** 필드가 새로 고쳐지고 URI로 채워집니다. 이 URI를 복사하여 정의된 패턴에 따라 Azure Portal에서 **엔터티 ID** 및 기타 정보를 구성하는 데 사용합니다.

    다. **ID 공급자 구성** 에서 **페더레이션 메타데이터 XML** 파일을 가져오고 **다음** 을 클릭합니다.

    d. **구성** 페이지로 이동하여 필요한 따라 필드를 완료합니다.

### <a name="create-software-ag-cloud-test-user"></a>Software AG Cloud 사용자 테스트 만들기

이 섹션에서는 Software AG Cloud에서 Britta Simon이라는 사용자를 만듭니다. Software AG Cloud는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Software AG Cloud에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

Microsoft Azure가 Software AG Cloud의 공급자로 구성되어 있다고 가정하고, `www.softwareag.cloud`로 이동하여 로그인 단추를 클릭하고 환경 이름을 입력합니다. 다음 화면에서 "\<IDP NAME\>으로 로그인" 링크를 클릭하고 자격 증명을 입력합니다. 인증되면 로그인되고 Software AG Cloud 홈 페이지로 이동합니다.

## <a name="next-steps"></a>다음 단계

Software AG Cloud가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).