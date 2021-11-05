---
title: '자습서: Learning Pool LMS와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Learning Pool LMS 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: jeedes
ms.openlocfilehash: eb7d97b5e796ae9433b695c15ef809f7674f53f3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032140"
---
# <a name="tutorial-azure-active-directory-integration-with-learning-pool-lms"></a>자습서: Learning Pool LMS와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Learning Pool LMS를 통합하는 방법에 대해 알아봅니다. Azure AD와 Learning Pool LMS를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Learning Pool LMS에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Learning Pool LMS에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Single Sign-On을 통한 Learning Pool LMS에 대한 활성 구독

> [!NOTE]
> Single Sign-On 프로젝트를 시작하면 Learning Pool LMS 배달 팀의 멤버가 이 프로세스를 안내합니다. Learning Pool LMS 배달 팀의 멤버와 연락하지 않는 경우 Learning Pool LMS 계정 관리자에게 문의하세요.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Learning Pool LMS에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-learning-pool-lms-from-the-gallery"></a>갤러리에서 Learning Pool LMS 추가

Learning Pool LMS가 Azure AD에 통합되도록 구성하려면 갤러리의 Learning Pool LMS를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Learning Pool LMS** 를 입력합니다.
1. 결과 패널에서 **Learning Pool LMS** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-learning-pool-lms"></a>Learning Pool LMS에 대한 Azure AD SSO 구성 및 테스트

기존 Azure 사용자를 사용하여 Learning Pool LMS에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Learning Pool LMS의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Learning Pool LMS에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 사용자 할당](#assign-an-azure-ad-user)** - 해당 사용자가 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Learning Pool LMS SSO 구성](#configure-learning-pool-lms-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Learning Pool LMS** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

1. **서비스 공급자 메타데이터 파일** 이 있으면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **메타데이터 파일 업로드** 를 클릭합니다.

    ![메타데이터 파일 업로드](common/upload-metadata.png)

    b. **폴더 로고** 를 클릭하여 메타데이터 파일을 선택하고 **업로드** 를 클릭합니다.

    ![메타데이터 파일 선택](common/browse-upload-metadata.png)

    다. 메타데이터 파일이 성공적으로 업로드되면 **식별자** 값이 [기본 SAML 구성] 섹션에 자동으로 채워집니다.

    **로그온 URL** 텍스트 상자에 `https://parliament.preview.Learningpool.com/auth/shibboleth/index.php` URL을 입력합니다.

    > [!Note]
    > **식별자** 값이 자동으로 입력되지 않으면 요구 사항에 따라 수동으로 값을 입력합니다.

5. Azure 사용자를 Learning Pool LMS의 사용자와 일치시키는 데 사용되는 하나 이상의 특성을 보내야 합니다. 일반적으로 기본 특성만으로도 충분하지만, 경우에 따라 일부 사용자 지정 특성을 보내야 할 수도 있습니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집** 아이콘을 클릭하여 [사용자 특성] 대화 상자를 열고, 필요한 경우 특성을 더 추가합니다.

    ![스크린샷은 편집 아이콘이 선택된 사용자 특성을 보여줍니다.](common/edit-attribute.png)

6. 위의 이미지와 같이 SAML 토큰 특성을 구성하기 위해 **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 **편집 아이콘** 을 사용하여 클레임을 편집하거나 **새 클레임 추가** 를 사용하여 클레임을 추가하고, 다음 단계를 수행합니다. 

    a. **새 클레임 추가** 를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![스크린샷은 새 클레임을 추가하는 옵션이 있는 사용자 클레임을 보여줍니다.](common/new-save-attribute.png)

    ![스크린샷은 설명된 값을 입력할 수 있는 사용자 클레임 관리 대화 상자를 보여줍니다.](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스** 를 비워 둡니다.

    d. 원본을 **특성** 으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인** 을 클릭합니다.

    g. **저장** 을 클릭합니다.

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **앱 페더레이션 메타데이터 URL** 옆에 있는 복사 단추를 클릭하고, 해당 URL을 Learning Pool 배달 팀에 다시 전달합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

### <a name="assign-an-azure-ad-user"></a>Azure AD 사용자 할당

이 섹션에서는 기존 Azure AD 사용자에게 Learning Pool LMS에 대한 액세스 권한을 부여하여 해당 사용자가 Azure Single Sign-On을 사용할 수 있도록 합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Learning Pool LMS** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 [사용자] 목록에서 적합한 사용자를 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-learning-pool-lms-sso"></a>Learning Pool LMS SSO 구성

Learning Pool 배달 팀에서 **앱 페더레이션 메타데이터 URL** 을 사용하여 SAML2 연결을 허용하도록 LMS를 구성합니다. 연결이 올바르게 구성되었는지 확인하기 위해 몇 가지 테스트 단계를 수행하라는 메시지가 표시되고, Learning Pool 배달 팀에서 이 프로세스를 안내합니다.

### <a name="test-sso"></a>SSO 테스트

Learning Pool 배달 팀에서 테스트 프로세스를 안내합니다.

## <a name="next-steps"></a>다음 단계

Learning Pool LMS가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
