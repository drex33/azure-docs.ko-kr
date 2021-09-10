---
title: CPU, Azure Stack Edge Pro GPU 장치에서 VM에 대한 메모리 모니터링
description: Azure Portal에서 Azure Stack Edge Pro GPU 장치의 VM에 대한 CPU, 메모리 메트릭을 모니터링하는 방법을 알아봅니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: c58b7fd1cf4ab08dd8d645e4fc5f537d9ee937e3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528889"
---
# <a name="monitor-vm-metrics-for-cpu-memory-on-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU의 CPU, 메모리에 대한 VM 메트릭 모니터링

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro GPU 장치에서 가상 머신에 대한 CPU 및 메모리 메트릭을 모니터링 하는 방법을 설명합니다.

## <a name="about-vm-metrics"></a>VM 메트릭에 대하여

가상 컴퓨터에 대한 **메트릭** 탭을 사용하여 CPU 및 메모리 메트릭을 확인하고, 기간을 조정하고, 관심있는 기간에 확대할 수 있습니다.

VM 메트릭은 VM의 게스트 운영 체제에서 수집된 CPU 및 메모리 사용량 데이터를 기반으로 합니다. 리소스 사용량은 분당 한번 샘플링 됩니다.

장치의 연결이 끊기면 메트릭이 장치에 캐시됩니다. 장치가 다시 연결 되 면 메트릭이 캐시에서 푸시되 고 VM **메트릭** 이 업데이트 됩니다.

## <a name="monitor-cpu-and-memory-metrics"></a>CPU 및 메모리 메트릭 모니터링하기

1. Azure Portal에서 디바이스를 열고 **가상 머신** 으로 이동합니다. 가상 머신을 선택하고 **메트릭** 을 선택합니다.

    ![Azure Stack Edge 장치의 가상 컴퓨터에 대 한 메트릭 탭을 보여 주는 스크린샷. 메트릭 탭이 강조 표시됩니다.](media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-01.png)

2. 기본적으로 그래프는 이전 시간에 대한 평균 CPU와 메모리 사용량을 보여줍니다. 다른 기간에 대한 데이터를 보려면 **마지막 데이터 표시** 옆에 있는 다른 옵션을 선택합니다.

    ![Azure Stack Edge 장치의 가상 컴퓨터에 대 한 메트릭 탭의 스크린샷. 마지막 옵션에 대한 데이터 표시 및 선택한 값이 강조 표시됩니다.](./media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-02.png)

3. 차트의 아무 곳이나 마우스로 가리키면 손으로 세로선이 표시되며 왼쪽이나 오른쪽으로 이동하여 이전 또는 이후의 데이터 샘플을 볼 수 있습니다. 해당 기간에 대한 세부 정보 보기를 열려면 클릭하세요.

    ![가상 컴퓨터에 대한 메트릭 탭을 보여주는 스크린샷. 차트 영역을 마우스로 가리킬 때 표시되는 포인터가 강조 표시됩니다.](./media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-03.png)


## <a name="next-steps"></a>다음 단계

- [장치에서 VM 작업 모니터링](azure-stack-edge-gpu-monitor-virtual-machine-activity.md).
- [지원 패키지에서 VM 게스트 로그 수집](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md).
