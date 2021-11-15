---
title: PIM에서 Azure 리소스 역할 설정 구성 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할 설정을 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2b3707fd3b9b56131d24274555761f616991600
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508743"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 리소스 역할 설정 구성

Azure 리소스 역할 설정을 구성할 때 Azure AD(Azure Active Directory) PIM(Privileged Identity Management)의 Azure 리소스 역할 할당에 적용되는 기본 설정을 정의합니다. 다음 절차에 따라 승인 워크플로를 구성하고 요청을 승인하거나 거부할 수 있는 사용자를 지정합니다.

## <a name="open-role-settings"></a>역할 설정 열기

다음 단계에 따라 Azure 리소스 역할에 대한 설정을 엽니다.

1. [권한 있는 역할 관리자](../roles/permissions-reference.md#privileged-role-administrator) 역할의 사용자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure 리소스** 를 선택합니다.

1. 관리하려는 리소스(예: 구독 또는 관리 그룹)를 선택합니다.

    ![관리할 수 있는 리소스를 나열하는 Azure 리소스 페이지](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. **설정** 을 선택합니다.

    ![Azure 리소스 역할을 나열하는 역할 설정 페이지](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. 설정을 구성하려는 역할을 선택합니다.

    ![몇 가지 할당 및 활성화 설정을 나열하는 역할 설정 세부 정보 페이지](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. **편집** 을 선택하여 **역할 설정 편집** 창을 엽니다. 첫 번째 탭에서는 Privileged Identity Management에서 역할 활성화에 대한 구성을 업데이트할 수 있습니다.

    ![활성화 탭이 열려 있는 역할 설정 편집 페이지](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. **할당** 탭 또는 페이지 맨 아래에 있는 **다음: 할당** 단추를 선택하여 할당 설정 탭을 엽니다. 이러한 설정은 Privileged Identity Management 인터페이스 내에서 수행되는 역할 할당을 제어합니다.

    ![역할 설정 페이지의 역할 할당 탭](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. **알림** 탭 또는 페이지 맨 아래에 있는 **다음: 활성화** 단추를 사용하여 이 역할에 대한 알림 설정 탭으로 이동합니다. 이러한 설정은 이 역할과 관련된 이메일 알림을 모두 제어합니다.

    ![역할 설정 페이지의 역할 알림 탭](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    역할 설정 페이지의 **알림** 탭에서 Privileged Identity Management를 사용하면 알림을 받는 사람과 수신하는 알림을 세밀하게 제어할 수 있습니다.

    - **이메일 비활성화**<br>기본 수신자 확인란을 선택 취소하고 추가 수신자를 삭제하여 특정 이메일을 비활성화할 수 있습니다.  

    - **지정된 이메일 주소로 이메일 제한**<br>기본 수신자 확인란을 선택을 취소하여 기본 수신자에게 보낸 이메일을 비활성화할 수 있습니다. 그런 다음 추가 수신자로 다른 이메일 주소를 추가할 수 있습니다. 이메일 주소를 둘 이상 추가하려면 세미콜론(;)을 사용하여 구분합니다.

    - **기본 수신자와 추가 수신자에게 모두 이메일 보내기**<br>기본 수신자 확인란을 선택하고 추가 수신자의 이메일 주소를 추가하여 기본 수신자와 추가 수신자에게 모두 이메일을 보낼 수 있습니다.

    - **중요 이메일만**<br>각 이메일 형식의 확인란을 선택하여 중요 이메일을 받을 수 있습니다. 즉, 이메일에 즉각적인 조치가 필요한 경우에만 Privileged Identity Management가 구성된 수신자에게 이메일을 계속 보냅니다. 예를 들어 사용자에게 역할 할당을 확장해 달라고 요청하는 이메일은 트리거되지 않지만 관리자가 확장 요청을 승인해야 하는 이메일은 트리거됩니다.

1. **업데이트** 단추를 선택하여 언제든지 역할 설정을 업데이트합니다.

## <a name="assignment-duration"></a>할당 기간

역할에 대한 설정을 구성할 때 각 할당 유형에 대한 2가지 할당 기간 옵션(적격 및 활성) 중에서 선택할 수 있습니다. 사용자가 Privileged Identity Management의 역할에 할당된 경우 이러한 옵션은 기본 최대 기간이 됩니다.

다음 **적격** 할당 기간 옵션 중 하나를 선택할 수 있습니다.

| | Description |
| --- | --- |
| **영구 적격 할당 허용** | 리소스 관리자는 영구 적격 할당을 할당할 수 있습니다. |
| **적격 할당 만료 기준 시간** | 리소스 관리자는 모든 적격 할당에 시작 및 종료 날짜가 지정되도록 요구할 수 있습니다. |

또한 다음 **활성** 할당 기간 옵션 중 하나를 선택할 수 있습니다.

| | Description |
| --- | --- |
| **영구 활성 할당 허용** | 리소스 관리자는 영구 활성 할당을 할당할 수 있습니다. |
| **활성 할당 만료 기준 시간** | 리소스 관리자는 모든 활성 할당에 시작 및 종료 날짜가 지정되도록 요구할 수 있습니다. |

> [!NOTE]
> 리소스 관리자는 지정된 종료 날짜가 있는 모든 할당을 갱신할 수 있습니다. 또한 사용자는 [역할 할당을 확장 또는 갱신](pim-resource-roles-renew-extend.md)하도록 셀프 서비스 요청을 시작할 수 있습니다.

## <a name="require-multifactor-authentication"></a>다단계 인증 필요

Privileged Identity Management는 서로 다른 두 가지 시나리오에 대해 선택적인 Azure AD Multi-Factor Authentication 적용을 제공합니다.

### <a name="on-active-assignment"></a>활성 할당 시

이 옵션을 사용하려면 관리자가 적격 역할 할당이 아닌 활성 역할을 만들기 전에 다단계 인증을 완료해야 합니다. 사용자가 할당된 순간부터 역할에서 이미 활성 상태이므로 해당 사용자가 자신의 역할 할당을 활성화할 때 Privileged Identity Management에서 다단계 인증을 적용할 수 없습니다.

활성 역할 할당을 만들 때 다단계 인증을 요구하려면 **활성 할당에 대한 Multi-Factor Authentication 필요** 상자를 선택하여 활성 할당에 다단계 인증을 적용할 수 있습니다.

### <a name="on-activation"></a>활성화 시

역할을 수행할 자격이 있는 사용자가 활성화하기 전에 Azure AD Multi-Factor Authentication을 사용하여 본인 인증을 하도록 요구할 수 있습니다. MFA(Multi-Factor Authentication)를 통해 사용자가 확실하게 본인 인증을 받을 수 있습니다. 이 옵션을 적용하면 사용자의 계정이 손상되었을 수 있는 상황에서 중요한 리소스를 보호할 수 있습니다.

활성화하기 전에 다단계 인증을 요구하려면 **활성화 시 Multi-Factor Authentication 필요** 확인란을 선택합니다.

자세한 내용은 [Multi-Factor Authentication 및 Privileged Identity Management](pim-how-to-require-mfa.md)를 참조하세요.

## <a name="activation-maximum-duration"></a>최대 활성화 기간

**최대 활성화 기간** 슬라이더를 사용하여 역할 할당이 만료되기 전에 활성 상태로 지속되는 최대 시간을 설정합니다. 이 값은 1~24시간일 수 있습니다.

## <a name="require-justification"></a>근거 필요

사용자 활성화 시 비즈니스 타당성을 입력하도록 요구할 수 있습니다. 근거를 요구하려면 **활성 할당에 대한 근거 필요** 확인란 또는 **활성화에 대한 근거 필요** 확인란을 선택합니다.

## <a name="require-approval-to-activate"></a>활성화할 승인 필요

역할을 활성화하기 위해 승인을 요구하려는 경우 다음 단계를 수행합니다.

1. **활성화하려면 승인 필요** 확인란을 선택합니다.

1. **승인자 선택** 을 선택하여 **구성원 또는 그룹 선택** 페이지를 엽니다.

    ![승인자를 선택할 사용자 또는 그룹 창 선택](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 하나 이상의 사용자 또는 그룹을 선택한 다음 **선택** 을 클릭합니다. 사용자 및 그룹의 조합을 추가할 수 있습니다. 하나 이상의 승인자를 선택해야 합니다. 기본 승인자가 없습니다.

    선택한 항목이 선택한 승인자 목록에 표시됩니다.

1. 모든 역할 설정을 지정한 후 **업데이트** 를 선택하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 할당](pim-resource-roles-assign-roles.md)
- [Privileged Identity Management에서 Azure 리소스 역할에 대한 보안 경고 구성](pim-resource-roles-configure-alerts.md)
