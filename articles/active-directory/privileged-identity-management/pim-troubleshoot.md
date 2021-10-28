---
title: Privileged Identity Management에서 리소스 액세스 거부 문제 해결 - Azure Active Directory | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 역할과 관련된 시스템 오류 문제를 해결하는 방법에 대해 알아봅니다.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: f357c41edf30870207ffde0f7d29ef9aadbfef72
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667177"
---
# <a name="troubleshoot-access-to-azure-resources-denied-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 리소스 액세스 거부 문제 해결

Azure AD(Azure Active Directory)의 PIM(Privileged Identity Management)과 관련하여 문제가 있나요? 다음에 나오는 정보는 작업을 다시 수행하는 데 도움이 될 수 있습니다.

## <a name="access-to-azure-resources-denied"></a>Azure 리소스에 대한 액세스가 거부됨

### <a name="problem"></a>문제

Azure 리소스에 대한 활성 소유자 또는 사용자 액세스 관리자로서 Privileged Identity Management 내에서 리소스를 볼 수 있지만 적격 할당을 수행하거나 리소스 개요 페이지에서 역할 할당 목록을 보는 등의 작업을 수행할 수 없습니다. 이러한 작업을 수행하면 권한 부여 오류가 발생합니다.

### <a name="cause"></a>원인

이 문제는 PIM 서비스 주체에 대한 사용자 액세스 관리자 역할이 실수로 구독에서 제거된 경우에 발생할 수 있습니다. Privileged Identity Management 서비스에서 Azure 리소스에 액세스할 수 있게 하려면 MS-PIM 서비스 주체가 Azure 구독에 대해 항상 [사용자 액세스 관리자 역할](../../role-based-access-control/built-in-roles.md#user-access-administrator)을 할당받아야 합니다.

### <a name="resolution"></a>해결 방법

구독 수준에서 Privileged identity Management 서비스 주체 이름(MS – PIM)에 사용자 액세스 관리자 역할을 할당합니다. 이 할당을 통해 Privileged identity Management 서비스에서 Azure 리소스에 액세스할 수 있어야 합니다. 이 역할은 요구 사항에 따라 관리 그룹 수준 또는 구독 수준에서 할당될 수 있습니다. 서비스 주체에 대한 자세한 내용은 [역할에 애플리케이션 할당](../develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management를 사용하기 위한 라이선스 요구 사항](subscription-requirements.md)
- [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](../roles/security-planning.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Privileged Identity Management 배포](pim-deployment-plan.md)