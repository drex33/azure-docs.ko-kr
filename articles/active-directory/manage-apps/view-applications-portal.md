---
title: '빠른 시작: 엔터프라이즈 애플리케이션 보기'
description: Azure Active Directory 테넌트를 사용하도록 등록된 엔터프라이즈 애플리케이션을 봅니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 09/07/2021
ms.author: davidmu
ms.reviewer: arvinh
ms.openlocfilehash: eb1a2a2359ed8c59c5de605977362307de5821b1
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061083"
---
# <a name="quickstart-view-enterprise-applications-in-azure-active-directory"></a>빠른 시작: Azure Active Directory에서 엔터프라이즈 애플리케이션 보기

이 빠른 시작에서는 Azure Active Directory 관리 센터를 사용하여 Azure AD(Azure Active Directory) 테넌트에 이미 구성된 엔터프라이즈 애플리케이션을 검색하고 확인하는 방법에 대해 알아봅니다.

이 빠른 시작의 단계를 테스트하려면 비 프로덕션 환경을 사용하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD 테넌트에 등록된 애플리케이션을 보려면 다음이 필요합니다.

- Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.
- [빠른 시작: 엔터프라이즈 애플리케이션 추가](add-application-portal.md) 단계의 완료

## <a name="view-a-list-of-applications"></a>애플리케이션 목록 보기

테넌트에 등록된 엔터프라이즈 애플리케이션을 보려면,

1. [Azure Active Directory 관리 센터](https://aad.portal.azure.com)로 이동하고, 필수 구성 요소에 나열된 역할 중 하나를 사용하여 로그인합니다.
1. 왼쪽 메뉴에서 **엔터프라이즈 애플리케이션** 을 선택합니다. **모든 애플리케이션** 창이 열리고, Azure AD 테넌트의 애플리케이션 목록이 표시됩니다.

    :::image type="content" source="media/view-applications-portal/view-enterprise-applications.png" alt-text="Azure AD 테넌트에 등록된 애플리케이션 보기":::

1. 더 많은 애플리케이션을 보려면 목록 아래에서 **추가 로드** 를 클릭합니다. 많은 애플리케이션이 테넌트에 있는 경우 목록을 스크롤하는 대신 특정 애플리케이션을 검색하는 것이 더 쉬울 수 있습니다.

## <a name="search-for-an-application"></a>애플리케이션 검색

특정 애플리케이션을 검색하려면:

1. **애플리케이션 종류** 메뉴에서 **모든 애플리케이션** 을 선택하고 **적용** 을 선택합니다.
1. 찾으려는 애플리케이션의 이름을 입력합니다. 애플리케이션이 Azure AD 테넌트에 추가되면 검색 결과에 나타납니다. 예를 들어 이전 빠른 시작에서 사용된 **Azure AD SAML Toolkit 1** 애플리케이션을 검색할 수 있습니다. 
1. 애플리케이션 이름의 처음 몇 글자를 입력해 보세요.

## <a name="select-viewing-options"></a>보기 옵션 선택

찾고 있는 항목에 따라 옵션을 선택합니다.

1. **애플리케이션 종류**, **애플리케이션 상태** 및 **애플리케이션 표시** 에 따라 애플리케이션을 볼 수 있습니다.
1. **애플리케이션 종류** 아래에서 다음 옵션 중 하나를 선택합니다.
    - **엔터프라이즈 애플리케이션** 은 Microsoft 이외의 애플리케이션을 보여줍니다.
    - **Microsoft 애플리케이션** 은 Microsoft 애플리케이션을 보여줍니다.
    - **모든 애플리케이션** 은 Microsoft 애플리케이션과 Microsoft 이외의 애플리케이션을 모두 보여줍니다.
1. **애플리케이션 상태** 에서 **모두**, **사용 안 함** 또는 **사용** 을 선택합니다. **모두** 옵션은 사용하도록 설정된 애플리케이션과 사용하지 않도록 설정된 애플리케이션을 모두 포함합니다.
1. **애플리케이션 표시** 아래에서 **모두** 또는 **숨겨진** 을 선택합니다. **숨겨진** 옵션은 테넌트에 있지만 사용자에게 보이지 않는 애플리케이션을 표시합니다.
1. 원하는 옵션을 선택한 후 **적용** 을 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

빠른 시작 전체에서 사용된 **Azure AD SAML Toolkit 1** 이라는 테스트 애플리케이션을 만든 경우 지금 삭제하여 테넌트를 정리할 수 있습니다. 자세한 내용은 [애플리케이션 삭제](delete-application-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

엔터프라이즈 애플리케이션을 삭제하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [애플리케이션 삭제](add-application-portal.md)
