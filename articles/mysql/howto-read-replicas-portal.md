---
title: 읽기 복제본 관리 - Azure Portal - Azure Database for MySQL
description: Azure Portal을 사용하여 Azure Database for MySQL에서 읽기 복제본을 설정하고 관리하는 방법을 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2020
ms.openlocfilehash: a1e8f9975a6b87767a1c8dfef6603d21a3fe80ec
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642175"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MySQL에서 읽기 복제본을 만들고 관리하는 방법

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

이 문서에서는 Azure Portal을 사용하여 Azure Database for MySQL 서비스에서 읽기 복제본을 만들고 관리하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

- 원본 서버로 사용할 [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-portal.md)

> [!IMPORTANT]
> 읽기 복제본 기능은 범용 또는 메모리 최적화 가격 책정 계층의 Azure Database for MySQL 서버에서만 사용 가능합니다. 원본 서버가 이러한 가격 책정 계층 중 하나에 포함되어 있는지 확인합니다.

## <a name="create-a-read-replica"></a>읽기 복제본 만들기

> [!IMPORTANT]
> 기존 복제본이 없는 원본에 대한 복제본을 만드는 경우 원본이 먼저 다시 시작하여 자체적으로 복제할 준비를 합니다. 이를 고려하고 사용량이 적은 기간 동안 이러한 작업을 수행합니다.
>
>주 서버에서 GTID를 사용하는 경우(`gtid_mode` = ON) 새로 만든 복제본도 GTID를 사용하도록 설정하고 GTID 기반 복제를 사용합니다. 자세한 내용은 [GTID(글로벌 트랜잭션 식별자)](concepts-read-replicas.md#global-transaction-identifier-gtid)를 참조하세요.

다음 단계에 따라 읽기 복제본 서버를 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 마스터로 사용할 기존 Azure Database for MySQL 서버를 선택합니다. 이 작업은 **개요** 페이지를 엽니다.

3. 메뉴의 **설정** 아래에서 **복제** 를 선택합니다.

4. **복제본 추가** 를 선택합니다.

   :::image type="content" source="./media/howto-read-replica-portal/add-replica.png" alt-text="Azure Database for MySQL - 복제":::

5. 복제본 서버의 이름을 입력합니다.

    :::image type="content" source="./media/howto-read-replica-portal/replica-name.png" alt-text="Azure Database for MySQL - 복제본 이름":::

6. 복제본 서버의 위치를 선택합니다. 기본 위치는 원본 서버의 위치와 같습니다.

    :::image type="content" source="./media/howto-read-replica-portal/replica-location.png" alt-text="Azure Database for MySQL - 복제본 위치":::

   > [!NOTE]
   > 복제본을 만들 수 있는 지역에 대해 자세히 알아보려면 [읽기 복제본 개념 문서](concepts-read-replicas.md)를 참조하세요. 

7. **확인** 을 선택하여 복제본 만들기를 확인합니다.

> [!NOTE]
> 읽기 복제본은 마스터와 같은 서버 구성을 사용하여 생성됩니다. 복제본이 생성된 후에 복제본 서버 구성을 변경할 수 있습니다. 복제본 서버는 항상 원본 서버와 동일한 리소스 그룹 및 동일한 구독에 생성됩니다. 다른 리소스 그룹 또는 다른 구독에 복제본 서버를 만들려면 복제본 서버를 만든 후에 [이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)할 수 있습니다. 복제본이 마스터를 따라갈 수 있도록 복제본 서버 구성을 원본과 같거나 더 큰 값으로 유지하는 것이 좋습니다.

생성된 복제본 서버는 **복제** 블레이드에서 확인할 수 있습니다.

   :::image type="content" source="./media/howto-read-replica-portal/list-replica.png" alt-text="Azure Database for MySQL - 복제본 나열":::

## <a name="stop-replication-to-a-replica-server"></a>복제본 서버로의 복제 중지

> [!IMPORTANT]
> 서버로의 복제는 중지하고 나면 취소할 수 없습니다. 원본과 복제본 사이의 복제가 중단된 경우, 중단을 취소할 수 없습니다. 복제를 중지하고 나면 복제본 서버는 독립 실행형 서버가 되어 읽기와 쓰기를 모두 지원합니다. 이 서버를 다시 복제본으로 설정할 수는 없습니다.

Azure Portal에서 원본과 복제본 서버 간의 복제를 중지하려면 다음 단계를 수행합니다.

1. Azure Portal에서 원본 Azure Database for MySQL 서버를 선택합니다. 

2. 메뉴의 **설정** 아래에서 **복제** 를 선택합니다.

3. 복제를 중지할 복제본 서버를 선택합니다.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-select.png" alt-text="Azure Database for MySQL - 복제 중지 서버 선택":::

4. **복제 중지** 를 선택합니다.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication.png" alt-text="Azure Database for MySQL - 복제 중지":::

5. **확인** 을 클릭하여 복제 중지를 확인합니다.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-confirm.png" alt-text="Azure Database for MySQL - 복제 중지 확인":::

## <a name="delete-a-replica-server"></a>복제본 서버 삭제

Azure Portal에서 읽기 복제본 서버를 삭제하려면 다음 단계를 수행합니다.

1. Azure Portal에서 원본 Azure Database for MySQL 서버를 선택합니다.

2. 메뉴의 **설정** 아래에서 **복제** 를 선택합니다.

3. 삭제할 복제본 서버를 선택합니다.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-select.png" alt-text="Azure Database for MySQL - 복제본 삭제 서버 선택":::

4. **복제본 삭제** 를 선택합니다.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica.png" alt-text="Azure Database for MySQL - 복제본 삭제":::

5. 복제본의 이름을 입력하고 **삭제** 를 클릭하여 복제본 삭제를 확인합니다.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-confirm.png" alt-text="Azure Database for MySQL - 복제본 삭제 확인":::

## <a name="delete-a-source-server"></a>원본 서버 삭제

> [!IMPORTANT]
> 원본 서버를 삭제하면 모든 복제본 서버에 대한 복제가 중지되며 원본 서버 자체도 삭제됩니다. 그러면 복제본 서버는 읽기와 쓰기를 모두 지원하는 독립 실행형 서버로 설정됩니다.

Azure Portal에서 원본 서버를 삭제하려면 다음 단계를 수행합니다.

1. Azure Portal에서 원본 Azure Database for MySQL 서버를 선택합니다.

2. **개요** 에서 **삭제** 를 선택합니다.

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-overview.png" alt-text="Azure Database for MySQL - 마스터 삭제":::

3. 원본 서버의 이름을 입력하고 **삭제** 를 클릭하여 원본 서버 삭제를 확인합니다.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-confirm.png" alt-text="Azure Database for MySQL - 마스터 삭제 확인":::

## <a name="monitor-replication"></a>복제 모니터링

1. [Azure Portal](https://portal.azure.com/)에서 모니터링할 복제본 Azure Database for MySQL 서버를 선택합니다.

2. 사이드바의 **모니터링** 섹션에서 **메트릭** 을 선택합니다.

3. 사용 가능한 메트릭의 드롭다운 목록에서 **복제 지연 시간(초)** 를 선택합니다.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-select-replication-lag.png" alt-text="복제 지연 시간 선택":::

4. 확인할 시간 범위를 선택합니다. 아래 그림에서는 시간 범위로 30분이 선택되어 있습니다.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="시간 범위 선택":::

5. 선택한 시간 범위의 복제 지연 시간을 확인합니다. 아래 그림에는 지난 30분 동안의 복제 지연 시간이 표시되어 있습니다.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="시간 범위 30분 선택":::

## <a name="next-steps"></a>다음 단계

- [읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아보기