---
title: Azure Stack Edge Pro GPU 디바이스에서 VM 작업 모니터링
description: Azure Portal에서 Azure Stack Edge Pro GPU 디바이스의 VM 작업을 모니터링하는 방법을 알아봅니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: a73337ad261d8d2d7aef2c6292c7387d7c4cad9a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566965"
---
# <a name="monitor-vm-activity-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에서 VM 작업 모니터링

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro GPU 디바이스의 가상 머신용 Azure Portal에서 활동 로그를 보는 방법을 설명합니다.

> [!NOTE]
> 가상 머신의 **메트릭** 탭에서 작업 기간 동안 VM의 CPU 및 메모리 사용량을 확대할 수 있습니다. 자세한 내용은 [VM 메트릭 모니터링](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)을 참조하세요.

## <a name="view-activity-logs"></a>활동 로그 보기

Azure Stack Edge Pro GPU 디바이스에서 가상 머신에 대한 활동 로그를 보려면 다음 단계를 수행합니다.

1. 디바이스로 이동한 다음 **가상 머신** 으로 이동합니다. **활동 로그** 를 선택합니다.

    ![Azure Stack Edge 디바이스의 가상 머신에 대한 활동 로그 창을 보여 주는 스크린샷. 탐색 창에서 활동 로그가 강조 표시됩니다.](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-01.png)

    디바이스의 가상 머신에 대한 VM 게스트 로그가 표시됩니다.

1. 목록 위의 필터를 사용하여 보고 싶은 활동을 대상으로 지정합니다.

    ![가상 머신에 대한 활동 로그 페이지의 시간 범위 옵션을 보여 주는 스크린샷. 선택한 시간 범위가 강조 표시됩니다.](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-02.png)<!--Reshoot to remove pointer. Lightbox treatment?-->

1. 관련 작업을 보려면 작업 이름 옆에 있는 아래쪽 화살표를 클릭합니다.

    ![Azure Stack Edge 디바이스의 가상 머신에 대한 활동 로그 페이지를 보여 주는 스크린샷. 확장된 작업이 목록에서 강조 표시됩니다.](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-03.png)<!--Reshoot to remove pointer. May be able to replace drop-down only.-->

Azure의 모든 **활동 로그** 창에서 작업을 필터링 및 정렬하고, 표시할 열을 선택하고, 특정 작업에 대한 세부 정보로 드릴다운하고, 오류, 실패한 배포, 경고, 서비스 상태 및 지난 24시간 동안의 보안 변경 사항에 대한 **빠른 인사이트** 를 얻을 수 있습니다. 로그 및 필터링 옵션에 대한 자세한 내용은 [활동 로그 보기](../azure-resource-manager/management/view-activity-logs.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [VM 배포 문제 해결](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [지원 패키지에서 VM 게스트 로그 수집](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)