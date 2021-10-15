---
title: Azure Stack Edge 디바이스 모니터링 | Microsoft Docs
description: Azure Portal 및 로컬 웹 UI를 사용하여 Azure Stack Edge 디바이스를 모니터링하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/11/2021
ms.author: alkohli
ms.openlocfilehash: 84ffadea8e8b6980b7bf311db9d2b7a2a36748b6
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047885"
---
# <a name="monitor-your-azure-stack-edge-device"></a>Azure Stack Edge 디바이스 모니터링

[!INCLUDE [applies-to-GPU-and-pro-r-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

이 문서에서는 Azure Stack Edge 디바이스를 모니터링하는 방법을 설명합니다. 디바이스를 모니터링하려면 Azure Portal 또는 로컬 웹 UI를 사용할 수 있습니다. Azure Portal 사용하여 메트릭을 보고, 디바이스 이벤트를 보고, 경고를 구성 및 관리합니다. 실제 디바이스에서 로컬 웹 UI를 사용하여 다양한 디바이스 구성 요소의 하드웨어 상태를 봅니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * 디바이스의 용량 및 트랜잭션 메트릭 보기
> * 디바이스 구성 요소의 하드웨어 상태 보기


## <a name="view-metrics"></a>메트릭 보기

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>디바이스의 메트릭

이 섹션에서는 디바이스의 모니터링 메트릭에 대해 설명합니다. 메트릭은 다음과 같습니다.

* 용량 메트릭. 용량 메트릭은 디바이스의 용량과 관련이 있습니다.

* 트랜잭션 메트릭. 트랜잭션 메트릭은 Azure Storage에 대한 읽기 및 쓰기 작업과 관련이 있습니다.

* Edge 컴퓨팅 메트릭. Edge 컴퓨팅 메트릭은 디바이스의 Edge 컴퓨팅 사용량과 관련이 있습니다.

메트릭의 전체 목록은 다음 표에 나와 있습니다.

|용량 메트릭                     |Description  |
|-------------------------------------|-------------|
|**사용 가능한 용량**               | 디바이스에 쓸 수 있는 데이터의 크기를 나타냅니다. 즉, 이 메트릭은 디바이스에서 사용할 수 있는 용량입니다. <br></br>디바이스와 클라우드 모두에 복사본이 있는 파일의 로컬 복사본을 삭제하여 디바이스 용량을 확보할 수 있습니다.        |
|**총 용량**                   | 로컬 캐시의 총 크기라고도 하는 데이터를 쓸 디바이스의 총 바이트를 나타냅니다. <br></br> 이제 데이터 디스크를 추가하여 기존 가상 디바이스의 용량을 늘릴 수 있습니다. VM에 대한 하이퍼바이저 관리를 통해 데이터 디스크를 추가한 다음, VM을 다시 시작합니다. 게이트웨이 디바이스의 로컬 스토리지 풀이 새로 추가된 데이터 디스크를 수용할 수 있도록 확장됩니다. <br></br>자세한 내용은 [Hyper-V 가상 머신용 하드 드라이브 추가](https://www.youtube.com/watch?v=EWdqUw9tTe4)로 이동하세요. |

|트랜잭션 메트릭              | Description         |
|-------------------------------------|---------|
|**클라우드 업로드 바이트 수(디바이스)**    | 디바이스의 모든 공유에 업로드된 모든 바이트의 합계입니다.        |
|**클라우드 업로드 바이트 수(공유)**     | 공유당 업로드된 바이트 수입니다. 이 메트릭은 다음과 같을 수 있습니다. <br></br> 평균 - 공유당 업로드된 모든 바이트의 합계/공유 수  <br></br>최대 - 공유에서 업로드된 최대 바이트 수 <br></br>최소 - 공유에서 업로드된 최소 바이트 수      |
|**클라우드 다운로드 처리량(공유)**| 공유당 다운로드된 바이트 수입니다. 이 메트릭은 다음과 같을 수 있습니다. <br></br> 평균 - 공유에서 읽거나 다운로드된 모든 바이트의 합계/공유 수 <br></br> 최대 - 공유에서 다운로드된 최대 바이트 수<br></br> 최소 - 공유에서 다운로드된 최소 바이트 수  |
|**클라우드 읽기 처리량**            | 디바이스의 모든 공유에 걸쳐 클라우드에서 읽은 모든 바이트의 합계입니다.     |
|**클라우드 업로드 처리량**          | 디바이스의 모든 공유에 걸쳐 클라우드에 쓴 모든 바이트의 합계입니다.     |
|**클라우드 업로드 처리량(공유)**  | 공유에서 클라우드에 쓴 모든 바이트 수의 합계/공유 수는 공유당 평균, 최대 및 최소입니다.      |
|**읽기 처리량 (네트워크)**           | 클라우드에서 읽은 모든 바이트 수에 대한 시스템 네트워크 처리량이 포함됩니다. 이 보기에는 공유로 제한되지 않는 데이터가 포함될 수 있습니다. <br></br>분할하면 연결되지 않았거나 사용하도록 설정되지 않은 어댑터를 포함하여 디바이스의 모든 네트워크 어댑터에 대한 트래픽이 표시됩니다.      |
|**쓰기 처리량 (네트워크)**       | 클라우드에 쓴 모든 바이트 수에 대한 시스템 네트워크 처리량이 포함됩니다. 이 보기에는 공유로 제한되지 않는 데이터가 포함될 수 있습니다. <br></br>분할하면 연결되지 않았거나 사용하도록 설정되지 않은 어댑터를 포함하여 디바이스의 모든 네트워크 어댑터에 대한 트래픽이 표시됩니다.          |

| Edge 컴퓨팅 메트릭              | Description         |
|-------------------------------------|---------|
|**Edge 컴퓨팅 - 메모리 사용량**      |           |
|**Edge 컴퓨팅 - CPU 사용률**    |         |


### <a name="view-device-events"></a>디바이스 이벤트 보기

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]


## <a name="view-hardware-status"></a>하드웨어 상태 보기

디바이스 구성 요소의 하드웨어 상태를 보려면 로컬 웹 UI에서 다음 단계를 수행합니다.

1. 디바이스의 로컬 웹 UI에 연결합니다.
2. **유지 관리 > 하드웨어 상태** 로 이동합니다. 다양한 디바이스 구성 요소의 상태를 볼 수 있습니다.

    ![하드웨어 상태 보기](media/azure-stack-edge-monitor/view-hardware-status.png)


## <a name="next-steps"></a>다음 단계

- [대역폭을 관리](azure-stack-edge-manage-bandwidth-schedules.md)하는 방법에 대해 알아봅니다.
- 경고 알림을 관리하는 방법을 [알아봅니다.](azure-stack-edge-gpu-manage-device-event-alert-notifications.md)