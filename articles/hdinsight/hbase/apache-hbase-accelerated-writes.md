---
title: Apache HBase에 대한 Azure HDInsight 가속 쓰기
description: Apache HBase Write Ahead Log의 성능을 향상시키기 위해 프리미엄 관리 디스크를 사용하는 Azure HDInsight Accelerated Writings 기능의 개요를 제공합니다.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/24/2020
ms.openlocfilehash: 76e2211eb146178d8e491237370147c3bfed7079
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070207"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Apache HBase에 대한 Azure HDInsight 가속 쓰기

이 문서에서는 Azure HDInsight의 Apache HBase에 대한 **가속 쓰기** 기능 및 쓰기 성능을 향상시키기 위해 효과적으로 사용할 수 있는 방법에 대한 배경 지식을 제공합니다. **가속 쓰기** 는 [Azure 프리미엄 SSD 관리 디스크](../../virtual-machines/disks-types.md#premium-ssds)를 사용하여 Apache HBase WAL(Write Ahead Log)의 성능을 개선합니다. Apache HBase에 대해 자세히 알아보려면 [HDInsight의 Apache HBase란?](apache-hbase-overview.md)을 참조하세요.

## <a name="overview-of-hbase-architecture"></a>HBase 아키텍처 개요

HBase에서 **행** 은 하나 이상의 **열** 로 구성되며 **행 키** 로 식별됩니다. 여러 행이 **테이블** 을 구성합니다. 열에는 해당 열에 있는 값의 타임스탬프 버전인 **셀** 이 포함됩니다. 열은 **열 패밀리** 로 그룹화되고, 열 패밀리의 모든 열은 **HFiles** 라는 스토리지 파일에 함께 저장됩니다.

HBase의 **지역** 은 데이터 처리 부하의 균형을 유지하는 데 사용됩니다. HBase는 먼저 단일 지역에 테이블의 행을 저장합니다. 행은 테이블의 데이터 양이 증가할수록 여러 지역에 걸쳐 분산됩니다. **지역 서버** 는 여러 지역에 대한 요청을 처리할 수 있습니다.

## <a name="write-ahead-log-for-apache-hbase"></a>Apache HBase의 Write Ahead Log

HBase는 먼저 데이터 업데이트를 WAL(Write Ahead Log)이라는 커밋 로그 형식에 기록합니다. 업데이트가 WAL에 저장된 후 메모리 내 **MemStore** 에 기록됩니다. 메모리의 데이터가 최대 용량에 도달하면 디스크에 **HFile** 로 기록됩니다.

MemStore를 플러시하는 동안에는 **RegionServer** 가 충돌하거나 사용할 수 없게 되면 WAL(Write Ahead Log)를 사용하여 업데이트를 재생할 수 있습니다. WAL을 사용하지 않을 경우 **HFile** 에 업데이트를 플러시하기 전에 **RegionServer** 가 충돌하면 해당 업데이트가 모두 손실됩니다.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Apache HBase용 Azure HDInsight의 가속 쓰기 기능

가속 쓰기 기능은 클라우드 스토리지에 있는 Write Ahead Log를 사용하여 발생하는 더 높은 쓰기 대기 시간 문제를 해결합니다.  HDInsight Apache HBase 클러스터의 가속 쓰기 기능은 모든 RegionServer(작업자 노드)에 프리미엄 SSD 관리 디스크를 연결합니다. 그런 다음, Write Ahead Log가 클라우드 스토리지 대신 이러한 프리미엄 관리 디스크에 탑재된 HDFS(Hadoop File System)에 기록됩니다.  프리미엄 관리 디스크는 SSD(Solid-State Disk)를 사용하고 내결함성을 갖춘 뛰어난 I/O 성능을 제공합니다.  비관리 디스크와 달리 하나의 스토리지 단위가 중단되더라도 동일한 가용성 집합의 다른 스토리지 단위에는 영향을 주지 않습니다.  따라서 관리 디스크는 애플리케이션의 쓰기 대기 시간을 단축시키고 복원력이 향상됩니다. Azure 관리 디스크에 대해 자세히 알아보려면 [Azure 관리 디스크 소개](../../virtual-machines/managed-disks-overview.md)를 참조하세요.

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>HDInsight에서 HBase에 대해 가속 쓰기를 사용하도록 설정하는 방법

가속 쓰기 기능을 사용하여 새 HBase 클러스터를 만들려면 [HDInsight에서 클러스터 설정](../hdinsight-hadoop-provision-linux-clusters.md)의 단계를 따릅니다. **기본** 탭에서 클러스터 유형을 HBase로 선택하고 구성 요소 버전을 지정한 후 **HBase 가속 쓰기를 사용하도록 설정** 옆의 확인란을 클릭합니다. 그런 다음 클러스터를 만들기 위한 나머지 단계를 계속 진행합니다.

:::image type="content" source="./media/apache-hbase-accelerated-writes/azure-portal-create-hbase-wals.png" alt-text="HDInsight Apache HBase에 대한 가속 쓰기 옵션 사용" border="true":::

## <a name="other-considerations"></a>기타 고려 사항

데이터 내구성을 유지하려면 최소 3개의 작업자 노드를 사용하여 클러스터를 만듭니다. 만든 후에는 클러스터를 3개 미만의 작업자 노드로 축소할 수 없습니다.

클러스터를 삭제하기 전에 HBase 테이블을 플러시하거나 사용하지 않도록 설정하여 Write Ahead Log 데이터를 잃지 않도록 합니다.

```
flush 'mytable'
```

```
disable 'mytable'
```

클러스터를 축소하는 경우 비슷한 단계를 수행합니다. 테이블을 플러시하고 들어오는 데이터를 중지하기 위해 테이블을 사용하지 않도록 설정합니다. 클러스터를 3개 미만의 노드로 축소할 수 없습니다.

이러한 단계를 수행하면 성공적으로 축소되고, 복제된 파일 또는 임시 파일로 인해 namenode가 안전 모드로 전환되지 않을 수 있습니다.

축소 후에 namenode가 안전 모드로 전환될 경우, hdfs 명령을 사용하여 복제된 블록 하에 다시 복제하고 hdfs를 안전 모드에서 벗어나게 합니다. 이 다시 복제를 통해 HBase를 성공적으로 다시 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Write Ahead Log 기능](https://hbase.apache.org/book.html#wal)의 공식 Apache HBase 설명서
* 가속 쓰기를 사용하도록 HDInsight Apache HBase 클러스터를 업그레이드하려면 [Apache HBase 클러스터를 새 버전으로 마이그레이션](apache-hbase-migrate-new-version.md)을 참조하세요.
