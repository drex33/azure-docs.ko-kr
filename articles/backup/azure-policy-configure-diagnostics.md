---
title: 대규모로 자격 증명 모음 진단 설정 구성
description: Azure Policy를 사용하여 지정된 범위의 모든 자격 증명 모음에 대한 Log Analytics 진단 설정 구성
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 34b0cac710833e1d1b29060aa37425d2e57ae828
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2021
ms.locfileid: "113303764"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>대규모로 자격 증명 모음 진단 설정 구성

Azure Backup에서 제공하는 보고 솔루션은 LA(Log Analytics)를 활용합니다. 지정된 자격 증명 모음의 데이터를 LA로 보내려면 해당 자격 증명 모음에 대한 [진단 설정](./backup-azure-diagnostic-events.md)을 만들어야 합니다.

자격 증명 모음마다 진단 설정을 수동으로 추가하는 작업은 번거로울 수 있습니다. 또한 만들어진 모든 새 자격 증명 모음에 대해 이 자격 증명 모음에 대한 보고서를 보려면 진단 설정을 사용하도록 설정해야 합니다.

Azure Backup은 LA를 대상으로 사용하는 대규모 진단 설정을 간단하게 만들 수 있도록 기본 제공 [Azure Policy](../governance/policy/index.yml)를 제공합니다. 이 정책은 지정된 또는 리소스 그룹의 모든 자격 증명 모음에 LA 진단 설정을 추가합니다. 다음 섹션에서는 이 정책을 사용하는 방법에 대한 지침을 제공합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

* 정책은 특정 구독의 모든 Recovery Services 자격 증명 모음(또는 구독 내의 리소스 그룹)에 한 번에 적용될 수 있습니다. 정책을 할당하는 사용자에게 정책이 할당된 구독에 대한 **소유자** 액세스 권한이 있어야 합니다.

* 사용자가 지정하는 LA 작업 영역(진단 데이터가 전송되는)은 정책이 할당된 자격 증명 모음과 다른 구독에 있을 수 있습니다. 사용자는 지정된 LA 작업 영역이 존재하는 구독에 대한 **읽기 권한자**, **기여자** 또는 **소유자** 권한이 있어야 합니다.

* 관리 그룹 범위는 현재 지원되지 않습니다.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="assigning-the-built-in-policy-to-a-scope"></a>범위에 기본 제공 정책 할당

필요한 범위에서 자격 증명 모음에 대한 정책을 할당하려면 다음 단계를 수행합니다.

1. Azure Portal에 로그인하고 **백업 센터** 대시보드로 이동합니다.
2. 왼쪽 메뉴에서 **백업을 위한 Azure 정책** 을 선택하여 Azure 리소스에서 기본 제공되는 모든 정책 목록을 가져옵니다.
3. **리소스별 범주에 대한 Log Analytics 작업 영역에 자격 증명 모음에 대한 진단 설정 배포** 라는 정책을 찾습니다.

    ![정책 정의 창](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. 정책 이름을 선택합니다. 이 정책에 대한 세부 정의로 리디렉션됩니다.

    ![세부 정책 정의](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. 창 위쪽에 있는 **할당** 단추를 선택합니다. 그러면 **정책 할당** 창으로 리디렉션됩니다.

6. **기본** 에서 **범위** 필드 옆에 있는 세 개의 점을 선택합니다. 그러면 적용할 정책의 구독을 선택할 수 있는 올바른 컨텍스트 창이 열립니다. 또한 필요에 따라 리소스 그룹을 선택하여 특정 리소스 그룹의 자격 증명 모음에만 정책이 적용되도록 할 수 있습니다.

    ![정책 할당 기본 사항](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. **매개 변수** 에서 다음 정보를 입력합니다.

    * **프로필 이름** - 정책에 의해 생성된 진단 설정에 할당되는 이름입니다.
    * **Log Analytics 작업 영역** - 진단 설정이 연결되어야 하는 Log Analytics 작업 영역입니다. 정책 할당 범위에서 모든 자격 증명 모음의 진단 데이터는 지정된 LA 작업 영역으로 푸시됩니다.

    * **제외 태그 이름(선택 사항) 및 제외 태그 값(선택 사항)** - 정책 할당에서 특정 태그 이름 및 값을 포함하는 자격 증명 모음을 제외하도록 선택할 수 있습니다. 예를 들어 'isTest' 태그가 'yes' 값으로 설정된 자격 증명 모음에 진단 설정을 추가 **하지 않으려면** **제외 태그 이름** 필드에 'isTest'를 입력하고 **제외 태그 값** 필드에 'yes'를 입력해야 합니다. 이러한 두 필드 중 하나(또는 둘 다)를 비워 두면 정책이 포함하는 태그에 관계없이 모든 관련 자격 증명 모음에 적용됩니다.

    ![정책 할당 매개 변수](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **수정 작업 만들기** - 정책을 범위에 할당하면 해당 범위에서 만든 모든 새 자격 증명 모음이 자동으로 LA 진단 설정을 구성합니다(자격 증명 모음이 생성된 시간부터 30분 이내). 범위의 기존 자격 증명 모음에 진단 설정을 추가하려면 정책 할당 시간에 수정 작업을 트리거할 수 있습니다. 수정 작업을 트리거하려면 **수정 작업 만들기** 확인란을 선택합니다.

    ![정책 할당 수정](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. **검토+만들기** 탭으로 이동하여 **만들기** 를 선택합니다.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>수정 작업이 자격 증명 모음에 적용되는 조건은 무엇입니까?

수정 작업은 정책 정의에 따라 비규격 자격 증명 모음에 적용됩니다. 다음 조건 중 하나를 충족하는 경우 자격 증명 모음이 비규격 상태입니다.

* 자격 증명 모음에 대한 진단 설정이 없습니다.
* 자격 증명 모음에 대한 진단 설정이 있지만 이 설정에는 LA를 대상으로 활성화된 **모든** 리소스별 이벤트와 토글에서 선택한 **리소스별** 이 없습니다.

따라서 사용자가 AzureDiagnostics 모드(Backup 보고서에서 지원됨)에서 AzureBackupReport 이벤트를 사용하는 자격 증명 모음을 사용하는 경우에도 리소스별 모드가 진단 설정([앞으로 이동](./backup-azure-diagnostic-events.md#legacy-event)) 생성에 권장되는 방법이므로 수정 작업은 이 자격 증명 모음에 계속 적용됩니다.

또한 사용자에게 6개의 리소스별 이벤트 중 하위 집합만 사용하도록 설정된 자격 증명 모음이 있는 경우에는 6개의 리소스별 이벤트를 모두 사용하도록 설정한 경우에만 Backup 보고서가 예상대로 작동하므로 수정 작업은 이 자격 증명 모음에 대해 적용됩니다.

> [!NOTE]
>
> 자격 증명 모음에 **리소스별 하위 집합** 범주를 사용하는 기존 진단 설정이 있고, 특정 LA 작업 영역에 데이터를 보내도록 구성된 경우(예: '작업 영역 X') 정책 할당에 제공된 대상 LA 작업 영역이 **같은** '작업 영역 X'인 경우 재구성 작업이 실패합니다(해당 자격 증명 모음에만 해당).
>
>이는 동일한 리소스에 대해 서로 다른 두 진단 설정에서 사용하도록 설정된 이벤트가 일부 형식에서 **겹치면** 설정에 대상과 동일한 LA 작업 영역이 있을 수 없기 때문입니다. 관련 자격 증명 모음으로 이동하고 대상으로 다른 LA 작업 영역을 사용하여 진단 설정을 구성하여 이 오류를 수동으로 해결해야 합니다.
>
> 이 경우 기존 설정에서 사용하도록 설정된 이벤트와 수정 작업에서 만든 설정에서 사용하도록 설정된 이벤트 사이에 겹치는 부분이 없기 때문에 기존 진단 설정이 작업 영역 X를 대상으로만 사용하도록 설정된 AzureBackupReport로 설정된 경우 수정 작업이 실패하지 **않습니다**.

## <a name="next-steps"></a>다음 단계

* [Backup 보고서 사용 방법 알아보기](./configure-reports.md)
* [Azure Policy에 대해 자세히 알아보기](../governance/policy/index.yml)
* [Azure Policy를 사용하여 지정된 범위에서 모든 VM에 대한 백업을 자동으로 사용하도록 설정](./backup-azure-auto-enable-backup.md)
