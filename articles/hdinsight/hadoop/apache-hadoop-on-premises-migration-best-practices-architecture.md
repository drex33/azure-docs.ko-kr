---
title: '아키텍처: Azure HDInsight에 대한 온-프레미스 Apache Hadoop'
description: 온-프레미스 Hadoop 클러스터를 Azure HDInsight로 마이그레이션하는 아키텍처 모범 사례를 알아봅니다.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 91e5effe1cfb948329a784fed06bd2bce0694c3d
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284099"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>온-프레미스 Apache Hadoop 클러스터를 Azure HDInsight로 마이그레이션 - 아키텍처 모범 사례

이 문서에서는 Azure HDInsight 시스템의 아키텍처에 대한 권장 사항을 제공합니다. 온-프레미스 Apache Hadoop 시스템을 Azure HDInsight로 마이그레이션하는 데 도움을 주는 모범 사례를 제공하는 시리즈의 일부입니다.

## <a name="use-multiple-workload-optimized-clusters"></a>여러 개의 워크로드 최적화 클러스터 사용

많은 온-프레미스 Apache Hadoop 배포는 여러 워크로드를 지원하는 하나의 대규모 클러스터로 구성되어 있습니다. 이 단일 클러스터는 복잡할 수 있으며, 모든 요소가 함께 작동하도록 만들기 위해 개별 서비스를 절충해야 할 수 있습니다. 온-프레미스 Hadoop 클러스터를 Azure HDInsight로 마이그레이션하려면 접근 방법을 변경해야 합니다.

Azure HDInsight 클러스터는 특정 유형의 컴퓨팅 용도로 설계되었습니다. 여러 클러스터에서 스토리지를 공유할 수 있기 때문에 다양한 작업의 요구를 충족하기 위해 여러 개의 워크로드 최적화 컴퓨팅 클러스터를 만들 수 있습니다. 클러스터 유형마다 해당 특정 워크로드에 대한 최적 구성이 있습니다. 다음 표에는 HDInsight에서 지원되는 클러스터 유형 및 해당 워크로드가 나와 있습니다.

|작업|HDInsight 클러스터 유형|
|---|---|
|일괄 처리(ETL/ELT)|Hadoop, Spark|
|데이터 웨어하우징|Hadoop, Spark, 대화형 쿼리|
|IoT/스트리밍|Kafka, Storm, Spark|
|NoSQL 트랜잭션 처리|HBase|
|메모리 내 캐싱을 사용한 보다 신속한 대화형 쿼리|대화형 쿼리|
|데이터 과학| Spark|

다음 표는 HDInsight 클러스터를 만드는 데 사용할 수 있는 다양한 방법을 보여 줍니다.

|도구|브라우저 기반|명령줄|REST API|SDK|
|---|---|---|---|---|
|[Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Azure CLI(버전 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](/dotnet/api/overview/azure/hdinsight)||||X|
|[Python SDK](/python/api/overview/azure/hdinsight)||||X|
|[Java SDK](/java/api/overview/azure/hdinsight)||||X|
|[Azure 리소스 관리자 템플릿](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

자세한 내용은 [HDInsight의 클러스터 유형](../hadoop/apache-hadoop-introduction.md) 문서를 참조하세요.

## <a name="use-transient-on-demand-clusters"></a>임시 주문형 클러스터 사용

HDInsight 클러스터가 오랜 기간 동안 사용되지 않을 수 있습니다. 리소스 비용을 절약하기 위해 HDInsight는 워크로드가 성공적으로 완료되면 삭제할 수 있는 주문형 임시 클러스터를 지원합니다.

클러스터를 삭제할 때 관련된 스토리지 계정과 외부 메타데이터는 제거되지 않습니다. 나중에 동일한 스토리지 계정 및 metastore를 사용하여 클러스터를 다시 만들 수 있습니다.

Azure Data Factory를 사용하여 주문형 HDInsight 클러스터를 만들 일정을 예약할 수 있습니다. 자세한 내용은 [Azure Data Factory를 사용하여 HDInsight에서 주문형 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-adf.md) 문서를 참조하세요.

## <a name="decouple-storage-from-compute"></a>컴퓨팅에서 스토리지 분리

일반적으로 온-프레미스 Hadoop 배포에서는 데이터 스토리지 및 데이터 처리에 동일한 머신 집합을 사용합니다. 컴퓨팅과 스토리지가 공존하기 때문에 함께 스케일링해야 합니다.

HDInsight 클러스터에서 스토리지는 컴퓨팅과 공존할 필요가 없으며 Azure Storage, Azure Data Lake Storage 또는 둘 다에 있을 수 있습니다. 컴퓨팅에서 스토리지를 분리할 경우 다음과 같은 혜택이 있습니다.

- 여러 클러스터에서 데이터 공유
- 데이터가 클러스터에 종속되지 않으므로 임시 클러스터 사용
- 스토리지 비용 절감
- 별도로 스토리지 및 컴퓨팅 크기 조정
- 여러 지역에서 데이터 복제

컴퓨팅 클러스터는 Azure 지역에서 스토리지 계정 리소스에 가까운 곳에 생성되어 컴퓨팅 및 스토리지 분리로 인한 성능 저하를 완화합니다. 고속 네트워크를 사용하면 컴퓨팅 노드가 Azure Storage 내의 데이터에 효율적으로 액세스할 수 있습니다.

## <a name="use-external-metadata-stores"></a>외부 메타데이터 저장소 사용

HDInsight 클러스터에서 작동하는 두 가지 주요 메타스토어인 [Apache Hive](https://hive.apache.org/) 및 [Apache Oozie](https://oozie.apache.org/)가 있습니다. Hive 메타스토어는 Hadoop, Spark, LLAP, Presto 및 Apache Pig를 비롯한 데이터 처리 엔진에서 사용할 수 있는 중앙 스키마 리포지토리입니다. Oozie metastore는 진행 중인 Hadoop 작업과 완료된 Hadoop 작업의 일정 및 상태에 대한 세부 정보를 저장합니다.

HDInsight는 Hive 및 Oozie metastore에 Azure SQL Database를 사용합니다. HDInsight 클러스터에서 metastore를 설정하는 방법에는 다음 두 가지가 있습니다.

1. 기본 metastore

    - 추가 비용이 없습니다.
    - 클러스터를 삭제하면 metastore도 삭제됩니다.
    - 여러 클러스터에서 metastore를 공유할 수 없습니다.
    - 5DTU 한도가 있는 기본 Azure SQL DB를 사용합니다.

1. 사용자 지정 외부 metastore

    - 외부 Azure SQL Database를 metastore로 지정합니다.
    - Hive 스키마 Oozie 작업 세부 정보를 비롯한 메타데이터 손실 없이 클러스터를 만들고 삭제할 수 있습니다.
    - 여러 유형의 클러스터에서 단일 metastore DB를 공유할 수 있습니다.
    - 필요에 따라 metastore를 확장할 수 있습니다.
    - 자세한 내용은 [Azure HDInsight에서 외부 메타데이터 저장소 사용](../hdinsight-use-external-metadata-stores.md)을 참조하세요.

## <a name="best-practices-for-hive-metastore"></a>Hive 메타스토어의 모범 사례

몇 가지 HDInsight Hive 메타스토어 모범 사례는 다음과 같습니다.

- 사용자 지정 외부 metastore를 사용하여 컴퓨팅 리소스 및 메타데이터를 구분합니다.
- 50DTU 및 250GB 스토리지를 제공하는 S2 계층 Azure SQL 인스턴스로 시작합니다. 병목 상태가 발생하는 경우 데이터베이스를 확장할 수 있습니다.
- 하나의 HDInsight 클러스터 버전에 대해 생성된 metastore를 다른 버전의 클러스터와 공유하지 않습니다. 여러 가지 Hive 버전에서는 다양한 스키마를 사용합니다. 예를 들어, Hive 1.2 및 Hive 2.1 클러스터에서 metastore를 공유할 수 없습니다.
- 사용자 지정 metastore를 정기적으로 백업합니다.
- metastore와 HDInsight 클러스터를 동일한 영역에 유지합니다.
- Azure Portal 같은 Azure SQL Database 모니터링 도구나 Azure Monitor 로그를 사용하여 성능 및 가용성에 대한 메타스토어를 모니터링합니다.
- 필요에 따라 `ANALYZE TABLE` 명령을 실행하여 테이블 및 열에 대한 통계를 생성합니다. `ANALYZE TABLE [table_name] COMPUTE STATISTICS`)을 입력합니다.

## <a name="best-practices-for-different-workloads"></a>다양한 워크로드에 대한 모범 사례

- 대화형 Hive 쿼리에 LLAP 클러스터를 사용하여 응답 시간을 향상합니다. [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP)는 쿼리의 메모리 내 캐싱을 허용하는 Hive 2.0의 새로운 기능입니다. LLAP 덕분에 Hive 쿼리를 훨씬 빠르게, [일부 경우에는 Hive 1.x보다 26배 더 빠르게](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/) 수행합니다.
- Hive 작업 대신 Spark 작업을 사용합니다.
- Impala 기반 쿼리를 LLAP 쿼리로 바꿉니다.
- MapReduce 작업을 Spark 작업으로 바꿉니다.
- Spark 구조적 스트리밍 작업을 사용하여 대기 시간이 낮은 Spark 일괄 작업을 바꿉니다.
- 데이터 오케스트레이션에 ADF(Azure Data Factory) 2.0을 사용합니다.
- 클러스터 관리에 Ambari를 사용합니다.
- 스크립트 처리를 위해 온-프레미스 HDFS에서 WASB, ADLS 또는 ADFS로 데이터 스토리지를 변경합니다.
- Hive 테이블 및 감사에 Ranger RBAC를 사용합니다.
- MongoDB 또는 Cassandra 대신 CosmosDB를 사용합니다.

## <a name="next-steps"></a>다음 단계

이 시리즈의 다음 문서를 읽어보세요.

- [온-프레미스에서 Azure HDInsight Hadoop으로 마이그레이션하는 인프라 모범 사례](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)