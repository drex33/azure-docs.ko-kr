---
title: Apache Cassandra용 Azure Managed Instance 소개
description: Apache Cassandra용 Azure Managed Instance에 대해 알아보세요. 이 서비스는 Azure에서 Apache Cassandra의 원시 오픈 소스 인스턴스의 배포 및 크기 조정을 관리합니다.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 65afb9af7d473a198b8570d563b9300b7d0e26c9
ms.sourcegitcommit: 1aeff9f012cfd868104ef0159c5204e402d75696
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133031887"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra"></a>Apache Cassandra용 Azure Managed Instance란?

Apache Cassandra용 Azure Managed Instance 서비스는 관리형 오픈 소스 Apache Cassandra 데이터 센터의 자동화된 배포 및 크기 조정 작업을 제공합니다. 이 서비스를 사용하면 하이브리드 시나리오를 가속화하고 지속적인 유지 관리를 줄일 수 있습니다.

:::image type="content" source="./media/introduction/icon.gif" alt-text="Apache Cassandra용 Azure Managed Instance는 Apache Cassandra에 대한 관리형 서비스입니다." border="false":::

## <a name="key-benefits"></a>주요 이점

### <a name="hybrid-deployments"></a>하이브리드 배포

이 서비스를 사용하여 가상 머신 확장 집합으로 자동 배포되는 Apache Cassandra 데이터 센터의 관리형 인스턴스를 새로운 또는 기존 Azure 가상 네트워크에 쉽게 배치할 수 있습니다. 이러한 데이터 센터는 Azure 또는 다른 클라우드 환경의 [Azure ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute)를 통해 온-프레미스에서 실행되는 기존 Apache Cassandra 링에 추가할 수 있습니다. 이렇게 하려면 [하이브리드 구성](configure-hybrid-cluster.md)을 사용합니다.

- **간소화된 배포:** 하이브리드 연결이 설정된 후에는 [간단한 명령](manage-resources-cli.md#create-datacenter)을 통해 Azure에 데이터 센터를 쉽게 배포할 수 있습니다.
- **메트릭:** 서비스에서 프로비전하는 각 데이터 센터 노드는 [Apache Cassandra용 메트릭 수집기](https://github.com/datastax/metric-collector-for-apache-cassandra)를 사용하여 메트릭을 내보냅니다. 메트릭은 [Prometheus 또는 Grafana에서 시각화](visualize-prometheus-grafana.md)할 수 있습니다. 또한 서비스는 [메트릭 및 진단 로깅에 대한 Azure Monitor](monitor-clusters.md)와 통합됩니다.

### <a name="simplified-scaling"></a>간소화된 크기 조정

관리형 인스턴스에서 데이터 센터의 규모를 확장 및 축소하는 작업이 완전히 관리됩니다. 필요한 노드 수를 선택하면 [간단한 명령](manage-resources-cli.md#update-datacenter)으로 크기 조정 오케스트레이터가 Cassandra 링 내에서 작업 설정을 처리합니다.

### <a name="managed-and-cost-effective"></a>관리형 및 비용 효율성

서비스는 다음과 같은 일반적인 Apache Cassandra 작업에 대한 [관리 작업](management-operations.md)을 제공합니다.

- 클러스터 프로비전
- 데이터 센터 프로비전
- 데이터 센터 크기 조정
- 데이터 센터 삭제
- 데이터 센터의 구성 변경
- Nodetool 복구
- 노드 상태 모니터링
- Virtual Machine 상태 모니터링
- 운영 체제 패치
- Apache Cassandra 패치
- 취약성 및 바이러스 검사
- 인증서 회전
- 스냅샷 백업

가격 책정 모델은 유연하고, 주문형의 인스턴스 기반이며, 라이선스 요금은 없습니다. 이 가격 책정 모델을 사용하면 특정 워크로드 요구 사항에 맞춰 조정할 수 있습니다. 노드당 코어 수, VM SKU, 메모리 크기 및 P30 디스크 수를 선택합니다.

## <a name="next-steps"></a>다음 단계

다음 빠른 시작을 진행하세요.

* [Azure Portal에서 관리형 인스턴스 만들기](create-cluster-portal.md)
* [Azure Databricks를 사용하여 관리형 Apache Spark 클러스터 배포](deploy-cluster-databricks.md)
* [Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 리소스 관리](manage-resources-cli.md)
