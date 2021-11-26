---
title: Azure Managed Instance for Apache Cassandra의 관리 작업
description: Azure Managed Instance for Apache Cassandra에서 지원하는 관리 작업에 대해 알아봅니다. 또한 독립 실행형 및 하이브리드 클러스터를 유지 관리 하는 경우 Azure 지원 팀과 고객 간의 책임 분리에 대해 설명합니다.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5061fc71dd5b429e576cdeabe0285cf0d9d440e3
ms.sourcegitcommit: e9e332a512ed615a3c8ad5a11baa21649f14116d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133096393"
---
# <a name="management-operations-in-azure-managed-instance-for-apache-cassandra"></a>Azure Managed Instance for Apache Cassandra의 관리 작업

Azure Managed Instance for Apache Cassandra는 관리형 오픈 소스 Apache Cassandra 데이터 센터에 자동화된 배포 및 크기 조정 작업을 제공합니다. 이 문서에서는 해당 서비스에서 제공하는 관리 작업 및 기능을 정의합니다. 또한 독립 실행형 및 [하이브리드](configure-hybrid-cluster.md) 클러스터를 유지 관리 하는 경우 Azure 지원 팀과 고객 간의 책임 분리에 대해 설명합니다.

## <a name="patching"></a>패치

* 운영 체제 수준 패치는 약 2주 주기로 자동으로 수행됩니다.

* Apache Cassandra 소프트웨어 수준 패치는 보안 취약성이 식별될 때 수행됩니다. 패치 주기는 다를 수 있습니다.

* 패치 중 컴퓨터는 한 번에 하나의 랙씩 재부팅됩니다. **쿼럼 ALL 설정이 사용되지 않고** 복제 요소가 **3 이상** 인 한, 애플리케이션 측면에서 성능 저하가 발생해서는 안 됩니다.

* Apache Cassandra의 버전은 `X.Y.Z` 형식입니다. 서비스 도구를 통해 주(X) 및 부(Y) 버전의 배포를 수동으로 제어할 수 있습니다. 반면 해당 주/부 버전 조합에 필요할 수 있는 Cassandra 패치(Z)는 자동으로 수행됩니다.  

## <a name="maintenance"></a>유지 관리

* [Nodetool 복구](https://docs.datastax.com/en/cassandra-oss/3.x/cassandra/tools/toolsRepair.html)는 [reaper](http://cassandra-reaper.io/)를 사용하여 서비스에서 자동으로 실행됩니다. 이 도구는 매주 한 번 실행됩니다. [하이브리드 배포](configure-hybrid-cluster.md)에 고유한 서비스를 사용하는 경우 이 기능을 사용하지 않도록 설정할 수 있습니다.

* 노드 상태 모니터링은 다음으로 구성됩니다.

  * Cassandra 링에서 각 노드의 멤버 자격을 적극적으로 모니터링합니다.
  * 가상 머신을 적극적으로 모니터링하여 Azure, Virtual Machines, 스토리지, Linux 및 지원 소프트웨어의 문제를 식별하고 해결합니다.

## <a name="support"></a>Support(지원)

Azure Managed Instance for Apache Cassandra는 관리형 클러스터의 데이터 센터 가용성을 위한 [SLA](https://azure.microsoft.com/support/legal/sla/managed-instance-apache-cassandra/v1_0/)를 제공합니다. 서비스 사용과 관련된 문제가 발생하는 경우 [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)에서 지원 요청을 제출합니다. 

>[!IMPORTANT]
> 지원 사례를 통해 보고된 문제를 조사 및 진단하고 가능한 경우 해결하거나 완화하려고 합니다. 그러나 CPU, 디스크 또는 네트워크 문제를 일으키는 Apache Cassandra 구성 수준 사용에 대한 책임은 사용자에게 있습니다.
>
> 이러한 문제의 예는 다음과 같습니다.
>
>  * 비효율적인 쿼리 작업
>  * 용량을 초과하는 처리량
>  * 스토리지 용량을 초과하는 데이터 수집
>  * 잘못된 Keyspace 구성 설정
>  * 잘못된 데이터 모델 또는 파티션 키 전략 
>
> 지원 사례를 조사하고 문제의 근본 원인이 Apache Cassandra 구성 수준(유지 관리하는 기본 플랫폼 수준 측면이 아님)에 있는 경우 사례가 종결될 수 있습니다. 가능한 경우 수정에 대한 권장 사항 및 지침도 제공합니다. 따라서 위와 같은 Apache Cassandra의 일반적인 애플리케이션/구성 수준 문제를 방지하기 위해 [메트릭을 사용하도록 설정](visualize-prometheus-grafana.md)하고/또는 [Azure Monitor 통합](monitor-clusters.md )에 익숙해지는 것이 좋습니다. 



## <a name="backup-and-restore"></a>백업 및 복원

스냅샷 백업은 기본적으로 활성화되어 있으며 4시간마다 수행됩니다. 백업은 내부 Azure Blob Storage 계정에 저장되며 최대 2일(48시간) 동안 유지됩니다. 백업에 대한 비용은 없습니다. 백업에서 복원하려면 Azure Portal에서 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출하세요.

> [!WARNING]
> 백업은 새 클러스터에만 복원됩니다. 백업은 실수로 삭제되는 시나리오를 위한 것이며 지역 중복이 아닙니다. 따라서 전체 지역 가동 중단 시 DR(재해 복구) 전략으로 사용하지 않는 것이 좋습니다. 지역 전체 중단으로부터 보호하려면 다중 지역 배포를 권장합니다. [여기](create-multi-region-cluster.md)에서 다중 지역 배포에 대한 빠른 시작을 살펴보세요. 

## <a name="security"></a>보안

Azure Managed Instance for Apache Cassandra는 다음과 같이 다양하고 명시적인 기본 제공 보안 제어 및 기능을 제공합니다.

* 제어되는 공급망을 사용하여 강화된 Linux 가상 머신 이미지
* 운영 체제 수준에서 일반적인 취약성 및 노출(CVE) 모니터링
* 관리형 가상 머신에서 호스팅되는 Apache Cassandra 및 Prometheus 소프트웨어 모두에 대한 인증서 전환
* 적극적인 취약성 검색
* 적극적인 바이러스 검색
* 보안 코딩 방법

## <a name="hybrid-support"></a>하이브리드 지원

[하이브리드](configure-hybrid-cluster.md) 클러스터가 구성된 경우 서비스에서 실행 되는 자동화된 reaper 작업은 전체 클러스터에 유용합니다. 여기에는 서비스에서 프로비전되지 않은 데이터 센터가 포함됩니다. 이 외에도 온-프레미스 또는 외부적으로 호스팅된 데이터 센터를 유지 관리하는 것은 사용자의 책임입니다.

## <a name="next-steps"></a>다음 단계

다음 빠른 시작을 진행하세요.
* [Azure Portal에서 관리형 인스턴스 만들기](create-cluster-portal.md)
* [Azure Databricks를 사용하여 관리형 Apache Spark 클러스터 배포](deploy-cluster-databricks.md)
* [Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 리소스 관리](manage-resources-cli.md)
