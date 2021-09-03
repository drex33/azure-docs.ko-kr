---
title: Azure Active Directory에서 SAML 기반 Single Sign-On 문제 해결
description: SAML 기반 Single Sign-On에 대해 구성된 Azure AD 앱의 문제를 해결합니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 10bb11d51ab6c8070c93f64aa2a18e8695907aad
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529009"
---
# <a name="troubleshoot-saml-based-single-sign-on-in-azure-active-directory"></a>Azure Active Directory에서 SAML 기반 Single Sign-On 문제 해결

애플리케이션을 구성할 때 문제가 발생 할 경우. 애플리케이션의 자습서에 있는 단계를 모두 수행했는지 확인합니다. 애플리케이션의 구성에는 애플리케이션 구성 방법에 관한 인라인 설명서가 있습니다. 또한 [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)에 액세스하면 자세한 단계별 지침을 볼 수 있습니다.

## <a name="cant-add-another-instance-of-the-application"></a>애플리케이션의 다른 인스턴스를 추가할 수 없음

애플리케이션의 두 번째 인스턴스를 추가하려면 다음을 수행할 수 있어야 합니다.

- 두 번째 인스턴스에 대한 고유 식별자를 구성합니다. 첫 번째 인스턴스에 대해 사용한 것과 동일한 식별자를 구성할 수 없습니다.
- 첫 번째 인스턴스에 대해 사용한 것과 다른 인증서를 구성합니다.

애플리케이션에서 위 사항 중에서 어느 것도 지원하지 않는 경우. 두 번째 인스턴스를 구성할 수 없습니다.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>식별자 또는 회신 URL을 추가할 수 없습니다.

식별자 또는 회신 URL을 구성할 수 없는 경우는 식별자 및 회신 URL 값이 애플리케이션에 대해 미리 구성된 패턴과 일치하는지 확인합니다.

애플리케이션에 대해 미리 구성된 패턴을 알려면:

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다. 7단계로 이동합니다. 이미 Azure AD의 애플리케이션 구성 블레이드에 있는 경우.
2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.
3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.
4. Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션** 을 클릭합니다.
5. **모든 애플리케이션** 을 클릭하여 모든 애플리케이션의 목록을 봅니다.
   - 여기에 표시하려는 애플리케이션이 보이지 않으면 **모든 애플리케이션 목록** 의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션** 으로 설정합니다.
6. Single Sign-On을 구성하려는 애플리케이션을 선택합니다.
7. 애플리케이션이 로드되면 애플리케이션의 왼쪽 탐색 메뉴에서 **Single Sign-On** 을 클릭합니다.
8. **모드** 드롭다운에서 **SAML 기반 로그온** 을 선택합니다.
9. **도메인 및 URL 섹션** 아래의 **식별자** 또는 **회신 URL** 텍스트 상자로 이동합니다.
10. 애플리케이션에 대해 지원되는 패턴을 아는 방법은 세 가지가 있습니다.
    - 텍스트 상자에서 지원되는 패턴이 자리 표시자로 표시됩니다. *예:* <https://contoso.com>.
    - 패턴이 지원되지 않으면 텍스트 상자에 값을 입력하려 할 때 빨간색 느낌표가 표시됩니다. 빨간색 느낌표 위에 마우스를 놓으면 지원되는 패턴이 표시됩니다.
    - 애플리케이션의 자습서에서 지원되는 패턴에 대한 정보를 볼 수도 있습니다. **Azure AD Single Sign-On 구성** 섹션 아래. **도메인 및 URL** 섹션 아래의 값 구성 단계로 이동합니다.

값이 Azure AD에 미리 구성된 패턴과 일치하지 않을 경우. 다음과 같습니다.

- 애플리케이션 공급업체의 도움을 받아 Azure AD에 미리 구성된 패턴과 일치하는 값을 얻습니다.
- 또는 <aadapprequest@microsoft.com>으로 Azure AD 팀에 문의하거나 자습서에 애플리케이션에서 지원되는 패턴으로 업데이트해 달라는 요청을 남길 수 있습니다.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>EntityID(사용자 식별자) 형식을 설정하는 위치

사용자 인증 후에 Azure AD에서 응답을 통해 애플리케이션으로 보내는 EntityID(사용자 식별자) 형식은 선택할 수 없습니다.

Azure AD에서는 선택한 값 또는 SAML AuthRequest에서 애플리케이션이 요청한 형식을 기반으로 NameID 특성(사용자 식별자)의 형식을 선택합니다. 자세한 내용은 NameIDPolicy 섹션 아래의 [Single Sign-On SAML 프로토콜](../develop/single-sign-on-saml-protocol.md#authnrequest) 문서에서 확인할 수 있습니다.

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Azure AD 메타데이터를 찾아서 애플리케이션 구성을 완료할 수 없음

Azure AD에서 애플리케이션 메타데이터 또는 인증서를 다운로드하려면 아래 단계를 수행합니다.

1. [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.
2. 왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스** 를 클릭하여 **Azure Active Directory 확장** 을 엽니다.
3. 필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.
4. Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션** 을 선택합니다.
5. **모든 애플리케이션** 을 선택하여 모든 애플리케이션 목록을 봅니다.
   - 여기에 표시하려는 애플리케이션이 표시되지 않으면 **모든 애플리케이션 목록** 의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 애플리케이션** 으로 설정합니다.
6. Single Sign-On을 구성한 애플리케이션을 선택합니다.
7. 애플리케이션이 로드되면 애플리케이션의 왼쪽 탐색 메뉴에서 **Single Sign-On** 을 클릭합니다.
8. **SAML 서명 인증서** 섹션으로 이동한 다음 **다운로드** 열 값을 클릭합니다. Single Sign-On을 구성해야 할 애플리케이션에 따라 메타데이터 XML이나 인증서를 다운로드하는 옵션이 표시됩니다.

Azure AD에서는 메타데이터를 가져오는 URL을 제공하지 않습니다. 메타데이터는 XML 파일로만 검색할 수 있습니다.

## <a name="customize-saml-claims-sent-to-an-application"></a>애플리케이션에 보낸 SAML 클레임 사용자 지정

애플리케이션에 전송된 SAML 특성 클레임을 사용자 지정하는 방법을 알아보려면 [Azure Active Directory의 클레임 매핑](../develop/active-directory-claims-mapping.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [애플리케이션 관리에 대한 빠른 시작 시리즈](view-applications-portal.md)
