---
title: 관리 ID에 대한 업데이트 및 로그인 활동 보기
description: 관리 ID에 수행된 활동 및 관리 ID에서 수행한 인증을 보기 위한 단계별 지침
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/26/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 937cdae8c1f191bc8e6b03d680829d8f674b8d62
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123077498"
---
# <a name="view-update-and-sign-in-activities-for-managed-identities"></a>관리 ID에 대한 업데이트 및 로그인 활동 보기

이 문서에서는 관리 ID에 대해 수행된 업데이트와 관리 ID에 의한 로그인 시도를 보는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요.
- 아직 Azure 계정이 없는 경우 [체험 계정에 가입](https://azure.microsoft.com/free/)합니다.

## <a name="view-updates-made-to-user-assigned-managed-identities"></a>사용자 할당 관리 ID에 대한 업데이트 보기

이 절차는 사용자 할당 관리 ID에 수행된 업데이트를 보는 방법을 보여 줍니다.

1. Azure Portal에서 **활동 로그** 로 이동합니다.

 ![Azure Portal에서 활동 로그로 이동](./media/how-to-view-managed-identity-activity/browse-to-activity-log.png)

2. **필터 추가** 검색 필을 선택하고 목록에서 **작업** 을 선택합니다.

![검색 필터 빌드 시작](./media/how-to-view-managed-identity-activity/start-adding-search-filter.png)

3. **작업** 드롭다운 목록에서 작업 이름 "사용자 할당 ID 삭제" 및 "UserAssignedIdentities 쓰기"를 입력합니다.

![검색 필터에 작업 추가](./media/how-to-view-managed-identity-activity/add-operations-to-search-filter.png)

4. 일치하는 작업이 표시되면 하나를 선택하여 요약을 봅니다.

![작업 요약 보기](./media/how-to-view-managed-identity-activity/view-summary-of-operation.png)

5. 작업에 대한 자세한 정보를 보려면 **JSON** 탭을 선택하고 수정된 ID에 대한 정보를 보려면 **속성** 노드로 스크롤합니다.

![작업의 세부 정보 보기](./media/how-to-view-managed-identity-activity/view-json-of-operation.png)

## <a name="view-role-assignments-added-and-removed-for-managed-identities"></a>관리 ID에 대해 추가 및 제거된 역할 할당 보기

 > [!NOTE] 
 > 역할 할당 변경 사항을 보려는 관리 ID의 개체(주체) ID로 검색해야 합니다.

1. 역할 할당 변경 사항을 보려는 관리 ID를 찾습니다. 시스템이 할당한 관리 ID를 찾는 경우 개체 ID가 리소스 아래의 **ID** 화면에 표시됩니다. 사용자 할당 ID를 찾는 경우 관리 ID의 **개요** 페이지에 개체 ID가 표시됩니다.

사용자가 할당한 ID:

![사용자 할당 ID의 개체 ID 가져오기](./media/how-to-view-managed-identity-activity/get-object-id-of-user-assigned-identity.png)

시스템 할당 ID:

![시스템 할당 ID의 개체 ID 가져오기](./media/how-to-view-managed-identity-activity/get-object-id-of-system-assigned-identity.png)

2. 개체 ID 를 복사합니다.
3. **활동 로그** 로 이동합니다.

 ![Azure Portal에서 활동 로그로 이동](./media/how-to-view-managed-identity-activity/browse-to-activity-log.png)

4. **필터 추가** 검색 필을 선택하고 목록에서 **작업** 을 선택합니다.

![검색 필터 빌드 시작](./media/how-to-view-managed-identity-activity/start-adding-search-filter.png)

5. **작업** 드롭다운 목록에서 "역할 할당 만들기" 및 "역할 할당 삭제" 작업 이름을 입력합니다.

![검색 필터에 역할 할당 작업 추가](./media/how-to-view-managed-identity-activity/add-role-assignment-operations-to-search-filter.png)

6. 검색 상자에 개체 ID를 붙여넣습니다. 결과는 자동으로 필터링됩니다.

![개체 ID로 검색](./media/how-to-view-managed-identity-activity/search-by-object-id.png)
 
7. 일치하는 작업이 표시되면 하나를 선택하여 요약을 봅니다.
 
![관리 ID에 대한 역할 할당 요약](./media/how-to-view-managed-identity-activity/summary-of-role-assignment-for-msi.png)

## <a name="view-authentication-attempts-by-managed-identities"></a>관리 ID별 인증 시도 보기

1. **Azure Active Directory** 로 이동합니다.

![Active Directory로 이동](./media/how-to-view-managed-identity-activity/browse-to-active-directory.png)

2.  **모니터링** 섹션에서 **로그인 로그** 를 선택합니다.

![로그인 로그 선택](./media/how-to-view-managed-identity-activity/sign-in-logs-menu-item.png)

3. **관리 ID 로그인** 탭을 선택합니다.

![관리 ID 로그인](./media/how-to-view-managed-identity-activity/msi-sign-ins.png)

4. 이제 로그인 이벤트가 관리 ID로 필터링됩니다.

![관리 ID 로그인 이벤트](./media/how-to-view-managed-identity-activity/msi-sign-in-events.png) 

5.  Azure Active Directory에서 ID의 엔터프라이즈 애플리케이션을 보려면 “관리 ID ID” 열을 선택합니다.
6.  Azure 리소스 또는 사용자 할당 관리 ID를 보려면 Azure Portal의 검색 창에서 이름으로 검색합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 리소스에 대한 관리 ID](./overview.md)
* [Azure 활동 로그](/azure/azure-monitor/essentials/activity-log)
* [Azure Active Directory 로그인 로그](/azure/active-directory/reports-monitoring/concept-sign-ins )