---
title: PIM(Privileged Identity Management)에서 권한 있는 액세스 그룹 할당에 대한 감사 보고서 보기 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 권한 있는 액세스 그룹 할당에 대한 활동 및 감사 기록을 봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72a415ba766dd9a1149dfe1089fe96ff2924988b
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668108"
---
# <a name="audit-activity-history-for-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Privileged Identity Management에서 권한 있는 액세스 그룹 할당에 대한 감사 활동 기록(미리 보기)

PIM(Privileged Identity Management)을 사용하면 Azure AD(Azure Active Directory) 조직 내에서 Azure 권한 있는 액세스 그룹 구성원 및 소유자에 대한 활동, 활성화 및 감사 기록을 볼 수 있습니다.

> [!NOTE]
> 조직에서 [Azure Lighthouse](../../lighthouse/overview.md)를 사용하는 서비스 공급자에 대해 아웃소싱된 관리 기능을 사용하는 경우 해당 서비스 공급자가 승인한 역할 할당은 여기에 표시되지 않습니다.

권한 있는 액세스 그룹의 감사 기록을 보려면 다음 단계를 따릅니다.

## <a name="view-resource-audit-history"></a>리소스 감사 기록 보기

**리소스 감사** 는 권한 있는 액세스 그룹과 연결된 모든 활동에 대한 보기를 제공합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **권한 있는 액세스 그룹(미리 보기)** 을 선택하세요.

1. 감사 기록을 보려는 권한 있는 액세스 그룹을 선택합니다.

1. **활동** 에서 **리소스 감사** 를 선택합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![필터를 사용하는 리소스 감사 목록](media/groups-audit/groups-resource-audit.png)

## <a name="view-my-audit"></a>내 감사 보기

**내 감사** 를 사용하여 권한 있는 액세스 그룹에 대한 개인 역할 활동을 볼 수 있습니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **권한 있는 액세스 그룹(미리 보기)** 을 선택하세요.

1. 감사 기록을 보려는 권한 있는 액세스 그룹을 선택합니다.

1. **활동** 에서 **내 감사** 를 선택합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![현재 사용자에 대한 감사 목록](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 그룹 멤버 자격 및 소유권 할당](groups-assign-member-owner.md)
- [PIM에서 권한 있는 액세스 그룹에 대한 요청 승인 또는 거부](groups-approval-workflow.md)
- [PIM에서 Azure AD 역할에 대한 감사 기록 보기](groups-audit.md)
