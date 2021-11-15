---
title: 애플리케이션에 암호 기반 Single Sign-On 추가
description: Azure Active Directory의 애플리케이션에 암호 기반 Single Sign-On을 추가합니다.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: f8b32e5adda8d09f928b6fc6b447a9363998017f
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074060"
---
# <a name="add-password-based-single-sign-on-to-an-application-in-azure-active-directory"></a>Azure Active Directory의 애플리케이션에 암호 기반 Single Sign-On 추가

이 문서에서는 Azure AD(Azure Active Directory)에서 암호 기반 SSO(Single Sign-On)를 설정하는 방법을 보여줍니다. 암호 기반 SSO를 사용하는 경우 사용자는 애플리케이션에 처음 액세스할 때 사용자 이름 및 암호를 사용하여 애플리케이션에 로그인합니다. 첫 번째 로그온 후에는 Azure AD가 사용자 이름 및 암호를 애플리케이션에 보냅니다. 

암호 기반 SSO는 애플리케이션에서 제공한 기존 인증 프로세스를 사용합니다. 애플리케이션에서 암호 기반 SSO를 사용하도록 설정하면 Azure AD는 애플리케이션에 대한 사용자 이름 및 암호를 수집하고 안전하게 저장합니다. 사용자 자격 증명은 암호화된 상태로 디렉터리에 저장됩니다. HTML 기반 로그인 페이지가 있는 클라우드 기반 애플리케이션에 암호 기반 SSO가 지원됩니다.

다음과 같은 경우에 암호 기반 SSO를 선택합니다.
- 애플리케이션은 SAML SSO 프로토콜을 지원하지 않습니다.
- 애플리케이션이 액세스 토큰 및 헤더 대신 사용자 이름과 암호를 사용하여 인증합니다.

암호 기반 SSO의 구성 페이지는 단순합니다. 애플리케이션에서 사용하는 로그온 페이지의 URL만 포함합니다. 이 문자열은 사용자 이름 입력 필드를 포함하는 페이지여야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure AD 테넌트에서 암호 기반 SSO를 구성하려면 다음이 필요합니다.
-   활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
-   다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.
-   암호 기반 SSO를 지원하는 애플리케이션입니다.

## <a name="configure-password-based-single-sign-on"></a>암호 기반 Single Sign-On 구성

1.  적절한 역할로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1.  Azure Services에서 **Azure Active Directory** 를 선택한 다음, **엔터프라이즈 애플리케이션** 을 선택합니다.
1.  암호 기반 SSO를 추가할 애플리케이션을 검색하여 선택합니다.
1.  **Single Sign-On** 을 선택한 다음, **암호 기반** 을 선택합니다.
1.  애플리케이션의 로그인 페이지 URL을 입력합니다.
1.  **저장** 을 선택합니다. 

Azure AD는 사용자 이름 및 암호 입력 필드에 대한 로그인 페이지의 HTML을 구문 분석합니다. 시도가 성공하면 완료된 것입니다. 다음 단계는 애플리케이션에 [사용자 또는 그룹을 할당](add-application-portal-assign-users.md)하는 것입니다. 

사용자 및 그룹을 할당한 후 사용자가 애플리케이션에 로그인할 때 사용할 자격 증명을 제공할 수 있습니다. 

1. **사용자 및 그룹** 을 선택하고 사용자 또는 그룹의 행에 대한 확인란을 선택한 다음 **자격 증명 업데이트** 를 선택합니다. 
1. 사용자 또는 그룹에 사용할 사용자 이름과 암호를 입력합니다. 그러지 않으면 시작할 때 자격 증명을 입력하라는 메시지가 사용자에게 표시됩니다.

## <a name="manual-configuration"></a>수동 구성

Azure AD의 구문 분석이 실패하는 경우 로그온을 수동으로 구성할 수 있습니다.

1. **{애플리케이션} 암호 Single Sign-On 설정 구성** 을 선택하여 **로그온 구성** 페이지를 표시합니다.
1. **로그인 필드 수동 검색** 을 선택합니다. 로그인 필드의 수동 검색을 설명하는 추가 지침이 표시됩니다.
1. **로그인 필드 캡처** 를 선택합니다. 새 탭에서 캡처 상태 페이지가 열리고 메타데이터 캡처가 현재 진행 중임 메시지가 표시됩니다.
1. **내 앱 확장 필요** 상자가 새 탭에 표시되면 **지금 설치** 를 선택하여 내 앱 보안 로그인 확장 브라우저 확장을 설치합니다. (브라우저 확장에는 Microsoft Edge 또는 Chrome이 필요합니다.) 그런 다음, 확장을 설치, 시작 및 사용하도록 설정하고 캡처 상태 페이지를 새로 고칩니다. 그러면 브라우저 확장에서 입력한 URL을 표시하는 다른 탭이 열립니다.
1. 입력한 URL이 있는 탭에서 로그인 프로세스를 진행합니다. 사용자 이름 및 암호를 입력하고 로그인을 시도합니다. (올바른 암호를 제공할 필요가 없습니다.) 캡처한 로그인 필드를 저장하라는 메시지가 표시됩니다.
1. **확인** 을 선택합니다. 브라우저 확장은 캡처 상태 페이지를 업데이트하며 **메타데이터가 애플리케이션을 위해 업데이트됨** 메시지가 표시됩니다. 브라우저 탭이 닫힙니다.
1. Azure AD 로그온 구성 페이지에서 **예, 앱에 성공적으로 로그인했습니다** 를 선택합니다.
1. **확인** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [앱에 대한 액세스 관리](what-is-access-management.md)
