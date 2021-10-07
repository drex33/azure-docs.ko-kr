---
title: '빠른 시작: 엔터프라이즈 애플리케이션 삭제'
description: Azure Active Directory에서 엔터프라이즈 애플리케이션을 삭제합니다.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergleenl
ms.openlocfilehash: 16bc055ed2b47ee5c212fa26387599a8d4ada7a8
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058206"
---
# <a name="quickstart-delete-an-enterprise-application-in-azure-active-directory"></a>빠른 시작: Azure Active Directory에서 엔터프라이즈 애플리케이션 삭제

이 빠른 시작에서는 Azure Active Directory 관리 센터를 사용하여 Azure AD(Azure Active Directory) 테넌트에 추가된 애플리케이션을 삭제합니다.

이 빠른 시작의 단계를 테스트하려면 비 프로덕션 환경을 사용하는 것이 좋습니다.

## <a name="prerequisites"></a>필수 구성 요소

엔터프라이즈 애플리케이션을 삭제하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.
- [빠른 시작: 엔터프라이즈 애플리케이션 추가](add-application-portal.md) 단계의 완료

## <a name="delete-an-enterprise-application"></a>엔터프라이즈 애플리케이션 삭제

엔터프라이즈 애플리케이션을 삭제하려면,

1. [Azure Active Directory 관리 센터](https://aad.portal.azure.com)로 이동하고, 필수 구성 요소에 나열된 역할 중 하나를 사용하여 로그인합니다.
1. 왼쪽 메뉴에서 **엔터프라이즈 애플리케이션** 을 선택합니다. **모든 애플리케이션** 창이 열리고, Azure AD 테넌트의 애플리케이션 목록이 표시됩니다. 삭제하려는 애플리케이션을 검색하여 선택합니다. 예를 들어 **Azure AD SAML Toolkit 1** 입니다.
1. 왼쪽 메뉴의 **관리** 섹션에서 **속성** 을 선택합니다.
1. **속성** 창의 위쪽에서 **삭제** 를 선택한 다음, **예** 를 선택하여 Azure AD 테넌트에서 애플리케이션을 삭제할 것인지 확인합니다.

    :::image type="content" source="media/delete-application-portal/delete-application.png" alt-text="엔터프라이즈 애플리케이션 삭제":::

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작 시리즈가 완료되면 애플리케이션을 삭제하여 테스트 테넌트를 정리하는 것이 좋습니다. 이 빠른 시작에서는 애플리케이션을 삭제하는 방법을 설명했습니다.

## <a name="next-steps"></a>다음 단계

Single Sign-On 배포 계획에 대해 자세히 알아봅니다.
> [!div class="nextstepaction"]
> [Single Sign-On 배포 계획](plan-sso-deployment.md)
