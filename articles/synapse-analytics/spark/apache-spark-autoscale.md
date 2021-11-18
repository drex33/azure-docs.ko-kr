---
title: Apache Spark 인스턴스 자동 스케일링
description: Azure Synapse 자동 스케일링 기능을 사용하여 Apache Spark 인스턴스를 자동으로 스케일링
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 03/31/2020
ms.openlocfilehash: b988e4b776128efa0a10d72a3e4727cf8a93985b
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132759994"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>Azure Synapse Analytics Apache Spark 풀 크기 자동 조정

Azure Synapse Analytics용 Apache Spark 풀의 자동 크기 조정 기능은 클러스터 인스턴스의 노드 수를 자동으로 스케일 업 및 다운합니다. Azure Synapse Analytics 풀에 새 Apache Spark를 만드는 동안 자동 크기 조정을 선택하면 최소 및 최대 노드 수를 설정할 수 있습니다. 그런 다음, 자동 크기 조정 기능은 부하의 리소스 요구 사항을 모니터링하면서 노드 수를 늘리거나 줄입니다. 이 기능을 사용하기 위한 추가 요금은 없습니다.

## <a name="metrics-monitoring"></a>메트릭 모니터링

자동 크기 조정은 지속적으로 Spark 인스턴스를 모니터링하면서 다음 메트릭을 수집합니다.

|메트릭|Description|
|---|---|
|보류 중인 총 CPU|보류 중인 모든 작업의 실행을 시작하는 데 필요한 총 코어 수입니다.|
|보류 중인 총 메모리|보류 중인 모든 작업의 실행을 시작하는 데 필요한 총 메모리(MB)입니다.|
|사용 가능한 총 CPU|활성 노드에서 사용되지 않는 모든 코어의 합계입니다.|
|사용 가능한 총 메모리|활성 노드에서 사용되지 않는 메모리(MB)의 합계입니다.|
|노드당 사용되는 메모리|노드의 로드입니다. 메모리 사용량이 10GB인 노드는 메모리 사용량이 2GB인 작업자보다 부하가 많은 것으로 간주됩니다.|

위의 메트릭은 30초마다 확인됩니다. 자동 크기 조정은 이러한 메트릭을 기반으로 스케일 업 및 스케일 다운 의사 결정을 내립니다.

## <a name="load-based-scale-conditions"></a>부하 기반 크기 조정 조건

다음 조건이 감지되면 자동 크기 조정에서 크기 조정 요청을 실행합니다.

|강화|스케일 다운|
|---|---|
|보류 중인 총 CPU가 사용 가능한 총 CPU보다 큰 시간이 1분을 초과합니다.|보류 중인 총 CPU가 2분 이상 사용 가능한 총 CPU보다 작습니다.|
|보류 중인 총 메모리가 사용 가능한 총 메모리보다 큰 시간이 1분을 초과합니다.|보류 중인 총 메모리가 2분 이상 사용 가능한 총 메모리보다 작습니다.|

강화를 위해 Azure Synapse 자동 크기 조정 서비스는 현재 CPU 및 메모리 요구 사항을 충족하는 데 필요한 새 노드 수를 계산한 다음, 필요한 노드 수를 추가하기 위한 강화 요청을 실행합니다.

스케일 다운의 경우 실행기 수, 노드당 애플리케이션 마스터, 현재 CPU 및 메모리 요구 사항에 따라 자동 크기 조정은 특정 개수의 노드를 제거하라는 요청을 실행합니다. 또한 서비스는 현재 작업 실행을 기반으로 제거할 후보를 검색합니다. 스케일 다운 작업은 먼저 노드를 해제한 다음, 클러스터에서 제거합니다.

## <a name="get-started"></a>시작하기

### <a name="create-a-serverless-apache-spark-pool-with-autoscaling"></a>자동 스케일링을 사용하여 서버리스 Apache Spark 풀 생성

자동 크기 조정 기능을 사용하려면 일반 풀 생성 프로세스의 일부로 다음을 수행합니다.

1. **기본** 탭에서 **자동 크기 조정 사용** 확인란을 선택합니다.
1. 다음 속성에 대해 원하는 값을 입력합니다.  

    * **최소** 노드 수입니다.
    * **최대** 노드 수입니다.

초기 노드 수는 최소입니다. 이 값은 생성 시 인스턴스의 초기 크기를 정의합니다. 최소 노드 수는 3개보다 적을 수 없습니다.

필요에 따라 Spark 작업의 단계에서 실행기 요구 사항이 크게 다르거나 처리된 데이터 볼륨이 시간에 따라 변동하는 시나리오에서 실행기의 동적 할당을 사용하도록 설정할 수 있습니다. 실행기의 동적 할당을 사용하도록 설정하면 필요에 따라 용량을 활용할 수 있습니다.

동적 할당을 사용하도록 설정하면 지정된 최소 및 최대 실행기 수 내에서 실행기 수를 조정하도록 작업이 허용됩니다.

Apache Spark 아래와 같이 코드를 통해 실행기의 동적 할당을 구성할 수 있습니다.

```
    %%configure -f
    {
        "conf" : {
            "spark.dynamicAllocation.maxExecutors" : "6",
            "spark.dynamicAllocation.enable": "true",
            "spark.dynamicAllocation.minExecutors": "2"
     }
    }
```
코드를 통해 지정된 기본값은 사용자 인터페이스를 통해 설정된 값을 재정의합니다.

동적 할당을 사용하도록 설정할 때 실행기는 실행기의 사용률에 따라 확장 또는 축소합니다. 이렇게 하면 실행기가 실행 중인 작업의 요구 사항에 따라 프로비전됩니다.

## <a name="best-practices"></a>모범 사례

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>스케일 업 또는 스케일 다운 작업의 대기 시간 고려

크기 조정 작업을 완료하는 데 1 ~ 5분 정도 걸릴 수 있습니다.

### <a name="prepare-for-scaling-down"></a>스케일 다운 준비

인스턴스 스케일 다운을 처리하는 동안 자동 스케일링 기능은 노드를 서비스 해제 상태로 전환하므로 해당 노드에서 새 실행기를 시작할 수 없습니다.

실행 중인 작업이 계속 실행되고 완료됩니다. 보류 중인 작업은 사용 가능한 노드가 더 적은 상태로 정상적으로 예약될 때까지 대기합니다.

## <a name="next-steps"></a>다음 단계

새 Spark 풀을 설정하기 위한 빠른 시작 [Spark 풀 만들기](../quickstart-create-apache-spark-pool-portal.md)
