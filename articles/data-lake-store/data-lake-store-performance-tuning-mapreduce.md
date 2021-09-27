---
title: Azure Data Lake Storage Gen1 성능 조정 - MapReduce
description: 매개 변수, 지침, 예제 계산 및 제한 사항을 포함하여 Azure Data Lake Storage Gen1에서 MapReduce의 성능 조정에 대해 알아봅니다.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: normesta
ms.openlocfilehash: cbe7c9b8ac535d6f69ac6043524d1e720b9a96fc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128660959"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>HDInsight의 MapReduce 및 Azure Data Lake Storage Gen1에 대한 성능 조정 지침

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure Data Lake Storage Gen1 계정**. 계정을 만드는 방법에 대한 지침은 [Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)을 참조하세요.
* Data Lake Storage Gen1 계정에 대한 액세스 권한이 있는 **Azure HDInsight 클러스터**. [Data Lake Storage Gen1을 사용하여 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요. 클러스터에 대한 원격 데스크톱을 사용하도록 설정해야 합니다.
* **HDInsight에서 MapReduce 사용**. 자세한 내용은 [HDInsight에서 Hadoop과 MapReduce 사용](../hdinsight/hadoop/hdinsight-use-mapreduce.md)을 참조하세요.
* **Data Lake Storage Gen1 성능 조정 지침을 검토** 합니다. 일반적인 성능 개념은 [Data Lake Storage Gen1 성능 조정 지침](./data-lake-store-performance-tuning-guidance.md)을 참조하세요.

## <a name="parameters"></a>매개 변수

MapReduce 작업을 실행할 때 Data Lake Storage Gen1에서 성능을 향상시키기 위해 구성할 수 있는 가장 중요한 매개 변수가 있습니다.

|매개 변수      | Description  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  각 매퍼에 할당할 메모리 양입니다.  |
|`Mapreduce.job.maps`     |  작업당 맵 태스크 수입니다.  |
|`Mapreduce.reduce.memory.mb`     |  각 Reducer에 할당할 메모리 양입니다.  |
|`Mapreduce.job.reduces`    |   작업당 reduce 태스크 수입니다.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>Mapreduce.map.memory / Mapreduce.reduce.memory

맵 및/또는 reduce 작업에 필요한 메모리 양에 따라 이 숫자를 조정합니다. Yarn 구성을 통해 Ambari에서 `mapreduce.map.memory` 및 `mapreduce.reduce.memory`의 기본값을 볼 수 있습니다. Ambari에서 YARN으로 이동하여 **Configs** 탭을 봅니다. YARN 메모리가 표시됩니다.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>Mapreduce.job.maps / Mapreduce.job.reduces

이는 생성할 매퍼 또는 Reducer의 최대 수를 결정합니다. 분할 수에 따라 MapReduce 작업에 대해 생성될 매퍼 수가 결정됩니다. 따라서 분할 수가 요청한 매퍼 수보다 적으면 요청한 것보다 적은 수의 매퍼를 얻을 수 있습니다.

## <a name="guidance"></a>지침

### <a name="step-1-determine-number-of-jobs-running"></a>1단계: 실행되는 작업 수 결정

기본적으로 MapReduce는 전체 클러스터를 작업에 사용합니다. 사용 가능한 컨테이너보다 적은 수의 매퍼를 사용하면 클러스터를 덜 사용할 수 있습니다. 이 문서의 내용에서는 애플리케이션이 클러스터에서 실행 중인 유일한 애플리케이션이라고 가정합니다.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>2단계: mapreduce.map.memory/mapreduce.reduce.memory 설정

맵 및 리듀스 태스크에 대한 메모리 크기는 특정 작업에 따라 달라집니다. 동시성을 늘리려면 메모리 크기를 줄일 수 있습니다. 동시에 실행 중인 태스크 수는 컨테이너 수에 따라 달라집니다. 매퍼 또는 리듀서당 메모리 양을 줄이면 더 많은 컨테이너를 생성할 수 있으며 따라서 더 많은 매퍼 또는 리듀서를 동시에 실행할 수 있습니다. 메모리 양을 너무 많이 줄이면 일부 프로세스에서 메모리 부족이 발생할 수 있습니다. 작업을 실행할 때 힙 오류가 발생하면 매퍼 또는 Reducer당 메모리를 늘립니다. 더 많은 컨테이너를 추가하면 각 추가 컨테이너당 오버헤드가 더 추가되며 이로 인해 성능이 저하될 수 있다는 것을 고려하세요. 다른 대안은 메모리가 많은 클러스터를 사용하거나 클러스터의 노드 수를 늘려 더 많은 메모리를 확보하는 것입니다. 메모리가 많아지면 더 많은 컨테이너가 사용되며 이것은 동시성 증가로 이어집니다.

### <a name="step-3-determine-total-yarn-memory"></a>3단계: 총 YARN 메모리 결정

mapreduce.job.maps/mapreduce.job.reduces를 튜닝하려면 사용 가능한 YARN 메모리 양을 고려합니다. 이 정보는 Ambari에 제공됩니다. YARN으로 이동하여 **Configs** 탭을 확인합니다. 이 창에 YARN 메모리가 표시됩니다. YARN 메모리에 클러스터에 있는 노드 수를 곱하여 총 YARN 메모리를 얻습니다.

`Total YARN memory = nodes * YARN memory per node`

비어 있는 클러스터를 사용 중인 경우 이 메모리가 클러스터의 총 YARN 메모리일 수 있습니다. 다른 애플리케이션에서 메모리를 사용하고 있으면 매퍼 또는 리듀서 수를 사용하려는 컨테이너 수로 줄여서 클러스터 메모리 중에서 일부만 사용하도록 선택할 수 있습니다.

### <a name="step-4-calculate-number-of-yarn-containers"></a>4단계: YARN 컨테이너 수 계산

YARN 컨테이너는 작업에 사용 가능한 동시성의 양을 결정합니다. 총 YARN 메모리를 가져와 mapreduce.map.memory로 나눕니다.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>5단계: mapreduce.job.maps/mapreduce.job.reduces 설정

mapreduce.job.maps/mapreduce.job.reduces를 사용 가능한 컨테이너 수 이상의 값으로 설정합니다. 매퍼 및 리듀서 수를 늘리면 더 나은 성능을 얻을 수 있는지 실험해볼 수 있습니다. 매퍼 수가 늘어나면 추가 오버헤드가 발생하므로 매퍼 수가 너무 많으면 성능이 저하될 수 있음에 유의해야 합니다.

기본적으로 CPU 예약 및 CPU 격리는 해제되어 있으므로 YARN 컨테이너 수가 메모리로 제한됩니다.

## <a name="example-calculation"></a>계산 예제

현재 D14 노드 8개로 구성된 클러스터가 있고 I/O 집약적인 작업을 실행하려고 한다고 가정해 보겠습니다. 수행할 계산은 다음과 같습니다.

### <a name="step-1-determine-number-of-jobs-running"></a>1단계: 실행되는 작업 수 결정

이 예제에서는 작업이 실행 중인 유일한 작업이라고 가정합니다.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>2단계: mapreduce.map.memory/mapreduce.reduce.memory 설정

이 예제에서는 I/O 집약적인 작업을 실행하고 맵 작업에 3GB의 메모리가 충분한지를 결정합니다.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>3단계: 총 YARN 메모리 결정

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>4단계: YARN 컨테이너 수 결정

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>5단계: mapreduce.job.maps/mapreduce.job.reduces 설정

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>제한 사항

**Data Lake Storage Gen1 제한**

다중 테넌트 서비스로서 Data Lake Storage Gen1은 계정 수준 대역폭 제한을 설정합니다. 이러한 제한에 도달하면 태스크 실패가 표시되기 시작합니다. 이것은 태스크 로그에서 제한 오류를 확인하여 파악할 수 있습니다. 작업에 대한 대역폭이 더 필요한 경우 문의하세요.

제한 여부를 확인하려면 클라이언트 쪽에서 디버그 로깅을 사용하도록 설정해야 합니다. 그 방법은 다음과 같습니다.

1. Ambari > YARN > Config > Advanced yarn-log4j에서 log4j 속성에 다음 속성을 배치합니다. log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. 구성을 적용하려면 노드/서비스를 다시 시작합니다.

3. 제한이 적용되면 YARN 로그 파일에 HTTP 429 오류 코드가 표시됩니다. YARN 로그 파일은 /tmp/&lt;user&gt;/yarn.log에 있습니다.

## <a name="examples-to-run"></a>실행 예제

Data Lake Storage Gen1에서 MapReduce가 실행되는 방식을 보여 주기 위해 다음 설정을 통해 클러스터에서 실행된 샘플 코드는 다음과 같습니다.

* 16 노드 D14v2
* HDI 3.6을 실행하는 Hadoop 클러스터

다음은 출발점으로 MapReduce Teragen, Terasort 및 Teravalidate를 실행할 몇 가지 명령 예입니다. 리소스에 따라 이러한 명령을 조정할 수 있습니다.

### <a name="teragen"></a>Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```