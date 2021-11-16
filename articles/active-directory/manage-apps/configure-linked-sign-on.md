---
title: 애플리케이션에 연결된 Single Sign-On 추가
description: Azure Active Directory의 애플리케이션에 연결된 Single Sign-On을 추가합니다.
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
ms.openlocfilehash: c8404b7ec361c90a6153cadc7ec6a71efb17fd1c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639625"
---
# <a name="add-linked-single-sign-on-to-an-application-in-azure-active-directory"></a>Azure Active Directory의 애플리케이션에 연결된 Single Sign-On 추가

이 문서에서는 Azure AD(Azure Active Directory)에서 애플리케이션에 대한 연결 기반 SSO(Single Sign-On)를 구성하는 방법을 보여 줍니다. 연결 기반 SSO를 통해 Azure AD는 이미 다른 서비스에서 SSO에 대해 구성된 애플리케이션에 단일 SSO를 제공할 수 있습니다. 연결됨 옵션을 사용하면 사용자가 조직의 내 앱 또는 Microsoft 365 포털에서 애플리케이션을 선택할 때 대상 위치를 구성할 수 있습니다.

연결 기반 SSO는 Azure AD를 통해 로그온 기능을 제공하지 않습니다. 이 옵션은 사용자가 내 앱 또는 Microsoft 365 포털에서 애플리케이션을 선택할 때 전송되는 위치를 설정합니다.

연결 기반 SSO가 중요한 몇 가지 일반적인 시나리오는 다음과 같습니다.
- AD FS(Active Directory Federation Services)와 같이 현재 페더레이션을 사용하는 사용자 지정 웹 애플리케이션에 대한 링크를 추가합니다.
- 사용자의 액세스 페이지에 표시하려는 특정 웹 페이지에 딥 링크를 추가합니다.
- 인증을 요구하지 않는 애플리케이션에 대한 링크를 추가합니다. 연결됨 옵션은 Azure AD 자격 증명을 통해 로그온 기능을 제공하지 않지만 엔터프라이즈 애플리케이션의 다른 기능 중 일부를 계속 사용할 수 있습니다. 예를 들어 감사 로그를 사용하고 사용자 지정 로고 및 애플리케이션 이름을 추가할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure AD 테넌트에서 연결 기반 SSO를 구성하려면 다음이 필요합니다.
-   활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
-   다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.
-   연결 기반 SSO를 지원하는 애플리케이션

## <a name="configure-linked-based-single-sign-on"></a>연결 기반 Single Sign-On 구성

1.  적절한 역할로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2.  Azure Services에서 **Azure Active Directory** 를 선택한 다음, **엔터프라이즈 애플리케이션** 을 선택합니다.
3.  연결된 SSO를 추가하려는 애플리케이션을 검색하여 선택합니다.
4.  **Single Sign-On** 을 선택하고 **연결됨** 을 선택합니다.
5.  애플리케이션의 로그인 페이지 URL을 입력합니다.
6.  **저장** 을 선택합니다. 

## <a name="next-steps"></a>다음 단계

- [앱에 대한 액세스 관리](what-is-access-management.md)