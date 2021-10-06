---
title: '빠른 시작: 사용자 계정 만들기 및 할당'
titleSuffix: Azure AD
description: Azure Active Directory 테넌트에서 사용자 계정을 만들고 애플리케이션에 할당합니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: 28f5cfe158c13928d9443df22551bcfd6e7d8e85
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058402"
---
# <a name="quickstart-create-and-assign-a-user-account-in-azure-active-directory"></a>빠른 시작: Azure Active Directory에서 사용자 계정 만들기 및 할당

이 빠른 시작에서는 Azure Active Directory 관리 센터를 사용하여 Azure AD(Azure Active Directory) 테넌트에서 사용자 계정을 만듭니다. 계정이 만들어지면 해당 계정을 테넌트에 추가한 엔터프라이즈 애플리케이션에 할당할 수 있습니다.

이 빠른 시작의 단계를 테스트하려면 비 프로덕션 환경을 사용하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건

사용자 계정을 만들고 엔터프라이즈 애플리케이션에 할당하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.
- [빠른 시작: 엔터프라이즈 애플리케이션 추가](add-application-portal.md) 단계의 완료

## <a name="create-a-user-account"></a>사용자 계정 만들기

Azure AD 테넌트에서 사용자 계정을 만들려면,

1. [Azure Active Directory 관리 센터](https://aad.portal.azure.com)로 이동하고, 필수 구성 요소에 나열된 역할 중 하나를 사용하여 로그인합니다.
1. 왼쪽 메뉴에서 **사용자** 를 선택합니다.
1. 창 위쪽에서 **새 사용자** 를 선택합니다.

    :::image type="content" source="media/add-application-portal-assign-users/new-user.png" alt-text="Azure AD 테넌트에 새 사용자 계정 추가":::
    
1. **사용자 이름** 필드에서 사용자 계정의 사용자 이름을 입력합니다. 예: `contosouser1@contoso.com`. `contoso.com`을 테넌트 도메인의 이름으로 변경해야 합니다.
1. **이름** 필드에서 계정의 사용자 이름을 입력합니다. 예: `contosouser1`.
1. **암호 자동 생성** 을 선택한 상태로 두고, **암호 표시** 를 선택합니다. 암호 상자에 표시된 값을 적어둡니다.
1. **만들기** 를 선택합니다.

## <a name="assign-a-user-account-to-an-enterprise-application"></a>엔터프라이즈 애플리케이션에 사용자 계정 할당

사용자 계정을 엔터프라이즈 애플리케이션에 할당하려면,

1. [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, 사용자 계정을 할당하려는 애플리케이션을 검색하여 선택합니다. 예를 들어 이전 빠른 시작에서 만든 **Azure AD SAML Toolkit 1** 이라는 애플리케이션입니다.
1. 왼쪽 창에서 **사용자 및 그룹** 을 선택한 다음, **사용자/그룹 추가** 를 선택합니다.

    :::image type="content" source="media/add-application-portal-assign-users/assign-user.png" alt-text="Azure AD 테넌트에서 zn 애플리케이션에 사용자 계정 할당":::

1. **할당 추가** 창의 **사용자 및 그룹** 아래에서 **선택된 항목 없음** 을 선택합니다.
1. 애플리케이션에 할당하려는 사용자를 검색하여 선택합니다. 예: `contosouser1@contoso.com`.
1. **선택** 을 선택합니다.
1. **할당 추가** 창의 아래쪽에서 **할당** 단추를 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 빠른 시작을 완료하려는 경우 만든 애플리케이션을 유지합니다. 그렇지 않으면 해당 애플리케이션을 삭제하여 테넌트를 정리하는 것이 좋습니다. 자세한 내용은 [애플리케이션 삭제](delete-application-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

엔터프라이즈 애플리케이션에 대한 Single Sign-On을 설정하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [Single Sign-On 사용](add-application-portal-setup-sso.md)
