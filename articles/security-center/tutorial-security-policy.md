---
title: 보안 정책 작업 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 보안 정책을 사용하는 방법을 설명합니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: memildin
ms.openlocfilehash: b7c8003bd40df21fb749fcb576afeaec5dacead9
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112236844"
---
# <a name="manage-security-policies"></a>보안 정책 관리

이 문서에서는 보안 정책을 구성하는 방법 및 Security Center에서 보안 정책을 보는 방법을 설명합니다. 

이니셔티브, 정책 및 권장 사항 간의 관계를 이해하려면 [보안 정책, 이니셔티브 및 권장 사항이란?](security-policy-concept.md)을 참조하십시오.

## <a name="who-can-edit-security-policies"></a>보안 정책은 누가 편집할 수 있나요?

Security Center는 Azure에서 사용자, 그룹 및 서비스에 할당할 수 있는 기본 제공 역할을 제공하는 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용합니다. 사용자가 Security Center를 열면 액세스 권한이 있는 리소스와 관련된 정보만 표시됩니다. 즉, 리소스의 구독에 대한 *소유자*, *기여자*, 또는 *읽기 권한자* 역할이 사용자에게 할당됩니다. 또한 두 가지의 특수한 Security Center 역할이 있습니다.

- **보안 읽기 권한자**: 권장 사항, 경고, 정책 및 상태와 같은 Security Center 항목을 볼 수 있는 권한을 가집니다. 변경할 수 없습니다.
- **보안 관리자**: *보안 읽기 권한자* 와 동일한 보기 권한을 가집니다. 보안 정책을 업데이트하고 경고를 해제할 수도 있습니다.

REST API를 통해 또는 Windows PowerShell을 사용하여 Azure Policy 포털을 통해 보안 정책을 편집할 수 있습니다.

## <a name="manage-your-security-policies"></a>보안 정책 관리

Security Center에서 보안 정책을 보려면

1. **Security Center** 대시보드에서 **보안 정책** 을 선택합니다.

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="정책 관리 페이지.":::

   **정책 관리** 화면에서 관리 그룹 구조뿐만 아니라 관리 그룹, 구독 및 작업 영역 수를 확인할 수 있습니다.

1. 확인하려는 구독 또는 관리 그룹의 정책을 선택합니다.

1. 해당 구독 또는 관리 그룹에 대한 보안 정책 페이지가 나타납니다. 사용 가능한 정책과 할당된 정책을 표시합니다.

    :::image type="content" source="./media/tutorial-security-policy/security-policy-page.png" alt-text="Security Center의 보안 정책 페이지" lightbox="./media/tutorial-security-policy/security-policy-page.png":::

    > [!NOTE]
    > 기본 정책과 함께 "MG 상속됨"이라는 레이블이 있으면 정책이 관리 그룹에 할당되고 사용자가 보고 있는 구독에서 상속된 것입니다.

1. 이 페이지에서 사용할 수 있는 옵션 중에서 선택합니다.

    1. 업계 표준을 사용하려면 **표준 추가** 를 선택합니다. 자세한 정보는 [규정 준수 대시보드에 표준 집합 사용자 지정](update-regulatory-compliance-packages.md)을 참조하세요.

    1. 사용자 지정 이니셔티브를 할당하고 관리하려면 **사용자 지정 이니셔티브 추가** 를 선택합니다. 자세한 정보는 [사용자 지정 보안 이니셔티브 및 정책 사용](custom-security-policies.md)을 참조하세요.

    1. 기본 이니셔티브를 보고 편집하려면 **실제 정책 보기** 를 선택하고 아래에 설명된 대로 진행합니다. 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="실제 정책 화면.":::

       이런 **보안 정책** 화면에는 선택한 구독 또는 관리 그룹에 할당된 정책에서 수행된 활동이 표시됩니다.
       
       * 위에서 구독 또는 관리 그룹에 적용된 각 정책 **할당** 을 열기 위해 제공된 링크를 사용합니다. 해당 링크를 사용하여 할당에 액세스하고 정책을 편집하거나 사용하지 않도록 설정할 수 있습니다. 예를 들어 특정 정책 할당이 Endpoint Protection을 효과적으로 거부하는 것으로 확인되면 이 링크를 사용하여 정책을 편집 하거나 사용하지 않도록 설정합니다.
       
       * 정책 목록에서 구독 또는 관리 그룹에 정책을 효과적으로 적용하는 것을 확인할 수 있습니다. 범위에 적용되는 각 정책의 설정이 고려되고 정책에서 수행되는 작업의 누적 결과가 제공됩니다. 예를 들어 하나의 할당에서 정책이 사용하지 않도록 설정되지만 기타 할당에서 정책이 AuditIfNotExist로 설정되는 경우 누적 효과는 AuditIfNotExist로 적용됩니다. 더 활성화된 효과가 항상 우선합니다.
       
       * 정책의 효과는 Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled일 수 있습니다. 효과를 적용하는 방법에 대한 자세한 내용은 [Policy 효과 이해](../governance/policy/concepts/effects.md)를 참조하세요.

       > [!NOTE]
       > 할당된 정책을 보는 경우 여러 할당을 확인할 수 있으며 각 할당이 자체적으로 구성되는 방법도 확인할 수 있습니다.


## <a name="disable-security-policies-and-disable-recommendations"></a>보안 정책 미사용 및 권장 구성 미사용

권장 사항이 작업 환경과 관련이 없는 경우 해당 권장 구성을 다시 표시하지 않도록 할 수 있습니다. 권장 사항을 사용하지 않도록 설정하려면 권장 구성을 생성하는 특정 정책을 사용하지 않도록 설정합니다.

사용하지 않도록 설정할 권장 사항은 Security Center의 규정 준수 도구를 사용하여 적용한 규정 표준에 필요한 경우에는 여전히 표시됩니다. 기본 제공 이니셔티브에서 정책을 사용하지 않도록 설정한 경우에도 규정 표준 이니셔티브의 정책은 규정 준수에 필요한 경우 계속해서 권장 사항을 트리거합니다. 규제 표준 이니셔티브에서 정책을 사용하지 않도록 설정할 수 없습니다.

권장 사항에 대한 자세한 정보는 [보안 권장 사항 관리](security-center-recommendations.md)를 참조하세요.

1. Security Center의 **정책 및 준수** 섹션에서 **보안 정책** 을 선택합니다.

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="Azure Security Center에서 정책 관리 프로세스 시작.":::

1. 사용하지 않으려는 권장 사항(및 정책)에 대해 구독 또는 관리 그룹을 선택합니다.

   > [!NOTE]
   > 관리 그룹은 해당 구독에 정책을 적용합니다. 따라서 정책을 사용하지 않도록 설정한 구독이 동일한 정책을 사용하는 관리 그룹에 속해 있는 경우 정책 권장 사항을 계속 받게 됩니다. 관리 수준에서 계속 정책이 적용되고 권장 사항이 생성됩니다.

1. **Security Center 기본 정책**, **업계 및 규제 표준**, 또는 **사용자 지정 이니셔티브** 섹션에서 사용하지 않으려는 정책을 포함하는 관련 이니셔티브를 선택합니다.

1. **매개 변수** 섹션을 열고 사용하지 않도록 설정할 권장 사항을 호출하는 정책을 검색합니다.

1. 드롭다운 목록에서 해당 정책에 대한 값을 **사용하지 않음** 으로 변경합니다.

   ![정책을 사용하지 않도록 설정합니다.](./media/tutorial-security-policy/disable-policy.png)

1. **저장** 을 선택합니다.

   > [!NOTE]
   > 이 변경의 효력이 발생하는 데 최대 12시간이 걸릴 수 있습니다.


## <a name="enable-a-security-policy"></a>보안 정책 사용

이니셔티브의 일부 정책은 기본값으로 사용하지 않도록 설정될 수 있습니다. 예를 들어 Azure Security 벤치마크 이니셔티브에서 일부 정책은 조직의 특정 규정 또는 규정 준수 요구 사항을 충족하는 경우에만 사용할 수 있도록 제공됩니다. 이러한 정책에는 고객 관리 키를 사용하여 미사용 데이터를 암호화하기 위한 권장 사항이 포함되어 있습니다. 예를 들어 "컨테이너 레지스트리는 CMK(고객 관리형 키)로 암호화해야 합니다" 같은 사항입니다.

사용하지 않도록 설정된 정책을 사용하도록 설정하고 리소스에 대해 평가되었는지 확인하려면 다음을 수행합니다.

1. Security Center의 **정책 및 준수** 섹션에서 **보안 정책** 을 선택합니다.

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="Azure Security Center에서 정책 관리 프로세스 시작.":::

1. 권장 사항(및 정책)을 사용하려는 구독 또는 관리 그룹을 선택합니다.

1. **Security Center 기본 정책**, **업계 및 규제 표준**, 또는 **사용자 지정 이니셔티브** 섹션에서 사용하고자 하는 정책이 포함된 관련 이니셔티브를 선택합니다.

1. **매개 변수** 섹션을 열고 사용하지 않도록 설정할 권장 사항을 호출하는 정책을 검색합니다.

1. 드롭다운 목록에서 해당 정책에 대한 값을 **AuditIfNotExists** 또는 **적용** 으로 변경합니다.

1. **저장** 을 선택합니다.

   > [!NOTE]
   > 이 변경의 효력이 발생하는 데 최대 12시간이 걸릴 수 있습니다.


## <a name="next-steps"></a>다음 단계
이 페이지에서는 보안 정책에 대해 설명했습니다. 관련 정보는 다음 페이지를 참조하세요.

- [PowerShell을 사용하여 정책을 설정하는 방법 알아보기](../governance/policy/assign-policy-powershell.md)
- [Azure Policy에서 보안 정책을 편집하는 방법](../governance/policy/tutorials/create-and-manage.md)
- [Azure Policy를 사용하여 구독 또는 관리 그룹에서 정책을 설정하는 방법 알아보기](../governance/policy/overview.md)
- [관리 그룹의 모든 구독에서 Security Center 사용하는 방법 알아보기](onboard-management-group.md)