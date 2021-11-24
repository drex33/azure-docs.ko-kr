---
title: Azure Portal의 Apache Cassandra용 Azure Managed Instance에 대한 질문과 대답
description: Apache Cassandra용 Azure Managed Instance에 대한 질문과 대답입니다. 이 문서에서는 관리형 인스턴스를 사용하는 시기, 이점, 처리량 한도, 지원되는 지역 및 기타 구성 세부 정보에 대한 질문에 대답합니다.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021, mode-other
ms.openlocfilehash: e358422e4331bb83d3eda74a75d9132d48c06de0
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133066506"
---
# <a name="frequently-asked-questions-about-azure-managed-instance-for-apache-cassandra"></a>Azure Managed Instance for Apache Cassandra에 대한 질문과 대답

이 문서에서는 Apache Cassandra용 Azure Managed Instance에 대한 질문에 대답합니다. 관리형 인스턴스를 사용하는 시기, 이점, 처리량 한도, 지원되는 지역 및 구성 세부 정보에 대해 알아봅니다.

## <a name="general-faq"></a>일반 FAQ

### <a name="what-are-the-benefits-azure-managed-instance-for-apache-cassandra"></a>Apache Cassandra용 Azure Managed Instance의 이점은 무엇인가요?

Apache Cassandra 데이터베이스는 성능을 저하시키지 않고 확장성과 고가용성이 필요한 경우에 적절한 선택입니다. 상용 하드웨어 또는 클라우드 인프라에서 선형적 확장성과 검증된 내결함성을 제공하므로 중요 업무용 데이터에 적합한 플랫폼입니다. Apache Cassandra용 Azure Managed Instance는 Azure에 배포된 오픈 소스 Apache Cassandra 데이터 센터의 인스턴스를 관리하는 서비스입니다.

전체를 클라우드에서 사용할 수도 있고 하이브리드 클라우드 및 온-프레미스 클러스터의 일부로 사용할 수도 있습니다. 이 서비스는 유지 관리 오버헤드 없이 오픈 소스 Apache Cassandra에서 활용하던 세밀한 구성과 제어를 원하는 고객에게 적합합니다.

### <a name="why-should-i-use-this-service-instead-of-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API 대신 이 서비스를 사용해야 하는 이유는 무엇인가요?

Apache Cassandra용 Azure Managed Instance는 Azure Cosmos DB 팀에서 제공합니다. 오픈 소스 Apache Cassandra 데이터 센터 및 클러스터를 배포, 유지 관리, 스케일링하는 독립 실행형 관리 서비스입니다. 반면 [Azure Cosmos DB Cassandra API](../cosmos-db/cassandra-introduction.md)는 Apache Cassandra 유선 프로토콜에 대한 상호 운용성 계층을 제공하는 Platform-as-a-Service입니다. Apache Cassandra 클러스터와 똑같은 방식으로 작동하는 플랫폼을 원하는 경우 관리형 인스턴스 서비스를 선택해야 합니다. 자세한 내용은 [Apache Cassandra용 Azure Managed Instance와 Azure Cosmos DB Cassandra API 비교](compare-cosmosdb-managed-instance.md) 문서를 참조하세요.

### <a name="is-azure-managed-instance-for-apache-cassandra-dependent-on-azure-cosmos-db"></a>Apache Cassandra용 Azure Managed Instance는 Azure Cosmos DB에 종속되어 있나요?

아니요, Apache Cassandra용 Azure Managed Instance와 Azure Cosmos DB 백 엔드 사이에는 아키텍처 종속성이 없습니다. 

### <a name="does-azure-managed-instance-for-apache-cassandra-have-an-sla"></a>Apache Cassandra용 Azure Managed Instance에 SLA가 있나요?

예, SLA가 [여기](https://azure.microsoft.com/support/legal/sla/managed-instance-apache-cassandra/v1_0/)에 게시됩니다. 

#### <a name="can-i-deploy-azure-managed-instance-for-apache-cassandra-in-any-region"></a>모든 지역에서 Apache Cassandra용 Azure Managed Instance를 배포할 수 있나요?

현재 관리되는 인스턴스는 일부 지역에서만 사용할 수 있습니다.

### <a name="what-are-the-storage-and-throughput-limits-of-azure-managed-instance-for-apache-cassandra"></a>Apache Cassandra용 Azure Managed Instance의 스토리지 및 처리량 제한은 얼마인가요?

선택한 Virtual Machine SKU에 따라 제한이 다릅니다.

### <a name="how-are-cassandra-repairs-carried-out-in-azure-managed-instance-for-apache-cassandra"></a>Azure Managed Instance for Apache Cassandra에서 Cassandra 복구는 어떻게 수행하나요?

[cassandra-reaper.io](http://cassandra-reaper.io/)를 사용합니다. 자동으로 실행하도록 설정됩니다.

### <a name="what-is-the-cost-of-azure-managed-instance-for-apache-cassandra"></a>Apache Cassandra용 Azure Managed Instance의 비용은 얼마인가요?

관리형 인스턴스 요금은 기본 VM 비용을 기반으로 하며, 약간의 가격 인상이 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/managed-instance-apache-cassandra/)를 참조하세요.

### <a name="can-i-use-yaml-file-settings-to-configure-behavior"></a>YAML 파일 설정을 사용하여 동작을 구성할 수 있나요?

예, Azure Resource Manager 템플릿 배포의 일부로 YAML 파일 구성을 포함할 수 있습니다.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>처리량과 함께 인프라를 모니터링하려면 어떻게 해야 하나요?

[Prometheus](https://prometheus.io/docs/introduction/overview/) 서버는 클러스터에서 수행되는 작업을 모니터링하기 위해 호스트되며 엔드포인트를 노출합니다. 10분 또는 데이터 10GB 중 먼저 도달하는 임계값까지 유지됩니다. 이 모니터링을 사용하려면 [페더레이션](https://prometheus.io/docs/prometheus/latest/federation/) 및 Grafana와 같은 적절한 대시보드 도구를 설정해야 합니다.

### <a name="does-azure-managed-instance-for-apache-cassandra-provide-full-backups"></a>Apache Cassandra용 Azure Managed Instance는 전체 백업을 제공하나요?

예, Azure Storage로 전체 백업하고 새 클러스터로 복원하는 기능을 제공합니다.

### <a name="how-can-i-migrate-data-from-my-existing-apache-cassandra-cluster-to-azure-managed-instance-for-apache-cassandra"></a>기존 Apache Cassandra 클러스터의 데이터를 Apache Cassandra용 Azure Managed Instance로 마이그레이션하려면 어떻게 해야 하나요?

Apache Cassandra용 Azure Managed Instance는 데이터 센터 간에 데이터를 복제하고 스트리밍하는 Apache Cassandra의 모든 기능을 지원합니다.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-azure-managed-instance-for-apache-cassandra"></a>온-프레미스 Apache Cassandra 클러스터를 Apache Cassandra용 Azure Managed Instance와 페어링할 수 있나요?

예, 서비스에서 배포한 Azure Virtual Network 삽입형 데이터 센터를 사용하여 하이브리드 클러스터를 구성할 수 있습니다. Managed Instance 데이터 센터는 같은 클러스터 링 내부에 있는 온-프레미스 데이터 센터와 통신할 수 있습니다.

### <a name="where-can-i-give-feedback-on-azure-managed-instance-for-apache-cassandra-features"></a>Apache Cassandra용 Azure Managed Instance 기능에 대한 피드백은 어디서 제출할 수 있나요?

[사용자 의견 피드백](https://feedback.azure.com/d365community/forum/3002b3be-0d25-ec11-b6e6-000d3a4f0858?c=e6e5c7c4-0d25-ec11-b6e6-000d3a4f0858#)에서 "관리형 Apache Cassandra" 범주를 통해 피드백을 제출할 수 있습니다.

계정 관련 문제를 해결하려면 Azure Portal에서 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출합니다.

## <a name="deployment-specific-faq"></a>배포 관련 FAQ

### <a name="will-the-managed-instance-support-node-addition-cluster-status-and-node-status-commands"></a>관리형 인스턴스는 노드 추가, 클러스터 상태 및 노드 상태 명령을 지원하나요?

`status`와 같은 모든 *읽기 전용* Nodetool 명령은 Azure CLI를 통해 사용할 수 있습니다. 하지만 관리형 인스턴스의 노드 상태는 Microsoft에서 관리하기 때문에 *노드 추가* 와 같은 작업을 사용할 수 없습니다. 하이브리드 모드에서는 *Nodetool* 을 사용하여 클러스터에 연결할 수 있습니다. 그러나 Nodetool을 사용하면 클러스터가 불안정해질 수 있으므로 사용하지 않는 것이 좋습니다. 클러스터의 관리형 인스턴스 데이터 센터 상태와 관련된 프로덕션 지원 SLA가 무효화될 수도 있습니다.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>테이블 메타데이터의 다양한 설정은 어떻게 되나요?

블룸 필터, 캐싱, 읽기 복구 가능성, gc_grace, 압축 memtable_flush_period 등의 테이블 메타데이터 설정은 자체 호스팅 Apache Cassandra 환경과 마찬가지로 완벽하게 지원됩니다.

## <a name="next-steps"></a>다음 단계

다른 API에서 자주 묻는 질문에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Portal에서 관리형 인스턴스 만들기](create-cluster-portal.md)
* [Azure Databricks를 사용하여 관리형 Apache Spark 클러스터 배포](deploy-cluster-databricks.md)
* [Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 리소스 관리](manage-resources-cli.md)
