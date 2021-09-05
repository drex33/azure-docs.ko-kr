---
title: Azure 서비스에 대한 권장된 정책
description: Azure Virtual Machines와 같은 Azure 서비스에 권장된 정책을 찾고 적용하는 방법을 설명합니다.
ms.date: 08/17/2021
ms.topic: conceptual
ms.custom: generated
ms.openlocfilehash: 06f68dec2c2a1ec8fa717dc721916c070a653b10
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538662"
---
# <a name="recommended-policies-for-azure-services"></a>Azure 서비스에 대한 권장된 정책

Azure Policy를 처음 접하는 고객은 리소스를 관리하는 일반 정책 정의를 찾는 경우가 종종 있습니다. Azure Policy **권장된 정책** 은 우선 일반 정책 정의에 초점을 맞춘 목록을 제공합니다. 지원되는 리소스에 대한 **권장된 정책** 환경은 해당 리소스에 대한 포털 환경에 포함됩니다.

추가 Azure Policy 기본 제공 기능은 [Azure Policy 기본 제공 정의](../samples/built-in-policies.md)를 참조하세요.

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

[Azure Virtual Machines](../../../virtual-machines/index.yml)에 대한 **권장된 정책은** 가상 머신의 **개요** 페이지와 **기능** 탭 아래에 있습니다. _Azure Policy_ 카드에서 "구성되지 않음" 또는 "#할당" 텍스트를 선택하여 권장된 정책으로 측면 창을 엽니다. 가상 머신이 멤버인 범위에 이미 할당된 모든 정책 정의는 회색으로 표시됩니다. 이 가상 머신에 적용할 권장된 정책을 선택하고 **정책 할당** 을 선택하여 각 머신에 대한 할당을 만듭니다.

조직에서 [리소스 및 리소스 계층 구조를 구성](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions)하여 완성하는 경우, 정책 할당을 리소스 당 하나에서 구독 또는 [관리 그룹](../../management-groups/index.yml) 수준으로 전환하는 것이 좋습니다.

### <a name="azure-virtual-machines-recommended-policies"></a>Azure Virtual Machines 권장 정책

|이름<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[재해 복구가 구성되어 있지 않은 가상 머신 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |재해 복구가 구성되지 않은 가상 머신을 감사합니다. 재해 복구에 대한 자세한 내용은 [https://aka.ms/asr-doc](../../../site-recovery/index.yml)를 참조하세요. |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[관리 디스크를 사용하지 않는 VM 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |이 정책은 관리 디스크를 사용하지 않는 VM을 감사합니다. |감사 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[Virtual Machines에 Azure Backup을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Azure Backup을 사용하도록 설정하여 Azure Virtual Machines를 보호하세요. Azure Backup은 Azure를 위한 안전하고 경제적인 데이터 보호 솔루션입니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [정책 효과 이해](./effects.md)를 검토합니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
