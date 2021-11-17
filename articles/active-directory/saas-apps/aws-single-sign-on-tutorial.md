---
title: '자습서: AWS Single Sign-on과 Azure AD SSO 통합'
description: Azure Active Directory 및 AWS Single Sign-On 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/26/2021
ms.author: jeedes
ms.openlocfilehash: 4e7d0f8edb34f7c6a91eeb8bac6f04b97440f764
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440843"
---
# <a name="tutorial-azure-ad-sso-integration-with-aws-single-sign-on"></a>자습서: AWS Single Sign-on과 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 AWS Single Sign-On을 통합하는 방법에 대해 알아봅니다. AWS Single Sign-On을 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 AWS Single Sign-On에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 AWS Single Sign-On에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* AWS Single Sign-on SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* AWS Single Sign-On에서 **SP 및 IDP** 시작 SSO를 지원합니다.

* AWS Single Sign-On에서 [**자동화된 사용자 프로비저닝**](./aws-single-sign-on-provisioning-tutorial.md)을 지원합니다.

## <a name="adding-aws-single-sign-on-from-the-gallery"></a>갤러리에서 AWS Single Sign-On 추가

AWS Single Sign-On이 Azure AD에 통합되도록 구성하려면 갤러리의 AWS Single Sign-On을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **AWS Single Sign-On** 을 입력합니다.
1. 결과 패널에서 **AWS Single Sign-On** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-sso-for-aws-single-sign-on"></a>AWS Single Sign-On에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 AWS Single Sign-On에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 AWS Single Sign-On의 관련 사용자 간에 연결 관계를 설정해야 합니다.

AWS Single Sign-On에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[AWS Single Sign-On SSO 구성](#configure-aws-single-sign-on-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[AWS Single Sign-On 테스트 사용자 만들기](#create-aws-single-sign-on-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 AWS Single Sign-On에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **AWS Single Sign-On** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **서비스 공급자 메타데이터 파일** 이 있는 경우 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **메타데이터 파일 업로드** 를 클릭합니다.

    b. **폴더 로고** 를 클릭하여 **[AWS Single Sign-On SSO 구성](#configure-aws-single-sign-on-sso)** 섹션에서 다운로드하도록 설명된 메타데이터 파일을 선택하고 **추가** 를 클릭합니다.

    ![image2](common/browse-upload-metadata.png)

    다. 메타데이터 파일이 성공적으로 업로드되면 **식별자** 및 **회신 URL** 값이 기본 SAML 구성 섹션에 자동으로 채워집니다.

    ![image3](common/idp-intiated.png)

    > [!Note]
    > **식별자** 및 **회신 URL** 값이 자동으로 채워지지 않으면 요구 사항에 따라 값을 수동으로 입력합니다.

1. **서비스 공급자 메타데이터 파일** 이 없는 경우 **기본 SAML 구성** 섹션에서 다음 단계를 수행하고, **IDP** 시작 모드에서 애플리케이션을 구성하려면 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://<REGION>.signin.aws.amazon.com/platform/saml/<ID>` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<REGION>.signin.aws.amazon.com/platform/saml/acs/<ID>` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://portal.sso.<REGION>.amazonaws.com/saml/assertion/<ID>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [AWS Single Sign-On 클라이언트 지원 팀](mailto:aws-sso-partners@amazon.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. AWS Single Sign-On 애플리케이션에는 사용자 지정 특성 매핑을 SAML 토큰 특성 구성에 추가해야 하는 특정 형식의 SAML 어설션이 필요합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/edit-attribute.png)


    > [!NOTE]
    > AWS SSO에서 ABAC를 사용하도록 설정하면 추가 특성이 세션 태그로 AWS 계정에 직접 전달될 수 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾고, **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **AWS Single Sign-On 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 B.Simon에게 AWS Single Sign-On에 대한 액세스 권한을 부여하여 해당 사용자가 Azure Single Sign-On을 사용하도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **AWS Single Sign-On** 을 클릭합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-aws-single-sign-on-sso"></a>AWS Single Sign-On SSO 구성

1. AWS Single Sign-On 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **AWS Single Sign-On 설정** 을 클릭하면 AWS Single Sign-On 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 AWS Single Sign-On에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3~10단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. AWS Single Sign-On을 수동으로 설정하려면 다른 웹 브라우저 창에서 관리자 권한으로 AWS Single Sign-On 회사 사이트에 로그인합니다.

1. **서비스 -> 보안, ID 및 규정 준수 -> AWS Single Sign-On** 으로 이동합니다.
2. 왼쪽 탐색 창에서 **Settings(설정)** 를 선택합니다.
3. **설정** 페이지에서 **ID 원본** 을 찾아 **변경** 을 클릭합니다.

    ![ID 원본 변경 서비스의 스크린샷](./media/aws-single-sign-on-tutorial/settings.png)

4. ID 원본 변경에서 **외부 ID 공급자** 를 선택합니다.

    
    ![외부 ID 공급자 섹션 선택 스크린샷](./media/aws-single-sign-on-tutorial/external-identity-provider.png)


1. **외부 ID 공급자 구성** 섹션에서 아래 단계를 수행합니다.

    ![메타데이터 다운로드 및 업로드 섹션의 스크린샷](./media/aws-single-sign-on-tutorial/upload-metadata.png)

    a. **서비스 공급자 메타데이터** 섹션에서 **AWS SSO SAML 메타데이터** 를 찾고 **메타데이터 파일 다운로드** 을 선택하여 메타데이터 파일을 다운로드하고, 컴퓨터에 저장하고, 이 메타데이터 파일을 사용하여 Azure Portal에 업로드합니다.

    b. **AWS SSO 로그인 URL** 값을 복사하고 이 값을 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **로그온 URL** 텍스트 상자에 붙여넣습니다.

    다. **Identity provider metadata(ID 공급자 메타데이터)** 섹션에서 **Browse(찾아보기)** 를 선택하여 Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다.

    d. **Next: Review(다음: 검토)** 를 선택합니다.

8. 텍스트 상자에 **ACCEPT** 를 입력하여 ID 원본을 변경합니다.

    ![구성 확인을 위한 스크린샷](./media/aws-single-sign-on-tutorial/accept.png)

9. **ID 원본 변경** 을 클릭합니다.

### <a name="create-aws-single-sign-on-test-user"></a>AWS Single Sign-On 테스트 사용자 만들기

1. **AWS SSO 콘솔** 을 엽니다.

2. 왼쪽 탐색 창에서 **Users(사용자)** 를 선택합니다.

3. [Users(사용자)] 페이지에서 **Add user(사용자 추가)** 를 선택합니다.

4. [Add user] 페이지에서 다음 단계를 수행합니다.

    a. **Username(사용자 이름)** 필드에서 B.Simon을 입력합니다.

    b. **Email address(이메일 주소)** 필드에서 `username@companydomain.extension`을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.

    다. **Confirm email address(이메일 주소 확인)** 필드에서 이전 단계의 이메일 주소를 다시 입력합니다.

    d. [First name(이름)] 필드에서 `Jane`을 입력합니다.

    e. [Last name(성)] 필드에서 `Doe`를 입력합니다.

    f. [Display name(표시 이름)] 필드에서 `Jane Doe`를 입력합니다.

    g. **Next: Groups(다음: 그룹)** 를 선택합니다.

    > [!NOTE]
    > AWS SSO에 입력한 사용자 이름이 해당 사용자의 Azure AD 로그인 이름과 일치하는지 확인합니다. 이렇게 하면 인증 문제를 방지하는 데 도움이 됩니다.

5. **사용자 추가** 를 선택합니다.
6. 다음으로 사용자를 AWS 계정에 할당합니다. 이렇게 하려면 AWS SSO 콘솔의 왼쪽 탐색 창에서 **AWS accounts(AWS 계정)** 를 선택합니다.
7. [AWS Accounts(AWS 계정)] 페이지에서 [AWS organization(AWS 조직)] 탭을 선택하고, 사용자에게 할당하려는 AWS 계정 옆의 확인란을 선택합니다. 그런 다음, **Assign users(사용자 할당)** 를 선택합니다.
8. [Assign Users(사용자 할당)] 페이지에서 B.Simon 사용자 옆의 확인란을 선택합니다. 그런 다음,**Next: Permission sets(다음: 권한 집합)** 를 선택합니다.
9. [select permission sets(권한 집합 선택)] 섹션 아래에서 B.Simon 사용자에게 할당하려는 권한 집합 옆의 확인란을 선택합니다. 기존 권한 집합이 없는 경우 **Create new permission set(새 권한 집합 만들기)** 를 선택합니다.

    > [!NOTE]
    > 권한 집합은 사용자 및 그룹의 AWS 계정에 대한 액세스 수준을 정의합니다. 권한 집합에 대한 자세한 내용은 AWS SSO **권한 집합** 페이지를 참조하세요.
10. **마침** 을 선택합니다.

> [!NOTE]
> AWS Single Sign-On은 자동 사용자 프로비저닝도 지원합니다. 자동 사용자 프로비저닝을 구성하는 방법에 대한 자세한 내용은 [여기](./aws-single-sign-on-provisioning-tutorial.md)서 확인할 수 있습니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 AWS Single Sign-On 로그온 URL로 리디렉션됩니다.  

* AWS Single Sign-On 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 AWS Single Sign-On에 자동으로 로그인됩니다 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 AWS Single Sign-On 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우 SSO를 설정한 AWS Single Sign-On에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.


## <a name="next-steps"></a>다음 단계

AWS Single Sign-On이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
