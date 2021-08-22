---
title: 범용 서비스 계층
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database 및 Azure SQL Managed Instance에 대한 범용 서비스 계층에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: mathoma, urmilano
ms.date: 02/07/2019
ms.openlocfilehash: 3b3532c74ba49a82d80a27cc656e0016e405dfea
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121617"
---
# <a name="general-purpose-service-tier---azure-sql-database-and-azure-sql-managed-instance"></a>범용 서비스 계층 - Azure SQL Database 및 Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> vCore 기반 구매 모델의 범용 서비스 계층은 DTU 기반 구매 모델의 표준 서비스 계층이라고 합니다. vCore 기반 구매 모델과 DTU 기반 구매 모델을 비교하려면 [모델 및 리소스 구매](purchasing-models.md)를 참조하세요.

Azure SQL Database 및 Azure SQL Managed Instance는 인프라 오류의 경우에도 99.99%의 가용성을 보장하기 위해 클라우드 환경에 적합한 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. 

Azure SQL Database와 SQL Managed Instance에서 사용하는 서비스 계층은 두 가지가 있습니다. 

- 범용
- 중요 비즈니스용

또한 Azure SQL Database에는 Azure SQL Managed Instance에서 현재 사용할 수 없는 세 번째 서비스 계층이 있습니다.

- 하이퍼스케일

범용 서비스 계층의 아키텍처 모델은 컴퓨팅과 스토리지 분리를 기반으로 합니다. 이 아키텍처 모델은 데이터베이스 파일을 확실하게 복제하고 내부 인프라 오류가 발생하는 경우에도 데이터 무손실을 보장하는 Azure Blob Storage의 고가용성 및 안정성을 기반으로 합니다.

다음 그림은 분리된 컴퓨팅 및 스토리지 레이어가 있는 표준 아키텍처 모델의 4개 노드를 보여줍니다.

![컴퓨팅과 스토리지의 분리](./media/service-tier-general-purpose/general-purpose-service-tier.png)

범용 서비스 계층의 아키텍처 모델에는 두 개의 계층이 있습니다.

- 상태 비저장 컴퓨팅 레이어는 `sqlservr.exe` 프로세스를 실행하고 일시적인 데이터와 캐시된 데이터(예: 계획 캐시, 버퍼 풀, 열 저장 풀)만 포함합니다. 이 상태 비저장 노드는 프로세스를 초기화하고 노드의 상태를 제어하며 필요한 경우 다른 위치로 장애 조치(failover)를 수행하는 Azure 서비스 패브릭에 의해 운영됩니다.
- 상태 저장 데이터 계층에는 데이터베이스 파일(.mdf/.ldf)이 있으며 Azure Blob Storage에 저장됩니다. Azure Blob Storage는 데이터베이스 파일에 배치된 모든 레코드의 데이터가 손실되지 않도록 보장합니다. Azure Storage에는 프로세스가 중단되더라도 로그 파일의 모든 레코드나 데이터 파일의 페이지가 보존되도록 하는 데이터 가용성/백업 기능이 기본 제공됩니다.

데이터베이스 엔진이나 운영 체제가 업그레이드되고, 기본 인프라의 일부 부분은 실패하거나 `sqlservr.exe` 프로세스에서 심각한 문제가 감지될 때마다, Azure Service Fabric은 상태 비저장 프로세스를 다른 상태 비저장 컴퓨팅 노드로 옮깁니다. 장애 조치(failover) 시간을 최소화하기 위해 기본 노드의 장애 조치(failover)가 발생하는 경우 새 컴퓨팅 서비스를 실행하려고 대기하는 예비 노드 세트가 있습니다. Azure Storage 계층의 데이터는 영향을 받지 않으며 데이터/로그 파일은 새로 초기화된 프로세스에 연결됩니다. 이 프로세스는 99.99%의 가용성을 보장하지만 전환 시간과 새 노드가 콜드 캐시로 시작된다는 사실로 인해 실행 중인 많은 워크로드로 어느 정도 성능에 영향을 미칠 수 있습니다.

## <a name="when-to-choose-this-service-tier"></a>이 서비스 계층을 선택하는 시기

범용 서비스 계층은 대부분의 일반 워크로드를 위해 설계된 Azure SQL Database 및 Azure SQL Managed Instance의 기본 서비스입니다. 대부분의 경우 Azure 가상 머신의 SQL Server와 일치하는 스토리지 대기 시간이 5~10ms인 99.99% SLA의 완전 관리형 데이터베이스 엔진이 필요한 경우 범용 계층을 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [SQL Managed Instance](../managed-instance/resource-limits.md#service-tier-characteristics)의 범용/표준 계층, [vCore 모델](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) 또는 [DTU 모델](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)의 단일 데이터베이스 또는 [vCore 모델](resource-limits-vcore-elastic-pools.md#general-purpose---provisioned-compute---gen4) 및 [DTU 모델](resource-limits-dtu-elastic-pools.md#standard-elastic-pool-limits)의 탄력적 풀의 리소스 특성(코어 수, I/O, 메모리)을 찾습니다.
- [중요 비즈니스용](service-tier-business-critical.md) 및 [하이퍼스케일](service-tier-hyperscale.md)에 대해 알아 봅니다.
- [Service Fabric](../../service-fabric/service-fabric-overview.md)에 대해 알아봅니다.
- 고가용성 및 재해 복구에 대한 자세한 옵션은 [비즈니스 연속성](business-continuity-high-availability-disaster-recover-hadr-overview.md)을 참조하세요.
