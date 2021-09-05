---
title: 일반적인 Azure Automanage 온보딩 오류 문제 해결
description: 일반적인 Automanage 온보딩 오류와 문제 해결 방법
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 7acca746acf17a6ef6dd837907a159cddb20be10
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113004199"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>일반적인 Automanage 온보딩 오류 문제 해결
Automanage에서 컴퓨터를 서비스에 온보딩하지 못할 수 있습니다. 이 문서에서는 배포 실패 문제를 해결하는 방법, 배포가 실패할 수 있는 몇 가지 일반적인 이유, 잠재적인 완화 단계를 설명합니다.

## <a name="troubleshooting-deployment-failures"></a>배포 실패 문제 해결
Automanage에 컴퓨터를 온보딩하면 Azure Resource Manager 배포가 생성됩니다. 자세한 내용은 배포에서 실패한 이유에 대한 자세한 내용을 참조하세요. 아래 그림에 있는 실패 세부 정보 플라이아웃에 배포에 대한 링크가 있습니다.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Automanage 실패 세부 정보 플라이아웃.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-machine"></a>실패한 머신이 포함된 리소스 그룹의 배포를 확인합니다.
실패 플라이아웃에는 온보딩에 실패한 컴퓨터를 포함한 리소스 그룹의 배포에 대한 링크가 포함됩니다. 또한 플라이아웃에는 배포를 필터링하는 데 사용할 수 있는 접두사 이름이 포함됩니다. 배포 링크를 클릭하면 배포 블레이드로 이동하며, 여기에서 배포를 필터링하여 컴퓨터에 대한 Automanage 배포를 확인할 수 있습니다. 여러 지역에 걸쳐 배포하는 경우 올바른 지역에서 배포를 클릭해야 합니다.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-machine"></a>실패한 머신이 포함된 구독의 배포를 확인합니다.
리소스 그룹 배포에 실패가 표시되지 않으면 다음 단계는 온보딩에 실패한 머신을 포함하는 구독의 배포를 확인하는 것입니다. 실패 플라이아웃의 **구독에 대한 배포** 링크를 클릭하고 **Automanage-DefaultResourceGroup** 필터를 사용하여 배포를 필터링합니다. 실패 블레이드의 리소스 그룹 이름을 사용하여 배포를 필터링합니다. 배포 이름 뒤에는 지역 이름이 붙습니다. 여러 지역에 걸쳐 배포하는 경우 올바른 지역에서 배포를 클릭해야 합니다.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Log Analytics 작업 영역에 연결된 구독에서 배포 확인
실패한 머신을 포함하는 리소스 그룹 또는 구독에서 실패한 배포가 표시되지 않고, 실패한 머신이 다른 구독의 Log Analytics 작업 영역에 연결된 경우 Log Analytics 작업 영역에 연결된 구독으로 이동하여 실패한 배포를 확인합니다.

## <a name="common-deployment-errors"></a>일반적인 배포 오류

오류 |  완화 방법
:-----|:-------------|
Automanage 계정 권한 부족 오류 | 이 오류는 최근에 새 Automanage 계정을 포함한 구독을 새 테넌트로 이동한 경우에 발생할 수 있습니다. 이 오류를 해결하는 단계는 [여기](./repair-automanage-account.md)에 있습니다.
지역 매핑 요구 사항이 일치하지 않는 작업 영역 | 컴퓨터가 현재 연결되어 있는 Log Analytics 작업 영역이 지원되는 Automation 지역에 매핑되어 있지 않으므로 Automanage에서 컴퓨터를 온보딩할 수 없습니다. 기존 Log Analytics 작업 영역 및 Automation 계정이 [지원되는 지역 매핑](../automation/how-to/region-mappings.md)에 있는지 확인합니다.
“‘관리되는 애플리케이션에서 만든 시스템 거부 할당’ 이름이 포함된 거부 할당으로 인해 액세스가 거부됨” | 리소스에 [denyAssignment](../role-based-access-control/deny-assignments.md)가 만들어져 Automanage에서 리소스에 액세스하지 못했습니다. 이 denyAssignment는 [청사진](../governance/blueprints/concepts/resource-locking.md) 또는 [관리되는 애플리케이션](../azure-resource-manager/managed-applications/overview.md)에서 만들었을 수 있습니다.
"OS Information: Name='(null)', ver='(null)', agent status='Not Ready'." | [지원되는 최소 에이전트 버전](/troubleshoot/azure/virtual-machines/support-extensions-agent-version)을 실행 중이고, 에이전트가 실행 중이며([Linux](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) 및 [Windows](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)), 에이전트 최신 상태([Linux](../virtual-machines/extensions/update-linux-agent.md) 및 [Windows](../virtual-machines/extensions/agent-windows.md))인지 확인합니다.
"VM OS 이름: , 버전 에 대한 OS를 확인할 수 없습니다. VM 에이전트가 실행 중이며 현재 상태가 준비 상태인지 확인하세요." | [지원되는 최소 에이전트 버전](/troubleshoot/azure/virtual-machines/support-extensions-agent-version)을 실행 중이고, 에이전트가 실행 중이며([Linux](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) 및 [Windows](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)), 에이전트 최신 상태([Linux](../virtual-machines/extensions/update-linux-agent.md) 및 [Windows](../virtual-machines/extensions/agent-windows.md))인지 확인합니다.
"확장 'IaaSAntimalware'를 처리하는 동안 VM이 오류를 보고했습니다." | 다른 맬웨어 방지/바이러스 백신 제품이 VM에 이미 설치되어 있지 않은지 확인합니다. 실패한 경우 고객 지원팀에 문의하세요.
ASC 작업 영역: Automanage에서 현재 _위치_ 에서 Log Analytics 서비스를 지원하지 않습니다. | VM이 [지원되는 지역](./automanage-virtual-machines.md#supported-regions)에 있는지 확인합니다.
정책 위반으로 인해 템플릿을 배포하지 못했습니다. 자세한 내용은 세부 정보를 참조하세요. | Automanage가 VM을 등록하지 못하도록 하는 정책이 있습니다. Automanage에 온보딩하려는 VM을 포함하는 구독 또는 리소스 그룹에 적용된 정책을 확인합니다.
"할당하지 못했습니다. 사용할 수 있는 추가 정보가 없습니다." | Microsoft Azure 지원을 통해 사례를 여세요.

## <a name="next-steps"></a>다음 단계

* [Azure Automation 자세히 알아보기](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Azure Portal에서 머신용 Automanage 사용](quick-create-virtual-machines-portal.md)