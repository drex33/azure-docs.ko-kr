---
title: 성능 조정 - Azure Data Lake Storage Gen1을 사용한 Spark
description: Spark on Azure HDInsight 및 Azure Data Lake Storage Gen1에 대한 성능 조정 지침에 대해 알아봅니다.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: normesta
ms.openlocfilehash: 13e6aedc6c54ae8a02f2f1d25524b5193f12f3a5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128680550"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen1"></a>HDInsight의 Spark 및 Azure Data Lake Storage Gen1에 대한 성능 조정 지침

Spark에서 성능을 조정할 때 클러스터에서 실행될 앱 수를 고려해야 합니다. 기본적으로 HDI 클러스터에서 4개의 앱을 동시에 실행할 수 있습니다(참고: 기본 설정은 변경될 수 있음). 사용할 앱 수를 줄여서 기본 설정을 재정의하고 해당 앱에 더 많은 클러스터를 사용하도록 할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure Data Lake Storage Gen1 계정**. 계정을 만드는 방법에 대한 지침은 [Azure Data Lake Storage Gen1 시작](data-lake-store-get-started-portal.md)을 참조하세요.
* Data Lake Storage Gen1 계정에 대한 액세스 권한이 있는 **Azure HDInsight 클러스터**. [Data Lake Storage Gen1을 사용하여 HDInsight 클러스터 만들기](data-lake-store-hdinsight-hadoop-use-portal.md)를 참조하세요. 클러스터에 대한 원격 데스크톱을 사용하도록 설정해야 합니다.
* **Data Lake Storage Gen1에서 Spark 클러스터 실행** 합니다. 자세한 내용은 [HDInsight Spark 클러스터를 사용하여 Data Lake Storage Gen1의 데이터 분석](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)을 참조하세요.
* **Data Lake Storage Gen1 성능 조정 지침**. 일반적인 성능 개념은 [Data Lake Storage Gen1 성능 조정 지침](./data-lake-store-performance-tuning-guidance.md)을 참조하세요. 

## <a name="parameters"></a>매개 변수

Spark 작업을 실행할 때 Data Lake Storage Gen1에서 성능을 향상시키기 위해 조정할 수 있는 가장 중요한 설정이 있습니다.

* **Num-executors** - 실행할 수 있는 동시 태스크 수입니다.

* **Executor-memory** - 각 실행기에 할당된 메모리 양입니다.

* **Executor-cores** - 각 실행기에 할당된 코어 수입니다.

**Num-executors** Num-executors는 병렬로 실행할 수 있는 최대 태스크 수를 설정합니다. 병렬로 실행할 수 있는 실제 태스크 수는 클러스터에서 사용 가능한 메모리 및 CPU 리소스로 제한됩니다.

**Executor-memory** 각 실행기에 할당되는 메모리 양입니다. 각 실행기에 필요한 메모리는 작업에 따라 다릅니다. 복잡한 작업의 경우 메모리가 더 많이 필요합니다. 읽기 및 쓰기와 같은 간단한 작업의 경우 메모리 요구 사항은 낮습니다. 각 실행기에 대한 메모리 양은 Ambari에서 볼 수 있습니다. Ambari에서 Spark로 이동하여 **Configs** 탭을 확인합니다.

**Executor-cores** 실행기당 사용된 코어 양을 설정하며 이 값에 따라 실행기당 실행할 수 있는 병렬 스레드 수가 결정됩니다. 예를 들어 executor-cores = 2인 경우 각 실행기는 실행기에서 2개의 병렬 태스크를 실행할 수 있습니다. 필요한 executor-cores는 작업에 따라 달라집니다. I/O가 많은 작업에는 태스크당 큰 메모리가 필요하지 않으므로 각 실행기에서 더 많은 병렬 태스크를 처리할 수 있습니다.

기본적으로 HDInsight에서 Spark를 실행할 때 각 물리적 코어에 대해 2개의 가상 YARN 코어가 정의됩니다. 이 값은 여러 스레드에서 동시성 및 컨텍스트 전환 횟수 간에 적절한 균형을 제공합니다.

## <a name="guidance"></a>지침

Data Lake Storage Gen1의 데이터로 작업하는 Spark 분석 워크로드를 실행하는 동안 Data Lake Storage Gen1의 성능을 극대화하려면 최신 HDInsight 버전을 사용하는 것이 좋습니다. 작업이 I/O 집약적인 경우 성능 개선을 위해 특정 매개 변수를 구성할 수 있습니다. Data Lake Storage Gen1은 높은 처리량을 처리할 수 있는 확장성 높은 스토리지 플랫폼입니다. 작업이 주로 읽기 또는 쓰기를 구성하는 경우 Data Lake Storage Gen1 간의 I/O에 대한 동시성이 증가하면 성능도 향상될 수 있습니다.

I/O 집약적인 작업에 대한 동시성을 높이는 몇 가지 일반적인 방법이 있습니다.

**1단계: 클러스터에서 실행되는 앱 수 결정** – 현재 앱을 포함하여 클러스터에서 실행되는 앱 수를 알아야 합니다. 각 Spark 설정에 대한 기본 값에서는 4개의 앱이 동시에 실행 중이라고 가정합니다. 따라서 앱당 클러스터의 25%만 사용할 수 있습니다. 더 나은 성능을 얻기 위해 실행기 수를 변경하여 기본값을 재정의할 수 있습니다.

**2단계: executor-memory 설정** – 가장 먼저 설정할 항목은 executor-memory입니다. 메모리는 실행할 작업에 따라 달라집니다. 실행기당 메모리 할당량을 줄여 동시성을 높일 수 있습니다. 작업을 실행할 때 메모리 부족 예외가 표시되면 이 매개 변수 값을 늘려야 합니다. 한 가지 대안은 메모리가 많은 클러스터를 사용하거나 클러스터 크기를 늘려 더 많은 메모리를 확보하는 것입니다. 메모리가 많아지면 더 많은 실행기가 사용되며 이것은 동시성 증가로 이어집니다.

**3단계: executor-cores 설정** – 복잡한 작업이 없는 I/O 집약적인 워크로드의 경우 실행기당 병렬 태스크 수를 늘리기 위해 executor-cores 수를 높게 시작하는 것이 좋습니다. executor-cores를 4로 설정하여 시작하는 것이 좋습니다.

```console
executor-cores = 4
```

executor-cores 수를 늘리면 더 많은 병렬 처리를 제공하므로 서로 다른 executor-cores로 실험할 수 있습니다. 더욱 복잡한 작업(operation)이 있는 작업(job)의 경우 실행기당 코어 수를 줄여야 합니다. executor-cores가 4보다 높게 설정된 경우 가비지 수집이 부족하여 성능이 저하될 수 있습니다.

**4단계: 클러스터에서 YARN 메모리 양 결정** – 이 정보는 Ambari에서 제공됩니다. YARN으로 이동하여 Contigs 탭을 확인합니다. 이 창에 YARN 메모리가 표시됩니다.
이 창에 있는 동안 기본 YARN 컨테이너 크기도 확인할 수 있습니다. YARN 컨테이너 크기는 실행기 매개 변수당 메모리와 같습니다.

총 YARN 메모리 = 노드 수 * 노드당 YARN 메모리

**5단계: num-executors 계산**

**메모리 제약 조건 계산** - num-executors 매개 변수는 메모리 또는 CPU에 의해 제한됩니다. 메모리 제약 조건은 애플리케이션에 사용 가능한 YARN 메모리 양에 따라 결정됩니다. 총 YARN 메모리를 가져와 executor-memory로 나눕니다. 앱 수에 맞게 제약 조건을 조정해야 하므로 앱 수로 나눕니다.

메모리 제약 조건 = (총 YARN 메모리 / 실행기 메모리)/ 앱 수

**CPU 제약 조건 계산** - CPU 제약 조건은 총 가상 코어 수를 실행기당 코어 수로 나누어 계산합니다. 각 물리적 코어당 2개의 가상 코어가 있습니다. 메모리 제약 조건과 마찬가지로 앱 수로 나눕니다.

가상 코어 = (클러스터의 노드 수 * 노드의 실제 코어 수 * 2) CPU 제약 조건 = (총 가상 코어 수/ 실행기당 코어 수)/ 앱 수

**num-executors 설정** – num-executors 매개 변수는 메모리 제약 조건 및 CPU 제약 조건 중 최소값을 사용하여 결정됩니다. 

num-executors = Min(총 가상 코어 수/실행기 당 코어 수, 사용 가능한 YARN memory / executor-memory) num-executors 수를 더 높게 설정해도 성능이 반드시 향상되는 것은 아닙니다. 더 많은 실행기를 추가하면 각 실행기당 오버헤드가 더 추가되며 이로 인해 성능이 저하될 수 있다는 것을 고려해야 합니다. Num-executors는 클러스터 리소스의 제한을 받습니다.

## <a name="example-calculation"></a>계산 예

실행 예정인 앱을 포함하여 현재 2개의 앱을 실행 중인 D4v2 노드 8개로 구성된 클러스터가 있다고 가정해 보겠습니다.

**1단계: 클러스터에서 실행되는 앱 수 결정** – 실행 예정인 앱을 포함하여 클러스터에 두 개의 앱이 있다는 것을 알고 있습니다.

**2단계: executor-memory 설정** – 이 예의 경우 I/O 집약적인 작업에 대해 6GB의 실행기 메모리면 충분하다고 결정합니다.

```console
executor-memory = 6GB
```

**3단계: executor-cores 설정** – I/O 집약적인 작업이므로 실행기당 코어 수를 4로 설정할 수 있습니다. 실행기당 코어 수를 4보다 높게 설정하면 가비지 수집 문제가 발생할 수 있습니다.

```console
executor-cores = 4
```

**4단계: 클러스터에서 YARN 메모리 양 결정** – Ambari로 이동하여 각 D4v2에 25GB의 YARN 메모리가 있는지 확인합니다. 8개의 노드가 있으므로 사용 가능한 YARN 메모리에 8을 곱합니다.

Total YARN 메모리 = 노드 수 * 노드당 YARN 메로리* 총 YARN 메모리 = 8개 노드 * 25GB = 200GB

**5단계: num-executors 계산** – num-executors 매개 변수는 메모리 제약 조건 및 CPU 제약 조건 중 최소값을 Spark에서 실행 중인 앱 수로 나누어 결정됩니다.

**메모리 제약 조건 계산** - 메모리 제약 조건은 총 YARN 메모리를 실행기당 메모리로 나누어 계산합니다.

메모리 제약 조건 = (총 YARN memory/executor memory) / 앱 수 메모리 제약 조건 = (200GB/6GB)/ 2 메모리 제약 조건 = 16(반올림) **CPU 제약 조건 계산** - CPU 제약 조건은 총 YARN 코어 수를 실행기당 코어 수로 나눈 값으로 계산됩니다.

YARN 코어 수 = 클러스터의 노드 수 * 노드당 코어 수 * 2개 YARN 코어 = 8개 노드 * D14당 8개 코어 * 2 = 128개 CPU 제약 조건 = (총 YARN 코어/실행기당 코어 수)/앱 수 CPU 제약 조건 = (128 / 4) / 2개 CPU 제약 조건 = 16

**num-executors 설정**

num-executors = Min(메모리 제약 조건, CPU 제약 조건) num-executors = Min(16, 16) num-executors = 16