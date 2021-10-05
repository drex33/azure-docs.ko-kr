---
title: 지역 간 Azure Automanage 가상 머신 이동
description: 지역 간에 자동으로 관리되는 가상 머신을 이동하는 방법을 알아봅니다.
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/05/2021
ms.custom: subject-moving-resources
ms.openlocfilehash: 657f7197f006612e17416ec2001adaeb3d7bef24
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456578"
---
# <a name="move-an-azure-automanage-virtual-machine-to-a-different-region"></a>Azure Automanage 가상 머신을 다른 지역으로 이동
이 문서에서는 VM(가상 머신)을 다른 지역으로 이동할 때 VM Automanage 설정을 유지하는 방법을 설명합니다. 가상 머신을 다른 지역으로 이동하는 것이 좋은 이유로는 새 Azure 지역을 활용하거나 내부 정책 및 거버넌스 요구 사항을 충족하거나 용량 계획 요구 사항에 대응하려는 경우가 있습니다. 이동하는 VM은 현재 자동 관리될 수 있으며 VM을 이동한 후에도 계속해서 자동 관리하려고 할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
* [Automanage에서 지원](./automanage-virtual-machines.md#prerequisites)되는 대상 지역인지 확인합니다.
* [여기](../automation/how-to/region-mappings.md)에서 Log Analytics 작업 영역 지역, Automation 계정 지역, 대상 지역이 지역 매핑에서 지원하는 모든 지역인지 확인합니다.

## <a name="prepare-your-automanaged-vms-for-moving"></a>자동으로 관리되는 VM의 이동 준비
자동으로 관리되는 VM에서 Automanage를 사용하지 않도록 설정합니다. Automanage 블레이드에서 VM을 선택하고 **자동 관리 사용 안 함** 을 클릭하여 이 작업을 수행할 수 있습니다.

## <a name="move-your-automanaged-vms-and-re-enable-automanage"></a>자동으로 관리되는 VM 이동 및 다시 Automanage 설정
VM을 이동하는 방법에 대한 세부 정보는 이 [문서](../resource-mover/tutorial-move-region-virtual-machines.md)를 참조하세요.

지역 간에 VM을 이동한 후에는 VM에 다시 Automanage를 설정할 수 있습니다. 세부 정보는 [여기](./automanage-virtual-machines.md#enabling-automanage-for-vms-in-azure-portal)에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Automanage에 관한 자세한 정보](./automanage-virtual-machines.md)
* [Azure Automanage에 관해 자주 묻는 질문과 대답 보기](./faq.yml)