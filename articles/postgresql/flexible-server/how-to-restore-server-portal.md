---
title: 복원 - Azure Portal - Azure Database for PostgreSQL - 유연한 서버
description: 이 문서에서는 Azure Portal을 통해 Azure Database for PostgreSQL에서 복원 작업을 수행하는 방법을 설명합니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/30/2021
ms.openlocfilehash: b385645ea8ec82d7e866e524e7edeedf45650cac
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133267630"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>유연한 서버의 특정 시점 복원



이 문서에서는 백업을 사용하여 유연한 서버에서 지정 시간 복구를 수행하는 단계별 절차를 제공합니다. 보존 기간 내의 최근 복원 지점이나 사용자 지정 복원 지점에 대해 수행할 수 있습니다.

## <a name="pre-requisites"></a>필수 구성 요소

이 방법 가이드를 완료하려면 다음이 필요합니다.

-   Azure Database for PostgreSQL - 유연한 서버가 있어야 합니다. 영역 중복성으로 구성된 유연한 서버에도 동일한 절차를 적용할 수 있습니다.

## <a name="restoring-to-the-latest-restore-point"></a>최근 복원 지점으로 복원

기존 백업을 사용하여 유연한 서버를 복원하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com/)에서 백업을 복원할 유연한 서버를 선택합니다.

2.  왼쪽 패널에서 **개요** 를 클릭하고 **복원** 을 클릭합니다.
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="복원 개요":::

3.  최근 복원 지점과 사용자 지정 복원 지점 중에서 선택할 수 있는 옵션이 있는 복원 페이지가 표시됩니다.

4.  **최근 복원 지점** 을 선택하고 **새 서버에 복원** 필드에 새 서버 이름을 입력합니다. 필요에 따라 복원할 가용성 영역을 선택할 수 있습니다.
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-latest.png" alt-text="최근 복원 시간":::

5.  **확인** 을 클릭합니다.

6.  복원 작업이 시작되었다는 알림이 표시됩니다.

## <a name="restoring-to-a-custom-restore-point"></a>사용자 지정 복원 지점으로 복원

기존 백업을 사용하여 유연한 서버를 복원하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com/)에서 백업을 복원할 유연한 서버를 선택합니다.

2.  개요 페이지에서 **복원** 을 클릭합니다.
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="복원 개요":::
    
3.  최근 복원 지점과 사용자 지정 복원 지점 중에서 선택할 수 있는 옵션이 있는 복원 페이지가 표시됩니다.

4.  **사용자 지정 복원 지점** 을 선택합니다.

5.  날짜 및 시간을 선택하고 **새 서버에 복원** 필드에 새 서버 이름을 입력합니다. 새 서버 이름을 제공하고 필요에 따라 복원할 **가용성 영역** 을 선택할 수 있습니다.
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom-2.png" alt-text="사용자 지정 복원 시간":::
 
6.  **확인** 을 클릭합니다.

7.  복원 작업이 시작되었다는 알림이 표시됩니다.

## <a name="performing-geo-restore-preview"></a>Geo-Restore 수행 (미리 보기)

원본 서버가 지역 중복 백업을 사용 하 여 구성 된 경우 쌍을 이루는 지역에 있는 서버를 복원할 수 있습니다. 첫 번째 복원의 경우 원본 서버가 생성 된 후 1 시간 이상 기다려야 합니다.

1.  [Azure Portal](https://portal.azure.com/)에서 백업을 지역에서 복원 하려는 유연한 서버를 선택 합니다.

2.  개요 페이지에서 **복원** 을 클릭합니다.
 :::image type="content" source="./media/how-to-restore-server-portal/geo-restore-click.png" alt-text="복원 클릭":::

3. 복원 페이지에서 Geo-Redundant 복원을 선택 하 여 쌍을 이루는 지역으로 복원 합니다. 
 :::image type="content" source="./media/how-to-restore-server-portal/geo-restore-choose-checkbox.png" alt-text="지역 복원 선택":::
 
4. 지역 및 데이터베이스 버전이 미리 선택 되어 있습니다. 쌍을 이루는 지역에서 사용 가능한 마지막 데이터로 복원 됩니다. 복원할 지역에서 **가용성 영역** 을 선택할 수 있습니다.

5. 기본적으로 복원 된 서버에 대 한 백업은 지역 중복 백업을 사용 하 여 구성 됩니다. 지역 중복 백업을 원하지 않는 경우 **서버 구성** 을 클릭 하 고 지역 중복 백업을 선택 취소할 수 있습니다.

6. 원본 서버가 **개인 액세스** 를 사용 하 여 구성 된 경우 원격 지역의 다른 VNET 으로만 복원할 수 있습니다. 기존 VNET을 선택 하거나 새 VNET을 만들고 서버를 해당 VNET으로 복원할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

-   [비즈니스 연속성](./concepts-business-continuity.md)에 대한 자세한 정보
-   [영역 중복 고가용성](./concepts-high-availability.md)에 대한 자세한 정보
-   [백업 및 복구](./concepts-backup-restore.md)에 대한 자세한 정보
