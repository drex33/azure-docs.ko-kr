---
title: Apache Hadoop 구성 요소 및 버전 - Azure HDInsight 4.0
description: Azure HDInsight 4.0의 Apache Hadoop 구성 요소 및 버전에 대해 자세히 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: f5e298235abadd5dab2f3e4283f201707c48d672
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236921"
---
# <a name="hdinsight-40-component-versions"></a>HDInsight 4.0 구성 요소 버전

이 문서에서는 Azure HDInsight 4.0의 Apache Hadoop 환경 구성 요소 및 버전에 대해 알아봅니다.

## <a name="apache-components-available-with-hdinsight-version-40"></a>HDInsight 버전 4.0에서 제공되는 Apache 구성 요소

HDInsight 4.0과 연결된 OSS 구성 요소 버전은 다음 표에 나열되어 있습니다.

| 구성 요소              | HDInsight 4.0 |
|------------------------|---------------|
| Apache Hadoop 및 YARN | 3.1.1         |
| Apache Tez             | 0.9.1         |
| Apache Pig             | 0.16.1        |
| Apache Hive            | 3.1.0         |
| Apache Ranger          | 1.1.0         |
| Apache HBase           | 2.1.6         |
| Apache Sqoop           | 1.5.0         |
| Apache Oozie           | 4.3.1         |
| Apache Zookeeper       | 3.4.6         |
| Apache Phoenix         | 5             |
| Apache Spark           | 2.4.4, 3.0.0(미리 보기)|
| Apache Livy            | 0.5           |
| Apache Kafka           | 2.1.1, 2.4.1(미리 보기)        |
| Apache Ambari          | 2.7.0         |
| Apache Zeppelin        | 0.8.0         |


이 표에는 사용이 중지된 특정 HDInsight 4.0 클러스터 형식이 나와 있습니다.

| 클러스터 유형                    | 프레임워크 버전 | 지원 만료 날짜      | 사용 중지 날짜 |
|---------------------------------|-------------------|------------------------------|-----------------|
| HDInsight 4.0 Spark             | 2.3               | 2020년 6월 30일                | 2020년 6월 30일   |
| HDInsight 4.0 Kafka             | 1.1               | 2020년 12월 31일                 | 2020년 12월 31일    |

## <a name="next-steps"></a>다음 단계

- [HDInsight의 Apache Hadoop, Spark 등에 대한 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise Security Package](./enterprise-security-package.md)
- [Windows PC의 HDInsight에서 Apache Hadoop 작업](hdinsight-hadoop-windows-tools.md)
