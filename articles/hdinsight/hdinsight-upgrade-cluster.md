---
title: 클러스터를 최신 버전으로 마이그레이션
titleSuffix: Azure HDInsight
description: Azure HDInsight 클러스터를 최신 버전으로 마이그레이션하기 위한 지침을 알아봅니다.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: 6fa655092de1c3e103381ccd58bf840b70a3809e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249764"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>HDInsight 클러스터를 최신 버전으로 마이그레이션

최신 HDInsight 기능을 활용하려면 HDInsight 클러스터를 정기적으로 최신 버전으로 마이그레이션하는 것이 좋습니다. HDInsight는 기존 클러스터가 최신 구성 요소 버전으로 업그레이드되는 현재 위치 업그레이드를 지원하지 않습니다. 원하는 구성 요소 및 플랫폼 버전으로 새 클러스터를 만든 다음 새 클러스터를 사용하도록 애플리케이션을 마이그레이션해야 합니다. 아래 지침에 따라 HDInsight 클러스터 버전을 마이그레이션합니다.

> [!NOTE]  
> 지원되는 HDInsight 버전에 대한 자세한 내용은 [HDInsight 구성 요소 버전](hdinsight-component-versioning.md#supported-hdinsight-versions)을 참조하세요.

## <a name="migration-tasks"></a>마이그레이션 작업

HDInsight 클러스터를 업그레이드하는 워크플로는 다음과 같습니다.
:::image type="content" source="./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png" alt-text="HDInsight 업그레이드 워크플로 다이어그램" border="false":::

1. HDInsight 클러스터를 업그레이드할 때 필요할 수 있는 변경 내용을 이해하려면 이 문서의 각 섹션을 읽어보세요.
2. 클러스터를 테스트/품질 보증 환경으로 만듭니다. 클러스터를 만드는 방법에 대한 자세한 내용은 [Linux 기반 HDInsight 클러스터를 만드는 방법 알아보기](hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.
3. 기존 작업, 데이터 원본 및 싱크를 새 환경으로 복사합니다.
4. 작업이 새 클러스터에서 예상대로 작동하는지 확인하려면 유효성 검사 테스트를 수행합니다.

예상대로 작동하는 것이 확인되면 마이그레이션을 위해 가동 중지 시간을 예약합니다. 이 가동 중지 시간 동안 다음 작업을 수행합니다.

1. 클러스터 노드에 로컬로 저장된 모든 임시 데이터를 백업합니다. 예를 들어 헤드 노드에 직접 저장된 데이터가 있는 경우입니다.
1. [기존 클러스터를 삭제합니다](./hdinsight-delete-cluster.md).
1. 이전 클러스터에서 사용된 것과 동일한 기본 데이터 저장소를 사용하여 최신(또는 지원되는) HDI 버전과 동일한 VNET 서브넷에서 클러스터를 만듭니다. 이렇게 하면 새 클러스터에서 기존의 프로덕션 데이터에 대해 작업을 계속할 수 있습니다.
1. 백업한 모든 임시 데이터를 가져옵니다.
1. 새 클러스터를 사용하여 작업을 시작하거나 계속 처리합니다.

## <a name="workload-specific-guidance"></a>워크로드 관련 지침

다음 문서에서는 특정 워크로드를 마이그레이션하는 방법에 대한 지침을 제공합니다.

* [HBase 마이그레이션](./hbase/apache-hbase-migrate-new-version.md)
* [Kafka 마이그레이션](./kafka/migrate-versions.md)
* [Hive/Interactive Query 마이그레이션](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>백업 및 복원

데이터베이스 백업 및 복원에 대한 자세한 내용은 [자동화된 데이터베이스 백업을 사용하여 Azure SQL Database에서 데이터베이스 복구](../azure-sql/database/recovery-using-backups.md)를 참조하세요.

## <a name="upgrade-scenarios"></a>업그레이드 시나리오

위에서 설명한 대로 새로운 기능과 수정 사항을 활용하기 위해 HDInsight 클러스터를 정기적으로 최신 버전으로 마이그레이션하는 것이 좋습니다. 클러스터를 삭제하고 다시 배포할 것을 요청하는 다음 이유 목록을 참조하세요.

* 클러스터 버전이 [사용 중지됨](hdinsight-retired-versions.md) 또는 [기본 지원](hdinsight-36-component-versioning.md) 최신 버전으로 해결될 클러스터 문제가 있습니다.
* 클러스터 문제의 근본 원인은 크기가 작은 VM과 관련된 것으로 확인됩니다. [Microsoft의 권장 노드 구성을 봅니다.](hdinsight-supported-node-configuration.md#all-supported-regions-except-brazil-south-and-japan-west)
* 고객이 지원 사례를 열고 Microsoft 엔지니어링 팀에서 최신 클러스터 버전에서 문제가 이미 해결되었다고 확인합니다.
* 기본 메타스토어 데이터베이스(Ambari, Hive, Oozie, Ranger)가 사용률 제한에 도달했습니다. Microsoft는 [사용자 지정 metastore](hdinsight-use-external-metadata-stores.md#custom-metastore) 데이터베이스를 사용하여 클러스터를 다시 만들도록 요청합니다.
* 클러스터 문제의 근본 원인은 **지원되지 않는 작업** 때문입니다. 다음은 지원되지 않는 몇 가지 일반적인 작업입니다.
     * **Ambari에서 서비스 이동 또는 추가** Ambari의 클러스터 서비스에 대한 정보를 볼 때 서비스 작업 메뉴에서 사용할 수 있는 작업 중 하나는 **이동 [서비스 이름]** 입니다. 또 다른 작업은 **[서비스 이름] 추가입니다.** 이러한 옵션은 모두 지원되지 않습니다.
     * **Python 패키지가 손상되었습니다.** HDInsight 클러스터는 기본 제공 Python 환경, Python 2.7 및 Python 3.5에 따라 달라집니다. 이러한 기본 제공 환경에서 사용자 지정 패키지를 직접 설치하면 예기치 않은 라이브러리 버전 변경이 발생할 수 있으며 클러스터가 중단됩니다. Spark 애플리케이션에 [대한 사용자 지정 외부 Python 패키지를 안전하게 설치하는](./spark/apache-spark-python-package-installation.md#safely-install-external-python-packages) 방법을 알아봅니다.
     * **타사 소프트웨어.** 고객은 HDInsight 클러스터에 타사 소프트웨어를 설치할 수 있습니다. 그러나 기존 기능을 중단하는 경우 클러스터를 다시 만들 것을 권장합니다.
     * **동일한 클러스터에 여러 워크로드가** 있습니다. HDInsight 4.0에서 Hive Warehouse Connector Spark 및 Interactive Query 워크로드에 대한 별도의 클러스터가 필요합니다. [다음 단계에 따라 Azure HDInsight 두 클러스터를 모두 설정합니다.](interactive-query/apache-hive-warehouse-connector.md) 마찬가지로 [Spark를 HBASE와](hdinsight-using-spark-query-hbase.md) 통합하려면 2개의 클러스터가 필요합니다.
     * **사용자 지정 Ambari DB 암호가 변경되었습니다.** Ambari DB 암호는 클러스터를 만드는 동안 설정되며 업데이트할 현재 메커니즘이 없습니다. 고객이 [사용자 지정 Ambari DB](hdinsight-custom-ambari-db.md)를 사용하여 클러스터를 배포하는 경우 SQL DB에서 DB 암호를 변경할 수 있습니다. 그러나 실행 중인 HDInsight 클러스터에 대해 이 암호를 업데이트할 수 있는 방법은 없습니다.

## <a name="next-steps"></a>다음 단계

* [Linux 기반 HDInsight 클러스터를 만드는 방법 알아보기](hdinsight-hadoop-provision-linux-clusters.md)
* [SSH를 사용하여 HDInsight에 연결](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Apache Ambari를 사용하여 Linux 기반 클러스터 관리](hdinsight-hadoop-manage-ambari.md)
* [HDInsight 릴리스 정보](./hdinsight-version-release.md)
