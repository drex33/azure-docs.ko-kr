---
title: Azure Data Lake Analytics를 Azure Synapse Analytics로 마이그레이션합니다.
description: 이 문서에서는 Azure Data Lake Analytics에서 Azure Synapse Analytics로 마이그레이션하는 방법을 설명합니다.
author: lingluo0531
ms.author: lingluo
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: migrate-azure-data-lake-analytics-to-synapse
ms.date: 08/25/2021
ms.openlocfilehash: 34a199fe8f488add4f1f2cdd9357dd9ac718d4f0
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254973"
---
# <a name="migrate-azure-data-lake-analytics-to-azure-synapse-analytics"></a>Azure Data Lake Analytics를 Azure Synapse Analytics로 마이그레이션

Microsoft는 고유한 빅 데이터 분석 경험을 위해 데이터 레이크와 데이터 웨어하우스를 모두 통합하는 것을 목표로 하는 Azure Synapse Analytics를 출시했습니다. 고객이 모든 다양한 데이터를 수집 및 분석하여 데이터 비효율성을 해결하고 함께 작업하는 데 도움이 됩니다. 또한 Azure Machine Learning 및 Power BI와의 Synapses 통합을 통해 조직이 데이터에서 인사이트를 얻고 모든 스마트 앱에 대해 기계 학습을 실행할 수 있는 능력이 향상됩니다. 

이 문서에서는 Azure Data Lake Analytics에서 Azure Synapse Analytics로 마이그레이션하는 방법을 보여 줍니다. 

## <a name="recommended-approach"></a>권장 접근 방식
- 1단계: 준비 상태 평가
- 2단계: 마이그레이션 준비
- 3단계: 데이터 및 애플리케이션 워크로드 마이그레이션
- 4단계: Azure Data Lake Analytics에서 Azure Synapse Analytics로 전환

### <a name="step-1-assess-readiness"></a>1단계: 준비 상태 평가

1. [Azure Synapse Analytics의 Apache Spark](../synapse-analytics/spark/apache-spark-overview.md)를 살펴보고 Azure Data Lake Analytics와 Azure Synapse Analytics의 Spark의 주요 차이점을 이해합니다. 

    |항목 | Azure 데이터 레이크 분석 | Synapse의 Spark |
    | --- | --- |--- |
    | 가격 책정  |분석 단위 시간당 |vCore 시간당|
    |엔진     |Azure 데이터 레이크 분석  |Apache Spark
    |기본 프로그래밍 언어    |U-SQL   |T-SQL, Python, Scala, Spark SQL 및 .NET
    |솔루션 탐색기   |Azure Data Lake Storage    |Azure Blob Storage, Azure Data Lake Storage

2. <a href="#questionnaire">마이그레이션 평가를 위한 설문지</a>를 검토하고 고려해야 할 발생할 수 있는 위험을 나열합니다. 

### <a name="step-2-prepare-to-migrate"></a>2단계: 마이그레이션 준비

1.  마이그레이션할 작업 및 데이터를 식별합니다.
    -   이 기회를 통해 더 이상 사용하지 않는 해당 작업을 정리합니다. 작업을 한 번에 모두 마이그레이션하지 않으려는 경우 이 기회를 통해 단계별로 마이그레이션할 수 있는 작업의 논리적 그룹을 식별합니다.
    -   데이터의 크기를 평가하고 Apache Spark 데이터 형식을 이해합니다. U-SQL 스크립트를 검토하고 스크립트 재작성 노력을 평가하고 Apache Spark 코드 개념을 이해합니다.

2.  마이그레이션이 비즈니스에 미치는 영향을 확인합니다. 예를 들어 마이그레이션이 이루어지는 동안 가동 중지 시간을 감당할 수 있는지 여부입니다.

3.  마이그레이션 계획을 만듭니다.

### <a name="step-3-migrate-data-and-application-workload"></a>3단계: 데이터 및 애플리케이션 워크로드 마이그레이션

1.  데이터를 Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 마이그레이션합니다. <br></br>
    Azure Data Lake Storage Gen1의 사용 중지는 2024년 2월부터입니다. [공식 발표](https://azure.microsoft.com/updates/action-required-switch-to-azure-data-lake-storage-gen2-by-29-february-2024/)를 참조하세요. 먼저 데이터를 Gen2로 마이그레이션하는 것이 좋습니다. [Azure Data Lake Analytics U-SQL 개발자를 위한 Apache Spark 데이터 형식 이해](understand-spark-data-formats.md)를 참조하고 파일과 U-SQL 테이블에 저장된 데이터를 모두 이동하여 Azure Synapse Analytics에 액세스할 수 있도록 합니다.  마이그레이션 가이드에 대한 자세한 내용은 [여기](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)에서 확인할 수 있습니다. 

2.  U-SQL 스크립트를 Spark로 변환합니다. 
    U-SQL 스크립트를 Spark로 변환하려면 [Azure Data Lake Analytics U-SQL 개발자를 위한 Apache Spark 코드 개념 이해](understand-spark-code-concepts.md)를 참조하세요. 

3.  작업 오케스트레이션 파이프라인을 새로운 Spark 프로그램으로 변환하거나 다시 만듭니다.

### <a name="step-4-cut-over-from-azure-data-lake-analytics-to-azure-synapse-analytics"></a>4단계: Azure Data Lake Analytics에서 Azure Synapse Analytics로 컷오버

애플리케이션과 워크로드가 안정적이라고 확신하면 Azure Synapse Analytics를 사용하여 비즈니스 시나리오를 충족할 수 있습니다. Azure Data Lake Analytics에서 실행 중인 나머지 파이프라인을 끄고 Azure Data Lake Analytics 계정을 서비스 해제합니다.

<a name="questionnaire"></a>
## <a name="questionnaire-for-migration-assessment"></a>마이그레이션 평가를 위한 설문지 

|범주   |질문  |참조|
| --- | --- |--- |
|마이그레이션 규모 평가 |얼마나 많은 Azure Data Lake Analytics 계정이 있나요? 얼마나 많은 파이프라인을 사용하고 있나요? 얼마나 많은 U-SQL 스크립트를 사용하고 있나요?| 마이그레이션할 데이터와 스크립트가 많을수록, 스크립트에서 더 많은 UDO/UDF를 사용할수록 마이그레이션이 더 어려워집니다. 마이그레이션에 필요한 시간과 리소스는 프로젝트 규모에 따라 잘 계획되어야 합니다.|
|데이터 원본 |데이터 원본 크기는 얼마나 되나요? 처리할 데이터 형식의 종류는 무엇인가요? |[Azure Data Lake Analytics U-SQL 개발자를 위한 Apache Spark 데이터 서식 이해](understand-spark-data-formats.md)|
|데이터 출력 |나중에 사용할 수 있도록 출력 데이터를 보관할 것인가요? 출력 데이터가 U-SQL 테이블에 저장되어 있다면 어떻게 처리해야 할까요? | 출력 데이터를 자주 사용하고 U-SQL 테이블에 저장하려면 스크립트를 변경하고 출력 데이터를 Spark 지원 데이터 형식으로 변경해야 합니다.|
|데이터 마이그레이션 |스토리지 마이그레이션 계획을 만들었나요? |[Gen1에서 Gen2로 Azure Data Lake Storage 마이그레이션](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md) |
|U-SQL 스크립트 변환|UDO/UDF(.NET, python 등)를 사용하나요? 사용하는 경우, UDO/UDF에서 어떤 언어를 사용하나요? 변환 도중 변환에 문제가 있나요? 연합 쿼리가 U-SQL에서 사용되고 있나요?|[Azure Data Lake Analytics U-SQL 개발자를 위한 Apache Spark 코드 개념 이해](understand-spark-code-concepts.md)|

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics](../synapse-analytics/get-started.md)
