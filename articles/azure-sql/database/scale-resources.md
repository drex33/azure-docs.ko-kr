---
title: 리소스 스케일링
description: 이 문서에서는 할당된 리소스를 추가하거나 제거하여 Azure SQL Database 및 SQL Managed Instance에서 데이터베이스를 스케일링하는 방법을 설명합니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: mathoma, urmilano, wiassaf
ms.date: 06/25/2019
ms.openlocfilehash: d6dd794a9eb0a7af1ed2e91a04c27d5321e14ca3
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179358"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>최소 가동 중지 시간으로 동적으로 데이터베이스 리소스 크기 조정
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 및 SQL Managed Instance를 사용하면 [가동 중지 시간](https://azure.microsoft.com/support/legal/sla/azure-sql-database)을 최소화하면서 데이터베이스에 더 많은 리소스를 동적으로 추가할 수 있습니다. 그러나 짧은 시간 동안 데이터베이스에 대한 연결이 끊어지는 전환 기간이 있는데, 이는 재시도 논리를 사용하여 완화할 수 있습니다.

## <a name="overview"></a>개요

앱에 대한 수요가 몇 개의 디바이스 및 고객에서 수백만으로 증가할 경우, Azure SQL Database 및 SQL Managed Instance에서 가동 중지 시간을 최소화하면서 즉시 스케일링합니다. 확장성은 PaaS(Platform as a Service)의 가장 중요한 특성 중 하나로, 필요한 경우 서비스에 더 많은 리소스를 동적으로 추가할 수 있습니다. Azure SQL Database를 사용하면 데이터베이스에 할당된 리소스(CPU 처리 능력, 메모리, IO 처리량 및 스토리지)를 쉽게 변경할 수 있습니다.

인덱싱 또는 쿼리 재작성 방법으로 해결할 수 없는, 애플리케이션 사용량 증가로 인한 성능 문제를 완화할 수 있습니다. 데이터베이스가 현재 리소스 한도에 도달하고 들어오는 워크로드를 처리하기 위해 추가 처리 능력이 필요할 때 리소스를 추가하면 빠르게 대응할 수 있습니다. Azure SQL Database를 사용하면 필요하지 않을 때 리소스를 축소하여 비용을 줄일 수도 있습니다.

하드웨어 구매 및 기본 인프라 변경에 대해 염려할 필요가 없습니다. Azure Portal에서 슬라이더를 사용하여 데이터베이스를 쉽게 스케일링할 수 있습니다.

![데이터베이스 성능 조정](./media/scale-resources/scale-performance.svg)

Azure SQL Database는 [DTU 기반 구매 모델](service-tiers-dtu.md)과 [vCore 기반 구매 모델](service-tiers-vcore.md)을 제공하는 반면, Azure SQL Managed Instance는 [vCore 기반 구매 모델](service-tiers-vcore.md)만 제공합니다. 

- [DTU 기반 구매 모델](service-tiers-dtu.md)은 경량 또는 중량 데이터베이스 워크로드를 지원하기 위해 기본, 표준 및 프리미엄의 세 가지 서비스 계층으로 컴퓨팅, 메모리 및 I/O 리소스를 혼합하여 제공합니다. 각 계층 내의 성능 수준은 다양하게 섞인 리소스를 제공합니다. 여기에 스토리지 리소스를 추가할 수 있습니다.
- [vCore 기반 구매 모델](service-tiers-vcore.md)을 통해 vCore 개수, 크기나 메모리 및 스토리지의 크기와 속도를 선택할 수 있습니다. 이 구매 모델은 범용, 중요 비즈니스용, 하이퍼스케일의 세 가지 서비스 계층을 제공합니다.

데이터베이스, 탄력적 풀 또는 관리되는 인스턴스에 대한 서비스 계층, 컴퓨팅 계층 및 리소스 제한은 언제든지 변경할 수 있습니다. 예를 들어 서버리스 컴퓨팅 계층을 사용하여 단일 데이터베이스에서 첫 번째 앱을 빌드한 다음, 솔루션의 요구 사항을 충족하기 위해 언제든지 수동으로 또는 프로그래밍 방식으로 서비스 계층을 프로비전된 컴퓨팅 계층으로 변경할 수 있습니다.

> [!NOTE]
> 데이터베이스의 서비스 계층을 변경할 수 없는 주목할 만한 예외는 다음과 같습니다.
> - 하이퍼스케일 서비스 계층의 데이터베이스는 현재 다른 서비스 계층으로 변경할 수 없습니다.
> - 중요 비즈니스용/Premium 서비스 [계층에서만 사용할 수](features-comparison.md#features-of-sql-database-and-sql-managed-instance) 있는 기능을 사용하는 데이터베이스는 범용/표준 서비스 계층을 사용하도록 변경할 수 없습니다.

워크로드 요구 사항을 충족하도록 서비스 목표를 변경하거나 크기를 조정하여 데이터베이스에 할당된 리소스를 조정할 수 있습니다. 또한 필요할 때 필요한 리소스에 대해서만 비용을 지불할 수 있습니다. 크기 조정 작업이 애플리케이션에 미칠 수 있는 잠재적 영향에 대한 [참고를](#impact-of-scale-up-or-scale-down-operations) 참조하세요.

> [!NOTE]
> 동적 확장성은 자동 크기 조정과 다릅니다. 자동 크기 조정은 서비스가 조건에 따라 자동으로 스케일링하는 경우인 반면, 동적 확장성은 가동 중지 시간을 최소화하면서 수동 스케일링을 허용합니다. Azure SQL Database 단일 데이터베이스의 크기를 수동으로 조정하거나 [서버리스 계층의](serverless-tier-overview.md)경우 컴퓨팅 리소스의 크기를 자동으로 조정하도록 를 설정합니다. [데이터베이스가 풀의](elastic-pool-overview.md)리소스를 공유할 수 있도록 하는 탄력적 풀은 현재 수동으로만 확장할 수 있습니다.

Azure SQL Database는 데이터베이스를 동적으로 스케일링하는 기능을 제공합니다.

- [단일 데이터베이스](single-database-scale.md)에서는 [DTU](resource-limits-dtu-single-databases.md) 또는 [vCore](resource-limits-vcore-single-databases.md) 모델 중 하나를 사용하여 각 데이터베이스에 할당되는 최대 리소스 양을 정의할 수 있습니다.
- [탄력적 풀](elastic-pool-scale.md)에서는 풀의 데이터베이스 그룹당 최대 리소스 한도를 정의할 수 있습니다.

Azure SQL Managed Instance를 사용하여 스케일링할 수도 있습니다. 

- [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)는 [vCore](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) 모드를 사용하며, 인스턴스에 할당된 최대 CPU 코어 수와 최대 스토리지를 정의할 수 있습니다. 관리되는 인스턴스 내의 모든 데이터베이스가 인스턴스에 할당된 리소스를 공유합니다.

## <a name="impact-of-scale-up-or-scale-down-operations"></a>강화 또는 규모 축소 작업의 영향

위에서 언급한 모든 종류에서 강화 또는 규모 축소 작업을 시작하면 데이터베이스 엔진 프로세스가 다시 시작되고 필요한 경우 다른 가상 머신으로 이동합니다. 데이터베이스 엔진 프로세스를 새 가상 머신으로 이동하는 것은 기존 Azure SQL Database 서비스를 계속 사용할 수 있는 **온라인 프로세스입니다.** 대상 데이터베이스 엔진이 쿼리를 처리할 준비가 되면 현재 데이터베이스 엔진에 대한 열린 연결이 [종료되고](single-database-scale.md#impact)커밋되지 않은 트랜잭션이 롤백됩니다. 대상 데이터베이스 엔진에 대한 새 연결이 만들어집니다.

> [!NOTE]
> 데이터 가져오기, 데이터 처리 작업, 인덱스 다시 작성 등의 장기 실행 트랜잭션이 실행되는 경우 또는 인스턴스에 활성 연결이 있는 경우에는 관리되는 인스턴스를 스케일링하지 않는 것이 좋습니다. 스케일링을 완료하는 데 평소보다 더 오래 걸리는 것을 방지하려면 장기 실행 작업이 모두 완료될 때 인스턴스를 스케일링해야 합니다.

> [!NOTE]
> 스케일 업/스케일 다운 프로세스가 완료되면 짧은 연결 중단을 예측할 수 있습니다. [표준 일시적 오류에 대한 재시도 논리](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)를 구현한 경우 장애 조치(failover)를 확인할 수 없습니다.

## <a name="alternative-scale-methods"></a>대체 크기 조정 방법

리소스 스케일링은 데이터베이스 또는 애플리케이션 코드를 변경하지 않고 데이터베이스 성능을 개선하는 가장 쉽고 효과적인 방법입니다. 경우에 따라 가장 높은 서비스 계층, 컴퓨팅 크기, 성능 최적화를 사용해도 워크로드가 성공적이고 비용 효과적인 방식으로 처리되지 않을 수 있습니다. 이 경우 다음과 같은 추가 옵션을 사용하여 데이터베이스를 스케일링할 수 있습니다.

- [읽기 스케일 아웃](read-scale-out.md)은 데이터의 읽기 전용 복제본이 한 개 있는 경우와 보고서 등 까다로운 읽기 전용 쿼리를 실행할 수 있는 경우에 사용할 수 있는 기능입니다. 읽기 전용 복제본은 주 데이터베이스의 리소스 사용량에 영향을 주지 않고 읽기 전용 워크로드를 처리합니다.
- [데이터베이스 분할](elastic-scale-introduction.md)은 데이터를 여러 데이터베이스로 분할하고 독립적으로 크기를 조정할 수 있는 기술 집합입니다.

## <a name="next-steps"></a>다음 단계

- 데이터베이스 코드를 변경하여 데이터베이스 성능을 향상하는 방법에 대한 자세한 내용은 [성능 권장 사항 찾기 및 적용](database-advisor-find-recommendations-portal.md)을 참조하세요.
- 기본 제공 데이터베이스 인텔리전스를 통해 데이터베이스를 최적화하는 방법에 대한 자세한 내용은 [자동 조정](automatic-tuning-overview.md)을 참조하세요.
- Azure SQL Database의 읽기 스케일 아웃에 대한 자세한 내용은 [읽기 전용 복제본을 사용하여 읽기 전용 쿼리 워크로드의 부하를 분산](read-scale-out.md)하는 방법을 참조하세요.
- 데이터베이스 분할에 대한 자세한 내용은 [Azure SQL Database를 사용하여 확장](elastic-scale-introduction.md)을 참조하세요.
- 스크립트를 사용하여 단일 데이터베이스를 모니터링하고 크기를 조정하는 예제는 [PowerShell을 사용하여 단일 SQL Database 모니터링 및 크기 조정을](scripts/monitor-and-scale-database-powershell.md)참조하세요.
