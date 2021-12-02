---
title: 백업 및 복원 - Azure Database for MySQL
description: Azure Database for MySQL 서버를 자동 백업하고 복원하는 방법을 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/27/2020
ms.custom: references_regions
ms.openlocfilehash: f75fffc54aa109cc94448aa2226d1625c5e1e3c0
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133437475"
---
# <a name="backup-and-restore-in-azure-database-for-mysql"></a>Azure Database for MySQL의 백업 및 복원

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL은 자동으로 서버 백업을 만들어 사용자가 로컬로 구성한 중복 스토리지 또는 지역 중복 스토리지에 저장합니다. 백업을 사용하여 특정 시점의 서버를 복원할 수 있습니다. 백업 및 복원은 실수로 인한 손상이나 삭제로부터 데이터를 보호하므로 비즈니스 연속성 전략의 필수적인 부분입니다.

## <a name="backups"></a>Backup

Azure Database for MySQL은 데이터 파일과 트랜잭션 로그를 백업합니다. 이러한 백업을 사용하면 서버를 구성된 백업 보존 기간 내의 특정 시점으로 복원할 수 있습니다. 기본 백업 보존 기간은 7일입니다. 필요에 따라 최대 35일까지 [구성](howto-restore-server-portal.md#set-backup-configuration)할 수 있습니다. 모든 백업은 AES 256비트 암호화를 사용하여 암호화됩니다.

이러한 백업 파일은 사용자에게 노출되지 않으며 내보낼 수 없습니다. 이러한 백업은 Azure Database for MySQL의 복원 작업에만 사용할 수 있습니다. [mysqldump](concepts-migrate-dump-restore.md)를 사용하여 데이터베이스를 복사할 수 있습니다.

백업 유형 및 빈도는 서버의 백엔드 스토리지에 따라 달라집니다.

### <a name="backup-type-and-frequency"></a>백업 유형 및 빈도

#### <a name="basic-storage-servers"></a>기본 스토리지 서버

기본 스토리지는 [기본 계층 서버](concepts-pricing-tiers.md)를 지원하는 백엔드 스토리지입니다. 기본 스토리지 서버에 대한 백업은 스냅샷을 기반으로 합니다. 전체 데이터베이스 스냅샷은 매일 수행됩니다. 기본 스토리지 서버에서는 차등 백업이 수행되지 않습니다. 모든 스냅샷 백업은 전체 데이터베이스 백업만 수행됩니다.

트랜잭션 로그 백업은 5분마다 발생합니다.

#### <a name="general-purpose-storage-v1-servers-supports-up-to-4-tb-storage"></a>범용 스토리지 v1 서버(최대 4TB의 스토리지 지원)

범용 스토리지는 [범용](concepts-pricing-tiers.md) 및 [메모리 최적화 계층](concepts-pricing-tiers.md) 서버를 지원하는 백엔드 스토리지입니다. 최대 4TB의 범용 스토리지 서버에서 전체 백업은 매주 한 번 수행됩니다. 차등 백업은 하루 두 번 수행됩니다. 트랜잭션 로그 백업은 5분마다 발생합니다. 최대 4TB 스토리지의 범용 스토리지에 대한 백업은 스냅샷을 기반으로 하지 않으며 백업 시 IO 대역폭을 소비합니다. 4TB 스토리지에서 1TB를 초과하는 대형 데이터베이스의 경우 다음을 고려하는 것이 좋습니다.

- 백업 IO를 고려해서 더 많은 IOPs를 프로비전합니다. 또는
- 또는 기본 스토리지 인프라를 원하는 [Azure 지역](./concepts-pricing-tiers.md#storage)에서 사용할 수 있는 경우 최대 16TB 스토리지를 지원하는 범용 스토리지로 마이그레이션합니다. 최대 16TB 스토리지를 지원하는 범용 스토리지에는 추가 비용이 없습니다. 16TB 스토리지로의 마이그레이션 지원이 필요하면 Azure Portal에서 지원 티켓을 엽니다.

#### <a name="general-purpose-storage-v2-servers-supports-up-to-16-tb-storage"></a>범용 스토리지 v2 서버(최대 16TB의 스토리지 지원)

[Azure 지역](./concepts-pricing-tiers.md#storage) 하위 집합에서 새로 프로비전되는 모든 서버는 최대 16TB 스토리지의 범용 스토리지를 지원할 수 있습니다. 즉, 최대 16TB 스토리지가 지원되는 모든 [지역](concepts-pricing-tiers.md#storage)의 기본 범용 스토리지입니다. 기본 16TB 스토리지 서버에서의 백업은 스냅샷을 기반으로 합니다. 첫 번째 전체 스냅샷 백업은 서버를 만든 직후에 예약됩니다. 스냅샷 백업은 매일 한 번 수행됩니다. 트랜잭션 로그 백업은 5분마다 발생합니다.

기본/범용 스토리지에 대한 자세한 내용은 [스토리지 설명서](./concepts-pricing-tiers.md#storage)를 참조하세요.

### <a name="backup-retention"></a>백업 보존

백업은 서버의 백업 보존 기간에 따라 보존됩니다. 보존 기간은 7~35일 사이로 선택할 수 있습니다. 기본 보존 기간은 7일입니다. 서버 생성 중에 또는 [Azure Portal](./howto-restore-server-portal.md#set-backup-configuration) 또는 [Azure CLI](./howto-restore-server-cli.md#set-backup-configuration)를 사용하여 나중에 백업 구성을 업데이트하여 보존 기간을 설정할 수 있습니다.

백업 보존 기간은 사용 가능한 백업을 기반으로 하기 때문에 특정 시점 복원을 검색할 수 있는 시간을 제어합니다. 백업 보존 기간은 복원 관점에서 복구 기간으로 취급될 수도 있습니다. 백업 보존 기간 내에 지정 시간 복구를 수행하기 위해 필요한 모든 백업이 백업 스토리지에 보존됩니다. 예를 들어 백업 보존 기간이 7일로 설정되었으면 복구 기간이 최근 7일로 간주됩니다. 이 시나리오에서는 최근 7일 동안 서버 복원에 필요한 모든 백업이 보존됩니다. 백업 보존 기간 7일 동안 다음이 수행됩니다.

- 범용 스토리지 v1 서버(최대 4TB의 스토리지 지원)는 가장 먼저 수행된 전체 데이터베이스 백업 이후 수행된 최대 2개의 전체 데이터베이스 백업과 모든 차등 백업, 트랜잭션 로그 백업을 보존합니다.
- 범용 스토리지 v2 서버(최대 16TB의 스토리지 지원)는 지난 8일 동안의 전체 데이터베이스 스냅샷과 트랜잭션 로그 백업을 보존합니다.

#### <a name="long-term-retention"></a>장기 보존

35일을 초과하는 장기 백업 보존은 현재까지 아직 서비스에서 기본적으로 지원되지 않습니다. 장기 보존을 위해서는 mysqldump를 사용하여 백업을 만들고 저장할 수 있는 방법이 있습니다. 이를 수행하는 방법은 저희 지원팀에서 올린 [단계별 안내 문서](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/automate-backups-of-your-azure-database-for-mysql-server-to/ba-p/1791157) 블로그 게시물을 참조하세요.

### <a name="backup-redundancy-options"></a>백업 중복 옵션

Azure Database for MySQL은 범용 및 메모리 최적화 계층에서 로컬로 중복되거나 지리적으로 중복된 백업 스토리지 중에서 선택할 수 있는 유연성을 제공합니다. 백업이 지역 중복 백업 스토리지에 저장되면 서버가 호스팅되는 지역에 저장될 뿐만 아니라 [쌍으로 연결된 데이터 센터](../availability-zones/cross-region-replication-azure.md)에도 복제됩니다. 이러한 지리적 중복성은 재해에 대비하여 다른 지역에서 서버를 복원할 수 있는 기능과 더 나은 보호 기능을 제공합니다. 기본 계층은 로컬 중복 백업 스토리지만 제공합니다.

> [!NOTE]
>인도 중부, 프랑스 중부, 아랍에미리트 북부, 남아프리카 공화국 북부 지역의 경우 범용 스토리지 v2 스토리지가 공개 미리 보기로 제공됩니다. 위에서 언급한 지역에서 범용 스토리지 v2(최대 16TB의 스토리지 지원)에서 원본 서버를 만드는 경우 지역 중복 백업을 사용하도록 설정할 수 없습니다. 

#### <a name="moving-from-locally-redundant-to-geo-redundant-backup-storage"></a>로컬 중복성에서 지리적 중복성 백업 스토리지로 이동

백업을 위한 로컬 중복 또는 지역 중복 스토리지를 구성하는 것은 서버를 만드는 동안에만 허용됩니다. 서버가 프로비전되면 백업 스토리지 중복 옵션을 변경할 수 없습니다. 백업 스토리지를 로컬 중복 스토리지에서 지리적 중복 스토리지로 이동하기 위해 지원되는 유일한 방법은 새 서버를 만들고 [덤프 및 복원](concepts-migrate-dump-restore.md)을 사용하여 데이터를 마이그레이션하는 방법입니다.

### <a name="backup-storage-cost"></a>백업 스토리지 비용

Azure Database for MySQL은 추가 비용 없이 최대 100%의 프로비전된 서버 스토리지를 백업 스토리지로 제공합니다. 사용되는 모든 추가 백업 스토리지는 매월 GB 단위로 청구됩니다. 예를 들어 250GB 스토리지 서버를 프로비전한 경우 서버 백업에 250GB를 추가 비용 없이 추가 스토리지로 이용할 수 있습니다. 250GB를 초과하여 백업에 소비된 스토리지는 [가격 책정 모델](https://azure.microsoft.com/pricing/details/mysql/)에 따라 비용이 청구됩니다.

Azure Portal에서 제공되는 Azure Monitor의 [사용된 백업 스토리지](concepts-monitoring.md) 메트릭을 사용하여 서버에서 사용된 백업 스토리지를 모니터할 수 있습니다. 사용된 백업 스토리지 메트릭은 서버에 설정된 백업 보존 기간에 따라 보존된 모든 전체 데이터베이스 백업, 차등 백업, 로그 백업에 사용된 스토리지의 합계를 나타냅니다. 백업 빈도는 서비스로 관리되며 앞에서 설명되었습니다. 서버에서 과도한 트랜잭션 작업을 수행하면 전체 데이터베이스 크기에 관계없이 백업 스토리지 사용량이 증가할 수 있습니다. 지리적 중복 스토리지의 경우 백업 스토리지 사용량은 로컬 중복 스토리지의 두 배입니다.

백업 스토리지 비용을 제어하기 위한 기본 방법은 적합한 백업 보존 기간을 설정하고 원하는 복구 목표에 맞게 올바른 백업 중복성 옵션을 선택하는 것입니다. 보존 기간은 7~35일 사이로 선택할 수 있습니다. 범용 및 메모리 최적화 서버는 백업에 대해 지리적 중복 스토리지를 사용하도록 선택할 수 있습니다.

## <a name="restore"></a>복원

Azure Database for MySQL에서 복원을 수행하면 원래 서버의 백업으로부터 새 서버가 생성되고 이 서버에 포함된 모든 데이터베이스를 복원합니다.

사용할 수 있는 두 가지 유형의 복원이 있습니다.

- **지정 시간 복구** 는 두 백업 중복성 옵션 모두 제공되며 전체 및 트랜잭션 로그 백업 조합을 사용하는 원본 서버와 동일한 지역에 새 서버를 만듭니다.
- **지리적 복원** 은 지리적 중복 스토리지를 사용하도록 서버를 구성한 경우에만 사용할 수 있으며, 최신 백업을 활용해서 다른 지역으로 서버를 복원할 수 있습니다.

서버 복구의 예상 시간은 여러 요인에 따라 달라집니다.
* 데이터베이스의 크기
* 관련된 트랜잭션 로그의 수
* 복원 지점으로 복구하기 위해 재생해야 하는 작업의 양
* 다른 지역으로 복원되는 경우의 네트워크 대역폭
* 대상 지역에서 처리되는 동시 복원 요청 개수
* 데이터베이스에 있는 테이블에 기본 키가 있는지 여부입니다. 빠른 복구를 위해서는 데이터베이스에서 모든 테이블에 기본 키를 추가하는 것이 좋습니다. 테이블에 기본 키가 있는지 확인하려면 다음 쿼리를 사용하면 됩니다.
```sql
select tab.table_schema as database_name, tab.table_name from information_schema.tables tab left join information_schema.table_constraints tco on tab.table_schema = tco.table_schema and tab.table_name = tco.table_name and tco.constraint_type = 'PRIMARY KEY' where tco.constraint_type is null and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') and tab.table_type = 'BASE TABLE' order by tab.table_schema, tab.table_name;
```
규모가 크거나 처리량이 매우 많은 데이터베이스의 경우 복원에 몇 시간이 걸릴 수 있습니다. 지역에서 장시간 가동 중단이 발생한 경우 재해 복구를 위해 많은 수의 지리적 복원 요청이 시작될 수 있습니다. 많은 요청이 있는 경우 개별 데이터베이스에 대한 복구 시간이 늘어날 수 있습니다. 대부분의 데이터베이스 복원은 12시간 이내에 완료됩니다.

> [!IMPORTANT]
> 삭제된 서버는 백업이 삭제된 후 **5일** 내에 복원될 수 있습니다. 데이터베이스 백업은 서버를 호스트하는 Azure 구독에서만 액세스하고 복원할 수 있습니다. 삭제된 서버를 복원하려면 [설명 단계](howto-restore-dropped-server.md)를 참조하세요. 배포 후에 실수로 인한 삭제 또는 예기치 않은 변경에서 서버 리소스를 보호하려면 관리자는 [관리 잠금](../azure-resource-manager/management/lock-resources.md)을 활용할 수 있습니다.

### <a name="point-in-time-restore"></a>지정 시간 복원

백업 중복 옵션과는 별도로 백업 보존 기간 내의 특정 시점으로 복원을 수행할 수 있습니다. 새 서버가 원본 서버와 동일한 Azure 지역에 만들어집니다. 이 경우 가격 책정 계층, 컴퓨팅 세대, vCore 수, 스토리지 크기, 백업 보존 기간 및 백업 중복 옵션에 대한 원래 서버의 구성으로 만들어집니다.

> [!NOTE]
> 복원 작업 후 기본값으로 재설정되는(그리고 기본 서버로 복사되지 않는) 두 가지 서버 매개변수가 있습니다.
>
> - time_zone - 이 값은 기본값 **SYSTEM** 으로 설정됩니다.
> - event_scheduler - event_scheduler는 복원된 서버에서 **OFF** 로 설정됩니다.
>
> [서버 매개변수](howto-server-parameters.md)를 다시 구성하여 이러한 서버 매개변수를 설정해야 합니다.

특정 시점 복원은 여러 시나리오에서 유용합니다. 예를 들어 사용자가 실수로 데이터를 삭제하거나 중요한 테이블 또는 데이터베이스를 삭제하는 경우, 또는 애플리케이션의 결함으로 인해 우연히 적절한 데이터를 잘못된 데이터로 덮어쓰는 경우가 있습니다.

마지막 5분 내의 특정 시점으로 복원하려면, 다음 트랜잭션 로그 백업이 완료될 때까지 기다려야 할 수도 있습니다.

### <a name="geo-restore"></a>지역 복원

지역 중복 백업을 위해 서버를 구성한 경우 서비스를 사용할 수 있는 다른 Azure 지역으로 서버를 복원할 수 있습니다. 
- 범용 스토리지 v1 서버(최대 4TM의 스토리지 지원)는 지리적으로 연결된 지역 또는 Azure Database for MySQL 단일 서버 서비스를 지원하는 모든 Azure 지역으로 복원될 수 있습니다.
- 범용 스토리지 v2 서버(최대 16TB의 스토리지 지원)는 범용 스토리지 v2 서버 인프라를 지원하는 Azure 지역으로만 복원될 수 있습니다. 지원되는 지역 목록은 [Azure Database for MySQL 가격 책정 계층](./concepts-pricing-tiers.md#storage)을 참조하세요.

지역 복원은 서버가 호스팅되는 지역에 사고가 발생하여 서버를 사용할 수 없는 경우에 대비한 기본 복구 옵션입니다. 지역에서 발생한 대규모 사고로 인해 데이터베이스 애플리케이션을 사용할 수 없는 경우 지역 중복 백업에서 다른 지역에 있는 서버로 서버를 복원할 수 있습니다. 지리적 복원에는 서버의 최신 백업이 활용됩니다. 백업을 수행할 때와 다른 지역으로 복제할 때 사이에 지연이 있습니다. 재해가 발생한 경우 최대 1시간 동안의 데이터가 손실되므로 이 지연은 최대 1시간일 수 있습니다.

> [!IMPORTANT]
>새로 만든 서버에 대해 지리적 복원이 수행되는 경우 최초 백업 동기화는 초기 전체 스냅샷 백업 복사 시간이 훨씬 크기 때문에 데이터 크기에 따라 24시간 넘게 걸릴 수 있습니다. 이후 스냅샷 백업은 증분 복사본이므로 서버 생성 후 24시간이 지나면 복원이 더 빨라집니다. RTO 정의를 위해 지리적 복원을 평가하는 경우 예측 효율을 높일 수 있도록 서버 생성 후 **24시간이 지날 때까지 기다린 후에만** 지리적 복원을 평가하는 것이 좋습니다.

지역 복원 중에 변경할 수 있는 서버 구성으로는 컴퓨팅 생성, vCore, 백업 보존 기간 및 백업 중복 옵션이 있습니다. 가격 책정 계층(기본, 범용 또는 메모리 최적화) 또는 스토리지 크기는 지역 복원 중에 변경할 수 없습니다.

예상 복구 시간은 데이터베이스 크기, 트랜잭션 로그 크기, 네트워크 대역폭 및 동일한 지역에서 동시에 복구되는 데이터베이스의 총 수를 포함한 여러 요소에 따라 달라집니다. 복구 시간은 일반적으로 12시간 미만입니다.

### <a name="perform-post-restore-tasks"></a>복원 후 작업 수행

복구 메커니즘에서 복원한 후에 다음 작업을 수행하여 사용자 및 애플리케이션이 다시 백업 및 실행되도록 해야 합니다.

- 새 서버가 원래 서버를 교체하기 위한 것이라면 클라이언트와 클라이언트 애플리케이션을 새 서버로 리디렉션합니다.
- 사용자가 연결할 수 있도록 적합한 VNet 규칙이 설정되었는지 확인합니다. 이러한 규칙은 원래 서버에서 복사되지 않습니다.
- 적절한 로그인 및 데이터베이스 수준 권한이 있는지 확인합니다.
- 필요에 따라 경고를 구성합니다.

## <a name="next-steps"></a>다음 단계

- 비즈니스 연속성에 대한 자세한 내용은  [비즈니스 연속성 개요](concepts-business-continuity.md)를 참조하세요.
- Azure Portal을 사용하여 지정 시간으로 복원을 수행하려면  [Azure Portal을 사용하여 지정 시간으로 서버 복원](howto-restore-server-portal.md)을 참조하세요.
- Azure CLI를 사용하여 지정 시간으로 복원을 수행하려면  [CLI를 사용하여 지정 시간으로 서버 복원](howto-restore-server-cli.md)을 참조하세요.
