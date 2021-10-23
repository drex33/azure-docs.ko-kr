---
title: Azure Policy를 사용하여 VM 인사이트 사용
description: Azure Policy를 사용 하 여 여러 Azure 가상 머신 또는 가상 머신 확장 집합에 대해 VM insights를 사용 하도록 설정 하는 방법을 설명 합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 6b3cdddcc07df6961cc6493404583e6cb7da96e4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224463"
---
# <a name="enable-vm-insights-by-using-azure-policy"></a>Azure Policy를 사용하여 VM 인사이트 사용
이 문서에서는 Azure Policy를 사용 하 여 azure Arc (미리 보기)와 연결 된 Azure virtual machines 또는 하이브리드 가상 머신에 대해 VM insights를 사용 하도록 설정 하는 방법을 설명 합니다. Azure Policy를 사용 하면 Azure 환경에서 VM 정보에 필요한 에이전트를 설치 하는 정책 정의를 할당 하 고 각 가상 머신이 만들어질 때 자동으로 Vm에 대 한 모니터링을 사용 하도록 설정할 수 있습니다. VM insights는 사용자 환경에서 비규격 Vm을 검색 및 재구성 하는 데 사용할 수 있는 기능을 제공 합니다. Azure Policy에서 직접 작업 하는 대신이 기능을 사용 합니다.

Azure Policy에 익숙하지 않은 경우 [Azure Policy를 사용 하 여 대규모로 Azure Monitor 배포](../best-practices.md)에서 간략하게 소개 하세요.

> [!NOTE]
> Azure 가상 머신 확장 집합에 Azure Policy를 사용 하거나 Azure virtual machines를 사용 하도록 Azure Policy 직접 작업 하려면 [Azure Policy를 사용 하 여 대규모로 Azure Monitor 배포](../best-practices.md)를 참조 하세요.

## <a name="vm-insights-initiatives"></a>VM insights 이니셔티브
VM insights는 Azure virtual machines에 Log Analytics 에이전트 및 종속성 에이전트를 설치 하기 위한 기본 제공 정책 정의를 제공 합니다. 다음 기본 제공 이니셔티브는 전체 모니터링을 사용 하기 위해 두 에이전트를 모두 설치 합니다. 이러한 이니셔티브를 관리 그룹, 구독 또는 리소스 그룹에 할당 하 여 해당 범위의 Windows 또는 Linux Azure 가상 컴퓨터에 에이전트를 자동으로 설치 합니다.

|Name |Description |
|:---|:---|
|VM 인사이트 사용 | Azure VM과 Azure Arc에 연결된 하이브리드 VM에 Log Analytics 에이전트 및 종속성 에이전트를 설치합니다. |
|가상 머신 확장 집합에 Azure Monitor 사용 | Azure 가상 머신 확장 집합에 Log Analytics 에이전트 및 종속성 에이전트를 설치 합니다. |



## <a name="open-policy-coverage-feature"></a>정책 적용 기능 열기
**VM Insights 정책 적용 범위** 에 액세스 하려면 Azure Portal의 **Azure Monitor** 메뉴에서 **가상 머신으로** 이동 합니다. **기타 온 보 딩 옵션** 을 선택한 다음 **정책 사용** 사용에서 **사용을 설정** 합니다.

[![Vm에서 Azure Monitor 시작 탭](./media/vminsights-enable-policy/get-started-page.png)](./media/vminsights-enable-policy/get-started-page.png#lightbox)

### <a name="create-new-assignment"></a>새 할당 만들기
아직 할당이 없는 경우 **정책 할당** 을 클릭 하 여 새 할당을 만듭니다.

[![할당 만들기](media/vminsights-enable-policy/create-assignment.png)](media/vminsights-enable-policy/create-assignment.png#lightbox)

이는 사용자가 선택한 범위를 사용 하 여 하드 코드 되 고 VM insights 이니셔티브 정의를 **사용 하도록 설정** 하는 것을 제외 하 고 Azure Policy 이니셔티브를 할당 하는 페이지와 동일 합니다. 필요에 따라 **할당 이름을** 변경 하 고 **설명을** 추가할 수 있습니다. 범위에 대 한 제외를 제공 하려면 **제외** 를 선택 합니다. 예를 들어, 범위는 관리 그룹이 될 수 있으며 해당 관리 그룹의 구독을 할당에서 제외할 수 있도록 지정할 수 있습니다.

[![이니셔티브 할당](media/vminsights-enable-policy/assign-initiative.png)](media/vminsights-enable-policy/assign-initiative.png#lightbox)

**매개 변수** 페이지에서 할당의 모든 가상 컴퓨터에 사용할 **Log Analytics 작업 영역** 을 선택 합니다. 다른 가상 컴퓨터에 대해 서로 다른 작업 영역을 지정 하려면 각각 고유한 범위를 사용 하 여 여러 할당을 만들어야 합니다. 

   > [!NOTE]
   > 작업 영역이 할당 범위를 벗어나는 경우 *Log Analytics 기여자* 권한을 정책 할당의 Principal ID에 부여합니다. 이렇게 하지 않으면 다음과 같은 배포 실패가 표시 될 수 있습니다. `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`

[![작업 영역](media/vminsights-enable-policy/assignment-workspace.png)](media/vminsights-enable-policy/assignment-workspace.png#lightbox)

**만들기를 클릭** 하 여 할당에 대 한 세부 정보를 검토 하려면 **검토 + 만들기** 를 클릭 합니다. 기존 가상 컴퓨터를 사용 하도록 설정 하는 데 여러 가지 수정 작업이 필요할 수 있으므로이 시점에서 수정 작업을 만들지 마십시오. 아래의 [준수 결과](#remediate-compliance-results) 수정을 참조 하세요.

### <a name="review-compliance"></a>준수 검토
할당을 만든 후에는 관리 그룹 및 구독에서 **VM insights를 사용 하도록 설정** 에 대 한 검사를 검토 하 고 관리할 수 있습니다. 그러면 각 관리 그룹 또는 구독에 존재 하는 가상 컴퓨터 수와 해당 호환성 상태가 표시 됩니다.

[![VM insights 정책 관리 페이지](media/vminsights-enable-policy/manage-policy-page-01.png)](media/vminsights-enable-policy/manage-policy-page-01.png#lightbox)


다음 표에는이 보기의 정보에 대 한 설명이 나와 있습니다.

| 함수 | Description | 
|----------|-------------| 
| **범위** | 관리 그룹 계층을 통해 드릴 다운할 수 있는 권한으로 액세스 하거나 상속한 관리 그룹 및 구독입니다.|
| **역할** | 사용자의 역할은 독자, 소유자 또는 참가자 일 수 있습니다. 구독에 대 한 액세스 권한은 있지만 자신이 속한 관리 그룹에 대 한 액세스 권한이 없는 경우에는이 값이 비어 있습니다. 이 역할은 정책 또는 이니셔티브 (소유자)를 할당 하거나, 편집 하거나, 준수를 확인 하는 등 볼 수 있는 데이터와 수행할 수 있는 작업을 결정 합니다. |
| **총 Vm** | 상태에 관계 없이 해당 범위에 있는 Vm의 총 수입니다. 관리 그룹의 경우 구독 또는 자식 관리 그룹에 중첩 된 Vm의 총 합계입니다. |
| **할당 검사** | 이니셔티브에서 적용 되는 Vm의 백분율입니다. |
| **할당 상태** | **성공** -범위에 있는 모든 vm에 Log Analytics 및 종속성 에이전트가 배포 되어 있습니다.<br>**경고** -구독이 관리 그룹 아래에 있지 않습니다.<br>**시작 되지 않음** -새 할당이 추가 되었습니다.<br>**잠금** -관리 그룹에 대 한 충분 한 권한이 없습니다.<br>**Blank** -vm이 없거나 정책이 할당 되지 않았습니다. |
| **규격 Vm** | Log Analytics 에이전트와 종속성 에이전트가 모두 설치 된 Vm 수 인 호환 되는 Vm 수입니다. 할당이 없거나, 범위에 Vm이 없거나, 적절 한 권한이 없는 경우에는이 값이 비어 있습니다. |
| **호환성** | 전반적인 규정 준수 번호는 규정을 준수 하는 고유 리소스의 합계를 모든 고유 리소스의 합계로 나눈 값입니다. |
| **규정 준수 상태** | **규격** -가상 컴퓨터 범위에 있는 모든 vm에는 Log Analytics 및 종속성 에이전트가 배포 되어 있거나 할당에 적용 되는 범위의 새 vm이 아직 평가 되지 않았습니다.<br>**비규격** -평가 되었지만 사용 하도록 설정 되지 않은 vm이 있으며 수정이 필요할 수 있습니다.<br>**시작 되지 않음** -새 할당이 추가 되었습니다.<br>**잠금** -관리 그룹에 대 한 충분 한 권한이 없습니다.<br>**Blank** -정책이 할당 되지 않습니다.  |


이니셔티브를 할당할 때 할당에서 선택 된 범위는 나열 된 범위 이거나 그 하위 집합인 것일 수 있습니다. 예를 들어 관리 그룹 (검사 범위)이 아닌 구독 (정책 범위)에 대 한 할당을 만들었을 수 있습니다. 이 경우 **할당** 범위의 값은 이니셔티브 범위의 vm을 검사 범위의 vm으로 나눈 값을 나타냅니다. 다른 경우에는 일부 Vm, 리소스 그룹 또는 정책 범위에서 구독을 제외 했을 수 있습니다. 값이 비어 있으면 정책이 나 이니셔티브가 없거나 사용 권한이 없음을 나타냅니다. 정보는 **할당 상태** 에서 제공 됩니다.


### <a name="remediate-compliance-results"></a>준수 결과 재구성
이니셔티브는 생성 되거나 수정 될 때 가상 컴퓨터에 적용 되지만 기존 Vm에는 적용 되지 않습니다. 할당에 100%의 준수가 표시 되지 않으면 수정 작업을 만들어 기존 Vm을 평가 하 고 사용 하도록 설정 하 고 줄임표 (...)를 선택 하 여 **준수 보기** 를 선택 합니다.

[![호환성 보기](media/vminsights-enable-policy/view-compliance.png)](media/vminsights-enable-policy/view-compliance.png#lightbox)

**규정 준수** 페이지에는 지정 된 필터와 일치 하는 할당 및 호환 여부가 나열 됩니다. 세부 정보를 보려면 할당을 클릭 합니다.

[![Azure VM에 대한 정책 준수](./media/vminsights-enable-policy/policy-view-compliance.png)](./media/vminsights-enable-policy/policy-view-compliance.png#lightbox)

**이니셔티브 규정 준수** 페이지에는 이니셔티브의 정책 정의와 각 규정 준수 여부가 나열 됩니다.

[![준수 세부 정보](media/vminsights-enable-policy/compliance-details.png)](media/vminsights-enable-policy/compliance-details.png#lightbox)

세부 정보를 보려면 정책 정의를 클릭 합니다. 정책 정의가 준수 하지 않는 것으로 표시 되는 시나리오는 다음과 같습니다.

* Log Analytics 에이전트 또는 종속성 에이전트가 배포 되지 않았습니다. 완화할 수정 작업을 만듭니다.
* VM 이미지 (OS)가 정책 정의에서 식별 되지 않습니다. 배포 정책의 조건에는 잘 알려진 Azure VM 이미지에서 배포된 VM만 포함됩니다. VM OS가 지원되는지 여부는 설명서를 확인하세요.
* Vm이 지정 된 Log Analytics 작업 영역에 로깅하지 않습니다. 이니셔티브 범위의 일부 Vm이 정책 할당에 지정 된 Vm이 아닌 Log Analytics 작업 영역에 연결 되어 있습니다.

[![정책 준수 세부 정보](media/vminsights-enable-policy/policy-compliance-details.png)](media/vminsights-enable-policy/policy-compliance-details.png#lightbox)

준수 문제를 완화 하는 수정 작업을 만들려면 **수정 작업 만들기** 를 클릭 합니다. 

[![새 수정 작업](media/vminsights-enable-policy/new-remediation-task.png)](media/vminsights-enable-policy/new-remediation-task.png#lightbox)

**수정을** 클릭하여 수정 작업을 만든 **다음** 수정을 클릭하여 시작합니다. 각 정책 정의에 대해 하나씩 여러 수정 작업을 만들어야 할 수 있습니다. 이니셔티브에 대한 수정 작업은 만들 수 없습니다.

[![스크린샷은 모니터 | 대한 정책 수정 창을 보여줍니다. Virtual Machines.](media/vminsights-enable-policy/remediation.png)](media/vminsights-enable-policy/remediation.png#lightbox)


수정 작업이 완료되면 VM은 VM 인사이트에 대해 설치된 에이전트를 준수하고 사용하도록 설정되어야 합니다. 


## <a name="azure-policy"></a>Azure Policy
Azure Policy 사용하여 가상 머신 확장 집합에 대한 모니터링을 사용하도록 설정하려면 모니터링할 리소스의 범위에 따라 **Virtual Machine Scale Sets Azure Monitor** 사용 이니셔티브를 Azure 관리 그룹, 구독 또는 리소스 그룹에 할당합니다. [관리 그룹은](../../governance/management-groups/overview.md) 특히 조직에 여러 구독이 있는 경우 범위 지정 정책에 유용합니다.

![Azure Portal의 이니셔티브 할당 페이지 스크린샷 이니셔티브 정의는 Virtual Machine Scale Sets Azure Monitor 사용으로 설정됩니다.](media/vminsights-enable-policy/virtual-machine-scale-set-assign-initiative.png)

데이터를 보낼 작업 영역을 선택합니다. 이 작업 영역에는 VM [인사이트에 대한 Log Analytics 작업 영역 구성에](vminsights-configure-workspace.md)설명된 대로 *VMInsights* 솔루션이 설치되어 있어야 합니다.

![작업 영역 선택을 보여 주는 스크린샷.](media/vminsights-enable-policy/virtual-machine-scale-set-workspace.png)

이 정책을 할당해야 하는 기존 가상 머신 확장 집합이 있는 경우 수정 작업을 만듭니다.

![수정 작업 만들기를 보여 주는 스크린샷](media/vminsights-enable-policy/virtual-machine-scale-set-remediation.png)



## <a name="next-steps"></a>다음 단계

이제 가상 머신에 모니터링을 사용하도록 설정했으므로 이 정보를 VM 인사이트로 분석할 수 있습니다. 

- 검색된 애플리케이션 종속성을 보려면 [VM 인사이트 Map 보기](vminsights-maps.md)를 참조하세요. 
- VM의 성능에서 병목 현상 및 전반적인 사용률을 식별하려면 [Azure VM 성능 보기](vminsights-performance.md)를 참조하세요.
