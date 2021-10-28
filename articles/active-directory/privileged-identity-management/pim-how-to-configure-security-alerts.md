---
title: PIM에서 Azure AD 역할에 대한 보안 경고 - Azure AD | Microsoft Docs
description: Azure Active Directory Privileged Identity Management에서 Azure AD 역할에 대한 보안 경고를 구성합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 06/30/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87a31a810b82824f1d25d79e581e6dbd638bf5bb
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668868"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할에 대한 보안 경고 구성

PIM(Privileged Identity Management)은 사용자의 Azure AD(Azure Active Directory) 조직에 의심스럽거나 안전하지 않은 활동이 있을 때 경고를 생성합니다. 경고가 트리거되면 Privileged Identity Management 대시보드에 표시됩니다. 경고를 선택하여 경고를 트리거하는 사용자 또는 역할을 나열하는 보고서를 확인합니다.

![경고 및 해당 심각도의 목록이 포함된 '경고' 페이지를 보여 주는 스크린샷](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>보안 경고

이 섹션에서는 Azure AD 역할에 대한 모든 보안 경고와, 해결 방법 및 예방 방법을 나열합니다. 심각도의 의미는 다음과 같습니다.

- **높음**: 정책 위반으로 인해 즉각적인 조치를 요구합니다.
- **보통**: 즉각적인 조치를 요구하지는 않지만 잠재적으로 정책이 위반될 수 있음을 나타냅니다.
- **낮음**: 즉각적인 조치를 요구하지는 않지만 정책 변경을 제안합니다.

### <a name="administrators-arent-using-their-privileged-roles"></a>관리자가 권한 있는 역할을 사용하지 않음

심각도: **낮음**

| | Description |
| --- | --- |
| **이 경고가 표시된 이유는 무엇인가요?** | 사용자에게 필요 없는 권한 있는 역할이 할당되면 공격 위험이 증대됩니다. 또한 잘 사용되지 않는 계정에서는 공격자가 드러나지 않을 가능성이 더 큽니다. |
| **해결 방법** | 목록의 사용자를 검토하고 필요 없는 권한 있는 역할에서 제거합니다. |
| **방지** | 비즈니스 타당성이 있는 사용자에게만 권한 있는 역할을 할당합니다. </br>사용자가 액세스 권한이 계속 필요한지 확인하도록 정기적인 [액세스 검토](./pim-create-azure-ad-roles-and-resource-roles-review.md)를 예약합니다. |
| **포털 내 완화 작업** | 해당하는 권한 있는 역할에서 계정을 제거합니다. |
| **트리거** | 사용자가 역할을 활성화하지 않고 지정된 기간(일)을 경과하면 트리거됩니다. |
| **일 수** | 이 설정은 사용자가 역할을 활성화하지 않고 지낼 수 있는 최대 일 수를 0~100 사이에서 지정합니다.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>활성화할 역할에 다단계 인증이 필요하지 않음

심각도: **낮음**

| | Description |
| --- | --- |
| **이 경고가 표시된 이유는 무엇인가요?** | 다단계 인증을 사용하지 않으면 손상된 사용자가 권한 있는 역할을 활성화할 수 있습니다. |
| **해결 방법** | 역할 목록을 검토하고 모든 역할에 대해 [다단계 인증을 요구](pim-how-to-change-default-settings.md)합니다. |
| **방지** | [모든 역할에 대해 MFA를 요구합니다](pim-how-to-change-default-settings.md).  |
| **포털 내 완화 작업** | 권한 있는 역할을 활성화하는 데 다단계 인증이 필요하도록 지정합니다. |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>조직에 Azure AD Premium P2가 없음

심각도: **낮음**

| | Description |
| --- | --- |
| **이 경고가 표시된 이유는 무엇인가요?** | 현재 Azure AD 조직에 Azure AD Premium P2가 없습니다. |
| **해결 방법** | [Azure AD 버전](../fundamentals/active-directory-whatis.md)에 대한 정보를 검토합니다. Azure AD Premium P2로 업그레이드합니다. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>권한 있는 역할의 잠재적인 부실 계정

심각도: **중간**

| | Description |
| --- | --- |
| **이 경고가 표시된 이유는 무엇인가요?** | 권한 있는 역할의 계정이 지난 90일 동안 암호를 변경하지 않았습니다. 이러한 계정은 유지되지 않고 공격자에게 취약한 서비스 또는 공유 계정일 수 있습니다. |
| **해결 방법** | 목록에서 계정을 검토합니다. 액세스가 더 이상 필요하지 않으면 권한 있는 역할에서 제거합니다. |
| **방지** | 암호를 아는 사용자에게 변경 내용이 있으면 공유되는 계정에서 강력한 암호가 회전되는지 확인합니다. </br>정기적으로 [액세스 검토](./pim-create-azure-ad-roles-and-resource-roles-review.md)를 사용하여 권한 있는 역할이 있는 계정을 검토하고, 더 이상 필요하지 않은 역할 할당을 제거합니다. |
| **포털 내 완화 작업** | 해당하는 권한 있는 역할에서 계정을 제거합니다. |
| **모범 사례** | 암호를 사용하여 인증하고 전역 관리자 또는 보안 관리자와 같이 권한이 높은 관리 역할에 할당된 공유, 서비스 및 응급 액세스 계정은 다음과 같은 경우에 자체의 암호를 회전시켜야 합니다.<ul><li>관리 액세스 권한의 남용 또는 손상과 관련된 보안 인시던트 이후</li><li>사용자의 권한이 변경되어 더 이상 관리자가 아닌 경우(예: 관리자였던 직원이 IT 부서 또는 조직을 퇴직한 후)</li><li>IT 직원에 대한 알려진 위반 또는 변경 사항이 없는 경우에도 정기적으로(예: 분기별 또는 연간)</li></ul>여러 사용자가 이러한 계정의 자격 증명에 액세스할 수 있으므로 해당 역할을 떠난 사용자가 더 이상 계정에 액세스할 수 없도록 자격 증명을 회전시켜야 합니다. [계정 보호에 대한 자세한 내용](../roles/security-planning.md)을 알아보세요. |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>역할이 Privileged Identity Management 외부에서 할당됨

심각도: **높음**

| | Description |
| --- | --- |
| **이 경고가 표시된 이유는 무엇인가요?** | Privileged Identity Management 외부에서 수행된 권한 있는 역할 할당이 제대로 모니터링되지 않고 있으며 활성 공격을 나타낼 수 있습니다. |
| **해결 방법** | 목록의 사용자를 검토하고 권한 있는 역할에서 Privileged Identity Management 외부에서 할당된 역할을 제거합니다. 경고 및 경고와 함께 제공되는 메일 알림을 경고 설정에서 사용하거나 사용하지 않도록 설정할 수도 있습니다. |
| **방지** | Privileged Identity Management 외부에서 사용자에게 권한 있는 역할이 할당되는 위치를 조사하고 향후 해당 위치에서의 할당을 금지합니다. |
| **포털 내 완화 작업** | 해당 사용자를 권한 있는 역할에서 제거합니다. |

### <a name="there-are-too-many-global-administrators"></a>글로벌 관리자가 너무 많음

심각도: **낮음**

| | Description |
| --- | --- |
| **이 경고가 표시된 이유는 무엇인가요?** | 전역 관리자는 가장 높은 권한 있는 역할입니다. 전역 관리자가 손상되면 공격자가 모든 권한에 대한 액세스를 확보하게 되므로 시스템 전체가 위험합니다. |
| **해결 방법** | 목록에서 사용자를 검토하고 전역 관리자 역할이 절대적으로 필요하지 않은 모든 사용자를 제거합니다. </br>이러한 사용자에게 더 낮은 권한 있는 역할을 대신 할당합니다. |
| **방지** | 사용자에게 필요한 최소한의 권한 있는 역할을 할당합니다. |
| **포털 내 완화 작업** | 해당하는 권한 있는 역할에서 계정을 제거합니다. |
| **트리거** | 서로 다른 두 조건이 충족되고, 그 두 조건을 모두 구성할 수 있을 때 트리거됩니다. 첫째, 전역 관리자 역할 할당의 특정 임계값에 도달해야 합니다. 둘째, 총 역할 할당의 특정 비율이 전역 관리자여야 합니다. 이런 측정값의 하나만 충족된다면 경고가 표시되지 않습니다. |
| **최소 글로벌 관리자 수** | 이 설정은 사용자가 Azure AD 조직에 최소한으로 필요하다고 판단하는 전역 관리자 역할 할당 수를 2~100 사이로 지정합니다. |
| **글로벌 관리자 백분율** | 이 설정은 전역 관리자인 관리자의 최소 비율을 0%~100% 사이로 지정하며, 그러면 Azure AD 조직이 이 비율 아래로 떨어지지 않습니다. |

### <a name="roles-are-being-activated-too-frequently"></a>역할이 너무 자주 활성화됨

심각도: **낮음**

| | Description |
| --- | --- |
| **이 경고가 표시된 이유는 무엇인가요?** | 동일한 사용자를 통해 같은 권한 있는 역할에 대해 여러 번 활성화하는 것은 공격의 징후입니다. |
| **해결 방법** | 해당 권한 있는 역할에 대한 [활성화 기간](pim-how-to-change-default-settings.md)이 작업을 수행할 만큼 충분히 길게 설정되어 있는지 확인합니다. |
| **방지** | 해당 권한 있는 역할에 대한 [활성화 기간](pim-how-to-change-default-settings.md)이 작업을 수행할 만큼 충분히 길게 설정되어 있는지 확인합니다.</br>여러 관리자가 공유하는 계정을 갖는 권한 있는 역할에 대해 [다단계 인증을 요구](pim-how-to-change-default-settings.md)합니다. |
| **포털 내 완화 작업** | 해당 없음 |
| **트리거** | 사용자가 지정된 기간 내에 동일한 권한 있는 역할을 여러 번 활성화한다면 트리거됩니다. 기간 및 활성화 횟수를 모두 구성할 수 있습니다. |
| **활성화 갱신 기간** | 이 설정은 의심스러운 갱신을 추적하는 데 사용할 기간을 일, 시, 분, 초로 지정합니다. |
| **활성화 갱신 수** | 이 설정은 사용자가 선택한 기간 내에 통지를 받으려는 활성화 수를 2~100 사이로 지정합니다. 슬라이더를 이동하거나 텍스트 상자에 숫자를 입력하여 이 설정을 변경할 수 있습니다. |

## <a name="customize-security-alert-settings"></a>보안 경고 설정 사용자 지정

**경고** 페이지에서 **설정** 을 선택합니다.

![설정이 강조 표시된 경고 페이지](media/pim-how-to-configure-security-alerts/alert-settings.png)

환경 및 보안 목표로 작업하는 다양한 경고에서 설정을 사용자 지정합니다.

![설정을 사용하도록 설정하고 구성하는 경고에 대한 페이지 설정](media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 역할 설정 구성](pim-how-to-change-default-settings.md)