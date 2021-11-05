---
title: 그룹 멤버 및 소유자 검색 및 필터링(미리 보기) -Azure Active Directory | Microsoft Docs
description: Azure Portal에서 그룹 멤버 및 소유자를 검색하고 필터링합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 10/22/2021
ms.author: curtand
ms.reviewer: jodah
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8774bef1dd939daea4384a25bc35d22f3475f57a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049619"
---
# <a name="search-groups-and-members-in-azure-active-directory"></a>Azure Active Directory에서 그룹 및 멤버 검색

이 문서에서는 그룹의 멤버 및 소유자를 검색하는 방법과 검색 필터를 사용하는 방법을 Azure AD(Azure Active Directory) 포털에 대해 설명합니다. 그룹 검색 기능은 다음과 같습니다.

- 그룹 이름의 부분 문자열 검색과 같은 그룹 검색 기능
- 멤버 및 소유자 목록의 필터링 및 정렬 옵션
- 멤버 및 소유자 목록에 대한 검색 기능

## <a name="group-search-and-sort"></a>그룹 검색 및 정렬

**모든 그룹** 페이지에서 검색 문자열을 입력하면 이제 **모든 그룹** 페이지에서만 "포함" 및 "다음으로 시작" 검색 간에 전환할 수 있습니다. 부분 문자열 검색은 전체 단어에 대해서만 수행되며 특수 문자는 AND 검색으로도 검색됩니다. 예를 들어 -Name을 검색하면 substring "Name"에 대한 검색과 "-"에 대한 검색이 시작됩니다. 하위 문자열 검색은 대/소문자를 구분합니다. 개체 ID 또는 mailNickname 속성도 검색됩니다.

![모든 그룹 페이지에서 새 하위 문자열 검색](./media/groups-members-owners-search/members-list.png)

예를 들어 "policy"를 검색하면 "MDM policy West"와 "Policy group"이 모두 반환됩니다. "New_policy"라는 그룹은 반환되지 않습니다. 이름을 기준으로 **모든 그룹** 목록을 오름차순 또는 내림차순으로 정렬할 수 있습니다.

## <a name="group-member-search-and-filter"></a>그룹 멤버 검색 및 필터링

### <a name="search-group-member-and-owner-lists"></a>그룹 멤버 및 소유자 목록 검색

특정 그룹의 멤버 또는 소유자를 이름으로 검색할 수 있으며, 검색 문자열을 입력하면 `contains` 검색이 자동으로 수행됩니다. 예를 들어 "Scott을 검색하면 Scott Wilkinson과 Maya Scott을 모두 반환합니다.

![그룹 멤버 및 소유자 목록에서 새 하위 문자열 검색](./media/groups-members-owners-search/groups-search-preview.png)

### <a name="filter-member-and-owner-lists"></a>멤버 및 소유자 목록 필터링

사용자 유형별로 그룹 멤버 및 소유자 목록을 필터링할 수도 있습니다. 이 정보는 멤버 또는 소유자 목록의 **사용자 유형** 열에서 찾을 수 있습니다. 목록을 필터링하여 멤버 또는 게스트만 볼 수 있습니다.

**멤버** 페이지에는 다른 그룹에서 그룹 멤버 자격을 상속하는 모든 사람을 포함하여 그룹의 모든 고유 멤버가 포함됩니다.

목록을 개별적으로 검색하고 필터링할 수도 있습니다. 모든 멤버 목록을 필터링해도 직접 멤버 목록에 적용되는 필터에는 영향을 미치지 않습니다.

## <a name="group-memberships"></a>그룹 멤버 자격

**그룹 멤버 자격** 페이지에서 그룹의 그룹 멤버 자격을 볼 수도 있습니다. **그룹 멤버 자격** 페이지는 다른 그룹 페이지와 유사한 검색, 정렬 및 필터링 작업을 지원합니다.

## <a name="group-member-counts"></a>그룹 멤버 수

그룹 **개요** 페이지는 그룹의 멤버 수를 제공합니다. **개요** 페이지에서 그룹의 총 직접 멤버 수와 총 멤버 수(상속된 멤버 자격을 포함한 그룹의 모든 고유 멤버)를 볼 수 있습니다.

![그룹 멤버 자격 수의 정확도 높이기](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>다음 단계

이러한 문서는 Azure AD의 그룹 작업에 대한 추가 정보를 제공합니다.

- [그룹 및 멤버 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
- [그룹 멤버 자격 관리](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [그룹의 사용자에 대한 동적 규칙 관리](groups-create-rule.md)
- [그룹 설정 편집](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [그룹을 사용하여 리소스에 대한 액세스 관리](../fundamentals/active-directory-manage-groups.md)
- [그룹을 사용하여 SaaS 앱에 대한 액세스 관리](groups-saasapps.md)
- [PowerShell 명령을 사용하여 그룹 관리](../enterprise-users/groups-settings-v2-cmdlets.md)
- [Azure Active Directory에 Azure 구독 추가](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
