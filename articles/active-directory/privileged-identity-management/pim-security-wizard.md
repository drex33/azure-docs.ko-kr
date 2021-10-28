---
title: Privileged Identity Management의 Azure AD 역할 검색 및 인사이트(미리 보기)(이전의 보안 마법사) - Azure Active Directory
description: 검색 및 인사이트(이전의 보안 마법사)는Privileged Identity Management를 사용하여 영구 Azure AD 역할 할당을 Just-In-Time 할당으로 변환하도록 지원합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21331670a04dc94905af0f1535a5eb06e7f083ae
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669609"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>Azure AD 역할의 검색 및 인사이트(미리 보기)(이전의 보안 마법사)

Azure AD(Azure Active Directory) 조직에서 PIM(Privileged Identity Management)을 시작하면 **검색 및 인사이트(미리 보기)** 페이지를 사용하여 시작할 수 있습니다. 이 기능은 조직의 권한 있는 역할에 할당되는 사람과 PIM을 사용하여 영구 역할 할당을 Just-In-Time 할당으로 빠르게 변경하는 방법을 보여줍니다. **검색 및 인사이트(미리 보기)** 에서 영구 권한 있는 역할 할당을 보거나 변경할 수 있습니다. 이 기능은 분석 도구 및 작업 도구입니다.

## <a name="discovery-and-insights-preview"></a>검색 및 인사이트(미리 보기)

조직에서 Privileged Identity Management 사용을 시작하기 전에 모든 역할 할당이 영구적입니다. 사용자는 권한이 필요하지 않은 경우에도 항상 할당된 역할에 포함됩니다. 이전의 보안 마법사를 대체하는 검색 및 인사이트(미리 보기)는 권한 있는 역할 목록과 현재 이러한 역할에 있는 사용자 수를 보여 줍니다. 할당된 사용자 중 한 명 이상에 대해 잘 모르는 경우 자세히 알아보기 위해 역할에 대한 할당을 나열할 수 있습니다.

:heavy_check_mark: **Microsoft에서는** 전역 관리자 역할에 영구적으로 할당되는 비상 계정 2개를 유지하도록 권장합니다. [Azure AD에서 응급 액세스 계정 관리](../roles/security-emergency-access.md)에서 설명하는 것처럼 이와 같은 비상 계정에는 일반 관리 계정과 동일한 다단계 인증 메커니즘이 필요하지 않습니다.

또한 사용자가 Microsoft 계정(Skype나 Outlook.com과 같은 Microsoft 서비스에 로그인하는 데 사용하는 계정)을 가진 경우 역할 할당을 영구적으로 유지합니다. Microsoft 계정이 있는 사용자에게 역할 할당을 활성화하는 데 다단계 인증을 요구하면 해당 사용자가 잠깁니다.

## <a name="open-discovery-and-insights-preview"></a>검색 및 인사이트(미리 보기) 열기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure AD 역할** 을 선택한 다음, **검색 및 인사이트(미리 보기)** 를 선택합니다. 페이지를 열면 검색 프로세스가 시작되어 관련 역할 할당을 찾습니다.

    ![3가지 옵션을 보여 주는 Azure AD 역할 - 검색 및 인사이트 페이지](./media/pim-security-wizard/new-preview-link.png)

1. **전역 관리자 축소** 를 선택합니다.

    ![“전역 관리자 축소” 작업이 선택된 “검색 및 인사이트(미리 보기)”를 보여 주는 스크린샷](./media/pim-security-wizard/new-preview-page.png)

1. 전역 관리자 역할 할당의 목록을 검토합니다.

    ![모든 전역 관리자를 표시하는 전역 관리자 축소 - 역할 창](./media/pim-security-wizard/new-global-administrator-list.png)

1. **다음** 을 선택하여 적격으로 만들 사용자 또는 그룹을 선택한 다음, **적격으로 만들기** 또는 **할당 제거** 를 선택합니다.

    ![역할에 적격으로 만들 멤버를 선택하는 옵션이 있는 멤버를 적격으로 변환 페이지](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. 모든 전역 관리자가 자신의 액세스를 검토하도록 요구할 수도 있습니다.

    ![액세스 검토 섹션을 보여 주는 전역 관리자 페이지](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. 변경 내용 중 하나를 선택하면 Azure 알림이 표시됩니다.

1. 그런 다음 **고정 액세스 제거** 또는 **서비스 주체 검토** 를 선택하여 다른 권한 있는 역할과 서비스 주체 역할 할당에 대해 위의 단계를 반복합니다. 서비스 주체 역할 할당의 경우 역할 할당만 제거할 수 있습니다.

    ![고정 액세스를 제거하고 서비스 주체를 검토하는 추가 인사이트 옵션 ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
