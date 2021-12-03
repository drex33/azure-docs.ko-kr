---
title: Azure에서 적절한 PostgreSQL 서버 옵션 선택
description: 배포에 적합한 PostgreSQL 서버 옵션을 선택하는 방법에 대한 지침을 제공합니다.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 12/01/2021
ms.openlocfilehash: 57066be8e76b821b6fd23fa1aceb140fe2710b7c
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133483043"
---
# <a name="choose-the-right-postgresql-server-option-in-azure"></a>Azure에서 적절한 PostgreSQL 서버 옵션 선택

Azure를 사용하면 PostgreSQL 서버워크 로드를 호스트된 가상 머신 IaaS(Infrastructure as a Service) 또는 호스트된 PaaS(Platform as a Service)로 실행할 수 있습니다. PaaS는 여러 배포 옵션을 제공하며, 옵션마다 여러 가지 서비스 계층이 제공됩니다. IaaS와 PaaS 중에 선택할 때 직접 데이터베이스를 관리하고 패치를 적용하고 백업을 수행할 것인지 아니면 이러한 작업을 Azure에 위임할 것인지 결정해야 합니다.

결정을 내릴 때 PaaS의 다음 세 가지 옵션 중에 고르거나 Azure VM에서 실행(IaaS)합니다.
- [Azure Database for PostgreSQL 단일 서버](./overview-single-server.md)
- [Azure Database for PostgreSQL 유연한 서버](./flexible-server/overview.md)
- [Azure Database for PostgreSQL 하이퍼스케일(Citus)](hyperscale/index.yml)

**PostgreSQL on Azure VMs** 옵션은 IaaS 산업 범주에 속합니다. 이 서비스를 선택하면 Azure 클라우드 플랫폼의 완전 관리형 가상 머신 내에서 PostgreSQL 서버를 실행할 수 있습니다. PostgreSQL의 모든 최신 버전과 에디션을 IaaS 가상 머신에 설치할 수 있습니다. Azure Database for PostgreSQL과 가장 큰 차이점으로 PostgreSQL on Azure VMs는 데이터베이스 엔진을 제어할 수 있습니다. 그러나 데이터베이스 엔진을 제어할 수 있는 대신 VM 및 여러 DBA(데이터베이스 관리) 작업을 직접 관리해야 합니다. 이러한 작업에는 데이터베이스 서버 유지 관리 및 패치, 데이터베이스 복구, 고가용성 디자인이 포함됩니다.

이러한 옵션들의 주요 차이점은 다음 표에 나와 있습니다.

| **Attribute** | **Postgres on Azure VMs** | **PostgreSQL as PaaS** |
| ----- | ----- | ----- |
| **가용성 SLA** |- [가상 컴퓨터 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) | - [단일 서버, 유연한 서버 및 Citus (Hyperscale) SLA](https://azure.microsoft.com/support/legal/sla/postgresql/v1_2/)|
| **OS 및 PostgreSQL 패치** | - 고객이 관리 | - 단일 서버 - 자동 <br> - 유연한 서버 – 자동(고객이 관리하는 선택적 창 제공) <br> - 하이퍼스케일(Citus) - 자동 |
| **고가용성** | - 고객이 고가용성을 설계, 구현, 테스트 및 유지 관리 기능으로 클러스터링, 복제 등이 포함될 수 있음 | - 단일 서버: 기본 제공 <br> - 유연한 서버: 기본 제공 <br> - 하이퍼스케일(Citus): 대기 모드를 사용하여 빌드 |
| **영역 중복성** | - Azure VM은 서로 다른 가용성 영역에서 실행되도록 설정할 수 있습니다. 온-프레미스 솔루션의 경우 고객이 자체적으로 보조 데이터 센터를 만들고 관리하고 유지 관리해야 합니다. | - 단일 서버: 예 <br> - 유연한 서버: 예 <br> - 하이퍼스케일(Citus): 예 |
| **하이브리드 시나리오** | - 고객이 관리 |- 단일 서버: 읽기-복제본 <br> - 유연한 서버: 미리 보기 중에는 사용할 수 없음 <br> - 하이퍼스케일(Citus): 예 |
| **백업 및 복원** | - 고객이 관리 | - 단일 서버: 로컬 및 지역에 대한 사용자 구성을 사용하여 기본 제공 <br> - 유연한 서버: 영역 중복 스토리지의 사용자 구성을 사용하여 기본 제공 <br> - 하이퍼스케일(Citus): 기본 제공 |
| **데이터베이스 작업 모니터링** | - 고객이 관리 | - 단일 서버, 유연한 서버 및 하이퍼스케일(Citus): 세 가지 옵션 모두 데이터베이스 작업에 대한 경고를 설정하고 임계값에 도달하면 작동하는 기능을 고객에게 제공합니다. |
| **Advanced Threat Protection** | - 고객이 직접 이 보호를 구축해야 합니다. |- 단일 서버: 예 <br> - 유연한 서버: 미리 보기 중에는 사용할 수 없음 <br> - 하이퍼스케일(Citus): 예 |
| **재해 복구** | - 고객이 관리 | - 단일 서버: 지역 중복 백업 및 지역 읽기-복제본 <br> - 유연한 서버: 미리 보기 중에는 사용할 수 없음 <br> - 하이퍼스케일(Citus): 예 |
| **지능형 성능** | - 고객이 관리 | - 단일 서버: 예 <br> - 유연한 서버: 미리 보기 중에는 사용할 수 없음 <br> - 하이퍼스케일(Citus): 예 |

## <a name="total-cost-of-ownership-tco"></a>TCO(총 소유 비용)

데이터베이스 호스팅을 위한 최적의 솔루션을 결정할 때 TCO가 결정적인 역할을 하는 경우가 종종 있습니다. 현금이 부족한 스타트업이거나 탄탄한 회사에 속해 있지만 예산이 넉넉하지 않은 팀인 경우 특히 그렇습니다. 이 섹션에서는 Azure Database for PostgreSQL 및 PostgreSQL on Azure VMs에 적용되는 Azure의 청구 및 라이선스 기본 사항에 대해 설명합니다.

## <a name="billing"></a>결제

Azure Database for PostgreSQL은 현재 리소스 가격이 다른 여러 계층에서 서비스로 제공됩니다. 모든 리소스 요금은 고정 요율로 시간당 청구됩니다. 현재 지원되는 서비스 계층, 컴퓨팅 크기 및 스토리지 용량에 대한 최신 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/postgresql/server/)를 참조하세요. 애플리케이션의 달라지는 처리량 수요에 맞게 서비스 계층 및 컴퓨팅 크기를 동적으로 조정할 수 있습니다. 발신 인터넷 트래픽은 일반 [데이터 전송 요금](https://azure.microsoft.com/pricing/details/data-transfers/)이 청구됩니다.

Azure Database for PostgreSQL을 선택하면 Microsoft에서 자동으로 데이터베이스 소프트웨어를 구성, 패치 및 업그레이드합니다. 이러한 작업이 자동화되어 있으므로 관리 비용이 줄어듭니다. 또한 Azure Database for PostgreSQL은 [자동화된 백업 링크]() 기능을 제공합니다. 이러한 기능을 사용하여 비용을 크게 절감할 수 있으며, 특히 데이터베이스 수가 많을 경우 그 효과가 큽니다. 반면, PostgreSQL on Azure VMs를 선택하면 PostgreSQL 버전을 선택하여 실행할 수 있습니다. 그러나 프로비저닝된 VM 요금과 데이터, 백업, 모니터링 데이터 및 로그 저장소와 관련된 스토리지 비용 및 사용한 특정 PostgreSQL 라이선스 유형(있는 경우)에 대한 비용을 지불해야 합니다.

Azure Database for PostgreSQL 단일 서버는 서비스에 대 한 [SLA 보증](https://azure.microsoft.com/support/legal/sla/postgresql/v1_2/) 을 계속 유지 하면서 모든 종류의 노드 수준 중단에 대해 영역 수준 (AZ)에서 기본 제공 고가용성을 제공 합니다. 유연한 서버는 영역 중복 구성 없이 [작동 시간 sla](https://azure.microsoft.com/support/legal/sla/postgresql/v1_2/) 를 제공 합니다. 그러나 VM 내에 있는 데이터베이스의 고가용성을 유지하려면 PostgreSQL 데이터베이스에서 사용할 수 있는 [스트리밍 복제](https://www.postgresql.org/docs/12/warm-standby.html#STREAMING-REPLICATION) 같은 고가용성 옵션을 사용하세요. 지원되는 고가용성 옵션을 사용하면 추가 SLA가 제공되지 않습니다. 하지만 추가 비용과 관리 오버헤드를 대가로 99.99%보다 높은 데이터베이스 가용성을 달성할 수 있습니다.

가격 책정에 대한 자세한 내용은 다음 문서를 참조하세요.
- [Azure Database for PostgreSQL 가격 책정](https://azure.microsoft.com/pricing/details/postgresql/server/)
- [가상 머신 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>관리

많은 기업이 클라우드 서비스로 전환하기로 결정할 때 비용과 더불어 가장 중요하게 생각하는 것은 관리의 복잡성을 줄이는 것입니다.

IaaS를 선택하면 Microsoft에서 다음 작업을 처리합니다.

- 기본 인프라를 관리합니다.
- 기본 하드웨어 및 OS의 자동 패치를 제공합니다.

PaaS를 선택하면 Microsoft에서 다음 작업을 처리합니다.

- 기본 인프라를 관리합니다.
- 기본 하드웨어, OS 및 데이터베이스 엔진의 자동 패치를 제공합니다.
- 데이터베이스의 고가용성을 관리합니다.
- 자동으로 백업을 수행하고 모든 데이터를 복제하여 재해 복구를 제공합니다.
- 기본적으로 미사용 데이터 및 사용 중인 데이터를 암호화합니다.
- 서버를 모니터링하고 쿼리 성능 Insight 및 성능 권장 사항에 대한 기능을 제공합니다.

Azure Database for PostgreSQL을 선택하면 계속해서 직접 데이터베이스를 관리할 수 있습니다. 하지만 더 이상 데이터베이스 엔진, 운영 체제 또는 하드웨어를 관리할 필요가 없습니다. 계속해서 직접 관리할 수 있는 항목은 다음과 같습니다.

- 데이터베이스
- 로그인
- 인덱스 튜닝
- 쿼리 튜닝
- 감사
- 보안

또한 다른 데이터 센터에 고가용성을 구성하는 데 필요한 구성 및 관리가 최소화됩니다.

- PostgreSQL on Azure VMs를 선택하면 운영 체제와 PostgreSQL 서버 인스턴스 구성을 완벽하게 제어할 수 있습니다. VM과 함께 운영 체제 및 데이터베이스 소프트웨어를 업데이트 또는 업그레이드할 시기와 적용할 패치를 결정합니다. 또한 바이러스 백신 애플리케이션과 같은 추가 소프트웨어를 설치할 시기를 결정합니다. 자동화된 일부 기능이 제공되므로 패치, 백업 및 고가용성이 크게 간소화됩니다. VM 크기, 디스크 수 및 해당 스토리지 구성을 제어할 수 있습니다. 자세한 내용은 [Azure를 위한 가상 머신 및 클라우드 서비스 크기](../virtual-machines/sizes.md)를 참조하세요.

## <a name="time-to-move-to-azure-postgresql-service-paas"></a>Azure PostgreSQL Service(PaaS)로 전환할 시간

- 새로운 솔루션에 대한 개발자 생산성과 빠른 출시 시간이 중요한 경우 클라우드용으로 설계된 애플리케이션에 적합한 솔루션은 Azure Database for PostgreSQL입니다. DBA 같은 프로그래밍 방식 기능을 제공하는 이 서비스는 기본 운영 체제 및 데이터베이스 관리 부담을 덜어 주기 때문에 클라우드 설계자와 개발자에게 적합합니다.

- 새로운 온-프레미스 하드웨어를 획득하는 데 드는 시간과 비용을 줄이려면 서비스에서 지원하지 않는 PostgreSQL 엔진을 세밀하게 제어하고 사용자 지정할 수 있어야 하는 애플리케이션 또는 기본 OS에 대한 액세스가 필요한 애플리케이션에 적합한 솔루션은 PostgreSQL on Azure VMs입니다.

## <a name="next-steps"></a>다음 단계

- Azure Database for [PostgreSQL 가격 책정](https://azure.microsoft.com/pricing/details/postgresql/server/)을 확인하세요.
- 첫 번째 서버를 만들어서 시작합니다.