---
title: Privileged Identity Management에서 Azure 리소스 역할에 대한 보안 경고 구성 - Azure Active Directory | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure 리소스 역할에 대한 보안 경고를 구성하는 방법을 알아봅니다.
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
ms.openlocfilehash: ae012d6c6fb332914da8083b264c5dd0241e7cd6
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669913"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 리소스 역할에 대한 보안 경고 구성

PIM(Privileged Identity Management)은 사용자의 Azure AD(Azure Active Directory) 조직에 의심스럽거나 안전하지 않은 활동이 있을 때 경고를 생성합니다. 경고가 트리거될 때 경고 페이지에 표시됩니다.

![Azure 리소스 - 경고, 위험 수준, 수를 나열하는 경고 페이지](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>경고 검토

경고를 선택하여 수정 지침과 함께 경고를 트리거하는 사용자 또는 역할을 나열하는 보고서를 확인합니다.

![마지막 검사 시간, 설명, 완화 단계, 형식, 심각도, 보안 영향, 다음을 방지하는 방법을 표시하는 경고 보고서](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>경고

| 경고 | 심각도 | 트리거 | 권장 |
| --- | --- | --- | --- |
| **너무 많은 소유자를 리소스에 할당** |중간 |너무 많은 사용자가가 소유자 역할을 보유하고 있습니다. |목록에서 사용자를 검토하고 권한이 적은 역할에 다시 할당합니다. |
| **너무 많은 영구 소유자를 리소스에 할당** |중간 |너무 많은 사용자가 역할에 영구적으로 할당되었습니다. |목록에서 사용자를 검토하고 일부를 다시 할당하여 역할 사용을 위한 활성화를 요구합니다. |
| **만든 역할 중복** |중간 |여러 역할에는 동일한 조건이 있습니다. |이러한 역할 중 하나만 사용합니다. |

### <a name="severity"></a>심각도

- **높음**: 정책 위반으로 인해 즉각적인 조치를 요구합니다. 
- **보통**: 즉각적인 조치를 요구하지는 않지만 잠재적으로 정책이 위반될 수 있음을 나타냅니다.
- **낮음**: 즉각적인 조치를 요구하지는 않지만 정책 변경을 제안합니다.

## <a name="configure-security-alert-settings"></a>보안 경고 설정 구성

경고 페이지에서 **설정** 으로 이동합니다.

![설정이 강조 표시된 경고 페이지](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

환경 및 보안 목표로 작업하는 다양한 경고에서 설정을 사용자 지정합니다.

![설정을 사용하도록 설정하고 구성하는 경고에 대한 페이지 설정](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
