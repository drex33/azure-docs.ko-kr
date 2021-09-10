---
title: Apache Spark 풀 개념
description: Azure Synapse Analytics의 Apache Spark 풀 크기 및 구성에 대한 소개입니다.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 08/19/2021
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: c674813aee7702e9887e909e4a8baf7ace074a2c
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597648"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 Apache Spark 풀 구성

Spark 풀은 Spark 인스턴스가 인스턴스화될 때 컴퓨팅 리소스 요구 사항 및 관련 동작 특성을 정의하는 메타데이터 집합입니다. 해당 특성은 이름, 노드 수, 노드 크기, 스케일링 동작, TTL(Time to live)을 포함하지만 이에 제한되지 않습니다. Spark 풀 자체는 리소스를 사용하지 않습니다. Spark 풀을 만들 때 발생하는 비용은 없습니다. 요금은 필요에 따라 대상 Spark 풀에서 Spark 작업이 실행되고 Spark 인스턴스가 인스턴스화되는 경우에만 발생합니다.

[Synapse Analytics에서 Spark 풀 시작](../quickstart-create-apache-spark-pool-portal.md)에서 Spark 풀을 만드는 방법과 모든 속성을 확인할 수 있습니다.

## <a name="isolated-compute"></a>격리된 컴퓨팅

격리된 컴퓨팅 옵션은 물리적 컴퓨팅 리소스를 단일 고객에게만 제공하여 신뢰할 수 없는 서비스에서 제공되는 Spark 컴퓨팅 리소스에 대한 추가 보안을 제공합니다.
격리된 컴퓨팅 옵션은 규정 준수 및 규정 요구 사항 충족 등의 이유로 다른 고객의 워크로드로부터 높은 수준의 격리가 필요한 워크로드에 가장 적합합니다.  
격리된 컴퓨팅 옵션은 XXXLarge(80 vCPU/504GB) 노드 크기에서만 사용할 수 있으며 다음 지역에서만 사용할 수 있습니다.  인스턴스를 다시 시작해야 할 수도 있지만 풀을 만든 후에 격리된 컴퓨팅 옵션을 사용하거나 사용하지 않도록 설정할 수 있습니다.  나중에 이 기능을 사용하도록 설정하려는 경우 Synapse 작업 영역이 격리된 컴퓨팅 지원 지역에 만들어졌는지 확인합니다.

* 미국 동부
* 미국 서부 2
* 미국 중남부
* US Gov 애리조나
* US Gov 버지니아

## <a name="nodes"></a>노드

Apache Spark 풀 인스턴스는 Spark 인스턴스에서 최소 세 개 이상의 노드를 포함하는 하나의 헤드 노드와 둘 이상의 작업자 노드로 구성됩니다.  헤드 노드는 Livy, Yarn Resource Manager, Zookeeper, Spark 드라이버 등의 추가 관리 서비스를 실행합니다.  모든 노드는 노드 에이전트 및 Yarn Node Manager와 같은 서비스를 실행합니다. 모든 작업자 노드는 Spark Executor 서비스를 실행합니다.

## <a name="node-sizes"></a>노드 크기

Spark 풀은 8개 vCore와 64GB 메모리를 갖춘 Small 컴퓨팅 노드에서 64개 vCore와 노드당 432GB 메모리를 갖춘 XXLarge 컴퓨팅에 이르는 노드 크기로 정의할 수 있습니다. 인스턴스를 다시 시작해야 할 수도 있지만, 풀을 만든 후 노드 크기를 변경할 수 있습니다.

|크기 | vCore | 메모리|
|-----|------|-------|
|소|4|32GB|
|중간|8|64GB|
|대|16|128GB|
|XLarge|32|256GB|
|XXLarge|64|432GB|
|XXX Large(격리된 컴퓨팅)|80|504GB|

## <a name="autoscale"></a>자동 크기 조정

Apache Spark 풀은 작업량에 따라 컴퓨팅 리소스를 자동으로 스케일링하는 기능을 제공합니다.  자동 스케일링 기능을 사용하는 경우 스케일링할 노드의 최소 개수 및 최대 개수를 설정할 수 있습니다.
자동 스케일링 기능을 사용하지 않도록 설정하면 설정된 노드 수가 고정된 상태로 유지됩니다.  이 설정은 인스턴스를 다시 시작해야 할 수도 있지만, 풀을 만든 후에 변경할 수 있습니다.

## <a name="automatic-pause"></a>자동 일시 중지

자동 일시 중지 기능은 설정된 유휴 시간이 지나면 리소스 할당을 해제하여 Apache Spark 풀의 총비용을 줄입니다.  이 기능을 사용하도록 설정하면 유휴 시간(분)을 설정할 수 있습니다.  자동 일시 중지 기능은 자동 스케일링 기능과는 독립적입니다. 자동 스케일링을 사용하거나 사용하지 않도록 설정했는지 여부에 관계없이 리소스를 일시 중지할 수 있습니다.  이 설정은 인스턴스를 다시 시작해야 할 수도 있지만, 풀을 만든 후에 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Synapse Analytics](../index.yml)
* [Apache Spark 설명서](https://spark.apache.org/docs/2.4.5/)