---
title: Azure Database for MySQL - 단일 서버의 주 버전 업그레이드
description: 이 문서에서는 Azure Database for MySQL - 단일 서버의 주 버전을 업그레이드하는 방법을 설명합니다.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/28/2021
ms.openlocfilehash: 0ab9eb86f6a482e167d5476205143a1e2f76ad66
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "122642162"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Azure Database for MySQL 단일 서버의 주 버전 업그레이드

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 ‘슬레이브’에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.
>

> [!IMPORTANT]
> Azure Database for MySQL 단일 서버에 대한 주 버전 업그레이드는 퍼블릭 미리 보기로 제공됩니다.

이 문서에서는 Azure Database for MySQL 단일 서버에서 MySQL 주 버전을 현재 위치로 업그레이드하는 방법을 설명합니다.

이 기능을 통해 고객은 데이터 이동이나 애플리케이션 연결 문자열 변경 없이 단추를 클릭하여 MySQL 5.6 서버를 MySQL 5.7로 현재 위치로 업그레이드할 수 있습니다.

> [!Note]
> * 주 버전 업그레이드는 MySQL 5.6에서 MySQL 5.7로의 주 버전 업그레이드에만 사용할 수 있습니다.
> * 업그레이드 작업 전체에서 서버를 사용할 수 없습니다. 따라서 계획된 유지 관리 기간 동안 업그레이드를 수행하는 것이 좋습니다. [읽기 복제본을 사용하여 MySQL 5.6에서 MySQL 5.7로 최소 가동 중지 시간 주 버전 업그레이드를 수행](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)하는 것이 좋습니다.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Azure Portal을 사용하여 MySQL 5.6에서 MySQL 5.7로 주 버전 업그레이드 수행

다음 단계에 따라 Azure Portal을 사용하여 Azure Database of MySQL 5.6 서버에 대한 주 버전 업그레이드를 수행합니다.

> [!IMPORTANT]
> 프로덕션을 직접 업그레이드하는 대신 서버의 복원된 복사본에서 먼저 업그레이드를 수행하는 것이 좋습니다. [지정 시간 복원을 수행하는 방법](howto-restore-server-portal.md#point-in-time-restore)을 참조하세요.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for MySQL 5.6 서버를 선택합니다.

2. **개요** 페이지의 도구 모음에서 **업그레이드** 단추를 클릭합니다.

3. **업그레이드** 섹션에서 **확인** 을 선택하여 Azure database for MySQL 5.6 서버를 5.7 서버로 업그레이드합니다.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL - 개요 - 업그레이드":::

4. 알림은 업그레이드가 성공적임을 확인합니다.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Azure CLI를 사용하여 MySQL 5.6에서 MySQL 5.7로 주 버전 업그레이드 수행

다음 단계에 따라 Azure CLI를 사용하여 Azure Database of MySQL 5.6 서버에 대한 주 버전 업그레이드를 수행합니다.

> [!IMPORTANT]
> 프로덕션을 직접 업그레이드하는 대신 서버의 복원된 복사본에서 먼저 업그레이드를 수행하는 것이 좋습니다. [지정 시간 복원을 수행하는 방법](howto-restore-server-cli.md#server-point-in-time-restore)을 참조하세요.

1. [Windows용 Azure CLI](/cli/azure/install-azure-cli)를 설치하거나 [Azure Cloud Shell](../cloud-shell/overview.md)에서 Azure CLI를 사용하여 업그레이드 명령을 실행합니다. 
 
   이 업그레이드를 수행하려면 Azure CLI 버전 2.16.0 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. az version을 실행하여 설치된 버전과 종속 라이브러리를 찾습니다. 최신 버전으로 업그레이드하려면 az upgrade를 실행합니다.

2. 로그인한 후 [az mysql server upgrade](/cli/azure/mysql/server#az_mysql_server_upgrade) 명령을 실행합니다.

   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   명령 프롬프트에 "-Running" 메시지가 표시됩니다. 이 메시지가 더 이상 표시되지 않는 경우 버전 업그레이드가 완료됩니다.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal"></a>Azure Portal을 사용하여 읽기 복제본에서 MySQL 5.6에서 MySQL 5.7로 주 버전 업그레이드 수행

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for MySQL 5.6 읽기 복제본 서버를 선택합니다.

2. **개요** 페이지의 도구 모음에서 **업그레이드** 단추를 클릭합니다.

3. **업그레이드** 섹션에서 **확인** 을 선택하여 Azure database for MySQL 5.6 읽기 복제본 서버를 5.7 서버로 업그레이드합니다.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL - 개요 - 업그레이드":::

4. 알림은 업그레이드가 성공적임을 확인합니다.

5. **개요** 페이지에서 Azure Database for MySQL 읽기 복제본 서버 버전이 5.7인지 확인합니다.

6. 이제 주 서버로 이동하여 [주 버전 업그레이드를 수행](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal)합니다.

## <a name="perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas"></a>읽기 복제본을 사용하여 MySQL 5.6에서 MySQL 5.7로 최소 가동 중지 시간 주 버전 업그레이드 수행

읽기 복제본을 사용하여 MySQL 5.6에서 MySQL 5.7로 최소 가동 중지 시간 주 버전 업그레이드를 수행할 수 있습니다. 이를 위해 먼저 서버의 읽기 복제본을 5.7로 업그레이드하고 나중에 애플리케이션을 장애 조치하여 읽기 본제본을 가리키도록 하고 새 주 복제본으로 만듭니다.

1. [Azure Portal](https://portal.azure.com/)에서 기존 Azure Database for MySQL 5.6을 선택합니다.

2. 주 서버에서 [읽기 복제본](./concepts-read-replicas.md#create-a-replica)을 만듭니다.

3. [읽기 복제본을 버전 5.7로 업그레이드](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal)합니다.

4. 복제본 서버가 버전 5.7에서 실행되고 있는지 확인하면 애플리케이션이 주 서버에 연결하지 못하게 합니다.
 
5. 복제 상태를 확인하고 모든 데이터가 동기화되도록 복제본을 주 복제본과 일치시키고 주 복제본에서 수행된 새 작업이 없는지 확인합니다.

   복제본 서버에서 [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) 명령을 호출하여 복제 상태를 확인합니다.

   ```sql
   SHOW SLAVE STATUS\G
   ```

   `Slave_IO_Running` 및 `Slave_SQL_Running`의 상태가 "yes"이고 `Seconds_Behind_Master`의 값이 "0"이면 복제가 제대로 작동하는 것입니다. `Seconds_Behind_Master`는 복제본이 얼마나 지연되었는지를 나타냅니다. 값이 "0"이 아니면 복제본 서버에서 업데이트를 처리하고 있는 것입니다. `Seconds_Behind_Master`가 "0"으로 확인되면 복제를 중지하는 것이 안전합니다.

6. [복제를 중지](./howto-read-replicas-portal.md#stop-replication-to-a-replica-server)하여 읽기 복제본을 주 복제본으로 승격합니다.

7. 애플리케이션에서 서버 5.7을 실행하는 새 주 복제본(이전 복제본)을 가리킵니다. 각 서버에는 고유한 연결 문자열이 있습니다. 원본 대신 (이전) 복제본을 가리키도록 애플리케이션을 업데이트합니다.

> [!Note]
> 이 시나리오에서는 4, 5 및 6단계 동안에만 가동 중지 시간이 발생합니다.


## <a name="frequently-asked-questions"></a>자주 묻는 질문

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>프로덕션 환경에서 업그레이드해야 하는 MySQL v5.6이 있습니다. 이 업그레이드 기능은 언제 GA되나요?

이 기능의 GA는 MySQL v5.6이 사용 중지되기 전에 예정되어 있습니다. 그러나 이 기능은 프로덕션 준비가 완료되었으며 Azure에서 완벽하게 지원되므로 사용자 환경에서 안심하고 실행할 수 있습니다. 권장 모범 사례로, 업그레이드하는 동안 가동 중지 시간을 예측할 수 있도록 서버의 복원된 복사본에서 먼저 실행하여 테스트하고, 프로덕션에서 실행하기 전에 애플리케이션 호환성 테스트를 수행하는 것이 좋습니다. 자세한 내용은 [지정 시간 복원을 수행](howto-restore-server-portal.md#point-in-time-restore)하여 서버의 지정 시간 복사본을 만드는 방법을 참조하세요. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>이로 인해 서버 가동 중지 시간이 발생하나요? 발생한다면 기간은 얼마나 되나요?

예, 업그레이드 프로세스 중에 서버를 사용할 수 없으므로 계획된 유지 관리 기간 동안 이 작업을 수행하는 것이 좋습니다. 예상 가동 중지 시간은 데이터베이스 크기, 프로비저닝된 스토리지 크기(프로비저닝된 IOP) 및 데이터베이스의 테이블 수에 따라 달라집니다. 업그레이드 시간은 서버의 테이블 수에 직접적으로 비례합니다. 기본 SKU 서버의 업그레이드는 표준 스토리지 플랫폼에 있기 때문에 시간이 더 오래 걸릴 것으로 예상됩니다. 서버 환경의 가동 중지 시간을 예측하려면 먼저 복원된 서버 복사본에서 업그레이드를 수행하는 것이 좋습니다. [읽기 복제본을 사용하여 MySQL 5.6에서 MySQL 5.7로 최소 가동 중지 시간 주 버전 업그레이드를 수행](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)하는 것이 좋습니다.

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>2021년 2월 5일 이전에 MySQL v5.6 서버를 업그레이드하지 않으면 어떻게 되나요?

이전처럼 MySQL v5.6 서버를 계속 실행할 수 있습니다. Azure는 서버에서 강제 업그레이드 수행하지 **않습니다**. 그러나 [Azure Database for MySQL 버전 관리 정책](concepts-version-policy.md)에 설명된 제한 사항이 적용됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Database for PostgreSQL 버전 관리 정책](concepts-version-policy.md)에 대해 자세히 알아봅니다.