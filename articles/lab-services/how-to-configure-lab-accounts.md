---
title: Azure Lab Services에서 VM의 자동 종료 구성
description: 이 문서에서는 랩 계정에서 VM의 자동 종료를 구성하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 08/17/2020
ms.openlocfilehash: 40b4f3971d2fd9fc84337bb38d49d04212b3f5ba
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228528"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>랩 계정에서 VM 자동 종료 구성

여러 자동 종료 비용 제어 기능을 사용하도록 설정하여 가상 머신이 활발하게 사용되지 않을 때 추가 비용을 사전에 방지할 수 있습니다. 다음 세 가지 자동 종료 및 연결 끊기 기능을 결합하면 사용자가 실수로 가상 머신을 실행 중인 상태로 두는 경우를 대부분 포착할 수 있습니다.
 
- OS가 유휴 상태로 간주하는 가상 머신에서 사용자 연결을 자동으로 끊습니다.
- 사용자가 연결을 끊을 때 가상 머신을 자동으로 종료합니다.
- 시작되었지만 사용자가 연결하지 않는 가상 머신을 자동으로 종료합니다.

자동 종료 기능에 대한 추가 세부 정보는 [자동 종료 설정을 사용하여 비용 컨트롤 최대화](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) 섹션을 참조하세요.

> [!IMPORTANT]
> Linux 랩은 사용자의 연결을 끊고 Vm이 시작 될 때 자동 종료를 지원 하지만 사용자는 연결 하지 않습니다.  지원은 [특정 배포 및 Linux 버전](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions)에 따라 달라 집니다.  [Data Science Virtual Machine Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) 이미지에서는 종료 설정이 지원 되지 않습니다. 

## <a name="enable-automatic-shutdown"></a>자동 종료 사용

1. [Azure Portal](https://portal.azure.com/)에서 **랩 계정** 페이지로 이동합니다.
1. 왼쪽 메뉴에서 **랩 설정** 을 선택합니다.
1. 시나리오에 적합한 자동 종료 설정을 선택합니다.  

    > [!div class="mx-imgBorder"]
    > ![랩 계정에서 자동 종료 설정](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    설정은 랩 계정에서 생성된 모든 랩에 적용됩니다. 랩 작성자(교육자)는 랩 수준에서 이 설정을 재정의할 수 있습니다. 랩 계정에서 이 설정을 변경하면 변경 후에 생성된 랩에만 영향을 줍니다.

    설정을 사용하지 않으려면 이 페이지에서 해당 확인란의 선택을 취소합니다.

## <a name="next-steps"></a>다음 단계

랩 소유자가 랩 수준에서 이 설정을 구성하거나 재정의하는 방법을 알아보려면 [랩에 대해 VM 자동 종료 구성](how-to-enable-shutdown-disconnect.md)을 참조하세요.
