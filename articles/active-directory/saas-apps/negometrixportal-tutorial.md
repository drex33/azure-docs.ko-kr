---
title: '자습서: NegometrixPortal SSO(Single Sign On)와 Azure AD SSO 통합'
description: Azure Active Directory와 NegometrixPortal SSO(Single Sign On) 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2021
ms.author: jeedes
ms.openlocfilehash: 3fdc55289c33482db7384eed51ae3a2e75f7ad99
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051556"
---
# <a name="tutorial-azure-ad-sso-integration-with-negometrixportal-single-sign-on-sso"></a>자습서: NegometrixPortal SSO(Single Sign On)와 Azure AD SSO 통합

이 자습서에서는 NegometrixPortal SSO(Single Sign On)와 Azure AD(Azure Active Directory)를 통합하는 방법을 알아봅니다. NegometrixPortal SSO(Single Sign On)와 Azure AD를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 NegometrixPortal SSO (Single Sign-on)에 액세스할 수 있는 사용자를 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 NegometrixPortal SSO(Single Sign-On)에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* NegometrixPortal SSO(Single Sign-On)가 설정된 구독입니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* NegometrixPortal SSO(Single Sign On)에서 **SP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-negometrixportal-single-sign-on-sso-from-the-gallery"></a>갤러리에서 NegometrixPortal SSO(Single Sign On) 추가

Azure AD로의 NegometrixPortal SSO(Single Sign-On) 통합을 구성하려면 갤러리에서 NegometrixPortal SSO(Single Sign-On)를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **NegometrixPortal SSO(Single Sign-On)** 를 입력합니다.
1. 결과 패널에서 **NegometrixPortal SSO(Single Sign On)** 를 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-negometrixportal-single-sign-on-sso"></a>NegometrixPortal SSO(Single Sign On)에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 NegometrixPortal SSO(Single Sign-on)로 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 NegometrixPortal SSO(Single Sign On)의 관련 사용자 간에 연결 관계를 설정해야 합니다.

NegometrixPortal SSO(Single Sign-on)에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[NegometrixPortal SSO(Single Sign On) 구성](#configure-negometrixportal-single-sign-on-sso-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[NegometrixPortal SSO(Single Sign On) 테스트 사용자 만들기](#create-negometrixportal-single-sign-on-sso-test-user)** - 사용자의 Azure AD 표현과 연결된 B. Simon에 해당하는 사용자를 NegometrixPortal SSO(Single Sign On)에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **NegometrixPortal SSO(Single Sign On)** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://portal.negometrix.com/sso/<CUSTOMURL>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 값을 가져오려면 [NegometrixPortal SSO(Single Sign On) 클라이언트 지원 팀](mailto:sander.hoek@negometrix.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. NegometrixPortal SSO(Single Sign On) 애플리케이션에는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 하는 특정 형식의 SAML 어설션이 필요합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 NegometrixPortal SSO(Single Sign On) 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | Name | 원본 특성|
    | ---------------|  --------- |
    | upn | user.userprincipalname |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL** 을 복사한 후 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 NegometrixPortal SSO(Single Sign On)에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **NegometrixPortal SSO(Single Sign On)** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-negometrixportal-single-sign-on-sso-sso"></a>NegometrixPortal SSO(Single Sign On) 구성

**NegometrixPortal SSO(Single Sign On)** 쪽에서 Single Sign On을 구성하려면 **앱 페더레이션 메타데이터 URL** 을 [NegometrixPortal SSO(Single Sign On) 지원 팀](mailto:sander.hoek@negometrix.com)에 보내야 합니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

### <a name="create-negometrixportal-single-sign-on-sso-test-user"></a>NegometrixPortal SSO(Single Sign On) 테스트 사용자 만들기

이 섹션에서는 NegometrixPortal SSO(Single Sign On)에서 B.Simon이라는 사용자를 만듭니다. [NegometrixPortal SSO(Single Sign On) 지원 팀](mailto:sander.hoek@negometrix.com)과 협력하여 NegometrixPortal SSO(Single Sign On) 플랫폼에 사용자를 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 NegometrixPortal SSO(Single Sign On) 로그온 URL로 리디렉션됩니다. 

* NegometrixPortal SSO(Single Sign On) 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 NegometrixPortal SSO(Single Sign-On) 타일을 클릭하면 NegometrixPortal SSO(Single Sign-On) 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

NegometrixPortal SSO(Single Sign On)가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
