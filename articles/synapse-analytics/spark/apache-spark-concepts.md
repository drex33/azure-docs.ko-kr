---
title: Apache Spark 핵심 개념
description: Azure Synapse Analytics의 Apache Spark 핵심 개념을 소개합니다.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 1ca0fd208ed739118708630a80b4829ce7519704
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715742"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Azure Synapse Analytics의 Apache Spark 핵심 개념

Apache Spark는 메모리 내 처리를 지원하여 빅 데이터 분석 애플리케이션의 성능을 향상하는 병렬 처리 프레임워크입니다. Azure Synapse Analytics의 Apache Spark는 Microsoft가 구현한 클라우드의 Apache Spark 중 하나입니다. 

Azure Synapse를 사용하면 Azure에서 Spark 기능을 쉽게 만들고 구성할 수 있습니다. Azure Synapse는 여기서 설명하는 Spark 기능을 다른 방법으로 구현합니다.

## <a name="spark-pools"></a>Spark 풀

서버리스 Apache Spark 풀은 Azure Portal에서 만듭니다. 이 풀은 인스턴스화하면 데이터를 처리하는 Spark 인스턴스를 만드는 데 사용되는 Spark 풀의 정의입니다. 생성된 Spark 풀은 메타데이터로만 존재하며, 따라서 리소스가 사용되거나, 실행되거나, 요금이 청구되지 않습니다. Spark 풀에는 Spark 인스턴스의 특성을 제어하는 일련의 속성이 있습니다. 이러한 특성은 이름, 크기, 크기 조정 동작, TTL(Time to live)을 포함하지만 이에 제한되지 않습니다.

Spark 풀을 만들 때 비용 또는 리소스가 들지 않으므로 원하는 만큼 다양한 구성을 사용하여 원하는 만큼 풀을 만들 수 있습니다. 또한 Spark 풀에 권한을 적용하여 사용자가 일부 풀에만 액세스하도록 설정할 수 있습니다.

가장 좋은 방법은 개발 및 디버깅에 사용할 소규모 Spark 풀을 만든 다음, 프로덕션 워크로드를 실행하기 위한 더 큰 Spark 풀을 만드는 것입니다.

[Azure Synapse Analytics에서 Spark 풀 시작](../quickstart-create-apache-spark-pool-portal.md)에서 Spark 풀을 만드는 방법과 모든 속성을 확인할 수 있습니다.

## <a name="spark-instances"></a>Spark 인스턴스

Spark 인스턴스는 Spark 풀에 연결하고, 세션을 만들고, 작업을 실행할 때 생성됩니다. 여러 사용자가 단일 Spark 풀에 액세스할 수 있기 때문에 연결하는 사용자마다 새 Spark 인스턴스가 만들어집니다. 

두 번째 작업을 제출하면 풀에 용량이 있는 경우 기존 Spark 인스턴스도 용량이 있습니다. 그런 다음, 기존 인스턴스가 작업을 처리합니다. 그렇지 않으면 풀 수준에서 용량을 사용할 수 있는 경우 새 Spark 인스턴스가 생성됩니다.

## <a name="examples"></a>예제

### <a name="example-1"></a>예 1

- 클러스터 크기가 노드 20개로 고정된 SP1이라는 Spark 풀을 만듭니다.
- 노드 10개를 사용하는 Notebook 작업 J1을 제출합니다. 그러면 이 작업을 처리하기 위해 SI1이 생성됩니다.
- 아직 풀에 용량이 있으므로 노드 10개를 사용하는 또 다른 작업 J2를 제출하면 인스턴스 J2가 SI1에서 처리됩니다.
- J2에서 11개 노드를 요청했다면 SP1 또는 SI1의 용량이 부족했을 것입니다. 이 경우 J2가 Notebook에서 온다면 작업이 거부되고, J2가 배치 작업에서 온다면 큐에 추가됩니다.

### <a name="example-2"></a>예제 2

- 노드 10–20개 사이에서 자동으로 크기가 조정되는 SP2라는 Spark 풀을 만듭니다.
- 노드 10개를 사용하는 Notebook 작업 J1을 제출합니다. 그러면 이 작업을 처리하기 위해 SI1이 생성됩니다.
- 아직 풀에 용량이 있으므로 노드 10개를 사용하는 또 다른 작업 J2를 제출하면 인스턴스가 자동으로 노드 20개로 확장되어 J2를 처리합니다.

### <a name="example-3"></a>예제 3

- 클러스터 크기가 노드 20개로 고정된 SP1이라는 Spark 풀을 만듭니다.
- 노드 10개를 사용하는 Notebook 작업 J1을 제출합니다. 그러면 이 작업을 처리하기 위해 SI1이 생성됩니다.
- 또 다른 사용자 U2가 노드 10개를 사용하는 작업 J3를 제출합니다. 그러면 이 작업을 처리하기 위해 새 Spark 인스턴스 SI2가 생성됩니다.
- 아직 풀에 용량이 있으므로 노드 10개를 사용하는 또 다른 작업 J2를 제출하면 인스턴스 J2가 SI1에서 처리됩니다.

## <a name="quotas-and-resource-constraints-in-apache-spark-for-azure-synapse"></a>Azure Synapse에 대한 Apache Spark의 할당량 및 리소스 제약 조건

### <a name="workspace-level"></a>작업 영역 수준

모든 Azure Synapse 작업 영역에는 Spark에 사용할 수 있는 vCore의 기본 할당량이 제공됩니다. 할당량은 사용자 할당량과 데이터 흐름 할당량 간에 분할되므로 사용 패턴이 작업 영역에서 모든 vCore를 사용하지 않습니다. 할당량은 구독 유형에 따라 다르지만 사용자와 데이터 흐름 사이에는 대칭적입니다. 그러나 작업 영역에서 남은 것보다 더 많은 vCore를 요청하면 다음과 같은 오류가 발생합니다.

```console
Failed to start session: [User] MAXIMUM_WORKSPACE_CAPACITY_EXCEEDED
Your Spark job requested 480 vcores.
However, the workspace only has xxx vcores available out of quota of yyy vcores.
Try reducing the numbers of vcores requested or increasing your vcore quota. Click here for more information - https://go.microsoft.com/fwlink/?linkid=213499
```

메시지의 링크는 이 문서를 가리킵니다.

다음 문서에서는 작업 영역 vCore 할당량 증가를 요청하는 방법을 설명합니다.

- 서비스 유형으로 "Azure Synapse Analytics"를 선택합니다.
- 할당량 정보 창에서 작업 영역당 Apache Spark(vCore)를 선택합니다.

[Azure Portal을 통해 용량 증가 요청](../../azure-portal/supportability/per-vm-quota-requests.md)

### <a name="spark-pool-level"></a>Spark 풀 수준

Spark 풀을 정의하는 경우 해당 풀에 대한 사용자당 할당량을 효과적으로 정의합니다. 여러 개의 Notebooks 또는 작업을 실행하거나 이 둘을 혼합하여 실행하면 풀 할당량이 소진될 수 있습니다. 이렇게 하면 다음과 같은 오류 메시지가 생성됩니다.

```console
Failed to start session: Your Spark job requested xx vcores.
However, the pool is consuming yy vcores out of available zz vcores.Try ending the running job(s) in the pool, reducing the numbers of vcores requested, increasing the pool maximum size or using another pool
```

이 문제를 해결하려면 Notebook 또는 작업을 실행하여 새 리소스 요청을 제출하기 전에 풀 리소스의 사용량을 줄여야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics](../index.yml)
- [Apache Spark 설명서](https://spark.apache.org/docs/2.4.5/)