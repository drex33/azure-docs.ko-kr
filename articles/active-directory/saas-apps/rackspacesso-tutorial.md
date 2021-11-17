---
title: '자습서: Rackspace SSO와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Rackspace SSO 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2021
ms.author: jeedes
ms.openlocfilehash: 5fffea39509166bcf600537372f0f37f1534308c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306936"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>자습서: Rackspace SSO와 Azure Active Directory 통합

이 자습서에서는 Rackspace SSO를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다. Azure AD와 Rackspace SSO를 통합하면 다음을 수행할 수 있습니다.

* Rackspace SSO에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Rackspace SSO에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

Rackspace SSO와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Rackspace SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Rackspace SSO는 **SP** 에서 시작된 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-rackspace-sso-from-the-gallery"></a>갤러리에서 Rackspace SSO 추가

Rackspace SSO가 Azure AD로 통합되도록 구성하려면 Rackspace SSO를 갤러리에서 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Rackspace SSO** 를 입력합니다.
1. 결과 패널에서 **Rackspace SSO** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-rackspace-sso"></a>Rackspace SSO에 대한 Azure AD SSO 구성 및 테스트

이 섹션에서는 **Britta Simon** 이라는 테스트 사용자를 기반으로 Rackspace SSO에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Rackspace에서 Single Sign-On을 사용하는 경우 Rackspace 사용자는 Rackspace 포털에 처음 로그인할 때 자동으로 만들어집니다. 

Rackspace SSO에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 단계를 수행해야 합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
2. **[Rackspace SSO Single Sign-On 구성](#configure-rackspace-sso-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Rackspace 제어판에서 특성 매핑 설정](#set-up-attribute-mapping-in-the-rackspace-control-panel)** - Azure AD 사용자에게 Rackspace 역할을 할당합니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Rackspace SSO** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서, [URL](https://login.rackspace.com/federate/sp.xml)에서 다운로드할 수 있는 **서비스 공급자 메타데이터 파일** 을 업로드하고 다음 단계를 수행합니다.

    a. **메타데이터 파일 업로드** 를 클릭합니다.

    ![스크린샷은 메타데이터 파일 업로드 링크가 있는 기본 SAML 구성을 보여줍니다.](common/upload-metadata.png)

    b. **폴더 로고** 를 클릭하여 메타데이터 파일을 선택하고 **업로드** 를 클릭합니다.

    ![스크린샷은 파일을 선택하고 업로드할 수 있는 대화 상자를 보여줍니다.](common/browse-upload-metadata.png)

    다. 메타데이터 파일이 성공적으로 업로드되면 필요한 URL이 자동으로 채워집니다.

    d. **로그온 URL** 텍스트 상자에 `https://login.rackspace.com/federate/` URL을 입력합니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

이 파일은 Rackspace에 업로드되어 필요한 ID 페더레이션 구성 설정을 채웁니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Rackspace SSO에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Rackspace SSO** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-rackspace-sso-single-sign-on"></a>Rackspace SSO Single Sign-On 구성

**Rackspace SSO** 쪽에서 Single Sign-On을 구성하려면 다음을 수행합니다.

1. [제어판에 ID 공급자 추가](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)에서 설명서를 참조하세요.
1. 이 설명서가 다음 작업을 수행하는 단계를 안내합니다.
    1. 새 ID 공급자 만들기
    1. 사용자가 로그인할 때 귀사를 식별하기 위해 사용하는 이메일 도메인을 지정합니다.
    1. Azure 제어판에서 이전에 다운로드한 **페더레이션 메타데이터 XML** 을 업로드합니다.

이렇게 하면 Azure 및 Rackspace가 연결하는 데 필요한 기본 SSO 설정이 올바르게 구성됩니다.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Rackspace 제어판에서 특성 매핑 설정

Rackspace는 **특성 매핑 정책** 을 사용하여 Single Sign-On 사용자에게 Rackspace 역할 및 그룹을 할당합니다. **특성 매핑 정책** 은 Azure AD SAML 클레임을 Rackspace에 필요한 사용자 구성으로 변환합니다. 추가 설명서는 Rackspace [특성 매핑 기본 사항 설명서](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)에서 확인할 수 있습니다. 몇 가지 고려 사항은 다음과 같습니다.

* Azure AD 그룹을 사용하여 다양한 수준의 Rackspace 액세스 권한을 할당하려면 Azure **Rackspace SSO** Single Sign-On 설정에서 그룹 클레임을 사용하도록 설정해야 합니다. 그러면 **특성 매핑 정책** 을 사용하여 해당 그룹을 원하는 Rackspace 역할 및 그룹에 일치시킵니다.

    ![그룹 클레임 설정](common/sso-groups-claim.png)

* 기본적으로 Azure AD는 그룹의 이름 대신 SAML 그룹에 있는 Azure AD 그룹의 UID를 보냅니다. 그러나 Azure AD에 온-프레미스 Active Directory를 동기화하는 경우 그룹의 실제 이름을 보내는 옵션이 있습니다.

    ![그룹 클레임 이름 설정](common/sso-groups-claims-names.png)

다음 **특성 매핑 정책** 예제는 다음과 같은 작업을 보여줍니다.
1. Rackspace 사용자의 이름을 `user.name` SAML 클레임으로 설정합니다. 아무 클레임이나 사용할 수 있지만 사용자의 이메일 주소가 포함된 필드로 설정하는 것이 가장 일반적입니다.
1. Azure AD 그룹을 일치시키거나 그룹 이름 또는 그룹 UID에 의해 사용자에 대한 Rackspace 역할 `admin` 및 `billing:admin`을 설정합니다. `roles` 필드에 있는 `"{0}"`의 *대체* 를 사용하며, 이는 `remote` 규칙 식의 결과로 바뀝니다.
1. `"{D}"` *기본 대체* 를 사용하여 Rackspace가 SAML 교환 시 표준 및 잘 알려진 SAML 클레임을 조회하여 추가 SAML 필드를 검색하게 합니다.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> 정책 파일을 편집할 때 반드시 YAML 구문의 유효성을 검사하는 텍스트 편집기를 사용해야 합니다.

더 많은 예제는 Rackspace [특성 매핑 기본 사항 설명서](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/)를 참조하세요.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Rackspace SSO 로그온 URL로 리디렉션됩니다. 

* Rackspace SSO 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Rackspace SSO 타일을 클릭하면 Rackspace SSO 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

**Rackspace SSO** Single Sign-On 설정의 **유효성 검사** 단추를 사용할 수도 있습니다.

   ![SSO 유효성 검사 단추](common/sso-validate-sign-on.png)

## <a name="next-steps"></a>다음 단계

Rackspace SSO가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
