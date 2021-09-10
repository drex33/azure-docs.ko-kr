---
title: Azure Arc 지원 SQL Managed Instance 서비스 계층
description: Azure Arc 지원 SQL Managed Instance 배포에 사용 가능한 서비스 계층을 설명합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: f0196d2a763b27c43fedb6371668321460f09c6f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121786187"
---
# <a name="azure-arc-enabled-sql-managed-instance-service-tiers"></a>Azure Arc 지원 SQL Managed Instance 서비스 계층

Azure SQL 제품군의 일부인 Azure Arc 지원 SQL Managed Instance는 2개의 [vCore](../../azure-sql/database/service-tiers-vcore.md) 서비스 계층에서 사용할 수 있습니다.

- **범용** 은 일반적인 성능 및 가용성 기능이 대부분의 워크로드용으로 설계된 예산 친화적 계층입니다.
- **중요 비즈니스용** 계층은 더 높은 가용성 기능과 함께 성능에 민감한 워크로드용으로 설계되었습니다.

현재까지 중요 비즈니스용 서비스 계층은 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 상태입니다. 범용 서비스 계층은 일반 공급 상태입니다. 

Azure에서 스토리지 및 컴퓨팅은 서비스 계층 간의 성능 처리량, 가용성 등을 위한 SLA(서비스 수준 계약)와 함께 Microsoft에서 제공됩니다. 고객은 Azure Arc 지원 데이터 서비스를 통해 스토리지 및 컴퓨팅을 제공합니다. 따라서 Azure Arc 지원 데이터 서비스와 함께 고객에게 보장된 SLA가 제공되지 않습니다. 하지만 고객이 서비스 계층에 관계없이 자신의 고유 성능 하드웨어를 사용할 수 있는 유연성이 있습니다. 

## <a name="service-tier-comparison"></a>서비스 계층 비교

다음은 2개의 서비스 계층 간 Azure Arc 지원 데이터 서비스에서 제공되는 여러 기능들에 대한 설명입니다.


Area | 중요 비즈니스용(미리 보기)* | 범용
----------|-----------------|------------------
기능 집합 | SQL Server Enterprise Edition과 동일 | SQL Server Standard Edition과 동일
CPU 제한/인스턴스 | 제한 없음  | 24개 코어
메모리 제한/인스턴스 | 제한 없음 | 128GB
고가용성 | 가용성 그룹 | Kubernetes 재배포 및 공유 스토리지를 포함하는 단일 인스턴스입니다.
읽기 확장 | 가용성 그룹 | None
IP 구성 요소 가격에 대한 AHB 환율 | 1:1 Enterprise Edition <br> 4:1 Standard Edition | 1:4 Enterprise Edition <br> 1:1 Standard Edition 
개발/테스트 가격 | 무료 | 무료

\* 현재 중요 비즈니스용 서비스 계층은 미리 보기 상태입니다. 미리 보기 기간 중에는 사용 요금이 발생하지 않습니다. 일반 공급 상태에 가까워짐에 따라 일부 기능이 변경될 수 있습니다.

## <a name="how-to-choose-between-the-service-tiers"></a>서비스 계층 선택 방법

고객이 자신의 비즈니스 요구에 맞게 성능 및 가용성 요구 사항을 지원하는 고유 하드웨어를 사용하기 때문에 서비스 계층 간의 주요 차이는 소프트웨어 수준에서 제공되는 기능입니다. 

### <a name="choose-general-purpose-if"></a>범용을 선택해야 하는 경우

- CPU/메모리 요구 사항이 범용 서비스 계층을 충족하거나 이러한 계층의 한계 내에 있는 경우
- Pod 재배포와 같이 Kubernetes에서 제공되는 고가용성 옵션이 워크로드에 충분한 경우
- 애플리케이션에 읽기 확장이 필요하지 않은 경우
- SQL Server Enterprise Edition과 같이 중요 비즈니스용 서비스 계층에서 제공되는 기능이 애플리케이션에 필요하지 않은 경우

### <a name="choose-business-critical-if"></a>중요 비즈니스용을 선택해야 하는 경우

- CPU/메모리 요구 사항이 범용 서비스 계층의 한계를 초과하는 경우
- 애플리케이션에 Kubernetes에서 제공되는 것보다 애플리케이션 장애 조치(failover)를 처리하기 위해 기본 제공되는 가용성 그룹과 같은 더 높은 고가용성 수준이 필요한 경우 
- 애플리케이션이 읽기 워크로드를 보조 복제본으로 오프로드할 수 있도록 읽기 확장을 활용할 수 있는 경우
- 애플리케이션에 중요 비즈니스용 서비스 계층에서만 제공되는 기능이 필요한 경우(SQL Server Enterprise Edition과 동일)
