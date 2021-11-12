---
title: Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹을 사용 하 여 데이터를 분산 하 고 확장 하는 개념
titleSuffix: Azure Arc-enabled data services
description: Azure Arc를 사용 하는 데이터 배포에 대 한 개념-사용 PostgreSQL Hyperscale 서버 그룹
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 06/02/2021
ms.topic: how-to
ms.openlocfilehash: aee8700274d074d94b6f6f8e1e153f256cb6e158
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294203"
---
# <a name="concepts-for-distributing-data-with-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc를 사용 하는 데이터 배포에 대 한 개념-사용 PostgreSQL Hyperscale 서버 그룹

이 문서에서는 Azure Arc 지원 PostgreSQL 하이퍼스케일을 최대한 활용할 수 있는 중요한 주요 개념을 설명합니다.
아래 연결된 문서는 Azure Database for PostgreSQL 하이퍼스케일(Citus)에 대해 설명된 개념을 다룹니다. Azure Arc 지원 PostgreSQL 하이퍼스케일과 동일한 기술이므로 동일한 개념과 관점을 적용합니다.

**두 서비스의 차이점은 무엇인가요?**
- _Azure Database for PostgreSQL 하이퍼스케일(Citus)_

Azure(PaaS)에서 Database as a Service로 제공되는 Postgres 데이터베이스 엔진의 하이퍼스케일 폼 팩터입니다. Citus 확장을 기반으로 하 여 하이퍼 크기 조정 환경을 가능 하 게 합니다. 이 폼 팩터에서 서비스는 Microsoft 데이터 센터에서 실행되며 Microsoft가 운영합니다.

- _Azure Azure Arc-사용 PostgreSQL Hyperscale_

Azure Arc 지원 데이터 서비스와 함께 제공되는 Postgres 데이터베이스 엔진의 하이퍼스케일 폼 팩터입니다. 이 폼 팩터에서 고객은 시스템을 호스트하고 운영하는 인프라를 제공합니다.

Azure Arc 지원 PostgreSQL 하이퍼스케일의 주요 개념은 아래에 요약되어 있습니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>노드 및 테이블
Azure Arc 지원 Postgres 하이퍼스케일을 최대한 활용하려면 다음 개념에 대해 알고 있어야 합니다.
- Azure Arc 지원 PostgreSQL 하이퍼스케일의 특수화된 Postgres 노드: 코디네이터 및 작업자
- 테이블 형식: 분산 테이블, 참조 테이블 및 로컬 테이블
- 분할된 데이터베이스

자세한 내용은 [Azure Database for PostgreSQL – 하이퍼스케일(Citus)의 노드 및 테이블](../../postgresql/concepts-hyperscale-nodes.md)을 참조하세요. 

## <a name="determine-the-application-type"></a>애플리케이션 유형 확인
빌드하려는 애플리케이션의 유형을 명확하게 식별해야 합니다. 그 이유는 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에서 효율적인 쿼리를 실행하려면 테이블이 서버 전반에 걸쳐 제대로 분산되어야 하기 때문입니다. 권장 배포는 애플리케이션 유형과 해당 쿼리 패턴에 따라 달라집니다. Azure Arc 지원 Postgres 하이퍼스케일에서 잘 작동하는 애플리케이션에는 크게 두 종류가 있습니다.
- 다중 테넌트 애플리케이션
- 실시간 애플리케이션

데이터 모델링의 첫 번째 단계는 사용 중인 애플리케이션과 더 유사한 종류를 식별하는 것입니다.

자세한 내용은 [애플리케이션 유형 결정](../../postgresql/concepts-hyperscale-app-type.md)을 참조하세요.


## <a name="choose-a-distribution-column"></a>배포 열 선택
분산 열을 선택해야 하는 이유

이는 가장 중요한 모델링 결정 중 하나입니다. Azure Arc 지원 PostgreSQL 하이퍼스케일은 행의 분포 열 값을 기준으로 분할된 데이터베이스에 행을 저장합니다. 올바르게 선택할 경우 관련 데이터가 동일한 물리적 노드에 그룹화되어 쿼리를 빠르게 만들고 모든 SQL 기능을 지원할 수 있습니다. 잘못 선택할 경우, 시스템이 느리게 실행되며 노드 전체에서 모든 SQL 기능을 지원하지 않습니다. 이 문서에서는 가장 일반적인 두 가지 하이퍼스케일 시나리오에 대한 배포 열 팁을 제공합니다.

자세한 내용은 [배포 열 선택](../../postgresql/concepts-hyperscale-choose-distribution-column.md)을 참조하세요.


## <a name="table-colocation"></a>테이블 공동 배치

공동 배치는 동일한 노드에 관련 정보를 함께 저장하는 것을 의미합니다. 네트워크 트래픽 없이 필요한 모든 데이터를 사용할 수 있으면 쿼리가 신속하게 진행될 수 있습니다. 서로 다른 노드에 있는 관련 데이터를 공동 배치하면 각 노드에서 쿼리를 효율적으로 병렬 실행할 수 있습니다.

자세한 내용은 [테이블 공동 배치](../../postgresql/concepts-hyperscale-colocation.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계
- [Azure Arc 지원 PostgreSQL 하이퍼스케일을 만드는 방법에 관해 알아보기](create-postgresql-hyperscale-server-group.md)
- [Arc 데이터 컨트롤러의 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 스케일 아웃하는 정보에 관해 알아보기](scale-out-in-postgresql-hyperscale-server-group.md)
- [Azure Arc 지원 Data Services에 관해 알아보기](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [Azure Arc에 대해 자세히 알아보세요.](https://aka.ms/azurearc)
