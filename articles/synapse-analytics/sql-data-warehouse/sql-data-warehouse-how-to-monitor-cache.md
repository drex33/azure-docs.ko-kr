---
title: Gen2 캐시 최적화
description: Azure Portal을 사용하여 Gen2 캐시를 모니터링하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: WilliamDAssafMSFT
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: wiassaf
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: ed5b0be7c9885e6fb84de110b7e9c848e398229c
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133363891"
---
# <a name="how-to-monitor-the-adaptive-cache"></a>적응형 캐시를 모니터링하는 방법

이 문서에서는 워크로드가 전용 SQL 풀의 적응형 캐시를 최적으로 활용하고 있는지 판단하여 느린 쿼리 성능을 모니터링하고 문제를 해결하는 방법에 대해 설명합니다.

전용 SQL 풀 스토리지 아키텍처는 NVMe 기반 SSD에 있는 캐시에 가장 자주 쿼리되는 columnstore 세그먼트를 자동으로 계층화합니다. 쿼리가 캐시에 있는 세그먼트를 검색하면 성능이 향상됩니다.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Azure Portal을 사용하여 문제 해결

Azure Monitor를 사용하여 캐시 메트릭을 보고 쿼리 성능 문제를 해결할 수 있습니다. 먼저 Azure Portal로 이동하여 **모니터링**, **메트릭** 및 **+ 범위 선택** 을 클릭합니다.

![Azure Portal의 메트릭에서 범위 선택을 선택한 화면을 보여 주는 스크린샷.](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

검색 및 드롭다운 막대를 사용하여 전용 SQL 풀을 찾습니다. 그런 다음, 적용을 선택합니다.

![데이터 웨어하우스를 선택할 수 있는 범위 선택 창을 보여 주는 스크린샷.](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

캐시 문제 해결에 대한 키 메트릭은 **캐시 적중 비율** 및 **캐시 사용 비율** 입니다. **캐시 적중 비율** 을 선택한 다음 **메트릭 추가** 단추를 사용하여 **캐시 사용 비율** 을 추가합니다. 

![캐시 메트릭](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>캐시 적중 및 사용 비율

아래 매트릭스에서는 캐시 메트릭의 값에 따라 시나리오를 설명합니다.

|                                | **높은 캐시 적중 비율** | **낮은 캐시 적중 비율** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **높은 캐시 사용 비율** |          시나리오 1           |          시나리오 2          |
| **낮은 캐시 사용 비율**  |          시나리오 3           |          시나리오 4          |

**시나리오 1:** 현재 캐시를 최적으로 사용하고 있습니다. 쿼리 속도를 저하시킬 수 있는 다른 영역의 [문제를 해결](sql-data-warehouse-manage-monitor.md)하세요.

**시나리오 2:** 현재 작업 중인 데이터 집합이 물리적 읽기로 인해 낮은 캐시 적중 비율을 초래할 수 있어 캐시에 적합하지 않습니다. 성능 수준의 규모를 확장하고 워크로드를 다시 실행하여 캐시를 채우는 것을 고려하세요.

**시나리오 3:** 캐시와 관련이 없는 이유로 인해 쿼리가 느리게 실행되는 것 같습니다. 쿼리 속도를 저하시킬 수 있는 다른 영역의 [문제를 해결](sql-data-warehouse-manage-monitor.md)하세요. 또한 캐시 크기를 줄여 비용을 절약하도록 [인스턴스 규모를 축소](sql-data-warehouse-manage-monitor.md)하는 것을 고려해 보세요. 

**시나리오 4:** 쿼리가 느려진 원인일 수 있는 콜드 캐시가 있습니다. 작업 데이터 세트가 캐시될 때 쿼리를 다시 실행해보세요. 

> [!IMPORTANT]
> 워크로드를 다시 실행한 후 캐시 적중 비율 또는 캐시 사용 비율이 업데이트되지 않으면 작업 집합이 이미 메모리에 있을 수 있습니다. 클러스터된 columnstore 테이블만 캐시됩니다.

## <a name="next-steps"></a>다음 단계
일반 쿼리 성능 튜닝에 대한 자세한 내용은 [쿼리 실행 모니터링](sql-data-warehouse-manage-monitor.md#monitor-query-execution)을 참조하세요.
