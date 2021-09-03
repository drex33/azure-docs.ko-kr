---
title: Azure Policy를 사용하여 VM 생성 시 백업 자동 활성화
description: Azure Policy 사용하여 지정된 범위에서 만든 모든 VM에 대한 백업을 자동으로 사용하는 방법을 설명하는 문서
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: fe70ba544bb75cee7faf3a4eb3aa2f5ef440909e
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2021
ms.locfileid: "113300613"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Azure Policy를 사용하여 VM 생성 시 백업 자동 활성화

조직에서 백업 또는 준수 관리자의 주요 업무 중 하나는 업무상 중요한 모든 컴퓨터가 적절한 보존 기간으로 백업되도록 하는 것입니다.

현재 Azure Backup에서는 다양한 기본 제공 정책을 제공([Azure Policy](../governance/policy/overview.md) 사용)하여 Azure Virtual Machines가 백업용으로 구성되었는지 자동으로 확인할 수 있습니다. 백업 팀과 리소스를 구성하는 방법에 따라 아래 정책 중 하나를 사용할 수 있습니다.

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>정책 1 - 동일한 위치에 있는 기존 복구 서비스 자격 증명 모음에 대해 지정된 태그가 없는 VM의 백업 구성

조직에 응용 프로그램 팀 간의 백업을 관리하는 중앙 백업 팀이 있는 경우 이 정책을 사용하여 관리되는 VM과 동일한 구독 및 위치에 있는 기존 중앙 Recovery Services 자격 증명 모음에 백업을 구성할 수 있습니다. 이 정책의 범위에서 특정 태그가 포함된 VM을 **제외** 하도록 선택할 수 있습니다.

## <a name="policy-2---configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>정책 2 - 동일한 위치에 있는 기존 Recovery Services 자격 증명 모음에 대해 지정된 태그가 있는 VM의 백업 구성
이 정책은 위의 정책 1과 동일하게 작동합니다. 유일한 차이점은 이 정책의 범위에서 특정 태그를 포함하는 VM을 **포함** 하는 데 이 정책을 사용할 수 있다는 것입니다. 

## <a name="policy-3---configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>정책 3 - 기본 정책을 사용하여 새 Recovery Services 자격 증명 모음에 대해 제공된 태그가 없는 VM의 백업 구성
전용 리소스 그룹에서 응용 프로그램을 구성하고 동일한 자격 증명 모음에서 응용 프로그램을 백업하려는 경우 이 정책을 사용하면 이 작업을 자동으로 관리할 수 있습니다. 이 정책의 범위에서 특정 태그가 포함된 VM을 **제외** 하도록 선택할 수 있습니다.

## <a name="policy-4---configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>정책 4 - 기본 정책을 사용하여 새 Recovery Services 자격 증명 모음에 대해 지정된 태그가 있는 VM의 백업 구성
이 정책은 위의 정책 3과 동일하게 작동합니다. 유일한 차이점은 이 정책의 범위에서 특정 태그를 포함하는 VM을 **포함** 하는 데 이 정책을 사용할 수 있다는 것입니다. 

Azure Backup은 위의 기능 외에 [감사 전용](../governance/policy/concepts/effects.md#audit) 정책도 제공합니다. **Virtual Machines에 대해 Azure Backup을 사용하도록 설정해야 합니다**. 이 정책은 백업을 사용하도록 설정하지 않은 가상 머신을 식별하지만 이러한 VM에 대한 백업을 자동으로 구성하지는 않습니다. 이는 VM의 전반적인 규정 준수를 평가만 하고 즉시 조치를 취하지는 않으려는 경우에 유용합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* 기본 제공 정책은 현재 Azure VM에 대해서만 지원됩니다. 사용자는 할당 중에 지정된 보존 정책이 VM 보존 정책인지 확인해야 합니다. 이 정책에서 지원하는 모든 VM SKU를 보려면 [이](./backup-azure-policy-supported-skus.md) 문서를 참조하세요.

* 정책 1과 2는 한 번에 하나의 위치와 구독에 할당할 수 있습니다. 위치 및 구독 전체에서 VM에 대한 백업을 사용하도록 설정하려면 각 위치 및 구독 조합에 대해 하나씩, 정책 할당의 인스턴스를 여러 개 만들어야 합니다.

* 정책 1과 2의 경우 현재 관리 그룹 범위가 지원되지 않습니다.

* 정책 1과 2의 경우 지정된 자격 증명 모음과 백업을 위해 구성된 VM은 다른 리소스 그룹에 있을 수 있습니다.

* 정책 3과 4는한 번에 하나의 구독(또는 구독 내의 리소스 그룹)에 할당할 수 있습니다.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>기본 제공 정책 사용

아래 단계에서는 지정된 범위에 정책 1: **동일한 위치에 있는 기존 복구 서비스 자격 증명 모음에 대해 지정된 태그가 없는 VM의 백업 구성** 을 할당하는 엔드투엔드 프로세스를 설명합니다. 다른 정책에도 유사한 지침이 적용됩니다. 할당되면 범위에서 만들어진 모든 새 VM이 자동으로 백업을 위해 구성됩니다.

1. Azure Portal에 로그인하고 **정책** 대시보드로 이동합니다.
2. 왼쪽 메뉴에서 **정의** 를 선택하여 Azure 리소스의 모든 기본 제공 정책 목록을 가져옵니다.
3. 목록을 **범주=백업** 으로 필터링하고 '특정 위치에 있는 VM을 동일한 위치에 있는 기존 중앙 자격 증명 모음으로 백업하는 작업 구성'이라는 정책을 선택합니다.
![정책 대시보드](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. 정책 이름을 선택합니다. 이 정책에 대한 세부 정의로 리디렉션됩니다.
![정책 정의 창](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. 창 위쪽에 있는 **할당** 단추를 선택합니다. 그러면 **정책 할당** 창으로 리디렉션됩니다.
6. **기본** 에서 **범위** 필드 옆에 있는 세 개의 점을 선택합니다. 그러면 적용할 정책의 구독을 선택할 수 있는 올바른 컨텍스트 창이 열립니다. 또한 필요에 따라 리소스 그룹을 선택하여 특정 리소스 그룹의 VM에만 정책이 적용되도록 할 수 있습니다.
![정책 할당 기본 사항](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. **매개 변수** 탭의 드롭다운에서 위치를 선택하고 범위의 VM이 연결해야 하는 자격 증명 모음 및 백업 정책을 선택합니다. 태그 이름 및 태그 값의 배열을 지정하도록 선택할 수도 있습니다. 지정된 태그에 지정된 값을 포함하는 VM은 정책 할당의 범위에서 제외됩니다.
![정책 할당 매개 변수](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. **효과** 가 deployIfNotExists로 설정되었는지 확인합니다.
9. **검토 + 만들기** 로 이동하고 **만들기** 를 선택합니다.

> [!NOTE]
>
> Azure Policy [수정](../governance/policy/how-to/remediate-resources.md)을 사용하여 기존 VM에서도 사용할 수 있습니다.

> [!NOTE]
>
> 이 정책은 한 번에 200개 이상의 VM에 할당하지 않는 것이 좋습니다. 이 정책이 200개가 넘는 VM에 할당될 경우 백업이 일정에 지정된 시간보다 몇 시간 뒤에 트리거될 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Policy에 대해 자세히 알아보기](../governance/policy/overview.md)