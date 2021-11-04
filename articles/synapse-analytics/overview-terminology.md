---
title: 용어 - Azure Synapse Analytics
description: 사용자에게 Azure Synapse Analytics를 안내하는 참조 가이드
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/02/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5d525d3657a48e01240721e4c868fa481db58dc0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074708"
---
# <a name="azure-synapse-analytics-terminology"></a>Azure Synapse Analytics 용어

이 문서에서는 Azure Synapse Analytics의 기본 개념을 안내합니다.

## <a name="basics"></a>기본

**Synapse 작업 영역** 은 Azure에서 클라우드 기반 엔터프라이즈 분석을 수행할 수 있는 안전한 협업 경계입니다. 작업 영역은 특정 지역에 배포되며 임시 데이터를 저장하기 위한 ADLS Gen2 계정 및 파일 시스템과 연결됩니다. 작업 영역은 리소스 그룹에 있습니다.

작업 영역에서는 SQL 및 Apache spark를 사용하여 분석을 수행할 수 있습니다. SQL 및 Spark 분석에 사용할 수 있는 리소스는 SQL 및 Spark **풀** 로 구성됩니다.

## <a name="linked-services"></a>연결된 서비스

작업 영역에는 여러 **연결 서비스**, 기본적으로 작업 영역이 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열이 포함될 수 있습니다.

## <a name="synapse-sql"></a>Synapse SQL

**Synapse SQL** 은 Synapse 작업 영역에서 T-SQL 기반 분석을 수행할 수 있습니다. Synapse SQL에는 전용 및 서버리스 두 개의 소비 모델이 있습니다.  전용 모델의 경우 **전용 SQL 풀** 을 사용합니다. 작업 영역에는 이러한 풀이 얼마든지 있을 수 있습니다. 서버리스 모델을 사용하려면 **서버리스 SQL 풀** 을 사용합니다. 모든 작업 영역에는 다음 풀 중 하나가 있습니다.

Synapse Studio 내에서 **SQL 스크립트** 를 실행하여 SQL 풀을 사용할 수 있습니다.

## <a name="apache-spark-for-synapse"></a>Synapse용 Apache Spark

Spark 분석을 사용하려면 Synapse 작업 영역에서 **서버리스 Apache Spark 풀** 을 만들고 사용합니다. Spark 풀 사용을 시작하면 작업 영역에서 **Spark 세션** 을 만들어 해당 세션과 관련된 리소스를 처리합니다.

Synapse에는 Spark를 사용하는 두 가지 방법이 있습니다.

* 데이터 과학 및 엔지니어링 데이터를 위한 **Spark Notebooks** 는 Scala, PySpark, C# 및 SparkSQL을 사용합니다.
* jar 파일을 사용하여 일괄 처리 Spark 작업을 실행하기 위한 **Spark 작업 정의**.

## <a name="pipelines"></a>Pipelines

파이프라인은 Azure Synapse에서 데이터 통합 기능을 제공하는 방식으로, 서비스 간에 데이터를 이동하고 작업을 오케스트레이션할 수 있습니다.

* **파이프라인** 은 작업을 함께 수행하는 활동의 논리적 그룹화입니다.
* **활동** 은 데이터 복사, Notebook 또는 SQL 스크립트 실행과 같은 데이터에 대해 수행할 작업을 파이프라인 내에서 정의합니다.
* **데이터 흐름** 은 내부적으로 Synapse Spark를 사용하는 데이터 변환을 수행하기 위한 코드 없는 환경을 제공하는 특정 유형의 작업입니다.
* **트리거** - 파이프라인을 실행합니다. 수동 또는 자동으로 실행 가능(일정, 연속 창 또는 이벤트 기반)
* **통합 데이터 세트** - 작업에서 입력 및 출력으로 사용할 데이터를 단순히 가리키거나 참조하는 데이터의 명명된 보기입니다. 연결된 서비스에 속합니다.

## <a name="data-explorer-preview"></a>Data Explorer(미리 보기)

Azure Synapse Data Explorer는 로그 및 원격 분석 데이터를 통해 인사이트를 확보할 수 있는 대화형 쿼리 환경을 고객에게 제공합니다.

* **Data Explorer 풀** 은 최적화된 쿼리 성능을 위한 로컬 SSD 스토리지(핫 캐시)와 지속성을 위한 다중 Blob Storage(콜드 캐시)가 있는 둘 이상의 컴퓨팅 노드를 포함하는 전용 클러스터입니다.
* **Data Explorer 데이터베이스** 는 Data Explorer 풀에서 호스트되며, 테이블 및 기타 데이터베이스 개체의 컬렉션으로 구성된 논리적 엔터티입니다. 풀당 데이터베이스가 두 개 이상 있을 수 있습니다.
* **테이블** 은 기존의 관계형 데이터 모델을 사용하여 구성된 데이터를 포함하는 데이터베이스 개체입니다. 데이터는 정렬된 열 목록을 정의하는 Data Explorer의 잘 정의된 테이블 스키마를 준수하는 레코드에 저장되며, 각 열에는 이름 및 스칼라 데이터 형식이 있습니다. 스칼라 데이터 형식은 구조화(*int*, *real*, *datetime* 또는 *timespan*) 또는 반구조화(*동적*) 형식이거나, 자유 텍스트(*문자열*)일 수 있습니다. 동적 형식은 단일 스칼라 값, 배열 또는 이러한 값의 사전을 보유할 수 있다는 점에서 JSON과 비슷합니다.
* **외부 테이블** 은 Data Explorer 데이터베이스 외부의 스토리지 또는 SQL 데이터 원본을 참조하는 테이블입니다. 테이블과 마찬가지로 외부 테이블에는 잘 정의된 스키마(열 이름 및 데이터 형식 쌍의 순서가 지정된 목록)가 있습니다. 데이터가 Data Explorer 풀로 수집되는 Data Explorer 테이블과 달리, 외부 테이블은 풀 외부에서 저장되고 관리되는 데이터에서 작동합니다. 외부 테이블은 데이터를 유지하지 않으며 데이터를 쿼리하거나 외부 데이터 저장소로 내보내는 데 사용됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Synapse Analytics 시작](get-started.md)
* [작업 영역 만들기](quickstart-create-workspace.md)
* [서버리스 SQL 풀 사용](quickstart-sql-on-demand.md)
* [Synapse Studio를 사용하여 Data Explorer 풀 만들기](data-explorer/data-explorer-create-pool-studio.md)
