---
title: Azure Policy를 사용하여 VM 인사이트 사용
description: Azure Policy 사용하여 여러 Azure 가상 머신 또는 가상 머신 확장 집합에 대해 VM 인사이트를 사용하도록 설정하는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 63e68a247dd9d38cffe1555806ab23391c38f1fa
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177451"
---
# <a name="enable-vm-insights-by-using-azure-policy"></a>Azure Policy를 사용하여 VM 인사이트 사용
이 문서에서는 Azure Policy 사용하여 Azure Arc(미리 보기)와 연결된 Azure 가상 머신 또는 하이브리드 가상 머신에 대한 VM 인사이트를 사용하도록 설정하는 방법을 설명합니다. Azure Policy 사용하면 Azure 환경에서 VM 인사이트에 필요한 에이전트를 설치하고 각 가상 머신이 만들어질 때 VM에 대한 모니터링을 자동으로 사용하도록 설정하는 정책 정의를 할당할 수 있습니다. VM 인사이트는 사용자 환경에서 비호환 VM을 검색하고 재구성할 수 있는 기능을 제공합니다. Azure Policy 직접 작업하는 대신 이 기능을 사용합니다.

Azure Policy 익숙하지 않은 경우 Azure Policy [사용하여 대규모 Azure Monitor 배포에서](../deploy-scale.md)간략하게 소개합니다.

> [!NOTE]
> Azure 가상 머신 확장 집합에서 Azure Policy 사용하거나 Azure Policy 직접 사용하여 Azure 가상 머신을 사용하도록 설정하려면 [Azure Policy 사용하여 대규모 Azure Monitor 배포를](../deploy-scale.md#vm-insights)참조하세요.

## <a name="vm-insights-initiatives"></a>VM 인사이트 이니셔티브
VM 인사이트는 Azure 가상 머신에 Log Analytics 에이전트 및 종속성 에이전트를 설치하는 기본 제공 정책 정의를 제공합니다. 다음 기본 제공 이니셔티브는 전체 모니터링을 사용하도록 설정하기 위해 두 에이전트를 모두 설치합니다. 관리 그룹, 구독 또는 리소스 그룹에 이러한 이니셔티브를 할당하여 해당 범위의 모든 Windows 또는 Linux Azure 가상 머신에 에이전트를 자동으로 설치합니다.

|Name |Description |
|:---|:---|
|VM 인사이트 사용 | Azure VM과 Azure Arc에 연결된 하이브리드 VM에 Log Analytics 에이전트 및 종속성 에이전트를 설치합니다. |
|가상 머신 확장 집합에 Azure Monitor 사용 | Azure 가상 머신 확장 집합에 Log Analytics 에이전트 및 종속성 에이전트를 설치합니다. |



## <a name="open-policy-coverage-feature"></a>정책 적용 범위 열기 기능
**VM 인사이트 정책 적용 범위에** 액세스하려면 **Azure Portal Azure Monitor** 메뉴의 가상 **머신으로** 이동합니다. **기타 온보딩 옵션을** 선택한 **다음, 정책을 사용하여 사용** 아래에서 **사용을** 선택합니다.

[![VM 시작 탭에서 Azure Monitor](./media/vminsights-enable-policy/get-started-page.png)](./media/vminsights-enable-policy/get-started-page.png#lightbox)

### <a name="create-new-assignment"></a>새 할당 만들기
할당이 없는 경우 정책 할당을 클릭하여 새 **할당을** 만듭니다.

[![할당 만들기](media/vminsights-enable-policy/create-assignment.png)](media/vminsights-enable-policy/create-assignment.png#lightbox)

이 페이지는 선택한 범위 및 **VM 인사이트 사용** 이니셔티브 정의로 하드 코딩된다는 점을 제외하고 Azure Policy 이니셔티브를 할당하는 것과 동일한 페이지입니다. 필요에 따라 할당 **이름을** 변경하고 **설명** 을 추가할 수 있습니다. 범위에 **제외를** 제공하려면 제외를 선택합니다. 예를 들어 범위는 관리 그룹이 될 수 있으며 할당에서 제외할 해당 관리 그룹의 구독을 지정할 수 있습니다.

[![이니셔티브 할당](media/vminsights-enable-policy/assign-initiative.png)](media/vminsights-enable-policy/assign-initiative.png#lightbox)

매개 **변수** 페이지에서 할당의 모든 가상 머신에서 사용할 **Log Analytics 작업 영역을** 선택합니다. 서로 다른 가상 머신에 대해 서로 다른 작업 영역을 지정하려면 각각 고유한 범위로 여러 할당을 만들어야 합니다. 

   > [!NOTE]
   > 작업 영역이 할당 범위를 벗어나는 경우 *Log Analytics 기여자* 권한을 정책 할당의 Principal ID에 부여합니다. 이렇게 하지 않으면 다음과 같은 배포 오류가 표시될 수 있습니다. `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`

[![작업 영역](media/vminsights-enable-policy/assignment-workspace.png)](media/vminsights-enable-policy/assignment-workspace.png#lightbox)

**검토 + 만들기를** 클릭하여 할당 세부 정보를 검토한 후 **만들기를** 클릭하여 할당을 만듭니다. 기존 가상 머신을 사용하도록 설정하기 위해 여러 수정 작업이 필요할 가능성이 높으므로 이 시점에서 수정 작업을 만들지 마세요. 아래 [규정 준수 결과 수정을](#remediate-compliance-results) 참조하세요.

### <a name="review-compliance"></a>규정 준수 검토
할당이 만들어지면 관리 그룹 및 구독에서 **VM 인사이트 사용** 이니셔티브에 대한 적용 범위를 검토하고 관리할 수 있습니다. 그러면 각 관리 그룹 또는 구독에 있는 가상 머신의 수와 해당 준수 상태가 표시됩니다.

[![VM 인사이트 정책 관리 페이지](media/vminsights-enable-policy/manage-policy-page-01.png)](media/vminsights-enable-policy/manage-policy-page-01.png#lightbox)


다음 표에서는 이 뷰의 정보에 대한 설명을 제공합니다.

| 함수 | 설명 | 
|----------|-------------| 
| **범위** | 관리 그룹 계층 구조를 드릴다운할 수 있는 기능을 사용하여 액세스 권한이 있거나 상속된 관리 그룹 및 구독|
| **역할** | 범위에서 사용자의 역할로, 읽기, 소유자 또는 기여자일 수 있습니다. 구독에 대한 액세스 권한이 있지만 구독이 속한 관리 그룹에 대한 액세스 권한이 없는 경우 이 값은 비어 있습니다. 이 역할은 볼 수 있는 데이터와 정책 또는 이니셔티브 할당(소유자), 편집 또는 규정 준수 보기 측면에서 수행할 수 있는 작업을 결정합니다. |
| **총 VM** | 해당 범위에 있는 VM의 상태에 관계없이 총 VM 수입니다. 관리 그룹의 경우 구독 또는 자식 관리 그룹에 중첩된 VM의 합계입니다. |
| **할당 적용 범위** | 이니셔티브가 적용되는 VM의 백분율입니다. |
| **할당 상태** | **성공** - 범위의 모든 VM에는 Log Analytics 및 종속성 에이전트가 배포되어 있습니다.<br>**경고** - 구독이 관리 그룹에 있지 않습니다.<br>**시작되지 않음** - 새 할당이 추가되었습니다.<br>**잠금** - 관리 그룹에 대한 충분한 권한이 없습니다.<br>**비어 있음** - VM이 없거나 정책이 할당되지 않았습니다. |
| **규격 VM** | 호환되는 VM 수- Log Analytics 에이전트와 종속성 에이전트가 모두 설치된 VM 수입니다. 할당이 없거나, 범위에 VM이 없거나, 적절한 권한이 없는 경우 이 값은 비어 있습니다. |
| **호환성** | 전체 규정 준수 번호는 규격인 고유 리소스의 합계를 모든 고유 리소스의 합계로 나눈 값입니다. |
| **규정 준수 상태** | **준수** - 범위 가상 머신의 모든 VM에는 Log Analytics 및 종속성 에이전트가 배포되어 있거나 할당이 적용되는 범위의 새 VM이 아직 평가되지 않았습니다.<br>**비준수** - 평가되었지만 사용하도록 설정되지 않은 VM이 있으며 수정이 필요할 수 있습니다.<br>**시작되지 않음** - 새 할당이 추가되었습니다.<br>**잠금** - 관리 그룹에 대한 충분한 권한이 없습니다.<br>**비어 있음** - 정책이 할당되지 않습니다.  |


이니셔티브를 할당할 때 할당에서 선택한 범위는 나열된 범위 또는 그 하위 집합일 수 있습니다. 예를 들어 관리 그룹(적용 범위)이 아닌 구독(정책 범위)에 대한 할당을 만들었을 수 있습니다. 이 경우 **할당 범위의** 값은 이니셔티브 범위의 VM을 적용 범위의 VM으로 나눈 값을 나타냅니다. 또 다른 경우 정책 범위에서 일부 VM, 리소스 그룹 또는 구독을 제외했을 수 있습니다. 값이 비어 있으면 정책 또는 이니셔티브가 존재하지 않거나 권한이 없음을 나타냅니다. 정보는 **할당 상태** 아래에 제공됩니다.


### <a name="remediate-compliance-results"></a>규정 준수 결과 수정
이니셔티브는 만들거나 수정할 때 가상 머신에 적용되지만 기존 VM에는 적용되지 않습니다. 할당에 100% 규정 준수가 표시되지 않으면 기존 VM을 평가하고 사용하도록 설정하는 수정 작업을 만들고 줄임표(...)를 선택하여 **준수 보기를** 선택합니다.

[![준수 보기](media/vminsights-enable-policy/view-compliance.png)](media/vminsights-enable-policy/view-compliance.png#lightbox)

**규정 준수** 페이지에는 지정된 필터와 일치하는 할당 및 규정 준수 여부가 나열됩니다. 할당을 클릭하여 세부 정보를 봅니다.

[![Azure VM에 대한 정책 준수](./media/vminsights-enable-policy/policy-view-compliance.png)](./media/vminsights-enable-policy/policy-view-compliance.png#lightbox)

**이니셔티브 준수** 페이지에는 이니셔티브의 정책 정의와 각 정책의 준수 여부가 나열됩니다.

[![준수 세부 정보](media/vminsights-enable-policy/compliance-details.png)](media/vminsights-enable-policy/compliance-details.png#lightbox)

정책 정의를 클릭하여 세부 정보를 봅니다. 정책 정의가 비준수로 표시될 시나리오는 다음과 같습니다.

* Log Analytics 에이전트 또는 종속성 에이전트는 배포되지 않습니다. 완화할 수정 작업을 만듭니다.
* VM 이미지(OS)는 정책 정의에서 식별되지 않습니다. 배포 정책의 조건에는 잘 알려진 Azure VM 이미지에서 배포된 VM만 포함됩니다. VM OS가 지원되는지 여부는 설명서를 확인하세요.
* VM은 지정된 Log Analytics 작업 영역에 로깅되지 않습니다. 이니셔티브 범위의 일부 VM은 정책 할당에 지정된 작업 영역이 아닌 Log Analytics 작업 영역에 연결됩니다.

[![정책 준수 세부 정보](media/vminsights-enable-policy/policy-compliance-details.png)](media/vminsights-enable-policy/policy-compliance-details.png#lightbox)

규정 준수 문제를 완화하기 위한 수정 작업을 만들려면 **수정 작업 만들기를** 클릭합니다. 

[![새 수정 작업](media/vminsights-enable-policy/new-remediation-task.png)](media/vminsights-enable-policy/new-remediation-task.png#lightbox)

재구성 **을 클릭 하** 여 수정 작업을 만든 **다음, 수정 하 여** 시작 합니다. 각 정책 정의에 대해 하나씩 여러 개의 재구성 작업을 만들어야 할 가능성이 높습니다. 이니셔티브에 대 한 수정 작업을 만들 수 없습니다.

[![모니터에 대 한 정책 수정 창이 스크린샷 화면에 표시 됩니다. Virtual Machines.](media/vminsights-enable-policy/remediation.png)](media/vminsights-enable-policy/remediation.png#lightbox)


재구성 작업이 완료 되 면 vm은 VM insights에 대해 설치 되 고 사용 하도록 설정 된 에이전트와 호환 되어야 합니다. 


## <a name="azure-policy"></a>Azure Policy
Azure Policy를 사용 하 여 가상 머신 확장 집합에 대 한 모니터링을 사용 하도록 설정 하려면 모니터링할 리소스의 범위에 따라 **Virtual Machine Scale Sets 이니셔티브에 대해 Azure Monitor 사용** 을 Azure 관리 그룹, 구독 또는 리소스 그룹에 할당 합니다. [관리 그룹](../../governance/management-groups/overview.md) 은 범위 지정 정책, 특히 조직에 여러 구독이 있는 경우에 유용 합니다.

![Azure Portal의 이니셔티브 할당 페이지 스크린샷 이니셔티브 정의는 Virtual Machine Scale Sets에 대해 Azure Monitor를 사용 하도록 설정 됩니다.](media/vminsights-enable-policy/virtual-machine-scale-set-assign-initiative.png)

데이터가 전송 될 작업 영역을 선택 합니다. [VM insights에 대 한 Log Analytics 작업 영역 구성](vminsights-configure-workspace.md)에 설명 된 대로이 작업 영역에는 *VMInsights* 솔루션이 설치 되어 있어야 합니다.

![작업 영역을 선택 하는 것을 보여 주는 스크린샷](media/vminsights-enable-policy/virtual-machine-scale-set-workspace.png)

이 정책을 할당 해야 하는 기존 가상 머신 확장 집합이 있는 경우 수정 작업을 만듭니다.

![수정 작업을 만드는 과정을 보여 주는 스크린샷](media/vminsights-enable-policy/virtual-machine-scale-set-remediation.png)



## <a name="next-steps"></a>다음 단계

이제 가상 머신에 모니터링을 사용하도록 설정했으므로 이 정보를 VM 인사이트로 분석할 수 있습니다. 

- 검색된 애플리케이션 종속성을 보려면 [VM 인사이트 Map 보기](vminsights-maps.md)를 참조하세요. 
- VM의 성능에서 병목 현상 및 전반적인 사용률을 식별하려면 [Azure VM 성능 보기](vminsights-performance.md)를 참조하세요.
