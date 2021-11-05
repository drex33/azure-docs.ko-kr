---
title: Azure AD 권한 관리에서 액세스 패키지에 대한 요청 보기 및 제거 - Azure Active Directory
description: Azure Active Directory 권한 관리에서 액세스 패키지에 대한 요청을 보고 제거하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 9/20/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140d648ddde6d520fdb27c6df0152cb9eb61c1fa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051804"
---
# <a name="view-and-remove-requests-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지에 대한 요청 보기 및 제거

Azure AD 권한 관리에서 액세스 패키지를 요청한 사용자, 해당 정책, 상태를 확인할 수 있습니다. 이 문서에서는 액세스 패키지에 대한 요청을 보고 더 이상 필요하지 않은 요청을 제거하는 방법에 대해 설명합니다.

## <a name="view-requests"></a>요청 보기

**필수 역할:** 전역 관리자, Identity Governance 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자 또는 액세스 패키지 할당 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. **요청** 을 클릭합니다.

1. 특정 요청을 클릭하여 추가 세부 정보를 확인합니다.

    ![액세스 패키지의 요청 목록](./media/entitlement-management-access-package-requests/requests-list.png)

1. **요청 내역 세부 정보** 를 클릭하여 누가 요청을 승인했는지, 승인 근거는 무엇이며, 언제 액세스 권한이 부여되었는지 확인할 수 있습니다.

요청이 "부분적으로 전달됨" 또는 "실패" 상태인 사용자 집합이 있는 경우 [재처리 기능](entitlement-management-reprocess-access-package-requests.md)을 사용하여 해당 요청을 다시 시도할 수 있습니다.

### <a name="view-assignments-with-microsoft-graph"></a>Microsoft Graph로 할당 보기
Microsoft Graph를 사용하여 액세스 패키지에 대한 요청을 검색할 수도 있습니다.  위임된 `EntitlementManagement.Read.All` 또는 `EntitlementManagement.ReadWrite.All` 권한이 있는 애플리케이션의 적절한 역할 사용자는 API를 호출하여 [accessPackageAssignmentRequests를 나열](/graph/api/accesspackageassignmentrequest-list?view=graph-rest-beta&preserve-view=true)할 수 있습니다. 필터를 제공하여 `$expand=accessPackage&$filter=accessPackage/id eq '9bbe5f7d-f1e7-4eb1-a586-38cdf6f8b1ea'`과 같은 특정 액세스 패키지를 나타낼 수 있습니다. 애플리케이션 권한 `EntitlementManagement.Read.All` 또는 `EntitlementManagement.ReadWrite.All`이 있는 애플리케이션도 이 API를 사용할 수 있습니다.

## <a name="remove-request-preview"></a>요청 제거(미리 보기)

더 이상 필요하지 않은 완료된 요청을 제거할 수도 있습니다. 요청을 제거하려면:

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. **요청** 을 클릭합니다.

1. 액세스 패키지에서 제거하려는 요청을 찾으세요.

1. 제거 단추를 선택합니다.

> [!NOTE]
> 액세스 패키지에서 완료된 요청을 제거하면 활성 할당이 제거되지 않고 요청 데이터만 제거됩니다. 따라서 요청자는 계속 액세스할 수 있습니다. 해당 액세스 패키지에서 할당 및 그에 따른 액세스 권한도 제거해야 하는 경우 왼쪽 메뉴에서 **할당** 을 클릭하고 할당을 찾은 다음 [할당을 제거](entitlement-management-access-package-assignments.md)합니다.

### <a name="remove-a-request-with-microsoft-graph"></a>Microsoft Graph로 요청 제거

Microsoft Graph를 사용하여 요청을 제거할 수도 있습니다.  위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 애플리케이션 또는 해당 애플리케이션 권한이 있는 애플리케이션에서 적절한 역할이 있는 사용자는 API를 호출하여 [accessPackageAssignmentRequest를 제거](/graph/api/accesspackageassignmentrequest-delete?view=graph-rest-beta&preserve-view=true)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [액세스 패키지 요청 재처리](entitlement-management-reprocess-access-package-requests.md)
- [액세스 패키지의 요청 및 승인 설정 변경](entitlement-management-access-package-request-policy.md)
- [액세스 패키지에 대한 할당 보기, 추가, 제거](entitlement-management-access-package-assignments.md)
- [요청 문제 해결](entitlement-management-troubleshoot.md#requests)
