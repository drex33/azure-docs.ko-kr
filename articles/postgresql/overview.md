---
title: Azure Database for PostgreSQL이란?
description: 유연한 서버의 컨텍스트에서 Azure Database for PostgreSQL 관계형 데이터베이스 서비스에 대한 개요를 제공합니다.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/30/2021
ms.openlocfilehash: e3e0e0682c1e02bd84ccc77226fd24c57292f685
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133267473"
---
# <a name="what-is-azure-database-for-postgresql"></a>Azure Database for PostgreSQL이란?

Azure Database for PostgreSQL은 [PostgreSQL 커뮤니티 버전](https://www.postgresql.org/)(GPLv2 라이선스에서 사용 가능) 데이터베이스 엔진을 기반으로 하는 Microsoft 클라우드의 관계형 데이터베이스 서비스입니다. PostgreSQL용 Azure Database는 다음과 같은 기능을 제공합니다.

- 기본 제공되는 고가용성
- 최대 35일 동안 자동 백업 및 특정 시점 복원을 사용하여 데이터 보호
- 서비스를 안전하고 최신 상태로 유지하기 위해 기본 하드웨어, 운영 체제 및 데이터베이스 엔진을 자동으로 유지 관리
- 예측 가능한 성능, 종량제 가격 책정 사용
- 몇 초 내에 탄력적 스케일링
- 엔터프라이즈급 보안 및 업계 최고 수준의 규정 준수를 통해 중요한 미사용 데이터 및 사용 중인 데이터를 안전하게 보호
- 모니터링 및 자동화를 통해 대규모 배포의 관리 및 모니터링 간소화
- 업계 최고 수준의 지원 환경

 :::image type="content" source="./media/overview/overview-what-is-azure-postgres.png" alt-text="Azure Database for PostgreSQL":::

이러한 기능에는 인증이 필요하지 않고 추가 비용 없이 제공됩니다. 그러면 가상 머신과 인프라를 관리하는 데 귀중한 시간과 리소스를 할당하는 대신 빠른 애플리케이션 개발에 집중하고 시장 출시 시간을 단축할 수 있습니다. 또한 오픈 소스 도구 및 선택한 플랫폼을 사용하여 애플리케이션을 개발하고 새로운 기술을 배울 필요 없이 비즈니스에서 요구하는 속도 및 효율성을 계속 제공할 수 있습니다.

## <a name="deployment-models"></a>배포 모델

PostgreSQL 커뮤니티 버전에서 제공하는 Azure Database for PostgreSQL은 다음과 같은 세 가지 배포 모드로 사용할 수 있습니다.

- 단일 서버
- 유연한 서버(미리 보기)
- 하이퍼스케일(Citus)

### <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 단일 서버

Azure Database for PostgreSQL 단일 서버는 데이터베이스 사용자 지정을 최소화한 완전 관리형 데이터베이스 서비스입니다. 단일 서버 플랫폼은 최소한의 사용자 구성과 제어를 통해 패치, 백업, 고가용성, 보안 등 대부분의 데이터베이스 관리 기능을 처리하도록 설계되었습니다. 이 아키텍처는 단일 가용성 영역에서 99.99%의 가용성을 기본 제공하도록 고가용성에 최적화되었습니다. PostgreSQL 커뮤니티 버전 9.5, 9.6, 10 및 11을 지원합니다. 이 서비스는 현재 다양한 [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/)에서 일반 공급됩니다.

단일 서버 배포 옵션에서 제공하는 세 가지 가격 책정 계층은 기본, 범용 및 메모리 최적화의 세 가지 가격 책정 계층 중 하나에서 만들 수 있습니다. 각 계층에서는 데이터베이스 작업을 지원하는 다양한 리소스 기능을 제공합니다. 한 달에 몇 달러로 작은 데이터베이스에 첫 번째 앱을 빌드하고 솔루션의 요구에 맞게 규모를 조정할 수 있습니다. 동적 확장성을 사용하면 데이터베이스가 빠르게 변화하는 리소스 요구 사항에 투명하게 대응할 수 있습니다. 필요할 경우 필요한 리소스에 대해서만 요금을 지불합니다. 자세한 내용은 [가격 책정 계층](./concepts-pricing-tiers.md)을 참조하세요.

단일 서버는 패치 일정 및 사용자 지정 PostgreSQL 구성 설정에 대한 세부적인 제어 없이 자동화된 패치를 처리하도록 설계된 클라우드 네이티브 애플리케이션에 가장 적합합니다.

단일 서버 배포 모드에 대한 자세한 개요는 [단일 서버 개요](./overview-single-server.md)를 참조하세요.

### <a name="azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버

Azure Database for PostgreSQL 유연한 서버는 데이터베이스 관리 기능 및 구성 설정에 대한 보다 세부적인 제어와 유연성을 제공하도록 설계된 완전 관리형 데이터베이스 서비스입니다. 일반적으로 이 서비스는 사용자 요구 사항에 따라 더 많은 유연성과 사용자 지정을 제공합니다. 유연한 서버 아키텍처는 사용자가 단일 가용성 영역 및 여러 가용성 영역에서 고가용성을 선택할 수 있습니다. 유연한 서버는 서버를 중지/시작하는 기능과 버스트 가능 컴퓨팅 계층을 통해 비용을 최적화할 수 있으므로 전체 컴퓨팅 용량이 지속적으로 필요하지 않는 워크로드에 적합합니다. 이 서비스는 현재 PostgreSQL 커뮤니티 버전 11 및 12를 지원하며 새 버전이 곧 추가될 예정입니다. 이 서비스는 현재 공개 미리 보기로 제공되며, 다양한 Azure 지역에서 사용할 수 있습니다.

유연한 서버는 다음과 같은 용도에 적합합니다.

- 더 나은 제어 및 사용자 지정이 필요한 애플리케이션 개발
- 서버를 중지/시작하는 기능을 사용하여 비용 최적화
- 영역 중복 고가용성
- 관리되는 유지 관리 기간
  
유연한 서버 배포 모드에 대한 자세한 개요는 [유연한 서버 개요](./flexible-server/overview.md)를 참조하세요.

### <a name="azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL – 하이퍼스케일(Citus)

하이퍼스케일(Citus) 옵션은 분할을 사용하여 여러 머신에 걸쳐 쿼리 크기를 수평으로 조정합니다. 쿼리 엔진은 들어오는 SQL 쿼리를 이러한 서버 간에 병렬 처리하여 큰 데이터 세트에서 더 빠르게 응답합니다. 이 옵션은 더 뛰어난 크기 조정과 성능이 필요한 애플리케이션, 일반적으로 100GB의 데이터에 이르거나 이미 초과한 워크로드를 처리합니다.

하이퍼스케일(Citus) 배포 옵션은 다음과 같은 이점을 누릴 수 있습니다.

- 분할을 사용하여 여러 머신에서 수평적 크기 조정
- 이러한 서버 간에 쿼리를 병렬 처리하여 큰 데이터 세트에서 더 빠르게 응답
- 다중 테넌트 애플리케이션, 실시간 운영 분석 및 높은 처리량 트랜잭션 워크로드에 대한 탁월한 지원
  
PostgreSQL용으로 빌드된 애플리케이션은 표준 [연결 라이브러리](./concepts-connection-libraries.md) 및 최소한의 변경을 통해 하이퍼스케일(Citus)에서 분산 쿼리를 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Database for PostgreSQL의 세 가지 배포 모드에 대해 자세히 알아보고 요구 사항에 맞는 옵션을 선택합니다.

- [단일 서버](./overview-single-server.md)
- [유연한 서버](./flexible-server/overview.md)
- [하이퍼스케일(Citus)](./hyperscale-overview.md)
