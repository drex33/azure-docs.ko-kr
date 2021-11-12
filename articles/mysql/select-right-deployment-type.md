---
title: 올바른 배포 유형 선택 - Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL을 IaaS(서비스 제공 인프라) 또는 PaaS(Platform as a Service)로 배포하기 전에 고려해야 하는 요인에 대해 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: f07cb6dc6e5ab01b4c172c660fa971b1ef2a3db9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286093"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Azure에서 적절한 MySQL 서버 옵션 선택

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure를 사용하면 MySQL 서버 워크로드를 호스트된 가상 머신 IaaS(서비스 제공 인프라) 또는 호스트된 PaaS(Platform as a Service)로 실행할 수 있습니다. PaaS에는 두 가지 배포 옵션이 있으며 각 배포 옵션 내에 서비스 계층이 있습니다. IaaS와 PaaS 중에서 선택할 경우 데이터베이스를 관리하거나, 패치, 백업, 보안, 모니터링, 크기 조정을 적용할지 또는 이러한 작업을 Azure에 위임할지 결정해야 합니다.

결정할 때 다음 두 가지 옵션을 고려하세요.

- **Azure Database for MySQL** 이 옵션은 안정적인 버전의 MySQL 커뮤니티 버전을 기반으로 하는 완전 관리형 MySQL 데이터베이스 엔진입니다. PaaS 산업 범주로 분류되며, Azure 클라우드 플랫폼에서 호스트되는 관계형 DBaaS(Database as a Service)입니다. Azure에서 관리되는 MySQL 인스턴스를 사용하면 기본 제공 기능(예: 자동화된 패치 적용, 고가용성, 자동 백업, 탄력적 크기 조정, 엔터프라이즈급 보안, 규정 준수 및 거버넌스, MySQL 서버가 온-프레미스 또는 Azure VM에서 추가적인 구성이 요구되는 모니터링 및 알림)을 사용할 수 있습니다. MySQL을 서비스로 사용하는 경우 중단 없이 제어를 강화하기 위해 스케일 업 또는 스케일 아웃하는 옵션이 종량제 방식으로 지원됩니다. [Azure Database for MySQL](overview.md), MySQL 커뮤니티 버전에서 제공하는 Azure Database for MySQL은 두 가지 배포 모드로 사용할 수 있습니다.

   - [유연한 서버](flexible-server/overview.md) - Azure Database for MySQL 유연한 서버는 데이터베이스 관리 기능 및 구성 설정에 대한 보다 세부적인 제어 및 유연성을 위해 설계된 완전 관리형 프로덕션 지원 데이터베이스 서비스입니다. 유연한 서버 아키텍처는 사용자가 단일 가용성 영역 및 여러 가용성 영역에서 고가용성을 선택할 수 있습니다. 유연한 서버는 서버를 중지/시작하는 기능과 버스트 가능 컴퓨팅 계층을 통해 비용을 최적화할 수 있으므로 전체 컴퓨팅 용량이 지속적으로 필요하지 않는 워크로드에 적합합니다. 유연한 서버는 또한 예약 인스턴스를 지원하여 최대 63%의 비용을 절감할 수 있으며 예측 가능한 컴퓨팅 용량 요구 사항이 있는 프로덕션 워크로드에 적합합니다. 이 서비스는 커뮤니티 버전 MySQL 5.7 및 8.0을 지원합니다. 이 서비스는 현재 다양한 [Azure 지역](flexible-server/overview.md#azure-regions)에서 일반 공급됩니다. 유연한 서버는 모든 새로운 개발 및 프로덕션 워크로드를 Azure Database for MySQL 서비스로 마이그레이션하는 데 가장 적합합니다.

   - [단일 서버는](single-server-overview.md) 최소한의 사용자 지정을 위해 설계된 완전 관리형 데이터베이스 서비스입니다. 단일 서버 플랫폼은 최소한의 사용자 구성과 제어를 통해 패치, 백업, 고가용성, 보안 등 대부분의 데이터베이스 관리 기능을 처리하도록 설계되었습니다. 이 아키텍처는 단일 가용성 영역에서 99.99%의 가용성을 기본 제공하도록 고가용성에 최적화되었습니다. 커뮤니티 버전의 MySQL 5.6(사용 중지), 5.7 및 8.0을 지원합니다. 이 서비스는 현재 다양한 [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/)에서 일반 공급됩니다. 단일 서버는 단일 서버 를 **이미 활용하는 기존 애플리케이션에만** 가장 적합합니다. 모든 새로운 개발 또는 마이그레이션의 경우 유연한 서버가 권장되는 배포 옵션입니다. 유연한 서버 배포 옵션과 단일 서버 배포 옵션 간의 차이점에 대해 알아보려면 [설명서에 적합한 배포 옵션 선택을 참조하세요.](select-right-deployment-type.md)
 
- **Azure VM의 MySQL**. 이 옵션은 IaaS 산업 범주에 속합니다. 이 서비스를 선택하면 Azure 클라우드 플랫폼의 관리형 가상 머신 내에서 MySQL 서버를 실행할 수 있습니다. 모든 최신 버전의 MySQL은 가상 머신에 설치할 수 있습니다.

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>Azure에서 MySQL 배포 옵션 비교

이러한 옵션들의 주요 차이점은 다음 표에 나와 있습니다.

| attribute          | Azure Database for MySQL<br/>단일 서버 |Azure Database for MySQL<br/>유연한 서버  |Azure VM의 MySQL |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:------------------|
| [**일반**](flexible-server/overview.md)  | | | |
| 일반 공급 | 일반 공급 | 일반 공급 | 일반 공급 |
| SLA(서비스 수준 계약) | 99.99% 가용성 SLA |99.99% 가용성 영역 사용| 99.99% 가용성 영역 사용|
| 기본 운영 체제 | Windows | Linux  | 사용자 관리 |
| MySQL 버전 | Community Edition | Community Edition | Community 또는 Enterprise Edition |
| MySQL 버전 지원 | 5.6(사용 중지), 5.7 & 8.0| 5.7 & 8.0 | 모든 버전|
| 애플리케이션 위치의 가용성 영역 선택 | 아니요 | 예 | 예 |
| 연결 문자열의 사용자 이름 | `<user_name>@server_name`. 예를 들어 `mysqlusr@mypgServer` | 사용자 이름. 예를 들어 `mysqlusr` | 사용자 이름. 예를 들어 `mysqlusr` | 
| [**컴퓨팅 & Storage 크기 조정**](flexible-server/concepts-compute-storage.md) | | | |
| 컴퓨팅 계층 | 기본, 범용 및 메모리 최적화 | 버스트 가능, 범용 및 메모리 최적화 | 버스트 가능, 범용 및 메모리 최적화 |
| 컴퓨팅 크기 조정 | 지원됨(기본 계층에서 기본 계층으로 크기 조정은 **지원되지 않음)**| 지원됨 | 지원 여부|
| 스토리지 크기 | 5GiB~16TiB| 20GiB~16TiB | 32GiB~32,767GiB|
| 온라인 스토리지 크기 조정 | 지원 여부| 지원됨| 지원되지 않음|
| 자동 스토리지 크기 조정 | 지원 여부| 지원됨| 지원되지 않음|
| IOPS 크기 조정 | 지원되지 않음| 지원됨| 지원되지 않음|
| [**비용 최적화**](https://azure.microsoft.com/pricing/details/mysql/flexible-server/) | | | |
| 예약 인스턴스 가격 책정 | 지원 여부 | 지원됨 | 지원됨 |
| 개발용 서버 중지/시작 | 서버를 최대 7 일까 지 중지할 수 있음 | 서버를 30 일까지 중지할 수 있습니다. | 지원됨 |
| 저렴 한 비용의 안정적인 SKU | 지원되지 않음 | 지원됨 | 지원됨 |
| [**네트워킹/보안**](concepts-security.md) | | | |
| 네트워크 연결 | - 서버 방화벽이 있는 공용 엔드포인트.<br/> - Private Link 지원으로 프라이빗 액세스.|- 서버 방화벽이 있는 공용 엔드포인트.<br/> - Virtual Network 통합을 통한 프라이빗 액세스.| - 서버 방화벽이 있는 공용 엔드포인트.<br/> - Private Link 지원으로 프라이빗 액세스.|
| SSL/TLS | TLS v1.2, 1.1 및 1.0에 대한 지원으로 기본적으로 사용하도록 설정됨 | TLS v1.2, 1.1 및 1.0에 대한 지원으로 기본적으로 사용하도록 설정됨| TLS v1.2, 1.1 및 1.0에서 지원됨 |
| 미사용 데이터 암호화 | BYOK (고객 관리 키)로 지원 됨 | 서비스 관리형 키로 지원됨 | 지원되지 않음|
| Azure AD 인증 | 지원됨 | 지원되지 않음 | 지원되지 않음|
| 클라우드 지원을 위한 Microsoft Defender | 예 | 아니요 | 아니요 |
| 서버 감사 | 지원됨 | 지원됨 | 사용자 관리 |
| [**& 유지 관리 패치**](flexible-server/concepts-maintenance.md) | | |
| 운영 체제 패치| 자동  | 자동  | 관리되는 사용자 |
| MySQL 부 버전 업그레이드  | 자동  | 자동 | 관리되는 사용자 |
| MySQL 내부 주 버전 업그레이드 | 5.6에서 5.7으로 지원 됨 | 지원되지 않음 | 사용자 관리 |
| 유지 관리 제어 | 시스템 관리 | 고객 관리 | 관리되는 사용자 |
| 유지 관리 기간 | 15시간 이내 언제든지 | 1시간 | 관리되는 사용자 |
| 계획된 유지 관리 알림 | 3일 | 5일 | 관리되는 사용자 |
| [**고가용성**](flexible-server/concepts-high-availability.md) | | | |
| 고가용성 | 기본 제공 HA (상시 대기 없음)| 상시 대기를 사용 하는 기본 제공 HA (상시 대기 없음), 동일한 영역 및 상시 대기를 사용 하는 영역 중복 HA | 관리되는 사용자 |
| 영역 중복 | 지원되지 않음 | 지원됨 | 지원됨|
| 대기 영역 배치 | 지원되지 않음 | 지원됨 | 지원됨|
| 자동 장애 조치(automatic failover) | 예(다른 서버 회전)| Yes | 사용자 관리|
| 사용자가 강제 장애 조치 (failover) 시작 | 아니요 | 예 | 사용자 관리 |
| 투명 한 응용 프로그램 장애 조치 | 예 | 예 | 사용자 관리|
| [**복제**](flexible-server/concepts-read-replicas.md) | | | |
| 읽기 복제본 지원 | 예 | 예 | 사용자 관리 |
| 지원 되는 읽기 복제본 수 | 5 | 10 | 사용자 관리 |
| 복제 모드 | 비동기 | 비동기 | 사용자 관리 |
| 읽기 복제본에 대 한 gtid 지원 | 지원됨 | 지원됨 | 사용자 관리 |
| 지역 간 지원 (지역에서 복제) | 예 | 지원되지 않음 | 사용자 관리 |
| 하이브리드 시나리오 | [데이터 내부 복제](./concepts-data-in-replication.md)에서 지원됨| [데이터 내부 복제](./flexible-server/concepts-data-in-replication.md)에서 지원됨 | 사용자 관리 |
| 데이터 내 복제에 대 한 gtid 지원 | 지원됨 | 지원됨 | 사용자 관리 |
| 데이터 복제 | 지원되지 않음 | 미리 보기 | 지원됨 |
| [**백업 및 복구**](flexible-server/concepts-backup-restore.md) | | | |
| 자동화된 백업 | 예 | 예 | 예 |
| 백업 보존 | 7-35일 | 1-35 일 | 사용자 관리 |
| 백업 장기 보존 | 사용자 관리 | 사용자 관리 | 사용자 관리 |
| 백업 내보내기 | 논리적 백업을 사용 하 여 지원 | 논리적 백업을 사용 하 여 지원 | 지원됨 |
| 보존 기간 내에 언제 든 지 특정 시점 복원 기능 | 예 | 예 | 사용자 관리 |
| 빠른 복원 지점 | 아니요 | 예 | 아니요 |
| 다른 영역에서 복원하는 기능 | 지원되지 않음 | 예 | 예 |
| 다른 VNET으로 복원하는 기능 | 아니요 | 예 | 예 |
| 다른 지역으로 복원하는 기능 | 예(지역 중복) | No | 사용자 관리 |
| 삭제된 서버를 복원하는 기능 | 예 | 아니요 | 아니요 |
| [**재해 복구**](flexible-server/concepts-business-continuity.md) | | | | 
| Azure 지역에서 DR | 지역 간 읽기 복제본, 지역 중복 백업 사용 | 지원되지 않음 | 사용자 관리 |
| 자동 장애 조치(automatic failover) | 예 | 지원되지 않음 | 아니요 |
| 동일한 r/w 엔드포인트를 사용할 수 있음 | 아니요 | 지원되지 않음 | 아니요 |
| [**모니터링**](flexible-server/concepts-monitoring.md) | | | |
| 경고 & Azure Monitor 통합 | 지원됨 | 지원됨 | 사용자 관리 |
| 데이터베이스 작업 모니터링 | 지원 여부 | 지원됨 | 사용자 관리 |
| Query Performance Insight | 지원 여부 | 지원 됨 (통합 문서 사용)| 사용자 관리 |
| 서버 로그 | 지원됨 | 지원 됨 (진단 로그 사용) | 사용자 관리 |
| 감사 로그 | 지원됨 | 지원됨 | 지원됨 | 
| 오류 로그 | 지원되지 않음 | 지원됨 | 지원됨 |
| Azure advisor 지원 | 지원됨 | 지원되지 않음 | 지원되지 않음 |
| **플러그 인** | | | |
| validate_password | 지원되지 않음 | 미리 보기 | 지원됨 |
| caching_sha2_password | 지원되지 않음 | 미리 보기 | 지원됨 |
| [**개발자 생산성**](flexible-server/quickstart-create-server-cli.md) | | | |
| 선단 관리 | Azure CLI, PowerShell, REST 및 Azure Resource Manager에서 지원됨 | Azure CLI, PowerShell, REST 및 Azure Resource Manager에서 지원됨  | Azure CLI, PowerShell, REST 및 Azure Resource Manager가 있는 VM에 지원됨 |
| Terraform 지원 | 지원됨 | 지원됨 | 지원됨 |
| GitHub 작업 | 지원됨 | 지원됨 | 사용자 관리 |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>PaaS 또는 IaaS 선택을 위한 비즈니스 동기

PaaS 또는 IaaS 중에서 MySQL 데이터베이스의 호스트를 결정할 때 영향을 줄 수 있는 여러 요인이 있습니다.

### <a name="cost"></a>비용

데이터베이스 호스팅을 위한 최적의 솔루션을 결정할 때 비용 절감이 결정적인 역할을 하는 경우가 종종 있습니다. 현금이 부족한 스타트업이거나 탄탄한 회사에 속해 있지만 예산이 넉넉하지 않은 팀인 경우 특히 그렇습니다. 이 섹션에서는 Azure Database for MySQL 및 Azure VM의 MySQL에 적용되는 Azure의 청구 및 라이선스 기본 사항에 대해 설명합니다.

#### <a name="billing"></a>결제

현재 Azure Database for MySQL은 서로 다른 리소스 가격을 갖는 여러 계층의 서비스로 제공됩니다. 모든 리소스 요금은 고정 요율로 시간당 청구됩니다. 현재 지원되는 서비스 계층, 컴퓨팅 크기 및 스토리지 용량에 대한 최신 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/mysql/)를 참조하세요. 애플리케이션의 다양한 처리량 요구에 맞게 서비스 계층과 컴퓨팅 크기를 동적으로 조정할 수 있습니다. 발신 인터넷 트래픽은 일반 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/)이 청구됩니다.

Azure Database for MySQL을 선택하면 Microsoft에서 자동으로 데이터베이스 소프트웨어를 구성, 패치 및 업그레이드합니다. 이러한 작업이 자동화되어 있으므로 관리 비용이 줄어듭니다. 또한 Azure Database for MySQL에는 [자동 백업](./concepts-backup.md) 기능이 있습니다. 이러한 기능을 사용하여 비용을 크게 절감할 수 있으며, 특히 데이터베이스 수가 많을 경우 그 효과가 큽니다. 대조적으로 Azure VM의 MySQL을 사용하면 모든 MySQL 버전을 선택하고 실행할 수 있습니다. 어떤 MySQL 버전을 사용하든 프로비저닝된 VM 요금과 데이터, 백업, 모니터링 데이터 및 로그 스토리지와 관련된 스토리지 비용 및 사용한 특정 MySQL 라이선스 유형(있는 경우)에 대한 비용을 지불해야 합니다.

Azure Database for MySQL은 서비스에 대한 99.99% SLA 보장을 유지하면서 모든 종류의 노드 수준 중단에 대해 기본 제공되는 고가용성을 제공합니다. 그러나 VM 내에 있는 데이터베이스의 고가용성을 유지하려면 MySQL 데이터베이스에서 사용할 수 있는 [MySQL 복제](https://dev.mysql.com/doc/refman/8.0/en/replication.html) 같은 고가용성 옵션을 사용하세요. 지원되는 고가용성 옵션을 사용하면 추가 SLA가 제공되지 않습니다. 하지만 추가 비용과 관리 오버헤드를 대가로 99.99%보다 높은 데이터베이스 가용성을 달성할 수 있습니다.

가격 책정에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Database for MySQL 가격 책정](https://azure.microsoft.com/pricing/details/mysql/)
- [가상 머신 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>관리

많은 기업이 클라우드 서비스로 전환하기로 결정할 때 비용과 더불어 가장 중요하게 생각하는 것은 관리의 복잡성을 줄이는 것입니다.

IaaS를 선택하면 Microsoft에서 다음 작업을 처리합니다.

- 기본 인프라를 관리합니다.
- 기본 하드웨어 및 OS의 자동화된 패치를 제공합니다.

PaaS를 선택하면 Microsoft에서 다음 작업을 처리합니다.

- 기본 인프라를 관리합니다.
- 기본 하드웨어, OS 및 데이터베이스 엔진의 자동화된 패치를 제공합니다.
- 데이터베이스의 고가용성을 관리합니다.
- 자동으로 백업을 수행하고 모든 데이터를 복제하여 재해 복구를 제공합니다.
- 기본적으로 미사용 데이터 및 사용 중인 데이터를 암호화합니다.
- 서버를 모니터링하고 쿼리 성능 Insight 및 성능 권장 사항에 대한 기능을 제공합니다.

다음 목록에서는 각 옵션에 대한 관리 고려 사항을 설명합니다.

- Azure Database for MySQL을 선택하면 계속해서 직접 데이터베이스를 관리할 수 있습니다. 하지만 더 이상 데이터베이스 엔진, 운영 체제 또는 하드웨어를 관리할 필요가 없습니다. 계속해서 직접 관리할 수 있는 항목은 다음과 같습니다.

  - 데이터베이스
  - 로그인
  - 인덱스 튜닝
  - 쿼리 튜닝
  - 감사
  - 보안

  또한 다른 데이터 센터에 고가용성을 구성하는 데 필요한 구성 및 관리가 최소화됩니다.

- Azure VM에 대한 MySQL 서버에서는 운영 체제와 MySQL 서버 인스턴스 구성을 완벽히 제어할 수 있습니다. VM과 함께 운영 체제 및 데이터베이스 소프트웨어를 업데이트 또는 업그레이드할 시기와 적용할 패치를 결정합니다. 또한 바이러스 백신 애플리케이션과 같은 추가 소프트웨어를 설치할 시기를 결정합니다. 자동화된 일부 기능이 제공되므로 패치, 백업 및 고가용성이 크게 간소화됩니다. VM 크기, 디스크 수 및 해당 스토리지 구성을 제어할 수 있습니다. 자세한 내용은 [Azure를 위한 가상 머신 및 클라우드 서비스 크기](../virtual-machines/sizes.md)를 참조하세요.

### <a name="time-to-move-to-azure"></a>Azure로 이동해야 하는 경우

- 새로운 솔루션에 대한 개발자 생산성과 빠른 출시 시간이 중요한 경우 클라우드용으로 설계된 애플리케이션에 적합한 솔루션은 Azure Database for MySQL입니다. DBA 같은 프로그래밍 방식 기능을 제공하는 이 서비스는 기본 운영 체제 및 데이터베이스 관리 부담을 덜어 주기 때문에 클라우드 설계자와 개발자에게 적합합니다.

- 새로운 온-프레미스 하드웨어를 획득하는 데 드는 시간과 비용을 줄이려면 서비스에서 지원하지 않는 MySQL 엔진을 세밀하게 제어하고 사용자 지정할 수 있어야 하는 애플리케이션 또는 기본 OS에 대한 액세스가 필요한 애플리케이션에 적합한 솔루션은 Azure VM의 MySQL입니다. 또한 이 솔루션은 Azure Database for MySQL이 잘못된 경우 기존 온-프레미스 애플리케이션 및 데이터베이스를 Azure에 그대로 마이그레이션하는 데 적합합니다.

프레젠테이션, 애플리케이션 및 데이터 계층을 변경할 필요가 없으므로 기존 솔루션 재설계에 따른 비용과 예산이 절감됩니다. 대신 모든 솔루션을 Azure에 마이그레이션하고 Azure 플랫폼에 필요할 수 있는 일부 성능 최적화를 수행하는 데 집중할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Database for MySQL 가격 책정](https://azure.microsoft.com/pricing/details/MySQL/)을 참조하세요.
- [첫 번째 서버를 만들어서](./quickstart-create-mysql-server-database-using-azure-portal.md) 시작합니다.
