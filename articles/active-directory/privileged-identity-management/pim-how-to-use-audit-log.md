---
title: Azure AD 역할에 대한 감사 로그 보고서 보기 | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure AD 역할에 대한 감사 기록을 보는 방법을 알아봅니다.
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
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 408ae958cb82557d6dabd92cd92e3967b9a8b40e
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667975"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할에 대한 감사 기록 보기

Azure Privileged Identity Management(PIM) 감사 기록을 사용하여 권한 있는 모든 역할에 대한 지난 30일의 모든 역할 할당 및 활성화를 볼 수 있습니다. 감사 데이터를 기본 보존 기간보다 오래 보존하려는 경우 Azure Monitor를 사용하여 Azure 스토리지 계정으로 라우팅할 수 있습니다. 자세한 내용은 [Azure 스토리지 계정에 Azure AD 로그 보관](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)을 참조하세요. 관리자, 최종 사용자 및 동기화 작업을 포함하여 Azure Active Directory(Azure AD) 조직에서 활동의 전체 감사 기록을 보려면 [Azure Active Directory 보안 및 작업 보고서](../reports-monitoring/overview-reports.md)를 사용할 수 있습니다.

Azure AD 역할에 대한 감사 기록을 보려면 다음 단계를 따르세요.

## <a name="view-resource-audit-history"></a>리소스 감사 기록 보기

리소스 감사는 Azure AD 역할과 연결된 모든 활동에 대한 보기를 제공합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure AD 역할** 을 선택합니다.

1. **리소스 감사** 를 선택합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![필터를 사용하는 Azure AD 역할 감사 목록](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>내 감사 보기

내 감사를 통해 자신의 개인 역할 작업을 볼 수 있습니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure AD 역할** 을 선택합니다.

1. 감사 기록을 보려는 리소스를 선택합니다.

1. **내 앱** 을 선택합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![현재 사용자에 대한 감사 목록](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할에 대한 작업 및 감사 기록을 봅니다](azure-pim-resource-rbac.md)
