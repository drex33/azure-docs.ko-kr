---
title: Automation 계정에서 Azure Automation 변경 내용 추적 및 인벤토리 사용
description: 이 문서에서는 Automation 계정에서 변경 내용 추적 및 인벤토리를 사용하도록 설정하는 방법을 설명합니다.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 4e5e0b563b2701a8b5ddbffbf485a3127d52ece0
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769897"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Automation 계정에서 변경 내용 추적 및 인벤토리 사용

이 문서에서는 Automation 계정을 사용하여 사용자 환경의 VM에서 [변경 내용 추적 및 인벤토리](overview.md) 기능을 사용하도록 설정하는 방법을 설명합니다. 대규모로 Azure VM을 사용하도록 설정하려면 변경 내용 추적 및 인벤토리를 사용하여 기존 VM을 사용하도록 설정해야 합니다.

> [!NOTE]
> 변경 내용 추적 및 인벤토리를 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다. 지원되는 매핑 쌍 목록은 [Automation 계정의 지역 매핑 및 Log Analytics 작업 영역](../how-to/region-mappings.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 컴퓨터를 관리하기 위한 [Automation 계정](../automation-security-overview.md)
* [가상 머신](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Azure에 로그인

https://portal.azure.com 에서 Azure에 로그인합니다.

## <a name="enable-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 사용

1. Automation 계정으로 이동하여 **구성 관리** 아래에서 **인벤토리** 또는 **변경 내용 추적** 을 선택합니다.

2. Log Analytics 작업 영역 및 Automation 계정을 선택하고 **사용** 을 클릭하여 변경 내용 추적 및 인벤토리를 사용하도록 설정합니다. 설정을 완료하는 데 최대 15분이 걸립니다.

    ![변경 내용 추적 및 인벤토리 사용](media/enable-from-automation-account/enable-feature.png)

## <a name="enable-azure-vms"></a>Azure VM을 사용하도록 설정

1. Automation 계정의 **구성 관리** 에서 **인벤토리** 또는 **변경 내용 추적** 을 선택합니다.

2. **+ Azure VM 추가** 를 클릭하고 목록에서 하나 이상의 VM을 선택합니다. 사용할 수 없는 가상 머신은 회색으로 표시되어 있으며 선택할 수 없습니다. Azure VM은 Automation 계정 위치와 상관없이 모든 지역에 있을 수 있습니다. 

3. **사용** 을 클릭하여 컴퓨터 그룹의 저장된 기능 검색에 선택한 VM을 추가합니다. 자세한 내용은 [변경 내용 추적 제한 및 인벤토리 배포 범위](manage-scope-configurations.md)를 참조하세요.

      [ ![Azure VM을 사용하도록 설정](./media/enable-from-automation-account/enable-azure-vms.png)](./media/enable-from-automation-account/enable-azure-vms-expanded.png#lightbox)

## <a name="enable-non-azure-vms"></a>비 Azure VM 사용

Azure에 없는 컴퓨터는 수동으로 추가해야 합니다. 먼저 머신을 [Azure Arc 지원 서버](../../azure-arc/servers/overview.md)에 연결하여 Windows 또는 Linux용 Log Analytics 에이전트를 설치한 다음, Azure Policy를 사용해 [*Linux* 또는 *Windows* Azure Arc 머신에 Log Analytics 에이전트 배포](../../governance/policy/samples/built-in-policies.md#monitoring) 기본 제공 정책을 할당하는 것이 좋습니다. VM용 Azure Monitor를 사용하여 머신을 모니터링하려는 경우에는 대신 [VM용 Azure Monitor 사용 설정](../../governance/policy/samples/built-in-initiatives.md#monitoring) 이니셔티브를 사용합니다.

1. Automation 계정의 **구성 관리** 에서 **인벤토리** 또는 **변경 내용 추적** 을 선택합니다.

2. **비Azure 컴퓨터 추가** 를 클릭합니다. 이 작업은 머신이 변경 내용 추적 및 인벤토리 작업 보고를 시작할 수 있게 [Windows에 대한 Log Analytics 에이전트를 설치 및 구성하기 위한 지침](../../azure-monitor/agents/log-analytics-agent.md)이 포함된 새 브라우저 창을 엽니다. Operations Manager에서 현재 관리하고 있는 머신을 사용하도록 설정하는 경우 새 에이전트가 필요하지 않으며 작업 영역 정보가 기존 에이전트에 입력됩니다.

## <a name="enable-machines-in-the-workspace"></a>작업 영역에서 머신 사용

수동으로 설치된 머신 또는 이미 사용자의 작업 영역에 보고된 머신은 Azure Automation에 추가해야 변경 내용 추적 및 인벤토리를 사용할 수 있습니다.

1. Automation 계정의 **구성 관리** 에서 **인벤토리** 또는 **변경 내용 추적** 을 선택합니다.

2. **컴퓨터 관리** 를 선택합니다. 이전에 **사용 가능한 모든 머신 및 향후 사용할 머신에서 사용하도록 설정** 옵션을 선택한 경우 **머신 관리** 옵션이 회색으로 표시될 수 있습니다.

    ![저장된 검색](media/enable-from-automation-account/manage-machines.png)

3. 사용 가능한 모든 머신에 변경 내용 추적 및 인벤토리를 사용하도록 설정하려면 **머신 관리** 페이지에서 **사용 가능한 모든 머신에서 사용하도록 설정** 을 선택합니다. 해당 작업을 수행하면 컨트롤에서 머신을 개별적으로 추가하지 못하고, 작업 영역에 보고하는 모든 머신을 컴퓨터 그룹으로 저장된 검색 쿼리에 추가합니다. 해당 작업을 선택하면 **머신 관리** 옵션을 사용할 수 없게 됩니다.

4. 사용 가능한 모든 향후 머신에서 기능을 사용하려면 **사용 가능한 모든 향후 머신에서 사용** 을 선택합니다. 해당 옵션은 저장된 검색 및 범위 구성을 작업 영역에서 삭제하고, 해당 작업 영역에 보고하는 모든 Azure 및 비 Azure 머신에 대한 기능을 개방합니다. 해당 작업을 선택하면 범위 구성이 더 이상 남아 있지 않으므로 **머신 관리** 옵션을 영구적으로 사용하지 않도록 설정합니다.

    > [!NOTE]
    > 해당 옵션은 Log Analytics 내에 저장된 검색 및 범위 구성을 삭제하므로 해당 옵션을 선택하기 전에 Log Analytics 작업 영역에서 삭제 잠금을 제거하는 것이 중요합니다. 잠금을 제거하지 않은 상태에서 해당 옵션을 선택하면 구성이 제거되지 않으므로 관련 구성을 수동으로 제거해야 합니다.

5. 필요한 경우 최초 저장된 검색을 다시 추가하여 범위 구성을 다시 추가할 수 있습니다. 자세한 내용은 [변경 내용 추적 제한 및 인벤토리 배포 범위](manage-scope-configurations.md)를 참조하세요.

6. 하나 이상의 머신에서 기능을 사용하려면 **선택한 머신에서 사용** 을 선택하고 해당 기능에 대해 사용하려는 각 머신 옆에 있는 **추가** 를 클릭합니다. 이 작업은 선택한 머신 이름을 컴퓨터 그룹에서 저장된 기능 검색 쿼리에 추가합니다.

## <a name="next-steps"></a>다음 단계

* 이 기능을 사용하는 방법에 대한 자세한 내용은 [변경 내용 추적](manage-change-tracking.md) 및 [인벤토리 관리](manage-inventory-vms.md)를 참조하세요.

* 이 기능의 일반적인 문제를 해결하려면 [변경 내용 추적 및 인벤토리 문제 해결](../troubleshoot/change-tracking.md)을 참조하세요.
