---
title: Apache Cassandra용 Azure Managed Instance와 Azure Cosmos DB Cassandra API 간의 차이점
description: Apache Cassandra용 Azure Managed Instance와 Azure Cosmos DB Cassandra API 간의 차이점에 대해 알아봅니다. 또한 이러한 각 서비스의 이점과 이러한 서비스를 선택해야 하는 시기에 대해 알아봅니다.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1927911563775711d6f0ec969ada82f1be286650
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032494"
---
# <a name="differences-between-azure-managed-instance-for-apache-cassandra-and-azure-cosmos-db-cassandra-api"></a>Apache Cassandra용 Azure Managed Instance와 Azure Cosmos DB Cassandra API 간의 차이점 

이 문서에서는 Apache Cassandra용 Azure Managed Instance와 Azure Cosmos DB Cassandra API 간의 차이점에 대해 알아봅니다. 이 문서에서는 두 서비스 중에 선택하는 방법 또는 고객 고유의 Apache Cassandra 환경을 호스트해야 하는 시기에 대한 권장 사항을 제공합니다.

## <a name="key-differences"></a>주요 차이점

Apache Cassandra용 Azure Managed Instance는 Azure에서 오픈 소스 Apache Cassandra 인스턴스의 노드 상태를 유지 관리하는 자동화된 배포, 스케일링 및 작업을 제공합니다. 또한 기존 온-프레미스 또는 클라우드 자체 호스팅 Apache Cassandra 클러스터의 용량을 스케일 아웃하는 기능을 제공합니다. 기존 클러스터 링에 관리형 Cassandra 데이터 센터를 추가하는 방법으로 스케일 아웃합니다.

Azure Cosmos DB의 [Cassandra API](../cosmos-db/cassandra-introduction.md)는 Microsoft의 전역적으로 분산된 클라우드 네이티브 데이터베이스 서비스인 [Azure Cosmos DB](../cosmos-db/index.yml)의 위에 있는 호환성 계층입니다. Azure의 이러한 서비스 조합은 복잡한 하이브리드 클라우드 환경에서 Apache Cassandra 사용자에게 지속적으로 선택권을 보장합니다.

## <a name="how-to-choose"></a>선택 방법

다음 표에서는 각 배포 방법에 적합한 일반적인 시나리오, 워크로드 요구 사항 및 목표를 보여줍니다.

| |온-프레미스 또는 Azure의 자체 호스팅 Apache Cassandra | Apache Cassandra용 Azure Managed Instance | Azure Cosmos DB Cassandra API |
|---------|---------|---------|---------|
|**배포 유형**| 사용자 지정 패치 또는 snitch를 사용하여 고도로 사용자 지정된 Apache Cassandra 배포가 있습니다. | 사용자 지정 코드가 없는 표준 오픈 소스 Apache Cassandra 배포가 있습니다. | Apache Cassandra에 종속된 것이 아니라 [유선 프로토콜](../cosmos-db/cassandra-support.md) 수준에서 모든 오픈 소스 클라이언트 드라이버와 호환되는 플랫폼을 원합니다. |
| **운영 오버헤드**| 클러스터를 배포, 구성 및 유지 관리할 수 있는 기존 Cassandra 전문가가 있습니다.  | Apache Cassandra 노드 상태에 대한 운영 오버헤드를 줄이면서도 복제 및 일관성 같은 플랫폼 수준 구성을 계속 제어할 수 있기를 원합니다. | 클라우드에서 완전 관리형 Platform-as-as-Service 데이터베이스를 사용하여 운영 오버헤드를 제거하려고 합니다. |
| **운영 체제 요구 사항**| 사용자 지정 또는 최적의 Virtual Machine 운영 체제 이미지를 유지하기 위해 충족해야 하는 요구 사항이 있습니다. | 바닐라 이미지를 사용할 수는 있지만 SKU, 메모리, 디스크 및 IOPS를 제어하고 싶습니다. | Azure Cosmos DB의 [요청 단위](../cosmos-db/request-units.md)처럼 처리량에 대한 일대일 관계를 사용하여 용량 프로비저닝을 단순화하고 정규화된 단일 메트릭으로 표현하고 싶습니다. |
| **가격 책정 모델**| Datastax 도구와 같은 관리 소프트웨어를 원하며 라이선스 비용에 만족합니다. | 순수한 오픈 소스 라이선스 및 VM 인스턴스 기반 가격 책정을 선호합니다. | [자동 스케일링](../cosmos-db/manage-scale-cassandra.md#use-autoscale) 및 [서버리스](../cosmos-db/serverless.md) 제품이 포함된 클라우드 기본 가격 책정을 사용하려 합니다. |
| **분석**| 분석 파이프라인을 빌드하고 유지 관리하기 위한 오버헤드에 관계 없이 분석 파이프라인 프로비저닝을 완벽하게 제어할 수 있기를 원합니다. | Azure Databricks와 같은 클라우드 기반 분석 서비스를 원합니다. | [Cosmos DB용 Azure Synapse Link](../cosmos-db/synapse-link.md)를 통해 플랫폼에 기본 제공되는 거의 실시간 하이브리드 트랜잭션 분석을 원합니다. |
| **워크로드 패턴**| 워크로드가 매우 안정적인 상태이며 클러스터에서 스케일링을 자주 할 필요가 없습니다. | 워크로드가 일정하지 않기 때문에 간편하게 데이터 센터의 노드를 스케일 업 또는 스케일 다운하거나 데이터 센터를 추가/제거할 수 있어야 합니다. | 워크로드가 일정하지 않은 경우가 많기 때문에 신속하게 대량으로 스케일 업 또는 스케일 다운할 수 있어야 합니다. |
| **SLA**| 일관성, 처리량, 가용성 및 재해 복구에 대한 SLA를 유지 관리하는 현재 프로세스에 만족합니다. | 일관성, 처리량 및 가용성에 대한 SLA를 유지 관리하는 현재 프로세스에 만족하지만 백업과 관련하여 도움이 필요합니다. | 일관성, 처리량, 가용성 및 재해 복구에 대한 완전히 포괄적인 SLA를 원합니다. |

## <a name="next-steps"></a>다음 단계

다음 빠른 시작을 진행하세요.

* [Azure Portal에서 관리형 인스턴스 만들기](create-cluster-portal.md)
