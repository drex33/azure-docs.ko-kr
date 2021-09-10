---
title: Automation 계정에서 Azure Automation 업데이트 관리 사용
description: 이 문서에서는 Automation 계정에서 업데이트 관리를 사용하도록 설정하는 방법을 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 11/09/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: bc17b875c8f16304d5292f7ed852909373b8c5d9
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768313"
---
# <a name="enable-update-management-from-an-automation-account"></a>Automation 계정에서 업데이트 관리 사용

이 문서에서는 Automation 계정을 사용하여 [Azure Arc 지원 서버](../../azure-arc/servers/overview.md)에 등록된 컴퓨터 또는 서버를 포함하여 사용자 환경의 VM에 [업데이트 관리](overview.md) 기능을 사용하도록 설정하는 방법에 대해 설명합니다. 대규모 Azure VM을 사용하도록 설정하려면 업데이트 관리를 사용하여 기존 Azure VM을 사용하도록 설정해야 합니다.

> [!NOTE]
> 업데이트 관리를 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다. 지원되는 매핑 쌍 목록은 [Automation 계정의 지역 매핑 및 Log Analytics 작업 영역](../how-to/region-mappings.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 컴퓨터를 관리하기 위한 [Automation 계정](../automation-security-overview.md)
* [Azure 가상 머신](../../virtual-machines/windows/quick-create-portal.md) 또는 Arc 지원 서버에 등록된 VM이나 서버. 비 Azure VM 또는 서버에는 Windows 또는 Linux용 [Log Analytics 에이전트](../../azure-monitor/agents/log-analytics-agent.md)가 설치되어 있고, Automation 계정 업데이트 관리에 연결된 작업 영역에 대한 보고가 사용하도록 설정되어 있어야 합니다. 먼저 컴퓨터를 [Azure Arc 지원 서버](../../azure-arc/servers/overview.md)에 연결하여 Windows 또는 Linux용 Log Analytics 에이전트를 설치한 후, Azure Policy를 사용하여 [Log Analytics 에이전트 배포 기본 제공 정책을 *Linux* 또는 *Windows* Azure Arc 컴퓨터](../../governance/policy/samples/built-in-policies.md#monitoring) 기본 정책에 할당하는 것이 좋습니다. 또는 VM용 Azure Monitor를 사용하여 컴퓨터를 모니터링하려는 경우 [VM용 Azure Monitor 사용](../../governance/policy/samples/built-in-initiatives.md#monitoring) 이니셔티브를 대신 사용합니다.


## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="enable-update-management"></a>업데이트 관리 사용

1. Automation 계정의 **업데이트 관리** 아래에서 **업데이트 관리** 를 선택합니다.

2. Log Analytics 작업 영역 및 Automation 계정을 선택하고, **사용** 을 선택하여 업데이트 관리를 사용하도록 설정합니다. 설정을 완료하는 데 최대 15분이 걸립니다.

    ![업데이트 관리 사용](media/enable-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Azure VM을 사용하도록 설정

1. Automation 계정의 **업데이트 관리** 아래에서 **업데이트 관리** 를 선택합니다.

2. **+ Azure VM 추가** 를 선택하고, 목록에서 하나 이상의 VM을 선택합니다. 사용할 수 없는 가상 머신은 회색으로 표시되어 있으며 선택할 수 없습니다. Azure VM은 Automation 계정 위치와 상관없이 모든 지역에 있을 수 있습니다.

3. **사용** 을 선택하여 선택한 VM을 기능에 대한 컴퓨터 그룹 저장된 검색에 추가합니다.

    ![Azure VM을 사용하도록 설정](media/enable-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>비 Azure VM 사용

Azure Arc 지원 서버에 등록된 컴퓨터를 포함하여, Azure 외부에서 호스팅되는 컴퓨터 또는 서버의 경우 다음 단계를 수행하여 업데이트 관리를 사용하도록 설정합니다.  

1. Automation 계정의 **업데이트 관리** 아래에서 **업데이트 관리** 를 선택합니다.

2. **비 Azure 컴퓨터 추가** 를 클릭합니다. 이 작업은 컴퓨터에서 업데이트 관리에 대한 보고를 시작할 수 있도록 [Windows용 Log Analytics 에이전트 설치 및 구성 지침](../../azure-monitor/agents/log-analytics-agent.md)이 포함된 새 브라우저 창을 엽니다. 현재 Operations Manager에서 관리하는 컴퓨터를 사용하도록 설정하는 경우 새 에이전트가 필요하지 않습니다. 작업 영역 정보가 에이전트 구성에 추가됩니다.

## <a name="enable-machines-in-the-workspace"></a>작업 영역에서 컴퓨터 사용

수동으로 설치된 컴퓨터나, 이미 사용자의 작업 영역에 보고하는 컴퓨터는 Azure Automation에 추가해야 업데이트 관리를 사용할 수 있습니다.

1. Automation 계정의 **업데이트 관리** 아래에서 **업데이트 관리** 를 선택합니다.

2. **컴퓨터 관리** 를 선택합니다. 이전에 **사용 가능한 모든 향후 머신에서 사용** 옵션을 선택한 경우 **머신 관리** 단추가 회색으로 표시될 수 있습니다.

    ![저장된 검색](media/enable-from-automation-account/managemachines.png)

3. 업데이트 관리를 작업 영역에 보고하는 사용 가능한 모든 컴퓨터에 사용하도록 설정하려면 [컴퓨터 관리] 페이지에서 **사용 가능한 모든 컴퓨터에서 사용** 을 선택합니다. 이 작업은 컴퓨터를 개별적으로 추가하는 컨트롤을 사용하지 않도록 설정하고, 작업 영역에 보고하는 모든 컴퓨터를 `MicrosoftDefaultComputerGroup` 컴퓨터 그룹 저장된 검색 쿼리에 추가합니다. 이 작업을 선택하면 **컴퓨터 관리** 옵션이 사용하지 않도록 설정됩니다.

4. 사용 가능한 모든 향후 머신에서 기능을 사용하려면 **사용 가능한 모든 향후 머신에서 사용** 을 선택합니다. 이 옵션은 작업 영역에서 저장된 검색 및 범위 구성을 삭제하고, 현재 또는 향후에 해당 작업 영역에 보고하는 모든 Azure 및 비 Azure 컴퓨터가 기능에 포함되도록 허용합니다. 이 작업을 선택하면 사용 가능한 범위 구성이 없으므로 **컴퓨터 관리** 옵션이 영구적으로 사용하지 않도록 설정됩니다.

    > [!NOTE]
    > 이 옵션은 Log Analytics 내에 저장된 검색 및 범위 구성을 삭제하므로 이 옵션을 선택하기 전에 Log Analytics 작업 영역에서 삭제 잠금을 제거해야 합니다. 그렇지 않은 경우 옵션에서 구성을 제거할 수 없으므로 수동으로 제거해야 합니다.

5. 필요한 경우 초기 저장된 검색 쿼리를 다시 추가하여 범위 구성을 다시 추가할 수 있습니다. 자세한 내용은 [업데이트 관리 배포 범위 제한](scope-configuration.md)을 참조하세요.

6. 기능을 하나 이상의 컴퓨터에 사용하도록 설정하려면 **선택한 컴퓨터에서 사용** 을 선택하고, 각 컴퓨터 옆에 있는 **추가** 를 선택합니다. 이 작업은 선택한 머신 이름을 컴퓨터 그룹에서 저장된 기능 검색 쿼리에 추가합니다.

## <a name="next-steps"></a>다음 단계

* 업데이트 관리를 VM에 사용하려면 [VM에 대한 업데이트 및 패치 관리](manage-updates-for-vm.md)를 참조하세요.

* 업데이트 관리를 사용하여 더 이상 VM 또는 서버를 관리할 필요가 없는 경우 [업데이트 관리에서 VM 제거](remove-vms.md)를 참조하세요.

* 일반적인 업데이트 관리 오류를 해결하려면 [업데이트 관리 문제 해결](../troubleshoot/update-management.md)을 참조하세요.
