---
title: 페더레이션 인증서 관리
description: 페더레이션 인증서에 대한 만료 날짜를 사용자 지정하는 방법 및 곧 만료되는 인증서를 갱신하는 방법에 대해 알아봅니다.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: davidmu
ms.reviewer: saumadan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 886defd9362d1001a32d3076623ffd87ce718fe3
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129617590"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리

이 문서에서는 Azure AD(Azure Active Directory)에서 SaaS(Software as a Service) 애플리케이션에 페더레이션된 SSO(Single Sign-On)를 설정하기 위해 만드는 인증서와 관련된 일반적인 질문과 정보를 다룹니다. Azure AD 앱 갤러리에서 또는 비갤러리 애플리케이션 템플릿을 사용하여 애플리케이션을 추가합니다. 페더레이션된 SSO 옵션을 사용하여 애플리케이션을 구성합니다.

이 문서는 [SAML(Security Assertion Markup Language)](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) 페더레이션을 통해 Azure AD SSO를 사용하도록 구성된 앱에만 관련됩니다.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>갤러리 및 비갤러리 애플리케이션에 대해 자동 생성된 인증서

갤러리에서 새 애플리케이션을 추가하고 SAML 기반 로그온을 구성하면(애플리케이션 개요 페이지에서 **Single Sign-On** > **SAML** 선택) Azure AD에서 해당 애플리케이션에 대해 3년 동안 유효한 인증서를 생성합니다. 활성 인증서를 보안 인증서( **.cer**) 파일로 다운로드하려면 해당 페이지(**SAML 기반 로그온**)로 돌아가 **SAML 서명 인증서** 제목의 다운로드 링크를 선택합니다. 원시(이진) 인증서 또는 Base64(base 64 인코딩 텍스트) 인증서 중에서 선택할 수 있습니다. 갤러리 애플리케이션의 경우 이 섹션에는 애플리케이션의 요구 사항에 따라 페더레이션 메타데이터 XML( **.xml** 파일)로 인증서를 다운로드하는 링크가 표시될 수도 있습니다.

![SAML 활성 서명 인증서 다운로드 옵션](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

**SAML 서명 인증서** 제목이 표시되는 **편집** 아이콘(연필)을 선택하고 **SAML 서명 인증서** 페이지를 표시하여 활성 또는 비활성 인증서를 다운로드할 수도 있습니다. 다운로드하려는 인증서 옆에 있는 줄임표( **...** )를 선택하고 원하는 인증서 형식을 선택합니다. 인증서를 PEM(Privacy-Enhanced Mail) 형식으로 다운로드하는 추가 옵션이 제공됩니다. 이 형식은 Base64와 동일하지만 Windows에서 인증서 형식으로 인식되지 않는 **.pem** 파일 이름 확장명을 사용합니다.

![SAML 서명 인증서 다운로드 옵션(활성 및 비활성)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>페더레이션 인증서의 만료 날짜 사용자 지정 및 새 인증서 롤오버

기본적으로 Azure는 SAML Single Sign-On 구성 중에 자동으로 만들어지는 인증서로 3년 후에 만료되도록 구성합니다. 인증서를 저장한 후에는 인증서의 날짜를 변경할 수 없으므로 다음을 수행해야 합니다.

1. 원하는 날짜로 새 인증서를 만듭니다.
1. 새 인증서를 저장합니다.
1. 올바른 형식으로 새 인증서를 다운로드합니다.
1. 애플리케이션에 새 인증서를 업로드합니다.
1. Azure Active Directory 포털에서 새 인증서를 활성으로 설정합니다.

다음 두 섹션은 이러한 단계를 수행하는 데 도움이 됩니다.

### <a name="create-a-new-certificate"></a>새 인증서 만들기

먼저 다른 만료 날짜를 사용하여 새 인증서를 만들고 저장합니다.

1. [Azure Active Directory 포털](https://aad.portal.azure.com/)에 로그인합니다. **Azure Active Directory 관리 센터** 페이지가 나타납니다.
1. 왼쪽 창에서 **엔터프라이즈 애플리케이션** 을 선택합니다. 계정의 엔터프라이즈 애플리케이션 목록이 표시됩니다.
1. 영향을 받는 애플리케이션을 선택합니다. 애플리케이션에 대한 개요 페이지가 표시됩니다.
1. 애플리케이션 개요 페이지의 왼쪽 창에서 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정 - 미리 보기** 페이지에서 **SAML 서명 인증서** 제목을 찾고 **편집** 아이콘(연필)을 선택합니다. **SAML 서명 인증서** 페이지가 표시됩니다. 여기에는 각 인증서의 상태(**활성** 또는 **비활성**), 만료 날짜 및 지문(해시 문자열)이 표시됩니다.
1. **새 인증서** 를 선택합니다. 인증서 목록 아래에 새 행이 나타납니다. 여기에서 만료 날짜의 기본값은 현재 날짜부터 정확히 3년 이후입니다. 변경 내용이 아직 저장되지 않았으므로 만료 날짜를 수정할 수 있습니다.
1. 새 인증서 행에서 만료 날짜 열 위로 마우스를 이동하고 **날짜 선택** 아이콘(달력)을 선택합니다. 새 행의 현재 만료 날짜를 표시하는 달력 컨트롤이 나타납니다.
1. 달력 컨트롤을 사용하여 새 날짜를 설정할 수 있습니다. 현재 날짜와 현재 날짜로부터 3년 기간 이내의 아무 날짜나 설정할 수 있습니다.
1. **저장** 을 선택합니다. 이제 새 인증서에 **비활성** 상태, 선택한 만료 날짜 및 지문이 표시됩니다. **참고**- 이미 만료된 기존 인증서가 있고 새 인증서를 만들면 아직 활성화하지 않았더라도 새 인증서가 서명 토큰에 대해 고려됩니다.
1. **X** 를 선택하여 **SAML로 Single Sign-On 설정 - 미리 보기** 페이지로 돌아갑니다.

### <a name="upload-and-activate-a-certificate"></a>인증서 업로드 및 활성화

다음으로, 새 인증서를 올바른 형식으로 다운로드하고, 애플리케이션에 업로드한 후 Azure Active Directory에서 활성으로 만듭니다.

1. 다음 중 하나를 수행하여 애플리케이션의 추가 SAML 로그온 구성 지침을 확인합니다.

   - 별도의 브라우저 창이나 탭에서 볼 수 있도록 **구성 가이드** 링크 선택 또는
   - **설정** 제목으로 이동하여 사이드바에 표시할 **단계별 지침 보기** 를 선택합니다.

1. 지침에서 인증서 업로드에 필요한 인코딩 형식을 확인합니다.
1. 앞의 [갤러리 및 비갤러리 애플리케이션에 대해 자동 생성된 인증서](#auto-generated-certificate-for-gallery-and-non-gallery-applications) 섹션에 제공되는 지침을 따르세요. 이 단계에서는 애플리케이션에서 업로드하는 데 필요한 인코딩 형식으로 인증서를 다운로드합니다.
1. 새 인증서로 롤오버하려면 **SAML 서명 인증서** 페이지로 돌아가 새로 저장된 인증서 행에서 줄임표( **...** )를 선택하고 **인증서 활성화** 를 선택합니다. 새 인증서의 상태가 **활성** 으로 변경되고 이전에 활성화된 인증서가 **비활성** 상태로 변경됩니다.
1. 앞에서 표시한 애플리케이션의 SAML 로그온 구성 지침에 따라 SAML 서명 인증서를 올바른 인코딩 형식으로 업로드할 수 있습니다.

애플리케이션에 인증서 만료에 대한 유효성 검사가 없고 Azure Active Directory와 애플리케이션 모두에서 인증서가 일치하는 경우 만료된 인증서가 있어도 앱에 계속 액세스할 수 있습니다. 애플리케이션에서 인증서의 만료 날짜를 확인할 수 있는지 확인합니다.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>인증서 만료에 대한 메일 알림 주소 추가

Azure AD에서는 SAML 인증서가 만료되기 60일, 30일 및 7일 전에 이메일 알림을 보냅니다. 알림을 받을 이메일 주소를 둘 이상 추가할 수 있습니다. 알림을 보낼 메일 주소를 지정하려면 다음을 수행합니다.

1. **SAML 서명 인증서** 페이지에서 **알림 메일 주소** 제목으로 이동합니다. 기본적으로 이 제목은 애플리케이션을 추가한 관리자의 메일 주소만 사용합니다.
1. 최종 메일 주소 아래에 인증서의 만료 알림을 받을 메일 주소를 입력한 후 Enter 키를 누릅니다.
1. 추가하려는 각 메일 주소에 대해 이전 단계를 반복합니다.
1. 삭제하려는 각 메일 주소에 대해 메일 주소 옆에 있는 **삭제** 아이콘(휴지통)을 선택합니다.
1. **저장** 을 선택합니다.

알림 목록에 최대 5개의 메일 주소를 추가할 수 있습니다(애플리케이션을 추가한 관리자의 메일 주소 포함). 더 많은 사용자에게 알림이 필요한 경우 배포 목록 메일을 사용합니다.

azure-noreply@microsoft.com으로부터 알림 이메일을 받습니다. 메일이 스팸 위치로 이동하지 않도록 방지하려면 이 메일을 연락처에 추가해야 합니다.

## <a name="renew-a-certificate-that-will-soon-expire"></a>곧 만료되는 인증서 갱신

인증서가 곧 만료되는 경우 사용자에게 심각한 가동 중지 시간이 발생하지 않도록 하는 절차를 사용하여 갱신할 수 있습니다. 만료 예정인 인증서를 갱신하려면

1. 기존 인증서와 겹치는 날짜를 사용하여 앞에 나오는 [새 인증서 만들기](#create-a-new-certificate) 섹션의 지침을 따릅니다. 해당 날짜로 인해 인증서 만료로 인한 가동 중지 시간이 제한됩니다.
1. 애플리케이션이 인증서를 자동으로 롤포워드할 수 있는 경우 다음 단계를 수행하여 새 인증서를 활성으로 설정합니다.
   1. **SAML 서명 인증서** 페이지로 돌아갑니다.
   1. 새로 저장된 인증서 행에서 줄임표( **...** )를 선택한 다음, **인증서 활성화** 를 선택합니다.
   1. 다음 두 단계를 건너뜁니다.

1. 앱이 한 번에 하나의 인증서만 처리할 수 있는 경우에는 다음 단계를 수행할 가동 중지 시간 간격을 선택합니다. 애플리케이션이 새 인증서를 자동으로 선택하지 않고 둘 이상의 서명 인증서를 처리할 수 있는 경우에는 언제든지 다음 단계를 수행할 수 있습니다.
1. 이전 인증서가 만료되기 전에 앞에 나오는 [인증서 업로드 및 활성화](#upload-and-activate-a-certificate) 섹션의 지침을 따르세요. Azure Active Directory 새 인증서가 업데이트된 후 애플리케이션 인증서가 업데이트되지 않으면 앱에서 인증이 실패할 수 있습니다.
1. 애플리케이션에 로그인하여 인증서가 제대로 작동하는지 확인합니다.

애플리케이션이 Azure Active Directory에 구성된 인증서 만료의 유효성을 검사하지 않고 인증서가 Azure Active Directory와 애플리케이션 모두에서 일치하는 경우 만료된 인증서가 있어도 앱에 계속 액세스할 수 있습니다. 애플리케이션에서 인증서 만료의 유효성을 검사할 수 있는지 확인합니다.

## <a name="related-articles"></a>관련된 문서

- [SaaS 애플리케이션과 Azure Active Directory 통합을 위한 자습서](../saas-apps/tutorial-list.md)
- [Azure Active Directory를 사용한 애플리케이션 관리](what-is-application-management.md)
- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](what-is-single-sign-on.md)
- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On 디버그](./debug-saml-sso-issues.md)
