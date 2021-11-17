---
title: '자습서: SAP Cloud Platform과 Azure AD SSO 통합'
description: Azure Active Directory 및 SAP Cloud Platform 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: 4d7628b1431cebed1136aa2cb1b09d92e7fbb30b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320803"
---
# <a name="tutorial-azure-ad-sso-integration-with-sap-cloud-platform"></a>자습서: SAP Cloud Platform과 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP Cloud Platform을 통합하는 방법에 대해 알아봅니다. Azure AD와 SAP Cloud Platform을 통합하면 다음을 수행할 수 있습니다.

* SAP Cloud Platform에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 SAP Cloud Platform에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SAP Cloud Platform SSO(Single Sign-On)를 사용하도록 설정된 구독.

>[!IMPORTANT]
>사용자 고유의 애플리케이션을 배포하거나 SAP Cloud Platform 계정에 대 한 single sign 테스트하도록 애플리케이션을 구독해야 합니다. 이 자습서에서는 애플리케이션이 계정에 배포됩니다.
> 

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* SAP Cloud Platform에서 **SP** 시작 SSO를 지원합니다.

## <a name="add-sap-cloud-platform-from-the-gallery"></a>갤러리에서 SAP Cloud Platform 추가

SAP Cloud Platform의 Azure AD 통합을 구성하려면 갤러리의 SAP Cloud Platform을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **SAP Cloud Platform** 을 입력합니다.
1. 결과 패널에서 **SAP Cloud Platform** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform"></a>SAP Cloud Platform에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 SAP Cloud Platform에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SAP Cloud Platform의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SAP Cloud Platform에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
2. **[SAP Cloud Platform SSO 구성](#configure-sap-cloud-platform-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[SAP Cloud Platform 테스트 사용자 만들기](#create-sap-cloud-platform-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SAP Cloud Platform에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **SAP Cloud Platform** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **식별자** 텍스트 상자에 다음 패턴 중 하나를 사용하여 SAP Cloud Platform의 URL 형식을 입력합니다. 

    | **식별자** |
    |-----------|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    b. **회신 URL** 텍스트 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다.

    | **회신 URL** |
    |----------|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    c. **로그온 URL** 텍스트 상자에 **SAP Cloud Platform** 애플리케이션에 사용자가 로그인하기 위해 사용한 URL을 입력합니다. SAP Cloud Platform 애플리케이션에서 보호된 리소스의 계정 관련 URL입니다. URL은 다음 패턴을 기반으로 합니다. `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >사용자의 인증을 필요로 하는 SAP Cloud Platform 애플리케이션의 URL입니다.
    > 

    | **로그온 URL** |
    |------|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 해당 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. 로그온 URL 및 식별자를 가져오려면 [SAP Cloud Platform 클라이언트 지원 팀](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html)에 문의하세요. 이 자습서의 뒷부분에 설명되어 있는 트러스트 관리 섹션에서 회신 URL을 가져올 수 있습니다.
    > 
4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 SAP Cloud Platform에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **SAP Cloud Platform** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-sap-cloud-platform-sso"></a>SAP Cloud Platform SSO 구성

1. 다른 웹 브라우저 창의 `https://account.<landscape host>.ondemand.com/cockpit`(예: https://account.hanatrial.ondemand.com/cockpit) )에 있는 SAP Cloud Platform Cockpit에 로그인합니다.

2. **신뢰** 탭을 클릭합니다.
   
    ![신뢰](./media/sap-hana-cloud-platform-tutorial/account.png "신뢰")

3. 트러스트 관리 섹션의 **로컬 서비스 공급자** 아래에서 다음 단계를 수행합니다.

    !["로컬 서비스 공급자" 탭이 선택되고 모든 텍스트 상자가 강조 표시된 "신뢰 관리" 섹션을 보여주는 스크린샷.](./media/sap-hana-cloud-platform-tutorial/service.png "트러스트 관리")
   
    a. **편집** 을 클릭합니다.

    b. **구성 유형** 으로 **사용자 지정** 을 선택합니다.

    다. **로컬 공급자 이름** 으로 기본값을 그대로 사용합니다. 이 값을 복사하고 SAP Cloud Platform의 Azure AD 구성에서 **식별자** 필드로 붙여넣습니다.

    d. **서명 키** 및 **서명 인증서** 키 쌍을 생성하려면 **키 쌍 생성** 을 클릭합니다.

    e. **주 전파** 로 **사용 안 함** 을 선택합니다.

    f. **강제 인증** 으로 **사용 안 함** 을 선택합니다.

    g. **저장** 을 클릭합니다.

4. **로컬 서비스 공급자** 설정을 저장한 후 다음을 수행하여 회신 URL을 가져옵니다.
   
    ![메타데이터 가져오기](./media/sap-hana-cloud-platform-tutorial/certificate.png "메타데이터 가져오기")

    a. **메타데이터 가져오기** 를 클릭하여 SAP Cloud Platform 메타데이터 파일을 다운로드합니다.

    b. 다운로드한 SAP Cloud Platform 메타데이터 XML 파일을 연 다음 **ns3:AssertionConsumerService** 태그를 찾습니다.
 
    다. **위치** 특성의 값을 복사한 다음 SAP Cloud Platform의 Azure AD 구성에서 **회신 URL** 필드로 붙여넣습니다.

5. **신뢰할 수 있는 ID 공급자** 탭을 클릭한 다음 **신뢰할 수 있는 ID 공급자 추가** 를 클릭합니다.
   
    !["신뢰할 수 있는 ID 공급자" 탭이 선택된 "신뢰 관리" 페이지를 보여주는 스크린샷.](./media/sap-hana-cloud-platform-tutorial/add-service.png "트러스트 관리")
   
    >[!NOTE]
    >신뢰할 수 있는 ID 공급자 목록을 관리하려면, 로컬 서비스 공급자 섹션에서 사용자 할당 구성 형식을 선택해야 합니다. 기본 구성 유형으로 SAP ID 서비스에 대한 편집할 수 없는 암시적 트러스트가 있습니다. 없음의 경우 신뢰 설정이 필요 없습니다.
    > 
    > 

6. **일반** 탭을 클릭한 다음 **찾아보기** 를 클릭하여 다운로드한 메타데이터 파일을 업로드합니다.
    
    ![신뢰 관리](./media/sap-hana-cloud-platform-tutorial/general.png "트러스트 관리")
    
    >[!NOTE]
    >메타데이터 파일을 업로드하면 **Single Sign-On URL**, **단일 로그아웃 URL** 및 **서명 인증서** 의 값이 자동으로 채워집니다.
    > 
     
7. **특성** 탭을 클릭합니다.

8. **특성** 탭에서 다음 단계를 수행합니다.
    
    ![특성](./media/sap-hana-cloud-platform-tutorial/principal-attribute.png "특성") 

    a. **어설션 기반 특성 추가** 를 클릭한 다음 어설션 기반 특성을 추가합니다.
       
    | 어설션 특성 | 보안 주체 특성 |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |이메일 |
   
    >[!NOTE]
    >특성의 구성은 SAML 응답에서 예상되는 특성 및 코드의 이 특성에 액세스하는 이름(보안 주체 특성)과 같은 SCP의 애플리케이션이 개발된 방법에 따라 다릅니다.
    > 
    
    b. 스크린샷의 **기본 특성** 은 설명 목적입니다. 시나리오 작업을 작성할 필요가 없습니다.  
 
    다. 스크린샷에 표시되는 **보안 주체 특성** 의 이름 및 값은 애플리케이션 개발 방법에 따라 달라집니다. 애플리케이션에 다른 매핑이 필요할 수 있습니다.

### <a name="assertion-based-groups"></a>어설션 기반 그룹

선택적 단계로, Azure Active Directory ID 공급자에 대한 어설션 기반 그룹을 구성할 수 있습니다.

SAP Cloud Platform에서 그룹을 사용하면 한 명 이상의 사용자를 SAP Cloud Platform 애플리케이션의 하나 이상의 역할에 동적으로 할당할 수 있으며 SAML 2.0 어셜션의 특성 값으로 결정됩니다. 

예를 들어, 어설션이 특성 "*contract=temporary*"를 포함하는 경우 적용되는 모든 사용자가 그룹 "*임시*"에 추가됩니다. 그룹 "*임시*"는 SAP Cloud Platform 계정에 배포되는 하나 이상의 애플리케이션에서 하나 이상의 역할을 포함할 수 있습니다.
 
SAP Cloud Platform 계정에서 애플리케이션의 하나 이상의 역할에 여러 사용자를 동시에 할당하려는 경우 어셜션 기반 그룹을 사용합니다. 특정 역할에 단일 또는 적은 수의 사용자를 할당하려는 경우, SAP Cloud Platform cockpit의 “**권한 부여**” 탭에 직접 할당하는 것이 좋습니다.

### <a name="create-sap-cloud-platform-test-user"></a>SAP Cloud Platform 테스트 사용자 만들기

Azure AD 사용자가 SAP Cloud Platform으로 로그를 사용하려면, SAP Cloud Platform에서 역할을 할당해야 합니다.

**사용자에게 역할을 할당하려면 다음 단계를 수행합니다.**

1. **SAP Cloud Platform** cockpit에 로그인합니다.

2. 다음을 수행합니다.
   
    ![권한 부여](./media/sap-hana-cloud-platform-tutorial/roles.png "권한 부여")
   
    a. **권한 부여** 를 클릭합니다.

    b. **사용자** 탭을 클릭합니다.

    다. **사용자** 텍스트 상자에 사용자의 이메일 주소를 입력합니다.

    d. **할당** 을 클릭하여 사용자에 역할을 할당합니다.

    e. **저장** 을 클릭합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 SAP Cloud Platform 로그온 URL로 리디렉션됩니다. 

* SAP Cloud Platform 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 SAP Cloud Platform 타일을 클릭하면 SSO를 설정한 SAP Cloud Platform 애플리케이션에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

SAP Cloud Platform이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
