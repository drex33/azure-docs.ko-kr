---
title: PIM(Privileged Identity Management)에서 권한 있는 액세스 그룹 관리 | Microsoft Docs
description: PIM(Privileged Identity Management)에서 권한 있는 액세스 그룹의 멤버 및 소유자를 관리하는 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/07/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86ad08d7c50195332460eab285f3d32d45eec8cb
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668013"
---
# <a name="management-capabilities-for-privileged-access-groups-preview"></a>권한 있는 액세스 그룹의 관리 기능(미리 보기)

이제 PIM(Privileged Identity Management)에서 권한 있는 액세스 그룹의 멤버 자격 또는 소유권에 대한 자격을 할당할 수 있습니다. 이 미리 보기부터 클라우드 그룹에 Azure AD(Azure Active Directory) 기본 제공 역할을 할당하고 PIM을 사용하여 그룹 멤버 및 소유자 자격과 활성화를 관리할 수 있습니다. Azure AD의 역할 할당 가능 그룹에 대한 자세한 내용은 [Azure AD 그룹을 사용하여 역할 할당 관리](../roles/groups-concept.md)를 참조하세요.

>[!Important]
> Exchange, 보안 및 규정 준수 센터 또는 SharePoint에 대한 관리 액세스 역할에 권한 있는 액세스 그룹을 할당하려면 권한 있는 액세스 그룹 환경이 아닌 Azure AD 포털 **역할 및 관리자** 환경을 사용하여 그룹에 활성화하기에 적합한 사용자 또는 그룹을 만듭니다.

## <a name="require-different-policies-for-each-role-assignable-group"></a>각 역할 할당 가능 그룹에 서로 다른 정책 필요

일부 조직에서는 Azure AD B2B 협업 같은 도구를 사용하여 파트너를 Azure AD 조직에 게스트로 초대합니다. 권한 있는 역할에 대한 모든 할당에 단일 Just-In-Time 정책을 사용하는 대신, 자체 정책을 사용하여 두 개의 서로 다른 권한 있는 액세스 그룹을 만들 수 있습니다. 신뢰할 수 있는 직원에게는 덜 엄격한 요구 사항을 적용하고, 할당된 그룹 활성화를 요청하는 파트너에게는 승인 워크플로와 같은 더 엄격한 요구 사항을 적용할 수 있습니다.

## <a name="activate-multiple-role-assignments-in-a-single-request"></a>단일 요청에서 여러 역할 할당 활성화

권한 있는 액세스 그룹 미리 보기를 사용하면 단일 Just-In-Time 요청을 통해 워크로드별 관리자에게 여러 역할에 대한 신속한 액세스 권한을 제공할 수 있습니다. 예를 들어, 계층 0 Office 관리자는 매일 인시던트를 철저히 조사하기 위해 Exchange 관리자, Office 앱 관리자, Teams 관리자 및 검색 관리자 역할에 대한 Just-In-Time 액세스가 필요할 수 있습니다. 이전에는 4회 연속 요청이 필요했으며, 이 프로세스에 다소 시간이 걸렸습니다. 이제는 “계층 0 Office 관리자”라는 역할 할당 가능 그룹을 만들고, 앞에서 설명한 네 가지 역할(또는 Azure AD 기본 제공 역할)에 할당하고, 그룹의 작업 섹션에서 권한 있는 액세스에 사용하도록 설정할 수 있습니다. 권한 있는 액세스에 사용하도록 설정하면 그룹의 멤버에 대한 Just-In-Time 설정을 구성하고 관리자 및 소유자를 적격 대상으로 할당할 수 있습니다. 관리자가 그룹으로 승격되면 네 가지 Azure AD 역할의 멤버가 됩니다.

## <a name="extend-and-renew-group-assignments"></a>그룹 할당 연장 및 갱신

시간이 제한된 소유자 또는 멤버 할당을 설정한 후 할당이 만료되면 어떻게 될까요? 이 새 버전에서는 이 시나리오에 대한 다음 두 가지 옵션이 제공됩니다.

- 연장 – 역할 할당이 만료될 시기가 다가오면 사용자는 Privileged Identity Management를 사용하여 역할 할당 연장을 요청할 수 있습니다.
- 갱신 – 역할 할당이 이미 만료된 경우 사용자는 Privileged Identity Management를 사용하여 역할 할당 갱신을 요청할 수 있습니다.

사용자가 시작하는 두 작업은 글로벌 관리자 또는 권한 있는 역할 관리자의 승인이 필요합니다. 관리자는 더 이상 만료를 관리하는 업무를 수행할 필요가 없습니다. 연장 또는 갱신 요청을 기다리다가 요청이 유효하면 요청을 승인하기만 하면 됩니다.

## <a name="next-steps"></a>다음 단계

- [권한 있는 액세스 그룹 소유자 또는 멤버 할당](groups-assign-member-owner.md)
- [권한 있는 액세스 그룹 멤버 및 소유자에 대한 활성화 요청 승인 또는 거부](groups-approval-workflow.md)
