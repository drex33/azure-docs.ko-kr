---
title: 크기 조정 작업 - Azure Portal - Azure Database for PostgreSQL - 유연한 서버
description: 이 문서에서는 Azure Portal을 통해 Azure Database for PostgreSQL에서 크기 조정 작업을 수행하는 방법을 설명합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/30/2021
ms.openlocfilehash: 3dd981ac3e16e2be556348c094283ac8e6118691
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133266960"
---
# <a name="scale-operations-in-flexible-server"></a>유연한 서버의 크기 조정 작업



이 문서에서는 컴퓨팅 및 스토리지에 대한 크기 조정 작업을 수행하는 단계를 제공합니다. 애플리케이션을 실행하는 데 적합한 vCores 수를 선택하는 것을 비롯하여 버스트 가능, 범용 및 메모리 최적화 SKU 간에 컴퓨팅 계층을 변경할 수 있습니다. 스토리지를 확장할 수도 있습니다. 예상 IOPS는 컴퓨팅 계층, vCores 및 스토리지 용량을 기준으로 표시됩니다. 비용 예측도 선택에 따라 표시됩니다.

> [!IMPORTANT]
> 스토리지를 스케일 다운할 수 없습니다.

## <a name="pre-requisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

-   Azure Database for PostgreSQL - 유연한 서버가 있어야 합니다. 영역 중복성으로 구성된 유연한 서버에도 동일한 절차를 적용할 수 있습니다.
> [!IMPORTANT]
> 고가용성으로 구성된 경우에는 버스트 가능한 SKU를 선택할 수 없습니다. 크기 조정 작업을 수행하는 동안 대기는 먼저 원하는 크기로 확장되고 기본 서버는 장애 조치(failover)되며 크기가 조정됩니다. 

## <a name="scaling-the-compute-tier-and-size"></a>컴퓨팅 계층 및 크기 조정

다음 단계에 따라 컴퓨팅 계층을 선택합니다.
 
1.  [Azure Portal](https://portal.azure.com/)에서 백업을 복원할 유연한 서버를 선택합니다.

2.  **컴퓨팅+스토리지** 를 클릭합니다.

3.  현재 설정이 있는 페이지가 표시됩니다.
 :::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="컴퓨팅+스토리지 보기":::

4.  버스트 가능, 범용 및 메모리 최적화 계층 사이에서 컴퓨팅 클래스를 선택할 수 있습니다.
   :::image type="content" source="./media/how-to-scale-compute-storage-portal/list-compute-tiers.png" alt-text="컴퓨팅 계층 나열":::


5.  기본 vCores 및 메모리 크기에 적합한 경우 다음 단계를 건너뛸 수 있습니다.

6.  vCores 수를 변경하려면 **컴퓨팅 크기** 의 드롭다운을 클릭하고 목록에서 원하는 vCores/메모리 수를 클릭하면 됩니다.
    
    - 버스트 가능 컴퓨팅 계층: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-burstable-dropdown.png" alt-text="버스트 가능 컴퓨팅":::

    - 범용 컴퓨팅 계층: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-general-purpose-dropdown.png" alt-text="범용 컴퓨팅":::

    - 메모리 최적화 컴퓨팅 계층: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-memory-optimized-dropdown.png" alt-text="메모리 최적화 컴퓨팅":::

7.  **저장** 을 클릭합니다. 
8.  확인 메시지가 표시됩니다. 계속하려면 **확인** 을 클릭합니다. 
9.  진행 중인 크기 조정 작업에 대한 알림입니다.


## <a name="scaling-storage-size"></a>스토리지 크기 조정

다음 단계에 따라 스토리지 크기를 늘립니다.

1.  [Azure Portal](https://portal.azure.com/)에서 스토리지 크기를 늘릴 유연한 서버를 선택합니다.
2.  **컴퓨팅+스토리지** 를 클릭합니다.

3.  현재 설정이 있는 페이지가 표시됩니다.
   
:::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="컴퓨팅+스토리지 클릭":::
4.  슬라이드 막대가 있는 **GiB의 스토리지 크기** 필드가 현재 크기로 표시됩니다.

5.  막대를 원하는 크기로 밉니다. 해당 IOPS 번호가 표시됩니다. IOPS는 컴퓨팅 계층 및 크기에 따라 달라집니다. 비용 정보도 표시됩니다. 

 :::image type="content" source="./media/how-to-scale-compute-storage-portal/storage-scaleup.png" alt-text="스토리지 스케일 업":::

6.  스토리지 크기에 적합한 경우 **저장** 을 클릭합니다. 
7.  확인 메시지가 표시됩니다. 계속하려면 **확인** 을 클릭합니다. 
8.  진행 중인 크기 조정 작업에 대한 알림입니다.

## <a name="next-steps"></a>다음 단계

-   [비즈니스 연속성](./concepts-business-continuity.md)에 대한 자세한 정보
-   [고가용성](./concepts-high-availability.md)에 대한 자세한 정보
-   [백업 및 복구](./concepts-backup-restore.md)에 대한 자세한 정보
