---
title: 개요 - Azure Database for MySQL - 유연한 서버
description: MySQL 커뮤니티 버전을 기반으로 하는 Microsoft 클라우드의 관계형 데이터베이스 서비스인 Azure Database for MySQL 유연한 서버에 대해 알아봅니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, references_regions
ms.topic: overview
ms.date: 08/10/2021
ms.openlocfilehash: c2cdd4009261306357bc9d840afa83bc1ebf40df
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111638"
---
# <a name="azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL - 유연한 서버(미리 보기)

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


MySQL 커뮤니티 버전에서 제공하는 Azure Database for MySQL은 두 가지 배포 모드로 사용할 수 있습니다.

- 단일 서버
- 유연한 서버(미리 보기)

이 문서에서는 유연한 서버 배포 모델의 핵심 개념에 대한 개요 및 소개를 제공합니다. 작업에 적합한 배포 옵션을 결정하는 방법에 대한 자세한 내용은 [Azure에서 올바른 MySQL 서버 옵션 선택](./../select-right-deployment-type.md)을 참조하세요.

## <a name="overview"></a>개요

Azure Database for MySQL 유연한 서버는 데이터베이스 관리 기능 및 구성 설정에 대한 보다 세부적인 제어와 유연성을 제공하도록 설계된 완전 관리형 데이터베이스 서비스입니다. 일반적으로 이 서비스는 사용자 요구 사항에 따라 더 많은 유연성과 서버 구성 사용자 지정을 제공합니다. 유연한 서버 아키텍처는 사용자가 단일 가용성 영역 및 여러 가용성 영역에서 고가용성을 선택할 수 있습니다. 또한 유연한 서버는 서버를 중지/시작하는 기능과 버스트 가능 SKU를 제공하므로 전체 컴퓨팅 용량이 지속적으로 필요하지 않는 워크로드에 적합합니다. 이 서비스는 현재 MySQL 커뮤니티 버전 5.7 및 8.0을 지원합니다. 이 서비스는 현재 미리 보기로 제공되며, 다양한 [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/)에서 사용할 수 있습니다.

유연한 서버는 다음에 가장 적합합니다.

- 더 나은 제어 및 사용자 지정이 필요한 애플리케이션 개발
- 영역 중복 고가용성
- 관리되는 유지 관리 기간

유연한 서버에 대한 최신 업데이트는 [Azure Database for MySQL - 유연한 서버의 새로운 사항](whats-new.md)을 참조하세요.

![유연한 서버 개념 다이어그램](media/overview/1-flexible-server-conceptual-diagram.png) 

## <a name="free-12-month-offer"></a>12개월 체험 제공

[Azure 체험 계정](https://azure.microsoft.com/free/)을 사용하면 유연한 서버를 12개월 동안 사용할 수 있으며 월별 한도는 다음과 같습니다.
* **750시간의 버스트 가능한 B1MS**. 인스턴스(매달 데이터베이스 인스턴스를 지속적으로 실행하는 데 충분한 시간입니다).
* **32GB** 의 스토리지 및 **32GB** 의 백업 스토리지. 

이 제공 서비스를 활용하여 Azure Database for MySQL – 유연한 서버를 사용하는 애플리케이션을 개발하고 배포할 수 있습니다. Azure 체험 계정을 사용하여 유연한 서버를 만들고 사용하는 방법을 확인하려면 [이 자습서](how-to-deploy-on-azure-free-account.md)를 참조하세요. 

## <a name="high-availability-within-and-across-availability-zones"></a>가용성 영역의 고가용성

Azure Database for MySQL 유연한 서버(미리 보기)를 사용하면 자동 장애 조치(failover)로 고가용성을 구성할 수 있습니다. 고가용성 솔루션은 커밋된 데이터가 오류로 인해 손실되지 않도록 하고 애플리케이션의 전반적인 작동 시간을 개선하도록 설계되었습니다.고가용성이 구성되면 유연한 서버가 대기 복제본을 자동으로 프로비저닝하고 관리합니다. 다음 두 가지의 고가용성 아키텍처 모델이 있습니다. 

- **영역 중복 HA(고가용성):** 이 옵션은 여러 가용성 영역에서 인프라의 완전한 격리 및 중복성을 위해 선호됩니다. 최고 수준의 가용성을 제공하지만 영역 간에 애플리케이션 중복성을 구성해야 합니다. 영역 중복 HA는 가용성 영역의 모든 인프라 장애에 대해 최고 수준의 가용성을 확보하고 가용성 영역 전체의 대기 시간이 허용되는 경우 선호됩니다. 영역 중복 HA는 지역이 여러 가용성 영역을 지원하고 영역 중복 프리미엄 파일 공유를 사용할 수 있는  [Azure 지역의 하위 집합](overview.md#azure-regions) 에서 사용할 수 있습니다. 

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="영역 중복 HA":::

- **동일 영역 HA(고가용성):** 이 옵션은 기본 서버와 대기 서버가 동일한 가용성 영역에 있으므로 네트워크 대기 시간이 짧은 인프라 중복에 선호됩니다. 영역 간에 애플리케이션 중복성을 구성하지 않고도 고가용성을 제공합니다. 동일한 영역 HA는 네트워크 대기 시간이 가장 짧은 단일 가용성 영역 내에서 최고 수준의 가용성을 확보하려는 경우 선호됩니다. 동일 영역 HA는 Azure Database for MySQL 유연한 서버를 만들 수 있는 모든 [Azure 지역](overview.md#azure-regions)에서 사용할 수 있습니다. 

:::image type="content" source="./media/concepts-high-availability/flexible-server-overview-same-zone-ha.png" alt-text="동일한 중복 고가용성":::

자세한 내용은 [고가용성 개념](concepts-high-availability.md)을 참조하세요.

## <a name="automated-patching-with-managed-maintenance-window"></a>관리되는 유지 관리 기간을 사용하여 자동 패치

서비스는 기본 하드웨어, OS 및 데이터베이스 엔진의 자동화된 패치를 수행합니다. 패치에는 보안 및 소프트웨어 업데이트가 포함됩니다. MySQL 엔진의 경우 계획된 유지 관리 릴리스에 부 버전 업그레이드도 포함됩니다. 사용자는 시스템 관리를 위해 패치 일정을 구성하거나 사용자 지정 일정을 정의할 수 있습니다. 유지 관리 일정 중에 패치가 적용되며, 업데이트를 완료하기 위해 패치 프로세스 중에 서버를 다시 시작해야 할 수도 있습니다. 사용자 지정 일정을 사용하면 사용자가 패치 주기를 예측 가능하도록 설정하고 비즈니스에 최소한의 영향을 주는 유지 관리 기간을 선택할 수 있습니다. 일반적으로 서비스는 연속 통합 및 릴리스의 일부로 월별 릴리스 일정을 따릅니다.

자세한 내용은 [예약된 유지 관리](concepts-maintenance.md)를 참조하세요. 

## <a name="automatic-backups"></a>자동 백업

유연한 서버 서비스는 자동으로 서버 백업을 만들어 사용자가 로컬로 구성한 중복 스토리지 또는 지역 중복 스토리지에 저장합니다. 백업을 통해 백업 보존 기간 내의 특정 지점으로 서버를 복원할 수 있습니다. 기본 백업 보존 기간은 7일입니다. 보존 기간은 선택적으로 최대 35일까지 구성할 수 있습니다. 모든 백업은 AES 256비트 암호화를 사용하여 암호화됩니다.

자세한 내용은 [백업 개념](concepts-backup-restore.md)을 참조하세요.

## <a name="network-isolation"></a>네트워크 격리

Azure Database for MySQL 유연한 서버에 연결하는 두 가지 네트워킹 옵션이 있습니다. 하나는 **프라이빗 액세스(VNet 통합)** 이고 다른 하나는 **퍼블릭 액세스(허용된 IP 주소)** 입니다. 

- **프라이빗 액세스(VNet 통합)** – [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)에 유연한 서버를 배포할 수 있습니다. Azure 가상 네트워크는 프라이빗하고 안전한 네트워크 통신을 제공합니다. 가상 네트워크의 리소스는 개인 IP 주소를 통해 통신할 수 있습니다.

  다음과 같은 기능을 원하는 경우 VNet 통합 옵션을 선택합니다.

  - 개인 IP 주소를 사용하여 동일한 가상 네트워크의 Azure 리소스에서 유연한 서버에 연결
  - VPN 또는 ExpressRoute를 사용하여 비 Azure 리소스에서 유연한 서버에 연결
  - 비 퍼블릭 엔드포인트

- **퍼블릭 액세스(허용된 IP 주소)** – 퍼블릭 엔드포인트를 사용하여 유연한 서버를 배포할 수 있습니다. 퍼블릭 엔드포인트는 공개적으로 확인할 수 있는 DNS 주소입니다. "허용되는 IP 주소"라는 말은 선택하는 IP 범위에 서버 액세스 권한을 부여한다는 뜻입니다. 이러한 권한을 **방화벽 규칙** 이라고 합니다.

자세한 내용은 [네트워킹 개념](concepts-networking.md)을 참조하세요.

## <a name="adjust-performance-and-scale-within-seconds"></a>몇 초 이내 성능 및 규모 조정

유연한 서버 서비스는 버스트 가능, 범용 및 메모리 최적화의 세 가지 SKU 계층으로 제공됩니다. 버스트 가능 계층은 전체 컴퓨팅 용량이 지속적으로 필요하지 않은 저렴한 개발 및 낮은 동시성 워크로드에 가장 적합합니다. 범용 및 메모리 최적화는 높은 동시성, 규모 및 예측 가능한 성능이 필요한 프로덕션 워크로드에 더 적합합니다. 한 달에 불과 몇 달러의 비용으로 작은 데이터베이스에 첫 번째 앱을 빌드하고, 이후에 솔루션의 요구에 맞게 원활하게 스케일링할 수 있습니다. 스토리지 스케일링은 온라인으로 수행되며 스토리지 자동 확장을 지원합니다. 유연한 서버를 사용하면 스토리지와 무관하게 무료 IOPS 제한을 초과하여 최대 20K IOPS를 추가로 프로비저닝할 수 있습니다. 이 기능을 사용하면 언제든지 워크로드 요구 사항에 따라 프로비전된 IOPS 수를 늘리거나 줄일 수 있습니다. 동적 확장성을 사용하면 데이터베이스가 빠르게 변화하는 리소스 요구 사항에 투명하게 대응할 수 있습니다. 사용하는 리소스 비용만 지불하면 됩니다. 

자세한 내용은 [컴퓨팅 및 스토리지 개념](concepts-compute-storage.md)을 참조하세요.

## <a name="scale-out-your-read-workload-with-up-to-10-read-replicas"></a>최대 10개의 읽기 복제본을 사용하여 읽기 워크로드 스케일 아웃

MySQL은 인터넷 규모 웹 및 모바일 애플리케이션을 실행하는 데 널리 사용되는 데이터베이스 엔진 중 하나입니다. 대부분의 고객은 온라인 교육 서비스, 비디오 스트리밍 서비스, 디지털 결제 솔루션, 전자 상거래 플랫폼, 게임 서비스, 뉴스 포털, 정부 및 의료 웹 사이트에서 이 기능을 사용합니다. 이러한 서비스는 웹 또는 모바일 애플리케이션의 트래픽이 늘어남에 따라 서비스를 제공하고 확장해야 합니다.

애플리케이션 쪽에서 애플리케이션은 일반적으로 Java 또는 PHP로 개발되고  [Azure 가상 머신 확장 집합](../../virtual-machine-scale-sets/overview.md)  또는  [Azure App Services](../../app-service/overview.md) 에서 실행되도록 마이그레이션되거나  [AKS(Azure Kubernetes Service)](../../aks/intro-kubernetes.md)에서 실행되도록 컨테이너화됩니다. 가상 머신 확장 집합, App Service 또는 AKS를 기본 인프라로 사용하면 새 VM을 즉시 프로비저닝하고 애플리케이션의 상태 비저장 구성 요소를 복제하여 요청을 처리함으로써 애플리케이션 확장이 단순화되지만, 데이터베이스는 중앙 집중식 상태 저장 구성 요소로 인해 병목 현상이 발생하는 경우가 많습니다.

읽기 복제본 기능을 사용하면 Azure Database for MySQL 유연한 서버에서 읽기 전용 서버로 데이터를 복제할 수 있습니다. 원본 서버에서 **최대 10개의 복제본** 으로 복제할 수 있습니다. 복제본은 MySQL 엔진의 네이티브 [이진 로그(binlog) 파일의 위치 기반 복제 기술](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)을 사용하여 비동기식으로 업데이트됩니다. [ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)과 같은 부하 분산 장치 프록시 솔루션을 사용하여 애플리케이션 리팩터링 비용 없이 애플리케이션 워크로드를 원활하게 확장하여 복제본을 읽을 수 있습니다. 

자세한 내용은 [읽기 복제본 개념](concepts-read-replicas.md)을 참조하세요.

## <a name="setup-hybrid-or-multi-cloud-data-synchronization-with-data-in-replication"></a>입력 데이터 복제를 사용하여 하이브리드 또는 다중 클라우드 데이터 동기화 설정

입력 데이터 복제를 사용하면 외부 MySQL 서버에서 Azure Database for MySQL Flexible 서비스로 데이터를 동기화할 수 있습니다. 외부 서버는 온-프레미스, 가상 머신, Azure Database for MySQL Single Server 또는 다른 클라우드 공급자가 호스트하는 데이터베이스 서비스에 있을 수 있습니다. 입력 데이터 복제는 위치 기반의 이진 로그(binlog) 파일을 기반으로 합니다. 입력 데이터 복제 사용을 고려할 주요 시나리오는 다음과 같습니다.
* 하이브리드 데이터 동기화
* 다중 클라우드 동기화
* [유연한 서버에 대한 최소 가동 중지 시간 마이그레이션](../../mysql/howto-migrate-single-flexible-minimum-downtime.md)

자세한 내용은 [입력 데이터 복제 개념](concepts-data-in-replication.md)을 참조하세요.


## <a name="stopstart-server-to-optimize-cost"></a>서버를 중지/시작하여 비용 최적화

유연한 서버 서비스를 사용하면 요청 시 서버를 중지하고 시작하여 비용을 최적화할 수 있습니다. 서버가 중지되는 즉시 컴퓨팅 계층에 대한 대금 청구가 중지됩니다. 이렇게 하면 개발, 테스트 및 시간 제한 예측 가능한 프로덕션 워크로드의 비용을 대폭 절감할 수 있습니다. 서버는 다시 시작하지 않는 한 7일 동안 중지된 상태로 유지됩니다.

자세한 내용은 [서버 개념](concept-servers.md)을 참조하세요.

## <a name="enterprise-grade-security-and-privacy"></a>엔터프라이즈급 보안 및 프라이버시

유연한 서버 서비스는 미사용 데이터의 스토리지 암호화를 위해 FIPS 140-2 유효성 검사 암호화 모듈을 사용합니다. 백업이 포함된 데이터 및 쿼리를 실행하는 동안 생성된 임시 파일이 암호화됩니다. 서비스는 Azure 스토리지 암호화에 포함된 AES 256비트 암호화를 사용하며, 키는 시스템에서 관리됩니다(기본값).

서비스는 기본적으로 적용되는 전송 계층 보안을 사용하여 동작 중인 데이터를 암호화합니다. 기본적으로 유연한 서버는 전송 계층 보안(1.2 TLS)을 사용하는 암호화된 연결만 지원하며 TLS 1.0 및 TLS 1.1을 사용하여 들어오는 연결은 모두 거부됩니다. require_secure_transport 서버 매개 변수를 설정하여 SSL 적용을 사용하지 않도록 설정하고, 서버에 대한 최소 tls_version 설정할 수 있습니다.

자세한 내용은 [유연한 서버에 암호화된 연결을 사용하는 방법](how-to-connect-tls-ssl.md)을 참조하세요.

유연한 서버는 [Azure 가상 네트워크](../../virtual-network/virtual-networks-overview.md)(VNet 통합)를 사용하여 서버에 대한 모든 프라이빗 액세스를 허용합니다. Azure 가상 네트워크의 서버는 프라이빗 IP 주소를 통해서만 연결할 수 있습니다. VNet 통합을 사용하면 퍼블릭 액세스를 거부하고 퍼블릭 엔드포인트를 사용하여 서버에 연결할 수 없습니다.

자세한 내용은 [네트워킹 개념](concepts-networking.md)을 참조하세요.

## <a name="monitoring-and-alerting"></a>모니터링 및 경고

유연한 서버 서비스는 기본 제공 성능 모니터링 및 경고 기능을 갖추고 있습니다. 모든 Azure 메트릭의 빈도는 1분이고 각 메트릭은 30일의 기록을 제공합니다. 메트릭에 대한 경고를 구성할 수 있습니다. 서비스는 리소스 사용률을 모니터링하도록 호스트 서버 메트릭을 노출하고, 쿼리 로그를 느리게 구성할 수 있도록 합니다. 이러한 도구를 사용하여 워크로드를 신속하게 최적화하고 최적의 성능을 얻을 수 있도록 서버를 구성할 수 있습니다. 또한 [MySQL Flexible Server에서의 Percona 모니터링 및 관리](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/monitor-azure-database-for-mysql-using-percona-monitoring-and/ba-p/2568545) 등, 커뮤니티 모니터링 도구를 사용하고 통합할 수도 있습니다. 

자세한 내용은 [모니터링 개념](concepts-monitoring.md)을 참조하세요.

## <a name="migration"></a>마이그레이션

이 서비스는 커뮤니티 버전의 MySQL을 실행합니다. 따라서 전체 애플리케이션이 호환되며, MySQL 엔진에서 개발된 기존 애플리케이션을 유연한 서버로 마이그레이션하기 위한 리팩터링 비용이 최소화됩니다. 유연한 서버로의 마이그레이션은 다음 옵션을 사용하여 수행할 수 있습니다.

### <a name="offline-migrations"></a>오프라인 마이그레이션
*   원본과 Azure 간의 네트워크 대역폭 상태가 좋은 경우(예: 고속 ExpressRoute) Azure Data Migration Service를 사용하는 것이 좋습니다. 단계별 지침인 [DMS - Azure Database Migration Service를 사용하여 오프라인으로 MySQL을 Azure Database for MySQL로 마이그레이션](../../dms/tutorial-mysql-azure-mysql-offline-portal.md)에서 자세히 알아봅니다.
*   mydumper/myloader를 통해 압축 설정을 활용하여 저속 네트워크(예: 퍼블릭 인터넷)를 통해 데이터를 효율적으로 이동합니다. 단계별 지침인 [mydumper/myloader를 사용하여 큰 데이터베이스를 Azure Database for MySQL로 마이그레이션](../../mysql/concepts-migrate-mydumper-myloader.md)을 통해 자세히 알아보세요.

### <a name="online-or-minimal-downtime-migrations"></a>온라인 또는 최소 가동 중지 시간 마이그레이션
초기 시드를 위해 mydumper/myloader 일치 백업/복원과 함께 입력 데이터 복제를 사용합니다. 단계별 지침인 [자습서: Azure Database for MySQL의 최소 가동 중지 시간 마이그레이션 – 단일 서버에서 Azure Database for MySQL로 - 유연한 서버](../../mysql/howto-migrate-single-flexible-minimum-downtime.md)에서 자세히 알아봅니다.

간단한 5단계를 통해 Azure Database for MySQL - 단일 서버에서 유연한 서버로 마이그레이션하려면 [이 블로그](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/migrate-from-azure-database-for-mysql-single-server-to-flexible/ba-p/2674057)를 참조하세요.

자세한 내용은 [Azure Database for MySQL 모니터링 가이드](../../mysql/migrate/mysql-on-premises-azure-db/01-mysql-migration-guide-intro.md)를 참조하세요.

## <a name="azure-regions"></a>Azure 지역

Azure에서 워크로드를 실행하는 이점 중 하나는 글로벌 연결입니다. Azure Database for MySQL에 대한 유연한 서버는 현재 다음 Azure 지역에서 사용할 수 있습니다.

| 지역 | 가용성 | 동일한 영역 HA | 영역 중복 HA |
| --- | --- | --- | --- |
| 오스트레일리아 동부 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 브라질 남부 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 캐나다 중부 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 미국 중부 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 미국 동부 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 미국 동부 2 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 프랑스 중부 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:|
| 독일 중서부 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 일본 동부 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 한국 중부 | :heavy_check_mark: | :x: | :x: |
| 북유럽 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 동남 아시아 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 스위스 북부 | :heavy_check_mark: | :x: | :x: |
| 영국 남부 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 미국 서부 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 미국 서부 2 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 서유럽 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 오스트레일리아 남동부 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 남아프리카 북부 | :heavy_check_mark: | :x: | :x: |
| 동아시아(홍콩) | :heavy_check_mark: | :x: | :x: |
| 인도 중부 | :heavy_check_mark: | :x: | :x: |

## <a name="contacts"></a>연락처

Azure Database for MySQL 유연한 서버에 대한 질문이나 제안이 있으면 Azure Database for MySQL 팀([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com))으로 이메일을 보내주세요. 이 이메일 주소는 기술 지원 별칭이 아닙니다.

또한 문의의 다음 사항을 적절히 고려해 주세요.

- Azure 고객 지원팀에 문의하려면 [Azure Portal에서 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
- 계정 관련 문제를 해결하려면 Azure Portal에서 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출합니다.
- 피드백을 제공하거나 새 기능을 요청하려면 [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql)를 통해 항목을 만드세요.

## <a name="next-steps"></a>다음 단계

Azure Database for MySQL 단일 서버 배포 모드에 대한 소개를 읽어 보았으므로 이제 다음을 수행할 수 있습니다.

- 첫 번째 서버를 만듭니다.
  - [Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버 만들기](quickstart-create-server-portal.md)
  - [Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버 만들기](quickstart-create-server-cli.md)
  - [Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버 관리](how-to-manage-server-portal.md)

- 기본 설정된 언어를 사용하여 첫 번째 앱을 빌드합니다.
  - [Python](connect-python.md)
  - [PHP](connect-php.md)
