---
title: PIM에서 Azure 리소스 역할 할당 갱신 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할 할당을 확장하거나 갱신하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 10/19/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87b8fb2eb7f6301762fcba9931dfd9da6c54b679
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130253679"
---
# <a name="extend-or-renew-azure-resource-role-assignments-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 리소스 역할 할당 확장 또는 갱신

Azure AD(Azure Active Directory) PIM(Privileged Identity Management)은 Azure 리소스에 대한 액세스 및 할당 수명 주기를 관리하는 컨트롤을 제공합니다. 관리자는 시작 및 종료 날짜-시간 속성을 사용하여 역할을 할당할 수 있습니다. 할당 종료에 도달하면 Privileged Identity Management에서 영향을 받는 사용자 또는 그룹에 메일 알림을 보냅니다. 또한 리소스 관리자에게도 적절한 액세스가 유지되도록 메일 알림을 보냅니다. 액세스가 연장되지 않더라도 할당이 갱신되어 만료된 상태에서 최대 30일 동안 표시됩니다.

## <a name="who-can-extend-and-renew"></a>연장 및 갱신할 수 있는 사람

리소스 관리자만 역할 할당을 연장하거나 갱신할 수 있습니다. 영향을 받는 사용자 또는 그룹은 만료 예정인 역할을 연장하도록 요청하고 이미 만료된 역할의 갱신을 요청할 수 있습니다.

## <a name="when-are-notifications-sent"></a>알림이 전송되는 시기

Privileged Identity Management에서 관리자와 영향을 받는 역할의 사용자 또는 그룹에 대해 만료 14일 이내 및 만료 하루 전에 메일 알림을 보냅니다. 할당이 공식적으로 만료되면 추가 메일이 전송됩니다.

만료될 예정이거나 만료된 역할을 할당한 사용자 또는 그룹이 연장이나 갱신을 요청하면 관리자에게 알림이 전송됩니다. 특정 관리자가 요청을 해결하면 다른 모든 관리자에게 해결 결정(승인 또는 거부)이 통지됩니다. 그러면 요청하는 사용자 또는 그룹에 결정에 대한 알림이 표시됩니다.

## <a name="extend-role-assignments"></a>역할 할당 연장

다음 단계에서는 역할 할당의 연장 또는 갱신을 요청, 해결 또는 관리에 대한 프로세스를 간략하게 설명합니다.

### <a name="self-extend-expiring-assignments"></a>만료되는 할당 자체 연장

역할에 할당된 사용자는 리소스의 **내 역할** 페이지에 있는 **적격** 또는 **활성** 탭 또는 Privileged Identity Management 포털의 최상위 수준 **내 역할** 페이지에서 만료되는 역할 할당을 직접 연장할 수 있습니다. 포털에서 사용자는 앞으로 14일 이내에 만료될 적격 및 활성(할당됨) 역할을 연장하도록 요청할 수 있습니다.

![Azure 리소스 - 작업 열이 있는 적격 역할을 나열하는 내 역할 페이지](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-ui.png)

할당 종료 날짜-시간이 14일 이내이면 **연장** 에 대한 링크가 Azure Portal에서 활성화됩니다. 다음 예제에서는 현재 날짜가 3월 27일이라고 가정합니다.

>[!Note]
>역할에 할당된 그룹의 경우 상속된 할당이 있는 사용자가 그룹 할당을 확장할 수 없도록 **확장** 링크를 사용할 수 없게 됩니다.

![활성화 또는 연장 링크를 포함하는 작업 열](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-within-14.png)

이 역할 할당의 연장을 요청하려면 **연장** 을 선택하여 요청 양식을 엽니다.

![이유 상자를 사용하여 역할 할당 창 연장](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-role-assignment-request.png)

원래 할당에 대한 정보를 보려면 **할당 세부 정보** 를 펼칩니다. 연장 요청의 이유를 입력하고 **연장** 을 선택합니다.

>[!NOTE]
>연장이 필요한 이유와 부여되어야 하는 연장 기간(이 정보가 있는 경우)에 대한 세부 정보를 포함시키는 것이 좋습니다.

![할당 세부 정보가 확장된 역할 할당 창 연장](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-form-complete.png)

잠시 후 리소스 관리자는 연장 요청을 검토하도록 요청하는 메일 알림을 받게 됩니다. 연장 요청이 이미 제출된 경우 Azure 알림이 포털에 표시됩니다.

![기존 보류 중인 역할 할당 확장이 이미 있음을 설명하는 알림](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-failed-existing-request.png)

**보류 중인 요청** 페이지로 이동하여 요청 상태를 보거나 취소합니다.

![Azure 리소스 - 보류 중인 요청 및 취소 링크를 나열하는 보류 중인 요청 페이지](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-cancel-request.png)

### <a name="admin-approved-extension"></a>관리자 승인된 연장

사용자 또는 그룹이 역할 할당을 연장하도록 요청을 제출하면 리소스 관리자는 원래 할당의 세부 정보와 요청에 대한 이유가 포함된 메일 알림을 받습니다. 알림에는 관리자가 요청을 승인하거나 거부할 수 있는 직접 링크가 포함됩니다.

관리자는 메일의 링크를 사용하는 것 외에도 Privileged Identity Management 관리 포털로 이동하고 왼쪽 창에서 **요청 승인** 을 선택하여 요청을 승인하거나 거부할 수 있습니다.

![Azure 리소스 - 승인 또는 거부할 요청 및 링크를 나열하는 요청 승인 페이지](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

관리자가 **승인** 또는 **거부** 를 선택하면 요청의 세부 정보가 감사 로그에 대한 비즈니스 타당성을 제공하는 필드와 함께 표시됩니다.

![요청자 이유, 할당 유형, 시작 시간, 종료 시간, 이유를 사용하여 역할 할당 요청 승인](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

역할 할당 연장에 대한 요청을 승인할 때 리소스 관리자는 새로운 시작 날짜 및 종료 날짜 및 할당 유형을 선택할 수 있습니다. 관리자가 특정 작업을 완료하도록 제한된 액세스를 제공하려는 경우 할당 유형 변경이 필요할 수 있습니다(예: 하루). 이 예제에서는 관리자가 **적격** 에서 **활성** 으로 할당을 변경할 수 있습니다. 즉, 활성화를 요구하지 않고도 요청자에게 액세스 권한을 제공할 수 있습니다.

### <a name="admin-initiated-extension"></a>관리자가 시작한 연장

역할에 할당된 사용자가 역할 할당에 대한 연장을 요청하지 않는 경우 관리자는 사용자를 대신하여 할당을 연장할 수 있습니다. 역할 할당을 관리자가 연장하면 승인이 필요하지 않지만 역할이 연장되면 다른 모든 관리자에게 알림이 전송됩니다.

역할 할당을 연장하려면 Privileged Identity Management에서 리소스 역할 또는 할당 보기로 이동합니다. 연장해야 하는 할당을 찾습니다. 그런 다음 작업 열에서 **연장** 을 선택합니다.

![Azure 리소스 - 연장할 링크가 있는 적격 역할을 나열하는 할당 페이지](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-extend.png)

## <a name="renew-role-assignments"></a>역할 할당 갱신

만료된 역할 할당을 갱신하는 프로세스는 연장 요청 프로세스와 개념적으로는 유사하지만 다릅니다. 다음 단계를 통해 필요한 경우 할당 및 관리자가 만료된 역할에 대한 액세스를 갱신할 수 있습니다.

### <a name="self-renew"></a>자체 갱신

리소스에 더 이상 액세스할 수 없는 사용자는 최대 30일까지 만료된 할당 내역에 액세스할 수 있습니다. 이렇게 하려면 왼쪽 창에서 **내 역할** 로 이동하고 Azure 리소스 역할 섹션에서 **만료된 역할** 탭을 선택합니다.

![내 역할 페이지 - 만료된 역할 탭](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-myroles.png)

표시되는 역할 목록의 기본값은 **적격 역할** 입니다. 드롭다운 메뉴를 사용하여 적격 및 활성 할당 역할을 전환합니다.

목록에서 역할 할당에 대한 갱신을 요청하려면 **갱신** 작업을 선택합니다. 그런 다음 요청 이유를 제공합니다. 리소스 관리자가 승인이나 거부를 결정하는 데 도움이 되도록 추가 컨텍스트 또는 비즈니스 타당성뿐만 아니라 기간을 제공하는 것이 좋습니다.

![이유 상자를 표시하는 역할 할당 창 갱신](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-request-form.png)

요청을 제출한 후 리소스 관리자에게 역할 할당을 갱신하도록 보류 중인 요청이 통지됩니다.

### <a name="admin-approves"></a>관리자 승인

리소스 관리자는 메일 알림의 링크를 사용하거나 Azure Portal에서 Privileged Identity Management에 액세스하고 왼쪽 창에서 **요청 승인** 을 선택하여 갱신 요청에 액세스할 수 있습니다.

![Azure 리소스 - 승인 또는 거부할 요청 및 링크를 나열하는 요청 승인 페이지](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-grid.png)

관리자가 **승인** 또는 **거부** 를 선택하면 요청의 세부 정보가 감사 로그에 대한 비즈니스 타당성을 제공하는 필드와 함께 표시됩니다.

![요청자 이유, 할당 유형, 시작 시간, 종료 시간, 이유를 사용하여 역할 할당 요청 승인](media/pim-resource-roles-renew-extend/aadpim-rbac-extend-admin-approve-blade.png)

역할 할당 갱신에 대한 요청을 승인할 때 리소스 관리자는 새로운 시작 날짜, 종료 날짜 및 할당 유형을 입력해야 합니다.

### <a name="admin-renew"></a>관리자 갱신

리소스 관리자는 리소스의 왼쪽 탐색 메뉴에 있는 **멤버** 탭에서 만료된 역할 할당을 갱신할 수 있습니다. 또한 리소스 역할의 **만료된 역할** 탭 내에서도 만료된 역할 할당을 갱신할 수 있습니다.

**멤버** 화면에서 만료된 모든 역할 할당 목록을 보려면 **만료된 역할** 을 선택합니다.

![Azure 리소스 - 갱신할 링크가 있는 만료된 역할을 나열하는 멤버 페이지](media/pim-resource-roles-renew-extend/aadpim-rbac-renew-from-member-blade.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할에 대한 요청 승인 또는 거부](pim-resource-roles-approval-workflow.md)
- [Privileged Identity Management에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
