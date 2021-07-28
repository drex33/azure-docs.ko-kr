---
title: 읽기 복제본 관리 - Azure Portal - Azure Database for PostgreSQL - 단일 서버
description: Azure Portal에서 읽기 복제본 Azure Database for PostgreSQL - 단일 서버를 관리하는 방법에 대해 알아봅니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.openlocfilehash: 9fdef187e9bdf77b29c548f767a4b4edfeb62f44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422181"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Azure Portal에서 Azure Database for PostgreSQL - 단일 서버에서 읽기 복제본 제작 및 관리

이 문서에서는 Azure Portal에서 Azure Database for PostgreSQL의 읽기 복제본을 만들고 관리하는 방법에 대해 알아봅니다. 읽기 복제본에 대한 자세한 내용은 [개요](concepts-read-replicas.md)를 참조하세요.


## <a name="prerequisites"></a>사전 요구 사항
주 서버가 될 [Azure Database for PostgreSQL 서버](quickstart-create-server-database-portal.md) 입니다.

## <a name="azure-replication-support"></a>Azure 복제본 지원

[읽기 복제본](concepts-read-replicas.md)과 [논리 디코딩](concepts-logical.md)은 모두 정보에 대한 Postgres WAL(Write Ahead Log)에 따라 달라집니다. 이러한 두 기능에는 Postgres의 다른 로깅 수준이 필요합니다. 논리 디코딩에는 읽기 복제본보다 높은 수준의 로깅이 필요합니다.

올바른 로깅 수준을 구성하려면 Azure 복제본 지원 매개 변수를 사용합니다. Azure 복제본 지원에는 세 가지 설정 옵션이 있습니다.

* **해제** - WAL에 최소 정보를 저장합니다. 이 설정은 대부분의 Azure Database for PostgreSQL 서버에서 사용할 수 없습니다.  
* **복제본** - **해제** 보다 자세한 정보를 표시합니다. 이는 [읽기 복제본](concepts-read-replicas.md)이 작동하는 데 필요한 최소 로깅 수준입니다. 이 설정은 대부분의 서버에서 기본값입니다.
* **논리** - **복제본** 보다 자세한 정보를 표시합니다. 논리 디코딩이 작동하기 위한 최소 로깅 수준입니다. 읽기 복제본도 이 설정에서 작동합니다.


> [!NOTE]
> 지속적인 쓰기 집약적 주 워크로드에 대한 읽기 복제본을 배포할 때 복제 지연이 계속 증가할 수 있으며, 주 워크로드의 속도를 따라잡지 못할 수도 있습니다. WAL 파일은 복제본에서 수신될 때까지 삭제되지 않기 때문에 주 서버에서 스토리지 사용량이 증가할 수도 있습니다.

## <a name="prepare-the-primary-server"></a>주 서버 준비

1. Azure Portal에서 마스터로 사용할 기존 Azure Database for PostgreSQL 서버를 선택합니다.

2. 서버 메뉴에서 **복제본** 을 선택합니다. Azure 복제 지원이 **복제본** 이상으로 설정된 경우에는 읽기 복제본을 만들 수 있습니다. 

3. Azure 복제 지원이 **복제본** 이상으로 설정되어 있지 않은 경우에는 이를 설정해야 합니다. **저장** 을 선택합니다.

   :::image type="content" source="./media/howto-read-replicas-portal/set-replica-save.png" alt-text="Azure Database for PostgreSQL - 복제 - 복제본 설정 및 저장":::

4. 변경 내용을 적용하기 위해 서버를 다시 시작하려면 **예** 를 선택합니다.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-restart.png" alt-text="Azure Database for PostgreSQL - 복제 - 다시 시작 확인":::

5. 작업이 완료되면 두 개의 Azure Portal 알림을 받게 됩니다. 서버 매개 변수를 업데이트하기 위한 하나의 알림이 있습니다. 서버 다시 시작에 대한 다른 알림이 바로 이어집니다.

   :::image type="content" source="./media/howto-read-replicas-portal/success-notifications.png" alt-text="성공 알림":::

6. Azure Portal 페이지를 새로 고쳐 복제 도구 모음을 업데이트합니다. 이제 이 서버에 대한 읽기 복제본을 만들 수 있습니다.
   

## <a name="create-a-read-replica"></a>읽기 복제본 만들기
읽기 복제본을 만들려면 다음 단계를 수행합니다.

1. 주 서버로 사용할 기존 Azure Database for PostgreSQL 서버를 선택합니다. 

2. 서버 사이드바의 **설정** 에서 **복제** 를 선택합니다.

3. **복제본 추가** 를 선택합니다.

   :::image type="content" source="./media/howto-read-replicas-portal/add-replica.png" alt-text="복제본 추가":::

4. 읽기 복제본의 이름을 입력합니다. 

    :::image type="content" source="./media/howto-read-replicas-portal/name-replica.png" alt-text="복제본 이름 지정":::

5. 복제본의 위치를 선택합니다. 기본 위치는 주 서버의 위치와 같습니다.

    :::image type="content" source="./media/howto-read-replicas-portal/location-replica.png" alt-text="위치를 선택합니다":::

   > [!NOTE]
   > 복제본을 만들 수 있는 지역에 대해 자세히 알아보려면 [읽기 복제본 개념 문서](concepts-read-replicas.md)를 참조하세요. 

6. **확인** 을 선택하여 복제본 만들기를 확인합니다.

읽기 복제본을 만든 후에는 **복제** 창에서 확인할 수 있습니다.

:::image type="content" source="./media/howto-read-replicas-portal/list-replica.png" alt-text="복제 창에서 새 복제본 보기":::
 

> [!IMPORTANT]
> 다음으로 [읽기 복제본 개요의 고려 사항 섹션](concepts-read-replicas.md#considerations)을 검토합니다.
>
> 주 서버 설정을 새 값으로 업데이트하기 전에 복제본 설정을 같거나 큰 값으로 업데이트합니다. 이렇게 하면 복제본이 마스터에 대한 변경 내용을 유지할 수 있습니다.

## <a name="stop-replication"></a>복제 중지
주 서버와 읽기 복제본 간의 복제를 중지할 수 있습니다.

> [!IMPORTANT]
> 주 서버와 읽기 복제본에 대한 복제를 중지한 경우 실행 취소할 수 없습니다. 읽기 복제본은 읽기 및 쓰기를 둘 다 지원하는 독립 실행형 서버가 됩니다. 독립 실행형 서버를 다시 복제본으로 만들 수 없습니다.

Azure Portal에서 주 서버와 읽기 복제본 간의 복제를 중지하려면 다음 단계를 수행합니다.

1. Azure Portal에서 주 Azure Database for PostgreSQL 서버를 선택합니다.

2. 서버 메뉴의 **설정** 아래에서 **복제** 를 선택합니다.

3. 복제를 중지할 복제본 서버를 선택합니다.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="복제본 선택":::
 
4. **복제 중지** 를 선택합니다.

   :::image type="content" source="./media/howto-read-replicas-portal/select-stop-replication.png" alt-text="복제 중지 선택":::
 
5. **확인** 을 선택하여 복제를 중지합니다.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-stop-replication.png" alt-text="복제 중지 확인":::
 

## <a name="delete-a-primary-server"></a>주 서버 삭제
주 서버를 삭제하려면 독립 실행형 Azure Database for PostgreSQL 서버를 삭제할 때와 동일한 단계를 거칩니다. 

> [!IMPORTANT]
> 주 서버를 삭제하면 모든 읽기 복제본에 대한 복제가 중지됩니다. 그러면 읽기 복제본은 읽기와 쓰기를 모두 지원하는 독립 실행형 서버가 됩니다.

Azure Portal에서 서버를 삭제하려면 다음 단계를 수행합니다.

1. Azure Portal에서 주 Azure Database for PostgreSQL 서버를 선택합니다.

2. 서버의 **개요** 페이지를 엽니다. **삭제** 를 선택합니다.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-server.png" alt-text="서버 개요 페이지에서 주 서버 삭제를 선택합니다":::
 
3. 삭제할 주 서버의 이름을 입력합니다. 다음으로 **삭제** 를 선택하여 주 서버의 삭제를 확인합니다.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete.png" alt-text="주 서버 삭제 확인":::
 

## <a name="delete-a-replica"></a>복제본 삭제
주 서버를 삭제하는 방법과 유사하게 읽기 복제본을 삭제할 수 있습니다.

- Azure Portal에서 읽기 복제본에 대한 **개요** 페이지를 엽니다. **삭제** 를 선택합니다.

   :::image type="content" source="./media/howto-read-replicas-portal/delete-replica.png" alt-text="복제본 개요 페이지에서 복제본을 선택하여 삭제":::
 
다음 단계에 따라 **복제** 창에서 읽기 복제본을 삭제할 수도 있습니다.

1. Azure Portal에서 주 Azure Database for PostgreSQL 서버를 선택합니다.

2. 서버 메뉴의 **설정** 아래에서 **복제** 를 선택합니다.

3. 삭제할 읽기 복제본을 선택합니다.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica.png" alt-text="삭제할 복제본 선택":::
 
4. **복제본 삭제** 를 선택합니다.

   :::image type="content" source="./media/howto-read-replicas-portal/select-delete-replica.png" alt-text="복제본 삭제 선택":::
 
5. 삭제할 복제본의 이름을 입력합니다. **삭제** 를 선택하여 복제본 삭제를 확인합니다.

   :::image type="content" source="./media/howto-read-replicas-portal/confirm-delete-replica.png" alt-text="복제본 삭제 확인":::
 

## <a name="monitor-a-replica"></a>복제본 모니터링
두 메트릭을 사용하여 읽기 복제본을 모니터링할 수 있습니다.

### <a name="max-lag-across-replicas-metric"></a>복제본 간 최대 지연 시간 메트릭
**복제본 간 최대 지연 시간** 메트릭은 주 서버와 가장 오래 지연된 복제본 간의 지연 시간을 바이트 단위로 보여줍니다. 

1.  Azure Portal에서 주 Azure Database for PostgreSQL 서버를 선택합니다.

2.  **메트릭** 을 선택합니다. **메트릭** 창에서 **복제본 간 최대 지연 시간** 을 선택합니다.

    :::image type="content" source="./media/howto-read-replicas-portal/select-max-lag.png" alt-text="복제본 간 최대 지연 시간 모니터링":::
 
3.  **집계** 에 대해 **최대** 를 선택합니다.


### <a name="replica-lag-metric"></a>복제본 지연 시간 메트릭
**복제본 지연 시간** 메트릭은 복제본에서 마지막으로 재생된 트랜잭션 이후의 시간을 보여 줍니다. 마스터에서 트랜잭션이 발생하지 않으면 이 메트릭은 이 지연 시간을 반영합니다.

1. Azure Portal에서 Azure Database for PostgreSQL 읽기 복제본을 선택합니다.

2. **메트릭** 을 선택합니다. **메트릭** 창에서 **복제본 지연 시간** 을 선택합니다.

   :::image type="content" source="./media/howto-read-replicas-portal/select-replica-lag.png" alt-text="복제본 지연 시간 모니터링":::
 
3. **집계** 에 대해 **최대** 를 선택합니다. 
 
## <a name="next-steps"></a>다음 단계
* [Azure Database for PostgreSQL의 읽기 복제본](concepts-read-replicas.md)에 대해 자세히 알아봅니다.
* [Azure CLI 및 REST API에서 읽기 복제본을 만들고 관리하는 방법](howto-read-replicas-cli.md)을 알아봅니다.