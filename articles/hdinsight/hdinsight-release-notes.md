---
title: Azure HDInsight 릴리스 정보
description: Azure HDInsight에 대한 최신 릴리스 정보입니다. Hadoop, Spark, Hive 등에 대한 개발 팁과 세부 정보를 확인하세요.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: cf726033a90e256a2d41a3249e237f5eb91aee84
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535941"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

## <a name="summary"></a>요약

Azure HDInsight는 Azure에서 오픈 소스 분석을 위해 기업 고객들 사이에서 가장 인기 있는 서비스 중 하나입니다.

릴리스 정보를 구독하려면 [이 GitHub 리포지토리](https://github.com/hdinsight/release-notes/releases)에서 릴리스를 확인하세요.

## <a name="release-date-07272021"></a>릴리스 날짜: 2021/07/27

이 릴리스는 HDInsight 3.6 및 4.0 모두에 적용됩니다. HDInsight 릴리스는 며칠 동안의 준비 작업을 거쳐 모든 지역에서 사용할 수 있게 됩니다. 여기에 나오는 릴리스 날짜는 첫 번째 지역 릴리스 날짜를 나타냅니다. 아래의 변경 내용이 표시되지 않으면 며칠 후에 해당 지역에서 릴리스가 라이브될 때까지 기다려주세요.

이 릴리스의 OS 버전은 다음과 같습니다.
- HDInsight 3.6: Ubuntu 16.04.7 LTS
- HDInsight 4.0: Ubuntu 18.04.5 LTS

## <a name="new-features"></a>새로운 기능
### <a name="new-azure-monitor-integration-experience-preview"></a>새 Azure Monitor 통합 환경(미리 보기)
새 Azure Monitor 통합 환경은 이번 릴리스와 함께 미국 동부와 서유럽에서 미리 보기로 제공됩니다. 새 Azure Monitor 환경에 대한 자세한 내용은 [여기](./log-analytics-migration.md#migrate-to-the-new-azure-monitor-integration)를 참조하세요.

## <a name="deprecation"></a>사용 중단
### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>2021년 7월 1일부터 HDInsight 3.6 기본 지원
2021년 7월 1일부터 Microsoft는 특정 HDInsight 3.6 클러스터 유형에 대한 [기본 지원](hdinsight-component-versioning.md#support-options-for-hdinsight-versions)을 제공합니다. 기본 지원 플랜은 2022년 4월 3일까지 이용할 수 있습니다. 2021년 7월 1일부터 기본 지원에 자동으로 등록됩니다. 옵트인하기 위해 수행할 작업은 없습니다. 기본 지원 아래에 클러스터 유형이 포함된 [설명서](hdinsight-36-component-versioning.md)를 참조하세요. 

기존 3.6 환경에 관한 변경 내용을 중지하고 HDInsight 3.6에 새로운 솔루션을 빌드하지 않는 것이 좋습니다. [클러스터를 HDInsight 4.0으로 마이그레이션](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40)하는 것이 좋습니다. [HDInsight 4.0의 새로운 기능](hdinsight-version-release.md#whats-new-in-hdinsight-40)에 관해 자세히 알아보세요.

## <a name="behavior-changes"></a>동작 변경
### <a name="hdinsight-interactive-query-only-supports-schedule-based-autoscale"></a>HDInsight Interactive Query는 일정 기반 자동 스케일링만 지원합니다.
고객 시나리오가 점점 더 확장되고 다양해짐에 따라 Interactive Query(LLAP) 부하 기반 자동 스케일링에 대한 몇 가지 제한 사항을 확인했습니다. 이 제한 사항은 LLAP 쿼리 역학의 특징, 향후 부하 예측 정확도 문제 및 LLAP 스케줄러의 작업 재배포 문제 때문에 발생합니다. 이 제한 사항으로 인해 사용자는 자동 스케일링이 사용되는 경우 LLAP 클러스터에서 쿼리 실행 속도가 느려지는 것을 알 수 있습니다. 성능에 미치는 영향이 자동 스케일링의 비용 이점보다 클 수 있습니다.

2021년 7월부터 HDInsight의 Interactive Query 워크로드는 일정 기반 자동 스케일링만 지원합니다. 새 Interactive Query 클러스터에서는 더 이상 부하 기반 자동 스케일링을 사용하도록 설정할 수 없습니다. 기존에 실행 중인 클러스터는 위에서 설명된 알려진 제한 사항을 적용하여 계속 실행할 수 있습니다. 

LLAP를 위해 일정 기반 자동 스케일링으로 이동하는 것이 좋습니다.  Grafana Hive 대시보드를 통해 클러스터의 현재 사용 패턴을 분석할 수 있습니다. 자세한 내용은 [Azure HDInsight 클러스터 자동 크기 조정](hdinsight-autoscale-clusters.md)을 참조하세요. 

## <a name="upcoming-changes"></a>예정된 변경
이후 릴리스에서는 다음과 같은 변경이 수행됩니다.

### <a name="built-in-llap-component-in-esp-spark-cluster-will-be-removed"></a>ESP Spark 클러스터의 기본 제공 LLAP 구성 요소가 제거됩니다.
HDInsight 4.0 ESP Spark 클러스터에는 두 헤드 노드에서 모두 실행되는 기본 제공 LLAP 구성 요소가 있습니다. ESP Spark 클러스터의 LLAP 구성 요소는 원래 HDInsight 3.6 ESP Spark용으로 추가되었지만 HDInsight 4.0 ESP Spark용 실제 사용자 사례는 없습니다. 2021년 9월로 예정된 다음 릴리스에서 HDInsight는 HDInsight 4.0 ESP Spark 클러스터에서 기본 제공 LLAP 구성 요소를 제거합니다. 이렇게 변경하면 헤드 노드 워크로드를 오프로드하고 ESP Spark와 ESP Interactive Hive 클러스터 유형 간의 혼동을 방지하는 데 도움이 됩니다.

## <a name="new-region"></a>새 지역
- 미국 서부 3
- JIO 인도 서부
- 오스트레일리아 중부

## <a name="component-version-change"></a>구성 요소 버전 변경
이 릴리스에서 변경된 구성 요소 버전은 다음과 같습니다.
- ORC 버전 1.5.1에서 1.5.9로 변경

[이 문서](./hdinsight-component-versioning.md)에서 HDInsight 4.0 및 HDInsight 3.6의 최신 구성 요소 버전을 찾을 수 있습니다.

## <a name="back-ported-jiras"></a>백포팅된 JIRA
이 릴리스를 위해 백포팅된 Apache JIRA는 다음과 같습니다.

| 영향을 받는 기능    |   Apache JIRA                                                      |
|---------------------|--------------------------------------------------------------------|
| 날짜/타임스탬프    | [HIVE-25104](https://issues.apache.org/jira/browse/HIVE-25104)     |
|                     | [HIVE-24074](https://issues.apache.org/jira/browse/HIVE-24074)     |
|                     | [HIVE-22840](https://issues.apache.org/jira/browse/HIVE-22840)     |
|                     | [HIVE-22589](https://issues.apache.org/jira/browse/HIVE-22589)     |
|                     | [HIVE-22405](https://issues.apache.org/jira/browse/HIVE-22405)     |
|                     | [HIVE-21729](https://issues.apache.org/jira/browse/HIVE-21729)     |
|                     | [HIVE-21291](https://issues.apache.org/jira/browse/HIVE-21291)     |
|                     | [HIVE-21290](https://issues.apache.org/jira/browse/HIVE-21290)     |
| UDF                 | [HIVE-25268](https://issues.apache.org/jira/browse/HIVE-25268)     |
|                     | [HIVE-25093](https://issues.apache.org/jira/browse/HIVE-25093)     |
|                     | [HIVE-22099](https://issues.apache.org/jira/browse/HIVE-22099)     |
|                     | [HIVE-24113](https://issues.apache.org/jira/browse/HIVE-24113)     |
|                     | [HIVE-22170](https://issues.apache.org/jira/browse/HIVE-22170)     |
|                     | [HIVE-22331](https://issues.apache.org/jira/browse/HIVE-22331)     |
| ORC                 | [HIVE-21991](https://issues.apache.org/jira/browse/HIVE-21991)     |
|                     | [HIVE-21815](https://issues.apache.org/jira/browse/HIVE-21815)     |
|                     | [HIVE-21862](https://issues.apache.org/jira/browse/HIVE-21862)     |
| 테이블 스키마        | [HIVE-20437](https://issues.apache.org/jira/browse/HIVE-20437)     |
|                     | [HIVE-22941](https://issues.apache.org/jira/browse/HIVE-22941)     |
|                     | [HIVE-21784](https://issues.apache.org/jira/browse/HIVE-21784)     |
|                     | [HIVE-21714](https://issues.apache.org/jira/browse/HIVE-21714)     |
|                     | [HIVE-18702](https://issues.apache.org/jira/browse/HIVE-18702)     |
|                     | [HIVE-21799](https://issues.apache.org/jira/browse/HIVE-21799)     |
|                     | [HIVE-21296](https://issues.apache.org/jira/browse/HIVE-21296)     |
| 워크로드 관리 | [HIVE-24201](https://issues.apache.org/jira/browse/HIVE-24201)     |
| 압축          | [HIVE-24882](https://issues.apache.org/jira/browse/HIVE-24882)     |
|                     | [HIVE-23058](https://issues.apache.org/jira/browse/HIVE-23058)     |
|                     | [HIVE-23046](https://issues.apache.org/jira/browse/HIVE-23046)     |
| 구체화된 뷰   | [HIVE-22566](https://issues.apache.org/jira/browse/HIVE-22566)     |