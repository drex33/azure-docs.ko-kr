---
title: 입력 데이터 복제 - Azure Database for MySQL Flexible
description: 입력 데이터 복제를 사용하여 외부 서버에서 Azure Database for MySQL Flexible 서비스로 동기화하는 방법에 대해 알아봅니다.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: 071672c5c2d3c741abd14dad94c8c150e427a3ce
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464777"
---
# <a name="replicate-data-into-azure-database-for-mysql-flexible--server-preview"></a>Azure Database for MySQL Flexible Server(미리 보기)에 데이터 복제

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

입력 데이터 복제를 사용하면 외부 MySQL 서버에서 Azure Database for MySQL Flexible 서비스로 데이터를 동기화할 수 있습니다. 외부 서버는 온-프레미스, 가상 머신, Azure Database for MySQL Single Server 또는 다른 클라우드 공급자가 호스트하는 데이터베이스 서비스에 있을 수 있습니다. 입력 데이터 복제는 위치 기반의 이진 로그(binlog) 파일을 기반으로 합니다. binlog 복제에 대한 자세히 알려면 [MySQL binlog 복제 개요](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)를 참조합니다.

> [!Note]
> GTID 기반 복제는 현재 Azure Database for MySQL Flexible Server에 지원되지 않습니다.<br>
> 영역 중복 고가용성 서버에 대한 입력 데이터 복제 구성은 지원되지 않습니다. 

## <a name="when-to-use-data-in-replication"></a>입력 데이터 복제를 사용하는 경우

입력 데이터 복제 사용을 고려할 주요 시나리오는 다음과 같습니다.

- **하이브리드 데이터 동기화:** 데이터 내부 복제를 사용하면 온-프레미스 서버와 Azure Databases for MySQL Flexible Server 간에 데이터를 동기화할 수 있습니다. 이 동기화는 하이브리드 애플리케이션을 만드는 데 유용합니다. 이 메서드는 기존 로컬 데이터베이스 서버가 있지만 최종 사용자에게 더 가까운 지역으로 데이터를 이동하려는 경우 매력적입니다.
- **다중 클라우드 동기화:** 복잡한 클라우드 솔루션의 경우 데이터 내부 복제를 사용하여 해당 클라우드에 호스팅된 데이터베이스 서비스 및 가상 머신을 포함하여 Azure Database for MySQL Flexible Server와 다른 클라우드 공급자 간에 데이터를 동기화합니다.
- **마이그레이션:** 고객은 입력 데이터 복제와 함께 [MyDumper/MyLoader](https://centminmod.com/mydumper.html)와 같은 오픈 소스 도구를 사용하여 최소 시간 마이그레이션을 수행할 수 있습니다. 원본에서 대상 데이터베이스로 프로덕션 부하를 선택적으로 사용하는 경우 데이터를 복제할 수 있습니다. 

마이그레이션 시나리오에 대해서는 [Azure DMS(Database Migration Service)](https://azure.microsoft.com/services/database-migration/)를 사용합니다.

## <a name="limitations-and-considerations"></a>제한 사항 및 고려 사항

### <a name="data-not-replicated"></a>데이터가 복제되지 않음

원본 서버의 [*mysql 시스템 데이터베이스*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)는 복제되지 않습니다. 또한 원본 서버에서 계정 및 사용 권한에 대해 변경한 내용은 복제되지 않습니다. 원본 서버에서 계정을 만들고 이 계정으로 복제 서버에 액세스해야 하는 경우 복제 서버에서 동일한 계정을 수동으로 만듭니다. 시스템 데이터베이스에 포함된 테이블을 이해하려면 [MySQL 설명서](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)를 참조합니다.

### <a name="data-in-replication-not-supported-on-ha-enabled-servers"></a>HA 사용 서버에서는 입력 데이터 복제가 지원되지 않음 
영역 중복 고가용성 서버에 대한 입력 데이터 복제 구성은 지원되지 않습니다. HA 사용 서버에서는 복제용 저장 프로시저 `mysql.az_replication_*`를 사용할 수 없습니다. 

### <a name="filtering"></a>필터링

테이블용 복제 필터를 만드는 데 사용된 `replicate_wild_ignore_table` 매개 변수는 현재 Azure Database for MySQL 유연한 서버에서 수정할 수 없습니다. 

### <a name="requirements"></a>요구 사항

- 원본 서버 버전은 MySQL 버전 5.7 이상이어야 합니다.
- 원본 및 복제 서버 버전에 대해 동일한 버전을 사용하는 것을 권장합니다. 예를 들어 둘 다 MySQL 버전 5.7이거나 둘 다 MySQL 버전 8.0이어야 합니다.
- 각 테이블에 기본 키를 포함하는 것을 권장합니다. 기본 키가 없는 테이블이 있는 경우 복제 시 속도가 저하될 수도 있습니다.
- 원본 서버는 MySQL InnoDB 엔진을 사용해야 합니다.
- 사용자는 이진 로깅을 구성하고 원본 서버에서 새 사용자를 만들 수 있는 권한이 있어야 합니다.
- 원본 서버에서 SSL을 사용하는 경우 도메인에 제공된 SSL CA 인증서가 `mysql.az_replication_change_master` 저장 프로시저에 포함되어 있는지 확인합니다. 다음 [예](./how-to-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) 및 `master_ssl_ca` 매개 변수를 참조합니다.
- 원본 서버를 호스트하는 컴퓨터에서 포트 3306에 대한 인바운드 및 아웃바운드 트래픽을 둘 다 허용하는지 확인합니다.
- 원본 서버에 DNS에 공개적으로 액세스할 수 있는 **공용 IP 주소** 가 있는지 또는 원본 서버에 FQDN(정규화된 도메인 이름)이 있는지 확인합니다.
- 퍼블릭 액세스의 경우, 공용 IP 주소가 원본 서버에 있는지, DNS에 공개적으로 액세스할 수 있는지 또는 FQDN(정규화된 도메인 이름)이 원본 서버에 있는지 확인합니다.
- 프라이빗 액세스의 경우, 원본 서버 이름을 확인할 수 있고 Azure Database for MySQL 인스턴스가 실행되고 있는 VNet에서 원본 서버 이름에 액세스할 수 있는지 확인합니다. 자세한 내용은 [Azure 가상 네트워크의 리소스 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [데이터 내부 복제 설정](how-to-data-in-replication.md) 방법 알아보기
- [일기 복제본을 사용하여 Azure에서 복제](concepts-read-replicas.md)에 대해 알아보기
