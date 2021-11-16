---
title: PIM에서 Azure AD 역할 설정 구성 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure AD 역할 설정을 구성하는 방법을 알아봅니다.
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
ms.openlocfilehash: 75624f84aa55c00bcdeab26505fa4f803d8a4f1f
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668507"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할 설정 구성

권한 있는 역할 관리자는 적격 역할 할당을 활성화한 사용자의 환경을 변경하는 등, 해당 Azure Active Directory(Azure AD) 조직에서 PIM(Privileged Identity Management)을 사용자 지정할 수 있습니다. 알림을 트리거하는 PIM 이벤트와 이를 수신하는 관리자에 대한 자세한 내용은 [Privileged Identity Management의 메일 알림](pim-email-notifications.md#notifications-for-azure-ad-roles)을 참조하세요.

## <a name="open-role-settings"></a>역할 설정 열기

다음 단계에 따라 Azure AD 역할에 대한 설정을 엽니다.

1. [권한 있는 관리자](../roles/permissions-reference.md#privileged-role-administrator) 역할의 사용자를 사용하여 [Azure Portal](https://portal.azure.com/) 에 로그인합니다.

1. **Azure AD Privileged Identity Management** &gt; **Azure AD 역할** &gt; **역할 설정** 을 엽니다.

    ![Azure AD 역할을 나열하는 역할 설정 페이지](./media/pim-how-to-change-default-settings/role-settings.png)

1. 설정을 구성하려는 역할을 선택합니다.

    ![몇 가지 할당 및 활성화 설정을 나열하는 역할 설정 세부 정보 페이지](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. **편집** 을 선택하여 역할 설정 페이지를 엽니다.

    ![할당 및 활성화 설정 업데이트 옵션을 사용하여 역할 설정 페이지 편집](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    각 역할에 대한 역할 설정 페이지에서 몇 가지 설정을 구성할 수 있습니다.

## <a name="assignment-duration"></a>할당 기간

역할에 대한 설정을 구성할 때 각 할당 유형에 대한 2가지 할당 기간 옵션(적격 및 활성) 중에서 선택할 수 있습니다. 사용자가 Privileged Identity Management의 역할에 할당된 경우 이러한 옵션은 기본 최대 기간이 됩니다.

다음 **적격** 할당 기간 옵션 중 하나를 선택할 수 있습니다.

| 설정 | 설명 |
| --- | --- |
| 영구 적격 할당 허용 | 전역 관리자 및 권한 있는 역할 관리자는 영구 적격 할당을 할당할 수 있습니다. |
| 적격 할당 만료 기준 시간 | 전역 관리자 및 권한 있는 역할 관리자가 모든 적격 할당에 지정된 시작 및 종료 날짜를 사용하도록 요구할 수 있습니다. |

또한 다음 **활성** 할당 기간 옵션 중 하나를 선택할 수 있습니다.

| 설정 | 설명 |
| --- | --- |
| 영구 활성 할당 허용 | 전역 관리자 및 권한 있는 역할 관리자는 영구 활성 할당을 할당할 수 있습니다. |
| 활성 할당 만료 기준 시간 | 전역 관리자 및 권한 있는 역할 관리자는 모든 활성 할당에 지정된 시작 및 종료 날짜를 포함하도록 요구할 수 있습니다. |

> [!NOTE]
> 지정된 종료 날짜가 있는 모든 할당은 전역 관리자 및 권한 있는 역할 관리자가 갱신할 수 있습니다. 또한 사용자는 [역할 할당을 확장 또는 갱신](pim-resource-roles-renew-extend.md)하도록 셀프 서비스 요청을 시작할 수 있습니다.

## <a name="require-multifactor-authentication"></a>다단계 인증 필요

Privileged Identity Management은 활성화 및 활성 할당 시 Azure AD Multi-Factor Authentication을 적용합니다.

### <a name="on-activation"></a>활성화 시

역할을 수행할 자격이 있는 사용자가 활성화하기 전에 Azure AD Multi-Factor Authentication을 사용하여 본인 인증을 하도록 요구할 수 있습니다. MFA(Multi-Factor Authentication)를 통해 사용자가 확실하게 본인 인증을 받을 수 있습니다. 이 옵션을 적용하면 사용자의 계정이 손상되었을 수 있는 상황에서 중요한 리소스를 보호할 수 있습니다.

역할 할당을 활성화하기 전에 다단계 인증을 요구하려면 **역할 편집 설정** 의 활성화 탭에서 **활성화 시 Azure MFA 필요** 옵션을 선택합니다.

### <a name="on-active-assignment"></a>활성 할당 시

이 옵션을 사용하려면 관리자가 적격 역할 할당이 아닌 활성 역할을 만들기 전에 다단계 인증을 완료해야 합니다. 사용자가 할당된 순간부터 역할에서 이미 활성 상태이므로 해당 사용자가 자신의 역할 할당을 사용할 때 Privileged Identity Management에서 다단계 인증을 적용할 수 없습니다.

활성 역할 할당을 만들 때 다단계 인증을 요구하려면 **역할 편집 설정** 의 할당 탭에서 **활성 할당에 Multi-Factor Authentication 필요** 옵션을 선택합니다.

자세한 내용은 [Multi-Factor Authentication 및 Privileged Identity Management](pim-how-to-require-mfa.md)를 참조하세요.

## <a name="activation-maximum-duration"></a>최대 활성화 기간

**최대 활성화 기간** 슬라이더를 사용하여 역할이 만료되기 전에 활성 상태로 지속되는 최대 시간을 설정합니다. 이 값은 1~24시간일 수 있습니다.

## <a name="require-justification"></a>근거 필요

사용자 활성화 시 비즈니스 타당성을 입력하도록 요구할 수 있습니다. 근거를 요구하려면 **활성 할당에 대한 근거 필요** 확인란 또는 **활성화에 대한 근거 필요** 확인란을 선택합니다.

## <a name="require-approval-to-activate"></a>활성화할 승인 필요

여러 승인자를 설정하는 경우 승인자 중 한 명이 승인되거나 거부되는 즉시 승인이 완료됩니다. 두 번째 또는 후속 승인자가 강제로 승인하도록 할 수 없습니다. 역할을 활성화하기 위해 승인을 요구하려는 경우 다음 단계를 따릅니다.

1. **활성화하려면 승인 필요** 확인란을 선택합니다.

1. **승인자 선택** 을 선택합니다.

    ![승인자를 선택할 사용자 또는 그룹 창 선택](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. 사용자를 하나 이상 선택한 다음 **선택** 을 클릭합니다. 하나 이상의 승인자를 선택합니다. 특정 승인자를 선택하지 않으면 권한 있는 역할 관리자 및 전역 관리자가 기본 승인자가 됩니다.

1. **업데이트** 를 선택하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management의 Azure AD 역할에 대한 보안 경고 구성](pim-how-to-configure-security-alerts.md)
