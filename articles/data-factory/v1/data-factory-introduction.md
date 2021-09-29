---
title: Data Factory, 데이터 통합 서비스 소개
description: 데이터의 이동과 변환을 오케스트레이션하고 자동화하는 클라우드 데이터 통합 서비스인 Azure Data Factory에 대해 알아봅니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: overview
ms.date: 01/22/2018
ms.openlocfilehash: 78ed8c774bc44c7a2756019287cd4364db1fe4d4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644508"
---
# <a name="introduction-to-azure-data-factory"></a>Azure Data Factory 소개 
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](data-factory-introduction.md)
> * [버전 2(현재 버전)](../introduction.md)

> [!NOTE]
> 이 문서의 내용은 Azure Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우 [Data Factory V2 소개](../introduction.md)를 참조하세요.


## <a name="what-is-azure-data-factory"></a>Azure 데이터 팩터리란 무엇인가요?
빅 데이터의 세계에서 기존 데이터는 어떻게 비즈니스에 활용될까요? 온-프레미스 데이터 소스 또는 기타 다양한 데이터 소스의 참조 데이터를 사용하여 클라우드에서 생성된 데이터를 보강할 수 있을까요? 

예를 들어 게임 회사는 클라우드에서 게임에 의해 생성되는 로그를 수집합니다. 회사에서는 고객 선호, 인구 통계, 사용량 동작 등에 대한 인사이트를 얻기 위해 이러한 로그를 분석하려고 합니다. 또한 회사는 상향 판매 및 교차 판매 기회를 식별하고, 매력적인 새로운 기능을 개발하고, 비즈니스 성장을 이끌고 고객에게 더 나은 환경을 제공하려고 합니다. 

이러한 로그를 분석하려면 회사는 온-프레미스 데이터 저장소에 있는 고객 정보, 게임 정보 및 마케팅 캠페인 정보와 같은 참조 데이터를 사용해야 합니다. 따라서 회사는 클라우드 데이터 저장소에서 로그 데이터를 수집하고 온-프레미스 데이터 저장소에서 데이터를 참조하려고 합니다. 

그런 다음 클라우드(Azure HDInsight)에서 Hadoop을 사용하여 데이터를 처리하려고 합니다. Azure Synapse Analytics와 같은 클라우드 데이터 웨어하우스 또는 SQL Server와 같은 온-프레미스 데이터 저장소에 결과 데이터를 게시하려고 합니다. 회사는 이 워크플로를 일주일에 한 번 실행하려고 합니다. 

회사는 온-프레미스와 클라우드 데이터 저장소 모두에서 데이터를 수집할 수 있는 워크플로를 만들 수 있는 플랫폼이 필요합니다. 또한 회사는 Hadoop과 같은 기존 컴퓨팅 서비스를 사용하여 데이터를 변환 또는 처리하고 결과를 사용할 BI 애플리케이션용 온-프레미스 또는 클라우드 데이터 저장소에 게시할 수 있어야 합니다. 

:::image type="content" source="media/data-factory-introduction/what-is-azure-data-factory.png" alt-text="데이터 팩터리 개요"::: 

Azure Data Factory는 이러한 종류의 시나리오에 대한 플랫폼입니다. *데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위해 클라우드에서 데이터 기반 워크플로를 만들 수 있는 클라우드 기반 데이터 통합 서비스* 입니다. Azure Data Factory를 사용하여 다음 작업을 수행할 수 있습니다. 

- 서로 다른 데이터 저장소의 데이터를 수집할 수 있는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 예약합니다.

- Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics 및 Azure Machine Learning과 같은 컴퓨팅 서비스를 사용하여 데이터를 처리하고 변환합니다.

-  사용할 BI(비즈니스 인텔리전스) 애플리케이션용 Azure Synapse Analytics와 같은 데이터 저장소에 출력 데이터를 게시합니다.  

기존의 ETL(추출 및 변환 및 로드) 플랫폼이 아닌 EL(추출 및 로드) 및 TL(변환 및 로드) 플랫폼이 지지를 얻고 있습니다. 변환은 파생된 열을 추가하고, 행의 수를 계산하고, 데이터를 정렬하는 등 대신 컴퓨팅 서비스를 사용하여 데이터를 처리합니다. 

현재 Azure Data Factory에서 워크플로가 사용하고 생성하는 데이터는 *시간 조각화 데이터*(매시간, 매일, 매주 등)입니다. 예를 들어, 파이프라인은 하루에 한 번 입력 데이터를 읽고, 데이터를 처리하고 출력 데이터를 생성할 수 있습니다. 또한 워크플로를 한 번만 실행할 수 있습니다.  
  

## <a name="how-does-it-work"></a>작동 원리 
Azure Data Factory의 파이프라인(데이터 기반 워크플로)는 일반적으로 다음 세 단계를 수행합니다.

:::image type="content" source="media/data-factory-introduction/three-information-production-stages.png" alt-text="Azure Data Factory의 세 단계":::

### <a name="connect-and-collect"></a>연결 및 수집
기업에는 다양한 형식의 데이터가 서로 다른 원본에 있습니다. 정보 프로덕션 시스템을 구축하는 첫 번째 단계는 데이터 및 처리의 모든 필요한 원본에 연결하는 것입니다. 이러한 원본은 SaaS 서비스, 파일 공유, FTP 및 웹 서비스를 포함합니다. 그런 다음 후속 처리를 위해 중앙 집중화된 위치에 필요한 데이터를 이동합니다.

데이터 팩터리가 없으면 기업은 사용자 지정 데이터 이동 구성 요소를 구축하거나 사용자 지정 서비스를 작성하여 이러한 데이터 원본과 처리를 통합해야 합니다. 이러한 시스템을 통합하고 유지 관리하는 것은 비용이 많이 들고 어렵습니다. 이러한 시스템은 완전히 관리되는 서비스에서 제공할 수 있는 엔터프라이즈급 모니터링, 경고 및 컨트롤이 종종 부족합니다.

Data Factory가 있으면 향후 분석에 사용할 수 있도록 데이터 파이프라인에서 복사 작업을 사용하여 온-프레미스 및 클라우드 원본 데이터 저장소에서 클라우드의 중앙 데이터 저장소로 데이터를 이동할 수 있습니다. 

예를 들어, Azure Data Lake Store에서 데이터를 수집하고 나중에 Azure Data Lake Analytics 컴퓨팅 서비스를 사용하여 데이터를 변환할 수 있습니다. 또는 Azure HDInsight Hadoop 클러스터를 사용하여 Azure Blob Storage에서 데이터를 수집하고 나중에 변환합니다.

### <a name="transform-and-enrich"></a>변환 및 보강
데이터가 클라우드에서 중앙 집중화된 데이터 저장소에 표시되면 HDInsight Hadoop, Spark, Data Lake Analytics 또는 Machine Learning과 같은 컴퓨팅 서비스를 사용하여 이를 처리하거나 변환합니다. 이제 유지 관리할 수 있고 제어된 일정에 따라 변환된 데이터를 안정적으로 생성하여 프로덕션 환경에 신뢰할 수 있는 데이터를 공급하려고 합니다. 

### <a name="publish"></a>게시 
클라우드에서 SQL Server와 같은 온-프레미스 원본에 변환된 데이터를 제공합니다. 또는 이를 BI, 분석 도구 및 기타 애플리케이션에 의한 소비를 위해 클라우드 스토리지 원본에 유지합니다.

## <a name="key-components"></a>핵심 구성 요소
Azure 구독에는 하나 이상의 Azure Data Factory 인스턴스(또는 Data Factory)가 있을 수 있습니다. Azure Data Factory는 네 가지 핵심 구성 요소로 구성됩니다. 이러한 구성 요소는 함께 작동하여 데이터를 이동하고 변환하는 단계를 사용하여 데이터 기반 워크플로를 작성할 수 있는 플랫폼을 제공합니다. 

### <a name="pipeline"></a>파이프라인
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인은 활동 그룹입니다. 파이프라인의 활동이 모여 작업을 수행합니다. 

예를 들어, Azure Blob에서 데이터를 수집한 다음 HDInsight 클러스터에서 Hive 쿼리를 실행하여 데이터를 분할하는 작업 그룹이 파이프라인에 포함될 수 있습니다. 이 방식의 장점은 파이프라인을 통해 개별 활동 단위가 아닌 하나의 집합으로써 활동을 관리할 수 있다는 점입니다. 예를 들어, 개별적 활동을 예약하는 대신 파이프라인을 배포하고 예약할 수 있습니다. 

### <a name="activity"></a>작업
파이프라인에는 하나 이상의 작업이 있을 수 있습니다. 활동은 데이터에 수행할 작업을 정의합니다. 예를 들어, 하나의 데이터 저장소에서 다른 데이터 저장소로 데이터를 복사하는 데 복사 작업을 사용할 수 있습니다. 마찬가지로, Hive 작업을 사용할 수 있습니다. Hive 작업은 데이터를 변환하거나 분석하기 위해 Azure HDInsight 클러스터에서 Hive 쿼리를 실행합니다. Data Factory는 데이터 이동 활동 및 데이터 변환 활동이라는 두 종류의 활동을 지원합니다.

### <a name="data-movement-activities"></a>데이터 이동 활동
데이터 팩터리의 복사 활동은 원본 데이터 저장소의 데이터를 싱크 데이터 저장소로 복사합니다. 모든 소스의 데이터를 모든 싱크에 쓸 수 있습니다. 데이터 저장소를 선택하면 해당 저장소에서/저장소로 데이터를 복사하는 방법을 확인할 수 있습니다. 데이터 팩터리는 다음과 같은 데이터 저장소를 지원합니다.

[!INCLUDE [data-factory-supported-data-stores](includes/data-factory-supported-data-stores.md)]

자세한 내용은 [복사 작업을 사용하여 데이터 이동](data-factory-data-movement-activities.md)을 참조하세요.

### <a name="data-transformation-activities"></a>데이터 변환 활동
[!INCLUDE [data-factory-transformation-activities](includes/data-factory-transformation-activities.md)]

자세한 내용은 [복사 작업을 사용하여 데이터 이동](data-factory-data-transformation-activities.md)을 참조하세요.

### <a name="custom-net-activities"></a>사용자 지정 .NET 활동
복사 작업에서 지원하지 않는 데이터 저장소에서/로 데이터를 이동해야 하거나 사용자의 고유한 논리를 사용하여 데이터를 변환해야 하는 경우 사용자 지정 .NET 활동을 만듭니다. 사용자 지정 활동을 만들고 사용하는 방법에 대한 자세한 내용은 [Azure Data Factory 파이프라인에서 사용자 지정 활동 사용](data-factory-use-custom-activities.md)을 참조하세요.

### <a name="datasets"></a>데이터 세트
작업은 0개 이상의 데이터 세트를 입력으로 사용하고 하나 이상의 데이터 세트를 출력으로 사용합니다. 데이터 세트는 데이터 저장소에 있는 데이터 구조를 나타냅니다. 이러한 구조는 활동(예: 입력 또는 출력)에서 사용하려는 데이터를 가리키거나 참조합니다. 

예를 들어, Azure Blob 데이터 세트는 파이프라인이 데이터를 읽어야 하는 Azure Blob Storage에서 Blob 컨테이너 및 폴더를 지정합니다. 또는 Azure SQL 테이블 데이터 세트는 작업에 의해 작성되는 출력 데이터에 대한 테이블을 지정합니다. 

### <a name="linked-services"></a>연결된 서비스
연결된 서비스는 데이터 팩터리에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. 연결된 서비스는 데이터 원본에 연결을 정의하고 데이터 세트는 데이터 구조를 나타낸다고 생각하시면 됩니다. 

예를 들어, Azure Storage 연결 서비스는 Azure Storage 계정에 연결할 연결 문자열을 지정합니다. Azure Blob 데이터 세트는 Blob 컨테이너 및 데이터가 포함된 폴더를 지정합니다.   

연결된 서비스는 데이터 팩터리 내에서 두 가지 이유로 사용됩니다.

* SQL Server 데이터베이스, Oracle 데이터베이스, 파일 공유 또는 Azure Blob 스토리지 계정을 포함하지만 여기에 국한되지 않는 *데이터 저장소* 를 나타내기 위해 사용됩니다. 지원되는 데이터 저장소 목록은 [데이터 이동 활동](#data-movement-activities) 섹션을 참조하세요.

* 활동의 실행을 호스팅할 수 있는 *컴퓨팅 리소스* 를 나타내기 위해 사용됩니다. 예를 들어, HDInsightHive 활동은 HDInsight Hadoop 클러스터에서 실행됩니다. 지원되는 컴퓨팅 환경 목록은 [데이터 변환 활동](#data-transformation-activities) 섹션을 참조하세요.

### <a name="relationship-between-data-factory-entities"></a>Data Factory 엔터티 간의 관계

:::image type="content" source="./media/data-factory-introduction/data-integration-service-key-concepts.png" alt-text="다이어그램: Data Factory, 클라우드 데이터 통합 서비스 - 주요 개념":::

## <a name="supported-regions"></a>지원되는 지역
현재 미국 서부, 미국 동부 및 북유럽 지역에서 데이터 팩터리를 만들 수 있습니다. 그러나 데이터 팩터리는 컴퓨팅 서비스를 사용하여 데이터 저장소 간에 데이터를 이동하고 데이터를 처리하도록 다른 Azure 지역에서 데이터 저장소 및 컴퓨팅 서비스에 액세스할 수 있습니다.

Azure 데이터 팩터리 자체는 데이터를 저장하지 않습니다. [지원되는 데이터 저장소](#data-movement-activities) 간에 데이터 이동을 오케스트레이션하는 데이터 기반 워크플로를 만들 수 있습니다. 다른 지역 또는 온-프레미스 환경에서 [컴퓨팅 서비스](#data-transformation-activities)를 사용하여 데이터를 처리할 수도 있습니다. 또한 프로그래밍 방식 및 UI 메커니즘을 모두 사용하여 [워크플로를 모니터링하고 관리](data-factory-monitor-manage-pipelines.md)할 수 있습니다.

데이터 팩터리는 미국 서부, 미국 동부 및 북유럽 지역에서만 사용할 수 있습니다. 그러나 데이터 팩터리에서 데이터 이동을 구동하는 서비스를 여러 지역에서 [전역적으로](data-factory-data-movement-activities.md#global) 사용할 수 있습니다. 데이터 저장소가 방화벽 뒤에 있는 경우 온-프레미스 환경에 설치된 [데이터 관리 게이트웨이](data-factory-move-data-between-onprem-and-cloud.md)가 대신 데이터를 이동시킵니다.

예를 들어, Azure HDInsight 클러스터 및 Azure Machine Learning과 같은 컴퓨팅 환경이 서유럽 지역에 있다고 가정해보겠습니다. 북유럽에서 Azure Data Factory 인스턴스를 만들고 사용할 수 있습니다. 그런 다음, 서유럽의 컴퓨팅 환경에서 작업을 예약하는 데 사용할 수 있습니다. 데이터 팩터리가 컴퓨팅 환경에 작업을 트리거하는 데는 몇 밀리초가 걸리지만 사용자의 컴퓨팅 환경에서 작업을 실행하는 데 걸리는 시간은 변경되지 않습니다.

## <a name="get-started-with-creating-a-pipeline"></a>파이프라인 만들기 시작
이러한 도구 또는 API 중 하나를 사용하여 Azure Data Factory에서 데이터 파이프라인을 만들 수 있습니다. 

- Visual Studio
- PowerShell
- .NET API
- REST API
- Azure Resource Manager 템플릿

데이터 파이프라인을 사용하여 데이터 팩터리를 빌드하는 방법을 알아보려면 다음 자습서의 단계별 지침을 따르세요.

| 자습서 | Description |
| --- | --- |
| [두 클라우드 데이터 저장소 간의 데이터 이동](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Blob 스토리지에서 SQL Database로 데이터를 이동하는 파이프라인을 포함한 데이터 팩터리를 만듭니다. |
| [Hadoop 클러스터를 사용하여 데이터 변환](data-factory-build-your-first-pipeline.md) |Azure HDInsight(Hadoop) 클러스터에서 Hive 스크립트를 실행하여 데이터를 처리하는 데이터 파이프라인으로 첫 번째 Azure 데이터 팩터리를 구축합니다. |
| [데이터 관리 게이트웨이를 사용하여 온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) |SQL Server 데이터베이스에서 Azure Blob으로 데이터를 이동하는 파이프라인이 포함된 데이터 팩터리를 빌드합니다. 자습서의 일부로 컴퓨터에 데이터 관리 게이트웨이를 설치하고 구성합니다. |
