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
ms.date: 09/02/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c1c30008a45130ba044cfaad91f4a3fd0726f00
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129986362"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Azure Active Directory에서 그룹 및 멤버 검색(미리 보기)

이 문서에서는 그룹의 멤버 및 소유자를 검색하는 방법과 Azure AD(Azure Active Directory) 포털에서 그룹 개선 미리 보기의 일부로 검색 필터를 사용하는 방법을 설명합니다. 멤버 및 소유자를 포함한 그룹을 빠르고 쉽게 관리할 수 있도록 그룹 환경에 많은 개선 사항을 제공합니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 미리 보기의 변경 내용은 다음과 같습니다.

- 그룹 이름의 부분 문자열 검색과 같은 새 그룹 검색 기능
- 멤버 및 소유자 목록의 새로운 필터링 및 정렬 옵션
- 멤버 및 소유자 목록에 대한 새 검색 기능
- 대규모 그룹의 그룹 수를 더욱 정확하게 계산

## <a name="enabling-and-managing-the-preview"></a>미리 보기 설정 및 관리

미리 보기에 쉽게 조인할 수 있습니다.

  1. [Azure AD Portal](https://portal.azure.com)에 로그인하고 **그룹** 을 선택합니다.
  2. 그룹 – 모든 그룹 페이지에서 페이지 상단의 배너를 선택하여 미리 보기에 참여합니다.

**모든 그룹** 페이지에서 **미리 보기 정보** 를 선택하여 최신 기능과 개선 사항을 확인할 수도 있습니다. 미리 보기에 참여하면 개선된 모든 그룹 페이지에서 미리 보기의 일부인 미리 보기 태그를 볼 수 있습니다. 모든 그룹 페이지가 이 미리 보기의 일부로 업데이트되지는 않았습니다.

문제가 있는 경우 **모든 그룹** 페이지 상단에 있는 배너를 선택하여 레거시 환경으로 전환할 수 있습니다. 사용 경험을 개선할 수 있도록 피드백을 제공해주셔서 감사합니다.

## <a name="group-search-and-sorting"></a>그룹 검색 및 정렬

그룹 목록 검색이 향상되어 검색 문자열을 입력할 수 있을 때 검색이 그룹 이름 목록에서 `startswith` 및 하위 문자열 검색을 자동으로 수행합니다. 하위 문자열 검색은 전체 단어에 대해서만 수행되며 특수 문자는 포함하지 않습니다. 하위 문자열 검색은 대/소문자를 구분합니다.

![모든 그룹 페이지에서 새 하위 문자열 검색](./media/groups-members-owners-search/groups-search-preview.png)

예를 들어 "정책"을 검색하면 이제 "MDM 정책 - 서 부" 및 "정책 그룹"이 모두 반환합니다. "New_policy"라는 그룹은 반환되지 않습니다.

- 그룹 멤버 자격 목록에서도 동일한 검색을 수행할 수 있습니다.
- 이제 이름 열 제목 오른쪽에 있는 화살표를 사용하여 그룹 목록을 이름별로 정렬하여 목록을 오름차순 또는 내림차순으로 정렬할 수 있습니다.

## <a name="group-member-search-and-filtering"></a>그룹 멤버 검색 및 필터링

### <a name="search-group-member-and-owner-lists"></a>그룹 멤버 및 소유자 목록 검색

이제 특정 그룹의 멤버를 이름으로 검색하고 그룹 소유자 목록에서도 동일한 검색을 수행할 수 있습니다. 새 환경에서 검색 상자에 문자열을 입력하면 startswith 검색이 자동으로 수행됩니다. 예를 들어 "Scott"을 검색하면 Scott Wilkinson이 반환됩니다.

![그룹 멤버 및 소유자 목록에서 새 하위 문자열 검색](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>멤버 및 소유자 목록 필터링

이제 검색 외에도 사용자 유형별 멤버 및 소유자 목록을 필터링할 수 있습니다. 목록의 사용자 유형 열에서 찾을 수 있는 정보입니다. 따라서 멤버 및 게스트를 기준으로 목록을 필터링하여 그룹에 게스트가 있는지 확인할 수 있습니다.

### <a name="view-and-manage-membership"></a>멤버 자격 보기 및 관리

이제 특정 그룹의 멤버를 직접 볼 수 있을 뿐 아니라 멤버 페이지 내에서 그룹의 모든 멤버 목록을 볼 수 있습니다. 멤버 목록에는 모든 전이적 멤버를 비롯한 그룹의 모든 고유 멤버가 포함됩니다.

직접 멤버 목록 및 모든 멤버 목록을 개별적으로 검색하고 필터링할 수도 있습니다. 모든 멤버 목록을 필터링해도 직접 멤버 목록에 적용되는 필터에는 영향을 주지 않습니다.

## <a name="improved-group-member-counts"></a>그룹 멤버 집계가 향상됨

그룹 **개요** 페이지를 개선하여 모든 규모의 그룹에 대해 그룹 멤버 수를 제공했습니다. 멤버 수가 1,000명을 초과하는 그룹에 대해서도 확인할 수 있습니다. 이제 **개요** 페이지에서 그룹의 총 직접 멤버 수와 총 멤버 수(전이적 멤버를 포함한 그룹의 모든 고유 멤버)를 볼 수 있습니다.

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
